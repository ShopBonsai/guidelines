# MongoDB

Handy MongoDB tips & tricks that will make your life easier (keywords: mongo, queries, examples, guide, template, commands, useful)

## Recommendations

### Use Projection

By default, queries in MongoDB return all fields in matching documents. To limit the amount of data that MongoDB sends to applications, you can include a projection document to specify or restrict fields to return.

```javascript
// Fetch merchant by name, return name and url

// Option 1
await Merchant.find({ name: 'The Real Slim Shady' }, 'name url');

// Option 2
await Merchant.find({ name: 'The Real Slim Shady' }).select('name url');
```

### Use Lean

By default, Mongoose queries return an instance of the Mongoose Document class. Documents are much heavier than vanilla JavaScript objects, because they have a lot of internal state for change tracking. Enabling the lean option tells Mongoose to skip instantiating a full Mongoose document and just give you the POJO.

```javascript
const merchantOrder = await MerchantOrder.findOne().lean();
```

### Use Aggregation

Whenever possible, use aggregation. It is much faster to have all operations run within mongo instead of javascript

## Handy Queries

### Get distinct list of brand names for products

```javascript
db.getCollection('products').distinct('brand.name');
```

### Get collection sorted from most recent

```javascript
db.getCollection('merchantOrders').find({}).sort({ createdAt: -1 });
```

### Role `NOT` Customer

```javascript
db.getCollection('users').find({ roles: { $not: /Customer/ } });
```

### Find yourself

```javascript
db.getCollection('users').find({"email": /me/i})

Note: `me` in this case is your email
```

### Find products created AFTER some time

```javascript
db.getCollection('products')
  .find({ createdAt: { $gt: new ISODate('2018-10-24 20:00:25.962Z') } })
  .map((doc) => doc._id);
```

### Create Index

```javascript
db.getCollection('userEvents').createIndex(
  {
    item_id: 1,
  },
  { background: true },
);
```

## Negative Search in Mongo

### Find what is NOT there

```javascript
db.medias.find({
  'cards.type': 'image',
  'cards.meta.url': /^(?!.*\.(jpg|jpeg|png|gif)$).*\..*$/i,
  publishedStage: 'live',
});
```

## Sources

- https://mongoosejs.com/docs/tutorials/lean.html
- https://mongoosejs.com/docs/queries.html
