---
title: Projektioner i en kunskapsbutik (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Spara och forma dina berikade data från AI-anrikningsindexeringspipelinen till ett kunskapslager för användning i andra scenarier än fulltextsökning. Knowledge Store är för närvarande i offentlig förhandsversion.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942979"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projektioner i ett kunskapslager i Azure Cognitive Search

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

Azure Cognitive Search möjliggör innehållsanrikning genom inbyggda kognitiva färdigheter och anpassade kunskaper som en del av indexeringen. Enrichments skapa ny information där ingen tidigare fanns: extrahera information från bilder, upptäcka sentiment, nyckelfraser och entiteter från text, för att nämna några. Enrichments lägger också struktur till odifferentierad text. Alla dessa processer resulterar i dokument som gör fulltextsökning mer effektiv. I många fall är utökade dokument användbara för andra scenarier än sökning, till exempel för kunskapsutvinning.

Projektioner, en del av [kunskapsarkivet,](knowledge-store-concept-intro.md)är vyer av berikade dokument som kan sparas i fysisk lagring för kunskapsbrytning. Med en projektion kan du "projicera" dina data till en form som stämmer överens med dina behov och bevarar relationer så att verktyg som Power BI kan läsa data utan ytterligare ansträngning.

Projektioner kan vara tabellformiga, med data som lagras i rader och kolumner i Azure Table storage eller JSON-objekt som lagras i Azure Blob-lagring. Du kan definiera flera projektioner av dina data när de berikas. Flera projektioner är användbara när du vill att samma data ska utformas på olika sätt för enskilda användningsfall.

Kunskapsarkivet stöder tre typer av projektioner:

+ **Tabeller**: För data som bäst representeras som rader och kolumner kan du med tabellprojektion definiera en schematiserad form eller projektion i Tabelllagring. Endast giltiga JSON-objekt kan projiceras som tabeller, det berikade dokumentet kan innehålla noder som inte heter JSON-objekt och när du projicerar dessa objekt skapar du ett giltigt JSON-objekt med en formfärdighet eller infogad formning.

+ **Objekt**: När du behöver en JSON-representation av dina data och berikanden sparas objektprojektioner som blobbar. Endast giltiga JSON-objekt kan projiceras som objekt, det berikade dokumentet kan innehålla noder som inte heter JSON-objekt och när du projicerar dessa objekt skapar du ett giltigt JSON-objekt med en formfärdighet eller infogad formning.

+ **Filer**: När du behöver spara bilderna som extraherats från dokumenten kan du spara de normaliserade bilderna för att blob-lagring.

Om du vill visa projektioner definierade i kontext går du igenom [Skapa ett kunskapslager i REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Projektionsgrupper

I vissa fall måste du projicera dina utökade data i olika former för att uppfylla olika mål. Med kunskapsarkivet kan du definiera flera grupper av projektioner. Projektionsgrupper har följande viktiga egenskaper hos ömsesidig exklusivitet och släktskap.

### <a name="mutual-exclusivity"></a>Ömsesidig exklusivitet

Allt innehåll som projiceras i en enda grupp är oberoende av data som projiceras i andra projektionsgrupper.
Detta oberoende innebär att du kan ha samma data formade på olika sätt, men upprepas i varje projektionsgrupp.

### <a name="relatedness"></a>Släktskap

Med projektionsgrupper kan du nu projicera dina dokument över projektionstyper samtidigt som relationerna mellan projektionstyper bevaras. Allt innehåll som projiceras inom en enda projektionsgrupp bevarar relationer inom data mellan projektionstyper. I tabeller baseras relationer på en genererad nyckel och varje underordnad nod behåller en referens till den överordnade noden. Mellan olika typer (tabeller, objekt och filer) bevaras relationer när en enda nod projiceras mellan olika typer. Tänk dig till exempel ett scenario där du har ett dokument som innehåller bilder och text. Du kan projicera texten till tabeller eller objekt och bilderna till filer där tabellerna eller objekten har en kolumn/egenskap som innehåller filens URL.

## <a name="input-shaping"></a>Ingångsformning

Att få dina data i rätt form eller struktur är nyckeln till effektiv användning, vare sig det är tabeller eller objekt. Möjligheten att forma eller strukturera dina data baserat på hur du planerar att komma åt och använda dem är en viktig funktion som exponeras som **Shaper-färdigheten** i kompetensen.  

Projektioner är lättare att definiera när du har ett objekt i anrikningsträdet som matchar projektionens schema. Med den uppdaterade [Shaper-färdigheten](cognitive-search-skill-shaper.md) kan du skriva ett objekt från olika noder i anrikningsträdet och överordna dem under en ny nod. **Med Shaper-färdigheten** kan du definiera komplexa typer med kapslade objekt.

När du har en ny form definierad som innehåller alla element som du behöver projicera ut, kan du nu använda den här formen som källa för projektionerna eller som indata till en annan färdighet.

## <a name="projection-slicing"></a>Projektion skivning

När du definierar en projektionsgrupp kan en enskild nod i anrikningsträdet delas in i flera relaterade tabeller eller objekt. Om du lägger till en projektion med en källsökväg som är underordnad för en befintlig projektion kommer den underordnade noden att skivas ut ur den överordnade noden och projiceras i den nya ännu relaterade tabellen eller objektet. Med den här tekniken kan du definiera en enda nod i en shaper-färdighet som kan vara källan för alla dina projektioner.

## <a name="table-projections"></a>Tabellprojektioner

Eftersom det gör det enklare att importera rekommenderar vi tabellprognoser för datautforskning med Power BI. Dessutom gör tabellprojektioner det möjligt att ändra kardinaliteten mellan tabellrelationer. 

Du kan projicera ett enda dokument i indexet i flera tabeller och bevara relationerna. När du projicerar till flera tabeller projiceras den fullständiga formen i varje tabell, såvida inte en underordnad nod är källan till en annan tabell i samma grupp.

### <a name="defining-a-table-projection"></a>Definiera en tabellprojektion

När du definierar en `knowledgeStore` tabellprojektion i elementet i kunskaperna börjar du med att mappa en nod i anrikningsträdet till tabellkällan. Vanligtvis är den här noden utdata från en **Shaper-färdighet** som du har lagt till i listan med kunskaper för att skapa en viss form som du behöver projicera i tabeller. Noden du väljer att projicera kan segmenteras för att projiceras i flera tabeller. Tabelldefinitionen är en lista över tabeller som du vill projicera.

Varje tabell kräver tre egenskaper:

+ tableName: Namnet på tabellen i Azure Storage.

+ generatedKeyName: Kolumnnamnet för nyckeln som unikt identifierar den här raden.

+ källa: Noden från anrikningsträdet som du köper dina berikningar från. Den här noden är vanligtvis utdata för en shaper, men kan vara resultatet av någon av färdigheterna.

Här är ett exempel på tabellprojektioner.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Som visas i det här exemplet modelleras nyckelfraserna och entiteterna i olika tabeller och innehåller en referens tillbaka till den överordnade (MainTable) för varje rad.

## <a name="object-projections"></a>Objektprojektioner

Objektprojektioner är JSON-representationer av anrikningsträdet som kan hämtas från vilken nod som helst. I många fall kan samma **Shaper-färdighet** som skapar en tabellprojektion användas för att generera en objektprojektion. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Om du skapar en objektprojektion krävs några objektspecifika attribut:

+ storageContainer: Blob-behållaren där objekten ska sparas
+ källa: Sökvägen till noden i anrikningsträdet som är roten till projektionen

## <a name="file-projection"></a>Arkivbild.

Filprojektioner liknar objektprojektioner och `normalized_images` agerar endast på samlingen. I likhet med objektprojektioner sparas filprojektioner i blob-behållaren med mappprefixet för det kodade värdet base64 för dokument-ID. Filprojektioner kan inte dela samma behållare som objektprojektioner och måste projiceras i en annan behållare.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Projektionslivscykel

Dina prognoser har en livscykel som är kopplad till källdata i datakällan. När dina data uppdateras och indexeras om uppdateras dina prognoser med resultatet av de berikanden som säkerställer att dina prognoser så småningom överensstämmer med data i datakällan. Projektionerna ärver den borttagningsprincip som du har konfigurerat för indexet. Projektioner tas inte bort när indexeraren eller själva söktjänsten tas bort.

## <a name="using-projections"></a>Använda projektioner

När indexeraren har körts kan du läsa de projicerade data i behållarna eller tabellerna som du har angett genom prognoser.

För analys är utforskning i Power BI lika enkelt som att ange Azure Table Storage som datakälla. Du kan enkelt skapa en uppsättning visualiseringar på dina data med hjälp av relationerna inom.

Alternativt, om du behöver använda de berikade data i en data science pipeline, kan du [läsa in data från blobbar i en Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Slutligen, om du behöver exportera dina data från kunskapsarkivet, har Azure Data Factory kopplingar för att exportera data och landa dem i den databas som du väljer. 

## <a name="next-steps"></a>Nästa steg

Som ett nästa steg kan du skapa ditt första kunskapslager med hjälp av exempeldata och instruktioner.

> [!div class="nextstepaction"]
> [Skapa ett kunskapslager i REST](knowledge-store-create-rest.md).

För en handledning som täcker avancerade projektioner begrepp som skivning, inline forma och relationer, börja med [att definiera prognoser i en kunskapsbutik](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiera projektioner i en kunskapsbutik](knowledge-store-projections-examples.md)
