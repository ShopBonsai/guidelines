# Development Guidelines

[Bonsai Logo](https://i.ibb.co/2gtS5CY/5f1efeb803215509150d1464-bonsai-lockup.png "Bonsai Logo")

These documents are intended for all developers at Bonsai providing necessary guidelines making development as easy as possible working at Bonsai. Feel free to suggest any changes or improvements!

## Fundamental values

- Clarity at the point of use is your most important goal. Entities such as methods and properties are declared only once but used repeatedly. Design code to make those uses clear and concise. When evaluating a design, reading a declaration is seldom sufficient; always examine a use case to make sure it looks clear in context.
- Clarity is more important than brevity. Although Javascript code can be compact, it is a non-goal to enable the smallest possible code with the fewest characters.
- Write a documentation comment for every function. Insights gained by writing documentation can have a profound impact on your design, so don’t put it off.

If you are having trouble describing your functionality in simple terms, you may have designed the wrong function.

## Languages & Frameworks

- [CSS](./development/css.MD)
- [Javascript](./development/javascript.MD)
  - [Node.js](./development/node.MD)
  - [React.js](./development/react.MD)
  - [Redux](./development/redux.MD)

## Pro tips

- Do not reinvent the wheel. Use all tools & libraries available if they provide the same functionalities. Make sure these are well-maintained and don't provide extra unneeded overhead.
- Look for already existing functions within codebase before writing it yourself.

## How to Contribute

// TODO: Add guidelines

## References

- [https://swift.org/documentation/api-design-guidelines/](https://swift.org/documentation/api-design-guidelines/)
