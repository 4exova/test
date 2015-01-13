## Быстрый старт по созданию статической страницы с БЭМ

В этой статье рассмотрен пример реализации статической страницы по [БЭМ-методологии](https://ru.bem.info/method/).

## Что должно получиться

Страница приветствия пользователя, содержащая поле ввода, кнопку и текст. При нажатии на кнопку страница обновляется и текст дополняется значением, введенным в поле.

![страница приветствия](https://img-fotki.yandex.ru/get/15561/289488726.0/0_21856b_45166628_orig)

## С чего начать

### Минимальные требования

Установленная платформа [Node.js 0.10](http://nodejs.org).

### Локальная копия и настройка окружения

Для быстрого и простого создания БЭМ-проекта потребуется [шаблонный репозиторий](https://github.com/bem/project-stub), содержащий необходимый минимум конфигурационных файлов и папок.

1.  Сделайте локальную копию `project-stub`.

    **NB** В данном документе описана процедура установки для ревизии   [13ae0e18ef8f48bc552b4944f7e5971c5b5f4768](https://github.com/bem/project-stub/ commit/13ae0e18ef8f48bc552b4944f7e5971c5b5f4768). Процесс установки последующих версий может отличаться.

    ```bash
    git clone https://github.com/bem/project-stub.git start-project
    cd start-project
    git checkout 13ae0e18ef8f48bc552b4944f7e5971c5b5f4768
    npm install
    ```

2.  Запустите сервер с помощью [ENB](https://ru.bem.info/tools/bem/enb-bem-techs/):

    ```bash
    node_modules/.bin/npm start
    ```

3.  Проверьте результат по ссылке [http://localhost:8080/desktop.bundles/index/index.html](http://localhost:8080/desktop.bundles/index/index.html).

    Должна открыться страница с примерами блоков библиотеки:

    ![главная страница](https://img-fotki.yandex.ru/get/15572/289488726.0/0_21775c_db513672_orig)

## Пошаговая инструкция по созданию проекта

1.  [Создайте страницу](#page_creation)
    1.1 [Опишите страницу в BEMJSON-файле](#BEMJSON_declaration)
2.  [Создайте блок](#block_creation)
3.  [Реализуйте блок hello](#block_hello_modification)
    3.1 [В технологии JavaScript](#JS_modification)
    3.2 [В технологии BEMHTML](#BEMHTML_modification)
    3.3 [В технологии CSS](#CSS_modification)

<a name="page_creation"></a>

### 1.  Создайте страницу

Исходники страниц размещаются в каталоге `desktop.bundles`. Изначально в проекте присутствует главная страница `index.html` с примерами блоков библиотеки [bem-components](http://ru.bem.info/libs/bem-components/).

Для начала работы с собственным проектом создайте новую страницу. Разместите в каталоге `desktop.bundles` папку с именем `hello` и добавьте в нее файл `hello.bemjson.js`.


<a name="BEMJSON_declaration"></a>

#### 1.1 Опишите страницу в BEMJSON-файле

[BEMJSON-файл](https://ru.bem.info/technology/bemjson/) – это структура страницы, описанная в терминах блоков, элементов и модификаторов.

1. Добавьте описание блока **hello** в файл `desktop.bundles/hello/hello.bemjson.js`.  
    Блок **hello** - это сущность, которая содержит в себе все необходимые для проекта элементы.

    ```js
    { block: 'hello' }
    ```

2. Поместите элемент **greeting** с текстом приветствия пользователя (поле **content**) в блок **hello**.  
    Элемент **greeting** - это подсущность, в которую вкладывается фраза приветствия и готовые реализации блоков.

    ```js
    content: [
            {
                block: 'hello',
                content: [
                    {
                        elem: 'greeting',
                        content: 'Привет, %пользователь%!'
                    }
                ]
            }
        ]
    ```

3. Чтобы создать поле ввода и кнопку возьмите готовые реализации блоков **input** и **button** из библиотеки `bem-components` и добавьте их в элемент **greeting**.
Так будет выглядеть полный код страницы `desktop.bundles/hello/hello.bemjson.js`:

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
                        content: 'Привет, %пользователь%!'
                    },
                    {
                            block: 'input',
                            mods: { theme: 'islands', size: 'm' },
                            name: 'name',
                            placeholder: 'Имя пользователя'
                    },
                    {
                            block : 'button',
                            mods : { theme : 'islands', size : 'm', type : 'submit' },
                            text : 'Нажать'
                    }
                ]
            }
        ]
    })
    ```
Чтобы убедиться, что страница отображает все необходимые элементы, откройте [http://localhost:8080/desktop.bundles/hello/hello.html](http://localhost:8080/desktop.bundles/hello/hello.html).

Если вы хотите внести какие-либо изменения в существующие блоки, это можно сделать на своем [уровне переопределения](https://ru.bem.info/tools/bem/bem-tools/levels/).

<a name="block_creation"></a>

### 2. Создайте блок

Чтобы элементы на странице работали должным образом необходимо прописать дополнительную функциональность блока **hello** на своем уровне переопределения.

1.  Создайте вручную директорию блока **hello** на уровне `desktop.blocks`.
2.  Разместите в ней необходимые для проекта [файлы технологий реализации блока](https://ru.bem.info/method/filesystem/) (`CSS`, `JS`, `BEMHTML`). Файлы и директория блока должны иметь одинаковое название.

    **hello.js**, **hello.bemhtml** описывают динамическую функциональность страницы.  
    **hello.css** изменяет внешний вид элементов на странице.

<a name="block_hello_modification"></a>

### 3. Реализуйте блок `hello`

<a name="JS_modification"></a>

#### 3.1 Реализуйте блок в технологии JavaScript

1. В файле `desktop.blocks/hello/hello.js` опишите реакцию блока на выполнение действий пользователем с помощью специального свойства `onSetMode`. При нажатии кнопки в текст приветствия будет подставляться имя пользователя, введенное в поле **input**.
JavaScript-код написан с использованием декларативного JavaScript-фреймворка – [i-bem.js](https://ru.bem.info/technology/i-bem/).

    ```js
    onSetMod: {
        'js': {
            'inited': function() {
                this._input = this.findBlockInside('input');

                this.bindTo('submit', function(e) {
                    e.preventDefault();
                    this.elem('greeting').text('Привет, ' + this._input.getVal() + '!');
                });
            }
        }
    }

    ```

2. Данный JavaScript-код представьте с помощью модульной системы [YModules](https://ru.bem.info/tools/bem/modules/):

    ```js
    modules.define(
        'hello', // имя блока
        ['i-bem__dom'], // подключение зависимости
        function(provide, BEMDOM) { // функция, в которую передаются имена используемых модулей
            provide(BEMDOM.decl('hello', { // декларация блока
                onSetMod: { // конструктор для описания реакции на события
                    'js': {
                        'inited': function() {
                            this._input = this.findBlockInside('input');

                            this.bindTo('submit', function(e) { // событие, на которое будет реакция
                                e.preventDefault(); // предотвращение работы события по умолчанию (отправка данных формы на сервер с перезагрузкой страницы)
                                this.elem('greeting').text('Привет, ' + this._input.getVal() + '!');
                            });
                        }
                    }
                }
            }));
        });
    ```

<a name="BEMHTML_modification"></a>

#### 3.2 Реализуйте блок в технологии BEMHTML

[BEMHTML](http://ru.bem.info/technology/bemhtml/current/rationale/) - технология, которая преобразует входные данные из BEMJSON-файла в HTML.

1. Напишите [BEMHTML-шаблон](http://ru.bem.info/technology/bemhtml/current/reference/), в котором укажите, что блок **hello** имеет JavaScript-реализацию.
2. Оберните блок `hello` в форму, добавив моду `tag`.

```js
block('hello')(
    js()(true),
    tag()('form')
);
```

<a name="CSS_modification"></a>

#### 3.3 Реализауйте блок в технологии CSS

Для блока `hello` создайте свои CSS-правила. Например, такие:

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
Чтобы изменить положение блока на странице и при этом оставить его в первоначальном виде в библиотеке, подмешайте элемент с помощью метода микс во входных данных (BEMJSON).

```js
{
    block: 'input',
    mods: { theme: 'islands', size: 'm' },
    mix: { block: 'hello', elem: 'input' }, // подмешиваем элемент для добавления CSS-правил
    name: 'name',
    placeholder: 'Имя пользователя'
}
```

<a name="build"></a>

## Результат

Чтобы увидеть итог проделанной работы обновите страницу:

    http://localhost:8080/desktop.bundles/hello/hello.html

Поскольку проект состоял всего из одной страницы, то в полной сборке не было необходимости. О том, как написать более сложный проект описано в статье [Создаем свой проект на БЭМ](http://ru.bem.info/tutorials/start-with-project-stub/).
