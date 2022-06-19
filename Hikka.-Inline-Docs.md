## Scopes
- Modules, which use **any** inline features should contain `# scope: inline`
- If module doesn't support FTG\GeekTG, specify `# scope: hikka_only`
- If module requires certain Hikka version, use: `# scope: hikka_min 1.0.0`

## Form creation
To create message buttons, use [form manager](https://github.com/hikariatama/Hikka/blob/master/hikka/inline/form.py#L46):

### Reference:
```python
async def form(
    self,
    text: str,
    message: Union[Message, int],
    reply_markup: Union[List[List[dict]], List[dict], dict] = None,
    *,
    force_me: Optional[bool] = False,
    always_allow: Optional[List[list]] = None,
    manual_security: Optional[bool] = False,
    disable_security: Optional[bool] = False,
    ttl: Optional[int] = None,
    on_unload: Optional[callable] = None,
    photo: Optional[str] = None,
    gif: Optional[str] = None,
    file: Optional[str] = None,
    mime_type: Optional[str] = None,
    video: Optional[str] = None,
    location: Optional[str] = None,
    audio: Optional[str] = None,
    silent: Optional[bool] = False,
) -> Union[InlineMessage, bool]:
    """
    Send inline form to chat
    :param text: Content of inline form. HTML markdown supported
    :param message: Where to send inline. Can be either `Message` or `int`
    :param reply_markup: List of buttons to insert in markup. List of dicts with keys: text, callback
    :param force_me: Either this form buttons must be pressed only by owner scope or no
    :param always_allow: Users, that are allowed to press buttons in addition to previous rules
    :param ttl: Time, when the form is going to be unloaded. Unload means, that the form
                buttons with inline queries and callback queries will become unusable, but
                buttons with type url will still work as usual. Pay attention, that ttl can't
                be bigger, than default one (1 day) and must be either `int` or `False`
    :param on_unload: Callback, called when form is unloaded and/or closed. You can clean up trash
                        or perform another needed action
    :param manual_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                            If you want to avoid this, pass `manual_security=True`
    :param disable_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                                If you want to disable all security checks on this form in particular, pass `disable_security=True`
    :param photo: Attach a photo to the form. URL must be supplied
    :param gif: Attach a gif to the form. URL must be supplied
    :param file: Attach a file to the form. URL must be supplied
    :param mime_type: Only needed, if `file` field is not empty. Must be either 'application/pdf' or 'application/zip'
    :param video: Attach a video to the form. URL must be supplied
    :param location: Attach a map point to the form. List/tuple must be supplied (latitude, longitude)
                        Example: (55.749931, 48.742371)
                        ⚠️ If you pass this parameter, you'll need to pass empty string to `text` ⚠️
    :param audio: Attach a audio to the form. URL must be supplied
    :param silent: Whether the form must be sent silently (w/o "Loading inline form..." message)
    :return: If form is sent, returns :obj:`InlineMessage`, otherwise returns `False`
    """
```
### Example:
```python
await self.inline.form(
    text="📊 Poll Hikka vs. FTG\n🌘 Hikka: No votes\n😔 FTG: No votes",
    message=message,
    reply_markup=[
        [
            {
                "text": "Hikka",
                "callback": self.vote,
                "args": [False]
            }
        ],
        [
            {
                "text": "FTG",
                "callback": self.vote,
                "args": [True]
            }
        ],
    ],
    force_me=False,  # optional: Allow other users to access form (all)
    always_allow=[659800858],  # optional: Permit users with IDs
    ttl=30,  # optional: Time to live of form in seconds
)
```
![Без имени-1](https://user-images.githubusercontent.com/36935426/157850552-ff489e8e-3f64-4139-b1d6-b95c430707c0.png)

Buttons examples:

### Button with function callback (most frequently used):
```python
{
    "text": "Button with function",
    "callback": self.callback_handler,
    "args": (arg1, ),  # optional arguments passed to callback
    "kwargs": {"arg1name": "arg1"},  # optional kwargs passed to callback
}
```
### Button with custom payload (button_callback_handler):
```python
{
    "text": "Button with custom payload",
    "data": "custom_payload",
}
```
### Button with link:
```python
{
    "text": "URL Button",
    "url": "https://example.com",
}
```
### Button which asks user to input some value:
```python
{
    "text": "✍️ Enter value",
    "input": "✍️ Enter new configuration value for this option",
    "handler": self.input_handler,
    "args": (arg1, ),  # optional arguments passed to callback
    "kwargs": {"arg1name": "arg1"},  # optional kwargs passed to callback
}
```

> ⚠️ **If error occurs, no exception will be raised, only `False` returned !**

## Gallery
There are [inline galleries](https://github.com/hikariatama/Hikka/blob/master/hikka/inline/gallery.py#L46)

### Reference:
```python
async def gallery(
    self,
    message: Union[Message, int],
    next_handler: Union[callable, List[str]],
    caption: Optional[Union[List[str], str, callable]] = "",
    *,
    custom_buttons: Optional[Union[List[List[dict]], List[dict], dict]] = None,
    force_me: Optional[bool] = False,
    always_allow: Optional[list] = None,
    manual_security: Optional[bool] = False,
    disable_security: Optional[bool] = False,
    ttl: Optional[Union[int, bool]] = False,
    on_unload: Optional[callable] = None,
    preload: Optional[Union[bool, int]] = False,
    gif: Optional[bool] = False,
    silent: Optional[bool] = False,
    _reattempt: bool = False,
) -> Union[bool, InlineMessage]:
    """
    Send inline gallery to chat
    :param caption: Caption for photo, or callable, returning caption
    :param message: Where to send inline. Can be either `Message` or `int`
    :param next_handler: Callback function, which must return url for next photo or list with photo urls
    :param custom_buttons: Custom buttons to add above native ones
    :param force_me: Either this gallery buttons must be pressed only by owner scope or no
    :param always_allow: Users, that are allowed to press buttons in addition to previous rules
    :param ttl: Time, when the gallery is going to be unloaded. Unload means, that the gallery
                will become unusable. Pay attention, that ttl can't
                be bigger, than default one (1 day) and must be either `int` or `False`
    :param on_unload: Callback, called when gallery is unloaded and/or closed. You can clean up trash
                        or perform another needed action
    :param preload: Either to preload gallery photos beforehand or no. If yes - specify threshold to
                    be loaded. Toggle this attribute, if your callback is too slow to load photos
                    in real time
    :param gif: Whether the gallery will be filled with gifs. If you omit this argument and specify
                gifs in `next_handler`, Hikka will try to determine the filetype of these images
    :param manual_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                            If you want to avoid this, pass `manual_security=True`
    :param disable_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                                If you want to disable all security checks on this gallery in particular, pass `disable_security=True`
    :param silent: Whether the gallery must be sent silently (w/o "Loading inline gallery..." message)
    :return: If gallery is sent, returns :obj:`InlineMessage`, otherwise returns `False`
    """
```
### Example
```python
def generate_caption() -> str:
    return random.choice(["Да", "Нет"])

async def photo() -> str:
    return (await utils.run_sync(requests.get, "https://api.catboys.com/img")).json()["url"]

await self.inline.gallery(
    message=message,
    next_handler=photo,
    caption=generate_caption,
)
```
`generate_caption` - method, which returns caption
`photo` - Async function, which returns photo(-s)
> Instead of `generate_caption` you can pass string or list
> Instead of `photo` you can pass list with urls
## InlineQuery Galleries
To allow user to call galleries via inline query (@hikka_xxxxxx_bot), user (built-in method)[https://github.com/hikariatama/Hikka/blob/master/hikka/inline/query_gallery.py#L14]

### Reference:
```python
async def query_gallery(
    self,
    query: InlineQuery,
    items: List[dict],
    *,
    force_me: Optional[bool] = False,
    disable_security: Optional[bool] = False,
    always_allow: Optional[list] = None,
) -> bool:
    """
    Answer inline query with a bunch of inline galleries
    :param query: `InlineQuery` which should be answered with inline gallery
    :param items: Array of dicts with inline results.
                    Each dict *must* has a:
                        - `title` - The title of the result
                        - `description` - Short description of the result
                        - `next_handler` - Inline gallery handler. Callback or awaitable
                    Each dict *can* has a:
                        - `caption` - Caption of photo. Defaults to `""`
                        - `force_me` - Whether the button must be accessed only by owner. Defaults to `False`
                        - `disable_security` - Whether to disable the security checks at all. Defaults to `False`
    :param force_me: Either this gallery buttons must be pressed only by owner scope or no
    :param always_allow: Users, that are allowed to press buttons in addition to previous rules
    :param disable_security: By default, Hikka will try to check security of gallery
                                If you want to disable all security checks on this gallery in particular, pass `disable_security=True`
    :return: Status of answer
    """
```
### Example
```python
async def catboy_inline_handler(self, query: InlineQuery):
    """Send Catboys"""
    await self.inline.query_gallery(
        query,
        [
            {
                "title": "🌘 Catboy",
                "description": "Send catboy photo",
                "next_handler": photo,
                "thumb_handler": photo,  # Optional
                "caption": lambda: f"<i>Enjoy! {utils.escape_html(utils.ascii_face())}</i>",  # Optional
                # Because of ^ this lambda, face will be generated every time the photo is switched

                # "caption": f"<i>Enjoy! {utils.escape_html(utils.ascii_face())}</i>",
                # If you make it without lambda ^, it will be generated once
            }
        ],
    )

```
## Inline list
You can use (inline lists)[https://github.com/hikariatama/Hikka/blob/master/hikka/inline/list.py#L30]

### Reference:
```python
async def list(
    self,
    message: Union[Message, int],
    strings: _List[str],
    *,
    force_me: Optional[bool] = False,
    always_allow: Optional[list] = None,
    manual_security: Optional[bool] = False,
    disable_security: Optional[bool] = False,
    ttl: Optional[Union[int, bool]] = False,
    on_unload: Optional[callable] = None,
    silent: Optional[bool] = False,
    custom_buttons: Optional[Union[_List[_List[dict]], _List[dict], dict]] = None,
) -> Union[bool, InlineMessage]:
    """
    Send inline list to chat
    :param message: Where to send list. Can be either `Message` or `int`
    :param strings: List of strings, which should become inline list
    :param force_me: Either this list buttons must be pressed only by owner scope or no
    :param always_allow: Users, that are allowed to press buttons in addition to previous rules
    :param ttl: Time, when the list is going to be unloaded. Unload means, that the list
                will become unusable. Pay attention, that ttl can't
                be bigger, than default one (1 day) and must be either `int` or `False`
    :param on_unload: Callback, called when list is unloaded and/or closed. You can clean up trash
                        or perform another needed action
    :param manual_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                            If you want to avoid this, pass `manual_security=True`
    :param disable_security: By default, Hikka will try to inherit inline buttons security from the caller (command)
                                If you want to disable all security checks on this list in particular, pass `disable_security=True`
    :param silent: Whether the list must be sent silently (w/o "Loading inline list..." message)
    :param custom_buttons: Custom buttons to add above native ones
    :return: If list is sent, returns :obj:`InlineMessage`, otherwise returns `False`
    """
```
### Example
```python
async def meancmd(self, message: Message) -> None:
    """<term> - Find definition of the word in urban dictionary"""
    args = utils.get_args_raw(message)

    ...

    await self.inline.list(
        message=message,
        strings=[self.strings("meaning").format(args, mean) for mean in means],
    )

```
# Inline buttons processing
Example, where along with callback 1 positional argument is passed
```python
from ..inline.types import InlineCall
async def _process_click_ai(self, call: InlineCall, arg1: str):
    # Do some stuff
```
In this case you can also use:
```python
await call.unload()  # Unload form from memory

await call.delete()  # Unload form from memory and delete message

await call.edit(
    text="Some new text",
    reply_markup=[
        [
            {
                "text": "New Button",
                "url": "https://ya.ru"
            }
        ]
    ],  # optional: Change buttons in message. If not specified, buttons will be removed
    disable_web_page_preview=True,  # optional: Disable link preview
    always_allow=[659800858],  # optional: Change allowed users
    force_me=False,  # optional: Change button privacy mode
)

call.form  # optional: Contains info about form
```
## Inline commands (@bot ...)
```python
from ..inline.types import InlineQuery

async def <name>_inline_handler(self, query: InlineQuery):
    # Process request
```
To get text, entered after the call, use `query.args`
You can wrap these handlers with:
- `@loader.support`
- `@loader.sudo`
- `@loader.owner`
- `@loader.inline_everyone`

### Useful shortcuts

- `await query.e400()` - Неверные аргументы
- `await query.e403()` - Недостаточно прав для доступа к ресурсу
- `await query.e404()` - Требуемый результат не найден
- `await query.e426()` - Необходимо обновление юзербота
- `await query.e500()` - Ошибка модуля. Смотри логи

### Answer Inline query

You need to return dictionary with answer:
- `{"message": "<b>Message text</b>", "title": "Text answer"}` 
- `{"photo": "https://i.imgur.com/hZIyI7v.jpeg", "title": "Photo"}`
- `{"video": "https://x0.at/wWN9.mp4", "title": "Video"}`
- `{"file": "https://x0.at/f7ps.pdf", "mime_type": "application/pdf", "title": "Document"}`
- `{"gif": "https://x0.at/Sey-.mp4", "title": "GIF animation"}`
