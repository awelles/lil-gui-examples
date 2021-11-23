# lil-gui-examples

```js
const params = {
    numberStr1: '0.5',
    numberStr2: '0.5',
    rgbaStr: 'rgba(255,0,0,0.5)',
    rrggbbaaStr: '#ff0000aa'
}
```

### Object with a string type number
```js
/*
    Preserving string type method 1:
    Create an intermidiate object, use onChange to update original params object
    Works fine, but .listen() ability is lost
*/
const obj1 = { numberStr1: parseFloat( params.numberStr1 ) };
gui.add( obj1, 'numberStr1', 0, 1 ).step( 0.01 )
    .onChange( () => {
        params.numberStr1 = obj1.numberStr1.toString();
    } )
    .listen();  // .listen() doesn't work
```

```js
/*
    Preserving string type method 2:
    Create an intermidiate object with getter/setter
    .listen() works, converting on every getValue
    .onChange()/onFinishChange() are still free, as conversion is invisible to controller
*/
const obj2 = {};
Object.defineProperties( obj2, {
    numberStr2: {
        get: function () { return parseFloat( params.numberStr2 ); },
        set: function ( value ) { params.numberStr2 = value.toString(); }
    },
} );
gui.add( obj2, 'numberStr2', 0, 1 ).step( 0.01 )
    .listen();  // .listen() works
```

### Object with a rgba string color
```js
/*
    Preserving #rgba string:
*/
const obj3 = { rgbaStr: params.rgbaStr.slice( 5, -1 ).split( ',' ) };
gui.addColor( obj3, 'rgbaStr', 255 )
    .onChange(
        value => {
            params.rgbaStr = `rgba(${ value.join() })`;
        }
    );

/*
    Add a NumberController for alpha
*/
const obj3a = { alpha: parseFloat( params.rgbaStr.slice( 5, -1 ).split( ',' )[ 3 ] ) };
gui.add( obj3a, 'alpha', 0, 1 )
    .onChange(
        value => {
            let rgbaArray = params.rgbaStr.slice( 5, -1 ).split( ',' );
            rgbaArray[ 3 ] = value;
            params.rgbaStr = `rgba(${ rgbaArray.join() })`;
        }
    );
```

### Object with a #rrggbbaa string color
```js
/*
    Preserving #rrggbbaa string:
*/
const obj4 = { rrggbbaaStr: params.rrggbbaaStr.slice( 0, -2 ) };
gui.addColor( obj4, 'rrggbbaaStr' )
    .onChange( value => {
        params.rrggbbaaStr = value + params.rrggbbaaStr.slice( -2 );
    } );

/*
    Add a NumberController for alpha
*/
const obj4a = { alpha: parseInt( params.rrggbbaaStr.slice( -2 ), 16 ) };
gui.add( obj4a, 'alpha', 0, 255 ).step( 1 )
    .onChange( value => {
        params.rrggbbaaStr = params.rrggbbaaStr.slice( 0, -2 ) + value.toString( 16 ).padStart( 2, '0' );
    } );
```