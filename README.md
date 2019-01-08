# CSS-IN-JS

Css in js is a discussion that is mostly about opinions and there is no right or wrong. This makes it hard to agree on a single approach. Some like pure css & others like css-in-js approaches.

## Intro

In this repo we are comparing 3 approaches:

- [external css files](https://github.com/webpack-contrib/css-loader) ([sass](https://github.com/webpack-contrib/sass-loader))
- [styled-components](https://www.styled-components.com/) (no css extraction, pure js)
- [astroturf](https://github.com/4Catalyzer/astroturf) (css extraction, same api as styled-components)
- [linaria](https://github.com/callstack/linaria) (css extraction, without styled api)

**Why these 4?** If you look at the css landscape there are [lots of libraries](https://github.com/MicheleBertoli/css-in-js) that want to tackle this problem. We shouldn't think to much about the names of the libraries but more the api's they have and what advantages or disadvantages they have.

Code can always be improved and automated please keep that in mind.

## What do we want to accomplish?

In a microfrontend world we talk about html, js & css files which gives us a great benefit of caching and reducing duplication. That's why an approach should support the extract to static css file feature.

We are judging each library on the following criteria:

- styled api
- classNames (css) API
- no dynamic props (enforced)
- static interpolations (when using sass, less, ...)
- no runtime
- extract to static css file

### External css files

This is the oldest trick in the book. You write your css separate from your components and import your styles into your js files.

styles.css

```css
.button {
  background-color: #000;
}
```

MyComponent.js

```js
import React from 'react';
import classNames from 'classnames';
import styles from './styles.css';

function MyComponent(props) {
  return (
    <button className={classNames(styles.button, props.theme)}>
      {props.children}
    </button>
  );
}

export default MyComponent;
```

- ❌ styled api
- ❌ classNames (css) API
- ✔️ ️ no dynamic props (enforced)
- ✔️ static interpolations (when using sass, less, ...)
- ✔️ no runtime
- ✔️ extract to static css file

### Styled-components

Styled-components trully is CSS-in-JS built for React. You only think about components & props. Everything is done in runtime, we don't talk about .css files anymore. You have other libraries that have exactly the same api (e.g. [emotion](https://github.com/emotion-js/emotion)).

MyComponent.js

```js
import styled from 'styled-components';
import styled-tools from 'styled-tools';

const MyComponent = styled.button`
  background-color: ${theme('backgroundColor', '#000')}
`;

export default MyComponent
```

- ✔️ styled api
- ✔️ classNames (css) API
- ❌ no dynamic props (enforced)
- ✔️ static interpolations
- ❌ no runtime
- ❌ extract to static css file

### Astroturf

Astroturf tries to be a middleground between css & js. It supports the same API as styled-components but does not allow prop changes in the css. It allows constants & functions if they are in the same file but nothing at runtime.

MyComponent.js

```js
import styled from 'astroturf';

const MyComponent = styled.button`
  background-color: #000;
`;

export default renameProp('theme', 'className')(MyComponent);
```

- ✔️ styled api
- ✔️ classNames (css) API
- ✔️ no dynamic props (enforced)
- ❌ static interpolations (only if it's in the same file)
- ✔️ no runtime
- ✔️ extract to static css file

### Linaria

Linaria supports a good css api that automatically extracts all css out of your components. It has a styled api, using it means we have to drop IE11 support as it uses css-vars under the hood to accomplish dynamic styling. So we will only use the css function.

MyComponent.js

```js
import { css } from 'linaria';

const button = css`
  background-color: #000;
`;

export default function Button({ children }) {
  return <button className={button}>{children}</button>;
}
```

- ❌ styled api
- ✔️ classNames (css) API
- ✔️ no dynamic props (enforced)
- ✔️ static interpolations
- ✔️ no runtime
- ✔️ extract to static css file

## Things to consider

Choosing an API is one thing but making sure the api works for all our usecases is another thing.

The philosophy of the new architecture is to make things more simple and lower the costs this means we should share more code.

- Share components between applications
- Share theming between these applications/components
- Create less components

## What are we building?

To test these libraries in a more real world scenario we want to test these different approaches so we are building a Header & article teasers.

## Prejudgements

There are some best practices that these concepts "break". Actually they don't break it and I'll try to blow them

### Separation of Concerns

Developers say that using CSS-in-JS like styled-components or astroturf actually break the seperation of concerns that html, js & css should be devided but saying this means you can't use JSX which means React. JSX combines html & js together. CSS-in-JS just adds css to the mix so it lives closer to the component.

More info: https://reactjs.org/docs/introducing-jsx.html#why-jsx

### CSS-in-JS is hard to debug

you lose your readable classnames and it's hard to debug styles. Well actually that's right you can't find where what styles are being used as your stylesheets or html all have scrabled classnames (`sc-fSKFWr`). Working in React means you should use the react devtools which gives you the opportunity to view these classnames. It's also possible to name your classnames the same way as your display name so you can find them in your html. Tools like stylelint still work fine.

### Notes

I'm biased, I really like CSS-in-JS in a React world as it makes you think about components & components only. Staying with external css files just means you're not thinking in components and are afraid of leaping into a new paradigm just like Graphql vs Rest has the same issues.
