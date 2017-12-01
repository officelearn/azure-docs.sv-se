---
title: "Fråga delat Azure SQL-databaser | Microsoft Docs"
description: "Köra frågor över shards med hjälp av klientbiblioteket för elastisk databas."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: torsteng
ms.openlocfilehash: 33128357bd5b2bd744c5c1c3032f658ebe865d49
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="multi-shard-querying"></a>Flera Fragmentera frågor
## <a name="overview"></a>Översikt
Med den [elastisk Databasverktyg](sql-database-elastic-scale-introduction.md), kan du skapa delat databaslösningar. **Flera Fragmentera frågar** används för aktiviteter som samling/rapportering av användningsdata som kräver att du kör en fråga som sträcker sig över flera delar. (Skiljer sig från detta till [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md), som utför alla arbete på en enda Fragmentera.) 

1. Hämta en **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) eller **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) med hjälp av den **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), eller **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metoden. Se  **[konstruera en ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)**  och  **[får en RangeShardMap eller ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Skapa en **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objekt.
3. Skapa en **MultiShardStatement eller MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Ange den **egenskapen CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) till ett T-SQL-kommando.
5. Kör kommandot genom att anropa den **ExecuteQueryAsync eller ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metoden.
6. Visa resultatet med hjälp av **MultiShardResultSet eller MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) klass. 

## <a name="example"></a>Exempel
Följande kod visar användningen av flera Fragmentera fråga med hjälp av en viss **ShardMap** med namnet *myShardMap*. 

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

En viktigaste skillnaden är konstruktionen av flera Fragmentera anslutningar. Där **SqlConnection** körs på en enskild databas i **MultiShardConnection** tar en ***samling shards*** som indata. Fyll i mängden shards från en Fragmentera karta. Frågan körs sedan på mängden shards med **UNION ALL** semantik att sammanställa ett övergripande resultat. Du kan också namnet på Fragmentera där raden härstammar från kan läggas till i utdata med hjälp av **ExecutionOptions** -egenskapen i kommandot. 

Observera anropet till **myShardMap.GetShards()**. Den här metoden hämtar alla delar från Fragmentera kartan och ger ett enkelt sätt att köra en fråga i alla relevanta databaser. Insamling av shards för en fråga med flera Fragmentera kan anpassas ytterligare genom att utföra en LINQ-fråga över returnerades från anropet till **myShardMap.GetShards()**. I kombination med ofullständiga resultat principen, är den aktuella kapaciteten i flera Fragmentera frågar utformad för fungerar bra för flera upp till hundratals delar.

En begränsning med flera Fragmentera frågar är för närvarande saknas för verifieringen av delar och shardlets som efterfrågas. Data-beroende routning verifierar att en given Fragmentera är en del av Fragmentera kartan vid tidpunkten för frågor, men flera Fragmentera frågor utför inte den här kontrollen. Detta kan leda till flera Fragmentera frågor som körs på databaser som har tagits bort från Fragmentera kartan.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Flera Fragmentera frågor och dela merge-operationer
Flera Fragmentera frågor bekräftar inte om shardlets på databasen deltar i pågående dela merge-åtgärder. (Se [skalning med hjälp av verktyget för elastisk databas delade dokument](sql-database-elastic-scale-overview-split-and-merge.md).) Detta kan leda till inkonsekvenser där rader från samma shardlet visas för flera databaser i samma fråga flera Fragmentera. Vara medveten om dessa begränsningar och Överväg tömningen pågående dela merge-åtgärder och ändringar av Fragmentera kartan när du utför flera Fragmentera frågor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


