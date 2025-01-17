# react-pan-and-zoom-hoc

React HOC that helps with panning and zooming elements

![Example](https://user-images.githubusercontent.com/5159398/61330478-f89cf800-a81f-11e9-960e-f5ab56f86a4f.gif)

## Installation

**Node**

```bash
npm install --save react-pan-and-zoom-hoc
```

**Browser**

Include `lib/panAndZoomHoc.js` with RequireJS.

## Building

```bash
git clone https://github.com/woutervh-/react-pan-and-zoom-hoc.git
npm install
npm run build
```

## Running example locally

```bash
git clone https://github.com/woutervh-/react-pan-and-zoom-hoc.git
npm install
npm run build
npm run examples
# Now you can open http://localhost:8888/ to view the example.
```

## Options

* `x` (number, default: `0.5`): initial x-coordinate that represents the horizontal center of the viewport.
* `y` (number, default `0.5`): initial y-coordinate that represents the vertical center of the viewport.
* `scale` (number, default `1`): initial scale of the viewport.
* `scaleFactor` (number, default `√2`): when zooming in or out, use this factor to multiply the scale by.
* `minScale` (number, default: `Number.EPSILON`): minimal allowed value for the scale.
* `maxScale` (number, default: `Number.POSITIVE_INFINITY`): maximal allowed value for the scale.
* `renderOnChange` (boolean, default: `false`): if `true`, when panning or zooming, it will force a re-render of the component.
* `passOnProps` (boolean, default: `false`): if `true`, will pass on the `x`, `y`, and `scale` props to the wrapped component. If `renderOnChange` is also set to `true` this will cause the props (with updated values) to be passed on every time a pan or zoom event occurs.
* `ignorePanOutside` (boolean, default: `false`): if `true`, when the mouse exits the element the panning will stop. When the mouse re-enters the element, and the mouse button is still down, then panning will resume.
* `disableScrollZoom` (boolean, default: `false`): if `true`, the scroll wheel zooming functionality will be disabled.
* `zoomEndTimeout` (number, default: `500`): after the user has used the scroll wheel to pan/zoom, the component will wait `zoomEndTimeout` milliseconds and then fire `onZoomEnd`. If the user uses the scroll wheel before this time has passed, the timeout will reset.
* `onPanStart` (function, optional): invoked when the component starts to pan. Receives the following arguments:
    * `event` (MouseEvent): original event which triggered the panning to start.
* `onPanMove` (function, optional): invoked when the component pans in the x or y direction. Receives the following arguments:
    * `x` (number): new x-coordinate.
    * `y` (number): new y-coordinate.
    * `event` (MouseEvent): original event which triggered the panning movement.
* `onPanEnd` (function, optional): invoked when the component stop panning. Receives the following arguments:
    * `x` (number): new x-coordinate.
    * `y` (number): new y-coordinate.
    * `event` (MouseEvent): original event which triggered the panning to stop.
* `onZoom` (function, optional): invoked when the user zooms using the scroll wheel. Receives the following arguments:
    * `x` (number): old x-coordinate.
    * `y` (number): old y-coordinate.
    * `scale` (number): old scale value.
    * `event` (WheelEvent): original event which triggered the pan/zoom event.
* `onZoomEnd` (function, optional): invoked when `zoomEndTimeout` amount of milliseconds has passed since the last scroll event.
* `onPanAndZoom` (function, optional): invoked when the component pans and zooms (for example when the mouse wheel is used). Receives the following arguments:
    * `x` (number): new x-coordinate.
    * `y` (number): new y-coordinate.
    * `scale` (number): new scale value.
    * `event` (WheelEvent): original event which triggered the pan/zoom event.

## Unmanaged example

```js
import React from 'react';
import panAndZoomHoc from 'react-pan-and-zoom-hoc';

class Figure extends React.Component {
    render() {
        const {x, y, scale, width, height, ...other} = this.props;
        return <div style={{width, height, overflow: 'hidden', border: '1px solid black'}}>
            <img style={{transform: `scale(${scale}, ${scale}) translate(${(0.5 - x) * width}px, ${(0.5 - y) * height}px`}} width={width} height={height} {...other}/>
        </div>;
    }
}

const PannableAndZoomableFigure = panAndZoomHoc(Figure);

class App extends React.Component {
    render() {
        return <PannableAndZoomableFigure
            renderOnChange={true}
            passOnProps={true}
            src="http://lorempixel.com/400/200/"
            width={400}
            height={200}
        />;
    }
}
```

## Managed example

```js
import React from 'react';
import panAndZoomHoc from '../src/panAndZoomHoc';

const InteractiveDiv = panAndZoomHoc('div');

class App extends React.Component {
    state = {
        x: 0.5,
        y: 0.5,
        scale: 1
    };

    handlePanAndZoom(x, y, scale) {
        this.setState({x, y, scale});
    }

    handlePanMove(x, y) {
        this.setState({x, y});
    }

    render() {
        const {x, y, scale} = this.state;

        return <InteractiveDiv
            x={x}
            y={y}
            scale={scale}
            scaleFactor={Math.sqrt(2)}
            minScale={1}
            maxScale={2 ** 18}
            onPanAndZoom={(x, y, scale) => this.handlePanAndZoom(x, y, scale)} style={{width: 250, height: 250, border: '1px solid black', position: 'relative'}}
            onPanMove={(x, y) => this.handlePanMove(x, y)}
        >
            <div style={{position: 'absolute', top: `${y * 100}%`, left: `${x * 100}%`, width: 1, height: 1, backgroundColor: 'black'}}/>
        </InteractiveDiv>;
    }
}
```
