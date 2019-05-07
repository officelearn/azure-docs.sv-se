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
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143223"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

Formigenkänning i Azure är en kognitiv tjänst som använder maskininlärningsteknik för att identifiera och extrahera nyckel/värde-par och tabelldata från formulärdokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Du kan anropa anpassade formulär Igenkännande modellen med hjälp av ett enkelt REST API för att minska komplexiteten och integrera enkelt i ditt arbetsflöde eller program. Du behöver bara fem formuläret dokument eller ett tomt formulär av samma typ som dina indata material att komma igång. Du kan få resultat snabbt, korrekt och anpassats efter dina specifika innehåll utan att behöva tung manuella åtgärder eller omfattande data science-expertis.

## <a name="request-access"></a>Begär åtkomst
Formuläret identifierare är tillgänglig som förhandsversion begränsad åtkomst. För att få åtkomst till förhandsversionen kan du fylla i och skicka den [Cognitive Services formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret. Formuläret begär information om dig, ditt företag och Användarscenario som du använder formuläret Igenkännande. Om din begäran har godkänts av Azure Cognitive Services-teamet, får du ett e-postmeddelande med instruktioner om hur du kommer åt tjänsten.

## <a name="what-it-does"></a>Vad läget gör

När du skickar in din indata träna algoritmen, kluster formulär per typer, identifierar vilka nycklar och tabeller finns och lär sig att koppla värden till nycklar och poster till tabeller. Med oövervakad inlärning kan modellen att förstå layout och relationer mellan fält och poster utan data för manuell märkning eller intensiv kodning och underhåll. Förtränade maskininlärning modeller kräver standardiserad och är mindre exakta med inkommande material som avviker från traditionella format, t.ex. däremot branschspecifika formulär.

När modellen tränas kan du testa, träna om och använder den för att på ett tillförlitligt sätt extrahera data från flera formulär efter dina behov.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Formuläret Igenkännande är tillgängligt som ett REST-API. Du kan skapa, träna och betygsätta en modell genom att anropa API: et och modellen kan du köra i en lokal dockerbehållare.

## <a name="input-requirements"></a>Krav för indata

Formuläret Igenkännande fungerar på inkommande dokument som uppfyller följande krav:

* JPG-, PNG- eller PDF-format (text eller skannade). Text inbäddade PDF-filer är bättre eftersom det finns ingen risk för fel i teckenextrahering och plats.
* Filstorleken måste vara mindre än 4 megabyte (MB)
* För avbildningar, måste dimensioner vara mellan 50 x 50 och 4200 x 4200 bildpunkter
* Om genomsökts från pappersdokument bör formulär vara hög kvalitet genomsökningar
* Måste använda det latinska alfabetet (engelska tecken)
* Utskrivna data (handskriven inte)
* Måste innehålla nycklar och värden
* Nycklar kan visas ovanför eller till vänster av värden, men inte mindre än eller till höger.

Dessutom stöder formuläret Igenkännande ännu inte följande typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler och så vidare) 
* Kryssrutorna eller radio knappar
* PDF-dokument som är längre än 50 sidor

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1:** Skapa en Form Igenkännande-resurs i Azure-portalen.

**Steg 2:** Testa en Snabbstart för praktisk erfarenhet:
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med cURL](quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med Python](quickstarts/python-train-extract.md)

Vi rekommenderar den kostnadsfria tjänsten i utbildningssyfte, men tänk på att antalet lediga sidor är begränsad till 500 sidor per månad.

**Steg 3:** Granska följande API: er för att träna och extrahera strukturerade data från formulär för REST API-användning.

| REST-API | Beskrivning |
|-----|-------------|
| Träna | Skapa en ny modell för att analysera dina formulär med hjälp av 5 formulär från samma typ eller ett tomt formulär.  |
| Analysera  |Analysera ett enskilt dokument som skickats in som en dataström för att extrahera nyckel / värde-par och tabeller i formuläret med din anpassade modell.  |

Utforska den [REST API-referensdokumentet](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Tjänsten erbjuds som en [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) i en Azure-tjänst under de [villkoren för Online Service](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Som med alla kognitiva tjänster bör utvecklare som använder tjänsten formuläret Igenkännande vara medveten om Microsofts policy på kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en [snabbstarten](quickstarts/curl-train-extract.md) att komma igång med den [API: er för Igenkännande](https://aka.ms/form-recognizer/api).
