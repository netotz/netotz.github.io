---
type: post
title: "What is the fastest way to concatenate strings in JavaScript?"
description: ""
date: 2022-11-13
tags: ["javascript", "typescript", "programming", "performance", "strings", "code readability", "benchmarking", "nodejs"]
showTableOfContents: true
---

The [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) says:

> JavaScript strings are immutable. This means that once a string is created, it is not possible to modify it. String methods create new strings based on the content of the current string — for example:
> - A concatenation of two strings using the concatenation operator (`+`) or `concat()`.

If you're a polyglot programmer (a nerd basically), you then know that many other programming languages also treat strings as an immutable data type, and that's because it comes with [many benefits](https://stackoverflow.com/a/9544198/10308289) like thread safety and predictable state.
But, although the advantages clearly outweigh the disadvantages, if a string cannot be modified, then the operations that "change" a string are consequently more expensive.
Those operations don't actually change it because it's immutable, so a new string must be created, or recreated with a difference, and recreating things is slow.
What is the impact of this in performance?
Let's benchmark it in NodeJS.

Imagine that, for some reason, you are using AWS DynamoDB as the database of your application.
The latest version of the AWS SDK, the v3, is written in TypeScript.
If you want to query a DynamoDB table by multiple conditions, you need to construct an expression string containing all of these conditions, and send it as a parameter of your query object.
To keep it simple, the condition expression of a TV shows table could look like:

```ts
'#season = :season AND #platform = :platform AND #episode > :episode AND ...'
```

where the left side of a condition is the column name and the right side is a value, which is provided in another parameter.

If in your API you just receive a bunch of column-value pairs as the conditions to query the database, you'll need to concatenate them in order to build the expression and send it.
I know, it's not an ORM, but it's what we have.
Based on this, it's easy to think about something like the following:

```ts
let expression = '';
for (const column of columns) {
    const condition = `#${column} = :${column}`;
    expression += expression ? ` AND ${condition}` : `${condition}`;
}
```

The ternary operator there looks noisy, but it's necessary because the expression cannot start with an `'AND'`.

A problem with this implementation is that since we're concatenating a new string to the current expression for every column, a new string is created by copying all the characters from the current expression plus the ones of the new condition.
If you are a big data scientist, you may have a table with hundreds of thousands of columns, and you need that data almost instantly.
Concatenating a very large amount of strings is then, in theory, slow enough to try to optimize it.

Some programming languages provide in their standard libraries an object that boosts the concatenation of several strings: the **string builder**.
This object can be thought in simple terms of an array of strings with a method that joins them all into a single string, and this is usually how it can be "implemented" (it's actually more like mocking) in languages without a proper string builder object, like JavaScript, which fortunately does provide an `Array.join` method.

```ts
// mock a string builder
const conditions: string[] = [];
for (const column of columns) {
    conditions.push(`#${column} = :${column}`);
}
const expression = conditions.join(' AND ');
```

The `.join` method supports an argument that represents the separator between every string in the array.
Of course, if there's nothing more needed in the `for` body, `.map` can be used instead:

```ts
const expression = columns.map((c) => `#${c} = :${c}`).join(' AND ');
```

This one, in my opinion, is the cleanest option and the easiest one to read.
It also *should* be faster, because instead of creating a new string in each iteration, we're collecting all the strings and just insert them into a new allocated string, kind of like a massive interpolation.
This makes sense, in theory.
But I bet you have been caught off guard in programming at least a few cases, especially by using a language like...


