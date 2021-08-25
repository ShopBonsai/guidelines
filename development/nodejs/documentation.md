# Documentation

## JSDoc

- Although our functions should be self-explanatory they just still have an extra piece of JSDoc above it so provide some extra insights.
  This also helps most IDE to properly show the information when using these functions.
- Most guidelines will automatically get enforced through linting but will only lead to warnings instead of errors.

### Requirements

- Description is required which will be on the first line.
  - Start with an action word, describe what each method does.
  - Describe the most important details in the first sentence.
  - Start a method description with Returns, if the method returns a value.
    - Same applies for Updates, Removes, ...
- `@param` Parameters are required.
  - Name is required.
  - Description is required, preceded by a hyphen.
  - Type is optional.
  - Default values are optional.
  - Begin boolean parameter descriptions with whether. See example below:
    `@param isValid - Whether model passed validation checks.`
- @throws Indicate whether function possibly throws an Error is required.
  - Type is required.
- `@todo` Indicate whether something still needs to get implemented.
  - Only if applicable to the entire function. Otherwise use // TODO within code block.
  - Refer to the Jira story if one exists using the following syntax: `[key-number]`.
    - ex. `[CORE-1234]`
- `@deprecated` Indicate whether the functionality is outdated but can't be removed yet.
  - Refer to Clubhouse story if it's caused by something else using `[ch]` syntax.
- `@returns` **Forbidden** to use unless return type is really unclear.
- Every sentence should end with a dot and start with a capital letter.

### Examples

- Regular parameter(s)

```typescript
/**
 * Returns merchant revenue in cents.
 * @param merchantOrder - Existing merchant order.
 */
const getMerchantRevenue = (merchantOrder: MOI.IMerchantOrder): number => {
  // ...
};
```

- Optional parameter(s)

```typescript
/**
 * Returns merchant revenue in cents.
 * @param [merchantOrder] - Existing merchant order.
 */
const getMerchantRevenue = (merchantOrder?: MOI.IMerchantOrder): number => {
  // ...
};
```

- Object parameter(s)
  Make sure to use parameter names that are clear and descriptive.

```typescript
// bad
/**
 * Assign the project to an employee.
 * @param input - Input object.
 * @param input.id - Input id.
 * @param input.name - Input name.
 */

// good
/**
 * Assign the project to an employee.
 * @param merchantOrder - Existing merchant order.
 * @param merchantOrder.id - The merchant order's identifier.
 * @param merchantOrder.name - The merchant order's name.
 */
```

Only required for destructured objects, not for all objects.

- Possible error that could be thrown. Multiple are possible and allowed within the same function.

```typescript
/**
 * Returns merchant revenue in cents.
 * @param merchantOrder - Existing merchant order.
 * @throws {NotFoundError} - Merchant order could not be found.
 */
const getMerchantRevenue = (merchantOrder: MOI.IMerchantOrder): number => {
  // ...
  throw new NotFoundError(errors.MERCHANT_NOT_FOUND);
};
```

Since Typescript doesn't support return type with an error possible being thrown this could help figuring out which function does.

- Function that needs to receive an update or different implementation.

```typescript
/**
 * Returns merchant revenue in cents.
 * @todo - Convert into currency once [CORE-5172] gets implemented.
 * @param merchantOrder - Existing merchant order.
 */
const getMerchantRevenue = (merchantOrder: MOI.IMerchantOrder): number => {
  // ...
};
```

- Indicate deprecated functionality.

```typescript
/**
 * Returns merchant revenue in cents.
 * @deprecated - deprecated since version 2.0 [ch516].
 * @param merchantOrder - Existing merchant order.
 */
const getMerchantRevenue = (merchantOrder: MOI.IMerchantOrder): number => {
  // ...
};
```

// TODO: Look into [TSDoc](https://github.com/microsoft/tsdoc)
// TODO: Add typedef for our custom errors so the types exist in JSDoc

## References

- <https://medium.com/@wojciechkrysiak/typescript-jsdoc-better-docs-7c03b6ea04df>
- <https://jsdoc.app/index.html>
- <https://keepachangelog.com/en/1.0.0/>
- <https://www.makeareadme.com/>
