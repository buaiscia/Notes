## Testing the styles of DOM elements with React Testing Library

Sometimes -or often, depending on the case- we need to test some particular style that can change depending on some property.
The way this is implemented depends on the library we use for the styling.

Supposing that we have some text, inside a component, that depending on the prop it's white or black.
In the DOM, it will reflect this way. However, especially as it's not a fixed element, it's better that we test how it behaves on the different pages.

The test itself using React Testing Library is very simple, because it checks directly the DOM:
```js
  render(<MyComponent>)
  
  const text = screen.getByText('text in the page')
  expect(text).toBeInTheDocument()
  expect(text).toHaveStyle({ color: '#FFFFF'})
```

The library convert the color in hex code against RGB, so if they match, the test will pass; otherwise, it will return the retuned RGB color (i.e. 0,0,0) and the expected one (i.e. 255,255,255). With it it's very easy to spot the mistake, or why a test is failing.

We can also test directly the prop that we're passing and the consequent change of color in the component:
```js
  const {rerender} = render(<MyComponent changingProp='white'>)
  
  expect(screen.getByText('text in the page')).toHaveStyle({ color: '#FFFFF'})
  
  rerender(<MyComponent changingProp='black'>)
  
  expect(screen.getByText('text in the page')).toHaveStyle({ color: '#00000'})
```

You have to notice here that I had to call screen.getByText twice, because that function is returning immediately the DOM element. If I would set it as before in a variable, at the moment of checking 'text' the second time, it would have stored the previous DOM element and not the updated one.
