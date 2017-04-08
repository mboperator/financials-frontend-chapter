# The Fractal Component Architecture

> by: M. Bernales

The Elm language advertises itself as a `delightful language for reliable webapps`. Perhaps its biggest yet understated benefit is that it's composable by design. Elm views are defined with their state management and side effects in line. These views can then be composed by parent views to create complex applications with side effects (such as localStorage, API calls) occuring at every level of the application. The composability of side effects is what gives this pattern its power.

By using `redux-loop` and `redux-modules`, we're able to emulate the simplicity and composablilty of `Elm` while programming with `javascript` and `react`.

These technologies combined allow us to compose redux applications together to create a single large application intuitively, without significantly increasing state management complexity.


## Normalized vs Denormalized State
Let's explore the example of a message board app. In idiomatic `redux`, the state tree might look something like this:
![DIAGRAM OF FLAT STATE WITH BOARDS, POSTS, COMMENTS](https://i.imgur.com/OHEd3AD.jpg)

Note that the state is organized by resource. Also note that each child resource is stored in a key that corresponds to its immediate parent. This pattern is a necessity due to the fact that `redux-thunk` and `redux-saga` work best when side effects are handled at the root. As a result, we must recreate the nested relationships in the selector, before the view is rendered.

This is how the same application state might be represented with a fractal approach:
![DIAGRAM OF FRACTAL STATE WITH BOARDS, POSTS, COMMENTS](https://i.imgur.com/Za6p9HA.jpg)
Note the intuitive relationship between the boards, posts, and comments. **The ability to handle side effects that occur deep within the state tree allows developers to create a state that more accurately depicts the true shape of the data.**

## Some Example Apps
With this strategy, each application can be developed like any other normal `redux` application. State is managed as if this component is the root. Side effects are handled at the root level as well.

Here are a few mini apps we created to illustrate the example:
### PokemonMe!
PokemonMe uses the public `Pokemon API` to deliver nostalgia on every button press. The app is comprised of of a single Component that holds the view, and a module which takes care of fetching and caching responses from the Pokemon API.

![PokemonMe Demo](https://i.imgur.com/GhT6J0k.gif)

[Link to repository](https://github.com/procore/redux-modules/tree/master/examples/PokemonMe/src)

### Stopwatch
A self managed stopwatch component which increments its time until the reset button is clicked.
![Stopwatch Demo](https://i.imgur.com/JJk8MtX.gif)

[Link to repository](https://github.com/procore/redux-modules/tree/master/examples/Stopwatch/src)

### SplitPane
SplitPane is an attempt to replicate pane splitting, similar to `tmux` and `iTerm`. The app comprises of a single Component that holds the view, and a module which takes care of state transformations and side effects.

This is a great example of the power of `redux-modules` and `loops` in that each parent box delegates state transformations to its children to the child reducer.
![SplitPane D](https://i.imgur.com/ha2bLxn.gif)

[Link to Repository](https://github.com/procore/redux-modules/tree/master/examples/SplitPane/src)

## Putting it together

![Infinite Split Pane](https://i.imgur.com/zHJY33E.gif)
This infinite split pane example tests the limits of the pattern.

Each of the apps rendered share a slice of the full parent state. By keeping the state nested and denormalized, there's no added complexity when it comes to managing child components.

Additionally, because each `parent` component manages its children, we have control over child state at every level. This gives us the ability to respond to global actions, as well as local ones.

[Link to repository](https://github.com/mboperator/infinitely-composable-webapp-demo)

## Benefits
### True composability
React components themselves are incredibly composable, our existing state management solutions not so much. This pattern allows developers to compose the components, their state, and the [side effects](https://github.com/reactjs/redux/issues/1528) that they might generate. This allows us compose _complete applications_, not just views.

### Reducers are always written as if they are operating on the root state
State transformations and side effects for child resources and regular resources are modeled exactly the same. This means that `comments` belonging to a `post`, `board`, or `user` can all use the same reducer.

### No more data joins in selectors
A common strategy used today is to use normalizr to pull nested resources into their own slice of the state tree, much like tables in a relational database. Having every resource stored in a slice of the root state, makes handling side effects is easy.

In practice we've noticed that this strategy becomes unwieldy at scale because of a few things:
- It requires us to recreate the relationships in the selector, which means we're effectively taking the denormalized server response, normalizing it in our state tree, and then denormalizing it again once we render the view.
- Updates to nested resources generally requires the id of the `parent` resource.
- It's not easy to reuse the same reducers and side effects. For example, at Procore we have a filter widget (originally backed by redux and thunks) used across multiple tables. When we wanted to render multiple Filter widgets on the page, we found that an `path` prop needed to be added and passed around with every action so that the filter would be able to access the correct slice of state to update.

### Side effects are replayable
`redux-loop` is designed in such a way that, unlike `redux-saga` and `redux-thunk`, only the action which kicks off side effects is recorded. Actions that are emitted as a result of a side effect, are not. This means that replaying a set of recorded actions - even those with side effects - will produce the same result (as long as the side effects don't throw).

## Conclusion
This pattern is the result of dozens of experiments in building reusable UI components throughout the two years that Procore been using React in production. This is not the only pattern we employ at here, but it's one that is quickly becoming adopted across our organization due to its ease of reuse and simplicity.

Thanks for reading! Check back next time for a post covering implementation, as well as a deep dive on nested effects.

## References
- [redux #1528 - Reducer Composition with Effects in JavaScript](https://github.com/reactjs/redux/issues/1528)
- [Composing Features and Behaviors in the Elm Architecture](https://github.com/foxdonut/adventures-reactive-web-dev/tree/master/client-elm#composing-features-and-behaviours-in-the-elm-architecture)
- [redux-modules](https://github.com/procore/redux-modules)
- [redux-loop](https://github.com/redux-loop/redux-loop)
