# Flash

### Native speed. Nothing in the way.

![Flash — GPU-native, cross-platform UI.](https://mateosuarez.dev/flash/og-flash.webp)

**GPU-native, cross-platform UI. Built from first principles.**

An entirely different foundation for interfaces.  
A reactive native core. A direct line to the GPU.  
And nothing running when nothing changes.

**No Skia. No WebView. No bridge. No canvas on web.**

[Explore Flash](https://mateosuarez.dev/flash) · [Try the illustrative playground](https://mateosuarez.dev/flash#playground) · [Follow development](#watch-flash-take-shape) · [Roadmap](#roadmap)

_Early development · Closed source · Free to use when released_

## What if the fastest UI was the one that simply did less?

Flash doesn’t keep a widget tree and figure out what changed. Your UI is a function. A signal changes, the function runs, and the GPU draws. When it settles, the frames stop.

**No retained UI tree. No diffing. No reconciliation.**

Components, props, state and composition remain familiar. Underneath, UI becomes layout and render commands, drawn by a pass-based GPU renderer — the way a game engine draws a frame.

Nothing is retained, so nothing can drift: no stale closures, no keys to babysit, no "why didn't this update", no memoization to hand-tune. The work isn't there to optimize.

**The architecture is the optimization.**

## Born in a game engine. Built for more.

Flash began as the UI framework for my own work-in-progress game engine, **Ban Engine**. I extracted it into a standalone technology so the same GPU-native foundation could power interfaces far beyond the engine.

Mobile apps. Desktop tools. Creative software. Dashboards. Game HUDs and editors. Standard web interfaces. Product viewers and data in three dimensions.

One platform-agnostic core, with native GPU backends underneath. Platform coverage grows through dedicated shells, input support and testing.

## Change something. Then watch it stop.

Signals track what your UI reads. A change invalidates its subscribers and schedules work. The UI runs, layout is computed in-process, and the GPU draws the resulting commands.

Animation, scroll momentum and a finger on the screen keep the frames they need alive. When they settle, Flash goes idle again. Timers wait without drawing anything.

**No change. No scheduled UI frames. No rendering work.**

The [signal playground](https://mateosuarez.dev/flash#playground) illustrates this idea: change the state, follow the frame path, then leave it alone and watch it stop.

## A straight line from state to screen.

### Any shader is a style.

Register a pixel shader once. Use it on an element like a background color. Glass, distortion, procedural patterns and custom SDF shapes use the same rendering foundation.

**Your effect. Your rules.**

### Many elements. One material.

Render passes are materials. Elements are instanced. Compatible elements sharing a material batch together while preserving draw order.

The GPU is the foundation of the interface, not something hidden behind another rendering engine.

### Layout in-process.

A native flex layout engine runs inside the frame: no cross-language hop, no layout thrash. The whole screen is laid out from scratch in microseconds, every frame that changes. Nothing to cache, nothing to invalidate.

### Smooth without the workarounds.

Layout, animation and GPU rendering belong to the native core. No worklets are needed to keep animation in that path. No mandatory UI / logic thread split to make the interface move smoothly.

Multi-thread ready, without requiring it as the starting point.

## Change the layout. Keep the flow.

Motion belongs to the framework. Tweens and springs animate individual values or whole styles: size, padding, radius, opacity and color move together. Retarget in flight; springs carry their velocity.

- **Layout that animates itself.** Add or remove items and the list makes room. Resize a component and its layout carries it and its children into the new arrangement.
- **Enter. Exit. Resurrect.** Bring an element back mid-exit and the animation turns around. The same identity continues from where it is.
- **Transforms that move without reflow.** Translate and scale what is drawn — a card following the finger, a button pressed in — while the layout stays still. Touches follow what you see.
- **Gesture-driven motion.** A spring starts at the drag's velocity, so a flick lands as fast as it was thrown. One value maps onto another: a backdrop fades with the sheet the finger is pulling.
- **Shared elements across screens.** A gallery card becomes a detail hero. Separate elements, a matching shared ID: the old geometry becomes the start, the new layout sets the destination. Position, size, corners and opacity morph together.

**Animate. Settle. Stop scheduling frames.**

## Every touch has one owner.

One arbiter decides who owns each touch, the way native platforms do:

- **A scroll never presses the button it ends on.** Buttons track the finger like native controls, and let go when it slides away.
- **Nested scrolling just works.** A carousel inside a list follows the drag's direction; the innermost view wins; a touch catches a gliding list mid-flight.
- **Gestures that run together.** Pull a sheet down from the top of its list and the sheet takes over in the same touch, as iOS sheets do.
- **Any element can answer a drag or a tap.** Pan with distance and velocity, tap, double tap, long press, press in and press out — on any view, not just buttons.
- **Decorative layers stay out of the way.** An overlay can let every touch through to what is beneath.

The framework's own scroll views and buttons are built on the same public gesture API your components use. No private path.

**One touch. One owner. No surprises.**

## The details are part of the foundation.

### Apple-style corners. By default.

Continuous corners on rounded elements. Switch to classic web / Material circular corners app-wide or per element.

**Same radius. Your kind of curve.**

`cornerRadius` keeps a consistent visual meaning across both styles. Corners are part of the renderer, so backgrounds, images, glass and rounded clipping share the same shape model.

### Depth you can feel.

Backdrop blur. Frosted glass. **Liquid Glass** with refraction, edge light, contact shadows and a spring-driven press lift.

These are materials in the native rendering system, not a separate visual layer. Custom effects use the same shader foundation.

Liquid Glass is currently **experimental**. The lens and press interaction run on device; broader batching, gesture-reactive deformation and liquid merging are being developed toward the full effect.

### The keyboard, handled.

Scroll views and floating panels move clear of the soft keyboard, following it as it slides, opt-in per container. Safe areas and density-independent sizing come with every screen.

## TypeScript. Without the detour.

Not just a UI framework. Flash’s native TSX authoring is powered by a purpose-built, ultralight TypeScript execution surface designed from scratch to be **bridgeless by design**.

**Real TypeScript. Typed bytecode. Zero bridge overhead. No heavy JS engine.**

### Bridgeless execution.

Application logic reaches native UI primitives and signals directly in place — **no serialization, no message queue, no asynchronous bridge hop**. A TypeScript signal _is_ the native signal.

When app state changes, the native layout and GPU rendering engine respond in the exact same frame, whether authored in C++ or TSX.

### TSX describes. Native renders.

Components, props and scoped signals on the surface. Native layout, animation and GPU rendering underneath. A familiar component lifecycle, cleanup and context above an immediate-mode foundation.

State-preserving hot reload, over-the-air bundle streaming, and AOT release builds are part of the authoring workflow being built around it.

### Native first. The web, too.

The standard web path is **Flash JSX**, my own existing web runtime: signals, SSR, streaming, hydration, routing, SEO, providers and layout animations.

**Direct web output. No canvas.**

Shared TSX components, state and logic are the goal: native rendering on device, Flash JSX on the web.

## Native essentials. Included.

Native capabilities are part of Flash’s foundation, exposed directly through one platform API:

| Capability                 | What it brings                                                                              |
| -------------------------- | ------------------------------------------------------------------------------------------- |
| Files & secure storage     | The app's own folders, and protected data in the system keystore.                           |
| Clipboard, links & sharing | Copy, open links and other apps, share through the system sheet.                            |
| Pickers                    | The system photo and file pickers. No permission: the user choosing is the consent.         |
| HTTP & network             | Requests from the platform's own stack, and whether the device is online, and how.          |
| Haptics                    | Feedback you can feel.                                                                      |
| Audio                      | Instant sounds for the interface, and players for music, voice and remote streams.          |
| Notifications              | Shown now or scheduled, surviving a reboot, the tap delivered to your app.                  |
| Permissions                | One model for every permission, as signals: the UI follows a change made in settings.       |
| Device & lifecycle         | Device information, locale and the app's lifecycle, as signals.                             |
| Camera, microphone & more  | Camera, microphone, location, contacts and an in-app media library, through the same model. |

**A capability should be a call, not a package hunt.** Thin wrappers over native OS APIs, with availability checks where platforms differ. State the OS owns arrives as signals; answers that take time arrive on the UI thread, ready to drop into your own. No endless package management or third-party dependency drift.

The problems every app has — routing, data fetching with caching and revalidation, translations — are part of the framework too, not assembled from packages.

The configuration direction is **`app.config.ts`**: app identity, icons, enabled builtins and permissions in one TypeScript file, generating the native app declarations.

## 3D & UI. The same frame.

Games, product viewers, AR and data in three dimensions. Materials and shaders embedded directly in your interface.

A shared device. A shared command queue. A shared color pipeline.

Two planned ways in:

- **A viewport.** Render a scene into an element, sized, rounded and laid out alongside the rest of the interface.
- **The shared frame.** Draw 3D directly between UI layers: a model over a panel, particles across the interface, glass blurring the live scene behind it — without a separate offscreen composition step.

Custom passes, compute and richer rendering belong to that same GPU foundation.

The scheduling rule carries forward: a still scene should request no new frames; motion wakes the frames it needs.

**Your renderer, inside your interface.**

## Looks familiar. Works differently.

Props say what an element is. Style says how it looks and lays out. Children say what it contains.

Components are functions. Styles are values. Events are callbacks. The same pattern composes across the interface.

### In C++

```cpp
Signal<int> count{0};

window([&] {
    column({}, { .gap = 12, .padding = 24 }, [&] {
        label({ .text = std::to_string(count.get()).c_str() },
              { .fontSize = 24 });

        button({ .id = "inc", .onTap = [&] { count.set(count.peek() + 1); } }, {}, [&] {
            label({ .text = "Increment" });
        });
    });
});
```

### In TSX

```tsx
import { signal } from "@mateosuarezdev/flash";

const count = signal(0);

function Counter() {
  return (
    <column style={{ gap: 12, padding: 24 }}>
      <label text={`${count.get()}`} style={{ fontSize: 24 }} />
      <button onTap={() => count.set(count.peek() + 1)}>
        <label text="Increment" />
      </button>
    </column>
  );
}
```

A signal. A function. A button. Two authoring surfaces over the same Flash foundation.

These examples introduce the API and authoring direction. Native TSX integration is in development; the public SDK is not available yet.

## A big idea. A real beginning.

Flash is an early, independent technology in active development. **One developer. Built from scratch.**

The native Android implementation runs today: the reactive core, widgets, styling, instanced rendering, MSDF text, animation, structural motion and transforms, continuous corners, blur and experimental Liquid Glass. Text input, keyboard avoidance, the complete touch and gesture system, timers and sixteen native builtins — from files and HTTP to audio, permissions and notifications — work on device too.

This README presents the technology and its full direction. Platform ports, builtins, 3D and native TSX integration continue to evolve.

| Output                                | Direction                                       | Status                                                       |
| ------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| **Android**                           | Native core · Vulkan                            | Running in development                                       |
| **iOS**                               | Native core · Metal                             | On the roadmap                                               |
| **Windows · macOS · Linux**           | D3D12 / Metal / Vulkan                          | On the roadmap                                               |
| **Web — Flash JSX**                   | Signals · SSR · streaming · SEO · DOM output    | Existing web runtime; shared native authoring in development |
| **Game engines**                      | Embedded native UI                              | Used in development with Ban Engine                          |
| **TV & connected / embedded devices** | Dedicated platform shells and input integration | Exploratory targets                                          |

### Latest progress

- **October 2026** — Native builtins: HTTP, network status, audio playback, permissions as signals, and local notifications.
- **October 2026** — Transforms that move what is drawn without reflowing the layout, and per-stage frame timing on CPU and GPU.
- **October 2026** — The touch and gesture system: one owner per touch, gestures on any element, sheets taking over from their lists, taps, double taps and long presses, timers.
- **Earlier** — The reactive core, the GPU renderer, styling, motion, continuous corners, glass, text input and keyboard avoidance.

### Flash Browser — future

_Native applications. Delivered over the internet._

**Typed bytecode. Bridgeless execution. Flash rendering.**

A future output with its own technology and identity, separate from the standard web path.

### Availability

Flash is **closed source** and is being built to be **free to use when released**. It is not available as a public SDK to try yet.

This repository is the public home for documentation, examples, roadmap and development updates. The framework implementation is not hosted here.

## Roadmap

A view of where Flash is going, rather than an internal task list or a promised release calendar.

| Area                 | What's next                                                                                                                                                                           |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Interface & text** | Text selection with handles and magnifier, clipboard actions in fields, IME composition, interactive keyboard dismissal, international text shaping and accessibility.                |
| **Gestures**         | Multi-touch: pinch and rotate.                                                                                                                                                        |
| **Layout**           | Closer CSS flexbox behavior, proportional grow factors, minimum-size semantics, line clamping and ellipsis, virtualized lists.                                                        |
| **Motion**           | Shared elements across screens, rotation transforms, shapes that move apart from their content, per-corner radii, squash, stretch and wobble, merging and morphing — for any element. |
| **Liquid Glass**     | Shared backdrop capture and blur optimization, animated glass properties, highlights that follow the touch.                                                                           |
| **Native builtins**  | Camera, microphone, location, contacts, an in-app media library, push notifications, background audio with system media controls, configured through `app.config.ts`.                 |
| **Batteries**        | A router, data queries with caching and revalidation, translations.                                                                                                                   |
| **Components**       | Broader unstyled primitives and an optional Flash Kit: Flash's own design language and feel, with theme tokens.                                                                       |
| **Platforms**        | iOS, Windows, macOS and Linux ports; GLES fallback for older Android devices; public SDK packaging and a richer demo gallery.                                                         |
| **Authoring**        | Native TSX on the custom bridgeless TypeScript VM, shared authoring with Flash JSX, scoped lifecycle, hot reload, bundle updates and AOT builds.                                      |
| **Rendering & 3D**   | Viewports, 3D in the shared frame, custom passes and compute, content blur and bloom, HDR / wide gamut, render graph and GPU-driven batching.                                         |

The native primitive API provides the foundation for the scripting layer. Future device families and Flash Browser remain longer-term directions.

## Watch Flash take shape.

I'm sharing demos, engineering decisions and progress as I build Flash.

- **[LinkedIn](https://www.linkedin.com/in/mateo-suarez-b96558249/)** — updates and engineering notes.
- **[YouTube](https://www.youtube.com/@mateosuarezdev)** — demos and development videos.
- **[Flash landing page](https://mateosuarez.dev/flash)** — the visual introduction and interactive studies.

The landing's browser demos illustrate Flash's ideas in HTML, CSS and JavaScript. They are not the Flash runtime or performance benchmarks.

## License

Flash is distributed under the **Mateo Suarez Free Use License v1.1** (Closed source, free for personal and commercial use).

See the full terms in [LICENSE.md](./LICENSE.md).
