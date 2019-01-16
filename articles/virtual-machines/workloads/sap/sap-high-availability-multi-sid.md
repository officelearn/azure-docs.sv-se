---
title: Skapa en SAP – flera SÄKERHETSIDENTIFIERARE konfiguration i Azure | Microsoft Docs
description: Guide till – flera SÄKERHETSIDENTIFIERARE konfiguration med hög tillgänglighet SAP NetWeaver på Windows virtuella datorer
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05fda1b4f71f67714b4723e15533d3555d497249
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321678"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Skapa en konfiguration för SAP NetWeaver multi-SID

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

I September 2016 släppte Microsoft en funktion där du kan hantera flera virtuella IP-adresser med hjälp av en Azure intern belastningsutjämnare. Den här funktionen finns redan i den externa belastningsutjämnaren som Azure.

Om du har en SAP-distribution kan du kan använda en intern belastningsutjämnare för att skapa en Windows-klusterkonfiguration för SAP ASCS/SCS, enligt beskrivningen i den [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer] [ sap-ha-guide].

Den här artikeln handlar om hur du flyttar från en enda ASCS/SCS-installation till en SAP – flera SÄKERHETSIDENTIFIERARE konfiguration genom att installera ytterligare SAP ASCS/SCS klustrade instanser i ett befintligt Windows Server Failover Clustering WSFC-kluster. När processen är slutförd, har du konfigurerat ett SAP – flera SÄKERHETSIDENTIFIERARE kluster.


## <a name="prerequisites"></a>Förutsättningar
Du redan har konfigurerat ett WSFC-kluster som används för en SAP ASCS/SCS-instans som beskrivs i den [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer] [ sap-ha-guide] och som visas i det här diagrammet.

![Hög tillgänglighet SAP ASCS/SCS-instans][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Mål-arkitektur

Målet är att installera flera SAP ABAP ASCS eller SAP Java SCS grupperade instanser i samma WSFC-klustret, som här:

![Flera SAP ASCS/SCS klustrade instanser i Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Det finns en gräns för antalet privata frontend IP-adresser för varje Azure intern belastningsutjämnare.
>
>Det maximala antalet SAP ASCS/SCS-instanser i ett WSFC-klustret är lika med det maximala antalet privata frontend IP-adresser för varje Azure intern belastningsutjämnare.
>

Mer information om belastningsutjämnare begränsningar finns i ”privat IP klient per belastningsutjämnare” i [begränsningar för nätverk: Azure Resource Manager][networking-limits-azure-resource-manager].

Fullständig liggande med två SAP-system med hög tillgänglighet skulle se ut så här:

![SAP hög tillgänglighet – flera SÄKERHETSIDENTIFIERARE installation med två SAP-system SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Inställningen måste uppfylla följande villkor:
> - SAP ASCS/SCS-instanser måste dela samma WSFC-klustret.
> - Varje DBMS SID måste ha sitt eget dedikerade WSFC-kluster.
> - SAP-programservrar som hör till en SAP-system SID måste ha sina egna dedikerade virtuella datorer.


## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen
För att förbereda din infrastruktur, kan du installera en ytterligare SAP ASCS/SCS-instans med följande parametrar:

| Parameternamn | Värde |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS intern belastningsutjämnare | PR5 |
| SAP virtuellt värdnamn | pr5-sap-cl |
| SAP ASCS/SCS virtuell värd IP-adress (ytterligare Azure load balancer IP-adress) | 10.0.0.50 |
| Antal instanser av SAP ASCS/SCS | 50 |
| ILB-avsökningsporten för ytterligare SAP ASCS/SCS-instans | 62350 |

> [!NOTE]
> För SAP ASCS/SCS-instanser kräver en unik avsökningsport i varje IP-adress. Om en IP-adress på en Azure intern belastningsutjämnare använder avsökningsporten 62300, kan inga andra IP-adressen för den belastningsutjämnaren använda avsökningsporten 62300.
>
>För våra syften eftersom avsökningsporten 62300 har redan reserverats, använder vi avsökningsporten 62350.

Du kan installera ytterligare SAP ASCS/SCS-instanser i det befintliga WSFC-klustret med två noder:

| Virtuell datorroll | Värdnamn för virtuell dator | Statisk IP-adress |
| --- | --- | --- |
| 1 nod för ASCS/SCS-instans |pr1-ascs-0 |10.0.0.10 |
| 2 klusternod för ASCS/SCS-instans |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen på DNS-servern

Du kan skapa en DNS-post för det virtuella värdnamnet på ASCS/SCS-instans med hjälp av följande parametrar:

| Ny SAP ASCS/SCS virtuellt värdnamn | Associerade IP-adress |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Ny värdnamn och IP-adress visas i DNS-hanteraren, enligt följande skärmbild:

![DNS-hanterarens lista markering definierade DNS-posten för den nya SAP ASCS/SCS klusternamn virtuella och TCP/IP-adress][sap-ha-guide-figure-6004]

Proceduren för att skapa en DNS-post är också beskrivs i detalj i huvudsakliga [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer][sap-ha-guide-9.1.1].

> [!NOTE]
> Den nya IP-adressen som du tilldelar till virtuella värdnamnet för ytterligare ASCS/SCS-instans måste vara samma som den nya IP-adress som tilldelats SAP Azure load balancer.
>
>I vårt scenario är den IP-adressen 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Lägga till en IP-adress till en befintlig Azure intern belastningsutjämnare med hjälp av PowerShell

Om du vill skapa fler än en SAP ASCS/SCS-instans i samma WSFC-klustret, lägga till en IP-adress till en befintlig Azure intern belastningsutjämnare med hjälp av PowerShell. Varje IP-adress kräver sin egen belastningsutjämningsregler, avsökningsporten, IP-adresspool på klientsidan och backend-poolen.

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. Uppdatera PowerShell-variabler för din miljö. Skriptet skapar alla nödvändiga belastningsutjämningsregler för alla SAP ASCS/SCS-portar.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
När skriptet har körts visas resultaten i Azure-portalen, enligt följande skärmbild:

![Ny frontend IP-pool i Azure portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lägga till diskar i klustret datorer och konfigurera SIOS resurs klusterdisken

Du måste lägga till en ny resurs för klusterdisk för varje ytterligare SAP ASCS/SCS-instans. För Windows Server 2012 R2 är WSFC-kluster dela disken används för tillfället programvarulösning SIOS DataKeeper.

Gör följande:
1. Lägga till ytterligare en disk eller diskar med samma storlek (som du behöver stripe-) till var och en av noderna i klustret och formatera dem.
2. Konfigurera lagringsreplikering med SIOS DataKeeper.

Den här proceduren förutsätter att du redan har installerat SIOS DataKeeper på datorer för WSFC-klustret. Om du har installerat den, måste du nu konfigurera replikering mellan datorer. Processen beskrivs i detalj i huvudsakliga [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer][sap-ha-guide-8.12.3.3].  

![DataKeeper synkron spegling för den nya SAP ASCS/SCS dela disken][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Distribuera virtuella datorer för SAP-programservrar och DBMS-kluster

För att slutföra infrastrukturförberedelser för andra SAP-system, gör du följande:

1. Distribuera dedikerade virtuella datorer för SAP-programservrar och placerar dem på sina egna dedikerade tillgänglighetsgruppen.
2. Distribuera dedikerade virtuella datorer för DBMS-klustret och placerar dem på sina egna dedikerade tillgänglighetsgruppen.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Installera andra SAP SID2 NetWeaver

Fullständig processen för att installera en andra SID2 för SAP-system beskrivs i huvudsakliga [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer][sap-ha-guide-9].

Övergripande visas på följande sätt:

1. [Installera den första klusternoden SAP][sap-ha-guide-9.1.2].  
 I det här steget ska du installerar SAP med en hög tillgänglighet ASCS/SCS-instans på den **befintliga WSFC-klusternoden 1**.

2. [Ändra SAP-profilen för ASCS/SCS-instans][sap-ha-guide-9.1.3].

3. [Konfigurera en avsökningsport][sap-ha-guide-9.1.4].  
 I det här steget ska konfigurerar du en SAP-klusterresursen SAP-SID2-IP-avsökningsporten med hjälp av PowerShell. Kör den här konfigurationen på en av noderna i SAP ASCS/SCS.

4. [Install databasinstansen] [sap-ha-guide-9.2].  
 I det här steget ska installerar du DBMS på en dedikerad WSFC-klustret.

5. [Install den andra noden i klustret] [sap-ha-guide-9.3].  
 I det här steget ska installerar du SAP med en hög tillgänglighet ASCS/SCS-instans på den befintliga WSFC-klusternoden 2.

6. Öppna portar i Windows-brandväggen för SAP ASCS/SCS-instans och ProbePort.  
 På båda klusternoderna som används för SAP ASCS/SCS-instanser, öppnar du alla Windows-brandväggen portar som används av SAP ASCS/SCS. De här portarna visas i den [guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer][sap-ha-guide-8.8].  
 Också öppna Azure interna avsökningen belastningsutjämnarporten, vilket är 62350 i vårt scenario.

7. [Ändra starttypen för tjänstinstansen SAP ÄNDARE Windows][sap-ha-guide-9.4].

8. [Installera den primära SAP-programservern] [ sap-ha-guide-9.5] på den nya dedikerade VM.

9. [Installera SAP ytterligare programservern] [ sap-ha-guide-9.6] på den nya dedikerade VM.

10. [Testa redundans för SAP ASCS/SCS-instans och SIOS replikering][sap-ha-guide-10].

## <a name="next-steps"></a>Nästa steg

- [Begränsningar för nätverk: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Flera virtuella IP-adresser för Azure Load Balancer][load-balancer-multivip-overview]
- [Guide för hög tillgänglighet SAP NetWeaver på virtuella Windows-datorer][sap-ha-guide]
