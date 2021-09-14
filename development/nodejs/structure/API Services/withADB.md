# With a DB

## Notes

These files are in addition to the structure laid out in [API Services](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/API-Services_sudgi).

## Diagram

![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-dvlj15Z42_/1ae9b0b64d796c1d458311fbf47214e4f46db3a58c82723ee31d6d9f933abbd3d6b498fdb11615cd91dd475b3b6ba1034d7a0531edcb553a50b9438822abae250fc859b06ea39705dbb20e660648bf87b27ec3e4b613041469b907637b12effd9b787bfe?auto=format%2Ccompress&fit=max&dpr=1)
[Edit Diagram](https://app.creately.com/diagram/48jWtMLRVOg/edit)

## Services

- For the most part, should be same as [Base API Services](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/API-Services_sudgi)
- **model.ts**

  - Optional
  - Contains database model.
  - Only applicable when models are specific to this service and not available within shared models package (mongodb-models).

  ```javascript
  export class Group extends Base {
  @prop({ required: true })
  name!: string;

  @prop()
  description?: string;

  @prop({ default: false })
  isFavorite?: boolean;
  }

  // Returns Typegoose class containing db operation functions  (passed along to repository only)
  export const DBGroup = getDbModel(Group);
  ```

  > Example uses [Typegoose](https://www.npmjs.com/package/@typegoose/typegoose)

- **repository.ts**

  - Only required when database models are being used & implemented directly in the service.
  - The repository pattern adds an abstraction layer over the top of data access.
  - Only applicable when models are specific to this service and not available within shared models package (mongodb-models). After reworking mongodb-models they should be the ones exposing repositories or services.

  ```javascript
  // Pass along Typegoose class for casting
  export class OrderRepository extends BaseRepository<Order> {
    constructor() {
      super(DBOrder);
    }

  /**
    * Return total amount of issues for an order.
    * @param {string} orderId - Unique order identifier.
    */
    async getTotalIssues(orderId: string): Promise<number> {
      const [match = { totalCount: 0 }]: { totalCount: number }[] =
      await this.dbModel.aggregate([
        { $match: { _id: orderId } },
        {
          $project: {
            _id: '$_id',
            totalCount: { $size: '$issues' },
          },
        },
      ]);

      return match.totalCount;
    }

  export const orderRepository = new OrderRepository();
  ```

  > Best practice would be to have a base repository containing most basic functionalities

* **mutate.ts**
  - Optional
  - Used for mutating data, not for merely fetching.
  - Only applicable when using data source that already exposes its models and/or repositories. For example when we use `core-models` which expose their database models and/or repositories which we wish to use and perform one or multiple operations onto. We should place all this logic combined into the `mutate.ts` file which will be used from within services.
  ```javascript
  export const addBreakdownToOrder = (orderId:string, values: IBreakdownCreate) => {
  // ...
  const breakdown = convertBreakdown(...)
  await orderRepository.updateById(orderId, { breakdown })
  }
  ```
