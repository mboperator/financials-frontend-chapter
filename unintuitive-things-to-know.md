# Unintuitive Things to Know

> by: M.Bernales & M. Hinrichs

## Selectors.

### Problem:
>It's easy to assume that the state is being passed directly to components as props when using `connectModule`

Potential Solutions:
- Better explanation of the selector concept
- Better explanation of how state is translated to props via `connect` or `connectModule`

### Problem:
>Selectors are used everywhere (sagas, modules, handlers, in their own files, as functions etc)

Potential Solutions:
- Better explanation of the selector concept
- Stronger opinion as to where to define them
- Documentation of places where they're commonly used
- Selector uses in general

## Method naming: handleX vs onX

### Convention:
- Use `handle` when naming methods that are called internally.
```js
class ClickyBox extends React.Component {
  handleClick() {
  	console.log('Boop');
  }

  render() {
  	return (
		<div onClick={this.handleClick}>
			Click me.
		</div>
	)
  }
}
```
- Use `on` when method is given to another component as props
```js
const SelectableOption = ({onSelect}) => (
	<li onClick={onSelect}>
		Something cool
	</li>
)
```

## CSS Modules
### Problem
> Currently there are two ways to define scss files for components

First Method:
```
OptionList /
  - __tests__/
  - index.js
  - OptionList.jsx
  - optionList.scss
  - Option.jsx
  - option.scss
```

Second Method:
```
OptionList /
  - __tests__/
  - styles/
    - optionList.scss
    - option.scss
  - index.js
  - OptionList.jsx
  - Option.jsx
```

## Structuring Sagas
### Problem
> Saga file organization is ambiguous at best. Currently we loosely define sagas 1:1 with their module, or one saga per file.

### Proposed Convention:
Store saga functions in a file that corresponds to the module which contains the actions that they dispatch.

```js
function* fetchAvailableBulkActions() {
  const bulkActionsEndpoint = yield select(
    state => state.app.getIn(['endpoints', 'bulkActions'])
  );

  try {
    const { data } = yield get(bulkActionsEndpoint);
    yield put(bulkActionsModule.actions.fetchSuccess(data));
  } catch (e) {
    yield put(bulkActionsModule.actions.fetchError(e));
  }
}

export default function* lineItemsSaga() {
  yield [
    takeLatest([
      lineItemsModule.constants.selectLineItem,
      lineItemsModule.constants.deselectLineItem,
    ], fetchAvailableBulkActions),
  ];
}
```

Note that though we are _listening_ to `lineItemsModule` we are `dispatching` `bulkActionsModule` actions.

## Redux Modules
### Problem: Responding to an action from multiple reducers
```js
const module = createModule({
  name: 'bulkActions',
  initialState: fromJS({
    _loading: false,
    _errors: {},
    collection: {},
  }),
  transformations: [
    {
      type: lineItemsModule.constants.selectLineItem,
      namespaced: false,
      reducer: asyncStart,
    },
    {
      type: lineItemsModule.constants.deselectLineItem,
      namespaced: false,
      reducer: asyncStart,
    },
    {
      type: 'fetchSuccess',
      reducer: reduceReducers(
        asyncResolve,
        (state, { payload }) => {
          return state.set('collection', fromJS(payload));
        }
      ),
    },
  ],
})
```

## Slow inputs?
### Problem
> Inputs are hurky/jerky when entering text

### Solution
- Implement `shouldComponentUpdate`


## The standard action shape is:
reference: https://github.com/acdlite/flux-standard-action
```
{
  type: 'actionName',
  payload: // action payload, eg your array of table data,
  meta: // meta information
}
```



## The argument you call the action with becomes the payload.
```js
/***** EXAMPLE 1 *****/
// module.js
reducer: (state, { payload }) => {
	return (state.update('table', table => payload));
}
// App.jsx
<button onClick={() => this.props.actions.random( tableGen(3, 3 )}>
</button>

/***** EXAMPLE 2 *****/
// module.js
reducer: (state, { payload }) => {
	return (state.update('table', table => payload.table));
}
// App.jsx
<button onClick={() => this.props.actions.random({table: tableGen(3, 3 )}}>
</button>
```

3,
npm install --save dependency-name will add dependencies to package.json


## My Store has the wrong initial state:
if we have:
`initialState: {table: tableGen(3,3)},`
then we can omit the second argument in createStore:
`const store = createStore(tableModule.reducer);`


## My onClick handlers aren't working
pass a wrapper function in view:
`<button onClick={ () => this.props.actions.randomTable({ table: tableGen(3,3) }) }>random table</button>`
but not:
`<button onClick={ this.props.actions.randomTable({ table: tableGen(3,3) }) }>random table</button>`

## How do I run a single test?
```
npm run test:one -s dataUtils-test.js
```
