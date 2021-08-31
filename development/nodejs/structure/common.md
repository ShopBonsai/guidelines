# Common

## Diagram

![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-AAtzHeq23K/d8253ec4200b59c71117df37b8d8d3c401bc16c365a19051d2f3be636ee908dcc2b90d8f3001ae4158aff8d6e7f3302a7b58da9d0b1c8dceb0a8fd63d70e1444ba5284ba345a0517c2f0ce63c16b94420e2ff1cbbf292d3f24fa71839e20051b828030fb?auto=format%2Ccompress&fit=max&dpr=1)

[Edit Diagram](https://app.creately.com/diagram/ldf6FgK5URh/edit)

## Source

### @types

- Folder containing all custom Typescript declaration files.
- Used to extend existing or outdated third-party Typescript definitions.
- Used to define all `process.env` variables

```javascript
// @types/jest/index.d.ts
/// <reference types="node" />
declare namespace jest {
  interface Matchers<R> {
    toThrowApiError(status: number, error: IErrorType): Promise<R>
    toMatchObjectInArray(obj: {}): R
  }
}
```

### Common

```javascript
// common/interfaces.ts - contains all reusable interfaces for the package/service
export enum EciIntegrationType {
  Magento = 'magento',
  Shopify = 'shopify',
  Widget = 'widget',
};

export interface IEciConfig {
  type: EciIntegrationType;
  url: string;
  token?: string;
  username?: string;
  password?: string;
}
```

### Constants

- Folder containing all global constants.
- File per grouped subject such as `filters`, `errors`, `environment`, ...

```javascript
// constants/filter.ts
export const DEFAULT_SORTING = {
  sortBy: 'createdAt',
  sortOrder: 'desc',
};
```

> It’s also allowed on top of this to have a constants.ts file per service if these are `constants` that will be 100% applicable to that specific service.

### Config

- Contains all static configuration
- Should be 1 place where db connection configuration etc. can be tweaked.

```javascript
// config/database.ts
export const MONGO_CONFIG = {
  url: ENV.MONGO_URL || 'mongodb://localhost:27017/coreDB',
};
```

### Lib

- Folder/file containing methods implemented using a **third party library**
- Folder/file containing methods implementing a specific independent larger utility
  - The code is fully decoupled from the currently hosting package/service
  - Could be potentially moved to its own package
  - Example: **Canadian Taxes and Avatax in Core workspace**
- Acts as an interface so that we can modify our interactions with the external library without having to navigate too deeply into the code base

```javascript
// widget/api/source/lib/algolia.ts
import algoliasearch from 'algoliasearch';
import { defaultAlgoliaConfig } from '../config/algolia';

const { appId, apiKey } = defaultAlgoliaConfig;

/**
 * Returns algolia search client with default options.
 * @returns {object} Algolia client.
 */
export const getAlgoliaClient = () => algoliasearch(appId, apiKey);
```

### Utils

- Folder containing files where you can place small snippets you can use throughout the application. Small functions to build bigger things with.
- Utility files should contain functionality grouped by an entity such as email, currency, etc.

```javascript
// widget/api/source/utils/email.ts
/**
 * Returns whether string matches valid email pattern.
 * @param {string} email - Email address.
 */
export const isValidEmail = (email: string): boolean =>
  /^([\w-\.]+@([\w-]+\.)+[\w-]{2,4})?$/.test(email);
```

### Tests

#### Unit

- Please refer to the [Testing Guidelines](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Testing_suPTM)

### Integration

- Please refer to the [Testing Guidelines](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Testing_suPTM)

### Utils

- Utilities specific for testing purposes.
- See “Utils” above
