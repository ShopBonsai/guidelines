# Development Guidelines

![Sanity Checks](https://github.com/ShopBonsai/guidelines/actions/workflows/main.yml/badge.svg)

These documents are intended for all developers at Bonsai providing necessary guidelines making development as easy as possible working at Bonsai.
Feel free to suggest any changes or improvements!

## Fundamental values

- Clarity at the point of use is your most important goal. Entities such as methods and properties are declared only once but used repeatedly.
  Design code to make those uses clear and concise. When evaluating a design, reading a declaration is seldom sufficient
  always examine a use case to make sure it looks clear in context.
- Clarity is more important than brevity. Although Javascript code can be compact, it is a non-goal to enable the smallest possible code with the fewest characters.
- Write a documentation comment for every function. Insights gained by writing documentation can have a profound impact on your design, so don’t put it off.

If you are having trouble describing your functionality in simple terms, you may have designed the wrong function.

## Concepts

- [RESTful API](./development/restapi.md)

## Languages & Frameworks

- [CSS](./development/css.md)
- [Javascript](./development/javascript.md)
  - [Node.js](./development/node.md)
  - [React.js](./development/react.md)
  - [Redux](./development/redux.md)

## Open Source

Because we love our the open-source community we've made our development guidelines public in full transparency!

We need to make sure that means **sensitive content is not included**:

- security & privacy policy violating content
- content considered competitive intelligence
- keys, tokens or credentials

> All sensitive information will be stored & available internally for all employees at Bonsai.

## Formatting

We use [Markdown](https://www.markdownguide.org/) as our formatting tool for all available documentation.

> [Markdownlint CLI](https://github.com/igorshubovych/markdownlint-cli)
> is being used to be make sure the content follows our formatting configuration to keep documentation consistent.

## How to Contribute

Use GitHub to create pull-requests, start discussions, and contribute to active issues.

If you are ready to start contributing, here are some things to keep in mind:

- This a versioned documentation repository, and commit messages should follow this format.
- If you are adding major sections or introducing major changes, then consider marking your commit as a breaking change.
- Feel free to consult our [CONTRIBUTING](./CONTRIBUTING.md) guide for more guidance on how to contribute.

// TODO: Add specific commit message format (preferably enforced through CLI tool)

### Local development setup

If you want to make sure your changes will pass our Github Actions steps, you might want to run things locally:

```shell
// Install dependencies
yarn

// Run sanity check by linting
yarn lint
```

## Pro tips

- Do not reinvent the wheel. Use all tools & libraries available if they provide the same functionalities.
  Make sure these are well-maintained and don't provide extra unneeded overhead.
- Look for already existing functions within codebase before writing it yourself.

## GitHub

Using the GitHub repository itself, rather than the "GitHub Wikis" feature, or other "Wiki" software ensures:

- Version tracking, and usage of GitHub GUI features (blame, history, branches, diff, etc ...)
- Ability to publish into a static website using GitHub Pages
- Leverage branch locking, testing automation and other GitHub features only available in repos

---

![Bonsai Logo](https://i.ibb.co/2gtS5CY/5f1efeb803215509150d1464-bonsai-lockup.png)
