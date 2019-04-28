---
title: Lista över Azure Storage-resurser med Storage-klientbiblioteket för C++ | Microsoft Docs
description: 'Lär dig hur du använder listor API: er i Microsoft Azure Storage-klientbibliotek för C++ att räkna upp behållare, blobbar, köer, tabeller och entiteter.'
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.subservice: common
ms.openlocfilehash: 13ddb4d64908421e999174623003acd2fb24024d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483459"
---
# <a name="list-azure-storage-resources-in-c"></a>Lista över Azure Storage-resurser i C++
Lista åtgärder är nyckeln till många utvecklingsscenarier med Azure Storage. Den här artikeln beskriver hur du mest effektivt räkna upp objekt i Azure Storage med på listan API: er som angavs i Microsoft Azure Storage-klientbiblioteket för C++.

> [!NOTE]
> Den här guiden riktar sig mot Azure Storage-klientbiblioteket för C++ version 2.x som är tillgänglig via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

Storage-klientbiblioteket erbjuder en mängd olika metoder för att lista eller fråga objekt i Azure Storage. Den här artikeln tar upp följande scenarier:

* Lista behållare i ett konto
* Lista blobar i en behållare eller virtuella blob directory
* Lista köer på ett konto
* Lista tabellerna i ett konto
* Kör frågor mot entiteter i en tabell

Var och en av dessa metoder visas med hjälp av olika överlagringar för olika scenarier.

## <a name="asynchronous-versus-synchronous"></a>Asynkrona och synkrona
Eftersom Storage-klientbiblioteket för C++ är byggt ovanpå den [C++ REST-biblioteket](https://github.com/Microsoft/cpprestsdk), vi stöds asynkrona åtgärder med hjälp av [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Exempel:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synkrona åtgärder omsluta motsvarande asynkrona åtgärder:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Om du arbetar med flera trådade program eller tjänster, rekommenderar vi att du använder async-API: er direkt i stället för att skapa en tråd för att anropa sync API: er, vilket avsevärt försämring av prestandan.

## <a name="segmented-listing"></a>Segmenterade lista
Skalan för molnlagring kräver segmenterade lista. Du kan exempelvis ha över en miljon blobarna i en Azure blob-behållare eller över en miljard entiteter i en Azure-tabell. Det här är inte teoretisk siffror, men verkliga kundärenden för användning.

Därför är det opraktiskt att lista alla objekt i ett enda svar. I stället kan du visa objekt med hjälp av sidindelning. Varje lista API: er har en *segmenterade* överbelasta.

Svaret för segmenterade liståtgärden innehåller:

* <i>_segment</i>, som innehåller de resultat som returneras för ett enda anrop till API: T för listan.
* *continuation_token*, som skickas till nästa anrop för att kunna hämta nästa sida i resultatet. När det finns inga fler resultat ska returneras, är fortsättningstoken null.

En typisk kommandot för att lista alla blobar i en behållare kan till exempel se ut som följande kodavsnitt. Koden finns i vår [exempel](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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

Observera att antalet resultat som returneras i en sida kan styras av parametern *max_results* i överlagringen för varje API, till exempel:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Om du inte anger den *max_results* parametern, som är standard maximivärdet för upp till 5 000 resultaten returneras i en enda sida.

Observera också att en fråga mot Azure Table storage kan returnera inga poster eller färre poster än värdet för den *max_results* parameter som du angav, även om fortsättningstoken inte är tom. En orsak kan vara att frågan inte kunde slutföras i fem sekunder. Så länge fortsättningstoken inte är tom frågan ska fortsätta och din kod anta att inte storleken på segment resultat.

Det rekommendera kodning mönstret för de flesta fall är uppdelat lista, som innehåller explicita fortskrider lista eller fråga och hur tjänsten ska svara på varje begäran. Särskilt för C++-program eller tjänster, kan det hjälpa att kontrollen på låg nivå av lista förloppet kontroll minne och prestanda.

## <a name="greedy-listing"></a>Girig lista
Tidigare versioner av Storage-klientbiblioteket för C++ (versioner 0.5.0 Förhandsgranska och tidigare) ingår icke-segmenterade listan API: er för tabeller och köer, som i följande exempel:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Dessa metoder har implementerats som omslutningar segmenterade API: er. För varje svar av segmenterade lista koden sist resultaten till en vector och returneras alla resultat efter fullständig behållarna genomsöktes.

Den här metoden fungerar när storage-konto eller tabellen innehåller ett litet antal objekt. Men med en ökning av antalet objekt kan det minne som krävs öka utan begränsning, eftersom alla resultat som finns kvar i minnet. Samma åtgärd kan ta lång tid, som anroparen har ingen information om förloppet.

Dessa girig lista API: er i SDK: N inte finns i C#, Java eller Node.js för JavaScript-miljö. För att undvika potentiella problem med att använda dessa girig API: er, vi har tagit bort dem i version 0.6.0 förhandsversion.

Om din kod anropar dessa girig API: er:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Du bör sedan ändra koden om du vill använda segmenterade lista API: er:

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

Genom att ange den *max_results* parametern för segmentet som du kan balansera mellan antalet begäranden och minnesförbrukningen för att uppfylla prestandaöverväganden för ditt program.

Dessutom om du använder segmenterade lista API: er och lagra data i en lokal samling i en ”girig” style också rekommenderar vi starkt att du omstrukturera din kod för att hantera data lagras i en lokal insamling noggrant i stor skala.

## <a name="lazy-listing"></a>Lazy lista
Även om girig lista upphöjt potentiella problem, är det praktiskt om det inte finns för många objekt i behållaren.

Om du använder också C# eller Oracle Java-SDK: er, bör du känna till de uppräkningsbara programmeringsmodell som erbjuder en lazy--format som lista, där data vid en viss förskjutning hämtas endast om det behövs. Iteratorn-baserade-mallen innehåller också ett liknande sätt i C++.

En typisk lazy lista API, med hjälp av **list_blobs** exempelvis ser ut så här:

```cpp
list_blob_item_iterator list_blobs() const;
```

Ett typiskt kodfragment som använder mönstret lazy lista kan se ut så här:

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

Observera att lazy lista endast är tillgänglig i synkront läge.

Lazy lista hämtar jämfört med girig lista, data vid behov. Under försättsbladen hämtar den data från Azure Storage endast när nästa Iteratorn flyttar till nästa segment. Därför minnesanvändning styrs med en begränsad storlek och åtgärden är snabb.

Lazy lista API: er ingår i Storage-klientbiblioteket för C++ i version 2.2.0.

## <a name="conclusion"></a>Sammanfattning
I den här artikeln beskrivs vi olika överlagringar för att lista API: er för olika objekt i Storage-klientbiblioteket för C++. Sammanfattningsvis:

* Async APIs rekommenderas under flera trådade scenarier.
* Segmenterade lista rekommenderas för de flesta scenarier.
* Lazy lista tillhandahålls i biblioteket som en praktisk omslutning i synkron scenarier.
* Girig lista rekommenderas inte och har tagits bort från biblioteket.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och -klientbiblioteket för C++ finns i följande resurser.

* [Använda Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Använda Queue Storage från C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage-klientbibliotek för C++ API-dokumentationen.](https://azure.github.io/azure-storage-cpp/)
* [Azure Storage Teamblogg](https://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)

