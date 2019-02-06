---
title: Skapa en kompetens i en pipeline för kognitiv sökning – Azure Search
description: Definiera extrahering av data, naturlig språkbearbetning, eller avbildning analysis steg för att utöka och extrahera strukturerad information från dina data för använder i Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 9369e076517e295a7d17011e024353614ec8ad46
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751981"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Så här skapar du en kompetens i en pipeline för berikande

Kognitiv sökning extraherar och förbättra data så att den blir sökbar i Azure Search. Vi kallar extrahering och berikande *kognitiva kunskaper*, kombinerade till ett *kompetens* refererade till under indexering. En kompetens kan använda [fördefinierade kunskaper](cognitive-search-predefined-skills.md) eller anpassade funktioner (se [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md) för mer information).

I den här artikeln får du lära dig hur du skapar en pipeline med funktioner för de färdigheter som du vill använda. En kompetens är ansluten till ett Azure Search [indexeraren](search-indexer-overview.md). En del av pipeline-design, som beskrivs i den här artikeln är att konstruera kompetens själva. 

> [!NOTE]
> En annan del av pipelinen design är att ange en indexerare som beskrivs i den [nästa steg](#next-step). En indexerardefinition innehåller en referens till kompetens, plus fältmappningar som används för att ansluta indata till utdata i målindex.

Viktiga saker att komma ihåg:

+ Du kan bara ha en kompetens per indexerare.
+ En kompetens måste ha minst en kunskap.
+ Du kan skapa flera kunskaper av samma typ (till exempel varianter av en avbildning analysis färdighet).

## <a name="begin-with-the-end-in-mind"></a>Börja med end i åtanke

En rekommenderade första steget är att bestämma vilka data som ska extraheras från dina rådata och hur du vill använda dessa data i en söklösning. Skapa en illustration av din pipeline för hela berikande kan hjälpa dig att identifiera steg som krävs.

Anta att du är intresserad av att bearbetning av en uppsättning kommentarer av analytiker som finansiella. För varje fil som du vill extrahera företagsnamn och allmän känslan i kommentarerna. Du kanske också vill skriva en anpassad enricher som använder Entitetssökning i Bing-tjänsten för att hitta mer information om företag, till exempel vilken typ av företaget bedriver verksamhet. I princip du vill extrahera information så här indexeras för varje dokument:

| record-text | Företag | sentiment | företagets beskrivningar |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99 | [”Microsoft Corporation är en American multinationella teknikföretag...”, ”LinkedIn är en och anställning-affärsorienterade sociala nätverk...”]

Följande diagram illustrerar en hypotetisk berikande pipeline:

![En hypotetisk berikande pipeline](media/cognitive-search-defining-skillset/sample-skillset.png "en hypotetisk berikande pipeline")


När du har verkliga uppfattning om vad du vill ha i pipelinen, du kan uttrycka kompetens som ger de här stegen. Funktionen är uttrycks gruppens kunskaper avgör när du överför indexerarens definition till Azure Search. Mer information om hur du överför indexeraren finns den [indexeraren dokumentation](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


I diagrammet, den *dokumentknäckning* steg sker automatiskt. I princip Azure Search kan öppna välkända filer och skapar en *innehåll* fält som innehåller den text som extraheras från varje dokument. Rutorna vit finns inbyggda enrichers och rutan prickad ”Entitetssökning i Bing” representerar en anpassad enricher som du skapar. Enligt, innehåller gruppens kunskaper avgör tre kunskaper.

## <a name="skillset-definition-in-rest"></a>Kompetens definition i REST

En kompetens definieras som en matris med kunskaper. Varje färdighet definierar källan för dess indata och namnet på de utdata som genereras. Med hjälp av den [skapa kompetens REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset), du kan definiera en kompetens som motsvarar till föregående diagram: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
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
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

När du skapar en kompetens måste ange du en beskrivning om du vill göra den kompetens lokal dokumentera. En beskrivning är valfritt, men användbart för att hålla reda på vad som gör en kompetens. Eftersom kompetens är ett JSON-dokument som inte tillåter kommentarer, måste du använda en `description` element för detta.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Nästa del i gruppens kunskaper avgör är en matris av kunskaper. Du kan tänka på varje färdighet som en primitiv hämtas av funktioner. Varje färdighet utför en liten uppgift i den här berikande pipeline. Var och en tar indata (eller en uppsättning indata) och returnerar några utdata. Följande avsnitt fokuserar på hur du anger fördefinierade och anpassade kunskaper, länkning kunskaper tillsammans med indata- och referenser. Indata kan komma från källdata eller från en annan färdigheter. Utdata kan mappas till ett fält i ett sökindex eller användas som indata till en underordnad färdighet.

## <a name="add-predefined-skills"></a>Lägg till fördefinierade kunskaper

Låt oss titta på den första färdighet, vilket är den fördefinierade [entitet erkännande färdighet](cognitive-search-skill-entity-recognition.md):

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

* Varje fördefinierade färdighet har `odata.type`, `input`, och `output` egenskaper. Egenskaper för färdighet innehåller ytterligare information som gäller för den färdigheten. För igenkänning av entiteter `categories` är en entitet från en fast uppsättning entitetstyper som pretrained modellen kan identifiera.

* Varje färdighet bör ha en ```"context"```. Kontexten representerar den nivå som äger rum. I färdighet ovan är kontexten hela dokumentet, vilket innebär att namngiven entitet erkännande färdighet anropas en gång per dokument. Utdata produceras också på den nivån. Mer specifikt ```"organizations"``` genereras som en medlem i ```"/document"```. I underordnade färdigheter som du kan referera till information som du skapade nyss ```"/document/organizations"```.  Om den ```"context"``` fältet har inte angetts uttryckligen, Standardkontexten är dokumentet.

* Kompetensen har en inmatning som kallas ”text”, med en källa indata till ```"/document/content"```. Färdighet (med namnet entitetsidentifiering) körs på den *innehåll* fält för varje dokument och som är ett standardfält som skapats av Azure blob-indexeraren. 

* Kompetensen har en utdata som kallas ```"organizations"```. Utdata finns endast under bearbetning. Om du vill länka dessa utdata till en underordnad färdighet inkommande, referera till utdata som ```"/document/organizations"```.

* För ett visst dokument, värdet för ```"/document/organizations"``` är en matris med organisationer som extraheras från texten. Exempel:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Vissa situationer kräver för att referera till varje element i en matris separat. Anta exempelvis att du vill skicka varje element i ```"/document/organizations"``` separat till en annan färdighet (till exempel den anpassade Bing entitet search enricher). Du kan referera till varje element i matrisen genom att lägga till en asterisk i sökvägen: ```"/document/organizations/*"``` 

Andra kunskap för extrahering av sentiment följer samma mönster som den första enricher. Det tar ```"/document/content"``` som indata och returnerar ett sentimentpoäng för varje förekomst av innehåll. Eftersom du inte har angett den ```"context"``` fältet uttryckligen, utdata (mySentiment) är nu en underordnad ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Lägg till en anpassad kompetens

Återkalla strukturen för anpassad Bing entitet search enricher:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
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

Den här definitionen är en [anpassade färdighet](cognitive-search-custom-skill-web-api.md) som anropar ett webb-API som en del av processen berikande. För varje organisation som identifieras av igenkänning av namngivna entiteter anropar kompetensen ett webb-API för att hitta beskrivningen av organisationen. Dirigering av när att anropa webb-API och hur flödar den information som tas emot hanteras internt av berikande-motorn. Dock måste initieringen behövs för att anropa den här anpassade API: T anges i JSON (till exempel uri, httpHeaders och indata förväntades). Vägledning i att skapa ett anpassat webb-API för berikande pipelinen finns i [hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md).

Lägg märke till att fältet ”kontexten” anges till ```"/document/organizations/*"``` med en asterisk, vilket innebär att det berikande steget kallas *för var och en* organisation under ```"/document/organizations"```. 

Utdata genereras i det här fallet en företagsbeskrivningen för varje organisation som har identifierats. När det gäller beskrivningen i en underordnad steget (till exempel i extrahering av diskussionsämne) du använder sökvägen ```"/document/organizations/*/description"``` att göra detta. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Enrichments skapar strukturen utanför ostrukturerad information

Gruppens kunskaper avgör genererar strukturerad information från Ostrukturerade data. Ta följande som exempel:

*”I det fjärde kvartalet, Microsoft loggas 1.1 miljarder USD i intäkter från LinkedIn, sociala nätverk företaget den köpte förra året. Förvärvet hjälper Microsoft att kombinera LinkedIn-funktioner med dess CRM- och Office-funktioner. Aktieägares är glada över med förloppet hittills ”.*

Ett resultat som sannolikt skulle vara genererade struktur liknar den i följande bild:

![Exemplet utdata struktur](media/cognitive-search-defining-skillset/enriched-doc.png "exempel utdata struktur")

Kom ihåg att den här strukturen är interna. Du kan inte att hämta det här diagrammet i kod.

<a name="next-step"></a>
## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med berikande pipeline och kompetens kan fortsätta med [så referera till kommentarer i en kompetens](cognitive-search-concept-annotations-syntax.md) eller [mappa utdata till fält i ett index](cognitive-search-output-field-mapping.md). 
