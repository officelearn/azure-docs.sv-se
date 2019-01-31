---
title: Fråga efter shardade Azure SQL-databaser | Microsoft Docs
description: Kör frågor över shards med hjälp av klientbiblioteket för elastiska databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 35759f03d7cf09a4114ca6dca74bd3ee92fdcbfa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462179"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Multi-shard-frågor med hjälp av verktyg för elastiska databaser

## <a name="overview"></a>Översikt

Med den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md), du kan skapa shardad databaslösningar. **Multi-shard-frågor** används för uppgifter som samling/rapportering som kräver att du kör en fråga som sträcker sig över flera shard. (Skiljer sig från detta till [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md), som utför allt arbete på en enda shard.)

1. Hämta en **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) eller **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) med hjälp av den **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), eller **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metoden. Se [konstruera en ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) och [hämta en RangeShardMap eller ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Skapa en **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) objekt.
3. Skapa en **MultiShardStatement eller MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Ange den **egenskapen CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) till ett T-SQL-kommando.
5. Kör kommandot genom att anropa den **ExecuteQueryAsync eller ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) metoden.
6. Visa resultatet med hjälp av den **MultiShardResultSet eller MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) klass.

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

En viktig skillnad är konstruktion av Multi-shard-anslutningar. Där **SqlConnection** körs på en enskild databas i **MultiShardConnection** tar en ***samling shards*** som indata. Lägg till i samlingen med shards från en skärvkarta. Frågan körs sedan i samling av shards med **UNION ALL** semantik sätta ihop ett enskilt övergripande resultat. Du kan också namnet på den shard som raden kommer från kan läggas till utdata med den **ExecutionOptions** -egenskapen i kommandot.

Observera anropet till **myShardMap.GetShards()**. Den här metoden hämtar alla shards från fragmentkartan och gör det enkelt att köra en fråga över alla relevanta databaser. Insamling av shards för en Multi-shard-fråga kan vara förfinade ytterligare genom att utföra en LINQ-fråga över denna datainsamling returneras vid anrop till **myShardMap.GetShards()**. I kombination med ofullständiga resultat principen, har den aktuella funktionen Multi-shard-frågor utformats för att fungera bra med tiotals upp till hundratals shards.

En begränsning med Multi-shard-frågor finns för närvarande bristen på validering av fragment och shardletar som efterfrågas. Databeroende routning verifierar att en viss shard är en del av fragmentkartan vid tidpunkten för fråga, men Multi-shard-frågor utför inte den här kontrollen. Detta kan leda till Multi-shard-frågor som körs på databaser som har tagits bort från fragmentkartan.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Multi-shard-frågor och åtgärder för dela / sammanslå

Multi-shard-frågor verifierar inte om shardletar på databasen deltar i pågående dela / sammanslå åtgärder. (Se [skala med verktyget elastiska databaser dela och slå samman](sql-database-elastic-scale-overview-split-and-merge.md).) Detta kan leda till inkonsekvenser där rader från samma shardlet visar för flera databaser i samma Multi-shard-fråga. Tänk på dessa begränsningar och Överväg tömning pågående åtgärder för dela / sammanslå och ändringar i fragmentkartan när du utför Multi-shard-frågor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]