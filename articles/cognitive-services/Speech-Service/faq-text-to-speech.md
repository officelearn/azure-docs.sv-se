---
title: Vanliga frågor om tal till Text-tjänst på Azure
description: Här är några svar på de mest populära frågorna om tal till Text.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282865"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Vanliga och frågor svar om text till tal

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du läsa andra supportalternativ [här](support.md).

## <a name="general"></a>Allmänt

**Fråga**: Vad är skillnaden mellan standardentiteter och anpassade voice-modeller?

**Svar**: standard röst-modeller (alias) rösttyper) har tränats med Microsoft som ägs av data och redan har distribuerats i molnet. Anpassade röst modeller gör att användarna antingen att anpassa en genomsnittlig modell och överföra timbre och uttryck sätt enligt talarens röst stil, eller för att träna en fullständig ny modell som baseras på träningsdata som sammanställts av användaren. Idag vill fler kunder ha en en av en typ, företagsanpassad ton för sina robotar. Anpassade röst att skapa plattform är det rätta valet för detta.

**Fråga**: hur börjar jag om jag vill använda en standard voice-modell?

**Svar**: mer än 80 standard röst modeller i över 45 språk är tillgängliga via HTTP-begäranden. Först måste du hämta en [prenumerationsnyckel](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Om du vill göra REST-anrop för redan distribuerade voice-modeller finns det [information här](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Fråga**: om jag vill använda en röstmeddelanden modell är API: et samma som standard röster?

**Svar**: när du har anpassade röst modellen skapas och distribueras, får du en unik slutpunkt för din modell. Du måste ange slutpunkten i din HTTP-åtgärdsbegäranden, Använd röst för att tala i dina appar. På samma sätt som är tillgängliga via REST API för text till tal-tjänst är också tillgänglig för din anpassade slutpunkt. Se hur du [skapar och använder din anpassade slutpunkt](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Fråga**: måste du förbereda träningsdata för att skapa anpassade röst modeller på egen hand?

**Svar**: du måste förbereda träningsdata själv. En samling taldata krävs för att skapa en modell i röstmeddelanden. Den här samlingen består av en uppsättning ljudfiler av tal inspelningar och en textfil för utskrift av varje ljudfil. Resultatet av din digitala röst beroende kraftigt av kvaliteten på dina utbildningsdata. För att skapa en bra text till tal-röst, är det viktigt att inspelningarna utförs i en tyst rum med hög kvalitet stående mikrofon. Konsekvent volym talar pris, talar försäljningsargument och även konsekvens i lättfattliga maner, ovanor tal är nödvändiga för att skapa en fantastisk digital röst. Vi rekommenderar starkt att du har de rösterna registreras i en inspelning studio.
För tillfället vi inte stödja online inspelning eller några rekommendationer för inspelning studio. Format-krav, se [hur du förbereder inspelningar och betyg](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Fråga**: vilka skript ska jag använda för att registrera taldata för anpassade ton? 

**Svar**: vi begränsar inte skript för röstinspelning. Du kan använda dina egna skript för att registrera tal. Se bara till du har tillräckligt med fonetisk täckning i ditt tal. För att träna en anpassad ton, som du kan börja med en liten mängd taldata, vilket kan vara 50 olika meningar (om 3-5 minuter tal). Ju mer data som du anger, desto mer naturliga rösten. Du kan börja att träna en fullständig rösttyp när du anger inspelningar av över 2 000 meningar (cirka 3 – 4 timmar tal). För att få en fullständig röst hög kvalitet, måste du förbereda inspelningar av mer än 6000 meningar (cirka 8 – 10 timmar tal).  
Vi tillhandahåller ytterligare tjänster för att hjälpa dig att förbereda skript för inspelning. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för frågor.

**Fråga**: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som är tillgängligt i portalen?

**Svar**: du kan skala upp din modell i steg om 20 samtidiga begäranden. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för förfrågningar om högre skalning.

**Fråga**: kan jag hämta Mina modellen och köra den lokalt?

**Svar**: modeller kan inte hämtas och köras lokalt.

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
