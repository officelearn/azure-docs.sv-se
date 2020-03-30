---
title: Skicka APNS VOIP-meddelanden med Azure Notification Hubs
description: Lär dig hur du skickar APNS VOIP-meddelanden via Azure Notification Hubs (stöds inte officiellt).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146893"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Använd APNS VOIP via Meddelandehubbar (stöds inte officiellt)

Det är möjligt att använda APNS VOIP-meddelanden via Azure Notification Hubs; Det finns dock inget officiellt stöd för detta scenario.

## <a name="considerations"></a>Överväganden

Om du fortfarande väljer att skicka APNS VOIP-meddelanden via Meddelandehubbar bör du vara medveten om följande begränsningar:

- För att skicka ett `apns-topic` VOIP-meddelande måste huvudet ställas `.voip` in på programpaket-ID + suffixet. För en exempelapp med bunt-ID `com.microsoft.nhubsample` `apns-topic` ska huvudet till exempel ställas in på`com.microsoft.nhubsample.voip.`

   Den här metoden fungerar inte bra med Azure Notification Hubs, eftersom appens paket-ID måste konfigureras som en del av hubbens APNS-autentiseringsuppgifter och värdet kan inte ändras. Meddelandehubbar tillåter inte heller att `apns-topic` värdet för huvudet åsidosätts vid körning.

   Om du vill skicka VOIP-meddelanden måste `.voip` du konfigurera en separat meddelandehubb med apppaket-ID:et.

- För att skicka ett `apns-push-type` VOIP-meddelande måste `voip`huvudet anges till värdet .

   För att hjälpa kunder med övergången till iOS 13 försöker Notification Hubs att dra slutsatsen rätt värde för `apns-push-type` huvudet. Inferenslogiken är avsiktligt enkel, i ett försök att undvika att bryta standardmeddelanden. Tyvärr orsakar den här metoden problem med VOIP-meddelanden, eftersom Apple behandlar VOIP-meddelanden som ett specialfall som inte följer samma regler som standardmeddelanden.

   Om du vill skicka VOIP-meddelanden måste `apns-push-type` du ange ett explicit värde för huvudet.

- Meddelandehubbar begränsar APNS nyttolaster till 4 KB, vilket dokumenteras av Apple. För VOIP-meddelanden tillåter Apple nyttolaster på upp till 5 kB. Meddelandehubbar skiljer inte mellan standard- och VOIP-meddelanden. Därför är alla anmälningar begränsade till 4 KB.

   Om du vill skicka VOIP-meddelanden får du inte överskrida storleksgränsen för nyttolast på 4 kB.

## <a name="next-steps"></a>Nästa steg

Mer information finns här på följande länkar:

- [Dokumentation `apns-topic` för `apns-push-type` och rubriker och värden, inklusive särskilda ärenden för VOIP-meddelanden](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentation för storleksgräns för nyttolast](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Uppdateringar av meddelandehubbar för iOS 13](push-notification-updates-ios-13.md#apns-push-type).
