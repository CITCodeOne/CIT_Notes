React.js is a JavaScript library developed by engineers at Facebook.

1. React is fast. Apps made in React can handle complex updates and still feel quick and responsive
2. React is modular. Instead of writing large, dense files of code, you can write many smaller, reusable files. React's modularity can be beautiful solution to #JavaScript's maintainability problems. 
3. React is flexible. You can use React for interesting projects that have nothing to do with making a web app. People are still figuring out React's potential. 
4. React is popular. While this reason has admittedly little to do with React's quality, the truth is that understanding React will make you more employable.

#### Hello world in React.js

```JSX
const h1 = <h1>Hello world</h1>;
```

React.js looks like a hybrid code between html and javascript. 

First part looks like JavaScript since it starts with const and ends with ;. But if you tried to run it in a HTML file, it wouldn't work. 

The same goes for the `<h1>Hello world</h1>` part, that looks exactly like HTML. But this part wouldn't work if you tried to run it in a JavaScript file. 

Although it contains what looks like it contains HTML, it doesn't. It does however belong in a JavaScript file. The part that looks like HTML, is something call #JSX.

### JSX
JSX is a syntax extension for JavaScript. It was written to be used with React. JSX code looks a lot like HTML. 

Syntax extension, in this case, means that JSX is not valid JavaScript. Web browsers can't read it. 

If a JavaScript file contains JSX code, then that file with have to be compiled. This means that before the file reaches a web browser, JSX compiler will translate any JSX into regular JavaScript. 

When creating JSX expressions, there is one very important rule. A JSX expression must have exactly one outermost element.

Example:
```JSX
const paragraphs = (
  <div id="i-am-the-outermost-element">
    <p>I am a paragraph.</p>
    <p>I, too, am a paragraph.</p>
  </div>
);
```

This code will work, however, this code will not work:

```JSX
const paragraphs = (
  <p>I am a paragraph.</p> 
  <p>I, too, am a paragraph.</p>
);
```

The first opening tag and the final closing tag of JSX expression must belong to the same JSX element.

Read more in [[Cheatsheet]]

#### The Virtual DOM
A special thing about React's root's render() method is that it only updates #DOM elements that have changed. 

Meaning that if you render the exact same thing twice in a row, the second render will do nothing. 

Why is that significant? Only updating the necessary DOM elements is a large part of what makes React so successful. Which is accomplished using Reacts Virtual DOM.