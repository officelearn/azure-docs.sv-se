---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060516"
---
### <a name="create-the-react-native-solution"></a>Skapa den reagera inbyggda lösningen

1. I `Terminal` kan du uppdatera dina miljö verktyg, som krävs för att fungera med reagera internt med hjälp av följande kommandon:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. I `Terminal` kör du följande kommando, om du har `React Native` CLI installerat för att avinstallera det. Använd `npx` för att automatiskt komma åt den senaste tillgängliga reagerande inbyggda CLI-versionen:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > Reagera internt har ett inbyggt kommando rads gränssnitt. I stället för att installera och hantera en viss version av CLI globalt rekommenderar vi att du ansluter till den aktuella versionen vid körning med `npx` , som levereras med Node.js. Med `npx react-native <command>` kommer den aktuella stabila versionen av CLI att laddas ned och köras när kommandot körs.

1. Navigera till mappen projekt där du vill skapa det nya programmet. Använd den typescript-baserade mallen genom att ange `--template` parametern:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Kör Metro-Server, som skapar JavaScript-paket och övervakar alla kod uppdateringar för att uppdatera paketen i real tid:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Verifiera konfigurationen genom att köra iOS-appen. Se till att du har startat en iOS-Simulator eller anslutit en iOS-enhet innan du kör följande kommando:

    ```bash
    npx react-native run-ios
    ```

1. Verifiera konfigurationen genom att köra Android-appen. Det krävs några ytterligare steg för att konfigurera en Android-emulator eller-enhet för att kunna komma åt den lokala tunnelbane servern. Följande kommandon genererar inledande JavaScript-paket för Android och infogar dem i mappen till gångar.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Det här skriptet distribueras i förväg med den första versionen av appen. När du har distribuerat konfigurerar du emulatorn eller enheten för att få åtkomst till Metro-servern genom att ange serverns IP-adress. Kör följande kommando för att skapa och köra Android-programmet:

    ```bash
    npx react-native run-android
    ```

    När du är i appen trycker du på `CMD+M` (emulator) eller skakar enheten för att fylla i inställningarna för utvecklare, navigerar till `Settings`  >  `Change Bundle Location` och anger IP-adressen för Metro-servern med standard porten: `<metro-server-ip-address>:8081` .

1. I `App.tsx` filen använder du en ändring i sidlayouten och sparar den och gör att ändringen avspeglas automatiskt i både iOS-och Android-appar.

    > [!NOTE]
    > Installations guide för detaljerad utvecklings miljö finns i den [officiella dokumentationen](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Installera de paket som krävs

Du behöver följande tre paket för att det här exemplet ska fungera:

1. [Reagera på inbyggda push-meddelanden iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios)  -  [Project-GitHub](https://github.com/react-native-community/push-notification-ios)

    Det här paketet skapades när PushNotificationIOS delades upp från kärnan i reagera internt. Paketet implementerar push-meddelanden för iOS och ger ett reagerar lokalt gränssnitt för att komma åt det. Kör följande kommando för att installera paketet:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Reagera på inbyggda push-meddelanden plattforms oberoende](https://www.npmjs.com/package/react-native-push-notification)

    Det här paketet implementerar lokala och fjärranslutna meddelanden på iOS och Android på ett plattforms oberoende sätt. Kör följande kommando för att installera paketet:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Enhets informations paket](https://www.npmjs.com/package/react-native-device-info) Paketet innehåller information om en enhet i körnings miljön. Använd den för att definiera en enhets identifierare som används för att registrera för push-meddelanden. Kör följande kommando för att installera paketet:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementera plattforms oberoende komponenter

1. Skapa och implementera `DemoNotificationHandler` :

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Skapa och implementera `DemoNotificationService` :

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Skapa och implementera `DemoNotificationRegistrationService` :

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Konfigurera appen. Öppna `package.json` och Lägg till följande skript definition:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Kör sedan det här skriptet, som kommer att kopiera standard konfigurationen till `config` mappen.

    ```bash
    yarn configure
    ```

    Sista steget är att uppdatera konfigurations filen som kopierades i föregående steg med information om API-åtkomst. Ange `apiKey` `apiUrl` parametrarna och:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementera gränssnittet för plattforms oberoende

1. Definiera sidlayout

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Använd format

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Initiera sid komponenten

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definiera knapp klicknings hanterare

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Hantera mottagna token-registreringar och push-meddelanden

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```