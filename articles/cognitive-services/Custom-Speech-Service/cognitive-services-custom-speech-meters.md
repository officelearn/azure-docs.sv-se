---
title: Anpassade tal Service mätare och kvoter på Azure | Microsoft Docs
description: Information om mätare och kvoter för anpassade tal-tjänsten på Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351471"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Anpassade tal Service mätare och kvoter

Du kan anpassa tal modeller för skrivfel tal till text med molnbaserad anpassad tal tjänst.

Börja med hjälp av tjänsten anpassad tal, gå till den [anpassade tal tjänstportalen](https://cris.ai).

Priser för aktuellt mätare, gå till den [kognitiva Services priser för anpassad tal Service](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) sidan.

## <a name="tiers-explained"></a>Nivåer förklaras
För att testa och prototyp endast föreslår vi att använda den kostnadsfria nivån F0. För drift föreslår vi att använda S2-lagret. Du kan skala distributionen av antalet skalenheter (SUs) som krävs för ditt scenario med hjälp av S2 nivån.

> [!NOTE]
> Du *kan* migrera mellan F0 nivå och S2 nivån.
>

## <a name="meters-explained"></a>Mätare förklaras

### <a name="scale-out"></a>Skalbarhet
Skala ut är en ny funktion som släpptes med nya priserna. Du kan styra antalet samtidiga begäranden som din modell kan bearbeta genom att använda skala ut.

Du kan ange samtidiga begäranden med hjälp av SU mått i den **skapa modellen distribution** vyn. Mer information finns i [skapa en anpassad till text till tal-slutpunkt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Beroende på mängden nätverkstrafik som du räkna förbrukning av modellen, kan du välja ett lämpligt antal SUs. 

> [!NOTE]
> Varje skalningsenhet garanterar 5 samtidiga begäranden. Du kan köpa 1 eller flera SUs efter behov. Eftersom antalet SUs ökar i steg 1, garanteras antalet samtidiga begäranden att öka i steg 5.
>

### <a name="log-management"></a>Hantering av
Du kan välja för att inaktivera ljud spårningar för en nyligen distribuerade modell en extra kostnad. Anpassade tal tjänsten loggar inte ljud förfrågningar eller betyg från den modellen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder anpassade tal tjänsten går du till den [anpassade tal tjänstportalen](https://cris.ai).

* [Kom igång](cognitive-services-custom-speech-get-started.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
 