# dropdown click



Classical way is to use CSS “sticky” to fix header on top of screen. 

Main pro: it’s only a css (plus you have to add some positioning relative, absolute...). That’s working well in general, sometimes you encounter problems: overflow hidden, event not working, position...

Why using javascript? 

Here it's a click (not onHover), so you will have javascript to respond the user click. The idea is to add some javascript to have a bullet proof dropdown working in all situations.


Intersection Observer API concept is very well explained here

https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API

If you search, you'll find lot of posts explaining Intersection Observer (lot of them are copy of this one!). 

Intersection Observer gives a callback when the element observed intersect (in and out), that’s it: no continuous listener while scrolling, low resource, and easy.


---
[Test it on CodePen](https://codepen.io/pierfarrugia/pen/mdLOGBY)

![alt text](https://aonecommunication.ch/content/intersection_observer_header_fixed/headerFixed.webp)

[Read this on HTML page](https://aonecommunication.ch/blog.html#intersection_observer_header_fixed)

[See it in action full screen](https://aonecommunication.ch/content/intersection_observer_header_fixed/io_header_01.html)



---
## HTML 

Very simple HTML structure:

- first section heroe

- header

- 2 sections (to have something to scroll)

- and a footer



---
## CSS

- section (including heroe) have 100vh height (to have some scroll), plus odd, even background to visually see the difference

- header and footer black background, header 75px height, footer 50px


Now we’ll add the HTML element to be observed by intersection observer. It’s just an empty element and it will sit just before header:

```html
<div class="sentinelHeader"></div>
```

We also have to add 2 CSS, “fixed-top” to fix the header, and “scrolled-offset”.

Fixed-top is easy to understand, it’s to fix the header on top of screen.
```css
.fixed-top {
	position: fixed;
	top: 0;
	right: 0;
	left: 0;
}
```

Scrolled-offset is to add margin-top 75px (header height) to next element after header to have a smooth transition when the header is fixed. Body background color is same color as header background color to avoid a visual flash when offset is active. Now you can use another color to have a visual smooth transition.
```css
.scrolled-offset {
	margin-top: 75px;
}
```


---
## Javascript

We have to observe the sentinelHeader.
```javascript
const initOberverHeader = () => {
    observerHeader.observe(document.querySelector('.sentinelHeader'));
}
```

We call function observerHeader with the element sentinelHeader.

### Now the big part the observer function:

```javascript
const observerHeader = new IntersectionObserver(function (entries, self) {
let selectHeader = document.querySelector('#header');
let nextElement = selectHeader.nextElementSibling;
if (window.scrollY > 50) {
	entries.forEach(entry => {
		if (entry.isIntersecting) {
			selectHeader.classList.remove('fixed-top');
			nextElement.classList.remove('scrolled-offset');
		} else {
			selectHeader.classList.add('fixed-top');
			nextElement.classList.add('scrolled-offset');
		}
	});
}
}, { root: null, threshold: 0, rootMargin: '0px' });
```


1. **last part: intersection observer options**

- 'root: null' is the standard, you don’t really need this line (null is viewport)

- 'threshold: 0', meaning when the element is 0% visible, what we are checking. Standard here is 1, you need this line

- 'rootMargin: '0px'', in fact default is ‘0px’, so you don’t really need this line

2. going back to the function: we select the 2 elements needed, the Header and the next element, here "section 1"

3. scrollY > 50, safe value! Strange behaviour you can encounter on mobile browser, and/or Chrome. Initial rendering in browser happens with its own user agent before your own css arrives. On mobile, nav bar can be top or down initially, in Chrome you have margin 8px. Intersect observer gives not intersecting if 0 so header is fixed initially. With this test it's working well.
4. entries: loop through the entries (sentinelHeader)

5. checking if entry is intersecting

6. if intersecting (sentinelHeader in viewport), remove class fixed-top and scrolled-offset

7. if not intersecting (sentinelHeader outside viewport), add class fixed-top and scrolled-offset


---
Now we just have to call the function, for example when window is loading:
```javascript
window.addEventListener('load', initOberverHeader);
```

Thanks for reading

*(tested on Chrome, Firefox, Edge, Safari)*
