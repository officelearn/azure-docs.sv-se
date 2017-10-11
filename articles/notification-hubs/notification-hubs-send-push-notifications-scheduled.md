---
title: Hur du skickar meddelanden schemalagda | Microsoft Docs
description: "Det här avsnittet beskriver hur du använder schemalagda meddelanden med Azure Notification Hubs."
services: notification-hubs
documentationcenter: .net
keywords: "push-meddelanden, push-meddelande, schemalägga push-meddelanden"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Så här: Skicka schemalagda meddelanden
## <a name="overview"></a>Översikt
Om du har ett scenario där du vill skicka ett meddelande vid en viss tidpunkt i framtiden, men inte har ett enkelt sätt att aktivera upp backend-koden för att skicka meddelandet. Standardnivån Notification Hubs stöder en funktion som gör att du kan schemalägga meddelanden upp till 7 dagar i framtiden.

När du skickar ett meddelande kan bara använda den [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) klassen i Notification Hubs SDK som visas i följande exempel:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Du kan också avbryta en tidigare schemalagda meddelanden med dess notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Det finns ingen begränsning för antalet schemalagda meddelanden som du kan skicka.

