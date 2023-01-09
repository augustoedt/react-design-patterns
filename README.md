# A study of react patterns from youtube channel

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

Example of two components, a large and a small one

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
        <ItemComponent key={i} {...{[resourceName: string]: item}} />;
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
      <RegularList
        items={people}
        resourceName="person"
        itemComponent={LargePersonListItem}
      />
    </>
  );
}
```
