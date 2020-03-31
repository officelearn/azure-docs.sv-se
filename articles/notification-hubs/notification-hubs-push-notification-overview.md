---
title: Vad är Azure Notification Hubs?
description: Lär dig hur du lägger till funktioner för push-meddelanden med Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f5b59d1669d89c73b93199d1f833da149003d399
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159344"
---
# <a name="what-is-azure-notification-hubs"></a>Vad är Azure Notification Hubs?

Azure Notification Hubs ger en lättanvänd och utskalad push-motor som gör att du kan skicka meddelanden till valfri plattform (iOS, Android, Windows, etc.) från alla backend (moln eller lokalt). Notification Hubs fungerar utmärkt för både företags- och konsumentscenarier. Här följer några exempel:

- Skicka meddelanden med senaste nytt till miljontals användare med låg latens.
- Skicka platsbaserade kuponger till intresserade användarsegment.
- Skicka händelserelaterade meddelanden till användare eller grupper med media-/sport-/ekonomi-/spelprogram.
- Skicka innehåll med erbjudanden till program engagera och marknadsföra gentemot kunder.
- Meddela användare av företagshändelser som nya meddelanden och arbetsobjekt.
- Skicka koder för multifaktorautentisering.

## <a name="what-are-push-notifications"></a>Vad är push-meddelanden?

Push-meddelanden är en form av app-till-användare-kommunikation där användare av mobilappar meddelas om viss önskad information, vanligtvis i ett popup-fönster eller en dialogruta på en mobil enhet. Användare väljer vanligtvis att visa eller avvisa meddelandet. om du väljer den förstnämnda öppnas den mobilapplikation som meddelade meddelandet. Vissa meddelanden är tysta - levereras bakom kulisserna för att appen ska bearbeta och bestämma vad du ska göra.

Push-meddelanden är viktiga för konsumentapparna när det gäller att öka engagemanget och användningen, och för företagsapparna när det gäller att kommunicera uppdaterad affärsinformation. Det är den bästa app-till-användare-kommunikationen eftersom den är energieffektiv för mobila enheter, flexibel för avsändare av meddelanden och tillgänglig när motsvarande program inte är aktiva.

> [!NOTE]
> Azure Notification Hubs stöder inte voip-push-meddelanden (Voice Over Internet Protocol). I [den här artikeln beskrivs dock hur du kan använda APNS VOIP-meddelanden](voip-apns.md) via Azure Notification Hubs.

Mer information om push-meddelanden för några populära plattformar finns i följande avsnitt:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Hur fungerar push-meddelanden?

Push-meddelanden levereras via plattformsspecifika infrastrukturer som kallas för *plattformsspecifika meddelandesystem* (Platform Notification Systems, PNS). De erbjuder grundläggande push-funktioner för att leverera ett meddelande till en enhet med ett medföljande handtag och har inget gemensamt gränssnitt. Om du vill skicka ett meddelande till alla kunder i Android-, iOS- och Windows-versionerna av en app måste utvecklaren arbeta separat med Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) och Windows Notification Service (WNS).

På hög nivå fungerar push-tekniken så här:

1. Ett program vill få ett meddelande, så det kontaktar PNS för målplattformen där appen körs och begär en unik och tillfällig push-handtag. Referenstypen beror på systemet (till exempel använder WNS URIs medan APNS använder token).
2. Klientappen lagrar den här referensen i appens backend eller-leverantör.
3. Om du vill skicka ett push-meddelande kontaktar appen pns med hjälp av handtaget för att rikta in sig på en viss klientapp.
4. PNS-systemet vidarebefordrar meddelandet till den enhet som anges av handtaget.

![Arbetsflöde för push-meddelanden](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Utmaningarna med push-meddelanden

PNS:er är kraftfulla. Det krävs ändå mycket arbete av apputvecklaren för att implementera även de mest grundläggande scenarierna för push-meddelanden, som att sända push-meddelanden till olika användarsegment.

Att skicka push-meddelanden kräver komplex infrastruktur som inte är relaterad till programmets huvudsakliga affärslogik. Några av infrastrukturutmaningarna är:

- **Plattformsberoende**
  - Backend kräver komplex och svår underhållen plattformsberoende logik för att skicka meddelanden till enheter på olika plattformar, eftersom PNSes inte är enhetliga.
- **Skala**
  - Enligt PNS-riktlinjerna måste enhetstoken uppdateras vid varje appstart. Den backend behandlar en stor mängd trafik och databasåtkomst bara för att hålla token uppdaterade. När antalet enheter växer till hundratals, tusentals eller miljoner är kostnaden för att skapa och underhålla denna infrastruktur enorm.
  - Merparten av PNS-systemen stöder inte sändning av meddelanden till flera enheter. En enkel sändning till en miljon enheter resulterar i en miljon anrop till PNS-systemen. Att skala den här mängden trafik med minimal svarstid är en intrikat uppgift.
- **Routning**
  - Även om PNSes är ett sätt att skicka meddelanden till enheter, är de flesta appmeddelanden inriktade på användare eller intressegrupper. Den bakrend måste ha ett register för att associera enheter med intressegrupper, användare, egenskaper, etc. Denna omkostnader ökar tiden till marknads- och underhållskostnader för en app.

## <a name="why-use-azure-notification-hubs"></a>Varför ska man använda Azure Notification Hubs?

Meddelandehubbar eliminerar alla komplexiteter som är förknippade med att skicka push-meddelanden på egen hand från appens backend. Dess utskalade multiplattform för en push-meddelandeinfrastruktur reducerar den push-relaterade kodningen och förenklar serverdelen. Med Notification Hubs är enheterna enbart ansvariga för att registrera sina PNS-handtag hos en hubb, medan serverdelen skickar meddelanden till användare eller intressegrupper, så som visas på följande bild:

![Meddelandehubbsdiagram](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs är din färdiga push-motor med följande fördelar:

- **Plattformsoberoende**
  - Stöd för alla större push-plattformar.
  - Ett gemensamt gränssnitt för alla plattformar i plattformsspecifika eller plattformsoberoende format med icke plattformsspecifikt arbete.
  - Hantering av enhetshandtag på ett och samma ställe.
- **Serverdelsoberoende**
  - Moln eller lokalt.
  - .NET, Node.js, Java, Python, etc.
- **Stor uppsättning av leveransmönster**
  - Sändning till en eller flera plattformar: Du kan omedelbart sända till miljontals enheter på olika plattformar med ett enda API-anrop.
  - Skicka till enhet: du kan rikta meddelanden till enskilda enheter.
  - Push till användare: Taggar och mallar hjälper dig att nå alla plattformsoberoende enheter för en användare.
  - Tryck till segment med dynamiska taggar: Taggarfunktionen hjälper dig att segmentera enheter och skicka till dem efter dina behov, oavsett om du skickar till ett segment eller ett uttryck för segment (Till exempel aktiv och bor i Seattle INTE ny användare). I stället för att vara begränsad till publicering-prenumerera, kan du uppdatera enhetstaggar var som helst och när som helst.
  - Lokaliserad push: Mallfunktionen hjälper till att uppnå lokalisering utan att påverka backend-koden.
  - Tyst push: du kan aktivera push-pull-mönster genom att skicka tysta meddelanden till enheter och utlösa dem när du vill slutföra vissa hämtningar eller åtgärder.
  - Schemalagd push: Du kan schemalägga meddelanden som ska skickas när som helst.
  - Direkt push: du kan hoppa över att registrera enheter med Notification Hubs-tjänsten och direkt batchpusha till en lista över enhetshandtag.
  - Anpassad push: Enhetsfushvariabler hjälper dig att skicka enhetsspecifika anpassade push-meddelanden med anpassade nyckelvärdespar.
- **Omfattande telemetri**
  - Allmän push-, enhets-, fel- och åtgärdstelemetri är tillgängliga både i Azure-portalen och programmässigt.
  - Telemetri per meddelande spårar varje push från ditt första begärandeanrop till tjänsten Notification Hubs som har skickat push-meddelandena.
  - Feedback från plattformsmeddelanden kommunicerar all feedback från PNSes för att hjälpa till med felsökning.
- **Skalbarhet**
  - Skicka snabba meddelanden till miljontals enheter utan att skapa om eller skapa enheter.
- **Säkerhet**
  - SAS (Shared Access Secret) eller federerad autentisering.

## <a name="next-steps"></a>Nästa steg

Kom igång med att skapa och använda en meddelandehubb genom att följa [Självstudier: Skicka meddelanden till mobila program](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
