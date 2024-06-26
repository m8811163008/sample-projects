Table of content
1. [Cryptography iOS-android-app](#cryptography-iOS-android-app)
2. [An international payment and E-money project](#emoney-project)
3. [E-commerce and multi-vendors application](#electronic-commerce-multi-vendor)
4. [Other projects to explore](#other-projects)

# Cryptography-iOS-android-app <a name="cryptography-iOS-android-app"></a>
Welcome to the Sample Application!

The Finance Application is a secure and adaptive mobile application developed using Flutter for both iOS and Android platforms to seamlessly adapt to both iOS and Android platforms. With its layered architecture using bloc, it provides an efficient user experience.

We prioritize the security of your financial data. The Finance Application implements industry-standard security measures to protect your sensitive information.
<p float="left">
  <img src="assets/android%20screens/flutter_07.png" alt="material dialog" height="400" />
     <img src="assets/ios%20screens/flutter_09.png" alt="ios bottom sheet" height="400" />
  <img src="assets/gif/line_chart.gif" alt="line chart gif" height="400" />
</p>

## Architecture
The Finance Application follows a layered architecture and feature-driven development approach, allowing for easy expansion and scalability as our team adds new features.

At the core, we have the RemoteApi layer, which handles communication with the server using HTTP. Additionally, we have the LocalStorage layer, which utilizes Hive NoSQL database for efficient and fast data storage.
<p float="left">
  <img src="assets/android%20screens/flutter_01.png" alt="login page" height="400" />
     <img src="assets/ios%20screens/flutter_01.png" alt="login page sheet" height="400" />
</p>
For each feature, we have a repository that acts as a bridge between the UI layer and the data layer. These repositories also include mappers to seamlessly map data models (API and local) to domain models, ensuring consistency across the UI and business logic layers.

On top of the repositories, we have the Component_library layer. This layer contains common widgets such as Scaffolds and TextFields as well as helper methods that adhere to Cupertino and Material Design specifications. We also have a theming model in this package to provide a consistent visual experience.
<p float="left">
  <img src="assets/android%20screens/flutter_03.png" alt="login page" height="400" />
     <img src="assets/ios%20screens/flutter_03.png" alt="login page sheet" height="400" />
</p>
Besides the Component_library we have features packages like login, register, dashboard and etc. Look at this [application architecture](https://github.com/m8811163008/Quotes#architecture)

## Security

At the Finance Application, we prioritize the security of your financial data. To enhance the security measures, we have implemented the following features:

Biometric Access: For devices that support biometric capabilities, we have enabled biometric authentication to provide an extra layer of security This ensures that only authorized users can access the application during different lifecycle changes.
<p float="left">
  <img src="assets/gif/face_id.gif" alt="face id" height="400" />
  <img src="assets/ios%20screens/flutter_17.png" alt="ios setting" height="400" />
  <img src="assets/gif/use_keypass.gif" alt="key pass" height="400" />
</p>
Secure Data Storage: To protect sensitive data such as sessions and credentials, we utilize secure shared preferences (AES-CBC-128 algorithm for encryption for both keys and values) and HiveCipher. These encryption techniques ensure that your data is stored securely and cannot be easily accessed by unauthorized individuals.

```dart
class KeyValueStorage {
  static const _secureKey = 'secure-sample-key';

  KeyValueStorage(
      {@visibleForTesting HiveInterface? hive,
      @visibleForTesting FlutterSecureStorage? secureStorage})
      : _hive = hive ?? Hive,
        _secureStorage = secureStorage ?? FlutterSecureStorage() {
    // register adaptors
  }

  // Opens hive box to store key value in local storage.
  //
  // If you need a secure box pass [isSecureBox] argument to true.
  // [isTemporary] data is used each time you open the application for example
  // to store the pool data.
  Future<Box<T>> _openBox<T>(String boxKey,
      {required bool isTemporary, required bool isSecureBox}) async {
    List<int>? encryptionKeyUnit8List;
    // Retrieve encryptedKey from flutter_scure_storage
    // Keychain used for ios and AES encription used for android devices.
    if (isSecureBox) {
      final encryptedKey = await _secureStorage.read(key: _secureKey);
      if (encryptedKey == null) {
        final key = _hive.generateSecureKey();
        await _secureStorage.write(
            key: _secureKey, value: base64UrlEncode(key));
      }
      final key = await _secureStorage.read(key: _secureKey);
      encryptionKeyUnit8List = base64Url.decode(key!);
    }
    if (_hive.isBoxOpen(boxKey)) {
      return _hive.box(boxKey);
    }
    final directory = await (isTemporary
        ? getTemporaryDirectory()
        : getApplicationDocumentsDirectory());
    final encyptionCipher =
        isSecureBox ? HiveAesCipher(encryptionKeyUnit8List!) : null;
    return _hive.openBox(
      boxKey,
      path: directory.path,
      encryptionCipher: encyptionCipher,
    );
  }
}
```
<p float="left">
  <img src="assets/android%20screens/flutter_16.png" alt="android setting" height="400" />
  <img src="assets/gif/secure_storage.gif" alt="secure storage" height="400" />
</p>

## Cutome widgets
In application development, I strive to deliver a pixel-perfect user interface to honor our talented UI/UX design team and provide a high-fidelity product experience. I developed widgets to achieve this level of precision and attention to detail.
<p float="left">
  <img src="assets/pie_chart_figma.png" alt="android setting" width="800" />
</p>
To create the pie chart, we utilized a custom painter and applied mathematical calculations to draw the arcs accurately. We also incorporated spacing between the segments and added curved designs at the end of each segment, ensuring a visually appealing chart.
<p float="left">
  <img src="assets/pie_chart.png" alt="android setting" width="800" />
</p>

```dart
class CurvePainter extends CustomPainter {

  ...
  // Calculate the arc start offset relative to previous segments.
  Offset _calculateRelativeArcPoint(
      {required double angle, double lineLenght = 5, bool isStart = false}) {
    int kharejGhesmat = angle ~/ (2 * pi);
    angle = angle - kharejGhesmat * (2 * pi);

    late final double x;
    late final double y;
    if (angle >= 0 && angle <= pi / 2) {
      x = isStart ? -lineLenght * cos(angle) : -lineLenght * cos(angle);
      y = isStart ? -lineLenght * sin(angle) : -lineLenght * sin(angle);
    } else if (angle > pi / 2 && angle < pi) {
      x = lineLenght * sin(angle - pi / 2);
      y = -lineLenght * cos(angle - pi / 2);
    } else if (angle >= pi && angle <= 3 * pi / 2) {
      x = lineLenght * cos(angle - pi);
      y = lineLenght * sin(angle - pi);
    } else if (angle > 3 * pi / 2 && angle < 2 * pi) {
      x = -lineLenght * sin(angle - 3 * pi / 2);
      y = lineLenght * cos(angle - 3 * pi / 2);
    } else {
      throw Exception('degree is not valid');
    }
    return Offset(x, y);
  }

  // Returns the offset of the starting point of the first segment of the pie chart.
  Offset _calculateStartPoint(
      {required Offset center, required double radius, required double angle}) {
    final double x = radius * cos(angle);
    final double y = radius * sin(angle);
    return Offset(x, y) + center;
  }
}
```

Additionally, a challenge with the shadow effects algorithm when the drawer of scaffolds opens. To overcome this issue and ensure effective shadow effects, we implemented the ImageFiltered widget. This solution resolved the bug now the shadows are displayed seamlessly.
<p float="left">
  <img src="assets/android%20screens/flutter_02.png" alt="android pie chart" height="400" />
  <img src="assets/ios%20screens/flutter_02.png" alt="ios pie chart" height="400" />
</p>
By leveraging custom widgets and implementing innovative solutions, we aim to provide an exceptional user experience within the Finance Application.

## iOS development
I have utilized Cupertino widgets to ensure a seamless and native user on iOS devices. These widgets, such as the date picker, time picker, and bottom sheets, adhere to Apple's design guidelines ([iOS-human-interface](https://developer.apple.com/design/human-interface-guidelines)), providing a familiar and intuitive interface for iOS users.

We are committed to delivering a professional and polished iOS experience within cross-platform applications. For Android development, we adhere [Material Design Version 3](https://m3.material.io/) specifications.

## other screenshots (iOS)
<p float="left">
  <img src="assets/ios%20screens/flutter_05.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_07.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_08.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_11.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_13.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_15.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_14.png" alt="" height="400" />
  <img src="assets/ios%20screens/flutter_18.png" alt="" height="400" />
</p>

## other screenshots (android)
<p float="left">
  <img src="assets/android%20screens/flutter_03.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_05.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_08.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_09.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_14.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_15.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_16.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_11.png" alt="" height="400" />
  <img src="assets/android%20screens/flutter_12.png" alt="" height="400" />
</p>


# An international payment and E-money project<a name="emoney-project"></a>
 <p float="left">
  <img src="assets/emoney/flutter_01.png" alt="" height="400" />
  <img src="assets/emoney/flutter_02.png" alt="" height="400" />
  <img src="assets/emoney/flutter_03.png" alt="" height="400" />
  <img src="assets/emoney/flutter_04.png" alt="" height="400" />
  <img src="assets/emoney/flutter_05.png" alt="" height="400" />
  <img src="assets/emoney/flutter_06.png" alt="" height="400" />
  <img src="assets/emoney/flutter_07.png" alt="" height="400" />
  <img src="assets/emoney/flutter_08.png" alt="" height="400" />
  <img src="assets/emoney/flutter_09.png" alt="" height="400" />
  <img src="assets/emoney/flutter_10.png" alt="" height="400" />
  <img src="assets/emoney/flutter_11.png" alt="" height="400" />
  <img src="assets/emoney/flutter_12.png" alt="" height="400" />
  <img src="assets/emoney/flutter_13.png" alt="" height="400" />
  <img src="assets/emoney/flutter_14.png" alt="" height="400" />
</p>
# Screenshots of an E-commerce and multi-vendors application<a name="electronic-commerce-multi-vendor"></a>
 <p float="left">
  <img src="assets/ishop/flutter_01.png" alt="" height="400" />
  <img src="assets/ishop/flutter_02.png" alt="" height="400" />
  <img src="assets/ishop/flutter_03.png" alt="" height="400" />
  <img src="assets/ishop/flutter_04.png" alt="" height="400" />
  <img src="assets/ishop/flutter_05.png" alt="" height="400" />
  <img src="assets/ishop/flutter_06.png" alt="" height="400" />
  <img src="assets/ishop/flutter_07.png" alt="" height="400" />
  <img src="assets/ishop/flutter_08.png" alt="" height="400" />
  <img src="assets/ishop/flutter_09.png" alt="" height="400" />
  <img src="assets/ishop/flutter_10.png" alt="" height="400" />
  <img src="assets/ishop/flutter_11.png" alt="" height="400" />
  <img src="assets/ishop/flutter_12.png" alt="" height="400" />
  <img src="assets/ishop/flutter_13.png" alt="" height="400" />
  <img src="assets/ishop/flutter_14.png" alt="" height="400" />
  <img src="assets/ishop/flutter_15.png" alt="" height="400" />
  <img src="assets/ishop/flutter_16.png" alt="" height="400" />
</p>
# Some other projects to explore<a name="other-projects"></a>
Here are some projects that are related.

* [Demonstraction of Clean architecture with BLoC pattern](https://github.com/m8811163008/Quotes)
* [Demonstration of sockets in Flutter](https://github.com/m8811163008/simple_socket_project/blob/main/web%20socket%20flutter.dart)
* [A-graph-project-with-TDD-approch](https://github.com/m8811163008/A-graph-project-with-TDD-approch)

