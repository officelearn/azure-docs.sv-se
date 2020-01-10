---
title: Introduktion till kunskaps lager (för hands version)
titleSuffix: Azure Cognitive Search
description: Skicka berikade dokument till Azure Storage där du kan visa, ändra form på och använda berikade dokument i Azure Kognitiv sökning och i andra program. Den här funktionen är en allmänt tillgänglig förhandsversion.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 9a6fa62384615f60da88bb41da8ad3538d34e62a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754106"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduktion till kunskaps lager i Azure Kognitiv sökning

> [!IMPORTANT] 
> Kunskaps lagret är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande begränsad Portal support och inget stöd för .NET SDK.

Kunskaps lager är en funktion i Azure Kognitiv sökning som sparar utdata från en [AI-pipeline](cognitive-search-concept-intro.md) för oberoende analys eller underordnad bearbetning. Ett *berikat dokument* är en pipeline-utdata som skapas från innehåll som har extraherats, strukturer ATS och analyser ATS med hjälp av AI-processer. I en standard-AI-pipeline är berikade dokument övergående, som endast används vid indexering och sedan ignoreras. Med kunskaps lager bevaras de dokument som är omfattande. 

Om du har använt kognitiva färdigheter tidigare vet du redan att *färdighetsuppsättningar* flyttar ett dokument genom en sekvens av anrikninger. Resultatet kan vara ett sökindex eller (nytt i den här förhands granskningen) projektioner i ett kunskaps lager. De två utmatningarna, Sök indexet och kunskaps lagret är produkter av samma pipeline. härleds från samma indata, men resulterar i utdata som är strukturerade, lagrade och används på olika sätt.

Fysiskt är ett kunskaps lager [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), antingen Azure Table Storage, Azure Blob Storage eller båda. Alla verktyg eller processer som kan ansluta till Azure Storage kan använda innehållet i ett kunskaps lager.

![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kunskaps lager i Pipeline-diagram")

## <a name="benefits-of-knowledge-store"></a>Fördelar med kunskaps lager

I ett kunskaps lager får du struktur, kontext och faktiskt innehåll – uppnår från ostrukturerade och delvis strukturerade datafiler som blobbar, bildfiler som har genomgått analyser eller till och med strukturerade data som har ändrats till nya formulär. I en [steg-för-steg-genom gång](knowledge-store-create-rest.md)kan du se hur ett kompakt JSON-dokument är partitionerat i under strukturer, rekonstituerat i nya strukturer och på annat sätt görs tillgängligt för efterföljande processer som Machine Learning och data science-arbetsbelastningar.

Även om det är användbart att se vad en AI-pipeline kan skapa, är det möjligt att ändra form på data i en kunskaps butik. Du kan börja med en grundläggande färdigheter och sedan iterera över den för att lägga till ökande struktur nivåer, som du sedan kan kombinera i nya strukturer, i andra appar förutom Azure Kognitiv sökning.

Räknat, fördelarna med kunskaps lager inkluderar följande:

+ Använd omfattande dokument i andra [analys-och rapporterings verktyg](#tools-and-apps) än Sök. Power BI med Power Query är ett övertygande val, men alla verktyg och appar som kan ansluta till Azure Storage kan hämta från ett kunskaps lager som du skapar.

+ Förfina en AI-indexerings pipeline vid fel sökning av steg och färdigheter definitioner. I ett kunskaps lager visas produkten av en färdigheter-definition i en AI-indexerad pipeline. Du kan använda dessa resultat för att utforma en bättre färdigheter, eftersom du kan se exakt vad som kan se ut. Du kan använda [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) i Azure Storage om du vill visa innehållet i ett kunskaps lager.

+ Forma data till nya formulär. Omformningen är att kodifiera i färdighetsuppsättningar, men punkten är att en färdigheter nu kan tillhandahålla den här funktionen. [Formaren-kunskapen](cognitive-search-skill-shaper.md) i Azure kognitiv sökning har utökats för att kunna hantera den här uppgiften. Med hjälp av omformningen kan du definiera en projektion som anpassas efter den avsedda användningen av data medan du bevarar relationer.

> [!Note]
> Är du nybörjare på AI-anrikning och kognitiva färdigheter? Azure Kognitiv sökning integreras med Cognitive Services vision-och språk funktioner för att extrahera och utöka källdata med OCR (optisk tecken läsning) över bildfiler, enhets igenkänning och nyckel fras extrahering från textfiler med mera. Mer information finns i [AI-anrikning i Azure kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fysisk lagring

Det fysiska uttrycket för ett kunskaps lager har ledas via `projections`-elementet i en `knowledgeStore`-definition i en färdigheter. Projektionen definierar en struktur för utdata så att den matchar den avsedda användningen.

Projektioner kan ledas som tabeller, objekt eller filer.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Den typ av projektion som du anger i den här strukturen avgör vilken typ av lagring som används av kunskaps lagret.

+ Table Storage används när du definierar `tables`. Definiera en tabell projektion när du behöver rapporterings strukturer i tabell form för indata till analys verktyg eller exportera som data ramar till andra data lager. Du kan ange flera `tables` för att hämta en delmängd eller ett tvärsnitt av dokument som är omfattande. I samma projektions grupp bevaras tabell relationer så att du kan arbeta med alla.

+ Blob Storage används när du definierar `objects` eller `files`. Den fysiska representationen av en `object` är en hierarkisk JSON-struktur som representerar ett berikat dokument. En `file` är en avbildning som extraheras från ett dokument, överfördes till Blob Storage.

Ett enskilt projekt objekt innehåller en uppsättning `tables`, `objects`, `files`och för många scenarier, vilket kan vara tillräckligt för att skapa en projektion. 

Det är dock möjligt att skapa flera uppsättningar `table`-`object`-`file` projektioner, och du kan göra det om du vill ha olika data relationer. I en mängd är data relaterade, förutsatt att dessa relationer finns och kan identifieras. Om du skapar ytterligare uppsättningar är dokumenten i varje grupp aldrig relaterade. Ett exempel på hur du använder flera projektions grupper kan vara om du vill att samma data ska användas med ditt online-system och det måste visas på ett särskilt sätt. du vill även att samma data ska användas i en data vetenskaps pipeline som visas olikfärgade.

## <a name="requirements"></a>Krav 

[Azure Storage](https://docs.microsoft.com/azure/storage/) krävs. Den tillhandahåller fysisk lagring. Du kan använda Blob Storage, Table Storage eller båda. Blob Storage används för informativt omfattande dokument, vanligt vis när utdata ska gå till efterföljande processer. Table Storage är till för segment av omfattande dokument, som ofta används för analys och rapportering.

[Färdigheter](cognitive-search-working-with-skillsets.md) krävs. Den innehåller `knowledgeStore` definitionen och bestämmer strukturen och sammansättningen för ett berikat dokument. Du kan inte skapa ett kunskaps lager med en tom färdigheter. Du måste ha minst en färdighet i en färdigheter.

[Indexerare](search-indexer-overview.md) krävs. En färdigheter anropas av en indexerare som styr körningen. Indexerare levereras med en egen uppsättning krav och attribut. Flera av dessa attribut har direkt försetts med ett kunskaps lager:

+ Indexerare kräver en [Azure-datakälla som stöds](search-indexer-overview.md#supported-data-sources) (pipelinen som slutligen skapar kunskaps lagret som börjar genom att hämta data från en källa som stöds i Azure). 

+ Indexerare kräver ett sökindex. En indexerare kräver att du anger ett index schema, även om du inte planerar att använda den. Ett minimalt index har ett sträng fält som anges som nyckel.

+ Indexerare tillhandahåller valfria fält mappningar som används för att ge alias till ett käll fält. Om en standard fält mappning behöver ändras (om du vill använda ett annat namn eller en annan typ) kan du skapa en [fält mappning](search-indexer-field-mappings.md) i en indexerare. För kunskaps lagers utdata kan målet vara ett fält i ett BLOB-objekt eller en tabell.

+ Indexerare har scheman och andra egenskaper, till exempel mekanismer för ändrings identifiering som tillhandahålls av olika data källor, kan också användas i ett kunskaps lager. Du kan till exempel [Schemalägga](search-howto-schedule-indexers.md) berikning med jämna mellanrum för att uppdatera innehållet. 

## <a name="how-to-create-a-knowledge-store"></a>Så här skapar du ett kunskaps lager

Om du vill skapa kunskaps lager använder du portalen eller förhands gransknings REST API (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Använd Azure Portal

Guiden **Importera data** innehåller alternativ för att skapa ett kunskaps lager. För inledande utforskning [skapar du din första kunskaps lagring i fyra steg](knowledge-store-connect-power-bi.md).

1. Välj en data källa som stöds.

1. Ange berikning: Anslut en resurs, Välj kunskaper och ange ett kunskaps lager. 

1. Skapa ett index schema. Guiden kräver det och kan härleda en åt dig.

1. Kör guiden. Extrahering, berikning och lagring sker i det här sista steget.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Använd skapa färdigheter och förhands gransknings REST API

En `knowledgeStore` definieras i en [färdigheter](cognitive-search-working-with-skillsets.md), som i sin tur anropas av en [indexerare](search-indexer-overview.md). Under anrikningen skapar Azure Kognitiv sökning ett utrymme i ditt Azure Storage-konto och projekt utgör de dokument som blobar eller i tabeller, beroende på din konfiguration.

För närvarande är förhands gransknings REST API den enda mekanismen genom vilken du kan skapa ett kunskaps lager program mässigt. Ett enkelt sätt att utforska är att [skapa ditt första kunskaps lager med Postman och REST API](knowledge-store-create-rest.md).

Referens innehållet för den här förhands gransknings funktionen finns i avsnittet [API-referens](#kstore-rest-api) i den här artikeln. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Så här ansluter du till verktyg och appar

När det finns lagrings utrymme kan alla verktyg och tekniker som ansluter till Azure Blob eller Table Storage användas för att utforska, analysera eller använda innehållet. Följande lista är en start:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) för att visa en omfattande dokument struktur och innehåll. Överväg detta som ditt bas linje verktyg för att Visa kunskaps lager innehåll.

+ [Power BI](knowledge-store-connect-power-bi.md) för rapportering och analys. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) för ytterligare modifiering.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referens

Det här avsnittet är en version av referens dokumentet [create färdigheter (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) som har ändrats för att inkludera en `knowledgeStore`-definition. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Exempel – knowledgeStore Embedded i en färdigheter

I följande exempel visas `knowledgeStore` längst ned i en färdigheter-definition. 

* Använd **post** eller **placering** för att formulera begäran.
* Använd `api-version=2019-05-06-Preview` versionen av REST API för att få åtkomst till kunskaps lager funktionerna. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

Bröd texten i begäran är ett JSON-dokument som definierar en färdigheter, vilket omfattar `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Syntax för begär ande text  

Följande JSON anger en `knowledgeStore`, som är en del av en [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), som anropas av en `indexer` (visas inte). Om du redan är bekant med AI-berikning bestämmer en färdigheter sammansättningen för ett berikat dokument. En färdigheter måste innehålla minst en kunskap, troligen en formaren-skicklighet om du är modulating data strukturer.

Syntaxen för att strukturera nytto lasten för begäran är följande.

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

En `knowledgeStore` har två egenskaper: en `storageConnectionString` till ett Azure Storage-konto och `projections` som definierar fysiskt lagrings utrymme. Du kan använda valfritt lagrings konto, men det är kostnads effektivt att använda tjänster i samma region.

En `projections`-samling innehåller projektions objekt. Varje projektions objekt måste ha `tables``objects``files` (ett av dem), som antingen har angetts eller är null. Syntaxen ovan visar två objekt, ett fullständigt angivet och det andra fullständigt null. När det har uttryckts i ett projekt objekt bevaras alla relationer mellan data, om de upptäcks. 

Skapa så många projektions objekt som du behöver för att stödja isolering och vissa scenarier (till exempel data strukturer som används för prospektering, jämfört med de som krävs i en arbets belastning för data vetenskap). Du kan få isolering och anpassning för vissa scenarier genom att ange `source` och `storageContainer` eller `table` till olika värden i ett objekt. Mer information och exempel finns i [arbeta med projektioner i ett kunskaps lager](knowledge-store-projection-overview.md).

|Egenskap      | Gäller | Beskrivning|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Krävs. I det här formatet: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Krävs. En samling egenskaps objekt bestående av `tables`, `objects`, `files` och deras respektive egenskaper. Oanvända projektioner kan anges till null.|  
|`source`| Alla projektioner| Sökvägen till noden i det berikande trädet som är roten för projektionen. Den här noden är resultatet av någon av färdigheterna i färdigheter. Sökvägar börjar med `/document/`som representerar det omfattande dokumentet men kan utökas till att `/document/content/` eller till noder i dokument trädet. Exempel: `/document/countries/*` (alla länder) eller `/document/countries/*/states/*` (alla stater i alla länder). Mer information om dokument Sök vägar finns i [färdigheter-koncept och sammansättning](cognitive-search-working-with-skillsets.md).|
|`tableName`| `tables`| En tabell som ska skapas i Azure Table Storage. |
|`storageContainer`| `objects`, `files`| Namnet på en behållare att skapa i Azure Blob Storage. |
|`generatedKeyName`| `tables`| En kolumn som skapats i tabellen som unikt identifierar ett dokument. Pipelinen fyller i den här kolumnen med genererade värden.|


### <a name="response"></a>Svar  

 För en lyckad begäran bör du se status koden "201 skapat". Som standard innehåller svars texten JSON för den färdigheter-definition som skapades. Kom ihåg att kunskaps lagret inte har skapats förrän du anropar en indexerare som refererar till den här färdigheter.

## <a name="next-steps"></a>Nästa steg

Kunskaps lager erbjuder beständiga dokument, är användbara när du utformar en färdigheter, eller när du skapar nya strukturer och innehåll för användning av klient program som kan komma åt ett Azure Storage-konto.

Det enklaste sättet att skapa omfattande dokument är [via portalen](knowledge-store-create-portal.md), men du kan också använda Postman och REST API, vilket är mer användbart om du vill veta mer om hur objekt skapas och refereras till.

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager med Postman och REST](knowledge-store-create-rest.md)
