---
title: Skillset-begrepp och arbetsflöde
titleSuffix: Azure Cognitive Search
description: Skillsets är där du skapar en AI-anrikningspipeline i Azure Cognitive Search. Lär dig viktiga begrepp och detaljer om kompetenskomposition.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191031"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Skillset-begrepp och komposition i Azure Cognitive Search

Den här artikeln är för utvecklare som behöver en djupare förståelse för hur anrikning pipeline fungerar och förutsätter att du har en begreppsmässig förståelse för AI anrikningsprocessen. Om du är ny detta koncept, börja med:
+ [AI-anrikning i Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Knowledge Store (förhandsgranskning)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Ange kompetensuppsättningen
En kompetens är en återanvändbar resurs i Azure Cognitive Search som anger en samling kognitiva färdigheter som används för att analysera, omvandla och berika text- eller bildinnehåll under indexeringen. Genom att skapa en kompetens kan du bifoga text- och bildberikande i datainmatningsfasen, extrahera och skapa ny information och nya strukturer från rått innehåll.

En kompetens har tre egenskaper:

+   ```skills```, en oordnad samling färdigheter för vilka plattformen bestämmer körningssekvensen baserat på de indata som krävs för varje färdighet
+   ```cognitiveServices```, den kognitiva tjänstnyckel som krävs för fakturering av kognitiva färdigheter som åberopas
+   ```knowledgeStore```visas det lagringskonto där dina utökade dokument projiceras



Skillsets är författade i JSON. Du kan skapa komplexa skillsets med looping och [förgrening](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) med hjälp av [uttrycksspråket](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Uttrycksspråket använder [JSON-pekarens lömska](https://tools.ietf.org/html/rfc6901) med några ändringar för att identifiera noder i anrikningsträdet. En ```"/"``` korsar en nivå lägre ```"*"``` i trädet och fungerar som en för-varje-operator i sammanhanget. Dessa begrepp beskrivs bäst med ett exempel. För att illustrera några av de begrepp och funktioner, kommer vi att gå igenom [hotellet recensioner prov](knowledge-store-connect-powerbi.md) skillset. Om du vill visa kompetensen när du har följt arbetsflödet för importdata måste du använda en REST API-klient för att [hämta kompetensen](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Anrikningsträd

Om du vill föreställa dig hur en kompetens gradvis berikar dokumentet ska vi börja med hur dokumentet ser ut innan någon berikning. Utdata från dokumentsprickor är beroende av datakällan och det specifika tolkningsläge som valts. Det här är också tillståndet för det dokument som [fältmappningarna](search-indexer-field-mappings.md) kan källat innehåll från när du lägger till data i sökindexet.
![Kunskapslager i pipelinediagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Kunskapslager i pipelinediagram")

När ett dokument är i anrikning pipeline, representeras det som ett träd av innehåll och tillhörande berikande. Detta träd instansieras som utdata av dokument sprickbildning. Formatet för anrikningsträdet gör det möjligt för anrikningspipelinen att koppla metadata till även primitiva datatyper, det är inte ett giltigt JSON-objekt men kan projiceras i ett giltigt JSON-format. I följande tabell visas tillståndet för ett dokument som förs in i anrikningspipelinen:

|Datakällläge\tolkningsläge|Default|JSON, JSON Lines & CSV|
|---|---|---|
|Blob Storage|/dokument/innehåll<br>/dokument/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Ej tillämpligt |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Ej tillämpligt|

 När färdigheter körs lägger de till nya noder i anrikningsträdet. Dessa nya noder kan sedan användas som indata för nedströmskunskaper, projicera till kunskapsarkivet eller mappa till indexfält. Enrichments är inte föränderliga: när de har skapats kan noder inte redigeras. Som dina skillsets blir mer komplexa, så kommer din berikning träd, men inte alla noder i anrikning trädet måste göra det till indexet eller kunskapsarkivet. 

Du kan selektivt bara spara en delmängd av anrikningarna i indexet eller kunskapsarkivet.
För resten av detta dokument kommer vi att anta att vi arbetar med [hotell recensioner exempel](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), men samma begrepp gäller för att berika dokument från alla andra datakällor.

### <a name="context"></a>Kontext
Varje färdighet kräver ett sammanhang. Ett sammanhang avgör:
+   Antalet gånger färdigheten körs, baserat på de valda noderna. För kontextvärden för typsamling kommer det att resultera i att färdigheten anropas en gång för varje instans i samlingen om du lägger till en ```/*``` i slutet. 
+   Där i anrikningsträdet läggs färdighetsutdata till. Utdata läggs alltid till i trädet som underordnade kontextnoden. 
+   Formen på ingångarna. För samlingar på flera nivåer påverkar inställningen av kontexten till den överordnade samlingen formen på indata för färdigheten. Om du till exempel har ett anrikningsträd med en lista över länder, berikas var och en med en lista med tillstånd som innehåller en lista med postnummer.

|Kontext|Indata|Form på indata|Inkallelse av färdighet|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |En lista över alla postnummer i landet |En gång per land |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |En lista över postnummer i delstaten | En gång per kombination av land och stat|

### <a name="sourcecontext"></a>KällaKontext

Den `sourceContext` används endast i skicklighet ingångar och [prognoser](knowledge-store-projection-overview.md). Det används för att konstruera flera nivåer, kapslade objekt. Du kan behöva skapa ett nytt objekt för att antingen skicka det som en indata till en färdighet eller projekt till kunskapsarkivet. Eftersom berikningsnoder kanske inte är ett giltigt JSON-objekt i anrikningsträdet och refererar till en nod i trädet returnerar endast nodens tillstånd när den skapades, med hjälp av anrikningar som färdighetsindata eller projektioner kräver att du skapar ett välformat JSON-objekt. Gör `sourceContext` att du kan skapa ett hierarkiskt, anonymt typobjekt, vilket skulle kräva flera kunskaper om du bara använde kontexten. Användning `sourceContext` visas i nästa avsnitt. Titta på färdighetsutdata som genererade en anrikning för att avgöra om det är ett giltigt JSON-objekt och inte en primitiv typ.

### <a name="projections"></a>Projektioner

Projektion är processen att välja noderna från anrikningsträdet som ska sparas i kunskapsarkivet. Projektioner är anpassade former av dokumentet (innehåll och anrikningar) som kan matas ut som antingen tabell- eller objektprojektioner. Mer information om hur du arbetar med projektioner finns i [Arbeta med projektioner](knowledge-store-projection-overview.md).

![Alternativ för fältmappning](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Alternativ för fältmappning för anrikningspipeline")

Diagrammet ovan beskriver väljaren du arbetar med baserat på var du befinner dig i anrikningspipelinen.

## <a name="generate-enriched-data"></a>Generera berikade data 

Låt oss nu gå igenom hotellet recensioner skillset, kan du följa [handledningen](knowledge-store-connect-powerbi.md) för att skapa kompetens eller [visa](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) skillset. Vi ska titta på:

* hur anrikningsträdet utvecklas med utförandet av varje färdighet 
* hur kontexten och indata fungerar för att avgöra hur många gånger en färdighet utför 
* vad formen på indata baseras på sammanhanget. 

Eftersom vi använder det avgränsade texttolkningsläget för indexeraren representerar ett dokument inom anrikningsprocessen en enda rad i CSV-filen.

### <a name="skill-1-split-skill"></a>Färdighet #1: Delad färdighet 

![anrikningsträd efter dokumentsprickning](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Berikande träd efter dokumentsprickor och före färdighetskörning")

Med skicklighet sammanhang ```"/document/reviews_text"```, denna färdighet `reviews_text`kommer att utföra en gång för . Färdighetsutdata är `reviews_text` en lista där segmenteras i 5000 teckensegment. Utdata från delningsfärdigheten namnges `pages` och läggs till i anrikningsträdet. Med `targetName` funktionen kan du byta namn på en färdighetsutdata innan du läggs till i anrikningsträdet.

Anrikningsträdet har nu en ny nod placerad under kontexten för färdigheten. Den här noden är tillgänglig för alla kunskaper, projektions- eller utdatafältmappningar.


Rotnoden för alla berikningar är `"/document"`. När du arbetar med blob-indexerare har `"/document"` `"/document/content"` noden underordnade noder och `"/document/normalized_images"`. När du arbetar med CSV-data, som vi är i det `"/document"`här exemplet, kommer kolumnnamnen att mappas till noder under . För att komma åt någon av de be enrichments som läggs till en nod av en färdighet behövs den fullständiga sökvägen för berikningen. Om du till exempel vill använda ```pages``` texten från noden som indata till en ```"/document/reviews_text/pages/*"```annan färdighet måste du ange den som .
 
 ![anrikningsträd efter färdighet #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Anrikningsträd efter färdighet #1 utför")

### <a name="skill-2-language-detection"></a>#2 språkidentifiering
 Medan språkidentifieringsfärdigheten är den tredje (färdighets- #3) färdighet som definieras i kompetensen, är det nästa färdighet att utföra. Eftersom det inte blockeras genom att kräva några indata, kommer det att köras parallellt med den tidigare färdigheten. Liksom den delade färdighet som föregick den anropas språkidentifieringsfärdigheten också en gång för varje dokument. Anrikningsträdet har nu en ny nod för språk.
 ![anrikningsträd efter färdighet #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Anrikningsträd efter färdighet #2 utför")
 
 ### <a name="skill-3-key-phrases-skill"></a>färdighet #3: Nyckelfraser skicklighet 

Med tanke ```/document/reviews_text/pages/*``` på sammanhanget för nyckelfraser skicklighet kommer att `pages` åberopas en gång för var och en av objekten i samlingen. Utdata från färdigheten blir en nod under det associerade sidelementet. 

 Du bör nu kunna titta på resten av färdigheter i skillset och visualisera hur trädet av berikande kommer att fortsätta att växa med genomförandet av varje färdighet. Vissa kunskaper, till exempel sammanslagningsfärdigheten och shaper-färdigheten, skapar också nya noder men använder bara data från befintliga noder och skapar inga nya berikande netto.

![anrikning träd efter alla färdigheter](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Berikande träd efter alla färdigheter")

Färgerna på kopplingarna i trädet ovan anger att anrikningarna har skapats av olika kunskaper och noderna måste åtgärdas individuellt och kommer inte att ingå i det objekt som returneras när du väljer den överordnade noden.

## <a name="save-enrichments-in-a-knowledge-store"></a>Spara berikningar i ett kunskapslager 

Skillsets definierar också ett kunskapslager där dina berikade dokument kan projiceras som tabeller eller objekt. Om du vill spara dina utökade data i kunskapsarkivet definierar du en uppsättning projektioner för det utökade dokumentet. Mer information om kunskapsarkivet finns i [översikt över kunskapsarkivet](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Skivning projektioner

När du definierar en tabellprojektionsgrupp kan en enskild nod i anrikningsträdet delas in i flera relaterade tabeller. Om du lägger till en tabell med en källsökväg som är underordnad för en befintlig tabellprojektion, blir den resulterande underordnade noden inte underordnad den befintliga tabellprojektionen, utan projiceras i den nya, relaterade tabellen. Med den här skivningstekniken kan du definiera en enda nod i en shaper-färdighet som kan vara källan för alla tabellprojektioner. 

### <a name="shaping-projections"></a>Forma prognoser

Det finns två sätt att definiera en projektion. Du kan använda en shaper-färdighet för att skapa en ny nod som är rotnoden för alla berikanden som du projicerar. Sedan, i dina prognoser, skulle du bara referera till utdata för shaper skicklighet. Du kan också infoga en projektion i själva projektionsdefinitionen.

Shaper-metoden är mer utförlig än inlineformning men säkerställer att alla mutationer i anrikningsträdet finns i färdigheterna och att utdata är ett objekt som kan återanvändas. Infogad formning gör att du kan skapa den form du behöver, men är ett anonymt objekt och är endast tillgängligt för den projektion som den har definierats för. Metoderna kan användas tillsammans eller separat. Den kompetens som skapas för dig i portalarbetsflödet innehåller båda. Den använder en shaper färdighet för tabellprojektionerna, men använder också infogad formning för att projicera nyckelfrastabellen.

Om du vill utöka exemplet kan du välja att ta bort den infogade formningen och använda en formfärdighet för att skapa en ny nod för nyckelfraserna. Om du vill skapa en form `hotelReviewsDocument`som `hotelReviewsPages`projiceras i tre tabeller, nämligen , , och `hotelReviewsKeyPhrases`beskrivs de två alternativen i följande avsnitt.


#### <a name="shaper-skill-and-projection"></a>Shaper skicklighet och projektion 

> [!Note]
> Några av kolumnerna från dokumenttabellen har tagits bort från det här exemplet för korthet.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Med `tableprojection` noden definierad `outputs` i avsnittet ovan kan vi nu använda skivningsfunktionen för att projicera delar av `tableprojection` noden i olika tabeller:

> [!Note]
> Detta är bara ett utdrag av projektionen i knowledge store-konfigurationen.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inline forma projektioner

Den infogade formningsmetoden kräver inte en shaper-färdighet eftersom alla former som behövs för projektionerna skapas vid den tidpunkt då de behövs. Om du vill projicera samma data som föregående exempel skulle alternativet infogad projektion se ut så här:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
En iakttagelse från båda tillvägagångssätten är hur `"sourceContext"`värden av projiceras med hjälp av `"Keyphrases"` . Noden, `"Keyphrases"` som innehåller en samling strängar, är i sig underordnad sidtexten. Men eftersom projektioner kräver ett JSON-objekt och sidan `"sourceContext"` är en primitiv (sträng), används nyckelfrasen i ett objekt med en namngiven egenskap. Denna teknik gör det möjligt att projicera även primitiver oberoende av dem.

## <a name="next-steps"></a>Nästa steg

Som ett nästa steg, skapa din första skillset med kognitiva färdigheter.

> [!div class="nextstepaction"]
> [Skapa din första kompetens](cognitive-search-defining-skillset.md).
