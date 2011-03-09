ChromaGL
========
Chroma Key video effect in a web page.

Two modes:
- `chroma`: keys out pixels based on color within euclidean distance from target color
- `pre`: video frame is split in half, containing source image (usually top half) and an alpha mask (usually bottom half).  Eventually will support up to three alpha masks (one in each color channel).

Requirements
------------
**ChromaGL** requires a browser with WebGL ([Chrome 9+, Firefox 4+, Safari nightly or Opera 11 for Windows](http://www.khronos.org/webgl/wiki/Getting_a_WebGL_Implementation)).

The method `hasWebGL()` is provided to test whether browser supports WebGL.  If not, constructor will throw an error unless `errorCallback` function is provided in options.

Basic Usage
-----------
For a video with a green background, on a page with one `<video>` element and one `<canvas>`

	window.addEventListener('load',function() {
		var chroma = new ChromaGL('video', 'canvas');
		chroma.addChromaKey('green');
		chroma.go();
	}, false);

API
---
`ChromaGL(source, target, options)`  
Object constructor

- source = canvas/img/video object (or selector string)
- target = canvas object (or selector string)
- options (not required):
	* clip = object containing `x`, `y`, `width` and `height` representing clipping area (all parameters are 0 to 1, as factor of full image dimensions)
	* alpha = object containing `x`, `y`, `width` and `height` representing part of image containing alpha mask
	* source = object containing `x`, `y`, `width` and `height` representing part of image containing source to be keyed (for use with alpha)
	* errorCallback: callback function to be called in case of error

`.hasWebGL()`: returns true if browser supports WebGL, else false  
`.source(source)`: Set source containing video or image to key. Can be changed after object creation.  
- source = canvas/img/video object (or tag/id string)

`.target(target)`: Set target canvas on which to paint keyed image. Can be changed after object creation.  
- target = canvas object (or tag/id string)

`.addChromaKey(keys, channel)`  
- keys = array or single of key parameters, any of the following formats:
	- string: 'pre' = pre-computed alpha channel, defaults to 
	- string: 'blue' or 'green' color preset
	- array of 3 numbers: RGB color
	- object: params for color/pre-rendered key
		- mode (required): 'pre' for pre-computed or 'chroma' for color-based
		- color (required): 'blue' or 'green' or array of 3 numbers (chroma mode only)
		- threshold: Euclidean distance cutoff (chroma mode only)
		- fuzzy: float >= 1.0, multiple of threshold as outer limit of feathering (chroma mode only)
		- source: which channel contains alpha mask. 0 = red, 1 = blue, 2 = green (pre mode only)
		- channel: select an output channel (overrides `channel` parameter to method)
- channel: select an output channel 0 = red, 1 = blue, 2 = green

returns array of key id's, in case you want to remove them later

`.removeChromaKey(id)`  
- id = single or array of integers of keys to delete

`.setThreshold(id, threshold, fuzzy)`: Change chroma key parameters for distance threshold  
- id = key to modify
- threshold = Euclidean distance cutoff
- fuzzy (optional) = float >= 1.0, multiple of threshold as outer limit of feathering

`.go(frameRate, play)`: Start a draw loop that updates and paints keyed image to canvas for every frame  
- frameRate (optional) = frames per second
- play (optional) = true/false, should we start playing the video

`.stop(pause)`: Stop draw loop  
- pause (optional) = true/false, should we pause the video
  
`.refresh(clear, noThrottle)`: Update frame from video and paint to canvas  
- clear: true/false whether to clear the canvas first
- noThrottle: true/false. Unless true, refresh will only paint canvas if there is a new frame or chroma parameters have changed
  
`.paint()`: Paints current video frame to target canvas  

`.draw(image, target, channel, invert)`:  Not implemented yet.  Will allow painting of images to canvas using partial alpha masks from different channels
- invert = bool, invert alpha mask

Notes
-----

To Do
-----
* Add WebGL framebuffer/texture to acceptable source media types
* Add WebGL framebuffer/texture as acceptable target instead of canvas
* Currently assumes target canvas is WebGL context. Allow painting to 2d context
* Provide more complete examples
* implement `draw()`
* Fall back to 2d canvas if WebGL is not supported. Will require much smaller video unless browsers change implementation of `getImageData`
* Add method for changing clipping area
* Allow clipping area for individual keys
* Add method/option for scaling?
* Add method for modifying source/alpha dimensions?
* Allow external static image as alpha mask?
* Alternate color spaces (currently uses YUV)
* Add key mode: luminance
