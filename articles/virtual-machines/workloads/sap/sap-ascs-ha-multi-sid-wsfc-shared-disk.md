---
title: "SAP ASCS/SCS instansen multi-SID hög tillgänglighet med Windows Server Failover Clustering och delad disk i Azure | Microsoft Docs"
description: "Flera SID hög tillgänglighet för en SAP ASCS/SCS-instans med Windows Server Failover Clustering och delad disk på Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82cc943f983b3dedfc0f64f2eec5b4425a4bf81
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0f3ee255-b31e-4b8a-a95a-d9ed6200468b

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



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

# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>SAP ASCS/SCS instansen multi-SID hög tillgänglighet med Windows Server Failover Clustering och delad disk på Azure

> ![Windows][Logo_Windows] Windows
>

I September 2016 Microsoft släppt en funktion där du kan hantera flera virtuella IP-adresser med hjälp av en [Azure intern belastningsutjämnare][load-balancer-multivip-overview]. Den här funktionen finns redan i Azure externa belastningsutjämnaren. 

Om du har en SAP-distribution måste du använda en intern belastningsutjämnare för att skapa en Windows-klusterkonfiguration för centrala SAP-tjänster (ASCS/SCS) instanser.

Den här artikeln fokuserar på hur du flyttar från en enda ASCS/SCS-installation till en SAP multi-SID-konfigurationen genom att installera ytterligare SAP ASCS/SCS klustrade instanser i en befintlig Windows Server Failover Clustering WSFC-kluster med delad disk. Du har konfigurerat en SAP multi-SID-klustret när den här processen har slutförts.

> [!NOTE]
> Den här funktionen är endast tillgänglig i Azure Resource Manager-distributionsmodellen.
>
>Det finns en gräns för antalet privata frontend IP-adresser för varje Azure interna belastningsutjämnare.
>
>Det maximala antalet SAP ASCS/SCS instanser i en WSFC-klustret är lika med det maximala antalet privata frontend IP-adresser för varje Azure interna belastningsutjämnare.
>

Mer information om belastningsutjämnare begränsningar finns i avsnittet ”privat frontend-IP per belastningsutjämnare i [nätverk gränser: Azure Resource Manager][networking-limits-azure-resource-manager].

## <a name="prerequisites"></a>Krav

Du redan har konfigurerat en WSFC-klustret ska använda för en SAP ASCS/SCS-instans med hjälp av **filresursen**som visas i det här diagrammet.

![Hög tillgänglighet SAP ASCS/SCS instans][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> Inställningen måste uppfylla följande villkor:
> * SAP ASCS/SCS-instanser måste dela samma WSFC-klustret.
> * Varje databashanteringssystem (DBMS) SID måste ha sitt eget dedikerade WSFC-kluster.
> * SAP-programservrar som hör till ett SAP system SID måste ha sina egna dedikerade virtuella datorer.

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>SAP ASCS/SCS multi-SID-arkitektur med delad disk

Målet är att installera flera SAP ABAP ASCS eller SAP Java SCS grupperade instanser i samma WSFC-klustret, som här:

![Flera SAP ASCS/SCS klustrade instanser i Azure][sap-ha-guide-figure-6002]

Mer information om belastningsutjämnare begränsningar finns i avsnittet ”privat frontend-IP per belastningsutjämnare i [nätverk gränser: Azure Resource Manager][networking-limits-azure-resource-manager].

Fullständig liggande med två system med hög tillgänglighet SAP skulle se ut så här:

![SAP hög tillgänglighet multi-SID-installation med två SAP system SID][sap-ha-guide-figure-6003]

## <a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a>Förbereda infrastrukturen för ett scenario för SAP multi-SID

För att förbereda infrastrukturen måste installera du en ytterligare SAP ASCS/SCS-instans med följande parametrar:

| Parameternamn | Värde |
| --- | --- |
| SAP ASCS/SCS SID |ascs-lb-PR1 |
| SAP DBMS intern belastningsutjämnare | PR5 |
| SAP virtuella värdnamn | pr5-sap-cl |
| SAP ASCS/SCS virtuell värd IP-adress (ytterligare Azure belastningsutjämnare IP-adress) | 10.0.0.50 |
| SAP ASCS/SCS instansnummer | 50 |
| ILB avsökningsport för ytterligare SAP ASCS/SCS-instans | 62350 |

> [!NOTE]
> För SAP ASCS/SCS-instanser kräver varje IP-adress en unik avsökningsport. Om en IP-adress på en Azure intern belastningsutjämnare använder avsökningsport 62300, kan inga andra IP-adressen på den belastningsutjämnaren använda avsökningsport 62300.
>
>För våra ändamål eftersom avsökningsport 62300 har redan reserverats, använder du avsökningsport 62350.

Du kan installera ytterligare SAP ASCS/SCS-instanser i den befintliga WSFC-klustret med två noder:

| Rollen virtuell dator | Värdnamn för virtuell dator | Statisk IP-adress |
| --- | --- | --- |
| Första klusternoden för ASCS/SCS-instans |PR1-ascs-0 |10.0.0.10 |
| Andra klusternod för ASCS/SCS-instans |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Skapa ett virtuellt värdnamn för den klustrade instansen SAP ASCS/SCS på DNS-servern

Du kan skapa en DNS-post för det virtuella värdnamnet på ASCS/SCS-instans med hjälp av följande parametrar:

| Nya SAP ASCS/SCS virtuella värdnamn | Tillhörande IP-adress |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Det nya värdnamnet och IP-adress visas i DNS-hanteraren som visas i följande skärmbild:

![DNS-hanterarens lista syntaxmarkering definierade DNS-posten för nya SAP ASCS/SCS klustra virtuella namn och TCP/IP-adress][sap-ha-guide-figure-6004]

> [!NOTE]
> Den nya IP-adressen som du tilldelar virtuella värdnamnet för ytterligare ASCS/SCS-instans måste vara samma som den nya IP-adressen som du tilldelade till SAP Azure belastningsutjämnare.
>
>I vårt scenario är IP-adressen 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Lägga till en IP-adress i en befintlig Azure intern belastningsutjämnare med hjälp av PowerShell

Om du vill skapa fler än en SAP ASCS/SCS-instans i samma WSFC-klustret använder du PowerShell för att lägga till en IP-adress i en befintlig Azure intern belastningsutjämnare. Varje IP-adress kräver sin egen regler för belastningsutjämning, avsökningsport, frontend IP-adresspool och backend-adresspool.

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. Uppdatera variablerna PowerShell för din miljö. Skriptet skapar alla nödvändiga belastningsutjämning regler för alla SAP ASCS/SCS-portar.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get a new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get an updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
När skriptet har körts, visas resultaten i Azure-portalen som visas i följande skärmbild:

![Ny frontend IP-pool i Azure-portalen][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lägg till diskar i klustret datorer och konfigurera SIOS klusterresurs disk

Du måste lägga till en ny disk i klusterresurs för varje ytterligare SAP ASCS/SCS-instans. För Windows Server 2012 R2 är WSFC resursen klusterdisken används för tillfället programvarulösning SIOS DataKeeper.

Gör följande:
1. Lägga till en annan disk eller diskar med samma storlek (som du behöver stripe-) i alla klusternoder och formateras.
2. Konfigurera storage-replikering med SIOS DataKeeper.

Den här proceduren förutsätter att du redan har installerat SIOS DataKeeper på datorer för WSFC-klustret. Om du har installerat det, måste du konfigurera replikering mellan datorerna. Processen beskrivs i detalj i [installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resursen klusterdisk][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].  

![DataKeeper synkron spegling för nya SAP ASCS/SCS dela disk][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>Distribuera virtuella datorer för SAP-programservrar och DBMS-kluster

Slutför infrastruktur inför andra SAP-systemet genom att göra följande:

1. Distribuera dedikerade virtuella datorer för SAP-programservrar och placera dem i sitt eget dedikerade tillgänglighetsgruppen.
2. Distribuera dedikerade virtuella datorer för DBMS-klustret och placera dem i sitt eget dedikerade tillgänglighetsgruppen.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>Installera en SAP NetWeaver multi-SID

En beskrivning av klar att installera en andra SAP SID2 system finns [SAP NetWeaver HA installation på Windows-redundanskluster och delad disk för en SAP ASCS/SCS instans][sap-high-availability-installation-wsfc-shared-disk].

Den övergripande proceduren är följande:

1. [Installera SAP med en hög tillgänglighet ASCS/SCS instans][sap-high-availability-installation-wsfc-shared-disk-install-ascs].  
 I det här steget installerar om du SAP med en hög tillgänglighet ASCS/SCS-instans på den befintliga WSFC-klusternoden 1.

2. [Ändra SAP-profil för ASCS/SCS-instansen][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [Konfigurera en avsökningsport][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 I det här steget konfigurerar du en SAP klusterresurs SAP-SID2-IP-avsökningsport med hjälp av PowerShell. Köra den här konfigurationen på en av klusternoderna SAP ASCS/SCS.

4. Installera databasinstansen.  
 Följ stegen i installationsguiden för SAP för att installera det andra klustret.

5. Installera den andra noden i klustret.  
 I det här steget installerar om du SAP med en hög tillgänglighet ASCS/SCS-instans på den befintliga WSFC-klusternoden 2. Följ stegen i installationsguiden för SAP för att installera det andra klustret.

6. Öppna portar i Windows-brandväggen för port för SAP ASCS/SCS-instans och avsökning.  
    På båda klusternoderna som används för SAP ASCS/SCS instanser, öppnar du alla portar i Windows-brandväggen som används av SAP ASCS/SCS. Portarna SAP ASCS/SCS instansen finns i kapitlet [SAP ASCS / SCS portar][sap-net-weaver-ports-ascs-scs-ports].

    En lista över alla andra SAP-portar, se [TCP/IP-portar för alla produkter som SAP][sap-net-weaver-ports].  

    Också öppna avsökningsport belastningsutjämnare för Azure interna belastning som är 62350 i vårt scenario. Beskrivningen [i den här artikeln][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port].

7. [Ändra starttypen för tjänstinstansen SAP utvärderas inleverans betalning (ERS) Windows][sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type].

8. Installera SAP primära programservern på den nya dedikerade VM, enligt beskrivningen i installationsguiden för SAP.  

9. Installera SAP ytterligare programservern på den nya dedikerade VM, enligt beskrivningen i installationsguiden för SAP.

10. [Testa redundans för SAP ASCS/SCS-instans och SIOS replikering][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl].

## <a name="next-steps"></a>Nästa steg

- [Nätverk gränser: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Flera VIP: er för Azure belastningsutjämnare][load-balancer-multivip-overview]
