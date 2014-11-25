# Как создать динамическую страницу с БЭМ

В этой статье мы рассмотрим пример реализации динамической функциональности страницы по БЭМ-методологии.

# Общее видение проекта

Нам нужно создать страницу, на которой будет находиться фраза приветствия, поле ввода и кнопка. После введения данных в поле и нажатия кнопки будет появляться приветственное сообщение.

# Установка

**Требования к установке:**

* наличие [Node.js](http://nodejs.org)

Устанавливаем свой собственный БЭМ-проект. В этом нам поможет шаблонный репозиторий, который содержит необходимый минимум конфигурационных файлов и папок, [project-stub](https://github.com/bem/project-stub). Делаем локальную копию `project-stub`.

    git clone https://github.com/bem/project-stub.git start-project  
    cd start-project      
    npm install  

Сейчас вызов всех команд [bem-tools](http://ru.bem.info/tools/bem/bem-tools/) возможен так `./node_modules/bem/bin/bem`. Для упрощения запуска устанавливаем npm-пакет `bem-cli`. Это позволит нам использовать bem-tools локально. 

    sudo npm install bem-cli -g  

Собираем проект:

    bem make

Для дальнейшей работы с проектом выполним команду: 

    bem server

Зайдите в браузер, чтобы проверить запустился ли БЭМ-сервер на компьютере:

    http://localhost:8080/desktop.bundles/index/index.html

Страница index.html содержит примеры блоков библиотеки `bem-components`.
Для изменений конфигураций проекта в активном окне нужно перезапускать `bem-server`. Текущий процесс прерывается (`Ctrl+C`) и снова возобновляется командой `bem-server`. 

# Пошаговая инструкция по созданию проекта

Этот раздел содержит пошаговую информацию по созданию динамической страницы.

* [Создание макета страницы](#page_creation) - разберём варианты создания страницы  
** [Написание BEMJSON-декларации](#BEMJSON_declaration) - задекларируем наш блок в BEMJSON-файле, с указанием всех необходимых элементов и модификаторов
* [Создание блока](#block_creation) - самостоятельно создадим блок  
** [Создание блока в определённой технологии](#block_using_concrete_tech) - рассмотрим возможность создавать файлы блока конкретных технологий 
* [Модификация блока `hello`](#block_hello_modification) - внесём необходимые изменения в файлы блока  
** [Модификация JS-файла](#JS_modification) - переопределим блок библиотеки с помощью технологии JavaScript  
** [Модфикация BEMHTML-файла](#BEMHTML_modification) - переопределим блок библиотеки с помощью технологии BEMHTML  
** [Модификация CSS-файла](#CSS_modification) - переопределим блок библиотеки с помощью технологии CSS
* [Сборка проекта](#build) - запустим сборку проекта

<a name="page_creation"></a>

## Создание макета страницы

Макеты страниц размещаются в каталоге `desktop.bundles`. Данный каталог содержит одну директорию - `index`. Вы можете продолжать работу с этой директорией. Для этого необходимо заменить декларацию файла `index.bemjson.js` на желаемую, [например](#BEMJSON_declaration). Или можно создать новый макет страницы, используя [bem-tools](http://ru.bem.info/tools/bem/bem-tools/) команду:

    bem create -l desktop.bundles -b hello

`-l desktop.bundles` - указывает на уровень переопределения `desktop.bundles`  
`-b hello` - определяет имя блока страницы, в нашем случае `hello`

<a name="BEMJSON_declaration"></a>

### Написание BEMJSON-декларации

Нам нужно создать блок приветствия, который будет состоять из отдельных элементов (поля ввода, кнопки и самой фразы приветствия). Для этого в BEMJSON-файле необходимо создать блок `hello`. В этот блок положить элемент `greeting`, который, в свою очередь, содержит 2 блока: `input` и `button`. Блоки `input` и `button` берём готовые из библиотеки [bem-components](https://github.com/bem/bem-components). Нужно просто задекларировать блоки на странице `desktop.bundles/hello/hello.bemjson.js` или на странице `desktop.bundles/index/index.bemjson.js`, если вы все-таки решили использовать директорию **index**. Если вы хотите изменить блоки, внесите все необходимые правки на своем уровне переопределения. Подробнее об уровнях переопределения можно почитать [здесь](http://ru.bem.info/tools/bem/bem-tools/commands/#Уровень-переопределения).

```
({
    block: 'page',
    title: 'hello',
    head: [
        { elem: 'css', url: '_hello.css' }
    ],
    scripts: [{ elem: 'js', url: '_hello.js' }],
    content: [
       {
           block: 'content',
           content: [
               {
                   block: 'hello',
                   name: 'BEMHTML',
                   content: [
                       {
                           elem: 'greeting',
                           content: 'Привет, %пользователь%!'
                       }, {
                               block: 'input',
                               mods: {theme: 'islands', size: 'm'},
                               placeholder: 'Имя пользователя'
                       }, {
	                           block : 'button',
	                           text : 'Пыщь',
	                           mods : { theme : 'islands', size : 'm' }
                       }
                 ]
               }
           ]
       }
    ]
})
```

Чтобы проверить запустился ли файл, зайдите в браузер по адресу:

    http://localhost:8080/desktop.bundles/hello/hello.html

<a name="block_creation"></a>

## Создание блока

На уровне `desktop.blocks` создаем директорию **hello**. По умолчанию блок будет представлен набором файлов для всех технологий реализации (`CSS/STYL`, `JS`, `BEMHTML`).

    bem create -l desktop.blocks -b hello 

`-l desktop.blocks` - указывает на уровень переопределения `desktop.blocks`  
`-b hello` - задаёт имя директории блока, в нашем случае `hello`     

Другой способ - создание блока вручную. Для этого на уровне `desktop.blocks` создадим папку **hello** и разместим в ней необходимые для проекта файлы технологий реализации блока.     

<a name="block_using_concrete_tech"></a>

### Создание блока в определенной технологии

Блок можно создавать в определенной технологии. Подробнее об этом вы можете почитать [здесь](http://ru.bem.info/tools/bem/bem-tools/commands/#Создание-блока-в-определённой-технологии).

<a name="block_hello_modification"></a>

## Модификация блока `hello`
Чтобы наш проект заработал должным образом, нужно модифицировать файлы технологий реализации. 

<a name="JS_modification"></a>

### Модификация JS-файла

Нам нужно написать JavaScript в терминах [модульной системы](http://ru.bem.info/tools/bem/modules/), который будет подставлять имя, введенное в поле `input` при нажатии кнопки в саму фразу приветствия. Для этого изменяем контент JavaScript-файла на: 

```
modules.define(
    'hello',
    ['i-bem__dom'],
    function(provide, BEMDOM) {
        provide(BEMDOM.decl('hello', {
            onSetMod: {
                'js': {
                    'inited': function() {
                        this._input = this.findBlockInside('input');
                        this._button = this.findBlockInside('button');

                        this._button.on('click', function() {
                            this.elem('greeting').text('Hello, ' + this._input.getVal() + '!');
                        }, this);
                    }
                }
            }
        }));
    });
```

<a name="BEMHTML_modification"></a>

### Модификация BEMHTML-файла

Пишем BEMHTML-шаблон, в котором указываем, что наш блок **hello** имеет JavaScript-реализацию:

```
block hello, js: true
```

<a name="CSS_modification"></a>

### Модификация CSS-файла

Для нового блока создаём свои CSS-правила. К примеру, такие:

```
.hello
    color: green
```
<a name="build"></a>

## Сборка проекта

При обновлении страницы bem-server пересобирал только ту часть проекта, которую затронули наши изменения. Для сборки всего проекта целиком воспользуемся командой:

    bem make

# Результат

Обновляем страницу в браузере с адресом 

    http://localhost:8080/desktop.bundles/hello/hello.html

и видим страницу приветствия:

*скрин*

Заполняем поле ввода любым именем, например, Вася, нажимаем кнопку и получаем результат:

*скрин*
