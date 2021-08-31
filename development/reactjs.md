# React.js

These pages are intended for all frontend developers providing necessary guidelines making frontend React.js development as easy as possible.

## CSS

### Only use REM for Fonts and Font-based properties

> Don't use rem/em for padding, margin, border etc.
> Set your <HTML> to font-size: 100% and just use rem/em for every font-size you want to set.
> So you have the maximum of accessibility in conjunction with maximum configurability

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
