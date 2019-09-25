---
title: Arbeta med projektioner i ett kunskaps lager (för hands version) – Azure Search
description: Spara och forma dina berikade data från AI indexerings pipelinen för användning i andra scenarier än Sök
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265190"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Arbeta med projektioner i ett kunskaps lager i Azure Search

> [!Note]
> Kunskaps lagret är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Azure Search möjliggör innehålls anrikning genom AI kognitiva kunskaper och anpassade kunskaper som en del av indexeringen. Du kan lägga till strukturer i dina dokument och göra sökningen mer effektiv. I många fall är de omfattande dokumenten användbara för andra scenarier än Sök, till exempel för kunskaps utvinning.

Projektioner, en komponent i [kunskaps lager](knowledge-store-concept-intro.md), är vyer av omfattande dokument som kan sparas i fysiska lagrings utrymmen för kunskaps utvinning. Med en projektion kan du "projicera" dina data i en form som överensstämmer med dina behov och bevarar relationer så att verktyg som Power BI kan läsa data utan ytterligare arbete. 

Projektioner kan vara tabell, med data som lagras i rader och kolumner i Azure Table Storage eller JSON-objekt som lagras i Azure Blob Storage. Du kan definiera flera projektioner av dina data när de är omfattande. Detta är användbart när du vill att samma data formad annorlunda för enskilda användnings fall. 

Kunskaps lagret stöder två typer av projektioner:

+ **Tabeller**: För data som bäst visas som rader och kolumner kan du med tabell projektioner definiera en schematiserade-form eller projektion i Table Storage. 

+ **Objekt**: När du behöver en JSON-representation av dina data och hur de ska berikas, sparas objekt projektion som blobbar.

Om du vill se projektioner som definierats i sammanhanget steg [för steg hur du kommer igång med kunskaps lager](knowledge-store-howto.md).

## <a name="projection-groups"></a>Projektions grupper

I vissa fall måste du projicera dina berikade data i olika former för att möta olika mål. I kunskaps lagret kan du definiera flera grupper av projektioner. Projektions grupper har följande nyckel egenskaper för ömsesidig exklusivitet och relateradhet.

### <a name="mutually-exclusivity"></a>Ömsesidigt exklusivitet

Allt innehåll som projiceras i en enda grupp är oberoende av data som projiceras i andra projektions grupper. Detta innebär att du kan ha samma data formad annorlunda, men upprepas i varje projektions grupp. 

En begränsning som tillämpas i projektions grupper är en ömsesidig exklusivitet med projektions typer med en projektions grupp. Du kan bara definiera tabell projektioner eller objekt projektioner inom en enskild grupp. Om du vill ha både tabeller och objekt definierar du en projektions grupp för tabeller och en andra projektions grupp för objekt.

### <a name="relatedness"></a>Relateradhet

Allt innehåll som projiceras inom en enskild projektions grupp bevarar relationer i data. Relationerna baseras på en genererad nyckel och varje underordnad nod behåller en referens till den överordnade noden. Relationer omfattar inte projektions grupper, och tabeller eller objekt som skapats i en projektions grupp har ingen relation till data som genererats i andra projektions grupper.

## <a name="input-shaping"></a>Inmatad form
Att hämta data i rätt form eller struktur är nyckeln till effektiv användning, vara en tabell eller objekt. Möjligheten att forma eller strukturera dina data baserat på hur du planerar åtkomst till och använder det är en viktig funktion som visas som **formaren** -kompetensen i färdigheter.  

Projektioner är enklare att definiera när du har ett objekt i det berikande trädet som matchar projektionens schema. Med den uppdaterade [formaren-kunskapen](cognitive-search-skill-shaper.md) kan du skapa ett objekt från olika noder i ditt anriknings träd och överordnade dem under en ny nod. Med **formaren** -kompetensen kan du definiera komplexa typer med kapslade objekt.

När du har definierat en ny form som innehåller alla element som du behöver för att projicera, kan du nu använda den här formen som källa för dina projektioner eller som inmatade i en annan färdighet.

## <a name="table-projections"></a>Tabell projektioner

Eftersom det gör det enklare att importera data, rekommenderar vi tabell prognoser för data utforskning med Power BI. Dessutom tillåter tabell projektioner att ändra kardinalitet mellan tabell relationer. 

Du kan projicera ett enskilt dokument i ditt index i flera tabeller och bevara relationerna. När du projicerar till flera tabeller, kommer hela formen att projiceras i varje tabell, om inte en underordnad nod är källan till en annan tabell inom samma grupp.

### <a name="defining-a-table-projection"></a>Definiera en tabell projektion

När du definierar en tabell projektion i `knowledgeStore` färdigheter-elementet, börjar du med att mappa en nod i berikande träd till tabell källan. Den här noden är vanligt vis resultatet av en **formaren** -färdighet som du har lagt till i listan med kunskaper för att skapa en speciell form som du behöver i projektet i tabeller. Den nod du väljer till projekt kan segmenteras till projekt i flera tabeller. Tabell definitionen är en lista över tabeller som du vill projicera. 

#### <a name="projection-slicing"></a>Segmentering av projektion
När du definierar en tabell projektions grupp kan en enda nod i ett berikande träd segmenteras i flera relaterade tabeller. Om du lägger till en tabell med en käll Sök väg som är underordnad en befintlig tabell projektion kommer den underordnade noden att bli segmenterad från den överordnade noden och projiceras i den nya ännu relaterade tabellen. På så sätt kan du definiera en enskild nod i en formaren-färdighet som kan vara källa för alla tabell projektioner.

Varje tabell kräver tre egenskaper:

+ TableName Namnet på tabellen i Azure Storage.

+ generatedKeyName: Kolumn namnet för den nyckel som unikt identifierar den här raden.

+ Källicensservern Noden från det anriknings träd du kan använda för att du ska kunna använda. Detta är vanligt vis resultatet av en formaren, men det kan vara utdata från någon av färdigheterna.

Här är ett exempel på tabell projektioner.

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
Som det visas i det här exemplet är nyckel fraser och entiteter modellerade i olika tabeller och kommer att innehålla en referens tillbaka till överordnad (MainTable) för varje rad. 

Följande bild är en referens till Caselaw-övningen för [att komma igång med kunskaps lager](knowledge-store-howto.md). I ett scenario där ett ärende har flera åsikter och varje åsikt har berikats genom att identifiera entiteter som finns i det kan du utforma projektionerna som de visas här.

![Entiteter och relationer i tabeller](media/knowledge-store-projection-overview/TableRelationships.png "Modellerings relationer i tabell projektioner")

## <a name="object-projections"></a>Objekt projektioner

Objekt projektioner är JSON-representationer av det berikande trädet som kan hämtas från vilken nod som helst. I många fall kan samma **formaren** -kompetens som skapar en tabell projektion användas för att generera en objekt projektion. 

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

Att skapa en objekt projektion kräver några objektattribut:

+ storageContainer: Den behållare där objekten ska sparas
+ Källicensservern Sökvägen till noden i det berikande trädet som är roten för projektionen
+ Knapp En sökväg som representerar en unik nyckel för det objekt som ska lagras. Den kommer att användas för att skapa namnet på blobben i behållaren.

## <a name="projection-lifecycle"></a>Projekt livs cykel

Dina projektioner har en livs cykel som är kopplad till data källans källdata. När dina data har uppdaterats och indexerats om, uppdateras dina projektioner med resultaten av de omfattande möjligheterna att se till att dina prognoser är konsekventa med data i data källan. Projektionerna ärver borttagnings principen som du har konfigurerat för ditt index. 

## <a name="using-projections"></a>Använda projektioner

När indexeraren har körts kan du läsa de planerade data i de behållare eller tabeller som du har angett genom projektioner. 

För analys är utforskning i Power BI lika enkelt som att ställa in Azure Table Storage som data källa. Du kan enkelt skapa en uppsättning visualiseringar på dina data som utnyttjar relationerna i.

Om du behöver använda de utförliga data i en data vetenskaps pipeline kan du också [läsa in data från blobbar i en Pandas-DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Slutligen, om du behöver exportera data från kunskaps lagret, Azure Data Factory har kopplingar för att exportera data och landa den i valfri databas. 

## <a name="next-steps"></a>Nästa steg

I nästa steg ska du skapa ditt första kunskaps lager med hjälp av exempel data och instruktioner.

> [!div class="nextstepaction"]
> [Så här skapar du ett kunskaps lager](knowledge-store-howto.md).
