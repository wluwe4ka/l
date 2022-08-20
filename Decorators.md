# Built-in decorators.
`@loader.tds` provides dynamic translate to commands \ class description.
- `"_cmd_doc_commandname"` - Translate command
- `"_ihandle_doc_handlername"` - Translate inline command
- `"_cls_doc"` - Translate module doc
This keys are not mandatory in main `strings`. You can put them in `strings_..` to translate docs to other languages
---
## Default commands permissions
E.g., for command `.mute` you can give default permission to admins who can ban users. For that you can use these decorators:
- `@loader.owner`
- `@loader.sudo`
- `@loader.support`
- `@loader.group_owner`
- `@loader.group_admin_add_admins`
- `@loader.group_admin_change_info`
- `@loader.group_admin_ban_users`
- `@loader.group_admin_delete_messages`
- `@loader.group_admin_pin_messages`
- `@loader.group_admin_invite_users`
- `@loader.group_admin`
- `@loader.group_member`
- `@loader.pm`
- `@loader.unrestricted`
- `@loader.inline_everyone`
---
## Infinite loops
`@loader.loop`
*Reference:*
```python
def loop(
    interval: int = 5,
    autostart: Optional[bool] = False,
    wait_before: Optional[bool] = False,
    stop_clause: Optional[str] = None,
) -> FunctionType:
    """
    Create new infinite loop from class method
    :param interval: Loop iterations delay
    :param autostart: Start loop once module is loaded
    :param wait_before: Insert delay before actual iteration, rather than after
    :param stop_clause: Database key, based on which the loop will run.
                       This key will be set to `True` once loop is started,
                       and will stop after key resets to `False`
    :attr status: Boolean, describing whether the loop is running
    """
```
---
## Commands and handlers
You can add handlers for commands and other stuff using decorators instead of placing `-cmd` in method name
E.g.:
`async def testcmd(...)` becomes:
```python
@loader.command()
async def test(...)
```

- `@loader.command()` - Decorator, that marks method as command with the name of method
- `@loader.inline_handler()` - Mark inline handler (`@hikka_..._bot <method>`)
- `@loader.callback_handler()` - Mark callback handler (only for [custom payload](https://github.com/hikariatama/Hikka/wiki/Inline#button-with-custom-payload-button_callback_handler) buttons, must be **handled properly**)
- `@loader.watcher()` - Mark watcher. You can create many watchers. Just ensure, that their method names are different (e.g. `watcher`, `watcher1`, `watcher2`)
  
You can pass:
- `ru_doc` - Translation for `@loader.command()` and `@loader.inline_handler()`. Supports not only Russian, but other languages. E.g. `ua_doc`, `de_doc` etc.

For every decorator in this section you can pass:
- `no_commands` - Ignore all userbot commands in watcher
- `only_commands` - Capture only userbot commands in watcher
- `out` - Capture only outgoing events
- `in` - Capture only incoming events
- `only_messages` - Capture only messages (not join events)
- `editable` - Capture only messages, which can be edited (no forwards etc.)
- `no_media` - Capture only messages without media and files
- `only_media` - Capture only messages with media and files
- `only_photos` - Capture only messages with photos
- `only_videos` - Capture only messages with videos
- `only_audios` - Capture only messages with audios
- `only_docs` - Capture only messages with documents
- `only_stickers` - Capture only messages with stickers
- `only_inline` - Capture only messages with inline queries
- `only_channels` - Capture only messages with channels
- `only_groups` - Capture only messages with groups
- `only_pm` - Capture only messages with private chats
- `startswith` - Capture only messages that start with given text
- `endswith` - Capture only messages that end with given text
- `contains` - Capture only messages that contain given text
- `regex` - Capture only messages that match given regex
- `func` - Capture only messages that pass given function
- `from_id` - Capture only messages from given user
- `chat_id` - Capture only messages from given chat
  
Examples:
- `@loader.watcher(out=True, only_channels=True, only_photos=True)` - Will capture only outgoing messages in channels, which contain photos
- `@loader.command(ru_doc="Тест", only_pm=True)` - Command with Russian doc translation "Тест", which can be executed only in personal messages