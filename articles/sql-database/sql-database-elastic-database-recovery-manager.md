---
title: Rätta Fragmentera kartan problem med hjälp av Recovery Manager | Microsoft Docs
description: Använd klassen RecoveryManager för att lösa problem med Fragmentera maps
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 350ea0d4b744467849916f2d958cc49fd72d3e4b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Korrigera shard-kartproblem med RecoveryManager-klassen
Den [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) klassen innehåller ADO.Net-program möjlighet att enkelt identifiera och åtgärda eventuella inkonsekvenser mellan globala Fragmentera karta (GSM) och lokala Fragmentera kartan (LSM) i en databasmiljö med delat. 

Spåra mappningen för varje databas i ett delat GSM och LSM. Ibland kan inträffar ett avbrott mellan GSM och LSM. I så fall använder du klassen RecoveryManager att identifiera och reparera avbrottet.

Klassen RecoveryManager är en del av den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md). 

![Fragmentera karta][1]

Termdefinitioner finns [elastisk databas verktyg ordlista](sql-database-elastic-scale-glossary.md). Att förstå hur **ShardMapManager** används för att hantera data i ett delat lösningen, se [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Varför använda recovery manager?
I en databasmiljö med delat finns en klient per databas och många databaser per server. Det kan också finnas flera servrar i miljön. Varje databas mappas i kartan Fragmentera så anrop kan dirigeras till rätt server och databas. Databaser spåras enligt en **horisontell partitionering nyckeln**, och varje Fragmentera har tilldelats en **viktiga värdeintervallet**. En nyckel för horisontell partitionering kan till exempel representerar kundernas namn från ”D” till ”F.” Mappningen av alla delar (aka databaser) och intervallen mappningen finns i den **globala Fragmentera karta (GSM)**. Varje databas innehåller också en karta områden som finns på Fragmentera som kallas för den **lokala Fragmentera karta (LSM)**. När en app som ansluter till en Fragmentera, cachelagras mappningen med appen för snabb hämtning. LSM används för att verifiera cachelagrade data. 

GSM och LSM kan bli synkroniserad av följande skäl:

1. Borttagningen av en Fragmentera vars intervallet anses inte längre används eller ändra namn på en Fragmentera. Om du tar bort en Fragmentera resulterar i en **frånkopplade Fragmentera mappning**. På liknande sätt kan har bytt namn till databasen orsaka en överbliven Fragmentera mappning. Beroende på syftet med ändringen av Fragmentera kan behöva tas bort eller Fragmentera platsen behöver uppdateras. Om du vill återställa en borttagen databas finns [återställa en borttagen databas](sql-database-recovery-using-backups.md).
2. Geo-redundans inträffar. Om du vill fortsätta, en uppdatera servernamnet och databasnamnet på Fragmentera kartan manager i programmet och uppdatera Fragmentera mappning information för alla delar i en Fragmentera karta. Om det finns en geo-redundans, bör sådana recovery logik automatiseras arbetsflödet växling vid fel. Automatisera återställningsåtgärder aktiverar ett friktionsfritt hanterbarhet för geo-aktiverade databaser och undviker mänsklig manuella åtgärder. Mer information om alternativ för att återställa en databas om det finns ett avbrott för data center, se [företagskontinuitet](sql-database-business-continuity.md) och [Disaster Recovery](sql-database-disaster-recovery.md).
3. En Fragmentera eller ShardMapManager databasen återställs till en tidigare punkt i tiden. Läs om punkten i tidsåterställningen med hjälp av säkerhetskopior i [återställning med hjälp av säkerhetskopior](sql-database-recovery-using-backups.md).

Mer information om Azure SQL Database elastisk Databasverktyg, geo-replikering och återställning finns i följande: 

* [Översikt: Molnet business continuity och databasen katastrofåterställning med SQL-databas](sql-database-business-continuity.md) 
* [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md)  
* [Hantering av ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Hämtar RecoveryManager från en ShardMapManager
Det första steget är att skapa en RecoveryManager-instans. Den [GetRecoveryManager metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) returnerar recovery manager för aktuellt [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) instans. Du måste hämta RecoveryManager för viss Fragmentera kartan för att åtgärda eventuella inkonsekvenser i mappningen Fragmentera. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

RecoveryManager initieras från ShardMapManager i det här exemplet. ShardMapManager som innehåller en ShardMap har också redan initierats. 

Eftersom den här programkod manipulerar Fragmentera kartan sig själv, ska autentiseringsuppgifter som har skrivskyddad behörighet på databasen för GSM som refereras av anslutningssträngen med de autentiseringsuppgifter som används i fabriksmetoden (i föregående exempel smmConnectionString). Autentiseringsuppgifterna är vanligtvis skiljer sig från autentiseringsuppgifterna som används för att öppna anslutningar för data-beroende routning. Mer information finns i [med autentiseringsuppgifter i klienten för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Ta bort en Fragmentera från ShardMap efter en Fragmentera har tagits bort
Den [DetachShard metoden](https://msdn.microsoft.com/library/azure/dn842083.aspx) tar bort den angivna Fragmentera från Fragmentera mappningen och tar bort mappningar som är associerade med Fragmentera.  

* Platsparametern är Fragmentera plats, särskilt servernamnet och databasnamnet på Fragmentera som frånkopplat. 
* Parametern shardMapName är Fragmentera mappningsnamn. Detta är endast krävs när flera Fragmentera maps hanteras av hanteraren för mappning av samma Fragmentera. Valfri. 


> [!IMPORTANT]
> Använd den här tekniken bara om du är säker på att intervallet för uppdaterade mappningen är tom. Metoderna ovan Kontrollera inte data för intervallet som ska flyttas, så det är bäst att lägga till kontroller i din kod.
>

Det här exemplet tar bort shards från Fragmentera kartan. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

Fragmentera kartan visar Fragmentera plats i GSM innan borttagningen av Fragmentera. Eftersom Fragmentera har tagits bort, förutsätts det var avsiktlig och vilka horisontell partitionering är inte längre används. Om inte, kan du köra punkt i tidpunkt för återställning. att återställa Fragmentera från en tidigare punkt i tiden. (I så fall granska följande avsnitt för att identifiera Fragmentera inkonsekvenser.) Om du vill återställa, se [punkten i tidsåterställningen](sql-database-recovery-using-backups.md).

Eftersom det förutsätts att databasen var avsiktlig är åtgärden slutliga administrativa rensa bort posten till Fragmentera i Fragmentera kartan manager. Detta hindrar programmet från oavsiktligt skriva information till ett område som inte förväntas.

## <a name="to-detect-mapping-differences"></a>Att identifiera mappning skillnader
Den [DetectMappingDifferences metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) markerar och returnerar en av Fragmentera mappningar (lokal eller global) som källa för sanningen och synkroniserar mappningar på båda Fragmentera maps (GSM och LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* Den *plats* anger servernamnet och databasnamnet. 
* Den *shardMapName* parametern är Fragmentera mappningsnamn. Detta är endast krävs om flera Fragmentera maps hanteras av hanteraren för mappning av samma Fragmentera. Valfri. 

## <a name="to-resolve-mapping-differences"></a>Lösa mappning skillnader
Den [ResolveMappingDifferences metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) väljer ett av Fragmentera maps (lokal eller global) som källa för sanningen och synkroniserar mappningar på båda Fragmentera maps (GSM och LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Den *RecoveryToken* parametern räknar skillnader i mappningar mellan GSM och LSM för specifika Fragmentera. 
* Den [MappingDifferenceResolution uppräkningen](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) används för att ange metod för att lösa skillnaden mellan Fragmentera mappningarna. 
* **MappingDifferenceResolution.KeepShardMapping** när LSM innehåller korrekt mappning och därför mappningen i Fragmentera ska användas. Detta gäller vanligtvis om det finns en växling vid fel: Fragmentera finns nu på en ny server. Eftersom Fragmentera måste först tas bort från GSM (metoden RecoveryManager.DetachShard), finns inte längre en mappning på GSM. LSM måste därför användas för att återupprätta Fragmentera mappningen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Koppla en Fragmentera till ShardMap när en Fragmentera har återställts
Den [AttachShard metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) bifogar angivna Fragmentera Fragmentera kartan. Sedan identifierar inkonsekvenser Fragmentera kartan och uppdaterar mappningar för att matcha Fragmentera vid återställningen Fragmentera. Det förutsätts att databasen är också har fått nytt namn för att återspegla det ursprungliga databasnamnet (innan Fragmentera återställdes), eftersom punkt i tiden återställningen som standard till en ny databas läggas till med tidsstämpeln. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* Den *plats* parametern är servernamnet och databasnamnet på Fragmentera som kopplas. 
* Den *shardMapName* parametern är Fragmentera mappningsnamn. Detta är endast krävs när flera Fragmentera maps hanteras av hanteraren för mappning av samma Fragmentera. Valfri. 

Det här exemplet lägger till en Fragmentera Fragmentera kartan som nyligen har återställts från en tidigare punkt i tiden. Eftersom Fragmentera (dvs mappning för Fragmentera i LSM) har återställts, är det eventuellt inkonsekvent med posten i GSM Fragmentera. Fragmentera var utanför den här exempelkoden återställas och bytt namn till det ursprungliga namnet på databasen. Eftersom den har återställts, förutsätts mappningen i LSM är betrodd mappningen. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Uppdatera Fragmentera platser efter en geo-redundans (återställa) för shards
Om det finns en geo-redundans, den sekundära databasen är åtkomlig skriva och blir den nya primära databasen. Namnet på servern och potentiellt databasen (beroende på din konfiguration), kan skilja sig från den ursprungliga primära servern. Därför måste avbildningar för Fragmentera i GSM och LSM åtgärdas. På liknande sätt, databasen har återställts till en plats eller ett annat namn eller till en tidigare tidpunkt, kan detta orsaka inkonsekvenser i Fragmentera maps. Fragmentera kartan Manager hanterar distributionen av öppna anslutningar till rätt databas. Distribution baserad på data i kartan Fragmentera och värdet för nyckeln för horisontell partitionering som är mål för program-begäran. Efter en geo-redundans, måste den här informationen uppdateras med korrekt servernamnet, databasnamnet och Fragmentera mappning av den återställda databasen. 

## <a name="best-practices"></a>Bästa praxis
GEO-redundans och återställning är åtgärder som vanligtvis hanteras av en administratör som molnet för det program som använder en Azure SQL-databaser funktionerna för verksamhetskontinuitet avsiktligt. Kontinuitet planering kräver processer, procedurer och åtgärder för att säkerställa att verksamheten kan fortsätta utan avbrott. Tillgängliga metoder baserat som en del av klassen RecoveryManager bör användas inom plan för att säkerställa GSM och LSM hålls uppdaterade på recovery åtgärd. Det finns fem grundläggande steg för att säkerställa korrekt GSM och LSM återspeglar korrekt information efter en redundansväxling. Programkod för att utföra dessa steg kan integreras i befintliga verktyg och arbetsflöde. 

1. Hämta RecoveryManager från ShardMapManager. 
2. Koppla bort den gamla Fragmentera från Fragmentera mappningen.
3. Bifoga den nya Fragmentera Fragmentera-karta, inklusive den nya platsen Fragmentera.
4. Identifiera inkonsekvenser i mappningen mellan GSM och LSM. 
5. Lösa skillnader mellan GSM och LSM, litar LSM. 

Det här exemplet utför följande steg:

1. Tar bort shards från Fragmentera kartan som avspeglar Fragmentera platser före redundansväxlingen.
2. Bifogar shards till kartan Fragmentera spegla de nya platserna för Fragmentera (parametern ”Configuration.SecondaryServer” är det nya servernamnet men namnet på databas).
3. Hämtar recovery token med hjälp av mappning skillnaderna mellan GSM och LSM för varje Fragmentera. 
4. Löser inkonsekvenserna av betrodda mappningen från LSM för varje Fragmentera. 
   
   ```
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

