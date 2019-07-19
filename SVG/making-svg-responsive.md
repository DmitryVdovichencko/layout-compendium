# Готовим отзывчивый SVGs с помощью CSS

Статья о том, как сделать встраиваемые SVG отзывчивыми в разных браузерах. Рассмотрим техники добавоения на страницу, как применить "Padding Hack" и как использовать инлайновые медиа-запросы чтобы сделать SVG адаптивными.

SVG можно добавить на страницу по-разному:

* Используем инлайновый <svg> тэг. 
* Используем тэг <img> 
* Используем тэг <object>
* Используем iframe
* Используем фоновое изображение CSS.

Теоретически, создание масштабируемого, относительно его контейнера, SVG, должно быть простым процессом, типа удаляем любой фиксированный габарит (высоту и/или ширину), а затем определяем значение аттрибута viewBox. Тем не менее, из-за различий в браузерных реализациях и разных несоответствий, веб не является страной радуги и единорогов, поэтому создание отзывчивого SVG не является таким очевидным, ведь браузеры определяют габариты SVG при встраивании разными способами и они имеют разногласия между собой.

Так что есть опредленные фиксы и хаки, которые могут помочь в достижении предсказуемого поведения во всех браузерах. В этой статье будут рассмотрены техники создания адаптивного или резинового SVG, и случаи когда лучше использовать те или иные приемы.

## Готовим резиновый SVG используя CSS.

Чтобы создать резиновый SVG первый логичный шаг, который стоит сделать - удалить аттрибуты `width` и `height`. Когда определены фиксированные значения `width` и/или `height` SVG будет стараться их поддерживать, что будет ограничивать его от того чтобы стать полностью "резиновым". Тем не менее, аттрибут `viewBox` лучше оставить. После удаления `width` и `height`можно добавить SVG на страницу одним из перечисленных способов.

Для демонстрации используем морской SVG логотип. За халявный лого спасибо Freepik.

![nautical logo](https://codropspz-tympanus.netdna-ssl.com/codrops/wp-content/uploads/2014/08/logo.png)

logo

SVG embedded using <img>
When an SVG is embedded as an image in an <img> tag, the height and width specified on the <img> tag are used by the browser as a viewport to render the SVG into. The contents of the SVG are then positioned inside the viewport depending on the value of the viewBox specified on the <svg>.

<img src="my_SVG_file.svg" alt="Image description." />
Normally, browsers are smart enough to determine the width and height of the SVG even if you don’t specify a width and height for the <img> element. For example, if you were to wrap the img in a div, without specifying the height and width of the img, Chrome and Firefox will both assume the img has width: 100%; the SVG is then stretched to fill the containing div, and its height is adjusted accordingly so that it preserves its aspect ratio. As the containing div is then resized, the img SVG responds in an expected way. This behavior is different from an img referencing a raster graphic such as a PNG image, for example; in the latter case, you’d have to explicitly set the width of the img to 100% using CSS to make it fluid.

In Internet Explorer, things are different. If you don’t specify a width for the img, it will assume the height is equal to 150px, which is the default height for replaced elements as specified in CSS—the default width is 300px. The SVG is then positioned inside the containing div such that its height is equal to 150px; the width is assumed to be 100%, though, making the SVG always 150px in height no matter how wide the container gets, resulting in extra white spaces on both sides. In order to work around this issue in IE, it is enough to explicitly set the width on the img to 100%. These results were obtained from tests made in Internet Explorer 9 and 11.

So, making an SVG embedded as an img fluid is doable by removing the explicit height and width set on the <svg>, and then adding one line of CSS for Internet Explorer:

/* fix for IE */
img {
    width: 100%;
}
Check the demo out in different browsers to see how the SVG behaves.

Note that if you choose to set a width and height on the <img> tag other than 100%, the SVG viewBox is going to fit inside the viewport created by these dimensions just like it would if the width and height were set on the <svg> element. This is due to the fact that the dimensions of the img establish the viewport for the SVG it references.

SVG embedded using <object>
An SVG embedded using an <object> tag behaves in a similar way to that embedded using <img> when it comes to making the SVG fluid.

<object type="image/svg+xml" data="my_SVG_file.svg">
    <!-- fallback here (<img> referencing a PNG version of the graphic, for example) -->
</object>
Just like with the img tag, Firefox and Chrome both assume the width of the SVG object to be 100%, and the height changes proportionally as the width increases or decreases, and hence the SVG scales as expected. The width and height set on the object will be the viewport inside which the SVG graphic is rendered.

In Internet Explorer, the same issue as the one we mentioned earlier occurs, where the SVG’s height is set to the default 150px. The issue is also fixed by setting the width of the object explicitly to 100%:

/* fix for IE */
object {
    width: 100%;
}
Here is a demo that you can check out.

SVG embedded using <iframe>
An <iframe> is very similar in function and features to an <object> in SVG.

<iframe src="my_SVG_file.svg">
    <!-- fallback here (<img> referencing a PNG version of the graphic, for example) -->
</iframe>
However, the way browsers seem to handle it is different. Chrome, Firefox, and Internet Explorer currently handle an iframe referencing an SVG (that doesn’t have a width and height) by defaulting to the default size for replaced elements that we mentioned before: 300px width and 150px height. In the previous cases, when IE used the default height of 150px, it assumed the width to be 100%. However, with the iframe, all three browsers set both the height and the width of the iframe to a fixed 300px by 150px size.

Applying width:100% stretches the iframe to fill the width of the container, but it does not change the height—it is still fixed at 150px; even when the container and the iframe shrink to a width less than the original width of the SVG, the iframe still maintains a 150px height, resulting in white space appearing at the top and bottom of the SVG. This happens in all three browsers.

The only way to change the height of the SVG is by explicitly setting it on the iframe. Of course, the height then, along with the width, form the viewport inside which the SVG is positioned. And if you want the SVG to fit inside the viewport without any white space above it, under it, or to its sides, you need to make sure you set the height and width of the viewport (the iframe) such that the aspect ratio of the height to the width is the same as the aspect ratio of the height to the width of the SVG. The aspect ratio of the SVG canvas is the aspect ratio of the height and width of the viewBox. ( For a detailed explanation of how the SVG viewport and viewBox work, check the Resources section for a link to a thorough guide.)

There is no “standard”, non-hacky way in CSS to create an element with a specific height-to-width ratio. However, a few years back, Thierry Koblentz posted an article on A List Apart explaining a technique—known today as The Padding Hack—that makes it possible to create intrinsic ratios for videos and iframes.

The idea behind the padding hack is to make use of the relation of an element’s padding to its width. When the padding is set in percentages, the percentage is calculated relative to the width of the element, even if you’re setting the padding on the top of bottom of the element. That same technique can be used in the case of SVG to make the SVG fluid. The following are the steps for making the iframe referencing the SVG fluid. We’ll explain each step as we get to it.

The SVG in the demos looks like this:

<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="194" height="186" viewBox="0 0 194 186">
    
</svg>
The Padding Hack – Step 1
The first step in making the SVG responsive when it is embedded in an iframe is the same step we did before: remove the height and width attribute from the root <svg> element.


<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
    
</svg>
The Padding Hack – Step 2
Next, make sure you set the viewBox on the <svg>. In most cases, the SVG will have a viewBox specified, so you want to keep that.

You don’t need to explicitly set the preserveAspectRatio because its default value (xMidYMid meet) is the value we would want to set anyway. So, unless the value of preserveAspectRatio is set to something else, you don’t really have to set it.

<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186" preserveAspectRatio="xMidYMid meet">
    
</svg>
The Padding Hack – Step 3
In order for the padding hack to work, the SVG needs to be wrapped in a container. Since the containing element is just that—a container—we’re going to use a generic div. This container is going to get the intrinsic ratio using the padding hack (next step), and then the iframe referencing the SVG will be scaled to fit inside the container.


<div class="container">
    <iframe src="my_SVG_file.svg">
        
    </iframe>
</div>
The Padding Hack – Step 4
Next, we apply some styles to the container following these rules:

.container {
    height: 0;             /* collapse the container's height */
    width: width-value;    /* specify any width you want (a percentage value, basically) */
    /* apply a padding using the following formula */
    /* this formula makes sure the aspect ratio of the container equals that of the svg graphic */
    padding-top: (svg height / svg width) * width-value;
    position: relative;    /* create positioning context for svg */
}
So, what exactly do the above rules do?

First, we collapse the container’s height. Since the padding is calculated relative to the width of the element, we’re going to only apply the padding to the element to expand it vertically again. Any additional height will throw off the aspect ratio we need.

After you specify the width of the container—which you’ll usually set to be fluid in the page using a percentage value—a padding is applied to the top (or bottom) of the container, following the above formula. This formula uses the values of the height and width attributes of the <svg> (the ones we removed in Step 1) to specify a padding value that allows the intrinsic ratio of the container to match that of the svg.

In our logo example, the width of the svg is 194px and the height is 186px. I’ve set the container’s width to 50%, so that it takes up half the available horizontal space. The padding applied to the container is equal to (186 / 194) * 50 = 48%. So, the code for the container looks like the following:

.container {
    width: 50%;
    height: 0;
    padding-top: 48%;
    position: relative;
}
Because the container’s height has been collapsed, and a fairly large amount of padding is applied to the top of it, its content—the SVG iframe—is pushed down so that it no longer “sits” inside the container anymore.

In order to “pull” the SVG back up, we’re going to position the iframe absolutely inside the container. This is where the position: relative; comes into play—it is used to create a positioning context for the SVG.

The Padding Hack – Step 5
Now that we have a positioning context, we’re going to position the iframe absolutely inside the container, and scale it so that it has the container’s height and width:

iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
That’s all you need for the SVG to be fluid. The iframe comes with a default border; you’ll probably want to get rid of that by adding border: none; to the above rule set.

Check the demo out. The demo shows the iframe with and without the padding hack inside its container. Resize the screen to see how the SVG responds in both cases.

SVG embedded inline using the <svg> tag
An SVG can be embedded inline in an HTML document using the <svg> tag.

<!-- Note: the xmlns is not required in an HTML5 document -->
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
    <!-- SVG content -->
</svg>
An inline svg‘s viewport is established by the height and width specified on the root <svg> element. Once the width and height attributes are removed, all browsers assume a width equal to 100% and stretch the SVG horizontally to fit the width of its container.

In Chrome and Firefox, the height of the SVG is calculated by the browser so that the SVG scales as expected. No extra work is needed to make the SVG fluid.[1]

In Internet Explorer (tested in version 9 and 11), the height is assumed to be 150px again, with the width of 100%. Just like in the case of the img embed, this height is fixed so that when the SVG shrinks on smaller screens, the SVG will contain white space above and below the content inside it. In the case of the img embed, explicitly specifying width: 100% was enough to fix this; however, setting the width to 100% on the inline svg does not fix it. So we’re left in a situation similar to that of the iframe embed. The only way to make the inline SVG fluid in this case is by using the padding hack as we did in the iframe case. The HTML and CSS would look like the following:

<div class="container">
    <svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
        
    </svg>
</div>
.container {
    width: 50%;
    height: 0;
    padding-top: 48%;
    position: relative;
}

svg {
    position: absolute;
    top: 0;
    left: 0;
}
Note that, unlike the iframe, the svg does not need a height and width set to fit inside the container. It wouldn’t hurt if you added them, but they’re not necessary in this case.

You can check out the demo.

[1] Note: At the time of writing of this article, the latest version of Chrome on Windows (Version 36.0.1985.143 m) also needs the padding hack to work. The SVG does scale well in general, but it seems to not go any smaller than its original dimensions. So, when the container is shrunk so that its width becomes less than the initial width of the SVG, the SVG sticks out (overflows). The padding hack fixes it. A test in Chrome Canary showed that this issue was non-existent; so hopefully the hack won’t be needed in the next stable update.

SVG embedded as a background image using CSS
One of the popular ways to embed an SVG is using a background image on an element. The following snippet shows an element with an SVG background image:

.element {
    background-image: url(my_SVG_image.svg);
    /* other styles */
}
Luckily, we don’t have to apply any fixes or hacks to make an SVG background image behave as expected—an SVG background image can be positioned, tiled, sized, and scaled as any bitmap background image can.

Check out the live demo showing a simple example of the above logo being used as a background image of a fluid element.

In the demo, I’ve used the padding hack so that the element has an intrinsic ratio similar to that of the SVG. You may or may not want/need to do this. I did this for demonstration purposes only so that the SVG fits exactly into the background at all times. I just collapsed the height of the element, and then applied the padding to get the aspect ratio I wanted. Without doing this, and because the element is empty in the demo, it would have collapsed anyway and the background image wouldn’t have been visible.

Making SVGs adaptive Using CSS Media Queries
The fact that the graphical elements inside an SVG are created using XML makes working with SVGs very liberating. Because SVG content is made up of XML tags that render graphics, we can select individual elements and apply specific styles to them, just like we can select HTML elements, using CSS selectors.

Just like you can change the styles of an HTML element—like background color, borders, etc.—you can change certain styles of an SVG element using CSS. SVG elements are usually styled using presentation attributes like fill, stroke, transform, and many others. However, only a subset of all presentation attributes can be set using CSS. You can find a list of SVG styling properties that can be set using CSS available in the SVG Styling specification. The list of attributes that can be set using CSS properties currently does not include the x, y, width, and height attributes; but these four will be added to the list and we will be able to set them using CSS in SVG2.

Some of the most commonly set styles using CSS include fill, which works similar to a background color on many elements; stroke, which is similar to border; opacity, display, transform, and a few others.

For the logo we have in our demos, we’re going to use the img tag to reference it. After applying the fix mentioned earlier to make the SVG fluid, we’re going to hide some elements on smaller screens, and change the fill color of others, thus slightly changing how it looks on different screen sizes that we’ll specify in the CSS media queries.

We’re going to add the media queries inside a <style> tag inside the root <svg>. The following is how the SVG file we’re going to be referencing looks like:

<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
    <style>
        /* CSS styles and media queries here */
    </style>
    
</svg>
And we’re going to reference the logo using an <img> tag like so:

<img src="logo.svg" alt="Logo" />
At this point, it’s time to point out that the sizes specified in the media queries refer to the size of the SVG viewport, except for when the SVG is embedded inline in the document using an <svg> tag. The SVG viewport is defined by the dimensions of the element referencing the SVG. For example, in the case when the SVG is referenced in an img tag, the width and height of the img specify the viewport. In other words, the conditions specified in the media queries apply to the img tag, so that (width: 30em) would apply styles to the SVG when the img is 30em wide.

That said, here is the markup for the SVG. Each of the elements inside it has an ID that we’re going to use in the CSS to select and style it:

<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
    <path id="curved_bg" fill="#195463" d="..."/>
    <g id="primary_content" fill="#ECECEC">
        <path id="icon" d="..."/>
        <path id="inner-circle" d="..."/>
        <path id="middle-circle" d="..."/>
    </g>
    <g id="secondary_content" fill="#ECECEC">
        <path id="bottom-text" d="..."/>
        <path id="upper-text" d="..."/>
        <path id="outer-circle" d="..."/>
        <circle id="left-dot" cx="31.1" cy="91.5" r="3"/>
        <circle id="right-dot" cx="163.4" cy="91.5" r="3"/>
    </g>
</svg>
Using a <style> tag inside the SVG, we’re going to specify the media queries that will change the SVG’s styles depending on the size of the viewport. The SVG will change as shown in the following image:

responsive-logo-variations

We’re only going to be using the CSS fill and opacity properties. As the viewport size decreases, the curvy background is first removed by setting its opacity to zero, and the fill color of the remaining content of the SVG is changed from white to dark navy. As the screen gets smaller, the text part of the logo is removed so that it takes up less screen estate. And finally, the circle surrounding the icon is removed and only the anchor icon remains on very small screens.

Adding the media queries, our SVG file looks like this:

<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 194 186">
    <style>
        svg * {
            transition: fill .1s ease-out, opacity .1s ease-out;
        }
        @media all and (max-width: 250px) {
            #curved_bg { 
                opacity: 0; 
            }
            #secondary_content, #primary_content { 
                fill: #195463; 
            }
        }
        @media all and (max-width: 200px) {
            #secondary_content {
                opacity: 0;
            }
        }
        @media all and (max-width: 150px) {
            #inner-circle, #middle-circle {
                opacity: 0;
            }
        }
    </style>
    <path id="curved_bg" fill="#195463" d="..."/>
    
</svg>
First, I added a transition to the elements inside the SVG so that they fade in and out and change color smoothly over the course of 0.1 seconds. Then, for each media query, part of the SVG is hidden.

Check the demo out and make sure to resize the screen to see the logo adapt.

Note that at the time of writing of this article, there is a bug in Chrome where the styles specified in a media query rule set are only applied if the query condition is specified in pixels. Queries specified in em don’t currently work. So, in order to make the demo work in Chrome, the media queries are set in pixels instead of em. You can check the state of this bug in this bug report.

Changing logos like this is one of several ways to make them responsive. Some company logos may take more screen estate on small screens than necessary, so we see a lot of logos adapt to this by getting rid of the textual part of the logo and leaving only the icon. Without SVG, this would be achieved by switching PNGs on different screen sizes. Not only can the bitmap logo look bad on high-resolution screens, but having to maintain two or more images just to hide parts of the logo isn’t very efficient. This is why SVGs would be a great choice for logos—they look good everywhere, and you can select specific parts using CSS and hide them as needed without having to maintain multiple files.

Wrapping Up
SVG implementations in evergreen browsers such as Chrome and Firefox are getting better by the day; so keep in mind that some of the information in this article may change any time in the future.

Issues occurring in Internet Explorer, however, will persist due to the fact that certain versions will still be around for quite some time, so the fixes mentioned above will still hold and be useful during that time.

SVGs are pretty great when it comes to creating responsive resolution-independent logos (and other graphics, for the matter). Of course, using media queries to adapt the SVG to different media conditions goes beyond customizing logos. You can animate shapes into other shapes (you might need JavaScript to help with this in some cases), customize colors, change fonts, show and hide items, and much more. Feel free to explore the possibilities and different use cases.

I hope you found this article useful. Thank you for reading!

You can download all examples shown in this tutorial here: Responsive SVGs demos

Resources and Further Reading
More about the SVG viewport and how content is rendered inside it: Understanding SVG Coordinate Systems & Transformations (Part 1) – The viewport, viewBox, and preserveAspectratio – by me.
How Media Queries Allow You to Optimize SVG Icons for Several Sizes – An article (and screencast) by Opera’s Andreas Bovens on the Dev.Opera blog.
Do you know of any other weird browser inconsistencies regarding responsive SVGs? What about tips and use cases for adaptive SVGs? Don’t hesitate to share them in the comments section below!
