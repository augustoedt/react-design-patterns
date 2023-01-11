# A study of react patterns

[Playlist Coding With Mr.M](https://www.youtube.com/watch?v=Luw90ESdSwI&list=PLgeETUaEEds5HE2uGWezJxYeZwpBonHr-)

## Design Patterns

### What are design patterns in this summary?

Efective solutions to common application development challenges for React. Not the Gang of Four OOP design patterns.

### Challenge solutions for:

- Creating reusable layouts
- Reusing complex logic between multiple components
- Working with forms
- Incorporating functional concepts into our code

## Layout Component Examples

Idea of layout compoments it's that they shouldn't care where they're being displayed

- Split Screens
- List and Items
- Modals

Example:

```tsx
<div styles={...}>
   <h1>Component Code ...</h1>
</div>

// to

childrens = () => <> <h1> </>

<div styles={...}>{childrens}</div>
```

### Split Screen

The first step it's creating the scaffold

```tsx
// splitscreen.tsx
export const SplitScreen = ({ left, right }: { left: Left; right: Right }) => {
  return (
    <div>
      <div>
        <Left />
      </div>
      <div>
        <Right />
      </div>
    </div>
  );
};
```

StyledComponents gives a good option to organize the style code.

```tsx
// splitscreen.tsx
import styled from "styled-components";

const Pane = styled.div`
  flex: 1;
`;

const Container = styled.div`
  display: flex;
`;

export const SplitScreen = ({ left, right }: { left: Left; right: Right }) => {
  return (
    <Pane>
      <Pane>
        <Left />
      </Pane>
      <Pane>
        <Right />
      </Pane>
    </Pane>
  );
};
```

Calling the component

```tsx
// app.tsx
const LeftHandComponent = () => {
  return <h1 style={{ background: "green" }}>Left!</h1>;
};
const RightHandComponent = () => {
  return <h1 style={{ background: "red" }}>Right!</h1>;
};

function App() {
  return <SplitScreen left={LeftHandComponent} right={RightHandComponent} />;
}

export default App;
```

Adding proportion weight to component

```tsx
// splitscreen.tsx
const Pane = styled.div`
  flex: ${(props => props.weight)};
`;
...
export const SplitScreen = ({
    left,
    right,
    leftWeight,
    rightWeight
    } : {
    left: Left;
    right: Right;
    leftWeight: number;
    rightWeight: number; }) => {
  return (
    <Pane>
      <Pane weight={leftWeight}>
        <Left />
      </Pane>
      <Pane weight={rightWeight}>
        <Right />
      </Pane>
    </Pane>
  );
};
```

```tsx
// app.tsx
const LeftHandComponent = ({ name }: { name: string }) => {
  return <h1 style={{ background: "green" }}>{name}</h1>;
};
const RightHandComponent = ({ message }: { message: string }) => {
  return <h1 style={{ background: "red" }}>{message</h1>;
};

function App() {
  return (
    <SplitScreen
      leftWeight={1}
      rightWeight={3}
      left={LeftHandComponent}
      right={RightHandComponent}
    />
  );
}
```

Finaly, one last optimization, instead of passing left and right components as a props we could add as children

```tsx
// splitscreen.tsx
...
export const SplitScreen = ({
    children
    leftWeight,
    rightWeight
    } : {
    children: ReactNode[];
    leftWeight: number;
    rightWeight: number; }) => {
    const [left, right] = children;
  return (
    <Pane>
      <Pane weight={leftWeight}>
        {left}
      </Pane>
      <Pane weight={rightWeight}>
        {right}
      </Pane>
    </Pane>
  );
};
```

```tsx
function App() {
  return (
    <SplitScreen
      leftWeight={1}
      rightWeight={3}
    >
        <LeftHandComponent name={"Hi "}/>
        <RightHandComponent message={"there!"}/>
    <SplitScreen>
  );
}
```

### List Component

Example of two components, a large and a small one for the given example data.

```ts
const person: Person[] = [
    {
        name: "Tom",
        age: 40,
        hairColor: "blue",
        hobbies: ["guittar", "piano"]
    }
    ...//...
    {
        name: "Jonh",
        age: 40,
        hairColor: "blue",
        hobbies: ["cooking", "dance", "travel"]
    }
]
const products: Products[] = [
    {
        name: "TV UHD 4K",
        price: '$4000',
        description: "Modern Smart TV",
        rating: 4.8
    }
    ...//...
    {
        name: "Nike Shots",
        price: '$300',
        description: "Space shoes",
        rating: 4.3
    }
]
```

Components for person item

```tsx
// SmallPersonListItem.tsx
export const SmallPersonListItem = ({ person }: { person: Person }) => {
  const { name, age } = person;

  return (
    <p>
      Name: {name}, Age: {age}
    </p>
  );
};

// LargePersonListItem.tsx
export const LargePersonListItem = ({ person }: { person: Person }) => {
  const { name, age, hairColor, hobbies } = person;
  return <>
    <h3>{name}</h3>
    <p>Age: {age}</p>
    <p>Hair Color: {hairColor}</p>
    <h3>Hobbies: </h3>
    <ul>
        {hobbies.map(hobby=> <li key={hobby}>{hobby}</li>)}
    </ul>
  </h3>
};
```

Components for product item

```tsx
// SmallProductListItem.tsx
export const SmallProductListItem = ({ product }: { product: Product }) => {
  const { name, price };
  return (
    <h3>
      {name}-{price}
    </h3>
  );
};
// LargeProductListItem.tsx
export const LargeProductListItem = ({ product }: { product: Product }) => {
  const { name, price, description, rating };
  return (
    <>
      <h3>{name}</h3>
      <p>{price}</p>
      <h3>Description:</h3>
      <p>{description}</p>
      <p>Average rating: {rating}</p>
    </p>
  );
};
```

Now create a single list component

```tsx
// RegularList.tsx
export const RegularList = ({
  items,
  resourceName,
  itemComponent,
}: {
  items;
  resourceName;
  itemComponent: ItemComponent;
}) => {
  return (
    <>
      {items.map((item, i) => {
        // resourceName specifies the property name of the
        // itemComponent that receives a unique item of a list.
        <ItemComponent key={i} {...{[resourceName: string]: item}} />;
      })}
    </>
  );
};
```

```tsx
// NumberedList.tsx
export const NumberedList = ({
  items,
  resourceName,
  itemComponent,
}: {
  items;
  resourceName;
  itemComponent: ItemComponent;
}) => {
  return (
    <>
      {items.map((item, i) => {
        <>
            <h3>{i+1}</h3>
            // resourceName specifies the property name of the
            // itemComponent that receives a unique item of a list.
            <ItemComponent key={i} {...{[resourceName: string]: item}} />;
        </>
      })}
    </>
  );
};
```

Implementation

```tsx
// App.tsx
function App() {
  return (
    <>
      <RegularList
        items={people}
        resourceName="person"
        itemComponent={SmallPersonListItem}
      />
      <NumberedList
        items={people}
        resourceName="person"
        itemComponent={LargePersonListItem}
      />
      <RegularList
        items={people}
        resourceName="product"
        itemComponent={SmallProductListItem}
      />
      <NumberedList
        items={people}
        resourceName="product"
        itemComponent={LargeProductListItem}
      />
    </>
  );
}
```

### Modal Components

```tsx
import styled from "styled-components";

const ModalBackground = styled.div`
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%
    height: 100%
    overflow: auto;
    background-color: rgba(0,0,0,0.5)
`;

const ModalBody = styled.div`
  background-color: white;
  margin: 10% auto;
  padding: 20px;
  width: 50%;
`;

export const Modal = ({ children }) => {
  const [shouldShow, setShouldShow] = useState(false);

  return (
    <>
      <button onClick={() => setShouldShow(!shouldShow)}>Show</button>
      {shouldShow && (
        <ModalBackground onClick={() => setShouldShow(false)}>
          <ModalBody onClick={(e) => e.stopPropagation()}>
            <button onClick={() => setShouldShow(false)}>Hide</button>
            {children}
          </ModalBody>
        </ModalBackground>
      )}
    </>
  );
};
```

Simple Implementation

```tsx
// App.tsx
function App() {
  return <>
    <Modal>
        <LargeProductListItem product={product[0]}>
    </Modal>
  </>
```

## Controlled vs. Uncontrolled Components

### Uncontrolled components

Components that keep track of their own states and release data only when some event occurs.

### Controlled components

Controlled components do not keep track of their own state, all state its passed is as props (that is, when we useState Hook with text inputs)

### Basic Example

```tsx
// controlled components
const MyComponent = ({data, changeData, onSubmit}) => {
  return ...;
}

<MyComponent
data={...}
changeData={()=> ...}
onSubmit={()=> ...}/>
```

### Uncontrolled Form

It doest matter which value has the input until the event its done.

```tsx
import { createRef } from "react";

export const UncontrolledForm = () => {
  const nameInput = createRef();
  const ageInput = createRef();
  const hairColorInput = createRef();

  const handleSubmit = () => {
    console.log(nameInput.current.value);
    console.log(ageInput.current.value);
    console.log(hairColorInput.current.value);
    e.preventDefault();
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" type="text" placeholder="Name" ref={nameInput} />
      <input name="age" type="text" placeholder="Age" ref={ageInput} />
      <input
        name="hairColor"
        type="text"
        placeholder="Hair Color"
        ref={hairColorInput}
      />
      <input type="submit" value="Submit" type="text" placeholder="Submit" />
    </form>
  );
};
```

### Controlled Form

```tsx
import { createRef } from "react";

export const UncontrolledForm = () => {
  const [nameInputError, setNameInputError] = useState()
  const [name setName] = useState();
  const [age setAge] = useState();
  const [hairColor setHairColor] = useState();

  const handleSubmit = () => {
    e.preventDefault();
  };

  useEffect(()=>{
    if(name.length<2){
      setNameInputError('Name must be two of more characters')
    }
    else {
      setNameInputError()
    }
  },[])

  return (
    <form onSubmit={handleSubmit}>
     {nameInputError && <p>{setNameInputError}</p>}
      <input name="name" type="text" placeholder="Name" onChange={(e)=>setName(e.target.value)} value={name}/>
      <input name="age" type="text" placeholder="Age" onChange={(e)=>setAge(e.target.value)} value={age} />
      <input
        name="hairColor"
        type="text"
        placeholder="Hair Color"
        onChange={(e)=>setHairColor(e.target.value)} value={hairColor}
      />
      <input type="submit" value="Submit" type="text" placeholder="Submit" />
    </form>
  );
};
```
