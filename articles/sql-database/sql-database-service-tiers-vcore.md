---
title: Azure SQL Database-tjänsten - vCore | Microsoft Docs
description: Läs mer om servicenivåer för enstaka och pool databaser att tillhandahålla prestandanivåer och lagringsstorlek.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: be5ecfdd4465d721dee49c4963cb2267b2b0a40a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>vCore-baserade inköpsmodell för Azure SQL Database (förhandsgranskning)

[Azure SQL Database](sql-database-technical-overview.md) erbjuder två köp modeller för bearbetning, lagring och i/o-resurser: DTU-baserade inköpsmodell och en vCore-baserade inköpsmodell (förhandsversion). Följande tabell och diagram Jämför och jämför dessa två köp modeller.

> [!IMPORTANT]
> DTU-baserade inköpsmodell finns [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md).


|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserade modellen|Den här modellen är baserad på ett anpassade mått för bearbetning, lagring och i/o-resurser. Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er](sql-database-what-is-a-dtu.md)?|Bäst för kunder som vill enkla, förkonfigurerade alternativ.| 
|vCore-baserade modellen|Den här modellen kan du skala beräknings-och lagringsresurserna oberoende av varandra. Du kan också använda Azure Hybrid-förmån för SQL Server för att få besparingar.|Bäst för kunder som värdet flexibilitet, kontroll- och insyn.|
||||  

![prismodellen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>vCore-baserade inköpsmodell (förhandsgranskning)

En virtuell kärna representerar logiska Processorn erbjuds med möjlighet att välja mellan generationer av maskinvara. VCore-baserade inköpsmodell (förhandsversion) ger ditt flexibilitet, kontroll, genomskinlighet enskilda resursförbrukning och ett enkelt sätt att översätta krav på lokal arbetsbelastning till molnet. Den här modellen kan du skala beräknings-, minnes- och storage baserat på deras behov av arbetsbelastning. I den vCore-baserade inköpsmodell (förhandsgranskning), kunder kan välja mellan generella och kritiska tjänstnivåer för företag (förhandsversion) för både [enkel databaser](sql-database-single-database-resources.md) och [elastiska pooler](sql-database-elastic-pool.md). 

Tjänstnivåer särskiljs med hjälp av en mängd prestanda, design för hög tillgänglighet, fel isolering, typer av lagring och i/o-intervall. Kunden måste separat konfigurera den nödvändiga tidsperioden för lagring och lagring för säkerhetskopior. När du använder vCore modellen enskilda databaser och elastiska pooler är tillgängliga för upp till 30 procent besparingarna med den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

I vCore-baserade köp modellen (förhandsgranskning) kunder betalningen för:
- Beräkna (tjänstnivån + antal vCores + maskinvarusystem) *
- Typ och mängden data och loggfilen lagringsutrymme 
- Antal IOs **
- Säkerhetskopiera lagring (RA-GRS) ** 

\* I den första förhandsversionen Gen 4 logiska processorer är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer

\*\* Under förhandsgranskning är 7 dagar för säkerhetskopieringar och IOs gratis

> [!IMPORTANT]
> Compute-, IOs, data och lagra loggen debiteras per databas eller elastisk pool. Lagring av säkerhetskopior debiteras per varje databas. Mer information om hanterade instans avgifter avser [Azure SQL-hanterade databasinstans](sql-database-managed-instance.md).

> [!IMPORTANT]
> Region begränsningar: 
>
> VCore-baserade inköpsmodell (förhandsversion) är inte tillgänglig i Australien sydost ännu. Förhandsgranskningen är inte tillgänglig på följande områden: Västeuropa, Frankrike Central, Storbritannien, Syd och Storbritannien, Väst.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Att välja tjänstnivån, bearbetning, minne, lagring och i/o-resurser

Konvertera till vCore-baserade inköpsmodell (förhandsversion) kan du skala beräknings-och lagringsresurser, matchar lokalt prestanda och optimera pris oberoende av varandra. Om den databas eller en elastisk pool förbrukar mer än 300 DTU konvertering till vCore kan minska dina kostnader. Du kan konvertera med hjälp av din API väljer eller Azure-portalen utan avbrott. Dock krävs inte konverteringen. Om DTU-inköpsmodell uppfyller dina företagskrav på prestanda och, ska du fortsätta använda den. Om du vill konvertera från DTU-modellen till vCore-modell, bör du välja prestandanivå med hjälp av följande tumregel: varje 100 DTU i standardnivån kräver minst 1 vCore och varje 125 DTU i Premium-nivån kräver minst 1 vCore.

Tabellen nedan hjälper dig att förstå skillnaderna mellan dessa två nivåer:

||**Generella**|**Verksamhetskritiska**|
|---|---|---|
|Bäst för|De flesta arbetsbelastningar. Erbjudanden budget objektorienterad belastningsutjämnade och skalbar beräkning och lagring-alternativ.|Affärsprogram med höga I/O-krav. Erbjuder den högsta uthålligheten mot fel tack vare flera isolerade repliker.|
|Compute|1-16 vCore|1-16 vCore|
|Minne|7 GB per kärna |7 GB per kärna |
|Lagring|Premium Fjärrlagring, 5 GB – 4 TB|Lokala SSD-lagringen, 5 GB – 1 TB|
|I/o-genomströmning (CA)|500 IOPS per vCore med 7500 högsta IOPS|5 000 IOPS per kärna|
|Tillgänglighet|1 repliken, utan läsning skalning|3 repliker, 1 [Läs skala](sql-database-read-scale-out.md), zonen redundant hög tillgänglighet|
|Säkerhetskopior|RA-GRS, 7-35 dagar (7 dagar som standard)|RA-GRS, 7-35 dagar (7 dagar som standard) *|
|I minnet|Gäller inte|Stöds|
|||

\* Under förhandsgranskningen gör säkerhetskopior kvarhållningsperioden kan inte konfigureras och löses till 7 dagar.

> [!IMPORTANT]
> Om du behöver mindre än en vCore av beräkningskapacitet, Använd DTU-baserade inköpsmodell.

Mer information om specifika prestandanivåer och lagring som kan användas för enskilda databaser finns [SQL-databas vCore-baserade gränserna för enskilda databaser](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) och elastiska pooler finns [SQL-databas vCore-baserade gränserna för elastiska pooler](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar. 

## <a name="storage-considerations"></a>Överväganden för lagring

Tänk också på följande:
- Allokerat lagringsutrymme används av datafiler (MDF) och loggfiler för filer (LDF).
- Varje prestandanivå stöder en maximal databasstorlek med en standard max storlek på 32 GB.
- När du konfigurerar nödvändiga databasens storlek (storleken på MDF), läggs 30% av ytterligare lagringsutrymme automatiskt till stöd för LDF
- Du kan välja alla databasstorleken mellan 10 GB och vad som stöds
 - För standardlagring, öka eller minska storleken i steg 10 GB
 - För Premium-lagring, öka eller minska storleken i steg om 250 GB
- På tjänstnivån generella `tempdb` använder en bifogade SSD och lagringen kostnad ingår i vCore pris.
- På tjänstnivån Business kritiska `tempdb` resurser bifogade SSD med MDF-filen och LDF-filerna och tempDB-utrymme kostnad ingår i vCore pris.

> [!IMPORTANT]
> Du debiteras för det totala lagringsutrymmet som allokerats för MDF-filen och LDF.

Du övervakar den aktuella totala storleken på MDF-filen och LDF [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Du övervakar den aktuella storleken för de enskilda MDF-filen och LDF-filerna [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Säkerhetskopiering och lagring

Lagring för säkerhetskopiering av databaser har allokerats för att stödja punkten i tiden återställa (PITR) och lång sikt Kvarhållning (LTR) funktioner i SQL-databas. Denna lagring är allokerade separat för varje databas och debiteras som två separata per databas avgifter. 

- **PITR**: individuell databas säkerhetskopiorna kopieras till RA-GRS lagring är automatiskt. Lagringsutrymme ökar storleken dynamiskt när nya säkerhetskopior skapas.  Lagringsutrymmet används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopior av transaktionsloggar var femte minut. Användningen av lagringsutrymme beror på frekvensen för ändring av databasen och kvarhållningsperioden. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minsta lagringsutrymme mängd lika med 1 x datastorleken tillhandahålls utan extra kostnad. För de flesta databaser är beloppet tillräckligt för att lagra 7 dagar för säkerhetskopior.
- **LTR**: SQL-databas kan konfigurera långsiktig kvarhållning av fullständiga säkerhetskopieringar för upp till 10 år. Om LTR principen är aktiverad kommer de här säkerhetskopior lagras i RA-GRS lagring automatiskt, men du kan styra hur ofta säkerhetskopiorna kopieras. För att uppfylla olika krav kan välja du olika kvarhållningsperioder för säkerhetskopiering varje vecka, månad och år. Den här konfigurationen definierar hur mycket lagringsutrymme som ska användas för LTR säkerhetskopieringar. Du kan använda LTR prisnivå Kalkylatorn för att beräkna kostnaden för LTR lagring. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid-förmånen

I den vCore-baserade inköpsmodell (förhandsgranskning), du kan utbyta befintliga licenser för rabatterade priser på SQL-databas med hjälp av den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Azure förmånen kan du använda dina lokala SQL Server-licenser för att spara upp till 30% på Azure SQL Database med Software Assurance dina lokala SQL Server-licenser.

![prissättning](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrering av enskilda databaser med länkar geo-replikering

Migrera till från DTU-baserade modell vCore-baserade modellen liknar uppgraderar eller nedgradera geo-replikering relationerna mellan Standard och Premium-databaser. Det krävs ingen avslutande geo-replikering, men användaren måste följa reglerna för ordningsföljd. När du uppgraderar, måste du uppgradera den sekundära databasen först och sedan uppgradera primärt. När nedgradera, i omvänd ordning: du måste först nedgradera den primära databasen och nedgradera sekundär. 

När du använder geo-replikering mellan två elastiska pooler, rekommenderas att du anger en pool som primär och den andra – som sekundärt. I så fall ska migrera elastiska pooler använda samma riktlinjer.  Det är dock är tekniskt möjligt att en elastisk pool innehåller både primära och sekundära databaser. I detta fall bör för att migrera korrekt du behandlar poolen med högre belastning som ”primär” och följ ordningsföljd regler i enlighet med detta.  

Följande tabell innehåller riktlinjer för specifika Migreringsscenarier: 

|Aktuella tjänstnivå|Mål-tjänstnivå|Migreringstyp|Användaråtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Laterala|Kan migrera i valfri ordning, men se till en lämplig vCore storlek *|
|Premium|Affärskritisk|Laterala|Kan migrera i vilken ordning som helst, men måste du se till att lämpliga vCore storlek *|
|Standard|Affärskritisk|Uppgradera|Migrera sekundära först|
|Affärskritisk|Standard|Nedgradera|Migrera primära först|
|Premium|Generellt syfte|Nedgradera|Migrera primära först|
|Generellt syfte|Premium|Uppgradera|Migrera sekundära först|
|Affärskritisk|Generellt syfte|Nedgradera|Migrera primära först|
|Generellt syfte|Affärskritisk|Uppgradera|Migrera sekundära först|
||||

\* Varje 100 DTU i standardnivån kräver minst 1 vCore och varje 125 DTU i Premium-nivån kräver minst 1 vCore

## <a name="migration-of-failover-groups"></a>Migrering av redundanskluster grupper 

Migrering av redundanskluster grupper med flera databaser kräver enskilda migrering av primära och sekundära databaser. Samma överväganden och ordningsföljd regler tillämpas under den här processen. När databaserna som har konverterats till vCore-baserade modellen, gäller gruppen växling vid fel med samma principinställningar. 

## <a name="creation-of-a-geo-replication-secondary"></a>Skapa en sekundär geo-replikering

Du kan bara skapa en geo sekundär med samma tjänstnivån som den primära servern. För databasen med hög loggen generation hastighet rekommenderas att sekundärt skapas med samma prestandanivå som den primära servern. Om du skapar en geo sekundär i den elastiska poolen för en enskild primär databas, rekommenderas att poolen har den `maxVCore` inställningen som matchar den primära databas prestandanivån. Om du skapar en geo sekundär i den elastiska poolen för en primär i en annan elastisk pool, rekommenderas att pooler har samma `maxVCore` inställningar

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Med databaskopian för att konvertera en DTU-baserad databas till en vCore-baserad databas.

Du kan kopiera en databas med DTU-baserade prestandanivå till en databas med en vCore-baserade prestanda nivå utan begränsningar eller särskilda ordningsföljd så länge målet prestandanivå stöder den maximala databasstorleken för källdatabasen. Detta beror på att databaskopian skapar en ögonblicksbild av data från och med starttiden för kopieringen och utför inte datasynkronisering mellan käll- och mål. 

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika prestandanivåer och lagring som kan användas, finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits.md) och [SQL-databas vCore-baserade gränserna för](sql-database-vcore-resource-limits.md).
- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Lär dig mer om [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
