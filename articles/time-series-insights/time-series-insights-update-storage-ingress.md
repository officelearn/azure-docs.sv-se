---
title: Datalagring och inträngning i förhandsversion – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig mer om datalagring och inträngning i förhandsversionen av Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246192"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Datalagring och inträngning i förhandsversionen av Azure Time Series Insights

I den här artikeln beskrivs uppdateringar av datalagring och ingående för förhandsversionen av Azure Time Series Insights. Den beskriver den underliggande lagringsstrukturen, filformatet och time series-ID-egenskapen. Den underliggande ingressprocessen, metodtips och aktuella förhandsgranskningsbegränsningar beskrivs också.

## <a name="data-ingress"></a>Datainträngning

Din Azure Time Series Insights-miljö innehåller en *inmatningsmotor* för att samla in, bearbeta och lagra tidsseriedata. 

Det finns vissa överväganden att tänka på för att säkerställa att alla inkommande data bearbetas, för att uppnå hög ingående skala och minimera *inmatningsfördröjning* (den tid det tar för Time Series Insights att läsa och bearbeta data från händelsekällan) när [du planerar din miljö](time-series-insights-update-plan.md).

Time Series Insights Förhandsgranska dataingressprinciper avgör var data kan hämtas från och vilket format data ska ha.

### <a name="ingress-policies"></a>Ingress politik

*Datainträngning* innebär hur data skickas till en förhandsversion av Azure Time Series Insights. 

Nyckelkonfiguration, formatering och metodtips sammanfattas nedan.

#### <a name="event-sources"></a>Händelsekällor

Förhandsversionen av Azure Time Series Insights stöder följande händelsekällor:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Förhandsversionen av Azure Time Series Insights stöder högst två händelsekällor per instans.

> [!IMPORTANT] 
> * Du kan uppleva en hög initial svarstid när du kopplar en händelsekälla till förhandsgranskningsmiljön. 
> Händelsekällans svarstid beror på antalet händelser som för närvarande finns i IoT Hub eller Event Hub.
> * Hög svarstid avtar när händelsekälladata först intas. Skicka in en supportbiljett via Azure-portalen om du upplever kontinuerlig hög latens.

#### <a name="supported-data-format-and-types"></a>Dataformat och typer som stöds

Azure Time Series Insights stöder UTF-8-kodade JSON som skickas från Azure IoT Hub eller Azure Event Hubs. 

De datatyper som stöds är:

| Datatyp | Beskrivning |
|---|---|
| **Bool** | En datatyp med ett `true` av `false`två lägen: eller . |
| **Datetime** | Representerar ett ögonblick i tiden, vanligtvis uttryckt som ett datum och tid på dagen. Uttryckt i [ISO 8601-format.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **Dubbel** | En dubbel precision 64-bitars [IEEE 754](https://ieeexplore.ieee.org/document/8766229) flyttal. |
| **sträng** | Textvärden, bestående av Unicode-tecken.          |

#### <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelsenyttolast, men dina data genomgår en förenklingsprocess när de lagras. 

Detaljerad information som beskriver hur du utformar dina JSON-händelser, skickar komplex typ och kapslad objekt förenkling är tillgänglig i [Hur man formar JSON för inträngning och fråga för](./time-series-insights-update-how-to-shape-events.md) att hjälpa till med planering och optimering.

### <a name="ingress-best-practices"></a>Metodtips för inträngning

Vi rekommenderar att du använder följande metodtips:

* Konfigurera Azure Time Series Insights och alla IoT Hub eller Event Hub i samma region för att minska potentiella svarstid.

* [Planera för dina skalbehov](time-series-insights-update-plan.md) genom att beräkna din förväntade inmatningshastighet och kontrollera att den ligger inom den takt som stöds nedan.

* Förstå hur du optimerar och formar dina JSON-data, samt de aktuella begränsningarna i förhandsversionen, genom att läsa [hur du formar JSON för inträngning och fråga](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Begränsningar för ingående skala och förhandsgranska 

Azure Time Series Insights Preview-begränsningar beskrivs nedan.

> [!TIP]
> Läs [Planera förhandsgranskningsmiljön](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) för en omfattande lista över alla förhandsgranskningsgränser.

#### <a name="per-environment-limitations"></a>Begränsningar per miljö

I allmänhet visas ingresshastigheter som faktorn för antalet enheter som finns i din organisation, händelseemissionsfrekvens och storleken på varje händelse:

*  **Antal anordningar** × **Händelseemissionsfrekvens** × **Storleken på varje händelse**.

Som standard kan förhandsversionen av Time Series Insights inta inkommande data med en hastighet av **upp till 1 megabyte per sekund (MBps) per Time Series Insights-miljö**.

> [!TIP] 
> * Miljöstöd för intag av hastigheter på upp till 16 MBps kan tillhandahållas på begäran.
> * Kontakta oss om du behöver högre dataflöde genom att skicka in en supportbiljett via Azure-portalen.
 
* **Exempel 1:**

    Contoso Shipping har 100 000 enheter som avger en händelse tre gånger per minut. Storleken på en händelse är 200 byte. De använder en eventhubb med fyra partitioner som händelsekällan Time Series Insights.

    * Intagshastigheten för deras Time Series Insights-miljö skulle vara: **100 000 enheter * 200 byte/händelse * (3/60 event/sek) = 1 MBps**.
    * Inmatningshastigheten per partition skulle vara 0,25 MBps.
    * Contoso Shippings inmatningshastighet skulle ligga inom begränsningen för förhandsgranskningsskalan.

* **Exempel 2:**

    Contoso Fleet Analytics har 60 000 enheter som avger en händelse varje sekund. De använder en IoT Hub 24 partitionsantal på 4 som händelsekällan Time Series Insights. Storleken på en händelse är 200 byte.

    * Miljöintagsfrekvensen skulle vara: **20 000 enheter * 200 byte/händelse * 1 händelse/sek = 4 MBps**.
    * Per partition hastighet skulle vara 1 MBps.
    * Contoso Fleet Analytics kan skicka en begäran till Time Series Insights via Azure-portalen för att öka inmatningshastigheten för sin miljö.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hubbpartitioner och per partitionsgränser

När du planerar time series insights-miljön är det viktigt att tänka på konfigurationen av händelsekällan/händelsekällorna som du ansluter till Time Series Insights. Både Azure IoT Hub och Event Hubs använder partitioner för att aktivera horisontell skala för händelsebearbetning. 

En *partition* är en ordnad sekvens av händelser som hålls i ett nav. Partitionsantalet anges under hubbgenereringsfasen och kan inte ändras. 

Granska [Hur många partitioner behöver jag](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need) för metodtips för partitionering av händelsehubbar?

> [!NOTE]
> De flesta IoT-hubbar som används med Azure Time Series Insights behöver bara fyra partitioner.

Oavsett om du skapar en ny hubb för time series insights-miljön eller använder en befintlig, måste du beräkna inmatningshastigheten per partition för att avgöra om den ligger inom förhandsgranskningsgränserna. 

Förhandsversionen av Azure Time Series Insights har för närvarande en allmän **gräns per partition på 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifika överväganden

När en enhet skapas i IoT Hub tilldelas den permanent till en partition. På så sätt kan IoT Hub garantera händelsebeställning (eftersom tilldelningen aldrig ändras).

En fast partition tilldelning påverkar också Time Series Insights instanser som intar data som skickas från IoT Hub nedströms. När meddelanden från flera enheter vidarebefordras till navet med samma gateway-enhets-ID kan de komma fram till samma partition samtidigt som de kan överskrida gränserna för per partitionsskala. 

**Effekt:**

* Om en enskild partition får en varaktig inmatningshastighet över förhandsgranskningsgränsen är det möjligt att Time Series Insights inte synkroniserar all enhetstelemetri innan IoT Hub-datalagringsperioden har överskridits. Därför kan skickade data gå förlorade om inmatningsgränserna konsekvent överskrids.

För att minska denna omständighet rekommenderar vi följande metodtips:

* Beräkna din per miljö och per partitionsintagsfrekvens innan du distribuerar lösningen.
* Se till att dina IoT Hub-enheter är belastningsbalanserade i så långt bortre utsträckning som möjligt.

> [!IMPORTANT]
> För miljöer som använder IoT Hub som en händelsekälla beräknar du inmatningshastigheten med hjälp av antalet navenheter som används för att vara säker på att hastigheten sjunker under begränsningen 0,5 MBps per partitionsbegränsning i förhandsversionen.
> * Även om flera händelser anländer samtidigt överskrids inte förhandsgranskningsgränsen.

  ![IoT-hubbpartitionsdiagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Mer information om hur du optimerar hubbdataflödet och partitioner:

* [IoT-hubbskala](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala för händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partitioner för händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Datalagring

När du skapar en *Pay-AKU-miljö för förhandsversion av en Time Series-statistik (PAYG)* skapar du två Azure-resurser:

* En Förhandsversion av Azure Time Series Insights-miljö som kan konfigureras för lagring av varma data.
* Ett Azure Storage-V1-blobkonto för lagring av kalla data.

Data i ditt varma arkiv är endast tillgängligt via [Time Series Query](./time-series-insights-update-tsq.md) och Azure Time Series [Insights Preview Explorer](./time-series-insights-update-explorer.md). Ditt varma arkiv innehåller de senaste data inom den [kvarhållningsperiod](./time-series-insights-update-plan.md#the-preview-environment) som valts när du skapar time series insights-miljön.

Time Series Insights Preview sparar dina kyllagerdata i Azure Blob-lagring i [parettfilformat](#parquet-file-format-and-folder-structure). Time Series Insights Preview hanterar dessa köldlagringsdata uteslutande, men det är tillgängligt för dig att läsa direkt som vanliga Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob-lagringskontot där köldlagringsdata finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten innehåller skriv- och borttagningsbehörigheter. Redigera eller ta inte bort data som Time Series Insights Preview skriver, eftersom det kan orsaka dataförlust.

### <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights Förhandsgranska partitioner och indexerar data för optimal frågeprestanda. Data blir tillgängliga för frågor från både varm (om aktiverat) och fryshus när det har indexerats. Mängden data som intas kan påverka den här tillgängligheten.

> [!IMPORTANT]
> Under förhandsgranskningen kan det uppstå en period på upp till 60 sekunder innan data blir tillgängliga. Om du upplever betydande svarstid utöver 60 sekunder skickar du in en supportbiljett via Azure-portalen.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage-information som är relevant för förhandsversionen av Azure Time Series Insights.

En grundlig beskrivning av Azure Blob-lagring finns i [introduktionen av lagringsblobar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagringskonto

När du skapar en Azure Time Series Insights Preview PAYG-miljö skapas ett Azure Storage-V1-blobkonto som ditt långsiktiga kyl- och fryshus.  

Förhandsversionen av Azure Time Series Insights behåller upp till två kopior av varje händelse i ditt Azure Storage-konto. En kopia lagrar händelser som beställts efter inmatningstid, vilket alltid ger åtkomst till händelser i en tidsbeställd sekvens. Med tiden skapar Time Series Insights Preview också en partitionerad kopia av data för att optimera för högpresterande time series insights-fråga. 

Under offentlig förhandsversion lagras data på obestämd tid i ditt Azure Storage-konto.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Blobbar för att skriva och redigera Time Series Insights

Om du vill säkerställa frågeprestanda och datatillgänglighet ska du inte redigera eller ta bort blobbar som Förhandsversionen av Time Series Insights skapar.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Komma åt Time Series Insights Förhandsgranska köldlagringsdata 

Förutom att komma åt dina data från [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) och Time Series [Query,](./time-series-insights-update-tsq.md)kanske du också vill komma åt dina data direkt från parquet-filer som lagras i kyl- och fryshuset. Du kan till exempel läsa, transformera och rensa data i en Jupyter-anteckningsbok och sedan använda den för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

Om du vill komma åt data direkt från ditt Azure Storage-konto behöver du läsbehörighet till kontot som används för att lagra förhandsversionsdata för Time Series Insights. Du kan sedan läsa valda data baserat på skapandetiden `PT=Time` för parettfilen som finns i mappen som beskrivs nedan i avsnittet [Parquet-filformat.](#parquet-file-format-and-folder-structure)  Mer information om hur du aktiverar läsåtkomst till ditt lagringskonto finns i [Hantera åtkomst till dina lagringskontoresurser](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Radering av data

Ta inte bort förhandsgranskningsfilerna för tidsseriestatistik. Hantera endast relaterade data från Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parkettfilformat och mappstruktur

Parkett är en öppen källkod columnar filformat som utformats för effektiv lagring och prestanda. Time Series Insights Preview använder Parkett för att aktivera tidsserie-ID-baserade frågeprestanda i stor skala.  

För mer information om parquet filtyp, läs [Parquet dokumentation](https://parquet.apache.org/documentation/latest/).

I förhandsversionen av Time Series Insights lagras kopior av dina data enligt följande:

* Den första, första kopian partitioneras efter inmatningstid och lagrar data ungefär i ankomstordning. Dessa data finns `PT=Time` i mappen:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra, partitionerade kopian grupperas efter tidsserie-ID:n och finns i `PT=TsId` mappen:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

I båda fallen motsvarar tidsegenskapen för parettfilen blobskapande tid. Data i `PT=Time` mappen bevaras utan ändringar när de har skrivits till filen. Data i `PT=TsId` mappen optimeras för fråga över tid och är inte statiska.

> [!NOTE]
> * `<YYYY>`kartor till en fyrsiffrig årsrepresentation.
> * `<MM>`kartor till en tvåsiffrig månadsrepresentation.
> * `<YYYYMMDDHHMMSSfff>`kartor till en tidsstämpelrepresentation med`YYYY`fyrsiffrigt`MM`år ( ),`DD`tvåsiffrig månad (`HH`), tvåsiffrig dag`MM`( ), tvåsiffrig`SS`timme ( ), tvåsiffrig minut`fff`( ), tvåsiffrig sekund ( ) och tresiffrig millisekunder ( ).

Time Series Insights Preview-händelser mappas till parettfilinnehåll enligt följande:

* Varje händelse mappas till en enda rad.
* Varje rad innehåller **tidsstämpelkolumnen** med en händelsetidsstämpel. Tidsstämpelegenskapen är aldrig null. **Händelseens gång** är standard om egenskapen tidsstämpel inte anges i händelsekällan. Den lagrade tidsstämpeln finns alltid i UTC.
* Varje rad innehåller TSID-kolumnerna (Time Series ID) enligt definitionen när time series Insights-miljön skapas. Egenskapsnamnet för TSID innehåller suffixet. `_string`
* Alla andra egenskaper som skickas som telemetridata mappas till `_bool` kolumnnamn `_datetime` som slutar med `_double` `_string` (sträng), (boolesk), (datetime) eller (dubbel), beroende på egenskapstypen.
* Det här mappningsschemat gäller den första versionen av filformatet, refererat till **V=1** och lagras i basmappen med samma namn. När den här funktionen utvecklas kan det här mappningsschemat ändras och referensnamnet ökas.

## <a name="next-steps"></a>Nästa steg

- Läs [om hur du formar JSON för inträngning och fråga](./time-series-insights-update-how-to-shape-events.md).

- Läs om den nya [datamodelleringen](./time-series-insights-update-tsm.md).
