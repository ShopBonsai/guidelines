# Modules

Starting with ECMAScript 2015, JavaScript has a concept of modules. TypeScript shares this concept.

Modules are executed within their own scope, not in the global scope; this means that variables, functions, classes, etc.
declared in a module are not visible outside the module unless they are explicitly exported using one of the export forms.

Conversely, to consume a variable, function, class, interface, etc. exported from a different module, it has to be imported using one of the import forms.
Modules are declarative; the relationships between modules are specified in terms of imports and exports at the file level.

## Exporting

We prefer to **make** most **functions available through exporting** even if they’re only being used within the same file.
This makes it easy to write small unit tests for those individual functions.

We also **prefer named exports** over default exports to keep our codebase consistent.
Both have their pro/cons but consistency can provide more benefits in this case.

```javascript
// bad
const myFunction = () => { ... }
export default myFunction

// good
export const myFunction = () => { ... }
```

## Importing

When importing we use the following rules

- Default import only if available for third-party module.
- Default imports from third-party modules can improve readability making it easy to differentiate between internal & external functions within code blocks.

```javascript
import R from 'ramda';
```

- **Importing named exports** are **preferred.**

```javascript
import { myFn } from '../utils/email';
```

- Import using alias can be used in specific occasions, mostly when having to use a spy within tests.
- When agreed upon (or by convention), alias imports are also allowed outside of tests:
  - Repositories

```javascript
// Good
import * as validator from '../validator';

const validateSpy = jest.spyOn(validator, 'isValidEmail');

// Good
import * as merchantRepository from '...';

merchantRepository.getAll();
```

## References

- <https://medium.com/@etherealm/named-export-vs-default-export-in-es6-affb483a0910>
- <https://medium.com/@krishankantsinghal/named-exports-vs-default-exports-c0dffa21946d>
