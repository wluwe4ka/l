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