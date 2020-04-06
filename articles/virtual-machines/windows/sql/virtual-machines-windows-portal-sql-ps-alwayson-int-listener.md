---
title: Konfigurera tillgänglighetsgrupplyssnare & belastningsutjämnare (PowerShell)
description: Konfigurera tillgänglighetsgruppavlyssnare på Azure Resource Manager-modellen med hjälp av en intern belastningsutjämnare med en eller flera IP-adresser.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: cabfc84d2bc0c9d08a457e67c0182d7550f04ceb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668894"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurera en eller flera gruppavlyssnare alltid på tillgänglighet - Resource Manager
I det här avsnittet visas hur du:

* Skapa en intern belastningsutjämnare för SQL Server-tillgänglighetsgrupper med PowerShell-cmdlets.
* Lägg till ytterligare IP-adresser i en belastningsutjämnare för mer än en tillgänglighetsgrupp. 

En lyssnare i tillgänglighetsgruppen är ett virtuellt nätverksnamn som klienter ansluter till för databasåtkomst. På virtuella Azure-datorer innehåller en belastningsutjämnare IP-adressen för lyssnaren. Belastningsutjämnaren dirigerar trafik till instansen av SQL Server som lyssnar på avsökningsporten. Vanligtvis använder en tillgänglighetsgrupp en intern belastningsutjämnare. En intern Azure-belastningsutjämnare kan vara värd för en eller flera IP-adresser. Varje IP-adress använder en specifik avsökningsport. Det här dokumentet visar hur du använder PowerShell för att skapa en belastningsutjämnare eller lägga till IP-adresser i en befintlig belastningsutjämnare för SQL Server-tillgänglighetsgrupper. 

Möjligheten att tilldela flera IP-adresser till en intern belastningsutjämnare är ny för Azure och är endast tillgänglig i Resource Manager-modellen. För att slutföra den här uppgiften måste du ha en SQL Server-tillgänglighetsgrupp distribuerad på virtuella Azure-datorer i Resource Manager-modellen. Båda virtuella SQL Server-datorerna måste tillhöra samma tillgänglighetsuppsättning. Du kan använda [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för att automatiskt skapa tillgänglighetsgruppen i Azure Resource Manager. Den här mallen skapar automatiskt tillgänglighetsgruppen, inklusive den interna belastningsutjämnaren åt dig. Om du vill kan du [konfigurera en alltid på tillgänglighetsgrupp manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Det här avsnittet kräver att dina tillgänglighetsgrupper redan är konfigurerade.  

Relaterade ämnen inkluderar:

* [Konfigurera AlwaysOn-tillgänglighetsgrupper i Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verifiera PowerShell-version

Exemplen i den här artikeln testas med Azure PowerShell-modul version 5.4.1.

Kontrollera att PowerShell-modulen är 5.4.1 eller senare.

Se [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurera Windows-brandväggen

Konfigurera Windows-brandväggen så att SQL Server-åtkomst tillåts. Brandväggsreglerna tillåter TCP-anslutningar till portarna som används av SQL Server-instansen och lyssnaren avsökningen. Detaljerade instruktioner finns i [Konfigurera en Windows-brandvägg för databasmotoråtkomst](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Skapa en inkommande regel för SQL Server-porten och för avsökningsporten.

Om du begränsar åtkomsten med en Azure Network Security Group kontrollerar du att tillåt-reglerna inkluderar SQL Server-IP-adresserna för serverdelen och den flytande IP-adresserna för belastningsutjämnaden för AG-lyssnaren och klustrets kärn-IP-adress, om tillämpligt.

## <a name="determine-the-load-balancer-sku-required"></a>Bestäm den belastningsutjämnare SKU som krävs

[Azure load balancer](../../../load-balancer/load-balancer-overview.md) är tillgänglig i 2 SKU: Basic & Standard. Standardbelastningsutjämnaren rekommenderas. Om de virtuella datorerna är i en tillgänglighetsuppsättning tillåts grundläggande belastningsutjämnare. Om de virtuella datorerna finns i en tillgänglighetszon krävs en standardbelastningsutjämnare. Standardbelastningsutjämnare kräver att alla VM-IP-adresser använder vanliga IP-adresser.

Den aktuella [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för en tillgänglighetsgrupp använder en grundläggande belastningsutjämnare med grundläggande IP-adresser.

   > [!NOTE]
   > Du måste konfigurera en [tjänstslutpunkt](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) om du använder en standardbelastningsutjämnare och Azure Storage för molnvittnet. 


Exemplen i den här artikeln anger en standardbelastningsutjämnare. I exemplen innehåller `-sku Standard`skriptet .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Om du vill skapa en `-sku Standard` grundläggande belastningsutjämnare tar du bort från raden som skapar belastningsutjämnaren. Ett exempel:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exempelskript: Skapa en intern belastningsutjämnare med PowerShell

> [!NOTE]
> Om du skapade din tillgänglighetsgrupp med [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)har den interna belastningsutjämnaren redan skapats.

Följande PowerShell-skript skapar en intern belastningsutjämnare, konfigurerar belastningsutjämningsreglerna och anger en IP-adress för belastningsutjämnaren. Om du vill köra skriptet öppnar du Windows PowerShell ISE och klistrar in skriptet i skriptfönstret. Används `Connect-AzAccount` för att logga in på PowerShell. Om du har flera Azure-prenumerationer använder du `Select-AzSubscription` för att ange prenumerationen. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Exempelskript: Lägga till en IP-adress i en befintlig belastningsutjämnare med PowerShell
Om du vill använda mer än en tillgänglighetsgrupp lägger du till ytterligare en IP-adress i belastningsutjämnaren. Varje IP-adress kräver en egen belastningsutjämningsregel, avsökningsport och främre port.

Frontend-porten är den port som program använder för att ansluta till SQL Server-instansen. IP-adresser för olika tillgänglighetsgrupper kan använda samma frontend-port.

> [!NOTE]
> För SQL Server-tillgänglighetsgrupper kräver varje IP-adress en specifik avsökningsport. Om till exempel en IP-adress på en belastningsutjämnare använder avsökningsport 59999, kan inga andra IP-adresser på den belastningsutjämnaren använda avsökningsport 59999.

* Information om belastningsutjämnaregränser finns i **Privat klient-IP per belastningsutjämnare** under [Nätverksgränser - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Information om begränsningar för tillgänglighetsgrupper finns i [Begränsningar (tillgänglighetsgrupper)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Följande skript lägger till en ny IP-adress i en befintlig belastningsutjämnare. ILB använder lyssnarporten för frontend-porten för belastningsutjämning. Den här porten kan vara den port som SQL Server lyssnar på. För standardinstanser av SQL Server är porten 1433. Belastningsutjämningsregeln för en tillgänglighetsgrupp kräver en flytande IP (direkt serverretur) så att backend-porten är densamma som frontend-porten. Uppdatera variablerna för din miljö. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurera lyssnaren

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ange lyssnarporten i SQL Server Management Studio

1. Starta SQL Server Management Studio och anslut till den primära repliken.

1. Navigera till **AlwaysOn-tillgänglighetsgrupper** | **Availability Groups** | med hög**tillgänglighet.** 

1. Du bör nu se lyssnarnamnet som du skapade i Redundansklusterhanteraren. Högerklicka på lyssnarens namn och klicka på **Egenskaper**.

1. I rutan **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard) och klickar sedan på **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren

Så här testar du anslutningen:

1. RDP till en SQL Server som finns i samma virtuella nätverk, men som inte äger repliken. Detta kan vara den andra SQL Server i klustret.

1. Använd **sqlcmd-verktyget** för att testa anslutningen. Följande skript upprättar till exempel en **sqlcmd-anslutning** till den primära repliken via lyssnaren med Windows-autentisering:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Om lyssnaren använder en annan port än standardporten (1433) anger du porten i anslutningssträngen. Följande sqlcmd-kommando ansluter till en lyssnare vid port 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD-anslutningen ansluter automatiskt till den instans av SQL Server som är värd för den primära repliken. 

> [!NOTE]
> Kontrollera att porten du anger är öppen i brandväggen för båda SQL-servrarna. Båda servrarna kräver en inkommande regel för den TCP-port som du använder. Mer information finns i [Lägga till eller redigera brandväggsregel.](https://technet.microsoft.com/library/cc753558.aspx) 
> 
> 

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar
Observera följande riktlinjer för tillgänglighet grupp avlyssnare i Azure med hjälp av intern belastningsutjämnare:

* Med en intern belastningsutjämnare kommer du bara åt lyssnaren från samma virtuella nätverk.

* Om du begränsar åtkomsten med en Azure Network Security Group kontrollerar du att tillåt-reglerna inkluderar SQL Server-IP-adresserna för serverdelen och den flytande IP-adresserna för belastningsutjämnaden för AG-lyssnaren och klustrets kärn-IP-adress, om tillämpligt.

* Skapa en tjänstslutpunkt när du använder en standardbelastningsutjämnare med Azure Storage för molnvittnet. Mer information finns i [Bevilja åtkomst från ett virtuellt nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Mer information
Mer information finns i [Konfigurera alltid på tillgänglighetsgrupp i Azure VM manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
Använd följande PowerShell-cmdlets för att skapa en intern belastningsutjämnare för virtuella Azure-datorer.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) skapar en belastningsutjämnare. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) skapar en front-end IP-konfiguration för en belastningsutjämnare. 
* [New-AzLoadBalancerRUleConfig](https://msdn.microsoft.com/library/mt619391.aspx) skapar en regelkonfiguration för en belastningsutjämnare. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) skapar en serverdelsadresspoolkonfiguration för en belastningsutjämnare. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) skapar en avsökningskonfiguration för en belastningsutjämnare.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) tar bort en belastningsutjämnare från en Azure-resursgrupp.
