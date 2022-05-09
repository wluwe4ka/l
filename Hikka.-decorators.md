# Встроенные декораторы.
`@loader.tds` позволяет динамически переводить модули.
- `"_cmd_doc_commandname"` - Перевод команды
- `"_ihandle_doc_handlername"`
- `"_cls_doc"` - Перевод описания модуля
Эти ключи надо класть в `strings_ru`
---
## Стандартные права для команд
Например, команде `.mute` можно по умолчанию дать разрешение на выполнение администраторам чата с правом на бан пользователей. Для этого можно использовать следующие декораторы с говорящими именами:
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
## Бесконечные циклы
`@loader.loop`
*Референс:*
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