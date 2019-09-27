## Убираем пробелы
Если мы хотим убрать все пробелы из строки, то мы можем использовать `replace` вместе с рег-ми выражениями
```js
var name = "ITMO University  ";
name.replace(/\s/g,''); // ITMOUniversity
\s --> space
g --> replace globally
We can also replace the space with '-'
var name = "Javascript jeep  ";
name.replace(/\s/g,'-'); // ITMO-University--
```

Если мы хотим удалить пробелы с одной из сторон, то можем вызывать методы `trimRight` и `trimLeft`
```js
var s = "string   ";
s = s.trimRight();    // "string"
//trimRight() returns a new string, removing the space on end of the string
If you want to remove the extra space at the beginning of the string then you can use
var s = "    string";
s = s.trimLeft();    // "string"

//trimLeft() returns a new string, removing the space on start of the string
```

И с обоих сторон с помощью `trim`
```js
var s = "   string   ";
s = s.trim();    // "string"

//trim() returns a new string, removing the space on start and end of the string
```
