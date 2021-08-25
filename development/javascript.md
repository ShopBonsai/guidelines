# Javascript

The following includes all guidelines specific for Javascript.

## Quotes

- Use single quotes instead of double quotes.

```javascript
// bad
const message = 'This is super cool.';

// good
const message = 'This is super cool.';
```

- Use string literals only when the string contains a dynamic value.

```javascript
// bad
const message = `This is super cool.`;

// good
const message = `Order with number ${orderNumber} got created.`;
```

## Semicolons

- Use semicolons.

```javascript
// bad
const order = getOrder(..)

// bad
{
  return order
}

// good
const order = getOrder(...);

// good
{
  return order;
}
```

## Const vs functions

- Prefer using const instead of functions.

```typescript
// bad
function createOrder(): IOrder {
  // ...
}

// good
const createOrder = (): IOrder => {
  // ...
};
```

## Destructuring

- Use object destructuring when accessing and using multiple properties of an object.
- Only applies for first-level objects where there aren't nullable or undefined keys. This would lead to setting unnecessary defaults

```javascript
// bad
const getFullName = (user: IUser): string => {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
};

// good
const getFullName = (user: IUser): string => {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
};

// best
const getFullName = ({ firstName, lastName }: IUser): string => `${firstName} ${lastName}`;
```

> eslint: [prefer-destructuring](https://eslint.org/docs/rules/prefer-destructuring).

## Optional chaining

- Use when trying to access nested object properties that can be nullable or undefined.

```javascript
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah',
  },
};

const dogName = adventurer.dog?.name;
```

> This feature will only be available in packages/services migrated to Babel 7 and above. Enable strictNullChecks will automatically recommend this practice.

## Parameters

The **maximum** number of function parameters should be 3, and you should always **try to use 2 or less**.

- If you need more than 3 parameter values, use an object.

```javascript
// bad
const createOrder = (
  param1: param1Type,
  param2: param2Type,
  param3: param3Type,
  param4: param4Type,
): ReturnType => {
  // ...
};

// good
const createOrder = (param1: param1Type, param2: param2Type): ReturnType => {
  // ...
};

// good
const createOrder = ({ param1, param2, param3, param4 }: ObjectType): ReturnType => {
  // ...
};
```

- Always put default parameters last.

```javascript
// bad
const handleThings = (opts: optsType = {}, name: nameType) => {
  // ...
};

// good
const handleThings = (name: nameType, opts: optsType = {}) => {
  // ...
};
```

## Promises

### Async/await

- Prefer using async/await whenever possible instead of regular chainable promises or callbacks.

```javascript
// bad
const createOrder = async (cb: cbType): Promise<ReturnType> => {
  isAdminUser(() => {
    cb(...)
  })
}

// bad
const createOrder = async (): Promise<ReturnType> {
  return isAdminUser(...)
   .then((isAdmin) => {
      // ...
   })
}

// good
const createOrder = async (): Promise<ReturnType> => {
  const isAdmin = await isAdminUser(...)
  // ...
}
```

- Do not use async when await is not present.

```javascript
// bad
const getName = async (): Promise<string> => {
  // ...
  return "Core API team"
}

// good
const getName = async (): Promise<ReturnType> {
  // ...
  const isAdmin = await isAdminUser(...)
  return ...
}
```

> eslint: [require-await](https://eslint.org/docs/rules/require-await)

## Conditional cases

### If case

- Always use curly brackets
- Avoid else case when returning within all available if cases

```javascript
// bad
{
  if (merchantFeeRate <= standardFeeRate) return merchantFeeRate;
  if (discountedFeeRate < standardFeeRate) {
    return standardFeeRate;
  } else {
    return discountedFeeRat;
  }
}

// good
{
  if (merchantFeeRate <= standardFeeRate) {
    return merchantFeeRate;
  }

  if (discountedFeeRate < standardFeeRate) {
    return standardFeeRate;
  }

  return discountedFeeRat;
}
```

### Ternary if

- Use ternary operators to set a value to a variable, or to reduce code if necessary.
- Use if-else statements for everything else.

```javascript
// bad
const createdOrder = (await createOrder(values))
  ? await createExternalOrder(otherValues)
  : x.name === 'Core'
  ? await createCoreOrder(otherValues)
  : null;

// good
const host = url ? new URL(url).host : '';
```

## Nullish coalescing operator

- Logical operator that returns its right-hand side operand when its left-hand side operand is null or undefined, and otherwise returns its left-hand side operand.
- The problem with && and || operators is the definition of truthy and falsy leads to bugs.
  These operators work well with null or undefined values, but other falsy values can produce unexpected results.
- The nullish coalescing operator ?? acts very similar to the operator ||, except that it doesn’t use “truthy” when evaluating the operator.
  Instead, it uses the definition of “nullish”, which means that the value is strictly equal to null or undefined.

```javascript
//1
//0

const team = null ?? 'A team';

//A team
```

## Variables

### Const vs let vs var

- Do not use var

```javascript
// bad
var name = 'Core API team';

// good
const name = 'Core API team';
```

- Use const instead of let.

```javascript
// bad
let name = 'Core API team';

// good
const name = 'Core API team';
```

- Create a separate function if let is reassigning value.

```javascript
// bad
const createOrder = (): ReturnType => {
  // ...
  let name;
  if(orderNumber === 1) {
    // ...
    const merchant = await getMerchant(...)
    name = "special order"
  } else {
    // ...
    const merchant = await getSpecialMerchant(...)
    name = "normal order"
  }
}

// good
function getOrderName = (orderNumber: number): ReturnType => {
  if(orderNumber === 1) {
    // ...
    return "special order"
  }

  // ...
  return "normal order"
}

function createOrder() {
  // ...
  const name = getOrderName(orderNumber)
  // ...
}
```

### Creating variables

```javascript
const summary = getReportSummary(invoice);
const transactions = getReportTransactions(invoice);

return { summary, transactions };
```

// or

```javascript
return {
  summary: getReportSummary(invoice),
  transactions: getReportTransactions(invoice),
};
```

- Don't create variable right before returning if it has no extra value or isn't being used anywhere.

```javascript
// bad
const ms = (hours, minutes, seconds): number => {
  const duration = ((hours * 60 + minutes) * 60 + seconds) * 1000;
  return duration;
};

// good
const ms = (hours, minutes, seconds) => ((hours * 60 + minutes) * 60 + seconds) * 1000;
```

> [eslint: sonarjs prefer-immediate-return](https://github.com/SonarSource/eslint-plugin-sonarjs/blob/master/docs/rules/prefer-immediate-return.md)

## Functions

### Length

- A function should be maximum 20 lines long.
- Use warning for these since it could happen occasionally.
- Blank lines and comments should be skipped.

> [eslint: max-lines-per-function](https://eslint.org/docs/rules/max-lines-per-function)

### Complexity

- Avoid complex function where the flow becomes impossible to read.
- Functions with high Cognitive Complexity will be difficult to maintain.

> [eslint: sonarjs cognitive-complexity](https://github.com/SonarSource/eslint-plugin-sonarjs/blob/master/docs/rules/cognitive-complexity.md), [eslint: complexity](https://eslint.org/docs/rules/complexity).

## Newlines

- Add newline at the end of file

> [eslint: eol-last](https://eslint.org/docs/rules/eol-last)

- Add newline between declarations if it improves readability.

> [eslint: padding-line-between-statements](https://eslint.org/docs/rules/padding-line-between-statements)

- Add newline between test suites

```javascript
describe('Create order', () => {
  // ...
});

describe('Update order', () => {
  // ...
});
```

- Add newline between other test cases/specs

```javascript
describe('Create order', () => {
  it('Should create order with all requirements', () => {
    // ...
  });

  it('Should throw error when customer order is missing', () => {
    // ...
  });
});
```

## Hardcoded values

- Do not use hardcoded values for connection strings etc.

```javascript
// bad
const mongoUrl = 'localhost:4000';

// good
const mongUrl = process.env.MONGO_URL;
```

## Classes

- Classes are a good fit for encapsulating stateful systems, such as the Kafka client.
- Do not use classes by default, you should be able to explain why you chose to use them!
- Generally, we use the default ESlint guidelines for writing classes. This includes, among other conventions:
  - Underscoring private functions with an underscore `_`

## References

- <https://makecode.com/extensions/naming-conventions>
- <https://github.com/airbnb/javascript/>
- <https://itnext.io/effective-function-parameters-in-javascript-1c4f2f4602ee>
- <https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c>
- <https://medium.com/@jraleman/ternary-operators-vs-if-else-statements-6c26f7d034f7>
- <https://github.com/goldbergyoni/nodebestpractices>
- <https://www.freecodecamp.org/news/typescript-curry-ramda-types-f747e99744ab/>
- <https://medium.com/@hernanrajchert/creating-typings-for-curry-using-ts-3-x-956da2780bbf>
- <https://www.w3.org/wiki/JavaScript_best_practices>
- <https://www.sonarsource.com/docs/CognitiveComplexity.pdf>
- <https://www.freecodecamp.org/news/constant-confusion-why-i-still-use-javascript-function-statements-984ece0b72fd/>
- <https://medium.com/javascript-in-plain-english/nullish-coalescing-operator-for-javascript-8f502b970ba8>
- <https://zellwk.com/blog/javascript-variables/>
