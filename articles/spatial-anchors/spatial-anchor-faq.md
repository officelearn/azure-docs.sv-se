---
title: Vanliga frågor om Azure Spatial Anchors | Microsoft Docs
description: Azure Spatial Anchors är en hanterad molntjänst och utvecklarplattform som möjliggör upplevelser med mixad verklighet på flera enheter och med flera användare på HoloLens-, iOS- och Android-enheter. De här vanliga frågorna och svaren behandlar tjänsten ur en teknisk synvinkel.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: d38f7a22d90c62b245e49e6bdb0a3041c4f1023f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303303"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Vanliga frågor om Azure Spatial Anchors

Azure Spatial Anchors är en hanterad molntjänst och utvecklarplattform som möjliggör spatialt medvetna upplevelser med mixad verklighet med flera användare på HoloLens-, iOS- och Android-enheter.

Mer information finns i [Översikt över Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Vanliga frågor och svar om Azure Spatial Anchors-produkter

**F: Vilka enheter har Azure Spatial Anchors stöd för?**

**S:** Med Azure Spatial Anchors kan utvecklare skapa appar på HoloLens, iOS-enheter med stöd för ARKit samt Android-enheter med stöd för ARCore. För iOS och Android omfattar detta både telefoner och surfplattor.

**F: Måste jag ha anslutning till molnet för att använda Azure Spatial Anchors?**

**S:** För närvarande kräver Azure Spatial Anchors en nätverksanslutning till Internet. Vi tar gärna emot dina kommentarer på vår [feedbackwebbplats](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**F: Vad är anslutningskraven för Azure Spatial Anchors?**

**S:** Azure Spatial Anchors fungerar med Wi-Fi och anslutningar med mobilt bredband.

**F: Hur korrekt hittar Azure Spatial Anchors fästpunkter?**

**S:** Många faktorer påverkar precisionen med att hitta fästpunkter – belysning, föremål i miljön och även den yta som fästpunkten är placerad på. För att avgöra om precisionen kommer att uppfylla dina behov kan du prova fästpunkterna i miljöer som liknar dem som du planerar att använda fästpunkterna i. Om du stöter på miljöer där precisionen inte uppfyller dina behov kan du läsa avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

**F: Hur lång tid tar det att skapa och hitta fästpunkter?**

**S:** Den tid som krävs för att skapa och hitta fästpunkter beror på många faktorer – nätverksanslutning, enhetens bearbetning och belastning samt den aktuella miljön. Vi har kunder som skapar program i många branscher, till exempel tillverkning, detaljhandel och spel, som uppger att tjänsten ger en mycket bra användarupplevelse för deras scenarier.

## <a name="privacy-faq"></a>Vanliga frågor och svar om sekretess

**F: Får alla mina appar åtkomst till en spatial fästpunkt när mitt program placerar en sådan någonstans?**

**S:** Fästpunkter isoleras per Azure-konto. Endast appar som du ger åtkomst till ditt konto får åtkomst till fästpunkter i kontot.

**F: Vilken information om en miljö överförs och lagras på tjänsten vid användning av Azure Spatial Anchors? Överförs och lagras bilder på miljön?**

**S**: När fästpunkter skapas eller hittas bearbetas bilder på miljön på enheten i ett härlett format. Det här härledda formatet skickas till och lagras i tjänsten.

För att ge transparens visas nedan en bild av miljön och det härledda molnet med glesa punkter. Punktmolnet visar den geometriska representationen av den miljö som överförs och lagras i tjänsten. För varje punkt i molnet med glesa punkter överför och lagrar vi en hash av den punktens visuella egenskaper. Hashen härleds från, men innehåller inte några pixeldata.

Azure Spatial Anchors följer [villkoren i serviceavtalet för Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) och [Microsofts sekretesspolicy](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![En miljö och dess härledda moln med glesa punkter](./media/sparce-point-cloud.png)
*Bild 1: En miljö och dess härledda moln med glesa punkter*


**F: Kan jag på något sätt skicka diagnostikinformation till Microsoft?**

**S**: Ja. Azure Spatial Anchors har ett diagnostikläge som utvecklare kan välja att använda via Azure Spatial Anchors API. Det här är användbart om du till exempel påträffar en miljö där du inte kan skapa och hitta fästpunkter på ett förutsägbart sätt. Vi kan komma att fråga om du kan skicka en diagnostikrapport som innehåller information som kan hjälpa oss att felsöka. Mer information finns i avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Vanliga frågor och svar om tillgänglighet och prissättning

**F: Tillhandahåller ni ett serviceavtal?**

**S:** Enligt vad som är standard för Azure-tjänster har vi en tillgänglighet som är större än 99,9 % som mål. Observera att Azure Spatial Anchors för närvarande är i förhandsversion och att [tilläggsvillkoren för förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) därför gäller.

**F: Kan jag publicera min appar till appbutiker med hjälp av Azure Spatial Anchors? Får jag använda Azure Spatial Anchors för verksamhetskritiska produktionsscenarier?**

**S:** Azure Spatial Anchors är för närvarande i förhandsversion, och under den här perioden kan du utveckla appar, [ge feedback](https://feedback.azure.com/forums/919252-azure-spatial-anchors) om produkten och planera inför produktionsdistributioner.

Datum för allmän tillgänglighet (GA) tillkännages snart.

**F: Finns det några kapacitetsbegränsningar?**
 
**S**: Ja, det förekommer kapacitetsbegränsningar.  Vi förväntar oss inte att du uppnår dem vid typisk utveckling och testning av program. För produktionsdistributioner kan vi ge stöd till våra kunders krav för hög skala. [Kontakta oss](mailto:azuremrs@microsoft.com) om du vill diskutera det. Under den här förhandsversionsfasen har vi ännu inte publicerat vår struktur för nivåer och prissättning, men vi planerar att göra det snart.

**F: I vilka regioner finns Azure Spatial Anchors tillgängligt?**

**S:** Du kan skapa ett Azure Spatial Anchors-konto i dag i Azure-regionen USA, östra 2. Det innebär att både beräkning och lagring som driver den här tjänsten finns i den här regionen. Dock finns det inga begränsningar för var klienterna kan finnas. I framtiden kommer att vi utöka regional tillgänglighet för tjänsten till alla primära Azure-regioner.

**F: Kostar Azure Spatial Anchors att använda? Kommer det någon att kosta?**

**S:** Information om priser under förhandsversionen finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Vanliga frågor och svar om teknik

**F: Hur fungerar Azure Spatial Anchors?**

**S:** Azure Spatial Anchors använder spårare för mixad verklighet/förstärkt verklighet. Dessa spårare ser miljön med kameror och spårar enheten i med sex frihetsgrader (6DoF) när den rör sig i miljön.

Med en 6DoF-spårare som byggblock kan du med Azure Spatial Anchors ange särskilda platser av intresse i den verkliga miljön som ”fästpunkter”. Du kan till exempel använda en fästpunkt för att rendera innehåll på en specifik plats i verkligheten.

När du skapar en fästpunkt registrerar klient-SDK miljöinformation runt den punkten och skickar den till tjänsten. Om en annan enhet söker efter fästpunkten i samma utrymme överförs liknande data till tjänsten. Dessa data matchas mot miljödata som lagrats tidigare. Positionen för fästpunkten i förhållande till enheten skickas sedan tillbaka för användning i programmet.

**F: Hur integreras Azure Spatial Anchors med ARKit och ARCore i iOS och Android?**

**S:** Azure Spatial Anchors utnyttjar de inbyggda funktionerna för spårning i ARKit och ARCore. Dessutom ger våra SDK:er för iOS och Android funktioner såsom bevarande av fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att helt enkelt ansluta till tjänsten.

**F: Hur integreras Azure Spatial Anchors med HoloLens?**

**S:** Azure Spatial Anchors utnyttjar de inbyggda funktionerna för spårning i HoloLens. Vi tillhandahåller en Azure Spatial Anchors-SDK för skapande av appar på HoloLens. SDK:n integreras med inbyggda HoloLens-funktioner och tillhandahåller ytterligare funktioner. De här funktionerna omfattar tillåtelse för apputvecklare att bevara fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att ansluta till tjänsten.

**F: Vilka plattformar och språk har Azure Spatial Anchors stöd för?**

**S:** Utvecklare kan skapa appar med Azure Spatial Anchors med hjälp av välbekanta verktyg och ramverk för sin enhet:

- Unity över HoloLens, iOS och Android
- Swift eller Objective-C i iOS
- Java eller Android NDK i Android
- C++/WinRT på HoloLens

Kom igång med [utveckling här](index.yml).

**F: Fungerar det med Unreal?**

**S:** Vi räknar med att ha stöd för Unreal snart.

**F: Fungerar det med Xamarin?**

**S:** Ja. Vi tillhandahåller inte någon Xamarin-SDK, men vi förväntar oss att utvecklare kan använda Azure Spatial Anchors i sina Xamarin-appar genom att integrera med Azure Spatial Anchors API.
