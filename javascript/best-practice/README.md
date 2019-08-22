# Best Practices for JavaScript development

## Content
* [Async / Await](#async-await)
* [Console log](#console-log)
* [Destructuring](#destructuring)
* [Loops](#loops)
* [Rest params](#rest-params)
* [Spread syntax](#spread-syntax)
* [Template literals](#template-literals)

## async-await

```js
const random = () => {
    return Promise.resolve(Math.random())
}

'Bad Promise Code 💩'

const sumRandomAsyncNums = () => {
    let first;
    let second;
    let third;

    return random()
        .then(v => {
            first = v;
            return random();
        })
        .then(v => {
            second = v;
            return random();
        })
        .then(v => {
            third = v;
            return first + second + third
        })
        .then(v => {
            console.log(`Result ${v}`)
        });
}


'Good Promise Code ✅'

const sumRandomAsyncNums = async() => {

    const first = await random();
    const second = await random();
    const third = await random();

    console.log(`Result ${first + second + third}`);

    if (await random()) {
        // do something
    }

    const randos = Promise.all([
        random(), 
        random(),
        random()
    ])

    for(const r of await randos) {
        console.log(r)
    }


}
```

## console-log
```js
const foo    = { name: 'tom',   age: 30, nervous: false };
const bar    = { name: 'dick',  age: 40, nervous: false };
const baz    = { name: 'harry', age: 50, nervous: true };


'Bad Code 💩'

console.log(foo);
console.log(bar);
console.log(baz);



'Good Code ✅'

// Computed Property Names

console.log('%c My Friends', 'color: orange; font-weight: bold;' )
console.log({ foo, bar, baz });

// Console.table(...)
console.table([foo, bar, baz])


// // Console.time
console.time('looper')

let i = 0;
while (i < 1000000) { i ++ }

console.timeEnd('looper')

// // Stack Trace Logs

const deleteMe = () => console.trace('bye bye database')

deleteMe()
deleteMe()
```

## Destructuring
```js
const turtle = {
    name: 'Bob 🐢',
    legs: 4,
    shell: true, 
    type: 'amphibious',
    meal: 10,
    diet: 'berries'
}

'Bad Code 💩'

function feed(animal) {
    return `Feed ${animal.name} ${animal.meal} kilos of ${animal.diet}`;
}


'Good Code ✅'

function feed({ name, meal, diet }) {
    return `Feed ${name} ${meal} kilos of ${diet}`;
}

// OR

function feed(animal) {
    const { name, meal, diet } = animal;
    return `Feed ${name} ${meal} kilos of ${diet}`;
}



console.log(feed(turtle))
```

## Loops
```js
const orders = [500, 30, 99, 15, 223];


'Bad Loop Code 💩'

const total = 0;
const withTax = [];
const highValue = [];
for (i = 0; i < orders.length; i++) { 

    // Reduce
    total += orders[i];

    // Map
    withTax.push(orders[i] * 1.1);

    // Filter
    if (orders[i] > 100) {
        highValue.push(orders[i])
    }
}


'Good Loop Code ✅'

// Reduce
const total = orders.reduce((acc, cur) => acc + cur)

// Map
const withTax = orders.map(v => v * 1.1)

// Filter
const highValue = orders.filter(v => v > 100);

/**
 * Every
 * @returns false
 */
const everyValueGreaterThan50 = orders.every(v => v > 50)

/**
 * Every
 * @returns true
 */
const everyValueGreaterThan10 = orders.every(v => v > 10)


/**
 * Some
 * @returns false
 */
const someValueGreaterThan500 = orders.some(v => v > 500)

/**
 * Some
 * @returns true
 */
const someValueGreaterThan10 = orders.some(v => v > 10)
```

## rest-params
```js
'Bad Function Code 💩'

function totalHitPoints(a, b, c, d) {
    return a + b + c + d;
}

'Good Function Code ✅'

function totalHitPoints(...hits) {
    return hits.reduce((a, b) => a + b);
}

totalHitPoints(1,2,3,4,5,6,7,)
```

## spread-syntax
```js

// Objects

const pikachu = { name: 'Pikachu 🐹'  };
const stats = { hp: 40, attack: 60, defense: 45 }

'Bad Object Code 💩'

pikachu['hp'] = stats.hp
pikachu['attack'] = stats.attack
pikachu['defense'] = stats.defense

// OR

const lvl0 = Object.assign(pikachu, stats)
const lvl1 = Object.assign(pikachu, { hp: 45 })

'Good Object Code ✅'

const lvl0 = { ...pikachu, ...stats }
const lvl1 = { ...pikachu, hp: 45 }


// Arrays

let pokemon = ['Arbok', 'Raichu', 'Sandshrew'];

'Bad Array Code 💩'

pokemon.push('Bulbasaur')
pokemon.push('Metapod')
pokemon.push('Weedle')

'Good Array Code ✅'

// Push 
pokemon = [...pokemon, 'Bulbasaur', 'Metapod', 'Weedle']

// Shift

pokemon = ['Bulbasaur', ...pokemon, 'Metapod', 'Weedle', ]
```

## template-literals
```js
const horse = {
    name: 'Topher 🐴',
    size: 'large',
    skills: ['jousting', 'racing'],
    age: 7
}

'Bad String Code 💩'
  
let bio = horse.name + ' is a ' + horse.size + ' horse skilled in ' + horse.skills.join(' & ')

'Good String Code ✅'
const { name, size, skills } = horse;
bio = `${name} is a ${size} horse skilled in ${skills.join(' & ')}`
console.log(bio);

// Advanced Tag Example

function horseAge(str, age) {
    
    const ageStr = age > 5 ? 'old' : 'young';
    return `${str[0]}${ageStr} at ${age} years`;
}

const bio2 = horseAge`This horse is ${horse.age}`;
console.log(bio2)
```
