---
title: Skapa en anpassad tal slutpunkt med anpassade tal-tjänsten i Azure | Microsoft Docs
description: Lär dig hur du skapar en anpassad till text till tal-slutpunkt med anpassade tal-tjänsten i kognitiva Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351633"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Skapa en anpassad till text till tal-slutpunkt
När du har skapat anpassade ljud modeller eller språk modeller, kan du distribuera dem i en anpassad till text till tal-slutpunkt. 

## <a name="create-an-endpoint"></a>Skapa en slutpunkt
Om du vill skapa en ny anpassad slutpunkt **distributioner** på den **anpassade tal** menyn överst på sidan. Den här åtgärden tar dig till den **distributioner** sida som innehåller en tabell med aktuella anpassade slutpunkter. Om du inte har skapat några slutpunkter är tabellen tom. Språket visas i rubriken för tabellen. 

Om du vill skapa en distribution för ett annat språk, Välj **ändra språk**. Mer information om språk som stöds finns [språk som stöds i anpassade tal Service](cognitive-services-custom-speech-change-locale.md).

Om du vill skapa en ny slutpunkt **Skapa nytt**. I den **skapa distribution** rutan anger du informationen i den **namn** och **beskrivning** kryssrutorna för din egen distribution.

I den **prenumeration** kombinationsrutan väljer du den prenumeration som du vill använda. Om den är en S2 prenumeration kan välja du skalenheter och innehåll loggning. Mer information om skalningsenheter och loggning finns [anpassade tal Service mätare och kvoter](../cognitive-services-custom-speech-meters.md).

I följande tabell visar hur skalningsenheter mappas till tillgängliga samtidiga begäranden:

| Skalningsenhet | Antalet samtidiga begäranden |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Du kan också välja om innehåll loggning växlas eller inaktivera. Det vill säga markerar du om slutpunkten trafiken lagras för intern användning av Microsoft. Om det inte är markerat ignoreras lagra trafiken. Utelämna innehåll loggning leder till extra kostnad. Läs den [informationssida med priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) mer information.

> [!NOTE]
> Innehåll loggning kallas ”Nej Trace” på sida med priser.
>


Dessutom erbjuder Microsoft en grov uppskattning av kostnaderna så att du är medveten om påverkan på kostnaderna för skalenheter och innehåll loggning. Denna uppskattning är en grov uppskattning och kan skilja sig från de verkliga kostnaderna.

> [!NOTE]
> De här inställningarna är inte tillgängliga med F0 (kostnadsfria nivån) prenumerationer.
>

I den **ljud modellen** väljer ljud modellen som du vill och i den **språk modellen** väljer du språk modellen som du vill använda. Alternativ för acoustic och språk modeller Inkludera alltid de grundläggande Microsoft-modellerna. Valet av grundläggande modellen begränsar kombinationerna. Du kan blanda vardagliga samtalsuttryck grundläggande modeller med Sök och kräver grundläggande modeller.

![Sidan Skapa distribution](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Se till att acceptera villkoren för användning och information om priser genom att markera kryssrutan.
>

När du har valt språk och acoustic modeller, Välj **skapa**. Den här åtgärden återgår till den **distributioner** sidan. Tabellen innehåller nu en post som motsvarar din nya slutpunkt. Status för slutpunkten visar det aktuella tillståndet när den skapas. Det kan ta upp till 30 minuter att skapa en instans av en ny slutpunkt med din anpassade modeller. När statusen för distributionen är *Slutför*, slutpunkten är redo för användning.

![Sidan distributioner](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

När distributionen är klar, blir en länk i distributionens namn. Om du väljer länken visas den **distributionsinformation** sidan som visar de URL: er för din anpassade slutpunkten ska användas med antingen en HTTP-begäran eller Microsoft kognitiva Services tal klientbiblioteket, som använder web sockets.

![Sidan Information om distribution](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Nästa steg

Flera självstudier finns:
* [Använda en anpassad till text till tal-slutpunkt](cognitive-services-custom-speech-use-endpoint.md)
* [Skapa en anpassad ljud modell](cognitive-services-custom-speech-create-acoustic-model.md)
* [Skapa en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
