---
title: 'Snabbstart: Träna en modell och extrahera formulärdata med cURL - formuläret Igenkännande'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda formatet Igenkännande REST-API med cURL tränar en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 3bfffc94bc11f9da2336d6edaeb96bf2e471c4ce
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602608"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med cURL

I den här snabbstarten ska du använda Azure formuläret Igenkännande REST-API med cURL för att träna och betygsätta formulär för att extrahera nyckel / värde-par och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här snabbstarten måste du ha:
- Åtkomst till förhandsversionen av formuläret Igenkännande begränsad åtkomst. För att få åtkomst till förhandsversionen kan fylla i och skicka den [formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret.
- [cURL](https://curl.haxx.se/windows/) installerad.
- En uppsättning minst fem typer av samma typ. Du använder dessa data för att träna modellen. Du kan använda en [provdatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten. Ladda upp data till roten i en Azure Blob Storage-konto.

## <a name="create-a-form-recognizer-resource"></a>Skapa en resurs för formuläret Igenkännande

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Träna en modell för formuläret Igenkännande

Först behöver du en uppsättning träningsdata i en Azure Storage blob. Du bör ha minst fem ifyllda former (PDF-dokument och/eller avbildningar) av samma typ/struktur som dina huvudsakliga indata. Eller du kan använda ett tomt formulär med två fyllts i formulär. Formuläret tomt namn måste innehålla ordet ”tom”. Se [skapa en datauppsättning för träning för en anpassad modell](../build-training-data-set.md) tips och alternativ för att sätta ihop dina utbildningsdata.

För att träna en modell för formuläret Igenkännande med dokument i Azure blob-behållare, anropa den **träna** API genom att köra följande cURL-kommando. Innan du kör kommandot gör dessa ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick från din prenumerationsnyckel för formuläret Igenkännande. Du hittar den på formuläret Igenkännande resursen **översikt** fliken.
1. Ersätt `<subscription key>` med prenumerationsnyckel som du kopierade i föregående steg.
1. Ersätt `<SAS URL>` med Azure Blob storage-behållare delade åt URL för signatur (SAS). Att hämta SAS-Webbadressen, öppna Microsoft Azure Storage Explorer, högerklicka på behållaren och välj **hämta signatur för delad åtkomst**. Kontrollera att den **Läs** och **lista** behörigheter kontrolleras och på **skapa**. Kopiera sedan värdet i den **URL** avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Du får en `200 (Success)` svar med följande JSON-utdata:

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

Obs den `"modelId"` värde. Du behöver det i följande steg.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrahera nyckel / värde-par och tabeller från formulär

Nu ska du analysera ett dokument och extrahera nyckel / värde-par och tabeller från den. Anropa den **modellera – analysera** API genom att köra cURL-kommando som följer. Innan du kör kommandot gör dessa ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick från din prenumerationsnyckel för formuläret Igenkännande. Du hittar den på formuläret Igenkännande resursen **översikt** fliken.
1. Ersätt `<modelID>` med modell-ID som du fick i föregående avsnitt.
1. Ersätt `<path to your form>` med sökvägen till ditt formulär (exempelvis C:\temp\file.pdf).
1. Ersätt `<file type>` med filtypen. Typer som stöds: `application/pdf`, `image/jpeg`, `image/png`.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i JSON. Nyckel / värde-par och tabeller som extraheras från formuläret representerar:

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

I den här snabbstarten använde du formuläret Igenkännande REST API med cURL du tränar en modell och kör den i ett exempelscenario. Därefter finns i referensdokumentationen för att utforska formuläret Igenkännande API: et i mer detalj.

> [!div class="nextstepaction"]
> [Referensdokumentation för REST API](https://aka.ms/form-recognizer/api)
