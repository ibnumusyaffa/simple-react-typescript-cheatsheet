# Simple React Typescript Cheatsheet


## Typing props with inline type

```tsx
function Button(props: { children: React.ReactNode }) {
  return <button>{props.children}</button>;
}
```

## Typing props with type (or inteface)

```tsx
type ButtonProps = {
  className: string;
  children: React.ReactNode;
};

function Button(props: ButtonProps) {
  return <button className={props.className}>{props.children}</button>;
}

// with destructuring
function OtherButton({ className, ...props }: ButtonProps) {
  return <button className={className}>{props.children}</button>;
}
```

## Typing props with default value

```tsx
type ButtonProps = {
  disabled?: boolean;
  className: string;
  children: React.ReactNode;
};

function Button({ disabled = true, ...props }: ButtonProps) {
  return (
    <button disabled={disabled} {...props}>
      {props.children}
    </button>
  );
}
```

## Typing props with children

```tsx
type ButtonProps = {
  // accept everything React can render
  children: React.ReactNode; 
};

function Button(props: ButtonProps) {
  return <button>{props.children}</button>;
}
```

## Using Native HTML props to React Components

### 1. Basic

```tsx
import React, { ComponentProps } from "react";

//ComponentProps<"button"> : get all type/props from native button element

function Button(props: ComponentProps<"button">) {
  return <button>{props.children}</button>;
}
```

### 2. Combine with your type

```tsx
import React, { ComponentProps } from "react";

type ButtonProps = ComponentProps<"button"> & {
  variant: "primary" | "secondary";
};

function Button(props: ButtonProps) {
  return <button {...props}>{props.children}</button>;
}
```

### 3. Overriding Native Props

```tsx
import React, { ComponentProps } from "react";

//remove onChange property from input with Omit<Type, Keys> and combine with new type
type InputProps = Omit<ComponentProps<"input">, "onChange"> & {
  onChange: (value: string) => void;
};

function Input(props: InputProps) {
  return <input {...props} />;
}
```

### 4. Extracting Props from Custom Components

useful when author of some external library dont export the type definition

```tsx
import { ComponentProps } from "react";
import { Navbar } from "some-ui-library";

type NavBarProps = ComponentProps<typeof NavBar>;
```

## Typing Event Handlers from native element

hover button element in VS Code, and copy paste the onClick event handler

```tsx
type ButtonProps = {
  onClick?: React.MouseEventHandler<HTMLButtonElement>;
};
```

## useState

```tsx
// ❌ Typescript already know `text` type is string
const [text, setText] = useState<string>("");
// ✅ no need to tell typescript
const [text, setText] = useState("");
```

```tsx
type Tag = {
  id: number;
  value: string;
};

const [tags, setTags] = useState<Tag[]>([]);
```

```tsx
// data : Data | undefined
const [data, setData] = useState<Data>();
// data : Data | undefined
const [data, setData] = useState<Data>(undefined);
// data : Data | null
const [data, setData] = useState<Data | null>(null);
```

## useCallback

```tsx
function App(props: { id: number }) {
                                //⬇️ add type here
  const handleClick = useCallback((message: string) => {
    console.log("name")
  }, [props.id]);

  return (
    <div>
      <p>{message}</p>
      <button onClick={()=> handleClick("hello")}>button 1</button>
      <button onClick={()=> handleClick("hello")}>button 2</button>
    </div>
  );
}
```

## useRef

### Basic useRef

```tsx
export const Component = () => {
  // pass type if it doesn't have initial value
  const id1 = useRef<string>();
  // no need to pass type if it have initial value
  const id2 = useRef("");

  useEffect(() => {
    id1.current = "Random value!";
  }, []);

  return <div></div>;
};

```

### useRef with HTML element

```tsx
export const Component = () => {
  // add null to initial value
  const ref = useRef<HTMLDivElement>(null);

  return <div ref={ref} />;
};

```

```tsx
export const Component = () => {
  // add null to initial value
  const ref = useRef<HTMLInputElement>(null);

  return <input ref={ref} />;
};

```

### useRef with forwardRef

```tsx
type InputProps = {
  className: string
}

const Search = React.forwardRef<HTMLInputElement, InputProps>((props, ref) => {
  return <input ref={ref} className={props.className} />
})
// add displayName if you are using function expression, so its has a name in react devtool
Search.displayName = 'Search'

function App() {
  const input = React.useRef<HTMLInputElement>(null)

  useEffect(() => {
    // focus to input element on first render
    if (input.current) {
      input.current.focus()
    }
  }, [])

  return <Search className='some-input' ref={input} />
}
```


### Making a Read-Only Ref Mutable

```tsx
export const Component = () => {
  const ref1 = useRef<string>(null);
  // if you pass null to initial value
  // this not allowed to change directly 
  ref1.current = "Hello";

  const ref2 = useRef<string>();
  // if initial value is undefined this is allowed to change (mutable)
  ref2.current = "Hello";

  return null;
};
```


## Types or Interfaces?

`interface`s are different from `type`s in TypeScript, but they can be used for very similar things as far as common React uses cases are concerned. Here's a helpful rule of thumb:

- Always use `interface` for public API's definition when authoring a library or 3rd party ambient type definitions.

- Consider using `type` for your React Component Props and State, because it is more constrained.

Types are useful for union types (e.g. `type MyType = TypeA | TypeB`) whereas Interfaces are better for declaring dictionary shapes and then `implementing` or `extending` them.
