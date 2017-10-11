---
title: "Visa en lista med Azure Storage-resurser med Storage-klientbibliotek för C++ | Microsoft Docs"
description: "Lär dig hur du använder lista API: er i Microsoft Azure Storage-klientbibliotek för C++ att räkna upp behållare, blobbar, köer, tabeller och enheter."
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="list-azure-storage-resources-in-c"></a>Visa en lista med Azure Storage-resurser i C++
Lista över åtgärder är nyckeln till många utvecklingsscenarier för med Azure Storage. Den här artikeln beskrivs mer effektivt att räkna upp objekt i Azure Storage med hjälp av listan API: er som finns i Microsoft Azure Storage-klientbibliotek för C++.

> [!NOTE]
> Den här handboken riktar sig till Azure Storage-klientbibliotek för C++ version 2.x som är tillgängliga via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

Storage-klientbiblioteket tillhandahåller en mängd olika metoder för att lista eller fråga objekt i Azure Storage. Den här artikeln tar följande scenarier:

* Lista behållare i ett konto
* Lista över blobbar i en behållare eller virtuella blob-katalog
* Lista över köer på ett konto
* Lista över tabeller i ett konto
* Fråga entiteter i en tabell

Var och en av dessa metoder visas med olika överlagringar för olika scenarier.

## <a name="asynchronous-versus-synchronous"></a>Asynkron och synkron
Eftersom Storage-klientbibliotek för C++ är byggt ovanpå det [C++ REST biblioteket](https://github.com/Microsoft/cpprestsdk), vi stöds asynkrona åtgärder med hjälp av [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Exempel:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synkrona åtgärder omsluta de motsvarande asynkrona åtgärderna:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Om du arbetar med flera trådade program eller tjänster rekommenderar vi att du använder async-API: er direkt i stället för att skapa en tråd för att anropa synkronisering API: er, vilket kan påverka prestandan avsevärt.

## <a name="segmented-listing"></a>Segmenterade lista
Skalan för molnlagring kräver segmenterade lista. Exempelvis kan du ha över en miljon blobbar i en Azure blob-behållaren eller över en miljard entiteter i en tabell i Azure. Detta är inte teoretisk siffror, men verkliga kundens användning fall.

Det är därför opraktiska att lista alla objekt i ett enda svar. I stället kan du visa objekt med hjälp av sidindelning. Varje lista API: er har en *segmenterat* överlagra.

Svaret för segmenterade lista innehåller:

* <i>_segment</i>, som innehåller de resultat som returnerats för ett enda anrop till API: et lista.
* *continuation_token*, som skickas till nästa anrop för att få nästa sida i resultaten. När det finns inga fler resultat ska returneras, är fortsättningstoken null.

Ett typiskt anrop för att lista alla blobbar i en behållare kan till exempel se ut som följande kodavsnitt. Koden är tillgängliga i vår [exempel](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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
        process_diretory(it->as_directory());
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

Om du inte anger den *max_results* parametern, som är standard maximivärdet för upp till 5 000 resultat returneras i en enda sida.

Observera också att en fråga mot Azure Table storage kan returnera några poster eller färre poster än värdet för den *max_results* parameter som du angett, även om fortsättningstoken inte är tom. En orsak kan vara att frågan inte kunde slutföras i fem sekunder. Så länge fortsättningstoken inte är tom frågan ska fortsätta och din kod anta inte att storleken på segment resultat.

Det rekommenderade kodning mönstret för de flesta fall är segmenterat lista, som innehåller explicita fortskrider lista eller fråga och hur tjänsten svarar på varje begäran. Särskilt för C++-program eller tjänster kan på lägre nivå kontroll över lista förloppet hjälpa kontrollen minne och prestanda.

## <a name="greedy-listing"></a>Girig lista
Tidigare versioner av Storage-klientbibliotek för C++ (versioner 0.5.0 Förhandsgranska och tidigare) med icke-segmenterade listan API: er för tabeller och köer, som i följande exempel:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Dessa metoder har implementerats som omslutningar av segmenterade API: er. För varje svar för segmenterade lista koden läggs resultaten till en vector och returneras alla resultat när fullständig behållarna har genomsökts.

Den här metoden kan fungera när lagringskontot eller tabell som innehåller ett litet antal objekt. Men med en ökning av antalet objekt kan det minne som krävs öka utan begränsning, eftersom alla resultat kvar i minnet. En åtgärd kan ta mycket lång tid under vilken anroparen har ingen information om förloppet.

Dessa girig lista API: er i SDK finns inte i C#, Java och JavaScript Node.js-miljö. Om du vill undvika potentiella problem med att använda API: erna girig vi bort dem i version 0.6.0 förhandsgranskning.

Om koden anropar dessa girig API: er:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Du bör ändra koden för att använda segmenterade lista API: er:

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

Genom att ange den *max_results* parametern för segmentet du balanserar mellan antal begäranden och minnesanvändning att uppfylla prestandaöverväganden för ditt program.

Dessutom om du använder segmenterade lista API: er och lagra data i en lokal samling i en ”girig” style också rekommenderar vi starkt att du refactor din kod för att hantera lagring av data i en lokal samling noggrant i större skala.

## <a name="lazy-listing"></a>Enkel lista
Girig lista aktiveras potentiella problem, är men praktiskt om det inte är för många objekt i behållaren.

Om du använder också C# eller Oracle Java SDK: er, bör du känna till Enumerable programmeringsmiljö, vilket ger en lazy--format som lista, där data vid en viss förskjutning hämtas endast om det behövs. Iterator-baserade mallen innehåller också ett liknande sätt i C++.

En typisk lazy lista API, med hjälp av **list_blobs** exempel ser ut så här:

```cpp
list_blob_item_iterator list_blobs() const;
```

En typisk kodstycke som använder lazy lista mönster kan se ut så här:

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

Observera att lazy lista endast är tillgängliga i synkront läge.

Jämfört med girig listan hämtar lazy lista data bara när det behövs. Under försättsbladen hämtar den data från Azure Storage endast när nästa iterator flyttar till nästa segment. Därför minnesanvändning kontrolleras med en begränsad storlek och åtgärden går snabbt.

Lazy lista API: er finns i Storage-klientbibliotek för C++ i version 2.2.0.

## <a name="conclusion"></a>Slutsats
I den här artikeln beskrivs vi olika överlagringar för att lista API: er för olika objekt i Storage-klientbibliotek för C++. Sammanfattningsvis:

* Async APIs rekommenderas under scenarier med flera trådmodell.
* Segmenterade lista rekommenderas för de flesta scenarier.
* Lazy lista anges i biblioteket som en lämplig omslutning i synkron scenarier.
* Girig lista rekommenderas inte och har tagits bort från biblioteket.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och klientbibliotek för C++ finns i följande resurser.

* [Hur du använder Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Använda Queue Storage från C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage-klientbibliotek för C++-API-dokumentationen.](http://azure.github.io/azure-storage-cpp/)
* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)

