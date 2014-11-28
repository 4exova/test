# Как создать динамическую страницу с БЭМ

В этой статье мы рассмотрим пример реализации динамической функциональности страницы по [БЭМ-методологии](http://ru.bem.info/method/).

# Что должно получиться

Итогом проделанной работы будет страница с приветствием пользователя. Страница будет состоять из поля ввода, кнопки и приветствия. После того, как пользователь введет имя в поле и нажмет кнопку, его имя автоматически подставится в приветствие.

# Установка

**Требования к установке**

Для начала работы с любым БЭМ-проектом нужен [Node.js](http://nodejs.org).

Устанавливаем свой собственный БЭМ-проект. В этом нам поможет [шаблонный репозиторий](https://github.com/bem/project-stub), который содержит необходимый минимум конфигурационных файлов и папок. Делаем локальную копию `project-stub`.

    git clone https://github.com/bem/project-stub.git start-project  
    cd start-project      
    npm install  

Собираем проект с помощью [ENB](http://enb-make.info/):

    node_modules/.bin/enb make

Запускаем сервер:

    node_modules/.bin/enb server

При изменении конфигурации проекта нужно перезапускать сервер. Текущий процесс прерывается (`Ctrl+C`) и снова возобновляется командой запуска.  

Открываем браузер, чтобы проверить запустился ли сервер на компьютере: 

    http://localhost:8080/desktop.bundles/index/index.html

По умолчанию эта страница содержит примеры блоков библиотеки `bem-components`.

Альтернативный вариант для сборки проекта –– [bem-tools](http://ru.bem.info/tools/bem/bem-tools/). Результаты сборки в обоих случаях одинаковы. 

Про сборку проекта при помощи `bem-tools` вы можете почитать, пройдя по это ссылке http://ru.bem.info/tools/bem/bem-tools/commands/#bem-make.

# Пошаговая инструкция по созданию проекта

Этот раздел содержит пошаговую информацию по созданию динамической страницы.

* [Создание страницы](#page_creation) –– рассмотрим варианты создания страницы.  
** [Описание страницы в BEMJSON-файле](#BEMJSON_declaration) –– опишем страницу в БЭМ-терминах.
* [Создание блока](#block_creation) –– самостоятельно создадим блок.  
** [Создание блока с файлом определенной технологии](#block_using_concrete_tech) –– рассмотрим возможность создавать файлы блока конкретных технологий. 
* [Переопределение блока `hello`](#block_hello_modification) –– внесем необходимые изменения в файлы технологий блока.  
** [Реализация блока в технологии JS](#JS_modification) –– реализуем блок в технологии JavaScript.  
** [Реализация блока в технологии BEMHTML](#BEMHTML_modification) –– реализуем блок в технологии BEMHTML.  
** [Реализация блока в технологии CSS](#CSS_modification) –– реализуем блок в технологии CSS.
* [Сборка проекта](#build) –– запустим сборку проекта.

<a name="page_creation"></a>

## Создание страницы

Макеты страниц размещаются в каталоге `desktop.bundles`. Данный каталог содержит одну директорию –– `index`. Вы можете продолжать работу с этой директорией. Для этого необходимо заменить декларацию файла `index.bemjson.js`. Пример описания BEMJSON-файла мы рассмотрим ниже.  

Альтернативный вариант –– создание новой страницы, используя команду [bem-tools](http://ru.bem.info/tools/bem/bem-tools/):

    bem create -l desktop.bundles -b hello

* `-l desktop.bundles` –– указывает на уровень переопределения `desktop.bundles`;  
* `-b hello` –– определяет имя блока страницы, в нашем случае `hello`.

Выбирайте удобный для вас вариант. Дальше мы продолжим создание проекта на основе страницы `hello`.

<a name="BEMJSON_declaration"></a>

### Описание страницы в BEMJSON-файле

#### Создаем свой блок

Первым делом нам нужно добавить на страницу блок приветствия. Для того, чтобы разместить его на странице, добавим блок **hello** в файл `desktop.bundles/hello/hello.bemjson.js`.

{ block: 'hello' }

Дальше внутрь блока **hello** поместим элемент **greeting** с приветствием пользователя:

```
{
  elem: 'greeting',
  content: 'Привет, %пользователь%!'
}
```

#### Используем готовые блоки

Кроме приветствия на странице должно быть поле ввода и кнопка. Блоки **input** и **button** мы берем готовые из библиотеки [bem-components](http://ru.bem.info/libs/bem-components/v2/). И вкладываем их в элемент **greeting**. 

```
{
  elem: 'greeting',
  content: 'Привет, %пользователь%!'
  }, 
  {
      block: 'input',
      mods: {theme: 'islands', size: 'm'},
      placeholder: 'Имя пользователя'
      }, 
      {
      block : 'button',
      text : 'Нажать',
      mods : { theme : 'islands', size : 'm' }
  }
}  
```

Если вы хотите внести какие-либо изменения в готовые блоки, это можно сделать на своем уровне переопределения. Подробнее об этом можно почитать, пройдя по ссылке http://ru.bem.info/tools/bem/bem-tools/levels/.

Все необходимые блоки добавлены. Так будет выглядеть полный код страницы `desktop.bundles/hello/hello.bemjson.js`:

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
	                           text : 'Нажать',
	                           mods : { theme : 'islands', size : 'm' }
                       }
                 ]
               }
           ]
       }
    ]
})
```

Чтобы проверить запустился ли файл, откройте браузер по адресу:

    http://localhost:8080/desktop.bundles/hello/hello.html

<a name="block_creation"></a>

## Создание блока

На уровне `desktop.blocks` создаем директорию блока **hello**. По умолчанию блок будет представлен набором файлов для всех технологий реализации (`CSS/STYL`, `JS`, `BEMHTML`).

    bem create -l desktop.blocks -b hello 

* `-l desktop.blocks` –– указывает на уровень переопределения `desktop.blocks`;  
* `-b hello` –– задает имя директории блока, в нашем случае `hello`.     

Другой способ –– создание блока вручную. Для этого на уровне `desktop.blocks` создадим папку **hello** и разместим в ней необходимые для проекта файлы технологий реализации блока.     

<a name="block_using_concrete_tech"></a>

### Создание блока с файлом определенной технологии

Блок можно создавать с файлом определенной технологии. Более подробную информацию об этом можно найти в командах [bem-tools](http://ru.bem.info/tools/bem/bem-tools/commands/#Создание-блока-в-определённой-технологии).

<a name="block_hello_modification"></a>

## Переопределение блока `hello`
Чтобы наш проект заработал должным образом, нужно переопределить файлы технологий реализации. 

<a name="JS_modification"></a>

### Реализация блока в технологии JS

В файле `desktop.blocks/hello/hello.js` описываем реакцию блока на выполнение действия, в нашем случае нажатие кнопки, с помощью специального свойства `onSetMode`. При нажатии кнопки в приветствие будет автоматически подставляться имя пользователя, введенное в поле **input**. 

```
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

```
Данный JavaScript заворачиваем в [модульную систему YModules](http://ru.bem.info/tools/bem/modules/):

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

### Реализация блока в технологии BEMHTML

Для того, чтобы наш JavaScript применился пишем BEMHTML-шаблон, в котором указываем, что наш блок **hello** имеет JavaScript-реализацию:

```
block hello, js: true
```

<a name="CSS_modification"></a>

### Реализация блока в технологии CSS

Для блока **hello** создаем свои CSS-правила. К примеру, такие:

```
.hello
    color: green
```
<a name="build"></a>

## Сборка проекта

При обновлении страницы сервер пересобирал только ту часть проекта, которую затронули наши изменения. Для сборки всего проекта целиком воспользуемся командой `ENB`:

    $ node_modules/.bin/enb make

# Результат

Обновляем страницу в браузере с адресом 

    http://localhost:8080/desktop.bundles/hello/hello.html

и видим страницу приветствия:

*скрин*

Заполняем поле ввода любым именем, например, Вася, нажимаем кнопку и получаем результат:

*скрин*
