---
title: Konfigurera en ILB-lyssnare för tillgänglighets grupper (klassisk)
description: Den här självstudien använder resurser som skapats med den klassiska distributions modellen och skapar en Always on-lyssnare för tillgänglighets grupp i för en SQL Server VM i Azure som använder en intern belastningsutjämnare.
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
ms.openlocfilehash: f05e1d46485b337acbd9390441359e086067db74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014831"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurera en ILB-lyssnare för tillgänglighets grupper på Azure SQL Server virtuella datorer
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du vill konfigurera en lyssnare för en Always on-tillgänglighetsgruppen i Resource Manager-modellen, se [Konfigurera en belastningsutjämnare för en Always on-tillgänglighets grupp i Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

Din tillgänglighets grupp kan bara innehålla repliker som endast är lokala eller endast Azure, eller som omfattar både lokalt och Azure för Hybrid konfigurationer. Azure-replikeringar kan finnas inom samma region eller i flera regioner som använder flera virtuella nätverk. Procedurerna i den här artikeln förutsätter att du redan har [konfigurerat en tillgänglighets grupp](../classic/portal-sql-alwayson-availability-groups.md) men ännu inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Rikt linjer och begränsningar för interna lyssnare
Användning av en intern belastningsutjämnare (ILB) med en tillgänglighets grupps lyssnare i Azure omfattas av följande rikt linjer:

* Tillgänglighets gruppens lyssnare stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Endast en intern tillgänglighets grupps lyssnare stöds för varje moln tjänst, eftersom lyssnaren har kon figurer ATS för ILB och det bara finns en ILB för varje moln tjänst. Det är dock möjligt att skapa flera externa lyssnare. Mer information finns i [Konfigurera en extern lyssnare för Always on-tillgänglighetsgrupper i Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Avgöra tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln fokuserar på att skapa en lyssnare som använder en ILB. Om du behöver en offentlig eller extern lyssnare, se den version av artikeln som beskriver hur du konfigurerar en [extern lyssnare](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade VM-slutpunkter med direkt Server retur
Först skapar du en ILB genom att köra skriptet senare i det här avsnittet.

Skapa en belastningsutjämnad slut punkt för varje virtuell dator som är värd för en Azure-replik. Om du har repliker i flera regioner måste varje replik för regionen finnas i samma moln tjänst i samma virtuella Azure-nätverk. Att skapa tillgänglighets grupp repliker som sträcker sig över flera Azure-regioner kräver att du konfigurerar flera virtuella nätverk. Mer information om hur du konfigurerar anslutning mellan virtuella nätverk finns i [Konfigurera virtuellt nätverk till virtuell nätverks anslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. I Azure Portal går du till varje virtuell dator som är värd för en replik för att se informationen.

2. Klicka på fliken **slut punkter** för varje virtuell dator.

3. Kontrol lera att **namnet** och den **offentliga porten** för den lyssnare slut punkt som du vill använda inte redan används. I exemplet i det här avsnittet är namnet min *slut punkt*och porten är *1433*.

4. Hämta och installera den senaste [PowerShell-modulen](https://azure.microsoft.com/downloads/)på den lokala klienten.

5. Starta Azure PowerShell.  
    En ny PowerShell-session öppnas med de Azure-administrativa moduler som lästs in.

6. Kör `Get-AzurePublishSettingsFile`. Denna cmdlet dirigerar dig till en webbläsare för att ladda ned en fil för publicerings inställningar till en lokal katalog. Du kan uppmanas att ange dina inloggnings uppgifter för din Azure-prenumeration.

7. Kör följande `Import-AzurePublishSettingsFile` kommando med sökvägen till filen med publicerings inställningar som du laddade ned:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    När filen med publicerings inställningar har importer ATS kan du hantera din Azure-prenumeration i PowerShell-sessionen.

8. Tilldela en statisk IP-adress för *ILB*. Granska den aktuella virtuella nätverks konfigurationen genom att köra följande kommando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Anteckna *under nätets* namn som innehåller de virtuella datorer som är värdar för replikerna. Det här namnet används i $SubnetName-parametern i skriptet.

10. Notera namnet på *VirtualNetworkSite* och Start- *AddressPrefix* för det undernät som innehåller de virtuella datorer som är värdar för replikerna. Leta efter en tillgänglig IP-adress genom att skicka båda värdena till `Test-AzureStaticVNetIP` kommandot och genom att undersöka *AvailableAddresses*. Om det virtuella nätverket till exempel heter *MyVNet* och har ett adress intervall för under nätet som börjar vid *172.16.0.128*, visar följande kommando tillgängliga adresser:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Välj en av de tillgängliga adresserna och Använd den i parametern $ILBStaticIP i skriptet i nästa steg.

12. Kopiera följande PowerShell-skript till en text redigerare och Ställ in variabel värden så att de passar din miljö. Standardvärden har angetts för vissa parametrar.  

    Befintliga distributioner som använder tillhörighets grupper kan inte lägga till en ILB. Mer information om kraven för ILB finns i [Översikt över intern belastningsutjämnare](../../../load-balancer/load-balancer-internal-overview.md).

    Om din tillgänglighets grupp sträcker sig över Azure-regioner måste du dessutom köra skriptet en gång i varje data Center för moln tjänsten och noder som finns i det data centret.

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

13. När du har angett variablerna kopierar du skriptet från text redigeraren till PowerShell-sessionen för att köra det. Om meddelandet fortfarande visas **>>** trycker du på RETUR igen för att kontrol lera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrol lera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna brand Väggs portarna i tillgänglighets gruppens noder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighets gruppens lyssnare

Skapa tillgänglighets gruppens lyssnare i två steg. Först skapar du kluster resursen för klient åtkomst punkten och konfigurerar beroenden. Sedan konfigurerar du kluster resurserna i PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klient åtkomst punkten och konfigurera kluster beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera kluster resurserna i PowerShell
1. För ILB måste du använda IP-adressen för den ILB som skapades tidigare. Använd följande skript för att hämta den här IP-adressen i PowerShell:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. På en av de virtuella datorerna kopierar du PowerShell-skriptet för operativ systemet till en text redigerare och anger sedan variablerna till de värden som du antecknade tidigare.

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

3. När du har angett variablerna, öppnar du ett upphöjt Windows PowerShell-fönster, klistrar in skriptet från text redigeraren till PowerShell-sessionen för att köra det. Om meddelandet fortfarande visas **>>** trycker du på RETUR igen för att kontrol lera att skriptet börjar köras.

4. Upprepa föregående steg för varje virtuell dator.  
    Det här skriptet konfigurerar IP-adressresursen med IP-adressen för moln tjänsten och anger andra parametrar, t. ex. avsöknings port. När IP-adressresursen är online kan den svara på avsökningen på avsöknings porten från den belastningsutjämnade slut punkt som du skapade tidigare.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljnings objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testa tillgänglighets gruppens lyssnare (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
