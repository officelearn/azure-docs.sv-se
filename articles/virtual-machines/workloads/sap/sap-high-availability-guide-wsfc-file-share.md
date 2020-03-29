---
title: Kluster-SAP ASCS/SCS på WSFC med filresurs i Azure | Microsoft-dokument
description: Lär dig hur du samlar en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en filresurs i Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 545bcd1fa521b945d822b7eb69945cf381bf480a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918673"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en filresurs i Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server redundanskluster är grunden för en SAP ASCS/SCS-installation med hög tillgänglighet och DBMS i Windows.

Ett redundanskluster är en grupp på 1+n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om ett nodfel inträffar beräknar Windows Server redundanskluster antalet fel som kan uppstå och underhåller fortfarande ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja mellan olika kvorumlägen för att uppnå redundanskluster.

## <a name="prerequisites"></a>Krav
Innan du börjar de uppgifter som beskrivs i den här artikeln läser du den här artikeln:

* [Azure Virtual Machines arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Klustring av SAP ASCS/SCS-instanser med hjälp av en filresurs stöds för SAP NetWeaver 7.40 (och senare), med SAP Kernel 7.49 (och senare).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server redundanskluster i Azure

Jämfört med bare-metal eller privata molndistributioner kräver Virtuella Azure-datorer ytterligare steg för att konfigurera Windows Server redundanskluster. När du skapar ett kluster måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namnmatchning i Azure och klustervirtualens värdnamn

Azure-molnplattformen erbjuder inte möjlighet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser. Du behöver en alternativ lösning för att konfigurera en virtuell IP-adress för att nå klusterresursen i molnet. 

Azure Load Balancer-tjänsten tillhandahåller en *intern belastningsutjämnare* för Azure. Med den interna belastningsutjämnaren når klienter klustret via klustrets virtuella IP-adress. 

Distribuera den interna belastningsutjämnaren i resursgruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga regler för vidarebefordran av portar med hjälp av avsökningsportarna för den interna belastningsutjämnaren. Klienterna kan ansluta via det virtuella värdnamnet. DNS-servern löser klustrets IP-adress. Den interna belastningsutjämnaren hanterar port vidarebefordran till den aktiva noden i klustret.

![Bild 1: Windows Server Redundansklusterkonfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Figur 1:** Windows Server redundansklusterkonfiguration i Azure utan en delad disk_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA med filresurs

SAP utvecklade en ny metod och ett alternativ till klusterdelade diskar, för klustring av en SAP ASCS/SCS-instans i ett Windows redundanskluster. I stället för att använda klusterdelade diskar kan du använda en SMB-filresurs för att distribuera GLOBALA SAP-värdfiler.

> [!NOTE]
> En SMB-filresurs är ett alternativ till att använda klusterdelade diskar för klustring av SAP ASCS/SCS-instanser.  
>

Den här arkitekturen är specifik på följande sätt:

* SAP-centrala tjänster (med egen filstruktur och meddelande- och efterföljande processer) är separata från SAP:s globala värdfiler.
* SAP-centrala tjänster körs under en SAP ASCS/SCS-instans.
* SAP ASCS/SCS-instansen är klustrade \<och kan nås med\> hjälp av det virtuella värdnamnet ASCS/SCS.
* GLOBALA SAP-filer placeras på SMB-filresursen och \<nås\> med hjälp \\ \\ &lt;av&gt;det globala\\&lt;värdnamnet för SAP: SAP global host \sapmnt SID&gt;\SYS\...
* SAP ASCS/SCS-instansen installeras på en lokal disk på båda klusternoderna.
* Nätverksnamnet \<för det virtuella\> värdnamnet ASCS/SCS skiljer sig från &lt;den globala värden för&gt;SAP.

![Bild 2: SAP ASCS/SCS HA-arkitektur med SMB-filresurs][sap-ha-guide-figure-8004]

_**Figur 2:** Ny SAP ASCS/SCS HA-arkitektur med en SMB-filresurs_

Förutsättningar för en SMB-filresurs:

* SMB 3.0-protokollet (eller senare).
* Möjlighet att ange Active Directory-åtkomstkontrollistor (ACL: `computer$` er) för Active Directory-användargrupper och datorobjektet.
* Filresursen måste ha-aktiverad:
    * Diskar som används för att lagra filer får inte vara en enda felpunkt.
    * Driftstopp för servern eller den virtuella datorn orsakar inte driftstopp för filresursen.

ROLLEN \<SAP\> SID-kluster innehåller inte klusterdelade diskar eller en allmän klusterresurs för filresurs.


![Bild 3: \<\> SAP SID-klusterrollresurser för att använda en filresurs][sap-ha-guide-figure-8005]

_**Figur 3:** SAP &lt;&gt; SID-klusterrollresurser för att använda en filresurs_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Utskalningsfilresurser med Storage Spaces Direct i Azure som en SAPMNT-filresurs

Du kan använda en skalningsfilresurs för att vara värd för och skydda GLOBALA SAP-värdfiler. En skala ut filresurs erbjuder också en mycket tillgänglig SAPMNT fildelningstjänst.

![Bild 4: Skala ut filresurs som används för att skydda SAP:s globala värdfiler][sap-ha-guide-figure-8006]

_**Figur 4:** En skala ut filresurs som används för att skydda GLOBALA SAP-värdfiler_

> [!IMPORTANT]
> Utskalningsfilresurser stöds fullt ut i Microsoft Azure-molnet och i lokala miljöer.
>

En skala ut filresurs erbjuder en mycket tillgänglig och horisontellt skalbar SAPMNT-filresurs.

Storage Spaces Direct används som delad disk för en utskalningsfilresurs. Du kan använda Storage Spaces Direct för att skapa mycket tillgänglig och skalbar lagring med hjälp av servrar med lokal lagring. Delad lagring som används för en utskalningsfilresurs, som för globala SAP-värdfiler, är inte en enda felpunkt.

När du väljer Storage Spaces Direct bör du tänka på följande användningsfall:

- De virtuella datorer som används för att skapa Storage Spaces Direct-klustret måste distribueras i en Azure-tillgänglighetsuppsättning.
- För haveriberedskap för ett Direct-kluster för lagringsutrymmen kan du använda [Azure Site Recovery Services](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage).
- Det stöds inte att sträcka ut Azure-kluster för lagringsutrymme över olika Azure-tillgänglighetszoner.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>SAP-förutsättningar för skalning av filresurser i Azure

Om du vill använda en utskalningsfilresurs måste systemet uppfylla följande krav:

* Minst två klusternoder för en utskalningsfilresurs.
* Varje nod måste ha minst två lokala diskar.
* Av prestandaskäl måste du använda *speglingsåtersåtergivande:*
    * Tvåvägsspegling för en skalningsfilresurs med två klusternoder.
    * Trevägsspegling för en skalningsfilresurs med tre (eller fler) klusternoder.
* Vi rekommenderar tre (eller flera) klusternoder för en skalningsfilresurs, med trevägsspegling.
    Den här inställningen ger mer skalbarhet och mer lagringsåterhet än skalningsfildelningskonfigurationen med två klusternoder och tvåvägsspegling.
* Du måste använda Azure Premium-diskar.
* Vi rekommenderar att du använder Azure Managed Disks.
* Vi rekommenderar att du formaterar volymer med hjälp av ReFS (Resilient File System).
    * Mer information finns i [SAP Note 1869038 - SAP-stöd för filsystem för refs][1869038] och kapitlet [Välja filsystem][planning-volumes-s2d-choosing-filesystem] i artikeln Planeringsvolymer i Storage Spaces Direct.
    * Se till att du installerar [den kumulativa uppdateringen av Microsoft KB4025334][kb4025334].
* Du kan använda Azure VM-storlekar i DS-serien eller DSv2-serien.
* För bra nätverksprestanda mellan virtuella datorer, som behövs för Storage Spaces Direct-disksynkronisering, använder du en vm-typ som har minst en "hög" nätverksbandbredd.
    Mer information finns i specifikationerna [för DSv2-serien][dv2-series] och [DS-serien.][ds-series]
* Vi rekommenderar att du reserverar viss oallokerad kapacitet i lagringspoolen. Om du lämnar en viss oallokerad kapacitet i lagringspoolen får volymerna utrymme att reparera "på plats" om en enhet misslyckas. Detta förbättrar datasäkerhet och prestanda.  Mer information finns i [Välja volymstorlek][choosing-the-size-of-volumes-s2d].
* Du behöver inte konfigurera Azures interna belastningsutjämnare för nätverksnamnet \<för skalningsfilen, till exempel för global SAP-värd\>. Detta görs för \<det virtuella värdnamnet\> ASCS/SCS för SAP ASCS/SCS-instansen eller för DBMS. En skala ut filresurs skalar ut belastningen över alla klusternoder. \<DEN globala\> SAP-värden använder den lokala IP-adressen för alla klusternoder.


> [!IMPORTANT]
> Du kan inte byta namn på FILRESURSEN SAPMNT, vilket pekar \<på den globala sap-värden\>. SAP stöder endast resursnamnet "sapmnt".
>
> Mer information finns i [SAP Note 2492395 - Kan delningsnamnet sapmnt ändras?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurera SAP ASCS/SCS-instanser och en utskalningsfilresurs i två kluster

Du kan distribuera SAP ASCS/SCS-instanser i \<ett\> kluster, med sin egen SAP SID-klusterroll. I det här fallet konfigurerar du skala ut filresursen i ett annat kluster, med en annan klusterroll.

> [!IMPORTANT]
>I det här fallet är SAP ASCS/SCS-instansen konfigurerad för \\ \\ &lt;att&gt;komma åt\\&lt;den&gt;globala SAP-värden med hjälp av UNC-sökväg SAP global värd \sapmnt SID \SYS\.
>

![Bild 5: SAP ASCS/SCS-instans och en utskalningsfilresurs som distribueras i två kluster][sap-ha-guide-figure-8007]

_**Figur 5:** En SAP ASCS/SCS-instans och en utskalningsfilresurs som distribueras i två kluster_

> [!IMPORTANT]
> I Azure-molnet måste varje kluster som används för SAP- och utskalningsfilresurser distribueras i sin egen Azure-tillgänglighetsuppsättning eller över Azure-tillgänglighetszoner. Detta säkerställer distribuerad placering av kluster-virtuella datorer över den underliggande Azure-infrastrukturen. Distributioner av tillgänglighetszon stöds med den här tekniken.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Allmän filresurs med SIOS DataKeeper som klusterdelade diskar


En allmän filresurs är ett annat alternativ för att uppnå en filresurs med högtillgänge.

I det här fallet kan du använda en SIOS-lösning från tredje part som en klusterdelad disk.

## <a name="next-steps"></a>Nästa steg

* [Förbereda Azure-infrastrukturen för SAP HA med hjälp av ett Windows redundanskluster och filresurs för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-file-share]
* [Installera SAP NetWeaver HA i ett Windows-redundanskluster och filresurs för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
* [Distribuera en utskalningsfilserver med två noder för utskalning av upd-lagring i Azure][deploy-sofs-s2d-in-azure]
* [Lagringsdirigering i Windows Server 2016][s2d-in-win-2016]
* [Djupdykning: Volymer i Lagringsutrymmen Direkt][deep-dive-volumes-in-s2d]
