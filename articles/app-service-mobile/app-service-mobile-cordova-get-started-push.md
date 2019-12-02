---
title: Lägga till push-meddelanden i en Apache Cordova-app
description: Lär dig hur du använder Mobile Apps för att skicka push-meddelanden till din Apache Cordova-app.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6ec214c0f1a4f8333bf88790de8d2936fce39002
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668918"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Lägga till push-meddelanden till din Apache Cordova-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabb starts projektet för Apache Cordova][5] så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabb starts Server projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .net backend server SDK för Mobile Apps][1].

## <a name="prerequisites"></a>Förhandskrav

I den här självstudien förutsätter vi att du har ett Apache Cordova-program som har utvecklats med Visual Studio 2015. Den här enheten bör köras på Google Android-emulator, en Android-enhet, en Windows-enhet eller en iOS-enhet.

För att slutföra den här kursen behöver du:

* En dator med [Visual Studio Community 2015][2] eller senare
* [Visual Studio Tools för Apache Cordova][4]
* Ett [aktivt Azure-konto][3]
* Ett slutfört [Apache Cordova snabb starts][5] projekt
* Android Ett [Google-konto][6] med en verifierad e-postadress
* Stoppa Ett [program medlemskap i Apple Developer][7] och en iOS-enhet (iOS Simulator stöder inte push-meddelanden)
* Aktivitets Ett [Microsoft Store Developer-konto][8] och en Windows 10-enhet

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Titta på en video som visar stegen i det här avsnittet][9].

## <a name="update-the-server-project"></a>Uppdatera Server projektet

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Ändra din Cordova-app

För att se till att ditt Apache Cordova app-projekt är redo att hantera push-meddelanden, installerar du Cordova push-plugin plus alla plattformsspecifika push-tjänster.

#### <a name="update-the-cordova-version-in-your-project"></a>Uppdatera Cordova-versionen i projektet.

Om ditt projekt använder en version av Apache Cordova som är tidigare än version "uppdaterar du klient projektet. Utför följande steg för att uppdatera projektet:

* Öppna Configuration designer genom att högerklicka på `config.xml`.
* Välj fliken **plattformar** .
* I text rutan **Cordova CLI** väljer **du kryss.** 
* Om du vill uppdatera projektet väljer du **bygge**och väljer sedan **build-lösning**.

#### <a name="install-the-push-plugin"></a>Installera push-plugin-programmet

Apache Cordova-program hanterar inte funktioner för enhet eller nätverk.  Dessa funktioner tillhandahålls av plugin-program som publiceras antingen på [NPM][10] eller på GitHub. `phonegap-plugin-push`-plugin-programmet hanterar push-meddelanden i nätverket.

Du kan installera push-plugin-programmet på något av följande sätt:

**Från kommando tolken:**

Kör följande kommando:

    cordova plugin add phonegap-plugin-push

**I Visual Studio:**

1. Öppna `config.xml`-filen i Solution Explorer. Välj sedan **plugin** -program > **anpassad**. Välj sedan **git** som installations källa.

2. Ange `https://github.com/phonegap/phonegap-plugin-push` som källa.

    ![Öppna filen config. xml i Solution Explorer][img1]

3. Välj pilen bredvid installations källan.

4. Om du redan har ett numeriskt projekt-ID för Google Developer Console-projektet i **SENDER_ID**kan du lägga till det här. Annars anger du ett värde för plats hållare, till exempel 777777. Om du riktar in dig på Android kan du uppdatera det här värdet i filen config. XML senare.

    >[!NOTE]
    >Från och med version 2.0.0 måste Google-Services. JSON installeras i rotmappen i projektet för att konfigurera avsändar-ID: t. Mer information finns i [installations dokumentationen.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Välj **Lägg till**.

Push-plugin-programmet är nu installerat.

#### <a name="install-the-device-plugin"></a>Installera enhets-plugin-programmet

Följ samma procedur som du använde för att installera push-plugin-programmet. Lägg till enhets-plugin-programmet från huvud-plugin-listan. (Du hittar det genom att välja **plugin** -program > **Core**.) Du behöver det här plugin-programmet för att hämta plattforms namnet.

#### <a name="register-your-device-when-the-application-starts"></a>Registrera enheten när programmet startas 

Inlednings vis inkluderar vi lite minimal kod för Android. Du kan senare ändra appen så att den körs på iOS eller Windows 10.

1. Lägg till ett anrop till **registerForPushNotifications** under återanropet för inloggnings processen. Du kan också lägga till det längst ned i **onDeviceReady** -metoden:

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

    I det här exemplet visas anrop av **registerForPushNotifications** när autentiseringen har slutförts. Du kan anropa `registerForPushNotifications()` så ofta som det behövs.

2. Lägg till den nya **registerForPushNotifications** -metoden enligt följande:

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
3. Android I föregående kod ersätter du `Your_Project_ID` med det numeriska projekt-ID: t för din app från [Google Developer-konsolen][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Valfritt Konfigurera och köra appen på Android

Slutför det här avsnittet om du vill aktivera push-meddelanden för Android.

#### <a name="enable-gcm"></a>Aktivera Firebase Cloud Messaging

Eftersom du är mål för Google Android-plattformen från början måste du aktivera Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurera Server delen för mobilapp för att skicka push-begäranden med FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurera din Cordova-app för Android

Öppna **config. XML**i Cordova-appen. Ersätt sedan `Your_Project_ID` med det numeriska projekt-ID: t för din app från [Google Developer-konsolen][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Öppna **index. js**. Uppdatera sedan koden för att använda ditt numeriska projekt-ID.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurera Android-enheten för USB-felsökning

Innan du kan distribuera programmet till din Android-enhet måste du aktivera USB-felsökning. Utför följande steg på din Android-telefon:

1. Gå till **inställningar** > **om telefonen**. Tryck sedan på **build-numret** tills utvecklarläge är aktiverat (ungefär sju gånger).
2. I **inställningar** > **alternativen för utvecklare**aktiverar du **USB-felsökning**. Anslut sedan din Android-telefon till din utvecklings dator med en USB-kabel.

Vi har testat detta med en Google Nexus 5X-enhet som kör Android 6,0 (Marshmallow). Men metoderna är gemensamma för alla moderna Android-versioner.

#### <a name="install-google-play-services"></a>Installera Google Play-tjänster

Push-plugin-programmet använder Android Google Play-tjänster för push-meddelanden.

1. I Visual Studio väljer du **verktyg** > **Android** - > **Android SDK Manager**. Expandera sedan mappen **extras** . Markera lämpliga rutor för att se till att var och en av följande SDK: er installeras:

   * Android 2,3 eller högre
   * Google databas revision 27 eller högre
   * Google Play-tjänster 9.0.2 eller högre

2. Välj **installera paket**. Vänta sedan på att installationen ska slutföras.

De aktuella bibliotek som krävs visas i [dokumentationen för PhoneGap-plugin-push-installation][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testa push-meddelanden i appen på Android

Nu kan du testa push-meddelanden genom att köra appen och infoga objekt i TodoItem-tabellen. Du kan testa från samma enhet eller från en andra enhet, förutsatt att du använder samma server del. Testa din Cordova-app på Android-plattformen på något av följande sätt:

* *På en fysisk enhet:* Anslut din Android-enhet till din utvecklings dator med en USB-kabel.  I stället för **Google Android-emulator**väljer du **enhet**. Visual Studio distribuerar programmet till enheten och kör programmet. Sedan kan du interagera med programmet på enheten.

  Skärm delnings program som [Mobizen][20] kan hjälpa dig att utveckla Android-program. Mobizen projekt på din Android-skärm till en webbläsare på din dator.

* *På en Android-emulator:* Det finns ytterligare konfigurations steg som krävs när du använder en emulator.

    Se till att du distribuerar till en virtuell enhet som har Google-API: er inställda som mål, som du ser i AVD-hanteraren (Android Virtual Device).

    ![Virtuella Android-Enhetshanteraren](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Om du vill använda en snabbare x86-emulator [installerar du HAXM-drivrutinen][11]och konfigurerar sedan emulatorn för att använda den.

    Lägg till ett Google-konto till Android-enheten genom att välja **appar** > **Inställningar** > **Lägg till konto**. Följ sedan anvisningarna.

    ![Lägga till ett Google-konto till Android-enheten](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Kör ToDoList-appen som tidigare och infoga ett nytt att göra-objekt. Den här gången visas en meddelande ikon i meddelande fältet. Du kan öppna meddelande lådan för att visa all meddelande text.

    ![Visa meddelande](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Valfritt Konfigurera och kör på iOS

Det här avsnittet används för att köra Cordova-projektet på iOS-enheter. Om du inte arbetar med iOS-enheter kan du hoppa över det här avsnittet.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installera och kör iOS-fjärrbuild-agenten på en Mac-eller moln tjänst

Innan du kan köra en Cordova-app på iOS med hjälp av Visual Studio går du igenom stegen i [installations guiden för iOS][12] för att installera och köra fjärrbuild-agenten.

Se till att du kan bygga appen för iOS. Stegen i installations guiden krävs för att skapa appen för iOS från Visual Studio. Om du inte har en Mac kan du skapa för iOS med hjälp av fjärran sluten build-agenten på en tjänst som MacInCloud. Mer information finns i [köra iOS-appen i molnet][21].

> [!NOTE]
> Xcode 7 eller senare krävs för att använda push-plugin-programmet på iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Hitta det ID som ska användas som app-ID

Innan du registrerar appen för push-meddelanden öppnar du config. xml i Cordova-appen, letar reda på `id`-attributvärdet i elementet widget och kopierar det sedan för senare användning. I följande XML är ID: t `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Använd den här identifieraren senare när du skapar ett app-ID på Apples utvecklings Portal. Om du skapar ett annat app-ID på Developer-portalen måste du utföra några ytterligare steg senare i den här självstudien. ID: t i elementet widget måste matcha app-ID: t i Developer-portalen.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apples utvecklings Portal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Kontrol lera att ditt app-ID matchar din Cordova-app

Om app-ID: t som du skapade i ditt Apple Developer-konto redan matchar ID: t för widgets-elementet i filen config. XML, kan du hoppa över det här steget. Men om ID: na inte matchar, utför du följande steg:

1. Ta bort mappen plattformar från projektet.
2. Ta bort mappen plugin-program från projektet.
3. Ta bort mappen node_modules från projektet.
4. Uppdatera ID-attributet för widgeten-elementet i filen config. xml för att använda det app-ID som du skapade i ditt Apple Developer-konto.
5. Återskapa ditt projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i din iOS-app

1. Se till att **iOS** är valt som distributions mål i Visual Studio. Välj sedan **enhet** för att köra push-meddelanden på den anslutna iOS-enheten.

    Du kan köra push-meddelanden på en iOS-enhet som är ansluten till din dator med iTunes. IOS-simulatorn stöder inte push-meddelanden.

2. Välj knappen **Kör** eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Välj **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Appen begär en bekräftelse för push-meddelanden under den första körningen.

3. Skriv en aktivitet i appen och välj sedan plus ikonen **(+)** .
4. Verifiera att ett meddelande har tagits emot. Välj **OK** för att stänga meddelandet.

## <a name="optional-configure-and-run-on-windows"></a>Valfritt Konfigurera och köra i Windows

I det här avsnittet beskrivs hur du kör appen Apache Cordova på Windows 10-enheter (PhoneGap push-plugin-programmet stöds i Windows 10). Om du inte arbetar med Windows-enheter kan du hoppa över det här avsnittet.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrera Windows-appen för push-meddelanden med WNS

Om du vill använda Store-alternativen i Visual Studio väljer du ett Windows-mål i listan med plattforms plattformar, till exempel **Windows-x64** eller **Windows-x86**. (Undvik **Windows-AnyCPU** för push-meddelanden.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Titta på en video som visar liknande steg][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera Notification Hub för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurera Cordova-appen så att den stöder Windows Push-meddelanden

Öppna konfigurations designern genom att högerklicka på **config. XML**. Välj sedan **Visa designer**. Välj sedan fliken **Windows** och välj sedan **Windows 10** under **Windows mål version**.

Om du vill ha stöd för push-meddelanden i dina standard-(debug)-versioner öppnar du filen **build. JSON** . Kopiera sedan konfigurationen "Release" till din fel söknings konfiguration.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Efter uppdateringen ska **build. JSON** -filen innehålla följande kod:

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

Bygg appen och kontrol lera att du inte har några fel. Nu bör klient programmet registreras för meddelanden från Mobile Apps Server del. Upprepa det här avsnittet för alla Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Windows-appen

I Visual Studio kontrollerar du att en Windows-plattform är vald som distributions mål, till exempel **Windows-x64** eller **Windows-x86**. Om du vill köra appen på en Windows 10-dator som är värd för Visual Studio väljer du **lokal dator**.

1. Klicka på knappen **Kör** för att skapa projektet och starta appen.

2. I appen anger du ett namn för en ny todoitem och väljer sedan plus ikonen **(+)** för att lägga till den.

Verifiera att ett meddelande tas emot när objektet läggs till.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Notification Hubs][17] för att lära dig mer om push-meddelanden.
* Om du inte redan har gjort det kan du fortsätta självstudien genom [att lägga till autentisering][14] i din Apache Cordova-app.

Lär dig hur du använder följande SDK: er:

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
