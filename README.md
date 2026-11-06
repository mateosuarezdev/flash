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

Nothing is retained, so nothing can drift: no stale closures, no ids to babysit, no "why didn't this update", no memoization to hand-tune. Every element keeps its identity from where it is written, whatever appears around it. The work isn't there to optimize.

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

## Components. Familiar from the first line.

A component is a function with its own state. Its setup runs once, when it first appears; what it returns draws it, every time something it shows changes.

```cpp
struct CounterProps { const char* name = ""; };

const auto Counter = Component<CounterProps>([](const CounterProps& props) {
    auto& count = signal(0);                     // its own state
    const char* name = props.name;

    return [&count, name] {                      // draws it
        button({ .onTap = [&] { count.set(count.peek() + 1); } }, {}, [&] {
            label({ .text = (std::string(name) + ": " + std::to_string(count.get())).c_str() });
        });
    };
});

Counter({ .name = "Likes" });                    // used like any element
```

- **State and props.** Signals made in the setup belong to that component, one set per instance. Props are its inputs.
- **No rules to remember.** Each place a component is written is its own instance, however conditional the code around it. No hook order, no dependency arrays, no memoization.
- **Composition.** Components render components, each with its own state.
- **Context.** Provide a value in a component — a form, a router — and read it anywhere below, through any depth of layout and plain functions. The nearest provider wins, and a missing one stops the app with its name instead of quietly handing you a default.
- **One call shape for everything.** A button, your own component, a list: props first, every field named, then children. `button({ .onTap = save }, style, children)`, `Counter({ .name = "Likes" })`, `Show({ .when = open.get() }, children)`. The same shape TSX writes as attributes and children.

### Context

A form provides its own state; everything inside it reads the one it is in, however deep. Two forms on one screen are two values.

```cpp
struct FormState {
    Signal<std::string> email{""};
    Signal<bool>        sending{false};
};
static Context<FormState> FormContext{"Form"};

const auto SubmitButton = Component<>([](const NoProps&) {
    FormState& form = FormContext.get();          // the form it is inside
    return [&form] {
        button({ .onTap = [&] { form.sending.set(true); } }, {}, [&] {
            label({ .text = form.sending.get() ? "Sending…" : "Sign in" });
        });
    };
});

const auto SignInForm = Component<>([](const NoProps&) {
    FormContext.provide();                        // this form's state, for everything inside it
    return [] {
        column({}, { .gap = 12 }, [] {
            EmailField();                         // reads the same form
            SubmitButton();
        });
    };
});
```

### Lifecycle

```cpp
onMount([] { /* first drawn */ });
effect([&] { /* runs again when what it reads changes */ });
onBeforeExit([](ExitToken token) { /* leaving: animate out, then token.done() */ });
onUnmount([] { /* gone: release what it holds */ });
```

A component lives while it is shown. Stop showing it and it goes: its cleanups run, its effects stop, its timers are cancelled, its state is freed. Show it again and it starts fresh.

### Hidden, not gone.

Some things are there but not in use: the tabs you are not on, the screens under the top of a stack. Omit them.

```cpp
Omit({ .when = tab != 0 }, [&] { HomeTab(); });
Omit({ .when = tab != 1 }, [&] { ProfileTab(); });
```

Both tabs are there; the hidden one is skipped from rendering and keeps its state. Skipped means not laid out and not drawn, so a hidden tab costs nothing per frame, however much it holds. Its components stay mounted, their state as it was, and what they started keeps running: a timer in a hidden tab goes on ticking. Bring it back and it is there, as alive as it was, its lists scrolled where you left them.

### Enter. Exit. Resurrect.

Shown through `Show`, a component gets its `onBeforeExit`: it animates out before it goes, live and in place, still in its spot in the layout and still running (a timer inside keeps ticking). Bring it back mid-exit and the same instance turns around, its state intact.

```cpp
const auto Panel = Component<>([](const NoProps&) {
    auto& look = state<Anim::Style>(FlashStyle{ .opacity = 0, .scale = 0.92f });
    onMount([&look] { look.animate({ .opacity = 1, .scale = 1 }); });    // enter

    onBeforeExit([&look](ExitToken token) {                              // exit
        look.animate({ .opacity = 0, .scale = 0.92f }, { .duration = 0.3f },
                     [token] { token.done(); });                         // finished: let it go
        token.onCancel([&look] { look.animate({ .opacity = 1, .scale = 1 }); });   // brought back
    });

    return [&look] {
        column({}, look.get(), [&] { /* everything inside fades with it */ });
    };
});

Show({ .when = open.get() }, [&] { Panel(); });
```

No prebuilt fades to fight: enter and exit are a few lines of your own motion, to your taste. One component can carry a whole group out, or each part can leave at its own pace. The exit is a callback with a "done" at its end, so anything can happen in between: a spring thrown from a gesture, a sequence, a save that has to finish first.

- **Leave in place, or step out of the flow.** A leaving component keeps its space until it is gone, or steps out of the layout at once and fades on top while everything around it closes in.
- **Switch between views.** Pick one of several branches by a value — a tab, a step, a status — and the old one slides or fades out on top while the new one takes its place. Change your mind mid-way and it turns back.
- **Lists whose items come and go on their own.** Hand a list your items and how to tell them apart: each item keeps its own state wherever it moves, an added one plays its own entrance, and a removed one plays its exit where it stood while the rest close in, or holding its space until it is done. Put it back mid-way and it turns back, state intact.
- **Gaps that close themselves.** Give the container a size transition and it animates to its new size when something leaves, with everything below following. No coordination code: the component owns how it leaves, the container owns its size.

### Control flow, as components

The shapes of a dynamic UI, each a call like any other:

| | |
| --- | --- |
| `Show` | One branch, shown or not, its components animating out before they go and turning back if wanted again. |
| `Switch` | One of several branches by a value: a tab, a step, a status. The old one leaves while the new one comes in. |
| `For` | A list by key: each item keeps its own state wherever it moves, and plays its own entrance and exit. |
| `Omit` | Skipped from rendering, state kept: the tabs you are not on. |
| `List` | Virtualized: ten thousand rows cost what fits on screen, with snapping for pickers and carousels. *Planned.* |

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

Multi-threaded where it pays: files, system calls and image decoding run on workers, while the UI stays on one thread you never have to synchronize.

## Slow work. Off the screen.

A screen draws at once. Flash's own slow work, such as decoding images, already runs on workers. Yours can too: hand anything that takes time (a file, the system keystore, a parse, a computation) to `Scheduler::Run`, and its answer comes back to the UI thread, ready to drop into a signal.

```cpp
auto& note = signal(std::string("loading..."));

Scheduler::Run([] { std::string t; FileSystem::ReadText(path, t); return t; },   // on a worker
               [&note](std::string t) { note.set(t); });                        // back on the UI thread
```

No locks, no threads to manage, no UI touched from the wrong thread. The answer is whatever the work returns, success or failure included. Cancel it when the screen goes, and it never arrives.

**The screen never waits.**

### Images, at the size you see them.

```cpp
image({ .src = "https://example.com/photo.jpg" },
      { .width = Px(100), .height = Px(100), .objectFit = ObjectFit::Cover });
```

- **One line, any source.** A file shipped with the app, a photo on the device, a URL.
- **Off the UI thread.** Downloaded, read and decoded in the background. The image's box holds its place until it arrives, so nothing jumps.
- **Decoded at the size it is drawn.** A 4000×3000 photo in a 100-point box takes a few hundred kilobytes of memory, not the ~48 MB of the full picture. A grid of large photos scrolls smoothly. Open one full screen and a sharper copy takes over.
- **Only what is on screen loads.** Images below the fold cost nothing until they scroll into view.
- **Shared and kept.** The same image anywhere is one decode, and coming back to it is instant.
- **Photos upright.** Turned the way the camera held them.

## Change the layout. Keep the flow.

Motion belongs to the framework. Tweens and springs animate individual values or whole styles: size, padding, radius, opacity and color move together. Retarget in flight; springs carry their velocity.

- **Layout that animates itself.** Add or remove items and the list makes room. Resize a component and its layout carries it and its children into the new arrangement.
- **Enter. Exit. Resurrect.** Components animate out before they go, live and in place. Bring one back mid-exit and the animation turns around: the same instance continues from where it is, its state intact.
- **Transforms that move without reflow.** Translate and scale what is drawn — a card following the finger, a button pressed in — while the layout stays still. Touches follow what you see.
- **Gesture-driven motion.** A spring starts at the drag's velocity, so a flick lands as fast as it was thrown. One value maps onto another: a backdrop fades with the sheet the finger is pulling.
- **Shared elements across screens.** A gallery card becomes a detail hero. Separate elements, a matching shared ID: the old geometry becomes the start, the new layout sets the destination. Position, size, corners and opacity morph together. *Planned.*

**Animate. Settle. Stop scheduling frames.**

## Every touch has one owner.

One arbiter decides who owns each touch, the way native platforms do:

- **A scroll never presses the button it ends on.** Buttons track the finger like native controls, and let go when it slides away.
- **Nested scrolling just works.** A carousel inside a list follows the drag's direction; the innermost view wins; a touch catches a gliding list mid-flight.
- **Gestures that run together.** Simultaneous gestures and hand-offs: pull a sheet down from the top of its list and the sheet takes over in the same touch, as iOS sheets do, then gives the movement back when you drag up again. Only the gestures you group together; a picker inside the list never moves the sheet.
- **Drags that start where you are.** A drag moves from where it is the moment it wins the touch, never jumping by the distance it took to tell it was a drag.
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

### Text that feels native.

Drag the caret and it floats under your finger, then springs into the gap where you'll type. A glass magnifier grows out of the text while you drag. Double tap selects a word, with handles you can drag; the system's own edit menu does the rest: cut, copy, paste, share, and every app on the phone that works with text. The long press behaves the way each platform's users expect: it moves the caret on iPhone and selects the word on Android.

### The keyboard, handled.

Scroll views and floating panels move clear of the soft keyboard, following it as it slides, opt-in per container. Safe areas and density-independent sizing come with every screen.

### Rotate. Resize. Keep going.

Turn the phone in the middle of a fling, unfold it, drag the window bigger: the scroll keeps gliding, the field keeps its text, every component keeps its state. Flash's state lives in the app itself, not in the window the system hands it, so when the screen changes there is nothing to save and nothing to restore. The layout simply flows into the new shape.

## TypeScript. Without the detour.

Not just a UI framework. Flash’s native TSX authoring is powered by a purpose-built, ultralight TypeScript execution surface designed from scratch to be **bridgeless by design**.

**Real TypeScript. Typed bytecode. Zero bridge overhead. No heavy JS engine.**

### Bridgeless execution.

Application logic reaches native UI primitives and signals directly in place — **no serialization, no message queue, no asynchronous bridge hop**. A TypeScript signal _is_ the native signal.

When app state changes, the native layout and GPU rendering engine respond in the exact same frame, whether authored in C++ or TSX.

### TSX describes. Native renders.

Components, props and scoped signals on the surface. Native layout, animation and GPU rendering underneath. A familiar component lifecycle, cleanup and context above an immediate-mode foundation.

State-preserving hot reload and over-the-air bundle streaming are part of the authoring workflow being built around it, in every build: the same VM runs in development and in release.

### Native first. The web, too.

The standard web path is **Flash JSX**, my own existing web runtime: signals, SSR, streaming, hydration, routing, SEO, providers and layout animations.

**Direct web output. No canvas.**

Shared TSX components, state and logic are the goal: native rendering on device, Flash JSX on the web.

## Native essentials. Included.

Native capabilities are part of Flash’s foundation, exposed directly through one platform API:

| Capability                 | What it brings                                                                       |
| -------------------------- | ------------------------------------------------------------------------------------ |
| Files & secure storage     | The app's own folders, and protected data in the system keystore. Secure storage behind a fingerprint, and large key/value storage: *planned*. |
| Clipboard, links & sharing | Copy, open links and other apps, share through the system sheet. Links that open the app (deep links) and an in-app browser for sign-in and help: *planned*. |
| Pickers                    | The system photo and file pickers. No permission: the user choosing is the consent.  |
| HTTP & network             | Requests from the platform's own stack, and whether the device is online, and how.   |
| Haptics                    | Feedback you can feel. Custom vibration patterns: *planned*.                         |
| Audio                      | Instant sounds for the interface, players for music, voice and remote streams, and recording from the microphone. |
| Notifications              | Shown now or scheduled, surviving a reboot, the tap delivered to your app. Push notifications and the icon badge: *planned*. |
| Permissions                | One model for every permission, as signals: the UI follows a change made in settings. |
| Device & lifecycle         | Device information, locale and the app's lifecycle, as signals. Battery level and charging: *planned*. |
| Keyboard & text editing    | The system keyboard in every type, and the system's own edit menu over Flash's fields. |
| Display & system UI        | Dark or light as a signal, the system bars' icons and immersive mode, the display's refresh rate, keeping the screen awake while something needs it, which way the screen is and a lock for the screen that needs one. Screen brightness, blocking screenshots: *planned*. |
| Accessibility settings     | The system's text size, bold text, high contrast, reduced motion and reduced transparency, as a signal. *Planned.* |
| Camera, location & more    | Camera, location, contacts and an in-app media library, through the same model. Saving files out to the device (a chosen folder, Downloads, the gallery). *Planned.* |
| Sensors & motion           | Motion and orientation (tilt and parallax), compass, pressure, light, and step counts. *Planned.* |
| Identity & payments        | Biometric confirmation, in-app purchases and subscriptions, the store's review prompt. *Planned.* |
| Speech                     | Text read aloud with the device's voices, and dictation. *Planned.*                  |
| More from the device       | Calendar, Bluetooth LE, NFC, barcode and QR scanning, app shortcuts, background tasks. *Planned.* |

**A capability should be a call, not a package hunt.** Thin wrappers over native OS APIs, with availability checks where platforms differ. State the OS owns arrives as signals; answers that take time arrive on the UI thread, ready to drop into your own. No endless package management or third-party dependency drift.

The configuration direction is **`app.config.ts`** (*in development:* Android, with the app's name, package, version, a
builtin's switch and build variants): app identity, icons, enabled builtins and permissions in one TypeScript file, generating the native app declarations. Builtins that need nothing are simply there; only those with a cost, such as a permission, are switched on. *Planned.*

### Batteries. Included too.

The problems every app has are part of the framework, not assembled from packages:

- **Router.** Path first: one pathname is the truth, so deep links, back and restoring all just work. Tabs that keep every tab alive, stacks with native swipe-back, nesting to any depth, modals and sheets as routes, each screen with its own state and its own motion, and tab presses that land where you left off. *In development.*
- **Data queries.** Caching, request de-duplication, stale-while-revalidate, invalidation, retries and mutations, with a local cache that survives restarts, optimistic updates that roll back on failure, and changes made offline sent when the network is back. One implementation for C++ and TSX. *Planned.*
- **Translations.** Messages in the industry's formats, with plurals, genders and variables; numbers, dates and currencies for the user's locale, in their preferred languages and with fallbacks. Change the phone's language and the app follows, open. *Planned.*

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

        button({ .onTap = [&] { count.set(count.peek() + 1); } }, {}, [&] {
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
      <label text={count.get()} style={{ fontSize: 24 }} />
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

The native Android implementation runs today: the reactive core, components with their own state, lifecycle and context, exit animations with resurrection, lists whose items animate in and out, hidden tabs that keep their state, widgets, styling, instanced rendering, MSDF text, animation, structural motion and transforms, continuous corners, blur and experimental Liquid Glass. Text input with native-feeling editing (caret dragging, selection, handles, the magnifier, the system's edit menu), keyboard avoidance, the complete touch and gesture system, timers, background work, images by source decoded at the size they are drawn, and twenty-three native builtins — from files and HTTP to audio playback and recording, permissions, notifications, dark mode, the system bars, the refresh rate, keeping the screen awake and orientation — work on device too.

This README presents the technology and its full direction. Platforms, builtins, 3D and native TSX integration continue to evolve.

| Output                                | Direction                                       | Status                                                       |
| ------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| **Android**                           | Native core · Vulkan                            | Running in development                                       |
| **iOS**                               | Native core · Metal                             | On the roadmap                                               |
| **Windows · macOS · Linux**           | D3D12 / Metal / Vulkan                          | On the roadmap                                               |
| **Web — Flash JSX**                   | Signals · SSR · streaming · SEO · DOM output    | Existing web runtime; shared native authoring in development |
| **Game engines**                      | Embedded native UI                              | Used in development with Ban Engine                          |
| **TV & connected / embedded devices** | Dedicated platform shells and input integration | Exploratory targets                                          |

### Latest progress

- **November 2026** — Background work: anything slow runs on a worker while the screen keeps moving, its answer back on the UI thread. Images by source (a file in the app, a photo on the device, a URL) decoded off the UI thread at the size they are drawn, loading as they scroll into view, shared and kept.
- **November 2026** — Audio recording from the microphone, asking for the permission at the moment it is needed, with a live level meter; the builtins' temporary files cleaned up at every start, off the main thread.

- **October 2026** — Faster startup: fonts and shaders are prepared when the app is built, not on the phone at every launch, so the first frame arrives about seven times sooner.
- **October 2026** — Landscape drawn the way games do it: each frame already turned to the panel, so the system has nothing left to rotate, at full frame rate; glass and blur following the screen through rotation, split-screen and folding.
- **October 2026** — Native builtins: dark or light following the system, the system bars (their icons' colour and immersive mode), the display's refresh rate asked for by the screen that needs it, keeping the screen awake while something needs it, and orientation: following the phone, with a lock for the screen that needs one.
- **October 2026** — Lists whose items animate in and out, every element keeping its identity from where it is written whatever appears around it, hidden tabs skipped from layout and drawing while they keep their state, and context.
- **October 2026** — TypeScript running natively on Android: my own bridgeless TypeScript VM, embedded in Flash, executing on the device and calling into native code. The first step toward authoring Flash in TSX.
- **October 2026** — Components on immediate mode: a setup that runs once, state per instance, identity from where the component is written, a lifecycle with cleanup. Exits the component writes itself, kept live in place or stepping out of the flow, turning around when brought back; containers that animate to their new size as things leave.
- **October 2026** — Text editing that feels native: the caret floating under the finger, a glass magnifier, word selection with handles, the system's edit menu, the long press per platform.
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

| Area                 | What's next                                                                                                                                       |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Interface & text** | Multi-line fields, password fields, IME composition, autofill, interactive keyboard dismissal, international text shaping and accessibility. |
| **Gestures**         | Multi-touch: pinch and rotate.                                                                                                                    |
| **Layout**           | Closer CSS flexbox behavior, proportional grow factors, minimum-size semantics, line clamping and ellipsis, virtualized lists.                    |
| **Motion**           | Shared elements across screens, rotation transforms, shapes that move apart from their content, per-corner radii, squash, stretch and wobble, merging and morphing — for any element. |
| **Liquid Glass**     | Shared backdrop capture and blur optimization, animated glass properties, highlights that follow the touch.                                      |
| **Native builtins**  | Camera, location, contacts, an in-app media library, push notifications, background audio with system media controls, accessibility settings, sensors, biometrics, in-app purchases, deep links, configured through `app.config.ts`. |
| **Batteries**        | Router: path first, tabs, stacks with native swipe-back, nesting, modals and sheets as routes. Data queries: caching, syncing, revalidation, local persistence, optimistic updates. Translations: industry-standard messages, plurals and locale formatting. |
| **Components**       | Broader unstyled primitives and an optional Flash Kit: Flash's own design language and feel, with theme tokens. |
| **Platforms**        | iOS, Windows, macOS and Linux; GLES fallback for older Android devices; public SDK packaging and a richer demo gallery.                     |
| **Authoring**        | Native TSX on the custom bridgeless TypeScript VM, shared authoring with Flash JSX, components and their lifecycle from TSX, hot reload and bundle updates.  |
| **Rendering & 3D**   | Viewports, 3D in the shared frame, custom passes and compute, content blur and bloom, HDR / wide gamut, render graph and GPU-driven batching.     |

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
