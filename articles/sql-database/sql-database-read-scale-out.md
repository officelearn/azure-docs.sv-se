---
title: Azure SQL-databas – Läs frågor på repliker | Microsoft Docs
description: Azure SQL Database ger möjlighet att läsa in saldo skrivskyddade arbetsbelastningar med hjälp av kapaciteten för skrivskyddade repliker - kallas Lässkalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619899"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Använda skrivskyddade repliker att läsa in saldo skrivskyddad frågearbetsbelastningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

**Lässkalning** kan du belastningsutjämna Azure SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för en skrivskyddad replik.

Varje databas i Premium-nivån ([DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md)) eller i nivån affärskritisk ([vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md)) etableras automatiskt med flera AlwaysON-repliker till stöd för serviceavtal för tillgänglighet. Detta visas i följande diagram.

![Skrivskyddade repliker](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

De sekundära replikerna etableras med samma beräkning storlek som den primära repliken. Den **Lässkalning** funktionen kan du belastningsutjämna SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för en av de skrivskyddade replikerna istället för att dela Läs-och repliken. Det här sättet skrivskyddad arbetsbelastning isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar, till exempel analyser, och därför kan få prestandafördelarna med hjälp av den här ytterligare kapacitet utan extra kostnad.

Om du vill använda funktionen Lässkalning med en viss databas, måste du uttryckligen aktivera det när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att aktivera den [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) eller [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdletar eller Azure Resource Manager REST API med hjälp av den [databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) metod.

När Lässkalning är aktiverade för en databas, program som ansluter till databasen hänvisas av gatewayen till Läs-och-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i den anslutningssträngen för program. Information om den `ApplicationIntent` egenskap, finns i [att ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om Lässkalning är inaktiverad eller egenskapen ReadScale på en nivå i tjänsten stöds inte, alla anslutningar dirigeras till den skrivskyddade repliken, oberoende av den `ApplicationIntent` egenskapen.

> [!NOTE]
> Fråga Data Store, Extended Events, SQL Profiler och granska funktioner stöds inte på skrivskyddade repliker. 
## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med repliker är att alla kopior alltid är i ett konsekvent tillstånd, men vid olika tidpunkter det finnas viss liten fördröjning mellan olika repliker. Lässkalning stöder konsekvens på sessionsnivå. Det innebär om skrivskyddade sessionen återansluts när ett anslutningsfel som orsakas av repliken otillgänglighet dirigeras om till en replik som inte är 100% uppdaterade med läs-och repliken. På samma sätt, om ett program skriver data med hjälp av en Skriv-session och läser den med hjälp av en skrivskyddad session omedelbart, det är möjligt att de senaste uppdateringarna inte visas direkt på repliken. Fördröjningen orsakas av en asynkron transaction log gör om-åtgärd.

> [!NOTE]
> Replikeringsfördröjningar för regionen är låga och den här situationen är ovanligt.

## <a name="connect-to-a-read-only-replica"></a>Ansluta till en skrivskyddad replik

När du aktiverar Lässkalning för en databas, den `ApplicationIntent` alternativet i anslutningssträngen som tillhandahålls av klienten avgör om anslutningen dirigeras till skrivning repliken eller till en skrivskyddad replik. Mer specifikt om den `ApplicationIntent` värdet är `ReadWrite` (standardvärde) anslutningen dirigeras till Läs-och databasrepliken. Det här är identiska med befintliga beteende. Om den `ApplicationIntent` värdet är `ReadOnly`, anslutningen dirigeras till en skrivskyddad replik.

Till exempel ansluter följande anslutningssträng klienten till en skrivskyddad replik (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Något av följande anslutningssträngar ansluter klienten till en skrivskyddad replik (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Kontrollera att det är en anslutning till en skrivskyddad replik

Du kan kontrollera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. READ_ONLY när du är ansluten till en skrivskyddad replik returneras.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Vid en given tidpunkt kan endast en av AlwaysON-repliker nås av ReadOnly-sessioner.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Övervakning och felsökning skrivskyddad replik

När du är ansluten till en skrivskyddad replik, du kan komma åt den prestanda mått med hjälp av den `sys.dm_db_resource_stats` DMV. För att komma åt fråga plan statistik, använder den `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` och `sys.dm_exec_sql_text` DMV: er.

> [!NOTE]
> DMV `sys.resource_stats` returnerar CPU-användning och lagring data för den primära repliken i den logiska huvuddatabasen.


## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera Lässkalning

Lässkalning är aktiverat som standard i [Managed Instance](sql-database-managed-instance.md) nivån affärskritisk. Funktionen ska vara explicit aktiverad i [databasen placeras på SQL-databasserver](sql-database-servers.md) Premium och affärskritisk nivåer. Här beskrivs metoder för att aktivera och inaktivera Lässkalning.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Aktivera och inaktivera Lässkalning

Hantera Lässkalning i Azure PowerShell kräver December 2016 Azure PowerShell-versionen eller senare. Den senaste versionen av PowerShell, se [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Aktivera eller inaktivera Läs skalbar i Azure PowerShell genom att aktivera den [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet och skickar det önskade värdet – `Enabled` eller `Disabled` --för den `-ReadScale` parametern. Du kan också använda den [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet för att skapa en ny databas med lässkalbarhet aktiverat.

Till exempel lässkalbarhet om du vill aktivera för en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Inaktivera Läs skalbar för en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Att skapa en ny databas med läsning skalbar aktiverad (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST-API: Aktivera och inaktivera Lässkalning

Om du vill skapa en databas med skrivskyddade skalbar aktiverad, eller aktivera eller inaktivera Läs skalbar för en befintlig databas, skapa eller uppdatera motsvarande databasen entiteten med det `readScale` egenskapen `Enabled` eller `Disabled` som i den nedan exemplet begäran.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Mer information finns i [databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Med hjälp av TempDB på skrivskyddade replik

TempDB-databasen inte har replikerats till de skrivskyddade replikerna. Varje replik har sin egen version av TempDB-databasen som skapades när repliken skapas. Det säkerställer att TempDB är uppdateringsbar och kan ändras vid körning av din fråga. Om din skrivskyddad arbetsbelastning är beroende av TempDB-objekt, bör du skapa de här objekten som en del av din Frågeskript. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Med geo-replikerade databaser Lässkalning

Om du använder Läs skalbar att läsa in saldo skrivskyddade arbetsbelastningar för en databas som är geo-replikerade (till exempel som en medlem i en redundansgrupp), se till att Läs är skalbara aktiverat på både primär och sekundär geo-replikerade databaser. Den här konfigurationen garanterar att samma upplevelse för Utjämning av nätverksbelastning fortsätter när ditt program som ansluter till den nya primärt efter en redundansväxling. Om du ansluter till geo-replikerad sekundär databas med lässkala aktiverad sessioner till `ApplicationIntent=ReadOnly` kommer att dirigeras till en av replikerna på samma sätt som vi dirigera anslutningar på den primära databasen.  Sessioner utan `ApplicationIntent=ReadOnly` kommer att dirigeras till den primära repliken för den georeplikerade sekundärt, vilket också är skrivskyddad. Eftersom geo-replikerad sekundär databas har en annan slutpunkt än den primära databasen, historiskt att få åtkomst till sekundärt det inte krävs för att ange `ApplicationIntent=ReadOnly`. Att säkerställa bakåtkompatibilitet, `sys.geo_replication_links` DMV visar `secondary_allow_connections=2` (alla klientanslutning tillåts).

> [!NOTE]
> Resursallokering eller andra belastningsutjämnade routning mellan lokala repliker av den sekundära databasen stöds inte.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder PowerShell för att ställa in Läs skalbar finns i den [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) eller [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdletar.
- Information om hur du använder REST API för att ställa in Läs skalbar finns i [databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
