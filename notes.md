# Notes: react-testing-library


## Intro

These notes are intended as a reference for personal use while I learn to better use react-testing-library.
The repo these notes sit within is a clone of a training repository provided by Ibrahima Ndaw at freecodecamp.
https://www.freecodecamp.org/news/8-simple-steps-to-start-testing-react-apps-using-react-testing-library-and-jest/

NB: I'm using jest. As such I won't use a manually entered ```afterEach(cleanup)``` in my tests. The guide linked above explains what this means.

---

## 0. What are we testing?

__react-testing-library__ is designed to test how React apps work in the DOM. These tests target DOM elements and events, because these things reflect the user's experience. 

For example, a user does not care if an addition function within a React app's component gives a value of 5 when passed the values 2 and 3.
However the user *will* care if a calculator app gives a wrong result.
These tests are designed to simulate user interactions, so rather than checking this hypothetical function's behaviour we would test that specific inputs via DOM elements like buttons and text fields lead to  the DOM presenting the right output in the right place.

We don't check that function X works directly;
We check that the process that function X is a part of works in terms of user interaction and service.

This allows us to write fewer tests while effectively verifying that our app functions correctly. 

---

## 1. Creating and Running Tests

Before we look at writing tests, we need to set up a directory for the test scripts. There's a specific format for the tests directory.

- In the ```src``` directory, create a subdirectory called `__tests__`. The name must be correct. it has two underscores before and two underscores after the word 'test'.
- The test files (where we write our tests) are js files. The convention is to name these after the component they test, but with the word ```test``` chucked in. The format is as follows:
    - for a component called ```app.js```
    - the test file would be called ```app.test.js```
- Distinct test files should be written for each component, for clarity and ease of use.

Once our tests are written, we need to run them.
To do this open a console and navigate to the react app's root directory. Then run ```npm test``` (if using npm and assuming jest is installed).
The results of the test will be shown in the console along with a handy menu of useful test commands.

---

## 2. Basic Syntax

### ```it``` or ```test```
- A function used to declare a test. 
- ```it``` and ```test``` are actually both the exact same function; two synonyms are provided for user preference.
- Takes two parameters:
  1. The name of the test
  2. A function that holds the test

### ```expect```
- The condition that the test needs to pass
 - Compares the received parameter to a matcher

 ### "matcher"
 - A function that is used to define the expected condition. 
 - For example, it may specify that a string should be rendered, or that a function should be called a certain number of times and/or using specific parameters. 
 - Matchers exist for a diverse range of test types.

 ### ```render```
 - The method used to render a given component. 
 - The result of the render is checked against the matcher.


## Example: Basic React Syntax

```
import React from 'react'                          
import {render} from '@testing-library/react'
import App from './App'
 
 it('should take a snapshot', () => {                 < "it" or "test" function is called.
                                                        The name of the test is the first parameter.
                                                        A function is the second parameter; this describes the test.
    const { asFragment } = render(<App />)            < "render" is used to render the App component. 
                                                        "render" returns various methods useful for testing.
                                                        Destructuring is used here to extract the "asFragment" method. 
    expect(asFragment(<App />)).toMatchSnapshot()     < "expect" specifies that this is the outcome to be tested.
                                                        "asFragment(<App/>)" is compared to a saves snapshot using the ".toMatchSnapshot()" method. 
   })
});
```
---

## 3. Screenshot Testing

The example above was a screenshot test. Screenshot tests show if a component's render output has *changed* since the last time a snapshot was taken and saved.

```
import React from 'react'
import { render } from '@testing-library/react'
import App from './App'
 
 it('should take a snapshot', () => {
    const { asFragment } = render(<App />)
    expect(asFragment(<App />)).toMatchSnapshot()
   })
});
```

When a screenshot test is run for the first time it saves a 'screenshot' of the rendered component's structure.
Subsequently when the test is run it compares the rendered component's current structure to that screnshot, and alerts the tester if differences exist.
The tester can then decide whether to 'fix' the change or accept the change and update the snapshot for future tests.

The screnshot itself is saved in the ```__snapshots__``` directory of your React app, which is automatically created when a snapshot test is first run in the react app. 

The snapshot's structure informs the tester of the component's structure. Here's a mock snapshot for a simple component that just returns a div containing a h1.

```
exports[`Take a snapshot should take a snapshot 1`] = `
<DocumentFragment>
  <div class="App">
    <h1>Testing</h1>
  </div>
</DocumentFragment>
`;
```

### <font color="orange">*-ALWAYS- check the structure of your snapshots when they are created or updated to ensure they are as they should be, or you may get false positives when testing!*</font>

- Pass Meaning: The render is identical to the screenshot.
- Fail Meaning: The render doesn't match the screenshot. This may or may not be a problem, as updating a component may cause a change to the render. The tester will need to compare the screenshot to the render and decide on whether to update the screenshot or not. ```test``` provides a comparison of the two in the console.

---

## 4. Testing the Presence of DOM Elements

When testing DOM elements we need to be able to select them from whthin the rendered components. There are a few ways to do this.

<br />

### Example 1: selectByTestId
When writing react components, we can include the parameter ```data-testid``` in any rendered HTML element. This special parameter is used specifically to allow our testing suite to verify whether these components are rendered during tests. They're used like ```id``` or ```class```, except that they're used specifically for testing.

There's an example in TestElements.js. It looks like this. Pay particular attention to the ```data-testid``` parameters.

### <font color="2d93ad">Component:</font>

```
import React from 'react'

const TestElements = () => {
 const [counter, setCounter] = React.useState(0)
  
 return (
  <>
    <h1 data-testid="counter">{ counter }</h1>
    <button data-testid="button-up" onClick={() => setCounter(counter + 1)}> Up</button>
    <button disabled data-testid="button-down" onClick={() => setCounter(counter - 1)}>Down</button>
 </>
    )
  }
  
export default TestElements
```

### <font color="4e4c67">Test:</font>

We use ```data-testid``` to target elements for our tests. Much of the test syntax is like that of the snapshot test, but there are a few differences.

```
  it('should equal to 0', () => {                              
    const { getByTestId } = render(<TestElements />);       < the "getByTestId" method is extracted from render by destructuring
    expect(getByTestId('counter')).toHaveTextContent(0)     < We target the element with testid 'counter', then apply the       
                                                              ".toHaveTextContent" method to it to see if it contains a specific string.
   });
```
  
<br />

### Example 2: getByText

This method is much like getByTestId, but it targets an element on the basis of the string it contains as a child.

Here's a super simple example.

### <font color="2d93ad">Component:</font>

```
const SayHello = () => {
  return (
    <>
      <h1>Hello there!</h1>
    </>
  )
}
  
export default SayHello
```

### <font color="4e4c67">Test:</font>

```
  it('should render the text "Hello there!", () => {                              
    const { getByText } = render(<SayHello />);               < the "getByTexy" method is extracted from render by destructuring
    expect(getByText('Hello there!')).toBeInTheDocument()     < We target the element containing the text 'Hello there!', then apply the       
                                                              ".tobeInTheDocument" method to it to see if it exists within the render.
   });
```

getByText has diverse applications. 

-It can verify whether a correct prop is passed into a component by providing mock props and seeing if they appear in the render.
-It can verufy whether a component processes data into a specific string format correctly.

I'll add more examples here as they come to mind.

---

## 5. Testing Events & their Outcomes

### Part 1: Testing Events

The ```fireEvent``` method is used in tests to implement an event on a specified rendered element.

```
fireEvent.click(getByTestId('button-a'))              < in this example fireEvent 'clicks' the element with testID 'button-a'
```

We can then test the state of the component after the event has been invoked. Here's an example.

### <font color="2d93ad">Component:</font>

```
const Counter = () => {

  const [counter, setCounter] = React.useState(0)
  
  return (
    <>
      <button onClick={() => setCounter(counter + 1)}>Press to count!</button>    < This element is a button.
                                                                                    When clicked it increments the value of ```counter``` state by 1.
      <h1 data-testid="counter">Current value: {counter}</h1>                     < This ```h1``` displays the current value of ```counter```.
    </h1>
  )
}
```
This component consists of a button which, when clicked, increases the value stored in state ```counter``` by 1.

### <font color="4e4c67">Test:</font>

This test verifies that the initial value of ```h1``` is 0;
and that clicking the button causes the  ```h1``` to have an output of ```1```.

```
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import TestEvents from './TestEvents'

it('increments counter', () => {
    const { getByTestId } = render(<Counter />); 

    expect(getByTestId('counter')).toHaveTextContent('1')
    
    fireEvent.click(getByTestId('counter'))

    expect(getByTestId('counter')).toHaveTextContent('1')
  });
```

<br />

### Part 2: Specifying Event Outcomes using Matchers

Once the event has been triggered we need to verify that the outcome is what we expect. We use __matchers__ for this.

A summary list of matchers for DOM elements can be found here: https://github.com/testing-library/jest-dom

(For reference, a list of 'normal' jest matchers can e found here: https://jestjs.io/docs/en/expect.html)

Here are some matchers I've found particularly useful.

- ```.toHaveAttribute```: Checks whether the given element has a specific attribute. Can also check the attribute's value.

```
expect(NameInput).toHaveAttribute("type", "text")


expect(EmailButton).toHaveAttribute("href", "mock@email.com")
 ```

---

## 6. Testing Asynchronous Events

After an asynchronous function is invoked it runs 'in the background' while the remaining script continues to execute, and returns its output (if any) once it has finished executing. As such, to test an asynchronous event we need to make sure that the test itself waits for the asynchronous function to complete before assessing anything affected by its output.

We have a special method for this: ```waitFor```.

we use ```waitFor``` in conjunction with ```await``` to produce an asynchronous ```expect``` instance. We apply a matcher to the expect method in the normal way. 

That's a clunky sentence! The example below may make it clearer.

### <font color="2d93ad">Component:</font>

```
const Counter = () => {
  const [counter, setCounter] = React.useState(0)

  const delayCount = () => (                                     <This function contains a setTimeout method, 
    setTimeout(() => {                                            which executes a passed function (1st parameter)
      setCounter(counter + 1)                                     after a specified timespan in milliseconds (2nd parameter).
    }, 500)
  )
  
return (
  <>
    <button data-testid="counter-button" onClick={delayCount}>Press to count!</button>      < Becasue of setTimeout the button takes 500ms to increment counter.
    <h1 data-testid="counter-display">Current value: { counter }</h1>
 </>
    )
  }
```

### <font color="4e4c67">Test:</font>

import { render, cleanup, fireEvent, waitFor } from '@testing-library/react';

 
  it('increments counter after 0.5s', async () => {
    const { getByTestId, getByText } = render(<Counter />); 

    fireEvent.click(getByTestId('counter-button'))                        < The counter button 'is clicked' using fireEvent.

    const counter = await waitFor(() => getByText('1'))                   < We use getByText to verify the presence of a counted number like before, 
                                                                             but we put this in an async waitFor method, which retries the function it contains until it returns true.
                                                                             waitFor has a default timeout of 1s.

    expect(counter).toHaveTextContent('1')                                < Thanks to counter being async, .toHaveTextContent will only run 
                                                                            after expect(counter) has executed (returned a value using getByText ('1')) or timed out.
  });




This document contains some additonal information on asynchronous testing tools: https://testing-library.com/docs/dom-testing-library/api-async

---



## Resources

Using these resources will help you design your tests.

- ```expect``` commands: https://jestjs.io/docs/en/expect.html

