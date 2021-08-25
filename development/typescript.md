# Typescript

## Types vs interfaces

- An interface can have multiple merged declarations, but a type alias for an object type literal cannot.

```typescript
// These two declarations become:
// interface Point { x: number; y: number; }
interface Point {
  x: number;
}
interface Point {
  y: number;
}

const point: Point = { x: 1, y: 2 };
```

- When using types to differentiate between choices of strings, enums will most likely be a better solution.

```typescript
type Partner = 'ExternalStore' | 'Merchant';
// vs
enum Partner {
  ExternalStore,
  Merchant,
}
```

> Choose for consistency within a project.

## References

- <https://github.com/basarat/typescript-book/blob/master/docs/styleguide/styleguide.md>
- <https://www.typescriptlang.org/docs/handbook/advanced-types.html#interfaces-vs-type-aliases>
