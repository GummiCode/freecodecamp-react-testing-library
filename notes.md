# Notes: react-testing-library


## 0. Intro

These notes are intended as a referernce for personal use while I learn to better use react-testing library.
The repo these notes sit within is a clone of a training repository provided by Ibrahima Ndaw at freecodecamp.
https://www.freecodecamp.org/news/8-simple-steps-to-start-testing-react-apps-using-react-testing-library-and-jest/

NB: I'm using jest. As such I won't use a manually entered ```afterEach(cleanup)``` in my tests. The guide linked above explains what this means.

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
- Describes the test itself
- Parameters:
  1. The name of the test
  2. A function that holds the test

### ```expect```
- The condition that the test needs to pass
 - Compares the received parameter to a matcher

 ### "matcher"
 - A function that is used to define the expected condition. 
 - For example, it may specify that a string should be rendered, or that a functiopn should be called a certain number of times and/or using specific parameters. 
 - Matchers exist for a diverse range of test types.

 ### ```render```
 - The method used to render a given component. 
 - The result of the render is checkedg against the matcher.


## Example: Basic React Syntax

```
import React from 'react'                          
import {render} from '@testing-library/react'
import App from './App'
 
 it('should take a snapshot', () => {                 < "it" or "test" can be used to define the block as a test. 
                                                        The name of the tes tis passed as a parameter.
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

## 4. Testing DOM Elements

When writing react components, we can include the parameter ```data-testid``` in any rendered HTML element. This special parameter is used specifically to allow our testing suite to verify whether these components are rendered during tests. They're used like ```id``` or ```class```, except that they're used specifically for testing.

There's an example in TestElements.js. It looks like this. Pay particular attention to the ```data-testid``` parameters.

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

We use ```data-testid``` to target elements  for our tests. Much of the test syntax is like that of the snapshot test, but there are a few differences.

```
  it('should equal to 0', () => {                              
    const { getByTestId } = render(<TestElements />);       < the "getByTestId" method is extracted from render by destructuring
    expect(getByTestId('counter')).toHaveTextContent(0)     < We target the element with testid 'counter', then apply the       
                                                              ".toHaveTextContent" method to it to see if it contains a specific string.
   });
```

---

## 5. Testing for Specific Text Content

The example above verifies whether a specific string is rendered by the component. This has diverse applications. 

-It can verify whether a correct prop is passed into a component by providing mock props and seeing if they appear in the render.
-It can verify whether a function in a component generates and provides a correct output to a rendered element.

I'll add more examples here as they come to mind.

---

