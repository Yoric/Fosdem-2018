# The JavaScript Binary AST

A proposal to speed up the web by Mozilla, Bloomberg, Facebook.

David Teller, Mozilla

Fosdem 2018

---

## The problem

- Google Sheets, Google Docs, Yahoo!, LinkedIn, Facebook: 3-7 compressed Mb+ JS code.
- Updated *very* often.
- Facebook: 500-900ms just *parsing* JavaScript (Chrome & Firefox).
- How can we make JS code start executing faster?

---

## Why is JavaScript loading so slow?

---

### A few simple steps

1. Download source file.
1. Decompress source file.
1. Convert encoding.
1. Tokenize.
1. Parse.
1. Generate Bytecode.
1. Start execution.

---

![Would That It Were So Simple](img/wouldthatitweresosimple.gif)

---

### Step 1: Get the code

* Download and decompress source file.
* Covert encoding.

(typically two/three successive tasks)

---

### Step 2. Tokenize the text (1)


```js
function foo(x) {
    return y;
}
```

.center[⇓]

* `Token.FunctionKeyword`
* `Token.Identifier(foo)`
* `Token.LPar`
* `Token.Identifier(x)`
* `Token.RPar`
* `Token.RBrace`
* ...

---

### Step 2. Tokenize the text (2)

Exercises:

* Is `for` an identifier or a keyword?
--

* What does `/` mean?
--

* Is `"use strict"` a string or a directive?
--

* How can I store my string efficiently?

--

* ...

--

**Hint** it depends.

--

Yeah, tokenization is hard. .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)]


---

### Step 3. Parse the tokens (1)

`[FunctionKeyword, Identifier(foo), ...]`

--

.center[⇓]

--

```yaml
FunctionDeclaration:
    isAsync: false
    isGenerator: false
    scope: ...,
    name:
        BindingIdentifier:
            name: "foo"
    params:
        - FormalParameters:
            items:
                - BindingIdentifier:
                    name: "x"
        rest: null,
    body: ...
```

---

### Step 3. Parse the tokens (2)

Exercise:

```javascript
var x = 10;
function foo(isReady) {
    if (isReady) {
        return x + 10;
    }
    // ...
}
```

What is the return of `foo(true)`?

--

**Hint** it depends.

---

### Step 3. Parse the tokens (3)

Parsing JS is hard:

```js
var x = 10;
function foo(isReady) {
    if (isReady) {
        return x + 10;
    }
}
console.log(foo(true)); // `20`

var x = 10;
function foo(isReady) {
    if (isReady) {
        return x + 10;
    }
    var x = 10;
}
console.log(foo(true)); // `NaN`
```

---

### Step 3. Parse the tokens (4)


* ...handle variables .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)];

--

* ...handle `this` .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)];

--

* ...handle `eval` .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)];

--

* ...handle `with` .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)];

--

* ...handle `"use strict"` .mini[![Would That It Were So Simple](img/wouldthatitweresosimple.gif)];

--

* also, you can't skip anything;

---

### Step 4. Wait, there's more!

* Perform safety-checks on the code.
* Generate browser-specific bytecode.
* Execute!

---

## So what can we make faster?

1. Download source file.
1. Decompress source file.
1. Convert encoding.
1. Tokenize.
1. Parse.
1. Perform safety-checks.
1. Generate Bytecode.
1. Start execution.

---

### Things people have tried

* Browser improvements
   * Lazy parsers (1)
   * Bytecode caching (2)
* JavaScript frameworks/toolchains
  * Minimizers (1)
  * Lazy loaders (3)
* Browser APIs
  * Wasm (3)
  * ServiceWorker loaders (3, 4)

.small[
(1) May decrease global performance.

(2) When the JS of the page doesn't change.

(3) Requires rewrite. Might not help.

(4) Hurts the web.
]

---

## Introducing the JavaScript Binary AST

---

### What is it?

* A proposal for the JavaScript language, by Mozilla, Bloomberg, Facebook.
* A new file format for JavaScript code.
* Smaller than .js, much faster to parse.
* **Not** uglified.
* **Not** a bytecode.

---

### Recall parsing


```yaml
FunctionDeclaration:
    isAsync: false
    isGenerator: false
    scope: ...,
    name:
        BindingIdentifier:
            name: "foo"
    params:
        - FormalParameters:
            items:
                - BindingIdentifier:
                    name: "x"
        rest: null,
    body: ...
```

That's an Abstract Syntax Tree (AST).

---

### Storing the AST

```js
1: "foo",
[
    /*FunctionDeclaration*/ 42,
    /*isAsync*/0,
    /*isGenerator*/0,
    ...,
    /*name*//*BindingIdentifier*/ 43,
    /*foo*/1,
    ...
]
```

(except compressed)

---

## What's the point?

---

### Things the browser needs to do.

1. Download binjs file.
1. Tokenize + Parse + Check **only what you use**.
1. Generate Bytecode.
1. Start execution.

---

### What is faster?

1. Smaller file (WIP).
1. Tokenization + Parsing can start earlier.
1. Trivial tokenization.
1. Format is **proof-carrying**.
  * Hard cases are already processed.
  * Checks are fast.
1. Parse **only** the code you execute, when needed.
1. Parse strings, identifiers, ... only **once**.
1. More opportunities for concurrency.

---

## Is there time for a demo?

---

## Calendar

---

### Early 2017

* Status: Proof of concept.
* Security: None.
* Speed: Extreme improvements (1).
* File size: Pretty good improvements (1).
* Standardization: Ecma step 1.

(1) No hard numbers because it's too early to make promises.

---

### Early 2018

* Status: Prototype 3.
* Security: As much as JavaScript, just easier to check.
* Reference implementation: ~90%.
* Firefox implementation: ~30%
* Speed: Not measured yet.
* File size: Improvements... but we want more.
* Specifications: High-level specifications ~90%.


---

### Hopefully late summer 2018

* Experimental deployment on a few major websites.
* Firefox implementation for opt-in users.
* Discussion with other browser vendors on further optimizations.

---

## Thanks for listening.

![It's that simple](img/simple.png)

### Any questions?