# Packages

All the files are examples to demonstrate the structure. They aren't required in every package!

## Notes

These files are in addition to the structure laid out in [Common](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Common_suim9).

## Diagram

![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-iGFfkBhsTL/354e218e5126e0810d788aabd7beff9867906703fba5c6f67bf81820177745193e4660ba4bc2fac4418455e769854c08f6f76f0a1840a0faa24f421a1f7c44feaf853bc6f1fa6fb572a5a00912a90708a8963c4d9713decb2d746c32938a0ab332b83780?auto=format%2Ccompress&fit=max&dpr=2)
[Edit Diagram](https://app.creately.com/diagram/YBHktWR69dt/edit)

## Services

### index.ts

- Exports methods, types and classes from the package.
- Exposed Methods

  - We need to ensure that exposed methods are exported properly, and that a mistake further down the line won’t break the method being exposed.
  - We should absolutely have tests for exported methods, but we could also use a tool similar to [api-extractor](https://www.npmjs.com/package/@microsoft/api-extractor) in order to be absolutely sure our exported methods are working.

  ```javascript
  import { IStoreConfig, IEciConfig, IPreCheckoutInput } from './interfaces';
  import { ApiRequest } from './lib/request';
  import { getOrderEmail } from './services/marketplace';
  import { importProducts, onboardProducts } from './services/merchant';

  export * from './interfaces';
  export { supportedIntegrations } from './constants';

  /**
   * Returns new ECI client.
   * @param {object} config - E-Commerce integration configuration (Magento-eci, Shopify-eci).
   */
  export const getEciClient = (config: IEciConfig) => (storeConfig: IStoreConfig) =>
    new EciClient(config, storeConfig);
  ```
