---
title: Översikt över storskalighet
description: Den här artikeln beskriver den storskaliga tjänst nivån i den vCore-baserade inköps modellen i Azure SQL Database och förklarar hur den skiljer sig från Generell användning-och Affärskritisk tjänst nivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: b2a8ad40092a2c02f00803e699de9d6dd8feebd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978636"
---
# <a name="hyperscale-service-tier"></a>Hyperskalatjänstnivå

Azure SQL Database baseras på SQL Server databas motor arkitektur som justeras för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre arkitektur modeller som används i Azure SQL Database:
- Generell användning/standard 
-  Hyperskalning
-  Affärskritisk/Premium

Den storskaliga tjänst nivån i Azure SQL Database är den senaste tjänst nivån i den vCore-baserade inköps modellen. Den här tjänst nivån är en mycket skalbar lagrings-och beräknings prestanda nivå som utnyttjar Azure-arkitekturen för att skala ut lagrings-och beräknings resurserna för en Azure SQL Database som är betydligt större än de tillgängliga gränserna för Generell användning och företag Kritiska tjänst nivåer.

> 
> [!NOTE]
> Mer information om Generell användning och Affärskritisk tjänst nivåer i den vCore-baserade inköps modellen finns i [generell användning](sql-database-service-tier-general-purpose.md) och [affärskritisk](sql-database-service-tier-business-critical.md) tjänst nivåer. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Vilka är funktionerna för storskalighet

Den storskaliga tjänst nivån i Azure SQL Database ger följande ytterligare funktioner:

- Stöd för upp till 100 TB databas storlek
- Nästan momentan databas säkerhets kopieringar (baserat på ögonblicks bilder av filer lagrade i Azure Blob Storage) oavsett storlek utan i/o-påverkan på beräknings resurser  
- Snabb återställning av databas tidpunkt (baserat på ögonblicks bilder av filer) på några minuter i stället för timmar eller dagar (inte en storlek på data åtgärd)
- Högre övergripande prestanda på grund av högre logg data flöde och snabbare transaktions överförings tider, oavsett data volymer
- Snabbt skala ut – du kan etablera en eller flera skrivskyddade noder för att avlasta din Läs arbets belastning och för användning som frekvent vänte läge
- Snabbt skala upp – du kan, i konstant tid, skala upp dina beräknings resurser för att hantera stora arbets belastningar som och vid behov och sedan skala beräknings resurserna tillbaka när de inte behövs.

Den storskaliga Service nivån tar bort många av de praktiska gränser som traditionellt sett visas i moln databaserna. Om de flesta andra databaser begränsas av de resurser som är tillgängliga i en enda nod, har databaser i den storskaliga tjänst nivån inga sådana begränsningar. Med sin flexibla lagrings arkitektur växer lagringen vid behov. I själva verket skapas inte storskaliga databaser med en definierad Max storlek. En storskalig databas växer vid behov – och du debiteras bara för den kapacitet som du använder. För Läs intensiva arbets belastningar ger den storskaliga Service nivån snabb skalbarhet genom att tillhandahålla ytterligare Läs repliker efter behov för att avlasta Läs arbets belastningar.

Dessutom är tiden som krävs för att skapa databas säkerhets kopior eller skala upp eller ned inte längre kopplad till data mängden i databasen. Storskaliga databaser kan säkerhets kopie ras nästan omedelbart. Du kan också skala en databas i terabyte i terabyte upp eller ned på några minuter. Den här funktionen gör att du inte kan tänka på att bli inramad av de första konfigurations alternativen.

Mer information om beräknings storlekarna för den storskaliga tjänst nivån finns i [Egenskaper för tjänst nivå](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vem bör tänka på den storskaliga tjänst nivån

Den storskaliga Service nivån är avsedd för de flesta företags arbets belastningar eftersom den ger stor flexibilitet och höga prestanda med oberoende skalbara beräknings-och lagrings resurser. Med möjligheten att automatiskt skala lagring upp till 100 TB är det ett bra val för kunder som:

- Har stora databaser lokalt och vill modernisera sina program genom att flytta till molnet
- Finns redan i molnet och begränsas av de maximala begränsningarna för databas storlek för andra tjänst nivåer (1-4 TB)
- Ha mindre databaser, men kräver snabba vertikala och vågräta beräknings skalningar, höga prestanda, omedelbar säkerhets kopiering och snabb databas återställning.

Den storskaliga tjänst nivån har stöd för ett brett utbud av SQL Server arbets belastningar, från ren OLTP till ren analys, men den är främst optimerad för OLTP-och HTAP-arbetsbelastningar (hybrid transaktion och analytisk bearbetning).

> [!IMPORTANT]
> Elastiska pooler har inte stöd för den storskaliga tjänst nivån.

## <a name="hyperscale-pricing-model"></a>Pris modell för storskalighet

Storskalig Service Tier är endast tillgängligt i [vCore-modellen](sql-database-service-tiers-vcore.md). För att passa den nya arkitekturen skiljer sig pris modellen något från Generell användning eller Affärskritisk tjänst nivåer:

- **Databehandling**:

  Det storskaliga beräknings enhets priset är per replik. [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) priset används automatiskt för att läsa skalnings repliker. Vi skapar en primär replik och en skrivskyddad replik per storskalig databas som standard.  Användare kan justera det totala antalet repliker, inklusive primärt från 1-5.

- **Lagring**:

  Du behöver inte ange den maximala data storleken när du konfigurerar en storskalig databas. På hyperskalningsnivån debiteras du för lagring för databasen baserat på faktisk användning. Storage tilldelas automatiskt mellan 10 GB och 100 TB, i steg som justeras dynamiskt mellan 10 GB och 40 GB.  

Mer information om priser för storskalig prissättning finns [Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arkitektur för distribuerade funktioner

Till skillnad från traditionella databas motorer som har centraliserat alla data hanterings funktioner på en plats/process (även kallade distribuerade databaser i produktion idag har flera kopior av en monolitisk-datamotor), separerar en storskalig databas motorn för frågekörning, där semantiken för olika data motorer avviker från de komponenter som tillhandahåller långsiktig lagring och hållbarhets tid för data. På det här sättet kan lagrings kapaciteten på ett smidigt sätt skalas ut så mycket som behövs (det inledande målet är 100 TB). Skrivskyddade repliker delar samma lagrings komponenter så att ingen data kopiering krävs för att skapa en ny läsbar replik. 

Följande diagram illustrerar de olika typerna av noder i en storskalig databas:

![Arkitektur](./media/sql-database-hyperscale/hyperscale-architecture2.png)

En storskalig databas innehåller följande typer av komponenter:

### <a name="compute"></a>Databearbetning

Compute-noden är där Relations motorn är i drift, så alla språk element, fråga bearbetning och så vidare inträffar. Alla användar interaktioner med en storskalig databas sker genom de här Compute-noderna. Compute-noder har SSD-baserade cacheminnen (märkta RBPEX-elastiska buffertpooltillägget i diagrammet ovan) för att minimera antalet nätverks fördröjningar som krävs för att hämta en sida med data. Det finns en primär Compute-nod där alla Läs-och skriv åtgärder och transaktioner bearbetas. Det finns en eller flera sekundära datornoder som fungerar som frekventa vänte läge för redundans, samt fungerar som skrivskyddade Compute-noder för avlastning av Läs arbets belastningar (om den här funktionen önskas).

### <a name="page-server"></a>Sid Server

Sid servrar är system som representerar en utskalad lagrings motor.  Varje sid Server ansvarar för en delmängd av sidorna i databasen.  Varje sid Server styr då mellan 128 GB och 1 TB data. Inga data delas på fler än en sid Server (utanför repliker som hålls för redundans och tillgänglighet). En sid servers jobb är att hantera databas sidor till datornoderna på begäran, och för att behålla sidorna uppdaterade som transaktioner uppdatera data. Sid servrar hålls uppdaterade genom att spela upp logg poster från logg tjänsten. Sid servrar upprätthåller också SSD-baserade cacheminnen för att förbättra prestanda. Långsiktig lagring av data sidor behålls i Azure Storage för ytterligare tillförlitlighet.

### <a name="log-service"></a>Logg tjänst

Logg tjänsten accepterar logg poster från den primära beräknings repliken, behåller dem i en hållbar cache och vidarebefordrar logg posterna till resten av beräknings replikerna (så att de kan uppdatera deras cacheminnen) samt de relevanta sid servrarna så att data kan uppdateras föreligg. På så sätt sprids alla data ändringar från den primära beräknings repliken genom logg tjänsten till alla sekundära beräknings repliker och sid servrar. Slutligen flyttas logg posterna ut till långsiktig lagring i Azure Storage, vilket är ett stort antal lagrings lager. Den här mekanismen tar bort behovet av frekvent logg trunkering. Logg tjänsten har också lokal cache för att påskynda åtkomsten till logg poster.

### <a name="azure-storage"></a>Azure Storage

Azure Storage innehåller alla datafiler i en databas. Sid servrar behåller datafilerna i Azure Storage aktuella. Den här lagringen används för säkerhets kopierings syfte och för replikering mellan Azure-regioner. Säkerhets kopieringar implementeras med lagrings ögonblicks bilder av datafiler. Återställnings åtgärder med hjälp av ögonblicks bilder är snabba oavsett data storlek. Data kan återställas till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna av databasen.

## <a name="backup-and-restore"></a>Säkerhetskopiera och återställ

Säkerhets kopiorna är fil-och ögonblicks bilder, och därför är de nästan momentant. Med lagrings-och beräknings separering kan du sänka säkerhets kopieringen/återställningen till lagrings lagret för att minska bearbetnings belastningen på den primära beräknings repliken. Det innebär att säkerhets kopieringen av databasen inte påverkar prestandan för den primära Compute-noden. återställningar görs på samma sätt genom att återställa till fil ögonblicks bilder och eftersom det inte är en storlek på data åtgärd. Restore är en konstant åtgärd och även om flera terabyte-databaser kan återställas på några minuter i stället för timmar eller dagar. Att skapa nya databaser genom att återställa en befintlig säkerhets kopia drar också nytta av den här funktionen: att skapa databas kopior inom samma logiska Server för utvecklings-eller testnings ändamål, till och med terabyte storleks databaser, är doable på några minuter.

## <a name="scale-and-performance-advantages"></a>Skalbarhet och prestanda för delar

Med möjligheten att snabbt kunna sätta upp eller ned ytterligare skrivskyddade Compute-noder ger storskaliga arkitektur avsevärda funktioner för Läs skalning och kan också frigöra den primära Compute-noden för att betjäna fler Skriv förfrågningar. Dessutom kan Compute-noderna skalas upp/ned snabbt på grund av den delade lagrings arkitekturen i den storskaliga arkitekturen.

## <a name="create-a-hyperscale-database"></a>Skapa en storskalig databas

En storskalig databas kan skapas med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Storskaliga databaser är bara tillgängliga med den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).

Följande T-SQL-kommando skapar en storskalig databas. Du måste ange både versions-och tjänst målet i `CREATE DATABASE`-instruktionen. Se [resurs gränserna](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen5) för en lista över giltiga tjänst mål.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Då skapas en storskalig databas på Gen5-maskinvara med 4 kärnor.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrera en befintlig Azure SQL Database till den storskaliga tjänst nivån

Du kan flytta dina befintliga Azure SQL-databaser till skalning med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). För närvarande är detta en enkelriktad migrering. Du kan inte flytta databaser från storskalig till en annan tjänst nivå, förutom genom att exportera och importera data. För Proofing of Concept (POC) rekommenderar vi att du skapar en kopia av dina produktions databaser och migrerar kopian till storskalig skala. Att migrera en befintlig Azure SQL-databas till den storskaliga nivån är en storlek på data åtgärd.

Följande T-SQL-kommando flyttar en databas till den storskaliga tjänst nivån. Du måste ange både versions-och tjänst målet i `ALTER DATABASE`-instruktionen.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en storskalig replik av en storskalig databas

I storskaliga databaser, anger argumentet `ApplicationIntent` i anslutnings strängen som anges av klienten om anslutningen dirigeras till Skriv repliken eller till en skrivskyddad sekundär replik. Om `ApplicationIntent` har angetts till `READONLY` och databasen inte har någon sekundär replik dirigeras anslutningen till den primära repliken och standardvärdet för `ReadWrite` beteendet.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Storskaliga sekundära repliker är alla identiska, med samma service nivå mål som den primära repliken. Om det finns fler än en sekundär replik fördelas arbets belastningen över alla tillgängliga sekundära. Varje sekundär replik uppdateras oberoende, vilket innebär att olika repliker kan ha olika data svars tider i förhållande till den primära repliken.

## <a name="database-high-availability-in-hyperscale"></a>Databas hög tillgänglighet i hög skala

Precis som i alla andra tjänst nivåer garanterar storskalig data hållbarhet för genomförda transaktioner oavsett tillgängligheten för beräknings repliker. Omfattningen av drift stopp på grund av att den primära repliken blir otillgänglig beror på typen av redundansväxling (planerad eller oplanerad) och på förekomst av minst en sekundär replik. I en planerad redundansväxling (dvs. en underhålls händelse) skapar systemet antingen den nya primära repliken innan en redundansväxling initieras, eller använder en befintlig sekundär replik som redundansväxlingen. I en oplanerad redundansväxling (dvs. ett maskin varu fel på den primära repliken) använder systemet en sekundär replik som ett failover-mål om det finns en sådan eller skapar en ny primär replik från poolen med tillgänglig beräknings kapacitet. I det senare fallet är stillestånds tiden längre, på grund av extra steg som krävs för att skapa den nya primära repliken.

Om du vill ha service avtal för storskalig skalning, se [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveri beredskap för storskaliga databaser

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Återställa en storskalig databas till en annan geografi
Om du behöver återställa en Azure SQL Database storskalig databas till en annan region än den som för närvarande är värdbaserad, som en del av en haveri beredskaps åtgärd eller en detalj nivå, en annan orsak, är den primära metoden att göra en geo-återställning av databasen.  Detta innebär exakt samma steg som vad du skulle använda för att återställa andra AZURE SQL-databaser till en annan region:
1. Skapa en SQL Database-Server i mål regionen om du inte redan har en lämplig server där.  Den här servern bör ägas av samma prenumeration som den ursprungliga (käll) servern.
2. Följ anvisningarna i avsnittet [geo-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) på sidan om hur du återställer Azure SQL-databaser från automatiska säkerhets kopieringar.

> [!NOTE]
> Eftersom källan och målet är i olika regioner kan databasen inte dela lagring av ögonblicks bilder med käll databasen som i icke-geo-återställningar, som är mycket snabbt.  Om det finns en geo-återställning av en storskalig databas, är det en åtgärd för data storlek, även om målet är i det kopplade området för den geo-replikerade lagringen.  Det innebär att en geo-återställning tar tid som är proportionell till storleken på databasen som återställs.  Om målet är i den kopplade regionen kommer kopian att finnas i ett Data Center, vilket kommer att bli betydligt snabbare än med en lång kopia över Internet, men det kopierar fortfarande alla bitar.

## <a name=regions></a>Tillgängliga regioner

Den Azure SQL Database nivån på den storskaliga nivån är för närvarande tillgänglig i följande regioner:

- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, centrala
- USA, centrala
- Kina, östra 2
- Kina, norra 2
- Asien, östra
- USA, östra
- USA, östra 2
- Frankrike, centrala
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Sydkorea, södra
- USA, norra centrala
- Europa, norra
- Sydafrika, norra
- USA, södra centrala
- Asien, sydöstra
- Storbritannien, södra
- Storbritannien, västra
- Europa, västra
- USA, västra
- USA, västra 2

Om du vill skapa en storskalig databas i en region som inte är listad som stöds kan du skicka en onboarding-begäran via Azure Portal. Vi arbetar för att expandera listan över regioner som stöds, så kontrol lera att listan över de senaste regionerna är tillbaka.

För att begära möjlighet att skapa storskaliga databaser i regioner som inte listas:

1. Välj **Hjälp + Support**på Azure Portal-menyn, eller Sök efter och välj **Hjälp + Support** från vilken sida som helst.

2. I [Azure hjälp och support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)väljer du [**ny supportbegäran**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

3. För **typ av problem**väljer du **begränsningar för tjänsten och prenumerationen (kvoter)** .

4. Välj den prenumeration som du vill använda för att skapa databaserna.

5. I **typ av kvot**väljer du **SQL-databas**.

    ![Bladet hjälp och support för Azure](media/sql-database-service-tier-hyperscale/new-support-request-screen.png)

6. Klicka på **Nästa: lösningar**.

7. Klicka på **Ange information**.

    ![Probleminformation](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Välj **SQL Database kvot typ**: **annan kvot förfrågan**.

9. Fyll i följande mall:

    ![Kvot information](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    Ange följande information i mallen

    > Begäran om att skapa SQL Database för Azure-storskalig i en ny region<br/> Region: [Fyll i din begärda region]  <br/>
    > Beräkna SKU/totalt antal kärnor, inklusive läsbara repliker <br/>
    > Beräknat antal TB 
    >

10. Välj **allvarlighets grad C**.

11. Välj lämplig kontakt metod och fyll i information.

12. Klicka på **Spara** och **Fortsätt**.

## <a name="known-limitations"></a>Kända begränsningar
Detta är de aktuella begränsningarna för den storskaliga tjänst nivån från och med GA.  Vi arbetar aktivt för att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| I fönstret hantera säkerhets kopior för en logisk server visas inte storskaliga databaser filtreras från SQL Server  | Storskaligt har en separat metod för att hantera säkerhets kopieringar, och eftersom inställningarna för långsiktig kvarhållning och tidpunkten för kvarhållning av säkerhets kopior inte tillämpas/är ogiltiga. Därför visas inte storskaliga databaser i fönstret hantera säkerhets kopiering. |
| Återställning av lagring vid olika tidpunkter | När en databas har migrerats till den storskaliga tjänst nivån, kan du återställa till en tidpunkt innan migreringen går ut.|
| Återställning av icke-storskalig databas till storskalig och tvärtom | Du kan inte återställa en storskalig databas till en databas som inte är storskalig, och du kan inte återställa en databas som inte är storskalig till en storskalig databas.|
| Om en databas har en eller flera datafiler som är större än 1 TB, Miss lyckas migreringen | I vissa fall kan det vara möjligt att undvika det här problemet genom att minska de stora filerna till mindre än 1 TB. Om du migrerar en databas som används under migreringsprocessen ser du till att ingen fil får större än 1 TB. Använd följande fråga för att fastställa storleken på databasfilerna. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Hanterad instans | Azure SQL Database hanterade instansen stöds för närvarande inte med storskaliga databaser. |
| Elastiska pooler |  Elastiska pooler stöds för närvarande inte med SQL Database storskalig.|
| Migrering till storskalig skalning är för närvarande en enkelriktad åtgärd | När en databas har migrerats till storskalig kan den inte migreras direkt till en icke-storskalig tjänst nivå. Det enda sättet att migrera en databas från storskalig till icke-storskalig är att exportera/importera med hjälp av en BACPAC-fil eller annan teknik för data förflyttning (Mass kopiering, Azure Data Factory, Azure Databricks, SSIS osv.)|
| Migrering av databaser med InMemory OLTP-objekt | Storskaligt stöder endast en delmängd av InMemory OLTP-objekt typer, inklusive minnesoptimerade tabell typer, internt kompilerade lagrade procedurer och funktioner. Men när det finns minnesbaserade OLTP-objekt i databasen stöds inte direkt migrering från Premium-och Affärskritisk-tjänst nivåer till storskalig skalning. Migrering av en sådan databas till storskalig kräver tre steg: (1) ta bort alla InMemory OLTP-objekt och deras beroenden. Om du vill bevara data i varaktiga minnesoptimerade tabeller konverterar du dem till disk tabeller. (2) ändra databasens tjänst nivå till skalning. (3) återskapa tidigare borttagna objekt. Varaktiga och icke-varaktiga minnesoptimerade tabeller stöds inte för närvarande i storskaliga och måste vara disk tabeller. Minnesoptimerade Table-variabler stöds. |
| Spårning av ändringar | Ändringsspårning finns för närvarande i en offentlig för hands version och kan aktive ras på nya eller befintliga storskaliga databaser. |
| Geo-replikering  | Du kan inte konfigurera geo-replikering för Azure SQL Database storskaligt. |
| Databas kopia | Du kan inte använda databas kopiering ännu för att skapa en ny databas i Azure SQL-skalning. |
| TDE/AKV-integrering | Transparent databas kryptering med hjälp av Azure Key Vault (kallas ofta för att hämta egna nycklar eller BYOK) stöds ännu inte för Azure SQL Database storskalig, men TDE med tjänst hanterade nycklar stöds fullt ut. |
|Intelligenta databas funktioner | Med undantag för alternativet "framtvinga plan" stöds inte alla andra alternativ för automatisk justering i den storskaliga: alternativen kan verka vara aktiverade, men inga rekommendationer eller åtgärder har gjorts. |
| Krymp databas | DBCC SHRINKDATABASE eller DBCC SHRINKFILE stöds för närvarande inte för storskaliga databaser. |
| Kontroll av databas integritet | DBCC CHECKDB stöds för närvarande inte för storskaliga databaser. Mer information om data integritets hantering i Azure SQL Database finns i [data integritet i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Nästa steg

- Vanliga frågor och svar om storskalighet finns i vanliga frågor och svar [om storskaliga frågor](sql-database-service-tier-hyperscale-faq.md).
- Information om tjänst nivåer finns i [tjänst nivåer](sql-database-service-tiers.md)
- Se [Översikt över resurs begränsningar på en logisk server](sql-database-resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.
- För inköps modell gränser för en enskild databas, se [Azure SQL Database vCore-baserade inköps modell gränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](sql-database-features.md).
