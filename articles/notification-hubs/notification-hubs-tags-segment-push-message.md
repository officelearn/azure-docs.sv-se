---
title: Routning och Tagguttryck
description: Det här avsnittet beskriver Routning och tagg uttryck för Azure notification hub.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: f52900d06049623a0a81e509abdc5e9ef76f95e5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451484"
---
# <a name="routing-and-tag-expressions"></a>Uttryck för Routning och tagg

## <a name="overview"></a>Översikt

Tagguttryck kan du target specifika uppsättningar enheter eller mer specifikt registreringar när du skickar ett push-meddelande via Meddelandehubbar.

## <a name="targeting-specific-registrations"></a>Riktar in sig på specifika registreringar

Det enda sättet att målet specifikt meddelande registreringar är att associera taggar med dem, rikta taggarna. Enligt beskrivningen i [Registreringshantering](notification-hubs-push-notification-registration-management.md)för att ta emot push-meddelanden som en app har att registrera en enhet som hanteras på en notification hub. När en registrering har skapats på en notification hub kan kan serverdelen för programmet skicka push-meddelanden till den.
Serverdelen för programmet kan välja registreringar till målet med ett specifikt meddelande på följande sätt:

1. **Skicka**: alla registreringar i meddelandehubben tar emot meddelandet.
2. **Taggen**: alla registreringar som innehåller den angivna taggen tar emot meddelandet.
3. **Tagguttryck**: alla registreringar vars uppsättning taggar matchar det angivna uttrycket tar emot meddelandet.

## <a name="tags"></a>Taggar

En tagg kan vara valfri sträng, upp till 120 tecken, som innehåller alfanumeriska och följande icke-alfanumeriska tecken: '_', ' @', '#', '. ',':', '-'. I följande exempel visar ett program som du kan ta emot popup-meddelanden om specifika musik grupper. I det här scenariot är ett enkelt sätt att dirigera meddelanden till etiketten registreringar med taggar som representerar olika band, som i följande bild:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

På den här bilden meddelandet taggade **Beatles** når den surfplatta som registrerats med taggen **Beatles**.

Läs mer om hur du skapar registreringar för taggar, [Registreringshantering](notification-hubs-push-notification-registration-management.md).

Du kan skicka meddelanden till taggar på metoderna skicka meddelanden med den `Microsoft.Azure.NotificationHubs.NotificationHubClient` klassen i den [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Du kan också använda Node.js- eller Push-meddelanden REST API: erna.  Här är ett exempel med hjälp av SDK.

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

Taggar måste inte vara företablerade och kan referera till flera appspecifika begrepp. Exempelvis kan användare av det här exempelprogrammet kommentera band och vill få toasts, inte bara för kommentarer om sina favorit band, utan också för alla kommentarer från sina vänner, oavsett den band där de kommentarer. Följande bild visar ett exempel på det här scenariot:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Alice är intresserade av uppdateringar för Beatles på den här bilden och Bob är intresserade av uppdateringar för Wailers. Bob är också intresserat Charlies kommentarer och Charlie är intresserad av att Wailers. När ett meddelande har skickats för Charlies kommentera Beatles, får både Alice och Bob den.

Medan du kan koda flera frågor i taggar (till exempel ”band_Beatles” eller ”follows_Charlie”), är taggar enkla strängar och inte egenskaper med värden. En registrering matchas endast på närvaron eller frånvaron av en viss tagg.

En fullständig stegvis självstudiekurs om hur du använder taggar för att skicka till intressegrupper, se [större nyheter](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Med hjälp av taggar till målanvändare

Ett annat sätt att använda taggar är att identifiera alla enheter av en viss användare. Registreringar taggas med en tagg som innehåller ett användar-ID, som i följande bild:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

På den här bilden taggade meddelandet uid: Alice når alla registreringar taggade ”uid:Alice”; därför alla Alices enheter.

## <a name="tag-expressions"></a>Tagguttryck

Det finns fall där ett meddelande har att rikta en uppsättning registreringar som identifieras inte som en enskild tagg, men med ett booleskt uttryck på taggar.

Överväg ett sport-program som skickar en påminnelse till alla i Boston om ett spel mellan Red Sox och Cardinals. Om klientappen registrerar taggar om intresse för team och plats, vara meddelandet mål för alla i Boston som är intresserade Red Sox eller Cardinals. Det här tillståndet kan uttryckas med följande booleskt uttryck:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Tagguttryck kan innehålla alla booleska operatorer som och (& &), eller (|) och inte (!). De kan också innehålla parenteser. Tagguttryck är begränsade till 20 taggar om de innehåller endast ORs; Annars är de begränsade till 6 taggar.

Här är ett exempel för att skicka meddelanden med Tagguttryck med hjälp av SDK.

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
