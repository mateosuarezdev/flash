# Flash — Changelog

Public milestones from Flash's development: new capabilities, meaningful improvements and fixes that affect the interface or authoring experience.

**Flash is in early development and is not available as a public SDK yet.** These entries describe progress in the development implementation, not downloadable releases.

[Explore Flash](https://mateosuarez.dev/flash) · [README](README.md) · [Roadmap](README.md#roadmap)

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
