# Normalized vs Denormalized State

Let's explore the example of a message board app. In idiomatic `redux`, the state tree might look something like this:  
![DIAGRAM OF FLAT STATE WITH BOARDS, POSTS, COMMENTS](https://i.imgur.com/OHEd3AD.jpg)

Note that the state is organized by resource. Also note that each child resource is stored in a key that corresponds to its immediate parent. This pattern is a necessity due to the fact that `redux-thunk` and `redux-saga` work best when side effects are handled at the root. As a result, we must recreate the nested relationships in the selector, before the view is rendered.

This is how the same application state might be represented with a fractal approach:  
![DIAGRAM OF FRACTAL STATE WITH BOARDS, POSTS, COMMENTS](https://i.imgur.com/Za6p9HA.jpg)  
Note the intuitive relationship between the boards, posts, and comments. **The ability to handle side effects that occur deep within the state tree allows developers to create a state that more accurately depicts the true shape of the data.**

