---
title: "Lägga till en Fragmentera med elastiska Databasverktyg | Microsoft Docs"
description: "Ange hur du använder Elastic Scale API: er för att lägga till nya delar en Fragmentera."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 62a349db-bebe-406f-a120-2f1986f2b286
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 7432bf00aa4d97d8dbc4b92a6a836698ee2b84d7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Lägga till en Fragmentera med hjälp av verktyg för elastisk databas
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Att lägga till en Fragmentera för ett nytt intervall eller en nyckel
Program behöver ofta helt enkelt lägga till nya delar för att hantera data som förväntas av nya nycklar eller nyckelintervall för en Fragmentera som redan finns. Till exempel ett delat program genom att klient-ID kan behöva etablera en ny Fragmentera för en ny klient eller varje månad delat data måste en ny Fragmentera etablerats före varje ny månad. 

Om nya värdeintervallet nycklar inte redan är en del av en befintlig mappning är det mycket enkelt att lägga till nya Fragmentera och associera den nya nyckeln eller området till att fragmentera. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exempel: lägga till en Fragmentera och räckvidd i en befintlig Fragmentera karta
Det här exemplet använder den [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) den [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\)) metoder, och skapar en instans av den [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)klass. I exemplet nedan, en databas med namnet **sample_shard_2** och alla nödvändiga schemaobjekt inuti den har skapats för att rymma intervallet [300, 400).  

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


Alternativt kan använda du Powershell för att skapa en ny Fragmentera kartan chef. Ett exempel är tillgänglig [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Att lägga till en Fragmentera för en tom del av ett befintligt intervall
I vissa fall kan du ett intervall redan har mappats till en Fragmentera och delvis fylls det med data, men du vill nu att dirigeras till en annan Fragmentera kommande data. Till exempel du Fragmentera av dagsintervall har redan den allokerade 50 dagar till en Fragmentera och dag 24 du vill att hamna i en annan Fragmentera framtida data. Den elastiska databasen [för delade sökvägssammanslagning](sql-database-elastic-scale-overview-split-and-merge.md) kan utföra denna åtgärd, men om dataflyttning inte är nödvändigt (t.ex, data för antal dagar [25, 50), d.v.s., dag 25 sammanlagt 50 uteslutande, ännu inte finns) du kan göra detta helt med hjälp av Fragmentera kartan Management-API: er direkt.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exempel: dela ett intervall och tilldela en nyligen tillagda Fragmentera tom del
En databas med namnet ”sample_shard_2” och alla nödvändiga schemaobjekt inuti den har skapats.  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 

        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
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

**Viktiga**: Använd den här tekniken bara om du är säker på att intervallet för den uppdaterade avbildningen är tom.  Metoderna ovan Kontrollera inte data för intervallet som ska flyttas, så det är bäst att lägga till kontroller i din kod.  Om raderna finns inom intervallet som ska flyttas, distribution för faktiska data inte att matcha uppdaterade Fragmentera kartan. Använd den [för delade sökvägssammanslagning](sql-database-elastic-scale-overview-split-and-merge.md) att utföra åtgärden i stället i dessa fall.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

