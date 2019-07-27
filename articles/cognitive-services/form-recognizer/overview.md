---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formulär tolken för att tolka formulär-och tabell data.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: e910f2733a5485d50ad387a1e82ce27e0ba8fdea
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562677"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

Azure formulär igenkänning är en kognitiv tjänst som använder maskin inlärnings teknik för att identifiera och extrahera nyckel/värde-par och tabell data från formulär dokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Du kan anropa din anpassade formulär igenkännings modell genom att använda en enkel REST API för att minska komplexiteten och enkelt integrera den i arbets flödet eller programmet. För att komma igång behöver du bara fem ifyllda formulär dokument eller två ifyllda formulär och en tom form av samma typ som ditt inmatade material. Du får snabbt korrekta resultat som skräddarsys efter ditt eget innehåll utan att du behöver göra en kraftig eller omfattande data vetenskaps expert.

## <a name="custom-models"></a>Anpassade modeller

Formulär tolkens anpassade modell tågen till dina egna data, och du behöver bara fem exempel inmatnings formulär att starta. När du skickar in indata-data kommer algoritmen att klustra formulären efter typ, identifierar vilka nycklar och tabeller som finns och associerar värden till nycklar och poster i tabeller. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

Med oövervakad inlärning kan modellen att förstå layout och relationer mellan fält och poster utan data för manuell märkning eller intensiv kodning och underhåll. Förtränade maskin inlärnings modeller kräver däremot standardiserade data. De är mindre exakta med inmatat material som skiljer sig från traditionella format, t. ex. branschspecifika formulär.

## <a name="prebuilt-receipt-model"></a>Fördefinierad kvitto modell

Formulär tolken innehåller också en modell för läsning av försäljnings kvitton. Den här modellen hämtar viktig information, till exempel tid och datum för transaktionen, handels information, belopp för skatter och total summor. Dessutom tränas den förinställda inbetalnings modellen att identifiera och returnera all text i ett kvitto.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Formulär tolken är tillgänglig som en REST API. Du kan skapa, träna och räkna upp en anpassad modell eller komma åt den inbyggda modellen genom att anropa dessa API: er. Om du vill kan du träna och köra anpassade modeller i en lokal Docker-behållare.

## <a name="input-requirements"></a>Krav för indatamängd
### <a name="custom-model"></a>Anpassad modell

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Fördefinierad kvitto modell

Ingångs kraven för kvitto modellen skiljer sig något åt.

* Formatet måste vara JPEG, PNG, BMP, PDF (text eller scannat) eller TIFF.
* Fil storleken måste vara mindre än 20 MB.
* Bild dimensioner måste vara mellan 50 x 50 pixlar och 10000 x 10000 bild punkter. 
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.
* För PDF och TIFF bearbetas bara de första 200 sidorna (med en prenumeration på kostnads fri nivå, bara de första två sidorna bearbetas).

## <a name="request-access"></a>Begär åtkomst

Formulär tolken är tillgänglig i en för hands version med begränsad åtkomst. För att få åtkomst till förhands granskningen, fyller du i och skickar [formulär tolken formulär för åtkomst förfrågan](https://aka.ms/FormRecognizerRequestAccess) . Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder formulär igenkänning för. Om din begäran godkänns av Azure Cognitive Services-teamet får du ett e-postmeddelande med instruktioner för att komma åt tjänsten.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1:** Skapa en formulär igenkännings resurs i Azure Portal.

**Steg 2:** Följ snabb starten för att använda REST API:
* [Snabbstart: Träna en formulär igenkännings modell och extrahera formulär data med hjälp av REST API med vändning](quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en formulär igenkännings modell och extrahera formulär data med hjälp av REST API med python](quickstarts/python-train-extract.md)
* [Snabbstart: Extrahera kvitto data med hjälp av sväng](quickstarts/curl-receipts.md)
* [Snabbstart: Extrahera kvitto data med hjälp av python](quickstarts/python-receipts.md)

Vi rekommenderar att du använder den kostnads fria tjänsten när du lär dig tekniken. Tänk på att antalet fria sidor är begränsat till 500 per månad.

**Steg 3:** Granska REST-API: erna

Du använder följande API: er för att träna och extrahera strukturerade data från formulär.

|||
|---|---|
| Träningsmodell| Träna en ny modell för att analysera dina formulär genom att använda fem formulär av samma typ. Eller träna med ett tomt formulär och två ifyllda formulär.  |
| Analysera formulär |Analysera ett enda dokument som skickas in som en ström för att extrahera nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| Analysera inleverans |Analysera ett enda kvitto dokument för att extrahera viktig information och annan kvitto text.|

Mer information får du genom att utforska [REST API referens dokumentationen](https://aka.ms/form-recognizer/api) . 

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Den här tjänsten erbjuds som en för [hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) av en Azure-tjänst under [tjänst villkoren online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Precis som med alla kognitiva tjänster bör utvecklare som använder formulär tolknings tjänsten vara medvetna om Microsofts principer för kund information. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med [formulär igenkännings-API: erna](https://aka.ms/form-recognizer/api)genom att slutföra en [snabb start](quickstarts/curl-train-extract.md) .