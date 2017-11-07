---
title: "Kluster för SAP-(A) SCS-instans på Windows-redundanskluster med hjälp av filresurs i Azure | Microsoft Docs"
description: "Klustring SAP (A) SCS instans på Windows-redundanskluster med hjälp av filresurs"
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
ms.openlocfilehash: 94d725cfb072091e57c96d3b2aca7b2e73657eef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
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
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
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


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Klustring SAP (A) SCS instans på Windows-redundanskluster med hjälp av filresurs på Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server Failover Clustering är grunden för en hög tillgänglighet SAP ASCS/SCS installation och DBMS i Windows.

Ett redundanskluster är en grupp 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om det inträffar ett nodfel, beräknar Windows Server Failover Clustering antalet fel som kan uppstå samtidigt som ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja från olika kvorumlägen att uppnå failover-kluster.

## <a name="prerequisite"></a>Krav
Se till att granska de här dokumenten innan du börjar med det här dokumentet:

* [Arkitektur för virtuella Azure-datorer med hög tillgänglighet och scenarier för SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>Klustring av SAP (A) SCS instanser med filresurs stöds för **SAP NetWeaver 7.40 (och senare)** produkter, med **SAP Kernel 7.49 (och senare)**.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server Failover-kluster i Azure

Azure Virtual Machines krävs jämfört med bare metal eller privat molndistributioner, ytterligare steg för att konfigurera Windows Server Failover Clustering. När du skapar ett kluster måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Namnmatchning i Azure och virtuella värdnamn för kluster

Plattformen Azure-molnet kan inte välja att konfigurera den virtuella IP-adresser som flytande IP-adresser. Du måste en alternativ lösning för att konfigurera en virtuell IP-adress till klusterresursen i molnet. Azure har en **intern belastningsutjämnare** i tjänsten Azure belastningsutjämnare. Med den interna belastningsutjämnaren nå klienter klustret via klustrets virtuella IP-adress. Du måste distribuera den interna belastningsutjämnaren i resursgruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga port vidarebefordran regler med avsökningen portar för den interna belastningsutjämnaren. Klienterna kan ansluta via virtuella värdnamn. DNS-servern löser klustrets IP-adress och interna belastningsutjämnare handtag porten vidarebefordran till den aktiva noden i klustret.

![Bild 1: Windows Server Failover Clustering konfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 1:** Windows Server Failover Clustering konfiguration i Azure utan en delad disk_

## <a name="sap-ascs-ha-with-file-share"></a>SAP-(A) SCS hög tillgänglighet med filresurs

SAP utvecklade ny metod och alternativ för klustret delade diskar till klustret SAP (A) SCS instans på Windows-redundanskluster.

Här används **SMB-filresurs** är ett alternativ för att distribuera **SAP globala värden filer**.

> [!NOTE]
>SMB-filresurs är ytterligare en möjlighet att klustra delade diskar för klustring för SAP (A) SCS instanser.  
>

Vad är specifik för den här arkitekturen är följande:

* **Central SAP-tjänster (med egna filen struktur och meddelandet och sätta processer) är avgränsade från SAP globala värd för filer**
* **Central SAP-tjänsterna körs under SAP (A) SCS-instans**
* SAP (A) SCS-instans är grupperad och kan nås med hjälp av virtuella värdnamn **< (A) SCSVirtualHostName >**
* SAP globala filer placeras på SMB-filresurs och hämtas med hjälp av den <SAPGLOBALHost> värdnamn \\ \\ &lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
* SAP (A) SCS-instansen är installerad på en lokal disk på båda klusternoderna
* Den **< (A) SCSVirtualHostName >** nätverksnamn skiljer sig från  **&lt;SAPGLOBALHost&gt;**

![Bild 2: Ny SAP (A) SCS HA arkitektur med SMB-filresurs][sap-ha-guide-figure-8004]

_**Bild 2:** ny SAP (A) SCS HA arkitektur med SMB-filresurs_

Krav för SMB-filresurs:

* SMB 3.0 (eller senare)-protokollet
* Möjlighet att ställa in Active Directory (AD) åtkomstkontrollistan (ACL) för **AD användargrupper** och **datorn objektet datorn$**
* Filresursen måste vara HA aktiverats:
    * Diskar som används för att lagra filer får inte vara en enskild felpunkt
    * Du måste se till att driftstopp för servrar/virtuella datorer som inte orsakar driftstopp för filresursen

Nu kan den **SAP &lt;SID&gt;**  klusterrollen inte innehåller klusterdelade diskar eller allmänna klustret filresurs.


![Bild 3: SAP < SID > roll klusterresurser när du använder filresurs][sap-ha-guide-figure-8005]

_**Bild 3:** **SAP &lt;SID&gt;**  klusterresurser roll vid användning av filresurs_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Skalbar filresurs (SOFS) med lagringsutrymmen direkt (S2D) på Azure som SAPMNT filresurs

Du kan använda SOFS skydda SAP globala värden filer, och att erbjuda hög tillgänglighet SAPMNT file share-tjänsten.

![Bild 4: SOFS filresurs som används för att skydda SAP globala värd för filer][sap-ha-guide-figure-8006]

_**Bild 4:** SOFS-filresurs som används för att skydda SAP globala värd för filer_

> [!IMPORTANT]
>SOFS filresursen stöds fullt ut på Microsoft Azure-molnet samt i miljöer med lokalt.
>

**SOFS** ger hög tillgänglighet och skalbara vågrätt SAPMNT filresurs.

**Storage Spaces Direct (S2D)**, används som **delad disk** gör skapa hög tillgänglighet och skalbar lagring med hjälp av servrar med lokal lagring för SOFS. Delad lagring som används för SOFS, t.ex. för SAP GLOBALHOST filer är därför inte en enskild felpunkt (SPOF).

> [!IMPORTANT]
>Om du planerar att installationsprogrammet för disaster recovery bör SOFS lösning för hög tillgänglighet filresurs i Azure.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>SAP-krav för SOFS i Azure

För SOFS måste du:

* Minst två klusternoder för SOFS

* Varje nod måste ha minst två lokala diskar

* Prestanda anledning, måste du använda **spegling återhämtning**:
    * **2-vägs** spegling för SOFS med två noder
    * **3-vägs** spegling för SOFS med tre (eller fler) klusternoder


* Det är **rekommenderar att du har 3 (eller flera kluster) noder för SOFS med 3-vägsspegling**.
Den här inställningen ger mer skalbarhet och bättre lagring återhämtning än SOFS-installation med 2 klusternoder och 2-vägsspegling.

* Du måste använda **Azure Premium-disk**

* Det är **rekommenderas** att använda **Azure hanterade diskar**

* Det är **rekommenderas** att formatera volymerna med en ny **flexibelt filsystem (ReFS)**
    * [SAP-kommentar 1869038 - SAP-stöd för ReFs-filsystemet] [1869038]
    * Se [väljer filsystemet] [ planning-volumes-s2d-choosing-filesystem] kapitel planera volymerna i Storage Spaces Direct.
    * Se till att installera det här [MS **KB4025334** kumulativ uppdatering][kb4025334].


* Du kan använda **DS-serien** eller **DSv2-serien** storlekar på Virtuella Azure

* Om du vill att bra bland VM nätverksprestanda som krävs för synkronisering av Storage Spaces Direct disk, bör du använda en VM-typ som har minst en **”hög” nätverksbandbredd**.
Mer information finns i [DSv2-serien] [ dv2-series] och [DS-serien] [ ds-series] specifikation.

* Det är **rekommenderas** lämna och **reservera vissa kapacitet i lagringspoolen inte allokerat**. Den ger mängder utrymme för att reparera ”på plats” när enheter misslyckas, förbättra datasäkerhet och prestanda.

 Mer information finns i [om du väljer storleken på volymer][choosing-the-size-of-volumes-s2d]


* SOFS virtuella Azure-datorer måste distribueras i **äger Azure Tillgänglighetsuppsättning**

* Det finns inget behov av att konfigurera Azure interna belastningsutjämnare för SOFS nätverk filresursnamn, t.ex. <SAPGlobalHostName>eftersom den är klar för < (A) SCSVirtualHostname > för DBMS eller SAP (A) SCS-instans. SOFS skalas ut belastningen över alla noder i klustret, så <SAPGlobalHostName> använder lokala IP-adress för alla klusternoder.


> [!IMPORTANT]
>SAPMNT filresurs som pekar på SAPGLOBALHOST kan inte ändras. SAP stöder inte ett annat resursnamn som sapmnt.
>[Obs 2492395 SAP - kan dela namnet sapmnt ändras?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>Konfigurera SAP-(A) SCS instanser och SOFS i två kluster

Du har möjlighet att distribuera SAP (A) SCS instanser i ett kluster med sina egna SAP <SID> klusterrollen. SOFS filresursen har konfigurerats i ett annat kluster med en annan klustrad roll.

> [!IMPORTANT]
>I det här scenariot SAP (A) SCS-instansen har konfigurerats för att komma åt SAP GLOBALHost med UNC-sökväg \\ \\ &lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
>

![Bild 5: SAP-(A) SCS-instansen och SOFS som distribuerats i två kluster][sap-ha-guide-figure-8007]

_**Bild 5:** SAP-(A) SCS-instansen och SOFS som distribuerats i två kluster_

> [!IMPORTANT]
>Distribuerad placeringen av dessa klustrets virtuella datorer mellan underliggande Azure-infrastrukturen för att säkerställa på Azure-molnet, varje kluster som används för SAP och SOFS filen resurser måste distribueras i sina egna Azure tillgänglighet har angetts.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>Allmän filresursen med SIOS som klusterdelade delade diskar


> [!IMPORTANT]
>SOFS rekommenderas lösning för hög tillgänglighet filresurs.
>
>Men om du planerar att också **katastrofåterställning** för hög tillgänglighet filresursen, du måste använda generiska filresursen och SISO teknik som för delade klusterdiskar.
>

Allmän filresursen är ett annat alternativ för att uppnå hög tillgänglighet filresurs.

Som ett kluster delad disk här, kan du använda 3 part SIOS lösning.

## <a name="next-steps"></a>Nästa steg

* [Azure förberedelse av infrastruktur för SAP hög tillgänglighet med hjälp av Windows-redundanskluster och filresursen för SAP (A) SCS-instans][sap-high-availability-infrastructure-wsfc-file-share]

* [SAP NetWeaver hög tillgänglighet Installation på Windows-redundanskluster och filresursen för SAP (A) SCS-instans][sap-high-availability-installation-wsfc-shared-disk]

* [Distribuera en tvånods-Storage Spaces Direct skalbar filserver för UPD lagring i Azure][deploy-sofs-s2d-in-azure]

* [Lagringsutrymmen direkt i Windows Server 2016][s2d-in-win-2016]

* [Ingående: Volymer i lagringsutrymmen direkt][deep-dive-volumes-in-s2d]
