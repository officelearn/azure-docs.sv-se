---
title: Azure Notification Hubs iOS 13-uppdateringar | Microsoft-dokument
description: Läs mer om iOS 13-brottsändringar i Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228146"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs-uppdateringar för iOS 13

Apple gjorde nyligen några ändringar i sin offentliga push-tjänst; ändringarna i linje mestadels med utgåvorna av iOS 13 och Xcode. I den här artikeln beskrivs effekten av dessa ändringar på Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>APNS push nyttolaständringar

### <a name="apns-push-type"></a>APNS push-typ

Apple kräver nu att utvecklare identifierar meddelanden som `apns-push-type` en avisering eller bakgrundsmeddelanden via det nya huvudet i APNS API. Enligt [Apples dokumentation:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"Värdet på denna rubrik måste korrekt återspegla innehållet i anmälans nyttolast. Om det finns en obalans, eller om huvudet saknas på nödvändiga system, kan APN returnera ett fel, fördröja leveransen av anmälan eller släppa det helt och hållet."

Utvecklare måste nu ange det här huvudet i program som skickar meddelanden via Azure Notification Hubs. På grund av en teknisk begränsning måste kunder använda tokenbaserad autentisering för APNS-autentiseringsuppgifter med begäranden som innehåller det här attributet. Om du använder certifikatbaserad autentisering för APNS-autentiseringsuppgifterna måste du växla till tokenbaserad autentisering.

Följande kodexempel visar hur du anger det här huvudattributet i meddelandebegäranden som skickas via Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Mallmeddelanden - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Inbyggda meddelanden - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Direkt REST-anrop

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

För att hjälpa dig under den här övergången, när Azure Notification `apns-push-type` Hubs identifierar ett meddelande som inte har uppsättningen, drar tjänsten slutsatsen att push-typen från meddelandebegäran och anger värdet automatiskt. Kom ihåg att du måste konfigurera Azure Notification Hubs för att använda tokenbaserad autentisering för att ange den obligatoriska huvudet. Mer information finns i [Tokenbaserad (HTTP/2) Autentisering för APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>APNS-prioritet

En annan mindre ändring, men en som kräver en ändring av backend-programmet `apns-priority` som skickar meddelanden, är kravet att för bakgrundsmeddelanden måste huvudet nu anges till 5. Många program `apns-priority` anger huvudet till 10 (som anger omedelbar leverans), eller inte ställa in den och få standardvärdet (som också är 10).

Det här värdet är inte längre tillåtet för bakgrundsmeddelanden och du måste ange värdet för varje begäran. Apple kommer inte att leverera bakgrundsmeddelanden om det här värdet saknas. Ett exempel:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-ändringar

I flera år använde `description` iOS-utvecklare attributet för de `deviceToken` data som skickades till push-tokendelegaten för att extrahera push-token som ett serverdelsprogram använder för att skicka meddelanden till enheten. Med Xcode 11 `description` ändrades attributet till ett annat format. Befintlig kod som utvecklare som används för det här attributet är nu bruten. Vi har uppdaterat Azure Notification Hubs SDK för att hantera den här ändringen, så uppdatera SDK som används av dina program till version 2.0.4 eller nyare av [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
