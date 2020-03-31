---
title: Konfigurera en extern lyssnare för tillgänglighetsgrupper
description: Den här självstudien går igenom steg för att skapa en gruppavlyssnare för alltid på tillgänglighet i Azure som är externt tillgänglig med hjälp av den offentliga virtuella IP-adressen för den associerade molntjänsten.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978188"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurera en extern lyssnare för tillgänglighetsgrupper på virtuella Azure SQL Server-datorer
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
> 
> 

Det här avsnittet visar hur du konfigurerar en lyssnare för en grupp som alltid är tillgänglig för tillgänglighet som är externt tillgänglig på internet. Detta möjliggörs genom att koppla molntjänstens **offentliga VIRTUAL IP-adress (VIP)** med lyssnaren.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Din tillgänglighetsgrupp kan innehålla repliker som endast är lokalt, azure eller spanar både lokalt och Azure för hybridkonfigurationer. Azure-repliker kan finnas inom samma region eller mellan flera regioner med hjälp av flera virtuella nätverk (VNets). Stegen nedan förutsätter att du redan har [konfigurerat en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Riktlinjer och begränsningar för externa lyssnare
Observera följande riktlinjer om tillgänglighetsgruppavlyssnare i Azure när du distribuerar med hjälp av molntjänstens offentliga VIP-adress:

* Lyssnare för tillgänglighetsgruppen stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Klientprogrammet måste finnas på en annan molntjänst än den som innehåller virtuella datorer i tillgänglighetsgruppen. Azure stöder inte direkt serverretur med klient och server i samma molntjänst.
* Som standard visar stegen i den här artikeln hur du konfigurerar en lyssnare för att använda ip-adressen (Cloud Service Virtual IP). Det är dock möjligt att reservera och skapa flera VIP-adresser för din molntjänst. På så sätt kan du använda stegen i den här artikeln för att skapa flera lyssnare som var och en är associerade med en annan VIP. Information om hur du skapar flera VIP-adresser finns i [Flera VIP-adresser per molntjänst](../../../load-balancer/load-balancer-multivip.md).
* Om du skapar en lyssnare för en hybridmiljö måste det lokala nätverket ha anslutning till det offentliga Internet utöver vpn från plats till plats med det virtuella Azure-nätverket. När i Azure-undernätet kan tillgänglighetsgruppavlyssnaren endast nås av den offentliga IP-adressen för respektive molntjänst.
* Det stöds inte för att skapa en extern lyssnare i samma molntjänst där du också har en intern lyssnare med hjälp av Internal Load Balancer (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Bestäm tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln fokuserar på att skapa en lyssnare som använder **extern belastningsutjämning**. Om du vill ha en lyssnare som är privat för ditt virtuella nätverk läser du den version av den här artikeln som innehåller steg för att konfigurera en [lyssnare med ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsbalanserade vm-slutpunkter med direkt serverretur
Extern belastningsutjämning använder den virtuella virtuella virtuella IP-adressen för molntjänsten som är värd för dina virtuella datorer. Så du behöver inte skapa eller konfigurera belastningsutjämnaren i det här fallet.

Du måste skapa en belastningsbalanserad slutpunkt för varje virtuell dator som är värd för en Azure-replik. Om du har repliker i flera regioner måste varje replik för den regionen finnas i samma molntjänst i samma virtuella nätverk. För att skapa kondränningsgrupprepliker som sträcker sig över flera Azure-regioner krävs att flera virtuella nätverk konfigureras. Mer information om hur du konfigurerar anslutning mellan virtuella nätverk finns i [Konfigurera VNet till VNet-anslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. I Azure-portalen navigerar du till varje virtuell dator som är värd för en replik och visar detaljerna.
2. Klicka på fliken **Slutpunkter** för var och en av de virtuella datorerna.
3. Kontrollera att **namn** och **offentlig port** för lyssnarens slutpunkt som du vill använda inte redan används. I exemplet nedan är namnet "MyEndpoint" och porten är "1433".
4. På din lokala klient laddar du ned och installerar [den senaste PowerShell-modulen](https://azure.microsoft.com/downloads/).
5. Starta **Azure PowerShell**. En ny PowerShell-session öppnas med Azure-administratörsmodulerna inlästa.
6. Kör **Get-AzurePublishSettingsFile**. Den här cmdleten leder dig till en webbläsare för att hämta en publiceringsinställningsfil till en lokal katalog. Du kan bli tillfrågad om dina inloggningsuppgifter för din Azure-prenumeration.
7. Kör kommandot **Importera-AzurePublishSettingsFile** med sökvägen till den publiceringsinställningsfil som du hämtade:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    När filen för publiceringsinställningar har importerats kan du hantera din Azure-prenumeration i PowerShell-sessionen.
    
1. Kopiera PowerShell-skriptet nedan till en textredigerare och ange variabelvärden som passar din miljö (standardvärden har angetts för vissa parametrar). Observera att om din tillgänglighetsgrupp sträcker sig över Azure-regioner måste du köra skriptet en gång i varje datacenter för molntjänsten och noder som finns i det datacentret.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. När du har angett variablerna kopierar du skriptet från textredigeraren till din Azure PowerShell-session för att köra det. Om uppmaningen fortfarande visas >> skriver du RETUR igen för att kontrollera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 är installerat om det behövs
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna brandväggsportarna i tillgänglighetsgruppnoder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa lyssnaren för tillgänglighetsgruppen

Skapa tillgänglighetsgrupplyssnaren i två steg. Skapa först klusterresursen för klientåtkomstpunkten och konfigurera beroenden. För det andra konfigurerar du klusterresurserna med PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera klusterberoenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klusterresurserna i PowerShell
1. För extern belastningsutjämning måste du hämta den offentliga virtuella IP-adressen för molntjänsten som innehåller dina repliker. Logga in på Azure-portalen. Navigera till molntjänsten som innehåller din vm-grupp för tillgänglighetsgrupp. Öppna **instrumentpanelsvyn.**
2. Notera adressen som visas under **VIP-adress (Public Virtual IP).** Om din lösning sträcker sig över virtuella nätverk upprepar du det här steget för varje molntjänst som innehåller en virtuell dator som är värd för en replik.
3. På en av de virtuella datorerna kopierar du PowerShell-skriptet nedan till en textredigerare och ställer in variablerna på de värden som du noterade tidigare.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. När du har angett variablerna öppnar du ett upphöjt Windows PowerShell-fönster och kopierar sedan skriptet från textredigeraren och klistrar in i din Azure PowerShell-session för att köra den. Om uppmaningen fortfarande visas >> skriver du RETUR igen för att kontrollera att skriptet börjar köras.
5. Upprepa detta på varje virtuell dator. Det här skriptet konfigurerar IP-adressresursen med molntjänstens IP-adress och anger andra parametrar som avsökningsporten. När IP-adressresursen är online kan den sedan svara på avsökningen på avsökningsporten från den belastningsbalanserade slutpunkten som skapades tidigare i den här självstudien.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljningsobjekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testa tillgänglighetsgrupplyssnaren (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testa tillgänglighetsgruppens lyssnare (över internet)
För att komma åt lyssnaren utanför det virtuella nätverket måste du använda extern/offentlig belastningsutjämning (beskrivs i det här avsnittet) i stället för ILB, som endast är tillgänglig inom samma virtuella nätverk. I anslutningssträngen anger du molntjänstnamnet. Om du till exempel hade en molntjänst med namnet *mycloudservice*skulle sqlcmd-uttrycket vara följande:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Till skillnad från föregående exempel måste SQL-autentisering användas, eftersom anroparen inte kan använda windows-autentisering via Internet. Mer information finns [i Alltid på tillgänglighetsgruppen i Azure VM: Scenarier för klientanslutning](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). När du använder SQL-autentisering, se till att du skapar samma inloggning på båda replikerna. Mer information om felsökning av inloggningar med tillgänglighetsgrupper finns i [Så här mappar du inloggningar eller använder innehållen SQL-databasanvändare för att ansluta till andra repliker och mappa till tillgänglighetsdatabaser](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Om alltid på replikerna finns i olika undernät måste klienter ange **MultisubnetFailover=True** i anslutningssträngen. Detta resulterar i parallella anslutningsförsök till repliker i de olika undernäten. Observera att det här scenariot innehåller en distribution mellan regioner på tillgänglighetsgrupp.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

