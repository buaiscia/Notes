## Tests finding bugs: an example

In almost every Sprint, at work, I try to reserve some space for solving tech debt and do some refactoring.
A part of resolving this debt is making integration tests. And sometimes it happens that those tests find some unknown bug.
That's what happened few days ago. As usual the names of the methods and variables are just an example.

The page I was writing the tests on is a simple one. Some text, three buttons and that's it.
However, it has some hooks as well, defining the logic of those buttons (opening another url, submitting the form, etc).

I started writing the tests in the usual way, using React Testing Library. Checking the rendering and the behavior of the page clicking on the different buttons.
When I was writing the test for one of the three button, however, it forced me to let me think: is this button always visible? Why?

```js
{showButton && (
   <Button onClick={onApplyClick}>
      DO THING
   </Button>
 )}
```

I checked the hooks and found out that it would render only when another property was true, or present. So why it was always there?

```js
  showButton: !!secondaryProp,
```

Following quickly the source of that property, it came to the point that it was fetched from the (easy-peasy) store, and it was set as initial values as 0, instead of being undefined. 

```js
const initialState: IState = {
  ...
  secondaryProp: 0,
```

The type of that property was just "number", instead of "number?". Therefore, it was always true.

```js
export interface IState {
  secondaryProp: number
```

To fix it, I changed the type and set the initial values as undefined. 

Re-checking that property I saw that another value was dependent on it being present or not. So in another hook I had to redefine the behavior in case the prop was falsy.

```js
const newUrl = secondaryProp && `${FETCHED_URL}/prop=${secondaryProp}`

const onClick = () => {
    if (!newUrl) {
      return
    }

    window.open(newUrl)
  }
```

So that's an interesting case of how making tests helps you reflect on what should be the behaviour and the logic of the methods and components.
