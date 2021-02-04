---
layout: post
title:      "Access THIS"
date:       2021-02-04 14:37:49 -0500
permalink:  access_this
---

Javascript and its use of first class functions took a bit of getting used to.  Dissecting code to truly understand what is going on beneath the hood of a single function sometimes requires examining several other functions, and after going down a lengthly rabbit hole, it made my head spin.  At first, it also seemed a bit tedious, indirect, and I didn't quite understand the benefits of calling functions on other functions.  But after spending the JS module learning the language, I started to get the hang of utilizing them and realized just how useful they can be.  And while there are many benefits of first class functions that I'm sure I'm just starting to discover, I stumbled upon one of them while working on my JS project.

It started as I was building out a reset button for my Virtual Lite-Brite app.  The reset button's job would be to reset the Lite-Brite canvas to either a clean slate or to the last saved version of the user's drawing.  It would also disable itself and it's sister 'save-button' once the canvas had been reset.   

The reset button was created within my `SideBar` class.  Creating a new instance of `SideBar` accepts a `drawing` object which would then assign the `drawing` object to `this`...`this` being the instance of `SideBar`:

```
class SideBar {
    constructor(drawing) {
        this.drawing = drawing
        this.createButtons()
        this.createDrawingList()
    }
		...
```

When building out my `createResetBtn()` function, I needed to test whether or not `this.drawing` was a new drawing (in which case, the canvas would reset to a clean slate) or an existing drawing (in which case, the canvas would reset to it's most recently saved version).  The easiest thing to do would be to test if `this.drawing.id` was truthy.  However, when adding my event listener, I was running into an error accessing `this.drawing` because `this` was no longer the `SideBar` instance. `this` was now the actual reset button-which made `this.drawing.id` throw an error because the reset button does not have a `drawing` attribute.

My original work-around for this was to create a variable that would assign itself to the `drawing` object which I could then use within my `addEventListener` to not only access the `drawing.id`, but also the `drawing.pattern` for resetting the board to it's most recently saved version.  The working code looked like this:


```
createResetBTN = () => {
        let div = document.createElement('div')
        this.reset = document.createElement('button')
        this.reset.id = 'reset-button'
        this.reset.classList.add("sidebar-button")
        this.reset.innerHTML = 'Reset'
        const drawing = this.drawing 
        this.reset.addEventListener('click', function (e) {
            if (drawing.id){
                let pegs = document.querySelectorAll('.peg')
                for (let i = 0; i < drawing.pattern.length; i++) {
                    pegs[i].setAttribute('fill', drawing.pattern[i])
                }
            } else {
                document.querySelectorAll('.peg').forEach(peg => peg.setAttribute('fill', 'black'))
            }
            this.disabled = true
            document.querySelector('#save-button').disabled = true
        })
        this.reset.disabled = true
        div.append(this.reset)
        sidebar.append(div)
    }
```

However, it was bothering me that I was required to re-assign the drawing object to yet another variable in order to access that already assigned data.  It wasn't very DRY and there had to be another way.

Enter another function!!

At first , I was trying to somehow bind the `SideBar` instance to the `addEventListener` callback function, but quickly realized that I could just create an entirely new function within my `SideBar` class which would automatically make my `SideBar` attributes accessible.  It would also clean up my `addEventListener` function and felt like a much cleaner way of doing things.  I first extracted the callback function code within my original `addEventListener` and changed it to create new `SideBar` function:

```
handleReset = () => {
        if (this.drawing.id){
            let pegs = document.querySelectorAll('.peg')
            for (let i = 0; i < this.drawing.pattern.length; i++) {
                pegs[i].setAttribute('fill', this.drawing.pattern[i])
            }
        } else {
            document.querySelectorAll('.peg').forEach(peg => peg.setAttribute('fill', 'black'))
        }
        this.reset.disabled = true
        document.querySelector('#save-button').disabled = true
    }
```

I then replaced the current callback function with my new `handleReset()` function within the `addEventListener` making my final version of `createResetBTN()` look like this:

```
createResetBTN = () => {
        let div = document.createElement('div')
        this.reset = document.createElement('button')
        this.reset.id = 'reset-button'
        this.reset.classList.add("sidebar-button")
        this.reset.innerHTML = 'Reset'
        this.reset.addEventListener('click', this.handleReset)
        this.reset.disabled = true
        div.append(this.reset)
        sidebar.append(div)
    }
```


