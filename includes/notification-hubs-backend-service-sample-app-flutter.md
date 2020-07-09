---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147825"
---
### <a name="create-the-flutter-solution"></a>Skapa Flutter-lösningen

1. Öppna en ny instans av **Visual Studio Code**.

1. Öppna **kommando paletten** (**Shift**  +  -**kommandot**  +  **P**).

1. Välj kommandot **Flutter: New Project** och tryck på **RETUR**.

1. Ange *push_demo* som **projekt namn** och välj sedan en **projekt plats**.

1. När du uppmanas att göra det väljer du **Hämta paket**.

1. **Kontroll**  +  **Klicka** på mappen **Kotlin** (under **app**  >  **src**  >  **main**) och välj sedan **Visa i Finder**. Byt sedan namn på de underordnade mapparna (under mappen **Kotlin** ) till ```com``` , ```<your_organization>``` ```pushdemo``` respektive.

    > [!NOTE]
    > När du använder **Visual Studio-kodfragmentet** används dessa mappar som standard **com**, **till exempel** **<project_name>**. Förutsatt att **mobcat** används för **organisationen**bör mappstrukturen visas som:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. Tillbaka i **Visual Studio Code**, uppdatera **applicationId** -värdet i **Android**-  >  **appen**  >  **build. gradle** till `com.<your_organization>.pushdemo` .

    > [!NOTE]
    > Du bör använda ditt eget organisations namn för *<your_organization* plats hållare för>. Om du till exempel använder **mobcat** som organisation resulterar det i ett **paket namn** värde för *com. mobcat. pushdemo*.

1. Uppdatera attributet **Package** i **AndroidManifest.xml** -filer, under **src**-och src-  >  **debug** **src**  >  **main** **src**  >  **profil** . Se till att värdena stämmer överens med **applicationId** som du använde i föregående steg.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Uppdatera ```android:label``` attributet i **AndroidManifest.xml** -filen under **src**  >  **main** to *PushDemo*. Lägg sedan till ```android:allowBackup``` attributet direkt under ```android:label``` och ange värdet **falskt**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Öppna filen **build. gradle** för app-nivå (**Android**-  >  **app**  >  **build. gradle**) och uppdatera sedan *compileSdkVersion* (från **Android** -avsnittet) för att använda API **29**. Uppdatera sedan värdena *minSdkVersion* och *targetSdkVersion* (från avsnittet **defaultConfig** ) till **26** respektive **29** .

    > [!NOTE]
    > Endast de enheter som kör **API-nivå 26 och senare** stöds för den här själv studie kursen, men du kan utöka den till att stödja enheter som kör äldre versioner.

1. **Kontroll**  +  **Klicka** på **iOS** -mappen och välj sedan **Öppna i Xcode**.

1. I **Xcode**klickar du på **löpare** ( **XCODEPROJ** överst, inte i mappen). Välj sedan mål för **löpare** och välj fliken **Allmänt** . När du har valt **alla** build-konfiguration uppdaterar du **paket identifieraren** till `com.<your_organization>.PushDemo` .

    > [!NOTE]
    > Du bör använda ditt eget organisations namn för *<your_organization* plats hållare för>. Om du till exempel använder **mobcat** som organisation resulterar det i ett **paket-ID** som är *com. mobcat. PushDemo*.

1. Klicka på **info. plist** och uppdatera sedan **paketets namn** värde till **PushDemo**

1. Stäng **Xcode** och återgå till **Visual Studio Code**.

1. Gå tillbaka **till Visual Studio Code**, **Öppna pubspec. yaml**, Lägg till [http-](https://pub.dev/packages/http) och [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **Dart-paket** som beroenden. Spara sedan filen och klicka på **Hämta paket** när du uppmanas att göra det.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. I **Terminal**ändrar du katalogen till **iOS** -mappen (för ditt Flutter-projekt). Kör sedan kommandot [**Pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) för att installera nya poddar (krävs av [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) -paketet).

1. **Kontroll**  +  **Klicka** på mappen **lib** och välj sedan **ny fil** på menyn med hjälp av *main_page. Dart* som fil namn. Lägg sedan till följande kod.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. I **main. Dart**ersätter du den mallade koden med följande.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. I **Terminal**skapar och kör du appen på varje mål plattform för att testa att den mall som körs körs på din enhet (er). Kontrol lera att de enheter som stöds är anslutna.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementera plattforms oberoende komponenter

1. **Kontroll**  +  **Klicka** på mappen **lib** och välj sedan **ny mapp** på menyn med hjälp av *modeller* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **modeller** och välj sedan **ny fil** på menyn med hjälp av *device_installation. Dart* som fil namn. Lägg sedan till följande kod.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Lägg till en ny fil i mappen **modeller** som heter *push_demo_action. Dart* för att definiera uppräkningen av åtgärder som stöds i det här exemplet.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Lägg till en ny mapp i projektet med namnet **tjänster** och Lägg sedan till en ny fil i mappen med namnet *device_installation_service. Dart* med följande implementering.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Du bör använda ditt eget organisations namn för *<your_organization* plats hållare för>. Att använda **mobcat** som organisation resulterar till exempel i ett [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) -namn för *com. mobcat. pushdemo/deviceinstallation*.
    >
    > Den här klassen kapslar in arbete med den underliggande inbyggda plattformen för att hämta nödvändig enhets installations information. En [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) underlättar dubbelriktad asynkron kommunikation med de underliggande inbyggda plattformarna. Den plattformsspecifika motparten för den här kanalen kommer att skapas i i senare steg.

1. Lägg till en annan fil i mappen med namnet *notification_action_service. Dart* med följande implementering.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Detta används som en enkel mekanism för att centralisera hanteringen av aviserings åtgärder så att de kan hanteras på ett plattforms oberoende sätt med hjälp av en starkt uppräkning. Tjänsten gör det möjligt för den underliggande inbyggda plattformen att utlösa en åtgärd när en anges i meddelande nytto lasten. Det gör det också möjligt för den gemensamma koden att retroaktivt kontrol lera om en åtgärd angavs när programmet startades när Flutter är redo att bearbeta den. Till exempel när appen startas genom att trycka på ett meddelande från meddelande centret.

1. Lägg till en ny fil i mappen **tjänster** med namnet *notification_registration_service. Dart* med följande implementering.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Den här klassen kapslar in användningen av **DeviceInstallationService** och begär anden till backend-tjänsten för att utföra kravet registrera, avregistrera och uppdatera registrerings åtgärder. Argumentet **apiKey** krävs endast om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .

1. Lägg till en ny fil i mappen **lib** som heter *config. Dart* med följande implementering.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Detta används som ett enkelt sätt att definiera program hemligheter. Ersätt plats hållarnas värden med dina egna. Du bör ha gjort en anteckning om dessa när du skapade backend-tjänsten. **URL: en för API-appen** ska vara `https://<api_app_name>.azurewebsites.net/` . **ApiKey** -medlemmen krävs bara om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .
    >
    > Se till att lägga till detta i din gitignore-fil för att undvika att spara dessa hemligheter i käll kontrollen.

### <a name="implement-the-cross-platform-ui"></a>Implementera gränssnittet för plattforms oberoende

1. I **main_page. Dart**ersätter du funktionen **build** med följande.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Lägg till den nödvändiga importen överst i filen **main_page. Dart** .

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Lägg till ett fält i **_MainPageState** -klassen för att lagra en referens till **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. I **_MainPageState** -klassen implementerar du händelse hanterare för knapparna **Registrera** och **avregistrera** **onPressed** -händelser. Anropa motsvarande **register** / **Avregistrerings** metoder och Visa sedan en avisering som visar resultatet.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Se till att följande importer finns överst i filen i **main. Dart**.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Deklarera en variabel för att lagra referens till en instans av **NotificationActionService** och initiera den.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Lägg till funktioner för att hantera visning av en avisering när en åtgärd utlöses.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Uppdatera **huvud** funktionen för att observera **NotificationActionService** **actionTriggered** -dataströmmen och kontrol lera om det finns några åtgärder som har registrerats under appens start.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Detta är bara att demonstrera mottagning och spridning av push-meddelanden. De kan vanligt vis hanteras i bakgrunden för att till exempel navigera till en viss vy eller uppdatera vissa data i stället för att visa en avisering i det här fallet.
