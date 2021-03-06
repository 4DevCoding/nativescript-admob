# NativeScript AdMob plugin

[![NPM version][npm-image]][npm-url]
[![Downloads][downloads-image]][npm-url]


[npm-image]:http://img.shields.io/npm/v/4dev-nativescript-admob.svg
[npm-url]:https://npmjs.org/package/4dev-nativescript-admob
[downloads-image]:http://img.shields.io/npm/dm/4dev-nativescript-admob.svg


## Installation
From the command prompt go to your app's root folder and execute:
```
tns plugin add 4dev-nativescript-admob
```

### iOS
Run `pod repo update` from a Terminal, otherwise the required Pod version may not be available on your system.

NOTE: Reward Video Ad is not implemented in ios.

## BANNER

If you want a quickstart, [clone our demo app](https://github.com/4DevCoding/nativescript-admob-demo).

Here are the supported functions:

### createBanner

#### TypeScript

```typescript
import { AD_SIZE, createBanner, hideBanner } from "nativescript-admob";
import { isIOS } from "tns-core-modules/platform";

const testing = true;
createBanner({
  // if this 'view' property is not set, the banner is overlayed on the current top most view
  // view: ..,
  size: AD_SIZE.SMART_BANNER,
  iosBannerId: "ca-app-pub-9517346003011652/3985369721",
  androidBannerId: testing
      ? "ca-app-pub-3940256099942544/6300978111"  // global test banner id
      : "ca-app-pub-9517346003011652/7749101329", // our registered banner id
  // Android automatically adds the connected device as test device with testing:true, iOS does not
  // iosTestDeviceIds: ["yourTestDeviceUDIDs", "canBeAddedHere"],
  margins: {
    // if both are set, top wins
    // top: 10
    bottom: isIOS ? 50 : 0
  },
  keywords: ["foo", "bar"]
}).then(
    () => this.message = "Banner created",
    error => this.message = "Error creating banner: " + error
)
```

#### JavaScript
```js
var admob = require("nativescript-admob");

admob.createBanner({
    // if this 'view' property is not set, the banner is overlayed on the current top most view
    // view: ..,
    testing: true, // set to false to get real banners
    size: size, // anything in admob.AD_SIZE, like admob.AD_SIZE.SMART_BANNER
    iosBannerId: "ca-app-pub-XXXXXX/YYYYYY", // add your own
    androidBannerId: "ca-app-pub-AAAAAAAA/BBBBBBB", // add your own
    // Android automatically adds the connected device as test device with testing:true, iOS does not
    iosTestDeviceIds: ["yourTestDeviceUDIDs", "canBeAddedHere"],
    margins: {
      // if both are set, top wins
      //top: 10
      bottom: 50
    },
    keywords: ["keyword1", "keyword2"] // add keywords for ad targeting
  }).then(
      function() {
        console.log("admob createBanner done");
      },
      function(error) {
        console.log("admob createBanner error: " + error);
      }
)
```

Note that you can trigger the function above at any moment, and since version 1.1.4
of this plugin you can even call it [from the `Page.loaded` event](https://github.com/EddyVerbruggen/nativescript-admob-demo/blob/master/AdMob/app/main-page.js#L9).

### hideBanner
NOTE: If you want to show a different banner than the one showing you don't need to call `hideBanner`
since `createBanner` will do that for you to prevent your app from crashing.

```js
// the .then(.. bit is optional btw
admob.hideBanner().then(
      function() {
        console.log("admob hideBanner done");
      },
      function(error) {
        console.log("admob hideBanner error: " + error);
      }
)
```

## INTERSTITIAL
To show a fullscreen ad, you can use this function. Note that Interstitial banners need to be loaded before
they can be shown, and there are two ways to do that:

* Use `createInterstitial` and have the plugin automatically preload the ad and show it when loaded. This is not recommended because there's a delay the user may notice.
* (Since plugin version 2.0.0) Use `preloadInterstitial`, and (at any time after its Promise resolves) `showInterstitial`. This will hide the preloading delay for your users. Note that the parameters of `createInterstitial` and `preloadInterstitial` are exactly the same so migration should be easy. 

### createInterstitial
Again, not recommended.

```js
admob.createInterstitial({
    testing: true,
    iosInterstitialId: "ca-app-pub-XXXXXX/YYYYY2", // add your own
    androidInterstitialId: "ca-app-pub-AAAAAAAA/BBBBBB2", // add your own
    // Android automatically adds the connected device as test device with testing:true, iOS does not
    iosTestDeviceIds: ["ce97330130c9047ce0d4430d37d713b2"],
    keywords: ["keyword1", "keyword2"] // add keywords for ad targeting
  }).then(
      function() {
        console.log("admob createInterstitial done");
      },
      function(error) {
        console.log("admob createInterstitial error: " + error);
      }
)
```

### preloadInterstitial
Use this for instance while loading your view, so it's ready for the moment you want to actually show it (by calling `showInterstitial`).

Note that the parameters are identical to `createInterstitial`.

```js
admob.preloadInterstitial({
    testing: true,
    iosInterstitialId: "ca-app-pub-XXXXXX/YYYYY2", // add your own
    androidInterstitialId: "ca-app-pub-AAAAAAAA/BBBBBB2", // add your own
    // Android automatically adds the connected device as test device with testing:true, iOS does not
    iosTestDeviceIds: ["ce97330130c9047ce0d4430d37d713b2"],
    keywords: ["keyword1", "keyword2"] // add keywords for ad targeting
  }).then(
      function() {
        console.log("interstitial preloaded - you can now call 'showInterstitial' whenever you're ready to do so");
      },
      function(error) {
        console.log("admob preloadInterstitial error: " + error);
      }
)
```

### showInterstitial
At any moment after `preloadInterstitial` successfully resolves, you can call `showInterstitial`.

Note that when you want to use `showInterstitial` again, you also have to use `preloadInterstitial` again because those ads can't be reused. 

```js
admob.showInterstitial().then(
      function() {
        // this will resolve almost immediately, and the interstitial is shown without a delay because it was already loaded
        console.log("interstitial showing");
      },
      function(error) {
        console.log("admob showInterstitial error: " + error);
      }
)
```

## REWARDED VIDEO
To show a fullscreen rewarded video ad, you can use this function. Note that rewarded video need to be loaded before
they can be shown:

Use `preloadVideoAd`, and (at any time after its Promise resolves) `showVideoAd`. This will hide the preloading delay for your users.

### preloadRewardedVideo
Use this for instance while loading your view, so it's ready for the moment you want to actually show it (by calling `showVideoAd`).

Note that the parameters are identical to `createInterstitial`. Dont forget to pass the callback function to receive de reward from ad.
admob.preloadVideoAd(args, callback) ...

```js
admob.preloadVideoAd({
    testing: true,
    iosInterstitialId: "ca-app-pub-XXXXXX/YYYYY2", // add your own
    androidInterstitialId: "ca-app-pub-AAAAAAAA/BBBBBB2", // add your own
    // Android automatically adds the connected device as test device with testing:true, iOS does not
    iosTestDeviceIds: ["ce97330130c9047ce0d4430d37d713b2"],
    keywords: ["keyword1", "keyword2"] // add keywords for ad targeting
  }, function(reward){ console.log(reward.amount); }).then(
      function(reward) {
        console.log("interstitial preloaded - you can now call 'showInterstitial' whenever you're ready to do so");
      },
      function(error) {
        console.log("admob preloadInterstitial error: " + error);
      }
)
```

### showRewardedVideo
At any moment after `preloadVideoAd` successfully resolves, you can call `showVideoAd`.

Note that when you want to use `showVideoAd` again, you also have to use `preloadVideoAd` again because those ads can't be reused. 

```js
admob.showVideoAd().then(
      function() {
        // this will resolve almost immediately, and the interstitial is shown without a delay because it was already loaded
        console.log("interstitial showing");
      },
      function(error) {
        console.log("admob showInterstitial error: " + error);
      }
)
```

## Tutorials
Need a little more help getting started? Check out these tutorials for using Admob in a NativeScript Android and iOS application.

* [Monetize with Google Admob in a NativeScript Vanilla Application](https://www.thepolyglotdeveloper.com/2016/03/monetize-with-google-admob-in-a-nativescript-mobile-app/)
* [Monetize with Google Admob in a NativeScript Angular Application](https://www.thepolyglotdeveloper.com/2016/11/google-admob-nativescript-angular-2/)
