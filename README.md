# Interstyler

Ответы на вопросы с собеседований по HTML и CSS.  
Простые ответы на сложные (и не очень) вопросы.  
Да-да, это повторение mdn, спецификаций, и статьи, что вы когда-то читали, ничего нового.

## Оглавление
- [Виды HTML-элементов](#виды-html-элементов)
- [Formatting Context](#formatting-context-контекст-форматирования)
- [Stacking Context](#stacking-context-контекст-наложения)
- [Специфичность CSS селекторов](#специфичность-css-селекторов)
- [Схлопывание внешних отступов](#схлопывание-внешних-отступов)
- [Float](#float)
- [Центрирование элемента на экране](#центрирование-элемента-на-экране)
- [CSS Методологии](#css-методологии)
- [Critical CSS](#critical-css)
- [Viewport](#viewport)
- [Препроцессоры](#препроцессоры)
- Анимации / Трансформации
- Техники изоляции стилей
- Способы подключения SVG
- [Оптимизация скорости загрузки](#Оптимизация-скорости-загрузки)
- RWD
- A11y (Доступность)
- Регрессионное тестирование
- Табличная / email-верстка
- Микроформаты и OpenGraph
- Retina-support
- Системы сборки

## Вопросы и ответы

### Виды HTML-элементов

Все HTML-теги классифицируются по их логической роли. Об этом пишет [спецификация](https://html.spec.whatwg.org/multipage/dom.html#content-models "ваш любимый whatwg"). Каждый элемент в html попадает в ноль или более категорий, которые объединяют элементы с похожими характеристиками. В спецификации используются следующие широкие категории:
- Metadata content (контент метаданных)
- Flow content (потоковый контент)
- Sectioning content (структурный контент)
- Heading content (заголовочный контент)
- Phrasing content (фразовый контент)
- Embedded content (встроенный контент)
- Interactive content (интерактивный контент)

Существует широко распространённое заблуждение, что в HTML существуют «блочные», «строчные», и даже «строчно-блочные» элементы. Однако это деление – историческая ошибка, о чём подробно можно прочитать [здесь](https://css-live.ru/articles/blochnyx-i-strochnyx-elementov-v-html-bolshe-net.html).  
Визуальная структура оформления, в которой есть «блочные», «строчные» и «внутристрочные» (инлайновые) боксы, относится _исключительно_ к CSS.

### Formatting Context (Контекст Форматирования)

_Я не нашёл чёткого определения контекста форматирования, поэтому если вы можете сформулировать лучше – жду ваших pull requestов_

__Контекст форматирования__ – область, которая была определена для разметки контента определенным образом. Каждый контекст форматирования имеет определенные правила о том, как в нём ведет себя разметка.

Существует несколько контекстов форматирования, основные:

- Инлайновый (Inline)

    В инлайновом контексте форматирования _боксы (boxes)_ располагаются горизонтально, один за другим, начиная с верхней части блока, в котором они содержатся. Горизонтальные `margin`'ы, `border`'ы и `padding`'и соблюдаются между этими боксами.  
    Прямоугольная область, содержащая блоки, образующие линию, называется _линейным боксом (line box)_.

    Ширина линейного бокса определяется содержащим блоком и наличием `float`'ов.

- Блочный (Block)

    Это регион страницы, в котором блоки размещаются в привычном для блоков порядке, и в котором плавающие элементы взаимодействуют с другими элементами.

    Блочный контекст форматирования может быть создан чем-либо из [этого списка](https://developer.mozilla.org/ru/docs/Web/Guide/CSS/Block_formatting_context#Summary "mdnчик родной").

    Правила размещения плавающих элементов и сброса обтекания применяются только к элементам внутри одного и того же блочного контекста форматирования. Плавающие элементы не влияют на размещение содержимого внутри других блочных контекстов форматирования, и отмена обтекания распространяется только на плавающие элементы из _того же самого_ контекста форматирования.  
    
    Схлопывание внешних отступов тоже происходит только между блоками из _одного и того же_ блочного контекста форматирования.

- Табличный (Table)

    С точки зрения модели визуального форматирования таблица может вести себя как элемент уровня блока (для элемента `display: table`) или элемента уровня строки (для элемента `display: inline-table`).

    В обоих случаях таблица генерирует контейнерный блок основного бокса, называемый __боксом-оберткой таблицы (table wrapper box)__, который содержит сам бокс таблицы и любые боксы заголовков (caption boxes).  
    __Бокс таблицы (table box)__ представляет собой бокс уровня блока, который содержит внутренние боксы таблицы.  
    __Боксы заголовков__ являются основными боксами уровня блока, которые сохраняют свои собственные области содержимого, отступов, полей и границ и отображаются как обычные блочные боксы внутри _бокса-обёртки таблицы_.  
    Размещаются ли _боксы заголовков_ до или после бокса таблицы, определяется свойством `caption-side`.

    __Бокс-обёртка таблицы__ имеет уровень блока для `display: table` и уровень строки для `display: inline-table`.  
    _Бокс-обёртка таблицы_ устанавливает контекст форматирования блока, а __бокс таблицы__ устанавливает контекст форматирования _таблицы_. Бокс таблицы (не бокс-обёртка таблицы) используется при выполнении вертикального выравнивания базовой линии для `inline-table`. Ширина бокс-обёртки таблицы – это border-edge ширина бокс таблицы в ней.  
    Проценты, указанные в `width` и `height` в таблице относятся к блоку, содержащему бокс-обёртку таблицы, а не саму бокс-обёртку таблицы.

    [Узнать больше на w3c](https://www.w3.org/TR/CSS22/tables.html#model)

- Флексовый (Flex)

    Flex-контайнер устанавливает новый __флексовый контекст форматирования (flex formatting context)__ для своего содержимого. Это то же самое, что и установление контекста форматирования блока, за исключением того, что вместо _box layout_ используется _flex layout_. Например, `float` не влияют на flex layout, а `margin`'ы flex контейнера _не схлопываются_ с `margin`'ами его содержимого. Flex-контейнеры образуют _содержащий блок (containing block)_ точно так же, как блочные контейнеры. Свойство `overflow` применяется к flex-контейнерам.

    [Узнать больше на w3c](https://www.w3.org/TR/css-flexbox-1/#flex-formatting-context)

- Сеточный (Grid)

    Grid-контейнер устанавливает новый __контекст форматирования сетки (grid formatting context)__ для его содержимого. Это то же самое, что и установление контекста форматирования блока, за исключением того, что вместо _box layout_ используется _grid layout_. Содержимое grid-контейнера выкладывается в сетку, причем _линии сетки (grid lines)_ образуют границы каждого содержащего блока элементов сетки (of each grid item's containing block). Свойство `overflow` применяется к контейнерам сетки.

    [Узнать больше на w3c](https://www.w3.org/TR/css-grid-1/#grid-formatting-context)

### Stacking Context (Контекст Наложения)

__Контекст наложения__ – это концепция трехмерного расположения HTML элементов вдоль оси Z.  
Если элемент располагается в самом низу одного контекста наложения, то __никаким__ образом __не получится__ отобразить его над другим элементом в соседнем контексте наложения, располагающимся выше по иерархии, даже с установленным `z-index` равным миллиону.

Корневой элемент (`<html>`) всегда содержит __корневой контекст наложения__. Любой элемент на странице, не участвующий в локальном контексте наложения (сформированном любым из последующих вариантов), участвует в корневом контексте наложения.

Новый (локальный) контекст наложения формируется следующими элементами:
- `position: fixed`,
- `position: absolute` и `position: relative` с `z-index`, у которого значение отличается от `auto`,
- flex-элемент с `z-index` отличным от `auto`, чей родительский элемент имеет свойство `display: flex` или `inline-flex`,
- элементы с `opacity` меньше чем 1,
- элементы с `transform` отличным от `none`,
- элементы с `mix-blend-mode` отличным от `normal`,
- элементы с `filter` отличным от `none`,
- элементы с `isolation` установленным в `isolate`,
- если мы указываем элементу атрибут `will-change` (при этом не обязательно присваивать ему значение),
- элементы с `-webkit-overflow-scrolling` установленным в `touch`.

### Специфичность CSS селекторов

__Специфичность__ – способ определения css-свойств, которые будут применяться к элементу.
Она представляет собой некоторую условную величину, рассчитываемую следующим образом:
1. За каждый идентификатор (`#id`) начисляется 100
2. За каждый класс (`.class`) и псевдокласс (например `:hover`) начисляется 10
3. За каждый селектор тега (`tagname`) и псевдоэлемент (например `::before`) начисляется 1.

Наиболее специфичный селектор применится к элементу.

Следует отметить, что универсальный селектор (`*`),  комбинаторы (`+`, `>`, `~`, '` `')  и отрицающий псевдокласс (`:not()`) __не влияют__ на специфичность. (Однако селекторы, объявленные внутри `:not()`, влияют).  
Стили, обьявленные в элементе (например, `style="font-weight:bold"`), всегда __переопределяют__ любые правила из внешних файлов стилей, их специфичность можно считать наивысшей.  
Когда при объявлении стиля используется модификатор `!important`, это объявление получает __наивысший__ приоритет среди всех прочих объявлений.  

Хотя технически модификатор `!important` не имеет со специфичностью ничего общего, он непосредственно на неё влияет. Поскольку `!important` усложняет отладку, нарушая естественное каскадирование ваших стилей, он не приветствуется и __следует избегать его использования__.  
Если к элементу применимы два взаимоисключающих стиля с модификатором `!important`, то применен будет стиль с большей специфичностью.

### Схлопывание внешних отступов

Свойства блоков `margin-top` и `margin-bottom` иногда комбинируются (схлопываются) в единый отступ, размер которого равен __наибольшему__ из комбинируемых отступов. Это поведение известно, как __схлопывание внешних отступов__.

Схлопывание внешних отступов происходит в трёх случаях:

1. Соседние элементы (siblings)

    Схлопываются отступы соседних элементов  (за исключением случая, когда последнему элементу применено свойство `clear`).

2. Родительский и первый/последний дочерние элементы

    Внешние отступы схлопываются если:

    - отстутствуют границы (`border`), внутренние отступы (`padding`), строчное содержимое или не используется свойство `clear` для отделения `margin-top` блока от `margin-top` блока, являющегося его первым дочерним элементом 
    - отстутствуют границы, внутренние отступы, строчное содержимое, `height`, `min-height` или `max-height` для отделения `margin-bottom` блока от `margin-bottom` его последнего дочернего элемента.  
Схлопнутые отступы заканчиваются за пределами родительского элемента.

3. Пустые блоки

    Если отстутствуют границы, внутренние отступы, строчное содержимое, `height` или `min-height` для отделения `margin-top` этого блока от его `margin-bottom`, то верхние и нижние внешние отступы этого блока схлопываются.

#### Отмена схлопывания margin

Схлопывание `margin` не срабатывает:

- для элементов с абсолютным позиционированием, т. е. таких, у которых `position` установлено как `absolute` или `fixed`;
- для обтекаемых элементов (для них свойство `float` задано как `left` или `right`);
- для элементов со свойством `display` заданым как `inline` или `inline-block`;
- для элементов, у родителя которых свойство `display` задано как `flex` или `inline-flex`;
- для элемента `<html>`.

Схлопывание не действует на дочерние элементы:

- если у родителя значение `overflow` задано как `auto`, `hidden` или `scroll`;
- если у родителя на стороне схлопывания задано свойство `padding`, со значением больше нуля;
- если у родителя на стороне схлопывания задано свойство `border`, со значением больше нуля.

### Float

Свойство `float` прижимает элемент к левому или правому краю _родителя_, а близлежащий текст обходит элемент с других сторон. Такое поведение текста напоминает поток воды, обтекающий камень, поэтому элементы с `float` называются _плавающими_, _обтекаемыми_ или _поплавками_.

У свойства `float` есть три значения: `left`, `right` и `none`.

Обтекаемые элементы _не участвуют в потоке документа_, когда все элементы выстраиваются последовательно друг за другом. Из-за этого обтекаемые элементы _не оказывают_ влияние на высоту блока, внутри которого они располагаются. 

Элемент с `float` со значением `left` или `right` ведёт себя таким образом, будто ему добавили свойство `display` со значением `block`. 

Элемент __по ширине сжимается__ до размеров содержимого, если для элемента явно не установлена ширина `width`.

#### Очистка Float

Для отмены обтекания используется несколько вариантов:

1. 100% ширина элементов

    Если обтекаемые элементы будет занимать всю доступную ширину, то остальные элементы, следующие за ними, будут начинаться с новой строки. Для этого суммарная ширина элементов должна равняться __100%__.
    
2. Использоание `overflow`

    Свойство `overflow` управляет отображением содержимого блока, если оно целиком не помещается и выходит за область заданных размеров. Использование `overflow` со значением `auto`, `scroll` или `hidden` отменяет действие `float`.  
    Но иногда возникают ситуации, когда применение этого метода недопустимо. Это происходит в тех случаях, когда элемент выходит за пределы блока, при этом он будет «обрезан».

3. Использование `clear`

    Для отмены действия `float` от вышестоящего элемента применяется свойство `clear` со следующими значениями:

    - `left` — отменяет действие значения `left` у `float`;
    - `right` — отменяет действие значения `right` у `float`;
    - `both` — одновременно отменяет действие значений `left` и right у `float`.
    
    Чтобы отменить действие обтекания, свойство `clear` надо добавлять к элементу, идущему _после_ элемента с `float`. Обычно вводят универсальный класс, к примеру, `clearfix` и вставляют пустой `<div>` с этим классом.
    
 4. Псевдоэлемент `::after`
 
     Частое включение пустого элемента `<div>` со свойством `clear` захламляет код, поэтому используется псевдоэлемент `::after` со следующими стилями:
     ```css
     .clearfix::after { 
       content: ""; /* Генерируем пустой элемент */
       clear: both; /* Отменяем обтекание*/
       display: block; /* Блочный элемент */
     }
     ```
    
    Для использования данного метода, необходимо добавить класс `clearfix` внутрь блока, содержащего плавающие элементы.
    
### Центрирование элемента на экране

Речь пойдёт о центрировании именно _на экране_. Существует несколько основных способов:

1. При помощи свойства `position`

    ```css
    .el{
      position: fixed; /* or absolute */
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }
    ```
    
    С помощью свойств `top` и `left` элемент сместится таким образом, что по середине экрана окажется верхний левый угол элемента. Свойство `transform: translate` необходимо для того, чтобы это исправить, и сместить элемент обратно на середину своей высоты и ширины.  
    В случае известной ширины и высоты, можно использовать следующие варианты кода:
    ```css
    .el{
      height: 200px;
      width: 200px;
      position: fixed; /* or absolute */
      left: calc(50% - 100px); /* 100px*2 - width элемента */
      top: calc(50% - 100px); /* 100px*2 - height элемента */
    }
    ```
    
    ```css
    .el{
      height: 200px;
      width: 200px;
      position: fixed; /* or absolute */
      left: 50%;
      top: 50%;
      margin: -100px 0 0 -100px;
    }
    ```
    
2. При помощи свойства `display: flex`
    
    Данное свойство применяется к __родителю__ центрируемого элемента. Далее имеются два варианта.  
    Либо __родителю__ также добавить свойства `justify-content: center` и `align-items: center`,  
    либо __элементу__ добавить свойство `margin: auto`.  
    Первый вариант является более предпочтительным.

### CSS Методологии

__CSS методологии__ необходимы для организации стилей. Когда проект становится большим, CSS напоминает макаронного монстра, которым необходимо управлять. 

Существует множество методологий:

1. [БЭМ](https://ru.bem.info "документация") – Блок, Элемент, Модификатор
   
    Одна из самых популярный на сегодняшний день. Автор – Яндекс.

    - __Блоки__ могут использоваться в нескольких местах сайта;
    - __Элементы__ являются частью блока и не имеют функционального смысла вне блока;
    - __Модификаторы__ представляют собой свойства блока или элемента, которые меняют его внешний вид или поведение.
   
    ```html
    <div class="block">
        <p class="block__element">Элемент</p>
        <p class="block__element block__element_modifier">Элемент с модификатором</p>
    </div>
    ```
    ```css
    .block {}
    .block__element {}
    .block__element_modifier {}
    ```
2. [Atomic CSS](https://acss.io "документация") – Атомарный CSS

    При использовании такого подхода для каждого повторно используемого свойства должен быть сформирован отдельный класс. Пример: стиль `margin-top: 10px` предполагает создание класса `Mt(10)`, стиль `width: 200px` создание класса `W(200px)`. 

    Такой стиль позволяет минимизировать объем CSS-кода за счет повторного использования деклараций, а также сравнительно легко вводить изменения в модули.

3. [SMACSS](http://smacss.com "документация") – Scalable and Modular Architecture for CSS

    __Масштабируемая и модульная архитектура для CSS__ способствует разбиению стилей на пять основных категорий, в том числе:

    - __Основа__ – включает в себя стили базовых элементов и охватывает общие значения по умолчанию
    - __Структура__ – устанавливает размеры и стили сетки разных элементов, определяет их компоновку
    - __Модуль__ – это более конкретные стили, ориентированные на отдельные части страницы, такие как навигация, или её основные особенности
    - __Состояние__ – используются для дополнения или переопределения других стилей в том случае, если модуль включает альтернативное состояние, к примеру, активную вкладку
    - __Тема__ – может быть добавлена, когда включены стили, основанные на оформлении или внешнем виде разных модулей
    
    ```html
    <div class="alert is-error">
      <p>...</p>
    </div>
    ```
    ```css
    .alert {...}
    .alert.is-error {...}
    .alert p {...}
    .alert.is-error p {…}
    ```
    
    В приведённом выше примере класс `alert` попадает в категорию _модуль_, в то время как класс `is-error` попадает в категорию _состояние_. Стили от каждой из этих категорий затем наследуются по необходимости.
    
4. [OOCSS](http://oocss.org "документация") – Объектно-ориентированный CSS

    __Объектно-ориентированный CSS__ определяет два принципа, которые помогут создавать масштабируемые веб-сайты со строгой архитектурой и рациональным количеством кода. Эти два принципа включают в себя:

    - разделение структуры и оформления;
    - разделение содержимого и контейнера.
    
    Сам подход _OOCSS_ предлагает не конкретные правила, а абстрактные рекомендации, поэтому метод достаточно сложен для применения на практике.
    
### Critical CSS

__Критический CSS__ – это минимальный набор блокирующего CSS, требуемого для рендеринга первого экрана с контентом пользователю.  
Использование этого набора стилей позволяет улучшить производительность сайта, путём ускорения рендеринга.

Для начала работы с _критическим CSS_, надо изменить подход к обработке CSS: разбить его на 2 файла.  
В первом файле будет набор необходимого CSS, который впоследствии станет _инлайновым_.  
А во втором весь оставшийся некритичный CSS, который будет асинхронно загружаться на страницу.

```html
<head>
  <style> /* inlined critical CSS */ </style>
  <script> loadCSS('non-critical.css'); </script>
</head>
```

Существуют инструменты, например [Critical](https://github.com/addyosmani/critical) от Эдди Османи, который позволяет автоматизировать этот процесс.

### Viewport

__Viewport__ – видимая пользователю область веб-страницы, то, что может увидеть пользователь, _не прибегая к прокрутке_.

__Meta-тег viewport__ сообщает браузеру о том, как именно обрабатывать размеры страницы, и изменять её масштаб. Этот тег необходимо добавлять в секцию `HEAD`.

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```
Атрибуты:

`width` – ширина области просмотра.

Значение атрибута является целое неотрицательно число от `200` до `10000` пикселей или константа `device-width`, которая задаёт ширину страницы в соответствии с размером экрана.

`initial-scale` – начальный масштаб страницы.

Значением атрибута является число с точкой от `0.1` до `1.0`. Значение `1.0` определяет _масштаб 1:1_, т.е. __«не масштабировать»__.

Про остальные атрибуты читайте на [MDN](https://developer.mozilla.org/ru/docs/Словарь/Viewport)

Кроме того, в CSS существуют единицы измерения, основанные на значении _viewport_:

1. `vmin` – viewport minimum – основаны на __меньшей__ стороне вьюпорта. Если _высота_ вьюпорта __меньше__ _ширины_, значение `1vmin` будет равно `1%` от _высоты_. Точно так же если _ширина_ __меньше__ _высоты_, то `1vmin` будет равен `1%` от _ширины_ вьюпорта.
2. `vmax` – viewport maximum – основаны на __большей__ стороне вьюпорта. Если _высота_ вьюпорта __больше__ _ширины_, то значение `1vmax` будет равно `1%` от _высоты_ вьюпорта. Если _ширина_ вьюпорта __больше__ _высоты_, то `1vmax` будет равен `1%` от ширины.
3. `vh` – viewport height – основаны на _высоте_ вьюпорта. Значение `1vh` равно `1%` высоты вьюпорта.
4. `vw` – viewport width – основаны на _ширине_ вьюпорта. Значение `1vw` равно `1%` ширины вьюпорта.

Обо всех _концептах viewport_ можно прочитать на [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Viewport_concepts) (на английском языке).

### Препроцессоры
__Препроцессоры__ – это программы, которые берут один тип данных и преобразуют его в другой тип данных.  
В случае CSS это надстройка, расширяющая стандартные возможности, которая позволяет ускорить разработку.

На данный момент существует несколько наиболее популярных препроцессоров:
- [Sass](#sass)
- [Less](#Less)
- [PostCSS](#PostCSS)

#### Sass

__Директива extend__ (`@extend`) сообщает Sass, что один селектор должен наследовать стили другого. Например:
```css
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

компилируется в

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd;
}

.seriousError {
  border-width: 3px;
}
```

__Миксины (примеси)__ позволяют определить стили, которые могут быть использованы повторно в любом месте документа.  
_Миксины_ также могут содержать целые CSS правила или что-либо другое.  
Они даже могут принимать _аргументы_, что позволяет создавать большое разнообразие стилей при помощи небольшого количества миксинов.

Миксины объявляются директивой `@mixin`. После неё должно стоять __имя миксина__ и, опционально, его __параметры__, и блок, содержащий __тело__ миксина. Например, можно определить миксин `large-text` следующим образом:

```css
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
```

Миксины вызываются в документ директивой `@include`. Она принимает имя миксина и, опционально, передаваемые в него аргументы, включает стили, определённые этим миксином, в текущее правило. Например:

```css
.page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}
```

Реальная разница между `@extend` и `@mixin` заключается в возможности передавать в миксин __аргументы__, выводя стили на основе значений этих аргументов, в то время как `@extend` лишь дополняет один блок стилей другим. 

Пример миксина с параметрами:
В аргументах можно определить значения по умолчанию, если `@mixin` вызывается без аргументов.

```css
@mixin awesome($w: 100%, $h: 100%) {
 width: $w;
 height: $h;
}
body {
 @include awesome(960px);
}
p {
 @include awesome;
}
```

Результат после компиляции будет схожим, однако ширина будет отличаться.

```css
body {
 width: 960px;
 height: 100%;
}
p {
 width: 100%;
 height: 100%;
}
```

#### Less

Данный препроцессор схож с __Sass__, вернее с SCSS – его версией, схожей с нативным CSS. Однако имеются и некоторые отличия.  
Переменные в __Less__ начинаются с символа `@`. Например:

```css
@primary:  green;
@secondary: blue;

.section {
  @color: primary;

  .element {
    color: @@color;
  }
}
```

Пример выше скомпилируется в:

```css
.section .element {
  color: green;
}
```

__Миксины__ задаются как обычные блоки свойств, которые затем вызываются в других блоках. Например:

```
.a, #b {
  color: red;
}
.mixin-class {
  .a();
}
.mixin-id {
  #b();
}
```

В данном примере `.a` и `#b` являются миксинами. При этом при вызове скобки можно опустить, хотя нежелательно, и такая возможность скоро исчезнет. Например:

```
.a(); 
.a;  // currently works, but deprecated; don't use
```

Аналогично Sass, скобки используются для передачи __параметров__, в этом случае они больше похожи на __функции__.

```
.average(@x, @y) {
  @result: ((@x + @y) / 2);
}

div {
  // call a mixin and look up its "@result" value
  padding: .average(16px, 50px)[@result];
}
```

#### PostCSS

__PostCSS__ значительно отличается от приведённых выше _препроцессоров_, его даже называют _постпроцессором_. Однако сами создатели позиционируют свой продукт иначе:

__PostCSS__ – инструмент для _преобразования_ CSS при помощи JavaScript.

Стоит понимать, что сам по себе _PostCSS_ это __набор плагинов__, из которых необходимо выбрать нужные.

__Autoprefixer__ – один из наиболее популярных плагинов, который добавялет вендорные префиксы, пользуясь правилом "Can I Use".

__Postcss-cssnext__ – позволяет использовать функции будущего CSS уже сегодня (включая __autoprefixer__).

__Cssnano__ – представляет собой модульный _CSS Minifier_.

О других популярных плагинах можно прочитать на [официальном сайте](https://post-css.ru/install "postcss").


### Оптимизация скорости загрузки

Выводы из статьи [Гонка за скоростью: ускоряем загрузку сайтов в эпоху смартфонов](https://github.com/devSchacht/articles/tree/master/articles/melikhov-speeding-up-loading-in-the-era-of-smartphones)

- Используйте preload и font-display:swap
- Используйте HTTP/2
- Попробуйте Server Push и Service Workers
- Уменьшайте ваши бандлы
- Тестируйте на реальных устройствах в реальной сети

