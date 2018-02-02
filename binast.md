# Making the web faster with the JavaScript Binary AST

## David Teller, Mozilla

## Fosdem 2018

---

## The problem

- Google Sheets, Google Docs, Yahoo!, LinkedIn, Facebook: 3-7 compressed Mb+ JS code.
- Updated *very* often.
- Facebook: 500-900ms just *parsing* JavaScript (Chrome & Firefox).

---

## Why is JavaScript loading so slow?

---

### 1. Get the code

* Download and decompress source file.
* Covert encoding.
...

---

### 2. Tokenize the text (1)


```js
function foo(x) {
    return y;
}
```

=>

* `Token.FunctionKeyword`
* `Token.Identifier("foo")`
* `Token.LPar`
* `Token.Identifier("x")`
* `Token.RPar`
* `Token.RBrace`
* ...

---

### 2. Tokenize the text (2)

Tokenizing JS is hard:

* What does `/` mean?
* Is `for` an identifier or a keyword?
* How can I store my string efficiently?
* Is `"use strict"` a string or a directive?
* ...

**Hint** it depends.

---

### 3. Parse the tokens (1)

```rust
FunctionDeclaration {
    isAsync: false,
    isGenerator: false,
    scope: ...,
    name: BindingIdentifier {
        name: "foo"
    }
    params: FormalParameters {
        items: [
            BindingIdentifier {
                name: "x"
            }
        ]
        rest: null,
    }
    body: FunctionBody {
        directives: [],
        statements: [
            ReturnStatement {
                expression: {
                    IdentifierExpression {
                        name: "y"
                    }
                }
            }
        ]
    }
}
```

---

### 3. Parse the tokens (2)

Exercise:

```js
var x = 10;
function foo(isReady) {
    if (isReady) {
        return x + 10;
    }
    // ...
}
```

What is the return of `foo(true)`?

**Hint** it depends.

---

### 3. Parse the tokens (3)

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

### 3. Parse the tokens (4)

Parsing JS is hard:

* syntax depends on `"use strict"`;
* variables and `this` are so complicated, man!;
* result depends on the presence of `eval`, `with`, ...;
* you can't skip anything;

---

### 4. Wait, there's more!

* Generate browser-specific bytecode.
* Execute!

---

## So what can we make faster?

1. Download source file.
1. Decompress source file.
1. Convert encoding.
1. Tokenize.
1. Parse.
1. Generate Bytecode.
1. Start execution.

---

### Things people have tried

* Browser improvements
  * Lazy parsers (1)
  * Bytecode caching (4)
* JavaScript frameworks/toolchains
  * Lazy loaders (2)
  * Minimizers (1)
* Browser APIs
  * ServiceWorker loaders (2, 3)
  * Wasm (2)

(1) Impacts global performance.

(2) Requires rewrite.

(3) Hurts the web.

(4) Works when the JS of the page doesn't change.

---

## Introducing the JavaScript Binary AST

---

### Objectives

* A new file format for JavaScript code.
* Smaller than .js, much faster to parse.
* **Not** uglified.

---

### Recall the AST?

```rust
FunctionDeclaration {
    isAsync: false,
    isGenerator: false,
    scope: ...,
    name: BindingIdentifier {
        name: "foo"
    }
    params: FormalParameters {
        items: [
            BindingIdentifier {
                name: "x"
            }
        ]
        rest: null,
    }
    body: FunctionBody {
        directives: [],
        statements: [
            ReturnStatement {
                expression: {
                    IdentifierExpression {
                        name: "y"
                    }
                }
            }
        ]
    }
}
```

---

### Transform it to this:

```js
1: "foo",
[/*FunctionDeclaration*/ 42,  /*isAsync*/0, /*isGenerator*/0, ..., /*name*//*BindingIdentifier*/ 43, /*foo*/1, ...]
```

(except compressed)

---

### Flow

1. Download binjs file.
1. Tokenize + Parse **only what you use**.
1. Generate Bytecode.
1. Start execution.

---

### What is faster?

1. Smaller file (1).
1. Parsing can start earlier.
1. Trivial tokenization.
1. Format is **proof-carrying**.
  * Hard cases `this`, `var`, `let`, ... are already processed.
1. Parse only the code you execute, when needed.
1. Parse strings, identifiers, ... only once.
1. More opportunities for concurrency.

(1) Hopefully.

---

### Calendar

* Early 2017: Proof of concept
  * No security.
  * Extreme speed improvements (1).
  * Pretty good size improvements (1).
* Early 2018: Working on prototype 3
  * Full security (WIP)
  * Speed not measured.
  * Size improvements... need improvements (1).
* Late summer 2018
  * Tests on major websites.

(1) No hard numbers because it's too early to make promises.

---

## Is there time for a demo?


---

## Thanks for listening.

### Any questions?