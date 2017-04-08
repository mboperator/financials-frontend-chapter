# Takeaways

### True composability

React components themselves are incredibly composable, our existing state management solutions not so much. This pattern allows developers to compose the components, their state, and the [side effects](https://github.com/reactjs/redux/issues/1528) that they might generate. This allows us compose _complete applications_, not just views.

### Reducers are always written as if they are operating on the root state

State transformations and side effects for child resources and regular resources are modeled exactly the same. This means that `comments` belonging to a `post`, `board`, or `user` can all use the same reducer.

### No more data joins in selectors

A common strategy used today is to use normalizr to pull nested resources into their own slice of the state tree, much like tables in a relational database. Having every resource stored in a slice of the root state, makes handling side effects is easy.

In practice we've noticed that this strategy becomes unwieldy at scale because of a few things:

* It requires us to recreate the relationships in the selector, which means we're effectively taking the denormalized server response, normalizing it in our state tree, and then denormalizing it again once we render the view.
* Updates to nested resources generally requires the id of the `parent` resource.
* It's not easy to reuse the same reducers and side effects. For example, at Procore we have a filter widget \(originally backed by redux and thunks\) used across multiple tables. When we wanted to render multiple Filter widgets on the page, we found that an `path` prop needed to be added and passed around with every action so that the filter would be able to access the correct slice of state to update.

### Side effects are replayable

`redux-loop` is designed in such a way that, unlike `redux-saga` and `redux-thunk`, only the action which kicks off side effects is recorded. Actions that are emitted as a result of a side effect, are not. This means that replaying a set of recorded actions - even those with side effects - will produce the same result \(as long as the side effects don't throw\).

## Conclusion

This pattern is the result of dozens of experiments in building reusable UI components throughout the two years that Procore been using React in production. This is not the only pattern we employ at here, but it's one that is quickly becoming adopted across our organization due to its ease of reuse and simplicity.

Thanks for reading! Check back next time for a post covering implementation, as well as a deep dive on nested effects.

## References

* [redux \#1528 - Reducer Composition with Effects in JavaScript](https://github.com/reactjs/redux/issues/1528)
* [Composing Features and Behaviors in the Elm Architecture](https://github.com/foxdonut/adventures-reactive-web-dev/tree/master/client-elm#composing-features-and-behaviours-in-the-elm-architecture)
* [redux-modules](https://github.com/procore/redux-modules)
* [redux-loop](https://github.com/redux-loop/redux-loop)



