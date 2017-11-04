---
title: "SAP NetWeaver HA Installation på Windows-redundanskluster och delad Disk för SAP (A) SCS-instansen på Azure | Microsoft Docs"
description: "SAP NetWeaver HA Installation på Windows-redundanskluster och delad Disk för SAP (A) SCS-instans"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 938ab6be6b2ba9e1403919cb62f68c65f114c067
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-shared-disk-for-sap-ascs-instance-on-azure"></a>SAP NetWeaver HA Installation på Windows-redundanskluster och delad Disk för SAP (A) SCS-instansen på Azure

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
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

Det här dokumentet är som beskriver hur du installerar och konfigurerar hög tillgänglighet SAP-system på Azure, med **Failover Cluster WSFC (Windows)** och använder **delad klusterdisk** för kluster SAP (A) SCS-instans.

## <a name="prerequisites"></a>Krav

Se till att granska de här dokumenten innan du börjar med installationen:

* [Arkitektur för Guide - klustring SAP (A) SCS instans på Windows-redundanskluster med hjälp av klustret delad Disk][sap-high-availability-guide-wsfc-shared-disk]

* [Azure förberedelse av infrastruktur för SAP hög tillgänglighet med hjälp av Windows-redundanskluster och delad Disk för SAP (A) SCS-instans][sap-high-availability-infrastructure-wsfc-shared-disk]

Vi beskriver inte DBMS-inställningarna eftersom inställningar varierar beroende på DBMS-system som du använder. Men förutsätter vi att få hög tillgänglighet med DBMS behandlas med funktioner som har stöd för olika DBMS-leverantörer för Azure. Till exempel alltid på eller databasspegling för SQL Server och Oracle Data Guard för Oracle-databaser. I scenariot som vi använder i den här artikeln inte vi lägga till mer skydd DBMS.

Det finns inga särskilda överväganden när olika DBMS tjänster interagera med den här typen av klustrad SAP ASCS/SCS konfiguration i Azure.

> [!NOTE]
> Installationsproceduren för SAP NetWeaver ABAP system, Java-system och ABAP + Java system är nästan identiska. Den viktigaste skillnaden är att ett SAP ABAP system har en ASCS-instans. SAP Java-systemet har en SCS-instans. SAP ABAP + Java-systemet har en ASCS-instans och en SCS-instans som körs i gruppen samma Microsoft för failover-kluster. Installationen skillnader för varje SAP NetWeaver installationen stack anges explicit. Du kan anta att alla delar är samma.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installera SAP med en hög tillgänglighet ASCS/SCS-instans

> [!IMPORTANT]
> Glöm inte att placera växlingsfilen på DataKeeper speglade volymer. DataKeeper stöder inte speglade volymer. Du kan lämna växlingsfilen på den tillfälliga enheten D av en virtuell Azure-dator, vilket är standard. Om den inte redan finns där, flytta växlingsfilen till enhet D på ditt Azure-datorn.
>
>

Dessa uppgifter omfattar att installera SAP med en hög tillgänglighet ASCS/SCS-instans:

* Skapa ett virtuellt värdnamn för den klustrade instansen SAP ASCS/SCS
* Installera den första klusternoden SAP
* Ändra SAP-profilen för ASCS/SCS-instans
* Lägger till en avsökningsport
* Öppna avsökningsport för Windows-brandväggen

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1.  Skapa en DNS-post för virtuella värdnamn för ASCS/SCS-instansen i Windows DNS-hanteraren.

  > [!IMPORTANT]
  > IP-adressen som du tilldelar virtuella värdnamn för ASCS/SCS-instansen måste vara samma som IP-adressen som du tilldelade till Azure belastningsutjämnare (**<*SID*> - lb - ascs **).  
  >
  >

  IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS (**pr1-ascs-sap**) är samma som IP-adressen för Azure belastningsutjämnare (**pr1-lb-ascs**).

  ![Bild 1: Definiera DNS-posten för SAP ASCS/SCS virtuell klusternamnet och TCP/IP-adress][sap-ha-guide-figure-3046]

  _**Bild 1:** definiera DNS-posten för SAP ASCS/SCS virtuell klusternamnet och TCP/IP-adress_

2.  Ange IP-adress som tilldelats till namnet på virtuella värddatorn, välja **DNS-hanteraren** > **domän**.

  ![Bild 2: Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS klusterkonfiguration][sap-ha-guide-figure-3047]

  _**Bild 2:** nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS klusterkonfiguration_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Installera den första klusternoden SAP

1.  Kör det första klustra nod alternativet på klusternoden A. Till exempel på den **pr1-ascs-0** värden.
2.  Om du vill behålla standard portar för den interna belastningsutjämnaren som Azure, väljer du:

  * **ABAP system**: **ASCS** instansen nummer **00**
  * **Java system**: **SCS** instansen nummer **01**
  * **ABAP + Java system**: **ASCS** instansen nummer **00** och **SCS** instansen nummer **01**

  För att använda instansen siffror än 00 för ABAP ASCS-instansen och 01 för Java-SCS beskrivs instans, första du behöver ändra den Azure interna standard belastningsutjämnare NLB-regler i [ändra reglerna för belastningsutjämning för ASCS/SCS standard för Azure intern belastningsutjämnare] [sap-hög tillgänglighet-guide-8,9].

Nästa några åtgärder beskrivs inte i dokumentationen för standard SAP-installationen.

> [!NOTE]
> I dokumentationen för installation av SAP beskriver hur du installerar den första noden i klustret ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Ändra SAP-profil för ASCS/SCS-instansen

Du måste lägga till en ny profil-parameter. Parametern profil förhindrar anslutningar mellan SAP arbetsprocesser och placera servern stängs när de är inaktiv för länge. Vi har nämnt problemet i [Lägg till registerposter på båda klusternoderna för SAP ASCS/SCS-instans] [sap-hög tillgänglighet-guide-8.11]. I det här avsnittet introduceras vi några grundläggande TCP/IP-anslutningsparametrar också två ändringar. I nästa steg behöver du sätta servern ska skicka en `keep_alive` signalerar så att anslutningarna inte nått Azure interna belastningsutjämnarens inaktiv tröskelvärdet.

Ändra SAP-profilen för ASCS/SCS-instans:

1.  Lägg till den här profilen parametern SAP ASCS/SCS instans-profilen:

  ```
  enque/encni/set_so_keepalive = true
  ```
  I vårt exempel är sökvägen:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Till exempel till SCS SAP instans profil och motsvarande sökväg:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Om du vill tillämpa ändringarna SAP ASCS /SCS-instansen startas om.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Lägga till en avsökning-Port

Använd den interna belastningsutjämnaren avsökningen funktioner för att göra hela klusterkonfigurationen arbeta med Azure belastningsutjämnare. Azure intern belastningsutjämnare distribuerar vanligtvis inkommande belastningen jämnt mellan deltagande virtuella datorer. Men fungerar det inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiva och kan inte ta emot någon av arbetsbelastningen. Det hjälper dig att en avsökning funktionalitet när Azure intern belastningsutjämnare tilldelar fungerar endast för en aktiv instans. Med funktionen avsökningen identifiera den interna belastningsutjämnaren vilka instanser som är aktiva och rikta endast instansen med arbetsbelastningen.

Lägg till en avsökningsport:

1.  Kontrollera aktuellt **ProbePort** inställningen genom att köra följande PowerShell-kommando:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Köra den från inom någon av de virtuella datorerna i klusterkonfigurationen.

2.  Definiera en avsökningsport. Avsökningen Standardportnumret är **0**. I vårt exempel vi använder avsökningsport **62000**.

  ![Bild 3: Avsökningsport för kluster-konfigurationen är 0 som standard][sap-ha-guide-figure-3048]

  _**Bild 3:** klustret configuration avsökningen standardporten är 0_

  Portnumret är definierad i SAP Azure Resource Manager-mallar. Du kan tilldela portnummer i PowerShell.

  Ange ett nytt ProbePort värde för den  **SAP <*SID*> IP ** klusterresurs, kör följande PowerShell-skript för att uppdatera variablerna PowerShell för din miljö:

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

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

  När du tar den  **SAP <*SID*> ** rollen online bör du kontrollera att **ProbePort** är inställd på det nya värdet.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  När skriptet körs, uppmanas du att starta om SAP klustergrupp för att aktivera ändringarna.

  ![Bild 4: Avsökning porten klustret när du ställer in det nya värdet][sap-ha-guide-figure-3049]

  _**Bild 4:** avsökning porten klustret när du ställer in det nya värdet_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Öppna Avsökningsport för Windows-brandväggen

Du måste öppna en Windows-brandväggen avsökningsport på båda klusternoderna. Använd följande skript för att öppna en Windows-brandväggen avsökningsport. Uppdatera variablerna PowerShell för din miljö.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

Den **ProbePort** är inställd på **62000**. Nu kan du komma åt filresursen  **\\\ascsha-clsap\sapmnt** från andra värdar, exempel från **ascsha dbas**.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Installera databasinstansen

För att installera databasinstansen, följer du processen som beskrivs i dokumentationen för SAP-installationen.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Installera den andra noden i klustret

Följ stegen i installationsguiden för SAP för att installera det andra klustret.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Ändra starttypen för SAP ÄNDARE Windows Service-instans

Ändra starttypen för tjänsten SAP ÄNDARE Windows att **automatiskt (förskjuten Start)** på båda klusternoderna.

![Bild 5: Ändra service-typen för instansen SAP ÄNDARE till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Bild 5:** ändra tjänsttypen för SAP ÄNDARE-instans till fördröjd automatisk_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Installera den primära SAP-programservern

Installera den primära programmet Server (PROVIDERADRESSER)-instansen <*SID*> - di - 0 på den virtuella datorn som du har angett som värd för PROVIDERADRESSERNA. Det finns inga beroenden på Azure eller DataKeeper-specifika inställningar.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Installera ytterligare programservern för SAP

Installera en SAP ytterligare program Server (AAS) på alla virtuella datorer som du har angett som värd för en SAP Application Server-instans. Till exempel på <*SID*> - di - 1 till <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Installationen av ett system med hög tillgänglighet SAP NetWeaver slutförs. Fortsätt sedan med testning av redundans.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testa redundans för SAP ASCS/SCS-instans och SIOS replikering
Det är enkelt att testa och övervaka en växling vid fel för SAP ASCS/SCS-instans och SIOS diskreplikering med hjälp av verktyg för hanteraren för redundanskluster och SIOS DataKeeper hantering och konfiguration.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS instans körs på en nod i klustret

Den **SAP PR1** klustergrupp körs på klusternoden A. Till exempel på **pr1-ascs-0**. Tilldela den delade diskenheten S, som är en del av den **SAP PR1** klustergrupp och som använder ASCS/SCS-instans för klustret nod A.

![Bild 6: Hanteraren för redundanskluster: det SAP < SID > klustergrupp körs på en nod i klustret][sap-ha-guide-figure-5000]

_**Bild 6:** Klusterhanterare för växling vid fel: det SAP <*SID*> klustergrupp körs på en nod i klustret_

I verktyget SIOS DataKeeper hantering och konfiguration kan du se att delad diskdata replikeras synkront i från volymen källenheten S på en nod i klustret till volymen målenheten S på klusternoden B. Till exempel har replikerats från **pr1-ascs-0 [10.0.0.40]** till **pr1-ascs-1 [10.0.0.41]**.

![Bild 7: I SIOS DataKeeper replikera lokala volymen från en nod i klustret till klusternod B][sap-ha-guide-figure-5001]

_**Bild 7:** i SIOS DataKeeper replikera lokala volymen från en nod i klustret till klusternoden B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Växling vid fel från nod A till B-nod

1.  Välj något av dessa alternativ för att påbörja en växling av SAP <*SID*> klustergrupp från klusternoden A klusternoden B:
  - Använd hanteraren för redundanskluster  
  - Använda PowerShell för Failover-kluster

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Starta om klustret nod A Windows-gästoperativsystem (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).  
3.  Starta om klustret nod A från Azure-portalen (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).  
4.  Starta om klustret nod A med hjälp av Azure PowerShell (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).

  Efter växling vid fel, SAP <*SID*> klustergrupp körs på klusternoden B. Till exempel den körs på **pr1-ascs-1**.

  ![Bild 8: I Hanteraren för redundanskluster, SAP < SID > klustergrupp körs på klusternoden B][sap-ha-guide-figure-5002]

  _**Figur 8**: I Hanteraren för redundanskluster, SAP <*SID*> klustergrupp körs på klusternoden B_

  Den delade disken nu är monterad på klustret nod B. SIOS DataKeeper replikerar data från volymen källenheten S på klusternoden B till volymen målenheten S på klusternoden A. Till exempel den replikerar från **pr1-ascs-1 [10.0.0.41]** till **pr1-ascs-0 [10.0.0.40]**.

  ![Bild 9: SIOS DataKeeper replikerar den lokala volymen från klusternoden B klustra nod A][sap-ha-guide-figure-5003]

  _**Bild 9:** SIOS DataKeeper replikerar den lokala volymen från klusternoden B klustra nod A_
