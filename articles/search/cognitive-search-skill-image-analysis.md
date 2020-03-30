---
title: Bildanalys kognitiv skicklighet
titleSuffix: Azure Cognitive Search
description: Extrahera semantisk text genom bildanalys med hjälp av bildanalys kognitiv färdighet i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6972e2f7ea219a1c8c8dbabbf9fe12a8fa59e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369479"
---
# <a name="image-analysis-cognitive-skill"></a>Bildanalys kognitiv skicklighet

Med **bildanalysen** extraheras en omfattande uppsättning visuella funktioner baserat på bildinnehållet. Du kan till exempel generera en bildtext från en bild, generera taggar eller identifiera kändisar och landmärken. Den här färdigheten använder maskininlärningsmodellerna som tillhandahålls av [Datorseende](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) i Cognitive Services. 

> [!NOTE]
> Små volymer (under 20 transaktioner) kan köras gratis i Azure Cognitive Search, men större arbetsbelastningar kräver [att en fakturerbar Cognitive Services-resurs kopplas](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.vision.imageAnalysisSkill 

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| standardSpråkkod   |  En sträng som anger vilket språk som ska returneras. Tjänsten returnerar igenkänning resulterar i ett angivet språk. Om den här parametern inte anges är standardvärdet "en". <br/><br/>Språk som stöds är: <br/>*sv* - Engelska (standard) <br/> *es* - spanska <br/> *ja* - Japanska <br/> *pt* - Portugisiska <br/> *zh* - Förenklad kinesiska|
| visuellaFeatures |  En matris med strängar som anger vilka visuella funktionstyper som ska returneras. Giltiga visuella funktionstyper är:  <ul><li>*vuxen* - upptäcker om bilden är pornografisk till sin natur (skildrar nakenhet eller en sexhandling), eller är blodig (skildrar extremt våld eller blod). Sexuellt suggestivt innehåll (aka racy innehåll) upptäcks också.</li><li>*varumärken* - upptäcker olika märken inom en bild, inklusive den ungefärliga platsen. Den visuella funktionen *varumärken* är endast tillgänglig på engelska.</li><li> *kategorier* - kategoriserar bildinnehåll enligt en taxonomi som definieras i [dokumentationen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)för Cognitive Services Computer Vision . </li><li>*beskrivning* - beskriver bildinnehållet med en fullständig mening på språk som stöds.</li><li>*ansikten* - upptäcker om ansikten finns. Om det finns, genererar koordinater, kön och ålder.</li><li> *objekt* - identifierar olika objekt i en bild, inklusive den ungefärliga platsen. *Objektens* visuella funktion är endast tillgänglig på engelska.</li><li> *taggar* - taggar bilden med en detaljerad lista med ord relaterade till bildinnehållet.</li></ul> Namn på visuella funktioner är skiftlägeskänsliga. Observera att *färg-* och *imageType-visuella* funktioner har inaktuella, men den här funktionen kan fortfarande nås via en [anpassad färdighet](https://go.microsoft.com/fwlink/?linkid=2121117).|
| Detaljer   | En matris med strängar som anger vilken domänspecifik information som ska returneras. Giltiga visuella funktionstyper är: <ul><li>*kändisar* - identifierar kändisar om det upptäcks i bilden.</li><li>*landmärken* - identifierar landmärken om det upptäcks i bilden. </li></ul> |

## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| image         | Komplex typ. För närvarande fungerar endast med fältet "/document/normalized_images", som produceras av ```imageAction``` Azure Blob-indexeraren när den är inställd på ett annat värde än ```none```. Mer information finns i [exemplet.](#sample-output)|



##  <a name="sample-skill-definition"></a>Exempel på färdighetsdefinition

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Exempelindex (endast för kategorierna, beskrivningen, ansiktena och taggarna)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Exempel på utdatafältmappning (för ovanstående index)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variation på utdatafältmappningar (kapslade egenskaper)

Du kan definiera utdatafältmappningar till egenskaper på lägre nivå, till exempel bara landmärken eller kändisar. I det här fallet kontrollerar du att indexschemat har ett fält som innehåller landmärken specifikt.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Exempelinmatning

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exempel på utdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Felfall
I följande felfall extraheras inga element.

| Felkod | Beskrivning |
|------------|-------------|
| Inte stödsSpråk | Det angivna språket stöds inte. |
| OgiltigtBildurl | Bildadressen är felaktigt formaterad eller inte tillgänglig.|
| OgiltigtBildformat | Indata är inte en giltig avbildning. |
| Ogiltig storlek | Indatabilden är för stor. |
| Inte stödsVisualFeature  | Den angivna funktionstypen är ogiltig. |
| Inte stödsImage | Bild som inte stöds, till exempel barnpornografi. |
| Ogiltigadetaljer | Domänspecifik modell som inte stöds. |

Om du får felet `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`som liknar kontrollerar du sökvägen. Både kändisar och landmärken är `detail`egenskaper under .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
