---
title: 'Snabbstart: Träna en modell och extrahera formulärdata med cURL - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda REST-API:et för formulärre recognizeer med cURL för att träna en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118402"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snabbstart: Träna en formulärmedkänningsmodell och extrahera formulärdata med hjälp av REST API med cURL

I den här snabbstarten använder du Azure Form Recognizer REST API med cURL för att träna och betygsätta formulär för att extrahera nyckelvärdespar och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du ha:
- [cURL](https://curl.haxx.se/windows/) installerat.
- En uppsättning av minst sex former av samma typ. Du kommer att använda fem av dessa för att träna modellen, och sedan ska du testa den med den sjätte formen. Formulären kan vara av olika filtyper men måste vara av samma typ av dokument. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten. Ladda upp utbildningsfilerna till roten för en blob-lagringsbehållare i ett Azure Storage-konto. Du kan placera testfilerna i en separat mapp.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en formulärakänningsmodell

Först behöver du en uppsättning utbildningsdata i en Azure Storage-blob. Du bör ha minst fem ifyllda formulär (PDF-dokument och/eller bilder) av samma typ/struktur som dina huvudsakliga indata. Du kan också använda ett enda tomt formulär med två ifyllda formulär. Det tomma formulärets filnamn måste innehålla ordet "tom". Se [Skapa en utbildningsdatauppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta ihop dina träningsdata.

> [!NOTE]
> Du kan använda den märkta datafunktionen för att manuellt märka vissa eller alla träningsdata i förväg. Detta är en mer komplex process men resulterar i en bättre utbildad modell. Se avsnittet [Träna med etiketter](../overview.md#train-with-labels) i översikten om du vill veta mer om den här funktionen.

Om du vill träna en formulärmedkänningsmodell med dokumenten i azure-blob-behållaren anropar du **[API:et för anpassad tågmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** genom att köra följande cURL-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick med din prenumeration på Formulär recognizer.
1. Ersätt `<subscription key>` med prenumerationsnyckeln som du kopierade från föregående steg.
1. Ersätt `<SAS URL>` med Azure Blob-lagringsbehållarens SAS-URL (Shared Access Signature). Om du vill hämta SAS-URL:en öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrollera att behörigheterna **Läs** och **Lista** är markerade och klicka på **Skapa**. Kopiera sedan värdet i **avsnittet URL.** Det bör ha `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formen: .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Du får ett `201 (Success)` svar med ett **platshuvud.** Värdet för det här huvudet är ID:et för den nya modellen som tränas. 

## <a name="get-training-results"></a>Få träningsresultat

När du har startat tågåtgärden använder du en ny åtgärd, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** för att kontrollera träningsstatusen. Skicka modell-ID:et till det här API-anropet för att kontrollera träningsstatus:

1. Ersätt `<Endpoint>` med slutpunkten som du fick med prenumerationsnyckeln För formulärmedkänningsnyckel.
1. Ersätt `<subscription key>` med din prenumerationsnyckel
1. Ersätt `<model ID>` med det modell-ID som du fick i föregående steg

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Du får ett `200 (Success)` svar med en JSON-brödtext i följande format. Lägg `"status"` märke till fältet. Detta kommer att `"ready"` ha värdet när utbildningen är klar. Om modellen inte är klar med träningen måste du fråga tjänsten igen genom att köra kommandot igen. Vi rekommenderar ett intervall på en sekund eller mer mellan samtalen.

Fältet `"modelId"` innehåller ID:et för den modell som du tränar. Du behöver det här för nästa steg.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analysera formulär för nyckelvärdespar och tabeller

Därefter ska du använda den nyutbildade modellen för att analysera ett dokument och extrahera nyckelvärdespar och tabeller från den. Anropa **[API:et analysera formulär](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** genom att köra följande cURL-kommando. Innan du kör kommandot gör du följande ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du har hämtat från prenumerationsnyckeln för Formulärmedkännare. Du hittar den på fliken **Resursöversikt för** formulärdeform.
1. Ersätt `<model ID>` med det modell-ID som du fick i föregående avsnitt.
1. Ersätt `<SAS URL>` med en SAS-URL till filen i Azure-lagring. Följ stegen i avsnittet Utbildning, men i stället för att hämta en SAS-URL för hela blob-behållaren får du en för den specifika fil som du vill analysera.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Du får ett `202 (Success)` svar med ett **operation-platshuvud.** Värdet för det här huvudet innehåller ett resultat-ID som du använder för att spåra resultatet av analysåtgärden. Spara det här resultat-ID:t för nästa steg.

## <a name="get-the-analyze-results"></a>Få analysresultaten

Använd följande API för att fråga resultaten av analysåtgärden.

1. Ersätt `<Endpoint>` med slutpunkten som du har hämtat från prenumerationsnyckeln för Formulärmedkännare. Du hittar den på fliken **Resursöversikt för** formulärdeform.
1. Ersätt `<result ID>` med det ID som du fick i föregående avsnitt.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Du får ett `200 (Success)` svar med en JSON-brödtext i följande format. Produktionen har förkortats för enkelhetens skull. Lägg `"status"` märke till fältet nära botten. Detta kommer att `"succeeded"` ha värdet när analysåtgärden är klar. Om åtgärden Analysera inte har slutförts måste du fråga tjänsten igen genom att köra kommandot igen. Vi rekommenderar ett intervall på en sekund eller mer mellan samtalen.

Huvud-/värdeparföreningarna och tabellerna `"pageResults"` finns i noden. Om du också har angett att oformaterad text har `"readResults"` utvinning av oformaterad text via parametern *includeTextDetails* URL visas innehållet och positionerna för all text i dokumentet.

```json
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

I den här snabbstarten använde du REST-API:et för formulärrec med cURL för att träna en modell och köra den i ett exempelscenario. Se sedan referensdokumentationen för att utforska API:et för formulärre recognizeer mer ingående.

> [!div class="nextstepaction"]
> [DOKUMENTATION FÖR REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
