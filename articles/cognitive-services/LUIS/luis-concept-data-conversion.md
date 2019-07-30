---
title: Data konvertering – LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du kan ändra yttranden innan förutsägelser i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619801"
---
# <a name="convert-data-format-of-utterances"></a>Konvertera dataformatet för yttranden
LUIS tillhandahåller följande konverteringar av en användar-uttryck innan du förutsäger "

* Tal till text med hjälp av [Cognitive Services Speech](../Speech-Service/overview.md) service. 

## <a name="speech-to-text"></a>Tal till text

Tal till text tillhandahålls som en integrering med LUIS. 

### <a name="intent-conversion-concepts"></a>Konverterings begrepp för avsikt
Konvertera tal till text i LUIS kan du skicka talat yttranden till en slutpunkt och ta emot svar LUIS förutsägelse. Processen är en integrering av den [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) tjänst med LUIS. Lär dig mer om tal till avsikt med en [själv studie kurs](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **taligenkänning för Bing** nyckel för den här integreringen. En **Språkförståelse** nyckeln som skapats i Azure portal som fungerar för den här integreringen. Använd inte start nyckeln LUIS.

### <a name="pricing-tier"></a>Prisnivå
Den här integrationen använder en annan [pris](luis-boundaries.md#key-limits) modell än vanliga language Understanding pris nivåer. 

### <a name="quota-usage"></a>Kvotanvändning
Se [viktiga begränsningar](luis-boundaries.md#key-limits) information. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extraherar data](luis-concept-data-extraction.md)

