---
title: 'Snabbstart: Träna en modell och extrahera formulärdata med cURL - formuläret Igenkännande'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du formuläret Igenkännande REST-API med cURL att tränar en modell och extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: cc6e8cdb7cd1719a8cd14cbfe6e576e07c34b32c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027266"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med cURL

I den här snabbstarten ska du använda med formuläret Igenkännande REST API med cURL för att träna och betygsätta formulär för att extrahera nyckel / värde-par och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du har fått tillgång till förhandsversionen av formuläret Igenkännande begränsad åtkomst. För att få åtkomst till förhandsversionen kan du fylla i och skicka den [Cognitive Services formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret. 
* Du måste ha [cURL](https://curl.haxx.se/windows/).
* Du måste ha en prenumerationsnyckel för formuläret Igenkännande. Följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på formuläret Igenkännande och få din nyckel.
* Du måste ha en minimiuppsättning fem typer av samma typ. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten.

## <a name="train-a-form-recognizer-model"></a>Träna en modell för formuläret Igenkännande

Först behöver du en uppsättning träningsdata. Du kan använda data i en Azure Blob eller dina egna lokala träningsdata. Du bör ha minst fem exempelformulär (PDF-dokument och/eller avbildningar) av samma typ/struktur som dina huvudsakliga indata. Du kan också använda ett tomt formulär. formulärets filnamnet innehåller ordet ”Töm”.

För att träna en modell för formuläret Igenkännande med hjälp av dokument i Azure Blob-behållare, anropa den **träna** API genom att köra cURL-kommandot nedan. Innan du kör kommandot måste du göra följande ändringar:

* Ersätt `<Endpoint>` till slutpunkten som du fick från din prenumerationsnyckel för formuläret Igenkännande. Du hittar den i formuläret Igenkännande resource översikt-fliken.
* Ersätt `<SAS URL>` med en Azure Blob Storage-behållare som delad åtkomst (signatur) URL där träningsdata finns.  
* Ersätt `<subscription key>` med din prenumerationsnyckel.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<SAS URL>\"}"
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

Anteckna den `"modelId"` value; du behöver för följande steg.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrahera nyckel / värde-par och tabeller från formulär

Nu ska du analysera ett dokument och extrahera nyckel / värde-par och tabeller från den. Anropa den **modellera – analysera** API genom att köra cURL-kommandot nedan. Innan du kör kommandot måste du göra följande ändringar:

* Ersätt `<Endpoint>` med slutpunkten som du fick din prenumerationsnyckel för formuläret Igenkännande. Du hittar den i formuläret Igenkännande resource översikt-fliken.
* Ersätt `<modelID>` med modell-ID som du fick i det föregående steget för att träna modellen.
* Ersätt `<path to your form>` med sökväg i formuläret.
* Ersätt `<subscription key>` med din prenumerationsnyckel.
* Ersätt `<file type>` med filtypen - typer som stöds, pdf, bild/jpeg, bild/png.

```bash
cURL cmd: curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@<path to your form>;type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i JSON och representerar extraherade nyckel / värde-par och tabeller i formuläret.

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

I den här guiden används formatet Igenkännande REST-API: er med cURL du tränar en modell och kör den i exemplet fall. Därefter finns i referensdokumentationen för att utforska formuläret Igenkännande API: et i mer detalj.

> [!div class="nextstepaction"]
> [Referensdokumentation för REST API](https://aka.ms/form-recognizer/api)
