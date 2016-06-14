---
layout: post
title: Using React Router
---

When building out applications with a server-side framework like Rails, one of its nicer features is the ability to *quickly* and *easily* set up routing. When working with a client-side application, it can become difficult for the user to infer what page they are on based on the url. Ember, a JavaScript framework, was one of the first to implement a solution for route handling. Ember Router provided developers the ability to change the url based on what template was being shown. Ember Router has spawn spinoffs for many of the major frameworks and libraries out there. We will be taking a look at React Router and how we can easily incorporate routing into a react application. Let's get started!

## Conceptualizing React Router

The idea behind routing is given a url, that url should point to a specific view. In our case, a view can refer to a specific group of components that are currently being rendered. If we are following RESTful conventions, a user can infer that the url `/users/julianfeliciano` is the user page of Julian Feliciano. Since there are no page refreshes when building out a react application, we want to be able to tell our application to update the url address based on a component.

We will keep our react application very simple to illustrate working with react router. Our application will have a landing page that allows us to type in a username, click on a submit button, and render a user page that shows the updated url and username on that page. First off, let's setup the our tooling.

## Getting Up and Running

To get started, clone done [this](https://github.com/julsfelic/webpack-boilerplate) webpack boilerplate and follow the instructions on the README to get setup. Once you have all your dependencies installed, go ahead and run your server in a seperate terminal window. We are ready to start making components!

# Creating the <Landing /> Component

The first component we want to create is the main Landing component. This component will point to the root (`/`) of our application. We will first hardcode the rendering of this component into our application, then we will slowly introduce react router into the mix. First off, let's create a directory at `/client/components/` then create our Landing component:

```js
/* /client/components/Landing.js */

import React from 'react';

class Landing extends React.Component {
  render() {
    return(
      <section className="landing">
        <h1>Learning React Router</h1>
        <h3>Please enter a username</h3>
        <input type="text" ref="username" />
        <button>Login</button>
      </section>
    );
  }
}

export default Landing;
```

This sets up a landing component that will eventually allow us to enter a username and navigate to that the users page. In order to render the Landing component we need to import it into our `App.js` file:

```js
/* /client/app.js */

import React from 'react';
import { render } from 'react-dom';

import Landing from './components/Landing';

render(<Landing />, document.getElementById('root'));
```

If you have seen a basic react app you'll see that there isn't anything very fancy going on at the moment. We are specifying our rendering target as `react-dom` since we are working with the DOM, importing our newely created `Landing` component and rendering the `Landing` component to our `index.html` page. If you were to take a look at the `index.html` page you will see that there is a empty `div` that has the id of root. Now that we have the Landing component being displayed, next we'll make our rendering our components a little more flexible by incoporating routing.

## Using React Router to Connect Landing With Our Root Route

As it stands, we can type any route we want in the url and get the same component every single time. What we would like is to have the root route (`/`) point to the Landing component, have the user show page (`/users/:userId`) point to our eventual User component and have a catch all component called NotFound presents a 404 page for any other route. We will first connect up Landing to the root.

Here is the updated code for `app.js`:

```js
/* /client/app.js */

import React from 'react';
import { render } from 'react-dom';
import { Router, Route, browserHistory } from 'react-router';

import Landing from './components/Landing';

const routes = (
  <Router history={browserHistory}>
    <Route path="/" component={Landing} />
  </Router>
);

render(routes, document.getElementById('root'));
```

Let's tackle this code piece by piece.

```js
import { Router, Route, browserHistory } from 'react-router';
```

This brings in the necessary dependencies in order to use react router. `Router` provides the routing context, `Route` gives us the ability to create routes and `browserHistory` provides browsing history to the router to allow the back and foward controlls to work as expected.

```js
const routes = (
  <Router history={browserHistory}>
    <Route path="/" component={Landing} />
  </Router>
);
```

This sets up our routes. To keep our code clean I am assigning the router to a constant, but you could also create the routes directly in the render method. The nice thing about react router is that the modules we included are components themselves. Knowing this, it makes it trivial to setup simple routes. The first component `<Router>` sets up our applications routing context and wrap all of our route definitions. In order to provide back and forward functionality to our application, we give the history prop the browserHistory module that we imported earlier.

Next is our route definition. The `<Route>` component expects two props: the path prop that points to the url path that we want our component to point to and a component prop that tells the route which component to render at the given route. There are many options for creating routes, such as nested routes, but for our purposes this simple route gets us displaying our Landing component at the root. If we attempt to go to another route now, we see a blank page, which isn't good but lets us know that our routing is working as expected. Let's get a 404 page up and running so our users know that they have gone to a unexpected path.

## Creating the NotFound Component and Routing

We won't spend too much time with create this component. The main purpose for creating the NotFound component is to show that you can have a route that 'catches' any routes that are not found in our routing. Here is the code for the NotFound component:

```js
/* /client/components/NotFound.js */

import React from 'react';

const NotFound = () => {
  return (
    <h3>Page not found!</h3>
  );
};

export default NotFound;
```

And in `app.js`:

```js
/* /client/app.js */

import React from 'react';
import { render } from 'react-dom';
import { Router, Route, browserHistory } from 'react-router';

import Landing from './components/Landing';
import NotFound from './components/NotFound';

const routes = (
  <Router history={browserHistory}>
    <Route path="/" component={Landing} />
    <Route path="*" component={NotFound} />
  </Router>
);

render(routes, document.getElementById('root'));
```

The main piece of code to look at is:

```js
<Route path="*" component={NotFound} />
```

Using the asterisk character (`*`) we can catch all paths and render out the `NotFound` component. Be carefull when using a route like this. If you don't add this route to the end of your routing you could accidentaly override paths that you didn't mean too! If you now go to a path that doesn't exist, you will see our make shift 404 page.

Our last component, `User` proposes a few interesting problems. Firstly, how do we grab the users name from the url? Secondly, how do we write a route that contains a dynamic variable such as a username? Luckily, react router makes this trivial!

## Creating the User Component and Routing

For our initial attempt, let's render a stub User component so we can create a route for it. Here is that component:

```js
/* /client/components/User.js */

import React from 'react';

const User = () => {
  return (
    <h1>Welcome, stubbed user!</h1>
  );
};

export default User;
```

And the corresponding changes to `app.js`:

```js
/* /client/app.js */

import React from 'react';
import { render } from 'react-dom';
import { Router, Route, browserHistory } from 'react-router';

import Landing from './components/Landing';
import User from './components/User';
import NotFound from './components/NotFound';

const routes = (
  <Router history={browserHistory}>
    <Route path="/" component={Landing} />
    <Route path="/users/:userId" component={User} />
    <Route path="*" component={NotFound} />
  </Router>
);

render(routes, document.getElementById('root'));
```

If we take a closer look at our new route for User:

```js
<Route path="/users/:userId" component={User} />
```

You'll notice something a little different about it. Inside of path, we can specify a dynamic variable by prepending that portion of the path with a colon (`:`) like we have with `:userId`. Now if we go back to our application and type `/users/any-name-of-your-choosing` we will see our stubbed out User component. Let's update the stubbed component to show the dynamic username.

When using react router, we have access to any dynamic parameters through `props`. Specifically, we can access the `:userId` parameter through `this.props.params.userId`:

```js
/* /client/components/User.js */

import React from 'react';

const User = ({params}) => {
  return (
    <h1>Welcome, {params.userId}!</h1>
  );
};

export default User;
```

We could use `this.props.params.userId` to access the userId. Instead, I'm using a new feature of ES6 called destructuring that allows us to pull out specific keys from a hash. In the case of the User component, the `params` hash is passed into our component, so we can pull out the params with the `const User = ({params}) => {` line. If we head back to our app now and pass in different names into our route, you'll notice that the component can now display the dynamic name.

## Creating a Link to Navigate Back to Our Route

It would be nice if from the user page we could navigate back to the root page without having to go into the url and type in the root of the app. Luckily, react router provides another module that allows for easy link creation. Inside of the User component, let's add a home link that takes us back to the landing page:

```js
/* /client/components/User.js */

import React from 'react';
import { Link } from 'react-router';

const User = ({params}) => {
  return (
    <div>
      <h1>Welcome, {params.userId}!</h1>
      <Link to="/">Back to Landing</Link>
    </div>
  );
};

export default User;
```

In order to create links, we need to import the `Link` module from react router. The `Link` component allows us to easily create links within our application. All we need to do is specify the path we want and the text and react router will handle the rest!

The finish line is almost near! All that is left is wiring up our landing page sumbit button to take us to a users page.

## Using pushState and refs to Navigate to Another Page

Behind the scenes, react router is using the HTML5 API pushState to update the url. Sometimes, we want to manually update the url ourselves. Luckily, react router gives us access to pushState so we can handle our case of navigating to a users page when they click submit. Here is the updated code:

```js
/* /client/components/Landing.js */

import React from 'react';
import { History } from 'react-router';
import reactMixin from 'react-mixin';

class Landing extends React.Component {
  loadUser(e) {
    e.stopPropagation();
    let userName = this.refs.username.value;
    this.history.pushState(null, '/users/' + userName);
  }

  render() {
    return(
      <section className="landing">
        <h1>Learning React Router</h1>
        <h3>Please enter a username</h3>
        <input type="text" ref="username" />
        <button onClick={this.loadUser.bind(this)}>Login</button>
      </section>
    );
  }
}

reactMixin.onClass(Landing, History);

export default Landing;
```

There is quite a bit goign on now, so let's start from the top.

```js
import { History } from 'react-router';
import reactMixin from 'react-mixin';
```

In order to gain access to pushState, we need to include the `History` module from react router. Once we have that, we need to mixin the module into our Landing class. Unfortunetly with ES6 classes, they do not currently support mixins, so we are using the `reactMixin` module to allow us to mixin History into Landing.

```js
  loadUser(e) {
    e.stopPropagation();
    let userName = this.refs.username.value;
    this.history.pushState(null, '/users/' + userName);
  }
```

This is the function we want fired when the user clicks on submit. The first thing it does is stop any event from bubbling past the button. Secondly, we grab the value of the input field using the `ref` we had given it earlier. To grab a ref, we use the incantation of `this.refs.name-of-ref`. From there, we can grab the value of our input. Lastly, we use the `history` function that was mixed in to our class to access `pushState`. For our application, we won't worry about the first argument past into `pushState`. The second argument we give it is the url we want to go to. Since we have the value of the username through our ref, we can build up the url needed to navigate to the user page.

```js
reactMixin.onClass(Landing, History);
```

Here we are using the `reactMixin` module we imported earlier to mixin `History` into `Landing`. With that in place, we can type in any username and be directed to their page!

## Wrapping Up

Being able to provide routes that are human readable and relate to the content being shown is a great way to enhance the user experience. On top of that, routing can help bring clarity to your react app and bring many of the benefits of routing on the server side to your client side application.
