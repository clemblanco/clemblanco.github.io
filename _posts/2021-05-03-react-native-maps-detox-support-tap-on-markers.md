---
layout:     post
title:      "react-native-maps - Detox: Support tap() on Map Markers"
date:       2021-05-03 15:07:32
categories: [open source]
comments:   false
---

Our React Native application relies quite heavily on Google Maps components provided by [`react-native-maps`](https://github.com/react-native-maps/react-native-maps).

When first starting to cover our map with tests, it all went pretty smoothly, we could make sure the map was visible, great.

However, anything to do with markers on the map started to be a bit of an issue, it's all explained [here](https://github.com/react-native-maps/react-native-maps/issues/3632#issuecomment-834132429):

> Calling `.tap()` on `<AIRGoogleMapMarker>` element doesn't tap at the correct x/y coordinate on the screen.
>
> It's always ending up tapping at the top-left corner of the MapView, even though my marker is aligned at the centre of the MapView.

<!--more-->

### # The Tech Stack

Okay so here is the deal, at Pod Point we are slowly moving towards covering most of our mobile application, coded using React Native, with end-to-end (E2E) tests.

We already had so far unit tests for each components and classes using [jest](https://jestjs.io) but we wanted to increase our confidence even more as our mobile application is key to a lot of users using our network of charging infrastructures.

We started to use Detox from Wix to do so. Detox slowly became the industry standard when talking about E2E testing with React Native. So far, the only potential option we'd have looked into was appium but our experience wasn't so great with it.

Detox is pretty cool, [check it out](https://github.com/wix/Detox) if you're into React Native.

### # The Workaround

While that issue on GitHub became stale and we didn't get any reply from the maintainers, we needed to be able to proceed.

We started looking into it and managed to find a ~hacky~ workaround which was adding a little bit of overhead on our test suite but nothing we couldn't handle.

#### ## Step 1: The Marker

So to make things easier, we are going to create a component to wrap the Marker component provided by `react-native-maps`.

As Detox is not capable to find out what the real position of the Marker is on the map, the idea here is to create some sort of shadow/hidden `<Text/>` view where we will store the **real** coordinate of the Marker on the map.

```jsx
import { Marker as RNMarker } from 'react-native-maps';

class Marker extends PureComponent {
  // ...

  render() {
      const {
          children,
          point,
          ...childProps
      } = this.props;

      return (
          <RNMarker
              { ...childProps }
              testID="marker"
          >
              { children }
              {/* You could easily render this <Text/> conditionally based on an ENV var
                  identifying your testing env for example */}
              <Text
                  style={ styles.markerPointInfo }
                  testID="marker-point-info"
              >
                  { JSON.stringify(point) }
              </Text>
          </RNMarker>
      );
  }
}

const styles = StyleSheet.create({
  markerPointInfo: {
    // This is ONLY used for Detox: hiding the x/y coordinate details.
    opacity: 0, // Hiding it...
    width: 25,  //
    height: 32, // Matches the real dimensions of the real Marker
  },
});

// ...

export default Marker;
```

So what we end up with here is an invisible `<Text/>` component on top of the Marker which will hold the `x` and `y` coordinates for us.

#### ## Step 2: The x/y coordinates

To find out what are these coordinates for each Marker, you'll have to use `pointForCoordinate()` from `react-native-maps`.

```jsx
import { MapView } from 'react-native-maps';
import { Marker } from 'your-marker-wrapper';

// ...

componentWillReceiveProps(nextProps) {
  if (this.props.coordinate !== nextProps.coordinate) {
    this.state.point = this.map.pointForCoordinate(nextProps.coordinate);
  }
}

render() {
  return (
    <MapView
      initialRegion={...}
      ref={map => { this.map = map }}
    >
      <Marker
        ref={marker => { this.marker = marker }}
        coordinate={this.state.coordinate}
        point={this.state.point}
      />
    </MapView>
  );
}
```

Obviously, if you have multiple markers or if these can change following a search or user interactions, you'd need to make sure you update the point x/y coordinates, but you get the gist.

#### ## Step 3: Tap the Marker from Detox

There is a bit of a ~hacky~ way to read `<Text/>` literal values from a Detox point of view. It's only supported for iOS but this is how you can do it for both.

```js
/**
 * Retrieve the `text` attribute from a React Component.
 *
 * @param {Object} target Element retrieved with Detox.
 * @return {String}
 */
async getElementText(target) {
  if (platform.isiOS) {
    const attributes = await target.getAttributes();

    if (!attributes.text) {
      const matcherArgs = target.matcher.predicate.values().join(': ');

      throw new Error(`The element [${matcherArgs}] doesn't have a 'text' attribute.`);
    }

    return attributes.text;
  }

  try {
    // This will throw exception so we can then scrap the Text value from the message.
    return await expect(target).toHaveText('_NOT_FOUND_');
  } catch (error) {
    const errorMessage = error.message.toString();
    const elementDefinition = errorMessage.match(/got: "(.*)"/i)[1];
    const [definition, elementName, stringProps] = elementDefinition.match(/^(.*?){(.*?)}$/i);
    const props = new Map(stringProps.split(', ').map(stringProp => stringProp.split('=')));

    if (!props.has('text')) {
      const matcherArgs = target._originalMatcher._call.value.args.join(', ');

      throw new Error(`The element ${elementName} [matcher args: ${matcherArgs}] doesn't have a 'text' attribute.`);
    }

    return props.get('text');
  }
}
```

Then all you need to do is to retrieve the `x` and `y` coordinates which are hold as a stringified JSON within the invisible `<Text/>` component and use them to tell Detox to tap at a specific x/y location on the currently visible screen.

```js
async tapMarker() {
  const pointAsString = await getElementText(element(by.id('marker-point-info')));

  const point = JSON.parse(pointAsString.replace(/\\/g, ''));

  await element(by.id('marker')).tap({
    x: point.x,
    y: point.y - 16, // bulls eye! 16 is half of 32 which is the height of our Marker
  });
}
```

### # Conclusion

This is obviously a bit hacky and we would ideally prefer to have this supported natively by `react-native-maps` and `detox` themselves but as hacky as this may look, it does actually work pretty well and never failed us once implemented.

I simplified this a lot here but once you have this working, you will need to make sure `x` and `y` coordinates are always up to date if you can interact with the map. If let's say you can zoom in and drag the map around (as in, it's not a static map) you will need to make sure you leverage your state management to keep these coordinates in sync for each marker.

This is also worth noting that this also works with Clustered Markers too, we use [`react-native-maps-super-cluster`](https://github.com/novalabio/react-native-maps-super-cluster) ourselves.

In terms of the overhead, we only render these hidden `<Text/>` components if an environment variable says that we are running in a `testing` environment. Therefore, this will not impact real user's performances. When you look at it, yes there is a slight overhead of rendering an additional `<Text/>` for each Marker and also updating/syncing these every time the main `<MapView>` re-renders but we can live with that for now.
