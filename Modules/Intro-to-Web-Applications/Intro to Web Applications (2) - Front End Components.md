# HTML
[HTML (HyperText Markup Language)](https://en.wikipedia.org/wiki/HTML) HTML is at the very core of any web page we see on the internet. It contains each page's basic elements, including titles, forms, images, etc.
```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```
This would display:  
<img width="1108" height="243" alt="Pasted image 20250905204048" src="https://github.com/user-attachments/assets/90254c23-f8f5-428e-883d-cb1facc25e8c" />  
The main `HTML` tag should contain all other elements within the page, which falls under `document`, distinguishing between `HTML` and documents written for other languages, such as `XML` documents.

Each HTML element is opened and closed with a tag that specifies the element's type 'e.g. `<p>` for paragraphs', where the content would be placed between these tags. Tags may also hold the element's id or class 'e.g. `<p id='para1'>` or `<p id='red-paragraphs'>`', which is needed for CSS to properly format the element.

## URL Encoding
In URLs browsers use ASCII encoding which only allows alphanumerical characters and certain special characters. Any other characters outside this set aren't allowed therefore needs to be encoded and represented by something else. When encoding these characters are replaced with a `%` sign followed by two hexadecimal digits (space can be represented by `%20` or `+`).

| Character | Encoding |
| --------- | -------- |
| space     | %20 or + |
| !         | %21      |
| "         | %22      |
| #         | %23      |
| $         | %24      |
| %         | %25      |
| &         | %26      |
| '         | %27      |
| (         | %28      |
| )         | %29      |
For example when we type `hi!` in google's search bar we will see it represented as `search?q=hi%21` in the URL.

## Usage
- `<head>`: usually contains elements that are not directly printed on the page, like the page title.
- `<body>`: all main page elements are located under it. 
- `<style>`: holds the page's CSS code.
- `<script>`: which holds the JS code of the page.
Each of these elements is a node within a [DOM (Document Object Model)](https://en.wikipedia.org/wiki/Document_Object_Model). The DOM standard is separated into 3 parts:
- Core DOM - the standard model for all document types
- XML DOM - the standard model for XML documents
- HTML DOM - the standard model for HTML documents
For example, from the above tree view, we can refer to DOMs as `document.head` or `document.h1`, and so on.

# Cascading Style Sheets (CSS)
It is the stylesheet language used alongside HTML to format and set the style of HTML elements. At a fundamental level, CSS is used to define the style of each class or type of HTML elements (i.e., `body` or `h1`). This could include the font family, font size, background color, text color and alignment, and more. Example:
```css
body {
  background-color: black;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: helvetica;
  font-size: 10px;
}
```
This is why we may set unique IDs or class names for certain HTML elements so that we can later refer to them within CSS or JavaScript when needed.

## Syntax
The style of each HTML element is defined between curly brackets and within it the properties and their values are defined as `element { property : value; }`.

CSS can also be used for advanced animations for a wide variety of uses, from moving items all the way to advanced 3D animations. Many CSS properties are available for animations, like `@keyframes`, `animation`, `animation-duration`, `animation-direction`, and many others.

Furthermore, CSS can be used alongside other languages to implement their styles, like `XML` or within `SVG` items, and can also be used in modern mobile development platforms to design entire mobile application User Interfaces (UI).

## Frameworks
CSS frameworks exist to make it much faster and easier to create beautiful HTML elements by providing a collection of CSS style-sheets and designs. Some of the most common ones:
- [Bootstrap](https://www.w3schools.com/bootstrap4/)
- [SASS](https://sass-lang.com/)
- [Foundation](https://en.wikipedia.org/wiki/Foundation_\(framework\))
- [Bulma](https://bulma.io/)
- [Pure](https://purecss.io/)

# JavaScript
While `HTML` and `CSS` are mainly in charge of how a web page looks, `JavaScript` is usually used to control any functionality that the front end web page requires. Without `JavaScript`, a web page would be mostly static and would not have much functionality or interactive elements.

Within the page source code, `JavaScript` code is loaded with the `<script>` tag, as follows:
```html
<script type="text/javascript">
..JavaScript code..
</script>
```
We can also load remote `JavaScript` code with `src` and the script's link:
```html
<script src="./script.js"></script>
```
As with HTML, there are many sites available online to experiment with `JavaScript`. One example is [JSFiddle](https://jsfiddle.net/) which can be used to test `JavaScript`, `CSS`, and `HTML` and save code snippets.

## Uses
- Updating the web page view in real-time.
- Dynamically updating content in real-time.
- Accepting and processing user input.
- Automate complex processes and perform HTTP requests to interact with the back end components through technologies like [Ajax](https://en.wikipedia.org/wiki/Ajax_\(programming\)).
- Used alongside `CSS` to drive advanced animations that would not be possible with `CSS` alone.

## Frameworks
These platforms make development more efficient. They introduce libraries that make it very simple to re-create advanced functionalities, like user login and user registration, and they introduce new technologies based on existing ones, like the use of dynamically changing `HTML` code, instead of using static `HTML` code. Some of the most common JavaScript frameworks:
- [Angular](https://www.w3schools.com/angular/angular_intro.asp)
- [React](https://www.w3schools.com/react/react_intro.asp)
- [Vue](https://www.w3schools.com/whatis/whatis_vue.asp)
- [jQuery](https://www.w3schools.com/jquery/)
