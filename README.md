# Pure Component

A `Pure Component` is a subclass of `React.Component`. The primary difference is that `PureComponent` automatically implements `shouldComponentUpdate()`, which checks if the **props** and **state** have changed (using shallow comparison).

This can help avoid unnecessary re-renders, especially when the props and state are large and unchanged.

```jsx
function double(number) {
return 2 * number;}
```

---

### Use Case

Use Pure Component when:

- Your component renders the same output given the same props and state.
- You want to optimize performance by skipping unnecessary renders.
- Your props/state are immutable or you are careful with object/array references.

### **Shallow Comparison in Pure Component**

When React performs a shallow comparison, it checks whether the values of props and state are the same by reference. For primitives (like numbers or strings), this means checking if the values are the same. For objects or arrays, React checks if the references are the same.

### Shallow comparison for **primitives**

```jsx
class MyComponent extends React.PureComponent {
  render() {
    return <div>{this.props.name}</div>;
  }
}
// Usage
<MyComponent name="John" />
```

Here, `name` is a primitive (`string`). The shallow comparison will check if the value of `name` has changed, and if it has not, the component will **not re-render**.

---

### Shallow comparison for **objects**

```jsx
class MyComponent extends React.PureComponent {
  render() {
    return (
      <div>
        {this.props.user.name}
      </div>
    );
  }
}
// Usage
const user = { name: 'John' };
<MyComponent user={user} />
```

When using objects or arrays as props, **shallow comparison** will only check if the reference to `user` has changed. If the reference is the same (even if the content has changed), `PureComponent` will **skip the render**.

---

### **How Shallow Comparison Works**

Shallow comparison compares **primitive values** directly, but for **objects and arrays**, it checks if the **reference** has changed, not if the content inside has changed.

### Example:

```jsx
class MyComponent extends React.PureComponent {
  render() {
    console.log('Rendering...');
    return <div>{this.props.data}</div>;
  }
}
// First render
const data = { a: 1 };
<MyComponent data={data} />
// Later
const newData = { a: 1 }; // different object reference but same value
<MyComponent data={newData} />
```

- Even though the contents of `data` have not changed, **newData** is a new reference to an object.
- `PureComponent` will **re-render** the component because the references are different.

---

### **Shallow Comparison for Arrays**

The shallow comparison for arrays works similarly to objects. React checks if the array reference has changed.

### Example:

```jsx
class MyComponent extends React.PureComponent {
  render() {
    return <div>{this.props.numbers.join(', ')}</div>;
  }
}

const numbers1 = [1, 2, 3];
const numbers2 = [1, 2, 3];
// Usage
<MyComponent numbers={numbers1} />
<MyComponent numbers={numbers2} />
```

Even though the arrays `numbers1` and `numbers2` contain the same values, they are different references. As a result, React will **re-render** the component because the shallow comparison detects that the array references have changed.

---

### **Handling Mutable Data Structures**

Pure Component relies on shallow comparisons, which can be tricky when dealing with mutable data (like objects or arrays) because even if the content hasn't changed, the reference might change if you update the object or array.

### Example of Mutable Data:

```jsx
class MyComponent extends React.PureComponent {
  render() {
    console.log('Rendering...');
    return <div>{this.props.data.name}</div>;
  }
}

const data = { name: 'John' };
const newData = data;
newData.name = 'Jane'; // mutating the existing object

<MyComponent data={newData} />

```

- **Problem**: Even though we mutated the data (`newData.name = 'Jane'`), the **reference to the object hasn't changed**.
- React's shallow comparison wouldn't detect this mutation and might skip the render. However, we could force the re-render by creating a new object reference.

---

### **When Not to Use Pure Component**

- **Dynamic objects**: If the props or state change frequently (e.g., complex objects or arrays), using `PureComponent` might actually hurt performance due to the overhead of shallow comparisons.
- **Deep object comparison**: If you need deep comparison (checking nested objects or arrays), `PureComponent` won't work. You'll need to handle deep comparison manually or use libraries like `Immutable.js`.