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
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ca5ba8d7b2d78440401e29344361538c3650ba48
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779165"
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

#### <a name="event-sources"></a>Händelsekällor

Azure Time Series Insights för hands version stöder följande händelse källor:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights för hands versionen stöder högst två händelse källor per instans. När du ansluter en händelse källa läser TSD-miljön alla händelser som för närvarande lagras i IoT-eller Händelsehubben, med början den äldsta händelsen.

> [!IMPORTANT]
>
> * Du kan få hög första svars tid när du kopplar en händelse källa till för hands versions miljön.
> Svars tiden för händelse källan beror på antalet händelser som för närvarande finns i IoT Hub eller Händelsehubben.
> * Hög latens kommer att undertryckas när händelsens källdata först matas in. Skicka in ett support ärende via Azure Portal om du får en kontinuerlig hög latens.

#### <a name="supported-data-format-and-types"></a>Data format och typer som stöds

Azure Time Series Insights stöder UTF-8-kodad JSON som skickas från Azure IoT Hub eller Azure Event Hubs. 

De data typer som stöds är:

| Datatyp | Beskrivning |
|---|---|
| **boolesk** | En datatyp med ett av två tillstånd: `true` eller `false` . |
| **dateTime** | Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Uttryckt i [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. |
| **Dubbelklicka** | En dubbel precision 64-bitars [IEEE 754](https://ieeexplore.ieee.org/document/8766229) -svävande punkt. |
| **sträng** | Text värden, bestående av Unicode-tecken.          |

#### <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelse nytto Last, men dina data kommer att genomgå en förenklings process när de lagras.

Detaljerad information som beskriver hur du kan forma dina JSON-händelser, skicka komplex typ och utjämningen av kapslat objekt finns i [så här formar du JSON för](./time-series-insights-update-how-to-shape-events.md) ingångs-och fråge frågor för att hjälpa till med planering och optimering.

### <a name="ingress-best-practices"></a>Ingress metod tips

Vi rekommenderar att du använder följande bästa praxis:

* Konfigurera Azure Time Series Insights och IoT Hub eller Händelsehubben i samma region för att minska potentiell latens.

* [Planera för skalnings behoven](time-series-insights-update-plan.md) genom att beräkna den förväntade inmatnings takten och kontrol lera att den ligger inom den frekvens som stöds nedan.

* Lär dig hur du optimerar och formar dina JSON-data, samt de aktuella begränsningar som finns i förhands granskningen, genom [att läsa hur du kan forma JSON för ingress och fråga](./time-series-insights-update-how-to-shape-events.md).

* Använd strömnings inmatning för nästan endast real tids data och senaste data, så stöds inte direkt uppspelning av historiska data.

#### <a name="historical-data-ingestion"></a>Historisk data inmatning

Det finns för närvarande inte stöd för att använda streaming-pipeline för att importera historiska data i Azure Time Series Insights Preview. Om du behöver importera tidigare data till din miljö följer du rikt linjerna nedan:

* Strömma inte Live och historiska data parallellt. Om du matar ut data från varandra kommer du att leda till försämrade frågor.
* Mata in historiska data i tidsordnad tid för bästa prestanda.
* Håll koll på gräns värdena för inmatnings flödet nedan.
* Inaktivera varmt Arkiv om data är äldre än lagrings perioden för din varma lagrings tid.

### <a name="ingress-scale-and-preview-limitations"></a>Ingress-skalning och för hands versions begränsningar

Azure Time Series Insights för hands versions begränsningar beskrivs nedan.

> [!TIP]
> Läs [Planera din för hands versions miljö](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) för en omfattande lista över alla begränsningar för för hands versionen.

#### <a name="per-environment-limitations"></a>Begränsningar per miljö

I allmänhet visas ingångs priser som faktor för antalet enheter i din organisation, frekvens för händelse utsläpp och storleken på varje händelse:

*  **Antal enheter** × **händelse utsläpps frekvens** × **storlek på varje händelse**.

Som standard kan Time Series Insights för hands version mata in inkommande data med en hastighet på **upp till 1 megabyte per sekund (Mbit/s) per Time Series Insights-miljö**. Det finns ytterligare begränsningar [per nav-partition](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Miljö stöd för att mata in hastigheter på upp till 16 MBps kan tillhandahållas av begäran.
> * Kontakta oss om du behöver mer data flöde genom att skicka in ett support ärende via Azure Portal.
 
* **Exempel 1:**

    Contoso-leverans har 100 000 enheter som genererar en händelse tre gånger per minut. Storleken på en händelse är 200 byte. De använder en IoT Hub med fyra partitioner som Time Series Insights händelse källa.

    * Inmatnings takten för Time Series Insightss miljön blir: **100 000 enheter * 200 byte/event * (3/60 Event/s) = 1 Mbit/s**.
    * Inmatnings hastigheten per partition blir 0,25 Mbit/s.
    * Contosos överförings takt skulle ligga inom begränsningen för för hands versionen.

* **Exempel 2:**

    Contoso flotta Analytics har 60 000 enheter som genererar en händelse varje sekund. De använder en Event Hub med ett antal partitioner på 4 som den Time Series Insights händelse källan. Storleken på en händelse är 200 byte.

    * Miljöns utmatnings frekvens skulle vara: **60 000 enheter * 200 byte/event * 1 händelse/SEK = 12 Mbit/s**.
    * Antalet per partition skulle bli 3 Mbit/s.
    * Contoso flottans analys "inmatnings takt är över miljön och partitionens gränser. De kan skicka en begäran till Time Series Insights via Azure Portal för att öka inmatnings takten för deras miljö och skapa en Händelsehubben med fler partitioner för att ligga inom gränserna för för hands versionen.

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
>
> * Även om flera händelser anländer samtidigt, överskrids inte för hands versions gränsen.

  ![IoT Hub partitions diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Se följande resurser för att lära dig mer om hur du optimerar hubben och partitionerna:

* [IoT Hub skala](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub-skala](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub-partitioner](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights för hands version av SKU-miljö ( *betala per* användning) skapar du två Azure-resurser:

* En Azure Time Series Insights för hands versions miljö som kan konfigureras för varm data lagring.
* Ett Azure Storage generella v1 BLOB-konto för kall data lagring.

Data i det varmt arkivet är bara tillgängliga via [Time Series-frågan](./time-series-insights-update-tsq.md) och [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md). Ditt varma Store kommer att innehålla senaste data inom den [kvarhållningsperiod](./time-series-insights-update-plan.md#the-preview-environment) som väljs när du skapar Time Series Insightss miljön.

Time Series Insights för hands version sparar dina kall data till Azure Blob Storage i [fil formatet Parquet](#parquet-file-format-and-folder-structure). Time Series Insights för hands versionen hanterar den här kall lagrings informationen exklusivt, men den är tillgänglig så att du kan läsa direkt som standard Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Time Series Insights för hands versions skrivningar, eftersom det kan orsaka data förlust.

### <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights förhandsgranska partitioner och indexera data för optimala frågor. Data blir tillgängliga för frågor från både varm (om aktive rad) och kall lagring när den har indexerats. Mängden data som matas in kan påverka denna tillgänglighet.

> [!IMPORTANT]
> Under för hands versionen kan du uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för för hands versionen av Azure Time Series Insights.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Azure Time Series Insights för hands versions PAYG-miljö skapas ett Azure Storage allmänt v1-BLOB-konto som ditt långsiktiga kall arkiv.  

Azure Time Series Insights för hands versionen behåller upp till två kopior av varje händelse i ditt Azure Storage-konto. En kopia lagrar händelser sorterade efter Inhämtnings tid, och tillåter alltid åtkomst till händelser i en tidsordnad sekvens. Med tiden skapar Time Series Insights för hands versionen även en ompartitionerad kopia av data för att optimera för att genomföra Time Series Insights fråga.

Under den offentliga för hands versionen lagras data på obestämd tid i ditt Azure Storage-konto.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Time Series Insights för hands versionen skapar.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Komma åt Time Series Insights för hands version av data

Förutom att komma åt dina data från den [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) -och [Time Series-frågan](./time-series-insights-update-tsq.md), kan du också vilja komma åt dina data direkt från Parquet-filerna som lagras i kyl lagret. Du kan till exempel läsa, transformera och rensa data i en Jupyter Notebook och sedan använda den för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

Om du vill komma åt data direkt från ditt Azure Storage-konto behöver du Läs behörighet till det konto som används för att lagra Time Series Insights för hands versions data. Du kan sedan läsa valda data baserat på skapande tiden för den Parquet-fil som finns i `PT=Time` mappen som beskrivs nedan i avsnittet [fil format för Parquet](#parquet-file-format-and-folder-structure) .  Mer information om hur du aktiverar Läs åtkomst till ditt lagrings konto finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Borttagning av data

Ta inte bort Time Series Insights för hands versions filerna. Hantera endast relaterade data i Time Series Insights för hands version.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som utformats för effektiv lagring och prestanda. Time Series Insights för hands versionen använder Parquet för att aktivera Time Series ID-baserade frågeresultat i stor skala.  

Mer information om filtypen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Time Series Insights för hands version lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Dessa data finns i `PT=Time` mappen:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra ompartitionerade kopian grupperas efter Time Series-ID: n och finns i `PT=TsId` mappen:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

I båda fallen motsvarar egenskapen Time för Parquet-filen den tid då bloben skapades. Data i `PT=Time` mappen bevaras utan ändringar när de skrivs till filen. Data i `PT=TsId` mappen kommer att optimeras för frågan över tid och är inte statisk.

> [!NOTE]
>
> * `<YYYY>`mappar till en 4-siffrig års representation.
> * `<MM>`mappar till en tvåsiffrig månads representation.
> * `<YYYYMMDDHHMMSSfff>`mappar till en tids stämplings representation med fyrsiffrigt år ( `YYYY` ), tvåsiffrig månad ( `MM` ), tvåsiffrig dag (), tvåsiffrig timme (), tvåsiffrig `DD` `HH` minut () `MM` , tvåsiffrig sekund ( `SS` ) och tresiffrig MS-siffrig MS ( `fff` ).

Time Series Insights för hands versions händelser mappas till fil innehållet i Parquet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen Time-stämpel inte anges i händelse källan. Den lagrade tidstämpeln är alltid i UTC.
* Varje rad innehåller Time Series-ID: n (TSID) som definieras när Time Series Insightss miljön skapas. TSID-egenskapens namn inkluderar `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_string` (sträng), `_bool` (Boolean), `_datetime` (datetime) eller `_double` (Double), beroende på egenskaps typen.
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1** och lagras i Bask-mappen med samma namn. När den här funktionen utvecklas kan det här mappnings schemat ändras och referens namnet ökas.

## <a name="next-steps"></a>Nästa steg

- Läs [om hur du formar JSON för](./time-series-insights-update-how-to-shape-events.md)ingångar och frågor.

- Läs om den nya [data modellen](./time-series-insights-update-tsm.md).
