---
title: Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för SAP ASCS/SCS | Microsoft Docs
description: Lär dig hur du förbereder Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b729327187a52f36d50f8a754f5521527bb07ac6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60717925"
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

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP – flera SÄKERHETSIDENTIFIERARE konfiguration med hög tillgänglighet)

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

Den här artikeln beskriver de steg du utför för att förbereda Azure-infrastrukturen för att installera och konfigurera en SAP-system med hög tillgänglighet i ett Windows-redundanskluster med hjälp av en *delad klusterdisk* som ett alternativ för kluster ett SAP ASCS-instans.

## <a name="prerequisites"></a>Nödvändiga komponenter

Läs den här artikeln innan du påbörjar installationen:

* [Arkitektur-guide: Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbered infrastrukturen för arkitektoniska mall 1
Azure Resource Manager-mallar för SAP underlätta distributionen av resurser som krävs.

Trelagers-mallar i Azure Resource Manager har också stöd för scenarier med hög tillgänglighet. Arkitektoniska mall 1 har till exempel två kluster. Varje kluster är en SAP felpunkt för SAP ASCS/SCS och DBMS.

Här är där du kan få Azure Resource Manager-mallar för exempelscenariot som beskrivs i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-avbildning med hjälp av Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Anpassad avbildning genom att använda Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Så här förbereder du infrastrukturen för arkitektoniska mall 1:

- I Azure-portalen i den **parametrar** fönstret i den **SYSTEMAVAILABILITY** väljer **HA**.

  ![Bild 1: Ställa in parametrar för SAP hög tillgänglighet Azure Resource Manager][sap-ha-guide-figure-3000]

_**Bild 1:** Ställa in parametrar för SAP hög tillgänglighet Azure Resource Manager_


  Skapa mallar:

  * **Virtuella datorer**:
    * SAP-programservern virtuella datorer: \<SAPSystemSID\>-di-\<Number\>
    * ASCS/SCS klustrets virtuella datorer: \<SAPSystemSID\>- ascs-\<tal\>
    * DBMS-kluster: \<SAPSystemSID\>-db-\<Number\>

  * **Nätverkskort för alla virtuella datorer med tillhörande IP-adresser**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ascs-\<Number\>
    * \<SAPSystemSID\>-nic-db-\<Number\>

  * **Azure storage-konton (endast ohanterade diskar)**:

  * **Tillgänglighetsgrupper** för:
    * SAP-programservern virtuella datorer: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS klustrets virtuella datorer: \<SAPSystemSID\>-avset-ascs
    * DBMS klustrets virtuella datorer: \<SAPSystemSID\>-avset-db

  * **Intern Azure belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instans och IP-adress \<SAPSystemSID\>- lb-ascs
    * Med alla portar för SQL Server DBMS och IP-adress \<SAPSystemSID\>- lb-db

  * **Nätverkssäkerhetsgrupp**: \<SAPSystemSID\>-nsg-ascs-0  
    * Med en öppen externa Remote Desktop Protocol (RDP)-port till den \<SAPSystemSID\>- ascs-0 virtuell dator

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare är dynamiska som standard. Ändra dem till den statiska IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuera virtuella datorer med anslutning till företagets nätverk (mellan lokala) ska användas i produktion
För produktion SAP-system, distribuera Azure-datorer med [anslutning till företagets nätverk (mellan lokala)] [ planning-guide-2.2] med hjälp av Azure VPN Gateway eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instans. Virtuellt nätverk och undernät har redan skapats och förberetts.
>
>

1. I Azure-portalen i den **parametrar** fönstret i den **NEWOREXISTINGSUBNET** väljer **befintliga**.
2. I den **SUBNETID** lägger du till fullständig sträng med din förberedd Azure-nätverk undernät-ID som du planerar att distribuera virtuella datorer i Azure.
3. Om du vill hämta en lista över alla Azure-nätverk undernät, kör du följande PowerShell-kommando:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Den **ID** fältet visar värdet för undernät-ID.
4. Om du vill hämta en lista över alla undernät-ID-värden, kör du följande PowerShell-kommando:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Undernät-ID som ser ut så här:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuera molnbaserad SAP-instanser för testning och demonstration
Du kan distribuera dina SAP-system för hög tillgänglighet i en endast molnbaserad distributionsmodell. Den här typen av distribution är främst användbart för Demonstrations- och användningsfall. Det är inte lämpligt för produktion.

- I Azure-portalen i den **parametrar** fönstret i den **NEWOREXISTINGSUBNET** väljer **nya**. Lämna den **SUBNETID** fält tomma.

  SAP Azure Resource Manager-mallen skapar automatiskt Azure-nätverk och undernät.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS-tjänsten i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbered infrastrukturen för arkitektoniska mall 2

Du kan använda Azure Resource Manager-mallen för SAP för att underlätta distributionen av resurser i infrastrukturen för SAP arkitektoniska mall 2.

Här är där du kan få Azure Resource Manager-mallar för det här distributionsscenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-avbildning genom att använda Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Anpassad avbildning genom att använda Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbered infrastrukturen för arkitektoniska mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för multi-SID. Du kan till exempel lägga till en ytterligare SAP ASCS/SCS-instans i en *befintliga* klusterkonfigurationen. Mer information finns i [konfigurera en ytterligare SAP ASCS/SCS-instans för en befintliga klusterkonfigurationen och skapa en SAP – flera SÄKERHETSIDENTIFIERARE konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster kan du använda multi-SID [snabbstartsmallarna på GitHub](https://github.com/Azure/azure-quickstart-templates).

Om du vill skapa ett nytt multi-SID-kluster, måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Databasmall för](#database-template)
* [Mall för program-servrar](#application-servers-template)

I följande avsnitt innehåller mer information om mallar och parametrar som du ska ange i mallarna.

### <a name="ASCS-SCS-template"></a> ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett redundanskluster i Windows Server som är värd för flera ASCS/SCS-instanser.

Du ställer in mallen ASCS/SCS multi-SID i den [ASCS/SCS – flera SÄKERHETSIDENTIFIERARE mallen] [ sap-templates-3-tier-multisid-xscs-marketplace-image] eller [ASCS/SCS – flera SÄKERHETSIDENTIFIERARE mallen genom att använda Managed Disks] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], ange värden för följande parametrar:

- **Resurs-Prefix**:  Ange resurs-prefixet som används som prefix i alla resurser som skapas under distributionen. Eftersom resurserna som inte tillhör bara en SAP-system, är prefixet för resursen inte SID för en SAP-system.  Prefixet måste vara mellan tre och sex tecken.
- **Stack typ**: Välj stack-typ av SAP-system. Beroende på vilken stack har Azure Load Balancer en (ABAP eller endast Java) eller två (ABAP + Java) privata IP-adresser per SAP-system.
- **OS-typ**: Välj operativsystem för de virtuella datorerna.
- **Antal SAP**: Välj antal SAP-system som du vill installera i det här klustret.
- **Systemets tillgänglighet**: Välj **HA**.
- **Administratörens användarnamn och lösenord för serveradministratören**: Skapa en ny användare som kan användas för att logga in på datorn.
- **Nytt eller befintligt undernät**: Ange om du vill skapa ett nytt virtuellt nätverk och undernät eller Använd ett befintligt undernät. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
- **Undernät-Id**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så här:

  /subscriptions/\<prenumerations-id\>/resourceGroups/\<Resursgruppsnamn\>/providers/Microsoft.Network/virtualNetworks/\<virtuella nätverksnamnet\>/subnets/ \<undernätsnamn\>

Mallen distribuerar en Azure Load Balancer-instans som har stöd för flera SAP-system:

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS sätta replikering Server (ERS) (endast Linux)-instanserna är konfigurerade för instansnummer 02, 12, 22...
- SCS ERS (endast Linux)-instanserna är konfigurerade för instansnummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 VIP(s) (2 för Linux), 1 x-VIP för ASCS/SCS och 1 x-VIP för ÄNDARE (endast Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SAP ASCS/SCS-portar
Följande lista innehåller alla belastningsutjämningsregler (där x är numret på SAP-system, till exempel 1, 2, 3...).
- Windows-specifika portar för varje SAP-system: 445, 5985
- ASCS-portar (instansnummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instansnummer x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASCS ERS portar i Linux (instansnummer x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS ERS portar i Linux (instansnummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad för att använda följande portar för avsökning (där x är numret på SAP-system, till exempel 1, 2, 3...):
- ASCS/SCS intern Azure load balancer avsökningsporten: 620x0
- ÄNDARE interna läsa in belastningsutjämnare avsökningsporten (endast Linux): 621x2

### <a name="database-template"></a> Databasmall för

Databas-mallen distribuerar en eller två virtuella datorer som du kan använda för att installera det relationella databashanteringssystemet (RDBMS) för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Du ställer in databasen – flera SÄKERHETSIDENTIFIERARE mallen, i den [databasen – flera SÄKERHETSIDENTIFIERARE mallen] [ sap-templates-3-tier-multisid-db-marketplace-image] eller [databasen – flera SÄKERHETSIDENTIFIERARE mallen genom att använda Managed Disks] [ sap-templates-3-tier-multisid-db-marketplace-image-md], ange värden för följande parametrar:

- **SAP-System-Id**: Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
- **OS-typ**: Välj operativsystem för de virtuella datorerna.
- **DbType**: Välj vilken typ av databas som du vill installera på klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på virtuella datorer. Kontrollera att du väljer rätt operativsystemtypen. Välj **Windows** för SQL och väljer en Linux-distribution för HANA. Azure belastningsutjämnare som är anslutet till de virtuella datorerna är konfigurerad för att stödja den valda databas-typ:
  * **SQL**: Belastningsutjämna belastningsutjämnarporten 1433. Se till att använda den här porten för SQL Server AlwaysOn-installationen.
  * **HANA**: Load balancer belastningsutjämna-portarna 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
  Belastningsutjämnaren använder avsökningsporten 62550.
- **SAP-systemstorlek**: Ange antalet SAP som innehåller det nya systemet. Om du inte vet hur många SAP kräver att systemet, be din SAP-teknikpartner eller systemintegratör.
- **Systemets tillgänglighet**: Välj **HA**.
- **Administratörens användarnamn och lösenord för serveradministratören**: Skapa en ny användare som kan användas för att logga in på datorn.
- **Undernät-Id**: Ange ID för det undernät som du använde under distributionen av ASCS/SCS-mall eller ID för det undernät som har skapats som en del av ASCS/SCS-mallsdistribution.

### <a name="application-servers-template"></a> Mall för program-servrar

Programmet servrar mallen distribuerar två eller flera virtuella datorer som kan användas som SAP-programservern instanser för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Konfigurera servrar – flera SÄKERHETSIDENTIFIERARE programmallen, i den [programmall servrar – flera SÄKERHETSIDENTIFIERARE] [ sap-templates-3-tier-multisid-apps-marketplace-image] eller [programmall servrar – flera SÄKERHETSIDENTIFIERARE genom att använda Managed Disks] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], ange värden för följande parametrar:

  -  **SAP-System-Id**: Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
  -  **OS-typ**: Välj operativsystem för de virtuella datorerna.
  -  **SAP-systemstorlek**: Antal SAP som innehåller det nya systemet. Om du inte vet hur många SAP kräver att systemet, be din SAP-teknikpartner eller systemintegratör.
  -  **Systemets tillgänglighet**: Välj **HA**.
  -  **Administratörens användarnamn och lösenord för serveradministratören**: Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**: Ange ID för det undernät som du använde under distributionen av ASCS/SCS-mall eller ID för det undernät som har skapats som en del av ASCS/SCS-mallsdistribution.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure-nätverk
I vårt exempel är 10.0.0.0/16 i adressutrymmet för Azure Virtual Network-instans. Det finns ett undernät som kallas undernät, med adressintervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar till nätverksinställningar i gästoperativsystemet. Detta omfattar IP-adresser, DNS-servrar och undernät. Konfigurera nätverksinställningarna i Azure. DHCP Dynamic Host Configuration Protocol ()-tjänsten sprider dina inställningar.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS-IP-adresser

Om du vill ange nödvändiga DNS-IP-adresser, gör du följande:

1. I Azure-portalen i den **DNS-servrar** fönstret, se till att det virtuella nätverket **DNS-servrar** alternativet är inställt på **anpassad DNS**.
2. Välj dina inställningar beroende på vilken typ av nätverk som du har. Mer information finns i följande resurser:
   * [Anslutning till företagets nätverk (mellan lokala)][planning-guide-2.2]: Lägg till IP-adresserna för DNS-servrar på plats.  
   Du kan utöka den lokala DNS-servrar till de virtuella datorerna som körs i Azure. I det här scenariot kan du lägga till IP-adresserna för de Azure-datorerna där du kör DNS-tjänsten.
   * För distribueringar av virtuella datorer som är isolerade i Azure: Distribuera en ytterligare virtuell dator i samma virtuella nätverk-instans som fungerar som en DNS-server. Lägg till IP-adresserna för de Azure-datorerna som du har ställt in upp till kör DNS-tjänsten.

   ![Bild 2: Konfigurera DNS-servrar för Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Bild 2:** Konfigurera DNS-servrar för Azure Virtual Network_

   > [!NOTE]
   > Om du ändrar IP-adresserna för DNS-servrar kan behöva du starta om de Azure-datorerna för att tillämpa ändringen och sprida de nya DNS-servrarna.
   >
   >

I vårt exempel har DNS-tjänsten installerats och konfigurerats på dessa Windows-datorer:

| Virtuell datorroll | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Värdnamn och statiska IP-adresser för de klustrade SAP ASCS/SCS-instans och DBMS klustrad instans

För lokal distribution behöver du de här reserverade värdnamn och IP-adresser:

| Rollen för virtuell värd-namn | Virtuellt värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första virtuell värd klusternamn (för klusterhantering) |pr1-ascs-vir |10.0.0.42 |
| Virtuella värdnamn för SAP ASCS/SCS-instans |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (hantering) |pr1-dbms-vir |10.0.0.32 |

När du skapar klustret kan skapa den virtuella värden namn pr1-ascs-vir och pr1-dbms-vir och de associera IP-adresser som hanterar själva klustret. Information om hur du gör detta finns i [samla in klusternoder i en klusterkonfiguration][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Du kan manuellt skapa de andra två virtuella värdnamn, pr1-ascs-sap och pr1 dbms sap, och de associerade IP-adresserna, DNS-servern. Den klustrade SAP ASCS/SCS-instansen och den klustrade DBMS-instansen använder du dessa resurser. Information om hur du gör detta finns i [skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ange statiska IP-adresser för de virtuella datorerna för SAP
När du distribuerar de virtuella datorerna ska användas i ditt kluster kan behöva du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1. I Azure-portalen väljer du **resursgrupp** > **nätverkskort** > **inställningar** > **IP-adress**.
2. I den **IP-adresser** fönstret under **tilldelning**väljer **statiska**. I den **IP-adress** anger du IP-adressen som du vill använda.

   > [!NOTE]
   > Om du ändrar IP-adressen för nätverkskortet kan behöva du starta om de Azure-datorerna för att tillämpa ändringen.  
   >
   >

   ![Bild 3: Ange statiska IP-adresser för nätverkskort på varje virtuell dator][sap-ha-guide-figure-3002]

   _**Bild 3:** Ange statiska IP-adresser för nätverkskort på varje virtuell dator_

   Upprepa det här steget för alla nätverksgränssnitt som är, för alla virtuella datorer, inklusive virtuella datorer som du vill använda för din Active Directory- eller DNS-tjänst.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Virtuell datorroll | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första serverinstansen för SAP-program |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste serverinstansen för SAP-program |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Andra noden i klustret för ASCS/SCS-instans |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Andra noden i klustret för DBMS-instans |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ange en statisk IP-adress för intern Azure belastningsutjämnare

SAP Azure Resource Manager-mallen skapar en Azure intern belastningsutjämnare som används för kluster för SAP ASCS/SCS-instans och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren för SAP ASCS/SCS: pr1-lb-ascs.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna belastningsutjämnaren för DBMS: pr1-lb-dbms.
>
>

Ange en statisk IP-adress för intern Azure belastningsutjämnare:

1. Den första distributionen anger interna belastningsutjämnarens IP-adressen till **dynamisk**. I Azure-portalen på den **IP-adresser** fönstret under **tilldelning**väljer **statiska**.
2. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS-instans.
3. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instans.

   ![Bild 4: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans][sap-ha-guide-figure-3003]

   _**Bild 4:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans_

I vårt exempel har vi två Azure interna belastningsutjämnare som har dessa statiska IP-adresser:

| Azure interna belastningsutjämnarroll | Azure interna belastningsutjämnarens namn | Statisk IP-adress |
| --- | --- | --- |
| Intern belastningsutjämnare för SAP ASCS/SCS-instans |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med standard-instansnummer 00
* En Java-SCS-instans med standard-instansnummer 01

När du installerar SAP ASCS/SCS-instans, måste du använda instansen Standardnumret 00 för din ASCS ABAP och instans Standardnumret 01 för din Java SCS-instans.

Skapa sedan de nödvändiga intern belastningsutjämning slutpunkter för SAP NetWeaver-portar.

Om du vill skapa nödvändiga intern belastningsutjämning slutpunkter först skapa dessa slutpunkter för SAP NetWeaver ABAP ASCS-portar för belastningsutjämning:

| Tjänsten/läsa in belastningsutjämning Regelnamn | Standardportnumren. | Konkreta portar för (ASCS-instansen med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Placera server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP-meddelandeservern / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Internt ABAP meddelande / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Meddelande-serverns HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP starta tjänsten ASCS HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP starta tjänsten ASCS HTTPS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Placera replikering / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

**Tabell 1:** Portnummer för SAP NetWeaver ABAP ASCS-instanser

Skapa sedan dessa slutpunkter för SAP NetWeaver Java SCS-portar för belastningsutjämning:

| Tjänsten/läsa in belastningsutjämning Regelnamn | Standardportnumren. | Konkreta portar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Placera server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Gateway-server / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java-meddelandeservern / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Meddelande-serverns HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP starta tjänsten SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP starta tjänsten SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Placera replikering / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP starta tjänsten ÄNDARE HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

**Tabell 2:** Portnummer för SAP NetWeaver Java SCS-instanser

![Bild 5: Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare][sap-ha-guide-figure-3004]

_**Bild 5:** Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare_

Ange IP-adressen för load balancer pr1-lb-dbms till IP-adressen för det virtuella värdnamnet för DBMS-instans.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare

Om du vill använda olika antal för SAP ASCS eller SCS-instanser måste du ändra namn och värden för deras hamnar från standardvärdena.

1. I Azure-portalen väljer du  **\<SID\>-lb-ascs belastningsutjämnare** > **läsa in belastningsutjämningsregler**.
2. Alla regler för belastningsutjämning som hör till SAP ASCS eller SCS-instans, ändra dessa värden:

   * Namn
   * Port
   * Backend-port

   Om du vill ändra standardvärdet för ASCS-instans från 00 och 31, måste du göra ändringar för alla portar som anges i tabell 1.

   Här är ett exempel på en uppdatering för port *lbrule3200*.

   ![Bild 6: Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare][sap-ha-guide-figure-3005]

   _**Bild 6:** Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Lägga till Windows-datorer i domänen

När du tilldelar en statisk IP-adress till de virtuella datorerna kan du lägga till de virtuella datorerna till domänen.

![Bild 7: Lägg till en virtuell dator till en domän][sap-ha-guide-figure-3006]

_**Bild 7:** Lägg till en virtuell dator till en domän_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans

Azure Load Balancer har en intern belastningsutjämnare att stängs anslutningar när anslutningar som är inaktiva för en viss tid (en timeout för inaktivitet). SAP-arbetsprocesser i dialogrutan instanser öppna anslutningar till SAP-sätta bearbeta när de första sätta/ta bort från kön för begäran måste skickas. De här anslutningarna förblir oftast upprättad förrän arbetsprocessen eller sätta processen startar om. Om den varit inaktiv under en angiven tidsperiod, stängs intern Azure belastningsutjämnare anslutningarna. Detta är inte ett problem Eftersom arbetsprocessen SAP som återupprättar anslutningen till sätta processen om det inte längre finns. Dessa aktiviteter finns dokumenterade i developer-spårning för SAP-processer, men de kan skapa en stor mängd extra innehåll i de spårningarna. Det är en bra idé att ändra TCP/IP `KeepAliveTime` och `KeepAliveInterval` på båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP-profil-parametrar som beskrivs senare i artikeln.

Om du vill lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans, Lägg först till dessa registerposter i Windows på både Windows-klusternoder för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabeltyp |REG_DWORD (Decimal) |
| Värde |120000 |
| Länkar till dokumentationen |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabell 3:** Ändra den första parametern för TCP/IP

Lägg sedan till den här Windows-registerposten på både Windows-klusternoder för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabeltyp |REG_DWORD (Decimal) |
| Värde |120000 |
| Länkar till dokumentationen |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabell 4:** Ändra den andra parametern för TCP/IP

Starta om båda klusternoderna för att tillämpa ändringarna.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurera ett redundanskluster för Windows Server för en SAP ASCS/SCS-instans

Hur du konfigurerar ett redundanskluster för Windows Server för en SAP ASCS/SCS-instans innebär att dessa uppgifter:

- Samla in klusternoder i en klusterkonfiguration.
- Konfigurera ett filresursvittne för klustret.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Samla in klusternoder i en klusterkonfiguration

1. Lägga till redundansklustring till båda klusternoderna i Lägg till roller och funktioner som guiden.
2. Konfigurera redundansklustret med hjälp av hanteraren för redundanskluster. I hanteraren för redundanskluster, Välj **Skapa kluster**, och Lägg sedan till bara namnet på det första klustret (nod A). Lägg inte till den andra noden ännu; Du kan lägga till den andra noden i ett senare steg.

   ![Bild 8: Lägg till server eller virtuell dator namnet på den första noden i klustret][sap-ha-guide-figure-3007]

   _**Bild 8:** Lägg till server eller virtuell dator namnet på den första noden i klustret_

3. Ange nätverksnamn (virtuellt värdnamn) i klustret.

   ![Bild 9: Ange klusternamnet][sap-ha-guide-figure-3008]

   _**Bild 9:** Ange klusternamnet_

4. När du har skapat klustret, kör du ett klustervalideringstest.

   ![Bild 10: Kör valideringskontrollen kluster][sap-ha-guide-figure-3009]

   _**Bild 10:** Kör valideringskontrollen kluster_

   Du kan ignorera alla varningar om diskar i det här läget i processen. Lägger du till ett filresursvittne och SIOS delade diskar senare. I det här skedet behöver du inte bekymra dig om att du har ett kvorum.

   ![Bild 11: Inga kvorumdisken hittas][sap-ha-guide-figure-3010]

   _**Bild 11:** Inga kvorumdisken hittas_

   ![Bild 12: En core klusterresurs måste en ny IP-adress][sap-ha-guide-figure-3011]

   _**Bild 12:** En core klusterresurs måste en ny IP-adress_

5. Ändra IP-adressen för klustertjänsten core. Klustret kan inte starta tills du ändrar IP-adressen för klustertjänsten core eftersom IP-adressen för servern som pekar på en av noderna i virtuell dator. Gör detta på den **egenskaper** sidan i core-klustertjänsten IP-resurs.

   Exempelvis kan måste vi du tilldela en IP-adress (i vårt exempel 10.0.0.42) för det kluster virtuell värd namnet pr1-ascs-vir.

   ![Bild 13: I dialogrutan Egenskaper ändrar du IP-adress][sap-ha-guide-figure-3012]

   _**Bild 13:** I den **egenskaper** dialogrutan ändrar du IP-adress_

   ![Bild 14: Tilldela IP-adressen som är reserverat för klustret][sap-ha-guide-figure-3013]

   _**Bild 14:** Tilldela IP-adressen som är reserverat för klustret_

6. Hämta virtuell värd klusternamnet.

   ![Bild 15: Core klustertjänsten är igång, med rätt IP-adress][sap-ha-guide-figure-3014]

   _**Bild 15:** Core klustertjänsten är igång, med rätt IP-adress_

7. Lägg till den andra noden i klustret.

   Nu när klustertjänsten core är igång kan du lägga till den andra noden i klustret.

   ![Bild 16 Lägg till den andra noden i klustret][sap-ha-guide-figure-3015]

   _**Bild 16:** Lägg till den andra noden i klustret_

8. Ange ett namn för den andra nod klustervärden.

   ![Bild 17: Ange andra värden klusternodnamnet][sap-ha-guide-figure-3016]

   _**Bild 17:** Ange andra värden klusternodnamnet_

   > [!IMPORTANT]
   > Kontrollera att den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan är *inte* valda.  
   >
   >

   ![Bild 18: Markera inte kryssrutan][sap-ha-guide-figure-3017]

   _**Bild 18:** Gör *inte* markerar du kryssrutan_

   Du kan ignorera varningar om kvorum och diskar. Du måste ange kvorum och dela disken senare, enligt beskrivningen i [installera SIOS DataKeeper Cluster Edition för en resurs för SAP ASCS/SCS klusterdisken][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Bild 19: Ignorera varningar om disken kvorum][sap-ha-guide-figure-3018]

   _**Bild 19:** Ignorera varningar om disken kvorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurera ett filresursvittne för kluster

När du konfigurerar ett filresursvittne i kluster inkluderar dessa uppgifter:

- Skapa en filresurs.
- Ange filen resurs vittne kvorum i hanteraren för redundanskluster.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Skapa en filresurs

1. Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

   I exemplen i den här artikeln är filresursvittnet på den Active Directory- eller DNS-server som körs i Azure. Filresursvittnet kallas domcontr-0. Eftersom du skulle har konfigurerat en VPN-anslutning till Azure (via VPN-Gateway eller Azure ExpressRoute), Active Directory eller DNS-tjänsten är på plats och är inte lämpliga att köra ett filresursvittne.

   > [!NOTE]
   > Om Active Directory eller DNS-tjänsten körs bara på plats, inte konfigurera din filresursvittne på Active Directory eller DNS-Windows operativsystemet som körs lokalt. Nätverksfördröjningen mellan noder som körs i Azure och Active Directory eller DNS på plats kan vara för stort och orsaka problem med nätverksanslutningen. Glöm inte att konfigurera filresursvittnet på en Azure-dator som körs nära noden i klustret.  
   >
   >

   Kvorum-enhet behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för enheten kvorum.

2. Lägg till klusternamnobjekt.

   ![Bild 20: Tilldela behörigheter för resursen för klusternamnobjekt][sap-ha-guide-figure-3019]

   _**Bild 20:** Tilldela behörigheter för resursen för klusternamnobjekt_

   Var noga med att behörigheterna som inkluderar behörighet att ändra data i resursen för klusternamnobjekt (i vårt exempel pr1-ascs-vir$).

3. Om du vill lägga till klusternamnobjekt i listan, Välj **Lägg till**. Ändra filtret för att söka efter datorobjekt, förutom de som visas i bild 22.

   ![Bild 21: Ändra objekttyper som kan innehålla datorer][sap-ha-guide-figure-3020]

   _**Bild 21:** Ändra **objekttyper** att inkludera datorer_

   ![Bild 22: Markera kryssrutan datorer][sap-ha-guide-figure-3021]

   _**Bild 22:** Välj den **datorer** kryssrutan_

4. Ange klusternamnobjekt som visas i bild 21. Eftersom posten redan har skapats, kan du ändra behörigheterna som visas i bild 20.

5. Välj den **Security** fliken för filresursen och ange sedan mer detaljerade behörigheter för klusternamnobjekt.

   ![Bild 23: Ange security attribut för klusterobjektet namn på filen resurs kvorum][sap-ha-guide-figure-3022]

   _**Bild 23:** Ange security attribut för klusterobjektet namn på filen resurs kvorum_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ange filen resurs vittne kvorum i hanteraren för redundanskluster

1. Öppna den kvorum inställningen guiden Konfigurera.

   ![Bild 24: Börja konfigurera inställningen guiden klusterkvorum][sap-ha-guide-figure-3023]

   _**Bild 24:** Börja konfigurera inställningen guiden klusterkvorum_

2. På den **Välj alternativ för kvorumkonfiguration** väljer **Välj kvorumvittnet**.

   ![Bild 25: Du kan välja bland kvorumkonfigurationer][sap-ha-guide-figure-3024]

   _**Bild 25:** Du kan välja bland kvorumkonfigurationer_

3. På den **Välj Kvorumvittne** väljer **konfigurera ett filresursvittne**.

   ![Bild 26: Välj filresursvittnet][sap-ha-guide-figure-3025]

   _**Bild 26:** Välj filresursvittnet_

4. Ange UNC-sökvägen till filresursen (i vårt exempel \\domcontr 0\FSW). Om du vill se en lista över ändringar som du kan göra **nästa**.

   ![Bild 27: Definiera filresursplats för resursen vittne][sap-ha-guide-figure-3026]

   _**Bild 27:** Definiera filresursplats för resursen vittne_

5. Välj önskade ändringar och välj sedan **nästa**. Du behöver konfigurera om klusterkonfigurationen har som visas i bild 28:  

   ![Bild 28: Bekräfta att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

   _**Bild 28:** Bekräfta att du har konfigurerat om klustret_

När du har installerat Windows-redundanskluster som du behöver ändra vissa tröskelvärden så att de kan anpassa redundans identifiering till villkoren i Azure. Parametrar som ska ändras finns dokumenterade i [justering failover-kluster nätverk tröskelvärden][tuning-failover-cluster-network-thresholds]. Förutsatt att de två virtuella datorerna som tillsammans bildar Windows klusterkonfigurationen för ASCS/SCS finns i samma undernät, ändra följande parametrar till dessa värden:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

De här inställningarna har testats med kunder och erbjuder en bra kompromiss. De är tillräckligt flexibel, men du får även redundanskluster som är tillräckligt snabbt i verkliga felvillkor på ett SAP-program eller en nod eller en VM-fel.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resurs klusterdisken

Nu har du en fungerande klusterkonfiguration för Windows Server-redundans i Azure. Om du vill installera en SAP ASCS/SCS-instans, behöver du en delad disk-resurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en tredjepartslösning som du kan använda för att skapa delade diskresurserna.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resurs klusterdisken omfattar dessa uppgifter:

- Lägg till Microsoft .NET Framework 3.5.
- Installera SIOS DataKeeper.
- Ställ in SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Add .NET Framework 3.5
.NET framework 3.5 är inte automatiskt aktiverats eller installerad på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver .NET på alla noder där du installerar DataKeeper, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows, som visas i bild 29:

  ![Bild 29: Installera .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden][sap-ha-guide-figure-3028]

  _**Bild 29:** Installera .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden_

  ![Bild 30: Installationen förloppsindikator när du installerar .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden][sap-ha-guide-figure-3029]

  _**Bild 30:** Installationen förloppsindikator när du installerar .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden_

- Använda kommandoradsverktyget dism.exe. Du behöver åtkomst till katalogen SxS på installationsmediet för Windows för den här typen av installation. Ange det här kommandot i en upphöjd kommandotolk:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Om du vill skapa virtuella delad lagring med SIOS DataKeeper, skapa en synkroniserad spegling och simulera klustrets delade lagring.

Innan du installerar programvaran SIOS skapa DataKeeperSvc domänanvändare.

> [!NOTE]
> Lägga till DataKeeperSvc-domänanvändare i gruppen lokala administratörer på båda klusternoderna.
>
>

Så här installerar SIOS DataKeeper:

1. Installera programvaran SIOS på båda klusternoderna.

   ![SIOS installer][sap-ha-guide-figure-3030]

   ![Bild 31: Första sidan i SIOS DataKeeper-installation][sap-ha-guide-figure-3031]

   _**Bild 31:** Första sidan i SIOS DataKeeper-installation_

2. I dialogrutan Välj **Ja**.

   ![Bild 32: DataKeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

   _**Bild 32:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3. I dialogrutan, rekommenderar vi att du väljer **domän eller Server**.

   ![Bild 33: Användarens val för SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Bild 33:** Användarens val för SIOS DataKeeper_

4. Ange domänanvändarnamn och lösenord som du skapade för SIOS DataKeeper.

   ![Bild 34: Ange domänanvändarnamn och lösenord för SIOS DataKeeper-installation][sap-ha-guide-figure-3034]

   _**Bild 34:** Ange domänanvändarnamn och lösenord för SIOS DataKeeper-installation_

5. Installera licensnyckel för din SIOS DataKeeper-instans såsom visas på bild 35.

   ![Bild 35: Ange licensnyckeln SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Bild 35:** Ange licensnyckeln SIOS DataKeeper_

6. När du uppmanas starta om den virtuella datorn.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Konfigurera SIOS DataKeeper

Starta konfigurationen när du har installerat SIOS DataKeeper på båda noderna. Målet med konfigurationen är att ha synkron datareplikering mellan dessa ytterligare diskar som är kopplade till var och en av de virtuella datorerna.

1. Starta verktyget DataKeeper hanterings- och välj sedan **Anslut Server**.

   ![Bild 36: SIOS DataKeeper hanterings- och verktyg][sap-ha-guide-figure-3036]

   _**Bild 36:** SIOS DataKeeper hanterings- och verktyg_

2. Ange namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till, och i andra steget, den andra noden.

   ![Bild 37: Infoga namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till och en andra steget, den andra noden][sap-ha-guide-figure-3037]

   _**Bild 37:** Infoga namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till och en andra steget, den andra noden_

3. Skapa replikeringen mellan de två noderna.

   ![Bild 38: Skapa ett replikeringsjobb][sap-ha-guide-figure-3038]

   _**Bild 38:** Skapa ett replikeringsjobb_

   En guide vägleder dig genom processen för att skapa ett replikeringsjobb.

4. Ange namnet på replikeringen.

   ![Bild 39: Ange namnet på replikeringen][sap-ha-guide-figure-3039]

   _**Bild 39:** Ange namnet på replikeringen_

   ![Bild 40: Definiera grunddata för den nod som ska vara den aktuella noden för källa][sap-ha-guide-figure-3040]

   _**Bild 40:** Definiera grunddata för den nod som ska vara den aktuella noden för källa_

5. Definiera namn, TCP/IP-adress och diskvolymen för målnoden.

   ![Bild 41: Definiera namn, TCP/IP-adress och diskvolymen för den aktuella målnoden][sap-ha-guide-figure-3041]

   _**Bild 41:** Definiera namn, TCP/IP-adress och diskvolymen för den aktuella målnoden_

6. Definiera komprimeringsalgoritmer. I vårt exempel rekommenderar vi att du komprimera dataströmmen för replikering. Särskilt i omsynkroniseringen situationer minskar komprimering av replikering stream avsevärt du tiden för omsynkronisering. Komprimering använder processor och RAM-resurser för en virtuell dator. När hastigheten med komprimering ökar, ökar mängden processorresurser som används. Du kan justera den här inställningen senare.

7. En annan inställning måste du kontrollera är om replikeringen sker synkront eller asynkront. När du skyddar SAP ASCS/SCS-konfigurationer, måste du använda synkron replikering.  

   ![Bild 42: Definiera replikeringsinformationen][sap-ha-guide-figure-3042]

   _**Bild 42:** Definiera replikeringsinformationen_

8. Definiera om den volym som replikeras av replikeringen ska representeras till Windows Server konfigurationen av ett redundanskluster som en delad disk. SAP ASCS/SCS-konfiguration, Välj **Ja** så att Windows-kluster ser replikerad volym som en delad disk som kan användas som en volym.

   ![Bild 43: Välj Ja om du vill ange replikerad volym som en volym][sap-ha-guide-figure-3043]

   _**Bild 43:** Välj **Ja** att ställa in replikerad volym som en volym_

   När volymen har skapats visas i DataKeeper hanterings- och verktyget att replikeringen är aktiv.

   ![Bild 44: DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv][sap-ha-guide-figure-3044]

   _**Bild 44:** DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv_

   Hanteraren för redundanskluster visar nu disken som en DataKeeper-disk som visas i bild 45:

   ![Bild 45: Hanteraren för redundanskluster visar disken som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _**Bild 45:** Hanteraren för redundanskluster visar disken som DataKeeper replikerade_

## <a name="next-steps"></a>Nästa steg

* [Installera SAP NetWeaver hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
