---
title: Anpassade Speech Service-mätare och kvoter för Azure | Microsoft Docs
description: Information om mätare och kvoter för Custom Speech Service på Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: nitinme
ms.openlocfilehash: 3f94ac32198c0d95488f207802ed5693e49b839d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872568"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Anpassade Speech Service-mätare och kvoter

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Med molnbaserad Custom Speech Service kan anpassa du talmodeller för tal till text avskrift.

Om du vill komma igång med Custom Speech Service måste du gå till den [Custom Speech Service portal](https://cris.ai).

För aktuellt prismätare, gå till den [priser för Cognitive Services för Custom Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) sidan.

## <a name="tiers-explained"></a>Nivåerna förklaras
För testning och prototyper endast föreslår vi att använda den kostnadsfria nivån av F0. För produktionssystem föreslår vi att använda på nivån S2. Med hjälp av S2-nivån kan du skala distributionen till antal skalningsenheter (su) som krävs för ditt scenario.

> [!NOTE]
> Du *kan* migrera mellan den F0 och S2-nivå.
>

## <a name="meters-explained"></a>Mätare som förklaras

### <a name="scale-out"></a>Skalbarhet
Skala ut är en ny funktion som vi har släppt med den nya prismodellen. Med skala ut kan styra du hur många samtidiga begäranden som din modell kan bearbeta.

Du kan ange samtidiga begäranden med hjälp av SU-mått i den **skapa Modelldistribution** vy. Mer information finns i [skapa en anpassad tal till text-slutpunkt](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Beroende på mängden nätverkstrafik som du räkna modellen förbrukar, kan du välja ett lämpligt antal SUs. 

> [!NOTE]
> Varje skalningsenhet ger 5 samtidiga begäranden. Du kan köpa 1 eller flera SUs, efter behov. Eftersom antalet SUs ökar i steg om 1, garanterat antalet samtidiga begäranden att öka i steg om 5.
>

### <a name="log-management"></a>Logghantering
Du kan välja att stänga av ljud spårningar för en nyligen distribuerade modell en extra kostnad. Med Custom Speech Service loggar inte ljud begäranden eller avskrifter från den modellen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Custom Speech Service går du till den [Custom Speech Service portal](https://cris.ai).

* [Kom igång](cognitive-services-custom-speech-get-started.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
 
