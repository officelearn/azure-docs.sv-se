---
title: Skicka push-meddelanden med Azure Notification Hubs och Node.js
description: Läs om hur du använder meddelandehubbar för att skicka push-meddelanden från ett Node.js-program.
keywords: push-meddelande,push-meddelanden,node.js push,ios push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213191"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Skicka push-meddelanden med Azure Notification Hubs och Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har ett konto skapar du ett kostnadsfritt utvärderingskonto på bara några minuter via [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Den här guiden visar hur du skickar push-meddelanden med hjälp av Azure Notification Hubs direkt från ett [Node.js-program.](https://nodejs.org)

Scenarierna omfattar att skicka push-meddelanden till program på följande plattformar:

- Android
- iOS
- Universell Windows-plattform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs ger en lättanvänd, skalbar infrastruktur med flera plattformar för att skicka push-meddelanden till mobila enheter. Mer information om tjänstinfrastrukturen finns på sidan [Azure Notification Hubs.](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx)

## <a name="create-a-nodejs-application"></a>Skapa ett nod.js-program

Det första steget i den här självstudien är att skapa ett nytt tomt nod.js-program. Instruktioner om hur du skapar ett Node.js-program finns i [Skapa och distribuera ett Node.js-program till Azure-webbplats,][nodejswebsite] [node.js Cloud Service][Node.js Cloud Service] med Windows PowerShell eller Webbplats med [WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurera ditt program för att använda meddelandehubbar

Om du vill använda Azure Notification Hubs måste du hämta och använda node.js [azure-paketet](https://www.npmjs.com/package/azure), som innehåller en inbyggd uppsättning hjälpbibliotek som kommunicerar med REST-tjänst för push-meddelande.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd Nod Package Manager (NPM) för att hämta paketet

1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Linux) och navigera till mappen där du skapade ditt tomma program.
2. Kör `npm install azure-sb` i kommandofönstret.
3. Du kan köra `ls` kommandot `dir` eller manuellt `node_modules` för att kontrollera att en mapp har skapats.
4. I den mappen hittar du **azure-paketet,** som innehåller de bibliotek du behöver för att komma åt meddelandehubben.

> [!NOTE]
> Du kan läsa mer om att installera NPM på den officiella [NPM blogg](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importera modulen
Lägg till följande längst upp i `server.js` programmets fil med hjälp av en textredigerare:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Konfigurera en Azure Notification Hub-anslutning

Med `NotificationHubService` objektet kan du arbeta med meddelandehubbar. Följande kod skapar `NotificationHubService` ett objekt för `hubname`meddelandehubben med namnet . Lägg till den längst `server.js` upp i filen, efter satsen för att importera azure-modulen:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Hämta anslutningsvärdet `connectionstring` från [Azure-portalen] genom att utföra följande steg:

1. Klicka på **Bläddra**i det vänstra navigeringsfönstret.
2. Välj **Meddelandehubbar**och leta sedan reda på det nav som du vill använda för exemplet. Du kan referera till [självstudien för komma igång i Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) om du behöver hjälp med att skapa en ny meddelandehubb.
3. Välj **Inställningar**.
4. Klicka på **Åtkomstprinciper**. Du ser både anslutningssträngar för delad och fullständig åtkomst.

![Azure-portal - Meddelandehubbar](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Du kan också hämta anslutningssträngen med hjälp av cmdleten **Get-AzureSbNamespace** som tillhandahålls av [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller kommandot **Azure SB namespace show** med Azure [Command-Line Interface (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Allmän arkitektur

Objektet `NotificationHubService` exponerar följande objektinstanser för att skicka push-meddelanden till specifika enheter och program:

- **Android** - `GcmService` använd objektet, som finns på`notificationHubService.gcm`
- **iOS** - `ApnsService` använd objektet, som är tillgängligt på`notificationHubService.apns`
- **Windows Phone** - `MpnsService` använd objektet, som är tillgängligt på`notificationHubService.mpns`
- **Universal Windows Platform** `WnsService` - använd objektet, som finns på`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Så här skickar du push-meddelanden till Android-program

Objektet `GcmService` innehåller `send` en metod som kan användas för att skicka push-meddelanden till Android-program. Metoden `send` accepterar följande parametrar:

- **Taggar** - taggidentifieraren. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nyttolast** - meddelandets JSON eller rå sträng nyttolast.
- **Motringning** - motringningsfunktionen.

Mer information om nyttolastformatet finns i [nyttolastdokumentationen](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Följande kod använder `GcmService` den instans `NotificationHubService` som exponeras av för att skicka ett push-meddelande till alla registrerade klienter.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Så här skickar du push-meddelanden till iOS-program

Samma som med Android-program `ApnsService` som `send` beskrivs ovan, ger objektet en metod som kan användas för att skicka push-meddelanden till iOS-program. Metoden `send` accepterar följande parametrar:

- **Taggar** - taggidentifieraren. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nyttolast** - meddelandets JSON eller sträng nyttolast.
- **Motringning** - motringningsfunktionen.

Mer information om nyttolastformatet finns i avsnittet **Meddelandenyttolaster i** dokumentet [Lokal- och push-meddelandeprogrammeringsguide.](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)

Följande kod använder `ApnsService` den instans `NotificationHubService` som exponeras av för att skicka ett varningsmeddelande till alla klienter:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Så här skickar du push-meddelanden till Windows Phone-program

Objektet `MpnsService` innehåller `send` en metod som kan användas för att skicka push-meddelanden till Windows Phone-program. Metoden `send` accepterar följande parametrar:

- **Taggar** - taggidentifieraren. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nyttolast** - meddelandets XML-nyttolast.
- **TargetName**  -  `toast` för popup-meddelanden. `token`för panelmeddelanden.
- **NotificationClass** - Prioriteten för meddelandet. Se avsnittet **HTTP-sidhuvudelement** i [push-meddelanden från ett serverdokument](https://msdn.microsoft.com/library/hh221551.aspx) för giltiga värden.
- **Alternativ** - valfria begäranden.
- **Motringning** - motringningsfunktionen.

En lista över `TargetName` `NotificationClass` giltiga och huvudalternativ finns i [Push-meddelandena från en serversida.](https://msdn.microsoft.com/library/hh221551.aspx)

Följande exempelkod använder `MpnsService` den instans `NotificationHubService` som exponeras av för att skicka ett popup-push-meddelande:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Så här skickar du push-meddelanden till UWP-program (Universal Windows Platform)

Objektet `WnsService` innehåller `send` en metod som kan användas för att skicka push-meddelanden till universella Windows-plattformsprogram.  Metoden `send` accepterar följande parametrar:

- **Taggar** - taggidentifieraren. Om ingen tagg anges skickas meddelandet till alla registrerade klienter.
- **Nyttolast** - nyttolasten för XML-meddelanden.
- **Typ** - meddelandetypen.
- **Alternativ** - valfria begäranden.
- **Motringning** - motringningsfunktionen.

En lista över giltiga typer och begäranden finns i [Begäran om push-meddelandetjänst och svarshuvuden](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Följande kod använder `WnsService` den instans `NotificationHubService` som exponeras av för att skicka ett popup-push-meddelande till en UWP-app:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Efterföljande moment

Med exempelkodavsnitten ovan kan du enkelt skapa tjänstinfrastruktur för att leverera push-meddelanden till en mängd olika enheter. Nu när du har lärt dig grunderna i att använda Meddelandehubbar med nod.js följer du de här länkarna för att lära dig mer om hur du kan utöka dessa funktioner ytterligare.

- Se MSDN-referensen för [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Besök [Azure SDK för nod-databasen] på GitHub om du vill ha fler exempel och implementeringsinformation.

[Azure SDK för Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
