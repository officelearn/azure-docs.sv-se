---
title: Korrigera shard-kartproblem med Recovery Manager | Microsoft Docs
description: Använd RecoveryManager-klassen för att lösa problem med fragmentkartor
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3aeee7cd4c588460a16b93237b08f13d8422a72a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721320"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Korrigera shard-kartproblem med RecoveryManager-klassen
Den [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) klassen ger ADO.Net-program möjlighet att enkelt identifiera och åtgärda eventuella inkonsekvenser mellan globala fragmentkartan (GSM) och lokala fragmentkartan (LSM) i en databasmiljö med fragmenterade (sharded). 

Spåra mappningen av varje databas i ett delat GSM och LSM. Ibland kan sker ett avbrott mellan GSM och LSM. I så fall använda RecoveryManager-klassen för att identifiera och reparera avbrottet.

RecoveryManager-klassen är en del av den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md). 

![Fragmentkartan][1]

Termdefinitioner finns [ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md). Att förstå hur **ShardMapManager** används för att hantera data i en shardad lösning, se [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Varför använda recovery manager?
I en databasmiljö för fragmenterade (sharded) finns en klient per databas och många databaser per server. Det kan också finnas många servrar i miljön. Varje databas mappas i fragmentkartan, så att anrop kan dirigeras till rätt server och databas. Databaser spåras enligt en **shardingnyckel**, och varje shard har tilldelats en **viktiga värdeintervallet**. En shardingnyckel kan till exempel representera kundernas namn från ”D” till ”F.” Mappningen av alla shards (även kallat databaser) och deras mappning intervall finns i den **globala fragmentkartan (GSM)**. Varje databas innehåller också en karta över de intervall som finns på den shard som kallas den **lokala fragmentkartan (LSM)**. När en app som ansluter till en shard, cachelagras mappningen med appen för snabb hämtning. LSM används för att verifiera cachelagrade data. 

GSM och LSM kan bli osynkroniserad av följande skäl:

1. Borttagningen av ett fragment vars intervallet anses inte längre används eller byta namn på en shard. Tar bort ett fragment resulterar i en **frånkopplade fragment mappning**. På samma sätt kan kan en omdöpt databas orsaka en överblivna shard-mappning. Beroende på syftet med ändringen fragmentet kan behöva tas bort eller fragmentets placering måste uppdateras. Om du vill återställa en borttagen databas finns i [återställa en borttagen databas](sql-database-recovery-using-backups.md).
2. En geo-redundans-händelse inträffar. Om du vill fortsätta, måste en uppdatera servernamnet och databasnamnet för karthanteraren i programmet och sedan uppdatera fragment mappning information för alla shards i en skärvkarta. Om det finns en geo-redundans, bör sådana recovery logic automatiseras inifrån själva arbetsflödet för redundans. Automatisera återställningsåtgärder möjliggör en smidig hanterbarhet för geo-aktiverade databaser och undviker manuella mänskliga åtgärder. Mer information om alternativ för att återställa en databas om det finns ett avbrott på datacentret, se [affärskontinuitet](sql-database-business-continuity.md) och [Disaster Recovery](sql-database-disaster-recovery.md).
3. En shard eller ShardMapManager-databasen återställs till en tidigare punkt i tiden. Mer information om punkt i tiden återställning med hjälp av säkerhetskopior, se [återställning med hjälp av säkerhetskopior](sql-database-recovery-using-backups.md).

Mer information om Azure SQL Database Elastic Database-verktyg, geo-replikering och återställning finns i följande: 

* [Översikt: Cloud business affärskontinuitet och databasen haveriberedskap med SQL-databas](sql-database-business-continuity.md) 
* [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md)  
* [ShardMap-hantering](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Hämta RecoveryManager från en ShardMapManager
Det första steget är att skapa en RecoveryManager-instans. Den [GetRecoveryManager metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) returnerar recovery manager för aktuellt [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) instans. För att åtgärda eventuella inkonsekvenser i fragmentkartan, måste du först hämta RecoveryManager för viss fragmentkartan. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

I det här exemplet har RecoveryManager initierats från ShardMapManager. ShardMapManager som innehåller en ShardMap har också redan initierats. 

Eftersom den här koden ändrar fragmentkartan själva, ska de autentiseringsuppgifter som används i standardmetoden (i det föregående exemplet smmConnectionString) vara autentiseringsuppgifter som har skrivskyddad behörighet för GSM-databasen som refereras av anslutningen sträng. De här autentiseringsuppgifterna är vanligtvis skiljer sig från autentiseringsuppgifterna som används för att öppna anslutningar för databeroende routning. Mer information finns i [med autentiseringsuppgifter i elastiska databaser klienten](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Ta bort ett fragment från ShardMap när en shard har tagits bort
Den [DetachShard metoden](https://msdn.microsoft.com/library/azure/dn842083.aspx) kopplar från det angivna fragmentet från fragmentkartan och tar bort mappningar som är associerade med fragmentet.  

* Parametern plats är fragmentets placering, särskilt servernamnet och databasnamnet för den shard som håller på att frånkopplas. 
* Parametern shardMapName är mappningsnamn fragment. Detta är endast krävs när flera fragmentkartor som hanteras av samma fragmentkartehanteraren. Valfri. 


> [!IMPORTANT]
> Använd den här tekniken bara om du är säker på att intervallet för uppdaterade mappningen är tom. Metoderna ovan Kontrollera inte data för intervallet som flyttas, så det är bäst att inkludera kontroller i din kod.
>

Det här exemplet tar bort shards från fragmentkartan. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

Fragmentkartan återspeglar fragmentets placering i GSM före borttagning av fragmentet. Eftersom sharden har tagits bort, förutsätts det var avsiktlig och nyckelintervall för horisontell partitionering är inte längre används. Om inte, du kan köra point in time-återställning. att återställa fragmentet från en tidigare punkt i tiden. (I så fall kan granska följande avsnitt för att identifiera fragment inkonsekvenser.) Om du vill återställa, se [punkten i tidsåterställning](sql-database-recovery-using-backups.md).

Eftersom det förutsätts borttagningen databasen var avsiktlig är slutlig rensning av administrativa åtgärden att ta bort posten till fragment i fragmentkartehanteraren. Detta hindrar programmet från oavsiktligt skriva information till ett område som inte förväntades.

## <a name="to-detect-mapping-differences"></a>Att identifiera mappning skillnader
Den [DetectMappingDifferences metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) markerar och returnerar en av fragmentkartor (lokal eller global) som källa till sanningen och synkroniserar mappningar på båda fragmentkartor (GSM och LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* Den *plats* anger servernamnet och databasnamnet. 
* Den *shardMapName* parametern är mappningsnamn fragment. Detta är endast krävs om flera fragmentkartor som hanteras av samma fragmentkartehanteraren. Valfri. 

## <a name="to-resolve-mapping-differences"></a>Du löser mappning skillnader
Den [ResolveMappingDifferences metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) väljer någon av fragmentkartor (lokal eller global) som källa till sanningen och synkroniserar mappningar på båda fragmentkartor (GSM och LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Den *RecoveryToken* parametern räknar upp skillnader i mappningarna mellan GSM och LSM för det specifika fragmentet. 
* Den [MappingDifferenceResolution uppräkning](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) används för att ange metoden för att lösa skillnaden mellan fragment-mappningar. 
* **MappingDifferenceResolution.KeepShardMapping** som rekommenderas när LSM innehåller korrekt mappning och därför mappningen i fragment ska användas. Detta är vanligtvis fallet om det finns en redundans: fragmentet finns nu på en ny server. Eftersom sharden måste först tas bort från GSM (metoden RecoveryManager.DetachShard), finns inte längre en mappning på GSM. Därför måste LSM användas för att återupprätta fragment mappningen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Koppla en shard till ShardMap när en shard har återställts
Den [AttachShard metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) kopplar det angivna fragmentet till fragmentkartan. Den identifierar inkonsekvenser fragment kartan och uppdaterar mappningarna för att matcha fragment vid återställningen fragment. Det förutsätts att databasen har även bytt namn för att återspegla det ursprungliga databasnamnet (innan sharden har återställts), eftersom point-in-time-återställning som standard till en ny databas läggas till med tidsstämpel. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* Den *plats* parametern är servernamnet och databasnamnet för fragmentet som kopplas. 
* Den *shardMapName* parametern är mappningsnamn fragment. Detta är endast krävs när flera fragmentkartor som hanteras av samma fragmentkartehanteraren. Valfri. 

Det här exemplet lägger till en shard fragmentkartan som nyligen har återställts från en tidigare återställningspunkt-tidpunkt. Eftersom fragment (nämligen mappningen fragment i LSM) har återställts, är det eventuellt inte överens med posten fragment i GSM. Utanför den här exempelkoden har fragmentet återställts och bytt namn till det ursprungliga namnet på databasen. Eftersom den har återställts, antas det att mappningen i LSM är betrodda mappningen. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Uppdatera platser för shards när en geo-redundans (återställning) för shards
Om det finns en geo-redundans, den sekundära databasen är åtkomlig skriva och blir den nya primära databasen. Namnet på servern och potentiellt databasen (beroende på din konfiguration), kan skilja sig från den ursprungliga primära databasen. Därför måste avbildningar för fragment i GSM och LSM åtgärdas. På samma sätt, om databasen har återställts till ett annat namn eller plats eller till en tidigare tidpunkt, detta kan orsaka inkonsekvenser i shard-kartor. Fragmentkartehanteraren hanterar distributionen av öppna anslutningar till rätt databas. Distribution är baserad på data i fragmentkartan och värdet för nyckeln för horisontell partitionering som är mål för program-begäran. När du har en geo-redundans, måste du uppdatera den här informationen med korrekt servernamnet, databasnamnet och shard mappning av den återställda databasen. 

## <a name="best-practices"></a>Bästa praxis
GEO-redundans och återställning är åtgärder som oftast hanteras av en molnadministratör av programmet avsiktligt använder en Azure SQL-databaser funktioner för affärskontinuitet. Affärskontinuitet planering kräver processer, procedurer och åtgärder för att säkerställa att verksamheten kan fortsätta utan avbrott. Metoderna som är tillgängliga baserat som en del av RecoveryManager-klassen ska användas i det här arbetsflödet så GSM och LSM hålls uppdaterade på de recovery-åtgärder som vidtagits. Det finns fem grundläggande steg för att garantera korrekt GSM och LSM återspeglar korrekt information efter en redundansväxling. Programkod för att köra de här stegen kan integreras i befintliga verktyg och arbetsflöde. 

1. Hämta RecoveryManager från ShardMapManager. 
2. Koppla från det gamla fragmentet från fragmentkartan.
3. Koppla det nya fragmentet till fragmentkartan, inklusive den nya shard-platsen.
4. Identifiera inkonsekvenser i mappningen mellan GSM och LSM. 
5. Lösa skillnader mellan GSM och LSM, betrodda LSM. 

Det här exemplet utför följande steg:

1. Tar bort shards från Fragmentkartan som återspeglar platser för shards innan redundansväxlingen.
2. Bifogar fragment till i Fragmentkartan återger de nya platser för shards (parametern ”Configuration.SecondaryServer” är det nya servernamnet men samma databasnamn).
3. Hämtar token för återställning med hjälp av mappning skillnader mellan GSM och LSM för varje fragment. 
4. Löser inkonsekvenser genom att lita mappningen från LSM för varje shard. 
   
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

