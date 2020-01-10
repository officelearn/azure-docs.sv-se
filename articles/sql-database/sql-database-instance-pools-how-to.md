---
title: Instruktioner för instansen i guiden (för hands version)
description: Den här artikeln beskriver hur du skapar och hanterar Azure SQL Database instanser (för hands version).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 5a45b9e3ba59a91f580ce0f2dc180adf5d20c87d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754053"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Instruktions guide för Azure SQL Database instanser (förhands granskning)

Den här artikeln innehåller information om hur du skapar och hanterar [instanser av instanser](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Åtgärder för instans-pool

I följande tabell visas tillgängliga åtgärder relaterade till instans-pooler och deras tillgänglighet i Azure Portal och PowerShell.

|Kommando|Azure portal|PowerShell|
|:---|:---|:---|
|Skapa instanspool|Inga|Ja|
|Uppdatera instans-pool (begränsat antal egenskaper)|Inga |Ja |
|Kontrol lera användning och egenskaper för entitetsinstansen|Inga|Ja |
|Ta bort instans-pool|Inga|Ja|
|Skapa hanterad instans i instans poolen|Inga|Ja|
|Uppdatera hanterad instans resursanvändning|Ja |Ja|
|Kontrol lera användning och egenskaper för hanterad instans|Ja|Ja|
|Ta bort hanterad instans från poolen|Ja|Ja|
|Skapa en databas i en hanterad instans som placerats i poolen|Ja|Ja|
|Ta bort en databas från hanterad instans|Ja|Ja|

Tillgängliga [PowerShell-kommandon](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Beskrivning |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Skapar en Azure SQL Database instans-pool. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Returnerar information om Azure SQL instance-poolen. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Anger egenskaper för en Azure SQL Database instans-pool. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Tar bort en Azure SQL Database instans-pool. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Returnerar information om användningen av Azure SQL-instansen. |


Om du vill använda PowerShell [installerar du den senaste versionen av PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)och följer anvisningarna för att [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

För åtgärder relaterade till instanser både i pooler och enskilda instanser använder du standard [kommandon för hanterade instanser](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), men egenskapen *namn på instanss bassäng* måste fyllas i när du använder dessa kommandon för en instans i en pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Distribuera hanterade instanser i pooler

Processen för att distribuera en instans till en pool består av följande två steg:

1. Distribution av en instans-pool. Detta är en tids krävande åtgärd där varaktigheten är samma som att distribuera en [enskild instans som skapats i ett tomt undernät](sql-database-managed-instance.md#managed-instance-management-operations).

2. Upprepande instans distribution i en instans-pool. Parametern för entitetsinstansen måste anges explicit som en del av den här åtgärden. Detta är en relativt snabb åtgärd som vanligt vis tar upp till 5 minuter.

I offentlig för hands version stöds båda stegen bara med PowerShell-och Resource Manager-mallar. Den Azure Portal upplevelsen är inte tillgänglig för närvarande.

När en hanterad instans har distribuerats till en pool *kan* du använda Azure Portal för att ändra dess egenskaper på sidan pris nivå.


## <a name="create-an-instance-pool"></a>Skapa en instans-pool

Så här skapar du en instans-pool:

1. [Skapa ett virtuellt nätverk med ett undernät](#create-a-virtual-network-with-a-subnet).
2. [Skapa en instans-pool](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Skapa ett virtuellt nätverk med ett undernät 

Information om hur du placerar flera instanser i samma virtuella nätverk finns i följande artiklar:

- [Bestäm storleken på VNet-undernät för en Azure SQL Database Hanterad instans](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Skapa ett nytt virtuellt nätverk och undernät med [Azure Portal-mallen](sql-database-managed-instance-create-vnet-subnet.md) eller följ instruktionerna för att [förbereda ett befintligt virtuellt nätverk](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Skapa en instans-pool 

När du har slutfört föregående steg är du redo att skapa en instans-pool.

Följande begränsningar gäller för instans pooler:

- Endast Generell användning och Gen5 är tillgängliga i offentlig för hands version.
- Poolnamn får bara innehålla gemener, siffror och bindestreck, och får inte börja med ett bindestreck.
- Om du vill använda AHB (Azure Hybrid-förmån) tillämpas den på instans nivå. Du kan ange licens typ när du skapar en pool eller uppdatera den när den har skapats.

> [!IMPORTANT]
> Distribution av en instans-pool är en tids krävande åtgärd som tar cirka 4,5 timmar.

Hämta nätverks parametrar:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Så här skapar du en instans-pool:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Eftersom distributionen av en instans är en tids krävande åtgärd måste du vänta tills den har slutförts innan du kör något av följande steg i den här artikeln.

## <a name="create-a-managed-instance-inside-the-pool"></a>Skapa en hanterad instans inuti poolen 

När du har slutfört distributionen av instansen är det dags att skapa en instans inuti den.

Kör följande kommando för att skapa en hanterad instans:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Det tar några minuter att distribuera en instans i en pool. När den första instansen har skapats kan ytterligare instanser skapas:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Skapa en databas inuti en instans 

Om du vill skapa och hantera databaser i en hanterad instans i en pool använder du kommandona för den enskilda instansen.

Så här skapar du en databas inuti en hanterad instans:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Hämta användning av instans-pool 
 
Så här hämtar du en lista över instanser i en pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Så här hämtar du resursanvändningen för poolen:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


För att få detaljerad användnings översikt över poolen och instanserna i den:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Visa en lista över databaser i en instans:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Det finns en gräns på 100 databaser per pool (inte per instans).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Skala en hanterad instans i en pool 


När du har fyllt i en hanterad instans med databaser kan du träffa instans gränser för lagring eller prestanda. I så fall kan du skala instansen om pool användningen inte har överskridits.
Att skala en hanterad instans i en pool är en åtgärd som tar några minuter. Förutsättningen för skalning är tillgänglig virtuella kärnor och lagring på instance Poolnivå.

Så här uppdaterar du antalet virtuella kärnor och lagrings storlek:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Så här uppdaterar du endast lagrings storlek:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Ansluta till en hanterad instans i en pool

Följande två steg krävs för att ansluta till en hanterad instans i en pool:

1. [Aktivera den offentliga slut punkten för instansen](#enable-the-public-endpoint-for-the-instance).
2. [Lägg till en regel för inkommande trafik i nätverks säkerhets gruppen (NSG)](#add-an-inbound-rule-to-the-network-security-group).

När båda stegen har slutförts kan du ansluta till instansen med hjälp av en offentlig slut punkts adress, port och autentiseringsuppgifter som angavs när instansen skapades. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Aktivera den offentliga slut punkten för instansen

Aktivering av den offentliga slut punkten för en instans kan göras via Azure Portal eller med hjälp av följande PowerShell-kommando:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Den här parametern kan också anges när instansen skapas.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Lägg till en regel för inkommande trafik i nätverks säkerhets gruppen 

Det här steget kan göras via Azure Portal eller med hjälp av PowerShell-kommandon och kan göras när under nätet har förberetts för den hanterade instansen.

Mer information finns i [Tillåt offentlig slut punkts trafik i nätverks säkerhets gruppen](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Flytta en befintlig enskild instans i en instans-pool 
 
Att flytta instanser in och ut ur en pool är en av de offentliga begränsningarna för för hands versionen. En lösning som kan användas är beroende av en tidpunkt för återställning av databaser från en instans utanför en pool till en instans som redan finns i en pool. 

Båda instanserna måste finnas i samma prenumeration och region. Återställning mellan regioner och över prenumerationer stöds inte för närvarande.

Den här processen har en drifts tid.

Flytta befintliga databaser:

1. Pausa arbets belastningar på den hanterade instans som du migrerar från.
2. Skapa skript för att skapa system databaser och köra dem på instansen i instansen.
3. Gör en tidpunkts återställning av varje databas från den enskilda instansen till instansen i poolen.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Peka ditt program på den nya instansen och återuppta det arbets belastningar.

Upprepa processen för varje databas om det finns flera databaser.


## <a name="next-steps"></a>Nästa steg

- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](sql-database-features.md).
- Mer information om VNet-konfiguration finns i [konfiguration av VNet-instansen VNet](sql-database-managed-instance-connectivity-architecture.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En själv studie kurs om hur du använder Azure Database Migration Service (DMS) för migrering finns i [migrering av hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av hanterade instanser av databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL Database att använda Azure SQL-analys](../azure-monitor/insights/azure-sql.md).
- Pris information finns i [priser för SQL Database Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
