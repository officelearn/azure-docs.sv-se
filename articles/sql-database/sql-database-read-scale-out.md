---
title: Azure SQL Database - läsa frågor på repliker | Microsoft Docs
description: Azure SQL Database ger möjlighet att läsa in saldo skrivskyddade arbetsbelastningar som använder skrivskyddade repliker - kallas Läs skalbar kapacitet.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: sashan
ms.openlocfilehash: 8de70c01f4c04d6df85c2f5acfe9efe18ff59c0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649694"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Använd skrivskyddade repliker att läsa in saldo skrivskyddade frågeresultat arbetsbelastningar (förhandsgranskning)

**Läs skalbar** kan du läsa in saldo Azure SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för skrivskyddade repliker. 

## <a name="overview-of-read-scale-out"></a>Översikt över Läs skalbar

Varje databas i Premium-nivån ([DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md)) eller i företag kritiska nivåer ([vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md)) etableras automatiskt med flera Always ON stöd för tillgänglighets-SLA replikerna. De här replikeringarna etableras med samma prestandanivå som skrivskyddad replik som används av vanlig databas-anslutningar. Den **Läs skalbar** funktionen kan du läsa in saldo SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för skrivskyddade repliker istället för att dela skrivskyddad replik. Det här sättet skrivskyddad arbetsbelastning isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar, t.ex analytics, och därför kan få prestandafördelarna med hjälp av den här ytterligare kapacitet utan extra kostnad.

Om du vill använda funktionen för Läs skalbar med en viss databas, måste du uttryckligen aktivera det när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att anropa den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [ Nya AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlets eller via Azure Resource Manager REST-API med hjälp av [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate) metod. 

När Läs skalbar har aktiverats för en databas måste program som ansluter till databasen kommer att dirigeras till skrivskyddad-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i programmets anslutningssträng. Mer information om den `ApplicationIntent` egenskapen finns [ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om Läs skalbar är inaktiverat eller egenskapen ReadScale på en tjänstnivå som inte stöds, dirigeras alla anslutningar till skrivskyddad replik, oberoende av den `ApplicationIntent` egenskapen.

> [!NOTE]
> Under förhandsgranskningen gör stöds frågan datalager och Extended Events inte på skrivskyddade repliker.

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med Alwayson är att replikerna är alltid i ett konsekvent tillstånd, men vid olika tidpunkter det kan finnas vissa liten fördröjning mellan olika repliker. Läs skalbar stöder session-nivå konsekvenskontroll. Det innebär om skrivskyddad sessionen återansluts när ett anslutningsfel på grund av att repliken, dirigeras till en replik som inte är 100% uppdaterade med skrivskyddad replik. Även om ett program skriver data med hjälp av en skrivskyddad-session och läser den med hjälp av en skrivskyddad session omedelbart, är det möjligt att de senaste uppdateringarna inte visas direkt. Det beror på att transaktionen loggen gör att replikerna är asynkron.

> [!NOTE]
> Replikeringsfördröjningar för regionen är låg och den här situationen är ovanligt.


## <a name="connecting-to-a-read-only-replica"></a>Ansluter till en skrivskyddad replik

När du aktiverar Läs skalbar för en databas i `ApplicationIntent` alternativet i anslutningssträngen som tillhandahölls av klienten avgör om anslutningen dirigeras till skrivåtgärder repliken eller till en skrivskyddad replik. I synnerhet om den `ApplicationIntent` värdet är `ReadWrite` (standardvärdet) anslutningen dirigeras till skrivskyddad databasrepliken. Detta är identiska med befintliga beteende. Om den `ApplicationIntent` värdet är `ReadOnly`, omdirigeras till en läsbar replik.

Till exempel ansluter följande anslutningssträng klienten till en skrivskyddad replik (ersätta objekten i hakparenteser med korrekta värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Något av följande anslutningssträngar ansluter klienten till en skrivskyddad replik (ersätta objekten i hakparenteser med korrekta värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Du kan kontrollera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. READ_ONLY när du är ansluten till en skrivskyddad replik returneras.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Aktivera och inaktivera Läs skalbar med hjälp av Azure PowerShell

Hantera Läs skalbar i Azure PowerShell kräver December 2016 Azure PowerShell version eller senare. Den senaste versionen i PowerShell finns i [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Aktivera eller inaktivera skrivskyddade skalbar i Azure PowerShell genom att anropa den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet och skickar det önskade värdet – `Enabled` eller `Disabled` --för den `-ReadScale` parameter. Du kan också använda den [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) för att skapa en ny databas med läsa skalbar aktiverad.

Till exempel läser om du vill aktivera skalbar för en befintlig databas (ersätta objekten i hakparenteser med korrekta värden för din miljö och släppa hakparenteser):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Inaktivera skrivskyddade skalbar för en befintlig databas (ersätta objekten i hakparenteser med korrekta värden för din miljö och släppa hakparenteser):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Skapa en ny databas med skrivskyddade skalbar-aktiverade (ersätta objekten i hakparenteser med korrekta värden för din miljö och släppa hakparenteser):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Aktivera och inaktivera Läs skalbar med hjälp av Azure SQL Database REST API

Om du vill skapa en databas med skrivskyddade skalbar aktiverad, eller aktivera eller inaktivera skrivskyddade skalbar för en befintlig databas, skapa eller uppdatera databasen motsvarande entitet med den `readScale` egenskapen `Enabled` eller `Disabled` som i den nedan exempel begäran.

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

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder PowerShell för att ange skrivskyddad skalbar finns i [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlets.
- Information om hur du använder REST API för att ange skrivskyddad skalbar finns [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate).
