---
title: 'Snabbstart: Extrahera text- och layoutinformation med Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du REST-API:et för formmedkärelayout layout med Python för att läsa text- och tabelldata från formulären.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482284"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Snabbstart: Extrahera text- och layoutinformation med rest-APIn för formulärmed Python

I den här snabbstarten använder du AZURE Form Recognizer REST API med Python för att extrahera information om textlayout och tabelldata från formulärdokument.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- Ett formulärdokument. Du kan hämta en bild från [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analysera formulärlayouten

Om du vill börja analysera layouten anropar du **[API:et analysera layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** med python-skriptet nedan. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick med din prenumeration på Formulär recognizer.
1. Ersätt `<path to your form>` med sökvägen till det lokala formulärdokumentet.
1. Ersätt `<subscription key>` med prenumerationsnyckeln som du kopierade från föregående steg.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Spara koden i en fil med ett py-tillägg. Till exempel *form-recognizer-layout.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Till exempel `python form-recognizer-layout.py`.

Du får ett `202 (Success)` svar som innehåller ett **funktionsställehuvud** som skriptet skriver ut på konsolen. Det här huvudet innehåller ett åtgärds-ID som du kan använda för att fråga status för den asynkrona åtgärden och hämta resultaten. I följande exempelvärde är `operations/` strängen efter operations-ID.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Hämta layoutresultaten

När du har **anropat API:et för analysera layout** anropar du **[API:et Hämta analyslayoutresultat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** för att få status för åtgärden och extraherade data. Lägg till följande kod längst ned i Python-skriptet. Den här koden använder åtgärds-ID-värdet i ett nytt API-anrop. Det här skriptet anropar API:et med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund eller mer.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Spara skriptet.
1. Använd kommandot `python` igen för att köra exemplet. Till exempel `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Granska svaret

Skriptet skriver ut svar på konsolen tills åtgärden **Analysera layout** är klar. Sedan kommer det att skriva ut extraherade data i JSON-format. Noden `"readResults"` innehåller varje textrad med respektive markeringsram placering på sidan. Fältet `"pageResults"` visar varje text i tabeller, var och en med sin radkolumnskoordinat.

Se följande fakturabild och motsvarande JSON-utdata. Produktionen har förkortats för enkelhetens skull.

> [!div class="mx-imgBorder"]
> ![Contoso fakturadokument med en tabell](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du REST-API:et för formulärmed Python för att extrahera textlayouten för en faktura. Se sedan referensdokumentationen för att utforska API:et för formulärre recognizeer mer ingående.

> [!div class="nextstepaction"]
> [DOKUMENTATION FÖR REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
