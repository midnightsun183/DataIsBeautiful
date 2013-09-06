Say you have an SVG built dynamically on the client by [D3](http://d3js.org). Now say you want to save it to a static PNG as a fallback for old browsers. There are lots of ways to do this using alternative renderers (such as [Batik](http://xmlgraphics.apache.org/batik/) and [PhantomJS](http://phantomjs.org/)), but what if you want your browser to do it, so that you the static PNG matches the dynamic SVG exactly, down to the subpixel?

Here’s how.

## 1. Save as SVG.

Use the [SVG Crowbar](http://nytimes.github.io/svg-crowbar/) bookmarklet to save the SVG to a file. Call this file “fallback.svg”. Open the SVG in your browser to verify that it looks right.

SVG Crowbar helpfully inlines any stylesheets you may have on the page, but you might find you need to edit a few of the styles by hand to get things to look right. For example, the font-family “sans-serif” won’t work in an SVG image, even though it works when the SVG is built client-side; you’ll have to make the font name explicit, such as “Helvetica” or “Arial”.

## 2. Convert SVG to PNG.

Create and save the following HTML file as “fallback.html”:

```html
<!DOCTYPE html>
<meta charset="utf-8">
<canvas width="640" height="3250"></canvas>
<script>

var canvas = document.querySelector("canvas"),
    context = canvas.getContext("2d");

var image = new Image;
image.src = "fallback.svg";
image.onload = function() {
  context.drawImage(image, 0, 0);

  var a = document.createElement("a");
  a.download = "fallback.png";
  a.href = canvas.toDataURL("image/png");
  a.click();
};

</script>
```

Now start a local webserver in the same directory as your fallback.html file. For example:

```bash
python -m SimpleHTTPServer 8008 &
```

Lastly, visit <http://localhost:8008/fallback.html>, and it will automatically download fallback.png! Hooray.

Note: if you are using Chrome or Safari, you’ll need to download [Chrome Canary](https://www.google.com/intl/en/chrome/browser/canary.html) due to this [recently-fixed WebKit bug](https://bugs.webkit.org/show_bug.cgi?id=119492).