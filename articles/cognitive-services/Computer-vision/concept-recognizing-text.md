---
title: Optisk tecken läsning (OCR) – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör optisk tecken läsning (OCR) av bilder och dokument med utskriven text och handskriven text med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cb931d0b9c3dd4d3fa0fa69f69f5f90fc37ea8f6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929201"
---
# <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Azures API för visuellt innehåll innehåller OCR-funktioner (optisk tecken läsning) som extraherar tryckt eller handskriven text från bilder. Du kan extrahera text från bilder, till exempel foton av licens plattor eller behållare med serie nummer, samt från dokument fakturor, fakturor, ekonomiska rapporter, artiklar med mera. 

## <a name="read-api"></a>Läs-API 

Visuellt innehåll [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) är Azures senaste OCR-teknik som extraherar tryckt text (på flera språk), handskriven text (endast engelska), siffror och valuta symboler från bilder och PDF-dokument med flera sidor. Det är optimerat för att extrahera text från text – tunga bilder och PDF-dokument med flera sidor med blandade språk. Det stöder identifiering av både skriven och handskriven text i samma bild eller dokument.

![Hur OCR konverterar bilder och dokument till strukturerade utdata med extraherad text](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Krav för indatamängd
Läs-API: s **Läs** åtgärd tar bilder och dokument som inaktuella. De har följande krav:

* Fil format som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF och TIFF bearbetas upp till 2000 sidor. För prenumeranter på den kostnads fria nivån bearbetas bara de första två sidorna.
* Fil storleken måste vara mindre än 50 MB och dimensioner minst 50 x 50 bild punkter och högst 10000 x 10000 bild punkter.
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.

> [!NOTE]
> **Språk information** 
>
> [Läs åtgärden](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) har en valfri begär ande parameter för språk. Det här är språk koden BCP-47 för texten i dokumentet. Läs stöder automatisk språk identifiering och flerspråkiga dokument, så du behöver bara ange en språkkod om du vill tvinga dokumentet att bearbetas som det specifika språket.

## <a name="the-read-operation"></a>Läs åtgärden

[Läs åtgärden](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) tar en bild eller ett PDF-dokument som indata och extraherar text asynkront. Anropet returnerar med ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det åtgärds-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Åtgärds plats | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-read-results-operation"></a>Åtgärden hämta Läs resultat

Det andra steget är att anropa åtgärden [Hämta Läs resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Den här åtgärden utförs som indatamängden för det åtgärds-ID som skapades av Läs åtgärden. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 1 till 2 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: åtgärden har inte startats. |
| |  | körs: åtgärden bearbetas. |
| |  | misslyckades: åtgärden misslyckades. |
| |  | lyckades: åtgärden har slutförts. |

> [!NOTE]
> Den kostnads fria nivån begränsar begär ande frekvensen till 20 anrop per minut. På den betalda nivån tillåts 10 begär Anden per sekund (RPS) som kan ökas på begäran. Använd support kanalen för Azure eller ditt konto team för att begära en högre RPS-taxa (Request/Second).

När fältet **status** har värdet **lyckades** innehåller JSON-svaret det extraherade text innehållet från din avbildning eller ditt dokument. JSON-svaret underhåller de ursprungliga rad grupperna av identifierade ord. Den innehåller de extraherade text raderna och deras avgränsnings Rute koordinater. Varje textrad innehåller alla extraherade ord med deras koordinater och förtroende poäng.

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Se följande exempel på ett lyckat JSON-svar:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Följ snabb starten för [extrahering av skrivna och handskrivna text](./QuickStarts/CSharp-hand-text.md) för att implementera OCR med C# och REST API.

## <a name="language-support"></a>Stöd för språk

### <a name="printed-text"></a>Utskriven text
[API: et Read 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) stöder extrahering av utskriven text på engelska, spanska, tyska, franska, italienska, portugisiska och nederländska språk. 

Den [offentliga för hands versionen av Read 3,1 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) lägger till stöd för förenklad kinesiska. Om ditt scenario kräver stöd för fler språk, se avsnittet [OCR-API](#ocr-api) . 

Se de [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) för den fullständiga listan över språk som stöds av OCR.

### <a name="handwritten-text"></a>Handskriven text
Läs åtgärden stöder för närvarande extrahering av handskriven text exklusivt på engelska.

## <a name="integration-options"></a>Integrations alternativ

### <a name="use-the-rest-api-or-client-sdk"></a>Använd REST API-eller klient-SDK: n
Filen [Read 3. x REST API](./QuickStarts/CSharp-hand-text.md) är det bästa alternativet för de flesta kunder på grund av enkel integrering och snabb produktivitet. Azure och Visuellt innehåll service hanterar skalning, prestanda, data säkerhet och efterlevnad för att möta kundernas behov.

### <a name="use-containers-for-on-premise-deployment"></a>Använda behållare för lokal distribution
Med den [Read 2,0 Docker-behållaren (för hands version)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) kan du distribuera de nya OCR-funktionerna i din egen lokala miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="read-ocr-examples"></a>Läs OCR-exempel

### <a name="text-from-images"></a>Text från bilder

Följande Read API-utdata visar den extraherade texten från en bild med olika text vinklar, färger och teckensnitt.

![En bild av flera ord med olika färger och vinklar, med extraherad text listad](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text från dokument

Read API kan också ta PDF-dokument som inmatade.

![Ett faktura dokument med extraherad text listad](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Handskriven text

Läs åtgärden extraherar handskriven text från bilder (för närvarande endast på engelska).

![En bild av en handskriven anteckning med extraherad text listad](./Images/handwritten-example.png)

### <a name="printed-text"></a>Utskriven text

Läs åtgärden kan extrahera utskriven text på flera olika språk.

![En bild av en spansk Textbook, med extraherad text listad](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Dokument med blandat språk

Read API stöder bilder och dokument som innehåller flera olika språk, vanligt vis kallade blandade språk dokument. Det fungerar genom att klassificera varje text rad i dokumentet på det identifierade språket innan dess text innehåll extraheras.

![En bild av fraser på flera språk, med extraherad text listad](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR-API

[OCR-API: t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) använder en äldre igenkännings modell, stöder bara bilder och körs synkront, och returneras omedelbart med den identifierade texten. Se de [språk som stöds för OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) och sedan Read API.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder Läs-/OCR-tjänsterna vara medvetna om Microsofts principer för kund information. Mer information finns på sidan Cognitive Services på [Microsoft Trust Center](https://www.microsoft.com/trust-center/product-overview) .

> [!NOTE]
> Datorn Vison 2,0 RecognizeText-åtgärder håller på att bli föråldrad med den nya Read-API: n som beskrivs i den här artikeln. Befintliga kunder ska [övergå till att använda Läs åtgärder](upgrade-api-versions.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [läsa 3,0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Läs mer om den [offentliga för hands versionen av läs 3,1 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) med stöd för förenklad kinesiska.
- Följ snabb starten för att [Extrahera text](./QuickStarts/CSharp-hand-text.md) för att implementera OCR med C#, Java, java script eller python tillsammans med REST API.
