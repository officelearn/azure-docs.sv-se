---
title: "Konfigurera en extern lyssnare för Always On-Tillgänglighetsgrupper | Microsoft Docs"
description: "Den här självstudiekursen vägleder dig genom stegen för att skapa en alltid på tillgänglighetsgruppens lyssnare i Azure som är tillgänglig externt med hjälp av den offentliga virtuella IP-adressen för tjänsten associerade molnet."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 8e506be42aea4fb3c48c29b771a78dcf694f4518
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurera en extern lyssnare för Always On-Tillgänglighetsgrupper i Azure
> [!div class="op_single_selector"]
> * [Internt lyssnare](../classic/ps-sql-int-listener.md)
> * [Externa lyssnare](../classic/ps-sql-ext-listener.md)
> 
> 

Det här avsnittet visar hur du konfigurerar en lyssnare för en Always On-Tillgänglighetsgruppen som är tillgänglig externt på internet. Detta är möjligt genom att associera Molntjänsten **offentliga virtuella IP-adresser (VIP)** adress med lyssnaren.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Tillgänglighetsgruppen kan innehålla repliker som finns lokalt, Azure, eller omfattar både lokalt och Azure för hybrid-konfigurationer. Azure repliker kan finnas i samma region eller över flera regioner med flera virtuella nätverk (Vnet). Stegen nedan förutsätter att du redan har [konfigureras en tillgänglighetsgrupp](../classic/portal-sql-alwayson-availability-groups.md) men inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Riktlinjer och begränsningar för externa lyssnare
Tänk på följande riktlinjer om tillgänglighetsgruppens lyssnare i Azure när du distribuerar med hjälp av cloud service blygdbenets VIP-adressen:

* Tillgänglighetsgruppslyssnaren stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Klientprogrammet måste finnas på en annan molntjänst än den som innehåller tillgänglighetsgruppen virtuella datorer. Azure stöder inte direkt serverretur med klient- och i samma molntjänst.
* Som standard visar stegen i den här artikeln hur du konfigurerar en lyssnare för att använda virtuella IP-adresser (VIP)-adressen cloud service. Det är dock möjligt att reservera och skapa flera VIP-adresser för Molntjänsten. Detta gör att du kan använda stegen i den här artikeln för att skapa flera lyssnare som är associerade med en annan VIP. Information om hur du skapar flera VIP-adresser finns [flera VIP: er per Molntjänsten](../../../load-balancer/load-balancer-multivip.md).
* Om du skapar en lyssnare för en hybridmiljö måste lokalt nätverk ha en offentlig Internetanslutning förutom plats-till-plats-VPN-anslutningar med virtuella Azure-nätverket. Tillgänglighetsgruppens lyssnare är tillgängligt endast offentliga IP-adressen för respektive moln-tjänsten i Azure-undernät.
* Det går inte för att skapa en extern lyssnare i samma Molntjänsten där du även har en intern lyssnare med hjälp av interna belastningen belastningsutjämnare (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Avgöra tillgängligheten för lyssnare
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln fokuserar på hur du skapar en lyssnare som använder **extern belastningsutjämning**. Om du vill att en lyssnare som är privat för ditt virtuella nätverk finns i versionen av den här artikeln innehåller steg för att skapa en [lyssnare med ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade slutpunkter för virtuell dator med direkt servern returnerade
Externa nätverksbelastning använder den virtuella offentliga virtuella IP-adressen för den molntjänst som är värd för dina virtuella datorer. Så behöver du inte skapa eller konfigurera belastningsutjämnaren i det här fallet.

För varje virtuell dator som värd för en replik av en Azure måste du skapa en slutpunkt för Utjämning av nätverksbelastning. Om du har repliker i flera områden, måste varje replik för den regionen finnas i samma molntjänst i samma virtuella nätverk. Skapa Tillgänglighetsgruppen repliker som sträcker sig över flera Azure-regioner kräver konfigurera flera virtuella nätverk. Mer information om hur du konfigurerar mellan VNet-anslutningen finns [konfigurera VNet till VNet Connectivity](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Navigera till varje virtuell dator som värd för en replik i Azure-portalen och visa information.
2. Klicka på den **slutpunkter** för var och en av de virtuella datorerna.
3. Kontrollera att den **namn** och **offentlig Port** för lyssnare slutpunkt som du vill använda är inte redan används. I exemplet nedan heter ”MyEndpoint” och porten är ”1433”.
4. På din lokala klient, ladda ned och installera [senaste PowerShell-modulen](https://azure.microsoft.com/downloads/).
5. Starta **Azure PowerShell**. En ny PowerShell-session öppnas med Azure administrativa moduler som lästs in.
6. Kör **Get-AzurePublishSettingsFile**. Denna cmdlet leder till en webbläsare för att hämta en fil med inställningar i Publicera till en lokal katalog. Du kan uppmanas att dina autentiseringsuppgifter för inloggning för din Azure-prenumeration.
7. Kör den **importera AzurePublishSettingsFile** kommandot med sökvägen till filen publicera inställningar som du hämtade:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    När filen publicera har importerats kan hantera du Azure-prenumerationen i PowerShell-sessionen.
    
1. Kopiera PowerShell-skriptet nedan i en textredigerare och ange värden för variabeln som passar din miljö (standardvärden har angetts för vissa parametrar). Observera att om tillgänglighetsgruppen sträcker sig över Azure-regioner, måste du köra skriptet en gång i varje datacenter för Molntjänsten och noder som tillhör det datacentret.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. När du har angett variablerna kopiera skriptet från textredigerare i Azure PowerShell-sessionen för att köra den. Om uppmaningen visas fortfarande >>, skriver du ange igen och kontrollera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna portar i brandväggen i tillgänglighet gruppnoder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighetsgruppens lyssnare

Skapa tillgänglighetsgruppens lyssnare i två steg. Först skapar klienten åtkomst punkt klusterresursen och konfigurera beroenden. Konfigurera andra klusterresurserna med PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klientåtkomstpunkten och konfigurera kluster-beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera klustrets resurser i PowerShell
1. Extern belastningsutjämning, måste du skaffa offentliga virtuella IP-adressen för den molntjänst som innehåller repliker. Logga in på Azure-portalen. Navigera till den molntjänst som innehåller tillgänglighetsgruppen VM. Öppna den **instrumentpanelen** vyn.
2. Observera den adress som anges under **offentliga virtuella IP-adressen**. Om din lösning sträcker sig över Vnet, kan du upprepa proceduren för varje tjänst i molnet som innehåller en virtuell dator som är värd för en replik.
3. På en av de virtuella datorerna, kopiera PowerShell-skriptet nedan i en textredigerare och ange variablerna de värden som du antecknade tidigare.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. När du har angett variablerna, öppna en kommandotolk i Windows PowerShell och sedan kopiera skriptet från textredigeraren och klistra in i Azure PowerShell-sessionen att köra den. Om uppmaningen visas fortfarande >>, skriver du ange igen och kontrollera att skriptet börjar köras.
5. Upprepa detta för varje virtuell dator. Det här skriptet konfigurerar IP-adressresurs med IP-adressen för Molntjänsten och anger andra parametrar som avsökningsport. När IP-adressresurs är online, svara den sedan på avsökning på avsökningsport från slutpunkten belastningsutjämnade skapade tidigare i den här kursen.

## <a name="bring-the-listener-online"></a>Ta lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljning av objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testa tillgänglighetsgruppens lyssnare (inom samma virtuella nätverk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testa tillgänglighetsgruppens lyssnare (via internet)
För att komma åt lyssnaren från utanför det virtuella nätverket måste du använda externa och offentliga belastningsutjämning (beskrivs i det här avsnittet) i stället för ILB, vilket är endast tillgänglig i samma virtuella nätverk. I anslutningssträngen anger du namnet på molnet. Till exempel om du har en molntjänst med namnet *mycloudservice*, instruktionen sqlcmd blir som följer:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Till skillnad från föregående exempel måste SQL-autentisering användas, eftersom anroparen inte kan använda windows-autentisering via internet. Mer information finns i [alltid på Tillgänglighetsgruppen i Azure VM: klienten anslutning scenarier](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). När du använder SQL-autentisering, se till att du skapar samma inloggningen på båda replikerna. Läs mer om hur du felsöker inloggningar med Tillgänglighetsgrupper [så att mappa inloggningar eller använda innehåller SQL-databasanvändare för att ansluta till andra repliker och mappa tillgänglighetsdatabaserna](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Om Always On-repliker är i olika undernät, måste klienter ange **MultisubnetFailover = True** i anslutningssträngen. Detta resulterar i parallella anslutningsförsök till repliker i olika undernät. Observera att det här scenariot innehåller en distribution för cross-region Always On-Tillgänglighetsgruppen.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

