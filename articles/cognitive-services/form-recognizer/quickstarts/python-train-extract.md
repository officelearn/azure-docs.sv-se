---
title: 'Snabb start: träna en modell och extrahera formulär data med hjälp av REST API med python-formulär tolken'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med python för att träna en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 5739827f1f6cf65cfe5c4aa8303c9f37eb569854
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264399"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Snabb start: träna en formulär igenkännings modell och extrahera formulär data med hjälp av REST API med python

I den här snabb starten använder du Azures formulär tolken REST API med python för att träna och Poäng Forms för att extrahera nyckel/värde-par och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav
För att slutföra den här snabb starten måste du ha:
- Åtkomst till för hands versionen av formulär igenkännings begränsad åtkomst. För att få åtkomst till förhands granskningen, fyller du i och skickar [formulär tolken formulär för åtkomst förfrågan](https://aka.ms/FormRecognizerRequestAccess) .
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En uppsättning minst fem formulär av samma typ. Du kommer att använda dessa data för att träna modellen. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabb starten. Ladda upp utbildnings filen till roten för en Blob Storage-behållare i ett Azure Storage-konto.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en formulär igenkännings modell

Först behöver du en uppsättning utbildnings data i en Azure Storage BLOB-behållare. Du bör ha minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur som dina viktigaste indata. Eller så kan du använda ett enda tomt formulär med två ifyllda formulär. Det tomma formulärets fil namn måste innehålla ordet "Empty". Se [skapa en tränings data uppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta samman dina tränings data.

Om du vill träna en formulär tolknings modell med dokumenten i din Azure Blob-behållare anropar du **träna** API genom att köra följande python-kod. Innan du kör koden gör du följande ändringar:

1. Ersätt `<Endpoint>` med slut punkts-URL: en för formulär igenkännings resursen.
1. Ersätt `<Subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `<SAS URL>` med Azure Blob Storages behållares URL för signatur för delad åtkomst (SAS). Hämta SAS-URL: en genom att öppna Microsoft Azure Storage Explorer, högerklicka på behållaren och välja **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Det ska ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-recognize-Train.py*.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python form-recognize-train.py`.

Du får ett `200 (Success)`-svar med följande JSON-utdata:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Observera värdet `"modelId"`. Du behöver den för att utföra följande steg.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrahera nyckel/värde-par och tabeller från formulär

Härnäst ska du analysera ett dokument och extrahera nyckel/värde-par och tabeller från det. Anropa **modell-** API: et för analys genom att köra python-skriptet som följer. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din prenumerations nyckel för formulär igenkänning. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<path to your form>` med fil Sök vägen för ditt formulär (till exempel C:\temp\file.pdf). I den här snabb starten kan du använda filerna under mappen **test** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Ersätt `<modelID>` med modell-ID: t som du fick i föregående avsnitt.
1. Ersätt `<file type>` med filtypen. Typer som stöds: `application/pdf`, `image/jpeg`, `image/png`.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<path to your form>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-recognize-Analyze.py*.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i JSON. Den representerar nyckel/värde-par och tabeller som extraheras från formuläret:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med python för att träna en modell och köra den i ett exempel scenario. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://aka.ms/form-recognizer/api)
