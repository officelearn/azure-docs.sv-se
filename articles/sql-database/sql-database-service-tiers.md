---
title: Azure SQL Database köpa modeller | Microsoft Docs
description: Lär dig mer om hur du köper modell för Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: f40fe3da6874d8656c9c0a0ddce9fed602cb25f9
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091923"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Azure SQL Database köpa modeller och resurser 

Logiska servrar i [Azure SQL Database](sql-database-technical-overview.md) finns i två inköpschef modeller för beräkning, lagring och IO-resurser: en DTU-baserade inköpsmodellen och en vCore-baserade inköpsmodellen. 

> [!NOTE]
> [Hanterade instanser](sql-database-managed-instance.md) i Azure SQL Database erbjuder den vCore-baserade inköpsmodellen.

Följande tabell och diagrammet Jämför och kontrastera dessa två inköpschef modeller.

> [!IMPORTANT]
> VCore-baserade inköpsmodellen Se [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md)

|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad modell|Den här modellen är baserad på ett paketerat mått av beräkning, lagring och IO-resurser. Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er finns i [vad är dtu: er och edtu: er](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Bäst för kunder som vill ha enkel, förkonfigurerade alternativ.| 
|vCore-baserad modell|Den här modellen kan du oberoende skala beräknings- och lagringsresurser. Du kan också använda Azure Hybrid-förmånen för SQL Server för att få kostnadsbesparingar.|Bäst för kunder som värde flexibilitet, kontroll och transparens.|
||||  

![Prismodell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>vCore-baserade inköpsmodellen 

En virtuell kärna representerar en logisk CPU med möjligheten att välja mellan av maskinvara. Den vCore-baserade inköpsmodellen ger den flexibilitet, kontroll, transparens av enskild resursförbrukning och ett enkelt sätt att överföra lokala arbetsbelastningskrav till molnet. Den här modellen kan du skala beräkning, minne och lagring utifrån deras arbetsbelastningsbehov. I den vCore-baserade inköpsmodellen, kunderna kan välja mellan generell användning och kritiska tjänstnivåer för företag för både [enkla databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). 

Den vCore-baserade inköpsmodellen kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera pris. Om din databas eller elastisk pool förbrukar mer än 300 DTU-konvertering till vCore minska dina kostnader. Du kan konvertera med hjälp av ditt API föredrar eller med hjälp av Azure-portalen utan avbrott. Dock krävs inte konverteringen. Om den DTU-inköpsmodellen uppfyller dina krav på prestanda och företag, bör du fortsätta använda den. Om du vill konvertera från DTU-modellen till vCore-modellen, bör du välja prestandanivån med hjälp av följande tumregel: varje 100 dtu: er i Standard-nivån kräver minst 1 vCore i nivån generell användning; varje 125 DTU på premiumnivån kräver minst 1 vCore i nivån affärskritisk.

I den vCore-baserade inköpsmodellen, kunder som betalar för:
- Compute (tjänstnivå + antal virtuella kärnor + maskinvarusystem) *
- Typ och mängden lagringsutrymme för data och loggfiler 
- Antal IOs **
- Säkerhetskopiera lagring (RA-GRS) ** 

\* I den första offentliga förhandsversionen kan den Gen 4 logiska CPU baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer.

\*\* 7 dagars säkerhetskopior och IOs är kostnadsfria i förhandsversionen.

> [!IMPORTANT]
> Beräknings-, IOs, data och logglagring debiteras per databas eller elastisk pool. Lagring för säkerhetskopior debiteras per varje databas. Information av avgifter för hanterad instans i [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Region begränsningar:** vCore-baserade inköpsmodellen ännu inte finns tillgänglig i följande regioner: västra Europa, Frankrike, centrala, Storbritannien, södra, Storbritannien, västra och Australien, sydöstra Australien.

## <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

Database Throughput Unit (DTU) representerar ett blandat mått på processor, minne, läser och skriver. DTU-baserade inköpsmodellen erbjuder en uppsättning förkonfigurerade paket av beräkningsresurser och inkluderat lagringsutrymme för att driva olika nivåer av programprestanda. Kunder som föredrar enkelheten med ett förkonfigurerat paket och fasta betalningar varje månad, kanske den DTU-baserade modellen passar deras behov. I DTU-baserade inköpsmodellen kunderna kan välja mellan **grundläggande**, **Standard**, och **Premium** tjänstnivåer för både [enkla databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Vad är Databastransaktionsenheter (dtu: er)?
För en enskild Azure SQL-databas på en specifik prestandanivå på en [tjänstnivå](sql-database-single-database-scale.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av någon annan databas i Azure-molnet), vilket ger en förutsägbar nivån på prestanda. Mängden resurser beräknas som ett antal Databastransaktionsenheter eller dtu: er och är ett paketerat mått av beräkning, lagring och IO-resurser. Förhållandet mellan här resursernas fördelning har ursprungligen bestämts av en [OLTP-benchmarkarbetsbelastning](sql-database-benchmark-overview.md), utformade för att representera verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överstiger mängden av någon av dessa resurser, är ditt dataflöde begränsade - resulterande långsammare prestanda och tidsgränser. De resurser som används av din arbetsbelastning inverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet och de resurser som används av andra arbetsbelastningar inverkar inte resurserna som är tillgängliga för din SQL-databas.

![avgränsningsruta](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er som är mest användbara för att förstå den relativa mängden resurser mellan tjänstnivåerna och Azure SQL-databaser på olika prestandanivåer. Till exempel motsvarar fördubbling av dtu: erna genom att öka prestandanivå för en databas fördubbling av uppsättningen resurser som är tillgängliga för databasen. En premium P11-databas med 1 750 DTU:er erbjuder exempelvis 350 gånger mer DTU-beräkningskraft än en Basic-databas med 5 DTU:er.  

Om du vill få bättre insikt i resursförbrukning (DTU) för din arbetsbelastning kan använda [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) till:

- Identifiera de viktigaste frågorna med CPU/varaktighet/körning antalet som potentiellt kan anpassas för bättre prestanda. Till exempel en i/o-intensiva fråga kan dra nytta av användningen av [InMemory-optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänstnivå och prestandanivå servicenivå.
- Granska nedåt i detaljerna för en fråga kan visa text och historik över resursanvändningen.
- Rekommendationer som visar åtgärder som utförs av för åtkomst av prestandajustering [SQL Database Advisor](sql-database-advisor.md).

Du kan ändra [DTU tjänstnivåerna](sql-database-service-tiers-dtu.md) när som helst med minimalt avbrott för ditt program (normalt mindre än fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. Det här scenariot kan använda du en elastisk pool med ett visst antal edtu: er som delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Vad är elastiska Databastransaktionsenheter (edtu: er)?
Snarare än ger en dedikerad uppsättning resurser (dtu: er) som inte behövs kanske alltid för en SQL-databas som alltid är tillgänglig, kan du placera databaser i en [elastisk pool](sql-database-elastic-pool.md) på en SQL Database-server som delar en pool av resurser mellan dessa databaser. De delade resurserna i en elastisk pool mäts som elastiska Databastransaktionsenheter eller edtu: er. Elastiska pooler erbjuder en enkel kostnadseffektiv lösning för att hantera prestandamål för flera databaser med mycket varierande och oförutsägbara användningsmönster. En elastisk pool garanterar resurser inte kan användas av en databas i poolen, även att se till att varje databas i poolen alltid har en minimal mängd nödvändiga resurser som är tillgängliga. 

![Introduktion till SQL Database: eDTU:er efter nivå](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En pool tilldelas ett bestämt antal edtu: er till ett fast pris. Inom den elastiska poolen har de enskilda databaserna flexibiliteten att skalas automatiskt inom konfigurerade parametrar. En databas under tyngre belastning förbrukar fler edtu: er för att möta efterfrågan. Databaser under ljusare laster förbrukar färre edtu: er. Databaser med utan belastning förbrukar inga edtu: er. Genom att etablera resurser för hela poolen, snarare än per databas, blir hanteringsuppgifter förenklade, vilket ger en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan avbrott för databasen och utan att databaserna i den elastiska poolen påverkas. Om eDTU:er sedan inte längre behövs, kan de tas bort från en befintlig pool när som helst. Du kan lägga till eller ta bort databaser i poolen eller begränsa antalet edtu: er en databas kan använda vid hög belastning för att reservera edtu: er för andra databaser. Om en databas förutsägbart underutnyttjar resurser, som du kan flytta från poolen och konfigurera den som en enkel databas med en förutsägbara mängd resurser som krävs.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hur vet jag hur många DTU:er min arbetsbelastning behöver?
Om du vill migrera en befintlig lokal VM-arbetsbelastning eller en VM-arbetsbelastning i SQL Server till Azure SQL Database kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att göra en ungefärlig uppskattning av hur många DTU:er du behöver. Du kan använda för en befintlig Azure SQL Database-arbetsbelastning [SQL Database Query Performance Insight](sql-database-query-performance.md) att förstå databasens resursförbrukning (dtu: er) för att få bättre insikt för att optimera din arbetsbelastning. Du kan också använda den [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV visa resursförbrukning för den senaste timmen. Du kan också katalogvyn [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visar resursförbrukning för de senaste 14 dagarna, men med lägre tillförlitlighet med medelvärden på fem minuter.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Hur vet jag om jag kan dra nytta av en elastisk pool med resurser?
Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av ett medelvärde för låg användning med relativt ovanliga användningstoppar. SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Mer information finns i [När ska jag använda en elastisk pool?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Vad händer när jag når min maximala dtu: er?
Prestandanivåerna kalibreras och regleras för att tillhandahålla de resurser som behövs för att din databasarbetsbelastning upp till maxvärdet för din valda tjänstnivå/prestandanivå. Om din arbetsbelastning når någon av processor, Data IO eller logg-i/o-gränserna, fortsätter du att ta emot den maximala nivån för resurser som är tillåtna, men du får också sannolikt ökad fråga svarstider. Dessa gränser resulterar inte i fel, men i en långsammare arbetsbelastning, såvida inte fördröjningarna blir så allvarliga att tidsgränsen för frågorna överskrids. Om du når de högsta tillåtna samtidiga användarsessioner/begäranden (arbetstrådar) visas explicita fel. Se [Azure SQL Database-resursgränser]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) information om resursbegränsningar som inte rör CPU, minne, data-IO eller logg-IO-transaktion.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelera-resultaten till verkliga databasprestanda
Det är viktigt att förstå att alla prestandamått är representativt och vägledande endast. Transaktionspriser uppnås med benchmark-programmet kan inte samma som de som kan uppnås med andra program. Benchmark består av en samling med annan transaktion typer kör mot ett schema som innehåller en mängd tabeller och datatyper. Medan prestandamått utövar samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar inte någon specifik klass av databas eller ett program. Målet med prestandamått är att tillhandahålla en rimlig guide till den relativa prestandan för en databas som kan förväntas vid skalning upp eller ned mellan prestandanivåer. I verkligheten kan databaser är av olika storlekar och komplexitet, stöta på olika kombinationer av arbetsbelastningar och svarar på olika sätt. Till exempel ett i/o-intensiva program kan träffa tröskelvärden för i/o tidigare eller ett CPU-intensiva program kan träffa CPU-gränser snabbare. Det finns ingen garanti för att en viss databas skalas på samma sätt som prestandamått under ökande belastning.

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
   * Välj en transaktion slumpmässigt (från en viktad distribution).
   * Utför den valda transaktionen och mäta svarstiden.
   * Vänta tills en pacing fördröjning.
3. Stäng anslutningen till databasen.
4. Avsluta.

Pacing fördröjningen (i steg 2c) väljs slumpmässigt, men med en distribution som har ett genomsnitt av 1.0 sekund. Därmed kan varje användare i genomsnitt generera högst en transaktion per sekund.

### <a name="scaling-rules"></a>Skalningsregler
Antalet användare som bestäms av databasens storlek (i skalfaktor enheter). Det finns en användare för varje fem skalfaktor enheter. På grund av pacing fördröjningen, kan en användare skapa högst en transaktion per sekund, i genomsnitt.

Till exempel en-skalfaktor av 500 (SF = 500) databasen kommer att ha 100 användare och kan högst 100 TPS. För att driva en högre TPS kräver frekvens fler användare och en större databas.

Tabellen nedan visar hur många användare som faktiskt varar i varje servicenivå för tjänstnivå och prestandanivå.

| Tjänstnivå (prestandanivå) | Användare | Databasstorlek |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Varaktighet för mätning
En giltig benchmark-körning kräver en stabil mätning varaktighet på minst en timme.

### <a name="metrics"></a>Mått
Viktiga mått i prestandamått är dataflöde och svarstid.

* Dataflödet är det grundläggande prestandamått i prestandamått. Dataflöde rapporteras i transaktioner per i-tid, räkna alla transaktionstyper.
* Svarstiden är ett mått på förutsägbara prestanda. Tidsbegränsning för svar varierar med tjänsteklass med högre klasser av tjänsten som har strängare svar tid krav, enligt nedan.

| Tjänsteklass | Mått för dataflöde | Tidsåtgången för svar |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95: e percentilen vid 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen vid 1.0 sekunder |
| Basic |Transaktioner per timme |80: e percentilen vid 2.0 sekunder |

## <a name="next-steps"></a>Nästa steg

- VCore-baserade inköpsmodellen Se [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md)
- DTU-baserade inköpsmodellen Se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md).
