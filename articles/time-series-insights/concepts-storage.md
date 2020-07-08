---
title: Översikt över lagring – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data lagring i Azure Time Series Insights.
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085573"
---
# <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights-SKU-miljö ( *betala per* användning) skapar du två Azure-resurser:

* En Azure Time Series Insights miljö som kan konfigureras för varmt data lagring.
* Ett Azure Storage konto för kall data lagring.

Data i det varmt arkivet är bara tillgängliga via [Time Series-frågor](./time-series-insights-update-tsq.md) och [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md). Ditt varma Store kommer att innehålla senaste data inom den [kvarhållningsperiod](./time-series-insights-update-plan.md#the-preview-environment) som väljs när du skapar Time Series Insightss miljön.

Azure Time Series Insights sparar dina kall lagrings data till Azure Blob Storage i [fil formatet Parquet](#parquet-file-format-and-folder-structure). Time Series Insights hanterar den här kall lagrings informationen exklusivt, men den är tillgänglig så att du kan läsa direkt som standard Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Azure Time Series Insights skrivningar, eftersom det kan leda till data förlust.

## <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights partitioner och indexerar data för bästa prestanda för frågor. Data blir tillgängliga för frågor från både varm (om aktive rad) och kall lagring när den har indexerats. Mängden data som matas in kan påverka denna tillgänglighet.

> [!IMPORTANT]
> Du kan uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för Azure Time Series Insights.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Azure Time Series Insights PAYG-miljö skapas ett Azure Storage-konto som långsiktigt kall arkiv.  

Azure Time Series Insights behåller upp till två kopior av varje händelse i ditt Azure Storage-konto. En kopia lagrar händelser sorterade efter Inhämtnings tid, och tillåter alltid åtkomst till händelser i en tidsordnad sekvens. Med tiden skapar Time Series Insights också en ompartitionerad kopia av de data som ska optimeras för att genomföra Time Series Insights fråga.

Alla dina data lagras oändligt i ditt Azure Storage-konto.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Time Series Insights skapar.

#### <a name="accessing-time-series-insights-cold-store-data"></a>Åtkomst till Time Series Insights kall lagrings data

Förutom att komma åt dina data från den [Time Series Insights Explorer](./time-series-insights-update-explorer.md) -och [tids serie frågan](./time-series-insights-update-tsq.md), kanske du också vill komma åt dina data direkt från Parquet-filerna som lagras i kyl lagret. Du kan till exempel läsa, transformera och rensa data i en Jupyter Notebook och sedan använda den för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

Om du vill komma åt data direkt från ditt Azure Storage-konto behöver du Läs behörighet till det konto som används för att lagra Time Series Insights för hands versions data. Du kan sedan läsa valda data baserat på skapande tiden för den Parquet-fil som finns i `PT=Time` mappen som beskrivs nedan i avsnittet [fil format för Parquet](#parquet-file-format-and-folder-structure) .  Mer information om hur du aktiverar Läs åtkomst till ditt lagrings konto finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Borttagning av data

Ta inte bort dina Azure Time Series Insights-filer. Hantera endast relaterade data i Azure Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som utformats för effektiv lagring och prestanda. Azure Time Series Insights använder Parquet för att aktivera Time Series ID-baserade frågor om prestanda i skala.  

Mer information om filtypen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Dessa data finns i `PT=Time` mappen:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra ompartitionerade kopian grupperas efter Time Series-ID: n och finns i `PT=TsId` mappen:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Tidsstämpeln i BLOB-namnen i `PT=Time` mappen motsvarar ankomst tiden för de data som ska Azure Time Series Insights och inte tidsstämpeln för händelserna.

Data i `PT=TsId` mappen kommer att optimeras för frågan över tid och är inte statisk. Vid ompartitionering kan vissa händelser finnas i flera blobbar. Namngivningen av Blobbarna i den här mappen är inte garanterat oförändrad. 

I allmänhet, om du behöver komma åt data direkt via Parquet-filer, använder du `PT=Time` mappen.  Med framtida funktioner kan du få effektiv åtkomst till `PT=TsId` mappen. 

> [!NOTE]
>
> * `<YYYY>`mappar till en 4-siffrig års representation.
> * `<MM>`mappar till en tvåsiffrig månads representation.
> * `<YYYYMMDDHHMMSSfff>`mappar till en tids stämplings representation med fyrsiffrigt år ( `YYYY` ), tvåsiffrig månad ( `MM` ), tvåsiffrig dag (), tvåsiffrig timme (), tvåsiffrig `DD` `HH` minut () `MM` , tvåsiffrig sekund ( `SS` ) och tresiffrig MS-siffrig MS ( `fff` ).

Time Series Insights för hands versions händelser mappas till fil innehållet i Parquet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen tidstämpel inte anges i händelse källan. Den lagrade tidstämpeln är alltid i UTC.
* Varje rad innehåller Time Series-ID: n (TSID) som definieras när Time Series Insightss miljön skapas. TSID-egenskapens namn inkluderar `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_bool` (Boolean), `_datetime` (tidstämpel), `_long` (Long), ( `_double` Double), `_string` (sträng) eller `dynamic` (dynamisk), beroende på egenskaps typen.  Mer information finns i om [vilka data typer som stöds](./concepts-supported-data-types.md).
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1** och lagras i Bask-mappen med samma namn. När den här funktionen utvecklas kan det här mappnings schemat ändras och referens namnet ökas.

## <a name="next-steps"></a>Nästa steg

* Läs om [data modellering](./time-series-insights-update-tsm.md).

* Planera [Azure Time Series Insights för hands versions miljön](./time-series-insights-update-plan.md).
