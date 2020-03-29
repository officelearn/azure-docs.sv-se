---
title: Konfigurera en ILB-lyssnare för tillgänglighetsgrupper (Klassisk)
description: Den här självstudien använder resurser som skapats med den klassiska distributionsmodellen och skapar en gruppavlyssnare alltid på tillgänglighet för en VIRTUELL SQL Server i Azure som använder en intern belastningsutjämnare.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978173"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurera en ILB-lyssnare för tillgänglighetsgrupper på virtuella Azure SQL Server-datorer
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver användningen av den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Information om hur du konfigurerar en lyssnare för en alltid på tillgänglighetsgrupp i Resource Manager-modellen finns i [Konfigurera en belastningsutjämnare för en alltid på tillgänglighetsgrupp i Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Din tillgänglighetsgrupp kan innehålla repliker som endast är lokalt eller Azure, eller som sträcker sig både lokalt och Azure för hybridkonfigurationer. Azure-repliker kan finnas inom samma region eller i flera regioner som använder flera virtuella nätverk. Procedurerna i den här artikeln förutsätter att du redan har [konfigurerat en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men ännu inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Riktlinjer och begränsningar för interna lyssnare
Användning av en intern belastningsutjämnare (ILB) med en tillgänglighetsgruppavlyssnare i Azure omfattas av följande riktlinjer:

* Lyssnare för tillgänglighetsgruppen stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Endast en intern tillgänglighetsgruppavlyssnare stöds för varje molntjänst, eftersom lyssnaren är konfigurerad till ILB och det bara finns en ILB för varje molntjänst. Det är dock möjligt att skapa flera externa lyssnare. Mer information finns i [Konfigurera en extern lyssnare för alltid på tillgänglighetsgrupper i Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Bestäm tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln fokuserar på att skapa en lyssnare som använder en ILB. Om du behöver en offentlig eller extern lyssnare läser du den version av den här artikeln som innehåller en beskrivning av inrättandet av en [extern lyssnare](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsbalanserade vm-slutpunkter med direkt serverretur
Du skapar först en ILB genom att köra skriptet senare i det här avsnittet.

Skapa en belastningsbalanserad slutpunkt för varje virtuell dator som är värd för en Azure-replik. Om du har repliker i flera regioner måste varje replik för den regionen finnas i samma molntjänst i samma virtuella Azure-nätverk. För att skapa konfiguration av tillgänglighetsgrupprepliker som sträcker sig över flera Azure-regioner krävs att flera virtuella nätverk konfigureras. Mer information om hur du konfigurerar anslutning mellan virtuella nätverk finns i [Konfigurera virtuellt nätverk till anslutning till virtuella nätverk](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Gå till varje virtuell dator som är värd för en replik i Azure-portalen.

2. Klicka på fliken **Slutpunkter** för varje virtuell dator.

3. Kontrollera att **Public Port** **slutpunkten för lyssnaren** som du vill använda inte redan används. I exemplet i det här avsnittet är namnet *MyEndpoint*och porten *1433*.

4. På din lokala klient laddar du ned och installerar den senaste [PowerShell-modulen](https://azure.microsoft.com/downloads/).

5. Starta Azure PowerShell.  
    En ny PowerShell-session öppnas, med Azure-administratörsmodulerna inlästa.

6. Kör `Get-AzurePublishSettingsFile`. Den här cmdleten leder dig till en webbläsare för att hämta en publiceringsinställningsfil till en lokal katalog. Du kan bli tillfrågad om dina inloggningsuppgifter för din Azure-prenumeration.

7. Kör följande `Import-AzurePublishSettingsFile` kommando med sökvägen till den publiceringsinställningsfil som du hämtade:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    När filen för publiceringsinställningar har importerats kan du hantera din Azure-prenumeration i PowerShell-sessionen.

8. För *ILB*tilldelar du en statisk IP-adress. Undersök den aktuella virtuella nätverkskonfigurationen genom att köra följande kommando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Observera *undernätsnamnet* för undernätet som innehåller de virtuella datorer som är värdar för replikerna. Det här namnet används i parametern $SubnetName i skriptet.

10. Observera *namnet på VirtualNetworkSite* och *startadressprefixet* för undernätet som innehåller de virtuella datorer som är värdar för replikerna. Leta efter en tillgänglig IP-adress `Test-AzureStaticVNetIP` genom att skicka båda värdena till kommandot och genom att undersöka *AvailableAddresses*. Om det virtuella nätverket till exempel heter *MyVNet* och har ett undernätsadressintervall som börjar på *172.16.0.128,* skulle följande kommando lista tillgängliga adresser:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Välj en av de tillgängliga adresserna och använd den i parametern $ILBStaticIP för skriptet i nästa steg.

12. Kopiera följande PowerShell-skript till en textredigerare och ange de variabla värden som passar din miljö. Standardvärden har angetts för vissa parametrar.  

    Befintliga distributioner som använder tillhörighetsgrupper kan inte lägga till en ILB. Mer information om ILB-krav finns i [Översikt över intern belastningsutjämnare](../../../load-balancer/load-balancer-internal-overview.md).

    Om din tillgänglighetsgrupp sträcker sig över Azure-regioner måste du också köra skriptet en gång i varje datacenter för molntjänsten och noder som finns i det datacentret.

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

13. När du har angett variablerna kopierar du skriptet från textredigeraren till PowerShell-sessionen för att köra det. Om uppmaningen **>>** fortfarande visas trycker du på Retur igen för att se till att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 är installerat om det behövs
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna brandväggsportarna i tillgänglighetsgruppnoder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa lyssnaren för tillgänglighetsgruppen

Skapa tillgänglighetsgrupplyssnaren i två steg. Skapa först klusterresursen för klientåtkomstpunkten och konfigurera beroenden. För det andra konfigurerar du klusterresurserna i PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera klusterberoenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klusterresurserna i PowerShell
1. För ILB måste du använda IP-adressen för ILB som skapades tidigare. Om du vill hämta den här IP-adressen i PowerShell använder du följande skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. På en av de virtuella datorerna kopierar du PowerShell-skriptet för operativsystemet till en textredigerare och ställer sedan in variablerna på de värden som du noterade tidigare.

    För Windows Server 2012 eller senare använder du följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    För Windows Server 2008 R2 använder du följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. När du har angett variablerna öppnar du ett upphöjt Windows PowerShell-fönster, klistrar in skriptet från textredigeraren i PowerShell-sessionen för att köra det. Om uppmaningen **>>** fortfarande visas trycker du på Retur igen för att se till att skriptet börjar köras.

4. Upprepa föregående steg för varje virtuell dator.  
    Det här skriptet konfigurerar IP-adressresursen med molntjänstens IP-adress och anger andra parametrar, till exempel avsökningsporten. När IP-adressresursen är online kan den svara på avsökningen på avsökningsporten från den belastningsbalanserade slutpunkten som du skapade tidigare.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljningsobjekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testa lyssnaren för tillgänglighetsgruppen (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
