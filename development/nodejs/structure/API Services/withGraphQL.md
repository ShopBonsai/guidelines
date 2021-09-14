# With GraphQL

## API Structure

### Common Approach

- Endpoints are organized in our `source/graphql` folder.
- Each set of endpoints is contained in its own separate folder. For example: `accounting`, `brand`, and `product`. The naming of that folder is always singular.
- Each folder contains three other folders: `logic`, `resolvers`, and `types`.
  - `logic` : contains the operational logic of the endpoint. Each endpoint should be separated into its own folder, for clarity. Additionally, each `logic` folder is likely to have a `types.ts` and `utils.ts` file.
    - The `types.ts` file defines shared Input Interfaces.
      - For example:

```javascript
export interface IDBPublisherInput extends IPaginationInput, IFiterInput, ISortInput {}
```

- `resolvers`

#🚧 WIP

```javascript
// publisher -> types -> queries.ts

import { ITypedef } from 'graphql-tools';

export const queries: ITypedef = `
  publisher (_id: String): Partner
  publishers (input: PublishersInput): PublishersResults
  publishersCount: PaginationTotalCount
`;
```

```javascript
  input PublishersInput @paginated @filterable @sortable
  input PublishersCountInput @filterable

  type PublishersResults {
    items: [Partner]
  }

  input CreatePublisherInput {
    fee: Int
    name: String
  }

  type CreatePublisherPayload {
    publisher: Partner
  }
```
