---
disqus: hackmd
---


Web accessibility \(also referred to as [**a11y**](https://en.wiktionary.org/wiki/a11y)\) is the design and creation of websites that can be used by everyone. Accessibility support is necessary to allow assistive technology to interpret web pages.

React fully supports building accessible websites, often by using standard HTML techniques.

## Standards and Guidelines

### WCAG

The [Web Content Accessibility Guidelines](https://www.w3.org/WAI/intro/wcag) provides guidelines for creating accessible web sites.

The following WCAG checklists provide an overview:

* [WCAG checklist from Wuhcag](https://www.wuhcag.com/wcag-checklist/)
* [WCAG checklist from WebAIM](http://webaim.org/standards/wcag/checklist)
* [Checklist from The A11Y Project](http://a11yproject.com/checklist.html)

### WAI-ARIA

The [Web Accessibility Initiative - Accessible Rich Internet Applications](https://www.w3.org/WAI/intro/aria) document contains techniques for building fully accessible JavaScript widgets.

Note that all `aria-*` HTML attributes are fully supported in JSX. Whereas most DOM properties and attributes in React are camelCased, these attributes should be hyphen-cased \(also known as kebab-case, lisp-case, etc\) as they are in plain HTML:

```text
<input
  type="text"
  aria-label={labelText}
  aria-required="true"
  onChange={onchangeHandler}
  value={inputValue}
  name="name"
/>
```

## Semantic HTML

Semantic HTML is the foundation of accessibility in a web application. Using the various HTML elements to reinforce the meaning of information in our websites will often give us accessibility for free.

* [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)

Sometimes we break HTML semantics when we add `<div>` elements to our JSX to make our React code work, especially when working with lists \(`<ol>`, `<ul>` and `<dl>`\) and the HTML `<table>`. In these cases we should rather use [React Fragments](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/docs/fragments.html) to group together multiple elements.

For example,

```text
import React, { Fragment } from 'react';

function ListItem({ item }) {
  return (
    <Fragment>
      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </Fragment>
  );
}

function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        <ListItem item={item} key={item.id} />
      ))}
    </dl>
  );
}
```

You can map a collection of items to an array of fragments as you would any other type of element as well:

```text
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // Fragments should also have a `key` prop when mapping collections
        <Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </Fragment>
      ))}
    </dl>
  );
}
```

When you don't need any props on the Fragment tag you can use the [short syntax](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/docs/fragments.html#short-syntax), if your tooling supports it:

```text
function ListItem({ item }) {
  return (
    <>
      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </>
  );
}
```

For more info, see [the Fragments documentation](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/docs/fragments.html).

## Accessible Forms

### Labeling

Every HTML form control, such as `<input>` and `<textarea>`, needs to be labeled accessibly. We need to provide descriptive labels that are also exposed to screen readers.

The following resources show us how to do this:

* [The W3C shows us how to label elements](https://www.w3.org/WAI/tutorials/forms/labels/)
* [WebAIM shows us how to label elements](http://webaim.org/techniques/forms/controls)
* [The Paciello Group explains accessible names](https://www.paciellogroup.com/blog/2017/04/what-is-an-accessible-name/)

Although these standard HTML practices can be directly used in React, note that the `for` attribute is written as `htmlFor` in JSX:

```text
<label htmlFor="namedInput">Name:</label>
<input id="namedInput" type="text" name="name"/>
```

### Notifying the user of errors

Error situations need to be understood by all users. The following link shows us how to expose error texts to screen readers as well:

* [The W3C demonstrates user notifications](https://www.w3.org/WAI/tutorials/forms/notifications/)
* [WebAIM looks at form validation](http://webaim.org/techniques/formvalidation/)

## Focus Control

Ensure that your web application can be fully operated with the keyboard only:

* [WebAIM talks about keyboard accessibility](http://webaim.org/techniques/keyboard/)

### Keyboard focus and focus outline

Keyboard focus refers to the current element in the DOM that is selected to accept input from the keyboard. We see it everywhere as a focus outline similar to that shown in the following image:

![Blue keyboard focus outline around a selected link.](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/images/docs/keyboard-focus.png)

Only ever use CSS that removes this outline, for example by setting `outline: 0`, if you are replacing it with another focus outline implementation.

### Mechanisms to skip to desired content

Provide a mechanism to allow users to skip past navigation sections in your application as this assists and speeds up keyboard navigation.

Skiplinks or Skip Navigation Links are hidden navigation links that only become visible when keyboard users interact with the page. They are very easy to implement with internal page anchors and some styling:

* [WebAIM - Skip Navigation Links](http://webaim.org/techniques/skipnav/)

Also use landmark elements and roles, such as `<main>` and `<aside>`, to demarcate page regions as assistive technology allow the user to quickly navigate to these sections.

Read more about the use of these elements to enhance accessibility here:

* [Accessible Landmarks](http://www.scottohara.me/blog/2018/03/03/landmarks.html)

### Programmatically managing focus

Our React applications continuously modify the HTML DOM during runtime, sometimes leading to keyboard focus being lost or set to an unexpected element. In order to repair this, we need to programmatically nudge the keyboard focus in the right direction. For example, by resetting keyboard focus to a button that opened a modal window after that modal window is closed.

MDN Web Docs takes a look at this and describes how we can build [keyboard-navigable JavaScript widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets).

To set focus in React, we can use [Refs to DOM elements](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/docs/refs-and-the-dom.html).

Using this, we first create a ref to an element in the JSX of a component class:

```text
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // Create a ref to store the textInput DOM element
    this.textInput = React.createRef();
  }
  render() {
  // Use the `ref` callback to store a reference to the text input DOM
  // element in an instance field (for example, this.textInput).
    return (
      <input
        type="text"
        ref={this.textInput}
      />
    );
  }
}
```

Then we can focus it elsewhere in our component when needed:

```javascript
 focus() {
   // Explicitly focus the text input using the raw DOM API
   // Note: we're accessing "current" to get the DOM node
   this.textInput.current.focus();
 }
```

Sometimes a parent component needs to set focus to an element in a child component. We can do this by [exposing DOM refs to parent components](https://github.com/Yukaii/documentation-tester/tree/d41653ad491aad24215f9c073c26434eaba7f63a/docs/refs-and-the-dom.html#exposing-dom-refs-to-parent-components) through a special prop on the child component that forwards the parent's ref to the child's DOM node.

```text
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.inputElement = React.createRef();
  }
  render() {
    return (
      <CustomTextInput inputRef={this.inputElement} />
    );
  }
}

// Now you can set focus when required.
this.inputElement.current.focus();
```

