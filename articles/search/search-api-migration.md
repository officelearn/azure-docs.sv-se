---
title: Uppgradera REST API-versioner
titleSuffix: Azure Cognitive Search
description: Granska skillnader i API-versioner och lär dig vilka åtgärder som krävs för att migrera befintlig kod till den senaste AZURE Cognitive Search-tjänst REST API-versionen.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112165"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Uppgradera till den senaste REST API-versionen för Azure Cognitive Search-tjänsten REST API

Om du använder en tidigare version av [REST-API:et](https://docs.microsoft.com/rest/api/searchservice/)för sök kommer den här artikeln att hjälpa dig att uppgradera programmet så att det använder den senaste allmänt tillgängliga API-versionen, 2019-05-06.

Version 2019-05-06 av REST API innehåller vissa ändringar från tidigare versioner. Dessa är oftast bakåtkompatibla, så att ändra din kod bör kräva endast minimal ansträngning, beroende på vilken version du använde tidigare. [Steg för att uppgradera](#UpgradeSteps) beskriver de kodändringar som krävs för att använda nya funktioner.

> [!NOTE]
> En Azure Cognitive Search-tjänstinstans stöder en rad REST API-versioner, inklusive tidigare. Du kan fortsätta att använda dessa API-versioner, men vi rekommenderar att du migrerar koden till den senaste versionen så att du kan komma åt nya funktioner.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Nyheter i version 2019-05-06
Version 2019-05-06 är den senaste allmänt tillgängliga versionen av REST API. Funktioner som har övergått till allmänt tillgänglig status i den här API-versionen inkluderar:

* [Komplettera automatiskt](index-add-suggesters.md) är en typeahead-funktion som slutför en delvis angiven terminmatning.

* [Komplexa typer](search-howto-complex-data-types.md) ger inbyggt stöd för strukturerade objektdata i sökindex.

* [JsonLines tolkningslägen](search-howto-index-json-blobs.md), en del av Azure Blob-indexering, skapar ett sökdokument per JSON-entitet som är avgränsat med en nyrad.

* [AI-anrikning](cognitive-search-concept-intro.md) tillhandahåller indexering som utnyttjar AI-anrikningsmotorerna i Cognitive Services.

Flera versioner av förhandsgranskningsfunktionen sammanfaller med den här allmänt tillgängliga uppdateringen. Mer om du vill granska listan över nya förhandsgranskningsfunktioner finns i [Sök REST api-version 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Befintlig kod som innehåller följande funktioner bryts på api-version=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer för Azure Cosmos DB - datakälla är nu "typ": "cosmosdb"

Om du använder en [Cosmos DB-indexerare](search-howto-index-cosmosdb.md )måste du ändra `"type": "documentdb"` till `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Resultatfel för indexeringskörning har inte längre status

Felstrukturen för indexeringskörning `status` hade tidigare ett element. Det här elementet togs bort eftersom det inte gav användbar information.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Indexer datakälla API returnerar inte längre anslutningssträngar

Från API-versioner 2019-05-06 och 2019-05-06-Preview och framåt returnerar datakäll-API:et inte längre anslutningssträngar i svaret på någon REST-åtgärd. I tidigare API-versioner, för datakällor som skapats med HJÄLP AV POST, azure cognitive search returnerade **201** följt av OData-svaret, som innehöll anslutningssträngen i oformaterad text.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Namngiven kognitiv färdighet för entitetsigenkänning har nu upphört

Om du anropar [namnentitetsigenkänningsfärdigheten](cognitive-search-skill-named-entity-recognition.md) i koden misslyckas anropet. Ersättningsfunktionen är [entitetsigenkänning](cognitive-search-skill-entity-recognition.md). Du bör kunna ersätta färdighetsreferensen med inga andra ändringar. API-signaturen är densamma för båda versionerna. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Om du uppgraderar från en tidigare GA-version, 2017-11-11 eller 2016-09-01, behöver du förmodligen inte göra några ändringar i din kod, förutom att ändra versionsnumret. De enda situationer där du kan behöva ändra kod är när:

* Koden misslyckas när okända egenskaper returneras i ett API-svar. Som standard bör ditt program ignorera egenskaper som det inte förstår.

* Koden behåller API-begäranden och försöker skicka dem till den nya API-versionen. Detta kan till exempel inträffa om programmet behåller fortsättningstoken som returneras från `@search.nextPageParameters` sök-API:et (efter mer information, leta efter i referensen för [sök-API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Om någon av dessa situationer gäller dig kan du behöva ändra koden därefter. Annars bör inga ändringar vara nödvändiga om du inte vill börja använda de [nya funktionerna](#WhatsNew) i version 2019-05-06.

Om du uppgraderar från en API-version för förhandsversion gäller ovanstående också, men du måste också vara medveten om att vissa förhandsversionsfunktioner inte är tillgängliga i version 2019-05-06:

* ["Mer så här" frågor](search-more-like-this.md)
* [CSV-blob-indexering](search-howto-index-csv-blobs.md)
* [MongoDB API-stöd för Cosmos DB-indexerare](search-howto-index-cosmosdb.md)

Om koden använder dessa funktioner kan du inte uppgradera till API version 2019-05-06 utan att ta bort din användning av dem.

> [!IMPORTANT]
> Förhandsversion API:er är avsedda för testning och utvärdering och bör inte användas i produktionsmiljöer.
> 

### <a name="upgrading-complex-types"></a>Uppgradera komplexa typer

Om koden använder komplexa typer med de äldre förhandsversionen av API-versionerna 2017-11-11-Preview eller 2016-09-01-Preview finns det några nya och ändrade gränser i version 2019-05-06 som du måste vara medveten om:

+ Gränserna för underfältens djup och antalet komplexa samlingar per index har sänkts. Om du har skapat index som överskrider dessa gränser med hjälp av api-versionerna för förhandsversionen misslyckas alla försök att uppdatera eller återskapa dem med API-version 2019-05-06. Om detta gäller dig måste du designa om schemat så att det passar inom de nya gränserna och sedan återskapa indexet.

+ Det finns en ny gräns i api-version 2019-05-06 för antalet element i komplexa samlingar per dokument. Om du har skapat index med dokument som överskrider dessa gränser med hjälp av api-versionerna för förhandsversionen misslyckas alla försök att indexera om dessa data med api-version 2019-05-06. Om detta gäller dig måste du minska antalet komplexa insamlingselement per dokument innan du indexerar om dina data.

Mer information finns i [Tjänstgränser för Azure Cognitive Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Så här uppgraderar du en gammal komplex typstruktur

Om koden använder komplexa typer med en av de äldre api-versionerna för förhandsversionen kanske du använder ett indexdefinitionsformat som ser ut så här:

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

Ett nyare trädliknande format för att definiera indexfält introducerades i API-version 2017-11-11-Preview. I det nya formatet har varje komplext fält en fältsamling där dess underfält definieras. I API-versionen 2019-05-06 används det nya formatet uteslutande och det går inte att försöka skapa eller uppdatera ett index med det gamla formatet. Om du har skapat index med det gamla formatet måste du använda API version 2017-11-11-Preview för att uppdatera dem till det nya formatet innan de kan hanteras med API-versionen 2019-05-06.

Du kan uppdatera "platta" index till det nya formatet med följande steg med API-versionen 2017-11-11-Preview:

1. Utför en GET-begäran om att hämta ditt index. Om det redan är i det nya formatet är du klar.

2. Översätt indexet från det "platta" formatet till det nya formatet. Du måste skriva kod för detta eftersom det inte finns någon exempelkod tillgänglig i skrivande stund.

3. Utför en PUT-begäran om att uppdatera indexet till det nya formatet. Se till att inte ändra någon annan information om indexet, till exempel sökbarhet/filterabilitet för fält, eftersom detta inte är tillåtet av API:et för uppdateringsindex.

> [!NOTE]
> Det går inte att hantera index som skapats med det gamla "platta" formatet från Azure-portalen. Uppgradera dina index från den "platta" representationen till "träd"-representationen så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

Granska referensdokumentationen för sök-REST API. Om du stöter på problem kan du be oss om hjälp med [StackOverflow](https://stackoverflow.com/) eller [kontakta supporten.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [REST API-referens för söktjänst](https://docs.microsoft.com/rest/api/searchservice/)

