# Logging

Logs are the events that reflect the various aspect of your application,
it is the easiest mode of troubleshooting and diagnosing your application,
if written correctly by the team.

Many times, as a developer, you need to debug some issue, we happily use debugger and breakpoints to understand where and what broke.
What would you do when your application is running on production?
Can you attach a debugger there and replay around the bug? Obviously no. Hence, this is where logging will help you.

## Application logs

Although these are not automatically generated we can still make a difference between those that are set up once to log specific actions compared to
logs that need to be added every time in between code.

We’ll log every incoming request using a logger middleware called morgan for example. These will be informative logs that are useful in every running environment.
We don’t have to add any logger statement in between other code. We set it up once and we’re good to go.

```javascript
// app.ts
import { NSlogger } from '@tree-house/logger';
const logger = NSlogger('req');

morgan.token('res-body', ({ body }) => scrubAndLog(ENV.nodeEnv)(body));
app.use(morgan(shortLogFormat, { stream: { write: logger.info } }));
```

## Manual logs

Most logs will not have to be added manually every time a new piece of code gets introduced, this would not be effective and frankly be considered overkill.
But there are definitely use cases such as debugging where we’d like to add manual logs for specific operations.

### Defining

We’ll use a namespace to gather all logs for a specific component.
Keep in mind functions can be called from multiple entry functions so they shouldn’t contain a flow but rather describe the component.

- Use camelCasing for longer components.
- If extra context is needed add a `:` followed by the extra context.

```javascript
const logger = NSlogger('shipping');
const logger = NSlogger('order:taxes');

logger.info(`Execute e-commerce api call for ${id}`);
```

### Errors

In order for logged errors to be useful when there’s an issue in production, they should contain as much context as possible.
This helps whomever is looking into the production issue.
Things like a proper Error object with a stack trace and inputs of the endpoint/function (but never include sensitive information like access tokens!):

```javascript
const errorLoggerExample = (nonSensitiveInput) => {
  try {
    doSomethingRisky(nonSensitiveInput);
  } catch (error) {
    NSlogger('my-namespace').error(
      'My function did not work as expected',
      error,
      nonSensitiveInput,
    );
  }
};
```

### Debugging

Sometimes we’d like to be able to debug our deployed services which simply isn’t possible by attaching a debugger onto such an environment.
In order for us to be able to have this information we will have to add debug logs.

- No need to mention the service name (shopify, togo, ...) since all logs will be separately shown in their own running service.
  On an Ops level we should be able to aggregate these and follow the entire flow.
- Try to avoid adding multiple debugging logs in the same function before calling another method.
  The logs should be placed inside the actual method to keep our code readable and make the logs independent of the flow they’re being used in.
- Use clear descriptions that provide extra information instead of short contextless sentences such as “Started shipping” etc.
- The easiest way to achieve this is by using an [higher-order function](https://en.wikipedia.org/wiki/Higher-order_function) as shown in the example.

```typescript
/**
 * Adds logs for debugging async functions.
 */
export const addDebugLogs =
  (log: ILogger) =>
  <T, U>(process: string, action: (input: T) => Promise<U>) =>
  async (input: T): Promise<U> => {
    log.debug(`Start: ${process}`);
    const result = await action(input);
    log.debug(`Finished: ${process}`);
    return result;
  };

/**
 * Creates customer transaction with specified status.
 */
export const createCustomerTransaction = addDebugLogs(
  'Creating customer transaction',
  createCustomerTransactionWithoutLogs,
);
```

**Filter specific logs**
Since we’re using the debug module behind the scenes we’re able to filter logs on space or comma-delimited names when running our service(s) using DEBUG=””.
This filter will match the namespace we used when creating an NSlogger instance.

```shell
DEBUG=shipping LOG_LEVEL=debug node start.ts
```

> **Filtering** is currently **only supported** for **debug level** logs.

## When to use

- Quick debugging of unexpected behaviour during development.
- Logs for your server application to log incoming requests, as well as any failures that might have happened.
- Optional debug logs for your library to assist the user with issues.
- Complicated operations where we need to be able to log the flow making it easier to pinpoint issues on deployed environments.
- Output of your CLI to print progress, confirmation messages or errors.

Log levels
Most logging tools offer different levels of logging based on the built-in Console within Node.js often with added “levels” providing extra granularity.
These levels allow us to filter logs based on their severity and the situation we’re using them for (during debugging session, on development, on production, ...)

Some tools such as Winston base their levels on standards such as RFC5424: severity of all levels is
assumed to be numerically ascending from most important to least important.

```shell
emerg: 0,
alert: 1,
crit: 2,
error: 3,
warning: 4,
notice: 5,
info: 6,
debug: 7
```

## Tools

There are numerous packages such as `winston`, `bunyan`, `pino` and numerous others providing logging functionality with extensive customizability.
We’ll be using [@tree-house/logger](https://www.npmjs.com/package/@tree-house/logger) written and maintained by our own team using [winston](https://www.npmjs.com/package/winston)
and [debug](https://www.npmjs.com/package/debug) behind the scenes.
This gives us an extra abstraction level and allows us to add extra Typescript functionality where needed.
On top of that we’ll be able to switch to any logger package and/or integrate with any external tool
without having to change the interface or our services using the package.

The current package offers two main functions called logger and NSlogger.
We should always prefer to use the NameSpace logger since it will allow us to group and potentially debug logs per namespace.
logger is still available in the package for backwards compatibility.

```javascript
import { NSlogger } from '@tree-house/logger';

const logger = NSlogger('MyWorkspace');
logger.error('Oooh no, someone made a boo boo!');
```

## Persistent logging

Sometimes we want to keep track of different states that can be used for analysis purposes or when we want to provide extra information to CX.
In this case we shouldn’t resort to adding logs but track these in a more persistent store such as a database.
This can be applicable when creating orders and keeping track of its state for example.

## Correlation ID

// TODO: Applicable if we were to actually move to microservices and want to be able to follow the entire flow.
This includes sending the potential error log id as a response.

Sources

- <https://www.twilio.com/blog/guide-node-js-logging>
- <https://blog.bitsrc.io/logging-best-practices-for-node-js-applications-8a0a5969b94c>
- <https://dzone.com/articles/microservices-logging-best-practices>
