---
title: Azure Notification Hubs
description: Lär dig hur du lägger till funktioner för push-meddelanden med Azure Notification Hubs.
author: ysxu
manager: erikre
editor: ''
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: ea8d569a8d51afee352860ab60a1fe311496b83c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-notification-hubs"></a>Azure Notification Hubs
## <a name="overview"></a>Översikt
Azure Notification Hubs ger dig ett enkelt att använda flera plattformar, skalats ut push-motorn. Med en enda plattformsoberoende API-anrop som skicka du enkelt riktade och anpassade push-meddelanden till alla mobila plattformar från alla serverdelar för molnet eller lokalt.

Notification Hubs fungerar bra för enterprise-och konsumentscenarier. Här följer några exempel som kunder använder Notification Hubs för:

* Skicka meddelanden för senaste nyheterna till miljoner användare med låg latens.
* Skicka platsbaserade kuponger till användarsegment intresserad av.
* Skicka händelse-relaterade meddelanden till användare eller grupper för media/sport/ekonomi/spelappar.
* Skicka erbjudanden innehållet till appar engagera och marknadsföra till kunder.
* Meddela användare om företagshändelser, till exempel nya meddelanden och arbetsobjekt.
* Skicka koder för multifaktorautentisering.

## <a name="what-are-push-notifications"></a>Vad är push-meddelanden?
Push-meddelanden är en typ av app-till-användare kommunikation där användare av mobila appar meddelas av vissa önskad information, vanligtvis i ett popup-fönster eller dialogruta. Användare kan vanligtvis välja att visa eller stänga meddelandet och välja den förra öppnas mobilappen som har kommunicerat meddelandet.

Push-meddelanden är mycket viktigt för konsumentappar öka användarna och användning och företagsappar vid kommunikationen uppdaterade affärsinformation. Det är bäst app-till-användare kommunikation eftersom den är energi sparas för mobila enheter, flexibla för meddelanden-avsändare och är tillgängliga när motsvarande appar inte är aktiva.

Mer information om push-meddelanden för några populära plattformar:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Så här fungerar push-meddelandena
Push-meddelanden levereras via plattformsspecifika infrastrukturer som kallas *plattformsspecifika meddelandesystem* (PNSes). De erbjuder barebone push-funktioner för leverans av meddelande till en enhet med en angiven hanteras och har inga gemensamt gränssnitt. Om du vill skicka ett meddelande till alla kunder över iOS, Android och Windows måste versioner av en app, utvecklaren arbeta med APNS (Apple Push Notification Service), FCM (Firebase Cloud Messaging) och WNS (Windows Notification Service) när skickar batchbearbetning.

På en hög nivå fungerar här så push:

1. Klientappen beslutar den vill ta emot push-meddelanden därför kontaktar motsvarande pns-systemet för att hämta dess unika och tillfällig push-referensen. Handtagstypen varierar beroende på systemet (t.ex. WNS har URI: er när APN har token).
2. Klientappen lagrar detta handtag i appens serverdel eller leverantören.
3. Om du vill skicka ett push-meddelande kontaktar appens serverdel pns-systemet genom att använda handtaget välja en viss klient-app.
4. PNS-systemet vidarebefordrar meddelandet till den enhet som anges av handtaget.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Utmaningarna med push-meddelanden
PNSes är kraftfulla, lämna de mycket arbete av apputvecklaren för att implementera även vanliga push notification scenarier, till exempel sända eller skicka push-meddelanden till segmenterade användare.

Push är ett av de mest efterfrågade funktionerna i mobila molntjänster, eftersom dess fungerande kräver komplexa infrastrukturer som är relaterade till appens huvudsakliga affärslogiken. Några av de infrastrukturella utmaningarna är:

* **Plattformsberoende**: 

  * Serverdelen måste ha komplexa och svårt att underhålla plattformsberoende logik för att skicka meddelanden till enheter på olika plattformar som PNSes inte finns unified.
* **Skala**:

  * Enligt PNS-riktlinjerna måste enhetstoken uppdateras vid varje appen startas. Det innebär att serverdelen gäller en stor mängd trafik och databasen åtkomst bara för att hålla token uppdaterade. När antalet enheter växer hundratals och tusentals miljoner är kostnaden för att skapa och underhålla infrastrukturen omfattande.
  * De flesta PNSes stöder inte sändning till flera enheter. Detta innebär en enkel sändning till en miljoner enheter resulterar i en miljon anrop till PNSes. Den här mängden trafik skalning med minimal svarstid är apputvecklarna.
* **Routning**:
  
  * Även om PNSes ger dig ett sätt att skicka meddelanden till enheter, riktas meddelanden för de flesta appar användare eller intressegrupper. Det innebär att serverdelen måste upprätthålla ett register för att koppla enheter till intressegrupper, användare, egenskaper och så vidare. Det här arbetet lägger till tid till marknaden och underhåll kostnader för en app.

## <a name="why-use-notification-hubs"></a>Varför bör du använda Notification Hubs?
Notification Hubs eliminerar alla svårigheter som är associerade med att aktivera push din egen. Dess flera plattformar, skalats ut push-infrastruktur minskar push-relaterade koder och förenklar din serverdel. Med Notification Hubs är enheter bara ansvarar för att registrera sina PNS-handtag med ett nav, medan serverdelen skickar meddelanden till användare eller intressegrupper, enligt följande bild:

![][1]

Meddelandehubbar är färdiga att använda push-motorn har följande fördelar:

* **Mellan olika plattformar**

  * Stöd för alla större push-plattformar, inklusive iOS, Android, Windows, och Kindle och Baidu.
  * Ett gemensamt gränssnitt till push för alla plattformar i plattformsspecifika eller plattformsoberoende format med inga plattformsspecifika arbete.
  * Enheten hantera på ett ställe.
* **Mellan serverdelar**
  
  * Molnet eller lokalt
  * .NET, Node.js, Java och så vidare.
* **Stor uppsättning av leveransmönster**:

  * *Sändning till en eller flera plattformar*: du kan direkt sändning till miljoner enheter på plattformar med ett enda API-anrop.
  * *Push-installera enheten*: du kan rikta meddelanden till enskilda enheter.
  * *Push till användaren*: taggar och mallar funktionerna kan du nå alla plattformar enheter av en användare.
  * *Push-segmentet med dynamiska taggar*: taggfunktionen hjälper dig att segment enheter och push till dem efter dina behov om du skickar till ett segment eller ett uttryck med segment (t.ex. active och liv i Seattle inte nya användare). I stället för att vara begränsade till pub-sub, kan du uppdatera enheten taggar var som helst och när som helst.
  * *Lokaliserade push*: mallfunktionen kan uppnå lokalisering utan att detta påverkar serverdelskoden.
  * *Tyst push*: möjliggör push-pull-mönster genom att skicka tyst meddelanden till enheter och utlösa dem för att slutföra vissa hämtar eller åtgärder.
  * *Schemalagda push*: du kan schemalägga för att skicka meddelanden när som helst.
  * *Direct push*: du kan hoppa över registrera enheter med vår tjänst och direkt batch push till en lista över enhetshandtag.
  * *Anpassade push*: enheten push-variabler kan du skicka enhetsspecifika personliga push-meddelanden med anpassade nyckel-värdepar.
* **Effektiv telemetri**
  
  * Allmän push, enhet, fel och åtgärden telemetri är tillgängligt i Azure-portalen och genom programmering.
  * Spårar varje push per meddelande telemetri från första begärandeanrop till vår tjänst har batchbearbetning push-meddelanden ut.
  * Platform Notification System Feedback kommunicerar alla feedback från Platform Notification Systems hjälp vid felsökning.
* **Skalbarhet** 
  
  * Skicka snabb meddelanden till miljontals enheter utan att behöva bygga om eller enhet horisontell partitionering.
* **Säkerhet**

  * Delad hemlighet av åtkomst (SAS) eller federerad autentisering.

## <a name="integration-with-app-service-mobile-apps"></a>Integrering med Mobilappar i Apptjänst
För att underlätta för en sömlös och enhetlig användarupplevelse av alla Azure-tjänster har [App Service Mobile Apps] inbyggt stöd för push-meddelanden som skickas via Notification Hubs. [App Service Mobile Apps] är en mycket skalbar, globalt tillgänglig plattform för mobilappsutveckling tänkt att användas av utvecklare av företagsprogram och systemintegrerare. Den ger mobilutvecklare tillgång till ett stort utbud av funktioner.

Utvecklare av mobilappar kan utnyttja Notification Hubs med följande arbetsflöde:

1. Hämta PNS-handtag för enheter
2. Registrera enheten med Notification Hub via lämplig Mobile Apps klient-SDK registrera API
   * Observera att Mobile Apps av säkerhetsskäl tar bort alla taggar vid registreringen. Arbeta med Notification Hubs direkt från din serverdel för att associera taggar med enheter.
3. Skicka meddelanden från din apps serverdel med Notification Hubs

Här är några av de fördelar som utvecklarna får tack vare den här integreringen:

* **Mobile Apps klient-SDK:**: dessa SDK: er med flera plattformar ger enkla API: er för registrering och tala med den meddelandehubb som har länkats till mobilappen automatiskt. Utvecklare behöver inte gräva djupt bland autentiseringsuppgifterna för Notification Hubs eller arbeta med en ytterligare tjänst.

  * *Push till användaren*: I SDK: er automatiskt taggar en given enhet med Mobile Apps autentiserat användar-ID att aktivera Användarscenario-push.
  * *Push-installera enheten*: I SDK: erna använder automatiskt installations-ID för Mobile Apps som GUID för att registrera med Notification Hubs, spara utvecklarna slipper upprätthålla flera olika GUID-tjänster.
* **Installationsmodell**: Mobile Apps fungerar med Notification Hubs senaste push-modell för att representera alla push-egenskaper som är kopplade till en enhet i en JSON-Installation som överensstämmer med Push Notification Services och är enkel att använda.
* **Flexibilitet**: utvecklare kan alltid välja att arbeta direkt med Notification Hubs även med integration på plats.
* **Integrerad upplevelse i [Azure-portalen]**: Push som en funktion är visuellt återgiven i Mobile Apps och utvecklarna kan enkelt arbeta med den associerade meddelandehubben via Mobile Apps.

## <a name="next-steps"></a>Nästa steg
Du hittar mer information om Notification Hubs under följande ämnen:

* **[Hur kunder använder Notification Hubs]**
* **[Självstudiekurser och guider om Notification Hubs]**
* **Notification Hubs komma igång Självstudier**: [iOS], [Android], [Windows Universal], [Windows Phone], [ Kindle], [Xamarin.iOS], [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Hur kunder använder Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
[Självstudiekurser och guider om Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[ Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure-portalen]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
