---
title: Prestanda räknare för att spåra Shard Map Manager
description: Prestanda räknare för ShardMapManager-klass och data beroende routning
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 6038ec1d83957f20ca6e2759eeb5a88e66c2f77f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443410"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Skapa prestanda räknare för att spåra prestanda för Shard Map Manager
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Prestanda räknare används för att spåra prestanda för [data beroende routnings](elastic-scale-data-dependent-routing.md) åtgärder. Dessa räknare är tillgängliga i prestanda övervakaren under kategorin Elastic Database: Shard-hantering.

Du kan samla in prestanda för en [Shard Map Manager](elastic-scale-shard-map-management.md), särskilt när du använder [data beroende routning](elastic-scale-data-dependent-routing.md). Räknare skapas med metoder för klassen Microsoft. Azure. SqlDatabase. ElasticScale. client.  


**För den senaste versionen:** Gå till [Microsoft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se även [uppgradera en app för att använda det senaste klient biblioteket för Elastic Database](elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Krav

* Om du vill skapa prestanda kategori och räknare måste användaren vara en del av den lokala gruppen **Administratörer** på den dator som är värd för programmet.  
* Om du vill skapa en prestanda räknar instans och uppdatera räknarna måste användaren vara medlem i gruppen användare av **Administratörer** eller **prestanda övervakning** .

## <a name="create-performance-category-and-counters"></a>Skapa prestanda kategori och räknare

Om du vill skapa räknarna anropar du CreatePerformanceCategoryAndCounters-metoden för [ShardMapManagementFactory-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Endast en administratör kan köra metoden:

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

Du kan också använda [det här](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-skriptet för att köra-metoden.
Metoden skapar följande prestanda räknare:  

* **Cachelagrade mappningar**: antal mappningar som cachelagrats i Shard-kartan.
* **DDR-åtgärder/SEK**: frekvensen för data beroende routnings åtgärder för Shard-kartan. Den här räknaren uppdateras när ett anrop till [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulterar i en lyckad anslutning till mål-Shard.
* **Mappning av söknings-cacheträffar träffar/SEK**: takt för lyckade sökåtgärder för sökning i Shard-mappningen.
* **Mappning av sökningar i cache-missar/SEK**: frekvensen av misslyckade söknings åtgärder för sökning i Shard-kartan.
* **Mappningar som lagts till eller uppdaterats i cache/s**: den hastighet med vilken mappningar läggs till eller uppdateras i cacheminnet för Shard-kartan.
* **Mappningar som tagits bort från cache/s**: den hastighet med vilken mappningar tas bort från cachen för Shard-kartan.

Prestanda räknare skapas för varje cachelagrad Shard-mappning per process.  

## <a name="notes"></a>Kommentarer

Följande händelser utlöser skapandet av prestanda räknare:  

* Initieringen av [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) med [Eager inläsning](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)om ShardMapManager innehåller några Shard Maps. Dessa omfattar metoderna [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) och [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Lyckad sökning av en Shard-karta (med [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Skapandet av Shard-mappningen med CreateShardMap () lyckades.

Prestanda räknarna kommer att uppdateras av alla cache-åtgärder som utförs på Shard-mappning och mappningar. Borttagning av Shard-mappningen med hjälp av DeleteShardMap () medför att prestanda räknar instansen tas bort.  

## <a name="best-practices"></a>Bästa praxis

* Skapandet av prestanda kategorin och räknare bör endast utföras en gång innan ShardMapManager-objektet skapas. Varje körning av kommandot CreatePerformanceCategoryAndCounters () rensar tidigare räknare (förlorar data som rapporter ATS av alla instanser) och skapar nya.  
* Prestanda räknar instanser skapas per process. Alla program krascher eller borttagningar av en Shard-mappning från cachen leder till att prestanda räknar instanserna tas bort.  

### <a name="see-also"></a>Se även

[Översikt över Elastic Database-funktioner](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
