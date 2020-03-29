---
title: Definiera projektioner i en kunskapsbutik
titleSuffix: Azure Cognitive Search
description: Exempel på vanliga mönster för hur du projicerar utökade dokument i kunskapsarkivet för användning med Power BI eller Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943670"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Kunskapslagerprojektioner: Hur man formar och exporterar anrikningar

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

Projektioner är det fysiska uttrycket för berikade dokument i ett kunskapslager. Effektiv användning av dina berikade dokument kräver struktur. I den här artikeln ska du utforska både struktur och relationer, lära dig hur du bygger ut projektionsegenskaper samt hur du relaterar data mellan projektionstyper som du skapar. 

Om du vill skapa en projektion måste du forma data med hjälp av antingen en [Shaper-färdighet](cognitive-search-skill-shaper.md) för att skapa ett anpassat objekt eller använda syntaxen för infogad formning i en projektionsdefinition. 

En dataform innehåller alla data som du tänker projicera och som har skapats som en hierarki med noder. Den här artikeln visar flera tekniker för att forma data så att den kan projiceras i fysiska strukturer som bidrar till rapportering, analys eller nedströmsbearbetning. 

Exemplen som presenteras i den här artikeln finns i det här [REST API-exemplet](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), som du kan hämta och köra i en HTTP-klient.

## <a name="introduction-to-the-examples"></a>Introduktion till exemplen

Om du är bekant med [projektioner](knowledge-store-projection-overview.md)kommer du ihåg att det finns tre typer:

+ Tabeller
+ Objekt
+ Filer

Tabellprojektioner lagras i Azure Table storage. Objekt- och filprojektioner skrivs till blob-lagring, där objektprojektioner sparas som JSON-filer, och kan innehålla innehåll från källdokumentet samt eventuella färdighetsutdata eller berikanden. Anrikningspipelinen kan också extrahera binärfiler som bilder, dessa binärfiler projiceras som filprojektioner. När ett binärt objekt projiceras som en objektprojektion sparas endast de metadata som är associerade med det som en JSON-blob. 

För att förstå skärningspunkten mellan dataformning och projektioner använder vi följande kompetens som grund för att utforska olika konfigurationer. Den här kompetensuppsättningen bearbetar rå bild- och textinnehåll. Prognoser kommer att definieras från innehållet i dokumentet och resultaten av kompetensen, för de scenarier som vi vill stödja.

> [!IMPORTANT] 
> När du experimenterar med prognoser är det användbart att [ange egenskapen indexerarcache](search-howto-incremental-index.md) för att säkerställa kostnadskontroll. Redigeringsprognoser leder till att hela dokumentet berikas igen om indexerarens cache inte är inställd. När cacheminnet har angetts och endast projektionerna uppdateras resulterar inte kompetenskörningar för tidigare berikade dokument i några nya Cognitive Services-avgifter.

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

Med hjälp av den `knowledgeStore` här kompetensuppsättningen, med dess `knowledgeStore` null som grund, fyller vårt första exempel i objektet, konfigurerat med projektioner som skapar tabelldatastrukturer som vi kan använda i andra scenarier. 

## <a name="projecting-to-tables"></a>Projicera till tabeller

Att projicera till tabeller i Azure Storage är användbart för rapportering och analys med hjälp av verktyg som Power BI. Power BI kan läsa från tabeller och identifiera relationer baserat på de nycklar som genereras under projektionen. Om du försöker skapa en instrumentpanel förenklas uppgiften om du har relaterade data. 

Anta att vi försöker skapa en instrumentpanel där vi kan visualisera nyckelfraserna som extraherats från dokument som ett ordmoln. För att skapa rätt datastruktur kan vi lägga till en Shaper-färdighet i kompetensen för att skapa en anpassad form som har de dokumentspecifika detaljerna och nyckelfraserna. Den anpassade formen `pbiShape` anropas `document` på rotnoden.

> [!NOTE] 
> Tabellprojektioner är Azure Storage-tabeller som styrs av de lagringsgränser som azure storage medför. Mer information finns i [begränsningar för tabelllagring](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Det är användbart att veta att entitetens storlek inte får överstiga 1 MB och en enskild egenskap får inte vara större än 64 KB. Dessa begränsningar gör tabeller till en bra lösning för att lagra ett stort antal små entiteter.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Använda en Shaper-färdighet för att skapa en anpassad form

Skapa en anpassad form som du kan projicera i tabelllagring. Utan en anpassad form kan en projektion bara referera till en enda nod (en projektion per utdata). Genom att skapa en anpassad form kan du sammanställa olika element till en ny logisk helhet som kan projiceras som en enda tabell, eller segmenteras och fördelas över en samling tabeller. 

I det här exemplet kombinerar den anpassade formen metadata och identifierade entiteter och nyckelfraser. Objektet anropas `pbiShape` och är `/document`överordnat under . 

> [!IMPORTANT] 
> Ett syfte med att forma är att se till att alla anrikningsnoder uttrycks i välformade JSON, vilket krävs för att projicera i kunskapslager. Detta gäller särskilt när ett anrikningsträd innehåller noder som inte är välformade JSON (till exempel när en berikning är överordnad till en primitiv som en sträng).
>
> Lägg märke till de `KeyPhrases` `Entities`två sista noderna och . Dessa är insvepta i ett giltigt `sourceContext`JSON-objekt med . Detta krävs `keyphrases` och `entities` är berikande på primitiver och måste konverteras till giltiga JSON innan de kan projiceras.
>


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

Lägg till ovanstående Shaper-färdighet i kompetensen. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Nu när vi har alla data som behövs för att projicera till tabeller, uppdatera knowledgeStore-objektet med tabelldefinitionerna. I det här exemplet har vi tre `tableName` `source` tabeller, definierade genom att ange och `generatedKeyName` egenskaper.

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

Du kan bearbeta ditt arbete genom att följa dessa steg:

1. Ange ```storageConnectionString``` egenskapen till en giltig V2-anslutningssträng för allmänt lagringskonto.  

1. Uppdatera kompetensen genom att utfärda PUT-begäran.

1. När du har uppdaterat kompetensen kör du indexeraren. 

Du har nu en fungerande projektion med tre tabeller. Om du importerar dessa tabeller till Power BI bör power bi identifieras relationerna automatiskt.

Innan du går vidare med nästa exempel, kan se över aspekter av tabellen projektion för att förstå mekaniken i skivning och relaterade data.

### <a name="slicing"></a>Skivning 

Skivning är en teknik som delar upp en hel konsoliderad form i beståndsdelar. Resultatet består av separata men relaterade tabeller som du kan arbeta med individuellt.

I exemplet `pbiShape` är den konsoliderade formen (eller berikningsnoden). I projektionsdefinitionen `pbiShape` segmentas du i ytterligare tabeller, vilket gör att ```keyPhrases``` ```Entities```du kan dra ut delar av formen och . I Power BI är detta användbart eftersom flera entiteter och nyckelfraser är associerade med varje dokument, och du får fler insikter om du kan se entiteter och nyckelfraser som kategoriserade data.

Skivning genererar implicit en relation mellan de överordnade och ```generatedKeyName``` underordnade tabellerna, med hjälp av den överordnade tabellen i den överordnade tabellen för att skapa en kolumn med samma namn i den underordnade tabellen. 

### <a name="naming-relationships"></a>Namnge relationer

Egenskaperna ```generatedKeyName``` ```referenceKeyName``` och används för att relatera data mellan tabeller eller till och med mellan projektionstyper. Varje rad i den underordnade tabellen/projektionen har en egenskap som pekar tillbaka till den överordnade. Namnet på kolumnen eller egenskapen i ```referenceKeyName``` det underordnade barnet är det från den överordnade. När ```referenceKeyName``` den inte tillhandahålls, tjänsten standard ```generatedKeyName``` det till från den överordnade. 

Power BI förlitar sig på dessa genererade nycklar för att identifiera relationer i tabellerna. Om du behöver kolumnen i den underordnade ```referenceKeyName``` tabellen med namnet annorlunda anger du egenskapen i den överordnade tabellen. Ett exempel skulle vara ```generatedKeyName``` att ange som ID i tabellen ```referenceKeyName``` pbiDocument och som DocumentID. Detta skulle resultera i kolumnen i tabellerna pbiEntities och pbiKeyPhrases som innehåller dokument-ID med namnet DocumentID.

## <a name="projecting-to-objects"></a>Projicera till objekt

Objektprojektioner har inte samma begränsningar som tabellprojektioner och är bättre lämpade för att projicera stora dokument. I det här exemplet projicerar vi hela dokumentet till en objektprojektion. Objektprojektioner är begränsade till en enskild projektion i en behållare och kan inte segmenteras.

För att definiera en objektprojektion ```objects``` använder vi matrisen i projektionerna. Du kan skapa en ny form med shaper-färdigheten eller använda infogad formning av objektprojektionen. Medan tabellexemplet visade metoden att skapa en form och skivning, visar det här exemplet användningen av infogad formning. 

Infogad formning är förmågan att skapa en ny form i definitionen av indata till en projektion. Infogad formning skapar ett anonymt objekt som är identiskt med `pbiShape`vad en Shaper-färdighet skulle ge (i vårt fall). Infogad formning är användbar om du definierar en form som du inte planerar att återanvända.

Egenskapen projektioner är en matris. I det här exemplet lägger vi till en ny projektionsförekomst i matrisen, där knowledgeStore-definitionen innehåller infogade projektioner. När du använder infogade projektioner kan du utelämna Shaper-färdigheten.

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

## <a name="projecting-to-file"></a>Projicera till fil

Filprojektioner är bilder som antingen extraheras från källdokumentet eller utdata av anrikning som kan projiceras ur anrikningsprocessen. Filprojektioner, som liknar objektprojektioner, implementeras som blobbar i Azure Storage och innehåller avbildningen. 

För att generera en filprojektion använder vi matrisen `files` i projektionsobjektet. I det här exemplet projiceras alla bilder `samplefile`som extraherats från dokumentet till en behållare som kallas .

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

Ett mer komplext scenario kan kräva att du projicerar innehåll över projektionstyper. Om du till exempel behöver projicera vissa data som nyckelfraser och entiteter i tabeller sparar du OCR-resultaten av text och layouttext som objekt och projicerar sedan bilderna som filer. 

I det här exemplet innehåller uppdateringar av kompetensen följande ändringar:

1. Skapa en tabell med en rad för varje dokument.
1. Skapa en tabell som är relaterad till dokumenttabellen med varje nyckelfras identifierad som en rad i den här tabellen.
1. Skapa en tabell som är relaterad till dokumenttabellen med varje entitet identifierad som en rad i den här tabellen.
1. Skapa en objektprojektion med layouttexten för varje bild.
1. Skapa en filprojektion som projicerar varje extraherad bild.
1. Skapa en korsreferenstabell som innehåller referenser till dokumenttabellen, objektprojektion med layouttexten och filprojektionen.

Dessa ändringar återspeglas i knowledgeStore-definitionen längre ner. 

### <a name="shape-data-for-cross-projection"></a>Formdata för korsprojektion

För att få de former vi behöver för dessa prognoser, börja med att `crossProjection`lägga till en ny Shaper skicklighet som skapar ett format objekt som heter . 

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

### <a name="define-table-object-and-file-projections"></a>Definiera tabell-, objekt- och filprojektioner

Från det konsoliderade crossProjection-objektet kan vi segmentera objektet i flera tabeller, fånga OCR-utdata som blobbar och sedan spara bilden som filer (även i Blob-lagring).

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

Objektprojektioner kräver ett behållarnamn för varje projektion, objektprojektioner eller filprojektioner som inte kan dela en behållare. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relationer mellan tabell-, objekt- och filprojektioner

I det här exemplet framhålls också en annan funktion i projektionerna. Genom att definiera flera typer av projektioner inom samma projektionsobjekt finns det en relation som uttrycks inom och mellan de olika typerna (tabeller, objekt, filer), så att du kan börja med en tabellrad för ett dokument och hitta all OCR-text för bilderna i dokumentet i objektprojektionen. 

Om du inte vill att data ska vara relaterade definierar du projektionerna i olika projektionsobjekt. Följande kodavsnitt resulterar till exempel i att tabellerna är relaterade, men utan relationer mellan tabellerna och objektprojektionerna (OCR-text). 

Projektionsgrupper är användbara när du vill projicera samma data i olika former för olika behov. Till exempel en projektionsgrupp för Power BI-instrumentpanelen och en annan projektionsgrupp för att samla in data som används för att träna en maskininlärningsmodell insvept i en anpassad färdighet.

När du skapar projektioner av olika typer genereras fil- och objektprojektioner först och banorna läggs till i tabellerna.

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

## <a name="common-issues"></a>Vanliga problem

När du definierar en projektion finns det några vanliga problem som kan orsaka oförutsedda resultat. Sök efter dessa problem om utdata i knowledge store inte är vad du förväntar dig.

+ Inte forma sträng anrikningar till giltiga JSON. När strängar berikas, till `merged_content` exempel berikas med nyckelfraser, representeras den `merged_content` berikade egenskapen som underordnad inom anrikningsträdet. Standardrepresentationen är inte välformulerad JSON. Så vid projektion tid, se till att omvandla anrikningen till ett giltigt JSON-objekt med ett namn och ett värde.

+ Utelämnar ```/*``` i slutet av en källsökväg. Om källan till en `/document/pbiShape/keyPhrases`projektion är projiceras nyckelfrasarrayen som ett enda objekt/en rad. Ange i stället källsökvägen så att `/document/pbiShape/keyPhrases/*` en enda rad eller ett objekt ska ges för var och en av nyckelfraserna.

+ Syntaxfel för sökväg. Sökvägsväljare är skiftlägeskänsliga och kan leda till saknade inmatningsvarningar om du inte använder det exakta skiftarfallet för väljaren.

## <a name="next-steps"></a>Nästa steg

Exemplen i den här artikeln visar vanliga mönster för hur du skapar projektioner. Nu när du har en god förståelse för begreppen, är du bättre rustad att bygga prognoser för ditt specifika scenario.

När du utforskar nya funktioner bör du betrakta inkrementell berikning som nästa steg. Inkrementell berikning baseras på cachelagring, vilket gör att du kan återanvända alla enrichments som inte annars påverkas av en kompetensändring. Detta är särskilt användbart för pipelines som innehåller OCR och bildanalys.

> [!div class="nextstepaction"]
> [Introduktion till inkrementell berikning och cachelagring](cognitive-search-incremental-indexing-conceptual.md)

Om du vill ha en översikt över projektioner kan du läsa mer om funktioner som grupper och skivning och hur du [definierar dem i en kompetens](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projektioner i en kunskapsbutik](knowledge-store-projection-overview.md)

