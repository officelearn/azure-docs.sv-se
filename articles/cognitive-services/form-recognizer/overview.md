---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Med Azure formulär igenkännings tjänsten kan du identifiera och extrahera nyckel/värde-par och tabell data från dina formulär dokument, samt extrahera större information från försäljnings kvitton och visitkort.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: automatisk data bearbetning, dokument bearbetning, automatisk data inmatning, formulär bearbetning
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318967"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure formulär igenkänning är en kognitiv tjänst som gör att du kan skapa automatiserad data bearbetnings program vara med hjälp av Machine Learning-teknik. Identifiera och extrahera text, nyckel/värde-par och tabell data från formulär dokument &mdash; tjänsten visar strukturerade data som innehåller relationerna i original filen. Du får snabbt korrekta resultat som skräddarsys efter ditt eget innehåll utan att du behöver göra en kraftig eller omfattande data vetenskaps expert. Använd formulär igenkänning för att automatisera data inmatning i dina program.

Formulär tolken består av anpassade dokument bearbetnings modeller, de fördefinierade inbetalnings-och visitkorts modellerna och layout-API: et. Du kan anropa formulär igenkännings modeller genom att använda ett REST API-eller klient biblioteks-SDK: er för att minska komplexiteten och integrera dem i arbets flödet eller programmet.

Formulär tolken består av följande tjänster:
* **[Anpassade modeller](#custom-models)** – extrahera nyckel/värde-par och tabell data från formulär. De här modellerna tränas med dina egna data, så de skräddarsys efter dina formulär.
* **[Färdiga modeller](#prebuilt-models)** – extrahera data från unika formulär typer med hjälp av förinställda modeller. För närvarande finns det färdiga modeller för försäljnings kvitton och visitkort på engelska.
* **[Layout-API](#layout-api)** – Extrahera text och tabell strukturer, tillsammans med deras gränser för avgränsnings rutor, från dokument.

## <a name="custom-models"></a>Anpassade modeller

Formulär igenkännings anpassade modeller tränar dina egna data och du behöver bara fem exempel inmatnings formulär att starta. En tränad dokument bearbetnings modell kan spara strukturerade data som innehåller relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

Du har följande alternativ när du tränar anpassade modeller: utbildning med märkta data och utan etiketterade data.

### <a name="train-without-labels"></a>Träna utan etiketter

Som standard använder formulär tolken oövervakad inlärning för att förstå layouten och relationerna mellan fält och poster i formulären. När du skickar in dina indata-formulär, identifierar algoritmen formulären efter typ, identifierar vilka nycklar och tabeller som finns och associerar värden till nycklar och poster i tabeller. Detta kräver inte manuella data etiketter eller intensiv kodning och underhåll, och vi rekommenderar att du provar den här metoden först.

Se [skapa en tränings data uppsättning](./build-training-data-set.md) för tips om hur du samlar in utbildnings dokument.

### <a name="train-with-labels"></a>Träna med etiketter

När du tränar med märkta data övervakas modellen för att extrahera värden av intresse med hjälp av de märkta formulär som du anger. Detta resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.

Formulär tolken använder [layout-API](#layout-api) för att lära dig de förväntade storlekarna och positionerna för de utskrivna och handskrivna text element Sedan använder den användardefinierade etiketter för att lära sig nyckel/värde-associationerna i dokumenten. Vi rekommenderar att du använder fem manuellt märkta formulär av samma typ för att komma igång när du tränar en ny modell och lägger till mer märkta data som behövs för att förbättra modellens noggrannhet.

## <a name="prebuilt-models"></a>Fördefinierade modeller

Formulär tolken innehåller också färdiga modeller för automatisk data bearbetning av unika formulär typer.

### <a name="prebuilt-receipt-model"></a>Fördefinierad kvitto modell

Fördefinierad kvitto modell används för att läsa kvitton på engelska försäljning från Australien, Kanada, Storbritannien, Indien och USA typ som &mdash; används av restauranger, gas stationer, åter försäljare och så vidare. Den här modellen hämtar viktig information, till exempel tid och datum för transaktionen, handels information, moms belopp, rad artiklar, total summor och mer. Dessutom tränas den förskapade kvitto modellen att identifiera och returnera all text i ett kvitto. Mer information finns i Guide för [kvitton](./concept-receipts.md) .

![exempel kvitto](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Fördefinierad visitkorts modell

Med visitkorts modellen kan du extrahera information som personens namn, befattning, adress, e-post, företags nummer och telefonnummer från visitkort på engelska. Mer information finns i konceptuell guide för [visitkort](./concept-business-cards.md) .

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
  * Extrahera data från försäljnings kvitton
    * [Extrahera kvitto data – sväng](quickstarts/curl-receipts.md)
    * [Extrahera kvitto data – python](quickstarts/python-receipts.md)
  * Extrahera data från visitkort
    * [Extrahera visitkorts data – python](quickstarts/python-business-cards.md)
  * Extrahera text-och tabell struktur från formulär
    * [Extrahera layoutinformation – python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Granska REST-API: erna

Du använder följande API: er för att träna modeller och extrahera strukturerade data från formulär.

|Name |Beskrivning |
|---|---|
| **Träna anpassad modell**| Träna en ny modell för att analysera dina formulär genom att använda fem formulär av samma typ. Ange parametern _useLabelFile_ för `true` att träna med manuellt märkta data. |
| **Analysera formulär** |Analysera ett enda dokument som skickas in som en ström för att extrahera text, nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| **Analysera inleverans** |Analysera ett enda kvitto dokument för att extrahera viktig information och annan kvitto text.|
| **Visitkort för analys** |Analysera ett visitkort för att extrahera viktig information och text.|
| **Analysera layout** |Analysera layouten för ett formulär för att extrahera text-och tabell strukturen.|

Mer information får du genom att utforska [REST API referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Om du är bekant med en tidigare version av API: n läser du artikeln [Nyheter](./whats-new.md) och lär dig mer om de senaste ändringarna.

## <a name="input-requirements"></a>Krav för indatamängd

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder formulär tolknings tjänsten vara medvetna om Microsofts principer för kund information. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabb start för klient bibliotek](quickstarts/client-library.md) för att komma igång med att skriva en app för formulär bearbetning med formulär tolken på valfritt språk.