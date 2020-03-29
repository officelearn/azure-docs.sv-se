---
title: Frågor om text till tal med vanliga frågor
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om tjänsten Text till tal.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110506"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Frågor om text till tal med vanliga frågor

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du läsa [andra supportalternativ.](support.md)

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en standardröstmodell och en anpassad röstmodell?**

**S**: Standardröstmodellen (kallas även _röstteckensnitt)_ har tränats med hjälp av Microsoft-ägda data och distribueras redan i molnet. Du kan använda en anpassad röstmodell antingen för att anpassa en genomsnittlig modell och överföra klang och uttryck för högtalarens röststil eller träna en fullständig, ny modell baserat på de träningsdata som användaren har utarbetat. Idag, fler och fler kunder vill ha en one-of-a-kind, märkesvaror röst för sina robotar. Den anpassade röstbyggplattformen är det rätta valet för det alternativet.

**F: Var börjar jag om jag vill använda en standardröstmodell?**

**S:** Mer än 80 standardröstmodeller på över 45 språk är tillgängliga via HTTP-begäranden. Hämta först en [prenumerationsnyckel](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Om du vill ringa REST-anrop till de förbeställda röstmodellerna läser du [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**F: Om jag vill använda en anpassad röstmodell, är API:et detsamma som det som används för standardröster?**

**S:** När en anpassad röstmodell skapas och distribueras får du en unik slutpunkt för din modell. Om du vill använda rösten för att tala i dina appar måste du ange slutpunkten i HTTP-begäranden. Samma funktioner som är tillgängliga i REST API för tjänsten Text till tal är tillgängliga för din anpassade slutpunkt. Lär dig hur du [skapar och använder din anpassade slutpunkt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**F: Måste jag förbereda träningsdata för att skapa anpassade röstmodeller på egen hand?**

**S**: Ja, du måste förbereda träningsdata själv för en anpassad röstmodell.

En samling taldata krävs för att skapa en anpassad röstmodell. Denna samling består av en uppsättning ljudfiler av talinspelningar och en textfil av transkriptionen av varje ljudfil. Resultatet av din digitala röst är starkt beroende av kvaliteten på dina träningsdata. För att producera en bra text-till-tal röst, är det viktigt att inspelningarna görs i ett tyst rum med en hög kvalitet, stående mikrofon. En konsekvent volym, talfrekvens och talhöjd, och även konsekvens i uttrycksfulla manér tal är avgörande för att bygga en stor digital röst. Vi rekommenderar starkt att spela in rösterna i en inspelningsstudio.

För närvarande tillhandahåller vi inte onlineinspelningsstöd eller har några rekommendationer för inspelningsstudion. Formatkravet finns i [hur du förbereder inspelningar och transkriptioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**F: Vilka skript ska jag använda för att spela in taldata för anpassad röstutbildning?**

**S:** Vi begränsar inte skripten för röstinspelning. Du kan använda dina egna skript för att spela in talet. Se bara till att du har tillräcklig fonetisk täckning i dina taldata. Om du vill träna en anpassad röst kan du börja med en liten mängd taldata, vilket kan vara 50 olika meningar (cirka 3-5 minuters tal). Ju mer data du tillhandahåller, desto mer naturlig blir din röst. Du kan börja träna ett fullständigt röstteckensnitt när du anger inspelningar med fler än 2 000 meningar (cirka 3–4 timmars tal). För att få en hög kvalitet, full röst, måste du förbereda inspelningar av mer än 6.000 meningar (ca 8-10 timmars tal).

Vi tillhandahåller ytterligare tjänster som hjälper dig att förbereda skript för inspelning. Kontakta [Kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för custom röst för frågor.

**F: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som erbjuds i portalen?**

**S:** Du kan skala upp modellen i steg om 20 samtidiga begäranden. Kontakta [Kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för custom voice för frågor om högre skalning.

**F: Kan jag ladda ner min modell och köra den lokalt?**

**S**: Modeller kan inte hämtas och köras lokalt.

**F: Begränsas mina begäranden?**

**S**: REST API begränsar begäranden till 25 per 5 sekunder. Detaljer finns på våra sidor för [text till tal](text-to-speech.md).

## <a name="next-steps"></a>Nästa steg

- [Troubleshooting](troubleshooting.md) (Felsökning)
- [Viktig information](releasenotes.md)
