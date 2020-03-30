---
title: Skapa en kunskapsuppsättning
titleSuffix: Azure Cognitive Search
description: Definiera dataextrahering, bearbetning av naturligt språk eller bildanalyssteg för att berika och extrahera strukturerad information från dina data för användning i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754562"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Så här skapar du en kompetens i en AI-anrikningspipeline i Azure Cognitive Search 

AI-anrikning extraherar och berikar data för att göra det sökbart i Azure Cognitive Search. Vi kallar extraktion och anrikning steg *kognitiva färdigheter,* kombineras till en *kompetens som* refereras under indexering. En kompetens kan använda inbyggda kunskaper eller anpassade kunskaper (se [Exempel: Skapa en anpassad färdighet i en AI-anrikningspipeline](cognitive-search-create-custom-skill-example.md) för mer information). [built-in skills](cognitive-search-predefined-skills.md)

I den här artikeln får du lära dig hur du skapar en anrikningspipeline för de kunskaper du vill använda. En kompetens är kopplad till en Azure Cognitive [Search-indexerare](search-indexer-overview.md). En del av pipeline design, som omfattas av denna artikel, är att bygga kompetensen själv. 

> [!NOTE]
> En annan del av pipeline-designen är att ange en indexerare som omfattas av [nästa steg](#next-step). En indexeringsdefinition innehåller en referens till kompetensen, plus fältmappningar som används för att ansluta indata till utdata i målindexet.

Viktiga punkter att komma ihåg:

+ Du kan bara ha en kompetens per indexerare.
+ En kompetens måste ha minst en färdighet.
+ Du kan skapa flera kunskaper av samma typ (till exempel varianter av en bildanalysfärdighet).

## <a name="begin-with-the-end-in-mind"></a>Börja med slutet i åtanke

Ett rekommenderat första steg är att bestämma vilka data som ska extraheras från dina rådata och hur du vill använda dessa data i en söklösning. Genom att skapa en illustration av hela anrikningspipelinen kan du identifiera de nödvändiga stegen.

Anta att du är intresserad av att bearbeta en uppsättning finansiella analytiker kommentarer. För varje fil vill du extrahera företagsnamn och den allmänna känslan i kommentarerna. Du kanske också vill skriva en anpassad berikare som använder tjänsten Bing Entity Search för att hitta ytterligare information om företaget, till exempel vilken typ av verksamhet företaget är bedrivet i. I huvudsak vill du extrahera information som följande, indexerad för varje dokument:

| post-text | Företag | Känsla | företagets beskrivningar |
|--------|-----|-----|-----|
|exempel-post| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation är ett amerikanskt multinationellt teknikföretag ..." , "LinkedIn är ett affärs- och sysselsättningsorienterat socialt nätverkande..."]

Följande diagram illustrerar en hypotetisk anrikningspipeline:

![En hypotetisk anrikningspipeline](media/cognitive-search-defining-skillset/sample-skillset.png "En hypotetisk anrikningspipeline")


När du har en rättvis uppfattning om vad du vill ha på gång kan du uttrycka den kompetens som ger dessa steg. Funktionellt uttrycks kompetensuppsättningen när du överför indexeringsdefinitionen till Azure Cognitive Search. Mer information om hur du laddar upp indexeraren finns i [indexeraren.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)


I diagrammet sker *dokumentsprickningssteget* automatiskt. I huvudsak vet Azure Cognitive Search hur man öppnar välkända filer och skapar ett *innehållsfält* som innehåller texten som extraheras från varje dokument. De vita rutorna är inbyggda berikare och den prickade rutan "Bing Entitetsökning" representerar en anpassad berikare som du skapar. Som illustreras innehåller kompetensen tre färdigheter.

## <a name="skillset-definition-in-rest"></a>Kompetensdefinition i REST

En kompetens definieras som en rad färdigheter. Varje färdighet definierar källan till dess indata och namnet på de producerade utdata. Med [REST-API:et Skapa kunskaper](https://docs.microsoft.com/rest/api/searchservice/create-skillset)kan du definiera en kompetensuppsättning som motsvarar det föregående diagrammet: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

När du skapar en kompetens kan du ge en beskrivning för att göra kompetensen självdokumenterande. En beskrivning är valfri, men användbar för att hålla reda på vad en kompetens gör. Eftersom skillset är ett JSON-dokument, som inte `description` tillåter kommentarer, måste du använda ett element för detta.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Nästa del i kompetensen är en rad färdigheter. Du kan tänka på varje färdighet som en primitiv av berikning. Varje färdighet utför en liten uppgift i den här anrikningspipelinen. Var och en tar en ingång (eller en uppsättning ingångar) och returnerar vissa utdata. De närmaste avsnitten fokuserar på hur man anger inbyggda och anpassade färdigheter, kedja färdigheter tillsammans genom input och output referenser. Indata kan komma från källdata eller från en annan färdighet. Utdata kan mappas till ett fält i ett sökindex eller användas som indata till en nedströms färdighet.

## <a name="add-built-in-skills"></a>Lägg till inbyggda färdigheter

Låt oss titta på den första färdigheten, som är den inbyggda [entitetsigenkänningsfärdigheten:](cognitive-search-skill-entity-recognition.md)

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Varje inbyggd färdighet `odata.type` `input`har `output` , och egenskaper. Färdighetsspecifika egenskaper ger ytterligare information som gäller för den färdigheten. För entitetsredovisning `categories` är en entitet bland en fast uppsättning entitetstyper som den förtränade modellen kan känna igen.

* Varje färdighet ```"context"```bör ha en . Sammanhanget representerar den nivå på vilken verksamheten äger rum. I färdigheten ovan är kontexten hela dokumentet, vilket innebär att entitetsigenkänningsfärdigheten anropas en gång per dokument. Utdata produceras också på den nivån. Mer specifikt ```"organizations"``` genereras som medlem ```"/document"```i . I nedströms färdigheter kan du referera ```"/document/organizations"```till den här nyskapade informationen som .  Om ```"context"``` fältet inte uttryckligen anges är standardkontexten dokumentet.

* Färdigheten har en indata som kallas "text", med en källinmatning inställd på ```"/document/content"```. Färdigheten (entitetsigenkänning) fungerar i *innehållsfältet* i varje dokument, vilket är ett standardfält som skapats av Azure blob-indexeraren. 

* Färdigheten har en ```"organizations"```utdata som kallas . Utdata finns endast under bearbetningen. Om du vill kedja utdata till en indata ```"/document/organizations"```för en nedströms-färdighet refererar du till utdata som .

* För ett visst dokument ```"/document/organizations"``` är värdet av en matris med organisationer som extraherats från texten. Ett exempel:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Vissa situationer kräver att varje element i en matris refereras separat. Anta till exempel att du vill ```"/document/organizations"``` skicka varje element separat till en annan färdighet (till exempel den anpassade Bing-entitetens sökberikare). Du kan referera till varje element i matrisen genom att lägga till en asterisk i sökvägen:```"/document/organizations/*"``` 

Den andra färdigheten för sentimentutvinning följer samma mönster som den första berikaren. Det ```"/document/content"``` tar som indata och returnerar en sentimentpoäng för varje innehållsinstans. Eftersom du inte ```"context"``` uttryckligen har angett fältet är utdata (mySentiment) nu underordnad ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Lägga till en anpassad färdighet

Återkalla strukturen för den anpassade Bing-entitetens sökberikare:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Den här definitionen är en [anpassad färdighet](cognitive-search-custom-skill-web-api.md) som anropar ett webb-API som en del av anrikningsprocessen. För varje organisation som identifieras av entitetsigenkänning anropar den här färdigheten ett webb-API för att hitta beskrivningen av den organisationen. Orkestreringen av när du ska anropa webb-API:et och hur du flödar den mottagna informationen hanteras internt av anrikningsmotorn. Initieringen som krävs för att anropa det här anpassade API:et måste dock anges i JSON (till exempel uri, httpHeaders och förväntade indata). Information om hur du skapar ett anpassat webb-API för anrikningspipelinen finns i [Så här definierar du ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md).

Observera att fältet "kontext" ```"/document/organizations/*"``` är inställt på med en asterisk, vilket ```"/document/organizations"```innebär att anrikningssteget anropas för *varje* organisation under . 

Utdata, i det här fallet en företagsbeskrivning, genereras för varje organisation som identifieras. När du refererar till beskrivningen i ett steg nedströms (till exempel ```"/document/organizations/*/description"``` i extrahering av nyckelfraser) använder du sökvägen för att göra det. 

## <a name="add-structure"></a>Lägg till struktur

Kompetensen genererar strukturerad information av ostrukturerade data. Ta följande som exempel:

*"Under det fjärde kvartalet loggade Microsoft 1,1 miljarder dollar i intäkter från LinkedIn, det sociala nätverksföretag man köpte förra året. Förvärvet gör det möjligt för Microsoft att kombinera LinkedIn-funktioner med sina CRM- och Office-funktioner. Aktieägarna är glada över framstegen hittills."*

Ett troligt resultat skulle vara en skapad struktur som liknar följande illustration:

![Exempel på utdatastruktur](media/cognitive-search-defining-skillset/enriched-doc.png "Exempel på utdatastruktur")

Hittills har den här strukturen endast varit intern, endast minne och endast använts i Azure Cognitive Search-index. Tillägget av en kunskapsbutik ger dig ett sätt att spara formade anrikningar för användning utanför sökningen.

## <a name="add-a-knowledge-store"></a>Lägga till ett kunskapslager

[Knowledge Store](knowledge-store-concept-intro.md) är en förhandsgranskningsfunktion i Azure Cognitive Search för att spara ditt berikade dokument. Ett kunskapslager som du skapar, som backas upp av ett Azure-lagringskonto, är den databas där dina utökade data landar. 

En definition av kunskapslager läggs till i en kompetens. En genomgång av hela processen finns [i Skapa ett kunskapslager i REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Du kan välja att spara de berikade dokumenten som tabeller med hierarkiska relationer bevarade eller som JSON-dokument i blob-lagring. Utdata från någon av färdigheterna i kompetensen kan anskaffas som indata för projektionen. Om du vill projicera data till en viss form kan den uppdaterade [shaper-färdigheten](cognitive-search-skill-shaper.md) nu modellera komplexa typer som du kan använda. 

<a name="next-step"></a>

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med anrikning pipeline och skillsets, fortsätta med [Hur referera anteckningar i en kompetens](cognitive-search-concept-annotations-syntax.md) eller Hur man mappar [utdata till fält i ett index](cognitive-search-output-field-mapping.md). 
