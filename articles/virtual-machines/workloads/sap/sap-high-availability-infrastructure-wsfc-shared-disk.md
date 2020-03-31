---
title: Azure-infrastruktur för SAP ASCS/SCS med WSFC-&delad disk | Microsoft-dokument
description: Lär dig hur du förbereder Azure-infrastrukturen för SAP HA med hjälp av ett Windows redundanskluster och delad disk för en SAP ASCS/SCS-instans.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279863"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Förbereda Azure-infrastrukturen för SAP HA med hjälp av ett Windows redundanskluster och delad disk för SAP ASCS/SCS

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (KONFIGURATION med flera SID med hög tillgänglighet)

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

I den här artikeln beskrivs de åtgärder du vidtar för att förbereda Azure-infrastrukturen för att installera och konfigurera ett SAP-system med hög tillgänglighet på ett Windows-redundanskluster med hjälp av en *klusterdelad disk* som ett alternativ för kluster av en SAP ASCS-instans.

## <a name="prerequisites"></a>Krav

Innan du påbörjar installationen läser du den här artikeln:

* [Arkitekturguide: Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbereda infrastrukturen för arkitektonisk mall 1
Azure Resource Manager-mallar för SAP hjälper till att förenkla distributionen av nödvändiga resurser.

Mallarna på tre nivåer i Azure Resource Manager stöder också scenarier med hög tillgänglighet. Arkitekturmall 1 har till exempel två kluster. Varje kluster är en SAP enda felpunkt för SAP ASCS/SCS och DBMS.

Här kan du hämta Azure Resource Manager-mallar för det exempelscenario som vi beskriver i den här artikeln:

* [Avbildning av Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-avbildning med hjälp av Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Så här förbereder du infrastrukturen för arkitektonisk mall 1:

- I rutan Parametrar i fönstret **SystemavaILABILITY** i Azure-portalen väljer du **HA**. **Parameters**

  ![Bild 1: Ange Azure Resource Manager-parametrar med hög tillgänglighet][sap-ha-guide-figure-3000]

_**Figur 1:** Ange Azure Resource Manager-parametrar med hög tillgänglighet_


  Mallarna skapar:

  * **Virtuella datorer:**
    * Virtuella SAP-programserver: \<SAPSystemSID\>\<-di- Antal\>
    * VIRTUELLA DATORER i ASCS/SCS-kluster: \<SAPSystemSID\>-ascs-\<Antal\>
    * DBMS-kluster: \<SAPSystemSID\>\<-db- Antal\>

  * **Nätverkskort för alla virtuella datorer, med tillhörande IP-adresser:**
    * \<SAPSystemSID\>-nic-di-\<Antal\>
    * \<SAPSystemSID\>-nic-ascs-\<Antal\>
    * \<SAPSystemSID\>-nic-db-\<Antal\>

  * **Azure-lagringskonton (endast ohanterade diskar):**

  * **Tillgänglighetsgrupper** för:
    * Virtuella SAP-programserver: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS-kluster \<virtuella datorer:\>SAPSystemSID -avset-ascs
    * DBMS kluster virtuella \<datorer:\>SAPSystemSID -avset-db

  * **Azure intern belastningsutjämnare:**
    * Med alla portar för ASCS/SCS-instansen och IP-adressen \<SAPSystemSID\>-lb-ascs
    * Med alla portar för SQL Server \<DBMS\>och IP-adress SAPSystemSID -lb-db

  * **Nätverkssäkerhetsgrupp**: \<SAPSystemSID\>-nsg-ascs-0  
    * Med en port för open external Remote \<Desktop Protocol\>(RDP) till den virtuella SAPSystemSID -ascs-0 virtuella datorn

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare är dynamiska som standard. Ändra dem till statiska IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Distribuera virtuella datorer med företagsnätverksanslutning (korslokalisering) som ska användas i produktion
För sap-system för produktion distribuerar du virtuella Azure-datorer med företagsnätverksanslutning med hjälp av Azure VPN Gateway eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda din Azure Virtual Network-instans. Det virtuella nätverket och undernätet har redan skapats och förberetts.
>
>

1. Välj **befintliga**i rutan PARAMETRAR i rutan **NEWOREXISTINGSUBNET** i Azure-portalen i fönstret **Parametrar.**
2. Lägg till hela strängen i ditt förberedda Azure-nätverksundernäts-ID i rutan **SUBNETID** där du planerar att distribuera dina virtuella Azure-datorer.
3. Om du vill hämta en lista över alla Undernät i Azure-nätverket kör du det här PowerShell-kommandot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Id-fältet** visar värdet för undernäts-ID.
4. Om du vill hämta en lista över alla värden i undernäts-ID kör du det här PowerShell-kommandot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Undernäts-ID:et ser ut så här:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Distribuera SAP-instanser endast för molnet för test och demo
Du kan distribuera ditt SAP-system med hög tillgänglighet i en distributionsmodell för endast molnet. Den här typen av distribution är främst användbar för demo- och testanvändningsfall. Det är inte lämpligt för produktionsanvändningsfall.

- Välj **nytt**i rutan **NEWOREXISTINGSUBNET** i rutan Parametrar i azure-portalen i fönstret **Parametrar.** Lämna **SUBNETID-fältet** tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt det virtuella Azure-nätverket och undernätet.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS-tjänst i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbereda infrastrukturen för arkitektonisk mall 2

Du kan använda den här Azure Resource Manager-mallen för SAP för att förenkla distributionen av nödvändiga infrastrukturresurser för SAP Architectural Template 2.

Här kan du hämta Azure Resource Manager-mallar för det här distributionsscenariot:

* [Avbildning av Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbereda infrastrukturen för arkitektonisk mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för flera SID. Du kan till exempel lägga till ytterligare en SAP ASCS/SCS-instans i en *befintlig* klusterkonfiguration. Mer information finns i [Konfigurera ytterligare en SAP ASCS/SCS-instans för en befintlig klusterkonfiguration för att skapa en SAP multi-SID-konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster kan du använda [snabbstartsmallarna för](https://github.com/Azure/azure-quickstart-templates)flera SID på GitHub .

Om du vill skapa ett nytt multi-SID-kluster måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Databasmall](#database-template)
* [Mall för programservrar](#application-servers-template)

Följande avsnitt innehåller mer information om de mallar och parametrar som du måste ange i mallarna.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett Windows Server-redundanskluster som är värd för flera ASCS/SCS-instanser.

Om du vill konfigurera ASCS/SCS multi-SID-mallen anger du värden för följande parametrar i [ASCS/SCS-mallen för flera SID-mallar för ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] med [flera SID-mallar för ASCS/SCS med hjälp av Hanterade diskar:][sap-templates-3-tier-multisid-xscs-marketplace-image-md]

- **Resursprefix:** Ange resursprefixet, som används för att prefixa alla resurser som skapas under distributionen. Eftersom resurserna inte bara tillhör ett SAP-system är resursprefixet inte SID för ett SAP-system.  Prefixet måste vara mellan tre och sex tecken.
- **stapeltyp:** Välj stacktyp för SAP-systemet. Beroende på stacktypen har Azure Load Balancer en (endast ABAP eller Java) eller två (ABAP+Java) privata IP-adresser per SAP-system.
- **OS-typ:** Välj operativsystemet för de virtuella datorerna.
- **ANTAL SAP-system:** Välj antalet SAP-system som du vill installera i det här klustret.
- **Systemtillgänglighet:** Välj **HA**.
- **Administratörsanvändarnamn och administratörslösenord:** Skapa en ny användare som kan användas för att logga in på datorn.
- **Nytt eller befintligt undernät**: Ange om du vill skapa ett nytt virtuellt nätverk och ett nytt undernät eller använda ett befintligt undernät. Om du redan har ett virtuellt nätverk som är anslutet till det lokala nätverket väljer du **befintlig**.
- **Undernäts-ID:** Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat att den virtuella datorn ska tilldelas, namnger du ID:t för det specifika undernätet. ID brukar se ut så här:

  \</subscriptions/subscription id /resourceGroups/resource group name /providers/Microsoft.Network/virtualNetworks/ virtual network name /subnets/ subnet name /subscription id\>/resourceGroups/\<resource group\>name /providers/Microsoft.Network/virtualNetworks/\<virtual network name\>/subnets/\<subnet name /\>

Mallen distribuerar en Azure Load Balancer-instans, som stöder flera SAP-system:

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS Enqueue Replication Server (endast Linux) instanser är konfigurerade till exempel nummer 02, 12, 22...
- Scs ERS-instanserna (endast Linux) är konfigurerade till exempel nummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 VIP(s) (2 för Linux), 1x VIP för ASCS/SCS och 1x VIP för ERS (endast Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS-portar
Följande lista innehåller alla belastningsutjämningsregler (där x är sap-systemets nummer, till exempel 1, 2, 3...):
- Windows-specifika portar för alla SAP-system: 445, 5985
- ASCS-portar (instansnummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instansnummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-portar på Linux (instansnummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portar på Linux (instansnummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad för att använda följande avsökningsportar (där x är sap-systemets nummer, till exempel 1, 2, 3...):
- ASCS/SCS interna lastutjämningsprodlarport: 620x0
- ERS interna lastutjämnare sond port (Endast Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Databasmall

Databasmallen distribuerar en eller två virtuella datorer som du kan använda för att installera relationsdatabashanteringssystemet (RDBMS) för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill konfigurera databasens multi-SID-mall anger du värden för följande parametrar i [databasens multi-SID-mall][sap-templates-3-tier-multisid-db-marketplace-image] eller [databasmall med flera SID-mallar med hjälp av Hanterade diskar:][sap-templates-3-tier-multisid-db-marketplace-image-md]

- **Sap-system-ID:** Ange SAP-system-ID för SAP-systemet som du vill installera. ID används som prefix för de resurser som distribueras.
- **Os typ:** Välj operativsystem för de virtuella datorerna.
- **Dbtype**: Välj den typ av databas som du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på de virtuella datorerna. Kontrollera att du väljer rätt operativsystemtyp. Välj **Windows** för SQL och välj en Linux-distribution för HANA. Azure Load Balancer som är ansluten till de virtuella datorerna är konfigurerad för att stödja den valda databastypen:
  * **SQL**: Belastningsutjämnarens belastningsutjämnadsport 1433. Se till att använda den här porten för din SQL Server AlwaysOn-konfiguration.
  * **HANA**: Belastningsutjämnares belastningsutjämnasportar 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
  Belastningsutjämnaren använder sondport 62550.
- **Sap System Storlek:** Ange antalet SAPS det nya systemet ger. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator.
- **Systemtillgänglighet:** Välj **HA**.
- **Administratörsanvändarnamn och administratörslösenord:** Skapa en ny användare som kan användas för att logga in på datorn.
- **Undernäts-ID:** Ange ID:t för det undernät som du använde under distributionen av ASCS/SCS-mallen eller ID:t för undernätet som skapades som en del av ASCS/SCS-malldistributionen.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Mall för programservrar

Programservermallen distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill konfigurera multi-SID-mallen för programservrar anger du värden för följande parametrar i [programservrarnas multi-SID-mall][sap-templates-3-tier-multisid-apps-marketplace-image] eller [programservrar med hjälp av Hanterade diskar:][sap-templates-3-tier-multisid-apps-marketplace-image-md]

  -  **Sap-system-ID:** Ange SAP-system-ID för SAP-systemet som du vill installera. ID används som prefix för de resurser som distribueras.
  -  **Os typ:** Välj operativsystem för de virtuella datorerna.
  -  **Sap System Storlek:** Antalet SAPS det nya systemet ger. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator.
  -  **Systemtillgänglighet:** Välj **HA**.
  -  **Administratörsanvändarnamn och administratörslösenord:** Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernäts-ID:** Ange ID:t för det undernät som du använde under distributionen av ASCS/SCS-mallen eller ID:t för undernätet som skapades som en del av ASCS/SCS-malldistributionen.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuella Azure-nätverk
I vårt exempel är adressutrymmet för Azure Virtual Network-instansen 10.0.0.0/16. Det finns ett undernät som heter Undernät, med ett adressintervall på 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar i nätverksinställningarna i gästoperativsystemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera alla nätverksinställningar i Azure. DHCP-tjänsten (Dynamic Host Configuration Protocol) sprider dina inställningar.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-adresser

Så här anger du de DNS-IP-adresser som krävs:

1. I fönstret **DNS-servrar i Azure-portalen** kontrollerar du att alternativet **dns-servrar** för virtuella nätverk är inställt på **Anpassad DNS**.
2. Välj dina inställningar baserat på vilken typ av nätverk du har. Mer information finns i följande resurser:
   * Lägg till IP-adresserna för de lokala DNS-servrarna.  
   Du kan utöka lokala DNS-servrar till de virtuella datorer som körs i Azure. I det scenariot kan du lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten på.
   * För VM-distributioner som är isolerade i Azure: Distribuera ytterligare en virtuell dator i samma instans för virtuellt nätverk som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har konfigurerat för att köra DNS-tjänsten.

   ![Bild 2: Konfigurera DNS-servrar för Virtuella Azure-nätverk][sap-ha-guide-figure-3001]

   _**Figur 2:** Konfigurera DNS-servrar för Azure Virtual Network_

   > [!NOTE]
   > Om du ändrar DNS-servrarnas IP-adresser måste du starta om virtuella Azure-datorer för att tillämpa ändringen och sprida de nya DNS-servrarna.
   >
   >

I vårt exempel installeras och konfigureras DNS-tjänsten på dessa virtuella Windows-datorer:

| Rollen för den virtuella datorn | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-servern |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Värdnamn och statiska IP-adresser för klustrade SAP ASCS/SCS-instans och DBMS-klustrade instans

För lokal distribution behöver du dessa reserverade värdnamn och IP-adresser:

| Roll för namn på virtuell värd | Namn på virtuellt värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första virtuella värdnamn för kluster (för klusterhantering) |pr1-ascs-vir |10.0.0.42 |
| Virtuellt värdnamn för SAP ASCS/SCS-instans |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (klusterhantering) |pr1-dbms-vir |10.0.0.32 |

När du skapar klustret skapar du de virtuella värdnamnen pr1-ascs-vir och pr1-dbms-vir och tillhörande IP-adresser som hanterar själva klustret. Information om hur du gör detta finns i [Samla in klusternoder i en klusterkonfiguration][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Du kan manuellt skapa de andra två virtuella värdnamnen, pr1-ascs-sap och pr1-dbms-sap och tillhörande IP-adresser på DNS-servern. Den klustrade SAP ASCS/SCS-instansen och den klustrade DBMS-instansen använder dessa resurser. Information om hur du gör detta finns i [Skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ange statiska IP-adresser för virtuella SAP-datorer
När du har distribuerat de virtuella datorer som ska användas i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1. Välj**IP-adress**för >  > **nätverkskortsinställningar för** >  **resursgrupp****i**Azure-portalen .
2. Välj **Statisk**under **Tilldelning**i **fönstret IP-adresser** . Ange den IP-adress som du vill använda i rutan **IP-adress.**

   > [!NOTE]
   > Om du ändrar IP-adressen för nätverkskortet måste du starta om virtuella Azure-datorer för att tillämpa ändringen.  
   >
   >

   ![Bild 3: Ange statiska IP-adresser för nätverkskortet för varje virtuell dator][sap-ha-guide-figure-3002]

   _**Figur 3:** Ange statiska IP-adresser för nätverkskortet för varje virtuell dator_

   Upprepa det här steget för alla nätverksgränssnitt, det vill säga för alla virtuella datorer, inklusive virtuella datorer som du vill använda för Active Directory eller DNS-tjänsten.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen för den virtuella datorn | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP-programserverinstans |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Andra SAP-programserverinstans |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP-programserverinstans |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Andra klusternod för ASCS/SCS-instans |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Den andra klusternoden för DBMS-instans |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ange en statisk IP-adress för Den interna lastbalanseraren i Azure

SAP Azure Resource Manager-mallen skapar en intern Azure-belastningsutjämningsfaktor som används för SAP ASCS/SCS-instansklustret och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för SAP ASCS/SCS interna belastningsutjämnaren: pr1-lb-ascs.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för DBMS interna belastningsutjämnare: pr1-lb-dbms.
>
>

Så här anger du en statisk IP-adress för den interna belastningsutjämnren i Azure:

1. Den första distributionen anger ip-adressen för intern belastningsutjämnare till **Dynamisk**. Välj **Statisk**i fönstret **Azure-portalen i IP-adressfönstret** . **Assignment**
2. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS-instansen.
3. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instansen.

   ![Bild 4: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen][sap-ha-guide-figure-3003]

   _**Figur 4:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen_

I vårt exempel har vi två interna Azure-belastningsutjämnare som har dessa statiska IP-adresser:

| Azure-rollen intern belastningsutjämning | Namn på Azure-internalsbalans | Statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS-instans intern belastningsutjämnare |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar du behöver:
* En ABAP ASCS-instans med standardinstansnummer 00
* En Java SCS-instans med standardinstansnummer 01

När du installerar SAP ASCS/SCS-instansen måste du använda standardinstansnummer 00 för DIN ABAP ASCS-instans och standardinstansnummer 01 för Java SCS-instansen.

Skapa sedan de interna belastningsutjämningsslutpunkterna för SAP NetWeaver-portarna.

Skapa först dessa belastningsutjämningsslutpunkter för SAP NetWeaver ABAP ASCS-portarna för att skapa obligatoriska interna belastningsutjämningsslutpunkter:

| Regelnamn för tjänst-/belastningsutjämning | Standardportnummer | Betongportar för (ASCS-instans med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Enqueue server / *lbrule3200* |32\<instansnummer\> |3200 |
| ABAP meddelandeserver / *lbrule3600* |36\<instansnummer\> |3600 |
| Internt ABAP-meddelande / *lbrule3900* |39\<Instansnummer\> |3900 |
| Meddelandeserver HTTP / *Lbrule8100* |81\<Instansnummer\> |8100 |
| SAP starttjänst ASCS HTTP / *Lbrule50013* |5\<Instansnummer\>13 |50013 |
| SAP-starttjänst ASCS HTTPS / *Lbrule50014* |5\<Instansnummer\>14 |50014 |
| Enqueue replikering / *Lbrule50016* |5\<Instansnummer\>16 |50016 |
| SAP starttjänst ERS HTTP *Lbrule51013* |5\<Instansnummer\>13 |51013 |
| SAP starttjänst ERS HTTP *Lbrule51014* |5\<Instansnummer\>14 |51014 |
| Windows Fjärrhantering (WinRM) *Lbrule5985* | |5985 |
| Fildelning *Lbrule445* | |445 |

**Tabell 1:** Portnummer för SAP NetWeaver ABAP ASCS-instanser

Skapa sedan dessa belastningsutjämningsslutpunkter för SAP NetWeaver Java SCS-portarna:

| Regelnamn för tjänst-/belastningsutjämning | Standardportnummer | Betongportar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Enqueue server / *lbrule3201* |32\<instansnummer\> |3201 |
| Gateway-server / *lbrule3301* |33\<instansnummer\> |3301 |
| Java meddelandeserver / *lbrule3900* |39\<Instansnummer\> |3901 |
| Meddelandeserver HTTP / *Lbrule8101* |81\<Instansnummer\> |8101 |
| SAP starttjänst SCS HTTP / *Lbrule50113* |5\<Instansnummer\>13 |50113 |
| SAP starttjänst SCS HTTPS / *Lbrule50114* |5\<Instansnummer\>14 |50114 |
| Enqueue replikering / *Lbrule50116* |5\<Instansnummer\>16 |50116 |
| SAP starttjänst ERS HTTP *Lbrule51113* |5\<Instansnummer\>13 |51113 |
| SAP starttjänst ERS HTTP *Lbrule51114* |5\<Instansnummer\>14 |51114 |
| Mer från WinRM *Lbrule5985* | |5985 |
| Fildelning *Lbrule445* | |445 |

**Tabell 2:** Portnummer för SAP NetWeaver Java SCS-instanser

![Bild 5: Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare][sap-ha-guide-figure-3004]

_**Figur 5:** Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare_

Ange IP-adressen för belastningsutjämnaren pr1-lb-dbms till IP-adressen för det virtuella värdnamnet för DBMS-instansen.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Ändra standardregler för belastningsutjämning för ASCS/SCS för den interna belastningsutjämnaren i Azure

Om du vill använda olika tal för SAP ASCS- eller SCS-instanserna måste du ändra namn och värden för deras portar från standardvärden.

1. I Azure-portalen väljer du >  ** \<SID\>-lb-ascs belastningsutjämningsregler**för**belastningsutjämning**.
2. För alla belastningsutjämningsregler som tillhör SAP ASCS- eller SCS-instansen ändrar du dessa värden:

   * Namn
   * Port
   * Backend-port

   Om du till exempel vill ändra standardnummer för ASCS-instans från 00 till 31 måste du göra ändringarna för alla portar som visas i tabell 1.

   Här är ett exempel på en uppdatering för port *lbrule3200*.

   ![Bild 6: Ändra standardbelastningsutjämningsreglerna för ASCS/SCS för standardbelastningsutjämning för Azures interna belastningsutjämnare][sap-ha-guide-figure-3005]

   _**Figur 6:** Ändra standardregler för belastningsutjämning för ASCS/SCS för den interna belastningsutjämnaren i Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Lägga till virtuella Windows-datorer i domänen

När du har tilldelat en statisk IP-adress till de virtuella datorerna lägger du till de virtuella datorerna i domänen.

![Bild 7: Lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Figur 7:** Lägga till en virtuell dator i en domän_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Lägga till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen

Azure Load Balancer har en intern belastningsutjämnare som stänger anslutningar när anslutningarna är inaktiva under en viss tidsperiod (en inaktiv timeout). SAP-arbetsprocesser i dialoginstanser öppnar anslutningar till SAP-enqueue-processen så snart den första enqueue/dequeue-begäran måste skickas. Dessa anslutningar förblir vanligtvis etablerade tills arbetsprocessen eller efterföljande processen startas om. Men om anslutningen är inaktiv under en viss tidsperiod, stänger Azure interna belastningsutjämnaren anslutningarna. Detta är inte ett problem eftersom SAP-arbetsprocessen återupprättar anslutningen till enqueue-processen om den inte längre finns. Dessa aktiviteter dokumenteras i utvecklarspårningarna av SAP-processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra `KeepAliveTime` TCP/IP och `KeepAliveInterval` på båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP-profilparametrar som beskrivs senare i artikeln.

Om du vill lägga till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen lägger du först till dessa Windows-registerposter i båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabell 3:** Ändra den första TCP/IP-parametern

Lägg sedan till den här Windows-registerposten på båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabell 4:** Ändra den andra TCP/IP-parametern

Om du vill tillämpa ändringarna startar du om båda klusternoderna.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurera ett Windows Server-redundanskluster för en SAP ASCS/SCS-instans

Att konfigurera ett Redundanskluster för Windows Server för en SAP ASCS/SCS-instans innebär följande uppgifter:

- Samla in klusternoderna i en klusterkonfiguration.
- Konfigurera ett klusterfilresursvittne.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Samla in klusternoderna i en klusterkonfiguration

1. Lägg till redundanskluster i båda klusternoderna i guiden Lägg till roll och funktioner.
2. Konfigurera redundansklustret med redundansklusterhanteraren. I Redundansklusterhanteraren väljer du **Skapa kluster**och lägger sedan bara till namnet på det första klustret (noden A). Lägg inte till den andra noden ännu. du lägger till den andra noden i ett senare steg.

   ![Bild 8: Lägga till server- eller virtuellt datornamn för den första klusternoden][sap-ha-guide-figure-3007]

   _**Figur 8:** Lägga till server- eller virtuellt datornamn för den första klusternoden_

3. Ange nätverkets namn (virtuellt värdnamn) för klustret.

   ![Bild 9: Ange klusternamnet][sap-ha-guide-figure-3008]

   _**Figur 9:** Ange klusternamnet_

4. När du har skapat klustret kör du ett klusterverifieringstest.

   ![Bild 10: Kör klustervalideringskontrollen][sap-ha-guide-figure-3009]

   _**Figur 10:** Kör klusterverifieringskontrollen_

   Du kan ignorera alla varningar om diskar just nu i processen. Du lägger till ett filresursvittne och SIOS delade diskar senare. I detta skede behöver du inte oroa dig för att ha ett kvorum.

   ![Bild 11: Ingen kvorumdisk hittades][sap-ha-guide-figure-3010]

   _**Figur 11:** Ingen kvorumdisk hittades_

   ![Bild 12: En kärnklusterresurs behöver en ny IP-adress][sap-ha-guide-figure-3011]

   _**Figur 12:** En kärnklusterresurs behöver en ny IP-adress_

5. Ändra IP-adressen för kärnklustertjänsten. Klustret kan inte starta förrän du ändrar IP-adressen för kärnklustertjänsten, eftersom serverns IP-adress pekar på en av noderna för den virtuella datorn. Gör detta på sidan **Egenskaper** för kärnklustertjänstens IP-resurs.

   Vi måste till exempel tilldela en IP-adress (i vårt exempel 10.0.0.42) för klustrets virtuella värdnamn pr1-ascs-vir.

   ![Bild 13: Ändra IP-adressen i dialogrutan Egenskaper][sap-ha-guide-figure-3012]

   _**Figur 13:** Ändra IP-adressen i dialogrutan **Egenskaper**_

   ![Bild 14: Tilldela DEN IP-adress som är reserverad för klustret][sap-ha-guide-figure-3013]

   _**Figur 14:** Tilldela IP-adressen som är reserverad för klustret_

6. Ta med det virtuella värdnamnet för klustret online.

   ![Bild 15: Klusterkärntjänsten är igång, med rätt IP-adress][sap-ha-guide-figure-3014]

   _**Figur 15:** Klusterkärnans tjänst är igång, med rätt IP-adress_

7. Lägg till den andra klusternoden.

   Nu när kärnklustertjänsten är igång kan du lägga till den andra klusternoden.

   ![Bild 16 Lägg till den andra klusternoden][sap-ha-guide-figure-3015]

   _**Figur 16:** Lägga till den andra klusternoden_

8. Ange ett namn för den andra klusternodvärden.

   ![Bild 17: Ange värdnamnet för den andra klusternoden][sap-ha-guide-figure-3016]

   _**Figur 17:** Ange värdnamnet för den andra klusternoden_

   > [!IMPORTANT]
   > Kontrollera att kryssrutan **Lägg till all kvalificerad lagring i klustret** *inte* är markerad.  
   >
   >

   ![Bild 18: Markera inte kryssrutan][sap-ha-guide-figure-3017]

   _**Figur 18:** Markera *inte* kryssrutan_

   Du kan ignorera varningar om kvorum och diskar. Du anger kvorumet och delar disken senare, enligt beskrivningen i [Installera SIOS DataKeeper Cluster Edition för en SAP ASCS/SCS-klusterresursdisk.][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]

   ![Bild 19: Ignorera varningar om diskkvorum][sap-ha-guide-figure-3018]

   _**Figur 19:** Ignorera varningar om diskkvorum_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurera ett vittne om klusterfilresurs

Konfigurera en klusterfil aktie vittne innebär dessa uppgifter:

- Skapa en filresurs.
- Ange kvorum för filresursvittnet i Redundansklusterhanteraren.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Skapa en filresurs

1. Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

   I exemplen i den här artikeln finns filresursvittnet på Active Directory- eller DNS-servern som körs i Azure. Filresursvittnet kallas domcontr-0. Eftersom du skulle ha konfigurerat en VPN-anslutning till Azure (via VPN Gateway eller Azure ExpressRoute) är din Active Directory- eller DNS-tjänst lokal och är inte lämplig att köra ett filresursvittne.

   > [!NOTE]
   > Om Active Directory- eller DNS-tjänsten bara körs lokalt ska du inte konfigurera filresursvittnet i Active Directory eller DNS Windows som körs lokalt. Nätverksfördröjning mellan klusternoder som körs i Azure och Active Directory eller DNS lokalt kan vara för stor och orsaka anslutningsproblem. Var noga med att konfigurera filresursvittnet på en virtuell Azure-dator som körs nära klusternoden.  
   >
   >

   Kvorumenheten behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för kvorumenheten.

2. Lägg till klusternamnsobjektet.

   ![Bild 20: Tilldela behörigheterna för resursen för klusternamnsobjektet][sap-ha-guide-figure-3019]

   _**Figur 20:** Tilldela behörigheterna för resursen för klusternamnsobjektet_

   Se till att behörigheterna innehåller behörigheten att ändra data i resursen för klusternamnsobjektet (i vårt exempel pr1-ascs-vir$).

3. Om du vill lägga till klusternamnsobjektet i listan väljer du **Lägg till**. Ändra filtret för att söka efter datorobjekt, utöver de som visas i figur 22.

   ![Bild 21: Ändra objekttyper så att datorer ingår][sap-ha-guide-figure-3020]

   _**Figur 21:** Ändra **objekttyper** så att datorer ingår_

   ![Bild 22: Markera kryssrutan Datorer][sap-ha-guide-figure-3021]

   _**Figur 22:** Markera kryssrutan **Datorer**_

4. Ange klusternamnsobjektet enligt figur 21. Eftersom posten redan har skapats kan du ändra behörigheterna, som visas i figur 20.

5. Välj fliken **Säkerhet** för resursen och ange sedan mer detaljerade behörigheter för klusternamnsobjektet.

   ![Bild 23: Ange säkerhetsattribut för klusternamnsobjektet i filresurskvorumet][sap-ha-guide-figure-3022]

   _**Figur 23:** Ange säkerhetsattribut för klusternamnsobjektet i filresurskvorumet_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ange kvorum för filresursvittnet i Klusterhanteraren för redundans

1. Öppna guiden Konfigurera kvoruminställning.

   ![Bild 24: Starta guiden Konfigurera klusterkvorum][sap-ha-guide-figure-3023]

   _**Figur 24:** Starta guiden Konfigurera klusterkvorum_

2. På sidan **Välj kvorumkonfigurationsalternativ** väljer du **Välj kvorumvittne**.

   ![Bild 25: Kvorumkonfigurationer du kan välja mellan][sap-ha-guide-figure-3024]

   _**Figur 25:** Kvorumkonfigurationer du kan välja mellan_

3. På sidan **Välj kvorumvittne** väljer du **Konfigurera ett filresursvittne**.

   ![Bild 26: Välj vittne om fildelning][sap-ha-guide-figure-3025]

   _**Figur 26:** Markera vittnet om filresurs_

4. Ange UNC-sökvägen till filresursen \\(i vårt exempel domcontr-0\FSW). Om du vill visa en lista över de ändringar du kan göra väljer du **Nästa**.

   ![Bild 27: Definiera platsen för filresursen för vittnesresursen][sap-ha-guide-figure-3026]

   _**Figur 27:** Definiera fildelningsplatsen för vittnesresursen_

5. Markera önskade ändringar och välj sedan **Nästa**. Du måste konfigurera om klusterkonfigurationen enligt figur 28:  

   ![Bild 28: Bekräftelse på att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

   _**Figur 28:** Bekräftelse på att du har konfigurerat om klustret_

När du har installerat Windows redundanskluster måste du ändra vissa tröskelvärden så att de anpassar redundanidentifiering till villkor i Azure. De parametrar som ska ändras dokumenteras i [Justera tröskelvärden för redundansklusternätverk][tuning-failover-cluster-network-thresholds]. Om du antar att de två virtuella datorerna som utgör Windows-klusterkonfigurationen för ASCS/SCS finns i samma undernät ändrar du följande parametrar till dessa värden:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Dessa inställningar har testats med kunder och erbjuder en bra kompromiss. De är tillräckligt motståndskraftiga, men de ger också redundans som är tillräckligt snabb i verkliga felförhållanden på en SAP-programvara eller i ett nod- eller vm-fel.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klusterresursdisken

Du har nu en fungerande Windows Server redundansklusterkonfiguration i Azure. Om du vill installera en SAP ASCS/SCS-instans behöver du en delad diskresurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en tredjepartslösning som du kan använda för att skapa delade diskresurser.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS klusterresurs disk innebär dessa uppgifter:

- Lägg till Microsoft .NET Framework 3.5.
- Installera SIOS DataKeeper.
- Ställ in SIOS DataKeeper.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Lägg till .NET Framework 3.5
.NET Framework 3.5 aktiveras eller installeras inte automatiskt på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver .NET för att vara på alla noder där du installerar DataKeeper, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows, som visas i bild 29:

  ![Bild 29: Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Figur 29:** Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 30: Förloppsindikator för installation när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Figur 30:** Installationsförloppsindikatorn när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommandoradsverktyget dism.exe. För den här typen av installation måste du komma åt SxS-katalogen i Windows-installationsmediet. Vid en upphöjd kommandotolk anger du det här kommandot:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Om du vill skapa virtuell delad lagring med SIOS DataKeeper skapar du en synkroniserad spegel och simulerar sedan klusterdelad lagring.

Innan du installerar SIOS-programvaran skapar du domänanvändaren DataKeeperSvc.

> [!NOTE]
> Lägg till DataKeeperSvc-domänanvändaren i gruppen Lokal administratör på båda klusternoderna.
>
>

Så här installerar du SIOS DataKeeper:

1. Installera SIOS-programvaran på båda klusternoderna.

   ![SIOS-installationsprogrammet][sap-ha-guide-figure-3030]

   ![Figur 31: Första sidan i SIOS DataKeeper-installationen][sap-ha-guide-figure-3031]

   _**Figur 31:** Första sidan i SIOS DataKeeper-installationen_

2. Välj **Ja**i dialogrutan .

   ![Bild 32: Datakeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

   _**Figur 32:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3. I dialogrutan rekommenderar vi att du väljer **Domän- eller Serverkonto**.

   ![Bild 33: Användarval för SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figur 33:** Användarval för SIOS DataKeeper_

4. Ange användarnamn och lösenord för domänkontot som du skapade för SIOS DataKeeper.

   ![Bild 34: Ange domännamn och lösenord för SIOS DataKeeper-installationen][sap-ha-guide-figure-3034]

   _**Figur 34:** Ange domännamn och lösenord för SIOS DataKeeper-installationen_

5. Installera licensnyckeln för din SIOS DataKeeper-instans, som visas i figur 35.

   ![Bild 35: Ange licensnyckeln för SIOS-datakeepern][sap-ha-guide-figure-3035]

   _**Figur 35:** Ange din SIOS-licensnyckel för DataKeeper_

6. Starta om den virtuella datorn när du uppmanas att göra det.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Konfigurera SIOS DataKeeper

Starta konfigurationen när du har installerat SIOS DataKeeper på båda noderna. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare diskar som är kopplade till var och en av de virtuella datorerna.

1. Starta verktyget DataKeeper Management and Configuration och välj sedan **Anslut server**.

   ![Bild 36: Verktyget för hantering och konfiguration av SIOS-datakeeper][sap-ha-guide-figure-3036]

   _**Figur 36:** Verktyg för hantering och konfiguration av SIOS-datakeeper_

2. Ange namnet eller TCP/IP-adressen för den första noden som hanterings- och konfigurationsverktyget ska ansluta till och, i ett andra steg, den andra noden.

   ![Bild 37: Infoga namnet eller TCP/IP-adressen för den första noden som verktyget Hantering och konfiguration ska ansluta till, och i ett andra steg ska den andra noden ansluta][sap-ha-guide-figure-3037]

   _**Figur 37:** Infoga namnet eller TCP/IP-adressen för den första noden som verktyget Hantering och konfiguration ska ansluta till, och i ett andra steg den andra noden_

3. Skapa replikeringsjobbet mellan de två noderna.

   ![Bild 38: Skapa ett replikeringsjobb][sap-ha-guide-figure-3038]

   _**Figur 38:** Skapa ett replikeringsjobb_

   En guide guidar dig genom processen att skapa ett replikeringsjobb.

4. Definiera namnet på replikeringsjobbet.

   ![Bild 39: Definiera namnet på replikeringsjobbet][sap-ha-guide-figure-3039]

   _**Figur 39:** Definiera namnet på replikeringsjobbet_

   ![Bild 40: Definiera basdata för noden, som ska vara den aktuella källnoden][sap-ha-guide-figure-3040]

   _**Figur 40:** Definiera basdata för noden, som ska vara den aktuella källnoden_

5. Definiera namn, TCP/IP-adress och diskvolym för målnoden.

   ![Bild 41: Definiera namn, TCP/IP-adress och diskvolym för den aktuella målnoden][sap-ha-guide-figure-3041]

   _**Figur 41:** Definiera namn, TCP/IP-adress och diskvolym för den aktuella målnoden_

6. Definiera komprimeringsalgoritmerna. I vårt exempel rekommenderar vi att du komprimerar replikeringsströmmen. Särskilt i omsynkroniseringssituationer minskar komprimeringen av replikeringsströmmen dramatiskt omsynkroniseringstiden. Komprimering använder CPU- och RAM-resurserna för en virtuell dator. När komprimeringshastigheten ökar ökar, så gör volymen av CPU-resurser som används. Du kan justera den här inställningen senare.

7. En annan inställning som du behöver kontrollera är om replikeringen sker asynkront eller synkront. När du skyddar SAP ASCS/SCS-konfigurationer måste du använda synkron replikering.  

   ![Bild 42: Definiera replikeringsinformation][sap-ha-guide-figure-3042]

   _**Figur 42:** Definiera replikeringsinformation_

8. Definiera om volymen som replikeras av replikeringsjobbet ska representeras till en Windows Server-redundansklusterkonfiguration som en delad disk. För SAP ASCS/SCS-konfigurationen väljer du **Ja** så att Windows-klustret ser den replikerade volymen som en delad disk som den kan använda som klustervolym.

   ![Bild 43: Välj Ja om du vill ställa in den replikerade volymen som en klustervolym][sap-ha-guide-figure-3043]

   _**Figur 43:** Välj **Ja** om du vill ange den replikerade volymen som en klustervolym_

   När volymen har skapats visar verktyget DataKeeper Management and Configuration att replikeringsjobbet är aktivt.

   ![Bild 44: DataKeeper synkron spegling för SAP ASCS/SCS-resursdisken är aktiv][sap-ha-guide-figure-3044]

   _**Figur 44:** DataKeeper synkron spegling för SAP ASCS /SCS-resursdisken är aktiv_

   Redundansklusterhanteraren visar nu disken som en DataKeeper-disk, som visas i bild 45:

   ![Bild 45: Redundansklusterhanteraren visar disken som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _**Figur 45:** Redundansklusterhanteraren visar disken som DataKeeper replikerade_

## <a name="next-steps"></a>Nästa steg

* [Installera SAP NetWeaver HA med hjälp av ett Windows redundanskluster och delad disk för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
