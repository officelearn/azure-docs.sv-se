---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Med formulär tolken för Azure Cognitive Services kan du identifiera och extrahera nyckel/värde-par och tabell data från formulär dokument.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723577"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure formulär igenkänning är en kognitiv tjänst som använder maskin inlärnings teknik för att identifiera och extrahera text, nyckel/värde-par och tabell data från formulär dokument. Den matar in text från formulär och matar ut strukturerade data som innehåller relationerna i original filen. Du får snabbt korrekta resultat som skräddarsys efter ditt eget innehåll utan att du behöver göra en kraftig eller omfattande data vetenskaps expert. Formulär tolken består av anpassade modeller, en fördefinierad kvitto modell och layout-API: et. Du kan anropa formulär igenkännings modeller genom att använda ett REST API för att minska komplexiteten och integrera dem i arbets flödet eller programmet.

Formulär tolken består av följande tjänster:
* **Anpassade modeller** – extrahera nyckel/värde-par och tabell data från formulär. De här modellerna tränas med dina egna data, så de skräddarsys efter dina formulär.
* **Färdiga modeller** – extrahera data från unika formulär typer med hjälp av förinställda modeller. För närvarande finns det färdiga modeller för försäljnings kvitton och visitkort på engelska.
* **Layout-API** – Extrahera text och tabell strukturer, tillsammans med deras gränser för avgränsnings rutor, från dokument.

<!-- add diagram -->

## <a name="custom-models"></a>Anpassade modeller

Formulär igenkännings anpassade modeller tränar dina egna data och du behöver bara fem exempel inmatnings formulär att starta. En utbildad modell kan spara strukturerade data som innehåller relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

Du har följande alternativ när du tränar anpassade modeller: utbildning med märkta data och utan etiketterade data.

### <a name="train-without-labels"></a>Träna utan etiketter

Som standard använder formulär tolken oövervakad inlärning för att förstå layouten och relationerna mellan fält och poster i formulären. När du skickar in dina indata-formulär, identifierar algoritmen formulären efter typ, identifierar vilka nycklar och tabeller som finns och associerar värden till nycklar och poster i tabeller. Detta kräver inte manuella data etiketter eller intensiv kodning och underhåll, och vi rekommenderar att du provar den här metoden först.

### <a name="train-with-labels"></a>Träna med etiketter

När du tränar med märkta data övervakas modellen för att extrahera värden av intresse med hjälp av de märkta formulär som du anger. Detta resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.

Formulär tolken använder [layout-API](#layout-api) för att lära dig de förväntade storlekarna och positionerna för de utskrivna och handskrivna text element Sedan använder den användardefinierade etiketter för att lära sig nyckel/värde-associationerna i dokumenten. Vi rekommenderar att du använder fem manuellt märkta formulär av samma typ för att komma igång när du tränar en ny modell och lägger till mer märkta data som behövs för att förbättra modellens noggrannhet.

## <a name="prebuilt-models"></a>Fördefinierade modeller

Formulär tolken innehåller även färdiga modeller för unika formulär typer.
### <a name="prebuilt-receipt-model"></a>Fördefinierad kvitto modell
Fördefinierad kvitto modell används för att läsa kvitton på engelska försäljning från Australien, Kanada, Storbritannien, Indien och USA typ som &mdash; används av restauranger, gas stationer, åter försäljare och så vidare. Den här modellen hämtar viktig information, till exempel tid och datum för transaktionen, handels information, moms belopp, rad artiklar, total summor och mer. Dessutom tränas den förskapade kvitto modellen att identifiera och returnera all text i ett kvitto. 

![exempel kvitto](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Fördefinierad visitkorts modell
Med visitkorts modellen kan du extrahera information som personens namn, befattning, adress, e-post, företags nummer och telefonnummer från visitkort på engelska. 

![exempel på visitkort](./media/business-card-english.jpg)

## <a name="layout-api"></a>Layout-API

Formulär tolken kan också Extrahera text-och tabell strukturer (rad-och kolumn nummer som är associerade med texten) med hjälp av optisk tecken läsning (OCR) med hög definition.

## <a name="get-started"></a>Kom igång

Kom igång genom att följa en snabb start för att extrahera data från dina formulär. Vi rekommenderar att du använder den kostnads fria tjänsten när du lär dig tekniken. Kom ihåg att antalet fria sidor är begränsat till 500 per månad.

* [Snabb start för klient bibliotek](./quickstarts/client-library.md) (alla språk, flera scenarier)
* Snabb start för webb gränssnitt
  * [Träna med etiketter – exempel på etikett verktyg](quickstarts/label-tool.md)
* REST-snabb starter
  * Träna anpassade modeller och extrahera formulär data
    * [Träna utan etiketter – sväng](quickstarts/curl-train-extract.md)
    * [Träna utan etiketter – python](quickstarts/python-train-extract.md)
    * [Träna med etiketter – python](quickstarts/python-labeled-data.md)
  * Extrahera data från USA Sales-kvitton
    * [Extrahera kvitto data – sväng](quickstarts/curl-receipts.md)
    * [Extrahera kvitto data – python](quickstarts/python-receipts.md)
  * Extrahera text-och tabell struktur från formulär
    * [Extrahera layoutinformation – python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Granska REST-API: erna

Du använder följande API: er för att träna modeller och extrahera strukturerade data från formulär.

|Namn |Beskrivning |
|---|---|
| **Träna anpassad modell**| Träna en ny modell för att analysera dina formulär genom att använda fem formulär av samma typ. Ange parametern _useLabelFile_ för `true` att träna med manuellt märkta data. |
| **Analysera formulär** |Analysera ett enda dokument som skickas in som en ström för att extrahera text, nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| **Analysera inleverans** |Analysera ett enda kvitto dokument för att extrahera viktig information och annan kvitto text.|
| **Analysera layout** |Analysera layouten för ett formulär för att extrahera text-och tabell strukturen.|

Mer information får du genom att utforska [REST API referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Om du är bekant med en tidigare version av API: n läser du artikeln [Nyheter](./whats-new.md) och lär dig mer om de senaste ändringarna.

## <a name="input-requirements"></a>Krav för indatamängd
### <a name="custom-model"></a>Anpassad modell

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>Fördefinierade

Ingångs kraven för kvitto modellen skiljer sig något åt.

* Formatet måste vara JPEG, PNG, PDF (text eller scannat) eller TIFF.
* Fil storleken måste vara mindre än 20 MB.
* Bild dimensioner måste vara mellan 50 x 50 pixlar och 10000 x 10000 bild punkter.
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.
* För PDF och TIFF bearbetas bara de första 200 sidorna (med en prenumeration på kostnads fri nivå, bara de första två sidorna bearbetas).

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder formulär tolknings tjänsten vara medvetna om Microsofts principer för kund information. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med [formulär igenkännings-API: erna](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)genom att slutföra en [snabb start](quickstarts/curl-train-extract.md) .