---
title: Azure Time Series Insights Preview data – lagring av Data- och ingångsanspråk i förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Förstå datalagring och ingress i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 9504e62ea99c835f43f0d86ec2cfa57a9afcb4e4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269999"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Lagring av data- och ingångsanspråk i förhandsversionen av Azure Time Series Insights

Den här artikeln redogörs för ändringar för lagring av data- och ingångsanspråk från förhandsversionen av Azure Time Series Insights. Den behandlar den underliggande lagringsstrukturen, filformat och Time Series-ID-egenskap. Artikeln beskriver också den underliggande ingress-processen, dataflöde och begränsningar.

## <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights Preview-användningsbaserad SKU-miljö, skapar du två resurser:

* En Time Series Insights-miljö.
* Ett Azure Storage allmänna V1-konto där data kommer att lagras.

Förhandsversionen av Time Series Insights använder Azure Blob storage med Parquet-filtypen. Time Series Insights hanterar alla dataåtgärder inklusive att skapa blobbar, indexering och partitionera data i Azure storage-kontot. Du kan skapa dessa blobar med hjälp av ett Azure storage-konto.

Som andra Azure Storage-blobbar kan Time Series Insights-skapade blobbar du läsa och skriva till dem för att stödja olika integrationsscenarier.

> [!TIP]
> Time Series Insights prestanda kan påverkas negativt om du vill läsa eller skriva till dina blobar för ofta.

En översikt över Azure Blob storage finns i [Storage-blobbar introduktion](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Mer information om filtypen Parquet finns i [filtyper som stöds i Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Parquet-filformat

Parquet är ett kolumnorienterade, datafilformat som utformats för:

* Samverkan
* Utrymmeseffektivitet
* Frågans effektiviteten

Time Series Insights valde Parquet eftersom det ger effektiv datakomprimering och kodning scheman med förbättrad prestanda som kan hantera komplexa data gruppvis.

En bättre förståelse för Parquet-filformat, finns i [Parquet dokumentation](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Händelse-strukturen i Parquet

Time Series Insights skapar och lagrar kopior av blobar i följande format:

1. Den första, inledande kopian har partitionerats med ankomsttid:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * BLOB Skapandetid för blobbar som partitioneras efter ankomsttid.

1. Den andra, partitionera om fragmentet kopian har partitionerats med en dynamisk grupp med Time Series-ID:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Minsta händelsetidsstämpel i en blob för blobar som har partitionerats med Time Series-ID.

> [!NOTE]
> * `<YYYY>` mappas till en 4-siffrig år-representation.
> * `<MM>` mappas till en 2-siffrig månad-representation.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tidsstämpel representation med 4 årtal (`YYYY`), 2-siffrig månad (`MM`), 2-siffrig dag (`DD`), 2-siffrig timme (`HH`), 2-siffrig minut (`MM`), 2-siffrig sekund (`SS`), och 3-siffriga millisekund (`fff`).

Time Series Insights-händelser är mappade till Parquet-filens innehåll på följande sätt:

* Varje händelse som mappar till en enda rad.
* Inbyggda **tidsstämpel** kolumn med en tidsstämpel för händelsen. Tidsstämpelsegenskapen är aldrig null. Standard **händelse källa Kötid** om tidsstämpel-egenskapen har inte angetts i datakällan. Tidsstämpeln är i UTC. 
* Alla andra egenskaper som är mappade till kolumnerna sluta med `_string` (sträng), `_bool` (boolesk), `_datetime` (datetime), och `_double` (dubbla), beroende på typ vlastnosti.
* Mappning av schemat för den första versionen av filformatet, som vi kallar är **V = 1**. Eftersom den här funktionen utvecklas, namnet på ökas till **V = 2**, **V = 3**och så vidare.

## <a name="partitions"></a>Partitioner

Varje förhandsversionen av Time Series Insights-miljö måste ha en Time Series-ID-egenskap och en tidsstämpel-egenskap som unikt identifierar den. Din Time Series-ID fungerar som en logisk partition för dina data och ger en naturlig gräns för förhandsversionen av Time Series Insights-miljö för att distribuera data över fysiska partitioner. Fysisk partition management hanteras av förhandsversionen av Time Series Insights i ett Azure storage-konto.

Time Series Insights använder dynamisk partitionering för att optimera lagring och frågeprestanda genom att släppa och återskapa partitioner. Time Series Insights Preview dynamisk algoritm för partitionering försöker att förhindra att en enda fysisk partition att data för flera olika, logiska partitioner. Med andra ord ser partitionering algoritmen till att alla data specifika till en enda gång-ID exklusivt finns i Parquet-filer utan att överlagrad med andra Time Series-ID: N. Algoritmen för dynamisk partitionering också försöker bevara den ursprungliga ordningen för händelser inom ett enda Time Series-ID.

Inledningsvis är inkommande när har data partitionerats med tidsstämpel så att en enda, logisk partition inom ett visst tidsintervall som går att sprida över flera fysiska partitioner. En enda fysisk partition kan också innehålla många eller alla logiska partitioner. På grund av storleksbegränsningar för blob, även med optimala partitionering, kan en enda logisk partition uppta flera fysiska partitioner.

> [!NOTE]
> Som standard är tidsstämpelvärdet meddelandet *Kötid* i din konfigurerade händelsekälla. 

Om du laddar upp historiska data eller batchbearbetning av meddelanden, tilldela värdet du vill lagra med dina data i tidsstämpel-egenskapen som mappar till lämplig tidsstämpel. Tidsstämpel-egenskapen är skiftlägeskänsliga. Mer information finns i [Tidsseriemodell](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Fysiska partitioner

En fysisk partition är en blockblob som lagras i ditt storage-konto. Den verkliga storleken på blobarna som kan variera eftersom storleken beror på push-priset. Men vi förväntar oss blobbarna är cirka 20 MB till 50 MB i storlek. Den här förväntan ledde Time Series Insights-teamet för att välja 20 MB som storleken att optimera frågeprestanda. Den här storleken kan ändras med tiden, beroende på filstorleken och hastighet för inkommande data.

> [!NOTE]
> * Blobbar storlek på 20 MB.
> * Azure-blobar är ibland partitionera om fragmentet för bättre prestanda genom att släppas och återskapas.
> * Samma Time Series Insights-data kan dessutom finnas i två eller flera blobbar.

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition är en partition inom en fysisk partition som lagrar alla data som är associerade med ett enskilt partitionsnyckelvärde. Time Series Insights Preview partitionerar logiskt varje blob baserat på två egenskaper:

* **Time Series-ID**: Partitionsnyckeln för alla Time Series Insights-data i händelseströmmen och modellen.
* **Tidsstämpel**: Den tid som baseras på den första inkommande.

Förhandsversionen av Time Series Insights tillhandahåller högpresterande frågor som baseras på dessa två egenskaper. Dessa två egenskaper ger även den effektivaste metoden för att leverera Time Series Insights-data snabbt.

Det är viktigt att välja en lämplig tid serien ID, eftersom det är en egenskap som inte kan ändras. Mer information finns i [Välj Time Series-ID: N](./time-series-insights-update-how-to-id.md).

## <a name="your-azure-storage-account"></a>Azure storage-kontot

### <a name="storage"></a>Storage

När du skapar en användningsbaserad Time Series Insights-miljö kan du skapa två resurser: en Time Series Insights-miljö och ett Azure Storage allmänna V1-konto där data kommer att lagras. Vi valde att göra Azure Storage general-purpose V1 standardresurs på grund av dess samverkan, pris och prestanda. 

Time Series Insights publicerar upp till två kopior av varje händelse i Azure storage-kontot. Den ursprungliga kopian bevaras alltid så att du kan fråga den performantly genom att använda andra tjänster. Du kan enkelt använda Spark, Hadoop och andra välbekanta verktyg över Time Series-ID: N via raw Parquet-filer, eftersom dessa motorer stöder grundläggande filtrering av filnamn. Gruppera blobar per år och månad är ett bra sätt att lista blobbar i en viss tidsperiod för ett anpassat jobb. 

Time Series Insights repartitions dessutom Parquet-filer för att optimera för API: er för Time Series Insights. Nyligen repartitioned filen sparas också.

Data lagras på obestämd tid i Azure storage-kontot under den offentliga Förhandsperioden.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights-blobar

För att säkerställa prestanda och tillgänglighet för data, inte redigera eller ta bort alla blobbar som skapas av Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Få åtkomst till och export av data från förhandsversionen av Time Series Insights

Du kanske vill komma åt data som lagras i förhandsversionen av Time Series Insights explorer ska användas tillsammans med andra tjänster. Exempelvis kan du använda dina data till rapporten i Power BI, använda machine learning med hjälp av Azure Machine Learning Studio eller för att använda i en notebook-program med Jupyter-anteckningsböcker.

Du kan komma åt dina data på tre Allmänt sätt:

* Från förhandsversionen av Time Series Insights explorer.
* Från API: er Time Series Insights Preview.
* Direkt från ett Azure storage-konto.

#### <a name="from-the-time-series-insights-preview-explorer"></a>Från förhandsversionen av Time Series Insights explorer

Du kan exportera data som en CSV-fil från förhandsversionen av Time Series Insights explorer. Mer information finns i [förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md).

#### <a name="from-the-time-series-insights-preview-apis"></a>Från API: er Time Series Insights Preview

API-slutpunkten kan nås på `/getRecorded`. Läs mer om detta API i [Time Series fråga](./time-series-insights-update-tsq.md).

#### <a name="from-an-azure-storage-account"></a>Från ett Azure storage-konto

* Du behöver läsåtkomst till det konto som du använder för att komma åt dina Time Series Insights-data. Mer information finns i [hantera åtkomst till din lagringskontoresurserna](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

* Mer information om direkta metoderna för att läsa data från Azure Blob storage finns i [flytta data till och från ditt lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Att exportera data från ett Azure storage-konto:

    * Kontrollera att ditt konto uppfyller de nödvändiga kraven för export av data. Mer information finns i [Storage importera och exportera krav](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

    * Mer information om andra sätt att exportera data från ditt Azure storage-konto, se [Import och export av data från BLOB-objekt](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Data raderas

Ta inte bort blobbar, eftersom Time Series Insights Preview underhåller metadata om blobbar i den.

## <a name="ingress"></a>Ingress

### <a name="time-series-insights-ingress-policies"></a>Time Series Insights inkommande principer

Time Series Insights Preview stöder samma händelsekällor och filtyper som för närvarande stöder Time Series Insights.

Händelsekällor som stöds är:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Azure Event Hub-instanser stöder Kafka.

Filtyper som stöds är:

* JSON: Mer information om de stöds JSON-former som vi kan hantera finns i [så form JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Tillgänglighet för data

Time Series Insights Preview indexerar data med hjälp av en strategi för optimering av blob-storlek. Data blir tillgängliga för frågor när den har indexerats, som baseras på hur mycket data kommer i och i vilken fart.

> [!IMPORTANT]
> * Time Series Insights allmänt tillgänglig (GA) version gör data tillgängliga inom 60 sekunder når en händelsekälla. 
> * Förvänta dig en längre period innan data görs tillgängliga i förhandsversionen. 
> * Om du får några betydande svarstid, måste du kontakta oss.

### <a name="scale"></a>Skala

Time Series Insights Preview stöder en inledande ingress-skala för upp till 6 megabit per sekund (Mbit/s) per miljö. Förbättrat stöd för skalning pågår. Vi planerar att uppdatera vår dokumentation för att återspegla dessa förbättringar.

## <a name="next-steps"></a>Nästa steg

Läs den [Azure Time Series Insights Förhandsgranska Storage och ingående](./time-series-insights-update-storage-ingress.md).

Läs mer om den nya [datamodellering](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
