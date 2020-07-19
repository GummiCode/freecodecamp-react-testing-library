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

The example above was a screenshot test.

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

The screnshot itself is saved in the ```__snapshots__``` directory of your React app, which is automatically created when a snapshot test is first run. The snapshot's structure informs the tester of the component's structure. Here's a mock snapshot for a simple component that just returns a div containing a h1.

```
exports[`Take a snapshot should take a snapshot 1`] = `
<DocumentFragment>
  <div class="App">
    <h1>Testing</h1>
  </div>
</DocumentFragment>
`;
```

- Pass Meaning: The render is identical to the screenshot.
- Fail Meaning: The render doesn't match the screenshot. This may or may not be a problem, as updating a component may cause a change to the render. The tester will need to compare the screenshot to the render and decide on whether to update the screenshot or not. ```test``` provides a comparison of the two in the console.


