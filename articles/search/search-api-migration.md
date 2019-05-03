---
title: Uppgradera till den senaste versionen i Azure Search Service REST API - Azure Search
description: Granska skillnaderna i API-versioner och lär dig vilka åtgärder som krävs för att migrera befintliga kod till den senaste Azure Search Service REST API-versionen.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 286d8bbc01b5916e842c196aed5a49ef1c76bc3c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025205"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Uppgradera till den senaste versionen av Azure Search Service REST API
Om du använder en tidigare version av den [Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/), den här artikeln hjälper dig att uppgradera programmet så att den senaste allmänt tillgängliga API-versionen 2019-05-06.

Version 2019-05-06 av REST API innehåller vissa ändringar från tidigare versioner. Det här är främst bakåtkompatibla, så ändra din kod kräver bara enkelt, beroende på vilken version du använde före. [Steg för att uppgradera](#UpgradeSteps) beskriver kodändringar som krävs för att använda nya funktioner.

> [!NOTE]
> En instans för Azure Search har stöd för ett intervall av REST API-versioner, inklusive tidigare värden. Du kan fortsätta att använda dessa API-versioner, men vi rekommenderar att du migrerar din kod till den senaste versionen så att du kan komma åt nya funktioner.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Vad är nytt i version 2019-05-06
Version 2019-05-06 är den senaste allmänt tillgängliga versionen av Azure Search Service REST API. Funktioner som har gått över till allmänt tillgänglig status i den här API-versionen:

* [Automatisk komplettering](index-add-suggesters.md) är en funktion för typeahead som slutför en delvis angivna termen som indata.

* [Komplexa typer](search-howto-complex-data-types.md) innehåller inbyggt stöd för strukturobjekt data i ett Azure Search-index.

* [JsonLines parsning lägen](search-howto-index-json-blobs.md)är en del av Azure Blob indexering, skapar ett Sök-dokument per JSON-entitet som avgränsas med en ny rad.

* [Cognitive Search](cognitive-search-concept-intro.md) ger indexering som utnyttjar AI berikande motorerna i Cognitive Services.

Flera förhandsversioner funktionen sammanfalla med den här allmänt tillgängliga uppdateringen. Om du vill granska listan över nya funktioner i förhandsversion, se [Search REST api-versionen 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Bryter befintlig kod som innehåller följande funktioner i api-versionen = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexerare för Azure Cosmos DB - datakälla är nu ”type”: ”cosmosdb”

Om du använder en [Cosmos DB-indexeraren](search-howto-index-cosmosdb.md ), måste du ändra `"type": "documentdb"` till `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Indexeraren körningsfel resultatet har inte längre status

Fel-strukturen för körning av indexerare tidigare hade en `status` element. Det här elementet har tagits bort eftersom den inte ge användbar information.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Datakälla för indexeraren API returnerar inte längre anslutningssträngar

Från API: et returnerar versioner 2019-05-06 och 2019-05-06-Preview och senare, datakällans API inte längre anslutningssträngar i svaret på eventuella REST-åtgärden. I tidigare versioner av API för datakällor som har skapats med INLÄGGET Azure Search returnerade **201** följt av OData-svar som innehåller anslutningssträngen i oformaterad text.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Med namnet Entitetsidentifiering kognitiva kunskaper har nu upphört

Om du anropar [namn Entitetsidentifiering](cognitive-search-skill-named-entity-recognition.md) kunskaper i din kod, kommer anropet att misslyckas. Ersättning fungerar [Entitetsidentifiering](cognitive-search-skill-entity-recognition.md). Du bör kunna Ersätt färdighet referensen med några andra ändringar. API-signaturen är densamma för båda versionerna. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Om du uppgraderar från en tidigare GA-version 2017-11-11 eller 2016-09-01 du förmodligen inte göra några ändringar i koden, annat än för att ändra versionsnumret. De enda situationer där du kan behöva ändra kod är när:

* Det går inte att din kod när okänd egenskaper returneras som en API-svar. Ditt program bör Ignorera egenskaper som inte förstår som standard.

* Din kod kvarstår API-begäranden och försöker skicka om dem till den nya API-versionen. Exempel: Detta kan inträffa om programmet kvarstår fortsättning token som returnerades från API: et Search (leta efter mer information `@search.nextPageParameters` i den [Search API-referens](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Om något av dessa fall gäller dig kan sedan du behöva ändras din kod. I annat fall inga ändringar bör vara nödvändigt om du inte vill börja använda den [nya funktioner](#WhatsNew) version 2019-05-06.

Om du uppgraderar från en API-förhandsversion ovanstående gäller också, men du måste också vara medveten om att vissa funktioner i förhandsversion inte är tillgängliga i version 2019-05-06:

* [”Mer som detta” frågor](search-more-like-this.md) fortsätter att vara en funktion som skrivskyddade.

Om din kod använder dessa funktioner kan du inte uppgradera till API-versionen 2019-05-06 utan att ta bort din användning av dem.

> [!IMPORTANT]
> Förhandsversion av API: er är avsedda för testning och utvärdering och ska inte användas i produktionsmiljöer.
> 

### <a name="upgrading-complex-types"></a>Uppgradera komplexa typer

Om din kod använder komplexa typer med de äldre förhandsversion API-versionerna 2017-11-11-förhandsversion eller 2016-09-01-Preview, finns det vissa begränsningar för nya och ändrade i version 2019-05-06 som du måste känna:

+ Begränsningar i djupet i underordnade fält och antalet komplexa samlingar per index har sänkts. Om du har skapat index som högre gränser med hjälp av api-förhandsversioner någon försöker uppdatera eller återskapa dem med hjälp av API-versionen 2019-05-06 kommer att misslyckas. Om detta gäller dig, behöver du kan ändra schemat för att rymmas inom de nya gränserna och sedan återskapa index.

+ Det finns en ny gräns i api-versionen 2019-05-06 på antalet element i komplexa samlingar per dokument. Om du har skapat index med dokument som överskrider gränserna med hjälp av api-förhandsversioner misslyckas alla försök att indexera data med hjälp av api-versionen 2019-05-06. Om detta gäller dig behöver att minska antalet komplex samlingselement per dokument innan omindexering dina data.

Mer information finns i [tjänstbegränsningar för Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Så här uppgraderar du en gammal komplex typ-struktur

Om din kod använder komplexa typer med en äldre API-versioner för förhandsversionen kan använder du ett format för definition av index som ser ut så här:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Ett nyare trädet-liknande format för att definiera fält i indexet introducerades i API-version 2017-11-11-förhandsversion. I det nya formatet varje komplexa fält har en samling fält där dess underordnade fält definieras. Detta nya format används exklusivt i API-versionen 2019-05-06, och går inte att skapa eller uppdatera ett index med det äldre formatet. Om du har index som skapats med hjälp av det äldre formatet, måste du använda API version 2017-11-11-Preview för att uppdatera dem till det nya formatet innan de kan hanteras med hjälp av API-versionen 2019-05-06.

Du kan uppdatera ”fast” index till det nya formatet med följande steg med hjälp av API-version 2017-11-11-Preview:

1. Utföra en GET-begäran att hämta ditt index. Om det redan finns i det nya formatet är du klar.

2. Översätt index från ”fast” formatet till det nya formatet. Har du skriva kod för den här eftersom det finns inga exempelkoden tillgänglig vid tidpunkten för detta skrivs.

3. Utföra en PUT-begäran för att uppdatera indexet till det nya formatet. Se till att du inte ändra någon annan information om indexet, till exempel sökbarheten/filterability av fält, eftersom detta inte tillåts av API: et för Update-Index.

> [!NOTE]
> Det går inte att hantera index som skapats med det gamla ”fast” formatet från Azure-portalen. Uppgradera dina index från ”fast” representation till ”” trädrepresentation vid första möjliga tillfälle.

## <a name="next-steps"></a>Nästa steg

Läs dokumentationen för Azure Search Service REST API-referens. Om du får problem kan be oss hjälp på [StackOverflow](https://stackoverflow.com/) eller [supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Search service REST API-referens](https://docs.microsoft.com/rest/api/searchservice/)

