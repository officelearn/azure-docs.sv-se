---
title: Lagrings översikt – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om data lagring i Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483277"
---
# <a name="data-storage"></a>Datalagring

När du skapar en Azure Time Series Insights Gen2-miljö skapar du två Azure-resurser:

* En Azure Time Series Insights Gen2-miljö som kan konfigureras för varm data lagring.
* Ett Azure Storage konto för kall data lagring.

Data i det varmt arkivet är bara tillgängliga via [Time Series-API: er](./time-series-insights-update-tsq.md) och [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md). Ditt varma Store kommer att innehålla senaste data inom den [kvarhållna kvarhållningsperiod](./time-series-insights-update-plan.md#the-preview-environment) som valts när du skapade Azure Time Series Insights Gen2-miljön.

Azure Time Series Insights Gen2 sparar dina kall data till Azure Blob Storage i [fil formatet Parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 hanterar den här kall lagrings informationen exklusivt, men den är tillgänglig så att du kan läsa direkt som standard Parquet-filer.

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Azure Time Series Insights Gen2 skrivningar, eftersom det kan orsaka data förlust.

## <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights Gen2 partitioner och indexerar data för optimala prestanda för frågor. Data blir tillgängliga för frågor från både varm (om aktive rad) och kall lagring när den har indexerats. Mängden data som matas in kan påverka denna tillgänglighet.

> [!IMPORTANT]
> Du kan uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="azure-storage"></a>Azure Storage

I det här avsnittet beskrivs Azure Storage information som är relevant för Azure Time Series Insights Gen2.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Ditt lagrings konto

När du skapar en Azure Time Series Insights Gen2-miljö skapas ett Azure Storage-konto som långsiktigt kall arkiv.  

Azure Time Series Insights Gen2 behåller upp till två kopior av varje händelse i ditt Azure Storage-konto. En kopia lagrar händelser sorterade efter Inhämtnings tid, och tillåter alltid åtkomst till händelser i en tidsordnad sekvens. Med tiden skapar Azure Time Series Insights Gen2 också en ompartitionerad kopia av data för att optimera för att genomföra frågor.

Alla dina data lagras oändligt i ditt Azure Storage-konto.

#### <a name="writing-and-editing-blobs"></a>Skriva och redigera blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Azure Time Series Insights Gen2 skapar.

#### <a name="accessing-cold-store-data"></a>Åtkomst till data för kall lagring

Förutom att komma åt dina data från API: erna för [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) och [Time Series](./time-series-insights-update-tsq.md), kanske du också vill komma åt dina data direkt från Parquet-filerna som lagras i kyl lagret. Du kan till exempel läsa, transformera och rensa data i en Jupyter Notebook och sedan använda den för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

Om du vill komma åt data direkt från ditt Azure Storage-konto behöver du Läs behörighet till det konto som används för att lagra dina Azure Time Series Insights Gen2-data. Du kan sedan läsa valda data baserat på skapande tiden för den Parquet-fil som finns i `PT=Time` mappen som beskrivs nedan i avsnittet [fil format för Parquet](#parquet-file-format-and-folder-structure) .  Mer information om hur du aktiverar Läs åtkomst till ditt lagrings konto finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Borttagning av data

Ta inte bort dina Azure Time Series Insights Gen2-filer. Hantera endast relaterade data i Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som utformats för effektiv lagring och prestanda. Azure Time Series Insights Gen2 använder Parquet för att aktivera tids serie-ID-baserade frågeresultat i stor skala.  

Mer information om filtypen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Dessa data finns i `PT=Time` mappen:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra ompartitionerade kopian grupperas efter Time Series-ID: n och finns i `PT=TsId` mappen:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Tidsstämpeln i BLOB-namnen i `PT=Time` mappen motsvarar ankomst tiden för data till Azure Time Series Insights Gen2 och inte tidsstämpeln för händelserna.

Data i `PT=TsId` mappen kommer att optimeras för frågan över tid och är inte statisk. Vid ompartitionering kan vissa händelser finnas i flera blobbar. Namngivningen av Blobbarna i den här mappen är inte garanterat oförändrad.

I allmänhet, om du behöver komma åt data direkt via Parquet-filer, använder du `PT=Time` mappen.  Med framtida funktioner kan du få effektiv åtkomst till `PT=TsId` mappen.

> [!NOTE]
>
> * `<YYYY>` mappar till en 4-siffrig års representation.
> * `<MM>` mappar till en tvåsiffrig månads representation.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tids stämplings representation med fyrsiffrigt år ( `YYYY` ), tvåsiffrig månad ( `MM` ), tvåsiffrig dag (), tvåsiffrig timme (), tvåsiffrig `DD` `HH` minut () `MM` , tvåsiffrig sekund ( `SS` ) och tresiffrig MS-siffrig MS ( `fff` ).

Azure Time Series Insights Gen2-händelser mappas till Parquet-filinnehållet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen tidstämpel inte anges i händelse källan. Den lagrade tidstämpeln är alltid i UTC.
* Varje rad innehåller Time Series-ID: n (TSID) som definieras när Azure Time Series Insights Gen2-miljön skapas. TSID-egenskapens namn inkluderar `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_bool` (Boolean), `_datetime` (tidstämpel), `_long` (Long), ( `_double` Double), `_string` (sträng) eller `dynamic` (dynamisk), beroende på egenskaps typen.  Mer information finns i om [vilka data typer som stöds](./concepts-supported-data-types.md).
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1** och lagras i Bask-mappen med samma namn. När den här funktionen utvecklas kan det här mappnings schemat ändras och referens namnet ökas.

## <a name="next-steps"></a>Nästa steg

* Läs om [data modellering](./time-series-insights-update-tsm.md).

* Planera din [Azure Time Series Insights Gen2-miljö](./time-series-insights-update-plan.md).
