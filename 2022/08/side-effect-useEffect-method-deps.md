## Side effects of useEffect over method dependencies

There are many ways in which we can use useEffect, some more correct than others. However, we have the tendency to use this hook wherever we have to update a state dependent on other variables, or update some variable upon fetching some data. 

One case is when we're updating some data and we call some function external to useEffect. This function will make React re-render the DOM on what's necessary.

```js
  const updatingUIMethod = () => {
    ...
    return value
  }
  
  useEffect(() => {
    setChangeState(updatingUIMethod())
  }, [updatingUIMethod] )
```

Now, if you have eslint installed in the project, especially with the react/recommended plugin, it may warn you that updatingUIMethod can create an infinite loop as it's continously updating the UI. Indeed, if you just run it as it is, React will throw the error 'Max depth reached', meaning it went on a re-rerendering loop calling again and again the function.

As eslint suggests, the easiest way to fix it is wrapping the function with the useCallback hook. UseCallback in fact re-run the function only when the input props change. Otherwise it will return the same result. 

So the simple solution is:
```js
  const updatingUIMethod = useCallback(() => {
    ...
  }, [methodchangingVariables])
```

Also, one has to be careful to not having nested methods that will cause a cascade effect. If the method chaging the DOM will be another function called by updatingUIMethod, also the other method will have to be wrapped with useCallback. This would create an anti-pattern instead of having simpler functions, so it's important to have those methods called by useEffect doing just one effect.
