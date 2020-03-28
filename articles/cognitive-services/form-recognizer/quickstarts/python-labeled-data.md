---
title: 'Snabbstart: Träna med etiketter med REST API och Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder funktionen Formulärmedkänningsankänningsdata med REST API och Python för att träna en anpassad modell.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 5469c2512e133d17e4d18cebb64ab9e2a21b1f83
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482303"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Träna en formulärmedkänningsmodell med etiketter med REST API och Python

I den här snabbstarten använder du REST-API:et för formulärmed Python för att träna en anpassad modell med manuellt märkta data. Se avsnittet [Träna med etiketter](../overview.md#train-with-labels) i översikten om du vill veta mer om den här funktionen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En uppsättning av minst sex former av samma typ. Du kommer att använda dessa data för att träna modellen och testa ett formulär. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten. Ladda upp utbildningsfilerna till roten för en blob-lagringsbehållare i ett Azure Storage-konto.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Ställ in träningsdata

Därefter måste du ställa in de indata som krävs. Den märkta datafunktionen har särskilda inmatningskrav utöver de som behövs för att träna en anpassad modell. 

Kontrollera att alla träningsdokument är av samma format. Om du har formulär i flera format ordnar du dem i undermappar baserat på gemensamt format. När du tränar måste du dirigera API:et till en undermapp.

För att kunna träna en modell med hjälp av märkta data behöver du följande filer som indata i undermappen. Du får lära dig hur du skapar filen nedan.

* **Källformulär** – formulären att extrahera data från. Typer som stöds är JPEG, PNG, BMP, PDF eller TIFF.
* **OCR-layoutfiler** - JSON-filer som beskriver storleken och positionerna för all läsbar text i varje källformulär. Du ska använda API:et för layout för formulärreform för att generera dessa data. 
* **Etikettfiler** - JSON-filer som beskriver dataetiketter som en användare har angett manuellt.

Alla dessa filer bör uppta samma undermapp och vara i följande format:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> När du märker formulär med [exempeletikettverktyget](./label-tool.md)Formulär igen skapar verktyget dessa etikett- och OCR-layoutfiler automatiskt.

### <a name="create-the-ocr-output-files"></a>Skapa OCR-utdatafiler

Du behöver OCR-resultatfiler för att tjänsten ska kunna överväga motsvarande indatafiler för märkt utbildning. Så här hämtar du OCR-resultat för ett visst källformulär:

1. Anropa **[Api:et analysera layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** på den lästa layoutbehållaren med indatafilen som en del av förfråsättningstexten. Spara ID:t som hittades i svarets **funktionsplatshuvud.**
1. Anropa **[API:et hämta analyslayoutresultat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** med hjälp av åtgärds-ID från föregående steg.
1. Hämta svaret och skriv innehållet till en fil. För varje källformulär ska motsvarande OCR-fil ha det `.ocr.json`ursprungliga filnamnet bifogat med . OCR JSON-utdata bör ha följande format. Se [exempel på OCR-fil](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) för ett fullständigt exempel. 

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

### <a name="create-the-label-files"></a>Skapa etikettfilerna

Etikettfiler innehåller nyckelvärdesassociationer som en användare har angett manuellt. De behövs för etikettdatautbildning, men alla källfiler behöver inte ha en motsvarande etikettfil. Källfiler utan etiketter kommer att behandlas som vanliga utbildningsdokument. Vi rekommenderar fem eller fler märkta filer för tillförlitlig utbildning.

När du skapar en etikettfil kan&mdash;du också ange regioners exakta positioner för värden i dokumentet. Detta kommer att ge utbildningen ännu högre noggrannhet. Regionerna är formaterade som en uppsättning med åtta värden som motsvarar fyra X,Y-koordinater: övre vänstra, övre högra, nedre högra och nedre vänstra. Koordinatvärdena är mellan noll och ett, skalas till sidans dimensioner.

För varje källformulär ska motsvarande etikettfil ha det ursprungliga `.labels.json`filnamnet bifogat med . Etikettfilen bör ha följande format. Se [exempeletikettfilen](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) för ett fullständigt exempel.

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

> [!NOTE]
> Du kan bara använda en etikett på varje textelement, och varje etikett kan bara användas en gång per sida. Du kan för närvarande inte använda en etikett på flera sidor.


## <a name="train-a-model-using-labeled-data"></a>Träna en modell med hjälp av etiketterade data

Om du vill träna en modell med märkta data anropar du **[API:et för anpassad tågmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** genom att köra följande python-kod. Innan du kör koden gör du följande ändringar:

1. Ersätt `<Endpoint>` med slutpunkts-URL:en för formulärmedkänningsresursen.
1. Ersätt `<SAS URL>` med Azure Blob-lagringsbehållarens SAS-URL (Shared Access Signature). Om du vill hämta SAS-URL:en öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrollera att behörigheterna **Läs** och **Lista** är markerade och klicka på **Skapa**. Kopiera sedan värdet i **avsnittet URL.** Det bör ha `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formen: .
1. Ersätt `<Blob folder name>` med mappnamnet i blob-behållaren där indata finns. Om dina data finns i roten lämnar du `"prefix"` det här tomt och tar bort fältet från http-begärans brödtext.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =  {
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

## <a name="get-training-results"></a>Få träningsresultat

När du har startat tågåtgärden använder du det returnerade ID:t för att få status för åtgärden. Lägg till följande kod längst ned i Python-skriptet. Detta använder ID-värdet från träningsanropet i ett nytt API-anrop. Utbildningsåtgärden är asynkron, så det här skriptet anropar API:et med jämna mellanrum tills träningsstatusen har slutförts. Vi rekommenderar ett intervall på en sekund eller mer.

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

När utbildningsprocessen är klar får du `201 (Success)` ett svar med JSON-innehåll som följande. Svaret har förkortats för enkelhetens skull.

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

När processen är klar får du `202 (Success)` ett svar med JSON-innehåll i följande format. Svaret har förkortats för enkelhetens skull. Huvudnyckel-/värdeassociationerna finns i noden. `"documentResults"` Layout-API-resultaten (innehållet och positionerna för all text `"readResults"` i dokumentet) finns i noden.

```json
{ 
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
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
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
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
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
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Förbättra resultaten

Undersök `"confidence"` värdena för varje nyckel/värderesultat under noden. `"documentResults"` Du bör också titta på `"readResults"` förtroendepoängen i noden, som motsvarar layoutåtgärden. Konfidensen för layoutresultaten påverkar inte förtroendet för resultaten för nyckel-/värdeutvinning, så du bör kontrollera båda.
* Om konfidenspoängen för layoutåtgärden är låga kan du försöka förbättra kvaliteten på indatadokumenten (se [Indatakrav).](../overview.md#input-requirements)
* Om konfidenspoängen för nyckel-/värdeextraheringsåtgärden är låga, se till att de dokument som analyseras är av samma typ som dokument som används i utbildningsuppsättningen. Om dokumenten i utbildningsuppsättningen har variationer i utseende kan du överväga att dela upp dem i olika mappar och träna en modell för varje variant.

### <a name="avoid-cluttered-labels"></a>Undvik röriga etiketter

Ibland när du använder olika etiketter inom samma textrad kan tjänsten sammanfoga dessa etiketter till ett fält. I en adress kan du till exempel märka ort, delstat och postnummer som olika fält, men under förutsägelse känns inte dessa fält igen separat.

Vi förstår att detta scenario är viktigt för våra kunder, och vi arbetar på att förbättra detta i framtiden. För närvarande rekommenderar vi våra användare att märka flera röriga fält som ett fält och sedan separera villkoren i en efterbearbetning av extraheringsresultaten.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder REST-API:et för formulärmed Python för att träna en modell med manuellt märkta data. Se sedan [API-referensdokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) för att utforska API:et för formulärre recognizeer mer ingående.
