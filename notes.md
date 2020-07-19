# Notes: react-testing-library


## Intro

These notes are intended as a referernce for personal use while I learn to better use react-testing library.
The repo these notes sit within is a clone of a training repository provided by Ibrahima Ndaw at freecodecamp.
https://www.freecodecamp.org/news/8-simple-steps-to-start-testing-react-apps-using-react-testing-library-and-jest/

## Basics

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

 ### ``render```
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


