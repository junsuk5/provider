# 4.1.0

- Added `builder` on the different providers.
  This parameters simplifies situations where we want to directly consume the value.

  As such, instead of:

  ```dart
  Provider(
    create: (_) => Something(),
    child: Consumer<Something>(
      builder: (_, something, __) {
        return Text('$something');
      },
    ),
  )
  ```

  we can write:

  ```dart
  Provider(
    create: (_) => Something(),
    builder: (_, something, __) {
      return Text('$something');
    },
  )
  ```

  The behavior is the same. This is only a small syntax sugar.

- Added a two extensions on [BuildContext], to slightly reduce the boilerplate:

  | before                                   | after               |
  | ---------------------------------------- | ------------------- |
  | `Provider.of<T>(context, listen: false)` | `context.read<T>()` |
  | `Provider.of<T>(context)`                | `context.watch<T>`  |

- Added a `Locator` typedef and an extension on [BuildContext], to help with
  being able to read providers from a class that doesn't depend on Flutter.

# 4.0.1

- stable release of 4.0.0-hotfix+1
- fix some typos

# 4.0.0-hotfix.1

- removed the inference of the `listen` flag of `Provider.of` in favor of an exception in debug mode if `listen` is true when it shouldn't.

  This is because it caused a critical performance issue. See https://github.com/rrousselGit/provider/issues/305

# 4.0.0

- `Selector` now deeply compares collections by default, and offers a `shouldRebuild`
  to customize the rebuild behavior.
- renamed `ProviderNotFoundError` to `ProviderNotFoundException`.
  This allows calling `Provider.of` inside a `try/catch` without triggering a
  warning.
- update provider to work with Flutter 1.12.1
- The creation and listening of objects using providers is now performed lazily.
  This means that objects are created the first time the value is read instead of
  the first time the provider is mounted.
- ~~The `listen` argument of `Provider.of` is now automatically inferred.
  It is no longer necessary to pass `listen: false` when calling `Provider.of`
  outside of the widget tree.~~ removed by 4.0.0-hotfix. See https://github.com/rrousselGit/provider/issues/305
- renamed `initialBuilder` & `builder` of `*ProxyProvider` to `create` & `update`
- renamed `builder` of `*Provider` to `create`
- added a `*ProxyProvider0` variant

# 3.2.0

- Deprecated "builder" of providers in favor to "create"
- Deprecated "initialBuilder"/"builder" of proxy providers in favor of respectively
  "create" and "update"

# 3.1.0

- Added `Selector`, similar to `Consumer` but can filter unneeded updates
- improved the overall documentation
- fixed a bug where `ChangeNotifierProvider.value` didn't update dependents
  when the `ChangeNotifier` instance changed.
- `Consumer` can now be used inside `MultiProvider`
  ```dart
  MultiProvider(
    providers: [
      Provider(builder: (_) => Foo()),
      Consumer<Foo>(
        builder: (context, foo, child) =>
          Provider.value(value: foo.bar, child: child),
      )
    ],
  );
  ```

# 3.0.0

## breaking (see the readme for migration steps):

- `Provider` now throws if used with a `Listenable`/`Stream`. This can be disabled by setting
  `Provider.debugCheckInvalidValueType` to `null`.
- The default constructor of `StreamProvider` has now builds a `Stream`
  instead of `StreamController`. The previous behavior has been moved to `StreamProvider.controller`.
- All `XXProvider.value` constructors now use `value` as parameter name.
- Added `FutureProvider`, which takes a future and updates dependents when the future completes.
- Providers can no longer be instantiated using `const` constructors.

## non-breaking:

- Added `ProxyProvider`, `ListenableProxyProvider`, and `ChangeNotifierProxyProvider`.
  These providers allows building values that depends on other providers,
  without loosing reactivity or manually handling the state.
- Added `DelegateWidget` and a few related classes to help building custom providers.
- Exposed the internal generic `InheritedWidget` to help building custom providers.

# 2.0.1

- fix a bug where `ListenableProvider.value`/`ChangeNotifierProvider.value`
  /`StreamProvider.value`/`ValueListenableProvider.value` subscribed/unsubscribed
  to their respective object too often
- fix a bug where `ListenableProvider.value`/`ChangeNotifierProvider.value` may
  rebuild too often or skip some.

# 2.0.0

- `Consumer` now takes an optional `child` argument for optimization purposes.
- merged `Provider` and `StatefulProvider`
- added a "builder" constructor to `ValueListenableProvider`
- normalized providers constructors such that the default constructor is a "builder",
  and offer a `value` named constructor.

# 1.6.1

- `Provider.of<T>` now crashes with a `ProviderNotFoundException` when no `Provider<T>`
  are found in the ancestors of the context used.

# 1.6.0

- new: `ChangeNotifierProvider`, similar to scoped_model that exposes `ChangeNotifer` subclass and
  rebuilds dependents only when `notifyListeners` is called.
- new: `ValueListenableProvider`, a provider that rebuilds whenever the value passed
  to a `ValueNotifier` change.

# 1.5.0

- new: Add `Consumer` with up to 6 parameters.
- new: `MultiProvider`, a provider that makes a tree of provider more readable
- new: `StreamProvider`, a stream that exposes to its descendants the current value of a `Stream`.

# 1.4.0

- Reintroduced `StatefulProvider` with a modified prototype.
  The second argument of `valueBuilder` and `didChangeDependencies` have been removed.
  And `valueBuilder` is now called only once for the whole life-cycle of `StatefulProvider`.

# 1.3.0

- Added `Consumer`, useful when we need to both expose and consume a value simultaneously.

# 1.2.0

- Added: `HookProvider`, a `Provider` that creates its value from a `Hook`.
- Deprecated `StatefulProvider`. Either make a `StatefulWidget` or use `HookProvider`.
- Integrated the widget inspector, so that `Provider` widget shows the current value.

# 1.1.1

- add `didChangeDependencies` callback to allow updating the value based on an `InheritedWidget`
- add `updateShouldNotify` method to both `Provider` and `StatefulProvider`

# 1.1.0

- `onDispose` has been added to `StatefulProvider`
- [BuildContext] is now passed to `valueBuilder` callback

[buildcontext]: (https://api.flutter.dev/flutter/widgets/BuildContext-class.html)