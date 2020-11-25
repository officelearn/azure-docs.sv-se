---
title: 'Snabb start: träna med etiketter med hjälp av REST API och python-formulär tolken'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder funktionen för formulär igenkänning med etiketten data med REST API och python för att träna en anpassad modell.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: f944a793d721e93d818723eae25a9ce80d9c15bc
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005101"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Träna en formulär igenkännings modell med etiketter med hjälp av REST API och python

I den här snabb starten använder du formulär tolken REST API med python för att träna en anpassad modell med manuellt märkta data. Mer information om den här funktionen finns i avsnittet [träna med etiketter](../overview.md#train-with-labels) i översikten.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En uppsättning av minst sex formulär av samma typ. Du använder dessa data för att träna modellen och testa ett formulär. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabb starten. Ladda ned och extrahera *sample_data.zip*. Ladda upp utbildnings-filerna till roten för en Blob Storage-behållare i ett Azure Storage konto med standard prestanda nivå.

> [!NOTE]
> I den här snabb starten används fjärrdokument som används av URL: en. Om du vill använda lokala filer i stället, se [referens dokumentationen för v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) och [referens dokumentation för v 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/TrainCustomModelAsync).

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Konfigurera tränings data

Härnäst måste du konfigurera nödvändiga indata. Funktionen märkta data har särskilda inmatnings krav utöver vad som behövs för att träna en anpassad modell utan etiketter.

Kontrol lera att alla utbildnings dokument har samma format. Om du har formulär i flera format ordnar du dem i undermappar efter format. När du tränar måste du dirigera API:et till en undermapp.

För att kunna träna en modell med märkta data behöver du följande filer som indata i undermappen. Du får lära dig hur du skapar de här filerna nedan.

* **Käll formulär** – formulär att extrahera data från. De typer som stöds är JPEG, PNG, PDF eller TIFF.
* **OCR** -presentationsfiler – dessa är JSON-filer som beskriver storlekarna och positionerna för all läsbar text i varje käll formulär. Du använder formatet för formulär tolkens layout för att skapa dessa data. 
* **Etikettfiler** – dessa är JSON-filer som beskriver de data etiketter som en användare har angett manuellt.

Alla dessa filer bör ha samma undermapp och ha följande format:

* input_file1.pdf 
* input_file1.pdf.ocr.jspå
* input_file1.pdf.labels.jspå 
* input_file2.pdf 
* input_file2.pdf.ocr.jspå
* input_file2.pdf.labels.jspå
* ...

> [!TIP]
> När du etiketterar formulär med hjälp av formulär tolkens [exempel etikett verktyg](./label-tool.md)skapar verktyget dessa etiketter och OCR-filer automatiskt.

### <a name="create-the-ocr-output-files"></a>Skapa filer för OCR-utdata

Du behöver filer med OCR-resultat för att tjänsten ska kunna beakta motsvarande indatafiler för etiketterad utbildning. Följ stegen nedan om du vill hämta OCR-resultat för ett angivet käll formulär:

1. Anropa API för **[analys av layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** i Read layout-behållaren med indatafilen som en del av begär ande texten. Spara ID: t som finns i svarets **Åtgärds plats** rubrik.
1. Anropa API: t för att **[analysera layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** med ÅTGÄRDS-ID från föregående steg.
1. Hämta svaret och skriv innehållet till en fil. För varje käll formulär ska motsvarande OCR-fil ha det ursprungliga fil namnet bifogad med `.ocr.json` . OCR-JSON-utdata ska ha följande format. I [exempel-OCR-filen](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) finns ett fullständigt exempel. 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>Skapa etikettfiler

Etikettfiler innehåller nyckel värdes kopplingar som en användare har angett manuellt. De behövs för etiketterad data träning, men alla källfiler måste ha motsvarande etikett fil. Källfiler utan etiketter kommer att behandlas som vanliga utbildnings dokument. Vi rekommenderar fem eller fler märkta filer för tillförlitlig utbildning. Du kan använda ett GRÄNSSNITTs verktyg som [exempel verktyget](./label-tool.md) för att generera dessa filer.

När du skapar en etikett fil kan du välja att ange exakta regioner &mdash; för värdena i dokumentet. Detta ger inlärningen ännu högre noggrannhet. Regionerna är formaterade som en uppsättning av åtta värden som motsvarar fyra X, Y-koordinater: övre vänstra, övre högra, nedre högra och nedre vänstra. Koordinaternas värden är mellan noll och ett, skalas till sidans dimensioner.

För varje käll formulär bör motsvarande etikett fil ha det ursprungliga fil namnet bifogad med `.labels.json` . Etikett filen ska ha följande format. Se [exempel etikett filen](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) för ett fullständigt exempel.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Du kan bara använda en etikett för varje text element och varje etikett kan bara tillämpas en gång per sida. Du kan inte använda en etikett på flera sidor.


## <a name="train-a-model-using-labeled-data"></a>Träna en modell med hjälp av märkta data

Om du vill träna en modell med märkta data anropar du det **[anpassade modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API: et för träna genom att köra följande python-kod. Innan du kör koden gör du följande ändringar:

1. Ersätt `<Endpoint>` med slut punkts-URL: en för formulär igenkännings resursen.
1. Ersätt `<SAS URL>` med Azure Blob Storage-behållarens URL för signatur för delad åtkomst (SAS). Hämta SAS-URL: en genom att öppna Microsoft Azure Storage Explorer, högerklicka på behållaren och välja **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Ersätt `<Blob folder name>` med mappnamnet i BLOB-behållaren där indata finns. Om dina data finns i roten lämnar du detta tomt och tar bort `"prefix"` fältet från bröd texten i HTTP-begäran.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.2/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>Hämta utbildnings resultat

När du har startat träna-åtgärden använder du det returnerade ID: t för att hämta status för åtgärden. Lägg till följande kod längst ned i python-skriptet. Detta använder ID-värdet från inlärnings anropet i ett nytt API-anrop. Inlärnings åtgärden är asynkron, så det här skriptet anropar API: n med jämna mellanrum tills inlärnings statusen har slutförts. Vi rekommenderar ett intervall på en sekund.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

När övnings processen har slutförts får du ett `201 (Success)` svar med JSON-innehåll som följande. Svaret har förkort ATS för enkelhetens skull.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Kopiera `"modelId"` värdet för användning i följande steg.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

När processen har slutförts får du ett `202 (Success)` svar med JSON-innehåll i följande format. Svaret har förkort ATS för enkelhetens skull. Huvud nyckel/värde-associationerna finns i `"documentResults"` noden. `"selectionMarks"`Noden (i för hands versionen av v 2.1) visar varje markerings märke (kryss markering) och om dess status är "markerad" eller "omarkerad". Layout-API-resultatet (innehållet och positionerna för all text i dokumentet) finns i `"readResults"` noden.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
            ]
          }
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/14/words/0",
                  "#/readResults/0/lines/14/words/1"
                ]
              },
              ...
            ]
          },
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v-2"></a>[v 2](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
        ], 
        "selectionMarks": [
          {
            "boundingBox": [
              3.9737,
              3.7475,
              4.1693,
              3.7475,
              4.1693,
              3.9428,
              3.9737,
              3.9428
            ],
            ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/12/words/0",
                  "#/readResults/0/lines/12/words/1"
                ]
              },
              ...
            ]
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>Förbättra resultaten

Undersök `"confidence"` värdena för varje nyckel/värde-resultat under `"documentResults"` noden. Du bör också titta på förtroende poängen i `"readResults"` noden, som motsvarar layout åtgärden. Resultatet av layouten påverkar inte säkerheten för extrahering av nyckel/värde, så du bör kontrol lera båda.
* Om förtroende poängen för layouten är låg, försöker du förbättra kvaliteten på dina inaktuella dokument (se [krav](../overview.md#input-requirements)för inläsning).
* Om förtroende poängen för extraherings åtgärden för nyckel/värde är låg, se till att dokumenten som analyseras är av samma typ som dokumenten som används i inlärnings uppsättningen. Om dokumenten i inlärnings uppsättningen har variationer i utseendet kan du överväga att dela upp dem i olika mappar och träna en modell för varje variation.

### <a name="avoid-cluttered-labels"></a>Undvik röriga etiketter

Ibland när du använder olika etiketter inom samma textrad, kan tjänsten sammanfoga dessa etiketter till ett fält. I en adress kan du t. ex. namnge ort, delstat och post nummer som olika fält, men under förutsägelse de fälten identifieras inte separat.

Vi förstår att det här scenariot är viktigt för våra kunder och vi arbetar på att förbättra det i framtiden. För närvarande rekommenderar vi våra användare att märka flera färdiga fält som ett fält och sedan separera villkoren i en efter bearbetning av extraherings resultatet.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder formulär tolken REST API med python för att träna en modell med manuellt märkta data. Sedan läser du API-referensens dokumentation för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
