---
title: Vanliga frågor och svar om Text till tal-tjänst i Azure
titleSuffix: Azure Cognitive Services
description: Få svar på de mest populära frågorna om Text till tal-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 9b8340221c61bedbe010b9ef280ada62cfee7a21
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754886"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Vanliga och frågor svar om text till tal

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du kolla [andra supportalternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en standard voice-modell och en anpassad voice-modell?**

**A**: Standard voice-modellen (kallas även en *rösttyp*) har tränats med hjälp av data som ägs av Microsoft och redan har distribuerats i molnet. Du kan använda en anpassad voice-modell för att anpassa en genomsnittlig modell och överför timbre och uttryck av talarens röst style eller öva med en fullständig, ny modell som baseras på träningsdata som sammanställts av användaren. Fler och fler kunder vill idag, en röst för en av en typ, företagsanpassad för sina robotar. Anpassade voice-sestavuje Se platforma är det rätta valet för det valda alternativet.

**F: Hur börjar jag om jag vill använda en standard voice-modell?**

**A**: Mer än 80 standard röst modeller i över 45 språk är tillgängliga via HTTP-begäranden. Hämta först en [prenumerationsnyckel](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). REST-anrop till predeployed voice-modeller finns det [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**F: Om jag vill använda en röstmeddelanden modell är API: et samma som det som används för att få en standard?**

**A**: När en anpassad voice-modell skapas och distribueras, kan du få en unik slutpunkt för din modell. Om du vill använda röst för att tala i dina appar, måste du ange slutpunkten i HTTP-förfrågningar. På samma sätt som är tillgänglig i REST-API för Text till tal-tjänsten är tillgänglig för din anpassade slutpunkt. Lär dig hur du [skapar och använder din anpassade slutpunkt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**F: Behöver jag förbereda träningsdata att skapa anpassade röst modeller på egen hand?**

**A**: Ja, du måste förbereda träningsdata själv för en anpassad voice-modell.

En samling taldata krävs för att skapa en modell i röstmeddelanden. Den här samlingen består av en uppsättning ljudfiler av tal inspelningar och en textfil för utskrift av varje ljudfil. Resultatet av din digitala röst beroende kraftigt kvaliteten på dina utbildningsdata. Det är viktigt att inspelningarna görs i en tyst rum med hög kvalitet, stående mikrofon för att skapa en bra rösten. En konsekvent volym talar hastighet och talar försäljningsargument och även konsekvens i lättfattliga maner, ovanor tal är behövs för att skapa en fantastisk digital röst. Vi rekommenderar starkt att spela in rösterna i en inspelning studio.

Vi inte för närvarande kan stödja online inspelning eller några rekommendationer för inspelning studio. Format-krav, se [hur du förbereder inspelningar och betyg](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**F: Vilka skript ska jag använda för att registrera taldata för anpassade ton?**

**A**: Vi begränsar inte skript för röstinspelning. Du kan använda dina egna skript för att registrera tal. Se bara till att du har tillräckligt med fonetisk täckning i ditt tal. Du kan börja med en liten mängd taldata, vilket kan vara 50 olika meningar (cirka 3-5 minuter tal) för att träna en anpassad röst. Ju mer data som du anger, desto mer naturliga rösten. Du kan börja att träna en fullständig rösttyp när du anger inspelningar av mer än 2 000 meningar (cirka 3 – 4 timmar tal). Om du vill ha en hög kvalitet, fullständig röst, måste du förbereda inspelningar av mer än 6 000 meningar (cirka 8 – 10 timmar tal).

Vi tillhandahåller ytterligare tjänster för att hjälpa dig att förbereda skript för inspelning. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för frågor.

**F: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som är tillgängligt i portalen?**

**A**: Du kan skala upp din modell i steg om 20 samtidiga begäranden. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för frågor om högre skalning.

**F: Kan jag hämta Mina modellen och köra den lokalt?**

**A**: Modeller kan inte hämtas och köras lokalt.

**F: Är Mina begäranden begränsas?**

**A**: REST API begränsar begäranden till 25 per 5 sekunder. Information finns i vår sidor för [Text till tal](text-to-speech.md). 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
