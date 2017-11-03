---
title: Skicka push-meddelanden med Azure Notification Hubs och Node.js
description: "Lär dig hur du använder Notification Hubs för att skicka push-meddelanden från en Node.js-program."
keywords: push-meddelande, push notifications,node.js push ios push
services: notification-hubs
documentationcenter: nodejs
author: ysxu
manager: erikre
editor: 
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/25/2016
ms.author: yuaxu
ms.openlocfilehash: dc4987b16b2e930641c6c90eff8b65c1bf8d573c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Skicka push-meddelanden med Azure Notification Hubs och Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Översikt
> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Den här guiden visar hur du skickar push-meddelanden med hjälp av Azure Notification Hubs direkt från ett Node.js-program. 

Scenarier som tas upp inkluderar skicka push-meddelanden till program på följande plattformar:

* Android
* iOS
* Windows Phone
* Universella Windows-plattformen 

Mer information om notification hubs finns i [nästa steg](#next) avsnitt.

## <a name="what-are-notification-hubs"></a>Vad är Notification Hub?
Azure Notification Hubs innehåller en lätt att använda flera plattformar, skalbar infrastruktur för att skicka push-meddelanden till mobila enheter. Mer information om-infrastruktur, finns det [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) sidan.

## <a name="create-a-nodejs-application"></a>Skapa en Node.js-program
Det första steget i den här kursen är att skapa en ny tom Node.js-program. Instruktioner om hur du skapar en Node.js-program finns i [skapa och distribuera ett Node.js-program till Azure-webbplats][nodejswebsite], [Node.js Molntjänsten] [ Node.js Cloud Service] med Windows PowerShell eller [webbplatsen med WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurera ditt program du använder Notification Hubs
Om du vill använda Azure Notification Hubs, du behöver hämta och använda Node.js [azure-paketet](https://www.npmjs.com/package/azure), som innehåller en inbyggd grupp helper bibliotek som kommunicerar med push notification REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd noden Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Linux) och navigera till mappen där du skapade ditt tomt program.
2. Typen **npm installera azure-sb** i kommandofönstret.
3. Du kan köra manuellt på **ls** eller **dir** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. Inuti den mappen hitta den **azure** paket som innehåller de bibliotek som du behöver åtkomst till Meddelandehubben.

> [!NOTE]
> Du kan lära dig mer om att installera NPM på officiellt [NPM blogg](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>Importera modulen
Med hjälp av en textredigerare, Lägg till följande överst i den **server.js** filen för programmet:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Skapa en anslutning med Azure Notification Hub
Den **NotificationHubService** objekt kan du arbeta med notification hubs. Följande kod skapar en **NotificationHubService** objekt för nofication hubben med namnet **hubname**. Lägg till den övre delen av den **server.js** filen när instruktionen för att importera modulen för azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Anslutningen **connectionstring** värde kan hämtas från den [Azure Portal] genom att utföra följande steg:

1. I det vänstra navigeringsfönstret klickar du på **Bläddra**.
2. Välj **Meddelandehubbar**, och sen hittar du vill använda för hubben. Du kan referera till den [Windows Store komma igång-kursen](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) om du behöver hjälp med att skapa en ny Meddelandehubb.
3. Välj **inställningar**.
4. Klicka på **åtkomstprinciper**. Båda anslutningssträngar för delade och fullständig åtkomst visas.

![Azure Portal – Notification hub](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Du kan också hämta den anslutningssträng genom att använda den **Get-AzureSbNamespace** cmdlet som tillhandahålls av [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller **azure sb namnområde visa** kommandot med de [Azure kommandoradsverktyget gränssnitt (Azure CLI)](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Allmän arkitektur
Den **NotificationHubService** objektet innehåller följande objektinstanser för att skicka push-meddelanden till specifika enheter och program:

* **Android** – Använd den **GcmService** -objekt som finns på **notificationHubService.gcm**
* **iOS** -använder den **ApnsService** -objekt som är tillgängliga i **notificationHubService.apns**
* **Windows Phone** -använder den **MpnsService** -objekt som finns på **notificationHubService.mpns**
* **Uwp** -använder den **WnsService** -objekt som finns på **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Så här: skicka push-meddelanden till Android-program
Den **GcmService** objektet innehåller en **skicka** metod som kan användas för att skicka push-meddelanden till Android-program. Den **skicka** metoden accepterar följande parametrar:

* **Taggar** -taggen identifierare. Om ingen tagg anges, skickas meddelandet till alla klienter.
* **Nyttolasten** -meddelandets JSON eller raw sträng nyttolast.
* **Motringning** -Återanropsfunktionen.

Mer information om nyttolastformatet finns i **nyttolast** avsnitt i den [implementera GCM-Server](http://developer.android.com/google/gcm/server.html#payload) dokumentet.

I följande kod används den **GcmService** instans som exponeras av den **NotificationHubService** att skicka ett push-meddelande till alla registrerade klienter.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Så här: skicka push-meddelanden i iOS-program
Samma som med Android program som beskrivs ovan, den **ApnsService** objektet innehåller en **skicka** metod som kan användas för att skicka push-meddelanden till iOS-program. Den **skicka** metoden accepterar följande parametrar:

* **Taggar** -taggen identifierare. Om ingen tagg anges, skickas meddelandet till alla klienter.
* **Nyttolasten** -meddelandets JSON eller sträng nyttolast.
* **Motringning** -Återanropsfunktionen.

Mer information om nyttolastformatet finns i **meddelande nyttolast** avsnitt i den [lokala Programmeringsguide och Push Notification](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentet.

I följande kod används den **ApnsService** instans som exponeras av den **NotificationHubService** att skicka ett meddelande till alla klienter:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Så här: skicka push-meddelanden till Windows Phone-program
Den **MpnsService** objektet innehåller en **skicka** metod som kan användas för att skicka push-meddelanden till Windows Phone-program. Den **skicka** metoden accepterar följande parametrar:

* **Taggar** -taggen identifierare. Om ingen tagg anges, skickas meddelandet till alla klienter.
* **Nyttolasten** -meddelandets nyttolast för XML.
* **Målnamn**  -  `toast` för popup-meddelanden. `token`för sida vid sida-meddelanden.
* **NotificationClass** -prioriteten för meddelandet. Finns det **HTTP-huvudet element** avsnitt i den [Push-meddelanden från en server](http://msdn.microsoft.com/library/hh221551.aspx) dokument för giltiga värden.
* **Alternativ** – valfritt begärandehuvuden.
* **Motringning** -Återanropsfunktionen.

En lista över giltiga **TargetName**, **NotificationClass** och Rubrikalternativ, ta en titt på [Push-meddelanden från en server](http://msdn.microsoft.com/library/hh221551.aspx) sidan.

Följande exempel koden använder den **MpnsService** instans som exponeras av den **NotificationHubService** att skicka ett popup-push-meddelande:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Så här: skicka push-meddelanden för universella Windowsplattformen (UWP)-program
Den **WnsService** objektet innehåller en **skicka** metod som kan användas för att skicka push-meddelanden till Uwp-program.  Den **skicka** metoden accepterar följande parametrar:

* **Taggar** -taggen identifierare. Om ingen tagg anges, skickas meddelandet till alla registrerade klienter.
* **Nyttolasten** -nyttolast för XML-meddelande.
* **Typen** -meddelandetyp.
* **Alternativ** – valfritt begärandehuvuden.
* **Motringning** -Återanropsfunktionen.

En lista över giltiga typer och begärandehuvuden finns [Push notification service begärande- och svarshuvuden](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

I följande kod används den **WnsService** instans som exponeras av den **NotificationHubService** att skicka ett popup-push-meddelande till en UWP-app:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Nästa steg
Fragmenten exemplet ovan kan du enkelt skapa infrastruktur för att leverera push-meddelanden till en mängd olika enheter. Nu när du har lärt dig grunderna i att använda Notification Hubs med node.js kan du följa dessa länkar om du vill veta mer om hur du kan utöka dessa ytterligare funktioner.

* Finns i MSDN-referens för [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Besök den [Azure SDK för noden] lagringsplats på GitHub för fler exempel och implementeringsdetaljer.

[Azure SDK för noden]: https://github.com/WindowsAzure/azure-sdk-for-node
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
[Azure Classic Portal]: http://manage.windowsazure.com
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[webbplatsen med WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
