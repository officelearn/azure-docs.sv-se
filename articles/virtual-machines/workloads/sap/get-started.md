---
title: Komma igång med SAP på Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VM) i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f0705f2fb14bc44a7738f38bc3ef53a6359dfb4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657779"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Med hjälp av Azure för värd för och köra SAP arbetsbelastningsscenarier
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8
[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b
[deploy-template-cli]:../../../resource-group-template-deploy.md
[deploy-template-portal]:../../../resource-group-template-deploy.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c


[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056
[ha-guide]:sap-high-availability-guide.md
[ha-guide-get-started]:sap-high-availability-guide-start.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP produkten tillgänglighet matris)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

Genom att välja Microsoft Azure som partnern SAP redo moln ska du kunna tillförlitligt köra dina verksamhetskritiska SAP arbetsbelastningar och scenarier på en skalbar, kompatibla och enterprise-beprövade plattform.  Få skalbarheten, flexibiliteten och kostnadseffektiviteten med Azure. Med utökade samarbetet mellan Microsoft och SAP, du kan köra SAP-program över scenarier för utveckling och testning och produktion i Azure - och har fullt stöd. Vi har du omfattas från SAP NetWeaver till SAP S4/HANA, SAP BI, Linux i Windows, SAP HANA till SQL.

Förutom värd SAP NetWeaver scenarier med olika DBMS på Azure, du kan vara värd för olika andra scenarier för SAP arbetsbelastning, som SAP BI i Azure. Dokumentation om SAP NetWeaver distributioner på Azure interna virtuella datorer finns i avsnittet ”SAP NetWeaver på Azure Virtual Machines”.

Azure har inbyggda virtuella Azure-erbjudanden som någonsin växer i storlek på processor och minne resurser för att täcka SAP arbetsbelastning som utnyttjar SAP HANA. Mer information om det här området slå upp dokument under avsnittet SAP HANA på Azure Virtual Machines ”.

Unik Azure för SAP HANA är ett unikt erbjudande som anger Azure förutom konkurrens. För att aktivera värd mer minne och processoranvändning dedikerade krävande SAP scenarier som involverar SAP HANA Azure erbjuder användningen av kunden bare metal-maskinvara för att köra SAP HANA-distributioner som kräver upp till 20 TB (60 TB skalbar) minne för S/4HANA eller andra SAP HANA-arbetsbelastning. Den här unika Azure lösningen för SAP HANA i Azure (stora instanser) kan du köra SAP HANA på den särskilda bare metal-maskinvaran med SAP programnivå eller arbetsbelastning i mitten-ware lager i interna virtuella datorer i Azure som värd. Den här lösningen dokumenteras i flera dokument i avsnittet ”SAP HANA i Azure (stora instanser)”.   

Värdscenario SAP arbetsbelastning i Azure kan också skapa kraven i Identity integration och enkel inloggning med hjälp av Azure aktivitet Directory till olika SAP-komponenter och SAP SaaS eller PaaS erbjuder. En lista över sådana integrering och Single-Sign-On-scenarier med Azure Active Directory (AAD) och SAP entiteter beskrivas och dokumenteras i avsnittet ”AAD SAP Identity Integration och Single-Sign-On”.


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA på SAP HANA i Azure (stora instanser)

### <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Översikt över och arkitektur för SAP HANA i Azure (stora instanser)
Rubrik: Översikt och arkitektur för SAP HANA i Azure (stora instanser)

Sammanfattning: Den här arkitekturen och tekniska distributionsguiden innehåller information som hjälper dig att distribuera SAP på den nya SAP HANA i Azure (stora instanser) i Azure. Det är inte avsedd att vara en heltäckande handbok som omfattar specifika inställningar för SAP-lösningar, men i stället användbar information i ditt första distributionen och pågående drift. Det bör inte ersätta SAP-dokumentation som rör installationen av SAP HANA (eller de många SAP stöd anteckningar omfattar området). Det ger dig en översikt och innehåller ytterligare information för att installera SAP HANA i Azure (stora instanser).

Uppdaterad: Oktober 2017

[Den här guiden hittar du här](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Infrastruktur och anslutning till SAP HANA i Azure (stora instanser)
Rubrik: Infrastruktur och anslutning till SAP HANA i Azure (stora instanser)

Sammanfattning: Efter inköpet av SAP HANA i Azure (stora instanser) är slutförd mellan dig och Microsoft enterprise-kontoteamet olika nätverkskonfigurationer krävs för att säkerställa rätt anslutning.  Det här dokumentet beskrivs den information som ska delas med följande information som krävs. Det här dokumentet beskrivs vilken information som har ska samlas in och vad konfigurationsskript behöver köras.

Uppdaterad: Oktober 2017

[Den här guiden hittar du här](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="install-sap-hana-in-sap-hana-on-azure-large-instances"></a>Installera SAP HANA i SAP HANA i Azure (stora instanser)
Rubrik: Installera SAP HANA på SAP HANA i Azure (stora instanser)

Sammanfattning: Det här dokumentet beskrivs installationen för att installera SAP HANA på din stora Azure-instans.

Uppdaterad: Juli 2017

[Den här guiden hittar du här](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Hög tillgänglighet och katastrofåterställning återställning av SAP HANA i Azure (stora instanser)
Rubrik: Hög tillgänglighet och katastrofåterställning för SAP HANA i Azure (stora instanser)

Sammanfattning: Hög tillgänglighet och Disaster Recovery (DR) är viktiga aspekter av din verksamhetskritiska SAP HANA körs på Azure (stora instanser) servrar. Det är importera att arbeta med SAP, din systemintegreraren och/eller Microsoft skapa och implementera rätt hr/DR-strategi för du korrekt. Viktiga saker som mål för återställning-plats (RPO) och återställning tid mål för Återställningstid, specifika för din miljö måste beaktas.  Det här dokumentet beskriver alternativen för att aktivera din önskade nivå med hög tillgänglighet och Katastrofåterställning.

Uppdaterad: Oktober 2017

[Det här dokumentet finns här](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>Felsökning och övervakning av SAP HANA i Azure (stora instanser)
Rubrik: Felsökning och övervakning av SAP HANA i Azure (stora instanser)

Sammanfattning: Den här handboken innehåller information som är användbara vid upprättandet av övervakning av din SAP HANA på Azure-miljön samt ytterligare information om felsökning.

Uppdaterad: Oktober 2017

[Det här dokumentet finns här](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på Azure Virtual Machines

### <a name="getting-started-with-sap-hana-on-azure"></a>Komma igång med SAP HANA på Azure
Rubrik: Snabbstartsguide till manuell installation av SAP HANA på Azure Virtual Machines

Sammanfattning: Den här snabbstartsguide hjälper till att konfigurera en single instance SAP HANA system på virtuella Azure-datorer genom en manuell installation av SAP NetWeaver 7.5 och SAP HANA SP12. Guiden förutsätter att läsaren är bekant med grundläggande Azure IaaS som hur du distribuerar virtuella datorer eller virtuella nätverk, antingen via Azure-portalen eller Powershell/CLI, inklusive möjligheten att använda json-mallar. Dessutom förväntas att läsaren är bekant med SAP HANA, SAP NetWeaver och hur du installerar det lokalt.

Uppdaterad: Juni 2017

[Den här guiden hittar du här](hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="s4hana-sap-cal-deployment-on-azure"></a>S/4HANA CAL SAP-distribution på Azure
Namn: Distribuera SAP S/4HANA eller BW/4HANA på Azure

Sammanfattning: Den här guiden hjälper till att demonstrera distributionen av SAP S/4HANA på Azure med hjälp av SAP Molnbibliotek för installation. SAP Molnbibliotek installation är en tjänst av SAP som gör det möjligt för att distribuera SAP-program på Azure. Guiden beskriver steg för steg distributionen.

Uppdaterad: Juni 2017

[Den här guiden hittar du här](cal-s4h.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-of-sap-hana-in-azure-virtual-machines"></a>Hög tillgänglighet för SAP HANA i virtuella Azure-datorer
Rubrik: Hög tillgänglighet för SAP HANA på Azure Virtual Machines

Sammanfattning: Den här guiden leder dig genom konfigurationen av SUSE 12 operativsystem och SAP HANA HANA System replikering med automatisk redundans för hög tillgänglighet. Guiden är specifika för SUSE och virtuella datorer i Azure. I guiden gäller inte ännu för Red Hat eller bare metal- eller privata moln eller andra Azure-offentligt moln-distributioner.

Uppdaterad: Juni 2017

[Den här guiden hittar du här](sap-hana-high-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-backup-overview-on-azure-vms"></a>Översikt av SAP HANA säkerhetskopiering på virtuella Azure-datorer
Rubrik: Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines

Sammanfattning: Den här guiden innehåller grundläggande information om säkerhetskopiering möjligheter som körs på Azure Virtual Machines för SAP HANA.

Uppdaterad: Mars 2017

[Den här guiden hittar du här](sap-hana-backup-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-file-level-backup-on-azure-vms"></a>SAP HANA nivån säkerhetskopiering på virtuella Azure-datorer
Rubrik: SAP HANA säkerhetskopia av ögonblicksbilder för lagring

Sammanfattning: Den här guiden innehåller information om att använda ögonblicksbilder säkerhetskopiering på virtuella Azure-datorer när du kör SAP HANA på Azure Virtual Machines.

Uppdaterad: Mars 2017

[Den här guiden hittar du här](sap-hana-backup-file-level.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="sap-hana-snapshot-based-backups-on-azure-vms"></a>Säkerhetskopior av SAP HANA-ögonblicksbilder baserat på Azure Virtual Machines
Rubrik: SAP HANA Azure Backup på filnivå

Sammanfattning: Den här guiden innehåller information om hur du använder SAP HANA nivån säkerhetskopiering körs på Azure Virtual Machines för SAP HANA

Uppdaterad: Mars 2017

[Den här guiden hittar du här](sap-hana-backup-storage-snapshots.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribueras på Azure Virtual Machines

### <a name="deploy-sap-ides-system-on-windows-and-sql-server-through-sap-cal-on-azure"></a>Distribuera SAP IDES system i Windows och SQL Server via SAP CAL på Azure
Rubrik: Testa SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer

Sammanfattning: Det här dokumentet beskriver distributionen av ett SAP IDES system baserat på Windows och SQL Server på Azure med hjälp av SAP Molnbibliotek för installation. SAP molnet installation biblioteket är en SAP-tjänst som gör att distributionen av SAP produkter på Azure. Det här dokumentet går steg för steg genom distributionen av ett SAP IDES system. Systemets IDES är bara ett exempel för flera andra dussin program som kan distribueras via SAP molnet installation på Microsoft Azure.

Uppdaterad: Juni 2017

[Den här guiden hittar du här](cal-ides-erp6-erp7-sp3-sql.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Snabbstartsguide för NetWeaver på SUSE Linux på Azure
Rubrik: Testa SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer

Sammanfattning: Den här artikeln beskrivs olika saker att tänka på när du kör SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer (VM). SAP NetWeaver stöds officiellt på SUSE Linux virtuella datorer i Azure. All information om Linux-versioner, SAP kernel-versioner och annan information finns i SAP Obs 1928533 ”SAP-program i Azure: produkter som stöds och Azure VM typer”.

Uppdaterad: September 2016

[Den här guiden hittar du här](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>Planering och implementering
Rubrik: Azure virtuella datorer planering och implementering för SAP NetWeaver

Sammanfattning: Det här dokumentet är guiden börja med om du funderar SAP NetWeaver i Azure virtuella datorer som körs. Den här guiden för planering och implementering hjälper dig att utvärdera om ett befintligt eller planerade SAP NetWeaver-baserat system kan distribueras till en miljö för virtuella datorer i Azure. Den omfattar flera SAP NetWeaver distributionsscenarier och innehåller SAP-konfigurationer som är specifika för Azure. Dokumentet listar och beskriver alla nödvändiga konfigurationsinformationen måste på SAP-/ Azure-sida för att köra en hybrid SAP liggande. Åtgärder som du kan vidta för att garantera hög tillgänglighet för SAP NetWeaver-baserade system på IaaS omfattas också.

Uppdaterad: Juni 2017

[Den här guiden hittar du här][planning-guide]

### <a name="high-availability-configurations-of-sap-netweaver-in-azure-vms"></a>Hög tillgänglighet konfigurationer för SAP NetWeaver i virtuella Azure-datorer
Rubrik: Azure Virtual Machines hög tillgänglighet för SAP NetWeaver

Sammanfattning: I detta dokument, vi beskriver de steg som du kan vidta för att distribuera hög tillgänglighet SAP-system i Azure med hjälp av Azure Resource Manager-distributionsmodellen. Vi dig igenom dessa viktiga uppgifter. I det här dokumentet beskrivs hur single---felpunkter komponenter som avancerade Business Application Programming (ABAP) SAP centrala tjänster (ASCS) / SAP centrala Services (SCS) och databashanteringssystem (DBMS) och redundanta komponenter som SAP-programserver kommer att skyddas när den körs i virtuella Azure-datorer. Ett steg för steg-exempel på en installation och konfiguration av en SAP-systemet för hög tillgänglighet i ett redundanskluster i Windows Server-kluster och SUSE Linux Enterprise Server-kluster Framework i Azure visas och visas i det här dokumentet.

Uppdaterad: Oktober 2017

[Den här guiden hittar du här][ha-guide-get-started]

### <a name="realizing-multi-sid-deployments-of-sap-netweaver-in-azure-vms"></a>Inse Multi-SID distributioner av SAP NetWeaver i virtuella Azure-datorer
Rubrik: Skapa en konfiguration för SAP NetWeaver multi-SID

Sammanfattning: Det här dokumentet är ett tillägg till dokumentet hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines. På grund av nya funktioner i Azure som har introducerats i September 2016 är det möjligt att distribuera flera SAP NetWeaver ASCS/SCS-instanser i ett par med virtuella Azure-datorer. Med denna konfiguration kan du minska antalet VMs som behövs för att distribuera Tänk högtillgänglig SAP NetWeaver konfigurationer. Guiden beskriver installationen av konfigurationerna multi-SID.

Uppdaterad: December 2016

[Den här guiden hittar du här](high-availability-multi-sid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>Distribution av SAP NetWeaver i virtuella Azure-datorer
Rubrik: Azure Virtual Machines distribution för SAP NetWeaver

Sammanfattning: Det här dokumentet innehåller riktlinjer för distribution av programvara för SAP NetWeaver till virtuella datorer i Azure. Det här dokumentet fokuserar på tre specifika distributionsscenarier med betoning på att aktivera Azure Monitoring-tillägg för SAP, inklusive felsökningsrekommendationer för Azure Monitoring-tillägget för SAP. Det här dokumentet förutsätter att du har läst guiden för planering och implementering.

Uppdaterad: Juni 2017

[Den här guiden hittar du här][deployment-guide]

### <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS Distributionsguide
Rubrik: Azure virtuella datorer DBMS-distribution för SAP NetWeaver

Sammanfattning: Det här dokumentet beskriver planerings- och implementeringsöverväganden för DBMS system som ska köras tillsammans med SAP. I den första delen listas och presenteras allmänna överväganden. Följande delar av papperet avser distributioner av olika DBMS i Azure som stöds av SAP. Olika DBMS som visas är SQL Server, SAP ASE och Oracle. I de specifika delarna beskrivs överväganden som du har till kontot för när du kör SAP-system på Azure tillsammans med dessa DBMS. Ämnen som vilka metoder för säkerhetskopiering och hög tillgänglighet som stöds av olika DBMS på Azure presenteras för användning med SAP-program.

Uppdaterad: Juni 2017

[Den här guiden hittar du här][dbms-guide]

### <a name="using-azure-site-recovery-for-sap-workload"></a>Med hjälp av Azure Site Recovery för SAP arbetsbelastning
Rubrik: SAP NetWeaver: skapa en lösning för katastrofåterställning med Azure Site Recovery

Sammanfattning: Det här dokumentet beskriver hur hur Azure Site Recovery services kan användas för att hantera för katastrofåterställning. Fall där Azure används som disaster recovery plats för en lokal SAP liggande med hjälp av Azure Site Recovery Services. Ett annat scenario som beskrivs i dokumentet är Azure till Azure (A2A) disaster recovery fallet och hur den hanteras med Azure Site Recovery.  

Uppdaterad: Augusti 2017

[Den här guiden hittar du här](http://aka.ms/asr-sap)
