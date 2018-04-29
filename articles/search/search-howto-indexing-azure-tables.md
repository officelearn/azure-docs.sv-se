---
title: Indexering Azure Table storage med Azure Search | Microsoft Docs
description: Lär dig att indexera data som lagras i Azure Table storage med Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: a171bdd11cd2de030937927eef34d5ad9e0507af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="index-azure-table-storage-with-azure-search"></a>Index Azure Table storage med Azure Search
Den här artikeln visar hur du använder Azure Search index-data som lagras i Azure Table storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurera Azure Table storage indexering

Du kan konfigurera en indexerare för Azure Table storage med hjälp av följande resurser:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

Här ser flödet med hjälp av REST API. 

### <a name="step-1-create-a-datasource"></a>Steg 1: Skapa en datakälla

En datakälla anger vilka data som ska indexera, autentiseringsuppgifter som krävs för att få åtkomst till data och principer som gör att Azure Search att effektivt identifiera ändringar i data.

För tabellen indexering måste datasource ha följande egenskaper:

- **namnet** är det unika namnet på datakällan i din söktjänst.
- **typen** måste vara `azuretable`.
- **autentiseringsuppgifter** parametern innehåller lagringsanslutningssträngen för kontot. Finns det [ange autentiseringsuppgifter](#Credentials) information.
- **behållaren** anger tabellens namn och en valfri fråga.
    - Ange namnet på tabellen med hjälp av den `name` parameter.
    - Du kan också ange en fråga med hjälp av den `query` parameter. 

> [!IMPORTANT] 
> När det är möjligt ska du använda ett filter på PartitionKey för bättre prestanda. Andra frågan har en fullständig tabellgenomsökning, vilket resulterar i sämre prestanda för stora tabeller. Finns det [prestandaöverväganden](#Performance) avsnitt.


Skapa en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Mer information om skapa Datasource-API finns [skapa Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sätt att ange autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifter för tabellen i något av följande sätt: 

- **Fullständig åtkomst lagringsanslutningssträng för kontot**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` får du anslutningssträngen i Azure portal genom att gå till den **lagring kontoblad** > **inställningar**  >  **Nycklar** (för klassiska lagringskonton) eller **inställningar** > **åtkomstnycklar** (för lagring av Azure Resource Manager konton).
- **Storage-konto delad åtkomst signatur anslutningssträngen**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` signatur för delad åtkomst bör ha i listan och läsbehörighet på behållare (tabeller i det här fallet) och objekt (rader).
-  **Signatur för delad åtkomst**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` signatur för delad åtkomst måste ha behörigheter för frågan (läsa) i tabellen.

Mer information om delad lagring komma åt signaturer, se [använder signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder autentiseringsuppgifter för signatur för delad åtkomst kan behöver du uppdatera datakällans autentiseringsuppgifter med förnyat signaturer för att förhindra att de gått ut. Om autentiseringsuppgifter för signatur för delad åtkomst ut misslyckas indexeraren med ett felmeddelande liknande ”autentiseringsuppgifter som har angetts i anslutningssträngen är ogiltig eller har upphört att gälla”.  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger vilka fält i ett dokument, attribut, och andra konstruktioner som formar sökningen upplevelse.

Att skapa ett index:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Mer information om hur du skapar index finns [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med ett mål sökindex och ger ett schema för att automatisera datauppdateringen. 

När index och datakälla har skapats är redo du att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexeraren körs varannan timme. (Schemaintervallet sätts till ”PT2H”). Ställa in intervall för ”PT30M” om du vill köra en indexerare var 30: e minut. Den kortaste stöds är fem minuter. Schemat är valfritt. Om det utelämnas används körs en indexeraren bara en gång när den skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om skapa indexeraren API finns [skapa indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Hantera olika fältnamn
Ibland skiljer fältnamn i ditt befintliga index sig från egenskapsnamn i tabellen. Du kan använda fältmappningar för att mappa egenskapsnamn från tabellen och fältnamnen i search-index. Läs mer om fältmappningar i [Azure Search indexeraren fältmappningar överbrygga skillnaderna mellan datakällor och Sök index](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Hantera dokument nycklar
I Azure Search identifierar dokumentnyckeln ett dokument. Varje sökindex måste ha exakt ett fält av typen `Edm.String`. Nyckelfältet krävs för varje dokument som läggs till i indexet. (I själva verket det är bara obligatoriskt fält.)

Eftersom rader har en sammansatt nyckel, Azure Search genererar ett syntetiskt fält med namnet `Key` som är en sammansättning av partition nyckel- och nyckelvärden. Till exempel om en rad PartitionKey är `PK1` och RowKey `RK1`, sedan `Key` fältvärde är `PK1RK1`.

> [!NOTE]
> Den `Key` värde får bestå av tecken som är ogiltiga i dokumentet nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av den `base64Encode` [fältet mappning funktionen](search-indexer-field-mappings.md#base64EncodeFunction). Kom ihåg att också använda URL-säkert Base64-kodning när skicka dokumentet nycklar i API-anrop, till exempel sökning om du gör detta.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Identifiering av inkrementell indexering och borttagning
När du skapar en tabell indexeraren ska köras enligt ett schema, den reindexes bara nya eller uppdaterade rader, som bestäms av en rad `Timestamp` värde. Du behöver inte ange en princip för identifiering av ändring. Inkrementell indexering aktiveras åt dig automatiskt.

Du kan använda en strategi för mjuk borttagning för att ange att vissa dokument måste tas bort från indexet. Lägga till en egenskap för att ange att den har tagits bort och konfigurera en princip för mjuk borttagning identifiering på datakällan i stället för att ta bort en rad. Till exempel följande princip anser att en rad har tagits bort om raden har en egenskap `IsDeleted` med värdet `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2017-11-11
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

Som standard använder Azure Search följande frågefilter: `Timestamp >= HighWaterMarkValue`. Eftersom Azure-tabeller som inte har ett sekundärt index på den `Timestamp` denna typ av fråga kräver en fullständig tabellgenomsökning och därför går långsamt för stora tabeller.


Här är två möjliga sätt för att förbättra prestanda för tabellen indexering. Bägge förlitar sig på tabellpartitioner: 

- Om dina data kan naturligt partitioneras i flera partition intervall, skapa en datakälla och en motsvarande indexerare för varje partition. Varje indexerare har nu bearbeta endast en specifik partitionsintervall, vilket resulterar i bättre prestanda för frågor. Om de data som måste indexeras har ett litet antal fasta partitioner, ännu bättre: varje indexerare har endast en partition sökning. Till exempel för att skapa en datakälla för bearbetning av en partitionsintervall med nycklar från `000` till `100`, använda en fråga så här: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Om dina data är partitionerad med tid (exempelvis kan du skapa en ny partition varje dag eller en vecka), Överväg följande metod: 
    - Använd en fråga i formatet: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Övervaka indexeraren förlopp med hjälp av [hämta indexeraren Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status), och regelbundet uppdatera den `<TimeStamp>` villkor för frågan baserat på senaste lyckade vattenmärke-värdet. 
    - Med den här metoden om du behöver att utlösa en fullständig indexeringen som du behöver återställa datasource frågan förutom återställa indexeraren. 


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan du skicka dem på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
