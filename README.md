# Typescript

1. Syntax
2. Interop med eksisterende kode
3. Verktøy
4. Oppsett
4. Leke

Typescript legger til compile-time typer til javascript. Klasser, interfaces etc.

## Syntax

Typescript er et supersett av Javascript, all kode som er gyldig JS er også gyldig TS. Filendelse for typescript er `.ts`.

### Basistyper

#### Booleans

```ts
let isDone: boolean = false;
```

#### Number

```ts
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

#### String

```ts
let color: string = "blue";
color = 'red';
```

Strings kan også deklareres med backticks, som i ES6

```ts
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`;
```

#### Array

```ts
let list: number[] = [1, 2, 3];
```

#### Tuple

```ts
let x: [string, number];

x = ["hello", 10]; // OK
x = [10, "hello"]; // Error
```

#### Enum

```ts
enum Color {Red, Green, Blue}
let c: Color = Color.Green;
```

#### Any

Hvis man interagerer med f.eks vanlig JS eller andre biblioteker som ikke har type definitions.

```ts
let notSure: any = 4;
notSure = "denne kan være hva som helst";
notSure = false; // Fremdeles hva som helst
```

#### Void

```ts
function warnUser(): void {
    alert("No return!");
}
```

#### Casting

```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

Kan også bruke `as` syntaks, som er ekvivalent.

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

### Variabeldeklarasjoner

Typescript støtter `let` og `const`, akkurat som vanlig ES6.

#### Destructuring

Som i ES6 er destructuring støttet.

```ts
let input = [1, 2];
let [first, second] = input;
console.log(first); // 1
console.log(second); // 2
```

Destructuring fungerer også med funksjonsargumenter:

```ts
function f([first, second]: [number, number]) {
    console.log(first); // 1
    console.log(second); // 2
}
f([1, 2]);
```

`...rest` fungerer også:

```ts
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // 1
console.log(rest); // [ 2, 3, 4 ]
```

Destructuring fungerer også med objekter.

```ts
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};
let { a, b } = o;

console.log(a) // "foo"
console.log(b) // 12
```

#### Object spread

Med lister:

```ts
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];

console.log(bothPlus) // [0, 1, 2, 3, 4, 5]
```

Med objekter:

```ts
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };

console.log(search);
/*
{
    food: "rich",
    price: "$$",
    ambiance: "noisy"
}
*/
```

### Interfaces

Typescript støtter interfaces, litt på samme måte som java/c#

```ts
interface LabelledValue {
    label: string;
}

function printLabel(labelledObj: LabelledValue) {
    console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj); // OK

let secondObj = { size: 10 }
printLabel(secondObj) // Fail
```

#### Optionals

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}
```

#### Readonly properties

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
p1.x = 5; // Fail
```

#### Funksjoner

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}
```

Her betyr ikke selve argumentnavnene noe for typesjekkingen, kun typene. Så dette fungerer også:

```ts
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
    let result = src.search(sub);
    return result > -1;
}
```

#### Arv av interfaces

```ts
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

Man kan også utvide flere interfaces samtidig:

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### Klasser

Typescript lar oss bruke klasser slik vi kjenner de fra objektorienterte språk.

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

#### Implementering av interfaces

```ts
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

#### Arv

```ts
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

#### Modifikatorer

Modifikatorene `public`, `private`, og `protected` fungerer på samme måte som vi er vant til fra `C#`. `public` er tilgjengelig for alle. `private` er kun tilgjengelig innad i klassen, mens `protected` er tilgjenglig fra klassen og dens barn. Alle properties er implisitt deklarert som `public` hvis man ikke spesifiserer det.

```ts
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

Properties deklarert med `readonly` kan kun settes i konstruktøren.

```ts
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // Fail
```

#### Gettere/Settere

Typescript tilgjengeliggjør gettere og settere.

```ts
class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        this._fullName = newName;
    }
}
```

#### Statics

Klasser kan ha statiske properties.

```ts
class Test {
    static testVar = "foo";
    constructor () { }
}

console.log(Test.testVar); // "foo"
```

#### Abstrakte klasser

```ts
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log("roaming the earth...");
    }
}

class Dog extends Animal {
    constructor() {
        super();
    }
}

let fido = new Dog();
fido.move() // "roaming the earth..."
```

### Funksjoner

Som vist over, funksjoner har også typer.

```ts
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number {
    return x+y;
};
```

#### Optional og default parametere

```ts
function buildName(firstName: string, lastName?: string) {
    // ..
}
```

```ts
function buildName(firstName: string, lastName = "Smith") {
    // ...
}
```

### Generics

Typescript har generics som fungerer på samme måte som C#

```ts
function identity<T>(arg: T): T {
    return arg;
}
```

```ts
class Greeter<T> {
    greeting: T;
    constructor(message: T) {
        this.greeting = message;
    }
    greet() {
        console.log(this.greeting);
    }
}

let greeter = new Greeter<string>("Hello, world");
greeter.greet() // "Hello, world"
```

#### Constraints

```ts
interface IHasLength {
    length: number;
}

function loggingIdentity<T extends IHasLength>(arg: T): T {
    console.log(arg.length); // Vet at T har length, siden den implementerer IHasLength
    return arg;
}
```

### Mer

Typescript har mye mer, hele dokumentasjonen finnes på https://www.typescriptlang.org/docs/

## Interop med biblioteker

For å bruke Typescript med eksterne biblioteker trenger man type definitions. Disse finner man på npm under `@types`-taggen. For å bruke typescript med node legger man f.eks in typer for node med `npm install --save @types/node`. Mens for react må man legge inn `@types/react` og `@types/react-dom`.

Alle typedefinitions finnes hos DefinetlyTyped på http://definitelytyped.org/

### Skrive egne type definitions

Alle typedefinitions skrives i egne filer med filendelse `.d.ts`. Her eksporterer man typene som skal tilgjengeliggjøres for typescript-compileren.

Eksempel:

```ts
export interface ManagementClientOptions {
  token: string;
  domain?: string;
}

export interface UserMetadata { }
export interface AppMetadata { }

export interface UserData {
  connection: string;
  email?: string;
  username?: string;
  password?: string;
  phone_number?: string;
  user_metadata?: UserMetadata;
  email_verified?: boolean;
  app_metadata?: AppMetadata;
}
```

Når typescript kompileres scannes alle undermapper for `.d.ts`-filer slik at kompilatoren vet hvilke typer som finnes.

## Verktøy

Typescript kan utvikles med stort sett alle verktøy, og de fleste editorer har verktøystøtte.

### Atom

I Atom kan pakken `atom-typescript` legges inn for å få typescript støtte rett i editoren. Hvis man i tillegg bruker linting kan `linter-tslint` legges inn.

### Visual Studio Code

VSCode har også en typescript-pakke som kan brukes og har verktøystøtte rett i editor.

## Oppsett

For å sette opp bruk av typescript må man legge inn npm-pakken `typescript`. Denne kan
enten legges inn globalt (`npm install -g typescript`) eller bare på prosjektnivå.

I begge tilfeller vil man få kommandolinjesupport for å kompilere typescript ved hjelp av
kommandoen `tsc`.

### tsc

`tsc` finner nærmeste `tsconfig.json`, og bruker denne for å finne ut hva som skal kompileres.

Et eksempel på `tsconfig.json`:

```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node"
  },
  "exclude": [
    "dist",
    "node_modules"
  ]
}
```

Denne konfigurasjonen sier at typescript skal kompileres fra `index.ts` i samme mappe som
`tsconfig.json`. Målet for kompileringen skal være `es5`-kompatibel kode, og koden skal
legges i `dist`-mappa.

### Bruk med webpack

For å bruke typescript med webpack kan man bruke `awesome-typescript-loader` istedenfor `babel-loader`
som man er vant til fra JS-verden.

Eksempel på webpack-config:

```js
module.exports = {
    entry: "./src/index.ts",
    output: {
        filename: "bundle.js",
        path: __dirname + "/dist"
    },
    resolve: {
        // Add '.ts' and '.tsx' as resolvable extensions.
        extensions: [".ts", ".tsx", ".js", ".json"]
    },
    module: {
        rules: [
            // All files with a '.ts' or '.tsx' extension will be handled by 'awesome-typescript-loader'.
            { test: /\.tsx?$/, loader: "awesome-typescript-loader" },
        ]
    },
};
```

## Mer lesning

* https://www.typescriptlang.org/
* https://atom.io/packages/atom-typescript
* https://code.visualstudio.com/docs/languages/typescript
* https://github.com/Microsoft/TypeScript/wiki

## Leke

Dette prosjektet kan kjøres ved hjelp av `npm run build` som bygger typescript som ligger i `index.ts`.

Alternativt har typescript en egen sandbox som kan finnes på `https://www.typescriptlang.org/play/`.
