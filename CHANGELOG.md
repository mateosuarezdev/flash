# Flash — Changelog

Public milestones from Flash's development: new capabilities, meaningful improvements and fixes that affect the interface or authoring experience.

**Flash is in early development and is not available as a public SDK yet.** These entries describe progress in the development implementation, not downloadable releases.

[Explore Flash](https://mateosuarez.dev/flash) · [README](README.md) · [Roadmap](README.md#roadmap)

## 2026-11-06 — Images, at the size you see them

- **Added** images by source: a file shipped with the app, a photo on the device or a URL, in one line.
- **Added** an image cache. Images are downloaded, read and decoded off the UI thread, at the size they are drawn: a large photo in a small box takes kilobytes of memory, not megabytes. Only what is on screen loads, the same image anywhere is one decode, and coming back to it is instant. Drawn bigger, a sharper copy takes over.
- **Added** photos shown upright, turned the way the camera held them.
- **Added** load and error callbacks on images. While an image loads, or if it fails, its box holds its place and shows its background.
- **Improved** the shape of an app's project: everything that ships, code and assets, lives in one folder.

## 2026-11-05 — Background work

- **Added** background work: anything slow runs on a worker while the screen keeps moving, and its answer comes back to the UI thread, ready to drop into a signal. Jobs can be cancelled when the screen that started them goes.
- **Improved** screens that read files, secure storage or query the system: they draw at once and fill in, without stuttering as they open.

## 2026-11-04 — Audio recording

- **Added** recording from the microphone, with a live level meter, asking for the permission at the moment it is needed.
- **Improved** the native features' temporary files: cleaned up at every start, off the main thread.

## 2026-11-03 — App configuration and build variants

- **Added** `app.config.ts` (in development): the app's name, package, version and the native features it switches on, in one TypeScript file that generates the native app declarations.
- **Added** three build variants, Development, Preview and Release, installable side by side.
- **Improved** how an app is built: Flash ships prebuilt, so an app compiles only its own code.

## 2026-10-30 — The router

- **Added** the router (in development): path first, stacks with animated push and pop, the native swipe-back and the system back, nesting, and sheets as routes.

## 2026-10-29 — Faster startup

- **Improved** startup: fonts and shaders are prepared when the app is built, not on the phone at every launch. The first frame arrives about seven times sooner.

## 2026-10-27 — Landscape at full frame rate

- **Improved** landscape: each frame is drawn already turned to the panel, the way games do it, so the system has nothing left to rotate. Glass and blur follow the screen through rotation, split-screen and folding.

## 2026-10-26 — The display and the system UI

- **Added** dark or light following the system, as a signal.
- **Added** the system bars: their icons' colour and immersive mode, with a safe area that stays stable while the bars come and go.
- **Added** the display's refresh rate, asked for by the screen that needs it, and keeping the screen awake while something needs it.
- **Added** orientation: following the phone, with a lock for the screen that needs one.

## 2026-10-24 — Lists, context and hidden tabs

- **Added** lists by key whose items keep their own state wherever they move, and play their own entrance and exit.
- **Added** context: provide a value in a component and read it anywhere below.
- **Added** hidden-but-alive content: skipped from layout and drawing, its state and scroll positions kept.
- **Improved** element identity: every element keeps its identity from where it is written, whatever appears around it.
- **Improved** drags, which now start from where the finger is the moment they win the touch, without a jump.
- **Fixed** a field's caret after its text is replaced from outside, and a long press on an empty field.

## 2026-10-21 — TypeScript on the device

- **Added** my own bridgeless TypeScript VM, embedded in Flash, running on the device and calling into native code. The first step toward authoring Flash in TSX.

## 2026-10-20 — Components

- **Added** components with their own state: a setup that runs once, state per instance and a lifecycle with cleanup.
- **Added** branches that animate out before they go and turn around when brought back mid-exit, their state intact.

## 2026-10-19 — Text editing that feels native, and image fitting

- **Added** native-feeling text editing: the caret floating under the finger, a glass magnifier, word selection with handles, the system's edit menu with the device's own items, and the long press each platform's users expect.
- **Added** `objectFit` and `objectPosition`, as in CSS, and `aspectRatio`.
- **Added** a background behind images, shown through transparent pixels and while they load.
- **Fixed** the keyboard not coming back after being dismissed, and taps on an element that could be lost.
- **Fixed** drawing order between different materials, so what is written on top is drawn on top.

## 2026-10-18 — Network, audio, notifications and transforms

- **Added** native features: HTTP from the platform's own stack, network status, audio playback, permissions as signals and local notifications.
- **Added** transforms that translate and scale what is drawn without reflowing the layout.
- **Added** text selection, the caret blinking on a timer, and text that scrolls inside its field.
- **Fixed** a scroll view's rubber band jumping back while it moved clear of the keyboard.

## 2026-10-17 — Every touch has one owner

- **Added** a touch and gesture system: one arbiter owns each touch, nested scroll views follow the drag's direction, and buttons let go when a scroll takes over.
- **Added** gestures on any element: pan with distance and velocity, tap, double tap and long press. Springs start at the drag's velocity, so a flick lands as fast as it was thrown.
- **Added** gestures that run together: a sheet that takes over from its list in the same touch.
- **Added** layers that let touches pass through, timers, and frame timing in the debug overlay.
- **Improved** events, which are now plain callbacks.

## 2026-10-15 — The keyboard, handled, and more native features

- **Added** keyboard avoidance: scroll views and floating panels move clear of the keyboard, following it as it slides.
- **Added** rubber-band scrolling and tapping to place the caret.
- **Added** native features: the system pickers, the app's lifecycle and identity, the device's locale as a signal, device information and secure storage.

## 2026-10-14 — The first native features

- **Added** the clipboard, opening links and other apps, sharing through the system sheet, haptics, and the app's own files.
- **Fixed** emoji getting mangled on their way to and from the platform, such as through the clipboard.

## 2026-10-06 — Native platform builtins

- **Added** a platform builtin foundation, with the soft keyboard as its first integrated capability and availability checks for platform differences.
- **Fixed** the soft keyboard failing to open in Android release builds.

## 2026-10-05 — Borders, rounded clipping and directional scrolling

- **Added** borders with colors, uniform or individual edge widths, and dividers between children. Borders follow the element's corner style and stay inside its bounds.
- **Added** animated border widths and colors as part of style animation.
- **Added** clipping on containers, including rounded corners and independent horizontal / vertical overflow controls.
- **Added** vertical, horizontal and two-axis scroll views. Scroll views clip their content on both axes, including rounded corners.
- **Fixed** clipped cards intercepting drag gestures intended for the surrounding scroll view.
- **Improved** clipping of absolutely positioned children within their parent.

## 2026-10-04 — More complete layout controls

- **Added** start, center and end alignment along both the main and cross axes.
- **Added** minimum width and height controls, letting elements retain a minimum size while growing with their content.
- **Improved** integration of these layout properties with style composition and animation.

## 2026-09-30 — Text input and refined corner styles

- **Added** a text input primitive with app-owned signal state, focus, caret, placeholder and character limits.
- **Added** Android soft keyboard integration, keyboard types and animated keyboard-height reporting.
- **Added** basic UTF-8-safe editing and accented Latin text rendering, including characters used in Spanish, Portuguese, French and German.
- **Added** per-element corner-style selection alongside the app-wide setting.
- **Improved** Continuous corners so switching between Continuous and Circular preserves comparable visual roundness at the same radius.
- **Fixed** corner styles changing unexpectedly during style animations and glass materials disappearing while their styles animated.

Advanced selection gestures, full complex-script shaping and IME composition remain in development.

## 2026-09-29 — Structural motion and Liquid Glass

- **Added** structural layout transitions: surrounding elements move into place when items are added, removed or change layout.
- **Added** an enter / exit animation workflow that keeps an element present until its exit settles.
- **Added** animations that can be started from event handlers and continue while their content moves off-screen.
- **Added** experimental Liquid Glass with refraction, edge light, contact shadows and a spring-driven press lift.
- **Added** framework-wide Continuous corners across backgrounds, images, blur and glass.
- **Improved** primitive styling: buttons leave fills and press decoration to the app, and can grow with their content while retaining a minimum touch-target height.
- **Improved** glass contact shadows and the consistency of rounded shapes.
- **Fixed** faded list items shifting unexpectedly on a subsequent scroll.

Liquid Glass remains experimental; further work on backdrop capture and batching is needed for interfaces with many glass elements.

## 2026-09-28 — Tweens, springs and animated styles

- **Added** tween and spring animation for individual values and whole styles, including size, padding, radius, opacity and color.
- **Added** mid-flight retargeting, with springs preserving their velocity.
- **Added** composable style lists, making base, conditional and animated styles easy to combine.
- **Improved** animation and scrolling timing to follow the display's frame clock.
- **Improved** frame scheduling so active motion keeps rendering alive and the interface returns to idle when it settles.

## 2026-09-27 — A unified authoring API and faithful colors

- **Added** a consistent `props → style → children` API across the core widgets.
- **Added** value-based style composition, including explicit zero and transparent overrides.
- **Added** fixed, percentage, content-sized and growing dimensions, plus relative, absolute and fixed positioning.
- **Added** rounded images, image tint and text styling through the shared style model.
- **Improved** scroll-view styling and the sizing and positioning of translucent or blurred elements.
- **Fixed** authored colors and images appearing lighter than intended, establishing a consistent linear color pipeline.

## 2026-06-12 — Frosted glass and composable controls

- **Added** backdrop tint, covering pure blur, frosted glass and opaque tinted panels.
- **Improved** widget composition with props-based calls and arbitrary button contents, including icons, text and custom layouts.
- **Improved** styling controls for gap, padding, background and corner radius.
- **Added** an optional continuous-rendering mode while keeping reactive, demand-driven rendering as the default.
- **Clarified** `backdropBlur` as the effect that blurs content behind an element.

## 2026-06-06 — Custom GPU effects and backdrop blur

- **Added** custom pixel shaders on UI elements, with local and screen-space coordinates, element dimensions and configurable parameters.
- **Added** live backdrop sampling for effects that read the content behind an element.
- **Added** backdrop blur through the style API, with rounded clipping and clean edges over moving content.

## 2026-06-04 — Opacity across an element and its children

- **Added** subtree opacity for containers and controls, fading their backgrounds, text and images together.
- **Kept** background transparency independent, so a translucent surface can retain fully opaque content.

## 2026-06-03 — Reactive widgets, layout and scrolling

- **Added** the reactive UI authoring surface: a registered UI function with buttons, labels, images, rows, columns and flexible layout.
- **Added** touch interaction, scrolling with momentum and density-independent sizing.
- **Added** safe-area handling for status bars, navigation bars and display cutouts.
- **Fixed** the interface failing to redraw after returning from the background.

## 2026-06-01 — Signals and sharp text

- **Added** signals, automatic dependency tracking and batched invalidation. Multiple state changes can schedule one UI update.
- **Added** font rendering and MSDF text for sharp glyphs across sizes.
- **Fixed** text-rendering artifacts and unreliable dynamic text rendering.

## 2026-05-31 — Image rendering

- **Added** image rendering and texture support.
- **Improved** the Android presentation with an interface free of the app title bar while retaining system bars.

## 2026-05-29 — Flash renders on Android

- **Established** the native Android / Vulkan rendering foundation and validated it on device.
- **Added** display-synchronized, request-driven frame scheduling and surface lifecycle handling.
- **Added** the first GPU-rendered rectangles, rounded shapes and clipping.

---

Upcoming capabilities are tracked in the [roadmap](README.md#roadmap). Development videos and updates are shared on [YouTube](https://www.youtube.com/@mateosuarezdev) and [LinkedIn](https://www.linkedin.com/in/mateo-suarez-b96558249/).
