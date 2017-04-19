# A Trivial React Component

> by: Michael Cola

# React Components in `procore/procore` and `procore/wrench`

### Why use `react_component`?
* The problem may have been solved by someone else, you don't need to reinvent the wheel.
* Back End engineers need only produce the properties necessary to implement a shared component, and don't have to worry about creating the component in wrench, only where it is on the page

## Usage

### Requirements
* Component is exposed to the `Window` in `procore/wrench` via [`src/components.js`](https://github.com/procore/wrench/blob/master/src/components.js)
	* Component must be defined and exported from the file location defined in `src/components.js`
* Usage in procore is on an `.erb` template, surrounding by ruby HTML interpolation: `<%= react_component() %>`

### API for the `react_component` method
* in [`app/helpers/application_helper.rb`](https://github.com/procore/procore/blob/master/app/helpers/application_helper.rb)
* `react_component` takes in a component class name, and the props necessary for that component to render, as a hash
  * Note: the keys in the hash are the same keys being read in wrench for the props on the component
	* Ex:
```ruby
react_component(
  "ExampleComponent",
  {
    links: @links
  }
)
```
### Component exposition in wrench
* Add a line to `src/components.js`
	* `import ExampleComponent from 'components/ExampleComponent';`
	* Component definition:
```jsx
import React, { PropTypes } from 'react';

class ExampleComponent extends React.Component {
  static propTypes = {
    editable: PropTypes.bool,
    links: PropTypes.object.isRequired,
  };

  static defaultProps = {
    editable: false,
  }

  render() {
    return (
      <a href={links.redirect} >
        Take me to your leader
      </a>
    );
  }
}
export default ExampleComponent;
```
**Note:** You may notice a different syntax for defining a component in wrench
```jsx
import { PropTypes } from 'react';

const ExampleComponent = (props) => {
  const { links } = props;

  return (
    <a href={links.redirect} >
      Take me to your leader
    </a>
  )
};

ExampleComponent.propTypes = {
  links: PropTypes.object.isRequired,
  editable: PropTypes.bool,
};

ExampleComponent.defaultProps = {
  editable: false,
};

export default ExampleComponent;
```
This is called a "Pure Component".

## Procore-specific Example: <FeedbackButton />
### procore/procore
In [`app/views/utilities/_beta_button.html.erb`](https://github.com/procore/procore/blob/master/app/views/utilities/_beta_button.erb#L2)
* `forum_name`, and `feedback_email` are strings, passed into the `beta_button` method, as defined [here](https://github.com/procore/procore/blob/master/app/helpers/application_helper.rb#L321).
```erb
  <%= react_component('FeedbackButton', { forumName: forum_name, email: feedback_email }) %>
```

### Wrench
In [`src/_shared/components/FeedbackButton/FeedbackButton.jsx`](https://github.com/procore/wrench/blob/master/src/_shared/components/FeedbackButton/FeedbackButton.jsx)
```jsx
import React, { PropTypes } from 'react';
import cx from 'classnames';

class FeedbackButton extends React.Component {
  static propTypes = {
    ...
    forumName: PropTypes.string,
    email: PropTypes.string,
  };

  static defaultProps = {
    ...
    forumName: 'Customer Feedback Forum.',
    email: '',
  };
  ...

  render() {
    const {
      ...
      forumName,
      email
    } = this.props;

    return (
      <div className="no_print beta-feedback inline-block">
        <div
          className="beta-feedback-button clickable fancybox"
          href="#beta-msg"
        >
          <div className="beta-feedback-title">{buttonHeader}</div>
          {buttonSubtitle && <div className="beta-feedback-subtitle">{buttonSubtitle}</div>}
          <span className="absolute abs-right abs-middle">
            <div className="beta-feedback-circle">&rarr;</div>
          </span>
        </div>
        <div className="no_print" id="beta-msg">
          <div className="fancybox-header">
            <h1>{header}</h1>
          </div>
          <section>
            <div>{description}</div>
            <div className="padding-big">
              {innerSubtitle && <h3>{innerSubtitle}</h3>}
              {bulletPoints && <ul className="base-list padding-top">
                {bulletPoints.map((bulletPoint, i) => {
                  return <li key={i}>{bulletPoint}</li>;
                })}
              </ul>}
            </div>
            <div>
              <p>
                <span>{supportDocumentationDescription}</span>
                <a target="_blank" href={supportDocumentationLink}>click here</a>.</p>
            </div>
            <div>
              <span>{message}</span>
              <span>
                <a
                  className={cx({ 'feedback-email': email })}
                  href={email ? `mailto:${email}` : feedbackForumLink}
                >
                  {forumName}
                </a>
              </span>
            </div>
          </section>
        </div>
      </div>
    );
  }
}

export default FeedbackButton;

```
