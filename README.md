# memo-usememo-usecallback

**re-render** :
React components automatically re-render whenever there is a change in their `state` or `props`.
`Re-Render Child Component when Parent State Changes`

**[Memoization](https://en.wikipedia.org/wiki/Memoization)** *is an optimization technique used primarily to speed up computer programs by storing the results of expensive function calls and returning the cached result when the same inputs occur again.*


## [memo](https://reactjs.org/docs/react-api.html#reactmemo)
`React.memo` is a [higher order component](https://reactjs.org/docs/higher-order-components.html). It’s similar to [React.PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) but for function components instead of classes. If your function component renders the same result given the same props, you can wrap it in a call to `React.memo` for a performance boost in some cases by memoizing the result. This means that React will skip rendering the component, and reuse the last rendered result.

```jsx
import React, { useState, memo } from 'react';
// just wrap your components in memo
const Text = memo(({ text }) => {
  return <p >{text}</p>
});
const ParentComponent = () => {
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
  
  return (
    <>
      <input onChange={(e) => setFirstName(e.target.value)} />
      <input onChange={(e) => setLastName(e.target.value)} />
      <Text text='Your name is:' />
      <Text text={firstName} />
      <Text text={lastName} />
    </>
  );
};
```

:warning: `React.memo` only checks for prop changes. If your function component wrapped in `React.memo` has a `useState` or `useContext Hook` in its implementation, **it will still rerender when state or context change.**


:bulb: By default it will only **shallowly** compare complex objects in the props object. If you want control over the comparison, you can also provide a custom comparison function as the second argument.
```jsx
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual);
```

<details><summary>Referential equality</summary>
  <p>
    
  ```javascript
  true === true // true
false === false // true
1 === 1 // true
'a' === 'a' // true
{} === {} // false
[] === [] // false
() => {} === () => {} // false
const z = {}
z === z // true
  ```
  </p>
  </details>
  
<details><summary>areEqual</summary>
<p>

```jsx
React.memo(Component, [areEqual(prevProps, nextProps)]);
```

```jsx
const areEqual = (prevProps, nextProps) => {
  return (prevProps.name === nextProps.name)
};
React.memo(Person, areEqual);
```
</p>
</details>


  
:warning: This method only exists as a [performance optimization](https://github.com/facebook/react/issues/15156). Do not rely on it to “prevent” a render, as this can lead to bugs.

## [UseMemo](https://reactjs.org/docs/hooks-reference.html#usememo)

`useMemo` will only recompute the memoized value when one of the dependencies has changed. This optimization helps to avoid expensive calculations on every render. Remember that the function passed to `useMemo` runs during rendering. Don’t do anything there that you wouldn’t normally do while rendering. For example, **side effects belong in `useEffect`, not `useMemo`.**

```jsx
// example with useMemo
import React, { useMemo } from "react";
const computeValueFromProp = (prop) => {
  // expensive computation here
}
const ComponentThatRendersOften = ({ prop1, prop2 }) => {
const valueComputedFromProp1 = useMemo(() => {
  return computeValueFromProp(prop1)
}, [prop1]);
return (
    <>
       <div >{valueComputedFromProp1}</div>
       <div >{prop2}</div>
    </>
  );
};
```
Referential Equality? ```javascript [] === [] // false``` That’s exactly what memoizing hooks like `useMemo` and `useCallback` are made for. If props is an array, we can put it in the `useMemo` hook and it will reference it, after a render, as equal. If a function or another non-primitive value is in the `useEffect` dependency, it will recreate a new array, because of [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures), and find it unequal.

The `useCallback` hook is similar to `useMemo`, but it returns a memoized function, while `useMemo` has a function that returns a value.

## [useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)

`useCallback` will return a memoized version of the callback that only changes if one of the dependencies has changed. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders (e.g. `shouldComponentUpdate`).

:bulb: We recommend using the `exhaustive-deps` rule as part of our `eslint-plugin-react-hooks` package. It warns when dependencies are specified incorrectly and suggests a fix.

```jsx
import React, { useState, useCallback } from 'react';
const ComponentThatRendersOften = ({ cb1, cb2 }) => {
  const [state, setState] = useState(...);
  const expensiveFunction = useCallback(() => {
    // expensive calculation here
    setState(...);
    cb1();
  }, [cb1]);
  return (
    <button onClick={expensiveFunction} />
  );
};
```

:memo: **참고 자료**   
* [https://kentcdodds.com/blog/usememo-and-usecallback](https://kentcdodds.com/blog/usememo-and-usecallback) :bookmark:   
* [https://www.youtube.com/watch?v=4BranN3qnDU&t=13s](https://www.youtube.com/watch?v=4BranN3qnDU&t=13s)   
* [https://www.youtube.com/watch?v=3cYtqrNUiVw](https://www.youtube.com/watch?v=3cYtqrNUiVw)   
* [https://dmitripavlutin.com/use-react-memo-wisely/](https://dmitripavlutin.com/use-react-memo-wisely/)   
* [https://medium.com/swlh/optimizing-react-performance-with-memo-usememo-and-usecallback-11fb34f4a3fa](https://medium.com/swlh/optimizing-react-performance-with-memo-usememo-and-usecallback-11fb34f4a3fa)   
* [https://alligator.io/react/learning-react-memo/](https://alligator.io/react/learning-react-memo/)   
* [https://alligator.io/react/usememo/](https://alligator.io/react/usememo/)   





