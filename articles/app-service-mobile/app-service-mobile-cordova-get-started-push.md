---
title: Lägg till push-meddelanden till en Apache Cordova-app med funktionen Mobile Apps i Azure App Service | Microsoft Docs
description: Lär dig hur du använder Mobile Apps för att skicka push-meddelanden till din Apache Cordova-app.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114357"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Lägg till push-meddelanden till din Apache Cordova-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till den [Apache Cordova-snabbstarten] [ 5] projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabbstartsprojektet på en server måste tilläggspaket för push-meddelande. Mer information finns i [arbeta med SDK för .NET backend-server för Mobile Apps][1].

## <a name="prerequisites"></a>Förhandskrav

Den här självstudien förutsätter att du har en Apache Cordova-App som har utvecklats med Visual Studio 2015. Den här enheten ska köras på Google Android-emulatorn, en Android-enhet, en Windows-enhet eller en iOS-enhet.

För att slutföra den här kursen behöver du:

* En dator med [Visual Studio Community 2015] [ 2] eller senare
* [Visual Studio Tools för Apache Cordova][4]
* En [aktivt Azure-konto][3]
* En slutförd [Apache Cordova-snabbstarten] [ 5] projekt
* (Android) En [Google-konto] [ 6] med en verifierad e-postadress
* (iOS) En [Apple Developer Program medlemskap] [ 7] och en iOS-enhet (iOS Simulator inte stöder push-meddelanden)
* (Windows) En [Microsoft Store-utvecklarkonto] [ 8] och en Windows 10-enhet

## <a name="configure-hub"></a>Konfigurera en notification hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Titta på en video som visar stegen i det här avsnittet][9].

## <a name="update-the-server-project"></a>Uppdatera serverprojektet

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Ändra din Cordova-app

För att säkerställa att ditt projekt för Apache Cordova-app är redo att hantera push-meddelanden, installerar du plugin-programmet för Cordova push plus alla plattformsspecifika push-tjänster.

#### <a name="update-the-cordova-version-in-your-project"></a>Uppdatera Cordova-versionen i projektet.

Om ditt projekt använder en version av Apache Cordova som är äldre än version 6.1.1, uppdatera klientprojektet. Om du vill uppdatera projektet, gör du följande:

* Öppna configuration designer genom att högerklicka på `config.xml`.
* Välj den **plattformar** fliken.
* I den **Cordova CLI** textrutan **6.1.1**. 
* Om du vill uppdatera projektet, Välj **skapa**, och välj sedan **skapa lösning**.

#### <a name="install-the-push-plugin"></a>Installera plugin-programmet push

Apache Cordova program hanterar inte enheten eller nätverket funktioner internt.  Dessa funktioner tillhandahålls av plugin-program som är publicerat antingen den [npm] [ 10] eller på GitHub. Den `phonegap-plugin-push` plugin-programmet hanterar nätverk push-meddelanden.

Du kan installera push-plugin-programmet i något av följande sätt:

**Från Kommandotolken:**

Kör följande kommando:

    cordova plugin add phonegap-plugin-push

**Från Visual Studio:**

1. I Solution Explorer öppnar du den `config.xml` filen. Välj sedan **plugin-program** > **anpassad**. Välj sedan **Git** som installationskälla.

2. Ange `https://github.com/phonegap/phonegap-plugin-push` som källa.

    ![Öppna filen config.xml i Solution Explorer][img1]

3. Välj pilen bredvid installationskällan.

4. I **SENDER_ID**, om du redan har en numerisk projekt-ID för Google Developer Console-projekt kan du lägga till den här. Annars kan ange ett platshållarvärde, till exempel 777777. Om du utvecklar Android måste uppdatera du det här värdet i filen Config.XML senare.

    >[!NOTE]
    >Från och med version 2.0.0 måste google-services.json vara installerad i rotmappen för ditt projekt för att konfigurera avsändar-ID. Mer information finns i den [installationsdokumentationen.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Välj **Lägg till**.

Push-plugin-programmet har installerats.

#### <a name="install-the-device-plugin"></a>Installera plugin-programmet för enhet

Följ samma steg som du använde för att installera push-plugin-programmet. Lägga till plugin-programmet för enheten från listan över Core plugin-program. (Du hittar det genom att välja **plugin-program** > **Core**.) Du behöver det här plugin-programmet för att hämta namnet på plattform.

#### <a name="register-your-device-when-the-application-starts"></a>Registrera din enhet när programmet startas 

Först har vi även inkludera vissa minimal kod för Android. Du kan senare ändra appen ska köras på iOS- eller Windows 10.

1. Lägg till ett anrop till **registerForPushNotifications** under återanrop för att logga in. Du kan också lägga det längst ned på den **onDeviceReady** metod:

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

    Det här exemplet visar anropa **registerForPushNotifications** när autentiseringen lyckas. Du kan anropa `registerForPushNotifications()` så ofta det krävs.

2. Lägg till de nya **registerForPushNotifications** metoden på följande sätt:

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
3. (Android) I den föregående koden, Ersätt `Your_Project_ID` med numeriskt projektet ID för din app från den [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Valfritt) Konfigurera och köra appen på Android

Slutför det här avsnittet för att aktivera push-meddelanden för Android.

#### <a name="enable-gcm"></a>Aktivera Firebase Cloud Messaging

Eftersom du utvecklar Google Android-plattformen först måste aktivera du Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurera Mobile App-serverdel för att skicka push-begäran med FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurera din Cordova-app för Android

Öppna i din Cordova-app **config.xml**. Ersätt sedan `Your_Project_ID` med numeriskt projektet ID för din app från den [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Öppna **index.js**. Uppdatera koden så att dina numeriska projekt-ID.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurera din Android-enhet för USB-felsökning

Innan du kan distribuera programmet till din Android-enhet, måste du aktivera USB-felsökning. Gör följande på din Android-telefon:

1. Gå till **inställningar** > **om telefonen**. Tryck sedan på den **Build-nummer** förrän utvecklarläget har aktiverats (cirka sju gånger).
2. Tillbaka i **inställningar** > **utvecklaralternativ**, aktivera **USB-felsökning**. Därefter ansluta din Android-telefon till utvecklingen av din dator med en USB-kabel.

Vi testade detta med hjälp av en Google Nexus 5 X-enhet som kör Android 6.0 (Marshmallow). Metoder som är dock gemensamma för alla moderna Android-versionen.

#### <a name="install-google-play-services"></a>Installera Google Play-tjänster

Push-plugin-programmet är beroende av Android Google Play-tjänster för push-meddelanden.

1. I Visual Studio väljer **verktyg** > **Android** > **Android SDK Manager**. Expandera sedan den **tillägg** mapp. Kontrollera rutorna för att säkerställa att var och en av de följande SDK: er är installerad:

   * Android 2.3 eller högre
   * Google Repository revision 27 eller högre
   * Google Play Services 9.0.2 eller högre

2. Välj **installera paket**. Vänta sedan tills installationen är klar.

De aktuella bibliotek som krävs finns i den [modul phonegap plugin push installationsdokumentationen][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Test-push-meddelanden i appen på Android

Du kan nu test-push-meddelanden genom att köra appen och lägga till objekt i TodoItem-tabellen. Du kan testa från samma enhet eller från en annan enhet, så länge du använder samma backend-servern. Testa din Cordova-app på Android-plattformen i något av följande sätt:

* *På en fysisk enhet:* Koppla en Android-enhet till din utvecklingsdator med en USB-kabel.  I stället för **Google Android-emulatorn**väljer **enhet**. Visual Studio distribuerar programmet till enheten och kör programmet. Du kan sedan interagera med program på enheten.

  Delning av skärmen program som [Mobizen] [ 20] kan hjälpa dig utveckla Android-program. Mobizen projekt Android skärmen för att en webbläsare på din dator.

* *På en Android-emulator:* Det finns ytterligare konfigurationssteg som krävs när du använder en emulator.

    Kontrollera att du distribuerar till en virtuell enhet som har Google APIs som mål, som visas i hanteraren för Android Virtual Device (AVD).

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Om du vill använda en snabbare x86 emulatorn [installera drivrutinen HAXM][11], och sedan konfigurera emulatorn för att använda den.

    Lägg till ett Google-konto till Android-enhet genom att välja **appar** > **inställningar** > **Lägg till konto**. Följ sedan anvisningarna.

    ![Lägg till ett Google-konto till Android-enhet](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Kör appen todolist som innan och infoga en ny att göra-objekt. Den här tiden kan visas en meddelandeikon i meddelandefältet. Du kan öppna lådan meddelande om du vill visa den fullständiga texten i meddelandet.

    ![Visa meddelande](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Valfritt) Konfigurera och kör på iOS

Det här avsnittet handlar om att köra Cordova-projekt på iOS-enheter. Om du inte arbetar med iOS-enheter, kan du hoppa över det här avsnittet.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installera och köra iOS remote build-agenten på en Mac- eller cloud service

Innan du kan köra en Cordova-app på iOS med hjälp av Visual Studio, gå igenom stegen i den [iOS konfigurera guiden] [ 12] att installera och köra den fjärranslutna skapandeagent.

Kontrollera att du kan skapa appen för iOS. Stegen i konfigurationsguiden krävs för att skapa appen för iOS från Visual Studio. Om du inte har en Mac-dator kan du skapa för iOS genom att använda remote build-agenten på en tjänst som MacInCloud. Mer information finns i [kör din iOS-app i molnet][21].

> [!NOTE]
> Xcode 7 eller senare krävs för att använda push-plugin-programmet på iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Hitta ID ska användas som App-ID

Innan du registrerar en app för push-meddelanden, öppna config.xml i din Cordova-app, hitta den `id` attributvärdet i widgeten elementet och sedan kopiera det för senare användning. I följande XML ID: T är `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Använd den här identifieraren senare, när du skapar en App-ID på Apple developer-portalen. Om du skapar ett annat App-ID på developer-portalen, måste du utföra några extra steg senare i den här självstudien. ID: T i widgeten-elementet måste matcha det App-ID på developer-portalen.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apple developer-portalen

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifiera att din App-ID matchar din Cordova-app

Om App-ID som du skapade i ditt Apple Developer-konto redan matchar ID: T för widget-elementet i filen Config.XML, kan du hoppa över det här steget. Men om de ID: N inte matchar, gör följande:

1. Ta bort mappen plattformar från ditt projekt.
2. Ta bort mappen plugin-program från ditt projekt.
3. Ta bort mappen node_modules från ditt projekt.
4. Uppdatera attributet id för widget-elementet i filen Config.XML att använda app-ID som du skapade i ditt Apple developer-konto.
5. Återskapa ditt projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Test-push-meddelanden i din iOS-app

1. I Visual Studio, se till att **iOS** väljs som mål för distributionen. Välj sedan **enhet** att köra push-meddelanden på en ansluten iOS-enhet.

    Du kan köra push-meddelanden på en iOS-enhet som är ansluten till datorn med iTunes. IOS-simulatorn stöder inte push-meddelanden.

2. Välj den **kör** knappen eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Välj sedan **OK** accepterar push-meddelanden.

   > [!NOTE]
   > Appen begär bekräftelse för push-meddelanden under den första körningen.

3. Skriv en uppgift i appen, och välj sedan plustecknet **(+)** ikon.
4. Kontrollera att ett meddelande togs emot. Välj sedan **OK** att stänga meddelandet.

## <a name="optional-configure-and-run-on-windows"></a>(Valfritt) Konfigurera och kör på Windows

Det här avsnittet beskrivs hur du kör Apache Cordova-app-projekt på Windows 10-enheter (PhoneGap push-plugin-programmet stöds på Windows 10). Om du inte arbetar med Windows-enheter, kan du hoppa över det här avsnittet.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrera din Windows-app för push-meddelanden med WNS

För att använda Store-alternativ i Visual Studio, välja ett Windows-mål från listan över plattformar som lösning, till exempel **Windows x64** eller **Windows x86**. (Undvika **Windows Platform** för push-meddelanden.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Se en video som visar liknande steg][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurera din Cordova-app för att stödja Windows push-meddelanden

Öppna configuration designer genom att högerklicka på **config.xml**. Välj sedan **Vydesigner**. Välj sedan den **Windows** fliken och välj sedan **Windows 10** under **Windows målversion**.

För push-meddelanden i dina standard (debug)-versioner måste du öppna den **build.json** fil. Kopiera sedan ”utgåvan”-konfigurationen i konfigurationen av felsökning.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Efter uppdateringen den **build.json** filen ska innehålla följande kod:

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

Skapa appar och kontrollera att du har inga fel. Klientappen bör nu registrera för meddelanden från Mobile Apps-serverdel. Upprepa det här avsnittet för varje Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Test-push-meddelanden i din Windows-app

I Visual Studio, se till att en Windows-plattform som har valts som mål distribution **Windows x64** eller **Windows x86**. För att köra appen på en Windows 10-dator som är värd för Visual Studio, Välj **lokal dator**.

1. Välj den **kör** för att skapa projektet och starta appen.

2. Skriv ett namn för en ny todoitem i appen, och välj sedan plustecknet **(+)** ikon för att lägga till den.

Kontrollera att ett meddelande tas emot när objektet har lagts till.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Meddelandehubbar] [ 17] vill veta mer om push-meddelanden.
* Om du inte redan har gjort det, fortsätta självstudien av [att lägga till autentisering] [ 14] till din Apache Cordova-app.

Lär dig hur du använder följande SDK: erna:

* [Apache Cordova SDK][15]
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
