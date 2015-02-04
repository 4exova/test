## Quick start for static page creation with BEM

The article describes step-by-step implementation of a static page using [BEM methodology](https://bem.info/method/).

## Expected outcome

A page that contains an input field, a button, and a greeting text. A value from the input field will be added to the greeting text when the user clicks the button.

![The greeting page](https://img-fotki.yandex.ru/get/16156/289488726.0/0_21888e_4908d18d_orig)

## First steps to start

### Minimal requirements

An installed platform [Node.js 0.10](http://nodejs.org).

### A local copy and environment setting

A [template repository](https://github.com/bem/project-stub) is the quickest and easiest way to start your BEM project. It contains the minimal configuration files and folders.

1.  Make local copy of `project-stub`.

    **NB** The document describes an installation procedure based on the revision [13ae0e18ef8f48bc552b4944f7e5971c5b5f4768](https://github.com/bem/project-stub/ commit/13ae0e18ef8f48bc552b4944f7e5971c5b5f4768). The installation procedure of next versions may differ.

    ```bash
    git clone https://github.com/bem/project-stub.git start-project
    cd start-project
    git checkout 13ae0e18ef8f48bc552b4944f7e5971c5b5f4768
    npm install
    ```

2.  Run a server with help of [ENB](https://ru.bem.info/tools/bem/enb-bem-techs/)(this article is available only in russian language).

    ```bash
    node_modules/.bin/npm start
    ```

3.  Check the result on [http://localhost:8080/desktop.bundles/index/index.html](http://localhost:8080/desktop.bundles/index/index.html).

    A page with library blocks examples should open:

    ![A main page](https://img-fotki.yandex.ru/get/15493/289488726.0/0_218be7_cbbd5b69_orig)

## Step-by-step instruction for the project creation

1.  [Create a page](#page_creation)
  1.1 [Describe the page in BEMJSON file](#BEMJSON_declaration)
2.  [Create a block](#block_creation)
3.  [Implement hello block](#block_hello_modification)
  3.1 [Use JavaScript technology](#JS_modification)
  3.2 [Use BEMHTML technology](#BEMHTML_modification)
  3.3 [Use CSS technology](#CSS_modification)

When all steps have been completed you can watch the [result](#result).

<a name="page_creation"></a>

### 1.  Create a page

Pages source code are stored in the `start-project/desktop.bundles` directory. The main page `index.html` contains blocks implementations of [bem-components](https://en.bem.info/libs/bem-components/) library.

Create a new page to start your own project.

1. Create `hello` directory in the `desktop.bundles`.
2. Add `hello.bemjson.js` file into `hello` directory.

<a name="BEMJSON_declaration"></a>

#### 1.1 Describe the page in BEMJSON file

A [BEMJSON file](https://en.bem.info/technology/bemjson/) describes a page structure in BEM terms: blocks, elements and modifiers.

1. Add a description of `hello` block  in the `desktop.bundles/hello/hello.bemjson.js` file.
  **hello** block is an entity that will contain all necessary elements for the project.

    ```js
    {
        block: 'page',
        title: 'hello',
        head: [
            { elem: 'css', url: '_hello.css' }
        ],
        scripts: [{ elem: 'js', url: '_hello.js' }],
        mods: { theme: 'islands' }
        content: [
            {
                block: 'hello'
            }
         ]
    }
    ```

2. Place `greeting` element with the greeting text (**content** field) into `hello` block.
  **greeting** element is a sub-entity, that contains the greeting phrase and ready-made blocks implementations of `bem-components` library.

    ```js
    content: [
            {
                block: 'hello',
                content: [
                    {
                        elem: 'greeting',
                        content: 'Hello, %user%!'
                `bem-components` library}
                ]
            }
        ]
    ```

3. To create an input field and a button use `input` and `button` blocks from `bem-components` library. Add these blocks to `greeting` element.

    ```js
    ({
        block: 'page',
        title: 'hello',
        head: [
            { elem: 'css', url: '_hello.css' }
        ],
        scripts: [{ elem: 'js', url: '_hello.js' }],
        mods: { theme: 'islands' },
        content: [
            {
                block: 'hello',
                content: [
                    {
                        elem: 'greeting',
                        content: 'Hello, %user%!'
                    },
                    {
                            block: 'input',
                            mods: { theme: 'islands', size: 'm' },
                            name: 'name',
                            placeholder: 'User name'
                    },
                    {
                            block : 'button',
                            mods : { theme : 'islands', size : 'm', type : 'submit' },
                            text : 'Click'
                    }
                ]
            }
        ]
    })
    ```

To consider that the page shows all necessary objects, open [http://localhost:8080/desktop.bundles/hello/hello.html](http://localhost:8080/desktop.bundles/hello/hello.html).

You can provide additional changes to existing blocks on your [redefinition level](https://en.bem.info/tools/bem/bem-tools/levels/).

<a name="block_creation"></a>

### 2. Create a block

To provide correct work to all object on the page, it is necessary to specify additional functionality of `hello` block on your redefinition level.

1.  Create a directory of `hello` block on `desktop.blocks` level.
2.  Create [the implementation technology files](https://bem.info/method/filesystem/) (`CSS`, `JS`, `BEMHTML`) required to the block in **hello** directory.
    A block directory name and its nested files must coincide with block name specified in a BEMJSON file.

   *  `hello.js` – describes dynamic page functionality
   *  `hello.bemhtml` – a template for generation of the block HTML representation
   *  `hello.css` – changes a custom design on the page

<a name="block_hello_modification"></a>

### 3. Implement `hello` block

To implement the block in BEM terms, use the created technology files.

<a name="JS_modification"></a>

#### 3.1 Implement `hello` block in JavaScript technology

1. Describe a block reaction depending on a user action using `onSetMod` property in the `desktop.blocks/hello/hello.js` file. A click on the button adds the user name from the input field into the greeting phrase.
JavaScript code is written using [i-bem.js](https://ru.bem.info/technology/i-bem/) (this article is available only in russian language) declarative JavaScript framework.

    ```js
    onSetMod: {
        'js': {
            'inited': function() {
                this._input = this.findBlockInside('input');

                this.bindTo('submit', function(e) {
                    e.preventDefault();
                    this.elem('greeting').text('Hello, ' + this._input.getVal() + '!');
                });
            }
        }
    }

    ```

2. To represent current JavaScript code, use [YModules](https://bem.info/tools/bem/modules/) modular system .

    ```js
    modules.define(
        'hello', // a block name
        ['i-bem__dom'], // dependence connection
        function(provide, BEMDOM) { // a function that received names of the used modules
            provide(BEMDOM.decl('hello', { // a block declaration
                onSetMod: { // a constructor that describes reaction on an event
                    'js': {
                        'inited': function() {
                            this._input = this.findBlockInside('input');

                            this.bindTo('submit', function(e) { // the event that causes reaction
                                e.preventDefault(); // prevention of event work by default (form data sending to the server with page reload)
                                this.elem('greeting').text('Hello, ' + this._input.getVal() + '!');
                            });
                        }
                    }
                }
            }));
        });
    ```

<a name="BEMHTML_modification"></a>

#### 3.2 Implement `hello` block in BEMHTML technology

[BEMHTML](https://bem.info/technology/bemhtml/current/rationale/) – technology that processes BEMJSON declaration to create HTML layout of a web page.

1. Write [BEMHTML tempate](https://bem.info/technology/bemhtml/current/reference/) and specify that `hello` block has JavaScript implementation.
2. Implement `hello` block with form, adding `tag` mode.

```js
block('hello')(
    js()(true),
    tag()('form')
);
```

<a name="CSS_modification"></a>

#### 3.3 Implement `hello` block in CSS technology

Create your own CSS rules for `hello` block. For example:

```js
.hello
{
    color: green;
    padding: 10%;
}

.hello__greeting
{
    margin-bottom: 12px;
}

.hello__input
{
    margin-right: 12px;
}
```
To change the position of the block on the page and leave it in its original form in the library, mix element using the method `mix` in the input data (BEMJSON).

```js
{
    block: 'input',
    mods: { theme: 'islands', size: 'm' },
    mix: { block: 'hello', elem: 'input' }, // mix element to add CSS rules
    name: 'name',
    placeholder: 'User name'
}
```
[Code sample](https://gist.github.com/4exova/683b6da16aa7aa0399f3) hello.bemjson.js.

<a name="result"></a>

## The final result

To see the result of the project, please refresh the page:

    http://localhost:8080/desktop.bundles/hello/hello.html

Since the project consists only of one page, in full build was no need. Description of a more complex project is in [Starting your own project](https://bem.info/tutorials/start-with-project-stub/) article.
