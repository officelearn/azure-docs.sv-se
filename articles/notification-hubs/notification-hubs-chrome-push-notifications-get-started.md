---
title: Skicka meddelanden till Chrome-appar med Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Chrome-app.
services: notification-hubs
keywords: mobila push-meddelanden, push-meddelanden, push-meddelande, push-meddelanden i chrome
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7bdc692104194bff4a25e6974ba72971af543cbf
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029007"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Självstudie: Skicka meddelanden till Chrome-appar med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Den här självstudiekursen visar dig hur du skapar en meddelandehubb och skickar push-meddelanden till en Google Chrome-exempelapp med hjälp av [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). Chrome-appen körs i en Google Chrome-webbläsarkontext och registreras med meddelandehubben. 

> [!NOTE]
> Push-meddelandena för Chrome-appar är inte generiska för avisering i webbläsare. De är specifika för webbläsarens utökningsbarhetsmodell (se [Översikt över Chrome-appar] för mer information). Chrome-appar kan köras i vanliga webbläsare på stationära och bärbara datorer men även på mobila enheter (Android och iOS) via Apache Cordova. Mer information finns i [Chrome-appar på mobila enheter].

I den här självstudien gör du följande:

> [!div class="checklist"]
> * [Aktivera Google Cloud Messaging](#register)
> * [Konfigurera meddelandehubben](#configure-hub)
> * [Anslut Chrome-appen till meddelandehubben](#connect-app)
> * [Skicka ett push-meddelande till Chrome-appen](#send)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a id="register"></a>Aktivera Google Cloud Messaging
1. Gå till webbplatsen [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) och logga in med dina Google-kontouppgifter
2. Välj **Skapa projekt** i verktygsfältet. 

    ![Knappen Skapa projekt](media/notification-hubs-chrome-get-started/create-project-button.png)
1. Ange ett lämpligt **projektnamn** och klicka sedan på knappen **Skapa**.
2. Välj meddelandeikonen (klockan) i verktygsfältet och välj meddelandet **Skapa projekt**. 

    ![Meddelande om att skapa projekt](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. Skriv ned **projektnumret** på **projekt**-sidan för projektet som du skapade. Du använder projektnumret som **GCM-avsändar-ID** i Chrome-appen för registrering med GCM.
   
    ![Google Cloud-konsolen – Projektnummer](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. Välj **Gå till API-översikt** på instrumentpanelen. 

    ![Knappen Gå till API-översikt](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. Välj **Aktivera API:er och tjänster** på sidan API och tjänster. 

    ![Aktivera API:er och tjänster](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. Sök i listan med nyckelordet **molnmeddelanden**. Välj **Google Cloud Messaging** i den filtrerade listan. 

    ![Google Cloud Messaging-API](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. Välj **Aktivera** på sidan **Google Cloud Messaging**.

    ![Aktivera GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. Växla till fliken **Autentiseringsuppgifter** på sidan **API och tjänster**. Välj **Skapa autentiseringsuppgifter** och välj sedan **API-nyckel**. 

    ![Knappen Skapa API-nyckel](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. Kopiera nyckeln till Urklipp genom att välja kopieringsknappen i dialogrutan **API-nyckel har skapats**. Spara den någonstans. Du kan använda det här värdet till att konfigurera aviseringshubben i nästa avsnitt, så att den kan skicka push-meddelanden till GCM.

    ![API-sidan](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Välj din API-nyckel i listan **API-nycklar**. På sidan API Välj **IP-adresser (webbservrar, cron-jobb osv)**  på API-nyckelsidan och ange **0.0.0.0/0** som IP-adress och klicka på Spara. 

    ![Aktivera IP-adresser](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Skapa din meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. Välj **Google (GCM)** i kategorin **MEDDELANDEINSTÄLLNINGAR**, ange **API-nyckel** för GCM-projektet och klicka på **Spara**.

      ![Azure Notification Hubs – Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Anslut Chrome-appen till meddelandehubben
Din meddelandehubb har nu konfigurerats för att fungera med GCM och du har anslutningssträngar för att registrera din app för att både ta emot och skicka push-meddelanden.

### <a name="create-a-new-chrome-app"></a>Skapa en ny Chrome-App
Följande exempel baseras på [Chrome App GCM Sample] och använder det rekommenderade sättet att skapa en Chrome-app. I det här avsnittet beskrivs de steg som är specifika för Azure Notification Hubs. 

> [!NOTE]
> Vi rekommenderar att du hämtar källan för den här Chrome-appen från [Chrome App Notification Hub Sample]. 

Chrome-appen skapas med hjälp av JavaScript och du kan använda den textredigerare som du tycker bäst om för att skapa appen. I följande bild visas hur Chrome-appen ser ut:

![Google Chrome-app][15]

1. Skapa en mapp och kalla den för `ChromePushApp`. Namnet är godtyckligt. Om du döper mappen till något annat måste du ersätta sökvägen i alla nödvändiga kodsegment.
2. Hämta [biblioteket crypto-js] från den mapp som du skapade i det andra steget. Den här biblioteksmappen innehåller två undermappar: `components` och `rollups`.
3. Skapa en `manifest.json`-fil. Alla Chrome-appar backas upp av en manifestfil som innehåller app-metadata. Och viktigast av allt: den innehåller även alla behörigheter som beviljas för appen när användaren installerar den.
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    Lägg märke till elementet `permissions`. Det anger att den här Chrome-appen kan ta emot push-meddelanden från GCM. Exempelappen använder också en ikonfil, `gcm_128.png`, som du hittar vid den källa som återanvänds från det ursprungliga GCM-exemplet. Du kan ersätta den med vilken bild som helst som uppfyller [ikonkriterierna](https://developer.chrome.com/apps/manifest/icons).
4. Skapa en fil med namnet `background.js` med hjälp av följande kod:
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    Filen gör att HTML-fönstret för Chrome-appen öppnas (**register.html**). Den definierar också hanteraren **messageReceived** för hanteringen av inkommande push-meddelanden.
5. Skapa en fil med namnet `register.html`. Den definierar användargränssnittet för Chrome-appen. 
   
   > [!NOTE]
   > I det här exemplet används **CryptoJS v3.1.2**. Om du har hämtat en annan version av biblioteket måste du ersätta versionen på ett lämpligt sätt i sökvägen `src`.
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. Skapa en fil med namnet `register.js` med koden i det här steget. Den här filen anger skriptet bakom `register.html`. Chrome-apparna tillåter inte infogad körning och därför måste du skapa ett separat säkerhetskopieringsskript för ditt användargränssnitt.
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    Skriptet har följande nyckelparametrar:
   
   * **window.onLoad** definierar knapptryckningshändelserna för de två knapparna i användargränssnittet. Den första knapptryckshändelsehanteraren står för registrering med GCM och den andra använder registrerings-ID:et som returneras efter GCM-registreringen för registrering med Azure Notification Hubs.
   * **updateLog** är funktionen som möjliggör kodlogginformation. 
   * **registerWithGCM** är den första knapptryckningshanteraren. Den gör att anropet `chrome.gcm.register` till GCM registrerar den aktuella Chrome App-instansen.
   * **registerCallback** är den återanropsfunktion som anropas när registreringsanropet för GCM svarar.
   * **registerWithNH** är den andra knapptryckningshanteraren. Den utför registreringar med Notification Hubs. Den får `hubName` och `connectionString` (som användaren har angett) och utformar REST-API-anropet för Notification Hubs Registration.
   * **splitConnectionString** och **generateSaSToken** är hjälpprogram som representerar JavaScript-implementeringen för skapandeprocessen av SaS-token. Dessa måste användas i alla REST-API-anrop. Mer information finns i [Vanliga koncept](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** är funktionen som gör ett HTTP-REST-anrop till Azure Notification Hubs.
   * **registrationPayload** definierar nyttolasten för registrering av XML. Mer information finns i [Skapa Registration NH REST API]. Uppdatera registrerings-ID:t i den med värdet du tog emot från GCM.
   * **client** är en instans av **XMLHttpRequest** som programmet använder för att utföra HTTP POST-begäran. Uppdatera rubriken `Authorization` med `sasToken`. Om det här anropet slutförs registreras den här Chrome App-instansen med Azure Notification Hubs.

        Den övergripande mappstrukturen för det här projektet bör likna följande struktur: ![Google Chrome App – mappstruktur][21]

### <a name="set-up-and-test-your-chrome-app"></a>Konfigurera och testa din Chrome-app
1. Öppna webbläsaren Chrome. Öppna **Chrome-tilläggen** och aktivera **utvecklarläget**.
   
    ![Google Chrome – Aktivera utvecklarläget][16]
2. Klicka på **Ladda uppackat tillägg** och gå till mappen där du skapade filerna. Du kan också använda **Utvecklarverktyget för Chrome-appar och tillägg** om du vill. Det här verktyget är en Chrome-app i sig själv (som installerats från Chrome Web Store) och ger dig avancerade felsökningsfunktioner för utveckling av Chrome-appar.
   
    ![Google Chrome – Inläsning av uppackat tillägg][17]
3. Om Chrome-appen har skapats utan fel dyker den upp.
   
    ![Google Chrome – Visning av Chrome-appen][18]
4. Ange det **projektnummer** som du tidigare fått från **Google Cloud-konsolen** som avsändar-ID. Klicka sedan på **Registrera med GCM**. Meddelandet **Registrering med GCM har genomförts** måste visas.
   
    ![Google Chrome – Anpassning av Chrome-appen][19]
5. Ange **meddelandehubbens namn** och den **DefaultListenSharedAccessSignature** som du tidigare fått från portalen. Klicka sedan på **Registrera med Azure Notification Hub**. Meddelandet **Registreringen på Notification Hub har genomförts!** måste visas, liksom den detaljerade informationen om registreringssvaret, som innehåller registrerings-ID:et för Azure Notification Hubs.
   
    ![Google Chrome – Ange detaljer för Notification Hub][20]  

## <a name="send"></a>Skicka ett meddelande till din Chrome-app
I testsyfte skickas Chrome-push-meddelanden med hjälp av en .NET-konsolapp. 

> [!NOTE]
> Du kan skicka push-meddelanden med Notification Hubs från alla serverdelar via det offentliga <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-gränssnittet</a>. I [dokumentationsportalen](https://azure.microsoft.com/documentation/services/notification-hubs/) finns fler plattformsoberoende exempel.
> 
> 

1. I Visual Studio väljer du **Ny** från **Arkiv**-menyn, och sedan **Projekt**. Under **Visual C#** klickar du på **Windows** och **Konsolapp** och sedan på **OK**.  Det här skapar ett nytt konsolprogramprojekt.
2. Öppna menyn **Verktyg**. Klicka på **NuGet-pakethanterare** och sedan på **Package Manager-konsolen**. Du ser Package Manager-konsolen i det nedre fönstret.
3. Kör följande kommando i konsolfönstret:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   En referens till Azure Service Bus-SDK:n med <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/"> WindowsAzure.ServiceBus NuGet-paketet läggs automatiskt till i projektet</a>
4. Öppna `Program.cs` och lägg till följande `using`-uttryck:
   
        using Microsoft.Azure.NotificationHubs;
5. Lägg till följande metod i `Program`-klassen:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    Ersätt platshållaren `<hub name>` med namnet på den meddelandehubb som visas i [portalen](https://portal.azure.com) på din Notification Hub-sida. Dessutom måste du ersätta platshållaren för anslutningssträngen med den anslutningssträng som heter `DefaultFullSharedAccessSignature`. Du fick den i konfigurationsavsnittet för meddelandehubben.
   
    > [!NOTE]
    > Kontrollera att du använder anslutningssträngen med **fullständig** åtkomst, inte enbart åtkomst för att **lyssna**. Anslutningssträngen med **lyssna**-åtkomst ger inte behörighet att skicka push-meddelanden.
6. Lägg till följande anrop i metoden `Main`:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Kontrollera att Chrome körs och starta konsolappen.
8. Du bör se följande popup-meddelande på skrivbordet.
   
    ![Google Chrome – Meddelande][13]
9. Du kan även visa alla meddelanden med hjälp av fönstret Chrome Notifications i aktivitetsfältet (i Windows) när Chrome körs.
   
    ![Google Chrome – Meddelandelista](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Du behöver inte köra Chrome-appen eller ha den öppen i webbläsaren (även om själva webbläsaren Chrome måste köras). Du kan också få en samlad vy över alla meddelanden i fönstret Chrome Notifications.
> 
> 

## <a name="next-steps"> </a>Nästa steg
I den här självstudiekursen har du skickat meddelanden till alla klienter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie: 

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome App Notification Hub Sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Översikt över Chrome-appar]: https://developer.chrome.com/apps/about_apps
[Chrome App GCM Sample]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome-appar på mobila enheter]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Skapa Registration NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteket crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
