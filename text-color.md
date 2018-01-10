# Changing Text Color Based on Background Color
## A menu button case study

Common design pattern - text plus hamburger icon.
Approach applies to *just* hamburger icon too.
I started the article with the assumption the Menu + Hamburger is better than just hamburger. More research tells me ... don't worry about it too much.

http://sitesforprofit.com/hamburger-is-ok

We wanted a sticky menu button -

```
.menu-icon {
  position: fixed;
}
```

But ran into legibility trouble.

As we scrolled, the button would become illegible (and back), dependent on the background color.

For this site, we didn't have to consider images as backgrounds. Although we used images for some section backgrounds, I used a fallback background-color setting that matched the images dominant color.

More advanced version could sample the image color underneath .menu-icon.

### How to get `background-color` using only JS.

Using only JS -

```
const sections = document.querySelectorAll('section');
const menu = document.querySelector('.menu-icon');

for (let i = 0; i < sections.length; i++) {

  const backgroundColor = window.getComputedStyle(section, null).getPropertyValue('background-color');

}
```

Note: I was using `sections.forEach` at first, but of course IE didn't like that. `getComputedStyle` is not supported on IE8 or below. Obviously this would have been easier with jQuery, but I have fun trying to solve things in vanilla JS.

Returns RGB value, e.g. rgb(153, 255, 51);

OK, so now we have the background color. Time to figure out the best contrasting color. *True* contrasting color can be quite ugly. Honestly, all text should either be light or dark. Colored text is for links. To keep things simple and pretty, we stayed with black or white text as the only options.

Here's the code -

```
// Color contrast helper function
// http://dannyruchtie.com/color-contrast-calculator-with-yiq/
function getContrastYIQ(rgb) {
  rgb = rgb.substring(4, rgb.length - 1)
    .replace(/ /g, '')
    .split(',');
  const yiq = ((rgb[0] * 299) + (rgb[1] * 587) + (rgb[2] * 114)) / 1000;
  return (yiq >= 128) ? 'black' : 'white';
}
```

I'm using a modified version of Danny Ruchie's contrast calculator to convert RGB to YIQ ([explain why]) and deriving the black or white choice from that.

Getting the right answer isn't enough though. Next, we must implement.

I used the nice ScrollMagic library by ... to set triggers on each section. Let's expand the for loop above

```

// Init ScrollMagic
const controller = new ScrollMagic.Controller();


const sections = document.querySelectorAll('section');
const menu = document.querySelector('.menu-icon');

for (let i = 0; i < sections.length; i++) {

  const trigger = '#' + section.id;
  const backgroundColor = window.getComputedStyle(section, null).getPropertyValue('background-color');
  const textColor = getContrastYIQ(backgroundColor);

  new ScrollMagic.Scene({
      triggerElement: trigger,
      triggerHook: "onLeave",
      offset: -50,
      reverse: true
    })
    .on("enter", function(event) {
      // Handle color change
    }
    .on("leave", function() {
      // Handle color change
    })
    .addTo(controller);
}
```

Pretty simple.
