---
title: Fragmenterade databaser för frågor
description: Kör frågor över shards med hjälp av klientbiblioteket för elastisk databas.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067302"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Frågar för flera fragment med elastiska databasverktyg

## <a name="overview"></a>Översikt

Med [verktygen Elastisk databas](sql-database-elastic-scale-introduction.md)kan du skapa fragmenterade databaslösningar. **Multi-shard-frågor** används för uppgifter som datainsamling/rapportering som kräver att du kör en fråga som sträcker sig över flera shards. (Jämför detta med [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md), som utför allt arbete på en enskild fragment.)

1. Hämta en **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) eller **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) med **metoden TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **tryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) eller **getshardmap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap) Se [Konstruera en ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) och [hämta en RangeShardMap eller ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Skapa ett **MultiShardConnection** -objekt ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection) [, .NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)
3. Skapa en **MultiShardStatement eller MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Ange **commandtext-egenskapen** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) till ett T-SQL-kommando.
5. Kör kommandot genom att anropa metoden **ExecuteQueryAsync eller ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Visa resultaten med klassen **MultiShardResultSet eller MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)

## <a name="example"></a>Exempel

Följande kod illustrerar användningen av flershardfrågor med hjälp av en viss **ShardMap** med namnet *myShardMap*.

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

En viktig skillnad är byggandet av multi-shard anslutningar. Där **SqlConnection** fungerar på en enskild databas tar **MultiShardConnection** en ***samling shards*** som indata. Fyll i samlingen med shards från en fragmentkarta. Frågan körs sedan på samlingen av shards med **UNION ALL** semantik för att montera ett enda övergripande resultat. Du kan också lägga till namnet på fragmentet där raden kommer från till utdata med hjälp av kommandot **ExecutionOptions.**

Observera anropet till **myShardMap.GetShards()**. Den här metoden hämtar alla shards från fragmentkartan och är ett enkelt sätt att köra en fråga i alla relevanta databaser. Samlingen av shards för en multi-shard fråga kan förfinas ytterligare genom att utföra en LINQ fråga över samlingen returneras från samtalet till **myShardMap.GetShards()**. I kombination med principen om partiella resultat har den aktuella funktionen i fråga om flera fragment utformats för att fungera bra för tiotals upp till hundratals shards.

En begränsning med flera fragmentfrågor är för närvarande bristen på validering för shards och shardlets som efterfrågas. Databeroende routning verifierar att en viss shard är en del av fragmentmappningen vid tidpunkten för frågan, men flera fragmentfrågor utför inte den här kontrollen. Detta kan leda till flera fragmentfrågor som körs på databaser som har tagits bort från fragmentkartan.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Fler fragmentfrågor och åtgärder för delad koppling

Multi-shard-frågor kontrollerar inte om shardlets i den efterfrågade databasen deltar i pågående split-merge-åtgärder. (Se [Skala med verktyget delad sammanfogning av elastisk databas](sql-database-elastic-scale-overview-split-and-merge.md).) Detta kan leda till inkonsekvenser där rader från samma shardlet visas för flera databaser i samma multi-shard-fråga. Var medveten om dessa begränsningar och överväg att tömma pågående split-merge-åtgärder och ändringar i fragmentkartan när du utför multi-shard-frågor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]