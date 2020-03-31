---
title: Vanliga frågor och svar
description: Vanliga frågor om azure spatialankar tjänsten.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844895"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Vanliga frågor om Azure Spatial Anchors

Azure Spatial Anchors är en hanterad molntjänst och utvecklarplattform som möjliggör rumsligt medvetna upplevelser med mixad verklighet med flera användare på HoloLens-, iOS- och Android-enheter.

Mer information finns i [Översikt över Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Vanliga frågor och svar om Azure Spatial Anchors-produkter

**F: Vilka enheter stöder Azure Spatial Anchors?**

**A.** Azure Spatial Anchors gör det möjligt för utvecklare att skapa appar på HoloLens, på iOS-enheter med ARKit-stöd och på Android-enheter med ARCore-stöd. för iOS och Android inkluderar detta både telefoner och surfplattor.

**F: Måste jag vara ansluten till molnet för att kunna använda Azure Spatial Anchors?**

**A.** Azure Spatial Anchors kräver för närvarande en nätverksanslutning till Internet. Vi tar gärna emot dina kommentarer på vår [feedbackwebbplats](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**F: Vilka är anslutningskraven för Azure Spatial Anchors?**

**A.** Azure Spatial Anchors fungerar med Wi-Fi och mobila bredbandsanslutningar.

**F: Hur exakt kan Azure Spatial Anchors hitta ankare?**

**A.** Många faktorer påverkar noggrannheten i att lokalisera ankare - ljusförhållanden, objekt i miljön, och även den yta där ankaret är placerad. För att avgöra om precisionen kommer att uppfylla dina behov kan du prova fästpunkterna i miljöer som liknar dem som du planerar att använda fästpunkterna i. Om du stöter på miljöer där precisionen inte uppfyller dina behov kan du läsa avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

**F: Hur lång tid tar det att skapa och hitta ankare?**

**A.** Den tid som krävs för att skapa och hitta ankare beror på många faktorer - nätverksanslutning, enhetens bearbetning och belastning och den specifika miljön. Vi har kunder som skapar program i många branscher, till exempel tillverkning, detaljhandel och spel, som uppger att tjänsten ger en mycket bra användarupplevelse för deras scenarier.

## <a name="privacy-faq"></a>Vanliga frågor och svar om sekretess

**F: När mitt program placerar ett spatialt ankare någonstans har alla appar tillgång till det?**

**A.** Fästpunkter är isolerade av Azure-konto. Endast appar som du ger åtkomst till ditt konto får åtkomst till fästpunkter i kontot.

**F: Vilken information om en miljö överförs och lagras på tjänsten när du använder Azure Spatial Anchors? Överförs och lagras bilder av miljön?**

**S**: När du skapar eller lokaliserar ankare bearbetas bilder av miljön på enheten till ett härlett format. Det här härledda formatet skickas till och lagras i tjänsten.

För att ge transparens visas nedan en bild av miljön och det härledda molnet med glesa punkter. Punktmolnet visar den geometriska representationen av den miljö som överförs och lagras i tjänsten. För varje punkt i molnet med glesa punkter överför och lagrar vi en hash av den punktens visuella egenskaper. Hashen härleds från, men innehåller inte några pixeldata.

Azure Spatial Anchors följer [villkoren i serviceavtalet för Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) och [Microsofts sekretesspolicy](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![En miljö och dess härledda glesa](./media/sparse-point-cloud.png)
punktmoln Bild*1: En miljö och dess härledda glesa punktmoln*


**F: Finns det något sätt att skicka diagnostikinformation till Microsoft?**

**A**: Ja. Azure Spatial Anchors har ett diagnostikläge som utvecklare kan välja att använda via Azure Spatial Anchors API. Det här är användbart om du till exempel påträffar en miljö där du inte kan skapa och hitta fästpunkter på ett förutsägbart sätt. Vi kan komma att fråga om du kan skicka en diagnostikrapport som innehåller information som kan hjälpa oss att felsöka. Mer information finns i avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Vanliga frågor och svar om tillgänglighet och prissättning

**F: Tillhandahåller du ett serviceavtal?**

**A.** Som standard för Azure-tjänster riktar vi in oss på en tillgänglighet som är större än 99,9 %. Observera att Azure Spatial Anchors för närvarande är i förhandsversion och att [tilläggsvillkoren för förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) därför gäller.

**F: Kan jag publicera mina appar med Azure Spatial Anchors i appbutiker? Kan jag använda Azure Spatial Anchors för verksamhetskritiska produktionsscenarier?**

**A.** Azure Spatial Anchors är för närvarande i förhandsversion och under denna tid inbjuder vi dig att utveckla appar, [ge feedback](https://feedback.azure.com/forums/919252-azure-spatial-anchors) om produkten och planera för dina produktionsdistributioner.

Datum för allmän tillgänglighet (GA) tillkännages snart.

**F: Har du några begränsningsgränser på plats?**

**A:** Ja, vi har begränsningsgränser.  Vi förväntar oss inte att du uppnår dem vid typisk utveckling och testning av program. För produktionsdistributioner kan vi ge stöd till våra kunders krav för hög skala. [Kontakta oss](mailto:azuremrs@microsoft.com) om du vill diskutera det. Under den här förhandsversionsfasen har vi ännu inte publicerat vår struktur för nivåer och prissättning, men vi planerar att göra det snart.

**F: I vilka regioner är Azure Spatial Anchors tillgängliga?**

**A.** Du kan skapa ett Azure Spatial Anchors-konto idag i Azure East US 2-regionen. Det innebär att både beräkning och lagring som driver den här tjänsten finns i den här regionen. Dock finns det inga begränsningar för var klienterna kan finnas. I framtiden kommer att vi utöka regional tillgänglighet för tjänsten till alla primära Azure-regioner.

**F: Debiterar du för Azure Spatial Anchors? Kommer du någonsin att ta betalt?**

**A.** Du hittar information om priser under förhandsversionen på vår [prissida](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Vanliga frågor och svar om teknik

**F: Hur fungerar Azure Spatial Anchors?**

**A.** Azure Spatial Anchors är beroende av mixade verklighet / augmented reality trackers. Dessa spårare ser miljön med kameror och spårar enheten i med sex frihetsgrader (6DoF) när den rör sig i miljön.

Med en 6DoF-spårare som byggblock kan du med Azure Spatial Anchors ange särskilda platser av intresse i den verkliga miljön som ”fästpunkter”. Du kan till exempel använda en fästpunkt för att rendera innehåll på en specifik plats i verkligheten.

När du skapar en fästpunkt registrerar klient-SDK miljöinformation runt den punkten och skickar den till tjänsten. Om en annan enhet söker efter fästpunkten i samma utrymme överförs liknande data till tjänsten. Dessa data matchas mot miljödata som lagrats tidigare. Positionen för fästpunkten i förhållande till enheten skickas sedan tillbaka för användning i programmet.

**F: Hur integrerar Azure Spatial Anchors med ARKit och ARCore på iOS och Android?**

**A.** Azure Spatial Anchors utnyttjar de inbyggda spårningsfunktionerna i ARKit och ARCore. Dessutom ger våra SDK:er för iOS och Android funktioner såsom bevarande av fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att helt enkelt ansluta till tjänsten.

**F: Hur integreras Azure Spatial Anchors med HoloLens?**

**A.** Azure Spatial Anchors utnyttjar hololens inbyggda spårningsfunktioner. Vi tillhandahåller en Azure Spatial Anchors-SDK för skapande av appar på HoloLens. SDK:n integreras med inbyggda HoloLens-funktioner och tillhandahåller ytterligare funktioner. De här funktionerna omfattar tillåtelse för apputvecklare att bevara fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att ansluta till tjänsten.

**F: Vilka plattformar och språk stöder Azure Spatial Anchors?**

**A.** Utvecklare kan skapa appar med Azure Spatial Anchors med välbekanta verktyg och ramverk för sin enhet:

- Unity över HoloLens, iOS och Android
- Xamarin på iOS och Android
- Swift eller Objective-C i iOS
- Java eller Android NDK i Android
- C++/WinRT på HoloLens

Kom igång med [utveckling här](index.yml).

**F: Fungerar det med Unreal?**

**A.** Stöd till Unreal kommer att övervägas i framtiden.

**F: Vilka portar och protokoll använder Azure Spatial Anchors?**

**A.** Azure Spatial Anchors kommunicerar via TCP-port 443 med hjälp av ett krypterat protokoll. För autentisering använder den [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), som kommunicerar med HTTPS över port 443.
