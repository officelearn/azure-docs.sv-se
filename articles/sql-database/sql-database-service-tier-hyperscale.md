---
title: Azure SQL Database hyperskala översikt | Microsoft Docs
description: Den här artikeln beskrivs hyperskala tjänstnivån i den vCore-baserade inköpsmodellen i Azure SQL-databas och hur den skiljer sig från tjänstnivåer för generell användning och affärskritisk.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 9455b8488bdf7c36c662a8f771e6b26d1a27b13e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236555"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Hyperskala tjänstnivå för upp till 100 TB

Azure SQL Database är baserad på SQL Server Database Engine-arkitektur som justeras för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns tre arkitekturmodeller som används i Azure SQL Database:
- Allmänt syfte/Standard 
-  Storskalig
-  Kritisk Business/Premium

Hyperskala tjänstnivån i Azure SQL Database är den senaste tjänstnivån i den vCore-baserade inköpsmodellen. Den här är en mycket skalbar lagring och beräkning prestandanivå som utnyttjar Azure-arkitektur för att skala ut lagring och beräkna resurser för en Azure SQL Database avsevärt utöver gränserna som är tillgängliga för generell användning och företag Kritiska tjänstnivåer.

> 
> [!NOTE]
> Mer information om tjänstnivåer generell användning och affärskritisk i den vCore-baserade inköpsmodellen finns [generella](sql-database-service-tier-general-purpose.md) och [affärskritisk](sql-database-service-tier-business-critical.md) tjänstnivåer. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Vad är hyperskala funktioner

Hyperskala tjänstnivån i Azure SQL Database tillhandahåller följande funktioner:

- Stöd för upp till 100 TB databasstorlek
- Stort sett ögonblickliga säkerhetskopior (baserat på ögonblicksbilder lagras i Azure Blob storage)-databas oavsett storlek utan i/o-inverkan på beräkning   
- Snabba återställningar för databasen (baserat på ögonblicksbilder) i minuter i stället för timmar eller dagar (inte en storlek på data igen)
- Högre övergripande prestanda på grund av högre log dataflöde och transaktionen commit snabbare oavsett datavolymer
- Snabb skala ut – du kan etablera en eller flera skrivskyddade noder för avlastning Läs arbetsbelastningen och användas som frekvent metoderna
- Snabbt skala upp – du kan, konstant tidpunkt, skala upp dina beräkningsresurser för tunga arbetsbelastningar när behövs och sedan skala beräkningsresurser tillbaka ned när det inte behövs.

Tjänstnivån hyperskala tar bort många av de praktiska gränserna traditionellt sett i molndatabaser. Databaser i hyperskala tjänstnivå har där de flesta andra databaser begränsas av de tillgängliga resurserna i en enda nod inga sådana gränser. Med dess flexibla lagringsarkitektur växer lagring efter behov. Hyperskala databaser skapas i själva verket inte med en definierad maxstorleken. En storskalig databas växer efter behov – och du debiteras endast för den kapacitet du använder. För läsintensiva arbetsbelastningar ger tjänstnivån hyperskala snabb skalbarhet genom att etablera ytterligare skrivskyddade repliker som behövs för att avlasta skrivskyddade arbetsbelastningar.

Dessutom tiden krävs för att skapa säkerhetskopior av databasen eller skala upp eller ned längre är knuten inte till mängden data i databasen. Hyperskala databaser kan säkerhetskopieras nästan omedelbart. Du kan även skala en databas i tiotals terabyte upp eller ned på några minuter. Den här funktionen behöver du inte orolig att boxed av valen inledande konfiguration.

Läs mer om storlekar för tjänstnivån hyperskala [tjänsten nivån egenskaper](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vilka bör överväga hyperskala tjänstnivån

Hyperskala tjänstnivå är främst avsett för kunder som har stora databaser antingen på plats och vill modernisera sina program genom att flytta till molnet eller för kunder som redan finns i molnet och begränsas av den maximala databasstorleken begränsningar (1 – 4 TB). Den är också avsedd för kunder som söker efter hög prestanda och hög skalbarhet för lagring och beräkning.

Tjänstnivån hyperskala stöder alla SQL Server-arbetsbelastningar, men den främst är optimerat för OLTP. Tjänstnivån hyperskala stöder också hybrid och analytiska arbetsbelastningar (datamart).

> [!IMPORTANT]
> Elastiska pooler har inte stöd för storskaliga tjänstnivån.

## <a name="hyperscale-pricing-model"></a>Hyperskala prismodellen

Hyperskala tjänstnivå finns bara i [vCore-modellen](sql-database-service-tiers-vcore.md). Så att den överensstämmer med den nya arkitekturen skiljer prismodellen sig från generell användning och affärskritiska tjänstnivåer:

- **Compute**:

  Enhetspriset för storskalig databearbetning är per replik. Den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) pris används för att läsa skala repliker automatiskt. Vi skapa en primär replik och en skrivskyddad replik per databas i hyperskala som standard.  Användare kan justera det totala antalet repliker inklusive primära från 1 till 5.

- **Storage**:

  Du behöver inte ange den maximala datastorleken när du konfigurerar en storskalig databas. På hyperskalningsnivån debiteras du för lagring för databasen baserat på faktisk användning. Storage tilldelas automatiskt mellan 10 GB och 100 TB, i steg som justeras dynamiskt mellan 10 och 40GB.  

Läs mer om priser för storskaliga [priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Distribuerade functions-arkitektur

Till skillnad från traditionella databasmotorer som har centraliserad alla hanteringsfunktioner för data i en enda plats/process (även så kallade distribuerade databaser i produktionsmiljön idag har flera kopior av en monolitisk motor), skiljer en storskalig databas bearbetning av motorn, där semantiken för olika datamotorer avvika från de komponenter som ger långsiktig lagring och hållbarhet för data. På så sätt kan lagringskapaciteten smidigt skalas ut så mycket som behövs (inledande mål är 100 TB). Skrivskyddade repliker dela samma lagringskomponenter så att ingen kopiering av data som krävs för att skapa en ny läsbar replik. 

Följande diagram illustrerar de olika typerna av noder i en storskalig databas:

![Arkitektur](./media/sql-database-hyperscale/hyperscale-architecture.png)

En storskalig databas innehåller följande typer av noder:

### <a name="compute-node"></a>Beräkningsnod

Beräkningsnoden är var den relationella motorn finns, så alla språkelement, frågebearbetning och så vidare, sker. Alla användarinteraktioner med en storskalig databas inträffar för beräkningsnoder. Compute-noder har SSD-baserad cacheminnen (märkta RBPEX - elastiska Buffertpooltillägget i föregående diagram) för att minimera antalet nätverk sändningar som krävs för att hämta en sida med data. Det finns en primär Beräkningsnoden där den skrivskyddade arbetsbelastningar och transaktioner bearbetas. Det finns en eller flera sekundära beräkningsnoder som fungerar som hot standby noder för växling vid fel, samt fungerar som skrivskyddade beräkningsnoder för avlastning läser arbetsbelastningar (om den här funktionen är det önskade).

### <a name="page-server-node"></a>Sidan server-nod

Sidan servrar är system som representerar en lagringsmotorn som skalats ut.  Varje sida server ansvarar för en delmängd av sidorna i databasen.  Så, varje sida kontroller mellan 128 GB och 1 TB data. Inga data delas på mer än en sida-server (utanför repliker som hålls för redundans och tillgänglighet). Jobbet för en sida-server är att skickar databassidor till compute-noder på begäran och som sidor uppdaterade transaktioner som uppdaterar data. Sidan servrar hålls uppdaterade genom att spela upp loggposter från Loggtjänsten. Sidan servrar innehåller också SSD-baserad cacheminnen för att förbättra prestandan. Långsiktig lagring av datasidor sparas i Azure Storage för extra tillförlitlighet.

### <a name="log-service-node"></a>Log service-noden

Noden log service accepterar poster från den primära Beräkningsnoden kvarstår dem i en beständig cache och vidarebefordrar loggposter till resten av compute-noder (så att de kan uppdatera sina cacheminnen) samt relevanta sidan-servrarna så att data kan vara uppdaterade t Här. På så sätt kan sprids alla ändringar från primära Beräkningsnoden via Loggtjänsten till alla sekundära compute-noder och sidan servrar. Slutligen skickas log-poster till långsiktig lagring i Azure Storage, vilket är en oändlig lagringsplats. Den här mekanismen tar bort nödvändigt vid frekventa loggtrunkering. Loggtjänsten har också lokal cache för att påskynda åtkomst.

### <a name="azure-storage-node"></a>Azure storage-nod

Azure storage-nod är slutdestinationen för data från sidan servrar. Den här lagringen används för säkerhetskopiering samt för replikering mellan Azure-regioner. Säkerhetskopior bestå av ögonblicksbilder av filer. Återställa åtgärden är snabba från de här ögonblicksbilderna och data kan återställas till valfri punkt i tiden.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopior av base och de är därför nästan omedelbart. Lagring och beräkning uppdelning aktivera push-överföring av säkerhetskopieringen/återställningen till storage-skiktet till minskning av bearbetning på den primära Beräkningsnoden. Säkerhetskopiering av en stor databas påverkar därför inte prestandan för den primära Beräkningsnoden. På samma sätt återställningar är klar genom att kopiera filen ögonblicksbilden och är därför inte en storlek på data igen. För återställningar inom samma lagringskonto går återställningsåtgärden snabbt.

## <a name="scale-and-performance-advantages"></a>Fördelarna med skalbarhet och prestanda

Med möjligheten att snabbt öka eller minska ytterligare skrivskyddad compute-noder kan hyperskala arkitekturen gör betydande läsa skalningsfunktioner och kan även frigöra primära Beräkningsnoden för att betjäna flera skrivbegäranden. Dessutom kan compute-noderna skalas upp och ned snabbt på grund av delad lagring-arkitektur i hyperskala-arkitekturen.

## <a name="create-a-hyperscale-database"></a>Skapa en databas i hyperskala

En storskalig databas kan skapas med den [Azure-portalen](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Hyperskala databaser är tillgängliga med enbart den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

Följande T-SQL-kommando skapar en databas i hyperskala. Du måste ange både edition och tjänsten mål i den `CREATE DATABASE` instruktionen.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Detta skapar en databas i hyperskala på Gen5 maskinvara med 4 kärnor.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrera en befintlig Azure SQL Database på tjänstnivån i hyperskala

Du kan flytta dina befintliga Azure SQL-databaser till hyperskala med hjälp av den [Azure-portalen](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Det här är en enkelriktad migrering just nu. Du kan inte flytta databaser från hyperskala till en annan tjänstnivå. Vi rekommenderar att du gör en kopia av produktionsdatabaserna och migrera till hyperskala för bevis på koncept (POC).

Följande T-SQL-kommando flyttar en databas i hyperskala tjänstnivån. Du måste ange både edition och tjänsten mål i den `ALTER DATABASE` instruktionen.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en lässkala replik av en storskalig databas

I hyperskala databaser, den `ApplicationIntent` argumentet i anslutningssträngen som tillhandahålls av klienten avgör om anslutningen dirigeras till skrivning repliken eller till en skrivskyddad sekundär replik. Om den `ApplicationIntent` inställd `READONLY` och databasen har inte en sekundär replik, anslutningen kommer att dirigeras till den primära repliken och standardvärdet är `ReadWrite` beteende.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveriberedskap för storskaliga databaser
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Återställa en storskalig databas till ett annat geografiskt område
Om du vill återställa en Azure SQL Database hyperskala-databas till en annan region än den som den finns i, som en del av en katastrofåterställning eller ökad detaljnivå, flytt eller någon annan anledning är det bästa sättet att göra en geo-återställning av databasen.  Detta omfattar exakt likadant som det du använder för att återställa alla andra AZURE SQL-databas till en annan region:
1. Skapa en SQL Database-server i målregionen om du inte redan har en lämplig server det.  Den här servern ska ägas av samma prenumeration som den ursprungliga (källa)-servern.
2. Följ instruktionerna i den [geo-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) avsnittet på sidan om hur du återställer Azure SQL-databaser från automatiska säkerhetskopieringar.

#### <a name="notes-on-geo-restores-of-a-hyperscale-database"></a>Anmärkningar om geo-återställning av en storskalig databas
Eftersom källan och målet finns i olika områden, kan inte databasen dela lagringsutrymme för ögonblicksbilder med källdatabasen som icke-geo-återställning, som Slutför mycket snabbt.  När det gäller en geo-återställning av en storskalig databas blir det en storlek för data-åtgärd, även om målet är i den parade regionen för geo-replikerade lagringsutrymmen.  Det innebär att göra en geo-återställning tar tid proportion till storleken på den databas som återställs.  Om målet är i den parade regionen, kopian inom ett datacenter, vilket kommer att vara betydligt snabbare än en fjärrsamtal kopia via internet, men den kommer fortfarande att kopiera alla bitar.

## <a name=regions></a>Tillgängliga regioner

Azure SQL Database hyperskala-nivån är nu tillgänglig i följande regioner:

- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, centrala
- Centrala USA
- Kina, östra 2
- Kina, norra 2
- Asien, östra
- Östra USA
- Östra USA 2
- Centrala Frankrike
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Sydkorea, södra
- USA, norra centrala
- Europa, norra
- Sydafrika, norra
- USA, södra centrala
- Sydostasien
- Södra Storbritannien
- Västra Storbritannien
- Europa, västra
- USA, västra
- USA, västra 2

Om du vill skapa storskalig databas i en region som inte anges som stöds kan du skicka en begäran om registrering via Azure-portalen. Vi arbetar för att expandera listan över regioner som stöds, så kontrollera tillbaka för senaste regionlistan.

Så här begär möjligheten att skapa storskaliga databaser i regioner som inte finns i listan:

1. Gå till [Azure hjälp och Support-bladet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Klicka på [ **ny supportbegäran**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure hjälp och Support-bladet](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. För **ärendetypen**väljer **begränsningar för tjänsten och -prenumeration (kvoter)**

4. Välj den prenumeration som du använder för att skapa databaser

5. För **Kvottypen**väljer **SQL-databas**

6. Klicka på **Nästa: Lösningar**

1. Klicka på **ange information**

    ![Uppgifter om problem](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Välj **kvottyp för SQL Database**: **Annan kvotförfrågan**

9. Fyll i följande mall:

    ![Kvotinformation](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    Ange följande information i mallen

    > Begäran om att skapa Azure hyperskala SQL-databas i en ny region<br/> Region: [Fyll i den efterfrågade regionen]  <br/>
    > Compute-SKU/Totalt antal kärnor, inklusive läsbara repliker <br/>
    > Antalet TB beräknat 
    >

10. Välj **Allvarlighetsgrad C**

11. Välj kontaktmetod som är lämplig och Fyll i information.

12. Klicka på **spara** och **fortsätta**

## <a name="known-limitations"></a>Kända begränsningar
Här är de aktuella begränsningarna på hyperskala tjänstnivån från och med GA.  Vi arbetar aktivt för att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| Fönstret hantera säkerhetskopior för en logisk server inte visar filtreras hyperskala databaser från SQLServer ->  | Hyperskala har en annan metod för att hantera säkerhetskopior, och därför långsiktig kvarhållning och punkt i tiden säkerhetskopiering kvarhållningsinställningar gäller inte / ogiltigförklaras. Hyperskala databaser visas därför inte i fönstret hantera säkerhetskopiering. |
| Återställning från tidpunkt | När en databas har migrerats till hyperskala tjänstnivån, stöds inte återställning till en punkt i tid före migreringen.|
| Återställning av icke - hyperskala DB Hypserscale och vice versa | Du kan inte återställa en storskalig databas till en icke-hyperskala-databas och inte heller kan du återställa en databas för icke-hyperskala i en storskalig databas.|
| Om en databasfil växer under migreringen på grund av en aktiv arbetsbelastning och korsar 1 TB per fil gräns, misslyckas migreringen | Åtgärder: <br> -Om möjligt, migrera databasen när det finns ingen uppdatering av arbetsbelastningar som körs.<br> -Försök igen migreringen, kommer att kunna så länge gränsen på 1 TB inte uppnås under migreringen.|
| Managed Instance | Azure SQL Database Managed Instance stöds inte för närvarande med hyperskala databaser. |
| Elastiska pooler |  Elastiska pooler stöds inte för närvarande med SQL Database hyperskala.|
| Migrering till hyperskala är för närvarande en enkel åtgärd | När en databas har migrerats till hyperskala, kan inte migreras direkt till en icke-hyperskala tjänstnivå. För närvarande är det enda sättet att migrera en databas från hyperskala till icke-hyperskala att exportera/importera med hjälp av en BACPAC-fil.|
| Migrering av databaser med InMemory-objekt | InMemory-objekt måste släppas och återskapas som icke-InMemory-objekt innan du migrerar en databas på hyperskala tjänstnivån.|
| Data för ändringsspårning | Du kommer inte att kunna använda ändringsspårning Data med hyperskala databaser. |
| Geo-replikering  | Du kan inte ännu konfigurera geo-replikering för Azure SQL Database hyperskala.  Du kan utföra geo-återställning (återställer databasen i ett annat geografiskt område, för DR- eller andra ändamål) |
| TDE/AKV-integreringen | Transparent databaskryptering med Azure Key Vault (kallas ofta Bring-Your-Own-nyckel eller BYOK) stöds inte ännu för Azure SQL Database i hyperskala men TDE med tjänsten hanterade nycklar stöds fullt ut. |
|Intelligent databasfunktioner | 1. Skapa Index, Drop Index rådgivare modeller har tränats inte för storskaliga databaser. <br/>2. Schemaproblemet, DbParameterization - nyligen tillagda rådgivare inte stöds för storskalig databas.|



## <a name="next-steps"></a>Nästa steg

- Frågor och svar i hyperskala, se [vanliga frågor och svar om hyperskala](sql-database-service-tier-hyperscale-faq.md).
- Information om tjänstnivåer finns i [tjänstnivåer](sql-database-service-tiers.md)
- Se [översikt över resource begränsar på en logisk server](sql-database-resource-limits-logical-server.md) information om begränsningar på de server och prenumeration.
- Köpa modellen gränser för en enskild databas, finns i [Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md).
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
