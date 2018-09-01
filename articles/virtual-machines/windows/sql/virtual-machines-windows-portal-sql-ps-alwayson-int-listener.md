---
title: Konfigurera Always On Availability-Grupplyssnare – Microsoft Azure | Microsoft Docs
description: Konfigurera Availability-Grupplyssnare på Azure Resource Manager-modellen, med en intern belastningsutjämnare med en eller flera IP-adresser.
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
ms.openlocfilehash: b4641c847db817df905f056847a26d003ac25fd1
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381803"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurera en eller flera Always On availability-grupplyssnare - Resource Manager
Det här avsnittet visar hur du:

* Skapa en intern belastningsutjämnare för SQL Server-Tillgänglighetsgrupper med PowerShell-cmdletar.
* Lägga till ytterligare IP-adresser till en belastningsutjämnare för flera tillgänglighetsgruppen. 

En tillgänglighetsgruppslyssnare är ett namn för virtuellt nätverk som klienterna ansluter till för databasåtkomst. På Azure-datorer, en belastningsutjämnare som innehåller IP-adressen för lyssnaren. Belastningsutjämnaren dirigerar trafik till instansen av SQL Server som lyssnar på avsökningsporten. Vanligtvis en tillgänglighetsgrupp som använder en intern belastningsutjämnare. En Azure intern belastningsutjämnare kan vara värd för en eller flera IP-adresser. Varje IP-adress använder en specifik avsökningsporten. Det här dokumentet visar hur du använder PowerShell för att skapa en belastningsutjämnare eller Lägg till IP-adresser i en befintlig belastningsutjämnare för SQL Server-Tillgänglighetsgrupper. 

Möjligheten att tilldela flera IP-adresser till en intern belastningsutjämnare är du nybörjare på Azure och är endast tillgängliga i Resource Manager-modellen. Du måste ha en SQL Server-tillgänglighetsgrupp som distribueras på Azure virtuella datorer i Resource Manager-modellen för att slutföra den här uppgiften. Både SQL Server-datorer måste tillhöra samma tillgänglighetsuppsättning. Du kan använda den [Microsoft-mallen för](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) att automatiskt skapa availability-gruppen i Azure Resource Manager. Den här mallen skapar automatiskt tillgänglighetsgruppen, inklusive den interna belastningsutjämnaren för dig. Om du föredrar, kan du [manuellt konfigurera en Always On-tillgänglighetsgrupp](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Det här avsnittet kräver att din Tillgänglighetsgrupper är redan konfigurerade.  

Relaterade ämnen innefattar:

* [Konfigurera AlwaysOn-Tillgänglighetsgrupper på Azure virtuella datorer (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurera Windows-brandväggen
Konfigurera Windows-brandväggen så att SQL Server-åtkomst. Brandväggsreglerna tillåta TCP-anslutningar till portar används av SQL Server-instansen och lyssnare-avsökning. Detaljerade anvisningar finns i [konfigurerar en Windows-brandvägg för Databasmotoråtkomst](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Skapa en regel för inkommande för SQL Server-porten och avsökningsporten.

Om du är att begränsa åtkomst med en Azure Network Security Group, se till att Tillåt-reglerna omfattar serverdel SQL Server VM IP-adresser och belastningsutjämnare flytande IP-adresser för AG-lyssnare och klustrets core IP-adress om det är tillämpligt.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exempel på skript: Skapa en intern belastningsutjämnare med PowerShell
> [!NOTE]
> Om du har skapat tillgänglighetsgruppen med den [Microsoft-mallen för](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), den interna belastningsutjämnaren har redan skapats. 
> 
> 

Följande PowerShell-skript skapar en intern belastningsutjämnare, konfigurerar regler för belastningsutjämning och anger en IP-adress för belastningsutjämnaren. Öppna Windows PowerShell ISE för att köra skriptet, och klistra in skriptet i skriptfönstret. Använd `Connect-AzureRmAccount` att logga in på PowerShell. Om du har flera Azure-prenumerationer kan du använda `Select-AzureRmSubscription ` att ställa in prenumerationen. 

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

## <a name="Add-IP"></a> Exempel på skript: lägga till en IP-adress till en befintlig belastningsutjämnare med PowerShell
För att använda fler än en tillgänglighetsgrupp måste du lägga till ytterligare IP-adress till belastningsutjämnaren. Varje IP-adress kräver sin egen belastningsutjämningsregel, avsökningsporten, och front port.

Frontend-porten är den port som program använda för att ansluta till SQL Server-instansen. IP-adresser för annan Tillgänglighetsgrupper kan använda samma frontend-porten.

> [!NOTE]
> För SQL Server-Tillgänglighetsgrupper kräver varje IP-adress en specifik avsökningsporten. Om en IP-adress för en belastningsutjämnare använder avsökningsporten 59999, kan inga andra IP-adresser på den belastningsutjämnaren använda avsökningsporten 59999.

* Information om load balancer begränsningar finns i **privata IP klient per belastningsutjämnare** under [nätverksgränser – Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Läs om hur tillgänglighet gruppgränser [begränsningar (Tillgänglighetsgrupper)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. Den interna Belastningsutjämnaren använder porten för lyssnare för frontend-porten för belastningsutjämning. Den här porten kan vara den port som SQL Server lyssnar på. För standardinstanser av SQL Server är porten 1433. Belastningsutjämningsregeln för en tillgänglighetsgrupp kräver en flytande IP (direkt serverreturnering) så backend-porten är samma som frontend-porten. Uppdatera variablerna för din miljö. 

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

1. Du bör nu se namnet på lyssnare som du skapade i hanteraren för redundanskluster. Högerklicka på namnet på lyssnare och på **egenskaper**.

1. I den **Port** skriver du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard), klicka sedan på **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren

Så här testar du anslutningen:

1. RDP till en SQL-Server som är i samma virtuella nätverk, men inte äger repliken. Detta kan vara SQL Server i klustret.

1. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Om lyssnaren använder en annan port än standardvärdet standardporten (1433), ange porten i anslutningssträngen. Till exempel ansluter kommandot sqlcmd till en lyssnare på port 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD-anslutning ansluter automatiskt till instansen av SQL Server är värd för den primära repliken. 

> [!NOTE]
> Se till att den port som du anger är öppen i brandväggen för både SQL-servrar. Båda servrarna kräver en regel för inkommande trafik för TCP-porten som du använder. Se [Lägg till eller redigera brandväggsregel](http://technet.microsoft.com/library/cc753558.aspx) för mer information. 
> 
> 

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar
Observera följande riktlinjer på tillgänglighetsgruppens lyssnare i Azure med hjälp av interna belastningsutjämnare:

* Med en intern belastningsutjämnare du bara åtkomst till lyssnaren från inom samma virtuella nätverk.

* Om du är att begränsa åtkomst med en Azure Network Security Group, se till att Tillåt-reglerna omfattar serverdel SQL Server VM IP-adresser och belastningsutjämnare flytande IP-adresser för AG-lyssnare och klustrets core IP-adress om det är tillämpligt.

## <a name="for-more-information"></a>Mer information
Mer information finns i [konfigurera Always On-tillgänglighetsgruppen i Azure VM manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
Använd följande PowerShell-cmdletar för att skapa en intern belastningsutjämnare för Azure-datorer.

* [Ny-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) skapar en belastningsutjämnare. 
* [Ny AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) skapar en frontend IP-konfiguration för en belastningsutjämnare. 
* [Ny-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) skapar en Regelkonfiguration för en belastningsutjämnare. 
* [Ny AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) skapar en backend-pool-adresskonfiguration för en belastningsutjämnare. 
* [Ny-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) skapar en avsökningskonfiguration för en belastningsutjämnare.
* [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) tar bort en belastningsutjämnare från en Azure-resursgrupp.
