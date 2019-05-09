# Luke's Edits *Remove after reading/applying to your Repository* 

Upon viewing the theme, what the following was identified: 
* Missing a stylesheet
* Inline CSS flipping the site 180 Degrees
* A large blank space between the header and the products on Category Pages with Product Filters

My first step to engage with this was to load the site (localhost:3000) was to view my JavaScript Console. I was using Google Chrome, and so I used "Inspect." In the console, I found 422 errors, which is not particularly verbose, or rather, it does not really point us in the right direction. From there, I attempted to find what was flipping the site. Because the entire site's CSS was missing besides these elements, this pointed me to look into Inline-CSS, or CSS within HTML/template files. These are commonly added to the header and footer files. 

From here, I viewed the page source. I found first that a lot of the expected header information was missing, but no CSS. Once I scrolled to the bottom, I located the following code: 

  ```      <style>
            body {
           width: 100%;
           height: 100%;
           -moz-transform: rotate(180deg);
           -webkit-transform: rotate(180deg);
           -ms-transform: rotate(180deg);
           -o-transform: rotate(180deg);
           transform: rotate(180deg);
        }
        </style> ```
        
 From here, I located the file, using XCode, which is under Templates > Components > Common > Footer.html, and removed the code. (Lines 92-102) 
 
 Next, I move to engage with the missing stylesheet. I have a feeling like it has to do with the header as I noted, but the Header file in that templates directory is normal. I then moved to look at Stencil CLI, which was running the site, and saw an error denoting that the file does not exist. When I navigated to the directory, I found that was absolutely correct, and so I copied over the example header from the most recent version of Cornerstone. From there, the stylesheet remained missing; however, the error in Stencil changed to "Error: no mixin names lazy-loaded-img", which noted line 168 as the corresponding space:
 
 ```{ Error: no mixin named lazy-loaded-img

Backtrace:
	layouts/header/header.scss:168
    at options.error (/Users/lukesteiner/.nvm/versions/node/v6.4.0/lib/node_modules/@bigcommerce/stencil-cli/node_modules/@bigcommerce/node-sass/lib/index.js:277:32)
  formatted: 'Error: no mixin named lazy-loaded-img\n\n       Backtrace:\n       \tlayouts/header/header.scss:168\n        on line 168 of layouts/header/header.scss\n>>     @include lazy-loaded-img;\n   -------------^\n',
  message: 'no mixin named lazy-loaded-img\n\nBacktrace:\n\tlayouts/header/header.scss:168',
  column: 14,
  line: 168,
  file: 'layouts/header/header.scss',
  status: 1 }```
  
Notice, it back traced to the file layouts/header/header.scss, and so when I move to the directory Assets > scss > layouts > header > header.scss, and edited the file. Upon removing line 168, which notes a call to load lazy-loaded-img (which doesn't exist) the stylesheet was applied to the site.
 
 Finally, upon visiting my example category page, I could see the large white space where a carousel would show on the homepage. From here Stencil is no longer throwing errors, the console and page source aren't wholly helpful, and the issue still appears to be CSS/styling related. With that in mind, I moved back to the Inspect tool, and used the "Inspect Element" cursor to select CSS. When I selected the full space, there were a few styles that were being applied, from classes such as container and body. Body stuck out to me, as the description for that file, from the file is as follows: 
 
 "Header is fixed on small screens use the content body to create the white space between it and the header on all situations." 
 
 Upon opening this file, under Assets > scss > layouts > body > _body.scss, I was able to see that the syntax was all "greyed," which indicates that it is commented out. This basically means that there are symbols before and/or after that are breaking the syntax, so that it does not run. By removing the */ from the front, and /* from the back, I fixed the syntax, and the white space was cleared. 

Moving forward, feel free to download this version of the theme, or make the changes I noted above. If you wish to be walked through how I troubleshooted this, and how I used the tools noted here, please come to my training with your peers on 5/10, in Missile Command, at 11AM. 

# Cornerstone
[![Build Status](https://travis-ci.org/bigcommerce/cornerstone.svg?branch=master)](https://travis-ci.org/bigcommerce/cornerstone)

Stencil's Cornerstone theme is the building block for BigCommerce theme developers to get started quickly developing premium quality themes on the BigCommerce platform.

### Stencil Utils
[Stencil-utils](https://github.com/bigcommerce/stencil-utils) is our supporting library for our events and remote interactions.

## JS API
When writing theme JavaScript (JS) there is an API in place for running JS on a per page basis. To properly write JS for your theme, the following page types are available to you:

* "pages/account/addresses"
* "pages/account/add-address"
* "pages/account/add-return"
* "pages/account/add-wishlist"
* "pages/account/recent-items"
* "pages/account/download-item"
* "pages/account/edit"
* "pages/account/return-saved"
* "pages/account/returns"
* "pages/auth/login"
* "pages/auth/account-created"
* "pages/auth/create-account"
* "pages/auth/new-password"
* "pages/blog"
* "pages/blog-post"
* "pages/brand"
* "pages/brands"
* "pages/cart"
* "pages/category"
* "pages/compare"
* "pages/errors"
* "pages/gift-certificate/purchase"
* "pages/gift-certificate/balance"
* "pages/gift-certificate/redeem"
* "global"
* "pages/home"
* "pages/order-complete"
* "pages/page"
* "pages/product"
* "pages/search"
* "pages/sitemap"
* "pages/subscribed"
* "page/account/wishlist-details"
* "pages/account/wishlists"

These page types will correspond to the pages within your theme. Each one of these page types map to an ES6 module that extends the base `PageManager` abstract class.

```javascript
    export default class Auth extends PageManager {
        constructor() {
            // Set up code goes here; attach to internals and use internals as you would 'this'
        }
    }
```

Within `PageManager` you will see methods that are available from all classes, but there are three really important methods. The following methods have the signature `func (callback)` and the callback takes `callback(err)` if there is an error.

#### before(callback)
When this method is implemented in your class, the code contained therein will be executed after the constructor but before the `loaded()` method. This provides a shim for your code before your main implementation logic runs.

```javascript
    export default class Auth extends PageManager {
        constructor() {
            // Set up code goes here
        }
        before(callback) {
            // Code that should be run before any other code in this class

            // Callback must be called to move on to the next method
            callback();
        }
    }
```

#### loaded(callback)
This method will be called when the constructor has run and `before()` has been executed. Main implementation code should live in the loaded method.

```javascript
    export default class Auth extends PageManager {
        constructor() {
            // Set up code goes here
        }
        loaded(callback) {
            // Main implementation logic here

            // Callback must be called to move on to the next method
            callback();
        }
    }
```

#### after(callback)
This method is for any cleanup that may need to happen and it will be executed after `before()` and `loaded()`.

```javascript
    export default class Auth extends PageManager {
        constructor() {
            // Set up code goes here
        }
        after(callback) {
            // Main implementation logic here

            // Callback must be called to move on to the next method
            callback();
        }
    }
```

### JS Template Context Injection
Occasionally you may need to use dynamic data from the template context within your client-side theme application code.

Two helpers are provided to help achieve this.

The inject helper allows you to compose a JSON object with a subset of the template context to be sent to the browser.

```
{{inject "stringBasedKey" contextValue}}
```

To retrieve the parsable JSON object, just call `{{jsContext}}` after all of the `{{@inject}}` calls.

For example, to setup the product name in your client-side app, you can do the following if you're in the context of a product:

```html
{{inject "myProductName" product.title}}

<script>
// Note the lack of quotes around the jsContext handlebars helper, it becomes a string automatically.
var jsContext = JSON.parse({{jsContext}}); // jsContext would output "{\"myProductName\": \"Sample Product\"}" which can feed directly into your JavaScript

console.log(jsContext.myProductName); // Will output: Sample Product
</script>
```

You can compose your JSON object across multiple pages to create a different set of client-side data depending on the currently loaded template context.

The stencil theme makes the jsContext available on both the active page scoped and global PageManager objects as `this.context`.


## Static assets
Some static assets in the Stencil theme are handled with Grunt if required. This
means you have some dependencies on grunt and npm. To get started:

First make sure you have Grunt installed globally on your machine:

```
npm install -g grunt-cli
```

and run:

```
npm install
```

### Icons
Icons are delivered via a single SVG sprite, which is embedded on the page in
`templates/layout/base.html`. It is generated via a grunt task `grunt svgstore`.

The task takes individual SVG files for each icon in `assets/icons` and bundles
them together, to be inlined on the top of the theme, inside a handlebars partial.
Each icon can then be called in a similar way to an inline image via:

```
<svg><use xlink:href="#icon-svgFileName" /></svg>
```

The ID of the SVG icon you are calling is based on the filename of the icon you want,
with `icon-` prepended. e.g. `xlink:href="#icon-facebook"`.

Simply add your new icon SVG file to the icons folder, and run `grunt svgstore`,
or just `grunt`.

#### License

(The MIT License)
Copyright (C) 2015-2017 BigCommerce Inc.
All rights reserved.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense,and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
