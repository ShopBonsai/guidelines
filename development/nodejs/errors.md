# Error Handling

## Error definitions

- All errors should be gathered into one configuration file.
- Every error gets a unique code assigned.

```typescript
import { errors as defaults, ErrorType } from '@tree-house/errors';

// Needed to enforce type and keep IDE autocompletion
const asType = <T extends { [key: string]: ErrorType }>(arg: T): T => arg;

export const ERRORS = asType({
  ...defaults,
  ...{
    urlNotFound: {
      code: 'URL_NOT_FOUND',
      message: 'The requested route does not exist',
    },
  },
});
```

## Blocking errors

- Express routes should be wrapped with try/catch to avoid unhandled errors.
- Throw errors when the flow will always get interrupted.
- Required to mention in JSDocs since Typescript has no support for declaring errors being thrown within functions.
  This only applies for user defined errors such as illustrated below.
  It’s impossible and not needed to define all possible third-party errors being thrown.

```typescript
/**
 * Returns an existing user by id.
 * @param {string} id - User identifier.
 * @throws {NotFoundError} - User could not be found by its id.
 */
const getUserById = async (id: string): Promise<IUser> => {
  const user = await userRepository.getById(id);
  if (!user) {
    throw new NotFoundError(ERRORS.userNotFound);
  }
  return user;
};
```

## Non-blocking errors

- When we need to keep track of errors occurring within the flow without interruption.
- Could be needed when processing multiple documents without throwing in between.

```javascript
const { data, errors } = await BPromise.safe(products.map((product) => getTotals(product)));
```

> Utility `BPromise.safe` will execute multiple promises and keep track of errors returning them at the end of execution.

## References

- <https://github.com/knor-el-snor/tree-house>
