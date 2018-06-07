---
title: Lägg till push-meddelanden i en Apache Cordova-app med funktionen Mobilappar i Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 13c1a53cfa3f998c9e3fa3ee1ee2dcec37357095
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598002"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Lägg till push-meddelanden i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt
I kursen får du lägga till push-meddelanden till den [Apache Cordova quickstart] [ 5] projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabbstartsprojektet server måste push notification extension-paketet. Mer information finns i [arbeta med .NET backend-server SDK för Mobile Apps][1].

## <a name="prerequisites"></a>Förhandskrav
Den här kursen förutsätter att du har en Apache Cordova-program som har utvecklats med Visual Studio 2015. Den här enheten ska köras på Google Android-emulatorn, en Android-enhet, en Windows-enhet eller en iOS-enhet.

För att slutföra den här kursen behöver du:

* En dator med [Visual Studio Community 2015] [ 2] eller senare
* [Visual Studio Tools för Apache Cordova][4]
* En [aktivt Azure-konto][3]
* En slutförd [Apache Cordova quickstart] [ 5] projekt
* (Android) En [Google-konto] [ 6] med en verifierad e-postadress
* (iOS) En [Apple Developer Program medlemskap] [ 7] och en iOS-enhet (iOS-simulatorn inte stöder push-meddelanden)
* (Windows) En [Store-utvecklarkonto] [ 8] och en Windows 10-enhet

## <a name="configure-hub"></a>Konfigurera en meddelandehubb
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Se en video som visar stegen i det här avsnittet][9].

## <a name="update-the-server-project"></a>Uppdatera serverprojektet
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Ändra din Cordova-app
Installera pluginprogrammet Cordova push plus alla plattformsspecifika push-tjänster för att säkerställa att projektet Apache Cordova-app är redo att hantera push-meddelanden.

#### <a name="update-the-cordova-version-in-your-project"></a>Uppdatera Cordova-version i projektet.
Om ditt projekt använder en version av Apache Cordova som är äldre än version 6.1.1, uppdatera klientprojektet. Om du vill uppdatera projektet, gör du följande: 

* Öppna configuration designer högerklickar du på `config.xml`.
* Välj den **plattformar** fliken.
* I den **Cordova CLI** text markerar **6.1.1**. 
* Om du vill uppdatera projektet, Välj **skapa**, och välj sedan **skapa lösning**.

#### <a name="install-the-push-plugin"></a>Installera push plugin-programmet
Apache Cordova-program hanterar inte internt enhet eller nätverket funktioner.  Dessa funktioner som tillhandahålls av plugin-program som publicerats antingen på [npm] [ 10] eller på GitHub. Den `phonegap-plugin-push` plugin-program hanterar nätverket push-meddelanden.

Du kan installera push plugin-programmet på något av följande sätt:

**Från Kommandotolken:**

Kör följande kommando:

    cordova plugin add phonegap-plugin-push

**Från Visual Studio:**

1. I Solution Explorer öppnar den `config.xml` filen. Välj därefter **plugin-program** > **anpassad**. Välj sedan **Git** som installationskälla. 
    
2. Ange `https://github.com/phonegap/phonegap-plugin-push` som källa.

    ![Öppna filen config.xml i Solution Explorer][img1]

3. Välj på pilen bredvid installationskälla.

4. I **SENDER_ID**, om du redan har ett numeriskt projekt-ID för Google Developer Console-projekt, du kan lägga till den här. Annars kan ange ett platshållarvärde, exempelvis 777777. Om du utvecklar för Android, kan du uppdatera det här värdet i filen Config.XML senare.

    >[!NOTE]
    >Från och med version 2.0.0 måste google-services.json vara installerad i rotmappen för ditt projekt för att konfigurera avsändaren-ID. Mer information finns i [dokumentationen.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)
5. Välj **Lägg till**.

Push-plugin-programmet har installerats.

#### <a name="install-the-device-plugin"></a>Installera plugin-program för enhet
Följ samma steg som du använde för att installera push plugin-programmet. Lägga till plugin-program för enhet från listan Core plugin-program. (Om du vill hitta det, Välj **plugin-program** > **Core**.) Du behöver den här plugin-programmet för att hämta plattformsnamnet på.

#### <a name="register-your-device-when-the-application-starts"></a>Registrera din enhet när programmet startas 
Vi ta först minimal kod för Android. Du kan senare ändra app att köras på iOS- eller Windows 10.

1. Lägg till ett anrop till **registerForPushNotifications** under återanrop för inloggning. Du kan också lägga det längst ned i den **onDeviceReady** metod:

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

    Det här exemplet visar anropar **registerForPushNotifications** när autentiseringen lyckas. Du kan anropa `registerForPushNotifications()` så ofta som krävs.

2. Lägg till de nya **registerForPushNotifications** metoden på följande sätt:

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
3. (Android) I föregående kod ersätter `Your_Project_ID` med numeriskt projektet ID för din app från den [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Valfritt) Konfigurera och köra appen på Android
Slutför det här avsnittet om du vill aktivera push-meddelanden för Android.

#### <a name="enable-gcm"></a>Aktivera Firebase Cloud Messaging
Eftersom du utvecklar för Google Android-plattformen först, måste du aktivera Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurera serverdelen för Mobilappen för att skicka push-begäranden som använder FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurera din Cordova-app för Android
Öppna config.xml i Cordova-app. Ersätt `Your_Project_ID` med numeriskt projektet ID för din app från den [Google Developer Console][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Öppna index.js. Uppdatera koden med numeriska projekt-ID.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Konfigurera din Android-enhet för USB-felsökning
Innan du kan distribuera programmet till din Android-enhet, måste du aktivera USB-felsökning. Gör följande på din Android-telefon:

1. Gå till **inställningar** > **om telefonen**. Tryck på **Build-nummer** förrän utvecklarläge har aktiverats (ungefär sju gånger).
2. Tillbaka i **inställningar** > **utvecklaralternativ**, aktivera **USB-felsökning**. Anslut din Android-telefon till din utveckling dator med en USB-kabel.

Vi testade detta med hjälp av en Google Nexus 5 X-enhet som kör Android 6.0 (Marshmallow). Teknikerna som är dock gemensamma för alla moderna Android-versionen.

#### <a name="install-google-play-services"></a>Installera Google Play-tjänster
Push-plugin-programmet är beroende av Android Google Play-tjänster för push-meddelanden.

1. I Visual Studio väljer **verktyg** > **Android** > **Android SDK Manager**. Expandera den **tillägg** mapp. Välj lämpliga kryssrutor för att kontrollera att var och en av följande SDK är installerad:

   * Android 2.3 eller högre
   * Google databasen revision 27 eller högre
   * Google Play Services 9.0.2 eller högre

2. Välj **installera paket**. Vänta sedan för att installationen ska slutföras.

De aktuella bibliotek som krävs finns i den [phonegap-plugin-push-installation av dokumentationen][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testa push-meddelanden i appen på Android
Du kan nu testa push-meddelanden genom att köra appen och lägga till objekt i tabellen TodoItem. Du kan testa från samma enhet eller från en annan enhet som du använder samma serverdelen. Testa din Cordova-app på Android-plattformen i något av följande sätt:

* *På en fysisk enhet:* Anslut din Android-enhet till utvecklingsdator med en USB-kabel.  I stället för **Google Android-emulatorn**väljer **enhet**. Visual Studio distribuerar programmet till enheten och kör programmet. Du kan sedan interagera med program på enheten.

  Delning av skärmen program såsom [Mobizen] [ 20] kan hjälpa dig att utveckla Android-program. Mobizen projekt skärmen Android till en webbläsare på din dator.

* *På en Android-emulatorn:* finns ytterligare konfigurationssteg som krävs när du använder en emulator.

    Kontrollera att du distribuerar till en virtuell enhet som innehåller Google APIs som mål, som visas i hanteraren för Android Virtual Device (AVD).

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Om du vill använda en snabbare x86 emulatorn [installera drivrutinen HAXM][11], och sedan konfigurera emulatorn för att använda den.

    Lägg till ett Google-konto för Android-enhet genom att välja **appar** > **inställningar** > **Lägg till konto**. Följ sedan anvisningarna.

    ![Lägg till ett Google-konto för Android-enhet](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Kör appen todolist som innan och infoga ett nytt todo-objekt. Den här tiden kan visas en meddelandeikonen i meddelandefältet. Du kan öppna lådan meddelande om du vill visa den fullständiga texten i meddelandet.

    ![Visa meddelande](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Valfritt) Konfigurera och köra på iOS
Det här avsnittet handlar om att köra Cordova-projektet på iOS-enheter. Om du inte arbetar med iOS-enheter, kan du hoppa över det här avsnittet.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installera och köra iOS remote build-agent på en Mac- eller molnet tjänst
Innan du kan köra en Cordova-app på iOS med Visual Studio, gå igenom stegen i den [iOS konfigurera guiden] [ 12] att installera och köra remote build-agenten.

Kontrollera att du kan bygga appen för iOS. Stegen i guiden för installation krävs för att bygga appen för iOS från Visual Studio. Om du inte har en Mac, kan du skapa för iOS genom att använda remote build-agenten på en tjänst som MacInCloud. Mer information finns i [kör iOS-app i molnet][21].

> [!NOTE]
> XCode 7 eller senare krävs för att använda push-plugin-programmet på iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Hitta ID som ska användas som App-ID
Innan du registrerar appen för push-meddelanden, öppna config.xml i Cordova-app hitta den `id` attributvärdet i elementet widget och sedan kopiera den för senare användning. I följande XML-ID: T är `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Använd den här identifieraren senare, när du skapar ett App-ID på Apple developer-portalen. Om du skapar ett annat App-ID på developer-portalen måste du utföra några extra steg senare i den här kursen. ID: T i widget-elementet måste matcha det App-ID på developer-portalen.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apple developer-portalen
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Kontrollera att din App-ID som matchar din Cordova-app
Om App-ID som du skapade i Apple Developer-konto redan matchar ID för widget-elementet i filen Config.XML, kan du hoppa över det här steget. Dock ID: N inte matchar gör du följande:

1. Ta bort mappen plattformar från projektet.
2. Ta bort mappen plugin-program från ditt projekt.
3. Ta bort mappen node_modules från projektet.
4. Uppdatera attributet id för elementet widget i Config.XML använda app-ID som du skapade i Apple developer-konto.
5. Återskapa projektet.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i iOS-app
1. I Visual Studio, se till att **iOS** väljs som mål för distribution. Välj sedan **enhet** att köra push-meddelanden på den anslutna iOS-enheten.

    Du kan köra push-meddelanden på en iOS-enhet som är ansluten till datorn med iTunes. IOS-simulatorn stöder inte push-meddelanden.

2. Välj den **kör** knappen eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Välj sedan **OK** att ta emot push-meddelanden.

   > [!NOTE]
   > Appen begär bekräftelse för push-meddelanden under den första körningen.

3. Skriv en aktivitet i appen och markera sedan på plustecknet **(+)** ikon.
4. Kontrollera att ett meddelande togs emot. Välj sedan **OK** att stänga meddelandet.

## <a name="optional-configure-and-run-on-windows"></a>(Valfritt) Konfigurera och köra i Windows
Det här avsnittet beskrivs hur du kör projektet Apache Cordova-app på Windows 10-enheter (PhoneGap push plugin-programmet stöds på Windows 10). Om du inte arbetar med Windows-enheter, kan du hoppa över det här avsnittet.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrera din Windows-app för push-meddelanden med WNS
Om du vill använda Store-alternativ i Visual Studio, Välj ett mål för Windows från listan över plattformar som lösning som **Windows x64** eller **Windows x86**. (Undvika **Windows Platform** för push-meddelanden.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Se en video som visar liknande steg][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurera din Cordova-app för att stödja Windows push-meddelanden
Öppna configuration designer genom att högerklicka på **config.xml**. Välj sedan **Vydesigner**. Välj sedan den **Windows** och välj sedan **Windows 10** under **Windows målversionen**.

Öppna filen build.json för att stödja push-meddelanden i standard (debug)-versioner. Kopiera sedan ”version”-konfigurationen till debug-konfiguration.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Efter uppdateringen måste bör filen build.json innehålla följande kod:

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

Bygga appen och kontrollera att du har några fel. Klientappen bör nu registrera för meddelanden från Mobile Apps-serverdel. Upprepa det här avsnittet för varje Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Windows-appen
Se till att en Windows-plattform som är markerad som mål distributionen i Visual Studio **Windows x64** eller **Windows x86**. Om du vill köra appen på en Windows 10-dator som är värd för Visual Studio väljer **lokal dator**.

1. Välj den **kör** för att skapa projektet och starta appen.

2. Skriv ett namn för en ny todoitem i appen, och välj sedan på plustecknet **(+)** ikon för att lägga till den.

Kontrollera att ett meddelande tas emot när objektet har lagts till.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Meddelandehubbar] [ 17] mer information om push-meddelanden.
* Om du inte redan har gjort det fortsätta kursen av [att lägga till autentisering] [ 14] i din Apache Cordova-app.

Lär dig hur du använder följande SDK:

* [Apache Cordova-SDK][15]
* [ASP.NET Server-SDK][1]
* [Node.js Server-SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
