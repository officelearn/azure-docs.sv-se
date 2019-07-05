---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formuläret Igenkännande för att parsa form-och tabelldata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e064faf3017b95cb3a5f3d9b89f178fb7f846766
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592604"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

Azure formuläret Igenkännande är en cognitive tjänst som använder maskininlärningsteknik för att identifiera och extrahera nyckel/värde-par och tabelldata från formuläret dokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Du kan anropa anpassade formulär Igenkännande modellen genom att använda ett enkelt REST API för att minska komplexiteten och integrera enkelt i ditt arbetsflöde eller program. För att komma igång behöver du bara fem ifyllt formulär dokument eller två ifyllda formulär plus en tom form av samma typ som ditt material som indata. Du kan snabbt få korrekta resultat som är skräddarsydda för din specifikt innehåll utan tunga manuella åtgärder eller omfattande data science-expertis.

## <a name="custom-models"></a>Anpassade modeller

Formuläret Igenkännande anpassade modellen träna till dina egna data och du behöver bara fem inkommande exempelformulär att starta. När du skickar in din indata algoritmen kluster formulären efter typ, identifierar vilka nycklar och tabeller finns och associerar värden till nycklar och poster till tabeller. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. När du tränar kan du testa och träna om den och använder den för att på ett tillförlitligt sätt extrahera data från flera formulär efter dina behov.

Med oövervakad inlärning kan modellen att förstå layout och relationer mellan fält och poster utan data för manuell märkning eller intensiv kodning och underhåll. Däremot kräver förtränade machine learning-modeller standardiserad. Det är mindre exakta med inkommande material som avviker från traditionella format som branschspecifika formulär.

## <a name="pre-built-receipt-model"></a>Färdiga kvitto modell

Formuläret Igenkännande innehåller också en modell för att läsa in. Den här modellen extraherar viktig information, till exempel tiden och datumet för transaktionen, handlare information, mängder skatter och summor och mer. Dessutom kan tränas den färdiga kvitton att känna igen och returnera all text i en inleverans.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Formuläret Igenkännande är tillgängligt som ett REST-API. Du kan skapa, träna, och en anpassad bedömningsmodell eller komma åt den färdiga modellen genom att aktivera dessa API: er. Om du vill kan du träna och kör anpassade modeller i en lokal dockerbehållare.

## <a name="input-requirements-custom-model"></a>Inkommande krav (anpassade modellen)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Begär åtkomst

Formuläret Igenkännande är tillgänglig i en förhandsversion med begränsad åtkomst. För att få åtkomst till förhandsversionen kan fylla i och skicka den [formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret. Formuläret begär information om dig, ditt företag och Användarscenario som du använder formuläret Igenkännande. Om din begäran har godkänts av Azure Cognitive Services-teamet, får du ett e-postmeddelande med instruktioner för att komma åt tjänsten.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1:** Skapa en Form Igenkännande-resurs i Azure-portalen.

**Steg 2:** Följ en Snabbstart för att använda REST-API:
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med cURL](quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med Python](quickstarts/python-train-extract.md)
* [Snabbstart: Extrahera kvitto data med cURL](quickstarts/curl-receipts.md)
* [Snabbstart: Extrahera kvitto data med hjälp av Python](quickstarts/python-receipts.md)

Vi rekommenderar att du använder den kostnadsfria tjänsten när du håller på att lära tekniken. Tänk på att antalet lediga sidor är begränsad till 500 per månad.

**Steg 3:** Granska REST-API: er

Du kan använda följande API: er för att träna och extrahera strukturerade data från formulär.

|||
|---|---|
| Träningsmodell| Skapa en ny modell för att analysera dina formulär med hjälp av fem typer av samma typ. Eller öva med ett tomt formulär och två fyllts i formulär.  |
| Analysera formulär |Analysera ett enskilt dokument som skickats in som en dataström för att extrahera nyckel/värde-par och tabeller i formuläret med din anpassade modell.  |
| Analysera kvitto |Analysera ett enda kvitto dokument för att extrahera viktig information och andra kvittotext.|

Utforska den [REST API-referensdokumentation](https://aka.ms/form-recognizer/api) vill veta mer. 

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Den här tjänsten erbjuds som en [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) av en Azure-tjänst under de [villkoren för Online Service](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Utvecklare som använder tjänsten formuläret Igenkännande bör vara medveten om Microsoft-principer på kundernas data precis som med alla kognitiva tjänster. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabbstarten](quickstarts/curl-train-extract.md) att komma igång med den [API: er för Igenkännande](https://aka.ms/form-recognizer/api).
