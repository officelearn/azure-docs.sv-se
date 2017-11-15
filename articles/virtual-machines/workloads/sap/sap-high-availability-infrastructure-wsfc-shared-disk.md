---
title: "Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för SAP ASCS/SCS | Microsoft Docs"
description: "Lär dig hur du förbereder Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av ett redundanskluster i Windows och delad disk för en SAP ASCS/SCS-instans."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2547d40ac39bc8188511f6682911fa302cf3e8a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

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
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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


> ![Windows][Logo_Windows] Windows
>

Den här artikeln beskriver de steg du vidta för att förbereda Azure-infrastrukturen för att installera och konfigurera ett SAP-system med hög tillgänglighet i ett Windows-redundanskluster med hjälp av en *delad klusterdisk* som ett alternativ för klustring en SAP ASCS-instans.

## <a name="prerequisites"></a>Krav

Innan du påbörjar installationen, kan du läsa den här artikeln:

* [Arkitektur-guide: kluster en SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbereda infrastrukturen för arkitektur mall 1
Azure Resource Manager-mallar för SAP underlätta distributionen av de nödvändiga resurserna.

Trelagers-mallar i Azure Resource Manager stöder också scenarier för hög tillgänglighet. Till exempel har arkitekturen mall 1 två kluster. Varje kluster är ett SAP felpunkt för SAP ASCS/SCS och DBMS.

Här är där du kan hämta Azure Resource Manager-mallar för exempelscenario som beskrivs i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-avbildning med hjälp av Azure hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Förbereda infrastrukturen för arkitektur mall 1:

- I Azure-portalen i den **parametrar** rutan i den **SYSTEMAVAILABILITY** väljer **HA**.

  ![Bild 1: Ange parametrar för SAP hög tillgänglighet Azure Resource Manager][sap-ha-guide-figure-3000]

_**Bild 1:** ange parametrar för SAP hög tillgänglighet Azure Resource Manager_


  Skapa mallar:

  * **Virtuella datorer**:
    * SAP programserver virtuella datorer: \<SAPSystemSID\>- di -\<tal\>
    * ASCS/SCS klustra virtuella datorer: \<SAPSystemSID\>- ascs-\<tal\>
    * DBMS-kluster: \<SAPSystemSID\>- db-\<tal\>

  * **Nätverkskort för alla virtuella datorer med tillhörande IP-adresser**:
    * \<SAPSystemSID\>- nic-di -\<tal\>
    * \<SAPSystemSID\>- nic-ascs -\<tal\>
    * \<SAPSystemSID\>- nic-db -\<tal\>

  * **Azure storage-konton (endast ohanterade diskar)**:

  * **Tillgänglighetsgrupper** för:
    * SAP programserver virtuella datorer: \<SAPSystemSID\>- avset di
    * SAP ASCS/SCS klustra virtuella datorer: \<SAPSystemSID\>ascs - avset
    * DBMS klustra virtuella datorer: \<SAPSystemSID\>- avset-db

  * **Azure intern belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instansen och IP-adress \<SAPSystemSID\>ascs - lb
    * Med alla portar för SQL Server DBMS och IP-adressen \<SAPSystemSID\>- lb-db

  * **Nätverkssäkerhetsgruppen**: \<SAPSystemSID\>- nsg-ascs-0  
    * Med en öppen externa Remote Desktop Protocol (RDP)-port till den \<SAPSystemSID\>- ascs-0 virtuell dator

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare är dynamiska som standard. Ändra dem till den statiska IP-adresser. Vi beskriver hur du gör det senare i artikeln.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Distribuera virtuella datorer med företagets nätverksanslutning (mellan platser) ska användas i produktion
Distribuera virtuella Azure-datorer med för produktion SAP-system, [företagets nätverksanslutning (mellan platser)] [ planning-guide-2.2] med hjälp av Azure VPN-Gateway eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instans. Virtuellt nätverk och undernät har redan skapats och förberetts.
>
>

1.  I Azure-portalen i den **parametrar** rutan i den **NEWOREXISTINGSUBNET** väljer **befintliga**.
2.  I den **SUBNETID** Lägg till fullständig sträng med din förberedda Azure-nätverk undernäts-ID om du planerar att distribuera din virtuella Azure-datorer.
3.  Hämta en lista över alla undernät i Azure-nätverk, Kör PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Den **ID** fältet visas värdet för undernät-ID.
4. Om du vill hämta en lista över alla undernät-ID-värden, Kör PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Undernät-ID som ser ut så här:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Distribuera endast molnbaserad SAP-instanser för testning och demo
Du kan distribuera din SAP-systemet för hög tillgänglighet i en endast molnbaserad distributionsmodell. Den här typen av distribution är främst användbart för demonstration och testa användningsområden. Det är inte lämpligt för produktion användningsfall.

- I Azure-portalen i den **parametrar** rutan i den **NEWOREXISTINGSUBNET** väljer **nya**. Lämna den **SUBNETID** fältet tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt den virtuella Azure-nätverk och undernät.

> [!NOTE]
> Du måste distribuera minst en dedikerad virtuell dator för Active Directory och DNS-tjänsten i samma Azure Virtual Network-instans. Mallen skapas inte de virtuella datorerna.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbereda infrastrukturen för arkitektur mall 2

Du kan använda den här Azure Resource Manager-mallen för SAP för att underlätta distributionen av infrastrukturresurser för SAP arkitektur mall 2.

Här är där du kan hämta Azure Resource Manager-mallar för det här distributionsscenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbereda infrastrukturen för arkitektur mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för multi-SID. Du kan exempelvis lägga till en ytterligare SAP ASCS/SCS-instans i en *befintliga* klusterkonfigurationen. Mer information finns i [konfigurera en ytterligare SAP ASCS/SCS-instans för en befintlig klusterkonfiguration att skapa en konfiguration för SAP multi-SID i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster, kan du använda multi-SID [snabbstartsmallar på GitHub](https://github.com/Azure/azure-quickstart-templates).

Om du vill skapa ett nytt multi-SID-kluster måste du distribuera följande tre mallar:

* [ASCS/SCS mall](#ASCS-SCS-template)
* [Databasmall för](#database-template)
* [Mall för servrar](#application-servers-template)

I följande avsnitt har mer information om mallar och parametrar som du måste ange i mallar.

### <a name="ASCS-SCS-template"></a>ASCS/SCS mall

ASCS/SCS mallen distribuerar två virtuella datorer som du kan använda för att skapa ett redundanskluster i Windows Server som är värd för flera ASCS/SCS-instanser.

Att konfigurera mallen ASCS/SCS multi-SID i den [ASCS/SCS multi-SID-mall] [ sap-templates-3-tier-multisid-xscs-marketplace-image] eller [ASCS/SCS multi-SID-mall med hjälp av hanterade diskar] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], ange värden för följande parametrar:

  - **Resursen prefixet**: Ange resurs-prefixet, vilket är visas för alla resurser som har skapats under distributionen. Resurserna som inte hör till endast en SAP-system, är prefixet för resursen inte eftersom SID för en SAP-system.  Prefixet måste vara mellan tre och sex tecken.
  - **Stacken typen**: Ange hur stack SAP-system. Beroende på stack-typ har Azure belastningsutjämnare en (ABAP eller endast Java) eller två (ABAP + Java) privata IP-adresser per SAP-system.
  -  **OS-typen**: Välj operativsystem för de virtuella datorerna.
  -  **SAP System antal**: Välj antalet SAP-system som du vill installera i det här klustret.
  -  **Filsystemets tillgänglighet**: Välj **HA**.
  -  **Admin Username och adminlösenord**: skapa en ny användare som kan användas för att logga in på datorn.
  -  **Nytt eller befintligt undernät**: Ange om du vill skapa ett nytt virtuellt nätverk och undernät eller använda ett befintligt undernät. Om du redan har ett virtuellt nätverk som är anslutna till ditt lokala nätverk väljer **befintliga**.
  -  **Undernät-Id**: Ange ID för det undernät som de virtuella datorerna ska anslutas. Välj undernätet i ditt VPN eller ExpressRoute virtuella nätverk att ansluta den virtuella datorn till ditt lokala nätverk. ID: T ser oftast ut så här:

   /subscriptions/\<prenumerations-id\>/resourceGroups/\<resursgruppnamn\>/providers/Microsoft.Network/virtualNetworks/\<virtuella nätverksnamnet\>/subnets/ \<undernätsnamn\>

Mallen distribuerar en belastningsutjämnare för Azure-instans som har stöd för flera SAP-system:

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS sätta replikering Server (ERS) (endast Linux)-instanserna är konfigurerade för instansnummer 02, 12, 22...
- SCS ERS (endast Linux)-instanserna är konfigurerade för instansnummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 VIP(s) (2 för Linux) och 1 x-VIP för ASCS/SCS. 1 x-VIP för ÄNDARE (endast Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS portar
Följande lista innehåller alla belastningsutjämningsregler (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- Windows-specifika portar för varje system för SAP: 445, 5985
- ASCS-portar (instansnummer x0): 32 x 0, 36 x 0, 39 x 0, 81 0 x, 5 x 013, 5 x 014, 5 x 016
- SCS portar (instansnummer x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASCS ERS portar på Linux (instansnummer x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS ERS portar på Linux (instansnummer x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Belastningsutjämnaren har konfigurerats för att använda följande avsökningen portar (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- ASCS/SCS interna läsa in belastningsutjämning avsökningsport: 620 x 0
- ÄNDARE interna läsa in belastningsutjämning avsökningsport (endast Linux): 621 x 2

### <a name="database-template"></a>Databasmall för

Mallen databasen distribuerar en eller två virtuella datorer som du kan använda för att installera systemet för relationsdatabashantering (RDBMS) för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Att konfigurera mallen databasen multi-SID i den [multi-SID-mall för databas] [ sap-templates-3-tier-multisid-db-marketplace-image] eller [databasen multi-SID-mall med hjälp av hanterade diskar] [ sap-templates-3-tier-multisid-db-marketplace-image-md], ange värden för följande parametrar:

  -  **SAP System-Id**: Ange SAP system-ID för SAP-systemet som du vill installera. ID som används som ett prefix för de resurser som har distribuerats.
  -  **OS-typen**: Välj operativsystem för de virtuella datorerna.
  -  **DbType**: Välj typ av databas som du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på de virtuella datorerna. Kontrollera att du väljer rätt operativsystem. Välj **Windows** för SQL och välj en Linux-distributionsplats för HANA. Azure belastningsutjämnare som är anslutna till de virtuella datorerna är konfigurerad för att stödja den valda databastyp:
    * **SQL**: belastningen belastningsutjämnaren belastningsutjämna port 1433. Se till att använda den här porten för SQL Server AlwaysOn-installationen.
    * **HANA**: belastningen belastningsutjämnaren belastningsutjämna portarna 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
    Belastningsutjämnaren använder avsökningsport 62550.
  -  **SAP systemstorlek**: Ange hur många SAP som innehåller det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren.
  -  **Filsystemets tillgänglighet**: Välj **HA**.
  -  **Admin Username och adminlösenord**: skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**: Ange ID för det undernät som du använde under distributionen av mallen ASCS/SCS eller ID för det undernät som har skapats som en del av malldistribution ASCS/SCS.

### <a name="application-servers-template"></a>Mall för servrar

Servrar programmall distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Att ställa in programmet servrar multi-SID-mall i den [programmall servrar multi-SID] [ sap-templates-3-tier-multisid-apps-marketplace-image] eller [mall multi-SID för servrar med hjälp av hanterade diskar] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], ange värden för följande parametrar:

  -  **SAP System-Id**: Ange SAP system-ID för SAP-systemet som du vill installera. ID som används som ett prefix för de resurser som har distribuerats.
  -  **OS-typen**: Välj operativsystem för de virtuella datorerna.
  -  **SAP systemstorlek**: antalet SAP som innehåller det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren.
  -  **Filsystemets tillgänglighet**: Välj **HA**.
  -  **Admin Username och adminlösenord**: skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**: Ange ID för det undernät som du använde under distributionen av mallen ASCS/SCS eller ID för det undernät som har skapats som en del av malldistribution ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuella Azure-nätverket
I vårt exempel är adressutrymmet på Azure Virtual Network-instansen 10.0.0.0/16. Det finns ett undernät som kallas undernät, med en-adressintervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare har distribuerats i det här virtuella nätverket.

> [!IMPORTANT]
> Inte göra ändringar i nätverksinställningar i gästoperativsystemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera nätverksinställningarna i Azure. Tjänsten DHCP Dynamic Host Configuration Protocol () sprider dina inställningar.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP-adresser

Om du vill ange krävs DNS IP-adresser, gör du följande:

1.  I Azure-portalen i den **DNS-servrar** rutan kontrollerar du att det virtuella nätverket **DNS-servrar** alternativet är inställt på **anpassad DNS**.
2.  Välj dina inställningar beroende på vilken typ av nätverk som du har. Mer information finns i följande resurser:
    * [Företagets nätverksanslutning (mellan platser)][planning-guide-2.2]: Lägg till IP-adresserna för de lokala DNS-servrarna.  
    Du kan utöka lokala DNS-servrar till de virtuella datorerna som körs i Azure. Du kan lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten i det scenariot.
    * [Endast molnbaserad distribution][planning-guide-2.1]: distribuera en ytterligare virtuell dator i samma virtuella nätverk-instans som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har angett till DNS-tjänsten körs.

    ![Bild 2: Konfigurera DNS-servrar för Azure-nätverk][sap-ha-guide-figure-3001]

    _**Bild 2:** konfigurera DNS-servrar för Azure-nätverk_

  > [!NOTE]
  > Du måste starta om de virtuella Azure-datorerna för att tillämpa ändringen och sprida de nya DNS-servrarna om du ändrar IP-adresser för DNS-servrar.
  >
  >

I vårt exempel har DNS-tjänsten installerats och konfigurerats på dessa virtuella Windows-datorer:

| Rollen virtuell dator | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr 0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-server |domcontr 1 |PR1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Värdnamn och statiska IP-adresser för SAP ASCS/SCS klustrade instansen och DBMS klustrad instans

För lokal distribution måste dessa reserverade värdnamn och IP-adresser:

| Virtuell värd namn roll | Virtuella värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första klustret virtuellt värdnamn (för hantering av kluster) |PR1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS virtuell värd instansnamn |PR1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (hantering) |PR1-dbms-vir |10.0.0.32 |

Skapa den virtuella värden namn pr1-ascs-vir och pr1-dbms-vir och tillhörande IP-adresser som hanterar själva klustret när du skapar klustret. Information om hur du gör detta finns [samla in klusternoder i en klusterkonfiguration][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Du kan manuellt skapa de andra två virtuella värdnamn, sap-ascs-pr1 och pr1 dbms sap, och de associerade IP-adresserna, DNS-servern. Använd dessa resurser den klustrade instansen SAP ASCS/SCS och den klustrade DBMS-instansen. Information om hur du gör detta finns [skapa ett virtuellt värdnamn för en klustrad instans av SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ange statiska IP-adresser för de virtuella datorerna för SAP
När du distribuerar virtuella datorer i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1.  Välj i Azure-portalen **resursgruppen** > **nätverkskort** > **inställningar** > **IP-adress**.
2.  I den **IP-adresser** rutan under **tilldelning**väljer **statiska**. I den **IP-adress** ange IP-adressen som du vill använda.

  > [!NOTE]
  > Om du ändrar IP-adressen för nätverkskortet, måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen.  
  >
  >

  ![Bild 3: Ange statiska IP-adresser för nätverkskort på varje virtuell dator][sap-ha-guide-figure-3002]

  _**Bild 3:** ange statiska IP-adresser för nätverkskort på varje virtuell dator_

  Upprepa det här steget för alla nätverksgränssnitt som är, för alla virtuella datorer, inklusive virtuella datorer som du vill använda för din Active Directory- eller DNS-tjänst.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen virtuell dator | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första serverinstansen för SAP-program |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste serverinstansen för SAP-program |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |PR1-ascs-0 |PR1-nic-ascs-0 |10.0.0.40 |
| Andra klusternod för ASCS/SCS-instans |PR1-ascs-1 |PR1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |PR1-db-0 |PR1-nic-db-0 |10.0.0.30 |
| Andra klusternod för DBMS-instans |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ange en statisk IP-adress för den interna belastningsutjämnaren som Azure

SAP Azure Resource Manager-mallen skapar en Azure intern belastningsutjämnare som används för det SAP ASCS/SCS instans och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren SAP ASCS/SCS: pr1-lb-ascs.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna belastningsutjämnaren DBMS: pr1-lb-dbms.
>
>

Ange en statisk IP-adress för den interna belastningsutjämnaren som Azure:

1.  Den första distributionen anger interna IP-adressen för belastningsutjämnaren till **dynamiska**. I Azure-portalen på den **IP-adresser** rutan under **tilldelning**väljer **statiska**.
2.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS-instansen.
3.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** IP-adressen för den virtuella värdnamnet för DBMS-instansen.

  ![Bild 4: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans][sap-ha-guide-figure-3003]

  _**Bild 4:** ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans_

I vårt exempel har vi två Azure interna belastningsutjämnare som har dessa statiska IP-adresser:

| Azure interna belastningsutjämnarrollen | Azure interna belastningsutjämnarens namn | Statisk IP-adress |
| --- | --- | --- |
| Interna belastningsutjämnare för SAP ASCS/SCS-instans |ascs-lb-PR1 |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |PR1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standard ASCS/SCS belastningsutjämningsregler för Azure interna belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med instans Standardnumret 00
* En Java-SCS-instans med standard-instansnummer 01

När du installerar din SAP ASCS/SCS-instans måste du använda instansen Standardnumret 00 för ABAP ASCS-instansen och instansen Standardnumret 01 för din Java SCS-instans.

Skapa sedan de nödvändiga interna belastningsutjämning slutpunkter för SAP NetWeaver-portar.

Om du vill skapa nödvändiga intern belastningsutjämning slutpunkter först skapa dessa slutpunkter för SAP NetWeaver ABAP ASCS-portar för belastningsutjämning:

| Tjänsten/belastningen belastningsutjämning Regelnamn | Standardportnumren. | Konkret portar för (ASCS-instans med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Sätta server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP meddelande server / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Internt ABAP meddelande / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Meddelandet serverns HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP starta tjänsten ASCS HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP starta tjänsten ASCS HTTPS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Sätta replikering / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

**Tabell 1:** portnummer för SAP NetWeaver ABAP ASCS-instanser

Skapa sedan dessa slutpunkter för SAP NetWeaver Java SCS-portar för belastningsutjämning:

| Tjänsten/belastningen belastningsutjämning Regelnamn | Standardportnumren. | Konkret portar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Sätta server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Gateway-server / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java meddelandet server / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Meddelandet serverns HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP starta tjänsten SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP starta tjänsten SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Sätta replikering / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

**Tabell 2:** portnummer för SAP NetWeaver Java SCS-instanser

![Bild 5: Standard ASCS/SCS belastningsutjämningsregler för Azure interna belastningsutjämnare][sap-ha-guide-figure-3004]

_**Bild 5:** standard ASCS/SCS regler för Azure intern belastningsutjämnare för belastningsutjämning_

Ange IP-adressen för load balancer pr1-lb-dbms IP-adressen för den virtuella värdnamnet för DBMS-instansen.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare

Om du vill använda andra värden för SAP ASCS eller SCS instanser måste du ändra namn och värden på deras hamnar från standardvärdena.

1.  Välj i Azure-portalen  **\<SID\>ascs - lb belastningsutjämnare** > **regler för att läsa in belastningsutjämning**.
2.  För alla belastningsutjämningsregler som tillhör instansen SAP ASCS eller SCS, ändra dessa värden:

  * Namn
  * Port
  * Backend-port

  Om du vill ändra standardvärdet för ASCS-instans från 00 och 31, måste du göra ändringar för alla portar som anges i tabell 1.

  Här är ett exempel på en uppdatering för port *lbrule3200*.

  ![Bild 6: Ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare][sap-ha-guide-figure-3005]

  _**Bild 6:** ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Lägga till Windows-datorer i domänen

När du tilldelar en statisk IP-adress till de virtuella datorerna kan du lägga till de virtuella datorerna till domänen.

![Bild 7: Lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Bild 7:** lägga till en virtuell dator i en domän_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Lägg till registerposter på båda klusternoderna för SAP ASCS/SCS-instansen

Azure belastningsutjämnare har en intern belastningsutjämnare att stängs anslutningar när anslutningar är inaktiva för en viss tid (en timeout för inaktivitet). SAP arbetsprocesser i dialogrutan instanser öppna anslutningar till SAP sätta bearbeta så snart som de första sätta/status Created begäran måste skickas. Dessa anslutningar förblir oftast etablerade förrän arbetsprocessen eller sätta processen startas om. Om anslutningen är inaktiv under en angiven tidsperiod, stängs den interna belastningsutjämnaren som Azure anslutningar. Detta är inte ett problem Eftersom arbetsprocessen för SAP återupprättar anslutningen till sätta processen om det inte längre finns. Dessa aktiviteter finns dokumenterade i developer spår av SAP processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra TCP/IP `KeepAliveTime` och `KeepAliveInterval` på båda klusternoderna. Kombinera ändringarna i TCP/IP-parametrar med SAP profil-parametrar som beskrivs senare i artikeln.

För att lägga till poster i registret på båda klusternoderna för SAP ASCS/SCS-instansen, Lägg först till dessa registerposter i Windows på båda klusternoderna i Windows för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabeltyp |REG_DWORD (decimalt) |
| Värde |120000 |
| Länka till dokumentationen |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabell 3:** ändra den första parametern för TCP/IP

Lägg sedan till den här registerposten för Windows på båda klusternoderna i Windows för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabeltyp |REG_DWORD (decimalt) |
| Värde |120000 |
| Länka till dokumentationen |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

**Tabell 4:** ändra andra TCP/IP-parameter

Starta om båda klusternoderna för att tillämpa ändringarna.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurera ett redundanskluster i Windows Server för en SAP ASCS/SCS-instans

Konfigurera ett redundanskluster i Windows Server för en SAP ASCS/SCS-instans innebär att dessa uppgifter:

- Samla in klusternoderna i en klusterkonfiguration.
- Konfigurera ett filresursvittne för klustret.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Samla in klusternoderna i en klusterkonfiguration

1.  Guiden Lägg till roller och funktioner, lägga till redundansklustring till båda klusternoderna.
2.  Ställ in failover-kluster med hjälp av hanteraren för redundanskluster. I hanteraren för redundanskluster, Välj **Skapa kluster**, och Lägg sedan till endast första klustrets namn (nod A). Lägg inte till den andra noden ännu; Du kan lägga till den andra noden i ett senare steg.

  ![Bild 8: Lägg till namnet på servern eller den virtuella datorn på den första noden i klustret][sap-ha-guide-figure-3007]

  _**Bild 8:** Lägg till namnet på servern eller den virtuella datorn på den första noden i klustret_

3.  Ange nätverksnamn (virtuellt värdnamn) i klustret.

  ![Bild 9: Ange klusternamnet][sap-ha-guide-figure-3008]

  _**Bild 9:** ange klusternamnet_

4.  När du har skapat klustret, kör du ett klustervalideringstest.

  ![Figur 10: Kör verifieringen kluster][sap-ha-guide-figure-3009]

  _**Figur 10:** kör verifieringen kluster_

  Du kan ignorera alla varningar om diskarna nu i processen. Du ska lägga till ett filresursvittne och SIOS delade diskar senare. I det här skedet behöver du inte bekymra dig om att ha ett kvorum.

  ![Figur 11: Inga kvorumdisken hittas][sap-ha-guide-figure-3010]

  _**Figur 11:** inga kvorumdisken hittas_

  ![Figur 12: En core klusterresurs måste en ny IP-adress][sap-ha-guide-figure-3011]

  _**Figur 12:** en core klusterresurs måste en ny IP-adress_

5.  Ändra IP-adressen för klustertjänsten kärnor. Klustret kan inte starta tills du ändrar IP-adressen för klustertjänsten core eftersom IP-adressen för servern som pekar på en av noderna virtuell dator. Gör detta på den **egenskaper** sidan core klustertjänsten IP-resurs.

  Till exempel måste vi du tilldela en IP-adress (i vårt exempel 10.0.0.42) för det kluster virtuell värd namnet pr1-ascs-vir.

  ![Figur 13: I dialogrutan Egenskaper ändra IP-adressen][sap-ha-guide-figure-3012]

  _**Figur 13:** i den **egenskaper** dialogrutan Ändra IP-adress_

  ![Figur 14: Tilldela IP-adress som är reserverad för klustret][sap-ha-guide-figure-3013]

  _**Figur 14:** tilldela IP-adress som är reserverad för klustret_

6.  Placera det virtuella värden klusternamnet.

  ![Figur 15: Core klustertjänsten är igång, med rätt IP-adress][sap-ha-guide-figure-3014]

  _**Figur 15:** core klustertjänsten är igång, med rätt IP-adress_

7.  Lägg till den andra noden i klustret.

  Nu när klustertjänsten kärnor är igång, kan du lägga till den andra noden i klustret.

  ![Bild 16 Lägg till den andra noden i klustret][sap-ha-guide-figure-3015]

  _**Bild 16:** lägga till den andra noden i klustret_

8.  Ange ett namn för den andra nod klustervärden.

  ![Figur 17: Ange andra värden klusternodnamnet][sap-ha-guide-figure-3016]

  _**Figur 17:** ange andra värden klusternodnamnet_

  > [!IMPORTANT]
  > Kontrollera att den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan är *inte* valda.  
  >
  >

  ![Figur 18: Markera inte kryssrutan][sap-ha-guide-figure-3017]

  _**Figur 18:** gör *inte* Markera kryssrutan_

  Du kan ignorera varningar om kvorum och diskar. Du måste ange kvorum och dela disken senare, enligt beskrivningen i [installera SIOS DataKeeper Cluster Edition för en resurs för SAP ASCS/SCS klusterdisk][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Bild 19: Ignorera varningar om disken kvorum][sap-ha-guide-figure-3018]

  _**Bild 19:** Ignorera varningar om disken kvorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurera ett filresursvittne för kluster

När du konfigurerar ett kluster filresursvittne inkluderar dessa uppgifter:

- Skapa en filresurs.
- Ange filen filresurs vittne kvorum i hanteraren för redundanskluster.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Skapa en filresurs

1.  Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

  I exemplen i den här artikeln är filresursvittnet på Active Directory- eller DNS-server som körs i Azure. Filresursvittnet kallas domcontr-0. Eftersom du vill att du har konfigurerat en VPN-anslutning till Azure (via VPN-Gateway eller Azure ExpressRoute), Active Directory eller DNS-tjänsten lokalt och är inte lämpligt att köra ett filresursvittne.

  > [!NOTE]
  > Om Active Directory- eller DNS-tjänsten körs bara lokalt, inte konfigurera din filresursvittne på Active Directory- eller DNS-Windows operativsystemet som körs lokalt. Nätverksfördröjningen mellan klusternoder som körs i Azure och Active Directory och DNS på lokalt kan vara för stort och orsaka problem med nätverksanslutningen. Se till att konfigurera filresursvittnet på en virtuell Azure-dator som kör nära noden i klustret.  
  >
  >

  Kvorum-enhet behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för enheten kvorum.

2.  Lägg till klusternamnobjektet.

  ![Figur 20: Tilldela behörigheter för resurs för klusternamnobjektet][sap-ha-guide-figure-3019]

  _**Figur 20:** tilldela behörigheter för resurs för klusternamnobjektet_

  Var noga med att behörigheterna som inkluderar behörighet att ändra data i resursen för klusternamnobjekt (i vårt exempel pr1-ascs-vir$).

3.  Om du vill lägga till klusternamnobjektet i listan, Välj **Lägg till**. Ändra filtreringen för att söka efter datorobjekt, förutom de som visas i figur 22.

  ![Figur 21: Ändra objekttyper att inkludera datorer][sap-ha-guide-figure-3020]

  _**Figur 21:** ändra **objekttyper** att inkludera datorer_

  ![Figur 22: Markera kryssrutan datorer][sap-ha-guide-figure-3021]

  _**Figur 22:** markerar du den **datorer** kryssruta_

4.  Ange klusternamnobjektet som visas i figur 21. Eftersom posten redan har skapats kan du ändra behörigheterna som visas i figur 20.

5.  Välj den **säkerhet** för resursen och ange sedan mer detaljerade behörigheter för klusternamnobjektet.

  ![Figur 23: Ange säkerhetsattribut för klusterobjektet namn på filen resursen kvorum][sap-ha-guide-figure-3022]

  _**Figur 23:** ange säkerhetsattribut för klusterobjektet namn på filen resursen kvorum_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ange filen filresurs vittne kvorum i hanteraren för redundanskluster

1.  Öppna den kvorum inställningen guiden Konfigurera.

  ![Figur 24: Starta konfigurera inställningen guiden klusterkvorum][sap-ha-guide-figure-3023]

  _**Figur 24:** börja konfigurera inställningen guiden klusterkvorum_

2.  På den **Välj alternativ för kvorumkonfiguration** väljer **Välj kvorumvittnet**.

  ![Bild 25: Kvorumkonfigurationer som du kan välja mellan][sap-ha-guide-figure-3024]

  _**Bild 25:** kvorumkonfigurationer som du kan välja mellan_

3.  På den **Välj Kvorumvittne** väljer **konfigurera ett filresursvittne**.

  ![Bild 26: Välj filresursvittnet][sap-ha-guide-figure-3025]

  _**Bild 26:** Välj filresursvittnet_

4.  Ange UNC-sökvägen till filresursen (i vårt exempel \\domcontr 0\FSW). Om du vill se en lista över ändringar kan du markera **nästa**.

  ![Bild 27: Definiera filresursplats för resursen vittne][sap-ha-guide-figure-3026]

  _**Bild 27:** definiera filresursplats för resursen vittne_

5.  Välj önskade ändringar och välj sedan **nästa**. Du måste konfigurera om klusterkonfigurationen har som visas i bild 28:  

  ![Bild 28: Bekräfta att klustret har konfigurerats om][sap-ha-guide-figure-3027]

  _**Bild 28:** bekräfta att klustret har konfigurerats om_

När du har installerat Windows-redundanskluster som du behöver ändra vissa tröskelvärden så att de anpassa redundans identifiering till villkoren i Azure. Parametrarna för att ändra dokumenteras i [justera failover cluster nätverket tröskelvärden][tuning-failover-cluster-network-thresholds]. Förutsatt att din två virtuella datorer som som utgör Windows klusterkonfigurationen för ASCS/SCS finns i samma undernät, ändra följande parametrar till dessa värden:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Dessa inställningar har testats med kunder och ger en bra kompromiss. De är tillräckligt flexibla, men de ger också redundans som är tillräckligt snabbt i verkliga felvillkor på ett program eller en nod eller ett VM-fel.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resursen klusterdisken

Nu har du en fungerande klusterkonfiguration för redundans för Windows Server i Azure. Om du vill installera en SAP ASCS/SCS-instans, behöver du en delad disk-resurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en fristående lösning som du kan använda för att skapa delade diskresurserna.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resursen klusterdisken omfattar dessa aktiviteter:

- Lägg till Microsoft .NET Framework 3.5.
- Installera SIOS DataKeeper.
- Ställ in SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Lägg till .NET Framework 3.5
.NET framework 3.5 är inte automatiskt aktiverats eller installerats på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver .NET på alla noder som du installerar DataKeeper, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows, som visas i figur 29:

  ![Bild 29: Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Bild 29:** installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 30: Installationen förloppsindikator när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Bild 30:** Installation förloppsindikator när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommandoradsverktyget dism.exe. För den här typen av installation som du behöver åtkomst till katalogen SxS på installationsmediet för Windows. Ange det här kommandot i en upphöjd kommandotolk:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. För att skapa virtuella delad lagring med SIOS DataKeeper, skapa en synkroniserad spegling och simulera delad klusterlagring.

Innan du installerar programvaran SIOS skapa DataKeeperSvc domänanvändare.

> [!NOTE]
> Lägg till domänanvändaren DataKeeperSvc i gruppen lokala administratörer på båda klusternoderna.
>
>

Installera SIOS DataKeeper:

1.  Installera den SIOS på båda klusternoderna.

  ![SIOS installer][sap-ha-guide-figure-3030]

  ![Bild 31: Första sidan i SIOS DataKeeper-installation][sap-ha-guide-figure-3031]

  _**Bild 31:** första sidan i SIOS DataKeeper-installation_

2.  I dialogrutan Välj **Ja**.

  ![Bild 32: DataKeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

  _**Bild 32:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3.  I dialogrutan, rekommenderar vi att du väljer **domän eller Server konto**.

  ![Bild 33: Användarens val för SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Bild 33:** användarens val för SIOS DataKeeper_

4.  Ange domänanvändarnamn och lösenord som du skapade för SIOS DataKeeper.

  ![Bild 34: Ange användarnamn och lösenord för SIOS DataKeeper-installation][sap-ha-guide-figure-3034]

  _**Bild 34:** ange användarnamn och lösenord för SIOS DataKeeper installationen_

5.  Installera licensnyckeln för din SIOS DataKeeper-instans som visas i bild 35.

  ![Bild 35: Ange licensnyckeln SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Bild 35:** Ange licensnyckeln SIOS DataKeeper_

6.  När du uppmanas starta om den virtuella datorn.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Ställ in SIOS DataKeeper

Starta konfigurationen när du har installerat SIOS DataKeeper på båda noderna. Målet med konfigurationen är att ha synkron replikering mellan ytterligare diskar som är kopplade till var och en av de virtuella datorerna.

1.  Starta verktyget DataKeeper hantering och konfiguration och välj sedan **ansluta servern**.

  ![Bild 36: SIOS DataKeeper hantering och verktyg][sap-ha-guide-figure-3036]

  _**Bild 36:** SIOS DataKeeper hantering och konfiguration av verktyget_

2.  Ange namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till, och i andra steget, den andra noden.

  ![Bild 37: Infoga namnet eller TCP/IP-adressen för den första noden hanteringen och konfigurationsverktyget ska ansluta till och i andra steget, den andra noden][sap-ha-guide-figure-3037]

  _**Bild 37:** infoga namn eller TCP/IP-adress för den första noden verktyget hantering och konfiguration ska ansluta till och i andra steget, den andra noden_

3.  Skapa jobbet replikering mellan två noder.

  ![Bild 38: Skapa ett jobb för replikering][sap-ha-guide-figure-3038]

  _**Bild 38:** skapa ett jobb för replikering_

  En guide hjälper dig att skapa ett jobb för replikering.

4.  Ange namnet på jobbet för replikering.

  ![Bild 39: Ange namnet på jobbet replikering][sap-ha-guide-figure-3039]

  _**Bild 39:** definiera namnet på jobbet replikering_

  ![Bild 40: Definiera grundläggande data för noden som ska vara aktuella Källnoden][sap-ha-guide-figure-3040]

  _**Bild 40:** definiera grundläggande data för noden som ska vara aktuella Källnoden_

5.  Definiera namn, TCP/IP-adress och volymen av målnoden.

  ![Bild 41: Definiera namn, TCP/IP-adress och diskvolymen för den aktuella målnoden][sap-ha-guide-figure-3041]

  _**Bild 41:** definiera namn, TCP/IP-adress och diskvolymen för den aktuella målnoden_

6.  Definiera komprimeringsalgoritmer. I vårt exempel rekommenderar vi att du komprimera dataströmmen för replikering. Särskilt i omsynkroniseringen situationer minskar komprimeringen av replikering dataströmmen kraftigt omsynkroniseringen tid. Komprimering använder processor och RAM resurser för en virtuell dator. När hastigheten med komprimering ökar, ökar mängden processorresurser som används. Du kan ändra denna inställning senare.

7.  En annan inställning som du behöver kontrollera är om replikeringen sker synkront eller asynkront. När du skyddar SAP ASCS/SCS konfigurationer, måste du använda synkron replikering.  

  ![Bild 42: Definiera replikeringsinformation][sap-ha-guide-figure-3042]

  _**Bild 42:** definiera replikeringsinformation_

8.  Definiera om volymen som replikeras av replikering jobbet ska representeras till Windows Server konfigurationen av ett redundanskluster som en delad disk. SAP ASCS/SCS-konfiguration, markera **Ja** så att Windows-kluster ser replikerade volymen som en delad disk som kan användas som en volym i klustret.

  ![Bild 43: Välj Ja om du vill ange replikerade volymen som en klustervolym][sap-ha-guide-figure-3043]

  _**Bild 43:** Välj **Ja** ange replikerade volymen som en klustervolym_

  När volymen har skapats, verktyget DataKeeper hantering och konfiguration som visar att jobbet replikering är aktiv.

  ![Bild 44: DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv][sap-ha-guide-figure-3044]

  _**Bild 44:** DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv_

  Hanteraren för redundanskluster innehåller nu disk som en DataKeeper disk som visas i bild 45:

  ![Bild 45: Klusterhanterare för växling vid fel visas den disk som DataKeeper replikeras][sap-ha-guide-figure-3045]

  _**Bild 45:** Klusterhanteraren visar disken som DataKeeper replikeras_

## <a name="next-steps"></a>Nästa steg

* [Installera SAP NetWeaver hög tillgänglighet med hjälp av ett redundanskluster i Windows och delad disk för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
