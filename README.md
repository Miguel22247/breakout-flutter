<<<<<<< HEAD

# breakout-flutter

## About the Project

A Breakout game implementation built with Flutter and Flame. Breakout is a classic arcade game where players control a paddle to bounce a ball and break bricks at the top of the screen. This project leverages the power of Flutter for cross-platform development and the Flame game engine for smooth 2D game mechanics.

### Features

- Classic Breakout gameplay mechanics
- Built with Flutter for cross-platform support (iOS, Android, Web, Desktop)
- Powered by Flame game engine for efficient 2D rendering
- Responsive paddle controls
- Brick-breaking physics

# brick_breaker

A simple Breakout-style game built with Flutter and the Flame game engine.

This repository is a small game project used as a learning/demo app and a
thin game framework for experimenting with Flame's component model and
Flutter overlays.

## Highlights

- Flutter + Flame game engine
- Fixed virtual resolution (camera) for predictable layout across devices
- `ValueNotifier`-driven score UI with Flutter overlays for start/win/lose

## Quick start

Prerequisites: Flutter SDK (see <https://docs.flutter.dev/get-started/install>).

Run locally:

```bash
flutter pub get
flutter run -d <device>
```

Build (examples):

```bash
flutter build apk --release    # Android
flutter build ios              # iOS (requires macOS + Xcode)
```

Useful checks:

```bash
flutter analyze
flutter test   # no tests yet; add under /test
```

## Project architecture (big picture)

- Entry: `lib/main.dart` — instantiates `GameApp`.
- App shell & overlays: `lib/src/widgets/game_app.dart` — wraps a `GameWidget` and
  wires `overlayBuilderMap` keyed by `PlayState.name` (see `lib/src/brick_breaker.dart`).
- Game core: `lib/src/brick_breaker.dart` — `class BrickBreaker extends FlameGame`.
  - Manages game state via `PlayState` enum, spawns/removes components, and exposes
    `ValueNotifier<int> score` used by the UI.
- Config/constants: `lib/src/config.dart` — centralized sizes, colors, and tuning
  constants (e.g. `gameWidth`, `gameHeight`, `brickColors`, `difficultyModifier`).
- Components: `lib/src/components/` (exports in
  `lib/src/components/components.dart`) — `Ball`, `Bat`, `Brick`, `PlayArea`.
- Widgets: `lib/src/widgets/` — UI overlays (`overlay_screen.dart`) and
  `ScoreCard` (`score_card.dart`) which listens to `BrickBreaker.score`.

Design notes:

- The game uses a fixed-resolution `CameraComponent.withFixedResolution(...)`
  so the entire game layout is expressed in the virtual coordinates defined in
  `config.dart`. When adding visuals or collision sizes, prefer those constants
  to keep the layout consistent across devices.
- Components are added/removed using `world.add(...)` and `world.removeAll(world.children.query<T>())`.
- Overlays are toggled by setting `brickBreaker.playState = PlayState.<state>` — the enum
  names are used directly as overlay keys in `GameWidget.overlayBuilderMap`.

## Common developer tasks & examples

- Add a new game component:
  1. Create `lib/src/components/<your_component>.dart`.
  2. Export it from `lib/src/components/components.dart`.
  3. Spawn it from `BrickBreaker` (e.g., in `onLoad()` or `startGame()`):
     `world.add(YourComponent(...));`

- Add a new overlay screen:
  1. Create a widget under `lib/src/widgets/` (e.g. `my_overlay.dart`).
  2. Add it to `overlayBuilderMap` in `GameWidget` inside
     `lib/src/widgets/game_app.dart` using `PlayState.<yourState>.name` as the key.
  3. Toggle via `game.playState = PlayState.<yourState>`.

- Update score UI:
  - `BrickBreaker.score` is a `ValueNotifier<int>`; mutate with `score.value++` and
    the `ScoreCard` (ValueListenableBuilder) will update automatically.

## Tests & CI

There are no unit tests right now. Recommended minimal steps when adding tests:

- Put tests in `/test` and use the standard Flutter test harness.
- Run `flutter test` locally and add a CI job that runs `flutter analyze` and `flutter test`.

## Tools, libraries & conventions

- Key packages (see `pubspec.yaml`): `flame`, `flutter_animate`, `google_fonts`.
- Linting: `flutter_lints` (configured via `analysis_options.yaml`). Run
  `flutter analyze` and follow the project's lints.

Conventions discovered in the codebase:

- Keep layout and game tuning in `lib/src/config.dart`.
- Use `world.children.query<T>()` to find/remove components rather than storing
  direct lists of spawned objects.
- Use enum `PlayState` names as overlay keys. Avoid string literals for overlay keys.

## File overview (important files)

```plaintext
lib/
  main.dart                # App entry
  src/
    brick_breaker.dart     # FlameGame subclass — main game logic
    config.dart            # Constants and layout/tuning values
    components/
      ball.dart
      bat.dart
      brick.dart
      play_area.dart
      components.dart      # re-exports
    widgets/
      game_app.dart        # Material app + GameWidget + overlays
      overlay_screen.dart
      score_card.dart
```

## Contributing / next steps

- If you'd like, I can:
  - add a minimal `/test` example and run `flutter test`;
  - add a GitHub Actions workflow to run `flutter analyze` and `flutter test` on PRs;
  - provide a short code example that adds a custom component and wires an overlay.

Please tell me which of those you'd like next, or any content you'd like added to
this README.
