---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Med Azure Cognitive Services Form Recognizer kan du identifiera och extrahera nyckel-/värdepar och tabelldata från formulärdokument.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6b19dc11438274ecf6218d5c0bd8c9ef3dafbf01
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052439"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer är en kognitiv tjänst som använder maskininlärningsteknik för att identifiera och extrahera text, nyckel-/värdepar och tabelldata från formulärdokument. Den matar in text från formulär och utdata strukturerade data som innehåller relationerna i den ursprungliga filen. Du får snabbt korrekta resultat som är skräddarsydda för ditt specifika innehåll utan tunga manuella ingrepp eller omfattande datavetenskap expertis. Formulärreformare består av anpassade modeller, den fördefinierade kvittomodellen och layout-API:et. Du kan anropa Formulär recognizer-modeller genom att använda ett REST API för att minska komplexiteten och integrera dem i ditt arbetsflöde eller program.

Formulärre recognizer består av följande tjänster:
* **Anpassade modeller** - Extrahera nyckel-/värdepar och tabelldata från formulär. Dessa modeller är tränade med dina egna data, så de är skräddarsydda för dina formulär.
* **Fördefinierad kvittomodell** - Extrahera data från USA-inleveranser med hjälp av en fördefinierad modell.
* **Layout API** - Extrahera text och tabellstrukturer, tillsammans med deras bindningsramkoordinater, från dokument.

<!-- add diagram -->

## <a name="custom-models"></a>Anpassade modeller

Formulär recognizer anpassade modeller träna till dina egna data, och du behöver bara fem exempel indataformulär för att starta. En tränad modell kan mata ut strukturerade data som innehåller relationerna i det ursprungliga formulärdokumentet. När du tränar modellen kan du testa och träna om den och så småningom använda den för att på ett tillförlitligt sätt extrahera data från fler formulär efter dina behov.

Du har följande alternativ när du tränar anpassade modeller: utbildning med märkta data och utan etiketterade data.

### <a name="train-without-labels"></a>Träna utan etiketter

Som standard använder Formulär recognizer oövervakad inlärning för att förstå layouten och relationerna mellan fält och poster i formulären. När du skickar indataformulären grupperar algoritmen formulären efter typ, identifierar vilka nycklar och tabeller som finns och associerar värden till nycklar och poster till tabeller. Detta kräver inte manuell datamärkning eller intensiv kodning och underhåll, och vi rekommenderar att du provar den här metoden först.

### <a name="train-with-labels"></a>Träna med etiketter

När du tränar med märkta data övervakar modellen inlärning för att extrahera värden av intresse med hjälp av de märkta formulär som du tillhandahåller. Detta resulterar i bättre presterande modeller och kan producera modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.

Formulärreformare använder [layout-API:et](#layout-api) för att lära dig förväntade storlekar och positioner för utskrivna och handskrivna textelement. Sedan används användarspecificerade etiketter för att lära sig nyckel-/värdeassociationerna i dokumenten. Vi rekommenderar att du använder fem manuellt märkta formulär av samma typ för att komma igång när du tränar en ny modell och lägger till mer märkta data efter behov för att förbättra modellens noggrannhet.

## <a name="prebuilt-receipt-model"></a>Fördefinierad kvittomodell

Formulär recognizer innehåller också en modell för att&mdash;läsa engelska försäljningsintäkter från USA den typ som används av restauranger, bensinstationer, detaljhandel, och så vidare ([provkvitto](./media/contoso-receipt-small.png)). Den här modellen extraherar viktig information som tid och datum för transaktionen, handelsinformation, mängder av skatter och summor med mera. Dessutom är den fördefinierade kvittomodellen tränad att känna igen och returnera all text på ett kvitto.

## <a name="layout-api"></a>Layout API

Formulärmedkänare kan också extrahera text och tabellstruktur (rad- och kolumnnummer som är associerade med texten) med hjälp av HD-optisk teckenigenkänning (OCR).

## <a name="get-started"></a>Komma igång

Följ en snabbstart för att komma igång med att extrahera data från formulären. Vi rekommenderar att du använder den kostnadsfria tjänsten när du lär dig tekniken. Kom ihåg att antalet lediga sidor är begränsat till 500 per månad.

* Custom - träna en modell till dina formulär
  * Träna utan etiketter
    * [Snabbstart: Träna en formulärmedkänningsmodell och extrahera formulärdata med hjälp av REST API med cURL](quickstarts/curl-train-extract.md)
    * [Snabbstart: Träna en formulärmedkänningsmodell och extrahera formulärdata med hjälp av REST API med Python](quickstarts/python-train-extract.md)
  * Träna med etiketter
    * [Träna en formulärmedkänningsmodell med etiketter med hjälp av exempeletikettverktyget](quickstarts/label-tool.md)
    * [Träna en formulärmedkänningsmodell med etiketter med REST API och Python](quickstarts/python-labeled-data.md)
* Fördefinierade kvitton - extrahera data från usa försäljningsintäkter
  * [Snabbstart: Extrahera inleveransdata med cURL](quickstarts/curl-receipts.md)
  * [Snabbstart: Extrahera inleveransdata med Python](quickstarts/python-receipts.md)
* Layout - extrahera text och tabellstruktur från formulär
  * [Snabbstart: Extrahera layoutdata med Python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Granska REST-API:erna

Du använder följande API:er för att träna modeller och extrahera strukturerade data från formulär.

|Namn |Beskrivning |
|---|---|
| **Anpassad modell för tåg**| Träna en ny modell för att analysera formulären med hjälp av fem former av samma typ. Ställ in parametern _useLabelFile_ på `true` att träna med manuellt märkta data. |
| **Analysera formulär** |Analysera ett enda dokument som skickas in som en ström för att extrahera text, nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| **Analysera kvitto** |Analysera ett enda inleveransdokument för att extrahera nyckelinformation och annan inleveranstext.|
| **Analysera layout** |Analysera layouten för ett formulär för att extrahera text och tabellstruktur.|

Utforska [rest API-referensdokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) om du vill veta mer. Om du är bekant med en tidigare version av API:et läser du den nya artikeln [Vad är nytt](./whats-new.md) för att lära dig mer om de senaste ändringarna.

## <a name="input-requirements"></a>Indatakrav
### <a name="custom-model"></a>Anpassad modell

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Fördefinierad kvittomodell

Inmatningskraven för inleveransmodellen är något annorlunda.

* Formatet måste vara JPEG, PNG, BMP, PDF (text eller skannad) eller TIFF.
* Filstorleken måste vara mindre än 20 MB.
* Bilddimensionerna måste vara mellan 50 x 50 pixlar och 1 0000 x 1 0000 pixlar.
* PDF-måtten måste vara högst 17 x 17 tum, vilket motsvarar pappersstorlekar för juridiska eller A3 och mindre.
* För PDF och TIFF bearbetas endast de första 200 sidorna (med en prenumeration på den kostnadsfria nivån bearbetas endast de två första sidorna).

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Den här tjänsten erbjuds som en [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) av en Azure-tjänst enligt [villkoren för onlinetjänster](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Precis som med alla kognitiva tjänster bör utvecklare som använder tjänsten Formulärrecenser vara medvetna om Microsofts principer för kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabbstart](quickstarts/curl-train-extract.md) för att komma igång med [API:erna för formulärmedkänningsreformat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
