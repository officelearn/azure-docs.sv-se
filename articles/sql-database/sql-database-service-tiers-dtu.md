---
title: Tjänstnivåer - DTU-baserad inköpsmodell
description: Lär dig mer om tjänstnivåer i den DTU-baserade inköpsmodellen för enskilda och poolade databaser för att tillhandahålla beräknings- och lagringsstorlekar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562127"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Tjänstnivåer i en DTU-baserad inköpsmodell

Tjänstnivåer i den DTU-baserade inköpsmodellen differentieras med en rad beräkningsstorlekar med en fast mängd inkluderad lagring, fast kvarhållningsperiod för säkerhetskopior och fast pris. Alla tjänstnivåer i den DTU-baserade inköpsmodellen ger flexibilitet att ändra beräkningsstorlekar med minimal [stilleståndstid.](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) Det finns dock en switch över period där anslutningen går förlorad till databasen under en kort tid, vilket kan minskas med hjälp av logik för återförsök. Enskilda databaser och elastiska pooler faktureras per timme baserat på tjänstnivå och beräkningsstorlek.

> [!IMPORTANT]
> SQL Database-hanterad instans stöder inte en DTU-baserad inköpsmodell. Mer information finns i [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Information om vCore-baserade tjänstnivåer finns i [vCore-baserade tjänstnivåer](sql-database-service-tiers-vcore.md). Information om hur du skiljer DTU-baserade tjänstnivåer och vCore-baserade tjänstnivåer finns i [Azure SQL Database inköpsmodeller](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Jämföra de DTU-baserade tjänstnivåerna

Att välja en tjänstnivå beror främst på kontinuitet, lagring och prestandakrav.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Målarbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion|
|Upptid SLA|99,99 %|99,99 %|99,99 %|
|Maximal kvarhållning av säkerhetskopiering|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medel, hög|
|IO-dataflöde (ungefärlig) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|IO-latens (ungefärlig)|5 ms (läs), 10 ms (skriva)|5 ms (läs), 10 ms (skriva)|2 ms (läs/skriv)|
|Indexering av Columnstore |Ej tillämpligt|S3 och högre|Stöds|
|OLTP i minnet|Ej tillämpligt|Ej tillämpligt|Stöds|
|||||

> [!IMPORTANT]
> Tjänstnivåerna Basic, Standard S0, S1 och S2 ger mindre än en processor (CPU).  För PROCESSOR-intensiva arbetsbelastningar rekommenderas en tjänstnivå på S3 eller mer. 
>
>När det gäller datalagring placeras tjänstnivåerna Basic, Standard S0 och S1 på standardsidan Blobbar. Standard page blobbar använder hårddisk (HDD)-baserade lagringsmedia och är bäst lämpade för utveckling, testning och andra sällan använda arbetsbelastningar som är mindre känsliga för prestandavariationer.
>

> [!NOTE]
> Du kan få en kostnadsfri Azure SQL-databas på basic-tjänstnivån tillsammans med ett kostnadsfritt Azure-konto för att utforska Azure. Information finns i [Skapa en hanterad molndatabas med ditt kostnadsfria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU för en databas och lagringsgränser

Beräkningsstorlekar uttrycks i termer av databastransaktionsenheter (DTI: er) för enskilda databaser och elastiska databastransaktionsenheter (eDU:er) för elastiska pooler. Mer information om DTU:er och eDTU:er finns i [DTU-baserad inköpsmodell](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximal lagringsstorlek | 2 GB | 1 TB | 4 TB  |
| Maximal DTU | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU för elastisk pool, lagring och poolade databasgränser

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximal lagringsstorlek per databas  | 2 GB | 1 TB | 1 TB |
| Maximal lagringsstorlek per pool | 156 GB | 4 TB | 4 TB |
| Högsta eDTU:er per databas | 5 | 3000 | 4000 |
| Maximala eDTU:er per pool | 1600 | 3000 | 4000 |
| Maximalt antal databaser per pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme i Premium-nivån är för närvarande tillgängligt i alla regioner utom: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD-regioner och US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU-riktmärke

Fysiska egenskaper (CPU, minne, IO) som är associerade till varje DTU-mått kalibreras med hjälp av ett riktmärke som simulerar verklig databasarbetsbelastning.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelera benchmarkresultat till databasens verkliga prestanda

Det är viktigt att förstå att alla riktmärken endast är representativa och vägledande. De transaktionsräntor som uppnås med benchmark-programmet kommer inte att vara desamma som de som kan uppnås med andra program. Riktmärket består av en samling olika transaktionstyper som körs mot ett schema som innehåller en rad tabeller och datatyper. Medan riktmärket använder samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar det inte någon specifik databas- eller programklass. Målet med riktmärket är att ge en rimlig guide till den relativa prestandan för en databas som kan förväntas när du skalar upp eller ned mellan beräkningsstorlekar. I verkligheten är databaser av olika storlek och komplexitet, stöter på olika blandningar av arbetsbelastningar och kommer att svara på olika sätt. Ett IO-intensivt program kan till exempel träffa IO-tröskelvärden tidigare, eller så kan ett PROCESSOR-intensivt program träffa CPU-gränser tidigare. Det finns ingen garanti för att någon särskild databas kommer att skalas på samma sätt som riktmärket under ökande belastning.

Riktmärket och dess metod beskrivs mer i detalj nedan.

### <a name="benchmark-summary"></a>Sammanfattning av referensvärdet

Riktmärket mäter prestanda för en blandning av grundläggande databasåtgärder som oftast förekommer i OLTP-arbetsbelastningar (Online Transaction Processing). Även om riktmärket är utformat med cloud computing i åtanke, har databasschemat, datapopulationen och transaktionerna utformats för att vara brett representativa för de grundläggande element som oftast används i OLTP-arbetsbelastningar.

### <a name="schema"></a>Schema

Schemat är utformat för att ha tillräckligt med variation och komplexitet för att stödja ett brett spektrum av åtgärder. Riktmärket går mot en databas som består av sex tabeller. Tabellerna kan delas in i tre kategorier: fast storlek, skalning och växande. Det finns två tabeller med fast storlek. tre skalningstabeller. och ett växande bord. Tabeller med fast storlek har ett konstant antal rader. Skalningstabeller har en kardinalitet som är proportionell mot databasens prestanda, men som inte ändras under referensvärdet. Den växande tabellen är dimensionerad som en skalningstabell vid första inläsningen, men sedan ändras kardinaliteten i samband med att du kör riktmärket när rader infogas och tas bort.

Schemat innehåller en blandning av datatyper, inklusive heltal, numeriskt, tecken och datum/tid. Schemat innehåller primära och sekundära nycklar, men inte några externa nycklar - det vill säga det finns inga referensintegritetsbegränsningar mellan tabeller.

Ett datagenereringsprogram genererar data för den ursprungliga databasen. Heltal och numeriska data genereras med olika strategier. I vissa fall fördelas värden slumpmässigt över ett intervall. I andra fall är en uppsättning värden slumpmässigt permuted för att säkerställa att en viss fördelning upprätthålls. Textfält genereras från en viktad lista med ord för att producera realistiska data.

Databasen är dimensionerad baserat på en "skalfaktor". Skalfaktorn (förkortad SF) bestämmer kardinaliteten för skalnings- och odlingstabellerna. Som beskrivs nedan i avsnittet Användare och tempo, databasens storlek, antal användare och maximal prestanda alla skala i proportion till varandra.

### <a name="transactions"></a>Transaktioner

Arbetsbelastningen består av nio transaktionstyper, som visas i tabellen nedan. Varje transaktion är utformad för att markera en viss uppsättning systemegenskaper i databasmotorn och systemmaskinvaran, med hög kontrast från de andra transaktionerna. Detta tillvägagångssätt gör det lättare att bedöma effekterna av olika komponenter på den övergripande prestandan. Transaktionen "Läs tung" ger till exempel ett stort antal läsåtgärder från disken.

| Transaktionstyp | Beskrivning |
| --- | --- |
| Läs Lite |VÄLJ; i minnet; Skrivskyddad |
| Läs medium |VÄLJ; mestadels i minnet; Skrivskyddad |
| Läs Heavy |VÄLJ; mestadels inte i minnet; Skrivskyddad |
| Uppdatera Lite |UPPDATERING; i minnet; läsa-skriva |
| Uppdatera tung |UPPDATERING; mestadels inte i minnet; läsa-skriva |
| Infoga Lite |INFOGA; i minnet; läsa-skriva |
| Sätt in tung |INFOGA; mestadels inte i minnet; läsa-skriva |
| Ta bort |TA BORT; blandning av i minnet och inte i minnet; läsa-skriva |
| CPU tung |VÄLJ; i minnet; relativt tung CPU-belastning; Skrivskyddad |

### <a name="workload-mix"></a>Blandning av arbetsbelastning

Transaktioner väljs slumpmässigt från en viktad fördelning med följande övergripande mix. Den totala mixen har ett läs-/skrivförhållande på cirka 2:1.

| Transaktionstyp | % av Mix |
| --- | --- |
| Läs Lite |35 |
| Läs medium |20 |
| Läs Heavy |5 |
| Uppdatera Lite |20 |
| Uppdatera tung |3 |
| Infoga Lite |3 |
| Sätt in tung |2 |
| Ta bort |2 |
| CPU tung |10 |

### <a name="users-and-pacing"></a>Användare och tempo

Benchmark-arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar för att simulera beteendet hos ett antal samtidiga användare. Även om alla anslutningar och transaktioner är maskingenererade, för enkelhetens skull hänvisar vi till dessa anslutningar som "användare". Även om varje användare arbetar oberoende av alla andra användare utför alla användare samma stegcykel som visas nedan:

1. Upprätta en databasanslutning.
2. Upprepa tills signaleras att avsluta:
   - Välj en transaktion slumpmässigt (från en viktad fördelning).
   - Utför den valda transaktionen och mät svarstiden.
   - Vänta på en fördröjning.
3. Stäng databasanslutningen.
4. Avsluta.

Tempofördröjningen (i steg 2c) väljs slumpmässigt, men med en fördelning som har ett genomsnitt på 1,0 sekund. Således kan varje användare i genomsnitt generera högst en transaktion per sekund.

### <a name="scaling-rules"></a>Skala regler

Antalet användare bestäms av databasens storlek (i skalfaktorenheter). Det finns en användare för var femte skalfaktorenheter. På grund av tempofördröjningen kan en användare generera högst en transaktion per sekund, i genomsnitt.

Till exempel kommer en skalfaktor på 500 (SF=500) databas att ha 100 användare och kan uppnå en maximal hastighet på 100 TPS. För att driva en högre TPS-hastighet krävs fler användare och en större databas.

### <a name="measurement-duration"></a>Mätningens varaktighet

En giltig benchmark-körning kräver en konstant mätlängd på minst en timme.

### <a name="metrics"></a>Mått

De viktigaste måtten i riktmärket är dataflöde och svarstid.

- Genomströmning är det viktigaste prestandamåttet i riktmärket. Dataflöde rapporteras i transaktioner per tidsenhet, räknat alla transaktionstyper.
- Svarstid är ett mått på prestanda förutsägbarhet. Svarstidens begränsning varierar med serviceklass, med högre serviceklasser som har ett strängare svarstidskrav, som visas nedan.

| Klass för tjänsten | Mått på dataflöde | Krav på svarstid |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95:e percentilen på 0,5 sekunder |
| Standard |Transaktioner per minut |90:e percentilen på 1,0 sekunder |
| Basic |Transaktioner per timme |80:e percentilen på 2,0 sekunder |

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för enskilda databaser finns i [SQL Database DTU-baserade resursgränser för enskilda databaser](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Mer information om specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för elastiska pooler finns i [SQL Database DTU-baserade resursgränser](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
