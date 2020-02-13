---
title: Definiera projektioner i ett kunskaps lager
titleSuffix: Azure Cognitive Search
description: Exempel på vanliga mönster för att projicera dokument i kunskaps lagret för användning med Power BI eller Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165519"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Kunskaps lager projektioner: så här gör du för att forma och exportera anrikninger till kunskaps lagret

> [!IMPORTANT] 
> Kunskaps lagret är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande begränsad Portal support och inget stöd för .NET SDK.

Projektioner är det fysiska uttrycket för berikade dokument i ett kunskaps lager. Effektiv användning av dina berikade dokument kräver struktur. I den här artikeln får du utforska både struktur och relationer, lära dig hur du skapar projektions egenskaper samt hur du relaterar data mellan de typer av projektioner som du skapar. 

Om du vill skapa en projektion måste du forma data med antingen en formaren-färdighet för att skapa ett anpassat objekt eller använda den infogade form-syntaxen. En data-form innehåller alla data som du planerar att projicera. I det här dokumentet får du ett exempel på varje alternativ. du kan välja att använda något av alternativen för projektioner som du skapar.


Det finns tre typer av projektioner:
+ Tabeller
+ Objekt
+ Filer

Tabell projektioner lagras i Azure Table Storage. Objekt-och fil projektioner skrivs till blob-lagring, objekt projektioner sparas som JSON-filer och kan innehålla innehåll från dokumentet och eventuella kunskaps utmatningar eller anrikninger. Pipelinen kan också extrahera binärfiler som bilder, dessa binärfiler projiceras som fil prognoser. När ett binärt objekt projiceras som en objekt projektion, sparas bara de metadata som är kopplade till den som en JSON-blob. 

För att förstå skärningen mellan data utformning och projektioner använder vi följande färdigheter som grund för att utforska olika konfigurationer. Den här färdigheter bearbetar RAW-avbildning och text innehåll. Projektioner definieras utifrån dokumentets innehåll och utmatningarna i färdigheterna för de scenarier vi vill ha stöd för.

Du kan också hämta och använda ett [REST API-exempel](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) med alla anrop i den här genom gången.

> [!IMPORTANT] 
> När du experimenterar med projektioner är det praktiskt att [ange egenskapen indexerare cache](search-howto-incremental-index.md) för att se till att kostnads kontrollen är inställd. Genom att redigera projektioner leder du till hela dokumentet som berikas igen om indexeraren cache inte har angetts. När cachen har angetts och endast projektionerna har uppdaterats, resulterar färdigheter-körningar för tidigare berikade dokument inte till några Cognitive Services avgifter.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Nu kan vi lägga till `knowledgeStore`-objektet och konfigurera projektionerna för var och en av scenarierna efter behov. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projicera till tabeller för scenarier som Power BI

> [!NOTE] 
> Eftersom kunskaps lagret är ett Azure Storage konto är tabell projekt Azure Storage tabeller och regleras av lagrings gränserna för tabeller, mer information finns i [tabell lagrings gränser](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Det är praktiskt att veta att enhetens storlek inte kan överstiga 1 MB och att en enskild egenskap inte får vara större än 64 KB. Dessa begränsningar gör tabeller till en lämplig lösning för att lagra ett stort antal små entiteter.

Power BI kan läsa från tabeller och identifiera relationer baserat på de nycklar som är skapade i kunskaps lagerens projektioner, vilket gör att tabeller är ett lämpligt alternativ för projekt data när du försöker skapa en instrument panel för dina berikade data. Förutsatt att vi försöker skapa en instrument panel där vi kan visualisera viktiga fraser som extraherats från dokument som ett Word-moln, kan vi lägga till en formaren-färdighet till färdigheter för att skapa en anpassad form som innehåller dokument-specifika detaljer och viktiga fraser. Lägg till formaren-kompetensen i färdigheter för att skapa en ny anrikning som kallas ```pbiShape``` på ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Använda en formaren-färdighet för att skapa en anpassad form

Skapa en anpassad form som du kan projicera i Table Storage. Utan en anpassad form kan en projektion bara referera till en enda nod (en projektion per utdata). Genom att skapa en anpassad form kan du aggregera olika element i en ny logisk helhet som kan projiceras som en enskild tabell, eller segmenteras och distribueras i en samling tabeller. I det här exemplet kombinerar den anpassade formen metadata och identifierade entiteter och nyckel fraser. Objektet kallas pbiShape och är överordnat under `/document`. 

> [!IMPORTANT] 
> Käll Sök vägar för anriknings måste vara välformulerade JSON-objekt, innan de kan projiceras. Anriknings trädet kan representera anrikninger som inte är väl utformade JSON, till exempel när en anrikning är överordnad till en primitve som en sträng. Observera hur `KeyPhrases` och `Entities` omsluts till ett giltigt JSON-objekt med `sourceContext`, detta krävs som `keyphrases` och `entities` är omfattande för primitiver och måste konverteras till giltig JSON innan de kan projiceras.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Lägg till formaren-kompetensen som vi precis definierade i kunskaps listan i färdigheter. 

Nu när vi har alla data som behövs för att projicera till tabeller, uppdaterar du knowledgeStore-objektet med tabell definitionerna. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Ange egenskapen ```storageConnectionString``` som giltig anslutnings sträng för lagrings konto för allmänt bruk. I det här scenariot definierar vi tre tabeller i objektet projektion genom att ange egenskaperna ```tableName```, ```source``` och ```generatedKeyName```. Nu kan du uppdatera färdigheter genom att utfärda begäran om placering.

### <a name="slicing"></a>Slicing 

När du börjar med en sammanställd form där allt innehåll som behöver projiceras finns i en enda form (eller en anrikning), ger segmentering dig möjlighet att segmentera en enskild nod i flera tabeller eller objekt. Här segmenteras ```pbiShape```-objektet i flera tabeller. Med segmenterings funktionen kan du dra ut delar av formen, ```keyPhrases``` och ```Entities``` i separata tabeller. Detta är användbart eftersom flera entiteter och-fraser är associerade med varje dokument. Segmentering av segmentering genererar en relation mellan överordnade och underordnade tabeller med hjälp av ```generatedKeyName``` i den överordnade tabellen för att skapa en kolumn med samma namn i den underordnade tabellen. 

### <a name="naming-relationships"></a>Namnge relationer
Egenskaperna ```generatedKeyName``` och ```referenceKeyName``` används för att relatera data över tabeller eller till och med mellan olika typer av projektioner. Varje rad i den underordnade tabellen/projektionen har en egenskap som pekar tillbaka till överordnad. Namnet på kolumnen eller egenskapen i det underordnade objektet är ```referenceKeyName``` från överordnad. När ```referenceKeyName``` inte anges, använder tjänsten den som standard ```generatedKeyName``` från överordnad. PowerBI är beroende av dessa genererade nycklar för att identifiera relationer i tabellerna. Om du behöver kolumnen i den underordnade tabellen med namnet annorlunda, anger du egenskapen ```referenceKeyName``` för den överordnade tabellen. Ett exempel är att ange ```generatedKeyName``` som-ID i tabellen pbiDocument och ```referenceKeyName``` som DocumentID. Detta resulterar i kolumnen i tabellerna pbiEntities och pbiKeyPhrases som innehåller dokument-ID: t som heter DocumentID.

Spara den uppdaterade färdigheter och kör indexeraren. nu har du en arbets projektion med tre tabeller. Om du importerar tabellerna till Power BI bör Power BI identifiera relationerna automatiskt.

## <a name="projecting-to-objects"></a>Projicera till objekt

Objekt projektioner har inte samma begränsningar som tabell projektioner, och passar bättre för att projicera stora dokument. I det här exemplet projicerar vi hela dokumentet till en objekt projektion. Objekt projektioner är begränsade till en enda projektion i en behållare.
För att definiera en objekt projektion använder vi ```objects``` matrisen i projektionerna. Du kan skapa en ny form med formaren-kompetensen eller använda infogad form av objekt projektion. Medan tabell exemplet demonstrerade metoden för att skapa en form och segmentering visar det här exemplet användningen av infogad form. Inline Forming är möjligheten att skapa en ny form i definitionen av indata till en projektion. Infogad Forming skapar ett anonymt objekt som är identiskt med det som en liknande formaren skulle generera. Infogad utformning är användbart om du definierar en form som du inte planerar att återanvända.
Projektions-egenskapen är en matris, i det här exemplet lägger vi till en ny projektions-instans i matrisen. Uppdatera knowledgeStore-definitionen med de projektioner som definierats infogas, du behöver inte en formaren-kunskap när du använder infogade projektioner.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Fil projektioner

Filprojektioner är bilder som antingen extraheras från käll dokumentet eller utmatningar av anrikninger som kan projiceras från beriknings processen. Fil projektioner som liknar objekt projektioner implementeras som blobbar och innehåller avbildningen. För att generera en filprojektion använder vi ```files``` matrisen i objektet projektion. I det här exemplet projekterar vi alla avbildningar som extraherats från dokumentet till en behållare med namnet `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projicera till flera typer

Ett mer komplext scenario kan kräva att du projicerar innehåll mellan olika typer av projektioner. Om du till exempel behöver projicera vissa data som nyckel fraser och entiteter till tabeller, sparar du OCR-resultatet av text-och layout-text som objekt och projicerar bilderna som filer. Den här uppdateringen av färdigheter kommer att:

1. Skapa en tabell med en rad för varje dokument.
2. Skapa en tabell som är relaterad till dokument tabellen med varje nyckel fras som identifieras som en rad i den här tabellen.
3. Skapa en tabell som är relaterad till dokument tabellen med varje entitet som identifieras som en rad i den här tabellen.
4. Skapa en objekt projektion med layout texten för varje bild.
5. Skapa en filprojektion och projicera varje extraherad avbildning.
6. Skapa en kors referens tabell som innehåller referenser till dokument tabellen, projektion av objekt med layout-text och filprojektion.

Börja med att lägga till en ny formaren-färdighet till kompetens mat ris som skapar ett formad objekt. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Objekt projektioner kräver ett behållar namn för varje projektion, objekt projektioner eller filprojektioner kan inte dela en behållare. 

### <a name="relationships"></a>Relationer

I det här exemplet beskrivs även en annan funktion i projektioner, genom att definiera flera typer av projektioner inom samma projektions-objekt, det finns en relation som uttrycks i och över olika typer (tabeller, objekt, filer) för projektioner, vilket tillåter du börjar med en tabell rad för ett dokument och hittar all OCR-text för bilderna i dokumentet i objektet projektion. Om du inte vill att data ska vara relaterade definierar du projektionerna i olika projektions objekt, till exempel om följande kod avsnitt leder till att tabellerna är relaterade, men inga relationer mellan tabellerna och text projektionerna i OCR. Projektions grupper är användbara när du vill projicera samma data i olika former för olika behov. Till exempel en projektions grupp för instrument panelen för Power BI och en annan projektions grupp för att använda data för att träna en AI-modell för en färdighet.
När du skapar projektioner av olika typer genereras fil-och objekt projektioner först och Sök vägarna läggs till i tabellerna.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

De här exemplen demonstrerade de vanligaste mönstren för hur du använder projektioner, men du bör nu också ha en god förståelse av begreppen för att skapa en projektion för ditt speciella scenario.

## <a name="common-issues"></a>Vanliga problem

När du definierar en projektion finns det några vanliga problem som kan orsaka oväntade resultat.

1. Ingen utformning av Strängs rika tecken. När strängar är berikade, till exempel ```merged_content``` som är omfattande med viktiga fraser, representeras den fördjupade egenskapen som underordnad till merged_content i berikande trädet. Men vid projektions tiden måste detta omvandlas till ett giltigt JSON-objekt med ett namn och ett värde.
2. Utelämna ```/*``` i slutet av en käll Sök väg. Om t. ex. källan till en projektion är ```/document/pbiShape/keyPhrases```, projiceras matrisen med nyckel fraser som ett enda objekt/rad. Att ange käll Sök vägen till ```/document/pbiShape/keyPhrases/*``` ger en enskild rad eller ett objekt för var och en av de viktigaste fraserna.
3. Sök vägs väljare är Skift läges känsliga och kan leda till saknade indatatyper om du inte använder det exakta fallet för väljaren.

