# **⚛️ React/JSX Style Guide**

## Table of Contents

### **Core Concepts**

1. [Basic Rules](#basic-rules)
2. [Component Definition](#component-definition)
3. [Project Organization](#project-organization)
4. [Class vs `React.createClass` vs Stateless](#class-vs-reactcreateclass-vs-stateless)
5. [Mixins](#mixins)

### **Code Style and Naming**

6. [Naming](#naming)
7. [Declaration](#declaration)
8. [Alignment](#alignment)
9. [Quotes](#quotes)
10. [Spacing](#spacing)

### **Components and Props**

11. [Props](#props)
12. [Refs](#refs)
13. [Parentheses](#parentheses)
14. [Tags](#tags)
15. [Methods](#methods)

### **Best Practices**

16. [Ordering](#ordering)
17. [`isMounted`](#ismounted)
18. [Use ES6 Classes](#use-es6-classes)
19. [Component Method and Property Ordering](#component-method-and-property-ordering)
20. [Name Handlers `handleEventName`](#name-handlers-handleeventname)
21. [Name Handlers in Props `onEventName`](#name-handlers-in-props-oneventname)
22. [Open Elements on the Same Line](#open-elements-on-the-same-line)
23. [Align and Sort HTML Properties](#align-and-sort-html-properties)
24. [Only Export a Single React Class](#only-export-a-single-react-class)
25. [Make Presentation Components Pure](#make-presentation-components-pure)
26. [Prefer Props to State](#prefer-props-to-state)
27. [Never Store State in the DOM](#never-store-state-in-the-dom)

### **Type Safety and State Management**

28. [Use Flow Instead of PropTypes](#use-flow-instead-of-proptypes)
29. [Annotate `children`](#annotate-children)
30. [Props Must Be Plain JSON](#props-must-be-plain-json)
31. [Pure Functions of Props and State](#pure-functions-of-props-and-state)

### **Lifecycle and Effects**

32. [Side Effect Free Until `componentDidMount`](#side-effect-free-until-componentdidmount)

### **Libraries and Tools**

33. [Do Not Use Backbone Models](#do-not-use-backbone-models)
34. [Minimize Use of jQuery](#minimize-use-of-jquery)
35. [Reuse Standard Components](#reuse-standard-components)

### **Styling**

36. [80 Columns, Soft Tabs of 2 Spaces](#80-columns-soft-tabs-of-2-spaces)
37. [Camel Case Instead of Dash-Case for Class Names](#camel-case-instead-of-dash-case-for-class-names)
38. [Never Use ID and Tag Name as Root Selectors](#never-use-id-and-tag-name-as-root-selectors)
39. [When Using Multiple Selectors, Give Each Selector Its Own Line](#when-using-multiple-selectors-give-each-selector-its-own-line)
40. [Break Lines in CSS Function Arguments](#break-lines-in-css-function-arguments)
41. [When writing rules, be sure to](#when-writing-rules-be-sure-to)

### **Parent-Child Relationships**

42. [The Parent Constrains the Child](#the-parent-constrains-the-child)
43. [The Parent Doesn’t Assume Child Structure](#the-parent-doesnt-assume-child-structure)
44. [Components Never Leak Margin](#components-never-leak-margin)
45. [The Parent Spaces the Children](#the-parent-spaces-the-children)

### **CSS Best Practices**

46. [Nested Classes Aren’t for Providing Scope](#nested-classes-arent-for-providing-scope)
47. [Variables, Lots of Variables!](#variables-lots-of-variables)

---

# React

## Basic Rules

- Only include one React component per file.
  - However, multiple [Stateless, or Pure, Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) are allowed per file. eslint: [`react/no-multi-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
- Always use JSX syntax.
- Do not use `React.createElement` unless you’re initializing the app from a file that is not JSX.
- [`react/forbid-prop-types`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/forbid-prop-types.md) will allow `arrays` and `objects` only if it is explicitly noted what `array` and `object` contains, using `arrayOf`, `objectOf`, or `shape`.

## Component definition

All components (presentation, containers or pages) should **always** be
defined as a directory, named with pascal casing. The main component file
should be `index.js`, main stylesheet `style.css`. CSS custom properties
can be kept in `properties.css`:

```
AwesomeCard/
├── index.js
├── properties.css
└── style.css
```

- Styles should always be defined in a separate CSS file
- Avoid prefixing or suffixing component names
  - E.g.: `lib/pages/UserPage` or `lib/container/UserContainer`
- On conflict rename on import time
  - `import UserContainer from '...'`
     - `import { User as UserContainer } from '...'`

**[⬆ back to top](#table-of-contents)**

## Project organization

Your project components should be separated in at least three directories:

```
awesome-react-project/
└── src/
   ├── components/
   ├── containers/
   └── pages/
```

Each of these directories have special types of components:

### `components/`

Stateless components. Shouldn't store state. Most components in this
directory will be function-based components. Stuff like buttons, inputs,
labels and all presentational components goes here. This components can
also accept functions as props and dispatch events, but no state should
be held inside.

### `containers/`

Container components can store state. Containers are built mostly from
the composition of presentational components with some styles to layout
them together. Containers can also store internal state and access refs
to provide additional logic, but all actions should be accepted as
component callbacks.

### `pages/`

Page components can store state, receive route parameters and dispatch
Redux actions when applicable. Pages are the highest level of application's
components. They represent the application routes and most times are
displayed by a router. Pages are also responsible for handling container
components callbacks and flowing data into children containers.

**[⬆ back to top](#table-of-contents)**

## Class vs `React.createClass` vs stateless

- If you have internal state and/or refs, prefer `class extends React.Component` over `React.createClass`. eslint: [`react/prefer-es6-class`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

  ```jsx
  // bad
  const Listing = React.createClass({
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    },
  });

  // good
  class Listing extends React.Component {
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    }
  }
  ```

  And if you don’t have state or refs, prefer normal functions (not arrow functions) over classes:

  ```jsx
  // bad
  class Listing extends React.Component {
    render() {
      return <div>{this.props.hello}</div>;
    }
  }

  // bad (relying on function name inference is discouraged)
  const Listing = ({ hello }) => <div>{hello}</div>;

  // good
  function Listing({ hello }) {
    return <div>{hello}</div>;
  }
  ```

## Mixins

- [Do not use mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html).

> Why? Mixins introduce implicit dependencies, cause name clashes, and cause snowballing complexity. Most use cases for mixins can be accomplished in better ways via components, higher-order components, or utility modules.

## Naming

- **Extensions**: Use `.jsx` extension for React components. eslint: [`react/jsx-filename-extension`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)
- **Filename**: Use PascalCase for filenames. E.g., `ReservationCard.jsx`.
- **Reference Naming**: Use PascalCase for React components and camelCase for their instances. eslint: [`react/jsx-pascal-case`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

  ```jsx
  // bad
  import reservationCard from "./ReservationCard";

  // good
  import ReservationCard from "./ReservationCard";

  // bad
  const ReservationItem = <ReservationCard />;

  // good
  const reservationItem = <ReservationCard />;
  ```

- **Component Naming**: Use the filename as the component name. For example, `ReservationCard.jsx` should have a reference name of `ReservationCard`. However, for root components of a directory, use `index.jsx` as the filename and use the directory name as the component name:

  ```jsx
  // bad
  import Footer from "./Footer/Footer";

  // bad
  import Footer from "./Footer/index";

  // good
  import Footer from "./Footer";
  ```

- **Higher-order Component Naming**: Use a composite of the higher-order component’s name and the passed-in component’s name as the `displayName` on the generated component. For example, the higher-order component `withFoo()`, when passed a component `Bar` should produce a component with a `displayName` of `withFoo(Bar)`.

  > Why? A component’s `displayName` may be used by developer tools or in error messages, and having a value that clearly expresses this relationship helps people understand what is happening.

  ```jsx
  // bad
  export default function withFoo(WrappedComponent) {
    return function WithFoo(props) {
      return <WrappedComponent {...props} foo />;
    }
  }

  // good
  export default function withFoo(WrappedComponent) {
    function WithFoo(props) {
      return <WrappedComponent {...props} foo />;
    }

    const wrappedComponentName = WrappedComponent.displayName
      || WrappedComponent.name
      || 'Component';

    WithFoo.displayName = `withFoo(${wrappedComponentName})`;
    return WithFoo;
  }
  ```

  **[⬆ back to top](#table-of-contents)**

- **Props Naming**: Avoid using DOM component prop names for different purposes.

  > Why? People expect props like `style` and `className` to mean one specific thing. Varying this API for a subset of your app makes the code less readable and less maintainable, and may cause bugs.

  ```jsx
  // bad
  <MyComponent style="fancy" />

  // bad
  <MyComponent className="fancy" />

  // good
  <MyComponent variant="fancy" />
  ```

## Declaration

- Do not use `displayName` for naming components. Instead, name the component by reference.

  ```jsx
  // bad
  export default React.createClass({
    displayName: 'ReservationCard',
    // stuff goes here
  });

  // good
  export default class ReservationCard extends React.Component {
  }
  ```

## Alignment

- Follow these alignment styles for JSX syntax. eslint: [`react/jsx-closing-bracket-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md) [`react/jsx-closing-tag-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-tag-location.md)

  ```jsx
  // bad
  <Foo superLongParam="bar"
       anotherSuperLongParam="baz" />

  // good
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  />

  // if props fit in one line then keep it on the same line
  <Foo bar="bar" />

  // children get indented normally
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  >
    <Quux />
  </Foo>

  // bad
  {showButton &&
    <Button />
  }

  // bad
  {
    showButton &&
      <Button />
  }

  // good
  {showButton && (
    <Button />
  )}

  // good
  {showButton && <Button />}

  // good
  {someReallyLongConditional
    && anotherLongConditional
    && (
      <Foo
        superLongParam="bar"
        anotherSuperLongParam="baz"
      />
    )
  }

  // good
  {someConditional ? (
    <Foo />
  ) : (
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />
  )}
  ```

## Quotes

- Always use double quotes (`"`) for JSX attributes, but single quotes (`'`) for all other JS. eslint: [`jsx-quotes`](https://eslint.org/docs/rules/jsx-quotes)

  > Why? Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.

  ```jsx
  // bad
  <Foo bar='bar' />

  // good
  <Foo bar="bar" />

  // bad
  <Foo style={{ left: "20px" }} />

  // good
  <Foo style={{ left: '20px' }} />
  ```

## Spacing

- Always include a single space in your self-closing tag. eslint: [`no-multi-spaces`](https://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-tag-spacing`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-tag-spacing.md)

  ```jsx
  // bad
  <Foo/>

  // very bad
  <Foo                 />

  // bad
  <Foo
   />

  // good
  <Foo />
  ```

- Do not pad JSX curly braces with spaces. eslint: [`react/jsx-curly-spacing`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

  ```jsx
  // bad
  <Foo bar={ baz } />

  // good
  <Foo bar={baz} />
  ```

**[⬆ back to top](#table-of-contents)**

## Props

- Always use camelCase for prop names, or PascalCase if the prop value is a React component.

  ```jsx
  // bad
  <Foo
    UserName="hello"
    phone_number={12345678}
  />

  // good
  <Foo
    userName="hello"
    phoneNumber={12345678}
    Component={SomeComponent}
  />
  ```

- Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

  ```jsx
  // bad
  <Foo
    hidden={true}
  />

  // good
  <Foo
    hidden
  />

  // good
  <Foo hidden />
  ```

- Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`. eslint: [`jsx-a11y/alt-text`](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

  ```jsx
  // bad
  <img src="hello.jpg" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />

  // good
  <img src="hello.jpg" alt="" />

  // good
  <img src="hello.jpg" role="presentation" />
  ```

- Do not use words like "image", "photo", or "picture" in `<img>` `alt` props. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

  ```jsx
  // bad
  <img src="hello.jpg" alt="Picture of me waving hello" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />
  ```

- Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/#usage_intro). eslint: [`jsx-a11y/aria-role`](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

  ```jsx
  // bad - not an ARIA role
  <div role="datepicker" />

  // bad - abstract ARIA role
  <div role="range" />

  // good
  <div role="button" />
  ```

- Do not use `accessKey` on elements. eslint: [`jsx-a11y/no-access-key`](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

> Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

```jsx
// bad
<div accessKey="h" />

// good
<div />
```

- Avoid using an array index as `key` prop, prefer a stable ID. eslint: [`react/no-array-index-key`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-array-index-key.md)

> Why? Not using a stable ID [is an anti-pattern](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) because it can negatively impact performance and cause issues with component state.

We don’t recommend using indexes for keys if the order of items may change.

```jsx
// bad
{
  todos.map((todo, index) => <Todo {...todo} key={index} />);
}

// good
{
  todos.map((todo) => <Todo {...todo} key={todo.id} />);
}
```

- Always define explicit defaultProps for all non-required props.

> Why? propTypes are a form of documentation, and providing defaultProps means the reader of your code doesn’t have to assume as much. In addition, it can mean that your code can omit certain type checks.

```jsx
// bad
function SFC({ foo, bar, children }) {
  return (
    <div>
      {foo}
      {bar}
      {children}
    </div>
  );
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// good
function SFC({ foo, bar, children }) {
  return (
    <div>
      {foo}
      {bar}
      {children}
    </div>
  );
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};
SFC.defaultProps = {
  bar: "",
  children: null,
};
```

- Use spread props sparingly.
  > Why? Otherwise you’re more likely to pass unnecessary props down to components. And for React v15.6.1 and older, you could [pass invalid HTML attributes to the DOM](https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html).

Exceptions:

- HOCs that proxy down props and hoist propTypes

```jsx
function HOC(WrappedComponent) {
  return class Proxy extends React.Component {
    Proxy.propTypes = {
      text: PropTypes.string,
      isLoading: PropTypes.bool
    };

    render() {
      return <WrappedComponent {...this.props} />
    }
  }
}
```

- Spreading objects with known, explicit props. This can be particularly useful when testing React components with Mocha’s beforeEach construct.

```jsx
export default function Foo {
  const props = {
    text: '',
    isPublished: false
  }

  return (<div {...props} />);
}
```

Notes for use:
Filter out unnecessary props when possible. Also, use [prop-types-exact](https://www.npmjs.com/package/prop-types-exact) to help prevent bugs.

```jsx
// bad
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...this.props} />
}

// good
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...relevantProps} />
}
```

**[⬆ back to top](#table-of-contents)**

## Refs

- Always use ref callbacks. eslint: [`react/no-string-refs`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

  ```jsx
  // bad
  <Foo
    ref="myRef"
  />

  // good
  <Foo
    ref={(ref) => { this.myRef = ref; }}
  />
  ```

## Parentheses

- Wrap JSX tags in parentheses when they span more than one line. eslint: [`react/jsx-wrap-multilines`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-wrap-multilines.md)

  ```jsx
  // bad
  render() {
    return <MyComponent variant="long body" foo="bar">
             <MyChild />
           </MyComponent>;
  }

  // good
  render() {
    return (
      <MyComponent variant="long body" foo="bar">
        <MyChild />
      </MyComponent>
    );
  }

  // good, when single line
  render() {
    const body = <div>hello</div>;
    return <MyComponent>{body}</MyComponent>;
  }
  ```

## Tags

- Always self-close tags that have no children. eslint: [`react/self-closing-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

  ```jsx
  // bad
  <Foo variant="stuff"></Foo>

  // good
  <Foo variant="stuff" />
  ```

- If your component has multiline properties, close its tag on a new line. eslint: [`react/jsx-closing-bracket-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

  ```jsx
  // bad
  <Foo
    bar="bar"
    baz="baz" />

  // good
  <Foo
    bar="bar"
    baz="baz"
  />
  ```

## Methods

- Use arrow functions to close over local variables. It is handy when you need to pass additional data to an event handler. Although, make sure they [do not massively hurt performance](https://www.bignerdranch.com/blog/choosing-the-best-approach-for-react-event-handlers/), in particular when passed to custom components that might be PureComponents, because they will trigger a possibly needless rerender every time.

  ```jsx
  function ItemList(props) {
    return (
      <ul>
        {props.items.map((item, index) => (
          <Item
            key={item.key}
            onClick={(event) => {
              doSomethingWith(event, item.name, index);
            }}
          />
        ))}
      </ul>
    );
  }
  ```

- Bind event handlers for the render method in the constructor. eslint: [`react/jsx-no-bind`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

  > Why? A bind call in the render path creates a brand new function on every single render. Do not use arrow functions in class fields, because it makes them [challenging to test and debug, and can negatively impact performance](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1), and because conceptually, class fields are for data, not logic.

  ```jsx
  // bad
  class extends React.Component {
    onClickDiv() {
      // do stuff
    }

    render() {
      return <div onClick={this.onClickDiv.bind(this)} />;
    }
  }

  // very bad
  class extends React.Component {
    onClickDiv = () => {
      // do stuff
    }

    render() {
      return <div onClick={this.onClickDiv} />
    }
  }

  // good
  class extends React.Component {
    constructor(props) {
      super(props);

      this.onClickDiv = this.onClickDiv.bind(this);
    }

    onClickDiv() {
      // do stuff
    }

    render() {
      return <div onClick={this.onClickDiv} />;
    }
  }
  ```

- Do not use underscore prefix for internal methods of a React component.

  > Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public. See issues [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.

  ```jsx
  // bad
  React.createClass({
    _onClickSubmit() {
      // do stuff
    },

    // other stuff
  });

  // good
  class extends React.Component {
    onClickSubmit() {
      // do stuff
    }

    // other stuff
  }
  ```

- Be sure to return a value in your `render` methods. eslint: [`react/require-render-return`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

  ```jsx
  // bad
  render() {
    (<div />);
  }

  // good
  render() {
    return (<div />);
  }
  ```

**[⬆ back to top](#table-of-contents)**

## Ordering

- Ordering for `class extends React.Component`:

1. optional `static` methods
1. `constructor`
1. `getChildContext`
1. `componentWillMount`
1. `componentDidMount`
1. `componentWillReceiveProps`
1. `shouldComponentUpdate`
1. `componentWillUpdate`
1. `componentDidUpdate`
1. `componentWillUnmount`
1. _event handlers starting with 'handle'_ like `handleSubmit()` or `handleChangeDescription()`
1. _event handlers starting with 'on'_ like `onClickSubmit()` or `onChangeDescription()`
1. _getter methods for `render`_ like `getSelectReason()` or `getFooterContent()`
1. _optional render methods_ like `renderNavigation()` or `renderProfilePicture()`
1. `render`

- How to define `propTypes`, `defaultProps`, `contextTypes`, etc...

  ```jsx
  import React from "react";
  import PropTypes from "prop-types";

  const propTypes = {
    id: PropTypes.number.isRequired,
    url: PropTypes.string.isRequired,
    text: PropTypes.string,
  };

  const defaultProps = {
    text: "Hello World",
  };

  class Link extends React.Component {
    static methodsAreOk() {
      return true;
    }

    render() {
      return (
        <a href={this.props.url} data-id={this.props.id}>
          {this.props.text}
        </a>
      );
    }
  }

  Link.propTypes = propTypes;
  Link.defaultProps = defaultProps;

  export default Link;
  ```

- Ordering for `React.createClass`: eslint: [`react/sort-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

1. `displayName`
1. `propTypes`
1. `contextTypes`
1. `childContextTypes`
1. `mixins`
1. `statics`
1. `defaultProps`
1. `getDefaultProps`
1. `getInitialState`
1. `getChildContext`
1. `componentWillMount`
1. `componentDidMount`
1. `componentWillReceiveProps`
1. `shouldComponentUpdate`
1. `componentWillUpdate`
1. `componentDidUpdate`
1. `componentWillUnmount`
1. _clickHandlers or eventHandlers_ like `onClickSubmit()` or `onChangeDescription()`
1. _getter methods for `render`_ like `getSelectReason()` or `getFooterContent()`
1. _optional render methods_ like `renderNavigation()` or `renderProfilePicture()`
1. `render`

## `isMounted`

- Do not use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

> Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

[anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

**[⬆ back to top](#table-of-contents)**

## Use ES2015 classes.

1. Use static properties for `defaultProps`.
2. Use an instance property for `state`.
3. Use flow for `props` and `state`.
4. Autobind event handlers and callbacks.

   Example:

   ```jsx
   import React, { Component } from "react";

   class Foo extends Component {
     static defaultProps = {};

     state = {};

     state: {};
     props: {};

     handleClick = (e) => {
       // handle the click
     };
   }
   ```

5. If `state` depends on `props`, define it in the constructor.

   Example:

   ```jsx
   class Bar extends Component {
     constructor(props) {
       super(props); // must be called first
       this.state = {
         value: props.value,
       };
     }
   }
   ```

6. Use higher order components instead of mixins.
   ES6 style classes do not support mixins. See the [mixins considered harmful](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)
   blog post for details of how to convert mixins to higher order components.

**Codemods**

Converting legacy components to use ES2015 classes? There's a codemod for that.

Convert one or more files with the following command:

```bash
$ tools/react-codemod.js class path/to/file path/to/other/file
```

## Component method and property ordering

Ordering within a React component is strict. The following example
illustrates the precise ordering of various component methods and
properties:

```js
class Foo extends Component {
    // Static properties
    static defaultProps = {}

    // The `constructor` method
    constructor() {
        super();
    }

    // Instance properties
    state = { hi: 5}

    // Flow `state` annotation
    // NOTE: `state` is special. All other Flow annotations live below.
    state: { hi: number }

    // React lifecycle hooks.
    // They should follow their chronological ordering:
    // 1. componentWillMount
    // 2. componentDidMount
    // 3. componentWillReceiveProps
    // 4. shouldComponentUpdate
    // 5. componentWillUpdate
    // 6. componentDidUpdate
    // 7. componentWillUnmount
    componentDidMount() { ... }

    // All other Flow annotations
    props: { ... }
    someRandomData: string,

    // All other instance methods
    handleClick = (e) => { ... }

    // Finally, the render method
    render() { ... }
}
```

## Name handlers `handleEventName`.

Example:

```jsx
<Component
  onClick={this.handleClick}
  onLaunchMissiles={this.handleLaunchMissiles}
/>
```

## Name handlers in props `onEventName`.

This is consistent with React's event naming: `onClick`, `onDrag`,
`onChange`, etc.

Example:

```jsx
<Component onLaunchMissiles={this.handleLaunchMissiles} />
```

#### Open elements on the same line.

The 80-character line limit is a bit tight, so we opt to conserve the extra 4.

Yes:

```jsx
return <div>...</div>;
```

No:

```jsx
return (
  // "div" is not on the same line as "return"
  <div>...</div>
);
```

## Align and sort HTML properties.

Fit them all on the same line if you can. If you can't, put each
property on a line of its own, indented four spaces, in sorted order.
The closing angle brace should be on a line of its own, indented the
same as the opening angle brace. This makes it easy to see the props
at a glance.

Yes:

```jsx
<div className="highlight" key="highlight-div">
<div
    className="highlight"
    key="highlight-div"
>
<Image
    className="highlight"
    key="highlight-div"
/>
```

No:

```jsx
<div className="highlight"      // property not on its own line
     key="highlight-div"
>
<div                            // closing brace not on its own line
    className="highlight"
    key="highlight-div">
<div                            // property is not sorted
    key="highlight-div"
    className="highlight"
>
```

## Only export a single react class.

Every .jsx file should export a single React class, and nothing else.
This is for testability; the fixture framework requires it to
function.

Note that the file can still define multiple classes, it just can't export
more than one.

## Make "presentation" components pure.

It's useful to think of the React world as divided into ["logic"
components and "presentation" components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0).

"Logic" components have application logic, but do not emit HTML
themselves.

"Presentation" components are typically reusable, and do emit HTML.

Logic components can have internal state, but presentation components
never should.

## Prefer [props to state](http://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#what-components-should-have-state).

You almost always want to use props. By avoiding state when possible,
you minimize redundancy, making it easier to reason about your
application.

A common pattern — which matches the "logic" vs. "presentation"
component distinction — is to create several stateless components
that just render data, and have a stateful component above them in the
hierarchy that passes its state to its children via props. The
stateful component encapsulates all of the interaction logic, while
the stateless components take care of rendering data in a declarative
way.

Copying data from props to state can cause the UI to get out of sync
and is especially bad.

## _Never_ store state in the DOM.

Do not use `data-` attributes or classes. All information
should be stored in JavaScript, either in the React component itself,
or in a React store if using a framework such as Redux.

## Type-checking with [Flow](https://flow.org/)

Flow is a type-checker that runs at compile-time to catch issues
and prevent bugs. It should be used on all new components.

For more information on how we use Flow, check the [Javascript
style guide](https://github.com/Khan/style-guides/blob/master/style/javascript.md#flow-rules)

## Use Flow instead of PropTypes

Props can now be validated with Flow instead of React's PropTypes.
Flow provides a much more expressive way to set types for props,
with the additional benefits of being able to annotate state (and
any additional methods or data).

Types can be defined on the class itself:

```jsx
class Foo extends Component {
  props: {
    name: string,
    uniqueId: number,
    complexData: {
      setOfThings: Array<string>,
    },
  };
}
```

If you're writing a Stateless Functional Component, or if you use
lifecycle methods that reference props (eg. `componentDidUpdate`),
you may find it helpful to define a Props type, and reference it:

```jsx
type Props = {
    numbers: Array<number>,
};

class Foo extends Component {
    props: Props

    shouldComponentUpdate(nextProps: Props) {
        ...
    }
}

const StatelessFoo = ({numbers}: Props) => {
    ...
};

```

## Annotate `children`

`children` is something of a special prop in React. Most often,
you'll want to pass a React element (or an array of React elements).

This can be annotated like so:

```js
children: React$Element<any> | Array<React$Element<any>>
```

Note that this is only the most common use-case for children.
Children can also be other types (like a string, or a function).

## Server-side rendering

To make components safe to render server-side, they must adhere
to a few more restrictions than regular components.

## Props must be plain JSON

In order to render server-side, the props are serialized and
deserialized from JSON. This means that e.g. dates must be
passed as timestamps as opposed to JS `Date` objects.

Note that this only applies to the root component being
rendered with server-side rendering. If the root component
constructs more complex data structures from props, and
passes those constructs down to child components, that won't
cause problems.

## Pure functions of props and state

Components must be pure functions of their `props` and `state`.
This means the output of their `render()` function must not
depend on either KA-specific globals, on browser-specific
state, or browser-specific APIs.

Examples of KA-specific globals include anything attached to
the `KA` global, e.g. `KA.getUserId()`, or data extracted from
the DOM such as `data-` properties attached to other DOM nodes.

Examples of browser-specific state include the user agent,
the screen resolution, the device pixel density etc.

An example of a browser-specific API is `canvas.getContext()`.

The output must be deterministic. One way to get
non-deterministic output is to generate random
IDs in `getInitialState()`, and have the output
of render depend on that ID. Don't do this.

## Side effect free until `componentDidMount`

The parts of the React component lifecycle that are run to
render on the server must be free from side effects.

Examples of side effects that must be avoided:

- Sending an AJAX request
- Mutating global JS state
- Injecting elements into the DOM
- Changing the page title
- `alert()`

The lifecycle methods that run on the server are currently:

- `getInitialState()`
- `getDefaultProps()`
- `componentWillMount()`
- `render()`

If you need to execute any of the above listed side effects,
you must do so in `componentDidMount` or later in the component
lifecycle. These functions are not executed server-side.

## Do not use Backbone models.

Use redux actions, or `fetch` directly, instead.

We are trying to remove Backbone from our codebase entirely.

## Minimize use of jQuery.

_Never_ use jQuery for DOM manipulation.

Try to avoid using jQuery plugins. When necessary, wrap the jQuery
plugin with a React component so you only have to touch the jQuery
once.

Use the [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch) (accessible via khanFetch) instead of `$.ajax`.

For more information on khanFetch, see the [javascript style guide](https://github.com/Khan/style-guides/blob/master/style/javascript.md#use-khanfetch-instead-of-ajaxgetpostgetjson).

## Reuse standard components.

If possible, re-use existing components, especially low-level, pure
components that emit HTML directly. If you write a new such one, and
it finds a use in a different project, put it in a shared location
such as the react.js package.

The standard shared location for useful components that have been
open sourced is the `react-components.js` package in
`javascript-packages.json`. This includes components such as these:

- `SetIntervalMixin` - provides a setInterval method so something can be
  done every x milliseconds
- `$_` - the i18n wrapper to allow for translating text in React.
- `TimeAgo` - “five minutes ago”, etc - this replaces $.timeago

Reusable components that have not (yet) been open sourced are in the
(poorly named) `react.js` package. This include components such as
these:

- `KUIButton` - render a Khan Academy styled button.
- `Modal` - create a modal dialog.

**[⬆ back to top](#table-of-contents)**

## CSS are modules!

We use CSS modules everywhere. CSS modules are great because they provide
scope to CSS and allow us to create compartmentalized styles that don't
leak to global scope. Here are our good practices of doing CSS modules:

## 80 columns, soft tabs of 2 spaces

Keep your code lines under 80 columns wide. This helps when opening multiple splits.
Use soft tabs of 2 spaces to save some space! :stuck_out_tongue:

## Camel case instead of dash-case for class names

With CSS modules, camel case makes much more sense:

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>lib/components/Input/index.js</code></th>
<th><code>lib/components/Input/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Item = ({ children }) => (
  <li className={style.circleBullet}>{children}</li>
);

export default Item;
```

</td>
<td>

```css
.circleBullet {
  list-style-type: disc;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## Never use ID and tag name as root selectors!

Using ID and tag name at the selector's root makes the rule to be applied
globally.

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>lib/components/Item/index.js</code></th>
<th><code>lib/components/Item/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Item = ({ title, thumbnail }) => (
  <div className={style.container}>
    <img src={thumbnail} alt={title} />
  </div>
);

export default Item;
```

</td>
<td>

```css
.container > img {
  background-color: #cccccc;
}
```

</td>
</tr>
</tbody>
<th colspan="2"><strong>BAD</strong></th>
</thead>
<thead>
<th><code>lib/components/Item/index.js</code></th>
<th><code>lib/components/Item/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Item = ({ title, thumbnail }) => (
  <div className={style.container}>
    <img src={thumbnail} alt={title} />
  </div>
);

export default Item;
```

</td>
<td>

```css
img {
  background-color: #cccccc;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## When using multiple selectors, give each selector its own line

Organize one selector per line, even when placing all of them at the same line doesn't
exceed 80 columns.

<table>
<thead>
<th><strong>GOOD</strong></th>
<th><strong>BAD</strong></th>
</thead>
<tbody>
<tr>
<td>

```css
.container > img,
.container > div,
.container > section {
  background-color: #cccccc;
}
```

</td>
<td>

```css
.container > img,
.container > div,
.container > section {
  background-color: #cccccc;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## Break lines in CSS function arguments

Sometimes, not to exceed the 80 columns limit, you need to break lines. While at it, be sure to do it right after the colon, and keep at one argument per line.

<table>
<thead>
<th><strong>GOOD</strong></th>
<th><strong>BAD</strong></th>
</thead>
<tbody>
<tr>
<td>

```css
.container {
  background-color: linear-gradient(
    0deg,
    var(--color-light-yellow-12),
    var(--color-light-yellow-10)
  );
}
```

</td>
<td>

```css
.container {
  background-color: linear-gradient(0deg, --color-light...
}

.container {
  background-color: linear-gradient(
    0deg, var(--color-light-yellow-12), var(--color-lig...
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## When writing rules, be sure to

- Put a space before the opening brace `{`
- In properties put a space after (but not before) the `:` character
- Put closing braces `}` of rule declarations on a new line
- Leave **ONE** blank line in between rule declarations

<table>
<thead>
<th><strong>GOOD</strong></th>
<th><strong>BAD</strong></th>
</thead>
<tbody>
<tr>
<td>

```css
.container {
  font-size: 12pt;
}

.thumbnail {
  width: 160px;
  height: 90px;
}
```

</td>
<td>

```css
.container {
  font-size: 12pt;
}
.thumbnail {
  width: 160px;
  height: 90px;
}
```

</td>
</tr>
</thead>
<tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## CSS Design Patterns

## The parent constrains the child

Leaf components shouldn't constrain width or height (unless it makes
sense). That said, most components should default to fill the parent:

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>lib/components/Input/index.js</code></th>
<th><code>lib/components/Input/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Input = ({ children }) => (
  <input className={style.input}>{children}</input>
);

export default Input;
```

</td>
<td>

```css
.input {
  box-sizing: border-box;
  padding: 10px;
  width: 100%;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## The parent doesn't assume the child's structure

Sometimes we don't want to fill the whole width by default. An example is
the button component, which we want to resize itself based on title width.

In this cases, we should allow the parent component to inject styles into
the child component's container. The child is responsible for choosing where
parent styles are injected.

For merging styles, always use [`classnames`][classnames] package. The
rightmost arguments overrides the leftmost ones.

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>lib/components/Button/index.js</code></th>
<th><code>lib/components/Button/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import classNames from "classnames";
import style from "./style.css";

const Button = ({ children, className }) => (
  <button className={classNames(style.button, className)}>{children}</button>
);

export default Button;
```

</td>
<td>

```css
.button {
  box-sizing: border-box;
  padding: 10px;
  width: 100%;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## Components never leak margin

All components are self-contained and their final size should never suffer
margin leakage! This allows the components to be much more reusable!

<table>
<thead>
  <th><strong>BAD</strong></th>
  <th><strong>GOOD</strong></th>
</thead>
<tbody>
<tr>
<td>

```
|--|-content size-|--| margin
 ____________________
|   ______________   | | margin
|  |              |  |
|  |              |  |
|  |              |  |
|  |______________|  |
|____________________| | margin

|---container size---|
```

</td>

<td>

```

   |-content size-|
    ______________
   |              |
   |              |
   |              |
   |______________|



```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

### The parent spaces the children

When building lists or grids:

- Build list/grid items as separate components
- Use the the list/grid container to space children
- To space them horizontally, use `margin-left`
- To space them vertically, use `margin-top`
- Select the `first-child` to reset margins

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>lib/containers/Reviews/index.js</code></th>
<th><code>lib/containers/Reviews/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Reviews = ({ items }) => (
  <div className={style.container}>
    {items.map((item) => (
      <img src={item.image} alt={item.title} />
    ))}
  </div>
);

export default Reviews;
```

</td>
<td>

```css
.container > img {
  margin-left: 10px;
}

.container > img:first-child {
  margin-left: unset;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## Nested classes aren't for providing scope

CSS modules already provides us scope. We don't need to use nested classes
for providing scope isolation. Use nested class selectors for modifying
children based on parent class. A use case is when a component is in
error or success state:

<table>
<thead>
<th colspan="2"><strong>BAD</strong></th>
</thead>
<thead>
<th><code>lib/components/Button/index.js</code></th>
<th><code>lib/components/Button/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Button = ({ children }) => (
  <button className={style.button}>
    <img className={style.icon} />
    {children}
  </button>
);

export default Button;
```

</td>
<td>

```css
.button {
  box-sizing: border-box;
  padding: 10px;
  width: 100%;
}

.button .icon {
  width: 22px;
  height: 22px;
}
```

</td>
</tr>
</tbody>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<tbody>
<thead>
<th><code>lib/components/Input/index.js</code></th>
<th><code>lib/components/Input/style.css</code></th>
</thead>
<tbody>
<tr>
<td>

```js
import style from "./style.css";

const Input = ({ value, onChange, error }) => (
  <div className={classNames({ [style.error]: error })}>
    <input onChange={onChange} />
    <p>{error}</p>
  </div>
);

export default Input;
```

</td>
<td>

```css
.error p {
  color: red;
  display: unset;
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**

## Variables, lots of variables!

We encourage the "variabilification". Always define variables to increase
reuse and make styles more consistent. The CSS4 specification defines a way
to declare native variables. We adopted them as the standard.

To define a variable accessible globally:

<table>
<thead>
<th colspan="2"><strong>GOOD</strong></th>
</thead>
<thead>
<th><code>app/App/variables.css</code></th>
<th><code>app/components/Button/styles.css</code></th>
</thead>
<tbody>
<tr>
<td>

```css
:root {
  --color-green-1: #6ccfae;
  --color-green-2: #6b66b5;
  --color-green-3: #aac257;
  --color-green-4: #68b5c1;
}
```

</td>
<td>

```css
.container {
  background-color: linear-gradient(
    0deg,
    var(--color-green-1),
    var(--color-green-2)
  );
}
```

</td>
</tr>
</tbody>
</table>

**[⬆ back to top](#table-of-contents)**
