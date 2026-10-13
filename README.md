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

**The architecture is the optimization.**

## Born in a game engine. Built for more.

Flash began as the UI framework for my own work-in-progress game engine, **Ban Engine**. I extracted it into a standalone technology so the same GPU-native foundation could power interfaces far beyond the engine.

Mobile apps. Desktop tools. Creative software. Dashboards. Game HUDs and editors. Standard web interfaces. Product viewers and data in three dimensions.

One platform-agnostic core, with native GPU backends underneath. Platform coverage grows through dedicated shells, input support and testing.

## Change something. Then watch it stop.

Signals track what your UI reads. A change invalidates its subscribers and schedules work. The UI runs, layout is computed in-process, and the GPU draws the resulting commands.

Animation and scroll momentum keep the frames they need alive. When they settle, Flash goes idle again.

**No change. No scheduled UI frames. No rendering work.**

The [signal playground](https://mateosuarez.dev/flash#playground) illustrates this idea: change the state, follow the frame path, then leave it alone and watch it stop.

## A straight line from state to screen.

### Any shader is a style.

Register a pixel shader once. Use it on an element like a background color. Glass, distortion, procedural patterns and custom SDF shapes use the same rendering foundation.

**Your effect. Your rules.**

### Many elements. One material.

Render passes are materials. Elements are instanced. Compatible elements sharing a material batch together while preserving draw order.

The GPU is the foundation of the interface, not something hidden behind another rendering engine.

### Smooth without the workarounds.

Layout, animation and GPU rendering belong to the native core. No worklets are needed to keep animation in that path. No mandatory UI / logic thread split to make the interface move smoothly.

Multi-thread ready, without requiring it as the starting point.

## Change the layout. Keep the flow.

Motion belongs to the framework. Tweens and springs animate individual values or whole styles: size, padding, radius, opacity and color move together. Retarget in flight; springs carry their velocity.

- **Layout that animates itself.** Add or remove items and the list makes room. Resize a component and its layout carries it and its children into the new arrangement.
- **Enter. Exit. Resurrect.** Bring an element back mid-exit and the animation turns around. The same identity continues from where it is.
- **Shared elements across screens.** A gallery card becomes a detail hero. Separate elements, a matching shared ID: the old geometry becomes the start, the new layout sets the destination. Position, size, corners and opacity morph together.
- **Gesture-driven motion.** Springs carry motion and velocity out of a drag.

Value animation and structural motion are working. Shared elements and deeper gesture integration are part of the roadmap.

**Animate. Settle. Stop scheduling frames.**

## The details are part of the foundation.

### Apple-style corners. By default.

Continuous corners on rounded elements. Switch to classic web / Material circular corners app-wide or per element.

**Same radius. Your kind of curve.**

`cornerRadius` keeps a consistent visual meaning across both styles. Corners are part of the renderer, so backgrounds, images, glass and rounded clipping share the same shape model.

### Depth you can feel.

Backdrop blur. Frosted glass. **Liquid Glass** with refraction, edge light, contact shadows and a spring-driven press lift.

These are materials in the native rendering system, not a separate visual layer. Custom effects use the same shader foundation.

Liquid Glass is currently **experimental**. The lens and press interaction run on device; broader batching, gesture-reactive deformation and liquid merging are being developed toward the full effect.

## TypeScript. Without the detour.

Not just a UI framework. Flash’s native TSX authoring is powered by a purpose-built, ultralight TypeScript execution surface designed from scratch to be **bridgeless by design**.

**Real TypeScript. Typed bytecode. Zero bridge overhead. No heavy JS engine.**

### Bridgeless execution.

Application logic reaches native UI primitives and signals directly in place — **no serialization, no message queue, no asynchronous bridge hop**. 

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

| Capability             | What it brings                                 |
| ---------------------- | ---------------------------------------------- |
| Files & secure storage | Local files and protected app data.            |
| Clipboard & sharing    | Copy, open links and share through the OS.     |
| Haptics & audio        | Feedback you can feel and hear.                |
| Notifications          | The system’s native notification surface.      |
| Camera & microphone    | Device input with explicit permissions.        |
| Location               | Platform location access through the same API. |
| Device & lifecycle     | Device information, locale and app lifecycle.  |

**A capability should be a call, not a package hunt.** Thin wrappers over native OS APIs, with availability checks where platforms differ and asynchronous results delivered as signals. No endless package management or third-party dependency drift.

The configuration direction is **`app.config.ts`**: app identity, icons, enabled builtins and permissions in one TypeScript file, generating the native app declarations.

Touch input, momentum scrolling, soft keyboard integration, safe areas and density-independent sizing already connect the device to the interface. The broader builtin set, richer text editing and accessibility are active development areas.

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

Components are functions. Styles are values. The same pattern composes across the interface.

### In C++

```cpp
Signal<int> count{0};

window([&] {
    column({}, { .gap = 12, .padding = 24 }, [&] {
        label({ .text = std::to_string(count.get()).c_str() },
              { .fontSize = 24 });

        if (button({ .id = "inc" }, {}, [&] {
            label({ .text = "Increment" });
        })) {
            count.set(count.peek() + 1);
        }
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
      <label text={count.get()} style={{ fontSize: 24 }} />
      <button onPress={() => count.set(count.peek() + 1)}>
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

The native Android implementation runs today, including the reactive core, widgets, styling, instanced rendering, MSDF text, animation, structural motion, continuous corners, blur and experimental Liquid Glass. Basic text input, keyboard integration and native platform foundations are also working.

This README presents the technology and its full direction. Platform ports, builtins, 3D and native TSX integration continue to evolve.

| Output                                | Direction                                       | Status                                                       |
| ------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| **Android**                           | Native core · Vulkan                            | Running in development                                       |
| **iOS**                               | Native core · Metal                             | On the roadmap                                               |
| **Windows · macOS · Linux**           | D3D12 / Metal / Vulkan                          | On the roadmap                                               |
| **Web — Flash JSX**                   | Signals · SSR · streaming · SEO · DOM output    | Existing web runtime; shared native authoring in development |
| **Game engines**                      | Embedded native UI                              | Used in development with Ban Engine                          |
| **TV & connected / embedded devices** | Dedicated platform shells and input integration | Exploratory targets                                          |

### Flash Browser — future

_Native applications. Delivered over the internet._

**Typed bytecode. Bridgeless execution. Flash rendering.**

A future output with its own technology and identity, separate from the standard web path.

### Availability

Flash is **closed source** and is being built to be **free to use when released**. It is not available as a public SDK to try yet.

This repository is the public home for documentation, examples, roadmap and development updates. The framework implementation is not hosted here.

## Roadmap

A view of where Flash is going, rather than an internal task list or a promised release calendar.

| Area                 | What's next                                                                                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Interface & text** | Richer caret and selection gestures, handles and magnifier, clipboard actions, IME composition, keyboard avoidance, international text shaping and accessibility. |
| **Layout**           | Closer CSS flexbox behavior, proportional grow factors, minimum-size semantics, line clamping and ellipsis.                                                       |
| **Motion**           | Shared elements across screens, gesture-driven springs and velocity continuity.                                                                                   |
| **Liquid Glass**     | Shared backdrop capture and blur optimization, animated glass properties, gesture-reactive deformation, moving highlights and liquid merging.                     |
| **Native builtins**  | Files, secure storage, clipboard, sharing, haptics, audio, notifications, device services and permission-based capabilities, configured through `app.config.ts`.  |
| **Components**       | Broader unstyled primitives and an optional Flash Kit with its own design language and theme tokens.                                                              |
| **Platforms**        | iOS, Windows, macOS and Linux ports; GLES fallback for older Android devices; public SDK packaging and a richer demo gallery.                                     |
| **Authoring**        | Native TSX on the custom bridgeless TypeScript VM, shared authoring with Flash JSX, scoped lifecycle, hot reload, bundle updates and AOT builds.                  |
| **Rendering & 3D**   | Viewports, 3D in the shared frame, custom passes and compute, content blur and bloom, HDR / wide gamut, render graph and GPU-driven batching.                     |

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