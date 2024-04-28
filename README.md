### Software Engineering (Frontend) Interview Preparation

- Intro to System Design - Video by Arpit Bhayani
https://youtu.be/1r9bPisYaOQ?si=koc6_B8IMinJDkA0

These examples will be brief and targeted, ideal for interview preparation and quick revision.

### 1. Infinite Scroll
```javascript
// React example using IntersectionObserver for infinite scroll
function InfiniteScrollComponent() {
  const [items, setItems] = useState([]);
  const loader = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting) {
        loadMoreItems();
      }
    }, { threshold: 1.0 });
    observer.observe(loader.current);

    return () => observer.disconnect();
  }, []);

  function loadMoreItems() {
    fetchMoreData().then(newItems => {
      setItems(prevItems => [...prevItems, ...newItems]);
    });
  }

  return (
    <div>
      {items.map(item => <div key={item.id}>{item.content}</div>)}
      <div ref={loader}>Loading...</div>
    </div>
  );
}
```
**Explanation**: Uses `IntersectionObserver` to detect when the "Loading..." div becomes visible, triggering the data fetching function to load more items.

### 2. Search Bar
```javascript
// Simple search bar in React
function SearchBar({ onSearch }) {
  const [input, setInput] = useState("");

  const handleChange = event => {
    setInput(event.target.value);
    onSearch(event.target.value);
  };

  return <input type="text" value={input} onChange={handleChange} placeholder="Search..." />;
}
```
**Explanation**: A controlled component that updates the state upon every keystroke and triggers a search function passed as a prop.

### 3. Todo List
```javascript
// Simple todo list in React
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [task, setTask] = useState("");

  const addTask = () => {
    if (task) {
      setTodos([...todos, { id: Date.now(), text: task }]);
      setTask("");
    }
  };

  return (
    <div>
      <input type="text" value={task} onChange={e => setTask(e.target.value)} />
      <button onClick={addTask}>Add Task</button>
      <ul>
        {todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
      </ul>
    </div>
  );
}
```
**Explanation**: Manages a list of todos with state for both the list and the current input. Adding a task updates the todo list array.

### 4. AutoComplete
```javascript
// AutoComplete Component in React
function AutoComplete({ suggestions }) {
  const [filteredSuggestions, setFilteredSuggestions] = useState([]);
  const [input, setInput] = useState("");

  const handleChange = (event) => {
    const input = event.target.value;
    setInput(input);
    const filtered = suggestions.filter(suggestion =>
      suggestion.toLowerCase().startsWith(input.toLowerCase())
    );
    setFilteredSuggestions(filtered);
  };

  return (
    <div>
      <input type="text" onChange={handleChange} value={input} />
      <ul>
        {filteredSuggestions.map(suggestion => <li key={suggestion}>{suggestion}</li>)}
      </ul>
    </div>
  );
}
```
**Explanation**: Filters suggestions based on user input, displaying only those that match the current input prefix.

### 5. Routing
```javascript
// Basic routing example using React Router
import { BrowserRouter as Router, Route, Switch, Link } from 'react-router-dom';

function App() {
  return (
    <Router>
      <div>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
        </nav>
        <Switch>
          <Route path="/about">
            <About />
          </Route>
          <Route path="/">
            <Home />
          </Route>
        </Switch>
      </div>
    </Router>
  );
}

function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}
```
**Explanation**: Uses React Router for SPA routing. `Switch` renders the first child `Route` that matches the path.


### 6. Use of useContext
```javascript
// React context and useContext hook example
import React, { useContext, createContext, useState } from 'react';

const UserContext = createContext();

function App() {
  const [user, setUser] = useState({ name: "John Doe", age: 30 });

  return (
    <UserContext.Provider value={user}>
      <UserProfile />
    </UserContext.Provider>
  );
}

function UserProfile() {
  const user = useContext(UserContext);
  return <div>Name: {user.name}, Age: {user.age}</div>;
}
```
**Explanation**: The `UserContext.Provider` provides the `user` object to nested components. `UserProfile` accesses this context with `useContext`, making the user data available within.

### 7. Custom Hook
```javascript
// Example of a custom hook in React for managing local storage
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.log(error);
      return initialValue;
    }
  });

  const setValue = value => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.log(error);
    }
  };

  return [storedValue, setValue];
}
```
**Explanation**: This custom hook abstracts local storage access and updates, providing an API similar to `useState` but persisting the state to local storage.

### 8. Implementation of Redux Saga
```javascript
// Redux Saga for asynchronous actions in Redux
import { takeLatest, call, put } from 'redux-saga/effects';
import axios from 'axios';

function* fetchUser(action) {
  try {
    const user = yield call(axios.get, `/api/user/${action.userId}`);
    yield put({ type: 'USER_FETCH_SUCCEEDED', user: user.data });
  } catch (error) {
    yield put({ type: 'USER_FETCH_FAILED', message: error.message });
  }
}

function* mySaga() {
  yield takeLatest('USER_FETCH_REQUESTED', fetchUser);
}

export default mySaga;
```
**Explanation**: This Redux Saga listens for `USER_FETCH_REQUESTED` actions and calls `fetchUser` saga which performs the API call. Success or failure actions are dispatched based on the API call result.

### 9. Stopwatch
```javascript
// Simple stopwatch component in React
import React, { useState, useEffect } from 'react';

function Stopwatch() {
  const [time, setTime] = useState(0);
  const [running, setRunning] = useState(false);

  useEffect(() => {
    let interval;
    if (running) {
      interval = setInterval(() => {
        setTime(prevTime => prevTime + 100);
      }, 100);
    } else if (!running) {
      clearInterval(interval);
    }
    return () => clearInterval(interval);
  }, [running]);

  return (
    <div>
      <h1>{(time / 1000).toFixed(1)} seconds</h1>
      <button onClick={() => setRunning(true)}>Start</button>
      <button onClick={() => setRunning(false)}>Stop</button>
      <button onClick={() => setTime(0)}>Reset</button>
    </div>
  );
}
```
**Explanation**: Implements a simple stopwatch that counts in tenths of a second. Controls are provided to start, stop, and reset the timer.

### 10. Real-time Data Fetch
```javascript
// Real-time data fetching with WebSocket in React
import React, { useState, useEffect } from 'react';

function RealTimeData() {
  const [data, setData] = useState(null);
  useEffect(() => {
    const ws = new WebSocket('wss://example.com/data');
    ws.onmessage = (event) => {
      setData(JSON.parse(event.data));
    };
    return () => ws.close();
  }, []);

  return <div>Latest Data: {JSON.stringify(data)}</div>;
}
```
**Explanation**: Connects to a WebSocket server and updates state with the latest data received from the server, displaying it in real-time.

### 11. OTP Input Field
```javascript
// OTP Input field component in React
import React, { useState } from 'react';

function OTPInput() {
  const [otp, setOtp] = useState(new Array(6).fill(""));

  const handleChange = (element, index) => {
    const newOtp = [...otp];
    newOtp[index] = element.value;
    setOtp(newOtp);
   

 if (element.nextSibling && element.value) {
      element.nextSibling.focus();
    }
  };

  return (
    <div>
      {otp.map((data, index) => (
        <input
          key={index}
          type="text"
          maxLength="1"
          value={data}
          onChange={e => handleChange(e.target, index)}
          onFocus={e => e.target.select()}
        />
      ))}
    </div>
  );
}
```
**Explanation**: Creates an OTP input field with auto-focus on the next input after each entry.

### 12. Higher-Order Component (HOC)
```javascript
// Example of a Higher-Order Component in React
import React from 'react';

function withLogging(WrappedComponent) {
  return class extends React.Component {
    componentDidMount() {
      console.log(`Component ${WrappedComponent.name} mounted`);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}

function MyComponent(props) {
  return <div>{props.message}</div>;
}

const MyComponentWithLogging = withLogging(MyComponent);

export default MyComponentWithLogging;
```
**Explanation**: An HOC that adds logging functionality. It logs to the console when the component mounts and passes all props through to the wrapped component.

These examples cover a wide range of common and useful patterns and functionalities in modern web development, providing a solid basis for your interview preparations.



## SOLID Principles

The SOLID principles are a set of design guidelines aimed at improving the maintainability, scalability, and flexibility of software development projects. Here's how each of these principles can be applied in JavaScript:

### 1. **Single Responsibility Principle (SRP)**
**Definition:** A class should have only one reason to change, meaning it should have only one job or responsibility.

**JavaScript Example:**
```javascript
// Bad: This class handles both user data management and JSON serialization
class User {
    constructor(user) {
        this.user = user;
    }

    saveUser() {
        const userJson = JSON.stringify(this.user);
        localStorage.setItem('user', userJson);
    }
}

// Good: Split responsibilities into two classes
class User {
    constructor(user) {
        this.user = user;
    }

    // This class only manages user data
}

class UserSerializer {
    // This class only handles serialization
    static serialize(user) {
        return JSON.stringify(user);
    }

    static save(user) {
        localStorage.setItem('user', this.serialize(user));
    }
}
```

### 2. **Open/Closed Principle (OCP)**
**Definition:** Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

**JavaScript Example:**
```javascript
// Bad: Modifying the existing class to add new functionality
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }

    area() {
        return this.width * this.height;
    }
}

// Good: Using inheritance to extend functionality
class Shape {
    area() {
        throw new Error("Area method must be implemented");
    }
}

class Rectangle extends Shape {
    constructor(width, height) {
        super();
        this.width = width;
        this.height = height;
    }

    area() {
        return this.width * this.height;
    }
}

class Circle extends Shape {
    constructor(radius) {
        super();
        this.radius = radius;
    }

    area() {
        return Math.PI * this.radius * this.radius;
    }
}
```

### 3. **Liskov Substitution Principle (LSP)**
**Definition:** Objects in a program should be replaceable with instances of their subtypes without altering the correctness of the program.

**JavaScript Example:**
```javascript
// Good: Subtypes can be substituted without affecting behavior
class Bird {
    fly() {
        console.log("Flying");
    }
}

class Duck extends Bird {
    quack() {
        console.log("Quacking");
    }
}

function makeItFly(bird) {
    bird.fly();
}

const duck = new Duck();
makeItFly(duck); // Correctly utilizes fly method from Bird
```

### 4. **Interface Segregation Principle (ISP)**
**Definition:** Clients should not be forced to depend on interfaces they do not use.

**JavaScript Example:**
Since JavaScript does not have interfaces in the traditional sense, this principle can be interpreted as avoiding the requirement for a class to have methods it does not use.

```javascript
// Bad: Implementing an interface that includes methods not used by the class
class Car {
    turnOn() {}
    drive() {}
    fly() { throw new Error("Can't fly"); }
}

// Good: Separate capabilities into more specific interfaces
class FlyingCar {
    turnOn() {}
    drive() {}
    fly() {}
}

class RegularCar {
    turnOn() {}
    drive() {}
}
```

### 5. **Dependency Inversion Principle (DIP)**
**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g., interfaces).

**JavaScript Example:**
```javascript
// Bad: High-level module directly depends on a low-level module
class InventoryRequest {
    constructor() {
        this.db = new MySQLDatabase();
    }

    request(item) {
        return this.db.find(item);
    }
}

// Good: Depend on an abstraction rather than a concrete class
class InventoryRequest {
    constructor(database) {
        this.db = database; // Dependency injection
    }

    request(item) {
        return this.db.find(item);
    }
}

class MySQLDatabase {
    find(item) {
        // MySQL specific finding logic
    }
}

class MongoDBDatabase {
    find(item) {
        // MongoDB specific finding logic
    }
}
```

These examples illustrate how the SOLID principles can be applied in JavaScript to create a design that is easy to maintain, extend, or modify.


## ACID Properties

ACID is an acronym that stands for Atomicity, Consistency, Isolation, and Durability. These are the set of properties that guarantee database transactions are processed reliably and ensure the integrity of data within the database. Here's a breakdown of each property:

### 1. **Atomicity**
Atomicity guarantees that each transaction is treated as a single "unit," which either succeeds completely or fails completely. If any part of the transaction fails, the entire transaction fails, and the database state is left unchanged. In essence, a transaction's changes are "atomic" in the sense that they are indivisible.

**Example**: Consider a banking application where a transaction involves transferring money from one account to another. This transaction can involve two operations: debiting one account and crediting another. Atomicity ensures that if either of these operations fail, the entire transaction fails, thus preventing a scenario where money is deducted from one account but not credited to the other.

### 2. **Consistency**
Consistency ensures that a transaction can only bring the database from one valid state to another, maintaining all predefined rules, including integrity constraints and cascades. This property does not guarantee correctness of the transaction in all ways the application might need (that is up to the application's developers); rather, it merely ensures that any transaction will leave the database in a consistent state.

**Example**: If a rule exists in a database that the total amount of money in the system must remain constant, the consistency property ensures that a transaction changing the total amount (perhaps through a bug or error) will be rejected.

### 3. **Isolation**
Isolation determines how transaction integrity is visible to other users and systems. Transactions are often executed concurrently (i.e., at the same time), and isolation ensures that the concurrent execution of transactions leaves the database in the same state that would have been obtained if the transactions had been executed serially (one after the other).

**Example**: If two banking transactions are withdrawing money from the same account at the same time, isolation ensures that each transaction interacts with the account as if the other transactions did not exist concurrently. This might be achieved by locking the account record until one transaction completes.

### 4. **Durability**
Durability ensures that once a transaction has been committed, it will remain so, even in the event of power loss, crashes, or errors. This means that the changes made by the transaction are permanently stored in the system and can be recovered in a system restart.

**Example**: After a transaction to save a record is completed and the user is notified of the success, the record will not disappear or revert to its old state even if the system crashes immediately after.

These ACID properties are crucial for the reliability of database systems, particularly in systems that handle important transactions, such as financial services, where the integrity and consistency of data are paramount.


## Protocols

In computing, protocols are defined sets of rules or standards that dictate how data is transmitted between different devices and applications across networks. Protocols are crucial for enabling communication between systems with defined formats and behaviors, ensuring that data sent by one system can be understood and processed by another. Here's a breakdown of some of the most commonly used protocols across different layers of network communication:

### 1. **Internet Protocol (IP)**
- **Layer**: Network layer
- **Purpose**: IP is responsible for addressing and routing packets of data so that they can travel across networks and arrive at the correct destination. IP addresses are used to uniquely identify devices on a network. The most widely used versions are IPv4 and IPv6.

### 2. **Transmission Control Protocol (TCP)**
- **Layer**: Transport layer
- **Purpose**: TCP provides reliable, ordered, and error-checked delivery of a stream of packets on the internet. TCP is used extensively across the internet for applications where data must arrive intact and in order, such as web browsing, email, and file transfer.

### 3. **User Datagram Protocol (UDP)**
- **Layer**: Transport layer
- **Purpose**: UDP is used for tasks that require fast, efficient transmission, such as games or voice and video communications over the network. Unlike TCP, UDP does not guarantee reliability or order, making it faster and more suitable for real-time applications.

### 4. **Hypertext Transfer Protocol (HTTP)**
- **Layer**: Application layer
- **Purpose**: HTTP is used for transmitting web pages over the internet, allowing web browsers to retrieve and display them. It works as a request-response protocol between a client and server.

### 5. **File Transfer Protocol (FTP)**
- **Layer**: Application layer
- **Purpose**: FTP is used for the transfer of computer files between a client and server on a network and provides mechanisms for user authentication.

### 6. **Simple Mail Transfer Protocol (SMTP)**
- **Layer**: Application layer
- **Purpose**: SMTP is used for sending emails across networks. It specifies how mail servers and clients should communicate email information.

### 7. **Secure Shell (SSH)**
- **Layer**: Application layer
- **Purpose**: SSH is a protocol used for secure remote login and other secure network services between two networked computers.

### 8. **Domain Name System (DNS)**
- **Layer**: Application layer
- **Purpose**: DNS is used to resolve human-readable domain names (like www.example.com) into machine-readable IP addresses.

### 9. **Hypertext Transfer Protocol Secure (HTTPS)**
- **Layer**: Application layer
- **Purpose**: HTTPS is an extension of HTTP. It is used for secure communication over a computer network, and is widely used on the Internet. HTTPS encrypts HTTP requests and responses with TLS (SSL), thereby securing the data exchange.

### 10. **Dynamic Host Configuration Protocol (DHCP)**
- **Layer**: Application layer
- **Purpose**: DHCP is a network management protocol used on IP networks whereby a DHCP server dynamically assigns an IP address and other network configuration parameters to each device on a network so they can communicate with other IP networks.

These protocols define how data is to be packetized, addressed, transmitted, routed, and received at the destination. They are essential in enabling diverse and dispersed systems and devices to communicate effectively.


## General Interview Questions (Topic Based)

In your frontend software engineer interview at Walmart, focusing on Low-Level Design (LLD), you can expect a mix of conceptual and practical questions. These might range from assessing your understanding of design principles to applying them in real-world scenarios. Here are some potential questions you could encounter:

### 1. **Design Patterns and Principles**
- Can you explain the SOLID principles? How would you apply them in JavaScript?
- What are some common design patterns you use in frontend development? How do they improve your code?
- Can you describe the MVC (Model-View-Controller) pattern and how it could be implemented in a JavaScript application?

### 2. **Component Design and Data Flow**
- How would you design a reusable modal component in React/Angular/Vue?
- Describe the data flow in a Redux application. How does it differ from the Context API in React?
- What considerations might you have when designing a form component that includes validation, error handling, and state management?

### 3. **Performance and Scalability**
- How do you ensure your frontend application is scalable and maintainable?
- What strategies would you use to optimize the performance of a web application?
- Discuss how you would handle state management in a large-scale application.

### 4. **Error Handling and Security**
- How do you design error handling in a JavaScript application?
- What security aspects do you consider when designing a frontend application?

### 5. **Scenario-Based Questions**
- How would you design a dashboard that displays real-time data from multiple sources?
- Design a low-level architecture for an e-commerce site that includes user authentication, product listings, and a shopping cart.
- Imagine we need a feature that allows users to customize the layout of their profile page. How would you design the system to handle this?

### 6. **Testing and Deployment**
- How do you design your applications to be testable? What types of tests do you consider essential?
- Can you explain the process of moving a frontend application from development to production? What are the key design considerations?

### 7. **Collaboration and Code Review**
- How do you ensure that your designs are clear and understandable to other developers?
- What do you look for in a code review, especially related to design?


Let's dive into potential solutions for the scenario-based low-level design questions you might face in your frontend interview at Walmart. Each solution will focus on key considerations, component design, and data management strategies using JavaScript and relevant frameworks.

### Scenario 1: Designing a Dashboard Displaying Real-Time Data from Multiple Sources

#### Key Considerations:
- **Real-time Updates**: Implementing efficient real-time data handling.
- **Scalability**: Handling multiple data sources without impacting performance.
- **Modularity**: Creating reusable components.

#### Potential Design:
1. **Component Structure**:
   - `DashboardComponent`: The main container for the dashboard.
   - `WidgetComponent`: Individual widgets that can be reused. Each widget fetches its data independently.
   - `DataFetcher`: A service or hook that manages API calls.

2. **Data Flow**:
   - Use WebSocket or server-sent events (SSE) for real-time data updates.
   - Each `WidgetComponent` subscribes to updates from its relevant data source via `DataFetcher`.

3. **State Management**:
   - If using React, Redux or Context API can be utilized to manage the state of the data across different widgets.
   - For Angular, services combined with RxJS subjects can be effective.

4. **Code Example**:
   ```javascript
   // DataFetcher.js - using WebSocket
   class DataFetcher {
      constructor(url) {
         this.socket = new WebSocket(url);
      }

      subscribe(onMessage) {
         this.socket.onmessage = onMessage;
      }

      unsubscribe() {
         this.socket.close();
      }
   }

   // WidgetComponent.jsx
   function WidgetComponent({ sourceUrl }) {
      const [data, setData] = useState(null);
      useEffect(() => {
         const fetcher = new DataFetcher(sourceUrl);
         fetcher.subscribe(event => {
            const newData = JSON.parse(event.data);
            setData(newData);
         });

         return () => fetcher.unsubscribe();
      }, [sourceUrl]);

      return <div>{data ? JSON.stringify(data) : "Loading..."}</div>;
   }
   ```

### Scenario 2: Design a Low-Level Architecture for an E-commerce Site

#### Key Considerations:
- **User Authentication**: Secure handling of user credentials and sessions.
- **Product Listings**: Efficient loading and updating of product information.
- **Shopping Cart**: Persistent and responsive cart system.

#### Potential Design:
1. **Component Structure**:
   - `AuthenticationService`: Manages user login, logout, and session storage.
   - `ProductList`: Displays products; fetches data from a server.
   - `Cart`: Manages shopping cart items, prices, and quantities.

2. **Data Flow**:
   - RESTful API for products and user management.
   - Local storage or Redux for managing cart state across the application.

3. **Code Example**:
   ```javascript
   // AuthenticationService.js
   class AuthenticationService {
      login(credentials) {
         return fetch('/api/login', {
            method: 'POST',
            body: JSON.stringify(credentials),
            headers: { 'Content-Type': 'application/json' },
         }).then(res => res.json());
      }
   }

   // Cart.jsx
   function Cart({ cartItems }) {
      return (
         <div>
            {cartItems.map(item => (
               <div key={item.id}>{item.name} - Qty: {item.quantity}</div>
            ))}
         </div>
      );
   }
   ```

### Scenario 3: Customizable User Profile Page Layout

#### Key Considerations:
- **Flexibility**: Users can choose which components to display.
- **Persistence**: User preferences should be saved and retrieved.

#### Potential Design:
1. **Component Structure**:
   - `ProfilePage`: The main container that loads user preferences and renders components accordingly.
   - `ComponentSelector`: Allows users to select components to display.

2. **Data Flow**:
   - Preferences are stored in a database and fetched when the user logs in.
   - State management through Context API or Redux to handle dynamic component rendering based on user preferences.

3. **Code Example**:
   ```javascript
   // ProfilePage.jsx
   function ProfilePage({ userId }) {
      const [layout, setLayout] = useState([]);

      useEffect(() => {
         fetch(`/api/users/${userId}/layout`)
            .then(res => res.json())
            .then(setLayout);
      }, [userId]);

      return (
         <div>
            {layout.map((ComponentId) => {
               const Component = componentRegistry[ComponentId];
               return <Component key={ComponentId} />;
            })}
         </div>
      );
   }
   ```

These examples provide a starting point for designing solutions in real-world scenarios, focusing on practical implementation using JavaScript and popular frontend frameworks.


### Preparation Tips
- **Review Your Past Projects**: Be ready to discuss specific instances where you applied low-level design principles in your past work. Prepare to explain your rationale, challenges faced, and outcomes.
- **Brush Up on Basics**: Make sure you're comfortable with JavaScript, CSS, HTML, and any frameworks or libraries you mention in your resume or might be relevant to the job.
- **Mock Interviews**: Consider conducting mock interviews with a focus on system design and problem-solving in frontend contexts.

Understanding these questions and preparing your responses can significantly improve your confidence and performance in the interview. Good luck!
