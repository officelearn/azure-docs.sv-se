---
title: Fråga efter shardade Azure SQL-databaser | Microsoft Docs
description: Kör frågor över shards med hjälp av klientbiblioteket för elastiska databaser.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 5af6779bfb6075aa3606cc32939ae715241afe8d
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166324"
---
# <a name="multi-shard-querying"></a>Multi-shard-frågor
## <a name="overview"></a>Översikt
Med den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md), du kan skapa shardad databaslösningar. **Multi-shard-frågor** används för uppgifter som samling/rapportering som kräver att du kör en fråga som sträcker sig över flera shard. (Skiljer sig från detta till [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md), som utför allt arbete på en enda shard.) 

1. Hämta en **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) eller **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) med hjälp av den **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), eller **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metoden. Se **[konstruera en ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** och  **[hämta en RangeShardMap eller ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Skapa en **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objekt.
3. Skapa en **MultiShardStatement eller MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Ange den **egenskapen CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) till ett T-SQL-kommando.
5. Kör kommandot genom att anropa den **ExecuteQueryAsync eller ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement.executeQueryAsync), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metoden.
6. Visa resultatet med hjälp av den **MultiShardResultSet eller MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) klass. 

## <a name="example"></a>Exempel
Följande kod visar användningen av Multi-shard frågor med hjälp av en viss **ShardMap** med namnet *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

En viktig skillnad är konstruktion av Multi-shard-anslutningar. Där **SqlConnection** körs på en enkel databas, den **MultiShardConnection** tar en ***samling shards*** som indata. Lägg till i samlingen med shards från en skärvkarta. Frågan körs sedan i samling av shards med **UNION ALL** semantik sätta ihop ett enskilt övergripande resultat. Du kan också namnet på den shard som raden kommer från kan läggas till utdata med den **ExecutionOptions** -egenskapen i kommandot. 

Observera anropet till **myShardMap.GetShards()**. Den här metoden hämtar alla shards från fragmentkartan och gör det enkelt att köra en fråga över alla relevanta databaser. Insamling av shards för en Multi-shard-fråga kan vara förfinade ytterligare genom att utföra en LINQ-fråga över denna datainsamling returneras vid anrop till **myShardMap.GetShards()**. I kombination med ofullständiga resultat principen, har den aktuella funktionen Multi-shard-frågor utformats för att fungera bra med tiotals upp till hundratals shards.

En begränsning med Multi-shard-frågor finns för närvarande bristen på validering av fragment och shardletar som efterfrågas. Databeroende routning verifierar att en viss shard är en del av fragmentkartan vid tidpunkten för fråga, men Multi-shard-frågor utför inte den här kontrollen. Detta kan leda till Multi-shard-frågor som körs på databaser som har tagits bort från fragmentkartan.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Multi-shard-frågor och åtgärder för dela / sammanslå
Multi-shard-frågor verifierar inte om shardletar på databasen deltar i pågående dela / sammanslå åtgärder. (Se [skala med verktyget elastiska databaser dela och slå samman](sql-database-elastic-scale-overview-split-and-merge.md).) Detta kan leda till inkonsekvenser där rader från samma shardlet visar för flera databaser i samma Multi-shard-fråga. Tänk på dessa begränsningar och Överväg tömning pågående åtgärder för dela / sammanslå och ändringar i fragmentkartan när du utför Multi-shard-frågor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


