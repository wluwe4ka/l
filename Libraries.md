# Libraries
If you have a code, which is used in several modules, you can pack it in library and then import it everywhere you need.  
There's separate config design for shared libraries.  
⚠️ **Libraries are available only starting from Hikka v1.2.10.** If you use anything listed below, make sure you have a `# scope: hikka_min 1.2.10`

## Example:
> https://libs.hikariatama.ru/musicdl.py:
```python
from .. import loader, utils
import telethon
import asyncio
import logging


class MusicDL_Lib(loader.Library):
    developer = "@hikariatama"

    async def dl(self, full_name: str) -> bytes:
        try:
            q = await self._client.inline_query("@losslessrobot", full_name)
            result = q.result.results[0]
            if not getattr(getattr(result, "send_message", None), "reply_markup", None):
                dl_file = result.document
            else:
                m = await q[0].click("me")

                dl_event = asyncio.Event()
                dl_file = None

                @self._client.on(
                    telethon.events.MessageEdited(chats=utils.get_chat_id(m))
                )
                async def handler(event: telethon.events.MessageEdited):
                    nonlocal dl_file
                    if (
                        event.message.id == m.id
                        and not getattr(event.message, "reply_markup", None)
                        and event.message.document
                    ):
                        dl_file = event.message.document
                        dl_event.set()
                        raise telethon.events.StopPropagation

                try:
                    await asyncio.wait_for(dl_event.wait(), timeout=40)
                except Exception:
                    await m.delete()
                    raise

            async with self._client.conversation("@DirectLinkGenerator_Bot") as conv:
                m = await conv.send_file(dl_file)
                r = await conv.get_response()
                await m.delete()
                await r.delete()
                return r.reply_markup.rows[0].buttons[1].url
        except Exception:
            logging.exception("Can't download")
            return None
```

## Detailed info about library structure:
> https://libs.hikariatama.ru/example.py:
```python
# -----------------------------------------------------------------------------
# This is example library code
# ⚠️ Any scopes, specified there will be ignored, including # requires:
# requires: example_pypi_package - THIS LINE DOES NOTHING

from .. import loader
import random
import logging

# Every library class name must end with `Lib` and be subclass of `loader.Library`
#            👇         👇
class ExampleLib(loader.Library):
    # You can specify the developer of library in this way:
    developer = "@hikariatama"

    # Declare library config in `__init__`:

    def __init__(self):
        # loader.ModuleConfig will throw!
        self.config = loader.LibraryConfig(
            loader.ConfigValue(
                "example_option",
                "default",
                "Example description",
                validator=loader.validators.String(),
            )
        )

    async def init(self):
        ...  # Do some stuff for init

    # ----------------------------------------------

    # ----------------------------------------------
    # Methods may be async or sync:
    def example_method(self) -> str:
        # self._client and self._db are available
        # No need to explicitly import them
        return (
            "anime neko "
            + random.choice(["girl", "boy"])
            + " --> "
            + self._client._tg_id
        )

    async def example_async(self):
        await self._client.send_message("me", "Hello!")

    # ----------------------------------------------


# --------------------------------------------------------------
# Then in your module code you can access this library in this way:

async def client_ready(self, client, db):
    ...
    self.example = await self.import_lib("https://link.to.your.server/example.py")

    logging.info(self.example.example_method())
    await self.example.example_async()


# If library throws an error, or is unavailable (404\403 etc),
# module will be UNLOADED FROM DB and will not be loaded again
# If you want to avoid this, use example below, or handle errors
# in your module code:


async def client_ready(self, client, db):
    try:
        self.example = await self.import_lib(...)
    except Exception:
        self.example = None

    if self.example:
        self.example.example_method()
    else:
        logging.warning("Library is not available along with method from it")


# --------------------------------------------------------------
# If you don't handle errors while importing (see example above), use `suspend_on_error` param
# to import the library:


async def client_ready(self, client, db):
    ...
    self.example = await self.import_lib(
        "https://link.to.your.server/example.py",
        suspend_on_error=True,  # 👈👈👈👈👈👈👈👈👈👈👈👈👈👈👈
    )

    logging.info(self.example.example_method())
    await self.example.example_async()


# This way, if Hikka can't load library, it'll suspend your module
# It won't be unloaded from db, so user will get it after restart, if
# library becomes available

# --------------------------------------------------------------

```