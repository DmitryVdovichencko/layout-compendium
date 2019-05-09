# Разбираемся с системой координат и трансформациями SVG. Часть 1 `viewport` , `viewBox`, `preserveAspectRatio`.
Оригинал: [Sara Soueidan "Understanding SVG Coordinate Systems and Transformations"](https://www.sarasoueidan.com/blog/svg-coordinate-systems/)

SVG элементы не регламентированы моделью CSS Box как элементы HTML. Это делает позиционирование и трансформацию этих элементов довольно непростыми и, как может показаться на первый взгляд, менее интуитивными. В любом случае, однажды разобравшись с тем, как работают система координат и трансформации SVG, манипуляции с SVG графикой станут намного легче и обретут больше смысла. В этой статье мы разберем три самых важных аттрибута SVG которые оказывают влияние на систему координат: `viewport` , `viewBox`, `preserveAspectRatio`.

Это первая часть серии из трех статей о системе координат и трансформациях в SVG.


[Разбираемся с системой координат и трансформациями SVG. Часть 1 `viewport` , `viewBox`, `preserveAspectRatio`](https://www.sarasoueidan.com/blog/svg-coordinate-systems/)

[Разбираемся с системой координат и трансформациями SVG. Часть 2 – Аттрибут `transform`](http://sarasoueidan.com/blog/svg-transformations)

[Разбираемся с системой координат и трансформациями SVG. Часть 3 – Определяем новый `viewport`](http://sarasoueidan.com/blog/nesting-svgs)
   
Для визуализации основных принципов и объяснений в статье далее, автором было создано интерактивное демо, позволящее поиграться со значениями аттрибутов `viewBox` и `preserveAspectRatio`.

[Здесь можно посмотреть интерактивное демо](https://www.sarasoueidan.com/demos/interactive-svg-coordinate-system/)

Демо это такая вишенка на торте, так что советую вернуться к чтению статьи, если вы посмотрите демо перед этим.

## Оболочка SVG

Оболочка это пространство или область, на которой отрисовано содержимое SVG. В принципе, эта оболочка бесконечна в обоих измерениях. Поэтому, SVG может быть любых размеров. Но, все же, SVG графика будет отрисована относительно определенной области известной как `viewport`. Области SVG, которые лежат за пределами `viewport` будут обрезаны и не отобразятся.

### Viewport

`Viewport` - это отображаемая область, где будет показана SVG графика. Вы можете думать о `viewport` как об окошке, через которое можно увидеть какой нибудь пейзаж. И пейзаж через это окно может быть виден полностью или частично.

`Viewport`  SVG похож на `viewport` браузера через который вы смотрите странички в интернете. Веб страница может быть любого размера; она может быть шире чем ширина `viewport` и, в большинстве случаев, длинее чем длина `viewport`. В любом случае в определенный момент через `viewport` браузера видны лишь части страницы.

Так или иначе, вся оболочка SVG будет отображена или только ее часть зависит от размера этой оболочки и значения аттрибута `preserveAspectRatio`. Не нужно сейчас об этом беспокоиться; позже мы поговорим подробнее об этом аттрибуте.

Вы определяете размер `viewport`, используя аттрибуты `width` и `height` внешнего `<svg>` элемента.

```html

<!-- viewport будет 800px на 600px -->
<svg width="800" height="600">
    <!-- содержимое SVG отрисованное внутри SVG оболочки -->
</svg>
```

В SVG, значения могут быть установлены с использованием единиц измерения или без них. Значения без единиц измерения говорят о том, что пространство будет определено с помощью пользовательских единиц измерения. Если значение, определено с пользовательскими единицами, значит предполагается что значение будет равно тому же числу в `px`. Это означает, что в примере выше `viewport` будет отрисован с размерами `800px` на `600px`.

Вы также можете определять значения с использованием единиц измерения. Поддерживаются следующие единицы измерения: `em, ex, px, pt, pc, cm, mm, in, %`

Как только будут установлены ширина и высота внешнего SVG элемента, браузер создаст исходную систему координат `viewport` и исходную систему координат пользователя.

### Исходная система координат

Исходная система координат `viewport` - это система координат созданная на `viewport` с началом в левом верхнем углу в точке `(0,0)`, позитивное направление оси X - вправо, позитивное направление оси Y - вниз, и одно деление в исходной системе координат равно одному `px` `viewport`. Эта система координат похожа на систему координат для HTML элементов в соответствие с блочной моделью CSS.

Пользовательская исходная система координат - это система координат созданная на основе оболочки SVG. Эта система координат изначально идентична системе координат `viewport` с началом в левом верхнем углу в точке `(0,0)`, позитивное направление оси X - вправо, позитивное направление оси Y - вниз. Используя аттрибут `viewBox` исходная пользовательская система координат - также известная как выбранная система координат или используемая пользовательская область - может быть изменена таким образом, что она больше не будет идентична системе координат `viewport`. Мы поговорим об этом подробнее в следующей главе. 

На данный момент мы не будем использовать аттрибут `viewBox`. Пользовательская система координат будет идентична системе координат `viewport`.

На следующем изображении, “оси” системы координат `viewport` - серые, а те оси что относятся к пользоватльской системе координат  `viewBox` - голубые. Поскольку обе системы координат на данный момент идентичны, они наложны друг на друга.

![initial-coordinate-systems](https://d33wubrfki0l68.cloudfront.net/fedcd70d34fc3a5dea2369e727b6a8e7081de43b/3496e/images/initial-coordinate-systems.jpg)

Попугай на этом SVG имеет рамку размером 200 единиц (200px в данном случае) в ширину и 300 единиц в высоту. Попугай отрисован в рамке на основе исходной системы координат

Новое пользовательское пространство (т.е. новая выбранная система координат) может быть получено с помощью определения трасформаций через аттрибут `transform` для элемента контейнера или графического элемента. Мы поговорим о трансформациях во второй части этой статьи и более детально в третьей последней части.

### `viewBox`

Мне нравится думать о `viewBox` как о __"реальной"__  системе координат. В конце концов, именно эта система координат используется для отрисовки графики SVG в оболочке. Эта система координат может быть меньше или больше чем `viewport`, и также может частично или полностью отображаться в окне просмотра.

В прошлый раз, пользовательская система координат была идентична системе координат `viewport`. Дело в том, что мы просто не определили ее по другому. Вот почему все расположение и отрисовка выполнены относительно системы координат `viewport`.
Как только мы создали систему координат `viewport` (назначив `width` и` height`) , браузер создал стандартную пользовательскую систему координат идентичную `viewport`.

Вы определяете вашу собственную пользовательскую систему координат используя аттрибут `viewBox`. Если пользовательская система координат, которую вы выбрали, имеет то же соотношение сторон (отношение высоты к ширине) как система координат `viewport`, то пользовательская система координат заполнит всю область `viewport` (поговорим о примерах через минуту). В любом случае, если ваша пользовательская система координат определена с другим соотношением сторон, вы можете использовать аттрибут `preserveAspectRatio`, чтобы определить будет ли вся графика отображаться внутри `viewport` и также вы можете использовать его для позиционирования графики внутри `viewport`. Мы рассмотрим все в деталях и примерах для таких случаев в сдедующей главе. А пока мы посмотрим на те примеры, где соотношение сторон `viewBox` совпадает с `viewport`  - в этих примерах `preserveAspectRatio` не будет оказывать никакого влияния.

Но прежде чем перейдем к примерам, посмотрим на синтаксис `viewBox`.

### Синтаксис `viewBox`

Аттрибут `viewBox` принимает четыре параметра: `<min-x>`, `<min-y>`, `width` и`height`.
```html
viewBox = <min-x> <min-y> <width> <height>
```

Значения `<min-x>` и `<min-y>` определяют левый верхний угол `viewBox`, а  `width` и `height` - ширину и высоту `viewBox`.

Заметьте, здесь ширина и высота не обязательно должны быть такими же как и в родительском `<svg>` элементе. Негативные значения ширины и высоты являются некорректными. Нулевые значения отменяют рендеринг элемента.

И также, можно заметить, что ширине `viewport` может быть назначено любое значение с помощью CSS. Например, если установить ширину 100% SVG заполнит документ. Каким бы ни было значение `viewBox`, оно будет вписано в вычисленную ширину наружного SVG.  

Так выглядит пример настройки `viewBox`:
```html
<!-- viewbox в этом примере равен viewport, но они могут быть различными -->
<svg width="800" height="600" viewbox="0 0 800 600">
    <!-- SVG содержимое отрисованное в оболочке SVG  -->
</svg>
```

Если вы читали о `viewBox` где-нибудь ранее, возможно вы  столкнулись с несколькими определениями, которые говорили о том, что вы можете использовать `viewBox` для преобразования графики SVG, масштабируя или перемещая ее. Это верно. Я собираюсь зайти дальше и сказать, что  с помощью `viewBox` вы можете даже обрезать SVG.

Лучший способ понять как работает `viewBox` и чем он отличается от `viewport` - визуализировать их. Так что давайте начнем с нескольких примеров. Сначала посмотрим примеры с одинаковым соотношением сторон `viewBox` и`viewport` , в этом случае, нам пока не нужно будет разбираться с тем, как работает `preserveAspectRatio`.

Вот простой пример. `viewBox` в этом примере будет в два раза меньше чем `viewport`. Мы не изменяли начало координат `viewBox` в данном случае, так что обе оси начинаются от нулевой точки. Ширина и высота `viewBox` будут составлять половину ширины и высоты 
`viewport`. Это означает, что мы сохранили соотношение сторон. 

```html
<svg width="800" height="600" viewbox="0 0 400 300">
    <!-- SVG содержимое отрисованное в оболочке SVG  -->
</svg>
```

Что именно делает `viewbox="0 0 400 300"`?

- [x] Определяет специальную область от левого верхнего края в точке (0, 0) до точки (400, 300)
- [x] SVG графика обрезается до этой области
- [x] Область масштабируется (увеличивается) для заполнения всего `viewport`
- [x] Пользовательская система координат приводится к системе координат `viewport` так что в данном случае одно деление пользовательской системы координат будет равно двум делениям `viewport`

Следующее изображение показывает результат применения `viewbox` к оболочке `<svg>` в нашем примере. Серые деления представляют систему координат `viewport`, голубые - пользовательскую систему координат созданную с помощью `viewbox`. Назначая `viewbox` можно получить результат похожий на обрезку графики по `viewbox` и масштабирования ее до заполнения всей области `viewport`. И помним, что мы, пока что, сохраняем то же соотношение сторон, что и у `viewport`. 

![viewbox-crop](https://d33wubrfki0l68.cloudfront.net/e6639267b77782b0d62ce5f29a4d9b07bf5df87a/14c69/images/viewbox-400-300-crop.jpg)

Все что вы можете нарисовать в оболочке SVG будет отрисовано относительно новой пользовательской системы координат.

Мне нравится представлять оболочку SVG с `viewBox` как Google Maps. Вы можете увеличить определенную область или регион в картах Google; только эта область будет отображена и увеличена до заполнеия всего `viewport` браузера. Тем не менее, вы знаете что остальная часть карты все еще здесь, просто она не отображается т.к. выходит за границы `viewport` - и значит эта оставшаяся часть будет отсечена.

Теперь давайте попробуем изменить значения `<min-x>` и `<min-y>`. Назначим обоим аттрибутам значение 100. Можно выбрать любое число, какое вы захотите. Соотношение сторон выберем таким же, как у `viewport` 

```html
<svg width="800" height="600" viewbox="100 100 200 150">
    <!-- SVG content drawn onto the SVG canvas -->
</svg>
```

Эффект применения `viewBox="100 100 200 15` такой же как и в предыдущем примере - обрезка изображения. Графика обрезана и отмасштабирована для заполнения `viewport` 

![viewbox-crop translate](https://d33wubrfki0l68.cloudfront.net/59a2fdb34e4735b8cdb865efe7233ac373b5fc43/d5d7d/images/viewbox-200-150-crop.jpg)

Еще раз, пользовательская система координат вписана в систему координат `viewport` - 200 пользовательских единиц соответствуют 800 единицам `viewport`, т.о. каждая пользовательская единица  равна 4 единицам `viewport` . В результате получается такой зум - эффект как на скриншоте выше. 

Также заметим, что в этом случае, определив ненулевые значения для `<min-x>` и `<min-y>` мы создаем эффект трансформации графики;более специфическим способом чем перенос оболочки SVG с помощью `transform="translate(-100 -100)"`  

Действительно, как говорит спецификация:

> Эффект применения `viewBox` в том, что со стороны пользователя автоматически поддерживается соответствующая матрица трансформаций чтобы вписать определенную область пользовательского пространства в границы области назначения (часто `viewport`).

Это просто более сложный способ сказать о том, что мы отмечали ранее: графика обрезается и масштабируется до заполнения `viewport`. Затем в спецификации добавлено примечание:

> В некоторых случаях на стороне пользователя необходима поддержка трансформации переноса в дополнение к масштабированию. Например, на внешнем SVG элементе, трансформация переноса понадобится если для `viewBox` определены ненулевые значения для `<min-x>` или `<min-y>`.

Чтобы лучше продемонстрировать трансформацию переноса, давайте применим отрицательное значение (-100) для `<min-x>` и `<min-y>`.
Эффект переноса будет такой же как при применении `transform="translate(100 100)"`; это означает, что графика будет перенесена вниз и вправо после обрезки и масштабирования. Так будет выглядеть код предыдущего примера с отрицательными значениями для `<min-x>` и `<min-y>`

```html
<svg width="800" height="600" viewbox="-100 -100 400 300">
    <!-- SVG content drawn onto the SVG canvas -->
</svg>
```

Результат применения примера выше будет выглядеть следующим образом:

![viewbox-crop-translate](https://d33wubrfki0l68.cloudfront.net/a31c4b3156fec4d20bc555d3ec4d9f7ca6c291fb/832dc/images/viewbox-400-300-crop-translate.jpg)

Заметим, что в отличие от применения аттрибута `transform`, автоматическая трансформация выполненная с применением `viewBox` не влияет на `x` и `y`, и ширину и высоту элемента с аттрибутом `viewBox`. Таким образом в примере выше, в котором показано что `svg` элемент имеющий аттрибуты `width`, `height` и `viewBox`, ширина и высота представлены элемента в системе координат, которая была до применения трансформации `viewBox`. Вы можете увидеть это в примерах выше, поскольку так же как и исходная ( серая ),
система координат `viewport` осталась неизменной после использования аттрибута `viewBox` на `svg`.

С другой стороны, как и свойство `transform`, это создает новую систему коррдинат для всех других аттрибутов и для элементов-потомков.
Как видно в примерах, как только пользовательская система координат создана - она уже не будет идентична системе координат `viewport`, как исходная система координат до использования `viewBox`. И все потомки `svg` будут позиционироваться и масштабироваться в новой пользовательской системе координат, а не в той, что была исходной.

Наш последний пример `viewBox` похож на предыдущий, но вместо обрезки оболочки мы собираемся расширить ее внутри `viewport` и посмотреть как это повлияет на графику. Мы определим `viewBox` с выстой и шириной больше чем у `viewport`, тем не менее соблюдая соотношение сторон `viewport`, мы разберемся с разными соотношениями сторон в следующей главе.

В этом примере, мы создадим `viewBox` в 1.5 раза превышающий `viewport`

```html
<svg width="800" height="600" viewbox="0 0 1200 900">
    <!-- SVG content drawn onto the SVG canvas -->
</svg>
```

Что случится теперь, когда пользовательская система координат должна будет масштабироваться до размеров 1200х900? Пользовательская система координат будет вписана в систему координат `viewport` таким образом, что каждая единица пользовательской системы координат будет равно отношению `vieport-width/vieBox-width` по ширине и `vieport-height/vieBox-height` по высоте в системе координат `viewport`. Это означает, что в нашем случае, каждая единица по оси `x` в пользовательской системе координат будет равна `0.66` по оси `x` `viewport` и каждая пользовательская единица по оси `y` будет равна `0.66` по оси `y` `viewport`.

Конечно, лучший способ понять все это - визуализировать результат. `viewBox` масштабируется таким образом чтобы поместиться внутри `viewport` как показано на следующем изображении. И т.к. графика отрисовывается в оболочке основаной на новой пользовательской системе координат, а не системе координат `viewport`, она выглядит меньше внутри `viewport`. Пользовательская система координат 1200x900 вписана в систему координат `viewport` 800x600. Серые деления представляют систему координат `viewport`; голубые - систему координат созданную `viewBox`.

![viewBox scaled 1.5](https://d33wubrfki0l68.cloudfront.net/423b1dae8696c091dc8f999c0f1c99a419302c48/2df7b/images/viewbox-1200-900.jpg)

До сих пор все наши примеры хорошо уживались с соотношением высоты и ширины `viewport`. Но что будет, если высота и ширина, определенные в аттрибуте `viewBox` имеют другое соотношение сторон, отличающееся от  `viewport`? Например,предположим мы выбрали `viewBox` 1000x500. Соотношение сторон теперь уже не такое как у `viewport`. Результат использования `viewBox = "0 0 1000 500"` для нашего примера будет выглядеть следующим образом:


![viebox 1000 x 500](https://d33wubrfki0l68.cloudfront.net/b78870133c46cf41fda5933597744438eaa730f7/573e9/images/viewbox-1000-500.jpg)

Пользовательская система координат, а значит и графика спозиционируются внутри `viewport` таким образом:

The user coordinate system and hence the graphic is positioned inside the viewport so that:
- [x] Весь `viewBox` помещается внутри `viewport`
- [x] В приоритете соотношение сторон `viewBox`. `viewBox` не растянется чтобы закрыть всю область `viewport`
- [x] `viewBox`будет расположен по центру `viewport`как по вертикали так и по горизонтали

Это стандартное поведение. Что может на него повлиять? И что если нам понадобится изменить позицию `viewBox` внутри `viewport`. И  тут в дело вступает аттрибут `preserveAspectRatio`

### The preserveAspectRatio Attribute

The preserveAspectRatio attribute is used to force a uniform scaling for the purposes of preserving the aspect ratio of a graphic.

If you define a user coordinate system with an aspect ratio different from that of the viewport’s, and if the browser were to stretch the viewbox to fit into the viewport as we’ve seen in previous examples, the difference in aspect ratios will cause the graphic to be distorted in either direction. So if the viewbox in the last example were to be stretched to fill the viewport in both directions, the graphic would look like so:
The result of mapping the user coordinate system to the viewport without preserving its aspect ratio. The graphic is distorted and looks shrunk horizontally while also being stretched vertically.

The distortion is also clearly visible (and unwanted, of course) when using a viewbox value of 0 0 200 300, which would be smaller than the dimensions of the viewport. I chose this value in particular so that the viewbox matches the size of the bounding box of the parrot. If the browser were to stretch the graphic to fill the entire viewport, it would look like the so:
The result of mapping the user coordinate system to the viewport without preserving its aspect ratio. The graphic is distorted.

The preserveAspectRatio attribute allows you to force uniform scaling of the viewbox, while maintaining the aspect ratio, and it allows you to specify how to position the viewbox inside the viewport if you don’t want it to be centered by default.
The preserveAspectRatio syntax

The official syntax for preserveAspectRatio is:
?
preserveAspectRatio = defer? <align> <meetOrSlice>?

It is usable on any element that establishes a new viewport (we’ll get into these in the next parts of the series).

The defer argument is optional, and is used only when you’re applying preserveAspectRatio to an <image>. It is ignored when used on any other element. Since <image> it outside the scope of this article, we’ll skip the defer option for now.

The align parameter indicates whether to force uniform scaling and, if so, the alignment method to use in case the aspect ratio of the viewBox doesn’t match the aspect ratio of the viewport.

If the align value is set to none, for example:
?
preserveAspectRatio = "none"

The graphic will be scaled to fit inside the viewport without maintaining the aspect ratio, just like we saw in the last two examples.

All other values of preserveAspectRatio force uniform scaling while preserving the viewbox’s aspect ratio, and specify how to align the viewbox inside the viewport. We’ll get into the values of align shortly.

The last argument, meetOrSlice is also optional, and it defaults to meet. This argument specifies whether or not the entire viewBox should be visible inside the viewport. If provided, it is separated from the align parameter by one or more spaces. For example:
?
preserveAspectRatio = "xMinYMin slice"

These values may seem foreign at first. To make understanding them easier and make them more familiar, you can think of the meetOrSlice value as being similar to the background-size values contain and cover; they work pretty much the same. meet is similar to contain, and slice is similar to cover. Here are the definitions and meaning of each value:

meet (The default value)
    Scale the graphic as much as possible while maintaining the following two criteria:

        aspect ratio is preserved
        the entire viewBox is visible within the viewport

    In this case, if the aspect ratio of the graphic does not match the viewport, some of the viewport will extend beyond the bounds of the viewBox (i.e., the area into which the viewBox will draw will be smaller than the viewport). (See the last example of The viewBox section.) In this case, the boundaries of the `viewBox` are contained inside the viewport such that the boundaries *meet*.

    This value is similar to background-size: contain. The background image is scaled as much as possible while preserving its aspect ratio and making sure it fits entirely into the background painting area. If the aspect ratio of the background is not the same as that of the element it is being applied to, parts of the background painting area will not be covered by the background image.
slice
    Scale the graphic so that the viewBox covers the entire viewport area, while maintaining its aspect ratio. The viewBox is scaled just enough to cover the viewport area (in both dimensions), but it is not scaled any more than needed to achieve that. In other words, it is scaled to the smallest size such that the width and height of the viewBox can completely cover the viewport.

    In this case, if the aspect ratio of the viewBox does not match the viewport, some of the viewBox will extend beyond the bounds of the viewport (i.e., the area into which the viewBox will draw is larger than the viewport). This will result in part of the `viewBox` being *sliced off*.

    You can think of this as being similar to background-size: cover. In the case of a background image, the image is scaled while preserving its intrinsic aspect ratio (if any), to the smallest size such that both its width and its height can completely cover the background positioning area.

So, meetOrSlice is used to specify whether or not the viewBox will be completely contained inside the viewport, or if it should be scaled as much as needed to cover the entire viewport, even if this means that a part of the viewbox will be “sliced off”.

For example, if we were to apply viewBox size of 200x300, and using both the meet and slice values, keeping the align value set to the default by the browser, the result for each value will look like the following:
The result of applying the meet parameter vs the slice parameter on a viewBox with an aspect ratio different from that of the viewport's aspect ratio.

The default value for align is xMidYMid, so, in both cases, the graphic is scaled so that its mid axes align with the mid axes of the viewport.

The align parameter takes one of nine values, or the none value. Any value other than none is used to uniformly scale the image preserving its aspect ratio, and it is also used to align the viewBox inside the viewport.

The align values works similar to the way background-position works when used with percentage values. You can think of the viewBox as being the background image. The way the positioning with align differs from background-position is that instead of positioning a specific point of the viewbox over a corresponding point of the viewport, it aligns specific “axes” of the viewBox with corresponding “axes” of the viewport.

In order to understand the meaning of each of the align values, we’re going to first introduce each of the “axes”.

Remember the <min-x> and <min-y> values of the viewBox? We’re going to use each of these to define the “min-x” axis and “min-y” axis on the viewBox. Additionally, we’re going to define two axes “max-x” and “max-y”, which will be positioned at <min-x> + <width> and <min-y> + <height>, respectively. And last but not least, we’ll define two axes “mid-x” and “mid-y”, which are positioned at <min-x> + (<width>/2) and <min-y> + (<height>/2), respectively.

Did that make things more confusing? If so, have a look at the following image to see what each of those axes represents. In the image, both <min-x> and <min-y> are set to their default 0 values. The viewBox is set to viewBox = "0 0 300 300".
The pink and orange solid lines represent the min-y and min-x values respectively. The dashed pink and orange lines represent the mid and max x and y values.

The dashed grey lines in the above image represent the mid-x and mid-y axes of the viewport. We’re going to use those to align the mid-x and mid-y of axes of the viewBox for some values. For the viewport, the min-x value is equal to 0, the min-y value is also 0, the max-x value is equal to the width of the viewBox, the max-y value is equal to its height, and the mid-x and mid-y represent the middle values of the width and height, respectively.

The alignment values are:

none
    Do not force uniform scaling. Scale the graphic content of the given element non-uniformly (without preserving aspect ratio) if necessary such that the element's bounding box exactly matches the viewport rectangle.

    In other words, the viewBox is stretched or shrunk as necssary so that it fills the entire viewport exactly, disregarding the aspect ratio. The graphic may be distorted.

    (Note: if <align> is none, then the optional <meetOrSlice> value is ignored.)
xMinYMin
    Force uniform scaling.
    Align the <min-x> of the element's viewBox with the smallest X value of the viewport.
    Align the <min-y> of the element's viewBox with the smallest Y value of the viewport.
    Think of this as being similar to background-position: 0% 0%;.
xMinYMid
    Force uniform scaling.
    Align the <min-x> of the element's viewBox with the smallest X value of the viewport.
    Align the midpoint Y value of the element's viewBox with the midpoint Y value of the viewport.
    Think of this as being similar to background-position: 0% 50%;.
xMinYMax
    Force uniform scaling.
    Align the <min-x> of the element's viewBox with the smallest X value of the viewport.
    Align the <min-y>+<height> of the element's viewBox with the maximum Y value of the viewport.
    Think of this as being similar to background-position: 0% 100%;.
xMidYMin
    Force uniform scaling.
    Align the midpoint X value of the element's viewBox with the midpoint X value of the viewport.
    Align the <min-y> of the element's viewBox with the smallest Y value of the viewport.
    Think of this as being similar to background-position: 50% 0%;.
xMidYMid (The default value)
    Force uniform scaling.
    Align the midpoint X value of the element's viewBox with the midpoint X value of the viewport.
    Align the midpoint Y value of the element's viewBox with the midpoint Y value of the viewport.
    Think of this as being similar to background-position: 50% 50%;.
xMidYMax
    Force uniform scaling.
    Align the midpoint X value of the element's viewBox with the midpoint X value of the viewport.
    Align the <min-y>+<height> of the element's viewBox with the maximum Y value of the viewport.
    Think of this as being similar to background-position: 50% 100%;.
xMaxYMin
    Force uniform scaling.
    Align the <min-x>+<width> of the element's viewBox with the maximum X value of the viewport.
    Align the <min-y> of the element's viewBox with the smallest Y value of the viewport.
    Think of this as being similar to background-position: 100% 0%;.
xMaxYMid
    Force uniform scaling.
    Align the <min-x>+<width> of the element's viewBox with the maximum X value of the viewport.
    Align the midpoint Y value of the element's viewBox with the midpoint Y value of the viewport.
    Think of this as being similar to background-position: 100% 50%;.
xMaxYMax
    Force uniform scaling.
    Align the <min-x>+<width> of the element's viewBox with the maximum X value of the viewport.
    Align the <min-y>+<height> of the element's viewBox with the maximum Y value of the viewport.
    Think of this as being similar to background-position: 100% 100%;.

So, using the align and meetOrSlice values of the preserveAspectRatio attribute, you can specify whether or not to scale the viewBox uniformly, how to align it inside the viewport, and whether or not it should be entirely visible inside the viewport.

Sometimes, and depending on the size of the viewBox, some values may have similar results. For example, in the viewBox="0 0 200 300" example from earlier, some alignments are identical using different align values. The value of meetOrSlice is set to meet in this case so that the entire viewBox is contained inside the viewport.
The result of aligning the viewBox using different align values. The meetOrSlice value is set to meet.

If we were to change the meetOrSlice value to slice, we’d get different results for different values. Notice how the viewBox is stretched so that it covers the entire viewport. The x-axis is stretched so that the 200 units cover the viewport’s 800 units. In order for this to happen, and to maintain the aspect ratio of the viewbox, the y-axis gets “sliced off” at the bottom, but you can image it extending below the viewport’s height.
The result of aligning the viewBox using different align values. The meetOrSlice value is set to slice.

Of course, different viewBox values will also look different from the 200x300 we’re using here. For the sake of brevity, I won’t get into more examples, and I’ll leave you to play with an interactive demo I created to help you better visualize how the viewBox and different preserveAspectRatio values work together when different values are used. You can check the interactive demo out by visiting the link in the next section.

But before we move to that, I just want to note that the mid-x, mid-y, max-x, and max-y values change if the values of the <min-x> and <min-y> change. You can play with the interactive demo and change these values to see how the axes and thus the alignment of the viewBox changes accordingly.

The following image shows the effect of using viewBox = "100 0 200 300" on the position of the alignment axes. We’re using the same example as earlier, but instead of having the <min-x> value be set to zero, we’re setting it to 100. You can set them to any number value you want. Notice how the min-x, mid-x, and max-x axes change. The preserveAspectRatio used here is the default xMidYMid meet, which means that the mid-* axes are aligned with the middle axes of the viewport.
The effect of changing the value of <min-x> on the position of the x-axes. The translucent blue area shows the area which is considered to be the viewBox area after changing the value of <min-x>.
