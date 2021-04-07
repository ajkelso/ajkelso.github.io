---
layout: post
title:      "Handling Change"
date:       2021-04-07 20:14:30 +0000
permalink:  handling_change
---


Creating controlled forms in React is an extremely helpful way to keep track of your user's inputs and aids in passing the form data to wherever it needs to go.  However, if you are working with a form that contains multiple inputs and are using a functional component, it can be very tedious to have to create multiple pieces of state with `useState` & `handleEachInputsChange` functions in order to save the data to state.

A DRYer way to do this is to utilize `useState` only once and compose the component's state to mirror something closer to the way you might handle state in a class component.

In my final, phase 5,  Flatiron project, I was building an app which would allow users to create Top Five lists (ala High Fidelity) for various Categories.  The app would also assign points to each list's Nominees in that category (based on their rank from 1-5) and then compile a master lists for each Category to display it's top ranked nominees. 

Because of this, I had a form which took in 6 inputs: 1 for the Category and 5 more for each 'place': 1st-5th.

The long form way of doing this would be to declare each input using useState and then create a different `handleChange` function to call on each input's `onChange` event:

```
const [category, setCategory]  = useState(")
const [first, setFirst] = useState(")
const [second, setSecond] = useState(")
...etc...

const handleCategoryChange = (e) => setCategory(e.target.value)
const handleFirstChange = (e) => setFirst(e.target.value)
const handleSecondChange = (e) => setSecond(e.target.value)
...etc...

<input type="text" placeholder="Choose a Category..." onChange={handleCategoryChange} value={category}/><br/>
                <label htmlFor="First">1.</label>
                <input type="text" name="first" onChange={handleFirstChange} value={first}/><br/>
                <label htmlFor="Second">2.</label>
                <input type="text" name="second" onChange={handleSecondChange} value={second}/><br/>
...etc...
```

While this will get the job done, it creates a lot more work by repeating code with minor differences and increases room for error.

Instead, I decided to declare a full form object utilizing `useState` so that I could abstract my `handleChange` function without declaring additional variables or functions:

```
const [formData, setFormData] = useState({
        category: "",
        first: "",
        second: "",
        third: "",
        fourth: "",
        fifth: ""
    })
```

This causes the `handleChange` function to look a bit more like a class component that utilizes `setState` and requires the use of the spread operator to make sure preexisting state data doesn't get lost.  However, creating the state in this format allows you to abstract the `handleChange` function so that you only have one to create:

```
const handleChange = (e) => {
        setFormData((prevalue) => {
            return {
                ...prevalue,
                [e.target.name]: e.target.value
            }      
        })
    }
```

As you'll notice, in order for the above function to work, you must also assign the `name` property to your inputs which should be that same as the keys within your state object that is associated with each input.  In addition, the value property must be adjusted so that the key gets called on your state object (ie: `value={category}` becomes `value={formData.category}`.My final form looks like this:

```
<form onSubmit={handleSubmit}>
                <input type="text" name="category" placeholder="Choose a Category..." onChange={handleChange} value={formData.category}/><br/>
                <label htmlFor="First">1.</label>
                <input type="text" name="first" onChange={handleChange} value={formData.first}/><br/>
                <label htmlFor="Second">2.</label>
                <input type="text" name="second" onChange={handleChange} value={formData.second}/><br/>
                <label htmlFor="Third">3.</label>
                <input type="text" name="third" onChange={handleChange} value={formData.third}/><br/>
                <label htmlFor="Fourth">4.</label>
                <input type="text" name="fourth" onChange={handleChange} value={formData.fourth}/><br/>
                <label htmlFor="Fifth">5.</label>
                <input type="text" name="fifth" onChange={handleChange} value={formData.fifth}/><br/>
                <input type="submit"/>
            </form>
```


