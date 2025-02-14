---
theme: default
# background: https://cover.sli.dev
background: /intro.png
# some information about your slides (markdown enabled)
title: Typescript Intermediate Concepts
info: |
  ## Slidev Starter Template
  Application grade coding.

fonts:
  sans: "chalet-new-york-sixty"
  serif: "Montserrat"
  mono: "chalet-new-york-sixty"

# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: true
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

<!-- # Typescript Intermediate Concepts

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  Let's dive in! <carbon:arrow-right />
</div> -->

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# What are we going to cover?

During this meetup we will dive into the following topics:

- **As const**
- **Indexed Access Types**
- **Satisfies Operator**
- **Type Predicate Inference**
- **Infer**
  <br>
  <br>

Read more about on [AA TS Training on Netlify!](https://sli.dev/guide/why)
<br>
Repo: [AA TS Training Material](https://typescript--aa-trainings.netlify.app/)
<br>
Slides bootstrapped using [Slidev Starter Template](https://sli.dev/themes/default.html)

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/features/slide-scope-style
-->

<style>
h1 {
  background-color: #2B90B6;
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Slido Time!

Have you heard of any of these concepts before?

<div grid="~ cols-2 gap-4" class="flex justify-center items-center">
<div class="flex flex-col items-center">

<img src="/slido-qna.png" alt="slido QnA" class="w-1/1.2 rounded shadow bg-white" />

</div>
<div class="flex flex-col items-center">

<img src="/agile-actors/icon_notes.png" alt="agile actors logo" class="w-1/2 rounded shadow " />
</div>
</div>
<Timer />

---

# As const

Very useful for ensuring immutability during build time.
Introduced in Typescript 3.4.

Why do we need this?

```ts {all|7|all} twoslash
const config = {
  name: "name",
  lastName: "lastName",
};

config.name = "whatever"; //Applicable!
```

<v-click>

Runtime immutability!

```ts twoslash
const config = {
  name: "name",
  lastName: "lastName",
} as const;

config.name = "whatever";
```

</v-click>

<div mt-20 v-click>

[Learn more](https://sli.dev/guide/animations#click-animation)

</div>

---

# As const VS Object.freeze

|                              |                         |                          |
| ---------------------------- | ----------------------- | ------------------------ |
| Feature                      | <kbd>as const</kbd>     | <kbd>Object.freeze</kbd> |
| Purpose                      | Type-level immutability | Runtime immutability     |
| Works with nested properties | Multi Level nesting     | Single Level nesting     |
| Runtime Impact               | ❌                      | ✅                       |

---

# Indexed Access Types

Access object properties using an index signature. In TS, an index signature is a way to define the shape of an object with keys that are not known at compile time.

```ts twoslash
type User = {
  id: number;
  name: string;
  isActive: boolean;
};

type UserName = User["name"];

type UserProperties = User["id" | "isActive"];
```

---

# Indexed Access Types Use Case

Extracting type from a value - real case scenario!

```ts twoslash
export const PERMISSIONS = [
  "view-document",
  "edit-document",
  "delete-document",
] as const;

export type Permission = (typeof PERMISSIONS)[number];
```

<!-- //Bonus Tip 🚀 - Extract into a utility type
export type ValueFrom<T> = T[keyof T];

//usage
export type Permission = ValueFrom<typeof PERMISSIONS>; -->

---

# Satisfies Operator Why ?

Introduced in TS 4.9 to ensure that some expression matches some type, but also want to keep the most specific type of that expression for inference purposes

```ts twoslash
type PersonName = "John" | "Jack" | "Justin";
type OtherDetails = {
  id: number;
  age: number;
};

type PersonInfo = PersonName | OtherDetails;

type Person = {
  myInfo: PersonInfo;
  myOtherInfo: PersonInfo;
};
const applicant: Person = {
  myInfo: "John",
  myOtherInfo: { id: 123, age: 22 },
};

applicant.myInfo.toUpperCase();
```

---

# Satisfies Operator

dynamic type inference...

```ts twoslash
type PersonName = "John" | "Jack" | "Justin";
type OtherDetails = {
  id: number;
  age: number;
};

type PersonInfo = PersonName | OtherDetails;

type Person = {
  myInfo: PersonInfo;
  myOtherInfo: PersonInfo;
};

const applicantWithSatisfies = {
  myInfo: "John",
  myOtherInfo: { id: 123, age: 22 },
} satisfies Person;

applicantWithSatisfies.myInfo.toUpperCase();
```

---

<div class="flex justify-center items-center h-full">
  <img src="/const-nuance.jpg" alt="const nuance" class="w-1/2 rounded shadow " />
</div>

---

# Satisfies Use case

Strongly type the URL params!

```ts twoslash
type UrlParams = {
  id: string;
  name: string;
};

const params = new URLSearchParams({
  id: "123",
} satisfies UrlParams);
```

---

# As const VS satisfies

|                                                         |                     |                      |
| ------------------------------------------------------- | ------------------- | -------------------- |
| Feature                                                 | <kbd>as const</kbd> | <kbd>satisfies</kbd> |
| Purpose                                                 | Solid immutability  | Deeply infer type    |
| No need to import custom library types for literal keys | ✅                  | ❌                   |
| IDE DX (squiggly lines)                                 | 🔄                  | ✅                   |

<v-click>

As a suggestion I would recommend to use satisfies as much as possible for better developer experience except if you want
solid immutability or have to deal with literal unions library types.

</v-click>

---

<div class="flex justify-center items-center h-full">
  <img src="/operators.jpg" alt="operators" class="w-1/2 rounded shadow " />
</div>

---

# Type predicate inference

It should be known by now that we can narrow down a type using if statements

````md magic-move
```ts
//some code

function isString(value: unknown) {
  return typeof value === "string";
}
```

```ts
//some code

function isString(value: unknown) {
  return typeof value === "string";
}

//then use it as
if (isString(text)) {
  //Pre TS 5.5 text is "string" | "number" ...
  console.log(text);
}
```

```ts
//some code

function isString(value: unknown) {
  return typeof value === "string";
}

//then use it as
if (isString(text)) {
  //Post TS 5.5 text is "string"
  console.log(text);
}
```

```

```
````

---

# Type predicate inference Use case

Array filter with inference (syntax matters!)

````md magic-move
```ts
//infers as string[]
const onlyTextArray = ["a", "b", "c", null].filter((member) => {
  return typeof member === "string";
});
```

```ts
//infers as string[]
const onlyTextArray = ["a", "b", "c", null].filter((member) => {
  return typeof member === "string";
});

//infers as string[]
const onlyTextArray2 = ["a", "b", "c", null].filter((member) => {
  return member !== null;
});
```

```

```
````

<v-click>

````md magic-move
```ts
//infers as (string | null)[] 💣 - DOESN'T WORK
const onlyTextArray3 = ["a", "b", "c", null].filter((member) => {
  return !!member;
});
```

```ts
//infers as (string | null)[] 💣 - DOESN'T WORK
const onlyTextArray3 = ["a", "b", "c", null].filter((member) => {
  return !!member;
});

//infers as (string | null)[] 💥 - DOESN'T WORK
const onlyTextArray4 = ["a", "b", "c", null].filter(Boolean);
```
````

</v-click>

<v-click>

🎁 Try to do the same for an array of objects at home! (hint use type guard)

</v-click>

---

# Infer

Used in conditional types to extract and infer specific types from complex structures (pattern matching)

```ts {all} twoslash
const func = (x: number): string => x.toString();

type FuncReturnType = ReturnType<typeof func>;
```

<v-click>

Let's make it ourselves!

```ts twoslash
const func = (x: number): string => x.toString();

//first example with Custom Implementation of ReturnType
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type ReturnTypeOfFunc = GetReturnType<typeof func>;

//second example with Promise
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type Result = UnwrapPromise<Promise<number>>;
```

</v-click>

---

# Infer Use case

Extract the category from the movie routes! (verb before the colon on key)

````md magic-move
```ts
const movieRoutes = {
  "watched:get": "/watched/get",
  "watched:rate": "/watched/rate/:movieId",
  "favorites:get": "/favorites/get",
  "favorites:add": "/favorites/add/:movieId",
} as const;
```

```ts
const movieRoutes = {
  "watched:get": "/watched/get",
  "watched:rate": "/watched/rate/:movieId",
  "favorites:get": "/favorites/get",
  "favorites:add": "/favorites/add/:movieId",
} as const;

//extract the type
type MovieRoutes = typeof movieRoutes;
```

```ts
const movieRoutes = {
  "watched:get": "/watched/get",
  "watched:rate": "/watched/rate/:movieId",
  "favorites:get": "/favorites/get",
  "favorites:add": "/favorites/add/:movieId",
} as const;

//extract the type
type MovieRoutes = typeof movieRoutes;

//extract keys
type MovieRoutesKeys = keyof MovieRoutes;
```

```ts
const movieRoutes = {
  "watched:get": "/watched/get",
  "watched:rate": "/watched/rate/:movieId",
  "favorites:get": "/favorites/get",
  "favorites:add": "/favorites/add/:movieId",
} as const;

//extract the type
type MovieRoutes = typeof movieRoutes;

//extract keys
type MovieRoutesKeys = keyof MovieRoutes;

//start pattern matching! ("watched" | "favorites" )
type MovieCategories = MovieRoutesKeys extends `${infer Category}:${string}`
  ? Category
  : never;
```
````

<v-click>

What will happen if we add this key value pair to the movieRoutes object? ('unknown': '/unknown')

</v-click>

---

# Slido Time!

Does Typescript use nominal or structural typing? (Senior Engineer interview question 🤔)

<div grid="~ cols-2 gap-8">
<div>

Think about this for a sec!

```ts twoslash
let ball = { diameter: 10 };
let sphere = { diameter: 20 };

sphere = ball;
```

</div>
<div>

<img src="/slido-qna.png" alt="slido-qna" class="w-1/2 rounded shadow bg-white mt-14" />

<Timer />
</div>
</div>

---

# Acknowledgements

<div grid="~ cols-2 gap-4" class="flex justify-center items-center">
<div class="flex flex-col w-1/2">
Kudos for the training material 🙌
<ul mt-5>
<li>Andreas Ntokas</li>
<li>Marianna Printezi</li>
<li>Aris Bousios</li>
</ul>

</div>
<img src="/agile-actors/icon_light_bulb_rev.png" alt="agile actors logo" class="w-1/3 rounded shadow " />
</div>

Learn More with our internally developed Typescript Training!

[Stackblitz](https://stackblitz.com/~/github.com/agileactors/ts-workshop?file=README.md) &
[Netlify](https://typescript--aa-trainings.netlify.app/)

---

# QnA Session

## Liked this meetup ? Add some rating ⭐️!

<br/>
<div grid="~ cols-2 gap-4" class="flex justify-center items-center">
<div class="flex flex-col items-center">

<img src="/slido-qna.png" alt="slido QnA" class="w-1/1.2 rounded shadow bg-white" />

</div>
<div class="flex flex-col items-center">

<img src="/agile-actors/icon_Question mark_rev.png" alt="agile actors logo" class="w-1/2 rounded shadow " />
</div>
</div>

<div class="flex justify-end">
  <PoweredBySlidev />
</div>

```

```
