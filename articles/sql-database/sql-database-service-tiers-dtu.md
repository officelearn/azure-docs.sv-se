---
title: Azure SQL Database-servicenivåerna - DTU | Microsoft Docs
description: Läs om tjänstnivåer för en enskild och databaser i en pool att tillhandahålla compute storlekar och lagringsstorlekar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6319deb36088317cb289134b7068720e97cb10b7
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507662"
---
# <a name="dtu-based-service-tiers"></a>DTU-baserade tjänstnivåer

DTU-baserade tjänstnivåer särskiljs med hjälp av olika storlekar med en fast mängd lagringsutrymme, fast kvarhållningsperiod för säkerhetskopior och fast pris. Alla tjänstnivåer ger flexibilitet för att ändra instansstorlekarna utan avbrott. Enkla databaser och elastiska pooler faktureras per timme baserat på tjänstnivå och beräkningsstorleken.

> [!IMPORTANT]
> SQL Database Managed Instance är stöder för närvarande i förhandsversionen inte en DTU-baserade inköpsmodellen. Mer information finns i [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Läs om hur vCore-baserade tjänstnivåer [vCore-baserade tjänstnivåer](sql-database-service-tiers-vcore.md). Information om hur man skiljer DTU-baserade tjänstnivåer och vCore-baserade tjänstnivåer finns i [Azure SQL Database köpa modeller](sql-database-service-tiers.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Jämför de DTU-baserade tjänstnivåerna

Välja tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.

||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:|
|Målarbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion||
|SLA för drifttid|99,99 %|99,99 %|99,99 %|Ej tillämpligt i förhandsversionen|
|Kvarhållning av säkerhetskopior|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medel, hög|
|I/o-genomströmning (ungefärlig) |2.5 IOPS per DTU| 2.5 IOPS per DTU | 48 IOPS per DTU|
|I/o-svarstid (ungefärlig)|5 ms (läsa), 10 ms (skriva)|5 ms (läsa), 10 ms (skriva)|2 ms (läsa/skriva)|
|Kolumnlagringsindexering |Gäller inte|S3 och senare|Stöds|
|Minnesintern OLTP|Gäller inte|Gäller inte|Stöds|
|||||

> [!NOTE]
> Du kan få en kostnadsfri Azure SQL-databas på Basic-tjänstnivå tillsammans med ett kostnadsfritt Azure-konto att utforska Azure. Mer information finns i [skapa en hanterad molndatabas med ditt kostnadsfria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Enkel databas DTU och Lagringsgränser

Compute-storlekar uttrycks i Databastransaktionsenheter (dtu: er) för enskilda databaser och elastiska Databastransaktionsenheter (edtu: er) för elastiska pooler. Mer information om dtu: er och edtu: er finns i [DTU-baserade inköpsmodellen](sql-database-service-tiers.md#dtu-based-purchasing-model)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Lagringsstorlek | 2 GB | 1 TB | 4 TB  |
| Maximala dtu: er | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastisk pool-eDTU-, lagrings- och databas i pool gränser

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: | --: |
| Maximala lagringsstorleken per databas  | 2 GB | 1 TB | 1 TB |
| Maximala lagringsstorleken per pool | 156 GB | 4 TB | 4 TB |
| Högsta edtu: er per databas | 5 | 3000 | 4000 |
| Högsta edtu: er per pool | 1600 | 3000 | 4000 |
| Högsta antal databaser per pool | 500  | 500 | 100 |
||||||

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom följande: USA, västra centrala, Kina, östra, centrala USDoDCentral, Tyskland, USDoDEast USA-förvaltad region sydväst, nordöstra USGov Iowa, Tyskland, Kina, norra. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU-Benchmark

Fysiska egenskaper (processor, minne, i/o) som är kopplade till varje DTU-mått kalibreras med hjälp av en benchmark som simulerar verkliga databas-arbetsbelastning.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelera-resultaten till verkliga databasprestanda

Det är viktigt att förstå att alla prestandamått är representativt och vägledande endast. Transaktionspriser uppnås med benchmark-programmet kan inte samma som de som kan uppnås med andra program. Benchmark består av en samling med annan transaktion typer kör mot ett schema som innehåller en mängd tabeller och datatyper. Medan prestandamått utövar samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar inte någon specifik klass av databas eller ett program. Målet med prestandamått är att tillhandahålla en rimlig guide till den relativa prestandan för en databas som kan förväntas vid skalning upp eller ned mellan storlekar. I verkligheten kan databaser är av olika storlekar och komplexitet, stöta på olika kombinationer av arbetsbelastningar och svarar på olika sätt. Till exempel ett i/o-intensiva program kan träffa tröskelvärden för i/o tidigare eller ett CPU-intensiva program kan träffa CPU-gränser snabbare. Det finns ingen garanti för att en viss databas skalas på samma sätt som prestandamått under ökande belastning.

Prestandamått och dess metoder beskrivs i detalj nedan.

### <a name="benchmark-summary"></a>Benchmark-översikt

ASDB mäter prestanda för en blandning av grundläggande databasåtgärder som förekommer oftast i online transaktionsbearbetning (OLTP) arbetsbelastningar. Även om prestandamått är utformad med molnbaserad databehandling i åtanke, databasschemat, ifyllnad av data och transaktioner som har utformats för att vara brett representativ för de grundläggande delarna som används mest i OLTP-arbetsbelastningar.

### <a name="schema"></a>Schema

Schemat har utformats för att ha tillräckligt med variationen och komplexiteten för en mängd olika åtgärder. Benchmark körs mot en databas som består av sex tabeller. Tabeller är indelade i tre kategorier: fast storlek, skalning och växer. Det finns två tabeller för fast storlek. tre skalning tabeller. och en växande tabell. Tabeller med fast storlek har ett konstant antal rader. Skalning tabeller har en kardinalitet som står i proportion till databasens prestanda, men inte ändras under prestandamått. Tabellen växande storlek som en skalning tabeller på första men kardinalitet ändringarna körs prestandamått som rader infogas och raderas.

Schemat innehåller en blandning av datatyper, inklusive heltal, numeriska tecken och datum/tid. Schemat innehåller primära och sekundära nycklarna, men inte alla sekundärnycklar – det vill säga det finns inga referensintegritetsbegränsningar mellan tabeller.

Ett program för generering av data genererar data för den ursprungliga databasen. Heltal och numeriska data genereras med olika strategier. I vissa fall kan distribueras värden slumpmässigt över en rad. I andra fall kan en uppsättning värden är slumpmässigt den omkastade för att säkerställa att en specifik distribution upprätthålls. Textfält genereras från en sorterad lista över ord att producera realistisk söker data.

Databasen är storlek baserat på en ”skalfaktor”. Skalningsfaktorn (förkortas SF) avgör Kardinaliteten för att skala och växande tabeller. Enligt beskrivningen nedan i avsnittet användare och Pacing, databasens storlek, antalet användare och maximala prestanda alla skala i förhållande till varandra.

### <a name="transactions"></a>Transaktioner

Arbetsbelastningen består av nio transaktionstyper som visas i tabellen nedan. Varje transaktion är utformad för att markera en viss uppsättning egenskaper i database engine och system för maskinvara med hög kontrast från andra transaktioner. Den här metoden gör det enklare att utvärdera effekten av olika komponenter för systemets prestanda. Till exempel genererar ”läsa” intensiv transaktionen ett stort antal läsåtgärder från disken.

| Transaktionstyp | Beskrivning |
| --- | --- |
| Läsa Lite |VÄLJ; minnesinterna; skrivskyddad |
| Läs Medium |VÄLJ; huvudsakligen i minnet; skrivskyddad |
| Läs aktiverat |VÄLJ; huvudsakligen inte i minnet; skrivskyddad |
| Uppdatera Lite |UPPDATERA; minnesinterna; skrivskyddad |
| Uppdatera aktiverat |UPPDATERA; huvudsakligen inte i minnet; skrivskyddad |
| Infoga Lite |INFOGA. minnesinterna; skrivskyddad |
| Infoga aktiverat |INFOGA. huvudsakligen inte i minnet; skrivskyddad |
| Ta bort |TA BORT. blandning av i minnet och inte i minnet; skrivskyddad |
| CPU-intensiv |VÄLJ; minnesinterna; relativt tung CPU-belastning. skrivskyddad |

### <a name="workload-mix"></a>Blandning av arbetsbelastning

Transaktioner väljs slumpmässigt bland en viktad distribution med följande övergripande blandning. Den övergripande kombination har en Läs/Skriv-förhållande på cirka 2:1.

| Transaktionstyp | % av blandning |
| --- | --- |
| Läsa Lite |35 |
| Läs Medium |20 |
| Läs aktiverat |5 |
| Uppdatera Lite |20 |
| Uppdatera aktiverat |3 |
| Infoga Lite |3 |
| Infoga aktiverat |2 |
| Ta bort |2 |
| CPU-intensiv |10 |

### <a name="users-and-pacing"></a>Användare och hastighetsstyrningen

Benchmark-arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar till simulera beteendet för ett antal samtidiga användare. Även om alla anslutningar och transaktioner är datorn som genereras för enkelhetens skull kallar vi dessa anslutningar ”användare”. Även om varje användare fungerar oberoende av alla andra användare, utför alla användare samma cykeln av stegen som visas nedan:

1. Upprätta en databasanslutning.
2. Upprepa tills signal om att avsluta:
   - Välj en transaktion slumpmässigt (från en viktad distribution).
   - Utför den valda transaktionen och mäta svarstiden.
   - Vänta tills en pacing fördröjning.
3. Stäng anslutningen till databasen.
4. Avsluta.

Pacing fördröjningen (i steg 2c) väljs slumpmässigt, men med en distribution som har ett genomsnitt av 1.0 sekund. Därmed kan varje användare i genomsnitt generera högst en transaktion per sekund.

### <a name="scaling-rules"></a>Skalningsregler

Antalet användare som bestäms av databasens storlek (i skalfaktor enheter). Det finns en användare för varje fem skalfaktor enheter. På grund av pacing fördröjningen, kan en användare skapa högst en transaktion per sekund, i genomsnitt.

Till exempel en-skalfaktor av 500 (SF = 500) databasen kommer att ha 100 användare och kan högst 100 TPS. För att driva en högre TPS kräver frekvens fler användare och en större databas.

### <a name="measurement-duration"></a>Varaktighet för mätning

En giltig benchmark-körning kräver en stabil mätning varaktighet på minst en timme.

### <a name="metrics"></a>Mått

Viktiga mått i prestandamått är dataflöde och svarstid.

- Dataflödet är det grundläggande prestandamått i prestandamått. Dataflöde rapporteras i transaktioner per i-tid, räkna alla transaktionstyper.
- Svarstiden är ett mått på förutsägbara prestanda. Tidsbegränsning för svar varierar med tjänsteklass med högre klasser av tjänsten som har strängare svar tid krav, enligt nedan.

| Tjänsteklass | Mått för dataflöde | Tidsåtgången för svar |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95: e percentilen vid 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen vid 1.0 sekunder |
| Basic |Transaktioner per timme |80: e percentilen vid 2.0 sekunder |

## <a name="next-steps"></a>Nästa steg

- För att få information om specifika storlekar och lagring som kan användas för enskilda databaser, se [SQL Database DTU-baserade resursbegränsningar för enskilda databaser](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- För att få information om specifika storlekar och lagring som kan användas för elastiska pooler, se [SQL Database DTU-baserade resursbegränsningar](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
