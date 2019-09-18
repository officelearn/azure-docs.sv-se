---
title: 'Snabbstart: Extrahera indata med hjälp av typografiska formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med sväng för att extrahera data från bilder av försäljnings kvitton.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: badd674030ec014f2e70050c3c45599a26b17882
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073815"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Snabbstart: Extrahera indata med formulär tolken REST API med vändning

I den här snabb starten använder du Azures formulär igenkännings REST API med sväng för att extrahera och identifiera relevant information i försäljnings kvitton.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här snabb starten måste du ha:
- Åtkomst till för hands versionen av formulär igenkännings begränsad åtkomst. För att få åtkomst till förhands granskningen, fyller du i och skickar [formulär tolken formulär för åtkomst förfrågan](https://aka.ms/FormRecognizerRequestAccess) .
- [spiralen](https://curl.haxx.se/windows/) är installerad.
- En URL för en avbildning av ett kvitto. Du kan använda en [exempel bild](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) för den här snabb starten.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analysera ett kvitto

Om du vill börja analysera ett kvitto anropar du API: et för att **analysera kvitto** med hjälp av kommandot vänd nedan. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<your receipt URL>` med URL-adressen för en kvitto avbildning.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Du får ett `202 (Success)` svar som innehåller en **Åtgärds plats** rubrik. Värdet för den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter status för åtgärden och hämta resultatet. I följande exempel är strängen efter `operations/` åtgärds-ID: t.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Hämta kvitto resultat

När du har anropat API för att **analysera kvitto** anropar du API: t **Get kvitto resultat** för att hämta status för åtgärden och de extraherade data.

1. Ersätt `<operationId>` med åtgärds-ID: t från föregående steg.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Granska svaret

Du får ett `200 (Success)` svar med JSON-utdata. Det första fältet, `"status"`anger status för åtgärden. Om åtgärden har slutförts `"recognitionResults"` innehåller fältet alla rader med text som har extraherats från inleveransen `"understandingResults"` och fältet innehåller nyckel/värde-information för de mest relevanta delarna av kvittot. Om åtgärden inte är slutförd kommer värdet för `"status"` att vara `"Running"` eller `"NotStarted"`, och du bör anropa API: et igen, antingen manuellt eller genom ett skript. Vi rekommenderar ett intervall på en sekund eller flera anrop mellan anrop.

Se följande kvitto avbildning och dess motsvarande JSON-utdata. Utdatan har kort ATS för läsbarhet.

![Ett kvitto från contoso Store](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med sväng för att extrahera innehållet i en försäljnings leverans. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
