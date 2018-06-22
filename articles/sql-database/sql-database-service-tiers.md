---
title: Azure SQL Database servicenivåer | Microsoft Docs
description: Läs mer om servicenivåer för enstaka och pool databaser att tillhandahålla prestandanivåer och lagringsstorlek.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6ca78596ad6c4ed2e14b127fe55a70bd2f55411a
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309892"
---
# <a name="service-tier-purchasing-models-and-resources"></a>Tjänstnivåmallen köp modeller och resurser 

Logiska servrar i [Azure SQL Database](sql-database-technical-overview.md) erbjuder två köp modeller för bearbetning, lagring och i/o-resurser: DTU-baserade inköpsmodell och en vCore-baserade inköpsmodell (förhandsversion). 

> [!NOTE]
> [Hanterade instanser](sql-database-managed-instance.md) erbjuder vCore-baserade inköpsmodell i Azure SQL Database.

Följande tabell och diagram Jämför och jämför dessa två köp modeller.

> [!IMPORTANT]
> VCore-baserade inköpsmodell (förhandsgranskning), se [vCore-baserade inköpsmodell](sql-database-service-tiers-vcore.md)

|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserade modellen|Den här modellen är baserad på ett anpassade mått för bearbetning, lagring och i/o-resurser. Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er](sql-database-what-is-a-dtu.md)?|Bäst för kunder som vill enkla, förkonfigurerade alternativ.| 
|vCore-baserade modellen|Den här modellen kan du skala beräknings-och lagringsresurserna oberoende av varandra. Du kan också använda Azure Hybrid-förmån för SQL Server för att få besparingar.|Bäst för kunder som värdet flexibilitet, kontroll- och insyn.|
||||  

![prismodellen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>vCore-baserade inköpsmodell (förhandsgranskning)

En virtuell kärna representerar logiska Processorn erbjuds med möjlighet att välja mellan generationer av maskinvara. VCore-baserade inköpsmodell (förhandsversion) ger ditt flexibilitet, kontroll, genomskinlighet enskilda resursförbrukning och ett enkelt sätt att översätta krav på lokal arbetsbelastning till molnet. Den här modellen kan du skala beräknings-, minnes- och storage baserat på deras behov av arbetsbelastning. I den vCore-baserade inköpsmodell (förhandsgranskning), kunder kan välja mellan generella och kritiska tjänstnivåer för företag (förhandsversion) för både [enkel databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). 

han vCore-baserade inköpsmodell (förhandsversion) kan du skala beräknings-och lagringsresurser, matchar lokalt prestanda och optimera pris oberoende av varandra. Om den databas eller en elastisk pool förbrukar mer än 300 DTU konvertering till vCore kan minska dina kostnader. Du kan konvertera med hjälp av din API väljer eller Azure-portalen utan avbrott. Dock krävs inte konverteringen. Om DTU-inköpsmodell uppfyller dina företagskrav på prestanda och, ska du fortsätta använda den. Om du vill konvertera från DTU-modellen till vCore-modell, bör du välja prestandanivå med hjälp av följande tumregel: varje 100 DTU i standardnivån kräver minst 1 vCore i generella skiktet; varje 125 DTU i Premium-nivån kräver minst 1 vCore i företag kritisk nivå.

I den vCore-baserade inköpsmodell (förhandsgranskning), kunder som betalar för:
- Beräkna (tjänstnivån + antal vCores + maskinvarusystem) *
- Typ och mängden data och loggfilen lagringsutrymme 
- Antal IOs **
- Säkerhetskopiera lagring (RA-GRS) ** 

\* I den första förhandsversionen Gen 4 logiska processorer är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz processorer.

\*\* Under förhandsgranskning är 7 dagar för säkerhetskopieringar och IOs ledigt.

> [!IMPORTANT]
> Compute-, IOs, data och lagra loggen debiteras per databas eller elastisk pool. Lagring av säkerhetskopior debiteras per varje databas. Mer information om hanterade instans avgifter avser [Azure SQL-hanterade databasinstans](sql-database-managed-instance.md).
> **Region begränsningar:** vCore-baserade inköpsmodell (förhandsgranskning) ännu inte finns i följande områden: Västeuropa, Frankrike Central, Storbritannien, Syd, Storbritannien, Väst och Australien sydost.

## <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodell

Databasen genomströmning Databastransaktionsenheten (DTU) representerar ett blandat mått av CPU, minne, läser och skriver. DTU-baserade inköpsmodell innehåller en uppsättning förinställda paket med beräkningsresurser och ingår lagringsutrymme till enheten olika nivåer av programprestanda. Kunder som föredrar enkelhet ett förkonfigurerade paket och fasta betalningar varje månad kanske DTU-baserade modell passar för deras behov. I DTU-baserade inköpsmodell kunder kan välja mellan **grundläggande**, **Standard**, och **Premium** tjänstnivåer för både [enkel databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Vad är Database Transaction Units (Dtu)?
För en enda Azure SQL-databas på specifika prestandanivåer inom en [tjänstnivån](sql-database-single-database-scale.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av andra databas i Azure-molnet), vilket ger en förutsägbar nivå av prestanda. Mängden resurser beräknas som ett antal Database Transaction Units eller dtu: er och är ett anpassade mått för bearbetning, lagring och i/o-resurser. Förhållandet mellan resurserna har ursprungligen bestäms av ett [OLTP-arbetsbelastning benchmark](sql-database-benchmark-overview.md)och utformade är typiska för verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överskrider mängden någon av dessa resurser, är din dataflödet begränsad – vilket resulterar i långsammare prestanda och timeout. De resurser som används av din arbetsbelastning påverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet och resurserna som används av andra arbetsbelastningar som inte påverkar resurserna som är tillgängliga för din SQL-databas.

![avgränsningsram](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er är mest användbara för att förstå den relativa mängden resurser mellan Azure SQL-databaser på olika prestandanivåer och servicenivåer. Till exempel dubblerade dtu: erna genom att öka prestandanivåerna för en databas är lika med dubblerade uppsättning resurser som är tillgängliga i den databasen. En premium P11-databas med 1 750 DTU:er erbjuder exempelvis 350 gånger mer DTU-beräkningskraft än en Basic-databas med 5 DTU:er.  

För att få en djupare inblick i resursanvändningen (DTU) för din arbetsbelastning, Använd [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) till:

- Identifiera de vanligaste frågorna med CPU/varaktighet/körningar som potentiellt kan anpassas för bättre prestanda. Till exempel en i/o-intensiva fråga kan dra nytta av [i minnet optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänst tjänstnivå och prestandanivå nivå.
- Detaljnivån i information om en fråga kan visa text och historik över resursutnyttjandet.
- Rekommendationer som visar åtgärder som utförs av för åtkomst av prestandajustering [SQL Database Advisor](sql-database-advisor.md).

Du kan ändra [DTU tjänstnivåer](sql-database-service-tiers-dtu.md) när som helst med minimal avbrottstid för ditt program (vanligtvis medelvärdet under fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här scenariot använder du en elastisk pool med ett visst antal edtu: er som ska delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Vad är elastiska Datatransaktionsenheter (edtu: er)?
Snarare än innehåller en särskild uppsättning resurser (dtu: er) som inte kanske alltid behövs för en SQL-databas som alltid är tillgänglig, kan du placera databaser i en [elastisk pool](sql-database-elastic-pool.md) på en SQL Database-server som delar en pool av resurser mellan dessa databaser. Delade resurser i en elastisk pool mäts av elastiska Datatransaktionsenheter eller edtu: er. Elastiska pooler ger en enkel och kostnadseffektiv lösning för att hantera prestandamål för flera databaser med vitt spridda och oförutsägbara användningsmönster. En elastisk pool garanterar resurser inte kan användas av en databas i poolen, medan säkerställt varje databas i poolen alltid har en minimal mängd nödvändiga resurser som är tillgängliga. 

![Introduktion till SQL Database: eDTU:er efter nivå](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En pool ges ett angivet antal edtu: er för ett fast pris. Inom den elastiska poolen har de enskilda databaserna flexibiliteten att skalas automatiskt inom konfigurerade parametrar. En databas större belastning förbrukar mer edtu: er för att uppfylla begäran. Databaser under ljusare belastningar förbrukar mindre edtu: er. Databaser med någon belastning kommer att använda några edtu: er. Genom att etablera resurser för hela poolen är i stället per databas, hanteringsuppgifter förenklade, vilket ger en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan avbrott för databasen och utan att databaserna i den elastiska poolen påverkas. Om eDTU:er sedan inte längre behövs, kan de tas bort från en befintlig pool när som helst. Du kan lägga till eller ta bort databaser i poolen eller begränsa mängden edtu: er som en databas kan använda hårt belastad för att reservera edtu: er för andra databaser. Om en databas förutsägbart under-använder resurser, som du kan flytta från poolen och konfigurera den som en enskild databas med en förutsägbar mängd resurser som krävs.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hur vet jag hur många DTU:er min arbetsbelastning behöver?
Om du vill migrera en befintlig lokal VM-arbetsbelastning eller en VM-arbetsbelastning i SQL Server till Azure SQL Database kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att göra en ungefärlig uppskattning av hur många DTU:er du behöver. Du kan använda för en befintlig Azure SQL Database-arbetsbelastning [SQL Database Query Performance Insight](sql-database-query-performance.md) att förstå din databas resursförbrukning (Dtu) för att få djupare insikter för att optimera din arbetsbelastning. Du kan också använda den [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV att visa resursanvändningen för den senaste timmen. Du kan också katalogvyn [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visar resursanvändningen under de senaste 14 dagarna, men på en lägre återgivningen av fem minuter långa medelvärden.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Hur vet jag om jag kan dra nytta av en elastisk pool med resurser?
Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. Det här mönstret kännetecknas av ett lågt utnyttjande medelvärde med relativt sällan användning toppar för en viss databas. SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Mer information finns i [När ska jag använda en elastisk pool?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Vad händer när jag träffar min maximala dtu: er?
Prestandanivåer kalibrera och regleras för att tillhandahålla de resurser som behövs för att köra din databas arbetsbelastning upp till maxvärdet för din valda nivån/prestandanivå. Om din arbetsbelastning är träffa en CPU/Data IO/logg-i/o-gränser, kommer att fortsätta att ta emot den maximala nivån för resurser som är tillåtna, men du får också sannolikt bättre frågan svarstider. Dessa gränser resulterar inte i fel, men i en långsammare arbetsbelastning, såvida inte fördröjningarna blir så allvarliga att tidsgränsen för frågorna överskrids. Om du når högsta tillåtna antal samtidiga användare sessioner/begäranden (trådar) visas explicit fel. Se [gränserna för Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) information om gränserna inte relaterade till CPU, minne, data-i/o eller transaktionen logg-i/o.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelerar-resultaten till verkligheten databasprestanda
Det är viktigt att förstå att alla prestandamått representativt och som bara är. Transaktionen-priser uppnås med benchmark-programmet inte desamma som de som kan uppnås med andra program. Benchmark omfattar en mängd olika transaktion typer körs mot ett schema som innehåller en uppsättning tabeller och -datatyper. Medan benchmark utnyttjar samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar inte någon specifik klass av databas eller ett program. Målet med benchmark är att tillhandahålla en rimlig guide till den relativa prestandan för en databas som kan förväntas vid skalning uppåt eller nedåt mellan prestandanivåer. I själva verket databaser av olika storlekar och komplexitet, får olika kombinationer av arbetsbelastningar och kommer att svara på olika sätt. Till exempel ett i/o-intensiva program kan träffa IO tröskelvärden snabbare eller ett processorintensiva program kan träffa CPU gränser snabbare. Det finns ingen garanti för att en viss databas skalar på samma sätt som benchmark under ökande belastning.

Benchmark och dess metoder beskrivs i detalj nedan.

### <a name="benchmark-summary"></a>Benchmark-översikt
ASDB mäter prestanda för en blandning av grundläggande databasåtgärder som förekommer oftast i online transaktionsbearbetning (OLTP) arbetsbelastningar. Även om benchmark har utformats med cloud computing-lösningar i åtanke, databasschemat, ifyllning av data och transaktioner är utformade för att vara brett representativ för grundläggande element som används mest i OLTP-arbetsbelastningar.

### <a name="schema"></a>Schema
Schemat är utformat för att ha tillräckligt med olika och komplexitet som stöder en mängd olika åtgärder. Benchmark körs mot en databas som består av sex tabeller. Tabellerna delas in i tre kategorier: fast storlek, skala och växer. Det finns två tabeller i fast storlek. tre skalning tabeller. och en växande tabell. Fast storlek tabeller har ett konstant antal rader. Skalning tabeller har en kardinalitet som är proportionell mot databasens prestanda, men inte ändra under benchmark. Tabellen växande storlek som en skalning tabell på första last, men ändringarna kardinalitet körs benchmark som rader infogas och tas bort.

Schemat innehåller en blandning av datatyper, inklusive heltal, numeriska tecken och datum/tid. Schemat innehåller primära och sekundära nycklarna, men inte alla sekundärnycklar - finns det ingen referensintegritetsbegränsningar mellan tabeller.

Ett program för generering av data genererar data för den ursprungliga databasen. Heltal och numeriska data som genereras med olika strategier. I vissa fall kan distribueras värden slumpmässigt över ett intervall. I annat fall en uppsättning värden är slumpmässigt den omkastade för att säkerställa att en specifik distributionsplats bibehålls. Textfält genereras från en sorterad lista över ord att skapa realistiska söker data.

Databasen är storlek baserat på ”skalfaktor”. Skalfaktor (förkortas SA) anger kardinalitet skalningen och växande tabeller. Som beskrivs nedan i avsnittet användare och Pacing databasens storlek, antal användare och högsta prestanda alla skala i förhållande till varandra.

### <a name="transactions"></a>Transaktioner
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

### <a name="workload-mix"></a>Blandning av arbetsbelastning
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

### <a name="users-and-pacing"></a>Användare och hastighetsstyrningen
Prestandamått arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar för att simulera beteendet för ett antal samtidiga användare. Även om alla anslutningar och transaktioner är datorn som genererats för enkelhetens skull refererar vi till dessa anslutningar som ”användare”. Även om varje användare fungerar oberoende av andra användare, utför alla användare samma cykeln av stegen som visas nedan:

1. Upprätta en databasanslutning.
2. Upprepa tills signal om att avsluta:
   * Välj en transaktion slumpmässigt (från en viktad distributionsplats).
   * Utför den markerade transaktionen och mäta svarstiden.
   * Vänta tills en pacing fördröjning.
3. Stäng anslutningen till databasen.
4. Avsluta.

Pacing fördröjningen (i steg 2c) väljs slumpmässigt, men med en distributionsplats som har 1.0 sekund i genomsnitt. Därmed kan varje användare i genomsnitt generera maximalt en transaktion per sekund.

### <a name="scaling-rules"></a>Skalning regler
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

### <a name="measurement-duration"></a>Mätning varaktighet
En giltig benchmark-körning kräver minst en timme stabilitet mätning varaktighet.

### <a name="metrics"></a>Mått
Viktiga mått i benchmark är genomflöde och svarstid.

* Dataflödet är det grundläggande prestandamåttet i benchmark. Genomströmning rapporteras i transaktioner per i-tid, inventering av alla transaktionstyper av.
* Svarstiden är ett mått på prestanda, förutsägbarhet. Tidsbegränsning för svar varierar beroende på typ av tjänster med högre klasser av tjänsten som har en strängare svar tiden som krävs, enligt nedan.

| Tjänsteklass | Genomströmning mått | Tiden som krävs för svaret |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95: e percentilen 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen 1.0 sekunder |
| Basic |Transaktioner per timme |80: e percentilen 2.0 sekunder |

## <a name="next-steps"></a>Nästa steg

- VCore-baserade inköpsmodell (förhandsgranskning), se [vCore-baserade inköpsmodell](sql-database-service-tiers-vcore.md)
- DTU-baserade inköpsmodell finns [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md).