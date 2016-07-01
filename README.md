# vivus.tis

Demos:



Vivus.tis is a port of JavaScript **[Vivus.js](https://github.com/maxwellito/vivus)** library for **[Sciter/TIScript](http://sciter.com/)**. See the original library to know about what it does.

Goal is to have this API the same as in vivus.js, so you can grab any existent JS/SVG sample and have it working in Sciter just by copy/pasting. However there is still some adaptations that needs to be done, mainly adding the lengths of every SVG `<path>`.

This way, just search for any Vivus.js sample and you will be amazed with what you can achieve with SVG stroke animations: [Codepen](http://codepen.io/search/pens?q=vivus&limit=all&type=type-pens)

## Usage


```html
<script type="text/tiscript">
  new Vivus(self#my-svg, { duration: 200 }, myCallback);
</script>

<svg id="my-svg">
  <path length="100" ...>
  <path length="150" ...>
  <path length="180" ...>
</svg>
```

Sciter currently doesn't supports calculating path's length, so you need to tell in your markup the length of each path in pixels by adding a `length="123"` attribute.

The good news is that you can view any Vivus.js example in any browser, open its developer tools (F12), right-click the SVG node, and 'Copy Outer HTML' to get all the necessary SVG markup.

Notice that vivus.js adds to every `<path>` something like `style="stroke-dasharray: 566, 568; stroke-dashoffset: 0;"` where 566 is the length of the path. I manually added a procedure to extract the length from this attribute as seen [here](https://github.com/MISoftware/vivus.tis/blob/master/vivus.tis#L95), so if your SVG markup has it, you are not required to add `length="123"` attributes.

### Constructor

The Vivus constructor asks for 3 parameters:

- Element instance or CSS/string selector of the DOM SVG element to interact with.<br/>
  It can be an inline SVG or a wrapper element to append an object tag from the option `file`
- Option object (described in the following table) (optional)
- Callback to call at the end of the animation (optional)

### Option list

Options are the same as in vivus.js, however I removed some of them because they are really not necessary in Sciter.

| Name       | Type     | Description |
|------------|----------|-------------|
|`type`      | string   | Defines what kind of animation will be used: `delayed`, `async`, `oneByOne`, `script`, `scenario` or `scenario-sync`. [Default: `delayed`] |
|`file`      | string   | Link to the SVG to animate. If set, Vivus will create an object tag and append it to the DOM element given to the constructor. Be careful, use the `onReady` callback before playing with the Vivus instance. |
|`start`     | string   | Defines how to trigger the animation (`inViewport` once the SVG is in the viewport, `manual` gives you the freedom to call draw method to start, `autostart` makes it start right now). [Default: `inViewport`] |
|`duration`  | integer  | Animation duration, in frames. [Default: `200`] |
|`delay`     | integer  | Time between the drawing of first and last path, in frames (only for `delayed` animations). |
|`pathTimingFunction` | function | Timing animation function for each path element of the SVG. Check the [timing function part](#timing-function). |
|`animTimingFunction` | function | Timing animation function for the complete SVG. Check the [timing function part](#timing-function). |
|`dashGap`   | integer  | Whitespace extra margin between dashes. Increase it in case of glitches at the initial state of the animation. [Default: `2`] |
|<s>`onReady`</s>   | N/A | N/A |
|<s>`forceRender`</s> | N/A | N/A |

### Methods

Methods table is 99% equal to vivus.js one. Unlike JS, in TIScript integers and floats are distinct types, so you must respect the parameter type in each method.

| Name          | Description         |
|---------------|---------------------|
| `play(speed:Float)` | Plays the animation with the speed given in parameter. This value can be negative to go backward, between 0 and 1 to go slowly, or superior to 1 to go fast. [Default: `1.0`] |
| `stop()`      | Stops the animation. |
| `reset()`     | Reinitialises the SVG to the original state: undrawn. |
| `finish()`    | Set the SVG to the final state: drawn. |
| `setFrameProgress(progress:Float)` | Set the progress of the animation. Progress must be a Float number between 0 and 1. |
| `getStatus() : Symbol` | Get the status of the animation between `#start`, `#progress`, `#end` |
| `destroy()`   | Reset the SVG but make the instance out of order. |

These methods return the Vivus instance so you can chain the actions.

```js
var myVivus = new Vivus('#my-svg-id');
myVivus
  .stop()
  .reset()
  .play(2)
```