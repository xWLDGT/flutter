# Copilot Instructions for Flutter Repository

This document helps AI assistants become productive when editing the Flutter engine/framework repository.
It is intended for copilots working on the **flutter** monorepo (located at `f:\flutter`) which contains
multiple packages, an engine, tools, and a large suite of tests.

---
## 💡 Big‑Picture Architecture

1. **Engine (`engine/`)** – C++/Skia-based rendering core, platform-specific embedder code.  Look here for
   low-level graphics, input, and platform channel implementations.  APIs are surfaced to Dart via FFI and
   generated bindings (`flutter/lib/ui`).
2. **Framework (`packages/flutter/` and other Dart packages)** – the Dart UI library (widgets, rendering,
   animation).  Most changes you'll make are in `packages/flutter/lib/src/...` and corresponding tests in
   `packages/flutter/test/`.
3. **Tools (`packages/flutter_tools/`)** – the `flutter` CLI, devserver, build pipeline.  Many integration tests
   are under this package.
4. **Dev infrastructure (`dev/`, `tools/`, `scripts/`)** – bots, benchmarks, CI helpers, analysis configs.
5. **Examples & tests** – look in `examples/`, `integration_tests/`, `packages/*/test` for usage patterns.

Communication between the engine and framework happens over platform channels (`services/platform_channel.dart`),
and the engine exposes the `dart:ui` library which the framework imports.

---
## 🔧 Common Workflows

- **Build the SDK or run tools**: use the provided wrapper:
  ```bash
  cd <repo root>
  ./bin/flutter doctor           # verifies dependencies
  ./bin/flutter pub get          # fetch Dart deps for packages you modify
  ./bin/flutter build <target>   # build examples / tests
  ```
- **Running unit tests**: most Dart packages support `flutter test` from their directory.  For engine tests,
  use `./bin/flutter test --local-engine=host_debug_unopt` or run `./flutter/tools/gn` to generate build files.
- **Integration tests** live under `dev/integration_tests` or packages like `integration_test`.
- **Formatting**: run `./bin/cache/dart-sdk/bin/dart format <paths>` or use `dart fix`.
- **Static analysis**: `./bin/flutter analyze` uses the repo-wide `analysis_options.yaml`.
- **Engine build**: consult `CONTRIBUTING.md` or run `./flutter/tools/gn` followed by `ninja -C out/host_debug_unopt`.

Note: the repo is very large; most patches only touch the framework or tools.

---
## 🧩 Project‑Specific Conventions

- **`@visibleForTesting`** is liberally used in framework code.  AI may need to add `// ignore:` comments for internal tests.
- **`// ignore_for_file:`** headers often appear at top of tests for unstable APIs.
- Use **null‑safety** and prefer non‑nullable types; run `dart migrate` when creating new packages.
- Strings passed to `expect()` in tests often compare JSON or diagnostic messages – search tests for examples.
- Use the `testWidgets` helper for widget tests; look at `packages/flutter_test/lib/src/...` for helpers.
- When adding new engine APIs, update `lib/ui/` bindings and run `dart run dev/bots/gn_tests.sh`.

Patterns to mimic:
- **Builders** in `examples/` demonstrate widget patterns (look at `flutter_gallery`).
- **Channel names** are defined in `services/*`; they are canonical – reuse existing names.

---
## 🔎 Searching the Codebase

- Start with `grep`/`rg` over `packages/flutter/lib/src` for framework-related symbols.
- For engine identifiers, search under `engine/src/flutter/` or `lib/ui` (Dart side).
- Many platform embeddings are in `shell/platform/*` (macOS, Windows, Android, iOS).

---
## 🚀 Getting Help for New Contributors

- Review `CONTRIBUTING.md` at repo root for build prerequisites and patch submission guidelines.
- Use `flutter doctor` often; failures in tests often point to missing `clang`, `git`, etc.
- When touching multiple packages, bump versions in their `pubspec.yaml` accordingly.

---
## ✅ Example Tasks

1. **Add a new diagnostic property to `Widget`**: modify `packages/flutter/lib/src/widgets/framework.dart`,
   update corresponding tests in `packages/flutter_test/test/widgets/*`, and ensure `flutter analyze` passes.
2. **Implement a platform channel method**: add code in `services/platform_channel.dart` and implement
   native handler in `shell/platform/<os>/...`; add unit tests under `packages/services/test`.

---
### 📝 Closing Notes
Keep responses concise; cite real file paths and existing classes when suggesting changes. Avoid generic
advice – tailor suggestions to Flutter's layered architecture and testing practices. When uncertain where
an API lives, search the repository; it's usually under `packages/<package_name>` or `engine/src`.

Please review and let me know if any sections need clarification or additional details. 👷‍♂️✨