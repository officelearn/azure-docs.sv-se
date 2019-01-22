---
title: Arkitektur för hög tillgänglighet för Azure virtuella datorer och scenarier för SAP NetWeaver | Microsoft Docs
description: Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver på Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de009d1dbc979a534b0fed8cee2afc867c5b79d4
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426921"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP – flera SÄKERHETSIDENTIFIERARE konfiguration med hög tillgänglighet)


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


## <a name="terminology-definitions"></a>Termer som är

**Hög tillgänglighet**: Refererar till en uppsättning tekniker som minimerar avbrott IT genom att tillhandahålla affärskontinuitet för IT-tjänster via redundanta, feltolerant eller redundans-skyddade-komponenterna på den *samma* datacenter. I vårt fall finns datacentralen inom en Azure-region.

**Haveriberedskap**: Avser även minimerar avbrott i IT-tjänster och deras recovery men över *olika* datacenter som kan vara hundratals mil bort från varandra. I vårt fall kan-Datacenter finnas i olika Azure-regioner inom samma geopolitiska region eller och så som anges av dig som kund.


## <a name="overview-of-high-availability"></a>Översikt över hög tillgänglighet
Hög tillgänglighet för SAP i Azure kan delas in i tre typer:

* **Hög tillgänglighet för Azure-infrastrukturen**: 

    Hög tillgänglighet kan exempelvis innehålla beräkning (VM), nätverk, eller lagring och dess fördelar för att öka tillgängligheten för SAP-program.

* **Med Azure-infrastrukturen virtuella datorn startas om för att uppnå *högre tillgänglighet* av SAP-program**: 

    Om du inte väljer att använda funktioner, till exempel Windows Server Failover Clustering (WSFC) eller Pacemaker på Linux används Azure VM-omstart. Det skyddar SAP-system mot planerade och oplanerade driftavbrott Azure fysisk infrastruktur och övergripande underliggande Azure-plattformen.

* **Hög tillgänglighet för SAP-program**: 

    För att uppnå full SAP system hög tillgänglighet, måste du skydda alla viktiga SAP-komponenter. Exempel:
    * Redundanta SAP-programservrar.
    * Unika komponenter. Ett exempel kan vara en enda åtkomstpunkt för fel (SPOF)-komponenten, till exempel en SAP ASCS/SCS-instans eller en databashanteringssystem (DBMS).

Hög tillgänglighet för SAP i Azure skiljer sig från SAP hög tillgänglighet i en lokal fysisk eller virtuell miljö. Följande dokumentet [SAP NetWeaver hög tillgänglighet och affärskontinuitet i virtuella miljöer med VMware och Hyper-V på Microsoft Windows] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] beskriver standard SAP hög tillgänglighet konfigurationer i virtualiserade miljöer på Windows.

Det finns inga sapinst-integrerade SAP konfiguration med hög tillgänglighet för Linux eftersom det inte finns för Windows. Information om SAP hög tillgänglighet lokalt för Linux finns i [partnerinformationen för hög tillgänglighet][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Hög tillgänglighet för Azure-infrastrukturen

### <a name="sla-for-single-instance-virtual-machines"></a>SERVICEAVTAL för enkelinstanser virtuella datorer

Det finns för närvarande en enda VM-serviceavtal på 99,9% med premium storage. Om du vill få en uppfattning om vad tillgängligheten för en enskild virtuell dator kan du skapa produkten av de olika tillgängliga [Azure serviceavtal][azure-sla].

Basen för beräkningen är 30 dagar per månad eller 43 200 minuter. Till exempel motsvarar ett 0,05% driftstopp 21,6 minuter. Tillgängligheten för de olika tjänsterna beräknas som vanligt, på följande sätt:

(Tillgänglighet Service #1/100) * (tillgänglighet Service #2/100) * (tillgänglighet Service #3/100) \*...

Exempel:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 eller en övergripande tillgänglighet 99.75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Flera instanser av virtuella datorer i samma tillgänglighetsuppsättning
För alla virtuella datorer som har två eller fler distribuerade instanser i samma *tillgänglighetsuppsättning*, garanterar vi att du har VM-anslutning till minst en instans under minst 99,95% av tiden.

När två eller flera virtuella datorer ingår i samma tillgänglighetsuppsättning, varje virtuell dator i tillgänglighetsuppsättningen tilldelas en *uppdateringsdomän* och en *feldomän* av underliggande Azure-plattformen.

* **Uppdateringsdomäner** garanterar att flera virtuella datorer inte startas om samtidigt under planerat underhåll av en Azure-infrastruktur. Endast en virtuell dator startas om samtidigt.

* **Feldomäner** garanterar att virtuella datorer distribuerade på de komponenter som inte delar samma strömkälla och nätverksswitch. Om servrar, en nätverksväxel eller en strömkälla genomgår en oplanerade driftstopp, påverkas endast en virtuell dator.

Mer information finns i [hantera tillgängligheten för Windows-datorer i Azure][azure-virtual-machines-manage-availability].

En tillgänglighetsuppsättning används för att uppnå hög tillgänglighet för:

* Redundanta SAP-programservrar.  
* Kluster med två eller flera noder (virtuella datorer, till exempel) som skyddar SPOFs, till exempel en SAP ASCS/SCS-instans eller en DBMS.


### <a name="azure-availability-zones"></a>Tillgänglighetszoner i Azure
Azure är på att lansera en begreppet [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) i olika [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). I Azure-regioner där Availability Zones erbjuds, har Azure-regioner flera datacenter som är oberoende i antal strömkälla, kylning och nätverk. För att erbjuda olika zoner i en enda Azure-region beror på att du ska distribuera program i två eller tre Tillgänglighetszoner som erbjuds. Om vi antar att problem i strömkällor och/eller påverkar bara en Tillgänglighetszon-infrastruktur, är programdistributionen inom en Azure-region fortfarande helt funktionella. Till slut med vissa minskad kapacitet eftersom vissa virtuella datorer i en zon kan gå förlorade. Men virtuella datorer i de andra två zonerna är fortfarande igång och körs. Azure-regioner som erbjuder zoner visas i [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview).

Med Availability Zones finns vissa saker att tänka på. Att tänka på listan som:

- Du kan inte distribuera Azure-Tillgänglighetsuppsättningar i en Tillgänglighetszon. Du måste välja en Tillgänglighetszon eller en Tillgänglighetsuppsättning som distribution ramen för en virtuell dator.
- Du kan inte använda den [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) att skapa redundans baserat på Windows Failover Cluster Services eller Linux Pacemaker-klusterlösningar. I stället måste du använda den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Azure Availability Zones ger inte inga garantier av vissa avståndet mellan olika zoner inom en region
- Nätverksfördröjningen mellan olika Azure Availability Zones i olika Azure-regioner kan skilja sig från Azure-region till region. Det ska finnas fall där du som en kund kan rimligen köra SAP-programnivån som distribueras över olika zoner sedan svarstiden i nätverk från en zon för aktiva DBMS VM fortfarande är acceptabelt från en process inverkan på företaget. Det kommer att vara kundscenarier där fördröjning mellan den aktiva DBMS VM i en zon och en SAP-programinstans i en virtuell dator i en annan zon kan vara för påträngande och inte godkänd för SAP-affärsprocesser. Arkitekturer för distribution måste därför vara olika med en aktiv/aktiv-arkitektur för programmet eller aktiv/passiv arkitektur om svarstiden är för hög.
- Med hjälp av [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/) är obligatoriskt för att distribuera till Azure Availability Zones 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Planerade och oplanerade underhåll av virtuella datorer

Två typer av händelser i Azure-plattformen kan påverka tillgängligheten för dina virtuella datorer:

* **Planerat underhåll** händelser är periodiska uppdateringar som Microsoft den underliggande Azure-plattformen. Uppdateringarna förbättra tillförlitligheten, prestanda och säkerhet för den plattformsinfrastruktur som dina virtuella datorer som körs på.

* **Oplanerat Underhåll** händelser inträffar när maskinvaran eller fysiska infrastrukturen som den virtuella datorn misslyckades på något sätt. Den kan innehålla lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När sådant fel upptäcks migrerar Azure-plattformen automatiskt den virtuella datorn från den felaktiga fysiska servern som är värd för den virtuella datorn till en felfri fysisk server. Sådana händelser är sällsynt, men de kan även orsaka den virtuella datorn ska startas om.

Mer information finns i [hantera tillgängligheten för Windows-datorer i Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundans i Azure Storage
Data i ditt storage-konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, vilket uppfyller SLA för Azure Storage som även körs vid tillfälliga maskinvarufel.

Eftersom Azure Storage behåller tre bilder av data som standard användning av RAID 5 eller RAID 1 över flera Azure-diskar är onödiga.

Mer information finns i [Azure Storage-replikering][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks är en resurstyp i Azure Resource Manager som du bör användas i stället för virtuella hårddiskar (VHD) som lagras i Azure storage-konton. Hanterade diskar justeras automatiskt med en Azure-tillgänglighetsuppsättning för den virtuella datorn som de är kopplade till. De öka tillgängligheten för den virtuella datorn och de tjänster som körs på den.

Mer information finns i [översikt över Azure Managed Disks][azure-storage-managed-disks-overview].

Vi rekommenderar att du använder hanterade diskar eftersom de förenkla distributionen och hanteringen av dina virtuella datorer.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Använda Azure-infrastrukturen hög tillgänglighet för att uppnå *högre tillgänglighet* av SAP-program

Om du inte väljer att använda funktioner, till exempel WSFC eller Pacemaker på Linux (för närvarande stöds endast för SUSE Linux Enterprise Server [SLES] 12 och senare), används omstart av Azure virtuella datorer. Det skyddar SAP-system mot planerade och oplanerade driftavbrott Azure fysisk infrastruktur och övergripande underliggande Azure-plattformen.

Mer information om den här metoden finns i [utnyttja Azure-infrastrukturen virtuella datorn startas om för att uppnå högre tillgänglighet för SAP-system][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Hög tillgänglighet för SAP-program på Azure IaaS

För att uppnå full SAP system hög tillgänglighet, måste du skydda alla viktiga SAP-komponenter. Exempel:
  * Redundanta SAP-programservrar.
  * Unika komponenter. Ett exempel kan vara en enda åtkomstpunkt för fel (SPOF)-komponenten, till exempel en SAP ASCS/SCS-instans eller en databashanteringssystem (DBMS).

I nästa avsnitt beskrivs hur du uppnår hög tillgänglighet för alla tre viktiga SAP systemkomponenter.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Arkitektur för hög tillgänglighet för SAP-programservrar

> Det här avsnittet gäller för:
>
> ![Windows][Logo_Windows] Windows och ![Linux][Logo_Linux] Linux
>

Vanligtvis behöver du inte en specifik lösning för hög tillgänglighet för SAP-servern och dialogrutan programinstanserna. Du kan uppnå hög tillgänglighet genom redundans och du konfigurerar flera dialogrutan instanser i olika instanser av virtuella Azure-datorer. Du bör ha minst två instanser av SAP programmet installerat i två instanser av virtuella Azure-datorer.

![Bild 1: SAP-programservern för hög tillgänglighet][sap-ha-guide-figure-2000]

_**Bild 1:** SAP-programservern för hög tillgänglighet_

Du måste placera alla virtuella datorer som värd SAP-programserverinstanser i samma Azure-tillgänglighetsuppsättning har angetts. En Azure-tillgänglighetsuppsättning säkerställer att:

* Alla virtuella datorer ingår i samma uppdateringsdomän.  
    En uppdateringsdomän säkerställer att de virtuella datorerna inte är uppdaterade på samma gång under planerat underhåll avbrott.

    Grundläggande funktioner, som bygger på annan uppdatering och feldomäner i en Azure skalningsenhet introducerades redan i den [uppdateringsdomäner] [ planning-guide-3.2.2] avsnittet.

* Alla virtuella datorer ingår i samma feldomän.  
    En feldomän säkerställer att virtuella datorer distribueras så att ingen enskild felpunkt som påverkar tillgängligheten för alla virtuella datorer.

Antalet uppdaterings- och feldomäner domäner som kan användas av en Azure tillgänglighetsuppsättning i en Azure skalningsenheten är begränsad. Om du lägga till fler virtuella datorer till en enskild tillgänglighetsuppsättning, kommer två eller flera virtuella datorer så småningom hamnar i samma domän för fel eller update.

Om du distribuerar några SAP programserverinstanser i sina dedikerade virtuella datorer växer under förutsättning att vi har fem uppdateringsdomäner nedan. Det faktiska högsta antalet uppdaterings- och feldomäner domäner i en tillgänglighetsuppsättning kan ändras i framtiden:

![Bild 2: Hög tillgänglighet för SAP-programservrarna i en Azure-tillgänglighetsuppsättning][planning-guide-figure-3000]
_**bild 2:** Ange hög tillgänglighet för SAP-programservrarna i en Azure-tillgänglighetsuppsättning_

Mer information finns i [hantera tillgängligheten för Windows-datorer i Azure][azure-virtual-machines-manage-availability].

Mer information finns i den [Azures tillgänglighetsuppsättningar] [ planning-guide-3.2.3] delen av virtuella Azure-datorer planering och implementering av SAP NetWeaver-dokumentet.

**Ohanterade diskar:** Eftersom Azure storage-kontot är en potentiell felpunkt, är det viktigt att du har minst två Azure storage-konton, där minst två virtuella datorer distribueras. I en perfekt installationsprogrammet, skulle diskar på varje virtuell dator som kör en instans för SAP-dialogrutan distribueras i ett annat lagringskonto.

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Azure hanterade diskar för dina SAP-installationer med hög tillgänglighet. Eftersom hanterade diskar automatiskt justera med tillgänglighetsuppsättningen för den virtuella datorn som de är kopplade till öka de tillgängligheten för den virtuella datorn och de tjänster som körs på den.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Arkitektur för hög tillgänglighet för en SAP ASCS/SCS-instans på Windows

> ![Windows][Logo_Windows] Windows
>

Du kan använda en WSFC-lösning för att skydda SAP ASCS/SCS-instans. Lösningen har två varianter:

* **Klustra SAP ASCS/SCS-instans med hjälp av klustrade delade diskar**: Mer information om den här arkitekturen finns i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk][sap-high-availability-guide-wsfc-shared-disk].   

* **Klustra SAP ASCS/SCS-instans med hjälp av filresursen**: Mer information om den här arkitekturen finns i [kluster ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av filresursen][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Arkitektur för hög tillgänglighet för en SAP ASCS/SCS-instans på Linux

> ![Linux][Logo_Linux] Linux
>
Läs mer om clustering SAP ASCS/SCS-instans med hjälp av SLES kluster framework [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program] [ sap-suse-ascs-ha].

Läs mer om clustering SAP ASCS/SCS-instans med hjälp av Red Hat-kluster framework [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver multi-SID-konfigurationen för en klustrad SAP ASCS/SCS-instans

> ![Windows][Logo_Windows] Windows
>
> Multi-SID är för närvarande endast med WSFC. Multi-SID stöds med hjälp av filresursen och delad disk.
>
Mer information om arkitektur för hög tillgänglighet för multi-SID finns:

* [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet för Windows Server Failover Clustering och filresurs][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet för Windows Server Failover Clustering och delad disk][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Hög tillgänglighet DBMS-instans

DBMS är också en enda kontaktpunkt i ett SAP-system. Du måste skydda den med hjälp av en lösning för hög tillgänglighet. Följande bild visar en lösning för SQL Server AlwaysOn hög tillgänglighet i Azure, med Windows Server Failover Clustering och intern Azure belastningsutjämnare. SQL Server AlwaysOn replikerar DBMS data och loggfiler filer med hjälp av en egen DBMS-replikering. I det här fallet behöver du inte delad klusterdisk, vilket förenklar hela installationen.

![Bild 3: Exempel på en hög tillgänglighet SAP DBMS, med SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Bild 3:** Exempel på en hög tillgänglighet SAP DBMS, med SQL Server AlwaysOn_

Mer information om kluster SQL Server för DBMS i Azure med hjälp av Azure Resource Manager-distributionsmodellen finns i följande artiklar:

* [Konfigurera en AlwaysOn-tillgänglighetsgrupp i Azure virtual machines manuellt med hjälp av Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurera en Azure intern belastningsutjämnare för en AlwaysOn-tillgänglighetsgrupp i Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Läs mer om clustering SAP HANA DBMS i Azure med Azure Resource Manager-distributionsmodellen [hög tillgänglighet för SAP HANA på Azure virtual machines (VM)][sap-hana-ha].
