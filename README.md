# SASS introduction

**This README is a quick and dirty html2markdown conversion, look at the `index.html` or https://zeropaper.github.io/x-sass-intro**

## Steps

- Create a folder (`mkdir sass-intro`) and go in it (`cd sass-intro`).
- Initialize a package.json (`npm init` and then answer the questions).
- Create a `index.html` file (`touch index.html)`.
- Create a folder for your sass files (`mkdir sass`).
- Create a `styles.scss` file inside the sass folder.
- Add the `link` tag to load the compiled CSS (point to `dist/styles.css`).
- We need some tools to compille the SCSS files into CSS files.  
  We saw that gulp-sass is not playing nice if there's a syntax error in your SCSS file.  
  So we will use other tools. 😊 [node-sass](https://npmjs.org/package/node-sass) which will compile the SCSS files into CSS (install it locally, as a development `npm i -D node-sass`)
- Make a dist directory (`mkdir dist`)
- Let's try node-sass (`./node_modules/.bin/node-sass`).  
  To compile our `sass/styles.scss` into `dist/styles.css` and watch for changes: `./node_modules/.bin/node-sass --watch --output dist --source-map true --source-map-contents sass --output-style compressed sass/styles.scss`.
- Add something to the `sass/styles.scss` (like, background-color of body).
- Instead of typing the long command line to compile the CSS, we will add a script in our `package.json`.  
  Open the `package.json` and add "start" script like this:

  ````
  "scripts": {
    "start": "node-sass --watch --output dist --source-map true --source-map-contents sass --output-style compressed sass/styles.scss",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ````

  (you can actually remove the `./node_modules/.bin/` because npm knows)  
  Now, in the terminal, stop the node-sass tool (`CTRL + C`) and then run `npm run start`.  
  Make a change to your SCSS file and save to check.
- If you want to run [live-server](https://npmjs.org/package/live-server) (and you probably want), open a new tab in your terminal (`CTRL + SHIFT + T`).
- Let's style the `<code>` and `<pre>`.  
    It's a good idea to declare a few variables and then alterate them with the tools (functions) that SASS provides:

  ````
  $body-bg-color: #eee;
  $body-color: #333;
  $border-radius-small: 3px;

  body {
    background-color: $body-bg-color;
  }

  code,
  pre {
    border-radius: $border-radius-small;
    border: 1px solid darken($body-bg-color, 30);
    background-color: darken($body-bg-color, 10);
  }
  ````

- If you want the right syntax highlighting (SASS) in Sublime Text 3:
  - Hit `CTRL + SHIFT + P` and then search for "Package Control: Install Package".
  - Then search for "SASS" and... install...
  - You might need to look at the bottom of the editor to change the syntax
- Let's take it to the next level with variables.  
  In your `sass` directory, create a file called `_variables.scss`.  
  Import the `_variables.scss` in your `styles.scss` so `@import "_variables";` (at the top of the file).  
  Add `!default` after the variable values of your `styles.scss` like that:

  ````
  $body-bg-color: #eee !default;
  $body-color: #333 !default;
  $border-radius-small: 3px !default;
  ````

  In your `_variables.scss` override the default variable values by declaring the same variables (but without the `!default`).

  `$body-bg-color: lightblue;`

  What happens is that the variables (defined in `_variables.scss`) will be used instead of the ones declared in your `styles.scss` because the variables in `styles.scss` have the `!default` (and therefore can be considered as "fallback" or "default" values).  
  The underscore at the beginning of the `_variables.scss` is important because it instructs the SASS compiler **not** to create a CSS file for it.
- Usually, we want to have 1 file which is not prefixed with underscore (and will therefore result in a CSS file after compilation).  
  So let's move the content of our `styles.scss` file (except the `@import` statement) to a new file called `_generic.scss` (which you have to create, obviously) and then import it in your `styles.scss`.
- Say hello to your best friend (the developer tools) and inspect a `<pre>` tag, look at the style and you will see in which file the declaration has been made (in this case, the `_generic.scss` file).  
  It might not look like much, but if you have dozens of SCSS files, you surely understand how useful it can be.
- With SASS you can manipulate colors with functions.  
  To demonstrate that create a new `_color-functions-demo.scss` file and declare a variable `$demo-color: #de5665;`. Add a class declaration `.color-functions-demo` which has the `$demo-color` for background color and its inverted value for color `color: invert($demo-color);`.  
  After the color declaration, add the following code:

  ````
  &:empty:before { // the :empty selector will select... empty elements (caution! a white space will not be considered empty)
    padding: 0.5rem;
    display: inline-block;
    content: '#{$demo-color}';// this #{$variable-name} is used because otherwise the resulting CSS would be '$demo-color'.
  }
  ````

  You will also need an empty div with the color-functions-demo class in your HTML (thanks capt'n obvious).
- SASS comes with an other powerful feature called `[@mixin](http://devdocs.io/sass/index#mixin)`, which we will try to illustrate by creating one for color function examples.  
  In your `_color-functions-demo.scss` add

  ````
  .demo-block:empty:before {
    padding: 0.5rem;
    display: inline-block;
  }

  @mixin color-demo($color) {
    .transformed {
      background-color: $color;
      color: invert($color);
      &:empty:before {
        content: '#{$color}';
      }
    }
  }

  .demo-block {
    .darken {
      @include color-demo(darken($demo-color, 20));
    }
    .invert {
      @include color-demo(invert($demo-color));
    }
    .saturate {
      @include color-demo(saturate($demo-color, 10));
    }
    .adjust-hue {
      @include color-demo(adjust-hue($demo-color, 90));
    }
  }
  ````

  and then use your mixin
  
  ````
  <table class="demo-block">
    <tbody>
      <tr class="darken">
        <td>darken</td>
        <td class="transformed"></td>
      </tr>
      <tr class="invert">
        <td>invert</td>
        <td class="transformed"></td>
      </tr>
      <tr class="saturate">
        <td>saturate</td>
        <td class="transformed"></td>
      </tr>
      <tr class="adjust-hue">
        <td>adjust-hue</td>
        <td class="transformed"></td>
      </tr>
    </tbody>
  </table>
  ````

- To demonstrate loops we will create a `_grid.scss` (and import it in the `styles.scss`) and create a "bootstrapish" grid system using the `@for` loop.
  ````
  <div class="row">
    <div class="col-1">.col-1</div>
    <div class="col-2">.col-2</div>
    <div class="col-3">.col-3</div>
    <div class="col-6">.col-6</div>
  </div>
  ````

- The ampersand sign allows you to nest selectors without spaces inbetween.

  ````
  .my-class-name {
    .child-class-name {
      // ...
    }
  }
  ````

  will compile to

  ````
  .my-class-name .child-class-name {}
  ````

  but if you need to avoid the space between both class names like for links styling

  ````
  .my-class {
    // some properties
    &-variant {
      // ...
    }
  }
  // or
  .other-class {
    // some properties
    &:before {}
  }
  ````

  will compile to

  ````
  .my-class {}
  .my-class-variant {}
  /* or */
  .other-class {}
  .other-class:before {}
  ````

  It's extremely useful for [BEM](http://getbem.com/naming/) naming convention (used by [MDC](https://material-components-web.appspot.com/card.html "look at the class names")).

  ````
  // kind of...
  .mdc-card {
    &__media {}
    &__primary {
      &--theme-dark {}
    }
  }
  ````
