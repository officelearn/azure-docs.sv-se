---
title: 'Snabbstart: Extrahera kvitto data med hjälp av Python - formuläret Igenkännande'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda formuläret Igenkännande REST-API med Python för att extrahera data från bilder av försäljningskvitton.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 8bd4d441859df6dbb36f594d8423eefd84274ec4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592546"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Snabbstart: Extrahera kvitto data med hjälp av formuläret Igenkännande REST-API med Python

I den här snabbstarten ska du använda Azure formuläret Igenkännande REST-API med Python för att extrahera och identifiera relevant information i försäljningskvitton.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här snabbstarten måste du ha:
- Åtkomst till förhandsversionen av formuläret Igenkännande begränsad åtkomst. För att få åtkomst till förhandsversionen kan fylla i och skicka den [formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret.
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En URL för en avbildning av en inleverans. Du kan använda en [exempelbild](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) för den här snabbstarten.

## <a name="create-a-form-recognizer-resource"></a>Skapa en resurs för formuläret Igenkännande

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analysera ett kvitto

Om du vill börja analysera ett kvitto måste du anropa den **analysera kvitto** API med Python-skriptet nedan. Innan du kör skriptet gör dessa ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick från din prenumerationsnyckel för formuläret Igenkännande. Du hittar den på formuläret Igenkännande resursen **översikt** fliken.
1. Ersätt `<your receipt URL>` med URL-adressen för en kvitto-avbildning.
1. Ersätt `<subscription key>` med prenumerationsnyckel som du kopierade i föregående steg.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Spara koden i en fil med filnamnstillägget .py. Till exempel *formulär-Igenkännande-receipts.py*.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python form-recognizer-receipts.py`.

Du får en `202 (Success)` svar som innehåller en **åtgärden plats** rubriken, som skriptet skrivs till konsolen. Den här rubriken innehåller en åtgärds-ID som du kan använda för att fråga efter status för åtgärden och få analysresultat. I följande exempelvärde, strängen efter `operations/` är åtgärden-ID.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Få kvitto resultat

När du har påbörjat den **analysera kvitto** API: et kan du anropa den **hämta kvitto resultatet** API för att hämta status för åtgärden och den extraherade data. Lägg till följande kod längst ned på Python-skriptet. Detta tar fram åtgärden ID-värde och skickar det till ett nytt API-anrop. Åtgärden är asynkron så att det här skriptet anropar API: et med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund eller mer.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Spara skriptet.
1. Återigen att använda den `python` kommando för att köra exemplet. Till exempel `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Granska svaret

Skriptet skrivs svar till konsolen förrän analysera åtgärden har slutförts. Sedan, skrivs den extraherade textdata i JSON-format. Den `"recognitionResults"` fältet innehåller varje rad med text som har extraherats från mottagandet, och `"understandingResults"` fältet innehåller nyckel/värde-information för de mest relevanta delarna av kvittot.

Se följande kvitto avbildningen och dess motsvarande JSON-utdata. Utdata har kortats ned för läsbarhet.

![Ett kvitto från Contoso store](../media/contoso-receipt.png)

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

I den här snabbstarten använde du formuläret Igenkännande REST API med Python du tränar en modell och kör den i ett exempelscenario. Därefter finns i referensdokumentationen för att utforska formuläret Igenkännande API: et i mer detalj.

> [!div class="nextstepaction"]
> [Referensdokumentation för REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
