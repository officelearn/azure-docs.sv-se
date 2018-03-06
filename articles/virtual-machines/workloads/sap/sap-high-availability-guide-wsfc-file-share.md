---
title: "Klustret en SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure | Microsoft Docs"
description: "Lär dig mer om klustret en SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fa09de63c6f148064861479138934976f1f3e38
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Klustret en SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en filresurs i Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server failover-kluster är grunden för en hög tillgänglighet SAP ASCS/SCS installation och DBMS i Windows.

Ett redundanskluster är en grupp 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om det inträffar ett nodfel, beräknar Windows Server failover clustering antalet fel som kan uppstå och ändå upprätthålla ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja från olika kvorumlägen att uppnå failover-kluster.

## <a name="prerequisites"></a>Krav
Innan du börjar de uppgifter som beskrivs i den här artikeln bör du läsa den här artikeln:

* [Arkitektur för hög tillgänglighet för Azure virtuella datorer och scenarier för SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Klustring SAP ASCS/SCS instanser med hjälp av en filresurs stöds för SAP NetWeaver 7.40 (och senare), med SAP Kernel 7.49 (och senare).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server failover-kluster i Azure

Azure Virtual Machines kräver jämfört med bare metal- eller privat molndistributioner ytterligare åtgärder för att konfigurera Windows Server failover clustering. När du skapar ett kluster måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namnmatchning i Azure och klusternamnet för virtuell värddator

Plattformen Azure-molnet kan inte välja att konfigurera den virtuella IP-adresser som flytande IP-adresser. Du måste en alternativ lösning för att konfigurera en virtuell IP-adress till klusterresursen i molnet. 

Tjänsten Azure belastningsutjämnare ger en *intern belastningsutjämnare* för Azure. Med den interna belastningsutjämnaren nå klienter klustret via klustrets virtuella IP-adress. 

Distribuera den interna belastningsutjämnaren i resursgruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga port vidarebefordran regler med hjälp av avsökningen portar för den interna belastningsutjämnaren. Klienterna kan ansluta via virtuella värdnamn. DNS-servern matchar IP-klusteradress. Den interna belastningsutjämnaren hanterar vidarebefordrade portar på den aktiva noden i klustret.

![Bild 1: Windows Server Failover Clustering konfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 1:** Windows Server failover-kluster konfiguration i Azure utan en delad disk_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS hög tillgänglighet med filresurs

SAP har utvecklat en ny metod och ett alternativ till att klustra delade diskar för klustring en SAP ASCS/SCS-instans på en Windows-redundanskluster. Du kan använda en SMB filresurs för att distribuera SAP globala värdfiler istället för att använda delade klusterdiskar.

> [!NOTE]
> En SMB filresurs är ett alternativ till att använda delade klusterdiskar för klustring SAP ASCS/SCS instanser.  
>

Den här arkitekturen är specifikt på följande sätt:

* Central SAP-tjänster (med sin egen fil struktur och meddelandet och sätta processer) skiljer sig från SAP globala värdfiler.
* Central SAP-tjänsterna köras under en SAP ASCS/SCS-instans.
* SAP ASCS/SCS-instans är grupperad och kan nås med hjälp av den \<ASCS/SCS virtuell värdnamn\> virtuellt värdnamn.
* SAP globala filer placeras på SMB-filresursen och kan nås via de \<SAP globala värden\> värdnamn: \\ \\ &lt;SAP globala värden&gt;\sapmnt\\ &lt;SID&gt;\SYS\....
* SAP ASCS/SCS-instansen är installerad på en lokal disk på båda klusternoderna.
* Den \<ASCS/SCS virtuell värdnamn\> nätverksnamn skiljer sig från &lt;SAP globala värden&gt;.

![Bild 2: SAP ASCS/SCS HA arkitektur med SMB-filresurs][sap-ha-guide-figure-8004]

_**Bild 2:** nya SAP ASCS/SCS HA arkitekturen med en SMB-filresurs_

Krav för en SMB filresurs:

* SMB 3.0 (eller senare)-protokollet.
* Möjlighet att ställa in åtkomst till Active Directory Kontrollistor (ACL) för Active Directory-användare, grupper och `computer$` datorobjekt.
* Filresursen måste vara aktiverad med hög tillgänglighet:
    * Diskar som används för att lagra filer får inte vara en enskild felpunkt.
    * Servern eller VM driftstopp orsakar inte driftstopp på filresursen.

SAP \<SID\> klusterrollen innehåller inte klusterdelade diskar eller en allmän filresurs för klustret.


![Bild 3: SAP \<SID\> klusterresurser roll för att använda en filresurs][sap-ha-guide-figure-8005]

_**Bild 3:** SAP &lt;SID&gt; klusterresurser roll för att använda en filresurs_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Skalbara filresurser med Storage Spaces Direct i Azure som en SAPMNT filresurs

Du kan använda en skalbar filresurs för att skydda SAP globala värdfiler. En skalbar filresurs erbjuder också en hög tillgänglighet SAPMNT file share-tjänsten.

![Bild 4: Skalbar filresurs som används för att skydda SAP globala värd för filer][sap-ha-guide-figure-8006]

_**Bild 4:** en skalbar filresurs som används för att skydda SAP globala värd för filer_

> [!IMPORTANT]
> Skalbara filresurser stöds fullt ut i Microsoft Azure-molnet och lokala miljöer.
>

En skalbar filresurs erbjuder en hög tillgänglighet och skalbara vågrätt SAPMNT filresurs.

Lagringsutrymmen används som en delad disk för en skalbar filresurs. Du kan använda lagringsutrymmen för att skapa hög tillgänglighet och skalbar lagring med hjälp av servrar med lokal lagring. Delad lagring som används för en skalbar filresurs, för SAP globala värd för filer, är inte en enskild felpunkt.

> [!IMPORTANT]
>Om du *inte* plan för att ställa in katastrofåterställning, bör du använda en skalbar filresurs som en lösning för en högtillgänglig filresurs i Azure.
>

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>SAP förutsättningar för skalbara filresurser i Azure

Om du vill använda en skalbar filresurs måste datorn uppfylla följande krav:

* Minst två klusternoder för en skalbar filresurs.
* Varje nod måste ha minst två lokala diskar.
* Prestanda anledning, måste du använda *spegling återhämtning*:
    * Dubbelriktad spegling för en skalbar filresurs med två noder i klustret.
    * Trevägs spegling för en skalbar filresurs med tre (eller fler) klusternoder.
* Vi rekommenderar tre (eller fler) noder för en skalbar filresurs, med trevägs spegling.
    Den här inställningen ger mer skalbarhet och bättre lagring återhämtning än inställningen för resursen skalbar filserver med två noder i klustret och dubbelriktad spegling.
* Du måste använda Azure Premium-diskar.
* Vi rekommenderar att du använder Azure hanterade diskar.
* Vi rekommenderar att du formatera volymerna med flexibelt filsystem (ReFS).
    * Mer information finns i [SAP Obs 1869038 - SAP-stöd för ReFs-filsystemet] [ 1869038] och [väljer filsystemet] [ planning-volumes-s2d-choosing-filesystem] kapitel i den artikeln planera volymer i Storage Spaces Direct.
    * Se till att du installerar [Microsoft KB4025334 kumulativ uppdatering][kb4025334].
* Du kan använda DS-serien eller DSv2-serien Azure VM-storlekar.
* Använd en VM-typ som har minst en ”hög” bandbredd för bra nätverksprestanda mellan virtuella datorer, vilket krävs för Storage Spaces Direct disk synkronisering.
    Mer information finns i [DSv2-serien] [ dv2-series] och [DS-serien] [ ds-series] specifikationer.
* Vi rekommenderar att du reservera vissa lediga kapacitet i lagringspoolen. Lämna vissa lediga kapacitet i lagringspoolen ger volymer med utrymme för att reparera ”på plats” om en enhet misslyckas. Detta förbättrar säkerheten för data och prestanda.  Mer information finns i [väljer storlek][choosing-the-size-of-volumes-s2d].
* Skalbar filresurs virtuella Azure-datorer måste distribueras i sina egna Azure tillgänglighetsuppsättning.
* Du behöver inte konfigurera Azure intern belastningsutjämnare för resursnamn nätverket skalbar filserver som för \<SAP globala värden\>. Detta görs de \<ASCS/SCS virtuell värdnamn\> för DBMS eller SAP ASCS/SCS-instans. En skalbar filresurs skalas ut belastningen över alla noder i klustret. \<SAP globala värden\> använder den lokala IP-adressen för alla klusternoder.


> [!IMPORTANT]
> Det går inte att byta namn på filresursen SAPMNT som pekar på \<SAP globala värden\>. SAP stöder bara den resursen namnet ”sapmnt”.

> Mer information finns i [SAP-kommentar 2492395 - kan dela namnet sapmnt ändras?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurera SAP ASCS- / SCS instanser och en skalbar filresurs i två kluster

Du kan distribuera SAP ASCS/SCS-instanser i ett kluster med sina egna SAP \<SID\> klusterrollen. I så fall måste konfigurera du skalbara filresursen på ett annat kluster med en annan klustrad roll.

> [!IMPORTANT]
>I det här scenariot SAP ASCS/SCS-instansen har konfigurerats för att komma åt den globala SAP-värden med hjälp av UNC-sökvägen \\ \\ &lt;SAP globala värden&gt;\sapmnt\\&lt;SID&gt;\SYS\.
>

![Bild 5: SAP ASCS/SCS-instans och en skalbar filresurs som distribuerats i två kluster][sap-ha-guide-figure-8007]

_**Bild 5:** en SAP ASCS/SCS-instans och en skalbar filresurs distribueras i två kluster_

> [!IMPORTANT]
> Varje kluster som används för SAP och skalbar filserver resurser måste distribueras i sin egen Azure tillgänglighet anges i Azure-molnet. Detta säkerställer distribuerade placering i kluster virtuella datorer över underliggande Azure-infrastrukturen.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Allmän filresursen med SIOS DataKeeper som klusterdelade delade diskar


> [!IMPORTANT]
> Vi rekommenderar en skalbar filserver resursen lösning för en högtillgänglig filresurs.
>
> Om du planerar att också ange katastrofåterställning för hög tillgänglighet filresursen, måste du använda en allmän filresursen och SISO DataKeeper för dina delade klusterdiskar.
>

En allmän filresurs är ett annat alternativ för att uppnå hög tillgänglighet filresurs.

I det här fallet kan du använda en tredje parts SIOS lösning som en delad klusterdisk.

## <a name="next-steps"></a>Nästa steg

* [Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows failover-kluster och filresursen för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-file-share]
* [Installera SAP NetWeaver hög tillgänglighet på en Windows failover-kluster och filresursen för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
* [Distribuera en tvånods-Storage Spaces Direct skalbar filserver för UPD lagring i Azure][deploy-sofs-s2d-in-azure]
* [Lagringsutrymmen direkt i Windows Server 2016][s2d-in-win-2016]
* [Ingående: volymer i lagringsutrymmen][deep-dive-volumes-in-s2d]
