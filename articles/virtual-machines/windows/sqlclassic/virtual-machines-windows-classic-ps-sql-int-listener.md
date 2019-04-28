---
title: Konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure | Microsoft Docs
description: Den här självstudien används resurser som skapats med den klassiska distributionsmodellen och skapar en alltid på tillgänglighetsgruppens lyssnare i Azure som använder en intern belastningsutjämnare.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097736"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

För att konfigurera en lyssnare för en Always On-tillgänglighetsgrupp i Resource Manager-modellen, se [konfigurera en belastningsutjämnare för en Always On-tillgänglighetsgrupp i Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Tillgänglighetsgruppen kan innehålla repliker som är endast lokalt eller Azure endast eller som omfattar både lokala och Azure för hybridkonfigurationer. Azure repliker kan finnas inom samma region eller över flera regioner med flera virtuella nätverk. Procedurerna i den här artikeln förutsätter att du har redan [konfigurerat en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men ännu inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Riktlinjer och begränsningar för interna lyssnare
Användning av en intern belastningsutjämnare (ILB) med en tillgänglighetsgruppslyssnare i Azure är underkastad följande riktlinjer:

* Tillgänglighetsgruppslyssnaren stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Endast en intern tillgänglighetsgruppens lyssnare stöds för varje tjänst i molnet, eftersom lyssnaren är konfigurerad för att den interna Belastningsutjämnaren det finns endast en ILB för varje tjänst i molnet. Det är dock möjligt att skapa flera externa lyssnare. Mer information finns i [konfigurera en extern lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Fastställa tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln handlar om hur du skapar en lyssnare som använder en ILB. Om du behöver en offentlig eller externa lyssnare kan se versionen av den här artikeln beskrivs inställningen upp en [extern lyssnare](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade slutpunkter för virtuella datorer med direct server returnerade
Först skapar du en ILB genom att köra skriptet senare i det här avsnittet.

Skapa en belastningsutjämnad slutpunkt för varje virtuell dator som är värd för en Azure-replik. Om du har replikerna i flera regioner kan måste varje replik för en regionen finnas i samma molntjänst i samma Azure-nätverk. Skapa availability group-repliker som sträcker sig över flera Azure-regioner kräver att du konfigurerar flera virtuella nätverk. Läs mer om hur du konfigurerar mellan virtuell nätverksanslutning [Konfigurera virtuella nätverk till virtuell nätverksanslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Gå till varje virtuell dator som är värd för en replik för att visa information i Azure-portalen.

2. Klicka på den **slutpunkter** fliken för varje virtuell dator.

3. Kontrollera att den **namn** och **offentlig Port** på lyssnare slutpunkt som du vill använda som inte redan används. I exemplet i det här avsnittet, namnet är *MyEndpoint*, och porten är *1433*.

4. På din lokala klient, hämtar och installerar senast [PowerShell-modulen](https://azure.microsoft.com/downloads/).

5. Starta Azure PowerShell.  
    En ny PowerShell-session öppnas med Azure-administrativa moduler läses in.

6. Kör `Get-AzurePublishSettingsFile`. Denna cmdlet dirigerar dig till en webbläsare för att ladda ned en fil med Publiceringsinställningar till en lokal katalog. Du kanske blir tillfrågad om dina inloggningsuppgifter för din Azure-prenumeration.

7. Kör följande `Import-AzurePublishSettingsFile` med sökvägen till den publiceringsinställningsfil som du laddade ned:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    När publiceringsinställningsfil har importerats kan du hantera din Azure-prenumeration i PowerShell-session.

8. För *ILB*, tilldela en statisk IP-adress. Kontrollera den aktuella konfigurationen för virtuellt nätverk genom att köra följande kommando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Obs den *undernät* namn för det undernät som innehåller virtuella datorer som är värdar för replikerna. Det här namnet används i parametern $SubnetName i skriptet.

10. Obs den *VirtualNetworkSite* namn och Start *AddressPrefix* för det undernät som innehåller de virtuella datorerna som är värdar för replikerna. Leta efter en tillgänglig IP-adress genom att skicka båda värdena till den `Test-AzureStaticVNetIP` kommandot och genom att undersöka den *AvailableAddresses*. Exempel: om det virtuella nätverket har namnet *MyVNet* och har ett adressintervall för undernätet som börjar vid *172.16.0.128*, kommandot Listar tillgängliga adresser:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Välj en av de tillgängliga adresserna och använda den i parametern $ILBStaticIP av skriptet i nästa steg.

12. Kopiera följande PowerShell-skript till en textredigerare och skapa variabelvärden som passar din miljö. Standardvärden har angetts för vissa parametrar.  

    Befintliga distributioner som använder tillhörighetsgrupper kan inte lägga till en ILB. Läs mer om krav för ILB [översikt över intern belastningsutjämnare](../../../load-balancer/load-balancer-internal-overview.md).

    Även om tillgänglighetsgruppen sträcker sig över Azure-regioner, måste du köra skriptet en gång i varje datacenter för Molntjänsten och noder som finns i det datacentret.

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

13. När du har angett variabeln, kopiera skriptet från textredigeraren till din PowerShell-session för att köra den. Om meddelandet visas fortfarande **>>**, tryck på RETUR igen för att kontrollera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna portar i brandväggen i grupp-noder för tillgänglighet
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighetsgruppens lyssnare

Skapa tillgänglighetsgruppens lyssnare i två steg. Först skapar klienten åtkomst punkt klusterresursen och konfigurera beroenden. Konfigurera andra klusterresurserna i PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera kluster-beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klusterresurserna i PowerShell
1. För ILB, måste du använda IP-adressen för den interna Belastningsutjämnaren som du skapade tidigare. Om du vill hämta den här IP-adress i PowerShell använder du följande skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Kopiera PowerShell-skript för ditt operativsystem till en textredigerare på en av de virtuella datorerna, och ange sedan variablerna till de värden du antecknade tidigare.

    För Windows Server 2012 eller senare, Använd följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    För Windows Server 2008 R2, använder du följande skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. När du har angett variabeln, öppna en upphöjd Windows PowerShell-fönster, klistra in skriptet från textredigeraren i din PowerShell-session för att köra den. Om meddelandet visas fortfarande **>>**, tryck på RETUR igen för att se till att skriptet börjar köras.

4. Upprepa föregående steg för varje virtuell dator.  
    Det här skriptet konfigurerar IP-adressresurs med IP-adressen för Molntjänsten och anger andra parametrar, till exempel avsökningsporten. När IP-adressresurs är online, kan det svara på avsökningen på avsökningsporten från den belastningsutjämnade slutpunkten som du skapade tidigare.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljning objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testa tillgänglighetsgruppens lyssnare (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
