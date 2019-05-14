---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formuläret Igenkännande för att parsa form-och tabelldata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601619"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

Formigenkänning i Azure är en kognitiv tjänst som använder maskininlärningsteknik för att identifiera och extrahera nyckel/värde-par och tabelldata från formulärdokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Du kan anropa anpassade formulär Igenkännande modellen genom att använda ett enkelt REST API för att minska komplexiteten och integrera enkelt i ditt arbetsflöde eller program. För att komma igång behöver du bara fem formuläret dokument eller ett tomt formulär av samma typ som ditt material som indata. Du kan snabbt få korrekta resultat som är skräddarsydda för din specifikt innehåll utan tunga manuella åtgärder eller omfattande data science-expertis.

## <a name="request-access"></a>Begär åtkomst
Formuläret Igenkännande är tillgänglig i en förhandsversion med begränsad åtkomst. För att få åtkomst till förhandsversionen kan fylla i och skicka den [formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret. Formuläret begär information om dig, ditt företag och Användarscenario som du använder formuläret Igenkännande. Om din begäran har godkänts av Azure Cognitive Services-teamet, får du ett e-postmeddelande med instruktioner för att komma åt tjänsten.

## <a name="what-it-does"></a>Vad läget gör

När du skickar in din indata träna algoritmen, kluster formulär per typer, identifierar vilka nycklar och tabeller finns och lär sig att koppla värden till nycklar och poster till tabeller. Med oövervakad inlärning kan modellen att förstå layout och relationer mellan fält och poster utan data för manuell märkning eller intensiv kodning och underhåll. Däremot kräver standardiserad förtränade machine learning-modeller och är mindre exakta när det används med inkommande material som avviker från traditionella format som branschspecifika formulär.

När du tränar kan du testa och träna om den och använder den för att på ett tillförlitligt sätt extrahera data från flera formulär efter dina behov.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Formuläret Igenkännande är tillgängligt som ett REST-API. Du kan skapa, träna och betygsätta en modell genom att anropa API: et. Om du vill kan köra du modellen i en lokal dockerbehållare.

## <a name="input-requirements"></a>Krav för indata

Formuläret Igenkännande fungerar på inkommande dokument som uppfyller dessa krav:

* Formatet måste vara JPG, PNG- eller PDF (text eller skannade). Text som är inbäddad PDF-filer är bästa eftersom det finns ingen risk för fel i teckenextrahering och plats.
* Filstorleken måste vara mindre än 4 MB (megabyte).
* Storleken måste vara mellan 50 x 50 bildpunkter och 4200 x 4200 bildpunkter för avbildningar.
* Om genomsökts från pappersdokument vara formulär genomsökningar av hög kvalitet.
* Text måste använda det latinska alfabetet (engelska tecken).
* Data måste skrivas (handskriven inte).
* Data måste innehålla nycklar och värden.
* Nycklar kan visas ovanför eller till vänster av värden, men inte mindre än eller till höger.

Formuläret Igenkännande stöder inte för närvarande dessa typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler och så vidare).
* Kryssrutorna eller radio knappar.
* PDF-dokument är längre än 50 sidor.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1:** Skapa en Form Igenkännande-resurs i Azure-portalen.

**Steg 2:** Testa en Snabbstart för praktisk erfarenhet:
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med cURL](quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med Python](quickstarts/python-train-extract.md)

Vi rekommenderar att du använder den kostnadsfria tjänsten när du håller på att lära tekniken, men tänk på att antalet lediga sidor är begränsad till 500 sidor per månad.

**Steg 3:** Granska REST-API: er

Använd följande API: er för att träna och extrahera strukturerade data från formulär.

| REST API | Beskrivning |
|-----|-------------|
| Träna | Skapa en ny modell för att analysera dina formulär med hjälp av fem formulär från samma typ eller ett tomt formulär.  |
| Analysera  |Analysera ett enskilt dokument som skickats in som en dataström för att extrahera nyckel / värde-par och tabeller i formuläret med din anpassade modell.  |

Utforska den [REST API-referensdokumentet](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Den här tjänsten erbjuds som en [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) av en Azure-tjänst under de [villkoren för Online Service](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Utvecklare som använder tjänsten formuläret Igenkännande bör vara medveten om Microsoft-principer på kundernas data precis som med alla kognitiva tjänster. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabbstarten](quickstarts/curl-train-extract.md) att komma igång med den [API: er för Igenkännande](https://aka.ms/form-recognizer/api).
