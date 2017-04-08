# PropTypes Philosophy and Example

> by: S. Spearman

## Purpose
To convince you that you should write `PropTypes` and provide some guidelines for how to write them.

## Before You Read
This document assumes familiarity with [`React.PropTypes`](https://facebook.github.io/react/docs/typechecking-with-proptypes.html).

## Why Write PropTypes?
- `PropTypes` serve as a form of documentation for users of a component and provide feedback when a user is not using a component as intended.
- Writing `PropTypes` forces one to think about the interface of their component.

## When to Use `isRequired`?
Chaining `.isRequired` onto any `PropTypes` declaration will cause a console warning when that declaration is not obeyed. As a rule of thumb, if the component will not "work" without a prop of a certain type, `isRequired` should be used. "Working" is a subjective term and, but at the very least the component should not produce any sort of JavaScript errors due to invalid props without also providing a console warning through `PropTypes`.

For example, let's say we have a `List` component that renders a `ListItem` for each item in a received list prop.
```jsx=
const List = ({ list }) => {
  return (
    list.map((item) => {
      <ListItem item={item} key={item.id} />
    });
  );
};

const ListItem = ({ text, title }) => {
  return (
    <h4>{title}</h4>
    <p>{text}</p>
  );
};

List.PropTypes = {
  list: arrayOf(shape({
    id: number.isRequired,
  })).isRequired,
};

ListItem.PropTypes = {
  text: string.isRequired,
  title: string.isRequired,
};
```

As it stands, `list` is a required prop because if it were not provided, the `list.map()` call would result in this error
```
Uncaught TypeError: Cannot read property 'map' of undefined
```
Furthermore, if a `list` prop is provided but the objects it contains do not have an `id` key, we would pass in `undefined` as the `key` prop to `ListItem`, which would result in all `ListItems` having the same key and cause some weird UI where not all your `ListItem`s will show up (see [this article for why correct keys are important](http://blog.arkency.com/2014/10/react-dot-js-and-dynamic-children-why-the-keys-are-important/)).

Both of these should qualify as *not* "working", and thus we've used `isRequired`.

## How Much Should I Define?
`PropTypes.shape` and `PropTypes.arrayOf` allow for definition of the shapes and contents of props. As a rule of thumb, you should only define as much as your component uses.

For example, let's say we have a `List` component that renders a `ListItem` for each item in a received list prop.
```jsx=
const List = ({ list }) => {
  return (
    list.map((item) => {
      <ListItem item={item} key={item.id} />
    });
  );
};

const ListItem = ({ text, title }) => {
  return (
    <h4>{title}</h4>
    <p>{text}</p>
  );
};
```

The `List` and `ListItem` together require that there is a prop called `list` which is an array where each item has the shape `{ id, text, title }`. However, each component is not dependent on all of these requirements.

Defining only as much as each component uses yields this result:
```javascript=
List.PropTypes = {
  list: arrayOf(shape({
    id: number.isRequired,
    /* `text` and `title` are part of each item too,
     * but are not used in this component
     */
  })).isRequired,
};

ListItem.PropTypes = {
  text: string.isRequired,
  title: string.isRequired,
};
```

If we were to now add a date field to `ListItem`, we need only to change `ListItem.PropTypes` while `List` is still only concerned with passing down a list.


## Immutable PropTypes
If your props are [immutable](https://facebook.github.io/immutable-js/) you can use the [react-immutable-proptypes](https://github.com/HurricaneJames/react-immutable-proptypes) package.

## Import Style
