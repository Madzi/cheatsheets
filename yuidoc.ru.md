# Форматирование YUI Doc коментариев javaScript

> Синтаксис **YUIDoc** должен быть знаком тем, что использовал **Javadoc**, **JSDoc**, **Doxygen**, или другие инструменты создания документации. Генератор YUIDoc полагается на теги, такие как `@param` или `@return` вложенные в блоки комментариев, начинающихся с `/**` и заканчивающихся `*/`. Он включается в себя небольшое количество тегов для документирования особенностей **YUI**, большинство тегов являются достаточно общими, чтобы использовать их с любым объектно-ориентированным языком.
>
>**ВАЖНО**: YUIDoc рассматривает только блоки коментариев, не рассматривая код. Это делает YUIDoc относительно простым и агностичным языком. Однако, это также означает, что необходимо всё объявлять явно. Фрагмент кода не будет отображаться как метод или класс, пока это не будет явно объявлено. Отсюда следует, что YUIDoc никогда не будет генерировать пустые заглушки для блоков кода (методов, свойств) без комментариев.

## Базовые требования

Каждый блок комментариев должна содержать один (и только один) **первичный тег** такой как `@class` или `@method`, и ноль или более вторичных тегов, таких как `@param`, `@type`, и `@extends`.

## Первичные теги

Каждый комментарий блока должен содержать один (и только один) из следующих тегов:

### module

```javascript
/**
Provides the base Widget class...

@module widget
**/
```

Тег указывает что блок описывает группу связанных классов. Например, модуль приложения YUI включает такие классы, как App.Base, Model и Router. Вы можете выборочно разбить модули на подмодули.

YUIDoc требует присутствия хотя бы одного модуля в дереве исходных текстов. Поскольку не всегда очевидно место в исходном JavaScript файле, где объявляется модуль, используется соглашение о том, что модуль объявляется в начале того файла, который содержит "базовый" или "первичный" класс.

Также смотри: `@class`, `@for`, `@main`, `@submodule`. 

### main

```javascript
/**
Provides more features for the widget module...

@module widget
@submodule widget-foo
@main widget
**/
```
Когда YUIDoc исследует папку модуля, чтобы собрать документацию по модулю и подмодулям, в ней может находиться несколько файлов. YUIDoc пытается определить в каком файле находится описание главного модуля. Если с этим возникают проблемы, вы можете добавить тег `@main` к описанию модуля/подмодуля, чтобы явно указать YUIDoc, какой блок рассматривать как описание главного модуля на странице API.

### class

```javascript
/**
A utility that brokers HTTP requests...

@class IO 
@constructor
**/
function IO (config) {
```

Тег указывает, что данный блок описывает класс. В JavaScript, обычно объект создаётся при помощи функции. Значение тега `@class` должно быть строкой, идентифицирующий функцию класса по отношению к родительскому объекту. Например, `@class` для **Y.DD.Drag** должен быть **Drag** (а `@namespace` должно быть **DD**).

YUIDoc ожидает, что методы, свойства, атрибуты и события принадлежат классу, поэтому в дереве исходников должен быть хотя бы один класс. Тег `@class` должен находится выше конструктора, методов, событий и т.д.. принадлежащих классу.

Тег `@class` может использоваться совместно с тегом `@constructor` или тегом `@static`.

Также смотри: `@constructor`, `@extends`, `@extensionfor`, `@for`, `@module`, `@namespace`, `@static`, `@uses`.

### method

```javascript
/**
Returns this model's attributes as...

@method toJSON
@return {Object} Copy of ...
**/
toJSON: function () {
```

Тег указывает, что данный блок описывает метод текущего класса. По умолчанию "текущим" является последний класс, который был обнаружен YUIDoc, но класс может быть явно указан с помощью тега `@for`.

Блок `@method` всегда должен предшествовать непосредственно определению метода. Описывая метод не забудьте указать параметры (`@param`) и возвращаемое значение (`@return`).

Также смотри : `@chainable`, `@class`, `@constructor`, `@for`, `@param`, `@return`, `@static`. 

### event

```javascript
/**
Fired when an error occurs...

@event error 
@param {String} msg A description of...
**/
var EVT_ERROR = 'error',
```

Тег указывает что блок описывает генерируемое классом событие, которое может представлять интерес. Как правило блок `@event` похож на блок `@method`, за исключением использования тегов `@return` и `@param`, используемых для описания свойств объекта получаемого слушателем событий.

В идеале, блок `@event` должен предшествовать коду, определяющему событие, даже если этот код - простое объявление строки. Если ваш блок `@event` не фиксирован, то вы должны определить его рядом с классом, которому принадлежит событие и сгруппировать с другими событиями, которые генерирует данный класс.

Также смотри: `@bubbles`, `@class`, `@for`, `@param`. 

### property

```javascript
/**
Template for this view's container...

@property containerTemplate 
@type String
@default "<div/>"
**/
containerTemplate: '<div/>',
```

Тег указывает что данный блок описывает свойство текущего класса.

Также как и в метод, блок `@property` следует всегда указывать непосредственно в месте определения свойства.
Необходимо не забывать указывать тип свойства `@type`, даже если переменная имеет тип `any` или `mixed`.

Также смотри: `@attribute`, `@default`, `@class`, `@for`, `@type`. 

### attribute

```javascript
/**
* Indicates whether this Widget
* has been rendered...
*
* @attribute rendered
* @readOnly
* @default false
* @type boolean
*/
ATTRS[RENDERED] = {
```

*[Специфично для YUI]* Тег указывает что блок описывает атрибут конфигурации. Атрибут является свойством конфигурационного объекта, который может быть передан при инициализации класса. Управление атрибутом осуществляется через YUI Attribute API. Атрибут - это "супер-свойство" с геттером, сеттером и другими полезными штуками, включая возможность автоматической генерации события на изменение.

Блок `@attribute` должен быть определён непосредственно перед атрибутом, который он описывает. Даже если атрибут находится внутри секции `ATTRS` объекта `Y.Base`.

Также смотри: `@property`, `@default`, `@class`, `@for`, `@type`, `@required`, `@optional`. 

## Вторичные теги

После выбора одного из первичных тегов, вы можете использовать один или несколько вторичных тегов:

### submodule

```javascript
/**
@module app
@submodule view
**/
```

Тег указывает что модуль на самом деле является подмодулем для другого модуля. Например, модуль `app-transitions` является подмодулем `app`.

В YUI подмодули позволяют уменьшить загружаемый код до необходимого минимума. Например, модуль `foo` имеет  подмодуль `foo-core` или `foo-base` с минимально-необходимым функционалом, и дополнительные модули `foo-*` расширяющие функционал. Используя YUI Loader можно загрузить только `foo-core`, или `foo-core` и другие модули, или целиком пакет.

Также смотри: `@module`.

### namespace

```javascript
/**
@namespace Test.Mock
**/
```

Тег определяет глобальное пространство имён. Тег `@namespace` позволяет исключить "корневой" или "глобальный" объект вашей библиотеки. Например, `Y.DD.Drag` имеет `@class Drag` и `@namespace DD` **не `Y.DD`**.

Поддержка `@namespace` позволяет вам ссылватьяс на классы в YUIDoc используя только имя класса.

Также смотри: `@class`. 

### extends

```javascript
/**
@class View
@constructor
@extends Base
**/
```

Тег указывает, что данный класс наследуется от другого, возможно с использованием `Y.extend()`, `Y.Base.create()` или похожим образом. YUIDoc сгенерирует документацию по методам, свойствам, событиям и атрибутам расширяемого класса и текущего класса. В схеме YUIDoc используемой по-умолчанию, пользователи могут выбирать какие из перегруженных элементов должны быть отображены.

Также смотри: `@class`, `@extensionfor`, `@uses`. 

### config

```javascript
/**
@config docScrollX 
@type Number 
**/
```

[Специфично для YUI] Псеводним для `@attribute`. В старых версиях YUI `@config` являлся несколько отличным от понятия атрибут, но сейчас эти два термина идентичны. В современной документации следует использовать тег `@attribute`.

### constructor

```javascript
/**
@class IO 
@constructor
**/
```

Тег указывает как данный класс инстанцируется (создаётся с помощью ключевого слова **new**). Тег `@class` должен идти в паре с тегом `@constructor` или тегом `@static`.

Также смотри: `@class`, `@static`.

### static

```javascript
/**
YUI user agent detection...

@class UA
@static
**/
```

Тег указывает что метод или класс являются статичными.

* Для метода указывается что метод может быть вызван без создания объекта класса `var node = Y.Node.create('<div/>');`
* Для класса указывается что его не нужно инстанцировать с помощью **new**. Вы можете вызывать все методы статически.

Тег `@class` должен идти в паре с тегом `@constructor` или тегом `@static`.

Также смотри: `@class`, `@constructor`, `@method`. 

### final

```javascript
/**
Identifies state changes 
originating from...

@property SRC_REPLACE
@type String
@static
@final
**/
```

Тег указывает что данное свойство или атрибут являются константой и не должны изменяться.

Также смотри: `@attribute`, `@property`, `@readOnly`, `@writeOnce`.

### readOnly

```javascript
/**
The current default button
as configured through...

@attribute defaultButton
@type Node
@default null
@readOnly
**/
```

*[Специфично для YUI]* Тег указывает что данный атрибут создаётся только для чтения и не может быть изменён вызовом метода `set()`. Атрибуты только для чтения всегда должны иметь значение по-умолчанию, которое следует описать используя тег `@default`.

Иногда используется со свойствами также как тег `@final`.

Также смотри: `@attribute`, `@default`, `@final`, `@property`, `@required`, `@optional`, `@writeOnce`.

### writeOnce

```javascript
/**
Diameter of the circular 
background object. Other 
objects scale accordingly.
Set this only before 
rendering.

@attribute diameter
@type {Number} number of px 
  in diameter
@default 100
@writeOnce
**/
```

*[Специфично для YUI]* Тег указывает, что атрибут конфигурации может быть записан однажды - как значение по-умолчанию, при создании объекта через конфигурацию или через первый вызов `set()`.

Также смотри: `@attribute`, `@default`, `@final`, `@required`, `@optional`, `@readOnly`.

### optional

```javascript
/**
An optional attribute, 
not required for proper
use.

@attribute extras
@type {Object} extra data
@optional
**/
```

*[Специфично для YUI]* Тег отображает что данный атрибут не требуется при создании класса.

Также смотри: `@attribute`, `@default`, `@final`, `@required`, `@readOnly`.

### required

```javascript
/**
A required attribute 
that is required for proper
use, module will likely fail
if this is not provided.

@attribute url
@type {String} url to fetch remote data from
@required
**/
```

*[Специфично для YUI]* Тег указывает что данный атрибут требуется указать при создании класса.

Также смотри: `@attribute`, `@default`, `@final`, `@optional`, `@readOnly`.

### *param

```javascript
/**
@param {String} name An 
  Attribute name or 
  object property path.
**/
```

```javascript
/**
@param {Object} [options] Data 
  to be mixed into the event 
  facade of the `change` 
  event(s) for these attributes.
  @param {Boolean} [options.silent]
    If `true`, no `change` event 
    will be fired.
**/
```

Тег определяет по порядку параметры для `@method`, для `@constructor` (обычно определяется внутри блока `@class`), или `@event`. Параметр ожет быть описан в следующем виде:

* `@param {type} name description`
* `@param name {type} description`

Элемент `{type}` не обязательный, но если вы используете его, то окружите его фигурными скобками. Таким образом YUIDoc поймёт, что это не имя. Имя может быть задано следующим образом:

* `[name] — optional parameter`
* `[name=foo] — default value is foo`
* `name* — placeholder for 1..n args`
* `[name]* — placeholder for 0..n args`

Также как и в примере вы можете вкладывать теги `@param`. Это позволяет документировать объекты, передаваемые в качестве параметров, которые сами имеют определённую структуру.

Также смотри: `@class`, `@constructor`, `@event`, `@method`, `@return`.

### return

```javascript
/**
@method generateClientId
@return {String} Unique clientId.
**/
```

Тег указывает на значение, возвращаемое методом. Тег`@return` имеет структуру `@return {type} description`. Элемент `{type}` необязательный.

Также смотри: `@method`, `@param`.

### for

```javascript
/**  
Some inner class 'foo'...  

@class foo  
@for OuterClass  
**/
```

```javascript
/**  
Some method 'bar' 
disconnected from 
its class 'FarawayClass'...

@method bar  
@for FarawayClass  
**/
```

Устанавливает область класса для YUIDoc.

Используя `@for OuterClass` в блоке `@class` создаёт вложенный класс. YUIDoc будет документировать методы и другие элементы следующие за этим блоком, как принадлежащие вложенному классу, но вложенный класс будет показан как принадлежащий внешнему (родительскому) классу.

Чтобы закрыть вложенный класс используйте `@for OuterClass` (снова) внутри последнего блока вложенного класса с тегом `@attribute`, `@event`, `@method` или `@property`. Это сбросит YUIDoc парсер, указав что следует использовать OuterClass в качестве основного класса для остальных элементов.

Если вы находитесь за пределами вложенного класса, используйте `@for FarawayClass` в боках `@attribute`, `@event`, `@method` или `@property` чтобы включить все элементы блока в нужный класс. Это полезно когда вы в модуле добавляете методы к прототипу класса, но само определение класса находится в другом файле.

Также смотри: `@class`, `@method`.

### type

```javascript
/**
@type String
**/
```

```javascript
/**
@type HTMLElement|Node|String 
**/
```

Тег указывает тип свойства или атрибута. Вы можете определить один тип или список типов разделённых вертикальной чертой `|`.

Также смотри: `@attribute`, `@default`, `@property`. 

### private

```javascript
/**
Reference to the internal JSONP 
instance used to make the queries.

@private
@property _jsonp
**/
```

Тег указывает что член класса не доступен извне. YUIDoc можно настроить так, что он не будет генерировать документацию для блоков с тегом `@private`. Все методы не помеченные `public` должны быть помечены как `private` или `protected`.

Также смотри: `@protected`.

### protected

```javascript
/**
Removes the `container` from 
the DOM and ...
 
@method _destroyContainer
@protected
**/
```

Тег указывает, что данный член класса может быть изменён в подклассе. Все методы и свойства не являющиеся публичными должны быть приватными или защищёнными.

Также смотри: `@private`.

### required

```javascript
/**
@module event-simulate
@requires event
**/
```

*Нераспространённый* тег указывает на одну или более зависимостей в объявлениях модуля. Может быть указан  один модуль или список из модулей разделённых запятыми.

Также смотри: `@extends`, `@extensionfor`, `@module`, `@submodule`.

### default

```javascript
/**
@default false
**/
```

Тег указывает значение свойства или атрибута по-умолчанию. Должен идти в паре с тегом `@type`.

Также смотри: `@attribute`, `@property`, `@type`. 

### *uses

```javascript
/**
@class Panel
@constructor
@extends Widget
@uses WidgetAutohide
@uses WidgetButtons
...
**/
```

Тег указывает что данный класс использует другие классы в качестве свойств, методов, добавленных членов, их прототипы, возможно с использованием `Y.mix()`, `Y.Base.mix()`, `Y.Base.create()`, или похожих методов. YUIDoc будет генерировать документацию для методов, свойств, событий и атрибутов совмещая с базовым классом, и ссылки на документацию к используемым классам. В схеме, используемой YUIDoc по-умолчанию, пользователи могут выбирать какие из элементов должны быть отображены.

Также смотри: `@class`, `@extends`, `@extensionfor`. 

### *example

```javascript
/**
@example
    model.set('foo', 'bar');
**/
```

Тег указывает что блок содержит демонстрационный код, который автоматически парсится и отображается YUIDoc с подсветкой кода. Ваш пример должен быть отделён от тега `@example` отступом. YUIDoc отображает все примеры с подсветкой синтаксиса с использованием тегов `<span>` и другими средствами разметки.

Один блок может включать несколько тегов `@example`.

### chainable

```javascript
/**
Renders this view ...

@method render
@chainable
**/
render: function () {
    return this;
},
```

Тег отображает, что данный метод возвращает **this** (текущий объект), что позволяет вам использовать другие методы этого объекта непосредственно в месте вызова.

Также смотри: `@method`. 

### deprecated

```javascript
/**
@property locale
@type String
@deprecated Use `config.lang` 
  instead.
**/
```

Тег указывает что модуль, класс или его член устарели и будут удалены в дальнейшем. Вы можете добавить к тегу строку с указаниями.

Также смотри: `@beta`, `@since`. 

### since

```javascript
/**
@since 3.4.0
**/
```

Тег указывает что модуль, класс или его член были добавлены в код начиная с определённой версии.

Также смотри: `@beta`, `@deprecated`. 

### async

```javascript
/**
@async
**/
```

*Нестандартный* тег, указывающий что метод является асинхронным и требуется callback.

### beta

```javascript
/**
@beta
**/
```

Тег указывает что метод, класс или его член ещё не реализованы полностью и возможно обратная несовместимость в дальнейшем.

Также смотри: `@deprecated`, `@since`.

### bubbles

```javascript
/**
Handles the mouseup DOM event...

@event drag:mouseup
@bubbles DDM
**/
```

Тег указывает конечный объект, приёмник сообщения. Этот тег будет полезен, если есть "управляющий" класс, перехватывающий и генерирующий собственные события.

Также смотри: `@event`. 

### extension, extensionfor, extension_for

```javascript
/**
@class PjaxBase
@extensionfor Router
**/
```

Этот тег указывает класс для которого описываемый является расширением, которое при желании может быть добавлено.

`@extensionfor` противоположен `@uses`. The key difference is that @uses means, "this class always has the 'used' class mixed into its prototype," while @extensionfor means, "this class can be mixed into the 'extensionfor' class, but it isn't baked in by default."

Также смотри: `@class`, `@extends`, `@uses`. 

## Распознаются но не отображаются

Следующие теги распознаются `DocParser`ом, но пока ещё не включены в отображение документации.

| Тег | Описание |
| ----- | --------------- |
| author | Автор информации о блоке |
| broadcast | Широковещательное событие для всех в определённых границах |
| *category | Категория в которую нужно поместить описание |

## Расширенное форматирование

YUIDoc поддерживает 3 типа форматирования в документе: HTML, [Markdown](http://daringfireball.net/projects/markdown/) и [Selleck](http://rgrove.github.com/selleck/).

| Формат | Поддержка |
| ------------ | ----------------- |
| HTML | Комментарии документа, содержащие стандартные HTML теги будут распознаны и YUIDoc отобразит их. |
| Markdown | Полная поддержка разметки [Markdown](http://daringfireball.net/projects/markdown/). |
| Selleck | Поддержка синтаксиса [Selleck](http://rgrove.github.com/selleck/) тоже поддерживается. |

### Markdown и подсветка кода
Внутри любого блока комментариев вы можете использовать Markdown или Selleck разметку. Если вы выделите фрагмент кода, то YUIDoc автоматически обернёт их в блок кода и подсветит его синтаксис.

```
/**
This is the __module__ description for the `YUIDoc` module.

    var options = {
        paths: [ './lib' ],
        outdir: './out'
    };

    var Y = require('yuidoc');
    var json = (new Y.YUIDoc(options)).run();

@class YUIDoc
@main yuidoc
*/
```

Будет отображено как

> This is the module description for the YUIDoc module.
>
>```javascript
>var options = {
>    paths: [ './lib' ],
>    outdir: './out'
>};
>
>var Y = require('yuidoc');
>var json = (new Y.YUIDoc(options)).run();
>```

### Перекрёстные ссылки модулей и классов

YUIDoc также определяет вспомогательные блоки (`blockHelper`) в стиле Handlebars, которые позволяют вам проще создавать перекрёстные ссылки для классов и модулей. Используйте следующий шаблон: `#crossLink "Class/item:type"`

```
#crossLink "Foo/bar:event"
#crossLink "Foo/bar:attribute"
#corssLink "Foo/bar:method" --default
```

Например, комментарий:

```
/**

This module also uses {{#crossLink "Foo"}}{{/crossLink}}, where Foo is a class name.
Also see {{#crossLink "myClass/Foo:method"}}{{/crossLink}}, where myClass is a class name and Foo is a method on that class.

This module uses {{#crossLinkModule "widget"}}{{/crossLinkModule}}, where widget is a module name

This module also uses {{#crossLink "Bar"}} an awesome class {{/crossLink}} named Bar.
*/
```

Будет преобразован в следующий HTML код:

```html
<p>
This module also uses <a href="../classes/Foo.html" class="crosslink">Foo</a>, 
where Foo is a class or module name.
</p>
<p>
Also see <a href="../classes/myClass.html#method_Foo">Foo</a>, where myClass 
is a class name and Foo is a method on that class.
</p>
<p>
This module uses <a href="../modules/widget.html">widget</a>, where widget is a module name
</p>
<p>
This module also uses <a href="../classes/Bar.html" class="crosslink">an awesome class</a>
named Bar.
</p>
```
