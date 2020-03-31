---
title: Lägga till push-meddelanden i en Apache Cordova-app
description: Läs om hur du använder mobilappar för att skicka push-meddelanden till din Apache Cordova-app.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461614"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Lägga till push-meddelanden i din Apache Cordova-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabbstartsprojektet Apache Cordova][5] så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information finns i [Arbeta med .NET-backend-servern SDK för mobilappar][1].

## <a name="prerequisites"></a><a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har ett Apache Cordova-program som har utvecklats med Visual Studio 2015. Den här enheten ska köras på Google Android Emulator, en Android-enhet, en Windows-enhet eller en iOS-enhet.

För att slutföra den här kursen behöver du:

* En dator med [Visual Studio Community 2015][2] eller senare
* [Visual Studio Verktyg för Apache Cordova][4]
* Ett [aktivt Azure-konto][3]
* Ett avslutat [apache cordova-snabbstartsprojekt][5]
* (Android) Ett [Google-konto][6] med en verifierad e-postadress
* (iOS) Ett [medlemskap i Apple Developer Program][7] och en iOS-enhet (iOS Simulator stöder inte push-meddelanden)
* (Windows) Ett [Microsoft Store-utvecklarkonto][8] och en Windows 10-enhet

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Titta på en video som visar stegen i det här avsnittet][9].

## <a name="update-the-server-project"></a>Uppdatera serverprojektet

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>Ändra din Cordova-app

För att säkerställa att ditt Apache Cordova-appprojekt är redo att hantera push-meddelanden installerar du Cordova push-plugin plus eventuella plattformsspecifika push-tjänster.

#### <a name="update-the-cordova-version-in-your-project"></a>Uppdatera Cordova-versionen i projektet.

Om projektet använder en version av Apache Cordova som är tidigare än version 6.1.1 uppdaterar du klientprojektet. Så här uppdaterar du projektet:

* Om du vill öppna konfigurationsdesignern högerklickar du på `config.xml`.
* Välj fliken **Plattformar.**
* Välj **6.1.1**i textrutan **Cordova CLI** . 
* Om du vill uppdatera projektet väljer du **Bygg**och väljer sedan **Bygg lösning**.

#### <a name="install-the-push-plugin"></a>Installera push plugin

Apache Cordova-program hanterar inte enhets- eller nätverksfunktioner internt.  Dessa funktioner tillhandahålls av plugins som publiceras antingen på [npm][10] eller på GitHub. Insticksprogrammet `phonegap-plugin-push` hanterar push-meddelanden för nätverk.

Du kan installera push plugin på något av följande sätt:

**Från kommandotolken:**

Kör följande kommando:

    cordova plugin add phonegap-plugin-push

**Inifrån Visual Studio:**

1. Öppna filen i `config.xml` Solution Explorer. Välj sedan **Plugins** > **Custom**. Välj sedan **Git** som installationskälla.

2. Ange `https://github.com/phonegap/phonegap-plugin-push` som källa.

    ![Öppna filen config.xml i Solution Explorer][img1]

3. Markera pilen bredvid installationskällan.

4. Om du redan har ett numeriskt projekt-ID för Projektet Google Developer Console **SENDER_ID kan**du lägga till det här om du redan har ett numeriskt projekt-ID för Projektet Google Developer Console. Annars anger du ett platshållarvärde, till exempel 777777. Om du riktar in dig på Android kan du uppdatera det här värdet i filen config.xml senare.

    >[!NOTE]
    >Från och med version 2.0.0 måste google-services.json installeras i projektets rotmapp för att konfigurera avsändande-ID:t. Mer information finns i [installationsdokumentationen.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Välj **Lägg till**.

Push plugin är nu installerat.

#### <a name="install-the-device-plugin"></a>Installera insticksprogrammet för enheten

Följ samma procedur som du använde för att installera push-plugin. Lägg till device plugin från Core plugins listan. (För att hitta den, välj **Plugins** > **Core**.) Du behöver denna plugin för att få plattformens namn.

#### <a name="register-your-device-when-the-application-starts"></a>Registrera enheten när programmet startar 

Inledningsvis inkluderar vi några minimal kod för Android. Senare kan du ändra appen så att den körs på iOS eller Windows 10.

1. Lägg till ett anrop för att **registreraFörPushNotifications** under motringningsprocessen för inloggningsprocessen. Du kan också lägga till den längst ned i **metoden onDeviceReady:**

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    I det här exemplet visas anropande **registerForPushNotifications** efter autentiseringen. Du kan `registerForPushNotifications()` ringa så ofta som krävs.

2. Lägg till den nya **metoden registerForPushNotifications** enligt följande:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) Ersätt `Your_Project_ID` med det numeriska projekt-ID:t för appen i koden [ovan.][18]

## <a name="optional-configure-and-run-the-app-on-android"></a>(Valfritt) Konfigurera och köra appen på Android

Fyll i det här avsnittet för att aktivera push-meddelanden för Android.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>Aktivera Firebase Cloud Messaging

Eftersom du riktar in dig på Google Android-plattformen från början måste du aktivera Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>Konfigurera backenden för mobilappen för att skicka push-begäranden med FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurera din Cordova-app för Android

Öppna **config.xml**i Cordova-appen . Ersätt `Your_Project_ID` sedan med det numeriska projekt-ID:t för din app från [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Öppna **index.js**. Uppdatera sedan koden för att använda det numeriska projekt-ID:et.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Konfigurera din Android-enhet för USB-felsökning

Innan du kan distribuera ditt program till din Android-enhet måste du aktivera USB-felsökning. Gör så här på din Android-telefon:

1. Gå till **Inställningar** > **om telefon**. Tryck sedan på **byggnumret** tills utvecklarläget är aktiverat (ungefär sju gånger).
2. Tillbaka i **Inställningar** > **Developer Options**, aktivera USB **felsökning**. Anslut sedan din Android-telefon till din utvecklingsdator med en USB-kabel.

Vi testade detta med hjälp av en Google Nexus 5X-enhet som kör Android 6.0 (Marshmallow). Men teknikerna är vanliga i alla moderna Android-release.

#### <a name="install-google-play-services"></a>Installera Google Play-tjänster

Push-plugin bygger på Android Google Play-tjänster för push-meddelanden.

1. I Visual Studio väljer du **Verktyg** > **Android** > **Android SDK Manager**. Expandera sedan mappen **Extras.** Kontrollera lämpliga rutor för att säkerställa att var och en av följande SDK:er är installerade:

   * Android 2.3 eller senare
   * Google Repository revision 27 eller senare
   * Google Play-tjänster 9.0.2 eller senare

2. Välj **Installera paket**. Vänta sedan tills installationen är klar.

De aktuella biblioteken som krävs visas i dokumentationen för installation av [phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testa push-meddelanden i appen på Android

Du kan nu testa push-meddelanden genom att köra appen och infoga objekt i tabellen TodoItem. Du kan testa från samma enhet eller från en andra enhet, så länge du använder samma backdel. Testa din Cordova-app på Android-plattformen på något av följande sätt:

* *På en fysisk enhet:* Anslut din Android-enhet till utvecklingsdatorn med en USB-kabel.  I stället för **Google Android Emulator**väljer du **Enhet**. Visual Studio distribuerar programmet till enheten och kör programmet. Du kan sedan interagera med programmet på enheten.

  Skärmdelningsprogram som [Mobizen][20] kan hjälpa dig att utveckla Android-program. Mobizen projicerar din Android-skärm till en webbläsare på datorn.

* *På en Android-emulator:* Det finns ytterligare konfigurationssteg som krävs när du använder en emulator.

    Kontrollera att du distribuerar till en virtuell enhet som har Google API:er inställda som mål, vilket visas i AVD-hanteraren (Android Virtual Device).

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Om du vill använda en snabbare x86-emulator [installerar du HAXM-drivrutinen][11]och konfigurerar sedan emulatorn så att den används.

    Lägg till ett Google-konto på Android-enheten genom att välja**Lägg till konto****för** >  **inställningar för appar** > . Följ sedan anvisningarna.

    ![Lägga till ett Google-konto på Android-enheten](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Kör todolist-appen som tidigare och infoga ett nytt todo-objekt. Den här gången visas en meddelandeikon i meddelandefältet. Du kan öppna meddelandelådan för att visa hela texten i meddelandet.

    ![Visa meddelande](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Valfritt) Konfigurera och köra på iOS

Det här avsnittet är för att köra Cordova-projektet på iOS-enheter. Om du inte arbetar med iOS-enheter kan du hoppa över det här avsnittet.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installera och kör iOS-fjärrbyggagenten på en Mac- eller molntjänst

Innan du kan köra en Cordova-app på iOS med Visual Studio går du igenom stegen i [installationsguiden][12] för iOS för att installera och köra fjärrbyggagenten.

Se till att du kan skapa appen för iOS. Stegen i installationsguiden krävs för att skapa appen för iOS från Visual Studio. Om du inte har en Mac kan du skapa för iOS genom att använda fjärrbyggagenten på en tjänst som MacInCloud. Mer information finns [i Kör din iOS-app i molnet][21].

> [!NOTE]
> Xcode 7 eller mer krävs för att använda push plugin på iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Hitta det ID som ska användas som app-ID

Innan du registrerar appen för push-meddelanden öppnar du config.xml `id` i cordova-appen, letar reda på attributvärdet i widgetelementet och kopierar den sedan för senare användning. I följande XML är ID: t `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Använd senare den här identifieraren när du skapar ett app-ID på Apples utvecklarportal. Om du skapar ett annat app-ID på utvecklarportalen måste du ta några extra steg senare i den här självstudien. ID:et i widgetelementet måste matcha app-ID:et på utvecklarportalen.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apples utvecklarportal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Kontrollera att ditt app-ID matchar din Cordova-app

Om app-ID:t som du skapade i ditt Apple-utvecklarkonto redan matchar ID:t för widgetelementet i filen config.xml kan du hoppa över det här steget. Om ID:na inte matchar gör du följande:

1. Ta bort plattformsmappen från projektet.
2. Ta bort plugins-mappen från projektet.
3. Ta bort mappen node_modules från projektet.
4. Uppdatera id-attributet för widgetelementet i filen config.xml för att använda det app-ID som du skapade i ditt Apple-utvecklarkonto.
5. Bygg om projektet.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i din iOS-app

1. Kontrollera att **iOS** är markerat som distributionsmål i Visual Studio. Välj sedan **Enhet** för att köra push-meddelanden på din anslutna iOS-enhet.

    Du kan köra push-meddelanden på en iOS-enhet som är ansluten till datorn med iTunes. iOS-simulatorn stöder inte push-meddelanden.

2. Välj **knappen Kör** eller **F5** i Visual Studio för att skapa projektet och starta appen på en iOS-enhet. Välj sedan **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Appen begär bekräftelse för push-meddelanden under den första körningen.

3. Skriv en uppgift i appen och välj sedan plusikonen **(+).**
4. Kontrollera att ett meddelande har tagits emot. Välj sedan **OK** för att avvisa meddelandet.

## <a name="optional-configure-and-run-on-windows"></a>(Valfritt) Konfigurera och köra på Windows

I det här avsnittet beskrivs hur du kör Apache Cordova-appprojektet på Windows 10-enheter (PhoneGap push-plugin stöds på Windows 10). Om du inte arbetar med Windows-enheter kan du hoppa över det här avsnittet.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrera din Windows-app för push-meddelanden med WNS

Om du vill använda Store-alternativen i Visual Studio väljer du ett Windows-mål i listan Lösningsplattformar, till exempel **Windows-x64** eller **Windows-x86**. (Undvik **Windows-AnyCPU** för push-meddelanden.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Titta på en video som visar liknande steg][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurera din Cordova-app för att stödja Windows push-meddelanden

Öppna konfigurationsdesignern genom att högerklicka på **config.xml**. Välj sedan **Visa designer**. Välj sedan fliken **Windows** och välj sedan **Windows 10** under **Windows Target Version**.

Om du vill stödja push-meddelanden i standardversionerna (felsökning) öppnar du **filen build.json.** Kopiera sedan "release"-konfigurationen till felsökningskonfigurationen.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Efter uppdateringen ska **filen build.json** innehålla följande kod:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Skapa appen och kontrollera att du inte har några fel. Din klientapp ska nu registrera sig för aviseringarna från den bakre delen av mobilapparna. Upprepa det här avsnittet för alla Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Din Windows-app

Kontrollera att en Windows-plattform har valts som distributionsmål i Visual Studio, till exempel **Windows-x64** eller **Windows-x86**. Om du vill köra appen på en Windows 10-dator som är värd för Visual Studio väljer du **Lokal dator**.

1. Välj knappen **Kör** för att skapa projektet och starta appen.

2. Skriv ett namn på en ny todoitem i appen och välj sedan plusikonen **(+)** för att lägga till den.

Kontrollera att ett meddelande tas emot när objektet läggs till.

## <a name="next-steps"></a><a name="next-steps"></a>Nästa steg

* Läs om [Meddelandehubbar][17] om du vill veta mer om push-meddelanden.
* Om du inte redan har gjort det fortsätter du självstudien genom att [lägga till autentisering][14] i din Apache Cordova-app.

Läs om hur du använder följande SDK:er:

* [Apache Cordova-SDK][15]
* [ASP.NET Server-SDK][1]
* [Node.js Server-SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
