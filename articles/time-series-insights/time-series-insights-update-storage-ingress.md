---
title: För hands version av data lagring och inträngande i Azure Time Series Insights | Microsoft Docs
description: Att förstå data lagring och inträngande i Azure Time Series Insights för hands versionen.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 9af53728ee038a6511c434aeedfdb9afdab6d04b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273886"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Data lagring och Inträng i Azure Time Series Insights för hands version

I den här artikeln beskrivs ändringar av data lagring och inträngande från Azure Time Series Insights för hands version. Den täcker de underliggande egenskaperna för lagrings struktur, fil format och Time Series ID. I artikeln diskuteras även underliggande process, data flöde och begränsningar.

## <a name="data-ingress"></a>Inkommande data

Azure Time Series Insights data ingress-principer bestämmer var data kan hämtas från och i vilket format.

[Översikt över modell för @no__t 1Time-serien](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Ingress-principer

Time Series Insights för hands versionen stöder samma händelse källor och filtyper som Time Series Insights för närvarande stöder:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights stöder JSON som skickas via Azure IoT Hub eller Azure Event Hubs. Om du vill optimera dina IoT JSON-data, lär du dig [hur du formar JSON](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights för hands version av SKU-miljö för förhands granskning skapar du två resurser:

* En Time Series Insightss miljö.
* Ett Azure Storage allmänt v1-konto där data ska lagras.

I förhands granskningen av Time Series Insights används Azure Blob Storage med fil typen Parquet. Time Series Insights hanterar alla data åtgärder, inklusive att skapa blobbar, indexera och partitionera data i Azure Storage-kontot. Du skapar dessa blobbar med hjälp av ett Azure Storage-konto.

Precis som andra Azure Storage blobbar kan Time Series Insights-skapade blobbar läsa och skriva till dem för att stödja olika integrations scenarier.

### <a name="data-availability"></a>Data tillgänglighet

Time Series Insights för hands versionen indexerar data med hjälp av en optimerings strategi för BLOB-storlek. Data blir tillgängliga för frågor efter att de indexerats, baserat på hur mycket data som kommer i och i vilken hastighet.

> [!IMPORTANT]
> * Time Series Insights General Availability-versionen (GA) kommer att göra data tillgängliga inom 60 sekunder efter att ha kommit till en händelse källa.
> * Under för hands versionen förväntas en längre period innan data görs tillgängliga.
> * Kontakta oss om du får en stor fördröjning.

### <a name="scale"></a>Skala

Time Series Insights för hands versionen stöder en inledande ingångs skala på upp till 1 megabyte byte per sekund (Mbit/s) per miljö. Stöd för förbättrad skalning pågår. Vi planerar att uppdatera vår dokumentation för att avspegla dessa förbättringar.

## <a name="parquet-file-format"></a>Parquet-filformat

Parquet är ett kolumn-orienterat data fil format som har utformats för:

* Samverkan
* Utrymmes effektivitet
* Frågans effektivitet

Time Series Insights valde Parquet eftersom det tillhandahåller effektiva data komprimerings-och kodnings scheman med förbättrade prestanda som kan hantera komplexa data i bulk.

Mer information om filtypen Parquet finns i Parquet- [dokumentationen](https://parquet.apache.org/documentation/latest/).

Mer information om fil formatet Parquet i Azure finns i [filtyper som stöds i Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Händelse struktur i Parquet

Time Series Insights skapar och lagrar kopior av blobbar i följande två format:

1. Den första kopian partitioneras efter ankomst tid:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * BLOB-skapande tid för blobbar partitionerade efter ankomst tid.

1. Den andra partitionerade kopian partitioneras av en dynamisk gruppering av Time Series-ID:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Minsta tids stämpling för händelse i en BLOB för BLOB-partitionerade med Time Series ID.

> [!NOTE]
> * `<YYYY>` mappar till en 4-siffrig års representation.
> * `<MM>` mappar till en 2-siffrig månads representation.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tidstämpel med fyrsiffrigt årtal (`YYYY`). tvåsiffrig månad (`MM`), 2-siffrig dag (`DD`), 2-siffrig timme (`HH`), 2-siffrig minut (`MM`), 2-siffrig sekund (`SS`) och 3-siffrig MS (`fff`).

Time Series Insights händelser mappas till Parquet fil innehåll på följande sätt:

* Varje händelse mappas till en enda rad.
* Inbyggd **timestamp** -kolumn med en händelse-tidsstämpel. Egenskapen timestamp är aldrig null. Den är som standard **händelse källans köade tid** om egenskapen timestamp inte anges i händelse källan. Tidsstämpeln är UTC. 
* Alla andra egenskaper som är mappade till kolumner slutar med `_string` (sträng), `_bool` (Boolean), `_datetime` (datetime) och `_double` (Double), beroende på egenskaps typ.
* Det är mappnings schema för den första versionen av fil formatet, som vi refererar till som **V = 1**. När den här funktionen utvecklas ökar namnet till **v = 2**, **V = 3**och så vidare.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för Azure Time Series Insights.

En grundlig beskrivning av Azure Blob Storage-tjänsten finns i [introduktionen till Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Time Series Insights "betala per användning"-miljö skapar du två resurser: en Time Series Insights miljö och ett Azure Storage generellt syfte v1-konto där data ska lagras. Vi valde att göra Azure Storage generell användning v1 som standard resurs på grund av dess driftskompatibilitet, pris och prestanda.

Time Series Insights publicerar upp till två kopior av varje händelse i ditt Azure Storage-konto. Den ursprungliga kopian bevaras alltid så att du snabbt kan fråga den med hjälp av andra tjänster. Du kan enkelt använda Spark, Hadoop och andra välkända verktyg över tids serie-ID: n över RAW Parquet-filer, eftersom dessa motorer stöder grundläggande fil namns filtrering. Gruppering av blobbar per år och månad är ett användbart sätt att lista blobbar inom ett särskilt tidsintervall för ett anpassat jobb.

Dessutom, Time Series Insights partitionerar om Parquet-filerna för att optimera för Time Series Insights-API: er. Den senast ompartitionerade filen sparas också.

Under den offentliga för hands versionen lagras data oändligt i ditt Azure Storage-konto.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som skapats av Time Series Insights.

> [!TIP]
> Time Series Insights prestanda kan påverkas negativt om du läser eller skriver till blobarna för ofta.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Komma åt och exportera data från Time Series Insights för hands version

Du kanske vill komma åt data som lagras i Time Series Insights Preview Explorer och använda dem tillsammans med andra tjänster. Du kanske till exempel vill använda dina data för att rapportera i Power BI, för att utföra maskin inlärning med Azure Machine Learning Studio eller om du vill använda i ett Notebook-program med Jupyter Notebooks.

Du kan komma åt dina data på tre sätt:

* I Time Series Insights Preview Explorer: du kan exportera data som en CSV-fil från Time Series Insights Preview Explorer. Mer information finns i [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* Från Time Series Insights Preview-API: er: API-slutpunkten kan nås vid `/getRecorded`. Mer information om det här API: et finns i [tids serie frågor](./time-series-insights-update-tsq.md).
* Direkt från ett Azure Storage-konto (nedan).

#### <a name="from-an-azure-storage-account"></a>Från ett Azure Storage-konto

* Du behöver Läs behörighet till det konto som du använder för att komma åt dina Time Series Insights data. Mer information finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).
* Mer information om direkta sätt att läsa data från Azure Blob Storage finns i [Välj en Azure-lösning för data överföring](../storage/common/storage-choose-data-transfer-solution.md).
* Så här exporterar du data från ett Azure Storage-konto:
    * Kontrol lera först att ditt konto uppfyller de nödvändiga kraven för export av data. Mer information finns i [krav för lagrings import och export](../storage/common/storage-import-export-requirements.md).
    * Information om andra sätt att exportera data från ditt Azure Storage-konto finns i [Importera och exportera data från blobbar](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Borttagning av data

Ta inte bort blobbar. Den Time Series Insights för hands versionen är inte bara användbar för att granska och underhålla en post med dina data, men för hands versionen av underhåll BLOB-metadata i varje blob.

## <a name="partitions"></a>Partitioner

Varje Time Series Insights för hands versions miljö måste ha en **Time Series ID-** egenskap och en **timestamp** -egenskap som unikt identifierar den. Ditt Time Series ID fungerar som en logisk partition för dina data och ger Time Series Insights för hands versions miljön en naturlig gräns för att distribuera data över fysiska partitioner. Fysiska partitioner hanteras av Time Series Insights för hands version i ett Azure Storage konto.

Time Series Insights använder dynamisk partitionering för att optimera lagring och fråga prestanda genom att släppa och återskapa partitioner. Algoritmen Time Series Insights för för hands versions dynamisk partitionering försöker förhindra att en enskild fysisk partition har data för flera, distinkta, logiska partitioner. Med andra ord behåller partitionerings algoritmen alla data som är specifikt för ett enda Time Series-ID i Parquet-filer utan att de överlämnas med andra Time Series-ID: n. Algoritmen för dynamisk partitionering försöker också bevara den ursprungliga ordningen av händelser inom ett enda tids serie-ID.

Inlednings vis, vid ingångs tid, partitioneras data av tidsstämpeln så att en enda logisk partition inom ett visst tidsintervall kan spridas över flera fysiska partitioner. En enda fysisk partition kan också innehålla många eller alla logiska partitioner. På grund av blobb storleks begränsningar, även med optimal partitionering, kan en enda logisk partition uppta flera fysiska partitioner.

> [!NOTE]
> Som standard är Tidsstämpelns värde meddelandet i *kö* i den konfigurerade händelse källan.

Om du överför historiska data eller batch-meddelanden tilldelar du det värde som du vill lagra med dina data till egenskapen timestamp som mappar till lämplig tidsstämpel. Egenskapen timestamp är Skift läges känslig. Mer information finns i [tids serie modell](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Fysiska partitioner

En fysisk partition är en Block-Blob som lagras i ditt lagrings konto. Den faktiska storleken på Blobbarna kan variera eftersom storleken beror på push-hastigheten. Men vi förväntar sig att blobbar ska vara cirka 20 MB till 50 MB. Den här förväntan ledde till att Time Series Insights-teamet väljer 20 MB som storlek för att optimera frågornas prestanda. Den här storleken kan ändras över tid, beroende på fil storlek och data ingångs hastigheten.

> [!NOTE]
> * Blobbarna får storleken 20 MB.
> * Azure-blobbar partitioneras ibland om för bättre prestanda genom att de släpps och skapas igen.
> * Samma Time Series Insightss data kan också finnas i två eller flera blobbar.

### <a name="logical-partitions"></a>Logiska partitioner

En logisk partition är en partition i en fysisk partition som lagrar alla data som är associerade med ett enda nyckel värde för en partition. Time Series Insights för hands version logiskt partitionerar varje BLOB baserat på två egenskaper:

* **Time Series-ID**: partitionsnyckel för alla Time Series Insights data i händelse strömmen och modellen.
* **Tidsstämpel**: tid baserat på inledande ingångs datum.

I förhands granskningen Time Series Insights finns de frågor som baseras på dessa två egenskaper. Dessa två egenskaper ger också den mest effektiva metoden för att leverera Time Series Insights data snabbt.

Det är viktigt att välja ett lämpligt tids serie-ID eftersom det är en oföränderlig egenskap. Mer information finns i [Choose Time Series ID](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights för hands versions lagring och inkommande](./time-series-insights-update-storage-ingress.md)trafik.

- Läs om den nya [data modellen](./time-series-insights-update-tsm.md).
