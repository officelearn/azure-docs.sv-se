---
title: Skicka schemalagda meddelanden | Microsoft Docs
description: I det här avsnittet beskrivs hur du använder schemalagda meddelanden med Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: push-meddelanden, push-meddelande, schemaläggning av push-meddelanden
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998279"
---
# <a name="how-to-send-scheduled-notifications"></a>Gör så här: skicka schemalagda meddelanden

Om du har ett scenario där du vill skicka ett meddelande vid ett senare tillfälle, men inte har ett enkelt sätt att aktivera din backend-kod för att skicka meddelandet. Aviserings hubbar på standard nivå stöder en funktion som gör att du kan schemalägga meddelanden upp till sju dagar i framtiden.


## <a name="schedule-your-notifications"></a>Schemalägg dina meddelanden
När du skickar ett meddelande använder du bara- [ `ScheduledNotification` klassen](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) i Notification Hubs SDK, som du ser i följande exempel:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Avbryt schemalagda meddelanden
Du kan också avbryta ett tidigare schemalagt meddelande med hjälp av dess notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Det finns ingen gräns för hur många schemalagda meddelanden du kan skicka.

## <a name="next-steps"></a>Nästa steg

Se följande självstudiekurser:

 - [Push-meddelanden till alla registrerade enheter](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Skicka push-meddelanden till specifika enheter](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Skicka lokaliserade meddelanden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Skicka push-meddelanden till specifika användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Skicka platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
