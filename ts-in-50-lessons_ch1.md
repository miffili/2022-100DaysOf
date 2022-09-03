# TypeScript in 50 Lessons

* TypeScript is superset of aka language on top of JavaScript
* TS is a _structural type system_

[_CodeSandbox for Chapter 1_](https://codesandbox.io/s/chapter-1-forked-4dmzqn?file=/index.js)

## 1.1 Red Squiggly Lines

* purpose
  * pointing out potential errors before code hits production
  * provides code analysis for JavaScript
* VSCode runs TypeScript out of the box; other IDEs can use plug-ins "TypeScript language server"
* in JS file, it can be activated by adding `//@ts-check`in the first line
* hovering over erros indicated by red squiggly line will show us an explanation of the problem

## 1.2 Hunting Bugs

* what TypeScript does:
  * making sure we're using the right names AKA _type check_
    * *CTRL + Space* in an object will give you autocomplete
  * making sure we're using the right type AKA _type inference_
    * when initializing a variable, TS interferes its type
    * when we then try to assign a different type it throws an error
    * also works on return types of methods
  * _semantic checks_
    * e.g. a loop initializes variable _i_ as constant -> will fail, as it cannot reassign i -> fixed with `let i = 0`
    * suggests fixes automatically

## 1.3 Types

> A **type** is a classification of data that defines the operations that can be done on that data, the meaning of the data, and the set of allowed values. Typing is checked by the compiler and/or run time to ensure the integrity of the data, enforce access restrictions, and interpret the data as meant by the developer.

* example
  * `"Hello World"`is a **string**
  * `1234` is a number
  * `true` is a boolean
* type defines how value shoule be interpreted, but also affords us operations
  * string can be altered by `.toUpperCase()`
  * number can be multiplied by 10
* JS has primitive types (number, string & boolean) & composite data types (like objects, arrays, functions), as well as symbols
* JS is **weakly typed**, meaning you can switch type as you go or combine values of different types
  * ```javascript
    let val = { a : 3 } + 5
    // [object Object]5! What does that even mean?
    ```
* TS is **strongly typed**, meaning you define a type, you'll have to stick with it (operators on values of different types will throw error as well)
* shapes:
  * primitive types are limited in range of values, e.g. boolean can be `true`, `false`, `undefined` or `none`
  * composite types like objects, arrays are defined by not only ranges of values but also by so called shaped
  * shapes tell more about the structural features of a type
    * types & names of properties of an object
    * types & names of parameters of a function
    * types & indexes of elements in an array
    * e.g. a `person` object
    * ```TypeScript
      const person = {
        firstName: 'Jack',
        lastName: 'Sparrow',
        age: 39
      }
      ```
      
🔀 taking a little detour (aka google search) about `structural type system`
* nominal (C++, Java) vs structural (Haskell, TS) type system
* from [TypeScript "Structural Typing Playgroud"](https://www.typescriptlang.org/play/typescript/language/structural-typing.ts.html)
  * ```
    // TypeScript is a Structural Type System. A structural type
    // system means that when comparing types, TypeScript only
    // takes into account the members on the type.

    // This is in contrast to nominal type systems, where you
    // could create two types but could not assign them to each
    // other.
    ```

## 1.4 Adding Types with JSDoc

* tool to anotate code using comments
```javascript
    /**
      * Adding two numbers. This annotation tells TypeScript
      * which types to expect. Two parameters (params) of
      * type number and a return type of number
      *
      * @param {number} numberOne
      * @param {number} numberTwo
      * @returns {number}
    */
    function addNumbers(numberOne, numberTwo) {
     return numberOne + numberTwo
    }
```
* usually JSDoc runs over annotated source code & create HTML documentation, TypeScript uses same annotations to get more information about intented types
* custom types
  * in JS objects can be declared on the run: intialize empty object with `const x = {}`, later define `x.hey = 'Ho'` & boom! new property
  * example:
  * ```javascript
      /**
        * typedef {Object} StorageItem
        * @property {number} weight
        * typedef {Object} ShipStorage
        * @property {number} max
        * @property {StorageItem[]} items
      */
      
      const storage = {
        max: undefined,
        items: []
       }
     ```
 > Types signal intention, define a contract, and make sure we use our program code in the way it was supposed to be.

---

## 1.5 Type Declaration Files

* up until now: tooling part of TypeScript – a type-checker, a language server giving feedback to code editors
* TypeScript as a programming language
	* calls itself superset of JavaScript
	* can't be executed by a a browser or Node.js

* custom type declarations
	* can be very explicit about the shape
	```typescript
		type ShipStorage = {
			max: number,
			items: StorageItem[]
		}

		const storage = {
			max: 6000,
			items: []
		}
	```
	* `storage` object has the _shape_ of a `ShipStorage` type
	* type has a certain _ structure_
* .d.ts files
	* aka type declaration files
	* add all custom types, but no extra program code
	* _types.d.ts_ sits next to main JavaScript file
	* to make them available export types
	```typescript
		export type StorageItem = {
			weight: number
		}
		
		export type ShipStorage = {
			max: number,
			items: StorageItem[]
		}
	```
	* point to types by importing them in JSDoc comments
	```javascript
		/** @typedef { import('./types.d').ShipStorage } ShipStorage */
		/** @typedef { import('./types.d').StorageItem } StorageItem */
	```
	* JS code hasn't changed -> fundamental principle of TypeScript: as easy as possible to add types to existing code

* so far the simplest process:
	* activate TS with `// @ts-check` 
	* use JSDoc comment type annotations for all primitive & composite types
	* create custom type definitons in type declaration files & load them as needed in JSDoc type annotations

## 1.6 Ambient Declaraion Files

* make globals known & defined, we can use ambient declarations
* these types are encompassing, exitsing & present on all sides
* in `ambient.d.ts` next to main JS file we can define function heads, global objects, variables we need throughout program
	* e.g. a global flag like `isDevelopment` variable that is of type `boolean` & lets us log debug information
	```typescript
		declare const isDevelopment: boolean
	```
* `declare` is a keyword that tells TS that this constant exists
* when using a library like jQuery, custom files can be installed via npm
	```bash
		npm i @types/jquery
	```
* types are being provided by a big community contributing custom types to almost all libraries that hasn't been written in TS

## 1.7 Tooling

* VSCode is set up to work with TypeScript out of the box, it's the "perfect TypeScript editor"
* for others like Sublime, VIM or Atom the tooling is also available
* tsc
	* install TypeScript globally with `nom install --location=global typescript`
		* with it we get tsc, the TS compiler
		* compiles TS code to JS
		* can also type-check JS programs outside of any editor
		* as with any compiler flags & configurations can be set
		* move type declaration files to folder called _@types_
		* initialize project by running `tsc --init` -> create prefilled _tsconfig.json_ (main config file for TS)
		* tsc & TypeScript refer to this file
* tsconfig.json
	* prefilled _tsconfig.json_ pretty good to get started, but let's make adjustments
		* `target`: defines target language specification of JS TS should be compiled to (`ES2015`, `ESNEXT`, ...)
		* `module`: how should modules be treated (`commonJS`, `es2020`, ...)
		* `esModuleInterop`: strongly connected to module; want to mix modules from different modules systems, set this flag to true
		* `allowJS`: allow reference to regular JS files
		* `checkJS`: similar to `//@ts-check`; tells TS to type-check JS files
* type-check code with `tsc --noEmit`
* `noEmit` tells TS to _not_ output files
* watch mode can be enabled with `tsc --noEmit --watch`
