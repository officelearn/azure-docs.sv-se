---
title: Att lägga till en shard med elastiska Databasverktyg | Microsoft Docs
description: Ange hur du använder Elastic Scale APIs för att lägga till nya fragmenten i en shard.
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
ms.date: 01/03/2019
ms.openlocfilehash: 0a6a4d8a2f2100f967cdc5ead84f56bfb15f564b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043557"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Att lägga till en shard med elastiska Databasverktyg

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Att lägga till en skärva för ett nytt intervall eller en nyckel

Program behöver ofta att lägga till nya fragmenten för att hantera data som förväntas från nya nycklar eller nyckelintervall för en skärvkarta som redan finns. Till exempel ett delat program genom att klient-ID kan behöva etablera en ny shard för en ny klient eller varje månad shardade data måste en ny shard etableras innan början av varje ny månad.

Om det nya området med nyckelvärden inte redan är en del av en befintlig mappning är det enkelt att lägga till det nya fragmentet och associera den nya nyckeln eller området till fragmentet.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exempel: lägga till en shard och räckvidd till en befintlig fragmentkartan

Det här exemplet används TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)), CreateRangeMapping ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metoder, och skapar en instans av ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)) klass. I exemplet nedan, en databas med namnet **sample_shard_2** och alla nödvändiga schemaobjekt inuti den har skapats för att lagra intervallet [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

För .NET-versionen kan du också använda PowerShell som ett alternativ för att skapa en ny Karthanteraren. Ett exempel är tillgängliga [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Att lägga till en skärva för ett tomt område på ett befintligt intervall

I vissa fall kan du redan mappat en rad till en shard och delvis fylls den med data, men nu vill kommande data så att de dirigeras till en annan shard. Exempelvis kan du fragment av dagsintervall har redan den allokerade 50 dagar till en shard och dag 24, som du vill att framtida data hamnar i olika shards. Den elastiska databasen [dela / sammanslå verktyget](sql-database-elastic-scale-overview-split-and-merge.md) kan utföra denna åtgärd, men om dataförflyttning inte är nödvändigt (t.ex, data för antalet dagar [25, 50), det vill säga, dag 25 att de 50 uteslutande, inte finns ännu) du kan göra detta helt och hållet med API: er med fragment kartan Management direkt.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exempel: dela en rad och tilldelas en nyligen tillagd shard tom delen

En databas med namnet ”sample_shard_2” och alla nödvändiga schemaobjekt inuti den har skapats.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Viktiga**:  Använd den här tekniken bara om du är säker på att intervallet för uppdaterade mappningen är tom.  Föregående metoder Kontrollera inte data för intervallet som flyttas, så det är bäst att inkludera kontroller i din kod.  Om det finns rader i intervallet som flyttas, matchar inte den faktiska Datadistributionen uppdaterade fragmentkartan. Använd den [dela / sammanslå verktyget](sql-database-elastic-scale-overview-split-and-merge.md) att utföra åtgärden i stället i dessa fall.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]