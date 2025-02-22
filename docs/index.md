---
layout: default
title: Node-gd Documentation
---

# API

## Version information

This is the documentation for the 2.x version. The 2.x version dropped support for `gd.createSync` and `gd.createTrueColorSync`. Also, all functions that used to accept a callback are changed. They return a Promise now. Effort is put into respecting Node's asynchronous nature by implementing N-API's AsyncWorker classes for all gd functions that utilize file I/O.

# Creating and opening graphic images

### gd.create(width, height)

#### Parameters

- `width`
  - desired width of the image to create, expects a `Number` without a fraction, e.g. `200`.
- `height`
  - desired height of the image, expects a `Number` without a fraction.

#### Return value

- `Promise`
  - The resolved `Promise` contains an instance of `gd.Image`

Create a new image in memory of specified size and returns a `Promise` for an instance of `gd.Image`. This is a paletted image, thus has a white background and a limited color range of at most 256 colors.

```javascript
const gd = require('node-gd');
const img = await gd.create(100, 100);

await img.saveJpeg('./test.jpg', 100)
img.destroy();
```

### gd.createTrueColor(width, height)

#### Parameters

- `width`
  - desired width of the image to create. Expects a `Number` without fraction, e.g. `Math.round(100 / 6)`
- `height`
  - desired height of the image

#### Return value

- `Promise`
  - The resolved `Promise` contains an instance of `gd.Image`

This is a true color image, and thus has a black background and a palette of millions of colors.

```javascript
const gd = require('node-gd');

const img = await gd.createTrueColor(100, 100);

await img.saveJpeg('./test.jpg', 100);
img.destroy();
```

### gd.createSync(width, height)

Synchronous version of `gd.create()`.

### gd.createTrueColorSync(width, height)

Synchronous version of `gd.createCreateTrueColor()`.

### gd.openJpeg(path)

#### Parameters

- `path`
  - A string referencing the JPEG image to open. Expects a `String`, e.g. `/path/to/image.jpg`

#### Return type

- `Promise`
  - The resolved `Promise` contains an instance of `gd.Image`

Open a JPG file. It will return a Promise which will resolve with an instance of `gd.Image`. This is a short hand of `gd.createFromJpeg`.

```javascript
const gd = require('node-gd');

// open Jpeg image
const image = await gd.openJpeg('/path/to/file.jpg');

// save it as a PNG image
await image.savePng('/path/to/file.png', 0);
img.destroy();
```

### gd.createFromJpeg(path)

#### Parameters

- `path`
  - A string referencing the JPEG image to open, e.g. `/path/to/image.jpg`

#### Return value

- `Promise`
  - Promise resolving to an instance of `gd.Image`

Asynchronously load an image from disk.

```javascript
const gd = require('node-gd');
var img = await gd.createFromJpeg('./path/to/image.jpg');

// do something with img

img.destroy();
```

### gd.createFromJpegPtr(data)

Load an image from a resource which is an instance of `Buffer`.

### gd.openPng(path)
Open a PNG file. Returns a Promise.

### gd.createFromPng(path)
Asynchronously load a Png from disk. Returns a Promise.

### gd.createFromPngPtr(data)
Load an image from a resource.

### gd.openGif(path)
Open a GIF file asynchronously. Returns a Promise.

### gd.createFromGif(path)
Asynchronously load a Gif from disk. Returns a Promise.

### gd.createFromGifPtr(data)

### gd.openGd2(path)
Returns a Promise.

### gd.createFromGd2(path)
Asynchronously load a Gd2 from disk. Returns a Promise.

### gd.createFromGd2Ptr(data)

### gd.openGd2Part(path, srcX, srcY, width, height[, callback])

### gd.createFromGd2Part(path, srcX, srcY, width, height)

### gd.createFromGd2PartPtr(data, srcX, srcY, width, height)

### gd.openWBMP(path)

Open a WBMP image file. [WBMP](https://en.wikipedia.org/wiki/Wireless_Application_Protocol_Bitmap_Format) stands for Wireless Application Protocol Bitmap Format, a monochrome graphics file format. Returns a Promise.

### gd.createFromWBMP(path)
Asynchronously load a WBMP from disk. Returns a Promise.

### gd.createFromWBMPPtr(data)

### gd.openBmp(path)
Open a BMP bitmap image file. Returns a Promise.

### gd.createFromBmp(path)
Asynchronously load a Bmp from disk. Returns a Promise.

### gd.createFromBmpPtr(data)

### gd.openTiff(path)
Returns a Promise.

### gd.createFromTiff(path)
Asynchronously load a Tiff from disk. Returns a Promise.

### gd.createFromTiffPtr(data)

### gd.openFile(path)

#### Parameters

- `path`
  - `String` referencing a file on disk, e.g. `/path/to/file.png`

#### Return value

- `Promise`
  - A `Promise` resolving an instance of `gd.Image`.

GD will try to find out of what type the supplied image is, and will open it likewise. This is a quite new convenience method for if you don't exactly know what the type is that you will try to open.

```javascript
const gd = require('node-gd');

const file = './image-with-some-extension';

const img = await gd.openFile(file);

// do something with img

img.destroy();
```

### gd.createFromFile(path)

Similar to `gd#openFile` but does not check if file exists. This may lead to unclear segmentation fault messages. Returns a Promise.

### gd.trueColor(red, green, blue)

#### Parameters

- `red`
- `green`
- `blue`
  - All expect a fractionless `Number` or hexadecimal number

#### Return value

- `Number`,
  - an integer representation of the supplied color values.

```javascript
const gd = require('node-gd');

var red = gd.trueColor(255, 0, 0); // 16711680
```
Alternatively, you can use the hex notation for color values for parameters for GD likewise:
```javascript
var red = 0xff0000; // red
```

### gd.trueColorAlpha(red, green, blue, alpha)
Returns an integer representation of the supplied `rgba` values. Be aware that in libgd the value for `alpha` ranges from `0` to `127`. From its source code:

```c++
[the alpha channel] occupies only the
LOWER 7 BITS of the MSB (most significant
bit). This very small loss of alpha channel
resolution allows gd 2.x to keep backwards
compatibility (...)
```

Example:

```javascript
const gd = require('node-gd');

var transparentRed = gd.trueColorAlpha(255, 0, 0, 63); // 1073676288
```

### gd.getGDVersion()

#### Return value

- `String`
  - Something like `2.1.1`

Will return a string representing the version of your currently installed GD version. Outputs something like `2.1.1`

```javascript
var gd = requide('node-gd');

if (gd.getGDVersion() >= '2.1.1') {
  const img = await gd.openFile('./test.png');

  // do something with img

  img.destroy();
}
```

# Manipulating graphic images

### gd.Image#destroy()

Free up allocated memory for image data, that is on the GD-level. The instance of gd.Image is not disposed of by this call. This is done by v8's garbage collection. In case you call any gd.Image function _after_ destroying the image, an `Error` is thrown telling you that the image has been destroyed.

```javascript
const image = await gd.create(100, 100);
image.destroy();
image.getPixel(50, 50); // will throw an error
```

### Drawing

### gd.Image#setPixel(x, y, color)
Set the color of a certain pixel. Use for color either `0xff00ff` or `gd.trueColor(255, 0, 255)`.

```javascript
const gd = require('node-gd');

const image = await gd.createTrueColor(101, 101);

image.setPixel(51, 51, 0xff00ff);

await image.savePng('./test.png', 0);
image.destroy();
```

### gd.Image#line(x1, y1, x2, y2, color)
Draw a line from a certain point to a next point.

### gd.Image#dashedLine(x1, y1, x2, y2, color)
Draw a dashed line from a certain point to a next point.

### gd.Image#polygon(array, color)
Draw a closed polygon. The first parameter shoud be an `Array` of `Object`s containing an x and y property.

```javascript
// draw a red triangle on a black background
const gd = require('node-gd');
var img = await gd.createTrueColor(100, 100);

var points = [
  { x: 10, y: 10 },
  { x: 70, y: 50 },
  { x: 30, y: 90 }
];

img.polygon(points, 0xff0000);
img.bmp('test.bmp', 0);
img.destroy();
```

### gd.Image#openPolygon(array, color)
Same as the above but start and end point will not be connected with a line.

### gd.Image#filledPolygon(array, color)
Same as the above but color value will be used to fill the polygon.

### gd.Image#rectangle(x1, y1, x2, y2, color)
Create a rectangle. The x and y values indicate upper left and lower right corner respectively.

```javascript
const gd = require('node-gd');
var img = await gd.create(300, 200);
var color0 = img.colorAllocate(255,255,255);
var color1 = img.colorAllocate(255,0,255);

img.rectangle(20, 20, 280, 180, color1);
await img.file('./test.png');

img.destroy();
```

### gd.Image#filledRectangle(x1, y1, x2, y2, color)
Create a filled rectangle i.e. a solid bar.

```javascript
const gd = require('node-gd');
var img = await gd.create(300, 200);

var color0 = img.colorAllocate(255,255,255);
var color1 = img.colorAllocate(255,0,255);

img.filledRectangle(20, 20, 280, 180, color1);

await img.file('./test.png');
img.destroy();
```

### gd.Image#arc(cx, cy, width, height, begin, end, color)
Draw an arc. `cx` and `cy` denote the center of the arc. The `begin` and `end` parameters are in degrees, from 0 to 360.

```javascript
const gd = require('node-gd');
var img = await gd.create(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);
var color2 = img.colorAllocate(0, 255, 255);

img.arc(150, 100, 200, 160, 0, 270, color1);
img.arc(150, 100, 120, 120, 90, 0, color2);

await img.file('./test.png');
img.destroy();
```

### gd.Image#filledArc(cx, cy, width, height, begin, end, color, style)
Draw a filled arc. The `style` parameter can be an integer generated by a bitwise OR of values 0, 1, 2, 4. These values stand for `arc`, `chord`, `noFill` and `edged`. A combination is possible, but `arc` and `chord` are mutually exclusive (see also the libgd documentation).

```javascript
const image = await gd.create(100, 100);
var c1 = image.colorAllocate(200, 50, 0); // red
var c2 = image.colorAllocate(0, 200, 50); // green
var c3 = image.colorAllocate(0, 50, 200); // blue

image.filledArc(50, 50, 80, 80, 0, 225, c2, 4);
image.arc(46, 46, 80, 80, 0, -90, c3);

await image.saveGif('test.gif');
image.destroy();
```

### gd.Image#ellipse(cx, cy, width, height, color)
Draw an ellipse.

### gd.Image#filledEllipse(cx, cy, width, height, color)
Draw a filled ellipse.

### gd.Image#fillToBorder(x, y, border, color)
Fill a drawn form up until its border.

```javascript
const gd = require('node-gd');
var img = await gd.create(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);
var color2 = img.colorAllocate(0, 255, 255);

img.ellipse(150, 100, 160, 160, color2);
img.fillToBorder(150,100, 2, color1);

await img.file('./test.png');
img.destroy();
```

### gd.Image#fill(x, y, color)

Create a white true color image

```javascript
var gd = requide('node-gd');
var img = await gd.createTrueColor(100, 100);
img.fill(0, 0, 0xffffff);
await img.file('./test.jpg');
img.destroy();
```

Create a transparent true color PNG

```javascript
var gd = requide('node-gd');
var img = await gd.createTrueColor(100, 100);
img.fill(0, 0, 0x7fffffff);
img.saveAlpha(1);
await img.file('./result.png');
img.destroy();
```

### gd.Image#setAntiAliased(color)

### gd.Image#setAntiAliasedDontBlend(color, dontblend)

### gd.Image#setBrush(image)

### gd.Image#setTile(image)

### gd.Image#setStyle(array)

### gd.Image#setThickness(thickness)
Set the thickness for lines.

```javascript
const gd = require('node-gd');
var img = await gd.create(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);

img.setThickness(30);
img.line(0, 0, 300, 200, color1);
img.line(0, 200, 300, 0, color1);

await img.file('./test.png');
img.destroy();
```

### gd.Image#alphaBlending(blending)
Turn on (`1`) or off (`0`) alphablending for what is being drawn next. This switch can be switched on or off during a drawing process in order to let some layer or drawn form blend or not with former drawn elements.

A newly created truecolor image for example has a black background. If you want to start out with a transparent canvas, you will first need to draw a non-blending fully transparent rectangle the size of that image. For example:

```javascript
const gd = require('node-gd');
var img = gd.createTrueColorSync(100, 100);

// turn off alpha blending
// you don't want your first filled rectangle to blend
// with the basic black background!
img.alphaBlending(0);

// you *do* want to save the full alpha channel
// i.e. I assume you want your image to be really transparent
img.saveAlpha(1);

// allocate any color as long as it's transparent!
// i.e. an alpha value of 127
var color = gd.trueColorAlpha(255, 255, 255, 127);
img.filledRectangle(0, 0, 100, 100, color);

// from here on you can add forms, texts or new images
// turn alpha blending on or off if you want the drawn
// elements to blend with other layers.
// img.alphaBlending(1);

await img.savePng('output.png', 0);
img.destroy();
```

### gd.Image#saveAlpha(saveFlag)
When set to `1`, the alpha information will be saved as a separate channel within the image. If turned off (`0`), all transparency will be handled within the image. Compare these two examples to see the difference:

```javascript
const gd = require('node-gd');
var img = await gd.createTrueColor(100, 100);
// save the full alpha channel
// the image will be transparent to e.g. a browser's background color
img.saveAlpha(1);
img.alphaBlending(0);

var color = gd.trueColorAlpha(255, 255, 255, 127);
img.filledRectangle(0, 0, 100, 100, color);

img.alphaBlending(1);

var red = gd.trueColorAlpha(255, 0, 0, 64);
var blue = gd.trueColorAlpha(0, 0, 255, 64);

img.filledRectangle(0, 0, 80, 80, red);
img.filledRectangle(20, 20, 100, 100, blue);

await img.savePng('output.png', 0);
img.destroy();
```

And with `saveAlpha` turned off

```javascript
const gd = require('node-gd');
var img = await gd.createTrueColor(100, 100);
// the image will have a white background
// but the red and blue squares will blend
// with each other and the white background
img.saveAlpha(0);
img.alphaBlending(0);

var color = gd.trueColor(255, 255, 255);
img.filledRectangle(0, 0, 100, 100, color);

img.alphaBlending(1);

var red = gd.trueColorAlpha(255, 0, 0, 64);
var blue = gd.trueColorAlpha(0, 0, 255, 64);

img.filledRectangle(0, 0, 80, 80, red);
img.filledRectangle(20, 20, 100, 100, blue);

await img.savePng('output2.png', 0);
img.destroy();
```


### gd.Image#setClip(x1, y1, x2, y2)
Set the clipping area.

### gd.Image#getClip()
Returns an object containing the coordinates of the clipping area.

### gd.Image#setResolution(res_x, res_y)
Unsigned integers should be used for `res_x` and `res_y`.

### Query image information

### gd.Image#getPixel(x, y)
For paletted images, this will return the palette index of the color for that pixel. For true color images, it will return the color value as `Number`.

```javascript
var gd = requide('node-gd');
var trueColorImage = await gd.openFile('./input.jpg');
console.log(trueColorImage.getPixel(20, 20));
// outputs something like 12205619

var paletteImage = await gd.openFile('./input.gif');
console.log(paletteImage.getPixel(20, 20));
// outputs something like 240

trueColorImage.destroy();
paletteImage.destroy();
```

### gd.Image#getTrueColorPixel(x, y)
This will return the color integer representation.

```javascript
const gd = require('node-gd');
var img = await gd.createTrueColor(300, 200);
var color = img.colorAllocate(255, 0, 255);

img.setThickness(30);
img.line(0, 0, 300, 200, color);
img.line(0, 200, 300, 0, color);

// will output ff00ff
console.log(img.getTrueColorPixel(50, 50).toString(16));

await img.file('./test.png');
img.destroy();
```

### gd.Image#imageColorAt(x, y)
This is the implementation of the PHP-GD specific method imagecolorat.

### gd.Image#getBoundsSafe(x, y)
Returns `0` if either x or y are out of the bounds of the image canvas, or `1` when within the bounds. Note that the pixel range starts at `0` for both x-axis and y-axis.

# Font and text

### gd.Image#stringFTBBox(color, font, size, angle, x, y, string)
`BBox` in this function name refers to the _bounding box_ of the supplied `string` parameter when it would be rendered on the image. This might be handy in case you want to check wether the text fits the image bounds. The return value of this function is an array of 8 elements, weirdly ordered: [xll, yll, xlr, ylr, xur, yur, xul, yul]. In ascii-art:
```
   xul           xur
yul ._____________. yur
    | Lorem ipsum |
    | dolor sit   |
yll ._____________. ylr
   xll           xlr

legend:
xul = x upper left
ylr = y lower right
```

The font color can be allocated with `img.colorAllocate(r, g, b)`. The `font` parameter should be an absolute path to a `ttf` font file.

### gd.Image#stringFT(color, font, size, angle, x, y, string, boundingbox)
`FT` refers to FreeType. Add text to the image. When the `boundingbox` parameter is set to be `true`, the return of this function is identical to `gd.Image#stringFTBBox`.

### Color handling

### gd.Image#colorAllocate(r, g, b)
Allocate a color in the color table.

### gd.Image#colorAllocateAlpha(r, g, b, a)
Allocate a color in the color table with transparency value.

### gd.Image#colorClosest(r, g, b)

### gd.Image#colorClosestAlpha(r, g, b, a)

### gd.Image#colorClosestHWB(r, g, b)

### gd.Image#colorExact(r, g, b)

### gd.Image#colorResolve(r, g, b)

### gd.Image#colorResolveAlpha(r, g, b, a)

### gd.Image#red(r)

### gd.Image#green(g)

### gd.Image#blue(b)

### gd.Image#alpha(color)
Get the alpha value of a color. *Note* after version node-gd@1.3.1 this method has been renamed to *lower case* `alpha` to reflect its function in comparison with the `gd.Image#red()`, `gd.Image#green()` and `gd.Image#blue()` methods.

### gd.Image#getTransparent()

### gd.Image#colorDeallocate(color)

### Color Manipulation

### gd.Image#colorTransparent(color)
Specifies a color index (if a palette image) or an RGB color (if a truecolor image) which should be considered 100% transparent. For truecolor images, this is ignored if an alpha channel is being saved. Use `gd.Image#saveAlpha(0)` to turn off the saving of a full alpha channel in a truecolor image.

### gd.Image#colorReplace(fromColor, toColor)
Replace one color with another. This returns the amount of changed pixels.

### gd.Image#colorReplaceThreshold(fromColor, toColor, threshold)
Replace one color with another. The `threshold` will broaden the range of replaced colors and is a decimal `Number` between `0` and `100`.

### gd.Image#colorReplaceArray(fromColors, toColors)
Replace colors in `fromColors` array to `toColors` array. The arrays should be of equal length.

# Effects

### gd.Image#grayscale()
Remove all color from an image and create a grayscaled image. Only available from libgd2 version 2.1.x.

### gd.Image#gaussianBlur()
Apply gaussian blur. Can be applied multiple times to an image to get more blur.

### gd.Image#negate()
Invert the colors of the image.

### gd.Image#brightness(brightness)
Supply an integer between `0` and `100`.

### gd.Image#contrast(contrast)
The value for contrast is a bit weird. A value of `100` wil return a complete grey image, with grey being exactly `rgb(127, 127, 127)`. For best results, the range applied should be between `-900` and `1100` (so actually `100 + 1000` and `100 - 1000`).

### gd.Image#selectiveBlur()

### gd.Image#emboss()

### gd.Image#flipHorizontal()
Flip the image on the canvas over the vertical axis. This means that what's at the right side of the images will be reflected to the left side: pixels move only horizontally.

### gd.Image#flipVertical()
Flip the image on the canvas over the horizontal axis, the oposite of the above.

### gd.Image#flipBoth()
Flip the image on the canvas over both axes.

### gd.Image#crop(x, y, width, height)
Crop the supplied image from a certain point to a certain size. Will return a new instance of `gd.Image`. Negative numbers, i.e. when going out of the image bounds, can result in images with black parts. Cropping transparent images currently does not work due to a bug in libgd. An alternative is to create the destination image first with a transparent background and then copy a portion of the source image on top of it.

### gd.Image#cropAuto(mode)
The mode parameter is a value from 0 to 4, so there are 5 modes: `0`: default: 4 corners or background, `1`: crop using the transparent color, `2`: crop black borders, `3`: crop white borders, `4`: crop using colors of the 4 corners.

### gd.Image#cropThreshold(color, threshold)
Crop by color, with a threshold. Use for color an integer and for threshold a number.

### gd.Image#sharpen(pct)
Does not work on non-true color images. A value lower than 0 for `pct` is ignored.

### gd.Image#createPaletteFromTrueColor(ditherFlag, colorsWanted)
Create and return a new palette based image instance of gd.Image. The `ditherFlag` is either `0` or `1` (a `Number`, not a `Boolean`), `colorsWanted` a value between 1 and 256.

### gd.Image#trueColorToPalette(ditherFlag, colorsWanted)
Modify the current image into a palette based image. The `ditherFlag` is either `0` or `1` (a `Number`, not a `Boolean`), `colorsWanted` a value between 1 and 256.

### gd.Image#paletteToTrueColor()
Modify the current image, if it is a palette based image, into a true color image. From libgd version `2.1.0` this returns either `0` or `1`, in version `2.0.26` it is void.

### gd.Image#colorMatch(gd.Image)
The parameter should be a palette based image, which will be modified and which can be saved afterwards. The return value is a `Number`. This method tries to better match the colors from the palette based image to those of its true color original. Will throw an `Error` in these cases:

* Images are not of identical dimensions
* The from image is not truecolor
* The target image is not a palette image
* Target image has no colors allocated in the palette

```javascript
const gd = require('node-gd');

// open a true color image
const trueColor = await gd.openFile('/path/to/image.jpg');

// create a new palette based image
var palette = trueColor.createPaletteFromTrueColor(1, 128);

// match colors to enhance the result of the palette image
trueColor.colorMatch(palette);

// save the palette based image
await palette.file('/path/to/result.gif');
palette.destroy();
```

### gd.Image#gifAnimBegin(anim, useGlobalColorMap, loops)
Create an animated GIF.

* `anim` is a `String` referencing a filename to be created, like `./anim.gif`
* `useGlobalColorMap` a value being `-1`, `0` or `1` which determines if the colorMap of the current image will be used as the global colorMap for the whole animation. `-1` indicates `do default`.
* The value for `loops` can be `-1` for no looping, `0` means infinite looping, any other value the amount of loops the animation should run.


### gd.Image#gifAnimAdd(anim, localColorMap, leftOffset, topOffset, delay, disposal, prevFrame)


* Add current image to the specified `anim`.
* `localColorMap` is a flag indicating wether GD should use this image's colorMap
* `leftOffset` and `topOffset` indicate the offset of this frame
* the `delay` is the delay before next frame (in 1/100 sec)
* `disposal` defines how this frame is handled when the next frame is loads. Usually this value should be set to `0`, quote from gd's source: _meaning that the pixels changed by this frame should remain on the display when the next frame begins to render_
* `prevFrame` should refer to the previous frame. If the current image is the first frame, supply `null`.


### gd.Image#gifAnimEnd(anim)
Write and close the GIF animation. A complete working example could look like this:

```javascript
const gd = require('node-gd');
var anim = './anim.gif';

// create first frame
var firstFrame = await gd.create(200,200);

// allocate some colors
var whiteBackground = firstFrame.colorAllocate(255, 255, 255);
var pink = firstFrame.colorAllocate(255, 0, 255);

// create first frame and draw an ellipse
firstFrame.ellipse(100, -50, 100, 100, pink);
// start animation
firstFrame.gifAnimBegin(anim, 1, -1);
firstFrame.gifAnimAdd(anim, 0, 0, 0, 5, 1, null);

var totalFrames = [];
for(var i = 0; i < 30; i++) {
  totalFrames.push(i);
}

totalFrames.forEach(async (i, idx, arr) => {
  var frame = await gd.create(200, 200);
  arr[idx] = frame;
  frame.ellipse(100, (i * 10 - 40), 100, 100, pink);
  var lastFrame = i === 0 ? firstFrame : arr[i - 1];
  frame.gifAnimAdd(anim, 0, 0, 0, 5, 1, lastFrame);
  frame.destroy();
});

firstFrame.gifAnimEnd(anim);
firstFrame.destroy();
```

# Copying and resizing

### gd.Image#copy(dest, dx, dy, sx, sy, width, height)
Copy an image onto a destination image: `dest`. You'll have to save the destination image to see the resulting image. This method returns _the image on which it is called_ in order to be able to chain methods.

```javascript
const gd = require('node-gd');
var output = '../output/image-watermark.png';

var watermark = await gd.createFromPng('../input/watermark.png');
var input = await gd.createFromPng('../input/input.png');

watermark.alphaBlending(1);
watermark.saveAlpha(1);

// copy watermark onto input, i.e. onto the destination
watermark.copy(input, 0, 0, 0, 0, 100, 100);

// save the destination
await input.savePng(output, 0);
input.destroy();
watermark.destroy();
```

Example using chaining:

```javascript
const gd = require('node-gd');
var output = '../output/image-watermark.png';
var output2 = '../output/image-watermark2.png';

var watermark = await gd.createFromPng('../input/watermark.png');
var input = await gd.createFromPng('../input/input.png');
var input2 = await gd.createFromPng('../input/input2.png');

watermark.alphaBlending(1)
.saveAlpha(1)
.copy(input, 0, 0, 0, 0, 100, 100)
.contrast(-900)
.copy(input2, 0, 0, 0, 0, 100, 100)

await input.savePng(output);
await input2.savePng(output2, 0);
```


### gd.Image#copyResized(dest, dx, dy, sx, sy, dw, dh, sw, sh)

### gd.Image#copyResampled(dest, dx, dy, sx, sy, dw, dh, sw, sh)

### gd.Image#copyRotated(dest, dx, dy, sx, sy, sw, sh, angle)

### gd.Image#copyMerge(dest, dx, dy, sx, sy, width, height, pct)

### gd.Image#copyMergeGray(dest, dx, dy, sx, sy, width, height pct)

### gd.Image#paletteCopy(dest)

### gd.Image#squareToCircle(radius)

# Misc

### gd.Image#compare(image)

### Saving graphic images
The functions `gd.Image#savePng`, `gd.Image#saveJpeg`, `gd.Image#saveGif`, etc. are convenience functions which will be processed asynchronously when a callback is supplied. All of the following have a counterpart like `gd.Image#png` and `gd.Image#pngPtr` which write to disk synchronously or store the image data in a memory pointer respectively. `gd.Image#jpeg` will return the instance of `gd.Image`, `gd.Image#jpgPtr` will return the newly created image data.

```javascript
const gd = require('node-gd');
gd.openPng('/path/to/input.png', async function(err, img) {
  if (err) {
    throw err;
  }

  // create jpg pointer from png
  var jpgImageData = img.jpegPtr(0); // jpeg quality 0
  // create gif pointer from png
  var gifImageData = img.gifPtr();

  // create instance of gd.Image() for jpg file
  var jpgImage = gd.createFromJpegPtr(jpgImageData);
  await jpgImage.file('./test01.jpg');

  // create instance of gd.Image() for gif file
  var gifImage = gd.createFromGifPtr(gifImageData);
  await gifImage.file('./test01.gif');
  img.destroy();
});
```
The above example shows how to create a JPEG and GIF file from a PNG file.

### gd.Image#savePng(path, level[, callback])
Save image data as a PNG file. The callback will receive an error object as a parameter, only if an error occurred. When a callback is supplied, the image will be written asynchronously by `fs.writeFile()`, using `gd.Image#pngPtr()` to first write it to memory in the given format. `level` can be value between `0` and `9` and refers to a zlib compression level. A level of `-1` will let libpng12 decide what the default is.

### gd.Image#saveJpeg(path, quality[, callback])
Save image data as a JPEG file. The callback will receive an error object as a parameter. Quality can be a `Number` between `0` and `100`.

### gd.Image#saveGif(path[, callback])
Save image data as a GIF file.

### gd.Image#saveGd(path[, callback])
Save image data as a GD file.

### gd.Image#saveGd2(path, chunkSize[, format][, callback])
Save image data as a GD2 file.

### gd.Image#saveWBMP(path, foreground[, callback])
Save image as a 2 color WBMP file. `foreground` is an integer value or `0x000000` value which defines the dark color of the image. All other colors will be changed into white.

### gd.Image#saveBmp(path, compression[, callback])
Only available from GD version 2.1.1. The compression parameter is eiterh `0` for no compression and `1` for compression. This value only affects paletted images.

### gd.Image#saveTiff(path[, callback])
As per libgd 2.2.4, opening TIFF files appears to be fixed, and saving image data as TIFF worked already fine. Therefore, `gd.openTiff()` is available again. Only available from GD version 2.1.1.

### gd.Image#file(path)
Lets GD decide in which format the image should be stored to disk, based on the supplied file name extension. Only available from GD version 2.1.1. Returns a Promise.

### Image properties
Any instance of `gd.Image()` has a basic set of instance properties accessible as read only values.

```javascript
const gd = require('node-gd');
var img = gd.creatTrueColor(100,100);
console.log(img);
img.destroy();
```

Will yield to something like:

```javascript
{
  colorsTotal: 0,
  interlace: true,
  height: 100,
  width: 100,
  trueColor: 1
}
```

### gd.Image#colorsTotal
For paletted images, returns the amount of colors in the palette.

### gd.Image#interlace
`Boolean` value for if the image is interlaced or not. This property can also be set. When set to `true` for Jpeg images, GD will save it as a progressive Jpeg image.

```javascript
const gd = require('node-gd');
var img = await gd.createTrueColor(100,100);
img.interlace = true; // set interlace to true

// will save jpeg as progressive jpeg image.
await img.saveJpeg('/path/to/output.jpg', 100);
img.destroy();
```

### gd.Image#height
Returns the height of the image as `Number`.

### gd.Image#width
Returns the width of the image as `Number`.

### gd.Image#trueColor
Returns nonzero if the image is a truecolor image, zero for a palette image.

# libgd2 version information
Be aware that since `node-gd` version 0.3.x libgd2 version 2.1.x is mostly supported. `node-gd` version 0.2.x is backed at best with libgd2 version 2.0.x. Run `gdlib-config --version` to check the version of libgd2 on your system. `node-gd` should build successfully for both libgd2 version 2.0.x as wel as for 2.1.x. The main difference is that some functions will not be available. These include:

* `toGrayscale()`
* `emboss()`
* `gaussianBlur()`
* `negate()`
* `brightness()`
* `contrast()`
* `selectiveBlur()`
* `openBmp()`
* `openFile()`
* `createFromBmp()`
* `createFromBmpPtr()`
* `createFromFile()`
* `saveBmp()`
* `saveTiff()`
* `saveImage()`

Another way to check the installed GD version on your system:

```javascript
const gd = require('node-gd');

// as of node-gd 0.4.x
var version = gd.getGDVersion();
console.log(version); // 2.0.1 or 2.1.1 or the like
```

# Test

```bash
$ npm test
```
The `test/output/` directory contains the resulting images of the test script. The tests use, in some cases, a GNU Freefont font, which is licensed under the GNU General Public License v3.


# Related

* [Original author's repo](https://github.com/taggon/node-gd)
* [node-canvas](https://github.com/LearnBoost/node-canvas) uses libcairo to emulate browser HTML5 Canvas' image manipulation abilities within Node.js

