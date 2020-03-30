---
title: Konfigurera en redundansgrupp
description: Lär dig hur du konfigurerar en grupp för automatisk redundans för en enda Azure SQL-databas, elastisk pool och hanterad instans med Azure-portalen, Az CLI och PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461822"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurera en redundansk grupp för Azure SQL Database

I det här avsnittet får du lära dig hur du konfigurerar en grupp för [automatisk redundans](sql-database-auto-failover-group.md) för en enda Azure SQL-databas, elastisk pool och hanterad instans med Azure-portalen eller PowerShell. 

## <a name="single-database"></a>Enskild databas
Skapa redundansgruppen och lägg till en enskild databas i den med Azure-portalen eller PowerShell.

### <a name="prerequisites"></a>Krav

Tänk på följande förutsättningar:

- Serverinloggnings- och brandväggsinställningarna för den sekundära servern måste matcha den primära serverns. 

### <a name="create-failover-group"></a>Skapa redundansgrupp

# <a name="portal"></a>[Portal](#tab/azure-portal)
Skapa din redundansgrupp och lägg till din enda databas i den med Hjälp av Azure-portalen.


1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den enda databas som du vill lägga till i redundansgruppen. 
1. Välj namnet på servern under **Servernamn** för att öppna inställningarna för servern.

   ![Öppna server för en enda db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan Lägg **till grupp** för att skapa en ny redundanskitetsgrupp. 

    ![Lägga till ny redundansgrupp](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. På sidan **Redundansk gruppera** du eller markera de värden som krävs och välj sedan **Skapa**.

   - **Databaser i gruppen**: Välj den databas som du vill lägga till i redundansgruppen. Om du lägger till databasen i redundansgruppen startas georeplikeringsprocessen automatiskt. 
        
    ![Lägga till SQL DB i redundansgruppen](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Skapa redundansgruppen och lägg till din enda databas med PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Redundanstest 

Testa redundans för redundansgruppen med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för redundansgruppen med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den enda databas som du vill lägga till i redundansgruppen. 

   ![Öppna server för en enda db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan den redundansgrupp som du just skapade. 
  
   ![Välj redundansgruppen från portalen](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **Redundans** i åtgärdsfönstret om du vill växla över redundansgruppen som innehåller en enda databas. 
1. Välj **Ja** på varningen som meddelar dig att TDS-sessioner kommer att kopplas från. 

   ![Växla över redundansgruppen som innehåller SQL-databasen](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Granska vilken server som nu är primär och vilken server som är sekundär. Om redundansen lyckades bör de två servrarna ha bytt roller. 
1. Välj **Redundans** igen om du vill att servrarna inte ska kunna återgå till sina ursprungliga roller. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testa redundans för redundansgruppen med PowerShell.  

Kontrollera rollen för den sekundära repliken: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Växla över till den sekundära servern: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Återgår till redundansgruppen till den primära servern:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Om du behöver ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundansgruppen kan det orsaka oförutsägbart beteende. 

## <a name="elastic-pool"></a>Elastisk pool
Skapa redundansgruppen och lägg till en elastisk pool i den med Azure-portalen eller PowerShell.  

### <a name="prerequisites"></a>Krav

Tänk på följande förutsättningar:

- Serverinloggnings- och brandväggsinställningarna för den sekundära servern måste matcha den primära serverns. 

### <a name="create-the-failover-group"></a>Skapa redundansgruppen 

Skapa redundansgruppen för din elastiska pool med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Skapa din redundansgrupp och lägg till din elastiska pool i den med Azure-portalen.

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den elastiska pool som du vill lägga till i redundansgruppen. 
1. Öppna inställningarna för servern i fönstret **Server name** **Översikt.**
  
    ![Öppen server för elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan Lägg **till grupp** för att skapa en ny redundanskitetsgrupp. 

    ![Lägga till ny redundansgrupp](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. På sidan **Redundansk gruppera** du eller markera de värden som krävs och välj sedan **Skapa**. Skapa en ny sekundär server eller välj en befintlig sekundär server. 

1. Välj **Databaser i gruppen** och välj sedan den elastiska pool som du vill lägga till i redundansgruppen. Om det inte redan finns en elastisk pool på den sekundära servern visas en varning där du uppmanas att skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK** för att skapa den elastiska poolen på den sekundära servern. 
        
    ![Lägga till elastisk pool i redundansgruppen](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Välj **Välj om** du vill använda inställningarna för den elastiska poolen på redundansgruppen och välj sedan **Skapa** för att skapa redundansgruppen. Om du lägger till den elastiska poolen i redundansgruppen startas georeplikeringsprocessen automatiskt. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa redundansgruppen och lägg till den elastiska poolen med PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Redundanstest

Testa redundans för din elastiska pool med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Redundansgruppen redundans till den sekundära servern och växla sedan tillbaka med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den elastiska pool som du vill lägga till i redundansgruppen. 
1. Öppna inställningarna för servern i fönstret **Server name** **Översikt.**
  
    ![Öppen server för elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan den redundansgrupp som du skapade i avsnitt 2. 
  
   ![Välj redundansgruppen från portalen](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **Redundans** i åtgärdsfönstret om du vill växla över redundansgruppen som innehåller den elastiska poolen. 
1. Välj **Ja** på varningen som meddelar dig att TDS-sessioner kommer att kopplas från. 

   ![Växla över redundansgruppen som innehåller SQL-databasen](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansen lyckades bör de två servrarna ha bytt roller. 
1. Välj **Redundans** igen om du vill återställa redundansgruppen till de ursprungliga inställningarna igen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testa redundans för redundansgruppen med PowerShell.

Kontrollera rollen för den sekundära repliken: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Växla över till den sekundära servern: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Om du behöver ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundansgruppen kan det orsaka oförutsägbart beteende. 

## <a name="managed-instance"></a>Hanterad instans

Skapa en redundansk grupp mellan två hanterade instanser med Azure-portalen eller PowerShell. 

Du måste antingen konfigurera [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) eller skapa en gateway för det virtuella nätverket för varje hanterad instans, ansluta de två gatewayerna och sedan skapa redundansgruppen. 

### <a name="prerequisites"></a>Krav
Tänk på följande förutsättningar:

- Den sekundära hanterade instansen måste vara tom.
- Undernätsområdet för det sekundära virtuella nätverket får inte överlappa undernätsområdet för det primära virtuella nätverket. 
- Sortering och tidszon för den sekundära instansen måste matcha den primära instansen. 
- När du ansluter de två gatewayerna bör den **delade nyckeln** vara densamma för båda anslutningarna. 

### <a name="create-primary-virtual-network-gateway"></a>Skapa primär virtuell nätverksgateway 

Om du inte har konfigurerat [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)kan du skapa den primära virtuella nätverksgatewayen med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa den primära virtuella nätverksgatewayen med Azure-portalen. 

1. Gå till resursgruppen i [Azure-portalen](https://portal.azure.com)och välj den **virtuella nätverksresursen** för din primära hanterade instans. 
1. Välj **Undernät** under **Inställningar** och välj sedan om du vill lägga till ett nytt **gateway-undernät**. Lämna standardvärdena. 

   ![Lägga till gateway för primär hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. När nätgatewayen har skapats väljer du **Skapa en** `Virtual network gateway` resurs från det vänstra navigeringsfönstret och skriver sedan i sökrutan. Välj den **virtuella nätverksgatewayresursen** som publiceras av **Microsoft**. 

   ![Skapa en ny virtuell nätverksgateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten för att konfigurera gatewayen din primära hanterade instans. 

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:
 
    | **Field** | Värde |
    | --- | --- |
    | **Prenumeration** |  Prenumerationen där din primära hanterade instans finns. |
    | **Namn** | Namnet på den virtuella nätverksgatewayen. | 
    | **Regionen** | Den region där din sekundära hanterade instans finns. |
    | **Gateway-typ** | Välj **VPN**. |
    | **VPN-typ** | Välj **Ruttbaserad** |
    | **Sku**| Lämna standardvärdet `VpnGw1`för . |
    | **Location**| Den plats där din sekundära hanterade instans och sekundära virtuella nätverk finns.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverket för den sekundära hanterade instansen. |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn på din IP-adress. |
    | &nbsp; | &nbsp; |

1. Lämna de andra värdena som standard och välj sedan **Granska + skapa** för att granska inställningarna för den virtuella nätverksgatewayen.

   ![Primära gatewayinställningar](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Välj **Skapa** om du vill skapa den nya virtuella nätverksgatewayen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa den primära virtuella nätverksgatewayen med PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Skapa sekundär virtuell nätverksgateway

Skapa den sekundära virtuella nätverksgatewayen med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Upprepa stegen i föregående avsnitt för att skapa det virtuella nätverksundernätet och gatewayen för den sekundära hanterade instansen. Fyll i de obligatoriska fälten för att konfigurera gatewayen för den sekundära hanterade instansen. 

   I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Field** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där din sekundära hanterade instans finns. |
   | **Namn** | Namnet på den virtuella nätverksgatewayen, till exempel `secondary-mi-gateway`. | 
   | **Regionen** | Den region där din sekundära hanterade instans finns. |
   | **Gateway-typ** | Välj **VPN**. |
   | **VPN-typ** | Välj **Ruttbaserad** |
   | **Sku**| Lämna standardvärdet `VpnGw1`för . |
   | **Location**| Den plats där din sekundära hanterade instans och sekundära virtuella nätverk finns.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i `vnet-sql-mi-secondary`avsnitt 2, till exempel . |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn på din `secondary-gateway-IP`IP-adress, till exempel . |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa den sekundära virtuella nätverksgatewayen med PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Anslut gateways 
Skapa anslutningar mellan de två gatewayerna med Azure-portalen eller PowerShell. 

Två anslutningar måste skapas - anslutningen från den primära gatewayen till den sekundära gatewayen och sedan anslutningen från den sekundära gatewayen till den primära gatewayen. 

Den delade nyckeln som används för båda anslutningarna ska vara densamma för varje anslutning. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Skapa anslutningar mellan de två gatewayerna med Azure-portalen. 

1. Välj **Skapa en resurs** från [Azure-portalen](https://portal.azure.com).
1. Skriv `connection` i sökrutan och tryck sedan på retur för att söka, vilket tar dig till **anslutningsresursen,** som publiceras av Microsoft.
1. Välj **Skapa** för att skapa anslutningen. 
1. På fliken **Grunderna** markerar du följande värden och väljer sedan **OK**. 
    1. Välj `VNet-to-VNet` för **typen Anslutning**. 
    1. Välj din prenumeration från listrutan. 
    1. Välj resursgruppen för den hanterade instansen i listrutan. 
    1. Välj platsen för din primära hanterade instans i listrutan 
1. Markera eller ange följande värden på fliken **Inställningar** och välj sedan **OK:**
    1. Välj den primära nätverksgatewayen för den `Primary-Gateway`första virtuella **nätverksgatewayen,** till exempel .  
    1. Välj den sekundära nätverksgatewayen för den `Secondary-Gateway`andra virtuella **nätverksgatewayen,** till exempel . 
    1. Markera kryssrutan bredvid **Upprätta dubbelriktad anslutning**. 
    1. Lämna antingen standardnamnet för den primära anslutningen eller byt namn på det till ett värde som du väljer. 
    1. Ange en **delad nyckel (PSK)** för `mi1m2psk`anslutningen, till exempel . 

   ![Skapa gatewayanslutning](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. På fliken **Sammanfattning** granskar du inställningarna för dubbelriktad anslutning och väljer sedan **OK** för att skapa anslutningen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa anslutningar mellan de två gatewayerna med PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Skapa redundansgruppen 
Skapa redundansgruppen för dina hanterade instanser med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa redundansgruppen för dina hanterade instanser med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den primära hanterade instans som du vill lägga till i redundansgruppen.  
1. Under **Inställningar**navigerar du till **Instansundanslagsgrupper** och väljer sedan att **lägga till grupp** för att öppna sidan **Instansundanslagsgrupp.** 

   ![Lägga till en redundansgrupp](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. På sidan **Instansundanslagsgrupp** skriver du namnet på redundansgruppen och väljer sedan den sekundära hanterade instansen i listrutan. Välj **Skapa** om du vill skapa redundansgruppen. 

   ![Skapa redundansgrupp](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. När redundansgruppsdistributionen är klar kommer du tillbaka till **redundansgruppssidan.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa redundansgruppen för hanterade instanser med PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Redundanstest

Testa redundans för redundansgruppen med Azure-portalen eller PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för redundansgruppen med Azure-portalen. 

1. Navigera till din _sekundära_ hanterade instans i [Azure-portalen](https://portal.azure.com) och välj **Instans redundansgrupper** under inställningar. 
1. Granska vilken hanterad instans som är den primära och vilken hanterad instans som är den sekundära. 
1. Välj **Redundans** och välj sedan **Ja** på varningen om att TDS-sessioner kopplas från. 

   ![Växla över redundansgruppen](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Granska vilken manged instans är den primära och vilken instans som är den sekundära. Om redundansen lyckades bör de två instanserna ha bytt roller. 

   ![Hanterade instanser har bytt roller efter redundans](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Gå till den nya _sekundära_ hanterade instansen och välj **Redundans** igen för att misslyckas med den primära instansen tillbaka till den primära rollen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testa redundans för redundansgruppen med PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Hitta slutpunkten för lyssnaren

När redundansgruppen har konfigurerats uppdaterar du anslutningssträngen för ditt program till lyssnarens slutpunkt. Detta håller ditt program anslutet till redundansgruppens lyssnare, i stället för den primära databasen, den elastiska poolen eller hanterade instansen. På så sätt behöver du inte uppdatera anslutningssträngen manuellt varje gång din Azure SQL-databasentitet växlar över och trafiken dirigeras till den entitet som för närvarande är primär. 

Slutpunkten för lyssnaren är `fog-name.database.windows.net`i form av , och visas i Azure-portalen när du visar redundansgruppen:

![Anslutningssträng för redundansgrupp](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Anmärkningar

- Om du tar bort en redundansgrupp för en enskild eller poolad databas stoppas inte replikeringen och den replikerade databasen tas inte bort. Du måste manuellt stoppa georeplikering och ta bort databasen från den sekundära servern om du vill lägga till en enskild eller poolad databas tillbaka till en redundanskationsgrupp när den har tagits bort. Om du inte gör något av `The operation cannot be performed due to multiple errors` något kan det resultera i ett fel som liknar när du försöker lägga till databasen i redundansgruppen. 


## <a name="next-steps"></a>Nästa steg

Detaljerade steg med att konfigurera en redundansgrupp finns i följande självstudier:
- [Lägga till en enskild databas i en redundansgrupp](sql-database-single-database-failover-group-tutorial.md)
- [Lägga till en elastisk pool till en redundansgrupp](sql-database-elastic-pool-failover-group-tutorial.md)
- [Lägga till hanterade instanser i en redundansgrupp](sql-database-managed-instance-failover-group-tutorial.md)
 
En översikt över Azure SQL Database med hög tillgänglighet finns i [geo-replikering](sql-database-active-geo-replication.md) och [automatisk redundansgrupper](sql-database-auto-failover-group.md). 
