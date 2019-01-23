---
title: Hur du skickar schemalagda meddelanden | Microsoft Docs
description: Det här avsnittet beskriver hur du använder schemalagda meddelanden med Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: push-meddelanden, push-meddelande, schemaläggning av push-meddelanden
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471845"
---
# <a name="how-to-send-scheduled-notifications"></a>Hur: Skicka schemalagda meddelanden

Om du har ett scenario där du vill skicka ett meddelande vid en viss tidpunkt i framtiden, men som inte har ett enkelt sätt att aktivera in backend-koden så att skicka meddelandet. Standard-nivån för meddelandehubbar stöd för en funktion som gör att du kan schemalägga meddelanden upp till sju dagar i framtiden.


## <a name="schedule-your-notifications"></a>Schemalägg meddelanden
När du skickar ett meddelande, använder bara den [ `ScheduledNotification` klass](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) i Notification Hubs SDK som visas i följande exempel:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Avbryt schemalagda meddelanden
Du kan också avbryta en tidigare schemalagda meddelande med hjälp av dess meddelande-ID:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Det finns ingen gräns för antalet schemalagda meddelanden som du kan skicka.

## <a name="next-steps"></a>Nästa steg

Se följande självstudiekurser:

 - [Push-meddelanden till alla registrerade enheter](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Skicka meddelanden till specifika enheter](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Skicka lokaliserade meddelanden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Skicka meddelanden till specifika användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
