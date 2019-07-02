# ***Context API***

## **What Is A Context API?**
A context API effectively allows you to keep the app's state and various functions in one place. After initialising the context in a component that component then has access to use the context's state and functions.

## **How To Set Up**
Firstly start off by creating a new js file for your Context API (eg. context.js). This file will usually be in the same folder as index.js and App.js.

### **Imports And Boilerplate**
The API will be a classed based component. This will demonstrate how to create a higher order API.
Examples are from the [Beach Resort](https://youtu.be/ScDWrogElmo) tutorial.

For imports only the following is required:
```js
import React, { Component } from 'react';
```

Next create the context:
```js
const RoomContext = React.createContext()
```

Following this the React Component class:
```js
class RoomProvider extends Component {
  state = {}

  render() {
    return (
      <RoomContext.Provider value={{...this.state}}>
        {this.props.children}
      </RoomContext.Provider>
    );
  }
}
```

Create an API consumer:
```js
const RoomConsumer = RoomContext.Consumer;
```

Exports:
```js
export function withRoomConsumer(Component) {
  return function ConsumerWrapper(props) {
    return (
      <RoomConsumer>
        {value => <Component {...props} context={value}/>}
      </RoomConsumer>
    );
  }
}

export { RoomProvider, RoomConsumer, RoomContext };
```

### **Wrap App Within The API**
To provide access to the Context API across the entire app we wrap it within the context just like we would with the Router in index.js.
```js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router } from 'react-router-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { RoomProvider } from './context';

ReactDOM.render(
  <RoomProvider>
    <Router>
      <App />
    </Router>
  </RoomProvider>
  
  , document.getElementById('root'));

serviceWorker.unregister();
```

Now the API is setup in a way that it can be eaisly be accessed by the entire app.

## **Using The API In A Component**

There are different ways of utilising the API in both functional and class based components.

### **Classed Based Components**
Import the context:
```js
import { RoomContext } from '../context';
```

Initialize the context within the component class:
```js
static contextType = RoomContext;
```

Deconstructing functions from the context:
```js
const { getRoom } = this.context;
// Additional functions can be deconstructed at once by seperating them with a comma.
// eg. { functionOne, functionTwo, functionThree }
```

### **Functional Based Components**
Import the context:
```js
import { useContext } from 'react';
import { RoomContext } from '../context';
```

Create the functional component and initialize the context:
```js
export default function RoomsFilter() {
  const context = useContext(RoomContext);

  // return
}
```

Deconstructing Context API state into variables:
```js
const {
  handleChange,
  type,
  capacity,
  price
} = context;
```