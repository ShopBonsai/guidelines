# Testing

## Why

Software testing is a key component of software product development because it improves consistency and performance.

## Types of Tests

### Unit Tests

- Test single functionality with a `specific input` expecting a `specific output`. These tests should be as small as possible with as little setup as needed.
- Use test database `without mocking read/writes`. These should be kept at a minimum if possible to `avoid heavy setup`.
- Mock external helpers and services

**When to use**

- Functions with more advanced logic
- Error cases
- Code not reachable through direct api call. These will be utilities most likely.

### Integration test

- Test API call or method (for package) to make sure the call is actually accessible, providing input parameters and returning correct output schema. We don’t have to check the actual specific output.
- Use test database without mocking read/writes.
- Mock external services
- Optional snapshots can be useful to compare outputs.

**When to use**

- Simple API call without any extreme logic
- Success scenario where complex logic is being tested through unit tests.

### Acceptance test

- Test full flow against a (preferably) deployed test environment. This involves no mocking and calling actual external services etc.
- QA team responsibility

## Testing Framework

**Jest** will be used as the main testing framework for unit and integration testing. For information visit https://jestjs.io/

- Simple syntax with lot of matchers out-of-the-box
- Easy mocking support
- _Okay_ documentation but great community
- Typescript support using Babel or TS-Jest
- Known by React / React Native developers

## Mindset

Just be mindful of what you’re expecting within a test. The best way to maintain this is by writing a description `Should ... when ... ` describing exactly the expected behaviour. This will reduce the setup and make the test more readable and easier to understand. If the test description is too long it means you should better split up the test into multiple ones. This can sometimes include duplicate setup steps which will still outweigh the cons.

## Structure

We try to use a simple structure in tests so whenever anyone has to add tests they can easily understand the requirements and the various scenarios that are being tested. Reading test cases should be a reflection of all the user stories and expected outcome.

### Describe

Place at least 2 `describe` blocks tests:

- For the name of the tested unit
- Use **Sentence case** for describe titles describing a **case or file location**.
- Use **camelCase** for describe titles matching a **function name**.
- For additional level of categorization like particular function

> This will improve readability of both tests in code and in result of a failing tests.

### It

`it` is an alias of `test` and in order to keep consistency across tests we prefer to use `it`. Use `it` to describe the result of the test that is expected. `it` description should cover two questions: “what” and “when” somethings has to happen. The user story should be clear by reading the `it` description.

For example, if the user story is to create merchant, it should be like this: `Should successfully create merchant`.
Some other examples: `Should throw...` , `Should successfully return..`.

```javascript
describe('Merchant service - retrieve merchant', () => {
  initDb();

  describe('getMerchantById', () => {
    it('Should succesfully return merchant by its id', async () => {
      const merchant = await createMockMerchant();

      const existingMerchant = await getMerchantById(merchant._id);
      expect(existingMerchant).toMatchObject(merchant);
    });

    it('Should throw an error when merchant does not exist', async () => {
      await expect(() => getMerchantById(uuid.v4())).toThrowApiError(
        httpStatus.NOT_FOUND,
        ERRORS.merchantNotFound,
      );
    });
  });
});
```

## Services - Unit tests

- Create a folder `__tests__` per service and add unit tests there within the source folder.
- Create one test file per function or combining a specific operation. In the example below, we have one `createCategory` function and one test file for it, but three `get` functions (`getCategoryById`, `getCategoryByName`, `getCategoriesByParentId`) and we have one test file for them as well.

![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-_PclFCfWVE/e7327f01a791b39a97a8e67346221981a82f2b2ea4ef94917c0647a0e4fc9f3aa7bd6704f40efd7147116341226350bbd6fd4ca9e1ae43680c0123b4087ddc0bdc62db9cca655e520a4bc7a4144e5d88fa25c0e979032233c0002d7e3886e7a436a3db62?auto=format%2Ccompress&fit=max&dpr=2)

## Other unit tests (ex. utility tests)

- Create a folder `__tests__` for utilities.
- Use the filename as the test filename.
  ![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-SVUHB7ozKt/2039a421147b963e88d773b3c67ed9b7998a8c90e7f4f08ad19aa9fb8f2a5da93feffe7d1259f3c2db4e2650aea47f29cf22c71ad0c8e13f62652fe35b53fc614914df8de39a15b416a445e76eda7f9f1bb741cfb2c5ee4cc56c9ed22b729a403cb4f823?auto=format%2Ccompress&fit=max&dpr=2)

## Integration tests

Place integration tests in the root folder, same level as source folder.
![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-2hDRCmb7pC/78853f05add2074f3b4365453b1689423f677a12843f2defb5235e9bc461bbaa17dc986d3d7bc3ca2a84641a5bc027c04b5f3c056d4e70e0f5ac8c51f943fa16dd12cb7cf926c3af6bff828c7711793d15dd06fb4ff2404c3f85f7387d7b9796236c802e?auto=format%2Ccompress&fit=max&dpr=2)

### Describe

Place at least 2 describe blocks tests:

1. For the route that you are testing, i.e. `/admin/collections`
2. For specific REST operation that will be tested including command `POST /`

### It

Use `it` to describe the result of operation that is expected, i.e. `Should successfully create a new product collection`

```javascript
const testRunner = integratify(app, { prefix: '/admin/collections', dataPath: 'data' });

describe('/admin/collections', () => {
  // eslint-disable-next-line fp/no-let
  let user: User;

  initDb();

  beforeAll(async () => {
    // Spy authentication middleware with current user
    user = await createMockUser();
    spyAuthMiddleware(user);
  });

  afterEach(() => {
    authMiddlewareSpy.mockClear();
  });

  describe('POST /', () => {
    it('Should successfully create a new product collection', async () => {
      const payload = {
        name: faker.random.word(),
        products: [uuid.v4(), uuid.v4()],
      };

      await testRunner
        .post('/')
        .send({
          payload,
        })
        .expect({
          status: httpStatus.CREATED,
          schema: serializedSchema(collectionOutputSchema),
          spies: [authMiddlewareSpy],
        });
    });
  });
});
```

> We use [integratify](https://github.com/knor-el-snor/integratify) to run our tests limiting the extra dependencies and configuration needed.

## Fetching data

- Initialize testing database before all the tests
- Add `beforeAll` after initializing the db, put mocks and spies there. It runs before any of the tests in this file run. If the function returns a promise or is a generator, Jest waits for that promise to resolve before running tests.
- Add `afterEach` to clear all the mocks. It runs a function after each one of the tests in this file completes. If the function returns a promise or is a generator, Jest waits for that promise to resolve before continuing.

```javascript
describe('Authentication service - login', () => {
  const findSpy = jest.spyOn(User, 'getUserByEmail');
  const refreshTokenSpy = jest.spyOn(refreshToken, 'storeRefreshToken');

  const userValues = {
    password: 'goddaaaaaaaaamnSon',
  };

  afterEach(async () => {
    jest.clearAllMocks();
  });

  it('Should throw an error when passwords do not match', async () => {
    const user = await mockDBUser(userValues);
    findSpy.mockResolvedValueOnce(user);

    await expect(() => login('test@bonsai.ca', faker.random.word())).toThrowApiError(
      httpStatus.BAD_REQUEST,
      ERRORS.loginFailed,
    );

    expect(findSpy).toHaveBeenCalledWith('test@bonsai.ca');
    expect(refreshTokenSpy).not.toHaveBeenCalled();
  });

  it('Should return valid jwt & refresh token if passwords match', async () => {
    const user = await mockDBUser(userValues);
    findSpy.mockResolvedValueOnce(user);
    refreshTokenSpy.mockResolvedValueOnce(user);

    const { accessToken, refreshToken } = await login('test@bonsai.ca', userValues.password);
    expect(accessToken).toEqual(expect.any(String));
    expect(refreshToken).toEqual(expect.any(String));

    expect(findSpy).toHaveBeenCalledWith('test@bonsai.ca');
    expect(refreshTokenSpy).toHaveBeenCalledWith(user._id, expect.any(String));
  });
});
```

## Jest Comparisons

- If it’s a set result that’s small & easy to compare, use **.toEqual** (Why not toBe? It’s usually not what we want: https://stackoverflow.com/questions/22413009/jasmine-javascript-testing-tobe-vs-toequal)
- If you wish to do a partial compare & easy to do so, use **.toMatchObject**
- If it’s a large response (+20 lines), you’re sure manual testing is providing the correct output, use **.matchSnapshot**
- If it’s a small response, you need to almost reuse the same functions as the function you’re testing just to compare, use **.matchInlineSnapshot**
- If you’re refactoring old untested code, you know it works properly and you’re going to refactor it, use **.toMatchSnapshot** on the old output, then refactor and see whether the snapshots match

## Mocks

### Naming

When naming mocks use name that states what this mock does, for example, fixtures: `mockBrand`, `mockDBBrand`; setup: `createMockBrand`

### Location

Create `__mocks__` folder on the same level with `__tests__`

### Types

**Mock functions** allow you to test the links between code by erasing the actual implementation of a function, capturing calls to the function (and the parameters passed in those calls), capturing instances of constructor functions when instantiated with new, and allowing test-time configuration of return values.

**Mock data** can be just an object, but using mock functions to inject test values into the code during a test is best practice that we follow.

Fetch the data in the test, or use a separate mock-data folder where fetching functions exist:
![](https://codaio.imgix.net/docs/7DszrlIOzU/blobs/bl-DafSbzpluB/f710512af86f14b1f966b9d04d090cf544046ed3f709207bf45c857908de785bd9c2b8d8a65cb94312ab30044fc6b026ea002ca391d7e893e38b5fced76d2db2a7fd6c6c2b75b129f7a8209b1033ec31766fd6f6fcce8d5b736d389811bfd9a9fe04a0dd?auto=format%2Ccompress&fit=max&dpr=2)

**Fixture** is a fixed state of objects used for running tests. For example, for creating of mock objects
**Setup** creates a database model with a specific set of data

- Make sure all mock data objects that are functions so values can be overwritten if needed and libraries such as faker will generate different data every time when requesting a mock object.
- Avoid global test fixtures and seeds, add data per-test.
- Place mocks at the same level as `__tests__` folder under `__mocks__`.
- Use `afterEach` to clear mocks after each test case (for example, mockClear, jest.clearAllMocks()).
- Use `beforeAll` for any set up that’s needed before any of the tests (for example, create mocks).
- Use `.toEqual` to compare recursively all properties of object instances ("deep" equality).
- Use `.toMatchObject` to check that a JavaScript object matches a subset of the properties of an object.

```javascript
export const mockUniversalBrand = (values: Partial<IUniversalBrand>): IUniversalBrand => ({
  name: faker.company.companyName(),
  country: 'CA',
  description: faker.random.words(),
  public_id: faker.random.uuid(),
  logo: faker.random.image(),
  ...values,
});
```

- Ramda’s `mergeDeep` can be used to overwrite nested objects but will be overkill in most cases.

### Inputs

For test inputs, mock functions generating random data may be satisfactory for some tests, but other tests will require specific input values. These inputs do not need to be moved out of the test file.

If many tests require a specific value, then this can be abstracted into a mock in fixtures. For example:

```javascript

// if many tests look like this
testFn(mockAddress({ country: 'US', ... }))

// this should become
testFn(mockUSAddress(...))
```

## Spies

### Naming

- Use verb to express the action which this function does and add `spy` suffix.
- Avoid using the entire function name appended by `spy`. This is not necessary and does not improve readability. Since the amount of spies should be limited to a minimum, overlapping names shouldn’t occur often.

```javascript
// bad (or less preferred)
const getMerchantByPublicSpy = jest.spyOn(merchantService, 'getMerchantByPublicId');
const getAvailableMarketplacesSpy = jest.spyOn(marketplaceUtils, 'getAvailableMarketplaces');
const pushProductToExportQueueSpy = jest.spyOn(queueUtils, 'pushProductToExportQueue');

// good
const publicMerchantSpy = jest.spyOn(merchantService, 'getMerchantByPublicId');
const availableMarketplacesSpy = jest.spyOn(marketplaceUtils, 'getAvailableMarketplaces');
const exportQueueSpy = jest.spyOn(queueUtils, 'pushProductToExportQueue');
```

Use spies when you want to ensure a function is called. Use `mockResolvedValueOnce` or `mockResolvedValue` to get the result that is expected from this function. It will be used in further test. `jest.spyOn` allows to mock either the whole module or the individual functions of the module.

When spying, the real object remains unchanged, and we just spy some specific methods of it: we take the existing object and spy some of its methods. Spies are useful when we have a huge class full of methods, and we want to mock certain methods. In this scenario, we should prefer using spies rather than mocks.

```javascript
describe('fromExportableToProduct', () => {
  const getMerchantSpy = jest.spyOn(merchantService, 'getMerchantByPublicId');

  it('Should convert exportable product into DB Product for creation', async () => {
    const merchantId = uuid.v4();
    getMerchantSpy.mockResolvedValueOnce({ _id: merchantId });

    const exportableProduct = mockExportableProduct({
      published_at: new Date().toString(),
    });

    const convertedProduct = await fromExportableToProduct(exportableProduct, merchantId, brandId);
    expect(convertedProduct).toMatchObject({
      publicId: exportableProduct.public_id,
      merchant: merchantId,
    });
  });
});
```

### Tips & preferences

- Make sure to use `mockResolvedValueOnce` where possible instead of `mockResolvedValue`. Keep in mind these mocked return values stay between `it` cases and can lead to unexpected behaviour otherwise. Every test should be runnable by itself independent of other tests in the flow.
- Make sure to clean mocks/spies between tests using `jest.clearAllMocks` or per specific one using `.mockClear()`.
- Use `toHaveBeenCalled` instead of `toBeCalled`. Both are just aliases but we prefer to keep this consistent across developers.

### Code coverage

Code coverage is a metric that can helps understand how much of the source is tested.

#### Use coverage reports to identify critical misses in testing

Coverage reports can provide actionable guidance. Most tools allow to dig into the coverage reports to see the actual items that weren't covered by tests and then use that to identify critical parts of the application that still need to be tested.

#### Make code coverage part of continuous integration flow

If your goal is 80% coverage, you might consider setting a failure threshold at 70% as a safety net for CI culture.

#### Good coverage does not equal good tests

Getting a great testing culture starts by getting the team to understand how the application is supposed to behave when someone uses it properly, but also when someone tries to break it. Code coverage tools can help understand where you should focus your attention next, but they won't tell you if your existing tests are robust enough for unexpected behaviours.
Achieving great coverage is an excellent goal, but it should be paired with having a robust test suite that can ensure that individual classes are not broken as well as verify the integrity of the system.

## Tips & Tricks

- Make sure type-checking is enabled in tests as well. But ignore casting to any for this case.

## Sources

- https://github.com/goldbergyoni/javascript-testing-best-practices/#-%EF%B8%8F21-enrich-your-testing-portfolio-look-beyond-unit-tests-and-the-pyramid
- https://jestjs.io/en/
- https://www.atlassian.com/continuous-delivery/software-testing/code-coverage
