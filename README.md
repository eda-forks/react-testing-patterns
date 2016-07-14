# Idiomatic React Testing Patterns

Testing React components seems simple _at first_. Then you need to test something that isn't a pure interaction and
things seem to break down. These 4 patterns should help you write readable, flexible tests for the type of component you are testing.

## Setup

I recommend doing all setup in the most functional way possible. If you can avoid it, don't set variables in a
`beforeEach`. This will help ensure tests are isolated and make things a bit easier to reason about. I use a pattern
that gives great defaults for each test example but allows every example to override `props` when needed:

```js
const getComponent = (props = {}) => {
	// Any test can override the default props by passing an object to the getComponent function
	props = Object.assign({
		onChange: sinon.spy(),
		title: 'Test Title',
		color: 'red'
	}, props);

	const component = ReactDOM.findDOMNode(TestUtils.renderIntoDocument(
		<MyComponent {...props} />
	));

	return Object.assign(props, { component });
};

// Usage
const { component, onChange } = getComponent();
const { component } = getComponent({ onChange: someFunc });
```

## The Patterns

There are 4 primary patterns that I've identified, have more ideas? Provide examples and rationale in the comments!

### Preferred "pure" patterns
- [**Basic rendering**](#file-test_render-js): how props change render results
- [**Stateless interactions**](#file-test_stateless_interaction-js): how interactions change render results or interact with callbacks supplied as props

### Impure patterns
Inevitably you will have impure components, these patterns provide consistency in how you test these types of interactions
- [**Stateful interactions**](#file-test_stateful_interaction-js): how interactions change render results relying on `this.state` or how lifecycle hooks behave
- [**Instance methods**](#file-test_instance_methods-js): how the component interacts with external libraries or APIs (very rare)

## Disclaimer

You may have a different testing stack and YMMV with how well these patterns work within that environment. For reference,
here is the stack we use at Cratejoy and the one I've had the most success with:

- [Karma](https://github.com/karma-runner/karma) test runner (configured with browserify + babel)
- [Mocha](https://github.com/mochajs/mocha) test framework
- [Chai](https://github.com/chaijs/chai) expect library
- [Sinon](https://github.com/sinonjs/sinon) mock library
