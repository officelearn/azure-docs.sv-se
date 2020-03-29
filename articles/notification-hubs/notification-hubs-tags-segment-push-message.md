---
title: Routning och tagguttryck i Azure Notification Hubs
description: Lär dig hur du dirigerar och taggar uttryck för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062302"
---
# <a name="routing-and-tag-expressions"></a>Routnings- och tagguttryck

## <a name="overview"></a>Översikt

Med tagguttryck kan du rikta in dig på specifika uppsättningar enheter, eller mer specifikt registreringar, när du skickar ett push-meddelande via meddelandehubbar.

## <a name="targeting-specific-registrations"></a>Inriktning på specifika registreringar

Det enda sättet att rikta specifika anmälan registreringar är att associera taggar med dem, sedan rikta dessa taggar. Som diskuterats i [Registreringshantering](notification-hubs-push-notification-registration-management.md)måste en app registrera en enhetsreferens på en meddelandehubb för att ta emot push-meddelanden. När appen har skapat en registrering på en meddelandehubb kan programmets backend skicka push-meddelanden till den. Programmets backend kan välja vilka registreringar som ska riktas med ett specifikt meddelande på följande sätt:

1. **Sändning**: alla registreringar i meddelandehubben får meddelandet.
2. **Tag**: alla registreringar som innehåller den angivna taggen får meddelandet.
3. **Tagguttryck**: alla registreringar vars uppsättning taggar matchar det angivna uttrycket får meddelandet.

## <a name="tags"></a>Taggar

En tagg kan vara vilken sträng som helst, upp till 120 tecken, som`_`innehåller`@`alfanumeriska och följande icke-alfanumeriska tecken: ' ', ', ',`#`',`.`', ',`:`', ', '.`-` I följande exempel visas ett program där du kan ta emot popup-meddelanden om specifika musikgrupper. I det här fallet är ett enkelt sätt att dirigera meddelanden att märka registreringar med taggar som representerar de olika banden, som i följande bild:

![Översikt över taggar](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

I figuren når meddelandet taggade med **Beatles** bara tabletten som registrerats med taggen **Beatles**.

Mer information om hur du skapar registreringar för taggar finns i [Registreringshantering](notification-hubs-push-notification-registration-management.md).

Du kan skicka meddelanden till taggar med `Microsoft.Azure.NotificationHubs.NotificationHubClient` hjälp av metoderna för skicka meddelanden för klassen i [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Du kan också använda Node.js eller REST-API:erna för push-meddelanden.  Här är ett exempel med hjälp av SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Taggar får inte företables och kan referera till flera appspecifika begrepp. Till exempel kan användare av det här exempelprogrammet kommentera band och vill ta emot popup-bröd, inte bara för kommentarerna på sina favoritband, utan också för alla kommentarer från sina vänner, oavsett vilket band de kommenterar. Följande bild visar ett exempel på det här scenariot:

![Taggar vänner](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

I det här exemplet är Alice intresserad av uppdateringar för Beatles, och Bob är intresserad av uppdateringar för Wailers. Bob är också intresserad av Charlies kommentarer, och Charlie är intresserad av Wailers. När ett meddelande skickas för Charlies kommentar till Beatles skickar Notification Hubs det till både Alice och Bob.

Du kan koda flera problem i `band_Beatles` taggar `follows_Charlie`(till exempel eller ) taggar är enkla strängar och inte egenskaper med värden. En registrering matchar endast på närvaron eller frånvaron av en viss tagg.

En fullständig steg-för-steg-självstudie om hur du använder taggar för att skicka till intressegrupper finns i [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs stöder högst 60 taggar per registrering.

## <a name="using-tags-to-target-users"></a>Använda taggar för att rikta in sig på användare

Ett annat sätt att använda taggar är att identifiera alla enheter som är associerade med en viss användare. Du kan tagga en registrering med en tagg som innehåller användar-ID:

![Tagga användare](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

I figuren når meddelandet `user_Alice` som taggats alla `user_Alice`enheter som har taggats med .

## <a name="tag-expressions"></a>Tagguttryck

Det finns fall där meddelanden måste rikta en uppsättning registreringar som inte identifieras av en enda tagg, utan av ett booleskt uttryck med hjälp av taggar.

Överväg en sport program som skickar en påminnelse till alla i Boston om ett spel mellan Red Sox och Cardinals. Om klientappen registrerar taggar om intresse för team och plats, bör meddelandet riktas till alla i Boston som är intresserade av antingen Red Sox eller Cardinals. Detta villkor kan uttryckas med följande boolesk uttryck:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Tagguttryck](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Tagguttryck stöder vanliga booleska `AND` `&&`operatorer som `NOT` `!`( ), `OR` (`||`och ( ). De kan också innehålla parenteser. Tagguttryck med `OR` endast operatorer kan referera till 20 taggar. uttryck `AND` med operatorer `OR` men inga operatorer kan referera till 10 taggar. I annat fall är tagguttryck begränsade till 6 taggar.

Här är ett exempel för att skicka meddelanden med tagguttryck med hjälp av SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
