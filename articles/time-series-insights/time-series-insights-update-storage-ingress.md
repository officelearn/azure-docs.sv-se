---
title: Data lagring och inträngande i förhands granskning – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data lagring och inträngande i Azure Time Series Insights för hands versionen.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 44c942e43cd4be1d04f56e828e3e17c58713a706
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559852"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Data lagring och Inträng i Azure Time Series Insights för hands version

I den här artikeln beskrivs uppdateringar av data lagring och ingångs information för Azure Time Series Insights för hands version. I den här artikeln beskrivs de underliggande egenskaperna för lagrings struktur, fil format och Time Series ID. De underliggande ingångs processerna, metod tips och aktuella för hands versions begränsningar beskrivs också.

## <a name="data-ingress"></a>Inkommande data

Din Azure Time Series Insightss miljö innehåller en inmatnings *motor* för att samla in, bearbeta och lagra Time Series-data. 

Det finns vissa saker att vara mindful av för att säkerställa att alla inkommande data bearbetas, för att uppnå höga ingångs skala och minimera inmatnings *fördröjningen* (den tid det tar för Time Series Insights att läsa och bearbeta data från händelse källan) när [du planerar din miljö](time-series-insights-update-plan.md).

Time Series Insights förhandsgranska data ingångs principer avgör var data kan hämtas från och vilket format data ska ha.

### <a name="ingress-policies"></a>Ingress-principer

*Data* ingångar omfattar hur data skickas till en Azure Time Series Insights för hands versions miljö. 

Nyckel konfiguration, formatering och metod tips sammanfattas nedan.

#### <a name="event-sources"></a>Händelse källor

Azure Time Series Insights för hands version stöder följande händelse källor:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights för hands versionen stöder högst två händelse källor per instans.

> [!IMPORTANT] 
> * Du kan få hög första svars tid när du kopplar en händelse källa till för hands versions miljön. 
> Svars tiden för händelse källan beror på antalet händelser som för närvarande finns i IoT Hub eller Händelsehubben.
> * Hög latens kommer att undertryckas när händelsens källdata först matas in. Skicka in ett support ärende via Azure Portal om du får en kontinuerlig hög latens.

#### <a name="supported-data-format-and-types"></a>Data format och typer som stöds

Azure Time Series Insights stöder UTF-8-kodad JSON som skickas från Azure IoT Hub eller Azure Event Hubs. 

De data typer som stöds är:

| Datatyp | Beskrivning |
|---|---|
| **booleska** | En datatyp med ett av två tillstånd: `true` eller `false`. |
| **dateTime** | Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Uttryckt i [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. |
| **Dubbelklicka** | En dubbel precision 64-bitars [IEEE 754](https://ieeexplore.ieee.org/document/8766229) -svävande punkt. |
| **nollängd** | Text värden, bestående av Unicode-tecken.          |

#### <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelse nytto Last, men dina data kommer att genomgå en förenklings process när de lagras. 

Detaljerad information som beskriver hur du kan forma dina JSON-händelser, skicka komplex typ och utjämningen av kapslat objekt finns i [så här formar du JSON för](./time-series-insights-update-how-to-shape-events.md) ingångs-och fråge frågor för att hjälpa till med planering och optimering.

### <a name="ingress-best-practices"></a>Ingress metod tips

Vi rekommenderar att du använder följande bästa praxis:

* Konfigurera Azure Time Series Insights och IoT Hub eller Händelsehubben i samma region för att minska potentiell latens.

* [Planera för skalnings behoven](time-series-insights-update-plan.md) genom att beräkna den förväntade inmatnings takten och kontrol lera att den ligger inom den frekvens som stöds nedan.

* Lär dig hur du optimerar och formar dina JSON-data, samt de aktuella begränsningar som finns i förhands granskningen, genom [att läsa hur du kan forma JSON för ingress och fråga](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Ingress-skalning och för hands versions begränsningar 

Azure Time Series Insights för hands versions begränsningar beskrivs nedan.

> [!TIP]
> Läs [Planera din för hands versions miljö](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) för en omfattande lista över alla begränsningar för för hands versionen.

#### <a name="per-environment-limitations"></a>Begränsningar per miljö

I allmänhet visas ingångs priser som faktor för antalet enheter i din organisation, frekvens för händelse utsläpp och storleken på varje händelse:

*  **Antal enheter** × **händelse utsläpps frekvens** × **storlek på varje händelse**.

Som standard kan Time Series Insights för hands version mata in inkommande data med en hastighet på **upp till 1 megabyte per sekund (Mbit/s) per Time Series Insights-miljö**.

> [!TIP] 
> * Miljö stöd för att mata in hastigheter på upp till 16 MBps kan tillhandahållas av begäran.
> * Kontakta oss om du behöver mer data flöde genom att skicka in ett support ärende via Azure Portal.
 
* **Exempel 1:**

    Contoso-leverans har 100 000 enheter som genererar en händelse tre gånger per minut. Storleken på en händelse är 200 byte. De använder en Event Hub med fyra partitioner som Time Series Insights händelse källa.

    * Inmatnings takten för Time Series Insightss miljön blir: **100 000 enheter * 200 byte/event * (3/60 Event/s) = 1 Mbit/s**.
    * Inmatnings hastigheten per partition blir 0,25 Mbit/s.
    * Contosos överförings takt skulle ligga inom begränsningen för för hands versionen.

* **Exempel 2:**

    Contoso flotta Analytics har 60 000 enheter som genererar en händelse varje sekund. De använder en IoT Hub 24 antal partitioner på 4 som Time Series Insights händelse källan. Storleken på en händelse är 200 byte.

    * Miljöns utmatnings frekvens skulle vara: **20 000 enheter * 200 byte/event * 1 händelse/SEK = 4 Mbps**.
    * Hastigheten per partition blir 1 Mbit/s.
    * Contoso flottans analys kan skicka en begäran till Time Series Insights genom Azure Portal för att öka inmatnings takten för deras miljö.

#### <a name="hub-partitions-and-per-partition-limits"></a>NAV partitioner och gränser per partition

När du planerar Time Series Insightss miljön är det viktigt att du funderar på konfigurationen av de händelse källor som du kommer att ansluta till Time Series Insights. Både Azure IoT Hub och Event Hubs använder partitioner för att aktivera horisontell skalning för händelse bearbetning. 

En *partition* är en ordnad sekvens med händelser som lagras i en hubb. Antalet partitioner anges under fasen skapande av hubb och kan inte ändras. 

För Event Hubs partitionering bör du läsa mer om [hur många partitioner behöver jag?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> De flesta IoT-hubbar som används med Azure Time Series Insights behöver bara fyra partitioner.

Oavsett om du skapar en ny hubb för din Time Series Insights-miljö eller använder en befintlig, måste du beräkna din användnings takt per partition för att avgöra om den ligger inom gränserna för för hands versionen. 

Azure Time Series Insights för hands versionen har för närvarande en **gräns för per partition på 0,5 Mbit/s**.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-/regionsspecifika överväganden

När en enhet skapas i IoT Hub tilldelas den permanent till en partition. När du gör det kan IoT Hub garantera händelse ordning (eftersom tilldelningen aldrig ändras).

En fast tilldelning av partitioner påverkar också Time Series Insights instanser som matar in data som skickas från IoT Hub. När meddelanden från flera enheter vidarebefordras till hubben med samma Gateway-enhets-ID kan de komma i samma partition samtidigt som de kan överskrida gränserna för varje partition. 

**Påverkan**:

* Om en enda partition får en varaktig takt med att mata in över för hands versionen, är det möjligt att Time Series Insights inte synkroniserar all telemetri innan IoT Hub data lagrings perioden har överskridits. Därför kan skickade data gå förlorade om inmatnings gränserna överskrids konsekvent.

För att minimera denna omständighet rekommenderar vi följande metod tips:

* Beräkna dina priser per miljö och per partition innan du distribuerar din lösning.
* Se till att dina IoT Hub enheter är belastningsutjämnade i det fall där det är möjligt.

> [!IMPORTANT]
> För miljöer som använder IoT Hub som händelse källa beräknar du inmatnings takten med antalet NAV enheter som används för att se till att priset sjunker under 0,5 Mbit/s per begränsning i partitionen.
> * Även om flera händelser anländer samtidigt, överskrids inte för hands versions gränsen.

  ![IoT Hub partitions diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Se följande resurser för att lära dig mer om hur du optimerar hubben och partitionerna:

* [IoT Hub skala](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub-skala](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub-partitioner](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights för hands version av SKU-miljö ( *betala per* användning) skapar du två Azure-resurser:

* En Azure Time Series Insights för hands versions miljö som kan konfigureras för varm lagring.
* Ett Azure Storage generella v1 BLOB-konto för kall data lagring.

Data i det varmt arkivet är bara tillgängliga via [Time Series-frågan](./time-series-insights-update-tsq.md) och [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md). 

Time Series Insights för hands version sparar dina kall data till Azure Blob Storage i [fil formatet Parquet](#parquet-file-format-and-folder-structure). Time Series Insights för hands versionen hanterar den här kall lagrings informationen exklusivt, men den är tillgänglig så att du kan läsa direkt som standard Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Time Series Insights för hands versions skrivningar, eftersom det kan orsaka data förlust.

### <a name="data-availability"></a>Data tillgänglighet

Azure Time Series Insights förhandsgranska partitioner och indexera data för optimala frågor. Data blir tillgängliga för frågor från både varm (om aktive rad) och kall lagring när den har indexerats. Mängden data som matas in kan påverka denna tillgänglighet.

> [!IMPORTANT]
> Under för hands versionen kan du uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för för hands versionen av Azure Time Series Insights.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Azure Time Series Insights för hands versions PAYG-miljö skapas ett Azure Storage allmänt v1-BLOB-konto som ditt långsiktiga kall arkiv.  

Azure Time Series Insights för hands version publicerar upp till två kopior av varje händelse i ditt Azure Storage-konto. Den ursprungliga kopian har händelser sorterade efter Inhämtnings tid. Den händelse ordningen **bevaras alltid** så att andra tjänster kan komma åt dina händelser utan sekvensering av problem. 

> [!NOTE]
> Du kan också använda Spark, Hadoop och andra välbekanta verktyg för att bearbeta RAW Parquet-filerna. 

Time Series Insights Preview partitionerar också om de Parquet-filer som ska optimeras för Time Series Insightss frågan. Den här ompartitionerade kopian av data sparas också. 

Under den offentliga för hands versionen lagras data på obestämd tid i ditt Azure Storage-konto.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Time Series Insights för hands versionen skapar.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Komma åt och exportera data från Time Series Insights för hands version

Du kanske vill komma åt data som visas i Time Series Insights Preview Explorer som används tillsammans med andra tjänster. Du kan till exempel använda dina data för att skapa en rapport i Power BI eller för att träna en maskin inlärnings modell med hjälp av Azure Machine Learning Studio. Eller så kan du använda dina data för att transformera, visualisera och modellera i dina Jupyter-anteckningsböcker.

Du kan komma åt dina data på tre sätt:

* I Time Series Insights Preview Explorer. Du kan exportera data som en CSV-fil från Utforskaren. Mer information finns i [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* Från Time Series Insights för hands versions-API med Get Events-frågan. Om du vill veta mer om detta API, Läs [tids serie fråga](./time-series-insights-update-tsq.md).
* Direkt från ett Azure Storage-konto. Du behöver Läs behörighet till det konto som du använder för att komma åt dina Time Series Insights för hands versions data. Mer information finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Borttagning av data

Ta inte bort Time Series Insights för hands versions filerna. Hantera endast relaterade data i Time Series Insights för hands version.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som har utformats för effektiv lagring och prestanda. Time Series Insights för hands version använder Parquet av dessa orsaker. Den partitionerar data efter tids serie-ID för frågans prestanda i skala.  

Mer information om filtypen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Time Series Insights för hands version lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Informationen finns i mappen `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra partitionerade kopian partitioneras av en gruppering av Time Series-ID: n och finns i mappen `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

I båda fallen motsvarar tids värdena skapande tid för BLOB. Data i `PT=Time`-mappen bevaras. Data i `PT=TsId` mapp kommer att optimeras för frågor över tid och kommer inte att vara statiska.

> [!NOTE]
> * `<YYYY>` mappar till en 4-siffrig års representation.
> * `<MM>` mappar till en månads representation med två siffror.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tids stämplings representation med fyrsiffrigt år (`YYYY`), tvåsiffrig månad (`MM`), tvåsiffrig dag (`DD`), tvåsiffrig timme (`HH`), tvåsiffrigt minut (`MM`), tvåsiffrig sekund (`SS`) och tresiffrig MS (`fff`).

Time Series Insights för hands versions händelser mappas till fil innehållet i Parquet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen Time-stämpel inte anges i händelse källan. Tidstämpeln är alltid i UTC.
* Varje rad innehåller Time Series ID-kolumn (er) som definieras när Time Series Insightss miljön skapas. Egenskaps namnet innehåller `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_string` (sträng), `_bool` (Boolean), `_datetime` (datetime) eller `_double` (Double), beroende på egenskaps typen.
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1**. När den här funktionen utvecklas kan namnet ökas.

## <a name="next-steps"></a>Nästa steg

- Läs [om hur du formar JSON för](./time-series-insights-update-how-to-shape-events.md)ingångar och frågor.

- Läs om den nya [data modellen](./time-series-insights-update-tsm.md).
