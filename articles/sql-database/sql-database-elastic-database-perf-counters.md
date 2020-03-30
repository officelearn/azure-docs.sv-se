---
title: Prestandaräknare för att spåra fragmentkarthanteraren
description: ShardMapManager-klass och databeroende routningsprestandaräknare
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823891"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Skapa prestandaräknare för att spåra prestanda för fragmentkarthanteraren

Prestandaräknare används för att spåra prestanda för [databeroende flödesåtgärder.](sql-database-elastic-scale-data-dependent-routing.md) Dessa räknare är tillgängliga i prestandaövervakaren under kategorin Elastisk databas: Shard Management.

Du kan samla in prestanda för en [shard karthanterare](sql-database-elastic-scale-shard-map-management.md), särskilt när du använder [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md). Räknare skapas med metoder för klassen Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**För den senaste versionen:** Gå till [Microsoft.Azure.SqlDatabase.elasticscale.client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se även [Uppgradera en app för att använda det senaste elastiska databasklientbiblioteket](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Krav

* Om du vill skapa prestandakategori och räknare måste användaren vara en del av den lokala **administratörsgruppen** på datorn som är värd för programmet.  
* Om du vill skapa en prestandaräknareinstans och uppdatera räknarna måste användaren vara medlem i gruppen **Administratörer** eller **Prestandaövervakare.**

## <a name="create-performance-category-and-counters"></a>Skapa prestandakategori och räknare

Om du vill skapa räknarna anropar du metoden CreatePerformanceCategoryAndCounters i [klassen ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Endast en administratör kan köra metoden:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Du kan också använda [det här](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-skriptet för att köra metoden.
Metoden skapar följande prestandaräknare:  

* **Cachelagrade mappningar**: Antal mappningar som cachelagrats för fragmentkartan.
* **DDR-åtgärder/s**: Hastighet för databeroende routningsåtgärder för fragmentkartan. Räknaren uppdateras när ett anrop till [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulterar i en lyckad anslutning till målsvanten.
* **Mappning av uppslagscachen träffar/sek**: Hastighet för lyckade cachesökningsåtgärder för mappningar i fragmentkartan.
* **Mappning av uppslagscachen missar/sek:** Hastighet för misslyckade cachesökningsåtgärder för mappningar i fragmentkartan.
* **Mappningar som läggs till eller uppdateras i cache/sek:** Hastighet med vilken mappningar läggs till eller uppdateras i cacheminnet för fragmentkartan.
* **Mappningar som tagits bort från cache/sek**: Hastighet med vilken mappningar tas bort från cacheminnet för fragmentkartan.

Prestandaräknare skapas för varje cachelagd fragmentkarta per process.  

## <a name="notes"></a>Anteckningar

Följande händelser utlöser skapandet av prestandaräknarna:  

* Initiering av [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) med [ivrig inläsning](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), om ShardMapManager innehåller några fragmentkartor. Dessa inkluderar [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) och [TryGetSqlShardMapManager-metoderna.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager)
* Lyckad sökning av en fragmentkarta (med [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Framgångsrikt skapa shard karta med CreateShardMap().

Prestandaräknarna uppdateras av alla cacheåtgärder som utförs på fragmentkartan och mappningar. En lyckad borttagning av fragmentmappningen med DeleteShardMap() resulterar i borttagning av prestandaräknareinstansen.  

## <a name="best-practices"></a>Bästa praxis

* Skapandet av prestandakategorin och räknarna bör endast utföras en gång innan ShardMapManager-objektet skapas. Varje körning av kommandot CreatePerformanceCategoryAndCounters() rensar tidigare räknare (förlorar data som rapporterats av alla instanser) och skapar nya.  
* Prestandaräknareinstanser skapas per process. Alla programkrascher eller borttagning av en fragmentkarta från cacheminnet kommer att resultera i radering av prestandaräknningsinstanserna.  

### <a name="see-also"></a>Se även

[Översikt över Elastic Database-funktioner](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
