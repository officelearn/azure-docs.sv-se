---
title: Översikt över Azure SQL Database storskalig | Microsoft Docs
description: Den här artikeln beskriver den storskaliga tjänst nivån i den vCore-baserade inköps modellen i Azure SQL Database och förklarar hur den skiljer sig från Generell användning-och Affärskritisk tjänst nivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 1d70c5d86221213ae3f9a2d31fdf40857cb516be
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845634"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Storskalig tjänste nivå för upp till 100 TB

Azure SQL Database baseras på SQL Server databas motor arkitektur som justeras för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre arkitektur modeller som används i Azure SQL Database:
- Generell användning/standard 
-  Storskalig
-  Affärskritisk/Premium

Den storskaliga tjänst nivån i Azure SQL Database är den senaste tjänst nivån i den vCore-baserade inköps modellen. Den här tjänst nivån är en mycket skalbar lagrings-och beräknings prestanda nivå som utnyttjar Azure-arkitekturen för att skala ut lagrings-och beräknings resurserna för en Azure SQL Database som är betydligt större än de tillgängliga gränserna för Generell användning och företag Kritiska tjänst nivåer.

> 
> [!NOTE]
> Mer information om Generell användning och Affärskritisk tjänst nivåer i den vCore-baserade inköps modellen finns i [generell användning](sql-database-service-tier-general-purpose.md) och [affärskritisk](sql-database-service-tier-business-critical.md) tjänst nivåer. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Vilka är funktionerna för storskalighet

Den storskaliga tjänst nivån i Azure SQL Database ger följande ytterligare funktioner:

- Stöd för upp till 100 TB databas storlek
- Nästan momentan databas säkerhets kopieringar (baserat på ögonblicks bilder av filer lagrade i Azure Blob Storage) oavsett storlek utan i/o-påverkan på beräknings resurser  
- Snabba databaser återställer (baserat på ögonblicks bilder av filer) på några minuter i stället för timmar eller dagar (inte en storlek på data åtgärd)
- Högre övergripande prestanda på grund av högre logg data flöde och snabbare transaktions överförings tider, oavsett data volymer
- Snabbt skala ut – du kan etablera en eller flera skrivskyddade noder för att avlasta din Läs arbets belastning och för användning som frekvent vänte läge
- Snabbt skala upp – du kan, i konstant tid, skala upp dina beräknings resurser för att hantera stora arbets belastningar som och vid behov och sedan skala beräknings resurserna tillbaka när de inte behövs.

Den storskaliga Service nivån tar bort många av de praktiska gränser som traditionellt sett visas i moln databaserna. Om de flesta andra databaser begränsas av de resurser som är tillgängliga i en enda nod, har databaser i den storskaliga tjänst nivån inga sådana begränsningar. Med sin flexibla lagrings arkitektur växer lagringen vid behov. I själva verket skapas inte storskaliga databaser med en definierad Max storlek. En storskalig databas växer vid behov – och du debiteras bara för den kapacitet som du använder. För Läs intensiva arbets belastningar ger den storskaliga Service nivån snabb skalbarhet genom att tillhandahålla ytterligare Läs repliker efter behov för att avlasta Läs arbets belastningar.

Dessutom är tiden som krävs för att skapa databas säkerhets kopior eller skala upp eller ned inte längre kopplad till data mängden i databasen. Storskaliga databaser kan säkerhets kopie ras nästan omedelbart. Du kan också skala en databas i terabyte i terabyte upp eller ned på några minuter. Den här funktionen gör att du inte kan tänka på att bli inramad av de första konfigurations alternativen.

Mer information om beräknings storlekarna för den storskaliga tjänst nivån finns i [Egenskaper för tjänst nivå](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vem bör tänka på den storskaliga tjänst nivån

Den storskaliga Service nivån är främst avsedd för kunder som har stora databaser, antingen lokalt och vill modernisera sina program genom att flytta till molnet eller för kunder som redan finns i molnet och begränsas av den maximala databas storleken begränsningar (1-4 TB). Den är också avsedd för kunder som söker hög prestanda och hög skalbarhet för lagring och beräkning.

Den storskaliga tjänst nivån stöder alla SQL Server arbets belastningar, men den är främst optimerad för OLTP. Den storskaliga tjänst nivån stöder även hybrid-och analys arbets belastningar (data mart).

> [!IMPORTANT]
> Elastiska pooler har inte stöd för den storskaliga tjänst nivån.

## <a name="hyperscale-pricing-model"></a>Pris modell för storskalighet

Storskalig Service Tier är endast tillgängligt i [vCore-modellen](sql-database-service-tiers-vcore.md). För att passa den nya arkitekturen skiljer sig pris modellen något från Generell användning eller Affärskritisk tjänst nivåer:

- **Compute**:

  Det storskaliga beräknings enhets priset är per replik. [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) priset används automatiskt för att läsa skalnings repliker. Vi skapar en primär replik och en skrivskyddad replik per storskalig databas som standard.  Användare kan justera det totala antalet repliker, inklusive primärt från 1-5.

- **Lagring**:

  Du behöver inte ange den maximala data storleken när du konfigurerar en storskalig databas. På hyperskalningsnivån debiteras du för lagring för databasen baserat på faktisk användning. Storage tilldelas automatiskt mellan 10 GB och 100 TB, i steg som justeras dynamiskt mellan 10 GB och 40 GB.  

Mer information om priser för storskalig prissättning finns [Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arkitektur för distribuerade funktioner

Till skillnad från traditionella databas motorer som har centraliserat alla data hanterings funktioner på en plats/process (även kallade distribuerade databaser i produktion idag har flera kopior av en monolitisk data motor), separeras en storskalig databas motorn för bearbetning av förfrågningar, där semantiken för olika data motorer avviker från de komponenter som tillhandahåller långsiktig lagring och hållbarhet för data. På det här sättet kan lagrings kapaciteten på ett smidigt sätt skalas ut så mycket som behövs (det inledande målet är 100 TB). Skrivskyddade repliker delar samma lagrings komponenter så att ingen data kopiering krävs för att skapa en ny läsbar replik. 

Följande diagram illustrerar de olika typerna av noder i en storskalig databas:

![Arkitektur](./media/sql-database-hyperscale/hyperscale-architecture.png)

En storskalig databas innehåller följande typer av noder:

### <a name="compute-node"></a>Beräkningsnod

Compute-noden är där Relations motorn är i drift, så alla språk element, fråga bearbetning och så vidare inträffar. Alla användar interaktioner med en storskalig databas sker genom de här Compute-noderna. Compute-noder har SSD-baserade cacheminnen (märkta RBPEX-elastiska buffertpooltillägget i diagrammet ovan) för att minimera antalet nätverks fördröjningar som krävs för att hämta en sida med data. Det finns en primär Compute-nod där alla Läs-och skriv åtgärder och transaktioner bearbetas. Det finns en eller flera sekundära datornoder som fungerar som frekventa vänte läge för redundans, samt fungerar som skrivskyddade Compute-noder för avlastning av Läs arbets belastningar (om den här funktionen önskas).

### <a name="page-server-node"></a>Page Server-nod

Sid servrar är system som representerar en utskalad lagrings motor.  Varje sid Server ansvarar för en delmängd av sidorna i databasen.  Varje sid Server styr då mellan 128 GB och 1 TB data. Inga data delas på fler än en sid Server (utanför repliker som hålls för redundans och tillgänglighet). En sid servers jobb är att hantera databas sidor till datornoderna på begäran, och för att behålla sidorna uppdaterade som transaktioner uppdatera data. Sid servrar hålls uppdaterade genom att spela upp logg poster från logg tjänsten. Sid servrar upprätthåller också SSD-baserade cacheminnen för att förbättra prestanda. Långsiktig lagring av data sidor behålls i Azure Storage för ytterligare tillförlitlighet.

### <a name="log-service-node"></a>Nod för logg tjänst

Log service-noden accepterar logg poster från den primära Compute-noden, behåller dem i en hållbar cache och vidarebefordrar logg posterna till resten av datornoderna (så att de kan uppdatera deras cacheminnen) samt de relevanta sid servrarna så att data kan uppdateras t. ex. Här. På så sätt sprids alla data ändringar från den primära Compute-noden genom logg tjänsten till alla sekundära datornoder och sid servrar. Slutligen flyttas logg posterna ut till långsiktig lagring i Azure Storage, vilket är en oändlig lagrings plats. Den här mekanismen tar bort behovet av frekvent logg trunkering. Logg tjänsten har också lokal cache för att påskynda åtkomsten.

### <a name="azure-storage-node"></a>Azure Storage-nod

Azure Storage-noden är slut destinationen för data från sid servrar. Den här lagringen används för säkerhets kopierings syfte och för replikering mellan Azure-regioner. Säkerhets kopieringar består av ögonblicks bilder av datafiler. Återställnings åtgärden är snabb från dessa ögonblicks bilder och data kan återställas till vilken tidpunkt som helst.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhets kopiorna är fil-och ögonblicks bilder och därför är de nästan momentant. Med lagrings-och beräknings separering kan du sänka säkerhets kopieringen/återställningen till lagrings lagret för att minska bearbetnings belastningen på den primära Compute-noden. Därför påverkar säkerhets kopieringen av en stor databas inte prestandan för den primära Compute-noden. Återställningar görs på samma sätt genom att kopiera fil ögonblicks bilden och därför är den inte en storlek på data åtgärd. Återställnings åtgärden är snabb för återställningar inom samma lagrings konto.

## <a name="scale-and-performance-advantages"></a>Skalbarhet och prestanda för delar

Med möjligheten att snabbt kunna sätta upp eller ned ytterligare skrivskyddade Compute-noder ger storskaliga arkitektur avsevärda funktioner för Läs skalning och kan också frigöra den primära Compute-noden för att betjäna fler Skriv förfrågningar. Dessutom kan Compute-noderna skalas upp/ned snabbt på grund av den delade lagrings arkitekturen i den storskaliga arkitekturen.

## <a name="create-a-hyperscale-database"></a>Skapa en storskalig databas

En storskalig databas kan skapas med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Storskaliga databaser är bara tillgängliga med den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).

Följande T-SQL-kommando skapar en storskalig databas. Du måste ange både versions-och tjänst målet i `CREATE DATABASE` instruktionen. Se [resurs gränserna](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier-for-provisioned-compute) för en lista över giltiga tjänst mål.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Då skapas en storskalig databas på Gen5-maskinvara med 4 kärnor.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrera en befintlig Azure SQL Database till den storskaliga tjänst nivån

Du kan flytta dina befintliga Azure SQL-databaser till skalning med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). För närvarande är detta en enkelriktad migrering. Du kan inte flytta databaser från storskalig till en annan tjänst nivå. Vi rekommenderar att du gör en kopia av dina produktions databaser och migrerar till storskaligt för koncept bevis (POC).

Följande T-SQL-kommando flyttar en databas till den storskaliga tjänst nivån. Du måste ange både versions-och tjänst målet i `ALTER DATABASE` instruktionen.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en storskalig replik av en storskalig databas

I storskaliga databaser `ApplicationIntent` anger argumentet i anslutnings strängen som klienten tillhandahåller om anslutningen dirigeras till Skriv repliken eller till en skrivskyddad sekundär replik. Om uppsättningen till `READONLY` och databasen inte har någon sekundär replik dirigeras anslutningen till den `ReadWrite` primära repliken och standardvärdet är beteendet. `ApplicationIntent`

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveri beredskap för storskaliga databaser
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Återställa en storskalig databas till en annan geografi
Om du behöver återställa en Azure SQL Database storskalig databas till en annan region än den som för närvarande är värdbaserad, som en del av en haveri beredskaps åtgärd eller en detalj nivå, en annan orsak, är den primära metoden att göra en geo-återställning av databasen.  Detta innebär exakt samma steg som vad du skulle använda för att återställa andra AZURE SQL-databaser till en annan region:
1. Skapa en SQL Database-Server i mål regionen om du inte redan har en lämplig server där.  Den här servern bör ägas av samma prenumeration som den ursprungliga (käll) servern.
2. Följ anvisningarna i avsnittet [geo-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) på sidan om hur du återställer Azure SQL-databaser från automatiska säkerhets kopieringar.

> [!NOTE]
> Eftersom källan och målet är i olika regioner kan databasen inte dela lagring av ögonblicks bilder med käll databasen som i icke-geo-återställningar, som är mycket snabbt.  Om det finns en geo-återställning av en storskalig databas, är det en åtgärd för data storlek, även om målet är i det kopplade området för den geo-replikerade lagringen.  Det innebär att en geo-återställning tar tid som är proportionell till storleken på databasen som återställs.  Om målet är i den kopplade regionen kommer kopian att finnas i ett Data Center, vilket kommer att bli betydligt snabbare än med en lång kopia över Internet, men det kopierar fortfarande alla bitar.

## <a name=regions></a>Tillgängliga regioner

Den Azure SQL Database nivån på den storskaliga nivån är för närvarande tillgänglig i följande regioner:

- Östra Australien
- Sydöstra Australien
- Södra Brasilien
- Centrala Kanada
- Centrala USA
- Kina, östra 2
- Kina, norra 2
- Östasien
- East US
- USA, östra 2
- Frankrike, centrala
- Östra Japan
- Västra Japan
- Sydkorea, centrala
- Sydkorea, södra
- Norra centrala USA
- Norra Europa
- Sydafrika, norra
- Södra centrala USA
- Sydostasien
- Storbritannien, södra
- Storbritannien, västra
- Västra Europa
- Västra USA
- Västra USA 2

Om du vill skapa en storskalig databas i en region som inte är listad som stöds kan du skicka en onboarding-begäran via Azure Portal. Vi arbetar för att expandera listan över regioner som stöds, så kontrol lera att listan över de senaste regionerna är tillbaka.

För att begära möjlighet att skapa storskaliga databaser i regioner som inte listas:

1. Gå till [bladet hjälp och support för Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Klicka på [ **ny supportbegäran**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Bladet hjälp och support för Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. För **typ av problem**väljer du **begränsningar för tjänsten och prenumerationen (kvoter)**

4. Välj den prenumeration som du vill använda för att skapa databas (er)

5. För **typ av kvot**väljer du **SQL-databas**

6. Klicka på **Nästa: Paketlösningar**

1. Klicka på **Ange information**

    ![Uppgifter om problem](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Välj **SQL Database kvot typ**: **Annan kvot förfrågan**

9. Fyll i följande mall:

    ![Kvotinformation](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    Ange följande information i mallen

    > Begäran om att skapa SQL Database för Azure-storskalig i en ny region<br/> Region: [Fyll i din begärda region]  <br/>
    > Beräkna SKU/totalt antal kärnor, inklusive läsbara repliker <br/>
    > Beräknat antal TB 
    >

10. Välj **Allvarlighetsgrad C**

11. Välj lämplig kontakt metod och fyll i information.

12. Klicka på **Spara** och **Fortsätt**

## <a name="known-limitations"></a>Kända begränsningar
Detta är de aktuella begränsningarna för den storskaliga tjänst nivån från och med GA.  Vi arbetar aktivt för att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| I fönstret hantera säkerhets kopior för en logisk server visas inte storskaliga databaser filtreras från SQL Server  | Storskaligt har en separat metod för att hantera säkerhets kopieringar, och eftersom inställningarna för långsiktig kvarhållning och tidpunkten för kvarhållning av säkerhets kopior inte tillämpas/är ogiltiga. Därför visas inte storskaliga databaser i fönstret hantera säkerhets kopiering. |
| Återställning från tidpunkt | När en databas har migrerats till den storskaliga tjänst nivån, kan du återställa till en tidpunkt innan migreringen går ut.|
| Återställning av icke-storskalig databas till Hypserscale och vice versa | Du kan inte återställa en storskalig databas till en databas som inte är storskalig, och du kan inte återställa en databas som inte är storskalig till en storskalig databas.|
| Om en databas fil växer under migreringen på grund av en aktiv arbets belastning och korsar 1 TB per fil gräns, Miss lyckas migreringen | Åtgärder <br> – Om möjligt migrerar du databasen när det inte finns någon uppdaterings arbets belastning som körs.<br> -Försök att migrera igen, det lyckas så länge som den 1 TB-gränserna inte är överkorsad under migreringen.|
| Managed Instance | Azure SQL Database hanterade instansen stöds för närvarande inte med storskaliga databaser. |
| Elastiska pooler |  Elastiska pooler stöds för närvarande inte med SQL Database storskalig.|
| Migrering till storskalig skalning är för närvarande en enkelriktad åtgärd | När en databas har migrerats till storskalig kan den inte migreras direkt till en icke-storskalig tjänst nivå. Det enda sättet att migrera en databas från storskalig till icke-storskalig är att exportera/importera med hjälp av en BACPAC-fil.|
| Migrering av databaser med beständiga minnes objekt | Storskaligt stöder endast icke-beständiga minnes objekt (tabell typer, inbyggda SPs och funktioner).  Permanenta InMemory-tabeller och andra objekt måste släppas och återskapas som icke-minnesbaserade objekt innan migreringen av en databas till den storskaliga tjänst nivån.|
| Spårning av ändringar | Du kommer inte att kunna använda Ändringsspårning med storskaliga databaser. |
| Geo-replikering  | Du kan inte konfigurera geo-replikering för Azure SQL Database storskaligt.  Du kan utföra geo-Restore (återställa databasen i en annan geografi, för DR eller andra syfte) |
| TDE/AKV-integrering | Transparent databas kryptering med hjälp av Azure Key Vault (kallas ofta för att hämta egna nycklar eller BYOK) stöds ännu inte för Azure SQL Database storskalig, men TDE med tjänst hanterade nycklar stöds fullt ut. |
|Intelligenta databas funktioner | 1. Skapa index, släpp index rådgivare modeller har inte tränats för databaser med stor skala. <br/>2. Schema problem, DbParameterization-nyligen tillagda rådgivare stöds inte för storskalig databas.|



## <a name="next-steps"></a>Nästa steg

- Vanliga frågor och svar om storskalighet finns i vanliga frågor och svar [om storskaliga frågor](sql-database-service-tier-hyperscale-faq.md).
- Information om tjänst nivåer finns i [tjänst nivåer](sql-database-service-tiers.md)
- Se [Översikt över resurs begränsningar på en logisk server](sql-database-resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.
- För inköps modell gränser för en enskild databas, se [Azure SQL Database vCore-baserade inköps modell gränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](sql-database-features.md).
