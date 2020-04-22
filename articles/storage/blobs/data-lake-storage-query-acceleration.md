---
title: Azure Data Lake Storage-frågeacceleration (förhandsgranskning)
description: Frågeacceleration (förhandsversion) är en ny funktion för Azure Data Lake Storage som gör det möjligt för program och analysramverk att dramatiskt optimera databearbetning genom att hämta endast de data som krävs för en bearbetning.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771825"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage-frågeacceleration (förhandsgranskning)

Frågeacceleration (förhandsversion) är en ny funktion för Azure Data Lake Storage som gör det möjligt för program och analysramverk att dramatiskt optimera databearbetning genom att hämta endast de data som de behöver för att utföra en viss åtgärd. Detta minskar den tid och processorkraft som krävs för att få kritiska insikter i lagrade data.

> [!NOTE]
> Frågeaccelerationfunktionen är i offentlig förhandsversion och är tillgänglig i regionerna Kanada Central och France Central. Information om hur du granskar begränsningar finns i artikeln [Kända problem.](data-lake-storage-known-issues.md) Om du vill registrera dig i förhandsgranskningen läser du [det här formuläret](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Översikt

Frågeacceleration accepterar *filtreringspredikater* och *kolumnprojektioner* som gör det möjligt för program att filtrera rader och kolumner när data läss från disken. Endast data som uppfyller villkoren för ett predikat överförs över nätverket till programmet. Detta minskar nätverksfördröjningen och beräkningskostnaden.  

Du kan använda SQL för att ange radfilterpredikater och kolumnprojektioner i en begäran om frågeacceleration. En begäran bearbetar bara en fil. Därför stöds inte avancerade relationsfunktioner i SQL, till exempel kopplingar och grupper efter aggregat. Frågeacceleration stöder CSV- och JSON-formaterade data som indata till varje begäran.

Frågeaccelerationsfunktionen är inte begränsad till DataSjölagring (lagringskonton som har det hierarkiska namnområdet aktiverat på dem). Frågeaccelerationen är helt kompatibel med blobbar i lagringskonton som **inte** har ett hierarkiskt namnområde aktiverat på dem. Det innebär att du kan uppnå samma minskning av nätverksfördröjning och beräkningskostnader när du bearbetar data som du redan har lagrat som blobbar i lagringskonton.

Ett exempel på hur du använder frågeacceleration i ett klientprogram finns i [Filtrera data med hjälp av Azure Data Lake Storage-frågeacceleration](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Dataflöde

Följande diagram illustrerar hur ett vanligt program använder frågeacceleration för att bearbeta data.

> [!div class="mx-imgBorder"]
> ![Översikt över frågeacceleration](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Klientprogrammet begär fildata genom att ange predikater och kolumnprojektioner.

2. Frågeacceleration tolkar den angivna SQL-frågan och distribuerar arbete för att tolka och filtrera data.

3. Processorer läser data från disken, tolkar data med hjälp av rätt format och filtrerar sedan data genom att använda de angivna predikat- och kolumnprojektionerna.

4. Frågeacceleration kombinerar svarssvandrarna för att strömma tillbaka till klientprogrammet.

5. Klientprogrammet tar emot och tolkar det strömmade svaret. Programmet behöver inte filtrera ytterligare data och kan använda önskad beräkning eller omvandling direkt.

## <a name="better-performance-at-a-lower-cost"></a>Bättre prestanda till en lägre kostnad

Frågeacceleration optimerar prestanda genom att minska mängden data som överförs och bearbetas av ditt program.

Om du vill beräkna ett aggregerat värde hämtar program ofta **alla** data från en fil och bearbetar och filtrerar sedan data lokalt. En analys av indata-/utdatamönster för analysarbetsbelastningar visar att program vanligtvis bara kräver 20 % av de data som de läser för att utföra en given beräkning. Denna statistik gäller även efter tillämpning av tekniker som [partition beskärning](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Det innebär att 80 % av dessa data i onödan överförs över nätverket, tolkas och filtreras efter program. Det här mönstret, som huvudsakligen är utformat för att ta bort onödiga data, medför en betydande beräkningskostnad.  

Även om Azure har ett branschledande nätverk, både när det gäller dataflöde och svarstid, är det fortfarande dyrt att överföra data över nätverket i onödan för programprestanda. Genom att filtrera bort oönskade data under lagringsbegäran eliminerar frågeacceleration den här kostnaden.

Dessutom kräver cpu-belastningen som krävs för att tolka och filtrera onödiga data ditt program för att etablera ett större antal och större virtuella datorer för att göra det arbete. Genom att överföra den här beräkningsbelastningen till frågeacceleration kan program uppnå betydande kostnadsbesparingar.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Program som kan dra nytta av frågeacceleration

Frågeacceleration är utformad för distribuerade analysramverk och databehandlingsprogram. 

Distribuerade analysramverk som Apache Spark och Apache Hive innehåller ett lagringsabstraktionslager inom ramen. Dessa motorer innehåller också frågeoptimerare som kan inbegripa kunskap om den underliggande I/O-tjänstens funktioner när du fastställer en optimal frågeplan för användarfrågor. Dessa ramverk börjar integrera frågeacceleration. Som ett resultat kommer användare av dessa ramverk att se förbättrad frågefördröjning och en lägre total ägandekostnad utan att behöva göra några ändringar i frågorna. 

Frågeacceleration är också avsedd för databehandlingsprogram. Dessa typer av program utför vanligtvis storskaliga dataomvandlingar som kanske inte direkt leder till analysinsikter så att de inte alltid använder etablerade distribuerade analysramverk. Dessa program har ofta en mer direkt relation med den underliggande lagringstjänsten så att de kan dra direkt nytta av funktioner som frågeacceleration. 

Ett exempel på hur ett program kan integrera frågeacceleration finns [i Filtrera data med hjälp av Azure Data Lake Storage-frågeacceleration](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Prissättning

På grund av den ökade beräkningsbelastningen i Azure Data Lake Storage-tjänsten skiljer sig prismodellen för att använda frågeacceleration från den normala Azure Data Lake Storage-transaktionsmodellen. Frågeacceleration tar ut en kostnad för mängden data som genomsöks samt en kostnad för mängden data som returneras till anroparen.

Trots ändringen av faktureringsmodellen är Frågeaccelerationens prismodell utformad för att sänka den totala ägandekostnaden för en arbetsbelastning, med tanke på minskningen av de mycket dyrare vm-kostnaderna.

## <a name="next-steps"></a>Nästa steg

- [Registreringsformulär för frågeacceleration](https://aka.ms/adls/qa-preview-signup)    
- [Filtrera data med hjälp av azure datasjölagringsfrågeacceleration](data-lake-storage-query-acceleration-how-to.md)
- [SQL-språkreferens för frågeacceleration (förhandsgranskning)](query-acceleration-sql-reference.md)
- REST-API-referens för frågeacceleration



