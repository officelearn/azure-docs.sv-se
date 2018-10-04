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
ms.reviewer: carlrab
manager: craigg
ms.date: 10/01/2018
ms.openlocfilehash: bc322857a459f9417ed7c89a6e4df7ce5c41c3f0
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246489"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Använd skrivskyddade repliker för att läsa in balansera skrivskyddad frågearbetsbelastningar (förhandsversion)

**Lässkalning** kan du belastningsutjämna Azure SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för en skrivskyddad replik. 

## <a name="overview-of-read-scale-out"></a>Översikt över Lässkalning

Varje databas i Premium-nivån ([DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md)) eller i nivån affärskritisk ([vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md)) etableras automatiskt med flera AlwaysON-repliker till stöd för serviceavtal för tillgänglighet.

![Skrivskyddade repliker](media/sql-database-managed-instance/business-critical-service-tier.png)

De här replikeringarna etableras med samma beräkningsstorleken som används av de vanliga databasanslutningarna skrivskyddade replik. Den **Lässkalning** funktionen kan du belastningsutjämna SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för en av de skrivskyddade replikerna istället för att dela Läs-och repliken. Det här sättet skrivskyddad arbetsbelastning isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar, till exempel analyser, och därför kan få prestandafördelarna med hjälp av den här ytterligare kapacitet utan extra kostnad.

Om du vill använda funktionen Lässkalning med en viss databas, måste du uttryckligen aktivera det när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att aktivera den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [ Ny-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdletar eller Azure Resource Manager REST API med hjälp av den [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate) metod. 

När Lässkalning är aktiverade för en databas, program som ansluter till den här databasen kommer att dirigeras till Läs-och-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i programmets anslutningssträng. Information om den `ApplicationIntent` egenskap, finns i [att ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om Lässkalning är inaktiverad eller egenskapen ReadScale på en nivå i tjänsten stöds inte, alla anslutningar dirigeras till den skrivskyddade repliken, oberoende av den `ApplicationIntent` egenskapen.

> [!NOTE]
> I förhandsversionen stöds inte fråga Data Store och utökade händelser på de skrivskyddade replikerna.

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med Alwayson är att alla kopior alltid är i ett konsekvent tillstånd, men vid olika tidpunkter det finnas viss liten fördröjning mellan olika repliker. Lässkalning stöder konsekvens på sessionsnivå. Det innebär om skrivskyddade sessionen återansluts när ett anslutningsfel som orsakas av repliken otillgänglighet dirigeras om till en replik som inte är 100% uppdaterade med läs-och repliken. På samma sätt, om ett program skriver data med hjälp av en Skriv-session och läser den med hjälp av en skrivskyddad session omedelbart, det är möjligt att de senaste uppdateringarna inte visas direkt. Det beror på att transaktionen log gör om att replikerna är asynkron.

> [!NOTE]
> Replikeringsfördröjningar för regionen är låga och den här situationen är ovanligt.


## <a name="connecting-to-a-read-only-replica"></a>Ansluta till en skrivskyddad replik

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

Du kan kontrollera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. READ_ONLY när du är ansluten till en skrivskyddad replik returneras.


```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```
> [!NOTE]
> Vid en given tidpunkt kan endast en av AlwaysON-repliker nås av ReadOnly-sessioner.

## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera Lässkalning

Lässkalning är aktiverat som standard i [Managed Instance](sql-database-managed-instance.md) affärskritisk tier(Preview). Funktionen ska vara explicit aktiverad i [databasen placeras på logisk server](sql-database-logical-servers.md) Premium och affärskritisk nivåer. Här beskrivs metoder för att aktivera och inaktivera Lässkalning. 

### <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Aktivera och inaktivera Lässkalning med hjälp av Azure PowerShell

Hantera Lässkalning i Azure PowerShell kräver December 2016 Azure PowerShell-versionen eller senare. Den senaste versionen av PowerShell, se [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Aktivera eller inaktivera Läs skalbar i Azure PowerShell genom att aktivera den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet och skickar det önskade värdet – `Enabled` eller `Disabled` --för den `-ReadScale` parametern. Du kan också använda den [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet för att skapa en ny databas med lässkalbarhet aktiverat.

Till exempel lässkalbarhet om du vill aktivera för en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Inaktivera Läs skalbar för en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Att skapa en ny databas med läsning skalbar aktiverad (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Aktivera och inaktivera Lässkalning med hjälp av Azure SQL Database REST API

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

Mer information finns i [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Med geo-replikerade databaser Lässkalning

Om du använder lässkalbarhet att läsa in saldo skrivskyddade arbetsbelastningar för en databas som är geo-replikerade (t.ex. som en medlem i en redundansgrupp), se till att läsa skalbara är aktiverad på både primär och sekundär geo-replikerade databaser. Det garanterar samma belastningsutjämning effekt när ditt program som ansluter till den nya primärt efter en redundansväxling. Om du ansluter till geo-replikerad sekundär databas med lässkala aktiverad sessioner till `ApplicationIntent=ReadOnly` kommer att dirigeras till en av replikerna på samma sätt som vi dirigera anslutningar på den primära databasen.  Sessioner utan `ApplicationIntent=ReadOnly` kommer att dirigeras till den primära repliken för den georeplikerade sekundärt, vilket också är skrivskyddad. Eftersom geo-replikerad sekundär databas har en annan slutpunkt än den primära databasen, historiskt att få åtkomst till sekundärt det inte krävs för att ange `ApplicationIntent=ReadOnly`. Att säkerställa bakåtkompatibilitet, `sys.geo_replication_links` DMV visar `secondary_allow_connections=2` (alla klientanslutning tillåts).

> [!NOTE]
> Under förhandsgranskning, resursallokering eller andra belastningen stöds belastningsutjämnade routning mellan lokala repliker av den sekundära databasen inte. 


## <a name="next-steps"></a>Nästa steg

- Information om hur du använder PowerShell för att ställa in Läs skalbar finns i den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdletar.
- Information om hur du använder REST API för att ställa in Läs skalbar finns i [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate).
