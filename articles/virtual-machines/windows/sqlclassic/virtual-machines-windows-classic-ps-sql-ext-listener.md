---
title: Konfigurera en extern lyssnare för Always On Tillgänglighetsgrupper | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att skapa en alltid på tillgänglighetsgruppens lyssnare i Azure som är externt tillgänglig med hjälp av den offentliga virtuella IP-adressen för den tillhörande Molntjänsten.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101633"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurera en extern lyssnare för Always On-Tillgänglighetsgrupper i Azure
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
> 
> 

Det här avsnittet visar hur du konfigurerar en lyssnare för en ständigt aktiverad tillgänglighetsgrupp som är externt tillgänglig på internet. Detta är möjligt genom att associera molntjänstens **offentliga virtuella IP (VIP)** adressen med lyssnaren.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Tillgänglighetsgruppen kan innehålla repliker som finns lokalt, Azure, eller omfattar både lokala och Azure för hybridkonfigurationer. Azure repliker kan finnas i samma region eller mellan flera regioner med flera virtuella nätverk (Vnet). Stegen nedan förutsätter att du redan har [konfigurerat en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Riktlinjer och begränsningar för externa lyssnare
Observera följande riktlinjer för tillgänglighetsgruppens lyssnare i Azure när du distribuerar med cloud service offentlig VIP-adress:

* Tillgänglighetsgruppslyssnaren stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Klientprogrammet måste finnas på en annan molntjänst än den som innehåller din tillgänglighetsgruppen virtuella datorer. Azure stöder inte direkt serverretur med klient och server i samma molntjänst.
* Stegen i den här artikeln visar hur du konfigurerar en lyssnare för att använda molnet tjänstadressen virtuell IP (VIP) som standard. Det är dock möjligt att reservera och skapa flera VIP-adresser för din molntjänst. På så sätt kan du använda stegen i den här artikeln för att skapa flera lyssnare som är associerade med en annan VIP. Information om hur du skapar flera virtuella IP-adresser finns i [flera virtuella IP-adresser per molntjänst](../../../load-balancer/load-balancer-multivip.md).
* Om du skapar en lyssnare för en hybridmiljö, måste det lokala nätverket ha en anslutning till det offentliga Internet förutom plats-till-plats-VPN-anslutningar med Azure-nätverket. Tillgänglighetsgruppens lyssnare är tillgängligt endast offentliga IP-adressen för respektive moln-tjänsten i Azure-undernätet.
* Det går inte för att skapa en extern lyssnare i samma molntjänst där du har också en intern lyssnare med den interna belastningsutjämnaren (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Fastställa tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln handlar om hur du skapar en lyssnare som använder **extern belastningsutjämning**. Om du vill att en lyssnare som är privata för ditt virtuella nätverk finns i versionen av den här artikeln innehåller steg för att skapa en [lyssnare med ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade slutpunkter för virtuella datorer med direct server returnerade
Extern belastningsutjämning använder den virtuella den offentliga virtuella IP-adressen för den molntjänst som är värd för dina virtuella datorer. Så behöver du inte skapa eller konfigurera belastningsutjämnaren i det här fallet.

Du måste skapa en belastningsutjämnad slutpunkt för varje virtuell dator som är värd för en Azure-replik. Om du har replikerna i flera regioner kan måste varje replik för en regionen finnas i samma molntjänst på samma virtuella nätverk. Skapa Availability Group-repliker som sträcker sig över flera Azure-regioner kräver att du konfigurerar flera virtuella nätverk. Mer information om hur du konfigurerar mellan VNet-anslutning finns i [konfigurera anslutningar mellan virtuella nätverk](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Navigera till varje virtuell dator som är värd för en replik i Azure-portalen och visa information.
2. Klicka på den **slutpunkter** flik för var och en av de virtuella datorerna.
3. Kontrollera att den **namn** och **offentlig Port** lyssnaren slutpunkten som du vill använda är inte redan används. Namnet är ”MyEndpoint” i exemplet nedan och porten är ”1433”.
4. På din lokala klient, ladda ned och installera [senaste PowerShell-modulen](https://azure.microsoft.com/downloads/).
5. Starta **Azure PowerShell**. Då öppnas en ny PowerShell-session med Azure-administrativa moduler läses in.
6. Run **Get-AzurePublishSettingsFile**. Denna cmdlet dirigerar dig till en webbläsare för att ladda ned en fil med Publiceringsinställningar till en lokal katalog. Du kan uppmanas för dina autentiseringsuppgifter för inloggning för dina Azure-prenumeration.
7. Kör den **Import AzurePublishSettingsFile** med sökvägen till den publiceringsinställningsfil som du laddade ned:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    När du har importerat publiceringsinställningsfil kan du hantera din Azure-prenumeration i PowerShell-session.
    
1. Kopiera PowerShell-skriptet nedan i en textredigerare och skapa variabelvärden som passar din miljö (standardvärden har angetts för vissa parametrar). Observera att om tillgänglighetsgruppen sträcker sig över Azure-regioner, måste du köra skriptet en gång i varje datacenter för Molntjänsten och noder som finns i det datacentret.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. När du har angett variabeln, kopiera skriptet från redigeringsprogram i Azure PowerShell-sessionen för att köra den. Om meddelandet visas fortfarande >>, skriver du ange igen för att kontrollera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna portar i brandväggen i grupp-noder för tillgänglighet
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighetsgruppens lyssnare

Skapa tillgänglighetsgruppens lyssnare i två steg. Först skapar klienten åtkomst punkt klusterresursen och konfigurera beroenden. Konfigurera sedan klustrets resurser med PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera kluster-beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klusterresurserna i PowerShell
1. Extern belastningsutjämning, måste du hämta den offentliga virtuella IP-adressen för den molntjänst som innehåller dina repliker. Logga in på Azure-portalen. Navigera till den molntjänst som innehåller din tillgänglighetsgruppen VM. Öppna den **instrumentpanelen** vy.
2. Observera den adress som anges under **offentlig virtuell IP-adress (VIP)**. Om din lösning sträcker sig över virtuella nätverk, upprepa det här steget för varje molntjänst som innehåller en virtuell dator som är värd för en replik.
3. På en av de virtuella datorerna, kopiera PowerShell-skriptet nedan i en textredigerare och ange variabler till de värden du antecknade tidigare.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. När du har lagt variablerna, öppna en upphöjd Windows PowerShell-fönster och sedan kopiera skriptet från textredigerare och klistra in i Azure PowerShell-session för att köra den. Om meddelandet visas fortfarande >>, skriver du ange igen för att kontrollera att skriptet börjar köras.
5. Upprepa det här på varje virtuell dator. Det här skriptet konfigurerar IP-adressresurs med IP-adressen för Molntjänsten och anger andra parametrar som avsökningsporten. När IP-adressresursen är online, svara den sedan på avsökningen på avsökningsporten från belastningsutjämnade slutpunkten som skapades tidigare i självstudien.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljning objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testa tillgänglighetsgruppens lyssnare (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testa tillgänglighetsgruppens lyssnare (via internet)
För att komma åt lyssnaren från utanför det virtuella nätverket måste du använda externa/offentlig belastningsutjämning (beskrivs i det här avsnittet) i stället för ILB, vilket är endast åtkomlig inom samma virtuella nätverk. I anslutningssträngen anger du molntjänstens namn. Exempel: Om du hade en molntjänst med namnet *mycloudservice*, sqlcmd-instruktionen är enligt följande:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Till skillnad från det tidigare exemplet måste SQL-autentisering användas, eftersom anroparen inte kan använda windows-autentisering via internet. Mer information finns i [ständigt aktiverad tillgänglighetsgrupp i virtuell Azure-dator: Scenarier för anslutning av klienten](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). När du använder SQL-autentisering måste du kontrollera att du skapar samma inloggning på båda replikerna. Läs mer om hur du felsöker inloggningar med Tillgänglighetsgrupper [mappa inloggningar eller använder oberoende databasanvändare som SQL för att ansluta till andra repliker och mappa tillgänglighetsdatabaserna](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Om Always On-repliker är i olika undernät, måste klienter ange **MultisubnetFailover = True** i anslutningssträngen. Detta resulterar i parallella anslutningsförsök till kopior i olika undernät. Observera att det här scenariot innehåller en interregionala Always On Availability Group-distribution.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

