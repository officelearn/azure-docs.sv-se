---
title: 'Snabbstart: Träna en modell och extrahera formulärdata med REST API med Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du REST-API:et för formulärmed Python för att träna en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 66668f46595c22426984a02c489297e962d061d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118077"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Snabbstart: Träna en formulärmedkänningsmodell och extrahera formulärdata med hjälp av REST API med Python

I den här snabbstarten använder du AZURE Form Recognizer REST API med Python för att träna och göra poäng för att extrahera nyckelvärdespar och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En uppsättning av minst fem former av samma typ. Du kommer att använda dessa data för att träna modellen. Formulären kan vara av olika filtyper men måste vara av samma typ av dokument. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten. Ladda upp utbildningsfilerna till roten för en blob-lagringsbehållare i ett Azure Storage-konto.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en formulärakänningsmodell

Först behöver du en uppsättning utbildningsdata i en Azure Storage-blob-behållare. Du bör ha minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur som dina huvudsakliga indata. Du kan också använda ett enda tomt formulär med två ifyllda formulär. Det tomma formulärets filnamn måste innehålla ordet "tom". Se [Skapa en utbildningsdatauppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta ihop dina träningsdata.

> [!NOTE]
> Du kan använda den märkta datafunktionen för att manuellt märka vissa eller alla träningsdata i förväg. Detta är en mer komplex process men resulterar i en bättre utbildad modell. Läs avsnittet [Träna med etiketter](../overview.md#train-with-labels) i översikten om du vill veta mer.

Om du vill träna en formulärmedkänningsmodell med dokumenten i azure-blob-behållaren anropar du **[API:et för anpassad tågmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** genom att köra följande python-kod. Innan du kör koden gör du följande ändringar:

1. Ersätt `<SAS URL>` med Azure Blob-lagringsbehållarens SAS-URL (Shared Access Signature). Om du vill hämta SAS-URL:en öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrollera att behörigheterna **Läs** och **Lista** är markerade och klicka på **Skapa**. Kopiera sedan värdet i **avsnittet URL.** Det bör ha `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formen: .
1. Ersätt `<subscription key>` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `<endpoint>` med slutpunkts-URL:en för formulärmedkänningsresursen.
1. Ersätt `<Blob folder name>` med sökvägen till mappen i blob-lagring där formulären finns. Om formulären finns i behållarens rot lämnar du strängen tom.

    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
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
1. Spara koden i en fil med ett py-tillägg. Till exempel *form-recognizer-train.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Till exempel `python form-recognizer-train.py`.

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

När utbildningsprocessen är klar får du `201 (Success)` ett svar med JSON-innehåll som följande:

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

När processen är klar får du `200 (Success)` ett svar med JSON-innehåll i följande format. Svaret har förkortats för enkelhetens skull. Huvud-/värdeparföreningarna och tabellerna `"pageResults"` finns i noden. Om du också har angett att oformaterad text har `"readResults"` utvinning av oformaterad text via parametern *includeTextDetails* URL visas innehållet och positionerna för all text i dokumentet.

```bash
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Förbättra resultaten

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du REST-API:et för formulärre recognizeer med Python för att träna en modell och köra den i ett exempelscenario. Se sedan referensdokumentationen för att utforska API:et för formulärre recognizeer mer ingående.

> [!div class="nextstepaction"]
> [DOKUMENTATION FÖR REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
