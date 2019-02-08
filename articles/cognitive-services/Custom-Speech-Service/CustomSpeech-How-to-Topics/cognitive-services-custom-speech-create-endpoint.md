---
title: Skapa en anpassad slutpunkt med Custom Speech Service på Azure | Microsoft Docs
description: Lär dig hur du skapar en anpassad tal till text-slutpunkt med Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: nitinme
ms.openlocfilehash: efd88a3f222b229fcd10432fc011d5ffa68cd414
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883958"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Skapa en anpassad slutpunkt för till tal till text

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

När du har skapat anpassade akustiska modeller eller språkmodeller, kan du distribuera dem i en anpassad tal till text-slutpunkt. 

## <a name="create-an-endpoint"></a>Skapa en slutpunkt
Om du vill skapa en ny anpassad slutpunkt, Välj **distributioner** på den **Custom Speech** menyn längst upp på sidan. Den här åtgärden tar dig till den **distributioner** sidan, som innehåller en tabell med aktuella anpassade slutpunkter. Om du inte har skapat några slutpunkter är tabellen tom. Den aktuella nationella inställningen visas i tabellrubriken. 

Om du vill skapa en distribution för ett annat språk, Välj **ändra språket**. Mer information om språk som stöds finns i [språk som stöds i Custom Speech Service](cognitive-services-custom-speech-change-locale.md).

Om du vill skapa en ny slutpunkt, Välj **Skapa ny**. I den **skapa distributionen** fönstret anger du informationen i den **namn** och **beskrivning** rutorna för anpassade distributionen.

I den **prenumeration** kombinationsrutan väljer du den prenumeration som du vill använda. Om det är en S2-prenumeration kan välja du skalningsenheter och innehållsloggning. Mer information om skalningsenheter och loggning finns i [Custom Speech Service-mätare och kvoter](../cognitive-services-custom-speech-meters.md).

I följande tabell visar hur skalningsenheter mappas till tillgängliga samtidiga begäranden:

| Skalningsenheten | Antalet samtidiga begäranden |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Du kan också välja om innehållsloggning växlas eller inaktivera. Det vill säga markerar du om slutpunkt-trafiken har lagrats för intern användning av Microsoft. Om den inte är markerad ska lagra trafiken ignoreras. Utelämna innehållsloggning leder till extra kostnad. Läs den [informationssidan med priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) mer information.

> [!NOTE]
> Innehållsloggning kallas ”Nej Trace” på sidan med priser.
>


Microsoft tillhandahåller dessutom en grov uppskattning av kostnaderna så att du är medveten om påverkan på kostnaderna för skalningsenheter och innehållsloggning. Den här beräkningen är en grov uppskattning och kan skilja sig från de verkliga kostnaderna.

> [!NOTE]
> De här inställningarna är inte tillgängliga med F0 (den kostnadsfria nivån)-prenumerationer.
>

I den **akustisk modell** väljer du den akustiska modellen som du vill, och i den **språkmodell** väljer språkmodellen som du vill. Alternativen för- och språkdata modeller är alltid de grundläggande Microsoft-modellerna. Valet av basmodellen begränsar kombinationerna. Du kan inte blanda konversationsanpassade grundläggande modeller med sökning och bestämmer grundläggande modeller.

![Sidan Skapa distribution](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Glöm inte att acceptera villkoren för användning och information om priser genom att välja kryssrutan.
>

När du har valt dina modeller för- och språkdata, Välj **skapa**. Den här åtgärden tillbaka till den **distributioner** sidan. Tabellen innehåller nu en post som motsvarar din nya slutpunkt. Status för den slutpunkt visar det aktuella tillståndet, medan det skapas. Det kan ta upp till 30 minuter att skapa en instans av en ny slutpunkt med anpassade modeller. När statusen för distributionen är *Slutför*, slutpunkten är redo att användas.

![Sidan distributioner](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

När distributionen är klar, blir en länk i distributionens namn. Att välja länken visar den **distributionsinformation** sidan som visar URL: er för din anpassade slutpunkt som ska användas med antingen en HTTP-begäran eller Microsoft Cognitive Services Talklientbiblioteket, som använder webbsockets.

![Sidan Information om distribution](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Nästa steg

Fler självstudier finns i:
* [Använda en anpassad tal till text-slutpunkt](cognitive-services-custom-speech-use-endpoint.md)
* [Skapa en anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md)
* [Skapa en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
