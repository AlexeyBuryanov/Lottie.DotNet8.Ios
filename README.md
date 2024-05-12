# Lottie.DotNet8.Ios

Lottie is a mobile library for iOS that parses [Adobe After Effects](http://www.adobe.com/products/aftereffects.html) animations exported as json with [Bodymovin](https://github.com/bodymovin/bodymovin) and renders them natively on mobile!

This library is a port of https://github.com/Baseflow/LottieXamarin/ to .NET 8 but limited to iOS.

[![Static Badge](https://img.shields.io/badge/NuGet-Lottie.DotNet8.Ios-blue)](https://www.nuget.org/packages/Lottie.DotNet8.Ios/)

## Using Lottie for net8-ios
Lottie supports iOS 8 and above.
Lottie animations can be loaded from bundled JSON or from a URL

The simplest way to use it is with LOTAnimationView:
```c#
LOTAnimationView animation = LOTAnimationView.AnimationNamed("LottieLogo1");
this.View.AddSubview(animation);
animation.PlayWithCompletion((animationFinished) => {
  // Do Something
});
//You can also use the awaitable version
//var animationFinished = await animation.PlayAsync();
```

Or you can load it programmatically from a NSUrl
```c#
LOTAnimationView animation = new LOTAnimationView(new NSUrl(url));
this.View.AddSubview(animation);
```

Lottie supports the iOS `UIViewContentModes` ScaleAspectFit and ScaleAspectFill

You can also set the animation progress interactively.
```c#
CGPoint translation = gesture.GetTranslationInView(this.View);
nfloat progress = translation.Y / this.View.Bounds.Size.Height;
animationView.AnimationProgress = progress;
```

Want to mask arbitrary views to animation layers in a Lottie View?
Easy-peasy as long as you know the name of the layer from After Effects

```c#
UIView snapshot = this.View.SnapshotView(afterScreenUpdates: true);
lottieAnimation.AddSubview(snapshot, layer: "AfterEffectsLayerName");
```

Lottie comes with a `UIViewController` animation-controller for making custom viewController transitions!

```c#
#region View Controller Transitioning
public class LOTAnimationTransitionDelegate : UIViewControllerTransitioningDelegate
{
    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController(UIViewController presented, UIViewController presenting, UIViewController source)
    {
        LOTAnimationTransitionController animationController =
            new LOTAnimationTransitionController(
            animation: "vcTransition1",
            fromLayer: "outLayer",
            toLayer: "inLayer");

        return animationController;
    }

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForDismissedController(UIViewController dismissed)
    {
        LOTAnimationTransitionController animationController = 
            new LOTAnimationTransitionController(
                animation: "vcTransition2",
                fromLayer: "outLayer",
                toLayer: "inLayer");

        return animationController;
    } 
}
#endregion
```

If your animation will be frequently reused, `LOTAnimationView` has an built in LRU Caching Strategy.


## Supported After Effects Features

### Keyframe Interpolation

---

* Linear Interpolation

* Bezier Interpolation

* Hold Interpolation

* Rove Across Time

* Spatial Bezier

### Solids

---

* Transform Anchor Point

* Transform Position

* Transform Scale

* Transform Rotation

* Transform Opacity

### Masks

---

* Path

* Opacity

* Multiple Masks (additive)

### Track Mattes

---

* Alpha Matte

### Parenting

---

* Multiple Parenting

* Nulls

### Shape Layers

---

* Rectangle (All properties)

* Ellipse (All properties)

* Polystar (All properties)

* Polygon (All properties. Integer point values only.)

* Path (All properties)

* Anchor Point

* Position

* Scale

* Rotation

* Opacity

* Group Transforms (Anchor point, position, scale etc)

* Multiple paths in one group

#### Stroke (shape layer)

---

* Stroke Color

* Stroke Opacity

* Stroke Width

* Line Cap

* Dashes

#### Fill (shape layer)

---

* Fill Color

* Fill Opacity

#### Trim Paths (shape layer)

---

* Trim Paths Start

* Trim Paths End

* Trim Paths Offset

## Performance and Memory
1. If the composition has no masks or mattes then the performance and memory overhead should be quite good. No bitmaps are created and most operations are simple canvas draw operations.
2. If the composition has mattes, 2-3 bitmaps will be created at the composition size. The bitmaps are created automatically by lottie when the animation view is added to the window and recycled when it is removed from the window. For this reason, it is not recommended to use animations with masks or mattes in a RecyclerView because it will cause significant bitmap churn. In addition to memory churn, additional bitmap.eraseColor() and canvas.drawBitmap() calls are necessary for masks and mattes which will slow down the performance of the animation. For small animations, the performance hit should not be large enough to be obvious when actually used.
4. If you are using your animation in a list, it is recommended to use a CacheStrategy in LottieAnimationView.setAnimation(String, CacheStrategy) so the animations do not have to be deserialized every time.

## Issues or feature requests?
File github issues for anything that is unexpectedly broken. If an After Effects file is not working, please attach it to your issue. Debugging without the original file is much more difficult.

