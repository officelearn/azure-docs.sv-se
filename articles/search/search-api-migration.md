---
title: Uppgradera till den senaste versionen av Azure Search Service REST API-Azure Search
description: Granska skillnader i API-versioner och lär dig vilka åtgärder som krävs för att migrera befintlig kod till den nyaste Azure Search Service REST API-versionen.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 6c1f7fdb1f349c9e31ba63d79a9b9e26ea9f09da
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182380"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Uppgradera till den senaste Azure Search tjänsten REST API version
Om du använder en tidigare version av [Azure Search tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/)hjälper den här artikeln dig att uppgradera ditt program till att använda den senaste allmänt tillgängliga API-versionen, 2019-05-06.

Version 2019-05-06 av REST API innehåller vissa ändringar från tidigare versioner. Dessa är huvudsakligen bakåtkompatibla, så att ändringar i din kod bara kräver minimal ansträngning, beroende på vilken version du använde tidigare. [Steg för att uppgradera](#UpgradeSteps) beskriver de kod ändringar som krävs för att använda nya funktioner.

> [!NOTE]
> En Azure Search tjänst instans stöder flera REST API versioner, inklusive tidigare. Du kan fortsätta att använda dessa API-versioner, men vi rekommenderar att du migrerar din kod till den senaste versionen så att du kan komma åt nya funktioner.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Vad är nytt i version 2019-05-06
Version 2019-05-06 är den nyaste allmänt tillgängliga versionen av Azure Search tjänsten REST API. Funktioner som har gått över till allmänt tillgängliga status i den här API-versionen är:

* Funktionen [komplettera automatiskt](index-add-suggesters.md) är en typeahead-funktion som slutför en delvis angiven term Indatatyp.

* [Komplexa typer](search-howto-complex-data-types.md) ger inbyggt stöd för strukturerade objekt data i ett Azure Search index.

* [JsonLines tolknings lägen](search-howto-index-json-blobs.md), en del av Azure Blob-indexering, skapar ett Sök dokument per JSON-entitet som skiljs åt av en ny rad.

* [Kognitiv sökning](cognitive-search-concept-intro.md) tillhandahåller indexering som utnyttjar AI-programanriknings motorer i Cognitive Services.

Flera för hands versions funktioner sammanfaller med den här allmänt tillgängliga uppdateringen. Se [search REST API-version 2019-05-06-Preview](search-api-preview.md)för att granska listan över nya för hands versions funktioner.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Befintlig kod som innehåller följande funktioner kommer att brytas på API-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexerare för Azure Cosmos DB-DataSource är nu "typ": "cosmosdb"

Om du använder en [Cosmos DB](search-howto-index-cosmosdb.md )-indexerare måste du ändra `"type": "documentdb"` till `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Resultat fel för körning av indexerare har inte längre status

Fel strukturen för körning av indexet hade tidigare ett `status` element. Det här elementet har tagits bort eftersom det inte tillhandahöll användbar information.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Data källans API för indexeraren returnerar inte längre anslutnings strängar

Från API-versionerna 2019-05-06 och 2019-05-06 – för hands versions hantering returnerar API: et för data källan inte längre anslutnings strängar i svar på eventuella REST-åtgärder. I tidigare API-versioner, för data källor som skapats med POST, returnerade Azure Search **201** följt av OData-svaret, som innehöll anslutnings strängen i oformaterad text.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Arbetsenhets igenkänning för entitets igenkänning upphör nu

Om du anropar [namn entitets igenkännings](cognitive-search-skill-named-entity-recognition.md) kunskaper i din kod, Miss kommer anropet. Ersättnings funktionen är [enhets igenkänning](cognitive-search-skill-entity-recognition.md). Du bör kunna ersätta kunskaps referensen utan andra ändringar. API-signaturen är densamma för båda versionerna. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Om du uppgraderar från en tidigare GA-version, 2017-11-11 eller 2016-09-01, behöver du förmodligen inte göra några ändringar i koden, förutom att ändra versions numret. De enda situationer där du kan behöva ändra kod är när:

* Koden fungerar inte när okända egenskaper returneras i ett API-svar. Som standard ska programmet ignorera egenskaper som det inte förstår.

* Din kod behåller API-begärandena och försöker skicka om dem till den nya API-versionen. Detta kan till exempel inträffa om ditt program behåller fortsättnings-token som returneras från Sök-API: et (mer information finns `@search.nextPageParameters` i [Sök-API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)-referensen).

Om någon av dessa situationer gäller dig kan du behöva ändra koden. Annars bör inga ändringar vara nödvändiga om du inte vill börja använda de [nya funktionerna](#WhatsNew) i version 2019-05-06.

Om du uppgraderar från en för hands version av API-versionen gäller ovanstående även, men du måste också vara medveten om att vissa för hands versions funktioner inte är tillgängliga i version 2019-05-06:

* ["Mer liknar detta" frågor](search-more-like-this.md)
* [Index för CSV-BLOB](search-howto-index-csv-blobs.md)
* [Stöd för MongoDB-API för Cosmos DB indexerare](search-howto-index-cosmosdb.md)

Om din kod använder dessa funktioner kommer du inte att kunna uppgradera till API-version 2019-05-06 utan att ta bort din användning av dem.

> [!IMPORTANT]
> För hands versions-API: er är avsedda för testning och utvärdering och ska inte användas i produktions miljöer.
> 

### <a name="upgrading-complex-types"></a>Uppgradera komplexa typer

Om din kod använder komplexa typer med äldre Preview API-versioner 2017-11-11-Preview eller 2016-09-01 – för hands version, finns det några nya och ändrade gränser i version 2019-05-06 som du måste vara medveten om:

+ Gränserna för djupet i under fält och antalet komplexa samlingar per index har sänkts. Om du har skapat index som överskrider dessa gränser med hjälp av API-versionerna för för hands versionen, kommer alla försök att uppdatera eller återskapa dem med hjälp av API version 2019-05-06 att Miss lyckas. Om detta gäller för dig måste du utforma om schemat så att det passar inom de nya gränserna och sedan återskapa ditt index.

+ Det finns en ny gräns i API-version 2019-05-06 för antalet element i komplexa samlingar per dokument. Om du har skapat index med dokument som överskrider dessa gränser med hjälp av API-versionerna för för hands versionen, kommer alla försök att indexera om dessa data med hjälp av API-version 2019-05-06 att Miss lyckas. Om detta gäller för dig måste du minska antalet komplexa samlings element per dokument innan du indexerar om dina data.

Mer information finns i [tjänst begränsningar för Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Så här uppgraderar du en gammal komplex typ struktur

Om din kod använder komplexa typer med en av de äldre API-versionerna för för hands versionen kan du använda ett index definitions format som ser ut så här:

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

Ett nyare träd som liknar formatet för att definiera index fält introducerades i API-versionen 2017-11-11-Preview. I det nya formatet har varje komplex fält en fält samling där dess under fält har definierats. I API version 2019-05-06 används det här nya formatet enbart och om du försöker skapa eller uppdatera ett index med det gamla formatet kommer att Miss lyckas. Om du har index som skapats med det gamla formatet måste du använda API-versionen 2017-11-11-Preview för att uppdatera dem till det nya formatet innan de kan hanteras med API-version 2019-05-06.

Du kan uppdatera "platta" index till det nya formatet med följande steg med hjälp av API-version 2017-11-11-för hands version:

1. Hämta ditt index genom att utföra en GET-begäran. Om den redan är i det nya formatet är du klar.

2. Översätt indexet från det "platta" formatet till det nya formatet. Du måste skriva kod för detta eftersom det inte finns någon exempel kod tillgänglig vid tidpunkten för den här skrivningen.

3. Utför en skicka-begäran för att uppdatera indexet till det nya formatet. Se till att du inte ändrar någon annan information om indexet, till exempel sökmöjlighet/filtrering av fält, eftersom detta inte tillåts av uppdaterings indexets API.

> [!NOTE]
> Det går inte att hantera index som skapats med det gamla "platta" formatet från Azure Portal. Uppgradera dina index från "platt" åter givning till "Tree"-representationen i den första bekvämligheten.

## <a name="next-steps"></a>Nästa steg

Läs dokumentationen om Azure Search Service REST API Reference. Om du stöter på problem kan du be oss om hjälp om [StackOverflow](https://stackoverflow.com/) eller [kontakta supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Sök tjänst REST API referens](https://docs.microsoft.com/rest/api/searchservice/)

