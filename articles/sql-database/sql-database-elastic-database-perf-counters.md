---
title: Skapa prestandaräknare för att spåra prestanda för karthanteraren för
description: ShardMapManager klass- och beroende routning prestandaräknare
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494926"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Skapa prestandaräknare för att spåra prestanda för karthanteraren för

Prestandaräknare som används för att spåra prestanda för [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) åtgärder. Dessa räknare kan nås i Prestandaövervakaren, under den ”elastisk databas: Fragmenthanterings ”kategori.

Du kan avbilda prestanda för en [karthanteraren](sql-database-elastic-scale-shard-map-management.md), särskilt när de använder [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md). Räknare skapas med metoderna i klassen Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**För den senaste versionen:** Gå till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Se även [uppgradera en app för att använda det senaste klientbiblioteket för elastiska databaser](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du vill skapa kategori för prestanda och räknare, måste användaren vara en del av lokalt **administratörer** på den dator som är värd för programmet.  
* Om du vill skapa en prestandaräknarinstans och uppdatera räknare, användaren måste vara medlem i den **administratörer** eller **användare av prestandaövervakning** grupp.

## <a name="create-performance-category-and-counters"></a>Skapa kategori för prestanda och räknare

För att skapa räknarna, anropar du metoden CreatePerformanceCategoryAndCounters i den [ShardMapManagementFactory klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Endast en administratör kan utföra den här metoden:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Du kan också använda [detta](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-skript för att utföra den här metoden.
Metoden skapar följande prestandaräknare:  

* **Cachelagrade mappningar**: Antalet mappningar som cachelagras i fragmentkartan.
* **DDR/sek**: Den hastighet med beroende routning dataåtgärder för fragmentkartan. Den här räknaren uppdateras när ett anrop till [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulterar i en lyckad anslutning till mål-fragment.
* **Mappning av sökning träffar/sek**: Antal lyckade cache sökåtgärder för mappningar i fragmentkartan.
* **Mappning av sökning cache missar/sek**: Antalet misslyckade cache sökåtgärder för mappningar i fragmentkartan.
* **Mappningar läggs till eller uppdateras i cache/sek**: Den hastighet som mappningar är läggs till eller uppdateras i cacheminnet för fragmentkartan.
* **Mappningar tas bort från cache/sek**: Den hastighet med vilken mappningar tas bort från cachen för fragmentkartan.

Prestandaräknare skapas för varje cachelagrade fragmentkartan processer.  

## <a name="notes"></a>Anteckningar

Följande händelser utlöser skapandet av prestandaräknare:  

* Initieringen av den [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) med [eager läser in](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), om ShardMapManager innehåller alla fragmentkartor. Dessa inkluderar den [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) och [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metoder.
* Lyckad sökning av en fragmentkartan (med hjälp av [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) eller [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Har skapats på fragmentkartan med CreateShardMap().

Prestandaräknare kommer att uppdateras av alla cacheåtgärder som utförs på fragmentkartan och mappningar. Lyckad borttagning av fragmentkartan med DeleteShardMap() leder till borttagning av räknare prestandainstans.  

## <a name="best-practices"></a>Bästa praxis

* Skapa kategori för prestanda och räknare bör bara utföras en gång innan skapandet av ShardMapManager-objektet. Varje körning av kommandot CreatePerformanceCategoryAndCounters() rensar tidigare räknare (att förlora data som rapporteras av alla instanser) och skapar nya.  
* Prestandaräknaren instanser skapas per process. Alla program krascha eller borttagning av en skärvkarta från cachen leder till borttagning av instanser för prestanda-räknare.  

### <a name="see-also"></a>Se också

[Funktionsöversikt för elastisk databas](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
