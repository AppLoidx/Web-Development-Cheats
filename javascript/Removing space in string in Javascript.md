## Learn how to remove space in string.
If you want to remove all the space in the string then , you can use replace method
```js
var name = "ITMO University  ";
name.replace(/\s/g,''); // ITMOUniversity
\s --> space
g --> replace globally
We can also replace the space with '-'
var name = "Javascript jeep  ";
name.replace(/\s/g,'-'); // ITMO-University--
```

If you want to remove the extra space at the end of the string then you can use
```js
var s = "string   ";
s = s.trimRight();    // "string"
//trimRight() returns a new string, removing the space on end of the string
If you want to remove the extra space at the beginning of the string then you can use
var s = "    string";
s = s.trimLeft();    // "string"

//trimLeft() returns a new string, removing the space on start of the string
```

If you want to remove the extra space at the both end of the string then you can use
```js
var s = "   string   ";
s = s.trim();    // "string"

//trim() returns a new string, removing the space on start and end of the string
```
