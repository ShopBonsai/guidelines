# Naming

## Variable

- Use camelCase for variable names.

`eslint: camelcase`

```javascript
const fooVar
```

- Do not use trailing or leading underscores.

```javascript
// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
const _firstName = 'Panda';

// good
const firstName = 'Panda';
```

> There are exceptions for parameters (see: Parameter)

- Acronyms and initialisms should always be all uppercased, or all lowercased.

```javascript
// bad
import SmsContainer from './containers/SmsContainer';

// bad
const HttpRequests = [
  // ...
];

// good
import SMSContainer from './containers/SMSContainer';

// good
const HTTPRequests = [
  // ...
];

// good
const http = '';

// also good
const httpRequests = [
  // ...
];

// best
import TextMessageContainer from './containers/TextMessageContainer';

// best
const requests = [
  // ...
];
```

- Avoid using company or technology specific names in code, filenames, etc.

```javascript
// bad
const contentfulUrl = '...';

// good
const imageUploadUrl = '...';

// bad
const getNodesFromNeo4j = () => {};

// good
const getNodesFromGraph = () => {};
```

## Arrays

- Use plural and singular forms correctly. Array variables should always have plural names (e.g. users, promises but never const user = []). This means avoiding vague names like collection and list, and specifying exactly what the list is of (e.g. $elements, links)

```javascript
// bad
const user = [];
const userList = [];
const userCollection = [];

// good
const users = [];
```

- Always use T[] or readonly T[] for all array types.

```javascript
//bad
const users: Array<string> = ['a', 'b'];

// good
const users: string[] = ['a', 'b'];
```

## Boolean

- Use prefix (`is`, `has`, `should`, ...) when using a boolean.

```javascript
// bad
const billable = true;

// good
const isBillable = true;
```

> This is also applicable for parameter names.

## Constant

- You may optionally uppercase a constant only if it (1) is exported, (2) is a const (it can not be reassigned), and (3) the programmer can trust it (and its nested properties) to never change.
- All const variables
  - Uppercasing should not be used for constants within a function. It should only be used for constants outside of functions however.
- Nested objects
  - Uppercase at the top level of export (e.g. EXPORTED_OBJECT.key) and maintain that all nested properties do not change.

```javascript
// bad
const calculateDiff = () => {
  const PRIVATE_VARIABLE = "should not be unnecessarily uppercased within a function"
  ...
}

// bad
export let REASSIGNABLE_VARIABLE = "do not use let with uppercase variables"

// ---

// allowed but does not supply semantic value
export const apiKey = "SOMEKEY"

// better in most cases
export const API_KEY = "SOMEKEY"

// ---

// bad - unnecessarily uppercases key while adding no semantic value
export const MAPPING = {
  KEY: "value",
}

// good
export const MAPPING = {
  key: "value",
}
```

## Defaults

- Use prefix when defining base or default constant value.

```javascript
// bad
const TIMEZONE_DEFAULT = 'UTC';
const PRICE_BASE = 12;

// good
const DEFAULT_TIMEZONE = 'UTC';
const BASE_PRICE = 12;
```

## Function

- Use camelCase for function names.

```javascript
function barFunc() {}
```

- Include all the words needed to avoid ambiguity for a person reading code where the name is used. Spell out words entirely instead of using acronyms. Although the names are longer, this helps convey the meaning of your API. Exceptions might be single letter identifiers, like the coordinate names x, y, z.

```javascript
// long but self-explanatory
export function doSomethingAwesome() {}

// not clear
export function dSA() {}
```

- Prefer function names that make use sites form grammatical English phrases.

```javascript
// bad
capitaliseSomeWords();

// good
capitalizingNouns();
```

- Omit needless words.
  - Think about it like this: “If I remove this word could the function be interpreted in a wrong way?”

```javascript
// ok
const addStatusToMerchantOrder = () => {
  // ...
};

// better
const addMerchantOrderStatus = () => {
  // ...
};
```

## Curried functions

- Curried functions shouldn't have a specific naming guideline and should obey the regular guidelines. The way the function works can be easily derived from its index signature.

```javascript
// bad
const makeConvertToCurrency = () => () => {
  // ...
};

// good
const convertToCurrency = (conversionRate: number) => (amount: number) => {
  // ...
};
```

- Executing curried functions

```javascript
// good
const toCurrencyRates = convertToCurrency({ CAD: 1, USD: 0.75 })
const currency = toCurrentRates(amount)

// good
const toCurrencyRates = convertToCurrency({ CAD: 1, USD: 0.75 })
const currencies = orders.map({amount} => toCurrencyRates(amount))

// Good if it does not reduce readability
const currency = convertToCurrency({ CAD: 1, USD: 0.75 })(amount)
```

## Callback functions

- Do not use `Fn`, `Func` or any other prefix/suffix for functions accepting a callback function. These functions should have a regular name indicating when these will get called.

```javascript
// bad
export const getName = (id: string, callbackFn) => {
  ...
  callbackFn();
};

// good
export const getName = (id: string, done) => {
  ...
  done()
}
```

## Prefixes

- **Get**
  Retrieving an existing entity.

  ```javascript
  // bad
  export function temperature() {
    ...
  }

  // good
  export function getTemperature() {
    ...
  }
  ```

- **Create**
  Create a new entity.

  ```javascript
  const createMerchantOrder = (order: IMerchantCreate) => {...}
  ```

- **Update**
  Update an existing entity.

  ```javascript
  const updateMerchantOrder = (id: string, values: IMerchantUpdate) => {...}
  ```

- **Remove**
  Remove an existing entity
  ```javascript
  const removeMerchantOrder = (id: string) => {...}
  ```

## Parameter

- Omit needless words. Every word in a name should convey salient information at the use site.

```javascript
// bad
export function convertCurrency(originalCurrency:ICurrency, currencyToConvertTo: ICurrency)

// good
export function convertCurrency(from: ICurrency, to:ICurrency)
export function convertCurrency(source: ICurrency, target:ICurrency)
```

- Use context clear names and only add extra explanation when needed. If the sole entity can be derived from the function name it shouldn't be prefixed or appended by that entity name.

```javascript
// bad
export function updateMerchant(merchantId: string, ...)

// good
export function updateMerchant(id: string, ...)
export function updateMerchant(publicId: string, ...)
export function addToOrder(id:string, merchantOrder: IMerchantOrder)

// good - applicable to object parameters
export function createOrder ({ orderId, products }: ICart) {
  // ...
}
```

- Use values for input values.

```javascript
// bad
export function createOrder(orderToCreateInput: IOrderCreate)

// good
export function createOrder(values: IOrderCreate)
```

- Only use `_` when indicating a parameter is unused. This should be used in exceptional cases.

```javascript
// bad
export function addFixedCost(a, _b) {
  //...
  // return a + FIXED_COST
}

// good
// This is a function requiring to have these parameters due to an external library
export function isAuthenticated(req: Request, _res: Response, next:NextFunction) {
  //...
  ... Read out req properties
  next()
}
```

## Interface

- Use PascalCase for name.
- Use camelCase for members.
- Prefix with "I"

```javascript
interface IMerchantOrder {
  firstName: string;
}
```

## Operations

### Input Values

- Append the name of the operation at the end of the interface it we're talking about CRUD values.

```javascript
interface IMerchantOrderCreate {
  firstName: string;
}

interface IMerchantOrderUpdate {
  firstName: string;
}
```

- Use base operation type if operations have overlapping properties
- Use `input` whenever they’re not a specific CRUD value but are just regular input values.

```javascript
interface IMerchantOrderInput {
  firstName: string;
}

interface IMerchantOrderCreate extends IMerchantOrderInput {}
interface IMerchantOrderUpdate extends IMerchantOrderInput {
  lastName: string;
}
```

> When there are only 1 or 2 properties different try to use Typescript Utility Types such as [Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittk).

### Output Values

- Services return general output interfaces.

```javascript
interface IMerchantOrder {
    id: string
    name: string
}
```

- Repositories will always return a plain javascript object or class without database operation functions

```javascript
export class MerchantOrder extends Base {
  @prop({ required: true })
  name!: string;
}

// Repository
export const getById = (id:string): Promise<MerchantOrder> {
  return DBMerchantOrder.findById(id);
}
```

> Example uses typegoose.

- When using ActiveRecord pattern (such as with mongoose and/or typegoose) prefix class with DB

```javascript
export const DBMerchantOrder = getModelForClass(MerchantOrder);
await DBMerchantOrder.create(...)
```

> Example uses `getModelForClass` from typegoose.

## Type

- Use PascalCase for name.
- Use camelCase for members.
- **Do not prefix** with "I"

```javascript
type OrderStatus = 'draft' | 'inReview' | 'done';
```

## Enum

- Singular
- Use PascalCase for enum names
- Use PascalCase for enum members
- Use camelCase for string values

```javascript
enum Color {
  Red = "red"
}
```

## Generic

By convention, type parameter names are single, uppercase letters. This stands in sharp contrast to the variable naming conventions that you already know about, and with good reason: Without this convention, it would be difficult to tell the difference between a type variable and an ordinary class or interface name.

The most commonly used type parameter names are:

- E – Element (used extensively by the Java Collections Framework)
- K – Key
- N – Number
- T – Type
- V – Value
- S,U,V etc. – 2nd, 3rd, 4th types

```javascript
// bad
function updateUser<UnknownType>(entity: UnknownType) {
  // ...
}

// good
function updateUser<T>(entity: T) {
  // ...
}
```

## Files

- Use camelCasing
- Singular naming for filenames
  - Exceptions
    - `fixtures.ts`
    - `interfaces.ts`
    - `constants.ts`
- Plural naming for folder names

More information can be found [here](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Structure_suo82).

## Databases

More information can be found [here](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Databases_su3Xt).

## Sources

- https://github.com/basarat/typescript-book/blob/master/docs/styleguide/styleguide.md
- https://makecode.com/extensions/naming-conventions
- https://github.com/airbnb/javascript/
- https://www.nexedi.com/Javascript-Naming_Conventions
- https://www.robinwieruch.de/javascript-map-array
- https://help.anylogic.com/index.jsp?topic=%2Fcom.anylogic.help%2Fhtml%2Fcode%2FConventions.html
- https://gist.github.com/donnut/fd56232da58d25ceecf1
- https://thoughtbot.com/blog/introduction-to-function-currying-in-swift
- https://wanago.io/2020/02/17/typescript-generics-discussing-naming-conventions/
- https://google.github.io/styleguide/jsguide.html#naming-rules-common-to-all-identifiers
- https://www.freecodecamp.org/news/typescript-curry-ramda-types-f747e99744ab/
