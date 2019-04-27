---
title: Installera SAP NetWeaver hög tillgänglighet på en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans i Azure | Microsoft Docs
description: Lär dig hur du installerar SAP NetWeaver hög tillgänglighet på en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1229b7f9e2a430a663a3e78bb457c03cf4a4a590
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714463"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Installera SAP NetWeaver hög tillgänglighet på en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans i Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Den här artikeln beskriver hur du installerar och konfigurerar en SAP-system med hög tillgänglighet i Azure med hjälp av ett redundanskluster i Windows Server och delad klusterdisk för clustering SAP ASCS/SCS-instans.

## <a name="prerequisites"></a>Nödvändiga komponenter

Läs dokumenten innan du påbörjar installationen:

* [Arkitektur-guide: Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk][sap-high-availability-guide-wsfc-shared-disk]

* [Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-shared-disk]

Vi beskriver inte DBMS-konfiguration i den här artikeln eftersom inställningar varierar beroende på DBMS system som du använder. Vi förutsätter att få hög tillgänglighet med DBMS behandlas med funktioner som har stöd för olika DBMS-leverantörer för Azure. Exempel är AlwaysOn eller databasspegling för SQL Server- och Oracle Data Guard för Oracle-databaser. I scenariot som vi använder i den här artikeln har vi inte lägga till bättre skydd för DBMS.

Det finns inga särskilda överväganden vid olika DBMS-tjänster som interagerar med en klustrad SAP ASCS eller SCS-konfiguration i Azure.

> [!NOTE]
> Installationsanvisningar för SAP NetWeaver ABAP-system, Java-system och ABAP + Java-system är nästan identiska. Den viktigaste skillnaden är att en SAP ABAP-system har en ASCS-instans. SAP-Java-systemet har en SCS-instans. SAP ABAP + Java-system har en ASCS-instans och en SCS-instans som körs i samma klustergrupp för Microsoft-redundans. Eventuella skillnader för installation för varje stack för installationen av SAP NetWeaver anges uttryckligen. Du kan anta att alla delar är samma.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installera SAP med en hög tillgänglighet ASCS/SCS-instans

> [!IMPORTANT]
> Glöm inte att placera växlingsfilen på SIOS DataKeeper speglade volymer. DataKeeper stöder inte speglade volymer. Du kan lämna växlingsfilen på den temporära enheten D Azure-dator, vilket är standard. Om den inte redan finns där, flytta växlingsfilen Windows till D-disken på din Azure-dator.
>
>

Installera SAP med en hög tillgänglighet ASCS/SCS-instans innebär att dessa uppgifter:

* Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen.
* Installera den första klusternoden SAP.
* Ändra SAP-profilen för ASCS/SCS-instans.
* Lägg till en avsökningsport.
* Öppna Windows-brandväggen avsökningsporten.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1. Skapa en DNS-post för det virtuella värdnamnet på ASCS/SCS-instans i Windows DNS-hanteraren.

   > [!IMPORTANT]
   > IP-adressen som du tilldelar till virtuella namnet på ASCS/SCS-instans måste vara samma som IP-adress som tilldelats Azure Load Balancer (\<SID\>- lb-ascs).  
   >
   >

   IP-adressen för det virtuella SAP ASCS/SCS-värdnamnet (pr1-ascs-sap) är samma som IP-adressen för Azure Load Balancer (pr1-lb-ascs).

   ![Bild 1: Definiera DNS-posten för virtuella SAP ASCS/SCS-klusternamnet och TCP/IP-adress][sap-ha-guide-figure-3046]

   _**Bild 1:** Definiera DNS-posten för virtuella SAP ASCS/SCS-klusternamnet och TCP/IP-adress_

2. För att definiera IP-adressen som är tilldelad till virtuella värdnamn, Välj **DNS-hanteraren** > **domän**.

   ![Bild 2: Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration][sap-ha-guide-figure-3047]

   _**Bild 2:** Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installera den första klusternoden SAP

1. Kör det första klustra noden alternativet på klusternod A. Till exempel på pr1-ascs-0 * värden.
2. Om du vill behålla standardvärdet portar för intern Azure belastningsutjämnare, väljer du:

   * **ABAP system**: **ASCS** antal instanser **00**
   * **Java system**: **SCS** antal instanser **01**
   * **ABAP + Java system**: **ASCS** antal instanser **00** och **SCS** antal instanser **01**

   Om du vill använda instans siffror än 00 för ASCS ABAP-instansen och 01 för Java SCS-instans du först ändra Azure intern load balancer standard reglerna för belastningsutjämning. Mer information finns i [ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare][sap-ha-guide-8.9].

Nästa några uppgifter beskrivs inte i dokumentationen för standard SAP-installation.

> [!NOTE]
> SAP-installationsdokumentationen beskriver hur du installerar den första ASCS/SCS-klusternoden.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändra SAP-profilen för ASCS/SCS-instans

Lägg först till en ny profil-parameter. Profilparametern förhindrar att anslutningar mellan SAP arbetsprocesser och placera servern stängs när de är inaktiva för länge. Vi nämna problemet scenariot i [lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans][sap-ha-guide-8.11]. I det avsnittet Dessutom presenterar vi två ändringar till vissa grundläggande TCP/IP-anslutningsparametrar. I ett andra steg du måste ange att placera servern kan skicka en `keep_alive` signalera så att anslutningarna inte når Azure interna belastningsutjämnarens inaktiv tröskelvärdet.

Ändra SAP-profilen för ASCS/SCS-instans:

1. Lägg till den här profilparametern i profilen för SAP ASCS/SCS-instans:

   ```
   enque/encni/set_so_keepalive = true
   ```
   I vårt exempel är sökvägen:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Till exempel till SAP SCS-instans profil och motsvarande sökväg:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Starta om SAP ASCS/SCS-instans för att tillämpa ändringarna.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Lägg till en avsökningsport

Använd funktioner för avsökning av den interna belastningsutjämnaren för att se hela klusterkonfigurationen arbeta med Azure Load Balancer. Intern Azure belastningsutjämnare distribuerar vanligtvis inkommande belastningen jämnt mellan deltagande virtuella datorer.

 Men fungerar det inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiva och kan inte acceptera någon av arbetsbelastningen. Det hjälper dig att en avsökning-funktionalitet när intern Azure belastningsutjämnare tilldelar fungerar endast för en aktiv instans. Med funktionen avsökningen kan den interna belastningsutjämnaren identifiera vilka instanser som är aktiva och sedan rikta endast instansen med arbetsbelastningen.

Lägga till en avsökningsport:

1. Kontrollera aktuellt **ProbePort** värde genom att köra följande PowerShell-kommando:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Kör kommandot från en av de virtuella datorerna i klusterkonfigurationen.

2. Definiera en avsökningsport. Avsökningen Standardportnumret är 0. I vårt exempel använder vi avsökningsport 62000.

   ![Bild 3: Avsökningsporten för kluster-konfiguration är 0 som standard][sap-ha-guide-figure-3048]

   _**Bild 3:** Klustret configuration avsökningen standardporten är 0_

   Portnumret är definierad i SAP Azure Resource Manager-mallar. Du kan tilldela portnumret i PowerShell.

   Ange ett nytt värde för ProbePort för SAP \<SID\> IP-klusterresursen, kör följande PowerShell-skript för att uppdatera variablerna PowerShell för din miljö:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   När du tar SAP \<SID\> rollen online-kluster, kontrollerar du att **ProbePort** är inställt på det nya värdet.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   När skriptet har körts uppmanas du att starta om klustergrupp SAP för att aktivera ändringarna.

   ![Bild 4: Avsökningsport klustret när du ställer in det nya värdet][sap-ha-guide-figure-3049]

   _**Bild 4:** Avsökningsport klustret när du ställer in det nya värdet_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öppna avsökningsporten för Windows-brandväggen

Öppna en Windows-brandväggen avsökningsport på båda klusternoderna. Använd följande skript för att öppna en Windows-brandväggen avsökningsporten. Uppdatera PowerShell-variabler för din miljö.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** är inställd på **62000**. Du kan nu komma åt filresursen \\\ascsha-clsap\sapmnt från andra värdar, till exempel från ascsha databasadministratörer.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installera database-instans

Följ processen som beskrivs i dokumentationen för SAP-installation för att installera databasinstansen.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installera den andra noden i klustret

Följ stegen som beskrivs i installationsguiden för SAP för att installera det andra klustret.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Ändra starttypen för SAP ÄNDARE Windows-tjänstinstans

Ändra starttypen för tjänsten SAP ÄNDARE Windows **automatiskt (förskjuten Start)** på båda klusternoderna.

![Bild 5: Ändra tjänsttyp för SAP ÄNDARE-instansen till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Bild 5:** Ändra tjänsttyp för SAP ÄNDARE-instansen till fördröjd automatisk_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installera den primära programservern för SAP

Installera den primära Application Server (PROVIDERADRESSER)-instansen \<SID\>-di-0 på den virtuella datorn som du har angett som värd för PROVIDERADRESSERNA. Det finns inga beroenden på Azure. Det finns inga DataKeeper-specifika inställningar.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installera ytterligare programserver för SAP

Installera en SAP ytterligare Application Server (AAS) på alla virtuella datorer som du har angett för att köra en instans av SAP-programservern. Till exempel på \<SID\>-di-1 till \<SID\>- di -&lt;n&gt;.

> [!NOTE]
> Detta avslutar installationen av ett system för SAP NetWeaver av hög tillgänglighet. Fortsätt sedan med testning av redundans.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testa redundans för SAP ASCS/SCS-instans och SIOS replikering
Det är enkelt att testa och övervaka en växling vid fel för SAP ASCS/SCS-instans och SIOS diskreplikering med hjälp av verktyg för hanteraren för redundanskluster och SIOS DataKeeper hanteringen och konfigurationen.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS-instans som körs på nod A

SAP PR1 klustergrupp körs på noden i klustret A. Till exempel på pr1-ascs-0. Tilldela den dela diskenheten S, som ingår i gruppen SAP PR1 kluster, till klusternod A. ASCS/SCS-instans använder också diskenheten S. 

![Bild 6: Hanteraren för redundanskluster: SAP \<SID\> klustergrupp körs på nod A][sap-ha-guide-figure-5000]

_**Bild 6:** Hanteraren för redundanskluster: SAP \<SID\> klustergrupp körs på nod A_

I verktyget SIOS DataKeeper hanterings- och kan du se att delad disk-data replikeras synkront i från volymen källenheten S på nod A till volymen målenheten S på klusternod B. Till exempel replikeras den från pr1-ascs-0 [10.0.0.40] till pr1-ascs-1 [10.0.0.41].

![Bild 7: I SIOS DataKeeper replikerar du lokala volymen från klusternoden A till klusternod B][sap-ha-guide-figure-5001]

_**Bild 7:** I SIOS DataKeeper replikerar du lokala volymen från klusternoden A till klusternod B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Redundansväxling från A-nod till nod B

1. Välj något av dessa alternativ för att påbörja en växling av SAP \<SID\> klustergrupp från klusternoden A till klusternod B:
   - Hanteraren för redundanskluster  
   - Redundanskluster PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Starta om klustret nod A i Windows-gästoperativsystemet. Detta startar en automatisk redundans för SAP \<SID\> klustergrupp från nod A till nod B.  
3. Starta om klustret nod A från Azure-portalen. Detta startar en automatisk redundans för SAP \<SID\> klustergrupp från nod A till nod B.  
4. Starta om klustret nod A med hjälp av Azure PowerShell. Detta startar en automatisk redundans för SAP \<SID\> klustergrupp från nod A till nod B.

   Efter en redundansväxling, SAP \<SID\> klustergrupp körs på noden i klustret B. Till exempel att den körs på pr1-ascs-1.

   ![Bild 8: I hanteraren för redundanskluster, SAP \<SID\> klustergrupp körs på nod B][sap-ha-guide-figure-5002]

   _**Bild 8**: I hanteraren för redundanskluster, SAP \<SID\> klustergrupp körs på nod B_

   Den delade disken är nu monterad på klustret noden B. SIOS DataKeeper replikera data från volymen källenhet S på nod B till volymen målenheten S på klusternod A. Till exempel replikerar den från pr1-ascs-1 [10.0.0.41] pr1 ascs 0 [10.0.0.40].

   ![Bild 9: SIOS DataKeeper replikerar den lokala volymen från klusternoden B ska ingå i klustret nod A][sap-ha-guide-figure-5003]

   _**Bild 9:** SIOS DataKeeper replikerar den lokala volymen från klusternoden B ska ingå i klustret nod A_
