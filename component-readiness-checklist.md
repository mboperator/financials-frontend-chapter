# The Component Readiness Checklist

> by: The FF Chapter

## Our Mission
We want our components to be performant 🚀, easy to implement 👓, and  internally consistent 💪.

## Container Components
- [ ] 👓 PropTypes are filled out
- [ ] 🚀 shouldComponentUpdate is implemented
- [ ] 🚀 Values derived from props should be calculated in selector if possible

## Presentational Components
- [ ] 👓 PropTypes are filled out
- [ ] 🚀 shouldComponentUpdate is implemented
- [ ] 👓 Props are clearly defined (not spread everywhere)

## Modules
- [ ] 💪 Data deserialization is happening in the middleware

## Selectors
- [ ] 🚀 toJS is avoided, or being used in its own selector
