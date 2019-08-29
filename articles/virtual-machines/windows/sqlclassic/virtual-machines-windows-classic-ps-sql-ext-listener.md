---
title: Konfigurera en extern lyssnare för Always on-tillgänglighetsgrupper | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att skapa en lyssnare för Always on-tillgänglighetsgrupper i Azure som kan nås externt med hjälp av den offentliga virtuella IP-adressen för den associerade moln tjänsten.
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
ms.openlocfilehash: 78881830d4e558daaad6e1929b30287e2731fb1b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100404"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurera en extern lyssnare för Always on-tillgänglighetsgrupper i Azure
> [!div class="op_single_selector"]
> * [Intern lyssnare](../classic/ps-sql-int-listener.md)
> * [Extern lyssnare](../classic/ps-sql-ext-listener.md)
> 
> 

Det här avsnittet visar hur du konfigurerar en lyssnare för en tillgänglighets grupp som alltid är tillgänglig via Internet. Detta görs möjligt genom att associera moln tjänstens **offentliga virtuella IP-adress (VIP)** med lyssnaren.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Din tillgänglighets grupp kan bara innehålla repliker som endast är lokala, endast Azure eller både lokalt och Azure för Hybrid konfigurationer. Azure-replikeringar kan finnas i samma region eller i flera regioner med hjälp av flera virtuella nätverk (virtuella nätverk). Stegen nedan förutsätter att du redan har [konfigurerat en tillgänglighets grupp](../classic/portal-sql-alwayson-availability-groups.md) men inte har konfigurerat en lyssnare.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Rikt linjer och begränsningar för externa lyssnare
Observera följande rikt linjer för tillgänglighets gruppens lyssnare i Azure när du distribuerar med hjälp av den offentliga VIP-adressen för moln tjänsten:

* Tillgänglighets gruppens lyssnare stöds på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Klient programmet måste finnas på en annan moln tjänst än den som innehåller dina virtuella dator namn för tillgänglighets gruppen. Azure stöder inte direkt Server retur med klienten och servern i samma moln tjänst.
* Som standard visar stegen i den här artikeln hur du konfigurerar en lyssnare att använda den virtuella IP-adressen (VIP) för moln tjänsten. Det är dock möjligt att reservera och skapa flera VIP-adresser för din moln tjänst. På så sätt kan du använda stegen i den här artikeln för att skapa flera lyssnare som är associerade med en annan VIP. Information om hur du skapar flera VIP-adresser finns i [flera virtuella IP-adresser per moln tjänst](../../../load-balancer/load-balancer-multivip.md).
* Om du skapar en lyssnare för en hybrid miljö måste det lokala nätverket ha anslutning till det offentliga Internet förutom VPN för plats-till-plats med det virtuella Azure-nätverket. I Azure-undernätet kan tillgänglighets gruppens lyssnare endast uppnås av den offentliga IP-adressen för respektive moln tjänst.
* Det finns inte stöd för att skapa en extern lyssnare i samma moln tjänst där du också har en intern lyssnare som använder den interna Load Balancer (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Avgöra tillgängligheten för lyssnaren
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Den här artikeln fokuserar på att skapa en lyssnare som använder **extern belastnings utjämning**. Om du vill ha en lyssnare som är privat för ditt virtuella nätverk, se den version av artikeln som innehåller steg för att konfigurera en [lyssnare med ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Skapa belastningsutjämnade VM-slutpunkter med direkt Server retur
Extern belastnings utjämning använder den virtuella offentliga virtuella IP-adressen för den moln tjänst som är värd för dina virtuella datorer. Så du behöver inte skapa eller konfigurera belastningsutjämnaren i det här fallet.

Du måste skapa en belastningsutjämnad slut punkt för varje virtuell dator som är värd för en Azure-replik. Om du har repliker i flera regioner måste varje replik för regionen finnas i samma moln tjänst i samma VNet. Att skapa tillgänglighets grupp repliker som sträcker sig över flera Azure-regioner kräver att du konfigurerar flera virtuella nätverk. Mer information om hur du konfigurerar Cross VNet-anslutning finns i [Konfigurera VNet till VNET-anslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. I Azure Portal navigerar du till varje virtuell dator som är värd för en replik och visar informationen.
2. Klicka på fliken **slut punkter** för var och en av de virtuella datorerna.
3. Kontrol lera att **namnet** och den **offentliga porten** för den lyssnare slut punkt som du vill använda inte redan används. I exemplet nedan är namnet "min slut punkt" och porten är "1433".
4. Hämta och installera [den senaste PowerShell-modulen](https://azure.microsoft.com/downloads/)på den lokala klienten.
5. Starta **Azure PowerShell**. En ny PowerShell-session öppnas med de Azure-administrativa moduler som lästs in.
6. Run **Get-AzurePublishSettingsFile**. Denna cmdlet dirigerar dig till en webbläsare för att ladda ned en fil för publicerings inställningar till en lokal katalog. Du kan uppmanas att ange dina inloggnings uppgifter för din Azure-prenumeration.
7. Kör kommandot **import-AzurePublishSettingsFile** med sökvägen till filen med publicerings inställningar som du laddade ned:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    När publicerings inställnings filen har importer ATS kan du hantera din Azure-prenumeration i PowerShell-sessionen.
    
1. Kopiera PowerShell-skriptet nedan till en text redigerare och ange variabel värden som passar din miljö (standardvärden har angetts för vissa parametrar). Observera att om din tillgänglighets grupp sträcker sig över Azure-regioner måste du köra skriptet en gång i varje data Center för moln tjänsten och noder som finns i det data centret.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. När du har angett variablerna kopierar du skriptet från text redigeraren till din Azure PowerShell-session för att köra det. Om meddelandet fortfarande visar > > skriver du retur igen för att kontrol lera att skriptet börjar köras.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrol lera att KB2854082 installeras vid behov
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öppna brand Väggs portarna i tillgänglighets gruppens noder
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighets gruppens lyssnare

Skapa tillgänglighets gruppens lyssnare i två steg. Först skapar du kluster resursen för klient åtkomst punkten och konfigurerar beroenden. Sedan konfigurerar du kluster resurserna med PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Skapa klient åtkomst punkten och konfigurera kluster beroenden
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurera kluster resurserna i PowerShell
1. För extern belastnings utjämning måste du hämta den offentliga virtuella IP-adressen för den moln tjänst som innehåller dina repliker. Logga in på Azure-portalen. Navigera till moln tjänsten som innehåller din tillgänglighets grupps virtuella dator. Öppna vyn **instrument panel** .
2. Observera adressen som visas under **offentlig virtuell IP-adress (VIP)** . Om din lösning omfattar virtuella nätverk upprepar du det här steget för varje moln tjänst som innehåller en virtuell dator som är värd för en replik.
3. På en av de virtuella datorerna kopierar du PowerShell-skriptet nedan till en text redigerare och anger variablerna till de värden som du antecknade tidigare.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. När du har angett variablerna, öppnar du ett upphöjt Windows PowerShell-fönster och kopierar skriptet från text redigeraren och klistrar in det i Azure PowerShell-sessionen för att köra det. Om meddelandet fortfarande visar > > skriver du retur igen för att kontrol lera att skriptet börjar köras.
5. Upprepa detta på varje virtuell dator. Det här skriptet konfigurerar IP-adressresursen med IP-adressen för moln tjänsten och anger andra parametrar som avsöknings port. När IP-adressresursen är online kan den svara på avsökningen på avsöknings porten från den belastningsutjämnade slut punkten som skapades tidigare i den här självstudien.

## <a name="bring-the-listener-online"></a>Ta med lyssnaren online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Uppföljnings objekt
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testa tillgänglighets gruppens lyssnare (inom samma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testa tillgänglighets gruppens lyssnare (via Internet)
För att få åtkomst till lyssnaren utanför det virtuella nätverket måste du använda extern/offentlig belastnings utjämning (beskrivs i det här avsnittet) i stället för ILB, som endast är tillgängligt inom samma VNet. I anslutnings strängen anger du namnet på moln tjänsten. Om du till exempel har en moln tjänst med namnet *mycloudservice*skulle SQLCMD-instruktionen vara följande:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Till skillnad från föregående exempel måste SQL-autentisering användas, eftersom anroparen inte kan använda Windows-autentisering via Internet. Mer information finns [i tillgänglighets gruppen Always on i Azure VM: Klient anslutnings scenarier](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). När du använder SQL-autentisering ser du till att du skapar samma inloggning på båda replikerna. Mer information om hur du felsöker inloggningar med tillgänglighets grupper finns i [så här mappar du inloggningar eller använder SQL Database-användare som är anslutna till andra repliker och mappar till tillgänglighets databaser](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Om Always on-repliker finns i olika undernät måste klienterna ange **MultisubnetFailover = True** i anslutnings strängen. Detta resulterar i parallella anslutnings försök till repliker i de olika under näten. Observera att det här scenariot omfattar en tillgänglighets grupps distribution över flera regioner.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

