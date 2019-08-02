---
title: Skapa prestanda räknare för att spåra prestanda för Shard Map Manager
description: Prestanda räknare för ShardMapManager-klass och data beroende routning
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568682"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Skapa prestanda räknare för att spåra prestanda för Shard Map Manager

Prestanda räknare används för att spåra prestanda för [data beroende routnings](sql-database-elastic-scale-data-dependent-routing.md) åtgärder. Dessa räknare är tillgängliga i prestanda övervakaren under "Elastic Database: Shard-hantering "-kategorin.

Du kan samla in prestanda för en [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md), särskilt när du använder [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). Räknare skapas med metoder för klassen Microsoft. Azure. SqlDatabase. ElasticScale. client.  


**För den senaste versionen:** Gå till [Microsoft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se även [uppgradera en app för att använda det senaste klient biblioteket för Elastic Database](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Förutsättningar

* Om du vill skapa prestanda kategori och räknare måste användaren vara en del av den lokala gruppen **Administratörer** på den dator som är värd för programmet.  
* Om du vill skapa en prestanda räknar instans och uppdatera räknarna måste användaren vara medlem i gruppen användare av **Administratörer** eller **prestanda övervakning** .

## <a name="create-performance-category-and-counters"></a>Skapa prestanda kategori och räknare

Om du vill skapa räknarna anropar du CreatePerformanceCategoryAndCounters-metoden för [ShardMapManagementFactory-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Endast en administratör kan köra metoden:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Du kan också använda [det här](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-skriptet för att köra-metoden.
Metoden skapar följande prestanda räknare:  

* **Cachelagrade mappningar**: Antal mappningar som cachelagrats för Shard-kartan.
* **DDR-åtgärder/SEK**: Takt för data beroende Dirigerings åtgärder för Shard-kartan. Den här räknaren uppdateras när ett anrop till [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulterar i en lyckad anslutning till mål-Shard.
* **Mappning av Sök-cacheträffar per sekund**: Antal lyckade cache söknings åtgärder för mappningar i Shard-kartan.
* **Mappar sökning efter Cachemissar/SEK**: Frekvens på misslyckade söknings åtgärder för cache för mappningar i Shard-kartan.
* **Mappningar som lagts till eller uppdaterats i cache/SEK**: Den hastighet med vilken mappningar läggs till eller uppdateras i cacheminnet för Shard-kartan.
* **Mappningar som tagits bort från cache/SEK**: Den hastighet med vilken mappningar tas bort från cachen för Shard-kartan.

Prestanda räknare skapas för varje cachelagrad Shard-mappning per process.  

## <a name="notes"></a>Anteckningar

Följande händelser utlöser skapandet av prestanda räknare:  

* Initieringen av [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) med [Eager inläsning](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)om ShardMapManager innehåller några Shard Maps. Dessa omfattar metoderna [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) och [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Lyckad sökning av en Shard-karta (med [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Skapandet av Shard-mappningen med CreateShardMap () lyckades.

Prestanda räknarna kommer att uppdateras av alla cache-åtgärder som utförs på Shard-mappning och mappningar. Borttagning av Shard-mappningen med hjälp av DeleteShardMap () medför att prestanda räknar instansen tas bort.  

## <a name="best-practices"></a>Bästa praxis

* Skapandet av prestanda kategorin och räknare bör endast utföras en gång innan ShardMapManager-objektet skapas. Varje körning av kommandot CreatePerformanceCategoryAndCounters () rensar tidigare räknare (förlorar data som rapporter ATS av alla instanser) och skapar nya.  
* Prestanda räknar instanser skapas per process. Alla program krascher eller borttagningar av en Shard-mappning från cachen leder till att prestanda räknar instanserna tas bort.  

### <a name="see-also"></a>Se också

[Översikt över Elastic Database-funktioner](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
