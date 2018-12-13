---
title: Indexera innehåll från Azure Table storage för fulltextsökning – Azure Search
description: Lär dig hur du indexera data lagrade i Azure Table storage med Azure Search.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 39455669dd739309ac0201de49b390c2390e0067
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317278"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Indexera Azure Table storage med Azure Search
Den här artikeln visar hur du använder Azure Search att indexera data lagrade i Azure Table storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurera Azure Table storage-indexering

Du kan konfigurera en indexerare för Azure Table storage med hjälp av dessa resurser:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

Här visar vi flödet med hjälp av REST-API. 

### <a name="step-1-create-a-datasource"></a>Steg 1: Skapa en datakälla

En datakälla anger vilka data som ska indexera, de autentiseringsuppgifter som krävs för att få åtkomst till data och principer som gör att Azure Search för att effektivt identifiera ändringar i data.

För tabellen indexering måste datakällan ha följande egenskaper:

- **namn på** är det unika namnet på datakällan i din söktjänst.
- **typ** måste vara `azuretable`.
- **autentiseringsuppgifter** parametern innehåller anslutningssträngen för lagringskonto. Se den [ange autentiseringsuppgifter](#Credentials) information.
- **behållaren** anger tabellens namn och en valfri fråga.
    - Ange namnet på tabellen med hjälp av den `name` parametern.
    - Du kan också ange en fråga med hjälp av den `query` parametern. 

> [!IMPORTANT] 
> När det är möjligt ska du använda ett filter på PartitionKey för bästa prestanda. Andra frågor som har en fullständig tabellsökning, vilket resulterar i sämre prestanda för stora tabeller. Se den [prestandaöverväganden](#Performance) avsnittet.


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

Läs mer om API: et för skapa datakällan [skapa Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sätt att ange autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifterna för tabellen i något av följande sätt: 

- **Fullständig åtkomst lagringskontots anslutningssträng**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Du kan hämta anslutningssträngen från Azure portal genom att gå till den **bladet Lagringskonto** > **inställningar** > **nycklar** (för klassisk Storage-konton) eller **inställningar** > **åtkomstnycklar** (för Azure Resource Manager-lagringskonton).
- **Storage-konto shared access signature anslutningssträngen**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Signatur för delad åtkomst bör ha i listan och läsbehörighet på behållare (tabeller i det här fallet) och objekt (tabellrader).
-  **Signatur för delad åtkomst i tabellen**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` Signatur för delad åtkomst bör ha fråga (läsa) behörighet på tabellen.

Mer information om storage delade åtkomstsignaturer, se [använda signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder autentiseringsuppgifter för signatur för delad åtkomst, behöver du autentiseringsuppgifterna för datakällan uppdateras regelbundet med förnyade signaturer för att förhindra att de gått ut. Om autentiseringsuppgifter för signatur för delad åtkomst går ut, misslyckas indexeraren med ett felmeddelande som liknar ”autentiseringsuppgifterna som anges i anslutningssträngen är ogiltig eller har upphört att gälla”.  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fälten i dokument, attribut, och andra konstruktioner som formar sökningen uppleva.

Skapa ett index:

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

Läs mer om hur du skapar index [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med en målsökindex och tillhandahåller ett schema för att automatisera datauppdateringen. 

När index och datakälla har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren körs varannan timme. (Schemaintervallet är inställd på ”PT2H”.) Ange intervallet till ”PT30M” för att köra en indexerare var 30: e minut. Den kortaste stöds är fem minuter. Schemat är valfritt. Om det utelämnas används körs en indexeraren en gång när den har skapats. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om Create Indexer API finns i [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Hantera med olika fältnamn
Ibland skiljer fältnamn i ditt befintliga index sig från kolumner i tabellen. Du kan använda fältmappningar för att mappa egenskapsnamnen från tabellen till fältnamn i sökindexet. Mer information om fältmappningar finns [fältmappningar för Azure Search-indexeraren överbrygga skillnaderna mellan datakällor och search index](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Hantera dokument nycklar
I Azure Search identifierar dokumentnyckeln ett dokument. Varje search-index måste ha exakt ett fält av typen `Edm.String`. Nyckelfältet krävs för varje dokument som läggs till i indexet. (I själva verket det är det enda obligatoriska fältet.)

Eftersom tabellrader har en sammansatt nyckel kan Azure Search genererar ett syntetiska fält med namnet `Key` som är en sammansättning av partition och radnyckel nyckelvärden. Till exempel om en rad PartitionKey är `PK1` och RowKey är `RK1`, kommer `Key` fälts värde är `PK1RK1`.

> [!NOTE]
> Den `Key` värdet får bestå av tecken som är ogiltiga i dokumentet nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av den `base64Encode` [fältet mappning funktionen](search-indexer-field-mappings.md#base64EncodeFunction). Om du gör detta måste du också använda URL-safe Base64-kodning när skicka dokumentet nycklarna i API-anrop, till exempel sökning.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementell indexering och borttagning av identifiering
När du ställer in en tabellindexerare ska köras enligt ett schema, den reindexes bara nya eller uppdaterade rader, vilket avgörs av en rad `Timestamp` värde. Du behöver inte ange en princip för identifiering av ändring. Inkrementell indexering aktiveras åt dig automatiskt.

Du kan använda en strategi för mjuk borttagning för att ange att vissa dokument måste tas bort från indexet. Lägga till en egenskap som indikerar att den har tagits bort och konfigurera en princip för mjuk borttagning på datakällan i stället för att ta bort en rad. Till exempel följande princip anser att en rad tas bort om raden har en egenskap `IsDeleted` med värdet `"true"`:

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

Som standard använder Azure Search följande frågefilter: `Timestamp >= HighWaterMarkValue`. Eftersom Azure-tabeller inte har ett sekundärt index den `Timestamp` den här typen av fråga kräver en fullständig tabellsökning och därför går långsamt för stora tabeller.


Här följer två möjliga sätt för att förbättra prestanda för tabellen indexering. Båda dessa metoder förlitar sig på tabellpartitioner: 

- Om dina data kan naturligt partitioneras i flera partitionsintervall, skapa en datakälla och en motsvarande indexerare för varje partitionsintervall. Varje indexeraren har nu att bearbeta endast en specifik partitionsintervall, vilket resulterar i bättre frågeprestanda. Om de data som måste indexeras har ett litet antal fasta partitioner, ännu bättre: varje indexeraren har endast en partition sökning. Till exempel för att skapa en datakälla för bearbetning av en partitionsintervall med nycklar från `000` till `100`, Använd en fråga så här: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Om dina data är partitionerad med tid (exempelvis kan du skapa en ny partition varje dag eller en vecka), Överväg följande metod: 
    - Använd en fråga i formatet: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Övervaka indexerarens förlopp med hjälp av [hämta API för indexerare Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status), och regelbundet uppdatera den `<TimeStamp>` villkor för frågan baserat på senaste lyckade vattenmärke-värdet. 
    - Om du behöver utlösa en fullständig omindexering med den här metoden måste du återställa datakällan frågan förutom att återställa indexeraren. 


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar eller idéer om förbättringar kan du skicka dem på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
