# Advanced React Techniques
## Fragments
A lot of times you might have to surround something in a `div` tag in order to satisfy React’s *Single Parent Element* requirement.  

```
//TABLE THAT USES THE COLUMN COMPONENT FOR TABLE DATA
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}

//NOT OKAY
class Columns extends React.Component {
  render() {
    return (
        <td>Hello</td>
        <td>World</td>
    );
  }
}

//BETTER SYNTAX
class Columns extends React.Component {
  render() {
    return (
      <div> //NOTICE WE NOW HAVE A DIV TAG SURROUNDING THE OUTERMOST ELEMENT
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

This becomes problematic because you wind up rendering a new div inside each column in your table now.  But React has introduced *Fragments* which allow you to easily create an single parent element while not unnecessarily rendering div elements.

```
//BEST SYNTAX
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}

//FRAGMENTS WILL TAKE A KEY PROP IF NEEDED, I.E. MAPPING OVER AN ARRAY
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // Without the `key`, React will fire a key warning
        <React.Fragment key={item.id}> //NOTICE THE KEY PROP
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```


### CONTEXT API

When to use: 
	* Context is designed to share data that can be considered “global” for a tree of React components.
		* Security, color themes, etc… 
	* If there is a prop that you’re passing down through several components, or one that you need to re-use several times throughout a component tree 
What is it?
	* The *Context* is a single object that is accessible through the Context API, which provides a *Provider* and *Consumer*, and that’s it!
```
const MyCoolComponent = React.createContext(values);
<MyCoolComponent.Provider values={sport: 'foosball'}>
<MyCoolComponent.Consumer>
{values => <div>{values.sport}</div>}
<MyCoolComponent.Consumer />
```

### HIGHER-ORDER COMPONENTS
A higher-order component (HOC) is an advanced technique in React for reusing component logic. HOCs are not part of the React API, per se. They are a pattern that emerges from React’s compositional nature.

What is it? 
	* It is a *Pure Function* that wraps, and then composes a component that is passed to it.  

```
function withSubscription(WrappedComponent, selectData) {
  // ...and returns another component...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ... that takes care of the subscription...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... and renders the wrapped component with the fresh data!
      // Notice that we pass through any additional props
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}

//Implementation
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);

```

Challenge:
[Render Props – React](https://reactjs.org/docs/render-props.html)
Render the cat that chases the mouse.
Use the context api to set a new image after you click a button.

https://gist.githubusercontent.com/mjackson/e53ef1e0be541a3aa11cbc8e7d9ccf54/raw/2a27efe4b34a2f95f38d790438b7d686288810fc/HOC.js
