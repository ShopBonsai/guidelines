# Javascript

## Quotes

- Use single quotes instead of double quotes.

```javascript
// bad
const message = "This is super cool.";

// good
const message = "This is super cool.";
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
