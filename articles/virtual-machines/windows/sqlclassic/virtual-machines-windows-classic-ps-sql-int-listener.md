---
title: "Konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure | Microsoft Docs"
description: "Den här kursen använder resurser som har skapats med den klassiska distributionsmodellen och skapar en alltid på tillgänglighetsgruppens lyssnare i Azure som använder en intern belastningsutjämnare."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 418920899612cac7336af14baff75c58a1cd8bef
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure
> [!div class="op_single_selector"]
> * [Internt lyssnare](../classic/ps-sql-int-listener.md)
> * [Externa lyssnare](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver användningen av den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du vill konfigurera en lyssnare för en tillgänglighetsgrupp alltid på i Resource Manager-modellen finns [konfigurera belastningsutjämning för en tillgänglighetsgrupp alltid på i Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Tillgänglighetsgruppen kan innehålla repliker som är endast till lokala eller endast Azure eller som sträcker sig över både lokalt och Azure för hybrid-konfigurationer. Azure repliker kan finnas i samma region eller över flera regioner som använder flera virtuella nätverk. Procedurerna i den här artikeln förutsätter att du redan har [konfigureras en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men ännu inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Riktlinjer och begränsningar för interna lyssnare
Användning av en intern belastningsutjämnare (ILB) med en tillgänglighetsgruppslyssnare i Azure regleras av följande riktlinjer:

* Tillgänglighetsgruppslyssnaren stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Endast en intern tillgänglighetsgruppens lyssnare stöds för varje molnbaserad tjänst bör eftersom lyssnaren har konfigurerats som ILB och det finns endast en ILB för varje tjänst i molnet. Det är dock möjligt att skapa flera externa lyssnare. Mer information finns i [konfigurera en extern lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Avgöra tillgängligheten för lyssnare
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln handlar om hur du skapar en lyssnare som använder en ILB. Om du behöver en offentlig eller externa lyssnare finns i versionen av den här artikeln beskrivs ställa in en [externa lyssnare](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade slutpunkter för virtuell dator med direkt servern returnerade
Du först skapa en ILB genom att köra skriptet senare i det här avsnittet.

Skapa en slutpunkt för Utjämning av nätverksbelastning för varje virtuell dator som är värd för en Azure replik. Om du har repliker i flera områden, måste varje replik för den regionen finnas i samma molntjänst i samma virtuella Azure-nätverket. Skapa tillgänglighet grupp repliker som sträcker sig över flera Azure-regioner måste du konfigurera flera virtuella nätverk. Mer information om hur du konfigurerar mellan virtuell nätverksanslutning finns [Konfigurera virtuella nätverk för virtuell nätverksanslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Gå till varje virtuell dator som är värd för en replik för att visa information i Azure-portalen.

2. Klicka på den **slutpunkter** för varje virtuell dator.

3. Kontrollera att den **namn** och **offentlig Port** för lyssnare slutpunkt som du vill använda som inte redan används. I exemplet i det här avsnittet är namnet *MyEndpoint*, och porten *1433*.

4. På din lokala klient, hämta och installera senaste [PowerShell-modulen](https://azure.microsoft.com/downloads/).

5. Starta Azure PowerShell.  
    En ny PowerShell-session öppnas med Azure administrativa moduler som lästs in.

6. Kör `Get-AzurePublishSettingsFile`. Denna cmdlet leder till en webbläsare för att hämta en fil med inställningar i Publicera till en lokal katalog. Du kan uppmanas att dina inloggningsuppgifter för din Azure-prenumeration.

7. Kör följande `Import-AzurePublishSettingsFile` kommandot med sökvägen till filen publicera inställningar som du hämtade:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    När filen publicera har importerats kan hantera du Azure-prenumerationen i PowerShell-sessionen.

8. För *ILB*, tilldela en statisk IP-adress. Kontrollera den aktuella konfigurationen för virtuella nätverk genom att köra följande kommando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Observera den *undernät* namn för det undernät som innehåller de virtuella datorerna som är värdar för replikerna. Det här namnet används i parametern $SubnetName i skriptet.

10. Observera den *VirtualNetworkSite* namn och Start *AddressPrefix* för det undernät som innehåller de virtuella datorerna som är värdar för replikerna. Leta efter en tillgänglig IP-adress genom att skicka båda värdena i `Test-AzureStaticVNetIP` kommando och genom att undersöka den *AvailableAddresses*. Om det virtuella nätverket har namnet till exempel *MyVNet* och har ett undernät-adressintervall som börjar vid *172.16.0.128*, följande kommando visar tillgängliga adresser:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Välj en av de tillgängliga adresserna och använda den i parametern $ILBStaticIP av skriptet i nästa steg.

12. Kopiera följande PowerShell-skript till en textredigerare och ange variabelvärden som passar din miljö. Standardvärden har angetts för vissa parametrar.  

    Befintliga distributioner som använder tillhörighetsgrupper kan inte lägga till en ILB. Läs mer om krav för ILB [översikt över intern belastningsutjämnare](../../../load-balancer/load-balancer-internal-overview.md).

    Även om tillgänglighetsgruppen sträcker sig över Azure-regioner, måste du köra skriptet en gång i varje datacenter för Molntjänsten och noder som tillhör det datacentret.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. När du har angett variablerna kopiera skriptet från textredigeraren i PowerShell-sessionen att köra den. Om uppmaningen visas fortfarande  **>>** , tryck på RETUR igen och kontrollera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna portar i brandväggen i tillgänglighet gruppnoder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighetsgruppens lyssnare

Skapa tillgänglighetsgruppens lyssnare i två steg. Först skapar klienten åtkomst punkt klusterresursen och konfigurera beroenden. Konfigurera andra klusterresurserna i PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera kluster-beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klustrets resurser i PowerShell
1. För ILB, måste du använda IP-adressen för ILB som du skapade tidigare. Om du vill hämta den här IP-adress i PowerShell använder du följande skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. På en av de virtuella datorerna, kopiera PowerShell-skript för ditt operativsystem till en textredigerare och ange sedan variablerna till värdena som du antecknade tidigare.

    För Windows Server 2012 eller senare, Använd följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    För Windows Server 2008 R2, använder du följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. När du har angett variabeln, öppna ett upphöjt Windows PowerShell-fönster, klistra in skriptet från textredigeraren i PowerShell-session för att köra den. Om uppmaningen visas fortfarande  **>>** , tryck på RETUR igen och kontrollera att skriptet börjar köras.

4. Upprepa föregående steg för varje virtuell dator.  
    Det här skriptet konfigurerar IP-adressresurs med IP-adressen för Molntjänsten och anger andra parametrar, till exempel avsökningsport. När IP-adressresurs är online, svarar den till avsökning på avsökningsport från den belastningsutjämnade slutpunkt som du skapade tidigare.

## <a name="bring-the-listener-online"></a>Ta lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljning av objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testa tillgänglighetsgruppens lyssnare (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
