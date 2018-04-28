---
title: Konfigurera alltid på tillgänglighetsgruppens lyssnare – Microsoft Azure | Microsoft Docs
description: Konfigurera tillgänglighet Tillgänglighetsgruppslyssnarnas på Azure Resource Manager-modellen med hjälp av en intern belastningsutjämnare med en eller flera IP-adresser.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 11aecd9b2bc1bc1521a0e27fc3cd06fe7426a26d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurera en eller flera alltid på tillgänglighet tillgänglighetsgruppslyssnarnas - Resource Manager
Det här avsnittet beskrivs hur du:

* Skapa en intern belastningsutjämnare för SQL Server-Tillgänglighetsgrupper med PowerShell-cmdlets.
* Lägg till ytterligare IP-adresser till en belastningsutjämnare för mer än en tillgänglighetsgrupp. 

En tillgänglighetsgruppslyssnare är ett namn på virtuella nätverk som klienterna ansluter till för åtkomst till databasen. På Azure virtual machines innehåller en belastningsutjämnare IP-adressen för lyssnaren. Belastningsutjämnaren dirigerar trafik till instansen av SQL Server som lyssnar på porten för avsökningen. En tillgänglighetsgrupp använder vanligtvis en intern belastningsutjämnare. En Azure intern belastningsutjämnare kan vara värd för en eller flera IP-adresser. Varje IP-adress använder en specifik avsökningsport. Det här dokumentet beskrivs hur du använder PowerShell för att skapa en belastningsutjämnare eller Lägg till IP-adresser i en befintlig belastningsutjämnare för SQL Server-Tillgänglighetsgrupper. 

Möjligheten att tilldela flera IP-adresser till en intern belastningsutjämnare är ny till Azure och är endast tillgängligt i Resource Manager-modellen. Du måste ha en SQL Server-tillgänglighetsgrupp som distribueras på virtuella Azure-datorer i Resource Manager-modellen för att slutföra den här uppgiften. Både SQL Server-datorer måste tillhöra samma tillgänglighetsuppsättning. Du kan använda den [Microsoft mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) att automatiskt skapa tillgänglighetsgruppen i Azure Resource Manager. Den här mallen skapar automatiskt tillgänglighetsgruppen, inklusive den interna belastningsutjämnaren för dig. Om du vill kan du [manuellt konfigurera en Always On-tillgänglighetsgrupp](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Det här avsnittet kräver att din Tillgänglighetsgrupper har redan konfigurerats.  

Närliggande ämnen innefattar:

* [Konfigurera AlwaysOn-Tillgänglighetsgrupper på Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurera Windows-brandväggen
Konfigurera Windows-brandväggen för att tillåta åtkomst för SQL Server. Brandväggsreglerna tillåter TCP-anslutningar till portar används av SQL Server-instansen och lyssnare-avsökning. Detaljerade instruktioner finns [konfigurera Windows-brandväggen för Databasmotoråtkomst](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Skapa en regel för inkommande trafik för SQL Server-porten och porten för avsökningen.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exempelskript: Skapa en intern belastningsutjämnare med PowerShell
> [!NOTE]
> Om du har skapat tillgänglighetsgruppen med den [Microsoft mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), den interna belastningsutjämnaren har redan skapats. 
> 
> 

Följande PowerShell-skript skapar en intern belastningsutjämnare, konfigurerar regler för belastningsutjämning och anger en IP-adress för belastningsutjämnaren. Kör skriptet, öppna Windows PowerShell ISE och klistra in skriptet i skriptfönstret. Använd `Connect-AzureRmAccount` att logga in i PowerShell. Om du har flera Azure-prenumerationer, Använd `Select-AzureRmSubscription ` att ställa in prenumerationen. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

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

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Exempelskript: lägga till en IP-adress i en befintlig belastningsutjämnare med PowerShell
Lägg till ytterligare en IP-adress till belastningsutjämnaren om du vill använda mer än en tillgänglighetsgrupp. Varje IP-adress kräver sin egen belastningsutjämning regeln, avsökningsport och främre port.

Frontend porten är den port som används av program för att ansluta till SQL Server-instansen. IP-adresser för olika Tillgänglighetsgrupper kan använda samma frontend-port.

> [!NOTE]
> För SQL Server-Tillgänglighetsgrupper kräver varje IP-adress en viss avsökningsport. Om en IP-adress för en belastningsutjämnare använder avsökningsport 59999, kan inga andra IP-adresser på den belastningsutjämnaren använda avsökningsport 59999.

* Information om belastningen belastningsutjämnaren gränser finns **klientdelen privata IP-adress per belastningsutjämnaren** under [nätverk gränser - Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Information om tillgänglighet grupp gränser finns [begränsningar (Tillgänglighetsgrupper)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. ILB använder porten för lyssnare för frontend-port för belastningsutjämning. Den här porten kan vara porten som SQL-servern lyssnar på. För standardinstanser av SQL Server-porten är 1433. Regel för en tillgänglighetsgrupp för belastningsutjämning kräver en flytande IP (direkt serverreturnering) så backend-porten är samma som frontend-porten. Uppdatera variablerna för din miljö. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurera lyssnaren

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ange porten för lyssnare i SQL Server Management Studio

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

1. Gå till **AlwaysOn hög tillgänglighet** | **Tillgänglighetsgrupper** | **tillgänglighetsgruppens lyssnare**. 

1. Du bör nu se lyssnare namnet som du skapade i hanteraren för redundanskluster. Högerklicka på grupplyssnarens namn och på **egenskaper**.

1. I den **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard), klicka på **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren

Att testa anslutningen:

1. RDP till en SQL-Server som är i samma virtuella nätverk, men inte äger repliken. Detta kan vara SQL Server i klustret.

1. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Om lyssnaren använder en annan port än standard port (1433), ange porten i anslutningssträngen. Till exempel ansluter kommandot sqlcmd till en lyssnare på port 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD anslutning ansluter automatiskt till instansen av SQL Server är värd för den primära repliken. 

> [!NOTE]
> Kontrollera att den port som du anger är öppen i brandväggen för både SQL-servrar. Båda servrarna kräver en inkommande regel för TCP-porten som du använder. Se [Lägg till eller redigera brandväggsregel](http://technet.microsoft.com/library/cc753558.aspx) för mer information. 
> 
> 

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar
Observera följande riktlinjer för tillgänglighetsgruppens lyssnare i Azure med hjälp av interna belastningsutjämnare:

* Med en intern belastningsutjämnare du bara komma åt lyssnaren från inom samma virtuella nätverk.


## <a name="for-more-information"></a>Mer information
Mer information finns i [konfigurera alltid på tillgänglighetsgruppen i Azure VM manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
Använd följande PowerShell-cmdlets för att skapa en intern belastningsutjämnare för virtuella Azure-datorer.

* [Nya AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) skapar en belastningsutjämnare. 
* [Nya AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) skapar en frontend IP-konfiguration för en belastningsutjämnare. 
* [Nya AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) skapar en Regelkonfiguration för en belastningsutjämnare. 
* [Nya AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) skapar en backend-pool-adresskonfiguration för en belastningsutjämnare. 
* [Nya AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) skapar en avsökning konfiguration för en belastningsutjämnare.
* [Ta bort AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) tar bort en belastningsutjämnare från en Azure-resursgrupp.
