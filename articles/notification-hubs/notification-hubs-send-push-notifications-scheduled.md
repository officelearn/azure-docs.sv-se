---
title: Hur du skickar meddelanden schemalagda | Microsoft Docs
description: Det här avsnittet beskriver hur du använder schemalagda meddelanden med Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: push-meddelanden, push-meddelande, schemalägga push-meddelanden
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776985"
---
# <a name="how-to-send-scheduled-notifications"></a>Så här: Skicka schemalagda meddelanden
## <a name="overview"></a>Översikt
Om du har ett scenario där du vill skicka ett meddelande vid en viss tidpunkt i framtiden, men inte har ett enkelt sätt att aktivera upp backend-koden för att skicka meddelandet. Standardnivån meddelandehubbar stöd för en funktion som gör att du kan schemalägga meddelanden upp till sju dagar i framtiden.

När du skickar ett meddelande kan bara använda den [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) klassen i Notification Hubs SDK som visas i följande exempel:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Du kan också avbryta en tidigare schemalagda meddelanden med dess notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Det finns ingen begränsning för antalet schemalagda meddelanden som du kan skicka.

