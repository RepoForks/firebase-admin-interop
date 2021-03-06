[![Build Status](https://travis-ci.org/pulyaevskiy/firebase-admin-interop.svg?branch=master)](https://travis-ci.org/pulyaevskiy/firebase-admin-interop)

Write server-side Firebase applications in Dart using Node.js as a runtime.

## Installation

1. Add this package as a dependency to your `pubspec.yaml`:

```yaml
dependencies:
  firebase_admin_interop: ^1.0.0-dev
```

Run `pub get`.

2. Create `package.json` file to install Node.js modules used by this library:

```json
{
    "dependencies": {
        "firebase-admin": "~5.8.1",
        "@google-cloud/firestore": "~0.11.1"
    }
}
```

Run `npm install`.

## Usage

Below is a simple example of using Realtime Database client:

```dart
import 'dart:async';
import 'package:firebase_admin_interop/firebase_admin_interop.dart';

Future<void> main() async {
  final serviceAccountKeyFilename = '/absolute/path/to/service-account.json';
  final admin = FirebaseAdmin.instance;
  final cert = admin.certFromPath(serviceAccountKeyFilename);
  final app = admin.initializeApp(new AppOptions(
    credential: cert,
    databaseURL: "YOUR_DB_URL",
  ));
  final ref = app.database().ref('/test-path');
  // Write value to the database at "/test-path" location.
  await ref.setValue("Hello world");
  // Read value from the same database location.
  var snapshot = await ref.once("value");
  print(snapshot.val()); // prints "Hello world".
}

```

Note that it is only possible to use JSON-compatible values when reading
and writing data to the Realtime Database. This includes all primitive
types (`int`, `double`, `bool`), string values (`String`) as well as
any `List` or `Map` instance.

> For Firestore there are a few more supported data types, like `DateTime`
> and `GeoPoint`.

## Building

This library depends on [node_interop][] package which provides Node.js 
bindings and [build_node_compilers][] package which allows compiling
Dart applications as Node.js modules.

[node_interop]: https://pub.dartlang.org/packages/node_interop
[build_node_compilers]: https://pub.dartlang.org/packages/build_node_compilers

To enable builders provided by [build_node_compilers][] first add following
dev dependencies to your `pubspec.yaml`:

```yaml
dev_dependencies:
  build_runner: ^0.7.9
  build_node_compilers: ^0.1.0
```

Next, create `build.yaml` file with following contents:

```yaml
targets:
  $default:
    sources:
      - "lib/**"
      - "node/**" # Assuming your main Dart file is in node/ folder (recommended).
      - "test/**" # Needed if you want compile and run tests with DDC
```

You can now build your project using `build_runner`:

```bash
# By default compiles with DDC
pub run build_runner build --output=build

# To compile with dart2js:
pub run build_runner build \
  --define="build_node_compilers|entrypoint=compiler=dart2js" \
  --define="build_node_compilers|entrypoint=dart2js_args=[\"--checked\"]" \ # optional, enables checked mode
  --output=build/
```

## Status

This is a early development version and breaking changes are likely to occur.

Make sure to checkout [CHANGELOG.md](https://github.com/pulyaevskiy/firebase-admin-interop/blob/master/CHANGELOG.md)
after every release, all notable changes and upgrade instructions will
be described there.

Current implementation coverage report:

- [x] admin
- [ ] admin.auth
- [x] admin.app
- [x] admin.credential
- [x] admin.database
- [x] admin.firestore
- [ ] admin.messaging
- [ ] admin.storage

## Features and bugs

Please file feature requests and bugs at the [issue tracker][tracker].

[tracker]: https://github.com/pulyaevskiy/firebase-admin-interop/issues
