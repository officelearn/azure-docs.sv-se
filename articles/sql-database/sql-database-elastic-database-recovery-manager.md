---
title: Återställningshanteraren för att åtgärda problem med fragmentkartan
description: Använd klassen RecoveryManager för att lösa problem med fragmentkartor
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823866"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Korrigera shard-kartproblem med RecoveryManager-klassen

Klassen [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) ger ADO.NET program möjlighet att enkelt identifiera och korrigera eventuella inkonsekvenser mellan den globala fragmentkartan (GSM) och den lokala fragmentkartan (LSM) i en fragmenterad databasmiljö.

GSM och LSM spårar mappningen av varje databas i en fragmenterad miljö. Ibland uppstår en paus mellan GSM och LSM. I så fall använder klassen RecoveryManager för att identifiera och reparera rasten.

Klassen RecoveryManager är en del av [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md).

![Shard karta][1]

För termdefinitioner finns i [Ordlistan för elastiska databasverktyg](sql-database-elastic-scale-glossary.md). Information om hur **ShardMapManager** används för att hantera data i en fragmenterad lösning finns i [Shard map management](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Varför använda återställningshanteraren

I en fragmenterad databasmiljö finns det en klient per databas och många databaser per server. Det kan också finnas många servrar i miljön. Varje databas mappas i fragmentkartan, så att anrop kan dirigeras till rätt server och databas. Databaser spåras enligt en **sharding-nyckel**och varje shard tilldelas ett **intervall med nyckelvärden**. En sharding-nyckel kan till exempel representera kundnamnen från "D" till "F". Mappningen av alla shards (aka databaser) och deras mappningsintervall finns i den **globala fragmentkartan (GSM)**. Varje databas innehåller också en karta över de områden som finns på shard som kallas den **lokala fragmentkartan (LSM)**. När en app ansluter till en fragment cachelagras mappningen med appen för snabb hämtning. LSM används för att validera cachelagrade data.

GSM och LSM kan bli ur synk av följande skäl:

1. Borttagningen av en shard vars intervall tros inte längre användas, eller byta namn på en shard. Om du tar bort en shard resulterar det i en **överbliven fragmentmappning**. På samma sätt kan en omdöpt databas orsaka en överbliven fragmentmappning. Beroende på avsikten med ändringen kan shard behöva tas bort eller fragmentplatsen måste uppdateras. Information om hur du återställer en borttagen databas finns i [Återställa en borttagen databas](sql-database-recovery-using-backups.md).
2. En geo-redundanshändelse inträffar. För att fortsätta måste man uppdatera servernamnet och databasnamnet för shard map manager i programmet och sedan uppdatera fragmentmappningsinformationen för alla shards i en fragmentkarta. Om det finns en geo-redundans bör sådan återställningslogik automatiseras i redundansarbetsflödet. Genom att automatisera återställningsåtgärder kan du undvika en friktionsfri hanterbarhet för geoaktiverade databaser och manuella åtgärder för mänskliga åtgärder undviks. Mer information om alternativ för att återställa en databas om det finns ett avbrott i ett datacenter finns i [Kontinuitet](sql-database-business-continuity.md) och [haveriberedskap](sql-database-disaster-recovery.md)för ett datacenter .
3. Antingen en shard eller ShardMapManager-databasen återställs till en tidigare tidpunkt. Mer information om tidsåterställning med hjälp av säkerhetskopior finns i [Återställning med hjälp av säkerhetskopior](sql-database-recovery-using-backups.md).

Mer information om Azure SQL Database Elastic Database-verktyg, georeplikering och Återställning finns i följande:

* [Översikt: Molnaffärskontinuitet och databaskatastrofåterställning med SQL Database](sql-database-business-continuity.md)
* [Komma igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md)  
* [Hantering av ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Hämta RecoveryManager från en ShardMapManager

Det första steget är att skapa en RecoveryManager-instans. [Metoden GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) returnerar återställningshanteraren för den aktuella [ShardMapManager-instansen.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Om du vill åtgärda eventuella inkonsekvenser i fragmentkartan måste du först hämta RecoveryManager för den specifika fragmentkartan.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

I det här exemplet initieras RecoveryManager från ShardMapManager. ShardMapManager som innehåller en ShardMap har också redan initierats.

Eftersom den här programkoden manipulerar själva fragmentmappningen bör autentiseringsuppgifterna som används i fabriksmetoden (i föregående exempel smmConnectionString) vara autentiseringsuppgifter som har läs-skrivbehörighet för GSM-databasen som refereras av anslutningen Sträng. Dessa autentiseringsuppgifter skiljer sig vanligtvis från autentiseringsuppgifter som används för att öppna anslutningar för databeroende routning. Mer information finns [i Använda autentiseringsuppgifter i den elastiska databasklienten](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Ta bort en fragment från ShardMap när en fragment har tagits bort

Metoden [Ta bortShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) tar bort den angivna fragmentet från fragmentkartan och tar bort mappningar som är associerade med fragmentet.  

* Platsparametern är fragmentplatsen, särskilt servernamnet och databasnamnet, för den fragment som tas bort.
* Parametern shardMapName är fragmentmappningsnamnet. Detta krävs endast när flera fragmentkartor hanteras av samma fragmentkarthanterare. Valfri.

> [!IMPORTANT]
> Använd bara den här tekniken om du är säker på att intervallet för den uppdaterade mappningen är tomt. Metoderna ovan kontrollerar inte data för det intervall som flyttas, så det är bäst att inkludera kontroller i koden.

I det här exemplet tas shards bort från fragmentkartan.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Fragmentkartan visar fragmentplatsen i GSM innan fragmentet tas bort. Eftersom fragmentet togs bort antas det att detta var avsiktligt och nyckelintervallet för sharding används inte längre. Om inte, kan du köra point-in tidsåterställning. för att återställa shard från en tidigare point-in-time. (I så fall bör du granska följande avsnitt för att upptäcka fragmentkonsekvenser.) För att återställa, se [Tidpunktåterställning](sql-database-recovery-using-backups.md).

Eftersom det antas att databasborttagningen var avsiktlig, är den slutliga administrativa rensningsåtgärden att ta bort posten till fragmentet i fragmentkarthanteraren. Detta förhindrar att programmet oavsiktligt skriver information till ett intervall som inte förväntas.

## <a name="to-detect-mapping-differences"></a>Så här identifierar du mappningsskillnader

Metoden [DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) väljer och returnerar en av fragmentkartorna (antingen lokala eller globala) som sanningskälla och stämmer av mappningar på båda fragmentkartorna (GSM och LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Platsen* anger servernamnet och databasnamnet.
* Parametern *shardMapName* är fragmentmappningsnamnet. Detta krävs endast om flera fragmentkartor hanteras av samma fragmentkarthanterare. Valfri.

## <a name="to-resolve-mapping-differences"></a>Så här löser du mappningsskillnader

Metoden [ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) väljer en av fragmentkartorna (antingen lokala eller globala) som sanningskälla och stämmer av mappningar på båda fragmentkartorna (GSM och LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Parametern *RecoveryToken* räknar upp skillnaderna i mappningarna mellan GSM och LSM för den specifika shard.
* [MappingDifferenceResolution-uppräkning](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) används för att ange metoden för att lösa skillnaden mellan fragmentmappningarna.
* **MappingDifferenceResolution.KeepShardMapping** rekommenderas att när LSM innehåller en korrekt mappning och därför bör mappningen i shard användas. Detta är vanligtvis fallet om det finns en redundans: shard finns nu på en ny server. Eftersom shard först måste tas bort från GSM (med metoden RecoveryManager.Ta bortShard) finns det inte längre en mappning på GSM. Därför måste LSM användas för att återupprätta fragmentmappningen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Koppla en shard till ShardMap när en shard har återställts

Metoden [AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) kopplar den angivna fragmentet till fragmentkartan. Den identifierar sedan eventuella fragmentkartkonsekvenser och uppdaterar mappningarna så att de matchar fragmentet vid punkten för fragmentåterställning. Det antas att databasen också har bytt namn för att återspegla det ursprungliga databasnamnet (innan shard återställdes), eftersom tidsåterställningsstandarden för punkttid till en ny databas som läggs till med tidsstämpeln.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Platsparametern är servernamnet och databasnamnet för den fragment som bifogas. *location*
* Parametern *shardMapName* är fragmentmappningsnamnet. Detta krävs endast när flera fragmentkartor hanteras av samma fragmentkarthanterare. Valfri.

I det här exemplet läggs en shard till fragmentkartan som nyligen har återställts från en tidigare tidpunkt. Eftersom shard (det vill säga mappningen för fragmentet i LSM) har återställts, är det potentiellt oförenligt med fragmentposten i GSM. Utanför den här exempelkoden återställdes shard och bytte namn till databasens ursprungliga namn. Eftersom den återställdes antas mappningen i LSM vara den betrodda mappningen.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Uppdatera fragmentplatser efter en geo-redundans (återställning) av shards

Om det finns en geo-redundans görs den sekundära databasen skrivbar och blir den nya primära databasen. Namnet på servern och eventuellt databasen (beroende på din konfiguration) kan skilja sig från den ursprungliga primär. Därför måste mappningsposterna för fragmentet i GSM och LSM åtgärdas. Om databasen återställs till ett annat namn eller en annan plats, eller till en tidigare tidpunkt, kan det orsaka inkonsekvenser i fragmentmappningarna. Shard Map Manager hanterar distributionen av öppna anslutningar till rätt databas. Distributionen baseras på data i fragmentkartan och värdet för sharding-nyckeln som är målet för programbegäran. Efter en geo-redundans måste den här informationen uppdateras med korrekt servernamn, databasnamn och fragmentmappning av den återställda databasen.

## <a name="best-practices"></a>Bästa praxis

Geo-redundans och återställning är åtgärder som vanligtvis hanteras av en molnadministratör för programmet som avsiktligt använder en av Azure SQL-databasers affärskontinuitetsfunktioner. Kontinuitetsplanering kräver processer, procedurer och åtgärder för att säkerställa att verksamheten kan fortsätta utan avbrott. De metoder som är tillgängliga som en del av klassen RecoveryManager bör användas inom detta arbetsflöde för att säkerställa att GSM och LSM hålls uppdaterade baserat på de återställningsåtgärder som vidtagits. Det finns fem grundläggande steg för att säkerställa att GSM och LSM återspeglar korrekt information efter en redundanshändelse. Programkoden för att köra dessa steg kan integreras i befintliga verktyg och arbetsflöden.

1. Hämta RecoveryManager från ShardMapManager.
2. Ta bort den gamla fragmenten från fragmentkartan.
3. Koppla den nya fragmentet till fragmentkartan, inklusive den nya fragmentplatsen.
4. Identifiera inkonsekvenser i mappningen mellan GSM och LSM.
5. Lös skillnader mellan GSM och LSM, lita på LSM.

I det här exemplet utförs följande steg:

1. Tar bort shards från fragmentkartan som återspeglar fragmentplatser före redundanshändelsen.
2. Kopplar shards till Shard-kartan som återspeglar de nya fragmentplatserna (parametern "Configuration.SecondaryServer" är det nya servernamnet men samma databasnamn).
3. Hämtar återställningstoken genom att identifiera mappningsskillnader mellan GSM och LSM för varje shard.
4. Löser inkonsekvenser genom att lita på mappningen från LSM för varje shard.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
