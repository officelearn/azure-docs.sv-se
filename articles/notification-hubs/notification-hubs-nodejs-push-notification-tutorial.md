---
title: Skicka push-meddelanden med Azure Notification Hub och Node.js
description: Lär dig hur du använder Meddelandehubbar för att skicka push-meddelanden från ett Node.js-program.
keywords: push-meddelande, push notifications,node.js push ios push
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bdeba401e99ad16555b9f6ea00017fc525302983
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995018"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Skicka push-meddelanden med Azure Notification Hub och Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har ett konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter via den [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Den här guiden visar hur du skickar push-meddelanden med hjälp av Azure Notification Hubs direkt från en [Node.js](https://nodejs.org) program.

Scenarier som omfattas är att skicka push-meddelanden till program på följande plattformar:

- Android
- iOS
- Universell Windows-plattform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Meddelandehubbar ger en enkel att använda, flera plattformar, skalbara infrastruktur för att skicka push-meddelanden till mobila enheter. Mer information om infrastrukturen, finns det [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) sidan.

## <a name="create-a-nodejs-application"></a>Skapa en Node.js-program

Det första steget i den här självstudien är att skapa en ny tom Node.js-program. Anvisningar om hur du skapar ett Node.js-program finns i [skapa och distribuera ett Node.js-program till Azure Web Site][nodejswebsite], [Node.js molntjänst] [ Node.js Cloud Service] med hjälp av Windows PowerShell eller [webbplats med WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurera ditt program du använder Notification Hubs

Om du vill använda Azure Notification Hubs, du behöver att hämta och använda Node.js [azure-paketet](https://www.npmjs.com/package/azure), som innehåller en inbyggd uppsättning helper-bibliotek som kommunicerar med push-meddelandetjänster för REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använda Node Package Manager (NPM) för att hämta paketet

1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Linux) och gå till den mapp där du skapade ditt tom program.
2. Köra `npm install azure-sb` i kommandofönstret.
3. Du kan köra manuellt i `ls` eller `dir` kommando för att kontrollera att en `node_modules` mappen har skapats.
4. I den mappen, hitta den **azure** paket som innehåller de bibliotek som du behöver komma åt Meddelandehubben.

> [!NOTE]
> Du kan lära dig mer om att installera NPM på officiellt [NPM blogg](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importera modulen
Använd en textredigerare och Lägg till följande överst i `server.js` i appen:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Skapa en anslutning med Azure Notification Hub

Den `NotificationHubService` objekt kan du arbeta med meddelandehubbar. Följande kod skapar en `NotificationHubService` objekt för meddelandehubben med namnet `hubname`. Lägga till den i den övre delen av den `server.js` filen efter instruktionen för att importera azure-modulen:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Hämta anslutningen `connectionstring` värdet från den [Azure Portal] genom att utföra följande steg:

1. I det vänstra navigeringsfönstret klickar du på **Bläddra**.
2. Välj **Meddelandehubbar**, och leta reda på den hubben som du vill använda för exemplet. Du kan referera till den [Windows Store komma igång-Självstudier](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) om du behöver hjälp med att skapa en ny Meddelandehubb.
3. Välj **inställningar**.
4. Klicka på **åtkomstprinciper**. Du kan se båda anslutningssträngarna för delade och full åtkomst.

![Azure portal – Meddelandehubbar](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Du kan också hämta den anslutningssträng genom att använda den **Get-AzureSbNamespace** cmdlet som tillhandahålls av [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller **azure sb namnområde show** kommandot med den [Kommandoradsgränssnittet för azure gränssnitt (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Allmän arkitektur

Den `NotificationHubService` objektet innehåller följande objektinstanser för att skicka push-meddelanden till specifika enheter och program:

- **Android** – Använd den `GcmService` objekt, som finns på `notificationHubService.gcm`
- **iOS** – Använd den `ApnsService` objekt, som är tillgänglig på `notificationHubService.apns`
- **Windows Phone** – Använd den `MpnsService` objekt, som finns på `notificationHubService.mpns`
- **Universal Windows Platform** – Använd den `WnsService` objekt, som finns på `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Anvisningar: Skicka push-meddelanden till Android-program

Den `GcmService` objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till Android-program. Den `send` metoden godkänner följande parametrar:

- **Taggar** -tagg-identifierare. Om ingen tagg har angetts, skickas meddelandet till alla klienter.
- **Nyttolasten** -meddelandets JSON eller rådatasträng nyttolast.
- **Motringning** -Återanropsfunktionen.

Mer information om nyttolastformatet finns i den **nyttolast** delen av den [implementera GCM-Server](https://developer.android.com/google/gcm/server.html#payload) dokumentet.

I följande kod används den `GcmService` instans som exponeras av den `NotificationHubService` att skicka ett push-meddelande till alla registrerade klienter.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Anvisningar: Skicka push-meddelanden till iOS-program

Samma som med Android-program som beskrivs ovan, den `ApnsService` objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till iOS-program. Den `send` metoden godkänner följande parametrar:

- **Taggar** -tagg-identifierare. Om ingen tagg har angetts, skickas meddelandet till alla klienter.
- **Nyttolasten** -meddelandets nyttolast för JSON eller sträng.
- **Motringning** -Återanropsfunktionen.

Läs mer om nyttolastformatet den **Meddelandenyttolasten** delen av den [lokala och Push-meddelande Programming Guide](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentet.

I följande kod används den `ApnsService` instans som exponeras av den `NotificationHubService` att skicka ett aviseringsmeddelande till alla klienter:

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

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Anvisningar: Skicka push-meddelanden till Windows Phone-program

Den `MpnsService` objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till Windows Phone-program. Den `send` metoden godkänner följande parametrar:

- **Taggar** -tagg-identifierare. Om ingen tagg har angetts, skickas meddelandet till alla klienter.
- **Nyttolasten** -meddelandets nyttolast för XML.
- **Målnamn**  -  `toast` för popup-meddelanden. `token` för sida vid sida-meddelanden.
- **NotificationClass** -prioriteten för meddelandet. Se den **huvudelement som HTTP-** delen av den [Push-meddelanden från en server](https://msdn.microsoft.com/library/hh221551.aspx) dokumentet för giltiga värden.
- **Alternativ** – valfritt rubriker i begäran.
- **Motringning** -Återanropsfunktionen.

En lista över giltiga `TargetName`, `NotificationClass` och Rubrikalternativ, Kolla in den [Push-meddelanden från en server](https://msdn.microsoft.com/library/hh221551.aspx) sidan.

Följande exempel på kod använder den `MpnsService` instans som exponeras av den `NotificationHubService` att skicka ett popup-push-meddelande:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Anvisningar: Skicka push-meddelanden till program för Universal Windows Platform (UWP)

Den `WnsService` objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till Universal Windows Platform-program.  Den `send` metoden godkänner följande parametrar:

- **Taggar** -tagg-identifierare. Om ingen tagg har angetts, skickas meddelandet till alla registrerade klienter.
- **Nyttolasten** -nyttolast för XML-meddelande.
- **Typ** -meddelandetypen.
- **Alternativ** – valfritt rubriker i begäran.
- **Motringning** -Återanropsfunktionen.

En lista över giltiga typer och begärandehuvuden finns i [Push notification service begärande- och svarshuvuden](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

I följande kod används den `WnsService` instans som exponeras av den `NotificationHubService` att skicka ett popup-push-meddelande till en UWP-app:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Nästa steg

Kodavsnitten exemplet ovan kan du enkelt skapa infrastruktur för att leverera push-meddelanden till en mängd olika enheter. Nu när du har lärt dig grunderna i att använda Meddelandehubbar med node.js kan du följa dessa länkar om du vill veta mer om hur du kan utöka funktionerna ytterligare.

- Finns i MSDN-referens för [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Gå till den [Azure SDK för Node] arkivet på GitHub för fler exempel och information om implementering.

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
