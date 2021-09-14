# API Services

### Notes

These files are in addition to the structure laid out in [Common](https://coda.io/d/Development-Guidelines_d7DszrlIOzU/Common_suim9).

### Diagram

![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-oBqF_Gg--z/e349c3c9b5e42c4b5c540d1b17f488b9c6c621eac24eab9b75de8651cf39c20869c53a50ce41c47dc019a93063a33545a14b5ec32332c48867dc48123836189b0e86420f826675585b56a60768c997b80c7805b3aca0c3218e464dde3057a106c5cc4245?auto=format%2Ccompress&fit=max&dpr=1)
[Edit Diagram](https://app.creately.com/diagram/R5AUPc8fWpV/edit)

## Source

### Folders

#### Controllers

- Manager/supervisor
- Takes what it needs from Express (or whatever framework we're using), does some checking/validation to figure out to which service(s) should the data from the request be sent to, and orchestrates those service calls.

```javascript
// controllers/order.ts
/**
 * Create customer order.
 * @param {object} req - Request containing body.
 * @param {object} res - Express response.
 */
export const createCustomerOrder = async (
  { body }: IBodyRequest<IOrderCreate>,
  res: Response,
): Promise<void> => {
  const order = await orderService.createCustomerOrder(body);
  await responder.success(res, {
    status: httpStatus.CREATED,
    payload: order,
    serializer: orderSerializer,
  });
};
```

> Example using `responder` from utils which will make sure everything gets parsed properly.

#### Jobs

- Once create order comes into play, we’ll work this out

#### Middlewares

- Execute any code.
- Make changes to the request and the response objects.
- End the request-response cycle.
- Call the next middleware in the stack.
- Can be asynchronous since it is callback based.
- A controller function is technically also just middleware that will be called at the end.

```javascript
// middlewares/logger.ts
/**
 * Log every http request for debugging purposes.
 * @param {object} req - Incoming request.
 * @param {object} _res - Outgoing response.
 * @param {object} next - Callback function to express.
 */
export const logRequest = async (
  { method, originalUrl }: Request,
  _res: Response,
  next: NextFunction,
): void => {
  await doStuff(); // async/await functionality works as well
  logger.debug(`${method} ${originalUrl}`);
  next();
};
```

> Shouldn't return response but call the next middleware function. Within `Express` calling the `next` function with parameter will go to the error handler and stop other middleware from running. This means it will end abruptly without getting to the controller.

#### Plugins

- Once create order comes into play, we'll work this out

#### Routes

- Routes are ways that we can handle user navigation to various URLs throughout the application.
- Calls controller function to execute.
- For `Express` specific it should wrap every controller function in try/catch to avoid `unhandledPromiseRejection`
- Possible to have multiple routes calling the same controller function with different values.

```javascript
// routes/order.ts
export const setup = (app: Application) =>
  router.use(app, '/orders').post(
    '/customer',
    {
      description: 'Creates customer order.',
      tags: ['Orders'],
      input: {
        body: orderCreateSchema,
      },
      output: {
        [httpStatus.OK]: serializedSchema(orderOutputSchema, { type: 'customerOrders' }),
      },
    },
    tryCatchRoute(controller.createCustomerOrder),
  );
```

> Example uses `no-hassle` package which enables easy input data validation and automatic Swagger docs generation.

#### Schemas

- Contains data validation schemas.
- Will be used for data input validation (body, query, params) in routes.
- Will be used for automatic output Swagger generation through `no-hassle`
- Will be used for output validation in integration tests.
- For our projects we use [Joi data validation library](https://github.com/hapijs/joi) to create schemas and validate against.

```javascript
// schemas/order.ts
export const orderCreateSchema = Joi.object({
  name: Joi.string()
    .description('Name of order')
    .example('Order 12704730347')
    .required(),

  language: Joi.string()
    .valid(Object.values(USER_LANGUAGES))
    .description('User language')
    .example(USER_LANGUAGES.English),
    .required()

  // ...
});
```

> Should only contain schemas specific to the service. Schemas across services/packages should go into `@shopbonsai/definitions`

#### Serializers

- Serialize data before sending back as API response
- Avoids having transform functions within services to control API response object(s).
- Most known library available is [jsonapi-serializer](https://www.npmjs.com/package/jsonapi-serializer) which is [JSON API 1.0](http://jsonapi.org/) compliant.

```javascript
// serializers/group.ts
export const groupSerializer = new Serializer('groups', {
  id: (_value: null, { _id }: IGroup) => _id,
  attributes: ['id', 'name', 'isFavorite', 'members', 'createdAt', 'updatedAt'],

  // If null default to false
  isFavourite: (value: boolean | null) => value || false,

  // Use other serializer
  members: groupMemberSerializer,
});
```

> Example uses [@tree-house/serializer](https://www.npmjs.com/package/@tree-house/serializer) which is not a requirement

#### Services

- Responsible for getting the work done and returning it to the controller. It contains the business logic that is necessary to actually meet the requirements and return what the consumer of the API is requesting. It can also throw since we’ll wrap every route in try/catch. When any error gets thrown our `responder` will be taking over to send the error as an API response.
- Folder split up into multiple files since this functionality will be the biggest one. One file is realistically not possible, unmaintainable and unreadable.
- Possible to create subfolders to split functionality for readability reasons. For example, Tax service contains 3 subfolders: `customer`, `merchant`, `shipping` . In this case it’s easy to maintain.

##### interfaces.ts

- Contains all interfaces specific to this entity service.

```javascript
export type IGroup = DocumentType<Group>;
export type IGroupId = Joi.extractType<typeof groupIdSchema>;
export type IGroupCreate = Joi.extractType<typeof groupCreateSchema>;
export type IGroupUpdate = Joi.extractType<typeof groupUpdateSchema>;
```

##### transform.ts

- Responsible for transforming input data into other formatted output data.

```javascript
export const toUpf = (values: SomeRandomFormat): IUniversalProduct => {
  // ...
  return {
    id,
    ...
  }
}
```

> A lot of the current mapping functionality can/should be moved into serializers!

##### index.ts

- Contains main business logic.
- If this becomes too much logic it should be split up using `utils` folder within service.

```javascript
/**
 * Update user status.
 * @param {string} userId - User identifier.
 * @param {string} status - User status.
 */
export const updateUserStatus = async (userId: string, status: UserStatus): Promise<IUser> => {
  const user = await userRepository.updateById(userId, { status });
  if (!user) throw new NotFoundError(errors.USER_NOT_FOUND);
  return user;
};
```

#### Files

##### app.ts

- Contains main server application
- Sets routes onto application
- Add middleware onto application

```javascript
// Create Express instance
const app: express.Application = express();

treehouse.setBodyParser(app, '*');

// Log every request in debug mode
app.use(logRequest);

// Health check (maintance mode etc.)
app.use(healthCheck);

// Setup before initialising swagger-ui
setupRoutes(app);

// Catch all other requests with 404 (should be last)
app.all('*', (_req, res) => responder.error(res, new NotFoundError(errors.URL_NOT_FOUND)));

// Error handling
app.use(<T>(error: T, _req: express.Request, res: express.Response, _next: express.NextFunction) =>
  responder.error(res, error),
);

export { app };
```

##### index.ts

- Uses server app and starts actual server
- Will also catch all unhandled/uncaught errors
- Load environment variables

```javascript
import './config/load-env'; // Load our environment variables

process.on('unhandledRejection', (error: Error) => {
  logger.error(`unhandledRejection: ${error.message}`);
});

process.on('uncaughtException', (error: Error) => {
  logger.error(`uncaughtException: ${error.stack}`);
});

treehouse.startServer(app, {
  title: 'Core API',
  port: 3005, // Should load from environment variables
  pre: async () => {
    // Open database connection
    await db.openConnection();
  },
});
```

##### [sdk.ts]

- Optional
- Provide accessible Javascript sdk which will expose available api routes.
- Wrap routes with axios calls so we can provide a javascript sdk easier to use than people having to look into Swagger although being autogenerated. This would only make sense to me if we can get this autogenerated as well (through no-hassle maybe). We have to input schemas and output schemas so I believe it should be possible to fully autogenerate this file. Maybe per route have a parameter saying: includeSdk: true/false.
- Example not yet possible since I'm not sure about the autogeneration feasibility
- Adding this here for mocks, we might want to use it: https://github.com/xogroup/felicity
