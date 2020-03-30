---
title: Instruktioner för instanspooler (förhandsgranskning)
description: I den här artikeln beskrivs hur du skapar och hanterar Azure SQL Database-instanspooler (förhandsversion).
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
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299370"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Instruktioner för Azure SQL Database-instanspooler (förhandsversion)

Den här artikeln innehåller information om hur du skapar och hanterar [instanspooler](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Instanspoolåtgärder

I följande tabell visas tillgängliga åtgärder som är relaterade till instanspooler och deras tillgänglighet i Azure-portalen och PowerShell.

|Kommando|Azure Portal|PowerShell|
|:---|:---|:---|
|Skapa instanspool|Inga|Ja|
|Uppdatera instanspool (begränsat antal egenskaper)|Inga |Ja |
|Kontrollera instanspoolanvändning och egenskaper|Inga|Ja |
|Ta bort instanspool|Inga|Ja|
|Skapa hanterad instans i instanspoolen|Inga|Ja|
|Uppdatera resursanvändning för hanterade instanser|Ja |Ja|
|Kontrollera användning och egenskaper för hanterade instanser|Ja|Ja|
|Ta bort hanterad instans från poolen|Ja|Ja|
|Skapa en databas i hanterad instans som placerats i poolen|Ja|Ja|
|Ta bort en databas från hanterad instans|Ja|Ja|

Tillgängliga [PowerShell-kommandon](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Beskrivning |
|:---|:---|
|[Ny-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Skapar en Azure SQL Database-instanspool. |
|[Hämta AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Returnerar information om Azure SQL-instanspool. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Anger egenskaper för en Azure SQL Database-instanspool. |
|[Ta bort-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Tar bort en Azure SQL Database-instanspool. |
|[Hämta AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Returnerar information om Azure SQL-instanspoolanvändning. |


Om du vill använda PowerShell [installerar du den senaste versionen av PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)och följer instruktionerna för att installera Azure [PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

För åtgärder som är relaterade till instanser både i pooler och enskilda instanser använder du [standardkommandona](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)för hanterade instanser , men *egenskapen för instanspoolnamn* måste fyllas i när dessa kommandon används för en instans i en pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Distribuera hanterade instanser i pooler

Processen att distribuera en instans till en pool består av följande två steg:

1. Enstaka instanspooldistribution. Detta är en tidskrävande åtgärd, där varaktigheten är densamma som att distribuera en enskild instans som [skapats i ett tomt undernät](sql-database-managed-instance.md#managed-instance-management-operations).

2. Repetitiv instansdistribution i en instanspool. Instanspoolparametern måste uttryckligen anges som en del av den här åtgärden. Detta är en relativt snabb operation som vanligtvis tar upp till 5 minuter.

I den offentliga förhandsversionen stöds båda stegen bara med PowerShell- och Resource Manager-mallar. Azure-portalupplevelsen är inte tillgänglig för närvarande.

När en hanterad instans har distribuerats till en pool *kan* du använda Azure-portalen för att ändra dess egenskaper på prisnivåsidan.


## <a name="create-an-instance-pool"></a>Skapa en instanspool

Så här skapar du en instanspool:

1. [Skapa ett virtuellt nätverk med ett undernät](#create-a-virtual-network-with-a-subnet).
2. [Skapa en instanspool](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Skapa ett virtuellt nätverk med ett undernät 

Om du vill placera flera instanspooler i samma virtuella nätverk läser du följande artiklar:

- [Fastställ VNet-undernätsstorlek för en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Skapa nytt virtuellt nätverk och undernät med hjälp av [Azure-portalmallen](sql-database-managed-instance-create-vnet-subnet.md) eller följ instruktionerna för [att förbereda ett befintligt virtuellt nätverk](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Skapa en instanspool 

När du har slutfört föregående steg är du redo att skapa en instanspool.

Följande begränsningar gäller för instanspooler:

- Endast Allmänt syfte och Gen5 är tillgängliga i offentlig förhandsversion.
- Poolnamnet kan bara innehålla gemener, tal och bindestreck och kan inte börja med ett bindestreck.
- Om du vill använda AHB (Azure Hybrid Benefit) tillämpas det på instanspoolnivå. Du kan ange licenstypen när poolen skapas eller uppdatera den när som helst när den har skapats.

> [!IMPORTANT]
> Att distribuera en instanspool är en tidskrävande åtgärd som tar cirka 4,5 timmar.

Så här hämtar du nätverksparametrar:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Så här skapar du en instanspool:

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
> Eftersom distribution av en instanspool är en tidskrävande åtgärd måste du vänta tills den är klar innan du kör något av följande steg i den här artikeln.

## <a name="create-a-managed-instance-inside-the-pool"></a>Skapa en hanterad instans i poolen 

Efter den lyckade distributionen av instanspoolen är det dags att skapa en instans inuti den.

Om du vill skapa en hanterad instans kör du följande kommando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Det tar ett par minuter att distribuera en instans i en pool. När den första instansen har skapats kan ytterligare instanser skapas:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Skapa en databas i en instans 

Om du vill skapa och hantera databaser i en hanterad instans som finns i en pool använder du kommandona för en instans.

Så här skapar du en databas i en hanterad instans:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Hämta instanspoolanvändning 
 
Så här hämtar du en lista över instanser i en pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Så här hämtar du poolresursanvändning:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Så här får du detaljerad användningsöversikt över poolen och instanser inuti den:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Så här listar du databaserna i en instans:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Det finns en gräns på 100 databaser per pool (inte per instans).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Skala en hanterad instans i en pool 


När du har fyllt i en hanterad instans med databaser kan du nå instansgränser för lagring eller prestanda. I så fall, om poolanvändningen inte har överskridits, kan du skala instansen.
Att skala en hanterad instans i en pool är en åtgärd som tar ett par minuter. Förutsättningen för skalning är tillgänglig vCores och lagring på instanspoolnivå.

Så här uppdaterar du antalet virtuella kärnor och lagringsstorlek:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Så här uppdaterar du endast lagringsstorleken:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Ansluta till en hanterad instans i en pool

För att ansluta till en hanterad instans i en pool krävs följande två steg:

1. [Aktivera den offentliga slutpunkten för instansen](#enable-the-public-endpoint-for-the-instance).
2. [Lägg till en inkommande regel i nätverkssäkerhetsgruppen (NSG)](#add-an-inbound-rule-to-the-network-security-group).

När båda stegen är klara kan du ansluta till instansen med hjälp av en offentlig slutpunktsadress, port och autentiseringsuppgifter som tillhandahålls när instansen skapas. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Aktivera den offentliga slutpunkten för instansen

Aktivera den offentliga slutpunkten för en instans kan göras via Azure-portalen eller med hjälp av följande PowerShell-kommando:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Den här parametern kan också ställas in när instans skapas.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Lägga till en inkommande regel i nätverkssäkerhetsgruppen 

Det här steget kan göras via Azure-portalen eller med PowerShell-kommandon och kan göras när som helst när undernätet har förberetts för den hanterade instansen.

Mer information finns i [Tillåt offentlig slutpunktstrafik i nätverkssäkerhetsgruppen](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Flytta en befintlig en instans i en instanspool 
 
Att flytta instanser in och ut ur en pool är en av begränsningarna för den offentliga förhandsversionen. En lösning som kan användas är beroende av point-in-time-återställning av databaser från en instans utanför en pool till en instans som redan finns i en pool. 

Båda instanserna måste finnas i samma prenumeration och region. Återställning mellan regioner och flera prenumerationer stöds för närvarande inte.

Den här processen har en period av driftstopp.

Så här flyttar du befintliga databaser:

1. Pausa arbetsbelastningar på den hanterade instans som du migrerar från.
2. Generera skript för att skapa systemdatabaser och köra dem på instansen som finns i instanspoolen.
3. Gör en point-in-time-återställning av varje databas från den enskilda instansen till instansen i poolen.

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

4. Peka programmet på den nya instansen och återuppta sina arbetsbelastningar.

Om det finns flera databaser upprepar du processen för varje databas.


## <a name="next-steps"></a>Nästa steg

- En lista över funktioner och jämförelser finns i [vanliga SQL-funktioner](sql-database-features.md).
- Mer information om VNet-konfiguration finns i [VNet-konfiguration för hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- En snabbstart som skapar en hanterad instans och återställer en databas från en säkerhetskopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En självstudiekurs med HJÄLP av Azure Database Migration Service (DMS) för migrering finns i [tjänstring för hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av prestanda för hanterade instansdatabaser med inbyggd felsökningsinformation finns i [Övervaka Azure SQL Database med Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Prisinformation finns i [SQL Database-priser för hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
