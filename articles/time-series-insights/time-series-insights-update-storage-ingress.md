---
title: Data lagring och inträngande i förhands granskning – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data lagring och inträngande i Azure Time Series Insights för hands versionen.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 62ee248c06d2b26b935f72b3bb73cf708f949c72
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014714"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Data lagring och Inträng i Azure Time Series Insights för hands version

I den här artikeln beskrivs uppdateringar av data lagring och ingångs information för Azure Time Series Insights för hands version. Den täcker de underliggande egenskaperna för lagrings struktur, fil format och Time Series ID. Den diskuterar också den underliggande ingångs processen, metod tips och aktuella begränsningar för för hands versionen.

## <a name="data-ingress"></a>Inkommande data

Din Azure Time Series Insightss miljö innehåller en inmatnings motor för att samla in, bearbeta och lagra Time Series-data. När du planerar din miljö finns det några saker att tänka på att ta hänsyn till för att säkerställa att alla inkommande data bearbetas och att uppnå höga ingångs skala och minimera inmatnings fördröjningen (den tid som krävs av TSD för att läsa och bearbeta data från händelsen Källa). I Time Series Insights för hands versionen bestämmer data ingångs principerna var data kan hämtas från och vilket format data ska ha.

### <a name="ingress-policies"></a>Ingress-principer

Time Series Insights för hands version stöder följande händelse källor:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights för hands versionen stöder högst två händelse källor per instans.
  
Azure Time Series Insights stöder JSON som skickas via Azure IoT Hub eller Azure Event Hubs.

> [!WARNING] 
> När du bifogar en ny händelse källa till din Time Series Insights för hands version, beroende på antalet händelser som för närvarande finns i din IoT Hub eller Händelsehubben, kan du få svars tiden för hög inledande inmatning. När data matas in bör du förvänta dig den här långa svars tiden till under sidan, men om du anger något annat kan du kontakta oss genom att skicka in ett support ärende via Azure Portal.

## <a name="ingress-best-practices"></a>Ingress metod tips

Vi rekommenderar att du använder följande bästa praxis:

* Konfigurera Time Series Insights och en IoT-hubb eller Event Hub i samma region. Detta minskar inmatnings fördröjningen som uppstår på grund av nätverket.
* Planera för skalnings behoven genom att beräkna den förväntade inmatnings takten och kontrol lera att den ligger inom den frekvens som stöds nedan
* Lär dig hur du optimerar och formar dina JSON-data, samt de aktuella begränsningar som finns i förhands granskningen, genom [att läsa hur du kan forma JSON för ingress och fråga](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Ingress-skalning och begränsningar i för hands versionen

Som standard har Time Series Insights för hands versionen stöd för en första ingångs skala på upp till 1 megabyte per sekund (MB/s) per miljö. Upp till 16 MB/s-dataflöde är tillgängligt vid behov, kontakta oss genom att skicka in ett support ärende i Azure Portal om det behövs. Det finns dessutom en gräns per partition på 0,5 MB/s. Detta har konsekvenser för kunder som använder IoT Hub specifikt, baserat på tillhörigheten mellan en IoT Hub enhets partition. I scenarier där en gateway-enhet vidarebefordrar meddelanden till hubben med hjälp av egna enhets-ID och anslutnings sträng, finns det risk för att nå 0,5 MB/s-gränsen för att meddelanden kommer att tas emot i en enda partition, även om händelse nytto lasten anger olika TS Kompatibilitet. I allmänhet visas ingångs pris som en faktor för antalet enheter i din organisation, frekvens för händelse utsläpp och storleken på en händelse. När du beräknar inmatnings takten bör IoT Hub användare använda antalet nav-anslutningar som används, snarare än totalt antal enheter i organisationen. Stöd för förbättrad skalning pågår. Den här dokumentationen kommer att uppdateras för att avspegla dessa förbättringar. 

> [!WARNING]
> För miljöer som använder IoT Hub som händelse källa beräknar du inmatnings takten med antalet NAV enheter som används.

Se följande länkar om du vill ha mer information om data flödes enheter och partitioner:

* [IoT Hub skala](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub-skala](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub-partitioner](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights för hands version av SKU-miljö för förhands granskning skapar du två Azure-resurser:

* En Time Series Insights för hands versions miljö som kan även innehålla funktioner för varm lagring.
* Ett Azure Storage generella v1 BLOB-konto för kall data lagring.

Data i det varmt arkivet är bara tillgängliga via [Time Series-frågan](./time-series-insights-update-tsq.md) och [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md). 

Time Series Insights för hands version sparar dina kall data till Azure Blob Storage i [fil formatet Parquet](#parquet-file-format-and-folder-structure). Time Series Insights för hands versionen hanterar den här kall lagrings informationen exklusivt, men den är tillgänglig så att du kan läsa direkt som standard Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Time Series Insights för hands versions skrivningar, eftersom det kan orsaka data förlust.

### <a name="data-availability"></a>Data tillgänglighet

Time Series Insights förhandsgranska partitioner och indexera data för optimala frågor. Data blir tillgängliga för frågor efter att de indexerats. Mängden data som matas in kan påverka denna tillgänglighet.

> [!IMPORTANT]
> Den kommande allmänna tillgänglighets versionen av Time Series Insights kommer att göra data tillgängliga i 60 sekunder efter att den lästs från händelse källan. Under förhands granskningen kan det uppstå en längre period innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för för hands versionen av Azure Time Series Insights.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Time Series Insights för hands versions miljö för förhands granskning skapas ett Azure Storage allmänt v1-BLOB-konto som ditt långsiktiga kall arkiv.  

Time Series Insights för hands version publicerar upp till två kopior av varje händelse i ditt Azure Storage-konto. Den ursprungliga kopian har händelser sorterade efter Inhämtnings tid och bevaras alltid, så du kan använda andra tjänster för att få åtkomst till den. Du kan använda Spark, Hadoop och andra välbekanta verktyg för att bearbeta RAW Parquet-filerna. 

Time Series Insights för hands version partitionerar om Parquet-filerna för att optimera för Time Series Insightss frågan. Den här ompartitionerade kopian av data sparas också.

Under den offentliga för hands versionen lagras data oändligt i ditt Azure Storage-konto.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Time Series Insights för hands versionen skapar.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Komma åt och exportera data från Time Series Insights för hands version

Du kanske vill komma åt data som visas i Time Series Insights Preview Explorer som används tillsammans med andra tjänster. Du kan till exempel använda dina data för att skapa en rapport i Power BI eller för att träna en maskin inlärnings modell med hjälp av Azure Machine Learning Studio. Eller så kan du använda dina data för att transformera, visualisera och modellera i dina Jupyter-anteckningsböcker.

Du kan komma åt dina data på tre sätt:

* I Time Series Insights Preview Explorer. Du kan exportera data som en CSV-fil från Utforskaren. Mer information finns i [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* Från Time Series Insights för hands versions-API. Du kan komma åt API-slutpunkten på `/getRecorded`. Mer information om det här API: et finns i [tids serie frågor](./time-series-insights-update-tsq.md).
* Direkt från ett Azure Storage-konto. Du behöver Läs behörighet till det konto som du använder för att komma åt dina Time Series Insights för hands versions data. Mer information finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Borttagning av data

Ta inte bort Time Series Insights för hands versions filerna. Du bör endast hantera relaterade data i Time Series Insights för hands version.

## <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som har utformats för effektiv lagring och prestanda. Time Series Insights för hands version använder Parquet av dessa orsaker. Den partitionerar data efter tids serie-ID för frågans prestanda i skala.  

Mer information om fil typen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Time Series Insights för hands version lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Informationen finns i mappen `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra partitionerade kopian partitioneras av en gruppering av Time Series-ID: n och finns i mappen `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

I båda fallen motsvarar tids värdena skapande tid för BLOB. Data i `PT=Time`-mappen bevaras. Data i `PT=TsId` mapp kommer att optimeras för frågor över tid och kommer inte att vara statiska.

> [!NOTE]
> * `<YYYY>` mappar till en 4-siffrig års representation.
> * `<MM>` mappar till en månads representation med två siffror.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tids stämplings representation med fyrsiffrigt år (`YYYY`), tvåsiffrig månad (`MM`), tvåsiffrig dag (`DD`), tvåsiffrig timme (`HH`), tvåsiffrig minut (`MM`), tvåsiffrig sekund (`SS`) och tresiffrig MS-siffrigt millisekunder (`fff`).

Time Series Insights för hands versions händelser mappas till fil innehållet i Parquet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen Time-stämpel inte anges i händelse källan. Tidstämpeln är alltid i UTC.
* Varje rad innehåller kolumnen Time Series ID som definieras när Time Series Insightss miljön skapas. Egenskaps namnet innehåller `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_string` (sträng), `_bool` (Boolean), `_datetime` (datetime) eller `_double` (Double), beroende på egenskaps typen.
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1**. När den här funktionen utvecklas kan namnet ökas.

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights för hands versions lagring och inkommande](./time-series-insights-update-storage-ingress.md)trafik.

- Läs om den nya [data modellen](./time-series-insights-update-tsm.md).
