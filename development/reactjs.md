# React.js

These pages are intended for all frontend developers providing necessary guidelines making frontend React.js development as easy as possible.

## Functions

### Selection Operators

- Don't use selection operators in place of control statements

```javascript
// bad
!isRunning && startRunning();

// good
if (!isRunning) {
  startRunning();
}
```

> Do not confuse the use of selection operators with Conditional Rendering in React.
> Conditional Rendering is a valid programming style and, as such, can be used freely.
> Please refer to AirBnB's React Guidelines for more info and examples

#### Example 1

```javascript
{
  isLoggedIn ? <LogoutButton /> : <LoginButton />;
}
```

#### Example 2

```javascript
{
  isModalOpen && <Modal />;
}
```
