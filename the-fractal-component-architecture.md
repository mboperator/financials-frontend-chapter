# The Fractal Component Architecture

> by: M. Bernales

The Elm language advertises itself as a `delightful language for reliable webapps`. Perhaps its biggest yet understated benefit is that it's composable by design. Elm views are defined with their state management and side effects in line. These views can then be composed by parent views to create complex applications with side effects \(such as localStorage, API calls\) occuring at every level of the application. The composability of side effects is what gives this pattern its power.

By using `redux-loop` and `redux-modules`, we're able to emulate the simplicity and composablilty of `Elm` while programming with `javascript` and `react`.

These technologies combined allow us to compose redux applications together to create a single large application intuitively, without significantly increasing state management complexity.



