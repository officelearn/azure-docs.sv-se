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
ms.date: 10/15/2018
ms.openlocfilehash: 372f1a0b7e2ad07612caaac478aea14693e002fa
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352310"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>Hyperskala tjänstnivå (förhandsversion) för upp till 100 TB

Hyperskala tjänstnivån i Azure SQL Database är den senaste tjänstnivån i den vCore-baserade inköpsmodellen. Den här är en mycket skalbar lagring och beräkning prestandanivå som utnyttjar Azure-arkitektur för att skala ut lagring och beräkna resurser för en Azure SQL Database avsevärt utöver gränserna som är tillgängliga för generell användning och företag Kritiska tjänstnivåer.

> [!IMPORTANT]
> Hyperskala tjänstnivå är för närvarande i offentlig förhandsversion och är tillgängliga i begränsade Azure-regioner. Regionlista fullständig finns i [hyperskala tjänsten nivå tillgängliga regioner](#available-regions). Vi rekommenderar inte någon produktionsarbetsbelastning körs ännu i hyperskala databaser. Du kan inte uppdatera en storskalig databas till andra tjänstnivåer. För testning syfte rekommenderar vi du gör en kopia av den aktuella databasen och uppdatera kopian hyperskala tjänstnivån.
> [!NOTE]
> Mer information om tjänstnivåer generell användning och affärskritisk i den vCore-baserade inköpsmodellen finns [tjänstnivåer för generell användning och affärskritisk](sql-database-service-tiers-general-purpose-business-critical.md). En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-service-tiers.md).
> [!IMPORTANT]
> Hyperskala tjänstnivå finns för närvarande i offentlig förhandsversion. Vi rekommenderar inte någon produktionsarbetsbelastning körs ännu i hyperskala databaser. Du kan inte uppdatera en storskalig databas till andra tjänstnivåer. För testning syfte rekommenderar vi du gör en kopia av den aktuella databasen och uppdatera kopian hyperskala tjänstnivån.

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

  Enhetspriset för storskalig databearbetning är per replik. Den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) pris används för att läsa skala repliker automatiskt. I offentlig förhandsversion skapar vi två repliker per databas i hyperskala som standard.

- **Storage**:

  Du behöver inte ange den maximala datastorleken när du konfigurerar en storskalig databas. På hyperskalningsnivån debiteras du för lagring för databasen baserat på faktisk användning. Lagring allokeras dynamiskt mellan 5 GB och 100 TB, i steg om 1 GB.  

Läs mer om priser för storskaliga [priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Distribuerade functions-arkitektur

Till skillnad från traditionella databasmotorer som har centraliserad alla hanteringsfunktioner för data i en enda plats/process (även så kallade distribuerade databaser i produktionsmiljön idag har flera kopior av en monolitisk motor), skiljer en storskalig databas bearbetning av motorn, där semantiken för olika datamotorer avvika från de komponenter som ger långsiktig lagring och hållbarhet för data. På så sätt kan lagringskapaciteten smidigt skalas ut så mycket som behövs (inledande mål är 100 TB). Skrivskyddade repliker dela samma beräkning komponenter så att ingen kopiering av data som krävs för att skapa en ny läsbar replik. I förhandsversionen stöds endast 1 skrivskyddad replik.

Följande diagram illustrerar de olika typerna av noder i en storskalig databas:

![Arkitektur](./media/sql-database-hyperscale/hyperscale-architecture.png)

En storskalig databas innehåller följande typer av noder:

### <a name="compute-node"></a>Beräkningsnod

Beräkningsnoden är var den relationella motorn finns, så alla språkelement, frågebearbetning och så vidare, sker. Alla användarinteraktioner med en storskalig databas inträffar för beräkningsnoder. Compute-noder har SSD-baserad cacheminnen (märkta RBPEX - elastiska Buffertpooltillägget i föregående diagram) för att minimera antalet nätverk sändningar som krävs för att hämta en sida med data. Det finns en primär Beräkningsnoden där den skrivskyddade arbetsbelastningar och transaktioner bearbetas. Det finns en eller flera sekundära beräkningsnoder som fungerar som hot standby noder för växling vid fel, samt fungerar som skrivskyddade beräkningsnoder för avlastning läser arbetsbelastningar (om den här funktionen är det önskade).

### <a name="page-server-node"></a>Sidan server-nod

Sidan servrar är system som representerar en lagringsmotorn som skalats ut.  Varje sida server ansvarar för en delmängd av sidorna i databasen.  Varje sida server styr praktiskt taget, 1 terabyte data. Inga data delas på mer än en sida-server (utanför repliker som hålls för redundans och tillgänglighet). Jobbet för en sida-server är att skickar databassidor till compute-noder på begäran och som sidor uppdaterade transaktioner som uppdaterar data. Sidan servrar hålls uppdaterade genom att spela upp loggposter från Loggtjänsten. Sidan servrar innehåller också SSD-baserad cacheminnen för att förbättra prestandan. Långsiktig lagring av datasidor sparas i Azure Storage för extra tillförlitlighet.

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
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrera en befintlig Azure SQL Database på tjänstnivån i hyperskala

Du kan flytta dina befintliga Azure SQL-databaser till hyperskala med hjälp av den [Azure-portalen](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). I offentlig förhandsversion är det en enkelriktad migrering. Du kan inte flytta databaser från hyperskala till en annan tjänstnivå. Vi rekommenderar att du gör en kopia av produktionsdatabaserna och migrera till hyperskala för bevis på koncept (POC).

Följande T-SQL-kommando flyttar en databas i hyperskala tjänstnivån. Du måste ange både edition och tjänsten mål i den `ALTER DATABASE` instruktionen.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

> [!IMPORTANT]
> [Transparent databasen datakryptering (TDE)](transparent-data-encryption-azure-sql.md) bör stängas av innan du ändrar en databas för icke-hyperskala att hyperskala.

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en lässkala replik av en storskalig databas

I hyperskala databaser, den `ApplicationIntent` argumentet i anslutningssträngen som tillhandahålls av klienten avgör om anslutningen dirigeras till skrivning repliken eller till en skrivskyddad sekundär replik. Om den `ApplicationIntent` inställd `READONLY` och databasen har inte en sekundär replik, anslutningen kommer att dirigeras till den primära repliken och standardvärdet är `ReadWrite` beteende.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>Tillgängliga regioner

Hyperskala tjänstnivå är för närvarande i offentlig förhandsversion och är tillgängliga i följande Azure-regioner: EastUS1, Usaöstra2, västra USA 2, CentralUS, Usanorracentrala, Europavästra, Europanorra, Västrastorbritannien, Australien, sydöstra Australien, SouthEastAsia, Sydostasien, Koreacentrala

## <a name="known-limitations"></a>Kända begränsningar

| Problem | Beskrivning |
| :---- | :--------- |
| ManageBackups fönstret för en logisk server inte visar filtreras hyperskala databaser från SQLServer ->  | Hyperskala har en annan metod för att hantera säkerhetskopior, och därför långsiktig kvarhållning och punkt i tiden säkerhetskopiering kvarhållningsinställningar gäller inte / ogiltigförklaras. Hyperskala databaser visas därför inte i fönstret hantera säkerhetskopiering. |
| Återställning från tidpunkt | När en databas har migrerats till hyperskala tjänstnivån, stöds inte återställning till point-in-tIme.|
| Om en databasfil växer under migreringen på grund av en aktiv arbetsbelastning och korsar 1 TB per fil gräns, misslyckas migreringen | Åtgärder: <br> -Om möjligt, migrera databasen när det finns ingen uppdatering av arbetsbelastningar som körs.<br> -Försök igen migreringen, kommer att kunna så länge gränsen på 1 TB inte uppnås under migreringen.|
| Hanterad instans stöds inte för närvarande | Stöds för närvarande inte |
| Migrering till hyperskala är för närvarande en enkel åtgärd | När en databas har migrerats till hyperskala, kan inte migreras direkt till en icke-hyperskala tjänstnivå. För närvarande är det enda sättet att migrera en databas från hyperskala till icke-hyperskala att exportera/importera med hjälp av en BACPAC-fil.|
| Migrering av databaser med InMemory-objekt stöds inte för närvarande | InMemory-objekt måste släppas och återskapas som icke-InMemory-objekt innan du migrerar en databas på hyperskala tjänstnivån.

## <a name="next-steps"></a>Nästa steg

- Frågor och svar i hyperskala, se [vanliga frågor och svar om hyperskala](sql-database-service-tier-hyperscale-faq.md).
- Information om tjänstnivåer finns i [tjänstnivåer](sql-database-service-tiers.md)
- Se [översikt över resource begränsar på en logisk server](sql-database-resource-limits-logical-server.md) information om begränsningar på de server och prenumeration.
- Köpa modellen gränser för en enskild databas, finns i [Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md).
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
