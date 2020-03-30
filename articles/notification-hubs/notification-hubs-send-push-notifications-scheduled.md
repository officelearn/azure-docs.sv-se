---
title: Så här skickar du schemalagda meddelanden | Microsoft-dokument
description: Det här avsnittet beskriver hur du använder schemalagda meddelanden med Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: push-meddelanden,push-meddelande,schemalägga push-meddelanden
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
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212999"
---
# <a name="how-to-send-scheduled-notifications"></a>Så här skickar du schemalagda meddelanden

Om du har ett scenario där du vill skicka ett meddelande någon gång i framtiden, men inte har ett enkelt sätt att väcka din backend-kod för att skicka meddelandet. Standardnivåmeddelandehubbar stöder en funktion som gör att du kan schemalägga meddelanden upp till sju dagar i framtiden.


## <a name="schedule-your-notifications"></a>Schemalägg dina aviseringar
När du skickar ett meddelande använder du bara [ `ScheduledNotification` klassen](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) i SDK-meddelandehubben som visas i följande exempel:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Avboka schemalagda meddelanden
Du kan också avbryta ett tidigare schemalagt meddelande med hjälp av meddelandetId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Det finns inga gränser för hur många schemalagda meddelanden du kan skicka.

## <a name="next-steps"></a>Nästa steg

Se följande självstudiekurser:

 - [Push-meddelanden till alla registrerade enheter](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Push-meddelanden till specifika enheter](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Skicka lokaliserade meddelanden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Skicka meddelanden till specifika användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
