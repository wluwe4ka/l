## Общая структура
Любой модуль начинается с:

- Лицензии
- Скопов
- Импорта зависимостей
- Подключения логирования
- Класса модуля, обернутого в декоратор @loader.tds
- Объявления базовых обработчиков


Все остальные действия выполняются внутри этого класса\*.

\*Важное примечание. Если функция не использует атрибуты и методы класса (`self.что-то`), ее стоит объявление стоит вынести за пределы класса, либо сделать статичным методом (`@staticmethod`)


## Справочник по структуре

`@loader.tds`
Декоратор, используемый на классе модуля. Нужен для добавления возможности локализации докстрингов команд.


```python
def __init__(self) -> None:
```
Вызывается при начале загрузки модуля. Обычно используется только для инициализации конфига.


```python
async def client_ready(self, client, db) -> None:
```
Вызывается в момент, когда готовы все инстансы (`client`, `db`). Основное место инициализации. Чаще всего здесь объявляют внутренние переменные, готовят базу данных, запускают нужные корутины и другие вещи, которые нужны при загрузке.


```python
async def on_unload(self) -> None:
```
Вызывается в момент выгрузки модуля. Максимальное время выполнения - 5 секунд, при превышении код прерывается. Используется для сброса бесконечных циклов и других действий, нужных при выгрузке \ перезагрузке модуля.

```python
async def testcmd(self, message: Message) -> None:
```
Таким образом выглядят обработчики команд. Все функции класса, которые заканчиваются на -cmd расцениваются как команды. Обратите внимание, что нельзя создать команду, которая начинается с цифры, содержит спецсимволы и др. Действуют те же ограничения, что и на обычные функции Python.

```python
async def watcher(self, message: Message) -> None:
```
Обработчик всех сообщений. В него приходят все события, которые вызваны другими сессиями (**не сессией юзербота**), включая сервисные сообщения.


## utils.py
Для повышения совместимости модуля рекомендуется использовать библиотеку `utils`. Она содержит множество полезных методов:

Возвращает список аргументов из сообщения
```python
def get_args(message: Message) -> List[str]:
```
---

Возвращает необработанные аргументы в виде одной строки
```python
def get_args_raw(message: Message) -> str:
```
---

Возвращает список аргументов, разделенных символом sep
```python
def get_args_split_by(message: Message, separator: str) -> List[str]:
```
---

Возвращает ID объекта (для чатов\каналов без -100)
```python
def get_chat_id(message: Message) -> int:
```
---

Экранирует HTML, данный в аргументе
```python
def escape_html(text: str, /) -> str:
```
---
Экранирует кавычки
```python
def escape_quotes(text: str, /) -> str:
```
---

Получает рабочую директорию
```python
def get_base_dir() -> str:
```
---

Получает директорию выбранного модуля
```python
def get_dir(mod: str) -> str:
```
---

Получает отправителя (не работает с сервисными сообщениями)
```python
async def get_user(message: Message) -> Union[None, User]:
```
---

Оборачивает синхронный код в коротину. Рекомендуется использовать тогда, когда нужно внутри модуля использовать синхронный код, чтобы не останавливать основной поток
```python
run_sync(func: FunctionType, *args, **kwargs) -> coroutine
```
---
Запускает асинхронный код синхронно
```python
def run_async(loop, coro):
```
---
Убирает потенциально компрометирующую информацию из telethon-объекта
```python
def censor(
    obj,
    to_censor=None,
    replace_with="redacted_{count}_chars",
):
```
---

Репозиционирование объектов и их смещений
```python
def relocate_entities(
    entities: list,
    offset: int,
    text: Union[str, None] = None,
) -> list:
```
---

Ответить на сообщение (редактировать, если возможно, иначе отправить новое)
```python
async def answer(
    message: Union[Message, CallbackQuery],
    response: str,
    **kwargs,
) -> list:
```
---

Получить ID вероятной цели
```python
async def get_target(message: Message, arg_no: int = 0) -> Union[int, None]:
```
---

Объединить два словаря
```python
def merge(a: dict, b: dict) -> dict:
```
---

Создать новый канал (если это требуется), и вернуть его entity
```python
async def asset_channel(
    client: "TelegramClient",  # noqa: F821
    title: str,
    description: str,
    *,
    silent: bool = False,
    archive: bool = False,
) -> Tuple[Channel, bool]:
```
---
Замьютить канал\чат, отправить его в архив
```python
async def dnd(client: "TelegramClient", peer: Entity, archive: bool = True) -> bool:  # noqa: F821
```
---

Получить пермалинк на entity
```python
def get_link(user: Union[User, Channel], /) -> str:
```
---

Разделить `_list` на чанки по `n`
```python
def chunks(_list: Union[list, tuple, set], n: int, /) -> list:
```



## strings
Хоть они и объявляются как словарь, на самом деле это не словарь. Их можно вызывать. Сделано это для упрощения локализации. Все строки, которые можно вынести в strings - выноси в strings. В этом же словаре объявляется отображаемое имя модуля. Старайся выбирать имя без пробелов, спецсимволов и другого шлака.

## db
База данных FTG. Практически всегда сохраняется в атрибут self.db или self.\_db.

Получение ключа из базы данных, заменяя его `default` в случае отсутствия:
```python
db.get(owner: str, key: str, default: Any = None) -> Many
```
---
Установка значения ключа базы данных
```python
db.set(owner: str, key: str, value: str) -> None
```
---
> Документация будет пополняться