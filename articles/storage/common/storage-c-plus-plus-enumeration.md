---
title: Lista Azure Storage-resurser med C++-klientbibliotek
description: Lär dig hur du använder list-API:erna i Microsoft Azure Storage Client Library for C++ för att räkna upp behållare, blobbar, köer, tabeller och entiteter.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74269454"
---
# <a name="list-azure-storage-resources-in-c"></a>Lista över Azure Storage-resurser i C++

Lista åtgärder är nyckeln till många utvecklingsscenarier med Azure Storage. I den här artikeln beskrivs hur du mest effektivt räknar upp objekt i Azure Storage med hjälp av listnings-API:erna som finns i Microsoft Azure Storage Client Library for C++.

> [!NOTE]
> Den här guiden riktar sig till Azure Storage Client Library för C++ version 2.x, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

Storage Client Library innehåller en mängd olika metoder för att lista eller fråga objekt i Azure Storage. I den här artikeln beskrivs följande scenarier:

* Lista behållare i ett konto
* Lista blobbar i en behållare eller virtuell blob-katalog
* Lista köer i ett konto
* Lista tabeller i ett konto
* Fråga entiteter i en tabell

Var och en av dessa metoder visas med hjälp av olika överbelastningar för olika scenarier.

## <a name="asynchronous-versus-synchronous"></a>Asynkron kontra synkron

Eftersom storage client library for C++ är byggt ovanpå [C++ REST-biblioteket](https://github.com/Microsoft/cpprestsdk)stöder vi i sig asynkrona åtgärder med [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Ett exempel:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synkrona åtgärder radbryt motsvarande asynkrona åtgärder:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Om du arbetar med flera trådprogram eller -tjänster rekommenderar vi att du använder asynkron-API:erna direkt i stället för att skapa en tråd för att anropa synkroniserings-API:erna, vilket avsevärt påverkar din prestanda.

## <a name="segmented-listing"></a>Segmenterad notering

Omfattningen av molnlagring kräver segmenterad lista. Du kan till exempel ha över en miljon blobbar i en Azure-blob-behållare eller över en miljard entiteter i en Azure-tabell. Dessa är inte teoretiska siffror, men verkliga kundanvändningsfall.

Det är därför opraktiskt att lista alla objekt i ett enda svar. I stället kan du lista objekt med växling. Var och en av noterings-API:erna har en *segmenterad* överbelastning.

Svaret för en segmenterad listning omfattar:

* *_segment*, som innehåller den uppsättning resultat som returneras för ett enda anrop till listnings-API:et.
* *continuation_token*, som skickas till nästa samtal för att få nästa sida med resultat. När det inte finns några fler resultat att returnera är fortsättningstoken null.

Ett vanligt anrop för att lista alla blobbar i en behållare kan till exempel se ut som följande kodavsnitt. Koden finns i våra [prover:](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Observera att antalet resultat som returneras på en sida kan styras av parametern *max_results* i överbelastningen av varje API, till exempel:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Om du inte anger *parametern max_results* returneras standardmaxavärdet på upp till 5 000 resultat på en enda sida.

Observera också att en fråga mot Azure Table storage inte kan returnera några poster, eller färre poster än värdet *för den max_results* parameter som du angav, även om fortsättningstoken inte är tom. En orsak kan vara att frågan inte kunde slutföras på fem sekunder. Så länge fortsättningstoken inte är tom ska frågan fortsätta och koden bör inte anta storleken på segmentresultaten.

Det rekommenderade kodningsmönstret för de flesta scenarier är segmenterad lista, vilket ger tydliga förlopp för listning eller frågor och hur tjänsten svarar på varje begäran. Särskilt för C++-program eller -tjänster kan kontroll på lägre nivå av listningsförloppet hjälpa till att kontrollera minne och prestanda.

## <a name="greedy-listing"></a>Girig notering

Tidigare versioner av Storage Client Library for C++ (version 0.5.0 Preview och tidigare) inkluderade icke-segmenterade list-API:er för tabeller och köer, som i följande exempel:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Dessa metoder har implementerats som omslag av segmenterade API:er. För varje svar i segmenterade listor bifogade koden resultaten till en vektor och returnerade alla resultat efter att de fullständiga behållarna genomsöktes.

Den här metoden kan fungera när lagringskontot eller tabellen innehåller ett litet antal objekt. Men med en ökning av antalet objekt kan det minne som krävs öka utan begränsning, eftersom alla resultat kvarstod i minnet. En notering operation kan ta mycket lång tid, under vilken den som ringer hade ingen information om dess framsteg.

Dessa giriga lista API:er i SDK finns inte i C#, Java eller JavaScript Node.js-miljön. För att undvika potentiella problem med att använda dessa giriga API:er tog vi bort dem i förhandsversionen av version 0.6.0.

Om koden anropar dessa giriga API:er:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Sedan bör du ändra koden för att använda segmenterade lista API: er:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Genom att ange *parametern max_results* för segmentet kan du balansera mellan antalet begäranden och minnesanvändning för att uppfylla prestandaöverväganden för ditt program.

Om du använder segmenterade list-API:er, men lagrar data i en lokal samling i en "girig" stil, rekommenderar vi dessutom starkt att du gör om koden för att hantera lagring av data i en lokal samling noggrant i stor skala.

## <a name="lazy-listing"></a>Lata notering

Även giriga notering upp potentiella problem, är det bekvämt om det inte finns för många objekt i behållaren.

Om du också använder C# eller Oracle Java SDK: er, bör du vara bekant med den uppräkningsbara programmeringsmodellen, som erbjuder en lat stil notering, där data på en viss förskjutning endast hämtas om det behövs. I C++tillhandahåller den iteratorbaserade mallen också en liknande metod.

Ett typiskt latliste-API med **list_blobs** som exempel ser ut så här:

```cpp
list_blob_item_iterator list_blobs() const;
```

Ett typiskt kodavsnitt som använder lata listningsmönstret kan se ut så här:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Observera att lata listor endast är tillgängliga i synkront läge.

Jämfört med giriga notering, lat notering hämtar data endast när det behövs. Under täcket hämtar den data från Azure Storage endast när nästa iterator flyttar in i nästa segment. Därför styrs minnesanvändningen med en begränsad storlek och åtgärden är snabb.

Lata list-API:er ingår i lagringsklientbiblioteket för C++ i version 2.2.0.

## <a name="conclusion"></a>Slutsats

I den här artikeln diskuterade vi olika överbelastningar för att lista API:er för olika objekt i storage client library för C++ . Sammanfattning:

* Async API:er rekommenderas starkt under flera trådningsscenarier.
* Segmenterad lista rekommenderas för de flesta scenarier.
* Lazy notering finns i biblioteket som ett bekvämt omslag i synkrona scenarier.
* Girighet lista rekommenderas inte och har tagits bort från biblioteket.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Storage och client library för C++finns i följande resurser.

* [Så här använder du Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Så här använder du Tabelllagring från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Så använder du Queue Storage från C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage Client Library för C++ API-dokumentation.](https://azure.github.io/azure-storage-cpp/)
* [Blogg för Azure Storage Team](https://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
