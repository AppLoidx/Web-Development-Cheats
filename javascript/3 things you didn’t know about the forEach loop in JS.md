## Do you think you know exactly how the forEach loop works in JS?

Well, these were my thoughts until recently: “just a regularfor loop where you can easily use break or return or continue“.
Today, I’ll show you 3 things which you might have not known about the forEach loop.

### 1.`return` doesn’t stop looping
Do you think the code below would print 1 2 and then stop?
```js
array = [1, 2, 3, 4];
array.forEach(function (element) {
  console.log(element);
  
  if (element === 2) 
    return;
  
});
// Output: 1 2 3 4
```

No, it won’t. If you come from a Java background, you would probably ask yourself how is that possible?<br>
The reason is that we are passing a callback function in our forEach function, which behaves just like a normal function and is applied to each element no matter if we return from one i.e. when element is 2 in our case.
From the official MDN docs:
>There is no way to stop or break a forEach() loop other than by throwing an exception. If you need such behavior, the forEach() method is the wrong tool.

Let’s rewrite the code from above:
```js
const array = [1, 2, 3, 4];
const callback = function(element) {
    console.log(element);
    
    if (element === 2) 
      return; // would this make a difference? no.
}
for (let i = 0; i < array.length; i++) {
    callback(array[i]);
}
// Output: 1 2 3 4
```

The returnstatement won’t make any difference, as we apply the function to each element at each iteration, hence it doesn’t care if you exited once i.e. when element is 2.

### 2. You cannot `break`
Do you think aforEach loop would break in the example below?
```js
const array = [1, 2, 3, 4];
array.forEach(function(element) {
  console.log(element);
  
  if (element === 2) 
    break;
});
// Output: Uncaught SyntaxError: Illegal break statement
```
No, it won’t even run because the break instruction is not technically in a loop.
<br><br>
Solution?

Just use a normal for loop. Nobody would laugh at you.
```js
const array = [1, 2, 3, 4];
for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
  
  if (array[i] === 2) 
    break;
}
// Output: 1 2
```
### 3. You cannot `continue`
Do you expect the code below to skip printing `2` to the console and only show `1 3 4` ?
```js
const array = [1, 2, 3, 4];
array.forEach(function (element) {
  if (element === 2) 
    continue;
  
  console.log(element);
});
// Output: Uncaught SyntaxError: Illegal continue statement: no surrounding iteration statement
```

No, it won’t even run because the continue instruction is not in a loop, similar to the break instruction.
<br><br>
Solution?

Just use a normal for loop again.
```js
for (let i = 0; i < array.length; i++) {
  if (array[i] === 2) 
    continue;
  
  console.log(array[i]);
}
// Output: 1 3 4
```

That was it! Hope you learned something new today.

**Author**: [Tiberiu Oprea](https://medium.com/@tiboprea?source=post_page-----ff02cec465b1----------------------)
