---
title: 'ES2020: Summary of new features with examples 
🔥'
date: 2020-04-12 16:39:07
published: true
cover_image: ""
description: "In this article, we’re going to review some of the latest and greatest features coming with ES2020."
tags: es2020, es6, es11, es7, es8, es9, es10, javascript, tc39
series:
canonical_url:
devto_publication: false
devto_draft: true
---
In this article, we’re going to review some of the latest and greatest features coming with ES2020. 🔥

# 🤠 Getting started
We are going to test these features in a Node.js environment using Babel.

## Method #1: creating a project from scratch
Start by creating a new project:
{% codeblock lang:bash %}{% raw %}
$ mkdir es2020-tests
$ yarn init
{% endraw %}{% endcodeblock %}

Now add `@babel/cli @babel/core @babel/node @babel/preset-env` dependencies:
{% codeblock lang:bash %}{% raw %}
$ yarn add --dev @babel/cli @babel/core @babel/node @babel/preset-env
{% endraw %}{% endcodeblock %}

Create a `.babelrc` file:
{% codeblock lang:json %}{% raw %}
{
    "presets": ["@babel/preset-env"]
}
{% endraw %}{% endcodeblock %}

## Method #2: clone the source code repository
You can also clone the following [GitHub](https://github.com/olivierloverde/es2020-examples) repository that includes the setup and source code of the examples in this article. 
{% codeblock lang:bash %}{% raw %}
$ git clone git@github.com:olivierloverde/es2020-examples.git
$ yarn install
{% endraw %}{% endcodeblock %}

# 🤖 The ES2020 features

## Private variable in Class

You can now declare a private variable in a class by using a hastag `#`. If a private variable is called outside of its class It will throw a `SyntaxError`.

{% codeblock lang:js %}{% raw %}
class MyClass {
    #privateVariable = "Hello private world"

    helloWorld() { console.info(this.#privateVariable) }
}

const myClass = new MyClass()
myClass.helloWorld() // works
console.info(myClass.#helloWorld) // SyntaxError: Private field '#helloWorld' must be declared in an enclosing class
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/privateClassVariables.js)

## BigInt

There was a limitation on largest possible integer because of how Javascript represented number internally (it is using a 64-bit floating point, see [IEE 754](https://en.wikipedia.org/wiki/IEEE_754). 
{% codeblock lang:js %}{% raw %}
const maxInteger = Number.MAX_SAFE_INTEGER;

console.info(maxInteger); // 9007199254740991
console.info(maxInteger + 1); // 9007199254740992
console.info(maxInteger + 2); // 9007199254740992 ??
console.info(maxInteger + 3); // 9007199254740994
console.info(maxInteger + 200); // 9007199254741192 ??
console.info(maxInteger * 200); // 1801439850948198100 ??
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/bigInt.js)


Now there is a native solution, BigInt is a built-in object that provides a way to represent whole numbers larger than 2⁵³ — 1, which is the largest number in JS  number).

You can create a BigInt by:
- creating a `BigInt` object: `const value = new BigInt(500)`
- appending a `n` to a number: `const value = 500n`

For the moment, it cannot be used with methods in the built-in `Math` object and cannot be operated with `Number`. Bitwise operators are supported except `>>>` because all BigInts are signed.

{% codeblock lang:js %}{% raw %}
// Using BigInt
const maxIntegerBigInt = BigInt(maxInteger);
console.info(maxIntegerBigInt); // 9007199254740991n
console.info(maxIntegerBigInt + 1n); // 9007199254740992n
console.info(maxIntegerBigInt + 2n); // 9007199254740993n 
console.info(maxIntegerBigInt + 3n); // 9007199254740994n 
console.info(maxIntegerBigInt + 200n); // 9007199254741191n
console.info(maxIntegerBigInt * 200n); // 1801439850948198200n
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/bigInt.js)

## Promise.allSettled()

Promise.allSettled takes an array of `Promise` object as argument and waits that all promises settle to return the corresponding result as an array of objects `{status, ?value, ?reason}`. 
{% codeblock lang:js %}{% raw %}
const resolvingPromise1000ms = new Promise((resolve, reject) => setTimeout(resolve, 1000));
const rejectingPromise2000ms = new Promise((resolve, reject) => setTimeout(reject, 2000));

const timeCheckpoint = Date.now();
Promise.allSettled([
    resolvingPromise1000ms, 
    rejectingPromise2000ms
]).then(data => {
    const elapsedTimeInMS = Date.now() - timeCheckpoint;
    console.info(`Promise.allSettled resolved after ${elapsedTimeInMS}ms`)
    console.info(data)
});

/*
Promise.allSettled resolved after 2006ms // ? not sure why we have 6ms
[
  { status: 'fulfilled', value: undefined },
  { status: 'rejected', reason: undefined }
]
*/
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/promiseAllSettled.js)

## Nullish Coalescing Operator
When you use `||` operator, it returns the first argument to be `true`. However, sometimes you a default value considered as `false` such as `0` or `""`. To avoid it we can use the nullish coalescing operator `??` like below:
{% codeblock lang:js %}{% raw %}
let object = {
    car: {
        speed: 0,
        name: ""
    }
};

console.info(object.car.speed || 90); // 90
console.info(object.car.speed ?? 90); // 0

console.info(null || true); // true
console.info(null ?? true); // true

console.info(undefined || true); // true
console.info(undefined ?? true); // true

console.info(0 || true); // true
console.info(0 ?? true); // 0

console.info("" || true); // true
console.info("" ?? true); // ""

console.info([] || true); // []
console.info([] ?? true); // []

console.info({} || true); // {}
console.info({} ?? true); // {}

console.info(true || "hey"); // true
console.info(true ?? "hey"); // true

console.info(false || true); // true
console.info(false ?? true); // false
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/nullishCoalescingOperator.js)

## Optional Chaining Operator
Let's take the following object as an example:
{% codeblock lang:js %}{% raw %}
let person = {
    name: "John",
    age: 20
};
{% endraw %}{% endcodeblock %}

Let's say we want to access a property on this object that we are not sure to have, we usually do:
{% codeblock lang:js %}{% raw %}
if (person.city !== undefined &&  person.city.locale !== undefined) {
    const cityLocale =  person.city.locale;
}
{% endraw %}{% endcodeblock %}
This ensures the program does not throw any "error cannot read property name of undefined".

Now with the optional chaining operator, we can be more concise:
{% codeblock lang:js %}{% raw %}
console.info(person?.city?.locale);
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/optionalChainingOperator.js)

## Dynamic Import
Dynamic `import()` returns a promise for the module namespace object of the requested module. Thus, we can now use the `import()` function with the `await` keyword and assign the module namespace object to a variable dynamically.
{% codeblock lang:js %}{% raw %}
const print = (value) => console.info(value);

export { print };
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/print.js)

{% codeblock lang:js %}{% raw %}
const doPrint = async (value) => {
    const Print = await import('./print.js');

    Print.print(value)
};

doPrint('Dynamic import works !');
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/dynamicImport.js)

## String.prototype.matchAll
`String.prototype.match` gives an array of all matches between a string and a regexp.

For example:
{% codeblock lang:js %}{% raw %}
const re = /(Mister )\w+/g;
const str = 'Mister Smith with Mister Galladon';
const matches = str.matchAll(re);

console.info(matches); // Object [RegExp String Iterator] {}
console.info(Array.from(matches));
/*
[
  [
    'Mister Smith',
    'Mister ',
    index: 0,
    input: 'Mister Smith with Mister Galladon',
    groups: undefined
  ],
  [
    'Mister Galladon',
    'Mister ',
    index: 18,
    input: 'Mister Smith with Mister Galladon',
    groups: undefined
  ]
]
*/
{% endraw %}{% endcodeblock %}
[Source on GitHub](https://github.com/olivierloverde/es2020-examples/blob/master/examples/matchAll.js)

---
You are now ready to use these new ES2020 features! Please leave me comment if you've liked it! 🙌

