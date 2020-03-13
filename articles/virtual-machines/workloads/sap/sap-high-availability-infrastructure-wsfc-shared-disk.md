---
title: Azure-infrastruktur för SAP ASCS/SCS med WSFC & delad disk | Microsoft Docs
description: Lär dig hur du förbereder Azure-infrastrukturen för SAP HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a49bc979923bf52d099e30615910c5bdb0601b6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279863"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID-konfiguration med hög tillgänglighet)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Den här artikeln beskriver de steg som du vidtar för att förbereda Azure-infrastrukturen för att installera och konfigurera ett SAP-system med hög tillgänglighet på ett Windows-redundanskluster med hjälp av en *klusterdelad disk* som ett alternativ för att klustra en SAP ASCS-instans.

## <a name="prerequisites"></a>Förutsättningar

Läs igenom den här artikeln innan du påbörjar installationen:

* [Arkitektur guide: klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbered infrastrukturen för arkitektur mal len 1
Azure Resource Manager mallar för SAP bidrar till att förenkla distributionen av nödvändiga resurser.

Mallarna på tre nivåer i Azure Resource Manager också stöd för scenarier med hög tillgänglighet. Arkitektur mal len 1 har till exempel två kluster. Varje kluster är en enkel fel punkt i SAP för SAP ASCS/SCS och DBMS.

Här kan du få Azure Resource Manager mallar för exempel scenariot som vi beskriver i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-avbildning med hjälp av Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Anpassad avbildning med hjälp av Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Förbereda infrastrukturen för arkitektur mal len 1:

- I Azure Portal i rutan **parametrar** i rutan **SYSTEMAVAILABILITY** väljer du **ha**.

  ![Bild 1: Ange Azure Resource Manager parametrar för hög tillgänglighet för SAP][sap-ha-guide-figure-3000]

_**Bild 1:** Ange Azure Resource Manager parametrar för hög tillgänglighet för SAP_


  Mallarna skapa:

  * **Virtuella datorer**:
    * Virtuella SAP-program Server-datorer: \<SAPSystemSID\>-di-\<Number\>
    * ASCS/SCS-kluster virtuella datorer: \<SAPSystemSID\>-ASCS-\<Number\>
    * DBMS-kluster: \<SAPSystemSID\>-DB-\<Number\>

  * **Nätverkskort för alla virtuella datorer, med tillhör ande IP-adresser**:
    * \<SAPSystemSID\>-NIC-di-\<Number\>
    * \<SAPSystemSID\>-NIC-ASCs-\<Number\>
    * \<SAPSystemSID\>-NIC-DB-\<Number\>

  * **Azure Storage-konton (endast ohanterade diskar)** :

  * **Tillgänglighets grupper** för:
    * Virtuella SAP-program Server-datorer: \<SAPSystemSID\>-avset-di
    * Virtuella SAP ASCS/SCS-kluster datorer: \<SAPSystemSID\>-avset-ASCS
    * Virtuella DBMS-kluster datorer: \<SAPSystemSID\>-avset-DB

  * **Intern Azure-belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instansen och IP-adressen \<SAPSystemSID\>-lb-ASCS
    * Med alla portar för SQL Server-DBMS och IP-\<SAPSystemSID\>– lb-DB

  * **Nätverks säkerhets grupp**: \<SAPSystemSID\>-NSG-ASCs-0  
    * Med en öppen extern Remote Desktop Protocol-port (RDP) till \<SAPSystemSID\>-ASCs-0 virtuell dator

> [!NOTE]
> Alla IP-adresser för nätverkskorten och de interna Azure-belastningsutjämnaren är dynamiska som standard. Ändra dem till statiska IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Distribuera virtuella datorer med företags nätverks anslutning (mellan platser) som ska användas i produktion
För produktion av SAP-system distribuerar du virtuella Azure-datorer med företags nätverks anslutning med hjälp av Azure VPN Gateway eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instansen. Det virtuella nätverket och under nätet har redan skapats och för beretts.
>
>

1. I Azure Portal i rutan **parametrar** i rutan **NEWOREXISTINGSUBNET** väljer du **befintlig**.
2. I rutan **SUBNETID** lägger du till den fullständiga strängen för ditt för beredda Azure Network Subnet-ID där du planerar att distribuera dina virtuella Azure-datorer.
3. Om du vill hämta en lista över alla Azure Network-undernät, kör du följande PowerShell-kommando:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   I fältet **ID** visas värdet för under nätets ID.
4. Om du vill hämta en lista över alla Undernäts-ID-värden kör du följande PowerShell-kommando:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Undernäts-ID: t ser ut så här:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Distribuera endast molnbaserade SAP-instanser för test och demo
Du kan distribuera SAP-systemet med hög tillgänglighet i en distributions modell som endast är molnbaserad. Den här typen av distribution är främst användbar för demo-och test användnings fall. Det passar inte för produktions användnings fall.

- I Azure Portal i rutan **parametrar** i rutan **NEWOREXISTINGSUBNET** väljer du **ny**. Lämna fältet **SUBNETID** tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt det virtuella Azure-nätverket och under nätet.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS-tjänsten i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbered infrastrukturen för arkitektur mal len 2

Du kan använda den här Azure Resource Manager mallen för SAP för att förenkla distributionen av de nödvändiga infrastruktur resurserna för SAP-arkitektur mal len 2.

Här kan du hämta Azure Resource Manager mallar för det här distributions scenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-avbildning med hjälp av Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Anpassad avbildning med hjälp av Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbered infrastrukturen för arkitektur mal len 3

Du kan förbereda infrastrukturen och konfigurera SAP för multi-SID. Du kan till exempel lägga till ytterligare en SAP ASCS/SCS-instans i en *befintlig* kluster konfiguration. Mer information finns i [Konfigurera ytterligare en SAP ASCS/SCS-instans för en befintlig kluster konfiguration för att skapa en SAP multi-sid-konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt kluster med flera säkerhets-ID kan du använda [snabb starts mallar för](https://github.com/Azure/azure-quickstart-templates)flera-sid på GitHub.

Om du vill skapa ett nytt kluster med flera säkerhets identifierare måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Mall för databas](#database-template)
* [Mall för program servrar](#application-servers-template)

I följande avsnitt finns mer information om de mallar och parametrar som du måste ange i mallarna.

### <a name="ASCS-SCS-template"></a>ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett Windows Server-redundanskluster som är värd för flera ASCS/SCS-instanser.

För att ställa in mallen ASCS/SCS multi-SID, i mallen [ASCS/SCS multi-sid][sap-templates-3-tier-multisid-xscs-marketplace-image] eller [ASCS/SCS multi-sid med Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]anger du värden för följande parametrar:

- **Resource prefix**: Ange resurspooler, som används för att ge prefix för alla resurser som skapas under distributionen. Eftersom resurserna inte tillhör bara ett SAP-system, är prefixet för resursen inte SID för ett SAP-system.  Prefixet måste vara mellan tre och sex tecken.
- **Typ av stack**: Välj BASTYPEN för SAP-systemet. Beroende på vilken typ av stack det handlar om Azure Load Balancer har en (endast ABAP eller Java) eller två privata IP-adresser (ABAP + Java) per SAP-system.
- **OS-typ**: Välj operativ system för de virtuella datorerna.
- **Antal SAP-system**: Välj det antal SAP-system som du vill installera i det här klustret.
- **System tillgänglighet**: Välj **ha**.
- **Administratörens användar namn och administratörs lösen ord**: skapa en ny användare som kan användas för att logga in på datorn.
- **Nytt eller befintligt undernät**: Ange om du vill skapa ett nytt virtuellt nätverk och undernät eller använda ett befintligt undernät. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
- **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut så här:

  /Subscriptions/\<prenumerations-ID\>/resourceGroups/\<resurs grupp namn\>/providers/Microsoft.Network/virtualNetworks/\<virtuellt nätverks namn\>/subnets/\<under näts namn\>

Mallen distribuerar en Azure Load Balancer instans, som har stöd för flera SAP-system:

- ASCS-instanserna har kon figurer ATS för instans nummer 00, 10, 20...
- SCS-instanserna har kon figurer ATS för instans nummer 01, 11, 21...
- ASCS ERS (endast Linux)-instanser har kon figurer ATS för instans nummer 02, 12, 22...
- SCS-instanserna för ERS (endast Linux) har kon figurer ATS för instans nummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 VIP (2 för Linux), 1x VIP för ASCS/SCS och 1x VIP för ERS (endast Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS-portar
Följande lista innehåller alla belastnings Utjämnings regler (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- Windows-/regionsspecifika portar för varje SAP-system: 445, 5985
- ASCS-portar (instans nummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instans nummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-portar på Linux (instans nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portar på Linux (instans nummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad att använda följande avsöknings portar (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- ASCS/SCS-avsöknings port för den interna belastningsutjämnaren: 620x0
- ERS intern belastnings Utjämnings port (endast Linux): 621x2

### <a name="database-template"></a>Mall för databas

Databas mal len distribuerar en eller två virtuella datorer som du kan använda för att installera RDBMS (Relations databas hanterings system) för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill ställa in databasens multi-SID-mall i mallen för [flera sid-][sap-templates-3-tier-multisid-db-marketplace-image] eller [databas-flera sid-mallar med hjälp av Managed disks][sap-templates-3-tier-multisid-db-marketplace-image-md]anger du värden för följande parametrar:

- **SAP-system-ID**: Ange SAP-system-ID: t för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
- **OS-typ**: Välj operativ system för de virtuella datorerna.
- **DbType**: Välj den typ av databas som du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **Hana** om du planerar att installera SAP HANA på de virtuella datorerna. Kontrol lera att du väljer rätt typ av operativ system. Välj **Windows** för SQL och välj en Linux-distribution för Hana. Azure Load Balancer som är anslutna till de virtuella datorerna har kon figurer ATS för att stödja den valda databas typen:
  * **SQL**: belastnings Utjämnings port 1433. Se till att använda den här porten för din SQL Server AlwaysOn-installation.
  * **Hana**: belastnings Utjämnings portarna 35015 och 35017. Se till att installera SAP HANA med instans nummer **50**.
  Belastningsutjämnaren använder avsöknings port 62550.
- **SAP-system storlek**: Ange antalet SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
- **System tillgänglighet**: Välj **ha**.
- **Administratörens användar namn och administratörs lösen ord**: skapa en ny användare som kan användas för att logga in på datorn.
- **Undernäts-ID**: Ange ID: t för det undernät som du använde vid distributionen av ASCS/SCS-mallen eller ID: t för det undernät som skapades som en del av ASCS/SCS-mallen.

### <a name="application-servers-template"></a>Mall för program servrar

Mallen program servrar distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill konfigurera en mall för program servrar med flera SID: t, i mallen för [program servrar med flera sid-][sap-templates-3-tier-multisid-apps-marketplace-image] eller [program servrar][sap-templates-3-tier-multisid-apps-marketplace-image-md], med hjälp av Managed disks, anger du värden för följande parametrar:

  -  **SAP-system-ID**: Ange SAP-system-ID: t för det SAP-system som du vill installera. ID används som prefix för de resurser som distribueras.
  -  **OS-typ**: Välj operativ system för de virtuella datorerna.
  -  **SAP-system storlek**: antalet SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
  -  **System tillgänglighet**: Välj **ha**.
  -  **Administratörens användar namn och administratörs lösen ord**: skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernäts-ID**: Ange ID: t för det undernät som du använde vid distributionen av ASCS/SCS-mallen eller ID: t för det undernät som skapades som en del av ASCS/SCS-mallen.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure-Virtual Network
I vårt exempel är adress utrymmet för Azure Virtual Network-instansen 10.0.0.0/16. Det finns ett undernät som heter undernät, med adress intervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar i nätverks inställningarna i gäst operativ systemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera alla nätverks inställningar i Azure. Tjänsten Dynamic Host Configuration Protocol (DHCP) sprider dina inställningar.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP-adresser

Utför följande steg för att ange de DNS-IP-adresser som krävs:

1. I rutan **DNS-servrar** i Azure Portal kontrollerar du att alternativet **DNS-servrar** för virtuella nätverk är inställt på **anpassad DNS**.
2. Välj inställningar baserat på vilken typ av nätverk du har. Mer information finns i följande resurser:
   * Lägg till IP-adresserna för lokala DNS-servrar.  
   Du kan utöka lokala DNS-servrar till de virtuella datorer som körs i Azure. I det scenariot kan du lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten på.
   * För VM-distributioner som är isolerade i Azure: distribuera ytterligare en virtuell dator i samma Virtual Network instans som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har konfigurerat för att köra DNS-tjänsten.

   ![Bild 2: Konfigurera DNS-servrar för Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Bild 2:** Konfigurera DNS-servrar för Azure Virtual Network_

   > [!NOTE]
   > Om du ändrar IP-adresserna för DNS-servrarna måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen och sprida nya DNS-servrar.
   >
   >

I vårt exempel installeras och konfigureras DNS-tjänsten på de virtuella Windows-datorerna:

| Rollen virtuell dator | Värddator namn för virtuell dator | Nätverks korts namn | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-Server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Värdnamn och statiska IP-adresser för SAP ASCS/SCS-klustrad instans och DBMS-klustrad instans

För lokal distribution behöver du dessa reserverade värdnamn och IP-adresser:

| Roll för virtuellt värd namn | Namn på virtuell värd | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första kluster namn för virtuell värd (för kluster hantering) |pr1-ascs-vir |10.0.0.42 |
| Namn på virtuell SAP-ASCS/SCS-instans |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster namn för virtuell värd (kluster hantering) |pr1-dbms-vir |10.0.0.32 |

När du skapar klustret skapar du de virtuella värd namnen PR1-ASCs-Vir och PR1-DBMS-Vir och de associerade IP-adresserna som hanterar själva klustret. Information om hur du gör detta finns i [samla in klusternoder i en kluster konfiguration][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Du kan skapa de andra två virtuella värd namnen manuellt, PR1-ASCs-SAP och PR1-DBMS-SAP och tillhör ande IP-adresser på DNS-servern. Den klustrade SAP-ASCS/SCS-instansen och den klustrade DBMS-instansen använder dessa resurser. Information om hur du gör detta finns i [skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ange statiska IP-adresser för virtuella SAP-datorer
När du har distribuerat de virtuella datorerna som ska användas i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gäst operativ systemet.

1. I Azure Portal väljer du **resurs grupp** > **nätverkskort** > **Inställningar** > **IP-adress**.
2. I rutan **IP-adresser** under **tilldelning**väljer du **statisk**. I rutan **IP-adress** anger du den IP-adress som du vill använda.

   > [!NOTE]
   > Om du ändrar IP-adressen för nätverkskortet måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen.  
   >
   >

   ![Bild 3: ange statiska IP-adresser för nätverkskortet för varje virtuell dator][sap-ha-guide-figure-3002]

   _**Bild 3:** Ange statiska IP-adresser för nätverkskortet för varje virtuell dator_

   Upprepa det här steget för alla nätverks gränssnitt, det vill säga för alla virtuella datorer, inklusive virtuella datorer som du vill använda för Active Directory eller DNS-tjänsten.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen virtuell dator | Värddator namn för virtuell dator | Nätverks korts namn | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP Application Server-instansen |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |pr1-di-1 |PR1-NIC-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP Application Server-instans |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |PR1-ASCs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Andra klusternoden för ASCS/SCS-instans |PR1-ASCs-1 |PR1-NIC-ASCs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Andra klusternoden för DBMS-instans |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ange en statisk IP-adress för den interna Azure-belastningsutjämnaren

SAP Azure Resource Manager-mallen skapar en intern Azure-belastningsutjämnare som används för instans klustret SAP ASCS/SCS och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värd namnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren för SAP ASCS/SCS: PR1-lb-ASCS.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna DBMS-belastningsutjämnaren: PR1 – lb-DBMS.
>
>

Ange en statisk IP-adress för den interna Azure-belastningsutjämnaren:

1. Den inledande distributionen anger den interna IP-adressen för belastningsutjämnaren till **dynamisk**. I Azure Portal väljer du **statisk**i fönstret **IP-adresser** under **tilldelning**.
2. Ange IP-adressen för den interna belastningsutjämnaren **PR1-lb-ASCs** till IP-adressen för det virtuella värd namnet för SAP ASCS/SCS-instansen.
3. Ange IP-adressen för den interna belastningsutjämnaren **PR1 – lb-DBMS** till IP-adressen för den virtuella värd namnet för DBMS-instansen.

   ![Bild 4: ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen][sap-ha-guide-figure-3003]

   _**Bild 4:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen_

I vårt exempel har vi två interna Azure-belastningsutjämnare som har följande statiska IP-adresser:

| Rollen Azures interna belastnings utjämning | Namn på Azure-intern belastningsutjämnare | Statisk IP-adress |
| --- | --- | --- |
| Intern belastningsutjämnare för SAP ASCS/SCS-instans |PR1 – lb-ASCs |10.0.0.43 |
| Intern belastningsutjämnare för SAP-DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standard ASCS/SCS-belastnings Utjämnings regler för den interna Azure-belastningsutjämnaren

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med standard instans talet 00
* En Java SCS-instans med standard instans numret 01

När du installerar din SAP ASCS/SCS-instans måste du använda standard instansen 00 för din ABAP ASCS-instans och standard instans numret 01 för din Java SCS-instans.

Skapa sedan de nödvändiga slut punkterna för intern belastnings utjämning för SAP NetWeaver-portarna.

För att skapa nödvändiga interna belastnings Utjämnings slut punkter skapar du först dessa slut punkter för belastnings utjämning för SAP NetWeaver ABAP ASCS-portarna:

| Namn på tjänst/belastnings Utjämnings regel | Standard port nummer | Konkreta portar för (ASCS-instans med instans nummer 00) (ERS med 10) |
| --- | --- | --- |
| Köa Server/ *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP meddelande Server/ *lbrule3600* |36\<InstanceNumber\> |3600 |
| Internt ABAP-meddelande/ *lbrule3900* |39\<InstanceNumber\> |3900 |
| Meddelande Server HTTP/ *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Köa replikering/ *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| WinRM- *Lbrule5985* (Windows Remote Management) | |5985 |
| *Lbrule445* för fil resurs | |445 |

**Tabell 1:** Port nummer för SAP NetWeaver ABAP ASCS-instanser

Skapa sedan de här slut punkterna för belastnings utjämning för SAP NetWeaver Java SCS-portarna:

| Namn på tjänst/belastnings Utjämnings regel | Standard port nummer | Konkreta portar för (SCS-instans med instans nummer 01) (ERS med 11) |
| --- | --- | --- |
| Köa Server/ *lbrule3201* |32\<InstanceNumber\> |3201 |
| Gateway-server/ *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java-meddelande Server/ *lbrule3900* |39\<InstanceNumber\> |3901 |
| Meddelande Server HTTP/ *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Köa replikering/ *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM- *Lbrule5985* | |5985 |
| *Lbrule445* för fil resurs | |445 |

**Tabell 2:** Port nummer för SAP NetWeaver Java SCS-instanserna

![Bild 5: standard regler för ASCS/SCS-belastning för den interna Azure-belastningsutjämnaren][sap-ha-guide-figure-3004]

_**Figur 5:** Standard ASCS/SCS-belastnings Utjämnings regler för den interna Azure-belastningsutjämnaren_

Ange IP-adressen för belastningsutjämnaren PR1 – lb-DBMS till IP-adressen för det virtuella värd namnet för DBMS-instansen.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren

Om du vill använda olika nummer för SAP ASCS-eller SCS-instanserna måste du ändra namn och värden för deras portar från standardvärdena.

1. I Azure Portal väljer du **\<SID\>-lb-ASCs för belastnings** utjämning > **belastnings Utjämnings regler**.
2. Ändra följande värden för alla belastnings Utjämnings regler som tillhör SAP ASCS-eller SCS-instansen:

   * Namn
   * Port
   * Server dels port

   Om du till exempel vill ändra standard instans numret för ASCS från 00 till 31 måste du göra ändringarna för alla portar som anges i tabell 1.

   Här är ett exempel på en uppdatering för port *lbrule3200*.

   ![Bild 6: ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren][sap-ha-guide-figure-3005]

   _**Bild 6:** Ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Lägg till virtuella Windows-datorer i domänen

När du har tilldelat en statisk IP-adress till de virtuella datorerna lägger du till de virtuella datorerna i domänen.

![Bild 7: lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Figur 7:** Lägga till en virtuell dator i en domän_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Lägg till register poster på båda klusternoderna för SAP ASCS/SCS-instansen

Azure Load Balancer har en intern belastningsutjämnare som stänger anslutningar när anslutningarna är inaktiva under en angiven tids period (tids gräns för inaktivitet). SAP-arbetsprocesser i dialog instanser öppnar anslutningar till SAP-kön så snart den första kön/avqueue-begäran måste skickas. Dessa anslutningar förblir vanligt vis etablerade tills arbets processen eller omstarten av kön startas om. Om anslutningen däremot är inaktiv under en angiven tids period stängs anslutningarna av Azure Internal Load Balancer. Detta är inte ett problem eftersom SAP-arbetsprocessen återupprättar anslutningen till kön om den inte längre finns. Dessa aktiviteter dokumenteras i utvecklarnas spår för SAP-processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra TCP/IP-`KeepAliveTime` och `KeepAliveInterval` på båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP Profile-parametrar, som beskrivs senare i artikeln.

Om du vill lägga till register poster på båda klusternoderna för SAP ASCS/SCS-instansen lägger du först till dessa Windows-registerposter på båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabel typ |REG_DWORD (Decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabell 3:** Ändra den första TCP/IP-parametern

Lägg sedan till den här Windows-registerposten på båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabel typ |REG_DWORD (Decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabell 4:** Ändra den andra TCP/IP-parametern

Om du vill tillämpa ändringarna startar du om båda klusternoderna.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurera ett Windows Server-redundanskluster för en SAP ASCS/SCS-instans

Att konfigurera ett Windows Server-redundanskluster för en SAP ASCS/SCS-instans omfattar följande uppgifter:

- Samla in klusternoderna i en kluster konfiguration.
- Konfigurera ett kluster fil resurs vittne.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Samla in klusternoderna i en kluster konfiguration

1. I guiden Lägg till roller och funktioner lägger du till kluster för växling vid fel i båda klusternoderna.
2. Konfigurera redundansklustret med hjälp av Klusterhanteraren för växling vid fel. I Klusterhanteraren för växling vid fel väljer du **skapa kluster**och lägger sedan till endast namnet på det första klustret (nod A). Lägg inte till den andra noden ännu; du lägger till den andra noden i ett senare steg.

   ![Figur 8: Lägg till servern eller namnet på den virtuella datorn för den första klusternoden][sap-ha-guide-figure-3007]

   _**Figur 8:** Lägg till servern eller namnet på den virtuella datorn för den första klusternoden_

3. Ange Nätverks namnet (det virtuella värd namnet) för klustret.

   ![Bild 9: Ange kluster namnet][sap-ha-guide-figure-3008]

   _**Bild 9:** Ange kluster namnet_

4. När du har skapat klustret kör du ett kluster validerings test.

   ![Bild 10: kör kluster verifierings kontrollen][sap-ha-guide-figure-3009]

   _**Bild 10:** Kör kluster verifierings kontrollen_

   Du kan ignorera eventuella varningar om diskarna i processen. Du kommer att lägga till ett fil resurs vittne och de SIOS delade diskarna senare. I det här skedet behöver du inte bekymra dig om att ha ett kvorum.

   ![Bild 11: det gick inte att hitta någon kvorumdisk][sap-ha-guide-figure-3010]

   _**Bild 11:** Ingen kvorumdisk hittades_

   ![Bild 12: en kärn kluster resurs behöver en ny IP-adress][sap-ha-guide-figure-3011]

   _**Figur 12:** En kärn kluster resurs behöver en ny IP-adress_

5. Ändra IP-adressen för kärn kluster tjänsten. Klustret kan inte starta förrän du ändrar IP-adressen för kärn kluster tjänsten, eftersom IP-adressen för-servern pekar på någon av noderna för virtuella datorer. Gör detta på **egenskaps** sidan för kärn kluster tjänstens IP-resurs.

   Vi måste t. ex. tilldela en IP-adress (i vårt exempel 10.0.0.42) för det virtuella kluster värd namnet PR1-ASCs-vir.

   ![Bild 13: ändra IP-adressen i dialog rutan Egenskaper][sap-ha-guide-figure-3012]

   _**Figur 13:** Ändra IP-adressen i dialog rutan **Egenskaper**_

   ![Bild 14: tilldela IP-adressen som är reserverad för klustret][sap-ha-guide-figure-3013]

   _**Bild 14:** Tilldela den IP-adress som är reserverad för klustret_

6. Ta det virtuella kluster värd namnet online.

   ![Bild 15: kluster kärn tjänsten är igång med rätt IP-adress][sap-ha-guide-figure-3014]

   _**Bild 15:** Kluster kärn tjänsten är igång med rätt IP-adress_

7. Lägg till den andra klusternoden.

   Nu när kärn kluster tjänsten är igång kan du lägga till den andra klusternoden.

   ![Figur 16 Lägg till den andra klusternoden][sap-ha-guide-figure-3015]

   _**Bild 16:** Lägg till den andra klusternoden_

8. Ange ett namn för den andra klusternodens värd.

   ![Bild 17: Ange det andra värd namnet för klusternoden][sap-ha-guide-figure-3016]

   _**Bild 17:** Ange det andra värd namnet för klusternoden_

   > [!IMPORTANT]
   > Se till att kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret** *inte* är markerad.  
   >
   >

   ![Bild 18: Markera inte kryss rutan][sap-ha-guide-figure-3017]

   _**Bild 18:** Markera *inte* kryss rutan_

   Du kan ignorera varningar om kvorum och diskar. Du ställer in kvorumet och delar disken senare, enligt beskrivningen i [Installera SIOS DataKeeper Cluster Edition för en SAP ASCS/SCS-kluster resurs disk][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Bild 19: Ignorera varningar om kvorumdisken][sap-ha-guide-figure-3018]

   _**Bild 19:** Ignorera varningar om kvorumdisken_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurera ett kluster fil resurs vittne

Att konfigurera ett kluster fil resurs vittne omfattar följande uppgifter:

- Skapa en fil resurs.
- Ange fil resurs vittnets kvorum i Klusterhanteraren för växling vid fel.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Skapa en fil resurs

1. Välj ett fil resurs vittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

   I exemplen i den här artikeln finns fil resurs vittnet på den Active Directory eller DNS-server som körs i Azure. Fil resurs vittnet kallas domcontr-0. Eftersom du skulle ha konfigurerat en VPN-anslutning till Azure (via VPN Gateway eller Azure ExpressRoute) är din Active Directory-eller DNS-tjänst lokalt och är inte lämplig för att köra ett fil resurs vittne.

   > [!NOTE]
   > Om Active Directory-eller DNS-tjänsten endast körs lokalt ska du inte konfigurera fil resurs vittnet på den Active Directory eller det DNS-Windows-operativsystem som körs lokalt. Nätverks fördröjningen mellan klusternoder som körs i Azure och Active Directory eller DNS lokalt kan vara för stor och orsaka anslutnings problem. Se till att konfigurera fil resurs vittnet på en virtuell Azure-dator som kör nära klusternoden.  
   >
   >

   Kvorumdisken måste ha minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för kvorumdisken.

2. Lägg till objektet kluster namn.

   ![Bild 20: Tilldela behörigheterna till resursen för objektet kluster namn][sap-ha-guide-figure-3019]

   _**Bild 20:** Tilldela behörighet till resursen för objektet kluster namn_

   Se till att behörigheterna omfattar behörigheten att ändra data i resursen för objektet kluster namn (i vårt exempel PR1-ASCs-vir $).

3. Välj **Lägg**till om du vill lägga till kluster namns objekt i listan. Ändra filtret för att kontrol lera dator objekt, utöver de som visas i bild 22.

   ![Bild 21: ändra objekt typer för att inkludera datorer][sap-ha-guide-figure-3020]

   _**Bild 21:** Ändra **objekt typer** för att inkludera datorer_

   ![Bild 22: Markera kryss rutan datorer][sap-ha-guide-figure-3021]

   _**Bild 22:** Markera kryss rutan **datorer**_

4. Ange kluster namns objekt så som visas i bild 21. Eftersom posten redan har skapats kan du ändra behörigheterna, som du ser i bild 20.

5. Välj fliken **säkerhet** i resursen och ange sedan mer detaljerade behörigheter för objektet kluster namn.

   ![Bild 23: Ange Säkerhetsattributen för objektet kluster namn på fil resursens kvorum][sap-ha-guide-figure-3022]

   _**Figur 23:** Ange Säkerhetsattributen för objektet kluster namn på fil resursens kvorum_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ange fil resurs vittnets kvorum i Klusterhanteraren för växling vid fel

1. Öppna guiden Konfigurera kvorumkonfigurationen.

   ![Bild 24: starta guiden Konfigurera klusterkvorum][sap-ha-guide-figure-3023]

   _**Bild 24:** Starta guiden Konfigurera klusterkvorum_

2. På sidan **Välj konfigurations alternativ för kvorum** väljer **du Välj ett kvorum**.

   ![Bild 25: kvorumkonfigurationen som du kan välja mellan][sap-ha-guide-figure-3024]

   _**Bild 25:** Kvorumresurser du kan välja mellan_

3. På sidan **Välj kvorumdisk** väljer du **Konfigurera ett fil resurs vittne**.

   ![Bild 26: Välj fil resurs vittnet][sap-ha-guide-figure-3025]

   _**Bild 26:** Välj fil resurs vittnet_

4. Ange UNC-sökvägen till fil resursen (i vårt exempel \\domcontr-0\FSW). Om du vill se en lista över de ändringar som du kan göra väljer du **Nästa**.

   ![Bild 27: definiera fil resurs platsen för vittnes resursen][sap-ha-guide-figure-3026]

   _**Bild 27:** Definiera fil resurs platsen för vittnes resursen_

5. Välj de ändringar som du vill använda och välj sedan **Nästa**. Du måste konfigurera om kluster konfigurationen på det sätt som visas i bild 28:  

   ![Bild 28: bekräfta att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

   _**Bild 28:** Bekräfta att du har konfigurerat om klustret_

När du har installerat Windows-redundansklustret måste du ändra vissa tröskelvärden så att de anpassar identifieringen av redundans till villkor i Azure. De parametrar som ska ändras dokumenteras i [Justera tröskelvärden för redundanskluster i nätverket][tuning-failover-cluster-network-thresholds]. Om du antar att de två virtuella datorerna som utgör Windows-klustrets konfiguration för ASCS/SCS finns i samma undernät, ändrar du följande parametrar till följande värden:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

De här inställningarna har testats med kunder och ger en bättre kompromiss. De är tillräckligt flexibla, men de ger också redundans som är tillräckligt snabba i verkliga fel tillstånd på en SAP-programvara eller i en nod eller ett VM-fel.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS Cluster Share disk

Nu har du en fungerande konfiguration av redundanskluster för Windows Server i Azure. Om du vill installera en SAP ASCS/SCS-instans behöver du en delad disk resurs. Du kan inte skapa de delade disk resurser du behöver i Azure. SIOS DataKeeper Cluster Edition är en lösning från tredje part som du kan använda för att skapa delade disk resurser.

Att installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klustrets resurs disk omfattar följande uppgifter:

- Lägg till Microsoft .NET Framework 3,5.
- Installera SIOS DataKeeper.
- Konfigurera SIOS-DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Lägg till .NET Framework 3,5
.NET Framework 3,5 aktive ras eller installeras inte automatiskt på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver att .NET finns på alla noder där du installerar DataKeeper måste du installera .NET Framework 3,5 på gäst operativ systemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3,5:

- Använd guiden Lägg till roller och funktioner i Windows, som du ser i bild 29:

  ![Bild 29: installera .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Bild 29:** Installera .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 30: förlopps indikator för installationen när du installerar .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Bild 30:** Installations förlopps indikator när du installerar .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommando rads verktyget DISM. exe. För den här typen av installation måste du ha åtkomst till katalogen SxS på installations mediet för Windows. Ange följande kommando i en upphöjd kommando tolk:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Skapa en virtuell delad lagring med SIOS DataKeeper genom att skapa en synkroniserad spegling och sedan simulera klusterdelad lagring.

Innan du installerar SIOS-programvaran skapar du DataKeeperSvc-domän användaren.

> [!NOTE]
> Lägg till domän användaren DataKeeperSvc i den lokala administratörs gruppen på båda klusternoderna.
>
>

Så här installerar du SIOS DataKeeper:

1. Installera SIOS-programvaran på båda klusternoderna.

   ![Installations program för SIOS][sap-ha-guide-figure-3030]

   ![Bild 31: första sidan i SIOS DataKeeper-installationen][sap-ha-guide-figure-3031]

   _**Bild 31:** Första sidan i SIOS DataKeeper-installationen_

2. Välj **Ja**i dialog rutan.

   ![Bild 32: DataKeeper informerar dig om att en tjänst kommer att inaktive ras][sap-ha-guide-figure-3032]

   _**Bild 32:** DataKeeper informerar dig om att en tjänst kommer att inaktive ras_

3. I dialog rutan rekommenderar vi att du väljer domän- **eller Server konto**.

   ![Bild 33: val av användare för SIOS-DataKeeper][sap-ha-guide-figure-3033]

   _**Bild 33:** Val av användare för SIOS-DataKeeper_

4. Ange det användar namn och lösen ord för domän kontot som du skapade för SIOS-DataKeeper.

   ![Bild 34: ange domän användar namn och lösen ord för SIOS DataKeeper-installationen][sap-ha-guide-figure-3034]

   _**Bild 34:** Ange domänens användar namn och lösen ord för SIOS DataKeeper-installationen_

5. Installera licens nyckeln för SIOS DataKeeper-instansen enligt bilden 35.

   ![Bild 35: Ange licens nyckeln för SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Bild 35:** Ange licens nyckeln för SIOS DataKeeper_

6. Starta om den virtuella datorn när du uppmanas till det.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Konfigurera SIOS DataKeeper

När du har installerat SIOS DataKeeper på båda noderna startar du konfigurationen. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare diskar som är anslutna till var och en av de virtuella datorerna.

1. Starta hanterings-och konfigurations verktyget för DataKeeper och välj sedan **Anslut Server**.

   ![Bild 36: hanterings-och konfigurations verktyg för SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Bild 36:** Hanterings-och konfigurations verktyg för SIOS-DataKeeper_

2. Ange namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till och, i ett andra steg, den andra noden.

   ![Bild 37: Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden][sap-ha-guide-figure-3037]

   _**Bild 37:** Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden_

3. Skapa ett replikerings jobb mellan de två noderna.

   ![Bild 38: skapa ett jobb för replikering][sap-ha-guide-figure-3038]

   _**Bild 38:** Skapa ett jobb för replikering_

   En guide vägleder dig genom processen med att skapa ett migreringsjobb.

4. Definiera namnet på replikeringslänken.

   ![Bild 39: definiera namnet på replikeringssystemet][sap-ha-guide-figure-3039]

   _**Bild 39:** Definiera namnet på det replikerade jobbet_

   ![Bild 40: definiera grunddata för noden, som ska vara den aktuella Källnoden][sap-ha-guide-figure-3040]

   _**Bild 40:** Definiera grunddata för noden, som ska vara den aktuella Källnoden_

5. Definiera namn, TCP/IP-adress och disk volym för målnoden.

   ![Bild 41: definiera namn, TCP/IP-adress och disk volym för den aktuella målnoden][sap-ha-guide-figure-3041]

   _**Bild 41:** Definiera namn, TCP/IP-adress och disk volym för den aktuella målnoden_

6. Definiera algoritmerna för komprimering. I vårt exempel rekommenderar vi att du komprimerar replik strömmen. I synnerhet i omsynkroniseringen minskar replikeringen av replikeringsuppsättningen dramatiskt omsynkroniseringen avsevärt. Komprimering använder processor-och RAM-resurser för en virtuell dator. I takt med att komprimerings hastigheten ökar, så är volymen av de processor resurser som används. Du kan ändra den här inställningen senare.

7. En annan inställning som du måste kontrol lera är om replikeringen sker asynkront eller synkront. När du skyddar SAP ASCS/SCS-konfigurationer måste du använda synkron replikering.  

   ![Bild 42: definiera replikeringsinformation][sap-ha-guide-figure-3042]

   _**Bild 42:** Definiera replikeringsinformation_

8. Ange om den volym som replikeras av replikeringssystemet ska representeras för en Windows Server-redundanskluster som en delad disk. För SAP ASCS/SCS-konfigurationen väljer du **Ja** så att Windows-klustret ser den replikerade volymen som en delad disk som kan användas som en kluster volym.

   ![Bild 43: Välj Ja för att ställa in den replikerade volymen som en kluster volym][sap-ha-guide-figure-3043]

   _**Bild 43:** Välj **Ja** för att ställa in den replikerade volymen som en kluster volym_

   När volymen har skapats visar hanterings-och konfigurations verktyget för DataKeeper att replikeringsläget är aktivt.

   ![Bild 44: DataKeeper synkron spegling för resursen SAP ASCS/SCS är aktiv][sap-ha-guide-figure-3044]

   _**Bild 44:** DataKeeper synkron spegling för resurs disken för SAP ASCS/SCS är aktiv_

   Klusterhanteraren för växling vid fel visar nu disken som en DataKeeper disk, som du ser i bild 45:

   ![Bild 45: Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _**Bild 45:** Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade_

## <a name="next-steps"></a>Nästa steg

* [Installera SAP NetWeaver HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP-ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
