# Interstyler

Ответы на вопросы с собеседований по HTML и CSS.
Простые ответы на сложные (и не очень) вопросы.  
Да-да, это повторение mdn, спецификаций, и статьи, что вы когда-то читали, ничего нового.

## Оглавление
- [Виды HTML-элементов](#виды-html-элементов)
- [Formatting Context](№formatting-context-контекст-форматирования)
- [Stacking Context](#stacking-context-контекст-наложения)
- [Специфичность CSS селекторов](#специфичность-css-селекторов)
- A11y (Доступность)
- CSS Методологии
- Оптимизацию скорости загрузки
- RWD
- Viewport
- Пропроцессоры Less и Sass, разница между ними, разница между extend/mixing/entend-only в Sass.
- Регрессионное тестирование
- Техники изоляции стилей
- Табличная / email-верстка
- Микроформаты и OpenGraph
- Retina-support
- Системы сборки
- PostCSS
- Анимации / Трансформации
- Что пошагово происходит с блоком, который получил float: left
- Виды HTML элементов
- Схлопывание margin'ов

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
Визуальная структура оформления, в которой есть «блочные», «строчные» и «внутристрочные» (инлайновые) боксы, относится исключительно к CSS.

### Formatting Context (Контекст Форматирования)

_Я не нашёл чёткого определения контекста форматирования, поэтому если вы можете сформулировать лучше – жду ваших pull requestов_

Контекст форматирования – область, которая была определена для разметки контента определенным образом. Каждый контекст форматирования имеет определенные правила о том, как в нём ведет себя разметка.

Существует несколько контекстов форматирования, основные:

- Инлайновый (Inline)
- Блочный (Block)
- Табличный (Table)
- Флексбоксовый (FlexBox)
- Гридовый (Grid)

### Stacking Context (Контекст Наложения)

Контекст наложения – это концепция трехмерного расположения HTML элементов вдоль оси Z.  
Если элемент располагается в самом низу одного контекста наложения, то **никаким** образом **не получится** отобразить его над другим элементом в соседнем контексте наложения, располагающимся выше по иерархии, даже с установленным `z-index` равным миллиону.

Корневой элемент (HTML) всегда содержит корневой контекст наложения. Любой элемент на странице, не участвующий в локальном контексте наложения (сформированном любым из последующих вариантов), участвует в корневом контексте наложения.

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

Специфичность – способ определения css-свойств, которые будут применяться к элементу.
Она представляет собой некоторую условную величину, рассчитываемую следующим образом:
1. За каждый идентификатор (`#id`) начисляется 100
2. За каждый класс (`.class`) и псевдокласс (например `:hover`) начисляется 10
3. За каждый селектор тега (`tagname`) и псевдоэлемент (например `::before`) начисляется 1.

Наиболее специфичный селектор применится к элементу.

Следует отметить, что универсальный селектор (`*`),  комбинаторы (`+`, `>`, `~`, '` `')  и отрицающий псевдокласс (`:not()`) не влияют на специфичность. (Однако селекторы, объявленные внутри `:not()`, влияют). Стили, обьявленные в элементе (например, `style="font-weight:bold"`), всегда переопределяют любые правила из внешних файлов стилей, их специфичность можно считать наивысшей.  
Когда при объявлении стиля используется модификатор `!important`, это объявление получает наивысший приоритет среди всех прочих объявлений. Хотя технически модификатор `!important` не имеет со специфичностью ничего общего, он непосредственно на неё влияет. Поскольку `!important` усложняет отладку, нарушая естественное каскадирование ваших стилей, он не приветствуется и следует избегать его использования. Если к элементу применимы два взаимоисключающих стиля с модификатором `!important`, то применен будет стиль с большей специфичностью.
