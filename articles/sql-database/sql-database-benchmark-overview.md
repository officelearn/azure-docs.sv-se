---
title: Azure SQL Database DTU benchmark-översikt
description: Det här avsnittet beskriver Azure SQL Database Benchmark används i mätning av prestanda i Azure SQL Database.
services: sql-database
author: jan-eng
manager: jhubbard
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: 0dae04ab3de16e2ef51134ec6979c6c289a6da3f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-dtu-benchmark-overview"></a>Azure SQL Database DTU benchmark-översikt
## <a name="overview"></a>Översikt
I DTU-baserade inköpsmodell Microsoft Azure SQL Database erbjuder tre [tjänstnivåer](sql-database-service-tiers.md) med flera prestandanivåer. Varje prestandanivå innehåller en ökande uppsättning resurser och ström, utformats för att ge allt högre genomströmning.

> [!IMPORTANT]
> Azure SQL Database stöder nu också en vCore-baserade inköpsmodell (förhandsversion). Mer information finns i [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview).

Det är viktigt för att kunna beräkna hur ökande kraften i varje prestandanivå översätter till ökad databasprestanda. Om du vill göra det här Microsoft har utvecklat Azure SQL Database Benchmark (ASDB). Benchmark utför en blandning av grundläggande åtgärder som finns i alla OLTP-arbetsbelastningar. Vi mäter genomflödet uppnås för databaser som körs i varje prestandanivå.

Resurser och kraften i varje servicenivå för tjänstnivå och prestandanivå uttrycks i [Database Transaction Units (Dtu)](sql-database-what-is-a-dtu.md). Dtu: er ger dig ett sätt att beskriva den relativa kapaciteten för prestandanivå baserat på ett blandat mått av CPU, minne, läser och skriver priser som erbjuds av varje prestandanivå. Dubblerade DTU klassificering av en databas som är lika med dubblerade power för databasen. Benchmark gör det möjligt för oss att utvärdera effekten på databasprestanda ökande ström som erbjuds av varje prestandanivå genom faktiska databasåtgärder vid skalning databasens storlek, antal användare, och transaktionen klassificeras i proportion till den resurserna i databasen.

Genom att uttrycka genomflödet av grundläggande tjänstnivån med transaktioner per timme, Standard-tjänstnivå med transaktioner per minut och premiumnivån med transaktioner per sekund, gör det det enklare att snabbt relatera prestanda risken för varje tjänstnivå kraven för ett program.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelerar-resultaten till verkligheten databasprestanda
Det är viktigt att förstå att ASDB, precis som alla prestandamått representativt och som endast är. Transaktionen-priser uppnås med benchmark-programmet inte desamma som de som kan uppnås med andra program. Benchmark omfattar en mängd olika transaktion typer körs mot ett schema som innehåller en uppsättning tabeller och -datatyper. Medan benchmark utnyttjar samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar inte någon specifik klass av databas eller ett program. Målet med benchmark är att tillhandahålla en rimlig guide till den relativa prestandan för en databas som kan förväntas vid skalning uppåt eller nedåt mellan prestandanivåer. I själva verket databaser av olika storlekar och komplexitet, får olika kombinationer av arbetsbelastningar och kommer att svara på olika sätt. Till exempel ett i/o-intensiva program kan träffa IO tröskelvärden snabbare eller ett processorintensiva program kan träffa CPU gränser snabbare. Det finns ingen garanti för att en viss databas skalar på samma sätt som benchmark under ökande belastning.

Benchmark och dess metoder beskrivs i detalj nedan.

## <a name="benchmark-summary"></a>Benchmark-översikt
ASDB mäter prestanda för en blandning av grundläggande databasåtgärder som förekommer oftast i online transaktionsbearbetning (OLTP) arbetsbelastningar. Även om benchmark har utformats med cloud computing-lösningar i åtanke, databasschemat, ifyllning av data och transaktioner är utformade för att vara brett representativ för grundläggande element som används mest i OLTP-arbetsbelastningar.

## <a name="schema"></a>Schema
Schemat är utformat för att ha tillräckligt med olika och komplexitet som stöder en mängd olika åtgärder. Benchmark körs mot en databas som består av sex tabeller. Tabellerna delas in i tre kategorier: fast storlek, skala och växer. Det finns två tabeller i fast storlek. tre skalning tabeller. och en växande tabell. Fast storlek tabeller har ett konstant antal rader. Skalning tabeller har en kardinalitet som är proportionell mot databasens prestanda, men inte ändra under benchmark. Tabellen växande storlek som en skalning tabell på första last, men ändringarna kardinalitet körs benchmark som rader infogas och tas bort.

Schemat innehåller en blandning av datatyper, inklusive heltal, numeriska tecken och datum/tid. Schemat innehåller primära och sekundära nycklarna, men inte alla sekundärnycklar - finns det ingen referensintegritetsbegränsningar mellan tabeller.

Ett program för generering av data genererar data för den ursprungliga databasen. Heltal och numeriska data som genereras med olika strategier. I vissa fall kan distribueras värden slumpmässigt över ett intervall. I annat fall en uppsättning värden är slumpmässigt den omkastade för att säkerställa att en specifik distributionsplats bibehålls. Textfält genereras från en sorterad lista över ord att skapa realistiska söker data.

Databasen är storlek baserat på ”skalfaktor”. Skalfaktor (förkortas SA) anger kardinalitet skalningen och växande tabeller. Som beskrivs nedan i avsnittet användare och Pacing databasens storlek, antal användare och högsta prestanda alla skala i förhållande till varandra.

## <a name="transactions"></a>Transaktioner
Arbetsbelastningen består av nio transaktionstyper som visas i tabellen nedan. Varje transaktion är utformad för att fokusera på en viss uppsättning egenskaper i database engine och system maskinvaran, med hög kontrast från andra transaktioner. Den här metoden gör det lättare att utvärdera effekten av olika komponenter för allmänna prestandan. Transaktionen ”Läs tunga” producerar till exempel ett stort antal läsåtgärder från disken.

| Transaktionstyp | Beskrivning |
| --- | --- |
| Läs Lite |VÄLJ; i minnet; skrivskyddad |
| Läs medel |VÄLJ; mestadels i minnet; skrivskyddad |
| Läs aktiverat |VÄLJ; oftast inte i minnet; skrivskyddad |
| Uppdatera Lite |UPPDATERA; i minnet; skrivskyddad |
| Uppdatera aktiverat |UPPDATERA; oftast inte i minnet; skrivskyddad |
| Infoga Lite |INFOGA. i minnet; skrivskyddad |
| Infoga aktiverat |INFOGA. oftast inte i minnet; skrivskyddad |
| Ta bort |TA BORT. blandning av i minnet och inte i minnet; skrivskyddad |
| CPU-aktiverat |VÄLJ; i minnet; relativt hög CPU-belastning; skrivskyddad |

## <a name="workload-mix"></a>Blandning av arbetsbelastning
Transaktioner väljs slumpmässigt från en viktad distributionsplats med följande övergripande blandning. Övergripande blandning har ett förhållande för läsning och skrivning på cirka 2:1.

| Transaktionstyp | % av blandning |
| --- | --- |
| Läs Lite |35 |
| Läs medel |20 |
| Läs aktiverat |5 |
| Uppdatera Lite |20 |
| Uppdatera aktiverat |3 |
| Infoga Lite |3 |
| Infoga aktiverat |2 |
| Ta bort |2 |
| CPU-aktiverat |10 |

## <a name="users-and-pacing"></a>Användare och hastighetsstyrningen
Prestandamått arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar för att simulera beteendet för ett antal samtidiga användare. Även om alla anslutningar och transaktioner är datorn som genererats för enkelhetens skull refererar vi till dessa anslutningar som ”användare”. Även om varje användare fungerar oberoende av andra användare, utför alla användare samma cykeln av stegen som visas nedan:

1. Upprätta en databasanslutning.
2. Upprepa tills signal om att avsluta:
   * Välj en transaktion slumpmässigt (från en viktad distributionsplats).
   * Utför den markerade transaktionen och mäta svarstiden.
   * Vänta tills en pacing fördröjning.
3. Stäng anslutningen till databasen.
4. Avsluta.

Pacing fördröjningen (i steg 2c) väljs slumpmässigt, men med en distributionsplats som har 1.0 sekund i genomsnitt. Därmed kan varje användare i genomsnitt generera maximalt en transaktion per sekund.

## <a name="scaling-rules"></a>Skalning regler
Antalet användare som bestäms av databasens storlek (i skalfaktor enheter). Det finns en användare för varje fem skalfaktor enheter. På grund av pacing fördröjningen kan en användare skapa maximalt en transaktion per sekund i genomsnitt.

Till exempel en-skalfaktor 500 (SA = 500) databasen har 100 användare och kan uppnå högst 100 Transaktionsprogram. Om du vill köra en högre Transaktionsprogram kräver frekvens fler användare och en större databas.

Tabellen nedan visar hur många användare som faktiskt råkat ut för varje servicenivå för tjänstnivå och prestandanivå.

| Tjänstnivån (prestandanivå) | Användare | Databasstorlek |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1-GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

## <a name="measurement-duration"></a>Mätning varaktighet
En giltig benchmark-körning kräver minst en timme stabilitet mätning varaktighet.

## <a name="metrics"></a>Mått
Viktiga mått i benchmark är genomflöde och svarstid.

* Dataflödet är det grundläggande prestandamåttet i benchmark. Genomströmning rapporteras i transaktioner per i-tid, inventering av alla transaktionstyper av.
* Svarstiden är ett mått på prestanda, förutsägbarhet. Tidsbegränsning för svar varierar beroende på typ av tjänster med högre klasser av tjänsten som har en strängare svar tiden som krävs, enligt nedan.

| Tjänsteklass | Genomströmning mått | Tiden som krävs för svaret |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95: e percentilen 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen 1.0 sekunder |
| Basic |Transaktioner per timme |80: e percentilen 2.0 sekunder |

## <a name="conclusion"></a>Sammanfattning
Azure SQL Database Benchmark mäter den relativa prestandan för Azure SQL Database köra inom intervallet för tillgängliga servicenivåer och prestandanivåer. Benchmark utför en blandning av grundläggande databasåtgärder som förekommer oftast i online transaktionsbearbetning (OLTP) arbetsbelastningar. Genom att mäta faktiska prestanda ger benchmark ett mer beskrivande bedömning av påverkan på dataflöde för att ändra prestandanivå än vad som är möjligt genom att bara lista de resurser som tillhandahålls av varje nivå, till exempel CPU-hastighet, minne och IOPS. I framtiden kommer fortsätter vi att utvecklas prestandamått för att utöka sitt omfång och expandera den informationen.

## <a name="resources"></a>Resurser
[Introduktion till SQL-databas](sql-database-technical-overview.md)

[Servicenivåer och prestandanivåer](sql-database-service-tiers.md)

[Prestandaråd för enskilda databaser](sql-database-performance-guidance.md)
