# Learn Advanced CSS Layout Techniques

## Target empty elements using the :empty pseudo-class

> You can target an element that has no child elements by using the `:empty` pseudo-class. With browser support down to IE9, it's solid, easy way to select empty elements without any additional markup.

Be aware that whitespace is considered a "child", so `:empty` will not work if the element has no children, but has space between the opening and closing tags.


## Target Positional Elements Using *-Of-Type CSS pseudo-classes

> Learn how to target elements based on their position inside of a parent element in relation to its siblings.

```css
article p:first-of-type {
  font-size: 2rem;
  font-style: italic;
}

article img:last-of-type {
  border: 10px solid slategray;
}

blackquote:only-of-type {
  border-left: 5px solid slategray;
  padding-left: 1rem;
  font-weight: bold;
  font-style: italic;
  color: slategray;
}

article p:nth-of-type(odd) {
  color: maroon;
}

/* article p:nth-of-type(4n + 3) {
  color: maroon;
} */
```


## Create a fixed-fluid-fixed layout using CSS `calc()`

> CSS `calc()` allows you to mix and match units to get real-time calculations. It's useful when you need to size elements but you have some unknown number that you have to account for.


## Dynamically Size Elements with Pure CSS

> Learn how to size elements based on the dimensions of the viewport, even when the browser is resized - no JavaScript needed!

```css
.Box {
  background-color: slategray;
  width: 60vw;
  height: 40vh;
  margin: 30vh 20vw 30vh 20vw;
}
```

```css
section {
  width: 100vw;
  height: 100vh;

  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;

  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

section:nth-of-type(1) {
  background-image: url(/* ... */);
}

section:nth-of-type(2) {
  background-image: url(/* ... */);
}

section:nth-of-type(3) {
  background-image: url(/* ... */);
}

section:nth-of-type(4) {
  background-image: url(/* ... */);
}
```


## Easily Reset Styles With a Single CSS value

> There are times where you need to reset a an element’s styles. Instead of overwriting it with even more style declarations, you can simply reset them.

```css
body: {
  color: #2E603C;
}

button {
  cursor: pointer;
  padding: 1.4rem;
  border: 0;
  border-radius: 4px;
  text-align: center;
  text-decoration: none;
  font-size: 1.4rem;
  font-weight: 500;
  color: white;
  background-color: #3AC569;
}

section button {
  color: unset;
  font-size: unset;
}
```

## Layout Responsive Fluid Columns Using CSS

> `column-width` operates like `min-width`, not `width`. The browser will render as many columns as it can with the width provided. If each column can take up more than the value provided, they will do so.

> `column-span` enables a specific element to ignore `column-count` and `column-width`. It can be set to an integer to span a certain number of columns, or "all" to span them all. However, this property does not work in Firefox. A workaround could be to move the element (say, a heading) outside of the container with the `columns` applied to it. That way, it remains outside of the automatic column flow.

> `column-fill` allows you to change the way content flows into columns. By default, it's set to "balance", where content is distributed as much as possible between columns. It can also be set to "auto", but in order to do so, it requires setting a fixed height. This breaks the idea of fluid, responsive layouts, so use it with caution. You'll also need some browser prefixes, so be sure to reference this [browser support chart](https://tympanus.net/codrops/css_reference/column-span/).


## Control Image Aspect Ratio Using CSS

> Resize images and videos to fill their parent and maintain their aspect ratio with pure CSS. The new `object-fit` and `object-position` properties allow you to scale images and videos, much like you could with `background-size` and `background-position`.

```css
img {
  width: 90vw;
  height: 75vh;
  border: 3px solid tomato;
  object-fit: cover; /* contain, fill, none, scale-down */
  object-position: 50% 50%;
}
```
