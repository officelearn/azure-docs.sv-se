---
title: Skapa indexerare (Azure Search-tjänsten REST api-version = 2017-11-11-Preview)
description: Indexerare har utökats för att inkludera outputFieldMapping parametrar som används för att mappa berikande utdata till ett fält i ett Azure Search-index i förhandsgranskningen API.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Skapa indexerare (Azure Search-tjänsten REST api-version = 2017-11-11-Preview)

Den här API-referensen är en preview-specifik version av dokumentation, som omfattar kognitiva Sök förbättringar av indexering.

Med den [allmänt tillgänglig](https://docs.microsoft.com/rest/api/searchservice/create-indexer) version, kan du skapa en ny indexerare i en Azure Search-tjänst med en HTTP POST-begäran. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
Den **api-nyckel** måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [säkerhet i Azure Search](search-security-overview.md) för mer information om nycklar. [Skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

Du kan också använda PUT och ange namnet på datakällan på URI: N. Om datakällan inte finns, skapas.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
Den **api-versionen** krävs. Den aktuella versionen är `2016-09-01`. Se [API-versioner i Azure Search](search-api-versions.md) mer information.

Data-plattformsspecifika vägledning om hur du skapar indexerare som börjar med [indexerare översikt](search-indexer-overview.md), som innehåller en fullständig lista över [relaterade artiklar](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Det maximala antalet tillåtna indexerare varierar beroende på prisnivå. Tjänsten gratis kan upp till 3 indexerare. Standard-tjänsten tillåter 50 indexerare. Se [Tjänstbegränsningarna](search-limits-quotas-capacity.md) mer information.    

## <a name="request"></a>Förfrågan  
 Brödtexten i begäran innehåller en definition av indexerare som anger datakällan och mål-index för indexering, samt valfritt indexeringsschema och parametrar.  

 Syntaxen för att strukturera nyttolasten i begäran är som följer. Ett exempel på begäran finns senare i det här avsnittet.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Indexerschemat  
En indexerare kan du ange ett schema. Om det finns ett schema körs indexeraren med jämna mellanrum enligt schemat. Schemaläggaren är inbyggd; Du kan inte använda en extern Schemaläggaren. **Schemat** har följande attribut: 

-   **intervallet**: krävs. Duration-värde som anger ett intervall eller tid för indexeraren körs. Minsta tillåtna intervall är 5 minuter. den längsta är en dag. Den måste formateras som ett XSD ”daytimeduration” XSD-värde (en begränsad delmängd av ett [ISO 8601 varaktighet](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värdet). Mönstret för detta är: `"P[nD][T[nH][nM]]".` exempel: `PT15M` för varje kvart `PT2H` för varannan timme.  

-   **startTime**: valfria. UTC datetime när indexeraren ska börja köra.  

### <a name="indexer-parameters"></a>Indexerare parametrar  
 En indexerare kan du ange flera parametrar som påverkar dess beteende. Alla nedanstående parametrar är valfria.  

-   **maxFailedItems**: antalet objekt som kan misslyckas med att indexera innan en indexerare som kör anses vara fel. Standardvärdet är 0. Information om misslyckade artiklarna returneras av den [Erhåll Status för indexeraren &#40;Azure Söktjänsts-REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) igen.  

-   **maxFailedItemsPerBatch**: antalet objekt som kan misslyckas med att indexera i varje batch innan en indexerare som kör anses vara fel. Standardvärdet är 0.  

-   **batchSize:** anger antalet objekt som läses från datakällan och indexeras som en enskild batch för att förbättra prestanda. Standardvärdet beror på typen av datakälla: det är 1000 för Azure SQL och Azure Cosmos DB och 10 för Azure Blob Storage.

### <a name="field-mapping-parameters"></a>Fältmappning parametrar

Indexerare definitioner innehålla fältet kopplingar för att mappa ett fält i datakällan till ett målfält i ett Azure Search-index. Det finns två typer av kopplingar beroende på om innehållsöverföringen följer en direkt eller utökade sökväg:

+ **fieldMappings** är valfria, tillämpas när inte matchar källan målet fältnamn eller när du vill ange en funktion.
+ **outputFieldMappings** krävs om du skapar [en berikande pipeline](cognitive-search-concept-intro.md). Fältet utdata är en konstruktion som definierats under processen berikande i en berikande pipeline. Fältet utdata kanske till exempel en sammansatt struktur som skapats under berikande från två olika fält i dokumentet källa. 

#### <a name="fieldmappings"></a>fieldMappings

Överväg en källtabell med ett fält i följande exempel `_id`. Azure Search kan inte namnet på ett fält som börjar med ett understreck, så du måste byta namn på fältet. Detta kan göras med hjälp av den `fieldMappings` egenskapen indexeraren på följande sätt:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Du kan ange flera fältmappningar:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Både käll- och fältnamn är inte skiftlägeskänsliga.

Läs om scenarier där fältmappningar är användbara i [Sök indexeraren fältmappningar](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

I kognitiva Sök scenarier där en kunskaper är bunden till en indexerare, måste du lägga till `outputFieldMappings` vill associera några utdata från ett berikande steg som ger innehåll till ett sökbara fält i indexet.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>Fältet mappning funktioner

Fältmappningar kan också användas för att omvandla källa fältvärden med *fältet mappning funktioner*. Till exempel en godtycklig strängvärdet kan vara base64-kodad så att den kan användas för att fylla i ett nyckelfält för dokumentet.

Mer information om när och hur du använder fältet mappning funktioner finns [fältet mappning fungerar](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Begäran body-exempel  
 I följande exempel skapas en indexerare som kopierar data från tabellen som refereras av den `ordersds` datakällan till den `orders` index enligt ett schema som börjar på 1 januari 2015 UTC och körs varje timme. Varje anrop av indexerare kommer att lyckas om mer än 5 objekt misslyckas med att indexera i varje batch och högst 10 objekt misslyckas med att indexera totalt.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Svar  
 201 skapad för en lyckad begäran.  

## <a name="see-also"></a>Se också

+ [Översikt över kognitiva sökning](cognitive-search-concept-intro.md)
+ [Snabbstart: Försök kognitiva sökning](cognitive-search-quickstart-blob.md)
+ [Mappning](cognitive-search-output-field-mapping.md)
