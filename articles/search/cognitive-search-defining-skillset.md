---
title: Skapa en kunskaper i en kognitiva Sök pipeline (Azure Search) | Microsoft Docs
description: Definiera data extrahering naturligt språk bearbetning, eller avbildning analys steg för att utöka och extrahera strukturerad information från dina data för använder i Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.openlocfilehash: 997b106f748a2f18e8141f77f3b9ff8bb6b9d971
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268236"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Så här skapar du en kunskaper i en berikande pipeline

Kognitiva sökningen extraherar och förbättra data så att den blir sökbara i Azure Search. Vi kallar extrahering och berikande *kognitiva kunskaper*, kombinerat till en *kunskaper* refererade till under indexeringen. En kunskaper kan använda [fördefinierade kunskaper](cognitive-search-predefined-skills.md) eller anpassade kunskaper (se [exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md) mer information).

I den här artikeln får du lära dig hur du skapar en berikande pipeline för kunskaper som du vill använda. En kunskaper som är kopplad till en Azure Search [indexeraren](search-indexer-overview.md). En del av pipeline-design, beskrivs i den här artikeln konstruera kunskaper sig själv. 

> [!NOTE]
> En annan del av pipeline-design är att ange en indexerare som beskrivs i den [nästa steg](#next-step). En indexerare definition innehåller en referens till kunskaper plus fältmappningar som används för att ansluta indata och utdata i mål-index.

Huvudpunkter att komma ihåg:

+ Du kan bara ha en kunskaper per indexeraren.
+ En kunskaper måste ha minst en kunskap.
+ Du kan skapa flera kunskaper av samma typ (till exempel varianter av en avbildning analys kunskaper), men varje kunskaper kan bara användas en gång i samma kunskaper.

## <a name="begin-with-the-end-in-mind"></a>Börja med slutet i åtanke

En rekommenderade första steget är att bestämma vilka data som ska extraheras från dina rådata och hur du vill använda dessa data i en lösning för sökning. Skapar en bild av hela berikande pipeline kan hjälpa dig att identifiera de nödvändiga stegen.

Anta att du är intresserad av bearbetning av en uppsättning finansiella analytiker kommentarer. För varje fil som du vill extrahera företagsnamn och allmänna sentiment kommentarer. Du kan också skriva en anpassad enricher som använder Bing enheten Search-tjänsten för att få ytterligare information om företaget, till exempel vilken typ av företaget bedriver verksamhet. I princip du vill extrahera information på följande indexerat för varje dokument:

| post-text | Företag | Sentiment | beskrivningar av företaget |
|--------|-----|-----|-----|
|exempelpost| [”Microsoft”, ”LinkedIn”] | 0,99 | [”Microsoft Corporation är en teknik för American flerspråkig företag...”, ”LinkedIn är en och anställningen-affärsorienterade sociala nätverk...”]

Följande diagram illustrerar en hypotetiska berikande pipeline:

![En hypotetiska berikande pipeline](media/cognitive-search-defining-skillset/sample-skillset.png "en hypotetiska berikande pipeline")


När du har verkliga uppfattning om vad du vill i pipelinen, kan du ange kunskaper som tillhandahåller de här stegen. Funktionen är uttrycks kunskaper när du överför din indexeraren definition till Azure Search. Mer information om hur du överför indexeraren finns i [indexeraren dokumentationen](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


I diagrammet skickar den *dokumentet knäcka* steg sker automatiskt. I princip Azure Search öppnas välkända filer och skapar en *innehåll* fält som innehåller den text som extraheras från varje dokument. Vit rutorna finns inbyggda enrichers och prickad ”Bing enheten” sökrutan representerar en anpassad enricher som du skapar. Enligt beskrivningen, innehåller kunskaper tre kunskaper.

## <a name="skillset-definition-in-rest"></a>Definition av kunskaper i REST

En kunskaper definieras som en matris med kunskaper. Varje kunskaper definierar källan för indata och namnet på de utdata som genereras. Med hjälp av den [skapa kunskaper REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset), kan du definiera en kunskaper som motsvarar föregående diagram: 

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

När du skapar en kunskaper måste ange du en beskrivning för att göra den kunskaper själva dokumentera. En beskrivning är valfritt men användbart för att hålla reda på vad en kunskaper gör. Eftersom kunskaper är en JSON-dokument som inte tillåter kommentarer, måste du använda en `description` elementet för denna.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Nästa del i kunskaper är en matris med kunskaper. Du kan se varje kunskaper som en primitiv av berikande. Varje kunskaper utför en liten aktivitet i denna berikande pipeline. Var och en tar indata (eller en uppsättning indata) och returnerar några utdata. De följande avsnitten fokuserar på hur du anger fördefinierade och anpassade kunskaper länkning kunskaper tillsammans med indata- och referenser. Indata kan komma från källdata eller från en annan kunskaper. Utdata kan mappas till ett fält i en sökindex eller används som indata för en underordnad kunskap.

## <a name="add-predefined-skills"></a>Lägg till fördefinierade kunskaper

Nu ska vi titta på den första kunskaper som är den fördefinierade [med namnet entitet recognition kunskaper](cognitive-search-skill-named-entity-recognition.md):

```json
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
      ],      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Varje fördefinierade kunskaper har `odata.type`, `input`, och `output` egenskaper. Kunskaper-specifika egenskaper innehåller ytterligare information som gäller för kompetensen. För entiteten recognition `categories` är en entitet från en fast uppsättning entitetstyper som känner igen pretrained modellen.

* Varje kunskaper ska ha en ```"context"```. Kontexten representerar den nivå som äger rum. I kompetensen ovan är kontexten att hela dokumentet, vilket innebär att namngiven enhet recognition kompetensen anropas en gång per dokument. Utdata produceras även på den nivån. Mer specifikt ```"organizations"``` genereras som medlem i ```"/document"```. I underordnade kunskaper som du kan referera till den nya informationen som ```"/document/organizations"```.  Om den ```"context"``` fältet inte uttryckligen har angett är standardkontexten dokumentet.

* Kompetensen har en inmatning som kallas ”text”, med en inkommande uppsättningen källan till ```"/document/content"```. Kompetensen (kallas entitet recognition) körs på den *innehåll* för varje dokument som är en standard fält som skapats av Azure blob-indexering. 

* Kompetensen har en utdata som kallas ```"organizations"```. Utdata finns endast under bearbetning. Referera för att kedja dessa utdata till en underordnad kunskap indata, utdata som ```"/document/organizations"```.

* För ett visst dokument, värdet för ```"/document/organizations"``` är en matris med organisationer som extraheras från texten. Exempel:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Vissa situationer anropa för att referera till varje element i en matris separat. Anta att du vill skicka varje element i ```"/document/organizations"``` separat till en annan kunskaper (till exempel den anpassade Bing enheten Sök enricher). Du kan referera till varje element i matrisen genom att lägga till en asterisk sökvägen: ```"/document/organizations/*"``` 

Andra kunskap för sentiment extrahering följer samma mönster som första enricher. Det tar ```"/document/content"``` som indata och returnerar ett sentiment poängen för varje förekomst av innehåll. Eftersom du inte har angett den ```"context"``` fältet explicit, utdata (mySentiment) är nu underordnad ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Lägg till en anpassad kunskap

Återkalla strukturen för anpassade Bing enheten Sök enricher:

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

Den här definitionen är en anpassad kunskap som anropar ett webb-API som en del av processen berikande. För varje organisation som identifieras av namngiven enhet recognition anropar kompetensen ett webb-API för att hitta beskrivningen av den organisationen. Dirigering av när webb-API-anrop och hur flödar uppgifter hanteras internt berikande-motorn. Dock måste krävs för att anropa den här anpassade API initieringen anges i JSON (som uri, httpHeaders och indata förväntades). Anvisningar för att skapa en anpassad webb-API för berikande pipelinen finns [hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md).

Observera att Kontextfältet ”” anges till ```"/document/organizations/*"``` med en asterisk, vilket innebär att steget berikande kallas *för varje* organisation under ```"/document/organizations"```. 

Utgående, genereras i det här fallet en företagsbeskrivning, för varje organisation som identifieras. När du refererar till beskrivningen i ett efterföljande steg (till exempel i viktiga frasen extrahering) du skulle använda sökvägen ```"/document/organizations/*/description"``` gör. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Enrichments skapa struktur utanför ostrukturerad information

Kunskaper genererar strukturerad information utanför Ostrukturerade data. Beakta följande exempel:

*”Den fjärde kvartal Microsoft loggade 1.1 miljarder $ i intäkter från LinkedIn, sociala nätverk företaget har köpt det senaste året. Förvärv hjälper Microsoft att kombinera LinkedIn-funktioner med dess CRM och Office-funktioner. Aktieägares är glad över med förloppet hittills ”.*

Ett troligt resultat skulle vara en genererad struktur som liknar följande bild:

![Exempel utdata strukturen](media/cognitive-search-defining-skillset/enriched-doc.png "exempel utdata struktur")

Kom ihåg att den här strukturen är intern. Du kan inte att hämta det här diagrammet i kod.

<a name="next-step"></a>
## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med berikande pipeline och kunskaper fortsätta med [så att referera till anteckningar i en kunskaper](cognitive-search-concept-annotations-syntax.md) eller [hur du mappar utdata till fält i ett index](cognitive-search-output-field-mapping.md). 
