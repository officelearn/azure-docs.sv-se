---
title: Sök över Azure Table lagringsinnehåll
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar data som lagras i Azure Table-lagring med en Azure Cognitive Search-indexerare.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112993"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Indexera tabeller från Azure Table-lagring med Azure Cognitive Search

Den här artikeln visar hur du använder Azure Cognitive Search för att indexera data som lagras i Azure Table storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurera Azure Table storage-indexering

Du kan ställa in en Azure Table storage indexer med hjälp av dessa resurser:

* [Azure-portal](https://ms.portal.azure.com)
* AZURE Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

Här demonstrerar vi flödet med hjälp av REST API. 

### <a name="step-1-create-a-datasource"></a>Steg 1: Skapa en datakälla

En datakälla anger vilka data som ska indexeras, vilka autentiseringsuppgifter som behövs för att komma åt data och de principer som gör det möjligt för Azure Cognitive Search att effektivt identifiera ändringar i data.

För tabellindexering måste datakällan ha följande egenskaper:

- **namnet** är det unika namnet på datakällan i söktjänsten.
- **typ** måste `azuretable`vara .
- **autentiseringsparametern** innehåller anslutningssträngen för lagringskonto. Mer information finns i avsnittet [Ange autentiseringsuppgifter.](#Credentials)
- **anger** tabellnamnet och en valfri fråga.
    - Ange tabellnamnet med `name` hjälp av parametern.
    - Du kan också ange en `query` fråga med hjälp av parametern. 

> [!IMPORTANT] 
> Använd ett filter på PartitionKey om möjligt för bättre prestanda. Alla andra frågor gör en fullständig tabellgenomsökning, vilket resulterar i dåliga prestanda för stora tabeller. Se avsnittet [Prestandaöverväganden.](#Performance)


Så här skapar du en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Mer information om API:et skapa datakälla finns i [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Olika sätt att ange autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifterna för tabellen på något av följande sätt: 

- **Anslutningssträng för lagringskonto**med fullständig åtkomst: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Du kan hämta anslutningssträngen från Azure-portalen genom att gå till inställningar för **lagringskontobladsinställningar** > **Settings** > **(för** klassiska lagringskonton) eller **Inställningars** > **åtkomstnycklar** (för Azure Resource Manager-lagringskonton).
- **Anslutningssträng för delad åtkomst för lagringskonto:** `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Signaturen för delad åtkomst ska ha listan och läsbehörigheten för behållare (tabeller i det här fallet) och objekt (tabellrader).
-  **Signatur för delad åtkomst för tabell:** `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` Signaturen för delad åtkomst ska ha frågebehörighet (läs) i tabellen.

Mer information om signaturer för delad åtkomst för lagring finns i [Använda signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder autentiseringsuppgifter för delad åtkomstsignatur måste du uppdatera datakällaautentiseringsuppgifterna regelbundet med förnyade signaturer för att förhindra att de upphör att gälla. Om autentiseringsuppgifterna för delad åtkomstsignatur upphör att gälla misslyckas indexeraren med ett felmeddelande som liknar "Autentiseringsuppgifterna i anslutningssträngen är ogiltiga eller har upphört att gälla".  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fälten i ett dokument, attributen och andra konstruktioner som formar sökupplevelsen.

Så här skapar du ett index:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Mer information om hur du skapar index finns i [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med ett målsökindex och tillhandahåller ett schema för att automatisera datauppdateringen. 

När indexet och datakällan har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren körs varannan timme. (Schemaintervallet är inställt på "PT2H".) Om du vill köra en indexerare var 30:e minut ställer du in intervallet på "PT30M". Det kortaste intervallet som stöds är fem minuter. Schemat är valfritt. Om den utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API:et skapa indexerare finns i [Skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexeringsscheman finns i [Så här schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Hantera olika fältnamn
Ibland skiljer sig fältnamnen i det befintliga indexet från egenskapsnamnen i tabellen. Du kan använda fältmappningar för att mappa egenskapsnamnen från tabellen till fältnamnen i sökindexet. Mer information om fältmappningar finns i [Azure Cognitive Search-indexerares fältmappningar överbrygga skillnaderna mellan datakällor och sökindex](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Hantera dokumentnycklar
I Azure Cognitive Search identifierar dokumentnyckeln unikt ett dokument. Varje sökindex måste ha exakt `Edm.String`ett nyckelfält av typen . Nyckelfältet krävs för varje dokument som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

Eftersom tabellrader har en sammansatt nyckel genererar Azure `Key` Cognitive Search ett syntetiskt fält som kallas som är en sammanfogning av partitionsnyckel- och radnyckelvärden. Om till exempel en rads `PK1` PartitionKey är `RK1`och `Key` RowKey är `PK1RK1`, är fältets värde .

> [!NOTE]
> Värdet `Key` kan innehålla tecken som är ogiltiga i dokumentnycklar, till exempel streck. Du kan hantera ogiltiga `base64Encode` tecken med hjälp av [fältmappningsfunktionen](search-indexer-field-mappings.md#base64EncodeFunction). Om du gör detta bör du komma ihåg att även använda URL-säker Base64-kodning när du skickar dokumentnycklar i API-anrop såsom Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementell identifiering av indexering och borttagning
När du ställer in en tabellindexerare så att den körs enligt ett schema indexerar `Timestamp` den bara om nya eller uppdaterade rader, enligt värdet av en rad. Du behöver inte ange en ändringsidentifieringsprincip. Inkrementell indexering är aktiverat för dig automatiskt.

Om du vill ange att vissa dokument måste tas bort från indexet kan du använda en strategi för mjuk borttagning. I stället för att ta bort en rad lägger du till en egenskap som anger att den tas bort och ställer in en princip för identifiering av mjuk borttagning på datakällan. Följande princip tar till exempel hänsyn till att en `IsDeleted` rad tas `"true"`bort om raden har en egenskap med värdet :

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Som standard använder Azure Cognitive Search `Timestamp >= HighWaterMarkValue`följande frågefilter: . Eftersom Azure-tabeller inte har ett `Timestamp` sekundärt index på fältet kräver den här typen av fråga en fullständig tabellgenomsökning och är därför långsam för stora tabeller.


Här är två möjliga metoder för att förbättra tabellindexeringsprestanda. Båda dessa metoder är beroende av att använda tabellpartitioner: 

- Om dina data naturligtvis kan delas in i flera partitionsområden skapar du en datakälla och en motsvarande indexerare för varje partitionsintervall. Varje indexerare måste nu bara bearbeta ett specifikt partitionsintervall, vilket resulterar i bättre frågeprestanda. Om de data som behöver indexeras har ett litet antal fasta partitioner, ännu bättre: varje indexerare gör bara en partitionsgenomsökning. Om du till exempel vill skapa en datakälla `000` `100`för bearbetning av ett partitionsområde med nycklar från till använder du en fråga som denna: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Om dina data partitioneras efter tid (du skapar till exempel en ny partition varje dag eller vecka) bör du överväga följande metod: 
    - Använd en fråga i `(PartitionKey ge <TimeStamp>) and (other filters)`formuläret: . 
    - Övervaka indexerarens förlopp med hjälp av Hämta `<TimeStamp>` [Indexer status-API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)och uppdatera regelbundet villkoret för frågan baserat på det senaste lyckade högvattenmärkesvärdet. 
    - Med den här metoden, om du behöver utlösa en fullständig omindexering, måste du återställa datakällan frågan förutom att återställa indexeraren. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Cognitive Search bättre
Om du har funktionsförfrågningar eller idéer för förbättringar skickar du in dem på vår [UserVoice-webbplats.](https://feedback.azure.com/forums/263029-azure-search/)
