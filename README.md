# simple-faq-box
An HTML and JavaScript implementation of an faq drop down box. Feel free to also take a look at:
* Our [YouTube Video](https://www.youtube.com/watch?v=pPEc6G0x0u4), where we code this in real time with some music.
* Our [Dev Page](https://bytethisstore.com/articles/pg/simple-faq-box), where we cover this in more detail.

## FAQ Box Requirements
For this example, we'll create a simple & straightforward faq box, which will:
* present all questions at once.
* hide all answers by default.
* let users toggle answer visibility by clicking on questions.
* let users view more than one answer at once.

## Writing the FAQ Component
There are a few different approaches we could take to write our component, including:
* Create the entire popup box and its elements with JavaScript.
* Create an HTML structure with the questions and answers, then use JavaScript to reference the elements and add JavaScript logic.
* Use **Web Components** to declare FAQ Box elements in the HTML document as if they were native elements.

For this example, we will use the web components option. In the sections below, we'll introduce some of the basic concepts of web components. For a detailed introduction, check out [Mozilla's Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) documentation.

## Creating the HTML Structure
We want to be able to structure our HTML so it is evident which components correspond to questions, answers, and containers; you should be able to understand exactly what is happening at a glance. For this example, our structure will look something like:
```html
<faq-box>
    <faq-box-qa>
        <faq-box-question>
            ... question goes here ...
        </faq-box-question>
        <faq-box-answer>
            ... answer goes here ...
        </faq-box-answer>
    </faq-box-qa>
    <!-- Additional questions & answers -->
</faq-box>
```
In the HTML above, we can clearly see what each element is for and how the overall structure is defined:
* **faq-box**: container for a set of questions and answers
* **faq-box-qa**: container for a single question and answer pair
* **faq-box-question**: a single question
* **faq-box-answer**: an answer tied to its sibling question node

Before we can use this type of HTML, we will need to *register* those components in some way.

## Registering Custom Elements
We will be "registering" the four elements shown above in two ways:
1. **Implicit:** use the custom tag names in the HTML markup and add CSS properties to those tag names.
1. **Explicit with Custom Elements Registry:** create a JavaScript class which extends *HTMLElement* and register it, along with its selector, with JavaScript's custom elements registry.

For this implementation, we will only *explicitly* register the ``faq-box-qa`` element, as that will be the only one which requires JavaScript functionality. The others will be handled *implicitly*.

To register our ``faq-box-qa`` with the registry, we will need to invoke JavaScript's custom elements functionality *after* our class has been declared:
```javascript
class FaqBoxQA {
    /* ..... */
}

customElements.define(
    //selector to use for this element
    "faq-box-qa",
    //element class reference
    FaqBoxQA
);
```
The **customElements** object is a global JavaScript object which can be invoked anywhere without any need for importing or unboxing.

## Creating the Component Class
In order to provide the show and hide functionality, we will need to implement some JavaScript for our **FaqBoxQA** class, corresponding to the ``faq-box-qa`` selector, to toggle the visibility of the answer when the question is clicked. To make our component class, we will create a JavaScript class which extends **HTMLElement**:
```javascript
class FaqBoxQA extends HTMLElement {

    constructor() {
        super();
        //answer will be hidden by default
        this._answerIsOpen = false;
    }

    get answerIsOpen() {
        return this._answerIsOpen;
    }

    //when we set if the answer is open, change CSS accordingly
    set answerIsOpen(isOpen) {
        this._answerIsOpen = isOpen;

        //update css for answer element
        const answerElement = this.lastElementChild;
        answerElement.style.display = isOpen
            ? "block"
            : "none";

        console.log("set answer is open:", isOpen);
    }

    /**
     * This is called by the JavaScript engine when the
     * component is placed onto the DOM
     * 
     * We'll add listeners for click events for questions
     */ 
    connectedCallback() {
        //wrap in a set timeout as children are not immediately available
        setTimeout(() => {
            const questionElement = this.firstElementChild;

            //add event listener to toggle answer visibility
            questionElement.addEventListener("click", () => {
                this.answerIsOpen = !this.answerIsOpen;
            });
        }, 50);
    }
}
```
The component above will be instantiated once per individual ``faq-box-qa`` component on the DOM; thus, it will not interfere with any other components. In other words, each instance will handle its own state and elements only.

## Styling the Custom Elements
The components can be styled in the same manner as normal HTML elements:
```css
faq-box {
    display: block;

    /* .... */
}

faq-box-qa {
    display: block;

    /* .... */
}

faq-box-question {
    display: block;
    
    /* .... */
}

faq-box-answer {
    /* .... */

    /* Will be updated to show/hide answer */
    display: none;
}
```
The only difference between styling these elements and elements such as *div* is: **custom elements and tags must have their "display" attribute specificed manually**. Setting ``display: block`` will ensure that styles are applied in the same way they would be to ``div`` elements.