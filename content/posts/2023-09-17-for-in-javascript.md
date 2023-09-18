---
author: 'Rahul Zhade'
date: 2023-09-17
linktitle: for … in vs for … of in JavaScript
title: '"for … in" vs "for … of" in JavaScript'
weight: 10
tags: ['javascript', 'iteration']
---

TIL that `for … in` and `for … of` have different behaviors in JavaScript.

As a native Python developer, I had presumed that `for … in` in JavaScript would have a similar behavior to the equivalent in Python. However, they have very different behaviors. See below:

### TLDR
| JavaScript        | Python               |
|-------------------|----------------------|
| `for (foo in [])` | `for i in range([])` |
| `for (foo of [])` | `for i in []`        |

### Details

`for … in` iterates over the *keys* of a particular object, whereas `for … of` iterates over the *values* of the object. It does this by using an [iteration protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols).

Now, how does this manifest itself? Let’s take a look at the behavior of both of these methods when trying to iterate over an array:

```javascript
const arr = ['hello', 'world', 'foo', 'bar']

for (val in arr) {
	console.log(val)
}
//> 0
//> 1
//> 2
//> 3

for (val of arr) {
	console.log(val)
}
//> hello
//> world
//> foo
//> bar
```

What’s going on here? Remember that everything in JavaScript is an object. Well, in JavaScript, [the default key](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in#array_iteration_and_for...in) for an array is the *array index*. As stated on the [MDN docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in#array_iteration_and_for...in),

> Array indexes are just enumerable properties with integer names
> Unlike `for...of`, `for...in` uses property enumeration instead of the array's iterator

Since `for … in` is *enumerating the properties of the object* instead of iterating over the values, it spits out the array indices as opposed to the values inside the array. The strings inside the array are the *value* that is being pointed to using the array index as a key, which is why `for … of` works as expected.

JavaScript.



Full credit to this [StackOverflow](https://stackoverflow.com/questions/29285897/difference-between-for-in-and-for-of-statements) post and the MDN Docs.[^1][^2][^3]

---
[^1]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in
[^2]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of
[^3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols
