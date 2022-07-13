## Basic sctructure
Any module starts with:

- License
- Scopes
- Dependencies import
- Logging init
- Module class, wrapped with @loader.tds
- Declaring basic handlers


All other actions live in module class\*.

\*Notice. If method doesn't use `self` (`self.anything`), it should be declared as [static](https://stackoverflow.com/a/735978/19170642) *except of commands, watchers and other stuff, used by Hikka under the hood*


## Structure docs

```python
def __init__(self):
```
Used only for config definition


```python
async def client_ready(self, client, db):
```
It's called when module is inited. Frequently used to start handlers, init APIs etc.


```python
async def on_dlmod(self, client, db):
```
It's called when module is *installed*. Frequently used to collect stats, join required channels.


```python
async def on_unload(self):
```
It's called when module is being unloaded. Here you can stop loops, exit APIs etc.

```python
async def testcmd(self, message: Message):
```
Handler of command. Any method, which ends with `cmd` will be considered as command

```python
async def watcher(self, message: Message):
```
All messages handler. All message events trigger this, including service messages


## utils.py
For utils docs, read the source code with comments and docstrings: [utils.py](https://github.com/hikariatama/Hikka/blob/master/hikka/utils.py)

## strings
This is not a dictionary. Used in translation purposes.
You can create strings for other languages, e.g. `strings_ru`, **HikkaDynamicTranslate** will automatically read this translation and apply it if neccessary.
Example:
```python
...
strings = {"name": "SomeModuleName", "hello_text": "Hello, world!"}
strings_ru = {"hello_text": "Привет, мир!"}
...
await utils.answer(message, self.strings("hello_text"))
```
## db
Get database value and replace it with `default` if not present:
```python
self.get(key: str, default: Any = None)
```
Example:
```python
self.get("token")
self.get("token", False")
self.get("state", True)
```
---
Set database value
```python
self.set(key: str, value: str)
```
Example:
```python
self.set("token", None)
self.set("state", True)
```