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
ms.date: 04/12/2016
ms.author: torsteng
ms.openlocfilehash: b4827fafdfd2f8b094c4f541a7511d6233dd4e6f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="multi-shard-querying"></a>Flera Fragmentera frågor
## <a name="overview"></a>Översikt
Med den [elastisk Databasverktyg](sql-database-elastic-scale-introduction.md), kan du skapa delat databaslösningar. **Flera Fragmentera frågar** används för aktiviteter som samling/rapportering av användningsdata som kräver att du kör en fråga som sträcker sig över flera delar. (Skiljer sig från detta till [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md), som utför alla arbete på en enda Fragmentera.) 

1. Hämta en [ **RangeShardMap** ](https://msdn.microsoft.com/library/azure/dn807318.aspx) eller [ **ListShardMap** ](https://msdn.microsoft.com/library/azure/dn807370.aspx) med hjälp av den [ **TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [ **TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx), eller [ **GetShardMap** ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) metod. Se [ **konstruera en ShardMapManager** ](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) och [ **får en RangeShardMap eller ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Skapa en  **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**  objekt.
3. Skapa en  **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
4. Ange den  **[egenskapen CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**  på ett T-SQL-kommando.
5. Kör kommandot genom att anropa den  **[ExecuteReader metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
6. Visa resultatet med hjälp av  **[MultiShardDataReader klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exempel
Följande kod visar användningen av flera Fragmentera fråga med hjälp av en viss **ShardMap** med namnet *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
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


En viktigaste skillnaden är konstruktionen av flera Fragmentera anslutningar. Där **SqlConnection** körs på en enskild databas i **MultiShardConnection** tar en ***samling shards*** som indata. Fyll i mängden shards från en Fragmentera karta. Frågan körs sedan på mängden shards med **UNION ALL** semantik att sammanställa ett övergripande resultat. Du kan också namnet på Fragmentera där raden härstammar från kan läggas till i utdata med hjälp av **ExecutionOptions** -egenskapen i kommandot. 

Observera anropet till **myShardMap.GetShards()**. Den här metoden hämtar alla delar från Fragmentera kartan och ger ett enkelt sätt att köra en fråga i alla relevanta databaser. Insamling av shards för en fråga med flera Fragmentera kan anpassas ytterligare genom att utföra en LINQ-fråga över returnerades från anropet till **myShardMap.GetShards()**. I kombination med ofullständiga resultat principen, är den aktuella kapaciteten i flera Fragmentera frågar utformad för fungerar bra för flera upp till hundratals delar.

En begränsning med flera Fragmentera frågar är för närvarande saknas för verifieringen av delar och shardlets som efterfrågas. Data-beroende routning verifierar att en given Fragmentera är en del av Fragmentera kartan vid tidpunkten för frågor, men flera Fragmentera frågor utför inte den här kontrollen. Detta kan leda till flera Fragmentera frågor som körs på databaser som har tagits bort från Fragmentera kartan.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Flera Fragmentera frågor och dela merge-operationer
Flera Fragmentera frågor bekräftar inte om shardlets på databasen deltar i pågående dela merge-åtgärder. (Se [skalning med hjälp av verktyget för elastisk databas delade dokument](sql-database-elastic-scale-overview-split-and-merge.md).) Detta kan leda till inkonsekvenser där rader från samma shardlet visas för flera databaser i samma fråga flera Fragmentera. Vara medveten om dessa begränsningar och Överväg tömningen pågående dela merge-åtgärder och ändringar av Fragmentera kartan när du utför flera Fragmentera frågor.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Se även
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**  klasser och metoder.

Hantera shards med hjälp av den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md). Innehåller ett namnområde som kallas [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) som gör möjligheten att fråga flera delar med hjälp av en enskild fråga och resultat. Det ger en frågar abstraktion över en mängd shards. Det ger också alternativa körningsprinciper, i synnerhet ofullständiga resultat att hantera fel vid fråga över flera delar.  

