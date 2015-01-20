## Quick start for static page creation with BEM

This article describes implementation example of static page using [BEM methodology](https://en.bem.info/method/).

## Expected outcome

User greeting page, that contains an input field, a button and a text. The page refreshes after button clicking and text is filled up with the value entered in the input field.

![greeting page](https://img-fotki.yandex.ru/get/16156/289488726.0/0_21888e_4908d18d_orig)

## Starting with

### Minimal requirements

Installed platform [Node.js 0.10](http://nodejs.org).

### Local copy and environment setting

For quick and easy creation of BEM project use [template repository](https://github.com/bem/project-stub). It contains the minimal configuration files and folders.

1.  Make local copy of `project-stub`.

    **NB** This document describes an installation procedure for revision [13ae0e18ef8f48bc552b4944f7e5971c5b5f4768](https://github.com/bem/project-stub/ commit/13ae0e18ef8f48bc552b4944f7e5971c5b5f4768). The installation procedure of next versions may differ.

    ```bash
    git clone https://github.com/bem/project-stub.git start-project
    cd start-project
    git checkout 13ae0e18ef8f48bc552b4944f7e5971c5b5f4768
    npm install
    ```

2.  Run the server with help of [ENB](https://ru.bem.info/tools/bem/enb-bem-techs/)(for the moment this article is available only in russian language).

    ```bash
    node_modules/.bin/npm start
    ```

3.  Check the result on [http://localhost:8080/desktop.bundles/index/index.html](http://localhost:8080/desktop.bundles/index/index.html).

    Page with library blocks implementations should open:

    ![main page](https://img-fotki.yandex.ru/get/15493/289488726.0/0_218be7_cbbd5b69_orig)

## Step-by-step instruction for project creation

1.  [Create page](#page_creation)  
  1.1 [Describe page in BEMJSON file](#BEMJSON_declaration)
2.  [Create block](#block_creation)
3.  [Implement block hello](#block_hello_modification)  
  3.1 [In JavaScript technology](#JS_modification)  
  3.2 [In BEMHTML technology](#BEMHTML_modification)  
  3.3 [In CSS technology](#CSS_modification)

When all steps have been completed you can watch the [result](#result). 

<a name="page_creation"></a>

### 1.  Create page

Pages source code are stored in the `start-project/desktop.bundles` directory. The main page `index.html` contains blocks implementations of the [bem-components](https://en.bem.info/libs/bem-components/) library.

Create new page to start your own project. Paste `hello` directory in the `desktop.bundles` and add `hello.bemjson.js` file into it.

<a name="BEMJSON_declaration"></a>

#### 1.1 Describe page in BEMJSON file

A [BEMJSON file](https://en.bem.info/technology/bemjson/) describes a page structure in BEM terms: blocks, elements and modifiers.

1. Add in your project description of block `hello` in the `desktop.bundles/hello/hello.bemjson.js` file.  
  **hello** block is an entity, that contains all necessary elements for the project.

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

2. Place `greeting` element with text of user greeting (**content** field) in `hello` block.  
  **greeting** element is a sub-entity, that contains greeting phrase and ready-made blocks implementations.

    ```js
    content: [
            {
                block: 'hello',
                content: [
                    {
                        elem: 'greeting',
                        content: 'Hello, %user%!'
                    }
                ]
            }
        ]
    ```

3. To create an input field and a button take provided by the `bem-components` library `input` and `button` blocks and add them to the `greeting` element.

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

To consider that page shows all necessary objects, open [http://localhost:8080/desktop.bundles/hello/hello.html](http://localhost:8080/desktop.bundles/hello/hello.html).

You may provide some changes into existing blocks on your [redefinition level](https://en.bem.info/tools/bem/bem-tools/levels/).

<a name="block_creation"></a>

### 2. Create block

To provide appropriate work of all object on the page, it is necessary to specify additional functionality of `hello` block on your redefinition level.

1.  Manually create directory of `hello` block on the `desktop.blocks` level.
2.  Place required for the project [block's implementation technology files](https://en.bem.info/method/filesystem/) (`CSS`, `JS`, `BEMHTML`) into **hello** directory.
    Block directory name and its nested files should coincide with block name specified in BEMJSON file.

   *  `hello.js` – describes dynamic page functionality;  
   *  `hello.bemhtml` – template for generation of block HTML representation;  
   *  `hello.css` – changes objects design on the page.

<a name="block_hello_modification"></a>

### 3. Implement hello block

For block representation in BEM terms it is necessary to describe it in the implementation technology files.

<a name="JS_modification"></a>

#### 3.1 Implement block in JavaScript technology

1. Describe a block reaction depending on user action with help of `onSetMod` property in the `desktop.blocks/hello/hello.js` file. After button clicking greeting text will be replaced with the user name entered in the input field.  
JavaScript code is written using the [i-bem.js](https://ru.bem.info/technology/i-bem/) (for the moment this article is available only in russian language) declarative JavaScript framework.

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

2. To represent current JavaScript code, use modular system [YModules](https://en.bem.info/tools/bem/modules/).

    ```js
    modules.define(
        'hello', // block name
        ['i-bem__dom'], // dependence connection
        function(provide, BEMDOM) { // function to which names of the used modules are transferred
            provide(BEMDOM.decl('hello', { // block declaration
                onSetMod: { // constructor for reaction description on event
                    'js': {
                        'inited': function() {
                            this._input = this.findBlockInside('input');

                            this.bindTo('submit', function(e) { // event on which there will be a reaction
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

#### 3.2 Implement block in BEMHTML technology

[BEMHTML](https://en.bem.info/technology/bemhtml/current/rationale/) – technology that processes BEMJSON declaration to create HTML layout of a web page.

1. Write [BEMHTML tempate](https://en.bem.info/technology/bemhtml/current/reference/) and specify that `hello` block has JavaScript implementation.
2. Implement `hello` block with form, adding `tag` mode.

```js
block('hello')(
    js()(true),
    tag()('form')
);
```

<a name="CSS_modification"></a>

#### 3.3 Implement block in CSS technology

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

## Result

To see the result of this project, please refresh the page:

    http://localhost:8080/desktop.bundles/hello/hello.html

Since the project consisted only of one page, in full build was no need. How to write a more complex project is described in [Starting your own project](https://en.bem.info/tutorials/start-with-project-stub/) article.
