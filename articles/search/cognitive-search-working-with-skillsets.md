---
title: Färdigheter-koncept och arbets flöde
titleSuffix: Azure Cognitive Search
description: Färdighetsuppsättningar är där du skapar en pipeline för AI-anrikning i Azure Kognitiv sökning. Lär dig viktiga begrepp och information om färdigheter-kompositionen.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0637e160454897af774c3bac48fc02866cb71835
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760801"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Färdigheter-koncept och-sammansättning i Azure Kognitiv sökning

Den här artikeln är för utvecklare som behöver en djupare förståelse för hur anriknings pipelinen fungerar och förutsätter att du har en konceptuell förståelse för AI-anrikningen. Om du har nytt det här konceptet börjar du med:
+ [AI-anrikning i Azure Kognitiv sökning](cognitive-search-concept-intro.md)
+ [Kunskaps lager (för hands version)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Ange färdigheter
En färdigheter är en återanvändbar resurs i Azure Kognitiv sökning som anger en samling kognitiva kunskaper som används för att analysera, transformera och berika text-eller bild innehåll under indexeringen. Genom att skapa en färdigheter kan du koppla text-och avbildnings berikare i data inmatnings fasen, extrahera och skapa ny information och strukturer från RAW-innehåll.

En färdigheter har tre egenskaper:

+   ```skills```en osorterad samling kunskaper för vilka plattformen bestämmer körnings ordningen baserat på de indata som krävs för varje färdighet
+   ```cognitiveServices```den kognitiva tjänst nyckeln som krävs för att fakturera de kognitiva färdigheter som anropas
+   ```knowledgeStore```, det lagrings konto där dina berikade dokument kommer att projiceras



Färdighetsuppsättningar har skapats i JSON. Du kan bygga komplexa färdighetsuppsättningar med slingor och [förgreningar](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) med hjälp av [uttrycks språket](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Uttrycks språkets sökvägar använder [JSON-pekaren](https://tools.ietf.org/html/rfc6901) med några ändringar för att identifiera noder i ett berikande träd. En ```"/"``` passerar en nivå lägre i trädet och ```"*"``` fungerar som en för-varje operator i kontexten. Dessa begrepp beskrivs bäst med ett exempel. För att illustrera några av begreppen och funktionerna går vi igenom exempel färdigheter för [hotell granskning](knowledge-store-connect-powerbi.md) . Om du vill visa färdigheter när du har följt arbets flödet för att importera data, måste du använda en REST API-klient för att [Hämta färdigheter](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Anriknings träd

För att Envision hur en färdigheter progressivt berikar ditt dokument, så börjar vi med vad dokumentet ser ut innan en berikning. Utmatningen av dokument sprickor är beroende av data källan och det angivna tolknings läget har valts. Detta är även läget för det dokument som [fält mappningar](search-indexer-field-mappings.md) kan käll innehåll från när data läggs till i sökindexet.
![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Kunskaps lager i Pipeline-diagram")

När ett dokument har berikats pipelinen visas det som ett träd med innehåll och tillhör ande berikare. Trädet instansieras som utdata från dokument sprickor. Formatet för anriknings träd möjliggör anriknings pipelinen för att bifoga metadata till till och med primitiva data typer, men det är inte ett giltigt JSON-objekt, men kan projiceras i ett giltigt JSON-format. I följande tabell visas en status för ett dokument som anges i pipelinen:

|Data Source\Parsing läge|Default|JSON, JSON-linjer & CSV|
|---|---|---|
|Blob-lagring|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Gäller inte |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Gäller inte|

 När färdigheter körs lägger de till nya noder i det berikande trädet. Dessa nya noder kan sedan användas som indata för underordnade kunskaper, projicera till kunskaps lagret eller mappa till index fält. Berikningar är inte föränderligt: när de har skapats går det inte att redigera noder. När din färdighetsuppsättningar får mer komplexa, så kommer ditt anriknings träd, men inte alla noder i anriknings trädet behöver göra det till indexet eller kunskaps lagret. Du kan selektivt bevara endast en delmängd av anrikningerna i indexet eller kunskaps lagret.

Du kan selektivt bevara endast en delmängd av anrikningerna i indexet eller kunskaps lagret.
För resten av det här dokumentet kommer vi att anta att vi arbetar med [hotell gransknings exempel](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), men samma koncept gäller för att ge dokument från alla andra data källor.

### <a name="context"></a>Kontext
Varje färdighet kräver en kontext. En kontext fastställer:
+   Antalet gånger som kompetensen körs, baserat på de valda noderna. Om du lägger till en ```/*``` i slutet av kontext värden av typen samling leder det till att den färdighet som anropas en gång för varje instans i samlingen skapas. 
+   Var i anriknings trädet läggs färdighets utmatningarna till. Utdata läggs alltid till i trädet som underordnade noder till kontextnoden. 
+   Figuren för indata. För samlingar med flera nivåer påverkar att ange kontexten till den överordnade samlingen formen på indata för kunskapen. Om du till exempel har ett berikande träd med en lista över länder, var och en med en lista över stater som innehåller en lista över ZipCodes.

|Kontext|Indata|Inmatad form|Kompetens anrop|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |En lista över alla ZipCodes i landet |En gång per land |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |En lista över ZipCodes i status | En gång per kombination av land och delstat|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` används endast i färdighets inmatning och [projektioner](knowledge-store-projection-overview.md). Den används för att skapa kapslade objekt på flera nivåer. Du kan behöva skapa ett nytt objekt för att antingen skicka det som inmatat till en kunskap eller ett projekt i kunskaps lagret. Eftersom anriknings noder kanske inte är ett giltigt JSON-objekt i ett berikande träd och som refererar till en nod i trädet, returnerar bara det läget för noden när den skapades, med hjälp av användnings området som kunskaps inmatning eller projektioner, vilket innebär att du kan skapa ett välformulerat JSON-objekt. Med `sourceContext` kan du skapa ett hierarkiskt, anonymt typ objekt, vilket skulle kräva flera kunskaper om du bara använde kontexten. Om du använder `sourceContext` visas nästa avsnitt. Titta på de kunskaps utdata som genererade en anrikning för att avgöra om det är ett giltigt JSON-objekt och inte en primitiv typ.

### <a name="projections"></a>Projektioner

Projektion är processen att välja noderna från det berikande trädet som ska sparas i kunskaps lagret. Projektioner är anpassade former i dokumentet (innehåll och anrikninger) som kan matas ut antingen som tabell-eller objekt projektioner. Mer information om hur du arbetar med projektioner finns i [arbeta med projektioner](knowledge-store-projection-overview.md).

![Alternativ för fält mappning](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Fält mappnings alternativ för anriknings pipeline")

Diagrammet ovan beskriver väljaren som du arbetar med, baserat på var du befinner dig i pipelinen för anrikning.

## <a name="generate-enriched-data"></a>Generera utförliga data 

Nu ska vi gå igenom färdigheter för hotell granskningar, du kan följa [självstudien](knowledge-store-connect-powerbi.md) för att skapa färdigheter eller [Visa](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) färdigheter. Vi ska titta på:

* Hur anriknings trädet utvecklas med körningen av varje färdighet 
* så här fungerar kontext och indata för att avgöra hur många gånger en färdighet körs 
* Hur indatatypen är baserat på kontexten. 

Eftersom vi använder avgränsat text tolknings läge för indexeraren representerar ett dokument i beriknings processen en enda rad i CSV-filen.

### <a name="skill-1-split-skill"></a>Kunskaps #1: dela kunskaper 

![anriknings träd efter dokument sprickor](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Anriknings träd efter dokument sprickor och innan kompetens körning")

Med kunskaps kontexten för ```"/document/reviews_text"```körs den här kunskapen en gång för `reviews_text`. Färdighets utmatningen är en lista där `reviews_text` delas upp i 5000-sträng segment. Resultatet från den delade kunskapen heter `pages` och läggs till i berikande trädet. Med hjälp av funktionen `targetName` kan du byta namn på en färdighets utmatning innan du lägger till den i berikande trädet.

Ditt anriknings träd har nu en ny nod som placerats under kunskaps kontexten. Den här noden är tillgänglig för alla kunskaper, projektioner och fält mappningar.


Rotnoden för alla berikningar är `"/document"`. När du arbetar med BLOB-indexerare kommer noden `"/document"` att ha underordnade noder för `"/document/content"` och `"/document/normalized_images"`. När du arbetar med CSV-data, som vi är i det här exemplet, kommer kolumn namnen att mappas till noder under `"/document"`. För att få åtkomst till någon av de omfattande tillägg som läggs till i en nod av en färdighet behövs den fullständiga sökvägen för berikning. Om du till exempel vill använda texten från ```pages```-noden som inmatad till en annan färdighet måste du ange den som ```"/document/reviews_text/pages/*"```.
 
 ![anriknings träd efter färdighets #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Anriknings träd efter att kunskaps #1 körts")

### <a name="skill-2-language-detection"></a>Identifiering av kunskaps #2 språk
 Även om språket för språk identifiering är den tredje kunskaps #3s kompetensen som definierats i färdigheter, är det nästa färdighet att köra. Eftersom den inte blockeras genom att kräva några indata körs den parallellt med den tidigare kunskapen. Precis som den delade kunskapen som föregår den, anropas även språk identifierings kunskapen en gång för varje dokument. Ditt anriknings träd har nu en ny nod för språk.
 ![anriknings träd efter färdighets #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Anriknings träd efter att kunskaps #2 körts")
 
 ### <a name="skill-3-key-phrases-skill"></a>Kompetens #3: kompetens för nyckel fraser 

Med tanke på att ```/document/reviews_text/pages/*``` nyckel frasernas färdighet anropas en gång för varje objekt i `pages`s samlingen. Utdata från färdigheten är en nod under det associerade sid elementet. 

 Nu bör du kunna titta på resten av färdigheterna i färdigheter och visualisera hur trädet i berikarna kommer att fortsätta att växa med körningen av varje färdighet. Vissa kunskaper, till exempel sammanfognings kunskaper och formaren-kunskaper, skapar också nya noder, men använder bara data från befintliga noder och skapar inte nya, nya-anrikninger.

![anriknings träd efter alla kunskaper](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Anriknings träd efter alla kunskaper")

Färgerna på kopplingarna i trädet ovan anger att berikarna har skapats av olika kunskaper och att noderna måste adresseras individuellt och kommer inte att ingå i det objekt som returneras när den överordnade noden väljs.

## <a name="save-enrichments-in-a-knowledge-store"></a>Spara berikningar i ett kunskaps lager 

Färdighetsuppsättningar definierar också ett kunskaps lager där dina berikade dokument kan projiceras som tabeller eller objekt. Om du vill spara dina berikade data i kunskaps lagret definierar du en uppsättning projektioner för ditt berikade dokument. Mer information om kunskaps lagret finns i [Översikt över kunskaps Arkiv](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projektion av segmentering

När du definierar en tabell projektions grupp kan en enda nod i ett berikande träd segmenteras i flera relaterade tabeller. Om du lägger till en tabell med en käll Sök väg som är underordnad en befintlig tabell projektion, kommer den resulterande underordnade noden inte att vara underordnad den befintliga Table-projektionen, utan kommer i stället att projiceras i den nya, relaterade tabellen. Med den här segmenterings metoden kan du definiera en enskild nod i en formaren-färdighet som kan vara källa för alla tabell projektioner. 

### <a name="shaping-projections"></a>Utformning av projektioner

Det finns två sätt att definiera en projektion. Du kan använda en formaren-färdighet för att skapa en ny nod som är rotnoden för alla berikade projekt. I dina projektioner refererar du sedan bara till utdata från formaren-kompetensen. Du kan också infoga en projektion i själva projektions definitionen.

Formaren-metoden är mer utförlig än infogad form men säkerställer att alla Mutations träd finns i kunskaperna och att utdata är ett objekt som kan återanvändas. Med infogad form givning kan du skapa den form du behöver, men är ett anonymt objekt och är bara tillgängligt för projektionen som den har definierats för. Metoderna kan användas tillsammans eller separat. Färdigheter som skapas åt dig i Portal arbets flödet innehåller båda. Den använder en formaren-färdighet för tabell projektioner, men använder infogad form för att projicera nyckel fraserna.

Om du vill utöka exemplet kan du välja att ta bort infogad form och använda en formaren-färdighet för att skapa en ny nod för nyckel fraserna. De två alternativen beskrivs i följande avsnitt för att skapa en form som projiceras i tre tabeller, nämligen `hotelReviewsDocument`, `hotelReviewsPages`och `hotelReviewsKeyPhrases`.


#### <a name="shaper-skill-and-projection"></a>Formaren-kunskaper och projektion 

> [!Note]
> Några av kolumnerna från dokument tabellen har tagits bort från det här exemplet för det kortfattat.
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

Med noden `tableprojection` som definieras i avsnittet `outputs` ovan kan vi nu använda segmenterings funktionen för att projicera delar av `tableprojection`-noden i olika tabeller:

> [!Note]
> Detta är bara ett kodfragment av projektionen i kunskaps lager konfigurationen.
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

#### <a name="inline-shaping-projections"></a>Inline Forming-projektioner

Den infogade form metoden kräver ingen formaren-kompetens eftersom alla former som behövs för projektionerna skapas vid den tidpunkt då de behövs. För att projicera samma data som i föregående exempel skulle alternativet infogad projektion se ut så här:

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
  
En observation från båda metoderna är hur värdena för `"Keyphrases"` projiceras med hjälp av `"sourceContext"`. `"Keyphrases"`-noden, som innehåller en samling strängar, är en underordnad sid text. Men eftersom projektioner kräver ett JSON-objekt och sidan är primitiv (sträng), används `"sourceContext"` för att omsluta nyckel frasen till ett objekt med en namngiven egenskap. Den här tekniken gör att även primitiver kan projiceras oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

I nästa steg ska du skapa din första färdigheter med kognitiva kunskaper.

> [!div class="nextstepaction"]
> [Skapa din första färdigheter](cognitive-search-defining-skillset.md).
