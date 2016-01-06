# React Native Material Design Demo App [Android]

This repository contains a React Native project, implementing the [react-native-material-design](https://github.com/react-native-material-design/react-native-material-design) package.

> Please submit all issues to [react-native-material-design](https://github.com/react-native-material-design/react-native-material-design/issues).

## Installation

### Development

1. Clone this repo.
2. Run `npm install`.
3. Ensure a device, or emulated Android image is connected (`adb devices`).
4. Run `react-native run-android`.

> If running on a device, run `adb reverse tcp:8081 tcp:8081`.

### APK

This APK is in Debug mode for now. Will move it into **Production mode**(which means, it will be faster and more optimized) soon.

[Download APK](https://github.com/react-native-material-design/demo-app/blob/master/demo-app.apk?raw=true)

## Can I use this as a base project?

Of course. Please remember though it is not a "fit all" solution and you'll need to configure it for your own project needs.

## How it works

This Android only demo app has been designed to be as minimal and basic as possible, to avoid confusion.

Key points:
- It's only working on Android for now.
- [Alt](http://alt.js.org) is used as a light weight flux implementation to handle application state.
- A custom `Navigate` class has been made to handle route navigation. It's a wrapper around the native 'Navigator' API, and does not force any set ways of working - see below for more information.
- Some aspects are dependant on React's [context](https://facebook.github.io/react/docs/context.html).

Each scene is located within `./src/scenes`, while the Toolbar is global to the entire app and is located within `./src/components`. The main entry point of the app is within `index.android.js`,
here the app undergoes a few cycles of component mounting, wrapping the scenes within a [`Navigator`](https://facebook.github.io/react-native/docs/navigator.html), which is wrapped within a [DrawerLayoutAndroid](https://facebook.github.io/react-native/docs/drawerlayoutandroid.html#content).

The refs for the `drawer` and `navigator` are created as application context, and are available when needed throughout the app. The `navigator` context however is an implementation of a custom `Navigate` class
found in `./src/utils/Navigate`, which allows for navigation around the app, and 'down' children components.

### Navigate

The Navigate class needs to be instantiated on application boot, with a reference to React's `Navigator` API passed. An example of this can be found [here]().

Once instantiated, the class exposed a few methods to help with simple app navigation. First however, we must define our routes. The class looks for a `routes.js` file in the root of the `src` directory.

#### routes.js

This is a simple object based pattern of component and children, an example of which can be found [here]().

Top level objects are considered to be the "root" scenes of your app, and are defined by object key name. Each object should contain a name and component, for example:

```
export default {

    root: {
        name: 'Root Scene',
        component: require('./scenes/SomeRootScene').default
    }

}
```

You can have as many root objects as you like.

If you wish to define some children of this route, simply add a `children` object, with the same pattern as above, for example:

```
export default {

    root: {
        name: 'Root Scene',
        component: require('./scenes/SomeRootScene').default,

        children: {
            child: {
                name: 'Child Scene',
                component: require('./scenes/SomeChildScene').default,
            }
        }
    }

}
```

You can have as many children, and children of children as you like.

#### API

##### `static init()`

Can be called without class instantiation, and is used to get a base route. This is handy for configuring initial routes, for example [here]().

```
Navigate.init('root');
```

> This can only be used with root routes.

##### `to(path, name, props)`

Used to directly access any route from any location.

```
Navigate.to('root.child', 'Custom Name', { some: 'prop' });
```

`path`: <string> Required path to a route.
`name`: <string> Optional name to use for the route. If left blank, the name from the `routes.js` file will be used.
`props`: <object> Option object of props to pass to the next scene.

##### `back(name, props)`

Used when going to the parent of the current route.

```
Navigate.back('Custom Name', { some: 'prop' });
```

`name`: <string> Optional name to use for the route. If left blank, the name from the `routes.js` file will be used.
`props`: <object> Option object of props to pass to the parent scene.

##### `forward(path, name, props)`

Used when going to a child of the current route.

```
// Assuming the current route is "root":
Navigate.forward('child', 'Custom Name', { some: 'prop' });
Navigate.forward(null, 'Custom Name', { some: 'prop' });
```

`path`: <string> Optional path to a child route. If no path is supplied, the first child of the parent defined in `routes.js` will be used.
`name`: <string> Optional name to use for the route. If left blank, the name from the `routes.js` file will be used.
`props`: <object> Option object of props to pass to the next scene.