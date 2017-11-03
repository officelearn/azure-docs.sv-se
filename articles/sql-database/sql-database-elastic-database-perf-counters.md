---
title: "Prestandaräknare för karthanteraren för shard"
description: "ShardMapManager klass- och beroende routning prestandaräknare"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: b090aba0-2e30-454c-96b3-dffa281f539a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: ddove
ms.openlocfilehash: 4a7a2e0b4f2be852c61fc3ccdd4dea2034b80918
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="performance-counters-for-shard-map-manager"></a>Prestandaräknare för karthanteraren för shard
Du kan avbilda prestanda för en [Fragmentera kartan manager](sql-database-elastic-scale-shard-map-management.md), särskilt när du använder [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). Räknare skapas med metoderna i klassen Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Räknare som används för att spåra prestanda för [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) åtgärder. Dessa räknare är tillgängliga i Prestandaövervakaren under kategorin ”elastisk: Fragmentera databashantering”.

**För den senaste versionen:** går du till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se även [uppgradera en app om du vill använda det senaste klientbiblioteket för elastisk databas](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Krav
* För att skapa prestandakategorin och räknare, måste användaren vara en del av lokalt **administratörer** på den dator som är värd för programmet.  
* Om du vill skapa en prestandaräknarinstans och uppdatera räknarna som användaren måste vara medlem i antingen den **administratörer** eller **Prestandaövervakningsanvändare** grupp. 

## <a name="create-performance-category-and-counters"></a>Skapa prestandakategorin och räknare
Om du vill skapa räknarna du anropa metoden CreatePeformanceCategoryAndCounters av den [ShardMapManagmentFactory klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Endast en administratör kan utföra den här metoden: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Du kan också använda [detta](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-skript för att utföra den här metoden. Metoden skapar följande prestandaräknare:  

* **Cachelagrade mappningar**: antalet avbildningar som cachelagrats för Fragmentera kartan.
* **DDR per sekund**: antal beroende routning dataåtgärder för Fragmentera kartan. Den här räknaren uppdateras när ett anrop till [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resulterar i en anslutning till mål-Fragmentera. 
* **Mappning av sökning träffar/sek**: antalet lyckade cache-sökning åtgärder för avbildningar i kartan Fragmentera. 
* **Mappning av sökning missar/sek i cache**: antal misslyckade cache-sökning åtgärder för avbildningar i kartan Fragmentera.
* **Mappningar läggs till eller uppdateras i/sek i cache**: frekvens som mappningar läggs eller uppdaterats i cacheminnet för Fragmentera kartan. 
* **Mappningar tas bort från cache/sek**: hastighet med vilken mappningar tas bort från cachen för Fragmentera kartan. 

Prestandaräknare skapas för varje cachelagrade Fragmentera mappning per process.  

## <a name="notes"></a>Anteckningar
Följande händelser utlösa skapandet av prestandaräknare:  

* Initieringen av den [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) med [gärna inläsning](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), om ShardMapManager innehåller alla Fragmentera maps. Dessa inkluderar den [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) och [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metoder.
* Lyckad sökning på en Fragmentera-mappning (med hjälp av [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Har skapats Fragmentera karta med hjälp av CreateShardMap().

Prestandaräknare kommer att uppdateras av alla cache-åtgärder som utförs på Fragmentera kartan och mappningar. Lyckad borttagning av Fragmentera kartan med DeleteShardMap () reults i borttagning av räknare prestandainstans.  

## <a name="best-practices"></a>Bästa praxis
* Att skapa prestandakategorin och räknare ska utföras en gång innan skapandet av ShardMapManager objekt. Varje körning av kommandot CreatePerformanceCategoryAndCounters() rensar tidigare räknare (förlorar data som rapporteras av alla instanser) och nya skapas.  
* Prestandaräknaren instanser skapas per process. Alla program kraschar eller borttagning av en Fragmentera mappning från cacheminnet leder borttagning av prestandaräknare instanser.  

### <a name="see-also"></a>Se även
[Översikt över funktioner i elastisk databas](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

