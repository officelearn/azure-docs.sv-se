---
title: Arbeta med projektioner i ett Arkiv för kunskap – Azure Search
description: Spara och utforma dina avancerad och data från AI-pipeline för fulltextindexering i situationer än sökning
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: 3ab5ffafd1b20eb0e3e453d3e730840baf9233e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028372"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Arbeta med projektioner i ett knowledge Arkiv i Azure Search

Azure Search kan innehåll berikande via AI kognitiva kunskaper och anpassade funktioner som en del av indexering. Enrichments ge struktur till dina dokument och göra sökningen mer effektivt. I många fall är avancerad och dokumenten användbart för scenarier än sökning, till exempel för knowledge utvinningsstrukturen.

Projektioner, en komponent i [knowledge store (förhandsversion)](knowledge-store-concept-intro.md), är vyer för avancerad och dokument som kan sparas till fysiska lagringsplatsen för knowledge utvinningsstrukturen syften. En projektion kan du ”project” dina data till en form som överensstämmer med dina behov, bevarar relationer så att verktyg som Power BI kan läsa in data med inget extra arbete. 

Projektioner kan vara tabular, med data som lagras i rader och kolumner i Azure Table storage eller JSON-objekt som lagras i Azure Blob storage. Du kan definiera flera projektioner av data som den berikas. Detta är användbart när du vill att samma data utformas på olika sätt för enskilda användningsfall. 

Knowledge store stöder två typer av projektioner:

+ **Tabeller**: För data som är bäst att visas som rader och kolumner, kan tabellen projektioner du definiera en schematiserade form eller projektion i Table storage. 

+ **Objekt**: När du behöver en JSON-representation av dina data och enrichments sparas objektet projektioner som blobar.

Om du vill se projektioner som angetts i kontexten, gå igenom [hur du kommer igång med knowledge store](knowledge-store-howto.md)

## <a name="projection-groups"></a>Projektion av grupper

I vissa fall behöver Projicera din avancerad och data i olika former att uppfylla olika mål. Knowledge store kan du definiera flera grupper av projektioner. Projektion av grupper har följande viktiga egenskaper för ömsesidig exklusivitet och släktskap.

### <a name="mutually-exclusivity"></a>Ömsesidigt exklusivitet

Allt innehåll som är planerade i en grupp är oberoende av data som projiceras in andra grupper för projektion. Detta innebär att du kan ha samma data utformas på olika sätt, men upprepas i varje grupp för projektion. 

En begränsning som tillämpas i projektion grupper är ömsesidig exklusivitet av projektionstyper med en projektion-grupp. Du kan bara definiera tabellen projektioner eller projektioner för objekt i samma grupp. Om du vill både tabeller och objekt kan du definiera en projektion grupp för tabeller och andra projektion gruppen för objekt.

### <a name="relatedness"></a>Släktskap

Allt innehåll som är planerade i en enda projektion grupp bevarar relationer i data. Relationer är baserade på en genererad nyckel och varje underordnad nod behåller en referens till den överordnade noden. Relationer sträcker sig inte projektion grupper och tabeller eller objekt som skapas i en projektion grupp har ingen relation till data som genereras i andra grupper för projektion.

## <a name="input-shaping"></a>Ange forma
Hämta dina data på rätt sätt eller struktur är avgörande för att effektivt att använda, tabeller och objekt. Möjligheten att forma eller strukturerar dina data baserat på hur du planerar att komma åt och använda det är en viktig funktion som visas som den **formaren** färdigheter inom kompetens.  

Projektioner är lättare att definiera när du har ett objekt i trädet berikande som matchar schemat för projektion. Den uppdaterade [formaren färdighet](cognitive-search-skill-shaper.md) kan du skapa ett objekt från olika noder i trädet berikande och överordnade dem under en ny nod. Den **formaren** färdighet kan du definiera komplexa typer med kapslade objekt.

När du har en ny form definieras som innehåller alla de element som du behöver för att projicera ut kan använda du nu formen som källa för dina projektioner eller som indata till en annan färdigheter.

## <a name="table-projections"></a>Tabellen projektioner

Eftersom det gör att importera enklare, rekommenderar vi att tabellen projektioner för datautforskning med Power BI. Dessutom kan tabellen projektioner för ändra ändra kardinalitet mellan Tabellrelation. 

Du kan projicera ett enskilt dokument i indexet i flera tabeller som bevaras relationerna. När du projicerar till flera tabeller, projected formen klar i varje tabell, såvida inte en underordnad nod är källan till en annan tabell i samma grupp.

### <a name="defining-a-table-projection"></a>Definiera en tabell-projektion

När du definierar en tabell projektion inom den `knowledgeStore` element av din kompetens, starta genom att mappa en nod på trädet berikande käll-tabellen. Den här noden är vanligtvis resultatet av en **formaren** färdigheter som du har lagt till i listan över kunskaper för att skapa en särskild form som du vill projicera till tabeller. Den nod som du väljer att projektet kan delas till projekt i flera tabeller. Tabeller-definitionen är en lista över tabeller som du vill projicera. 

Varje tabell kräver tre egenskaper:

+ Tabellnamn: Namnet på tabellen i Azure Storage.

+ generatedKeyName: Kolumnnamnet för den nyckel som unikt identifierar den här raden.

+ Källa: Noden från trädet berikande du sourcing din enrichments från. Detta är vanligtvis resultatet av en formaren, men det kan vara resultatet av någon av färdigheterna.

Här är ett exempel på tabellen projektioner.

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
Som visas i det här exemplet, nyckelfraser och entiteter är modellerade i olika tabeller och innehåller en referens till överordnat (%{maintable/) för varje rad. 

Följande bild är en referens till Caselaw Övning i [hur du kommer igång med knowledge store](knowledge-store-howto.md). I ett scenario där ett ärende har flera åsikter och varje åsikt berikats genom att identifiera enheter som ingår i detta kan du utforma projektionerna som visas här.

![Enheter och relationer i tabeller](media/knowledge-store-projection-overview/TableRelationships.png "modellering relationer i tabellen projektioner")

## <a name="object-projections"></a>Objektet projektioner

Objektet projektioner är JSON-representationer av trädet berikande, som kan hämtas från en nod. I många fall kan samma **formaren** färdigheter som skapar en tabell projektion kan användas för att generera en projektion för objektet. 

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Generera en projektion objekt kräver några objektspecifika attribut:

+ storageContainer: Behållaren där objekt som ska sparas
+ Källa: Sökvägen till noden i trädet berikande, som är roten för projektionen
+ Nyckel: En sökväg som representerar en unik nyckel för objektet som ska lagras. Den används för att skapa namnet på bloben i behållaren.

## <a name="projection-lifecycle"></a>Projektion livscykel

Dina projektioner har en livscykel som är kopplad till datakällan i datakällan. Eftersom dina data uppdateras och omindexeras, uppdateras dina projektioner med resultatet av enrichments försäkra dig om dina projektioner är konsekvent med data i datakällan. Projektionerna ärver ta bort principen som du har konfigurerat för ditt index. 

## <a name="using-projections"></a>Med hjälp av projektioner

När indexeraren har körts kan du läsa planerade data i behållare och tabeller som du har angett via projektioner. 

Utforska i Power BI är lika enkelt som att Azure-tabellagring som datakälla för analys. Du kan enkelt skapa en uppsättning visualiseringar på dina data att utnyttja relationer i.

Du kan också om du vill använda avancerad och data i en datavetenskapspipeline kan du [in data från BLOB-objekt i en Pandas-DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Om du vill exportera data från knowledge store slutligen har anslutningsappar för att exportera data och hamnar i databasen för valfri Azure Data Factory. 

## <a name="next-steps"></a>Nästa steg

Skapa din första knowledge store med hjälp av exempeldata och instruktioner som ett nästa steg.

> [!div class="nextstepaction"]
> [Så här skapar du en knowledge store](knowledge-store-howto.md).