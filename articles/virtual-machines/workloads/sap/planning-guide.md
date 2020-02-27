---
title: 'SAP på Azure: planerings-och implementerings guide'
description: Azure Virtual Machines planera och implementera SAP-NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e06ed31de35ad29a0fda271feaaf50b5efaf8a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616821"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines planera och implementera SAP-NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Microsoft Azure gör det möjligt för företag att förvärva beräknings-och lagrings resurser i minimal tid utan att de får längd. Med tjänsten Azure Virtual Machine kan företag distribuera klassiska program, t. ex. SAP NetWeaver-baserade program till Azure och utöka sin tillförlitlighet och tillgänglighet utan att ytterligare resurser är tillgängliga lokalt. Azure Virtual Machine-tjänster har även stöd för anslutning mellan platser, vilket gör det möjligt för företag att aktivt integrera Azure-Virtual Machines i sina lokala domäner, deras privata moln och deras liggande SAP-system.
I den här white paper beskrivs grunderna i Microsoft Azure virtuella datorn och en genom gång av planerings-och implementerings överväganden för SAP NetWeaver-installationer i Azure och därför bör dokumentet läsas innan du påbörjar den faktiska distributioner av SAP NetWeaver på Azure.
Pappret kompletterar SAP-installations dokumentation och SAP-anteckningar, som representerar de primära resurserna för installationer och distributioner av SAP-program på vissa plattformar.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Sammanfattning
Molnbaserad data behandling är en mycket vanlig term som används för att få mer och mer betydelse inom IT-branschen, från små företag till stora och multinationella företag.

Microsoft Azure är Cloud Services plattform från Microsoft, som erbjuder ett brett spektrum av nya möjligheter. Nu kan kunderna snabbt etablera och avetablera program som en tjänst i molnet, så att de inte är begränsade till tekniska eller budgetmässiga restriktioner. I stället för att investera tid och budget i maskin varu infrastruktur kan företag fokusera på program, affärs processer och dess fördelar för kunder och användare.

Med Microsoft Azure Virtual Machine-tjänsterna, erbjuder Microsoft en omfattande plattform för infrastruktur som en tjänst (IaaS). SAP NetWeaver-baserade program stöds på virtuella Azure-datorer (IaaS). I det här dokumentet beskrivs hur du planerar och implementerar SAP NetWeaver-baserade program inom Microsoft Azure som vilken plattform som helst.

Själva pappret fokuserar på två huvudsakliga aspekter:

* I den första delen beskrivs två distributions mönster som stöds för SAP NetWeaver-baserade program på Azure. Den beskriver också allmän hantering av Azure med SAP-distributioner i åtanke.
* Den andra delen innehåller information om hur du implementerar de olika scenarier som beskrivs i den första delen.

Mer resurser finns i kapitel [resurser][planning-guide-1.2] i det här dokumentet.

### <a name="definitions-upfront"></a>Definitioner, framtill
I hela dokumentet använder vi följande villkor:

* IaaS: infrastruktur som en tjänst
* PaaS: plattform som en tjänst
* SaaS: program vara som en tjänst
* SAP-komponent: ett enskilt SAP-program som ECC, BW, Solution Manager eller S/4HANA.  SAP-komponenter kan baseras på traditionella ABAP-eller Java-tekniker eller icke-NetWeaver-baserade program som affärs objekt.
* SAP-miljö: en eller flera SAP-komponenter grupperade logiskt för att utföra en affärs funktion som utveckling, QAS, utbildning, DR eller produktion.
* SAP, liggande: den här termen avser hela SAP-till gångar i en kunds IT-liggande. SAP-landskapet innehåller alla produktions miljöer och icke-produktions miljöer.
* SAP-system: kombinationen av DBMS-skikt och program nivå i, till exempel ett SAP ERP-utvecklingssystem, SAP BW test system, SAP CRM-produktionssystem osv. I Azure-distributioner finns det inte stöd för att dela upp dessa två skikt mellan lokalt och Azure. Innebär att ett SAP-system antingen är distribuerat lokalt eller distribuerat i Azure. Du kan dock distribuera de olika systemen i ett SAP-landskap till antingen Azure eller lokalt. Du kan t. ex. distribuera SAP CRM-utvecklings-och test system i Azure, men i det lokala SAP CRM-operativsystemet.
* Mellan platser eller hybrider: beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, flera-plats-eller ExpressRoute-anslutning mellan lokala data Center (er) och Azure. I gemensam Azure-dokumentation beskrivs även dessa typer av distributioner som olika platser eller hybrid scenarier. Orsaken till anslutningen är att utöka lokala domäner, lokala Active Directory/OpenLDAP och lokala DNS i Azure. Det lokala landskapet utökas till Azure-till gångar för prenumerationen. De virtuella datorerna kan vara en del av den lokala domänen. Domän användare av den lokala domänen kan komma åt servrarna och kan köra tjänster på de virtuella datorerna (t. ex. DBMS-tjänster). Kommunikation och namn matchning mellan virtuella datorer som distribuerats lokalt och Azure-distribuerade virtuella datorer är möjligt. Detta är det vanligaste och nästan exklusiva fallet som distribuerar SAP-tillgångar till Azure. Mer information finns i [den här][vpn-gateway-cross-premises-options] artikeln och [detta][vpn-gateway-site-to-site-create].
* Azure Monitoring Extension, förbättrad övervakning och Azure-tillägg för SAP: Beskriv ett och samma objekt. Den beskriver ett VM-tillägg som måste distribueras av dig för att tillhandahålla grundläggande data om Azure-infrastrukturen till SAP-värd agenten. SAP i SAP-anteckningar kan referera till det som övervaknings tillägg eller förbättrad övervakning. I Azure refererar vi till det som Azure- **tillägg för SAP**.

> [!NOTE]
> Mellan lokala eller hybrid distributioner av SAP-system där Azure Virtual Machines som kör SAP-system är medlemmar i en lokal domän som stöds för produktion av SAP-system. Lokala eller hybrid konfigurationer stöds för att distribuera delar eller slutföra SAP-landskap till Azure. Även om du kör hela SAP liggande i Azure måste de virtuella datorerna vara en del av den lokala domänen och ADS/OpenLDAP. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Resurser
Start punkten för SAP-arbetsbelastningen på Azure-dokumentationen finns [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Från och med den här start punkten hittar du många artiklar som beskriver avsnitten i:

- SAP NetWeaver och Business One på Azure
- SAP DBMS-handböcker för olika DBMS-system i Azure
- Hög tillgänglighet och haveri beredskap för SAP-arbetsbelastningar på Azure
- Detaljerad vägledning för att köra SAP HANA på Azure
- Vägledning som är särskilt för Azure HANA-stora instanser för SAP HANA-DBMS 


> [!IMPORTANT]
> När det är möjligt används en länk till de refererande SAP-installations guiderna eller annan SAP-dokumentation (referens InstGuide-01, se <http://service.sap.com/instguides>). När det gäller förutsättningarna, installations processen eller information om särskilda SAP-funktioner bör SAP-dokumentationen och guiderna alltid läsas noga, eftersom Microsoft-dokumenten bara täcker vissa uppgifter för SAP-program som är installerade och körs i en Microsoft Azure virtuell dator.
>
>

Följande SAP-anteckningar är relaterade till ämnet i SAP på Azure:

| Antecknings nummer | Rubrik |
| --- | --- |
| [1928533] |SAP-program på Azure: produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: stöd för krav |
| [1999351] |Felsöka utökad Azure-övervakning för SAP |
| [2178632] |Nyckel övervaknings mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering i Windows: förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: förbättrad övervakning |
| [2243692] |Linux på Microsoft Azure (IaaS) virtuell dator: SAP-licens problem |
| [1984787] |SUSE LINUX Enterprise Server 12: installations information |
| [2002167] |Red Hat Enterprise Linux 7. x: installation och uppgradering |
| [2069760] |Oracle Linux 7. x SAP-installation och uppgradering |
| [1597355] |Rekommendation för växlings utrymme för Linux |

Läs även [SCN-wikin](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP-anteckningar för Linux.

Allmänna standard begränsningar och maximala begränsningar för Azure-prenumerationer finns i [den här artikeln][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Möjliga scenarier
SAP visas ofta som ett av de mest verksamhets kritiska programmen i företag. Arkitekturen och åtgärderna i dessa program är huvudsakligen komplexa och säkerställer att du uppfyller kraven på tillgänglighet och prestanda är viktigt.

Därför måste företag tänka noga om vilken moln leverantör som ska väljas för att köra affärs kritiska affärs processer på. Azure är den perfekta offentliga moln plattformen för affärs kritiska SAP-program och affärs processer. Med tanke på den stora mängden Azure-infrastruktur kan nästan alla befintliga SAP-NetWeaver och S/4HANA-system finnas i Azure idag. Azure tillhandahåller virtuella datorer med många terabyte minne och mer än 200 processorer. Utöver att Azure erbjuder [Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), som tillåter skalbara Hana-distributioner på upp till 24TB och skalbara Hana-distributioner på upp till 120 TB. En kan ange idag att nästan alla lokala SAP-scenarier kan köras i Azure också. 

En grov Beskrivning av scenarierna och vissa scenarier som inte stöds finns i dokumentet SAP- [arbetsbelastning i scenarier med virtuella Azure-datorer som stöds](./sap-planning-supported-configurations.md).

Kontrol lera de här scenarierna och några av de villkor som har angetts som ej stöds i den hänvisade dokumentationen genom att planera och utveckla den arkitektur som du vill distribuera till Azure.

Övergripande det vanligaste distributions mönstret är ett scenario med flera platser som visas

![VPN med plats-till-plats-anslutning (mellan platser)][planning-guide-figure-300]

Anledningen till att många kunder kan tillämpa ett distributions mönster för flera platser är att det är så stort att det är genomskinligt för alla program att utökas lokalt till Azure med Azure ExpressRoute och behandla Azure som ett virtuellt Data Center. När fler och fler till gångar flyttas till Azure kommer den Azure-distribuerade infrastrukturen och nätverks infrastrukturen att växa och de lokala resurserna kommer att minska i enlighet med detta. Allt som är transparent för användare och program.

För att kunna distribuera SAP-system till antingen Azure IaaS eller IaaS i allmänhet är det viktigt att förstå de betydande skillnaderna mellan erbjudandena för traditionella Outsourcer eller värdar och IaaS-erbjudanden. Den traditionella värddatorn eller utlösaren anpassar infrastrukturen (nätverk, lagring och server typ) till arbets belastningen som en kund vill vara värd för, det är i stället kundens eller partnerns ansvar att karakterisera arbets belastningen och välja rätt Azure komponenter för virtuella datorer, lagring och nätverk för IaaS-distributioner.

För att kunna samla in data för planering av distributionen till Azure är det viktigt att:

- Utvärdera vilka SAP-produkter som stöds körs på virtuella Azure-datorer
- Utvärdera vilka speciella operativ system versioner som stöds av vissa virtuella Azure-datorer för dessa SAP-produkter
- Utvärdera vilka DBMS-versioner som stöds för dina SAP-produkter med vissa virtuella Azure-datorer
- Utvärdera om några av de nödvändiga OS/DBMS-versionerna kräver att du utför SAP-version, uppgradering av support paket och kernel-uppgraderingar för att komma till en konfiguration som stöds
- Utvärdera om du behöver flytta till olika operativ system för att kunna distribuera på Azure.

Information om SAP-komponenter som stöds på Azure, Azure-infrastrukturs enheter som stöds och relaterade operativ Systems versioner och DBMS-versioner beskrivs i artikeln [vilka SAP-program som stöds för Azure-distributioner](./sap-supported-product-on-azure.md). Resultat som erhålls från utvärderingen av giltiga SAP-versioner, operativ system och DBMS-versioner har stor påverkan på ansträngningarna att flytta SAP-system till Azure. Resultat från den här utvärderingen är att definiera om det skulle kunna vara betydande förberedelse ansträngningar i fall där SAP release-uppgraderingar eller ändringar av operativ system behövs.



## <a name="first-steps-planning-a-deployment"></a>Första stegen planera en distribution
Det första steget i distributions planeringen är att inte söka efter virtuella datorer som är tillgängliga för att köra SAP. Det första steget kan vara en tids krävande, men viktigast är att arbeta med efterlevnads-och säkerhets team i företaget på vilka gräns villkor som gäller för att distribuera vilken typ av SAP-arbetsbelastning eller affärs process till ett offentligt moln. Om ditt företag distribuerar annan program vara innan du i Azure, kan processen vara lätt. Om ditt företag är mer i början av resan kan det finnas större diskussioner som är nödvändiga för att ta reda på gräns villkoren, säkerhets villkor, som gör att vissa SAP-data och SAP-affärsprocesser kan hanteras i ett offentligt moln.

Som användbar hjälp kan du peka på [Microsoft Compliance-erbjudanden](https://docs.microsoft.com/microsoft-365/compliance/offering-home) för en lista över efterlevnadsprinciper som Microsoft kan tillhandahålla. 

Andra frågor som data kryptering för data i rest eller annan kryptering i Azure-tjänsten dokumenteras i [Översikt över Azure-kryptering](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview).

Funktionen underskattar inte den här fasen av projektet i planeringen. Endast när du har avtal och regler kring det här avsnittet måste du gå till nästa steg, som är planeringen av den nätverks arkitektur som du distribuerar i Azure.



## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure tjänster för virtuella datorer
Microsoft Azures plattformen är en plattform för moln tjänster på Internet som är värd för och drivs i Microsoft Data Center. Plattformen innehåller Microsoft Azure virtuella dator tjänster (infrastruktur som en tjänst eller IaaS) och en uppsättning funktioner för att PaaS-funktioner (Platform as a Service).

Azure-plattformen minskar behovet av upp-front-teknik och infrastruktur inköp. Det fören klar underhåll och drift program genom att tillhandahålla beräknings-och lagrings utrymme på begäran för att vara värd för, skala och hantera webb program och anslutna program. Infrastruktur hantering automatiseras med en plattform som är utformad för hög tillgänglighet och dynamisk skalning för att matcha användnings behov med alternativet för en pris modell enligt principen betala per användning.

![Placering av Microsoft Azure virtuella dator tjänster][planning-guide-figure-400]

Med Azure Virtual Machine-tjänster är det möjligt för Microsoft att distribuera anpassade Server avbildningar till Azure som IaaS-instanser. Virtual Machines i Azure baseras på virtuella hård diskar (VHD) och kan köra olika operativ system som gäst operativ system.

I ett drifts perspektiv erbjuder Azure Virtual Machine-tjänsten liknande upplevelser som virtuella datorer som distribueras lokalt. Det har dock stor nytta av att du inte behöver köpa, administrera och hantera infrastrukturen. Utvecklare och administratörer har fullständig kontroll över operativ system avbildningen på de här virtuella datorerna. Administratörer kan logga in via fjärr anslutning på de virtuella datorerna för att utföra underhålls-och fel söknings aktiviteter samt program distributions uppgifter. Vad gäller distribution är de enda begränsningarna storlekarna och funktionerna i virtuella Azure-datorer. Dessa storlekar kanske inte är lika detaljerade i konfigurationen eftersom det kan göras lokalt. Det finns ett urval av VM-typer som representerar en kombination av:

* Antal virtuella processorer
* Minne
* Antal virtuella hård diskar som kan kopplas
* Nätverks-och lagrings bandbredd

Storlek och begränsningar för olika storlekar på virtuella datorer som erbjuds kan visas i en tabell i [den här artikeln (Linux)][virtual-machines-sizes-linux] och [den här artikeln (Windows)][virtual-machines-sizes-windows].

Det är inte säkert att alla olika VM-serier erbjuds i var och en av Azure-regionerna. Tänk också på att inte alla virtuella datorer eller VM-serier är certifierade för SAP.

> [!IMPORTANT]
> För användning av SAP NetWeaver, S/4HANA och SAP HANA, stöds bara den delmängd av VM-typer och konfigurationer som anges i SAP anmärkning [1928533] . För SAP HANA certifierade Azure-enheter kontrollerar du [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
>

Microsoft Azures plattformen är en plattform för flera innehavare. Det leder till att lagring, nätverk och andra resurser delas mellan klienter. Intelligent begränsning och kvot logik används för att förhindra att en klient påverkar prestandan hos en annan klient (svår granne) på ett drastiskt sätt. Särskilt för att certifiera Azure-plattformen för SAP HANA måste Microsoft bevisa resurs isoleringen för de fall där flera virtuella datorer kan köras på samma värd regelbundet till SAP. Även om logiken i Azure försöker behålla varianser i bandbredden som är små, är det mycket delade plattformar att introducera större varianser i tillgänglighet för resurs/bandbredd än kunder kan uppleva i sina lokala distributioner. Sannolikheten att ett SAP-system på Azure kan uppleva större varianser än i ett lokalt system måste tas med i beräkningen.

Kunderna måste därför vara bekant med de olika funktionerna i de Azure-typer som stöds med SAP i följande områden:

* PROCESSOR-och minnes resurser för olika VM-typer 
* IOPS-bandbredd för olika VM-typer
* Nätverksfunktioner för olika VM-typer

De flesta av dessa data hittar du [här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].


### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-regioner
Virtual Machines distribueras till så kallade *Azure-regioner*. En Azure-region kan vara en eller flera data Center som finns nära varandra. För de flesta regionerna i världen har Microsoft minst två Azure-regioner. I Europa finns det till exempel en Azure-region i *Nord Europa* och *en av Västeuropa*. Sådana två Azure-regioner i ett geografiskt politisk region skiljs åt med tillräckligt stort avstånd så att fysiska eller tekniska haveri effekter inte påverkar både Azure-regioner i samma geografiskt politisk region. Eftersom Microsoft ständigt skapar nya Azure-regioner i olika politiska regioner globalt, växer antalet regioner ständigt och från och med dec 2015 uppnås antalet 20 Azure-regioner med ytterligare regioner som redan har meddelats. Du som kund kan distribuera SAP-system i alla dessa regioner, inklusive de två Azure-regionerna i Kina. Aktuell information om Azure-regioner finns på följande webbplats: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Koncept för Microsoft Azure virtuell dator
Microsoft Azure erbjuder en IaaS-lösning (Infrastructure as a Service) som fungerar som värd för Virtual Machines med liknande funktioner som en lokal virtualiseringslösning. Du kan skapa Virtual Machines inifrån Azure Portal, PowerShell eller CLI, som även erbjuder funktioner för distribution och hantering.

Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall för att distribuera programmet flera gånger under varje fas av programmets livs cykel.

Mer information om hur du använder Resource Manager-mallar finns här:

* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager mallar och Azure CLI](../../linux/create-ssh-secured-vm-from-template.md)
* [Hantera virtuella datorer med hjälp av Azure Resource Manager och PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

En annan intressant funktion är möjligheten att skapa bilder från Virtual Machines, vilket gör att du kan förbereda vissa databaser där du snabbt kan distribuera virtuella dator instanser som uppfyller dina krav.

Mer information om hur du skapar bilder från Virtual Machines finns i [den här artikeln (Linux)][virtual-machines-linux-capture-image-resource-manager] och [den här artikeln (Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Fel domäner
Fel domäner representerar en fysisk felenhet, nära relaterad till den fysiska infrastrukturen i Data Center, medan ett fysiskt blad eller en rack kan betraktas som en feldomän, det finns ingen direkt mappning mellan de två.

När du distribuerar flera Virtual Machines som en del av ett SAP-system i Microsoft Azure virtuella dator tjänster kan du påverka Azure Fabric Controller för att distribuera programmet till olika fel domäner, vilket uppfyller kraven i Microsoft Azure service avtal. Distributionen av fel domäner i en Azure-Scale-enhet (samling av hundratals datornoder eller lagringsnoder och nätverk) eller tilldelningen av virtuella datorer till en speciell fel domän är dock något som du inte har direkt kontroll över. För att dirigera Azure Fabric-styrenheten för att distribuera en uppsättning virtuella datorer över olika fel domäner måste du tilldela en Azure-tillgänglighets uppsättning till de virtuella datorerna vid distributions tillfället. Mer information om Azures tillgänglighets uppsättningar finns i kapitel [tillgänglighets uppsättningar för Azure][planning-guide-3.2.3] i det här dokumentet.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uppgradera domäner
Uppgraderings domäner representerar en logisk enhet som hjälper till att fastställa hur en virtuell dator i ett SAP-system, som består av SAP-instanser som körs i flera virtuella datorer, uppdateras. Vid en uppgradering går Microsoft Azure igenom processen med att uppdatera uppgraderings domänerna en i taget. Genom att sprida virtuella datorer vid distributions tiden över olika uppgraderings domäner kan du skydda ditt SAP-system delvis från potentiella stillestånds tider. För att Azure ska kunna distribuera de virtuella datorerna i ett SAP-system sprids över olika uppgraderings domäner måste du ange ett särskilt attribut vid distributions tiden för varje virtuell dator. På liknande sätt som fel domäner är en Azure Scale Unit uppdelad i flera uppgraderings domäner. För att dirigera Azure Fabric-styrenheten för att distribuera en uppsättning virtuella datorer över olika uppgraderings domäner måste du tilldela en Azure-tillgänglighets uppsättning till de virtuella datorerna vid distributions tillfället. Mer information om Azures tillgänglighets uppsättningar finns i kapitel [tillgänglighets uppsättningar för Azure][planning-guide-3.2.3] nedan.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Tillgänglighets uppsättningar i Azure
Azure-Virtual Machines inom en Azure-tillgänglighets uppsättning distribueras av Azure Fabric Controller över olika fel-och uppgraderings domäner. Syftet med distributionen över olika fel-och uppgraderings domäner är att förhindra att alla virtuella datorer i SAP-system stängs vid infrastruktur underhåll eller fel i en fel domän. Som standard ingår inte virtuella datorer i en tillgänglighets uppsättning. Deltagande av en virtuell dator i en tillgänglighets uppsättning definieras vid distributions tillfället eller senare av en omkonfiguration och omdistribution av en virtuell dator.

Läs [den här artikeln][virtual-machines-manage-availability] för att förstå konceptet med tillgänglighets uppsättningar i Azure och hur tillgänglighets uppsättningar relaterar till fel-och uppgraderings domäner

Om du vill definiera tillgänglighets uppsättningar för Azure Resource Manager via en JSON-mall, se [specifikationerna för REST-API](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) och Sök efter "tillgänglighet".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Lagring: Microsoft Azure Storage och data diskar
Microsoft Azure Virtual Machines använder olika lagrings typer. När du implementerar SAP på Azure Virtual Machine-tjänster är det viktigt att förstå skillnaderna mellan dessa två huvud typer av lagring:

* Icke-beständig, flyktig lagring.
* Beständig lagring.

Virtuella Azure-datorer erbjuder icke-beständiga diskar när en virtuell dator har distribuerats. I händelse av en VM-omstart rensas allt innehåll på dessa enheter. Därför är det en förutsättning att datafiler och logg-/återställnings fil för databaser inte bör finnas på de icke-beständiga enheterna. Det kan finnas undantag för vissa databaser, där dessa icke-beständiga enheter kan vara lämpliga för tempdb och temporära tabell utrymmen. Undvik dock att använda enheterna för virtuella datorer i A-serien eftersom dessa icke-sparade enheter är begränsade i data flöde med den virtuella dator serien. Mer information finns i artikeln [förstå den tillfälliga enheten på virtuella Windows-datorer i Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Enhets D:\ i en virtuell Azure-dator är en icke beständig enhet som backas upp av vissa lokala diskar på Azure Compute-noden. Eftersom den inte är beständig innebär det att alla ändringar som görs i innehållet på D:\ enheten förloras när den virtuella datorn startas om. Efter "alla ändringar", t. ex. filer lagrade, kataloger som skapats, program som installerats osv.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuella Linux Azure-datorer monterar automatiskt en enhet på/mnt/Resource som är en icke beständig enhet som backas upp av lokala diskar på Azure Compute-noden. Eftersom den är icke-beständig innebär det att alla ändringar som görs i innehållet i/mnt/Resource förloras när den virtuella datorn startas om. Ändringar, t. ex. filer lagrade, kataloger som skapats, program som installerats osv.
> 
> 

---

Microsoft Azure Storage tillhandahåller beständig lagring och de typiska skydds nivåer och redundans som visas på SAN-lagring. Diskar som baseras på Azure Storage är virtuella hård diskar (VHD: er) som finns i Azure Storage-tjänsterna. Den lokala OS-disken (Windows C:\, Linux-/dev/sda1) lagras på Azure Storage och ytterligare volymer/diskar som monteras på den virtuella datorn lagras även där.

Det går att ladda upp en befintlig virtuell hård disk lokalt eller skapa en tom virtuell hård disk i Azure och koppla de virtuella hård diskarna till distribuerade virtuella datorer.

När du har skapat eller laddat upp en virtuell hård disk i Azure Storage kan du montera och koppla dem till en befintlig virtuell dator och kopiera befintliga (demonterade) VHD.

Eftersom de virtuella hård diskarna är bestående, är data och ändringar i de virtuella hård diskarna säkra när de startar om och återskapar en virtuell dator instans. Även om en instans tas bort förblir de här virtuella hård diskarna säkra och kan distribueras om, eller om det går att montera icke-OS-diskar på andra virtuella datorer.

Mer information om Azure Storage hittar du här:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure standard Storage
Azure standard Storage var den typ av lagring som är tillgänglig när Azure-IaaS släpptes. Det gällde IOPS-kvoter per enskild disk. Svars tiden upplevde inte i samma klass som SAN/NAS-enheter som vanligt vis har distribuerats för avancerade SAP-system som finns lokalt. Azure standard-lagring visat sig vara tillräckligt för många hundratals SAP-system under tiden som distribuerats i Azure.

Diskar som lagras på Azures standard lagrings konton debiteras baserat på faktiska data som lagras, volymen lagrings transaktioner, utgående data överföringar och alternativ för redundans som valts. Många diskar kan skapas med maximalt 1 TB storlek, men om de inte är tomma är det ingen kostnad. Om du sedan fyller en virtuell hård disk med 100 GB, debiteras du för lagring av 100 GB och inte för den nominella storlek som den virtuella hård disken skapades med.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure-Premium Storage
Azure Premium Storage lanserades med målet att tillhandahålla:

* Bättre I/O-latens.
* Bättre data flöde.
* Mindre variation i i/O-latens.

I detta syfte har många ändringar införts och vilka de två viktigaste är:

* Användning av SSD-diskar i Azure Storage noder
* En ny Read cache som backas upp av den lokala SSD-noden i en Azure-datornod

I motsats till standard lagring där funktionerna inte ändrades beroende på storleken på disken (eller VHD) har Premium Storage för närvarande tre olika disk kategorier, som visas i den här artikeln: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Du ser att IOPS/disk och disk data flöde/disk är beroende av storleks kategorin för diskarna

Kostnads grund för Premium Storage är inte den faktiska data volym som lagras på sådana diskar, men storleks kategorin för en sådan disk, oberoende av mängden data som lagras på disken.

Du kan också skapa diskar på Premium Storage som inte är direkt mappar till de storleks kategorier som visas. Detta kan vara fallet, särskilt när du kopierar diskar från standard lagring till Premium Storage. I sådana fall utförs en mappning till nästa största Premium Storage disk alternativ.

De flesta Azure VM-familjer som certifierats med SAP kan arbeta med Premium Storage och eller en blandning mellan Azure standard och Premium Storage.

Om du checkar ut delen i VM-seriens virtuella datorer i [den här artikeln (Linux)][virtual-machines-sizes-linux] och [den här artikeln (Windows)][virtual-machines-sizes-windows]inser du att det finns data volym begränsningar för Premium Storage diskar på VM-nivåns kornig het. Andra virtuella datorer i DS-serien eller GS-serien har också olika begränsningar för antalet data diskar som kan monteras. Dessa gränser beskrivs också i den artikel som nämns ovan. Men i grunden innebär det att om du t. ex. monterar 32 x P30-diskar till en enskild DS14 VM kan du inte få 32 x maximalt data flöde för en P30-disk. I stället begränsas det maximala data flödet på VM-nivå enligt beskrivningen i artikeln.

Mer information om Premium Storage hittar du här: <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure Storage konton

När du distribuerar tjänster eller virtuella datorer i Azure kan distribution av virtuella hård diskar och VM-avbildningar ordnas i enheter som kallas Azure Storage-konton. När du planerar en Azure-distribution måste du noggrant överväga begränsningarna i Azure. På den ena sidan finns det ett begränsat antal lagrings konton per Azure-prenumeration. Även om varje Azure Storage konto kan innehålla ett stort antal VHD-filer, finns det en fast gräns för det totala antalet IOPS per lagrings konto. När du distribuerar hundratals virtuella SAP-datorer med DBMS-system som skapar betydande IO-anrop, rekommenderar vi att du distribuerar virtuella IOPS-DBMS virtuella datorer mellan flera Azure Storage-konton. Vård måste inte överskrida den aktuella gränsen för Azure Storage konton per prenumeration. Eftersom lagring är en viktig del av databas distributionen för ett SAP-system, diskuteras det här konceptet i detalj i den redan refererade [DBMS-distributions guiden][dbms-guide].

Mer information om Azure Storage-konton finns i [skalbarhets målen för standard lagrings konton](../../../storage/common/scalability-targets-standard-account.md) och [skalbarhets mål för Premium Page Blob Storage-konton](../../../storage/blobs/scalability-targets-premium-page-blobs.md). Genom att läsa de här artiklarna inser du att det finns skillnader i begränsningarna mellan Azure standard Storage-konton och Premium Storage-konton. Större skillnader är mängden data som kan lagras i ett sådant lagrings konto. I standard lagring är volymen en större storlek än med Premium Storage. På den andra sidan är standard lagrings kontot i hög grad begränsat i IOPS (se kolumn **Total begär ande frekvens**), medan Azure Premium Storage-kontot inte har någon sådan begränsning. Vi kommer att diskutera information och resultat av dessa skillnader när du diskuterar distributioner av SAP-system, särskilt DBMS-servrarna.

Inom ett lagrings konto har du möjlighet att skapa olika behållare i syfte att organisera och kategorisera olika virtuella hård diskar. Dessa behållare används till exempel separata virtuella hård diskar för olika virtuella datorer. Det finns inga prestanda konsekvenser i att bara använda en behållare eller flera behållare under ett enda Azure Storage konto.

I Azure visas ett VHD-namn efter följande namn anslutning som måste ange ett unikt namn för den virtuella hård disken i Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Strängen ovan måste vara unikt identifiera den virtuella hård disk som lagras på Azure Storage.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Managed Disks är en ny resurs typ i Azure Resource Manager som kan användas i stället för virtuella hård diskar som lagras i Azure Storage konton. Managed Disks automatiskt justeras mot tillgänglighets uppsättningen för den virtuella datorn som de är kopplade till och därför ökar tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översikts artikeln](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Vi rekommenderar att du använder hanterad disk, eftersom de fören klar distributionen och hanteringen av dina virtuella datorer.
SAP stöder för närvarande endast Premium Managed Disks. Mer information finns i SAP NOTE [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage återhämtning

Microsoft Azure Storage lagrar den grundläggande virtuella hård disken (med OS) och anslutna diskar eller BLOBBAR på minst tre separata lagringsnoder. Detta faktum kallas lokalt redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure. 

Det finns flera olika typer av redundans, som beskrivs i artikeln [Azure Storage replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Från och med Azure Premium Storage, vilket är den rekommenderade typen av lagring för virtuella DBMS-datorer och diskar som lagrar databas-och logg-/återskapa-filer, är den enda tillgängliga metoden LRS. Därför måste du konfigurera databas metoder, t. ex. SQL Server Always on, Oracle data Guard eller HANA-systemreplikering för att aktivera databasreplikering till en annan Azure-region eller en annan Azure-tillgänglighets zon.


> [!NOTE]
> För DBMS-distributioner rekommenderas inte användningen av Geo-redundant lagring som tillgänglig med Azure standard Storage eftersom den har allvarlig prestanda påverkan och inte besvarar Skriv ordningen mellan olika virtuella hård diskar som är anslutna till en virtuell dator. Det faktum att det inte går att använda Skriv ordningen mellan olika virtuella hård diskar är en hög potential för att få fram inkonsekventa databaser på replikerings sidan om databasen och logg/gör om filer sprids över flera virtuella hård diskar (så ofta fallet) på den virtuella käll datorn.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure nätverk

Microsoft Azure tillhandahåller en nätverks infrastruktur som tillåter mappning av alla scenarier, som vi vill realisera med SAP-programvara. Funktionerna är:

* Åtkomst från utsidan, direkt till de virtuella datorerna via Windows Terminal Services eller SSH/VNC
* Åtkomst till tjänster och vissa portar som används av program i de virtuella datorerna
* Intern kommunikation och namn matchning mellan en grupp virtuella datorer som distribueras som virtuella Azure-datorer
* Anslutning mellan olika platser mellan en kunds lokala nätverk och Azure-nätverket
* Mellan Azure-region eller data Center anslutning mellan Azure-platser

Mer information finns här: <https://azure.microsoft.com/documentation/services/virtual-network/>

Det finns många olika möjligheter att konfigurera namn och IP-matchning i Azure. Det finns också en Azure DNS tjänst som kan användas i stället för att konfigurera en egen DNS-server. Mer information finns i [den här artikeln][virtual-networks-manage-dns-in-vnet] och på [den här sidan](https://azure.microsoft.com/services/dns/).

För Cross-premises-eller hybrid scenarier förlitar vi sig på att den lokala AD/OpenLDAP/DNS har utökats via VPN eller privat anslutning till Azure. För vissa scenarier som dokumenteras här kan det vara nödvändigt att ha en AD-OpenLDAP-replik installerad i Azure.

Eftersom nätverk och namn matchning är en viktig del av databas distributionen för ett SAP-system, diskuteras det här konceptet mer detaljerat i [distributions guiden för DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Azure Virtual Networks

Genom att skapa en Azure-Virtual Network kan du definiera adress intervallet för de privata IP-adresser som allokerats av Azure DHCP-funktioner. I scenarier med flera platser allokeras det angivna IP-adressintervallet fortfarande med hjälp av DHCP av Azure. Domän namns matchning görs dock lokalt (förutsatt att de virtuella datorerna är en del av en lokal domän) och därmed kan matcha adresser bortom olika Azure-Cloud Services.

Varje virtuell dator i Azure måste vara ansluten till en Virtual Network.

Mer information hittar du i [den här artikeln][resource-groups-networking] och på [den här sidan](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> När en virtuell dator har distribuerats kan du som standard inte ändra Virtual Network-konfigurationen. TCP/IP-inställningarna måste vara kvar på Azure DHCP-servern. Standard beteendet är dynamisk IP-tilldelning.
>
>

MAC-adressen för det virtuella nätverkskortet kan ändras, till exempel efter storleks ändring och Windows-eller Linux-gästen hämtar det nya nätverkskortet och automatiskt använder DHCP för att tilldela IP-och DNS-adresser i det här fallet.

##### <a name="static-ip-assignment"></a>Statisk IP-tilldelning
Det är möjligt att tilldela fasta eller reserverade IP-adresser till virtuella datorer i ett Azure-Virtual Network. Om du kör de virtuella datorerna i ett Azure-Virtual Network öppnas en bra möjlighet att använda den här funktionen om det behövs eller krävs för vissa scenarier. IP-tilldelningen är giltig för hela den virtuella datorn, oberoende av om den virtuella datorn körs eller stängs av. Därför måste du ange det totala antalet virtuella datorer (som kör och stoppade virtuella datorer) i kontot när du definierar intervallet för IP-adresser för Virtual Network. IP-adressen är tilldelad antingen tills den virtuella datorn och dess nätverks gränssnitt tas bort eller tills IP-adressen tilldelas på nytt. Mer information finns i [den här artikeln][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure till enskilda virtuella nätverkskort. Du bör inte tilldela statiska IP-adresser i gäst operativ systemet till en vNIC. Vissa Azure-tjänster som Azure Backup-tjänsten är beroende av att minst primär vNIC är inställd på DHCP och inte statiska IP-adresser. Se även dokumentet [Felsöka säkerhets kopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Flera nätverkskort per virtuell dator

Du kan definiera flera virtuella nätverks gränssnitts kort (vNIC) för en virtuell Azure-dator. Med möjligheten att ha flera virtuella nätverkskort kan du börja konfigurera nätverks trafik separation där exempelvis klient trafik dirigeras genom en vNIC och Server dels trafik dirigeras genom en andra vNIC. Beroende av typen av virtuell dator finns det olika begränsningar för antalet virtuella nätverkskort som en virtuell dator kan ha tilldelats. Detaljerad information, funktioner och begränsningar finns i följande artiklar:

* [Skapa en virtuell Windows-dator med flera nätverkskort][virtual-networks-multiple-nics-windows]
* [Skapa en virtuell Linux-dator med flera nätverkskort][virtual-networks-multiple-nics-linux]
* [Distribuera virtuella datorer med flera nätverkskort med en mall][virtual-network-deploy-multinic-arm-template]
* [Distribuera virtuella datorer med flera nätverkskort med hjälp av PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Distribuera virtuella datorer med flera nätverkskort med hjälp av Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Plats-till-plats-anslutning

Virtuella Azure-datorer och lokala nätverk är länkade till en transparent och permanent VPN-anslutning. Det förväntas bli det vanligaste distributions mönstret för SAP i Azure. Antagande är att operativa procedurer och processer med SAP-instanser i Azure bör fungera transparent. Det innebär att du bör kunna skriva ut från dessa system samt använda SAP transport Management system (TMS) för att transportera ändringar från ett utvecklings system i Azure till ett test system som distribueras lokalt. Mer dokumentation kring plats-till-plats finns i [den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-tunnel enhet

För att skapa en plats-till-plats-anslutning (lokalt Data Center till Azure Data Center) måste du antingen hämta och konfigurera en VPN-enhet eller använda tjänsten Routning och fjärråtkomst (RRAS) som introducerades som en program varu komponent med Windows Server 2012.

* [Skapa ett virtuellt nätverk med en plats-till-plats-VPN-anslutning med PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Om VPN-enheter för plats-till-plats-VPN Gateway-anslutningar][vpn-gateway-about-vpn-devices]
* [Vanliga frågor och svar om VPN Gateway][vpn-gateway-vpn-faq]

![Plats-till-plats-anslutning mellan lokala platser och Azure][planning-guide-figure-600]

Bilden ovan visar två Azure-prenumerationer som har reserverade IP-adresser för användning i virtuella nätverk i Azure. Anslutningen från det lokala nätverket till Azure upprättas via VPN.

#### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Punkt-till-plats-VPN kräver att varje klient dator ansluter med sin egen VPN till Azure. För SAP-scenarier tittar vi på, punkt-till-plats-anslutning är inte praktiskt. Därför ges inga ytterligare referenser till punkt-till-plats-VPN-anslutning.

Mer information hittar du här
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN för flera platser

Azure nuförtiden erbjuder också möjligheten att skapa VPN-anslutningar på flera platser för en Azure-prenumeration. Tidigare var en enskild prenumeration begränsad till en plats-till-plats-VPN-anslutning. Den här begränsningen gick bort med VPN-anslutningar på flera platser för en enda prenumeration. Detta gör det möjligt att använda mer än en Azure-region för en speciell prenumeration genom konfigurationer med flera platser.

Mer dokumentation finns i [den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>VNet-till-VNet-anslutning

Med VPN för flera platser måste du konfigurera en separat Azure-Virtual Network i varje region. Du har dock ofta kravet att program komponenterna i de olika regionerna ska kommunicera med varandra. Vi rekommenderar att denna kommunikation inte vidarebefordras från en Azure-region till en lokal plats och därifrån till den andra Azure-regionen. Azure ger dig möjlighet att konfigurera en anslutning från en Azure-Virtual Network i en region till en annan Azure-Virtual Network som finns i en annan region. Den här funktionen kallas VNet-till-VNet-anslutning. Du hittar mer information om den här funktionen här: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Privat anslutning till Azure-ExpressRoute

Med Microsoft Azure ExpressRoute kan du skapa privata anslutningar mellan Azures Data Center och antingen kundens lokala infrastruktur eller i en miljö med en gemensam plats. ExpressRoute erbjuds av olika MPLS-VPN-providers (Packet Switched) eller andra nätverks tjänst leverantörer. ExpressRoute-anslutningar går inte via offentligt Internet. ExpressRoute-anslutningar ger högre säkerhet, mer tillförlitlighet genom flera parallella kretsar, snabbare hastigheter och lägre fördröjning än vanliga anslutningar via Internet.

Hitta mer information om Azure ExpressRoute och erbjudanden här:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route möjliggör flera Azure-prenumerationer via en ExpressRoute-krets som dokumenteras här

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Tvingad tunnel trafik i händelse av olika platser
För virtuella datorer som ansluter till lokala domäner via plats-till-plats-, punkt-till-plats-eller ExpressRoute, måste du se till att Internet-proxyinställningarna inte har distribuerats för alla användare i de virtuella datorerna. Som standard skulle program som körs i de virtuella datorerna eller användare som använder en webbläsare för att komma åt Internet inte gå via företagets proxy, men skulle ansluta direkt via Azure till Internet. Men även om proxyinställningarna inte är en 100%-lösning för att dirigera trafiken via företagets proxy, eftersom det är ansvars område och tjänster för att kontrol lera proxyn. Om program som körs på den virtuella datorn inte gör det eller om en administratör ändrar inställningarna kan trafik till Internet tas ur drift direkt via Azure till Internet.

För att undvika sådan direkt Internet anslutning kan du konfigurera Tvingad tunnel trafik med plats-till-plats-anslutning mellan lokalt och Azure. En detaljerad beskrivning av funktionen för Tvingad tunnel trafik publiceras här <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Tvingad tunnel trafik med ExpressRoute har Aktiver ATS av kunder som skickar en standard väg via ExpressRoute BGP-peering-sessioner.

#### <a name="summary-of-azure-networking"></a>Översikt över Azure-nätverk

Det här kapitlet innehåller många viktiga punkter om Azure-nätverk. Här är en sammanfattning av de viktigaste punkterna:

* Med virtuella Azure-nätverk kan du lagra en nätverks struktur i Azure-distributionen. Virtuella nätverk kan vara isolerade mot varandra eller med hjälp av nätverks säkerhets grupps trafik mellan virtuella nätverk kan styras.
* Virtuella Azure-nätverk kan utnyttjas för att tilldela IP-adressintervall till virtuella datorer eller tilldela fasta IP-adresser till virtuella datorer
* Om du vill konfigurera en plats-till-plats-eller punkt-till-plats-anslutning måste du först skapa en Azure-Virtual Network
* När en virtuell dator har distribuerats går det inte längre att ändra Virtual Network som tilldelats den virtuella datorn

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvoter i Azure Virtual Machine-tjänster
Vi måste vara tydliga om det faktum att lagrings-och nätverks infrastrukturen delas mellan virtuella datorer som kör en mängd olika tjänster i Azure-infrastrukturen. Och precis som i kundens egna data Center, sker överetablering av några av infrastruktur resurserna i en viss grad. Microsoft Azures plattformen använder disk, processor, nätverk och andra kvoter för att begränsa resursanvändningen och bevara konsekvent och deterministisk prestanda.  De olika typerna av virtuella datorer (A5, A6 osv.) har olika kvoter för antalet diskar, CPU: n, RAM-minne och nätverk.

> [!NOTE]
> PROCESSOR-och minnes resurser för de VM-typer som stöds av SAP är förallokerade på noderna. Det innebär att när den virtuella datorn har distribuerats är resurserna på värden tillgängliga som de definieras av VM-typen.
>
>

När du planerar och ändrar storlek på SAP på Azure-lösningar måste kvoterna för varje virtuell dator storlek beaktas. De virtuella dator kvoterna beskrivs [här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

De kvoter som beskrivs representerar de teoretiska maximala värdena.  Gränsen för IOPS per disk kan uppnås med Small IOs (8 KB), men kanske inte kan uppnås med stor IOs (1 MB).  IOPS-gränsen tillämpas på en enskild disk granularitet.

Som ett grovt besluts träd för att avgöra om ett SAP-system passar in i Azures tjänster för virtuella datorer och dess funktioner eller om ett befintligt system måste konfigureras på ett annat sätt för att kunna distribuera systemet på Azure, kan besluts trädet nedan användas:

![Besluts träd för att bestämma möjlighet att distribuera SAP på Azure][planning-guide-figure-700]

**Steg 1**: den viktigaste informationen att börja med är SAPS-kravet för ett angivet SAP-system. SAPS-kraven måste delas upp i DBMS-delen och SAP-programdelen, även om SAP-systemet redan har distribuerats lokalt i en konfiguration på två nivåer. För befintliga system kan SAPS som är relaterade till maskin varan som används ofta bestämmas eller beräknas utifrån befintliga SAP-benchmarks. Du hittar resultaten här: <https://sap.com/about/benchmark.html>.
För nyligen distribuerade SAP-system bör du ha genomgått en storleks bedömning, vilket bör fastställa systemets krav på SAPS.
Se även den här bloggen och det bifogade dokumentet för SAP-storlek på Azure: <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Steg 2**: för befintliga system ska i/o-volymer och i/o-åtgärder per sekund på DBMS-servern mätas. För nyligen planerade system bör storleks övningen för det nya systemet också ge grova idéer om I/O-kraven på DBMS-sidan. Om du är osäker måste du utföra ett koncept bevis.

**Steg 3**: jämför SAPS-kravet för DBMS-servern med SAPS de olika VM-typerna för Azure kan tillhandahålla. Informationen om SAPS av olika typer av virtuella Azure-datorer finns dokumenterad i SAP NOTE [1928533]. Fokus bör vara på den virtuella DBMS-datorn först eftersom databas skiktet är skiktet i ett SAP NetWeaver-system som inte skalas ut i de flesta distributioner. SAP-programlagret kan däremot skalas ut. Om inget av de SAP-typer som stöds av Azure VM-typer kan leverera nödvändiga SAPS, kan arbets belastningen för det planerade SAP-systemet inte köras på Azure. Du måste antingen distribuera systemet lokalt eller så måste du ändra arbets belastnings volymen för systemet.

**Steg 4**: som dokumenteras [här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows]tillämpar Azure en IOPS-kvot per disk oberoende om du använder standard lagring eller Premium Storage. Beroende på typ av virtuell dator, antalet data diskar som kan monteras varierar. Det innebär att du kan beräkna ett högsta IOPS-nummer som kan uppnås med var och en av de olika typerna av virtuella datorer. Beroende av databasens fillayout kan du göra stripe-diskar för att bli en volym i gäst operativ systemet. Men om den aktuella IOPS-volymen i ett distribuerat SAP-system överskrider de beräknade gränserna för den största VM-typen i Azure och om det inte finns någon risk att kompensera med mer minne, kan arbets belastningen för SAP-systemet påverkas allvarligt. I sådana fall kan du trycka på en punkt där du inte bör distribuera systemet på Azure.

**Steg 5**: särskilt i SAP-system, som distribueras lokalt i konfigurationer på två nivåer, är sannolikheten att systemet kan behöva konfigureras på Azure i en konfiguration på tre nivåer. I det här steget måste du kontrol lera om det finns en komponent i SAP-programlagret, som inte kan skalas ut och som inte passar in i processor-och minnes resurser som de olika Azure VM-typerna erbjuder. Om det verkligen finns en sådan komponent kan inte SAP-systemet och dess arbets belastning distribueras till Azure. Men om du kan skala ut SAP-programkomponenter till flera virtuella Azure-datorer kan systemet distribueras till Azure.

**Steg 6**: om DBMS-och SAP-programmets skikt komponenter kan köras i virtuella Azure-datorer måste konfigurationen definieras med avseende på:

* Antal virtuella Azure-datorer
* VM-typer för enskilda komponenter
* Antal virtuella hård diskar i DBMS VM för att tillhandahålla tillräckligt med IOPS

## <a name="managing-azure-assets"></a>Hantera Azure-tillgångar

### <a name="azure-portal"></a>Azure-portalen

Azure Portal är ett av tre gränssnitt för att hantera distributioner av virtuella Azure-datorer. De grundläggande hanterings aktiviteterna, t. ex. distribution av virtuella datorer från avbildningar, kan göras via Azure Portal. Dessutom är skapandet av lagrings konton, virtuella nätverk och andra Azure-komponenter också aktiviteter som Azure Portal kan hantera bra. Funktioner som att ladda upp virtuella hård diskar från lokala datorer till Azure eller kopiera en virtuell hård disk i Azure är dock aktiviteter, vilket kräver verktyg från tredje part eller administration via PowerShell eller CLI.

![Översikt över Microsoft Azure-portalen virtuell dator][planning-guide-figure-800]


Administrations-och konfigurations uppgifter för den virtuella dator instansen är möjliga i Azure Portal.

Förutom att starta om och stänga av en virtuell dator kan du också koppla, koppla från och skapa data diskar för den virtuella dator instansen, för att avbilda instansen för avbildnings förberedelse och konfigurera storleken på den virtuella dator instansen.

Azure Portal innehåller grundläggande funktioner för att distribuera och konfigurera virtuella datorer och många andra Azure-tjänster. Alla tillgängliga funktioner omfattas dock inte av Azure Portal. I Azure Portal går det inte att utföra uppgifter som:

* Laddar upp VHD: er till Azure
* Kopiera virtuella datorer


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Hantering via Microsoft Azure PowerShell-cmdletar

Windows PowerShell är ett kraftfullt och utöknings Bart ramverk som har varit mycket omfattande av kunder som distribuerar ett större antal system i Azure. Efter installationen av PowerShell-cmdletar på en stationär dator, bärbar dator eller dedikerad hanterings Station kan PowerShell-cmdlet: ar köras via fjärr anslutning.

Processen för att aktivera en lokal stationär dator/bärbar dator för användning av Azure PowerShell-cmdletar och hur du konfigurerar dem för användning med Azure-prenumerationerna beskrivs i [den här artikeln][powershell-install-configure].

Mer detaljerade anvisningar om hur du installerar, uppdaterar och konfigurerar Azure PowerShell-cmdletar finns i [det här kapitlet i distributions guiden][deployment-guide-4.1].

Kund upplevelsen har hittills varit att PowerShell (PS) är det mest kraftfulla verktyget för att distribuera virtuella datorer och att skapa anpassade steg i distributionen av virtuella datorer. Alla kunder som kör SAP-instanser i Azure använder PS-cmdlet: ar för att komplettera de hanterings uppgifter som de gör i Azure Portal eller även använder PS-cmdlet: ar enbart för att hantera sina distributioner i Azure. Eftersom de Azure-/regionsspecifika cmdletarna delar samma namngivnings konvention som över 2000 Windows-relaterade cmdlets, är det en enkel uppgift för Windows-administratörer att utnyttja dessa cmdlets.

Se exemplet här: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Distribution av Azure-tillägget för SAP (se kapitel [Azure-tillägg för SAP][planning-guide-9.1] i det här dokumentet) är bara möjlig via POWERSHELL eller cli. Därför är det nödvändigt att konfigurera och konfigurera PowerShell eller CLI när du distribuerar eller administrerar ett SAP NetWeaver-system i Azure.  

Eftersom Azure tillhandahåller fler funktioner kommer nya PS-cmdlets att läggas till som kräver en uppdatering av cmdletarna. Därför är det klokt att kontrol lera Azures hämtnings plats minst en gång i månaden <https://azure.microsoft.com/downloads/> för en ny version av cmdletarna. Den nya versionen installeras ovanpå den äldre versionen.

En allmän lista över Azure-relaterade PowerShell-kommandon finns här: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Hantering via Microsoft Azure CLI-kommandon

För kunder som använder Linux och vill hantera Azure-resurser kan det hända att PowerShell inte är ett alternativ. Microsoft erbjuder Azure CLI som ett alternativ.
Azure CLI innehåller en uppsättning kommandon med öppen källkod, plattforms oberoende funktioner för att arbeta med Azure-plattformen. Azure CLI innehåller många av de funktioner som finns i Azure Portal.

Information om installation, konfiguration och hur du använder CLI-kommandon för att utföra Azure-aktiviteter finns i

* [Installera den klassiska Azure CLI][xplat-cli]
* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI] [../../linux/create-ssh-secured-vm-from-template.md]
* [Använd den klassiska Azure-CLI: en för Mac, Linux och Windows med Azure Resource Manager][xplat-cli-azure-resource-manager]

Läs även kapitel [Azure CLI för virtuella Linux-datorer][deployment-guide-4.5.2] i [distributions guiden][planning-guide] för hur du använder Azure CLI för att distribuera Azure-tillägget för SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Olika sätt att distribuera virtuella datorer för SAP i Azure

I det här kapitlet får du lära dig olika sätt att distribuera en virtuell dator i Azure. Ytterligare förberedelse procedurer, samt hantering av virtuella hård diskar och virtuella datorer i Azure, beskrivs i det här kapitlet.

### <a name="deployment-of-vms-for-sap"></a>Distribution av virtuella datorer för SAP

Microsoft Azure erbjuder flera olika sätt att distribuera virtuella datorer och tillhör ande diskar. Det är därför viktigt att förstå skillnaderna eftersom virtuella datorers förberedelser kan variera beroende på distributions metoden. I allmänhet tar vi en titt på följande scenarier:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Flytta en virtuell dator från lokal plats till Azure med en icke-generaliserad disk

Du planerar att flytta ett angivet SAP-system från en lokal plats till Azure. Detta kan göras genom att ladda upp den virtuella hård disken som innehåller operativ systemet, SAP-binärfiler och DBMS-binärfiler plus de virtuella hård diskarna med data-och loggfilerna för DBMS till Azure. Till skillnad från [scenario #2 nedan][planning-guide-5.1.2]behåller du användar kontona för värd namnet, SAP sid och SAP på den virtuella Azure-datorn som de konfigurerades i den lokala miljön. Därför är det inte nödvändigt att generalisera avbildningen. Se kapitel [förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk][planning-guide-5.2.1] i det här dokumentet för de lokala förberedelse stegen och överföra icke-generaliserade virtuella datorer eller virtuella hård diskar till Azure. Läs kapitel [Scenario 3: flytta en virtuell dator från en lokal plats med en icke-generaliserad virtuell Azure-dator med SAP][deployment-guide-3.4] i [distributions guiden][deployment-guide] för detaljerade anvisningar om hur du distribuerar en sådan avbildning i Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Distribuera en virtuell dator med en kundspecifik avbildning

På grund av särskilda krav på uppdatering av operativ system-eller DBMS-versionen kanske de angivna avbildningarna på Azure Marketplace inte passar dina behov. Därför kan du behöva skapa en virtuell dator med en egen privat OS/DBMS VM-avbildning, som kan distribueras flera gånger efteråt. För att förbereda en sådan privat avbildning för duplicering måste följande objekt beaktas:

---
> ![Windows][Logo_Windows] Windows
>
> Se mer information här: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Windows-inställningarna (t. ex. Windows SID och värdnamn) måste vara abstrakta/generaliserade på den lokala virtuella datorn via Sysprep-kommandot.
>
>
> ![Linux][Logo_Linux] Linux
>
> Följ stegen som beskrivs i de här artiklarna för [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]eller [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle]för att förbereda en virtuell hård disk som ska överföras till Azure.
>
>

---
Om du redan har installerat SAP-innehåll i din lokala virtuella dator (särskilt för 2-nivå system) kan du anpassa inställningarna för SAP-systemet efter distributionen av den virtuella Azure-datorn via den instans namn procedur som stöds av SAP Software Provisioning Manager (SAP NOTE [1619720]). Se kapitel [förberedelser för att distribuera en virtuell dator med en kundspecifik avbildning för SAP][planning-guide-5.2.2] och [Ladda upp en virtuell hård disk från lokalt till Azure][planning-guide-5.3.2] i det här dokumentet för lokala förberedelse steg och uppladdning av en generaliserad virtuell dator till Azure. Läs kapitel [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3] i [distributions guiden][deployment-guide] för detaljerade anvisningar om hur du distribuerar en sådan avbildning i Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

Du vill använda en virtuell dator avbildning från Microsoft eller tredje part från Azure Marketplace för att distribuera den virtuella datorn. När du har distribuerat den virtuella datorn i Azure följer du samma rikt linjer och verktyg för att installera SAP-programvaran och/eller DBMS i den virtuella datorn som du skulle göra i en lokal miljö. Mer detaljerad distributions beskrivning finns i kapitel [Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP][deployment-guide-3.2] i [distributions guiden][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Förbereda virtuella datorer med SAP för Azure

Innan du överför virtuella datorer till Azure måste du se till att de virtuella datorerna och de virtuella hård diskarna uppfyller vissa krav. Det finns små skillnader beroende på vilken distributions metod som används.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk

En vanlig distributions metod är att flytta en befintlig virtuell dator, som kör ett SAP-system från lokal plats till Azure. Den virtuella datorn och SAP-systemet på den virtuella datorn bör bara köras i Azure med samma värdnamn och förmodligen samma SAP-SID. I det här fallet bör gäst operativ systemet för den virtuella datorn inte generaliseras för flera distributioner. Om det lokala nätverket har utökats till Azure kan även samma domän konton användas i den virtuella datorn som de användes innan lokalt.

Krav när du förbereder din egen Azure VM-disk:

* Den virtuella hård disken som innehåller operativ systemet kan endast ha en maximal storlek på 127GB. Den här begränsningen togs bort i slutet av mars 2015. Nu kan den virtuella hård disken som innehåller operativ systemet vara upp till 1 TB i storlek som andra Azure Storage värdbaserade virtuella hård diskar också.
* Den måste vara i det fasta VHD-formatet. Dynamiska virtuella hård diskar eller virtuella hård diskar i VHDx-format stöds ännu inte i Azure. Dynamiska virtuella hård diskar konverteras till statiska virtuella hård diskar när du laddar upp den virtuella hård disken med PowerShell-cmdletarna eller CLI
* Virtuella hård diskar, som monteras på den virtuella datorn och ska monteras igen i Azure till den virtuella datorn måste också ha ett fast VHD-format. Läs [den här artikeln (Linux)](../../linux/managed-disks-overview.md) och [den här artikeln (Windows)](../../windows/managed-disks-overview.md)) för storleks gränser för data diskar. Dynamiska virtuella hård diskar konverteras till statiska virtuella hård diskar när du laddar upp den virtuella hård disken med PowerShell-cmdletarna eller CLI
* Lägg till ett annat lokalt konto med administratörs behörighet, som kan användas av Microsoft support eller som kan tilldelas som kontext för tjänster och program som ska köras i tills den virtuella datorn har distribuerats och mer lämpliga användare kan användas.
* Lägg till andra lokala konton som de kan behöva för det angivna distributions scenariot.

---
> ![Windows][Logo_Windows] Windows
>
> I det här scenariot krävs ingen generalisering (Sysprep) av den virtuella datorn för att ladda upp och distribuera den virtuella datorn i Azure.
> Kontrol lera att enheten D:\ används inte.
> Ange automonterad disk för anslutna diskar enligt beskrivningen i kapitel [Ange automontera för anslutna diskar][planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> I det här scenariot krävs ingen generalisering (waagent) av den virtuella datorn för att ladda upp och distribuera den virtuella datorn i Azure.
> Se till att/mnt/Resource inte används och att alla diskar är monterade via uuid. För OS-disken kontrollerar du att Start programmet också återspeglar den UUID-baserade monteringen.
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Förberedelser inför distribution av en virtuell dator med en kundspecifik avbildning för SAP

VHD-filer som innehåller ett generaliserat operativ system lagras i behållare på Azure Storage konton eller som hanterade disk avbildningar. Du kan distribuera en ny virtuell dator från en sådan avbildning genom att referera till den virtuella hård disken eller den hanterade disk avbildningen som en källa i mallen för distributions mal len enligt beskrivningen i kapitel [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3] i [distributions guiden][deployment-guide].

Krav när du förbereder din egen Azure VM-avbildning:

* Den virtuella hård disken som innehåller operativ systemet kan endast ha en maximal storlek på 127GB. Den här begränsningen togs bort i slutet av mars 2015. Nu kan den virtuella hård disken som innehåller operativ systemet vara upp till 1 TB i storlek som andra Azure Storage värdbaserade virtuella hård diskar också.
* Den måste vara i det fasta VHD-formatet. Dynamiska virtuella hård diskar eller virtuella hård diskar i VHDx-format stöds ännu inte i Azure. Dynamiska virtuella hård diskar konverteras till statiska virtuella hård diskar när du laddar upp den virtuella hård disken med PowerShell-cmdletarna eller CLI
* Virtuella hård diskar, som monteras på den virtuella datorn och ska monteras igen i Azure till den virtuella datorn måste också ha ett fast VHD-format. Läs [den här artikeln (Linux)](../../windows/managed-disks-overview.md) och [den här artikeln (Windows)](../../linux/managed-disks-overview.md) för storleks gränser för data diskar. Dynamiska virtuella hård diskar konverteras till statiska virtuella hård diskar när du laddar upp den virtuella hård disken med PowerShell-cmdletarna eller CLI
* Lägg till andra lokala konton som de kan behöva för det angivna distributions scenariot.
* Om avbildningen innehåller en installation av SAP-NetWeaver och omnamnering av värd namnet från det ursprungliga namnet vid Azure-distributionen är troligt, rekommenderar vi att du kopierar de senaste versionerna av SAP Software Provisioning Manager-DVD: n till mallen. På så sätt kan du enkelt använda de funktioner för SAP som har angetts för att anpassa det ändrade värd namnet och/eller ändra SID för SAP-systemet i den distribuerade virtuella dator avbildningen så fort en ny kopia startas.

---
> ![Windows][Logo_Windows] Windows
>
> Kontrol lera att enheten D:\ används inte ange automonterad disk för anslutna diskar enligt beskrivningen i kapitel [Ange automontera för anslutna diskar][planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> Se till att/mnt/Resource inte används och att alla diskar är monterade via uuid. För OS-disken kontrollerar du att Start programmet också återspeglar den UUID-baserade monteringen.
>
>

---
* SAP GUI (för administration och konfigurations syfte) kan vara förinstallerat i en sådan mall.
* Annan program vara som krävs för att köra de virtuella datorerna i scenarier med flera platser kan installeras så länge den här program varan kan arbeta med den virtuella datorns namnbyte.

Om den virtuella datorn är för beredd för att vara generisk och slutligen oberoende av konton/användare som inte är tillgängliga i det riktade scenariot för Azure-distribution, utförs det senaste förberedelse steget för att generalisera sådan avbildning.

##### <a name="generalizing-a-vm"></a>Generalisera en virtuell dator
---
> ![Windows][Logo_Windows] Windows
>
> Det sista steget är att logga in på en virtuell dator med ett administratörs konto. Öppna ett Windows-kommando fönster som *administratör*. Gå till%windir%\windows\system32\sysprep och kör Sysprep. exe.
> Ett litet fönster visas. Det är viktigt att kontrol lera **generalize** -alternativet (Standardvärdet är avmarkerat) och ändra avstängnings alternativet från standardvärdet ' reboot ' till ' shutdown '. Den här proceduren förutsätter att Sysprep-processen körs lokalt i gäst operativ systemet på en virtuell dator.
> Om du vill utföra proceduren med en virtuell dator som redan körs i Azure följer du stegen som beskrivs i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Så här avbildar du en virtuell Linux-dator som ska användas som mall för Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Överföra virtuella datorer och virtuella hård diskar mellan lokala datorer och Azure
Eftersom det inte går att ladda upp VM-avbildningar och diskar till Azure via Azure Portal måste du använda Azure PowerShell-cmdletar eller CLI. En annan möjlighet är att använda verktyget ' AzCopy '. Verktyget kan kopiera virtuella hård diskar mellan lokalt och Azure (i båda riktningarna). Den kan också kopiera virtuella hård diskar mellan Azure-regioner. Läs [den här dokumentationen][storage-use-azcopy] för hämtning och användning av AzCopy.

Ett tredje alternativ är att använda olika verktyg som är orienterade från andra tillverkare. Se dock till att dessa verktyg stöder Azure Page blobbar. I vårt syfte måste vi använda Azure Page BLOB Store (skillnaderna beskrivs här: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). De verktyg som tillhandahålls av Azure är också effektiva för att komprimera virtuella datorer och virtuella hård diskar, som måste överföras. Detta är viktigt eftersom den här effektiviteten i komprimeringen minskar uppladdnings tiden (som varierar beroende på uppladdnings länken till Internet från den lokala anläggningen och det riktade Azure-distributions området). Det är ett rimligt antagande att det tar längre tid att ladda upp en virtuell dator eller en virtuell hård disk från den europeiska platsen till de amerikanska Azure-datacenterna än att ladda upp samma virtuella datorer/virtuella hård diskar till de europeiska Azure-datacenterna.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Ladda upp en virtuell hård disk från lokal plats till Azure
För att överföra en befintlig virtuell dator eller virtuell hård disk från det lokala nätverket måste en virtuell dator eller virtuell hård disk uppfylla kraven som anges i kapitel [förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk][planning-guide-5.2.1] i det här dokumentet.

Sådan virtuell dator behöver inte generaliseras och kan laddas upp i det tillstånd och den form den har efter avstängning på den lokala sidan. Detsamma gäller för ytterligare virtuella hård diskar, som inte innehåller något operativ system.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Ladda upp en virtuell hård disk och göra den till en Azure-disk
I det här fallet vill vi ladda upp en virtuell hård disk, antingen med eller utan ett operativ system, och montera den på en virtuell dator som en data disk eller använda den som en operativ system disk. Detta är en process med flera steg

**PowerShell**

* Logga in på din prenumeration med *Connect-AzAccount*
* Ange prenumerationen för din kontext med *set-AzContext* och parametern SubscriptionId eller SubscriptionName-se <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Ladda upp den virtuella hård disken med *Add-AzVhd* till ett Azure Storage konto – se <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Valfritt Skapa en hanterad disk från den virtuella hård disken med *New-AzDisk* -se <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Ange operativ system disken för en ny VM-konfiguration till den virtuella hård disken eller hanterad disk med *set-AzVMOSDisk* – se <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Skapa en ny virtuell dator från den virtuella dator konfigurationen med *New-AzVM* -se <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Lägg till en datadisk till en ny virtuell dator med *Add-AzVMDataDisk* -se <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Logga in på din prenumeration med *AZ-inloggning*
* Välj din prenumeration med *AZ-konto uppsättning--prenumerations `<subscription name or id`>*
* Ladda upp den virtuella hård disken med *AZ Storage BLOB upload* -se [använda Azure CLI med Azure Storage][storage-azure-cli]
* Valfritt Skapa en hanterad disk från den virtuella hård disken med *AZ disk Create* -se https://docs.microsoft.com/cli/azure/disk
* Skapa en ny virtuell dator som anger den överförda virtuella hård disken eller hanterade diskar som OS-disk med *AZ VM Create* och parameter *--Attach-OS-disk*
* Lägg till en datadisk till en ny virtuell dator med *AZ VM disk Attach* och parameter *-New*

**Mall**

* Ladda upp den virtuella hård disken med PowerShell eller Azure CLI
* Valfritt Skapa en hanterad disk från den virtuella hård disken med PowerShell, Azure CLI eller Azure Portal
* Distribuera den virtuella datorn med en JSON-mall som refererar till den virtuella hård disken som visas i [den här exempel-JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller med Managed disks som visas i [den här exempel-JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)

#### <a name="deployment-of-a-vm-image"></a>Distribution av en VM-avbildning
Om du vill överföra en befintlig virtuell dator eller virtuell hård disk från det lokala nätverket måste du, för att kunna använda den som en virtuell Azure-dator avbildning, så att en virtuell dator eller virtuell hård disk uppfyller kraven som anges i kapitel [förberedelser för att distribuera en virtuell dator med en kundspecifik avbildning för SAP][planning-guide-5.2.2] av det här dokumentet.

* Använd *Sysprep* på Windows eller *waagent-deetablering* i Linux för att generalisera din virtuella dator – se [Sysprep teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en virtuell Linux-dator som ska användas som en Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *Connect-AzAccount*
* Ange prenumerationen för din kontext med *set-AzContext* och parametern SubscriptionId eller SubscriptionName-se <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Ladda upp den virtuella hård disken med *Add-AzVhd* till ett Azure Storage konto – se <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Valfritt Skapa en hanterad disk avbildning från den virtuella hård disken med *New-AzImage* -se <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Ange operativ system disken för en ny VM-konfiguration till
  * VHD med *set-AzVMOSDisk-SourceImageUri-CreateOption fromImage* -se <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Hanterad disk avbildnings *uppsättning – AzVMSourceImage* – se <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Skapa en ny virtuell dator från den virtuella dator konfigurationen med *New-AzVM* -se <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* Använd *Sysprep* på Windows eller *waagent-deetablering* i Linux för att generalisera din virtuella dator – se [Sysprep teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en virtuell Linux-dator som ska användas som en Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *AZ-inloggning*
* Välj din prenumeration med *AZ-konto uppsättning--prenumerations `<subscription name or id`>*
* Ladda upp den virtuella hård disken med *AZ Storage BLOB upload* -se [använda Azure CLI med Azure Storage][storage-azure-cli]
* Valfritt Skapa en hanterad disk avbildning från den virtuella hård disken med *AZ avbildning Create* -se https://docs.microsoft.com/cli/azure/image
* Skapa en ny virtuell dator som anger den överförda virtuella hård disken eller hanterade disk avbildningen som en OS-disk med *AZ VM Create* och parameter *--image*

**Mall**

* Använd *Sysprep* på Windows eller *waagent-deetablering* i Linux för att generalisera din virtuella dator – se [Sysprep teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en virtuell Linux-dator som ska användas som en Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Ladda upp den virtuella hård disken med PowerShell eller Azure CLI
* Valfritt Skapa en hanterad disk avbildning från den virtuella hård disken med PowerShell, Azure CLI eller Azure Portal
* Distribuera den virtuella datorn med en JSON-mall som refererar till avbildningens virtuella hård disk som visas i [den här exempel-JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller med hjälp av den hanterade disk avbildningen som du ser i [det här exemplet](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Laddar ned VHD: er eller Managed Disks till lokalt
Azure-infrastruktur som en tjänst är inte en enkelriktad gata som bara kan ladda upp virtuella hård diskar och SAP-system. Du kan också flytta SAP-system från Azure tillbaka till den lokala världen.

Under hämtningen kan de virtuella hård diskarna eller Managed Disks inte vara aktiva. Även när du laddar ned diskar, som är monterade på virtuella datorer, måste den virtuella datorn stängas av och frigöras. Om du bara vill hämta det databas innehåll som ska användas för att konfigurera ett nytt system lokalt och om det är acceptabelt under hämtningen och installationen av det nya systemet som systemet i Azure fortfarande kan fungera kan du undvika en lång stillestånds tid genom att utföra en komprimerad säkerhets kopiering av databasen till en disk och bara ladda ned den disken i stället för att även hämta den virtuella datorn för OS-basen.

#### <a name="powershell"></a>PowerShell

* Laddar ned en hanterad disk  
  Du måste först få åtkomst till den underliggande blobben för den hanterade disken. Sedan kan du kopiera den underliggande blobben till ett nytt lagrings konto och ladda ned blobben från det här lagrings kontot.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Hämta en virtuell hård disk  
  När SAP-systemet har stoppats och den virtuella datorn stängs av kan du använda PowerShell-cmdleten Save-AzVhd på det lokala målet för att ladda ned VHD-diskarna tillbaka till den lokala världen. För att göra det behöver du webb adressen till den virtuella hård disken, som du hittar i "lagrings avsnittet" i Azure Portal (du måste navigera till lagrings kontot och lagrings behållaren där den virtuella hård disken skapades) och du måste veta var den virtuella hård disken ska kopieras.

  Sedan kan du använda kommandot genom att definiera parametern SourceUri som URL för den virtuella hård disk som ska laddas ned och LocalFilePath som den fysiska platsen för den virtuella hård disken (inklusive dess namn). Kommandot kan se ut så här:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Mer information om cmdleten Save-AzVhd finns här <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>Azure CLI
* Laddar ned en hanterad disk  
  Du måste först få åtkomst till den underliggande blobben för den hanterade disken. Sedan kan du kopiera den underliggande blobben till ett nytt lagrings konto och ladda ned blobben från det här lagrings kontot.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Hämta en virtuell hård disk   
  När SAP-systemet har stoppats och den virtuella datorn stängs av kan du använda Azure CLI-kommandot _Azure Storage BLOB-hämtning_ på det lokala målet för att ladda ned VHD-diskarna tillbaka till den lokala världen. För att göra det måste du ha namnet och den virtuella hård diskens behållare, som du hittar i "lagrings avsnittet" i Azure Portal (måste gå till lagrings kontot och lagrings behållaren där den virtuella hård disken skapades) och du måste veta var den virtuella hård disken ska vara COP IED till.

  Sedan kan du använda kommandot genom att definiera parameter-blob och behållare för den virtuella hård disken som ska laddas ned och målet som den fysiska mål platsen för den virtuella hård disken (inklusive dess namn). Kommandot kan se ut så här:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Överföra virtuella datorer och diskar i Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopiera SAP-system i Azure

Ett SAP-system eller en dedikerad DBMS-server som har stöd för ett SAP-program skikt kommer sannolikt att bestå av flera diskar, som innehåller antingen operativ systemet med binärfilerna eller data-och loggfilerna för SAP-databasen. Varken Azure-funktionen för att kopiera diskar eller Azure-funktionerna för att spara diskar på en lokal disk har en mekanism för synkronisering, som ögonblicks bilder av flera diskar på ett konsekvent sätt. Därför skulle statusen för de kopierade eller sparade diskarna även om de är monterade mot samma virtuella dator vara annorlunda. Det innebär att i det konkreta fallet att ha olika data och loggfiler som finns i de olika diskarna skulle databasen i slutet vara inkonsekvent.

**Slutsats: för att kopiera eller spara diskar som ingår i en SAP-systemkonfiguration måste du stoppa SAP-systemet och även behöva stänga av den distribuerade virtuella datorn. Du kan bara kopiera eller ladda ned disk uppsättningen för att antingen skapa en kopia av SAP-systemet i Azure eller lokalt.**

Data diskar kan lagras som VHD-filer i ett Azure Storage konto och kan kopplas direkt till en virtuell dator eller användas som en avbildning. I det här fallet kopieras den virtuella hård disken till en annan plats innan den kopplas till den virtuella datorn. Det fullständiga namnet på VHD-filen i Azure måste vara unikt inom Azure. Som tidigare nämnts är namnet en typ av namn på tre delar som ser ut så här:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Det går också att Managed Disks data diskar. I det här fallet används den hanterade disken för att skapa en ny hanterad disk innan den kopplas till den virtuella datorn. Namnet på den hanterade disken måste vara unikt inom en resurs grupp.

##### <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell cmdlets för att kopiera en virtuell hård disk som visas i [den här artikeln][storage-powershell-guide-full-copy-vhd]. Om du vill skapa en ny hanterad disk använder du New-AzDiskConfig och New-AzDisk som visas i följande exempel.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Du kan använda Azure CLI för att kopiera en virtuell hård disk som visas i [den här artikeln][storage-azure-cli-copy-blobs]. Om du vill skapa en ny hanterad disk använder du *AZ disk Create* som visas i följande exempel.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage verktyg

* <https://storageexplorer.com/>

Professional-versioner av Azure Storage Explorer hittar du här:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Kopieringen av en virtuell hård disk i ett lagrings konto är en process som bara tar några sekunder (liknande SAN-maskinvara som skapar ögonblicks bilder med Lazy Copy och kopiering vid skrivning). När du har en kopia av VHD-filen kan du koppla den till en virtuell dator eller använda den som en avbildning för att koppla kopior av den virtuella hård disken till virtuella datorer.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure CLI

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopiera diskar mellan Azure Storage-konton
Det går inte att utföra den här uppgiften på Azure Portal. Du kan använda Azure PowerShell-cmdletar, Azure CLI eller en lagrings webbläsare från tredje part. PowerShell-cmdletarna eller CLI-kommandona kan skapa och hantera blobbar, bland annat möjligheten att asynkront kopiera blobbar mellan lagrings konton och mellan regioner i Azure-prenumerationen.

##### <a name="powershell"></a>PowerShell
Du kan också kopiera virtuella hård diskar mellan prenumerationer. Mer information finns i [den här artikeln][storage-powershell-guide-full-copy-vhd].

Basic-flödet för PS-cmdlet-logiken ser ut så här:

* Skapa en lagrings konto kontext för **käll** lagrings kontot med *New-AzStorageContext* -se <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Skapa en lagrings konto kontext för **mål** lagrings kontot med *New-AzStorageContext* -se <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Starta kopian med

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Kontrol lera status för kopian i en slinga med

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Koppla den nya virtuella hård disken till en virtuell dator enligt beskrivningen ovan.

Exempel finns i [den här artikeln][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure CLI
* Starta kopian med

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Kontrol lera status om kopian fortfarande finns i en slinga med

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Koppla den nya virtuella hård disken till en virtuell dator enligt beskrivningen ovan.

Exempel finns i [den här artikeln][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Disk hantering

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM/disk struktur för SAP-distributioner

Vi rekommenderar att hanteringen av strukturen för en virtuell dator och de tillhör ande diskarna är enkla. I lokala installationer har kunder utvecklat många olika sätt att strukturera en server installation.

* En bas disk som innehåller operativ systemet och alla binärfiler för DBMS och/eller SAP. Sedan mars 2015 kan disken vara upp till 1 TB i stället för tidigare begränsningar som är begränsad till 127GB.
* En eller flera diskar som innehåller DBMS-loggfilen för SAP-databasen och logg filen för det DBMS-baserade lagrings utrymmet (om DBMS stöder detta). Om kraven för databas logg IOPS är höga måste du Stripa flera diskar för att uppnå den IOPS-volym som krävs.
* Ett antal diskar som innehåller en eller två databasfiler för SAP-databasen och data DBMS Temp-datafilerna också (om DBMS stöder detta).

![Referens konfiguration för Azure IaaS VM för SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Med många kunder såg vi konfigurationer där till exempel SAP-och DBMS-binärfiler inte har installerats på c:\ enhet där operativ systemet installerades. Det fanns olika orsaker till detta, men när vi gick tillbaka till roten var det vanligt vis att enheterna var små och operativ system uppgraderingar behövde ytterligare utrymme 10-15 år sedan. Båda villkoren gäller inte för ofta längre. I dag är c:\ enheten kan mappas på stora volym diskar eller virtuella datorer. För att hålla distributionerna enkla i deras struktur rekommenderar vi att du följer följande distributions mönster för SAP NetWeaver-system i Azure
>
> Växlings filen för Windows-operativsystemet bör vara på D: enhet (icke-beständig disk)
>
> ![Linux][Logo_Linux] Linux
>
> Placera Linux-swapfile under/mnt/mnt/Resource på Linux enligt beskrivningen i [den här artikeln][virtual-machines-linux-agent-user-guide]. Växlings filen kan konfigureras i konfigurations filen för Linux-agentens/etc/waagent.conf. Lägg till eller ändra följande inställningar:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Om du vill aktivera ändringarna måste du starta om Linux-agenten med

```
sudo service waagent restart
```

Läs SAP NOTE [1597355] för mer information om den rekommenderade växlings filens storlek

---
Antalet diskar som används för DBMS-datafilerna och vilken typ av Azure Storage diskarna är värd för, bör fastställas av IOPS-kraven och den latens som krävs. Exakta kvoter beskrivs i [den här artikeln (Linux)][virtual-machines-sizes-linux] och [den här artikeln (Windows)][virtual-machines-sizes-windows].

Erfarenhet av SAP-distributioner under de senaste två åren är några lektioner, som kan sammanfattas som:

* IOPS-trafik till olika datafiler är inte alltid samma eftersom befintliga kund system kan ha olika storleks data filer som representerar sina SAP-databaser. Detta innebär att det är bättre att använda en RAID-konfiguration över flera diskar för att placera datafilerna som LUN-hämtas från dem. Det finns situationer, särskilt med Azure standard Storage där en IOPS-taxa når kvoten för en enskild disk mot DBMS-transaktionshanteraren. I sådana scenarier rekommenderar vi att du använder Premium Storage, eller att du kan aggregera flera standard lagrings diskar med en programs rand.

---
> ![Windows][Logo_Windows] Windows
>
> * [Prestandametodtips för SQL Server i Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurera programvaru-RAID på Linux][virtual-machines-linux-configure-raid]
> * [Konfigurera LVM på en virtuell Linux-dator i Azure][virtual-machines-linux-configure-lvm]
> * [Azure Storage hemligheter och I/O-optimeringar I Linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Premium Storage visar betydande bättre prestanda, särskilt för viktiga skrivningar i transaktions loggen. För SAP-scenarier som förväntas leverera produktion som prestanda rekommenderar vi starkt att du använder VM-serien som kan utnyttja Azure Premium Storage.

Tänk på att disken som innehåller operativ systemet, och som vi rekommenderar, binärfilerna för SAP och databasen (bas-VM) också inte längre är begränsad till 127GB. Det kan nu ha upp till 1 TB storlek. Detta bör vara tillräckligt med utrymme för att behålla all nödvändig fil, inklusive t. ex. SAP batch job-loggar.

Mer information och mer information, specifikt för virtuella DBMS-datorer, finns i [distributions guiden för DBMS][dbms-guide]

#### <a name="disk-handling"></a>Disk hantering

I de flesta fall måste du skapa ytterligare diskar för att kunna distribuera SAP-databasen till den virtuella datorn. Vi har talat om överväganden om antalet diskar i kapitlet om [VM/disk Structure för SAP-distributioner][planning-guide-5.5.1] av det här dokumentet. Med Azure Portal kan du ansluta och koppla från diskar när en virtuell bas dator har distribuerats. Diskarna kan kopplas/kopplas från när den virtuella datorn är igång samt när den har stoppats. När du kopplar en disk, Azure Portal erbjuda en tom disk eller en befintlig disk, vilket vid tidpunkten inte är kopplat till en annan virtuell dator.

**Obs**: diskar kan bara kopplas till en virtuell dator vid en bestämd tidpunkt.

![Ansluta/koppla från diskar med Azure standard Storage][planning-guide-figure-1400]

Under distributionen av en ny virtuell dator kan du välja om du vill använda Managed Disks eller placera diskarna på Azure Storage-konton. Om du vill använda Premium Storage rekommenderar vi att du använder Managed Disks.

Därefter måste du bestämma om du vill skapa en ny och en tom disk eller om du vill välja en befintlig disk som överfördes tidigare och som ska kopplas till den virtuella datorn nu.

**Viktigt**: du vill **inte** använda cachelagring av värdar med Azure standard Storage. Du bör lämna inställningen för värd-cachen till standardvärdet NONE. Med Azure Premium Storage bör du aktivera cachelagring av filer om I/O-variabeln huvudsakligen läser som typisk I/O-trafik mot datafiler I databasen. I händelse av databas transaktions logg filen rekommenderas ingen cachelagring.

---
> ![Windows][Logo_Windows] Windows
>
> [Så här ansluter du en datadisk i Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Om diskarna är anslutna måste du logga in på den virtuella datorn för att öppna Windows Disk Manager. Om automount inte är aktiverat enligt rekommendationer i kapitel [inställningen automontera för anslutna diskar][planning-guide-5.5.3], måste den nyligen anslutna volymen tas online och initieras.
>
> ![Linux][Logo_Linux] Linux
>
> Om diskarna är anslutna måste du logga in på den virtuella datorn och initiera diskarna enligt beskrivningen i [den här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Om den nya disken är en tom disk, måste du även formatera disken. För formatering, särskilt för DBMS-data och loggfiler, samma rekommendationer som för distribution utan operativ system i DBMS tillämpas.

Som redan nämnts i kapitlet [Microsoft Azure begreppet virtuell dator][planning-guide-3.2]ger ett Azure Storage konto inte några oändliga resurser vad gäller i/O-volym, IOPS och data volym. Virtuella DBMS-datorer påverkas vanligt vis av detta. Det kan vara bäst att använda ett separat lagrings konto för varje virtuell dator om du har några virtuella datorer i/O-volymer som ska distribueras för att ligga inom gränsen för Azure Storage kontots volym. Annars måste du se hur du kan balansera de virtuella datorerna mellan olika lagrings konton utan att använda gränsen för varje enskilt lagrings konto. Mer information beskrivs i [distributions guiden för DBMS][dbms-guide]. Du bör också ha de här begränsningarna i åtanke för virtuella datorer i SAP-programservern eller andra virtuella datorer, vilket kan kräva ytterligare virtuella hård diskar. Dessa begränsningar gäller inte om du använder hanterad disk. Om du planerar att använda Premium Storage rekommenderar vi att du använder hanterad disk.

Ett annat ämne, som är relevant för lagrings konton är om de virtuella hård diskarna i ett lagrings konto får geo-replikeras. Geo-replikering är aktiverat eller inaktiverat på lagrings konto nivån och inte på VM-nivån. Om geo-replikering är aktiverat replikeras de virtuella hård diskarna i lagrings kontot till ett annat Azure-datacenter inom samma region. Innan du bestämmer detta bör du tänka på följande begränsning:

Azure geo-replikering fungerar lokalt på varje virtuell hård disk i en virtuell dator och replikerar inte IOs i kronologisk ordning över flera virtuella hård diskar i en virtuell dator. Därför replikeras den virtuella hård disken som representerar den virtuella bas datorn samt eventuella ytterligare virtuella hård diskar som är anslutna till den virtuella datorn oberoende av varandra. Det innebär att det inte finns någon synkronisering mellan ändringarna i de olika virtuella hård diskarna. Det faktum att IOs replikeras oberoende av i vilken ordning de skrivs, innebär att geo-replikering inte är ett värde för databas servrar som har sina databaser distribuerade över flera virtuella hård diskar. Förutom DBMS kan det också finnas andra program där processer skriver eller manipulerar data i olika virtuella hård diskar och där det är viktigt att behålla ändrings ordningen. Om det är ett krav bör geo-replikering i Azure inte aktive ras. Beroende på om du behöver eller vill använda geo-replikering för en uppsättning virtuella datorer, men inte för en annan uppsättning, kan du redan kategorisera virtuella datorer och deras relaterade virtuella hård diskar till olika lagrings konton som har geo-replikering aktiverat eller inaktiverat.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ställer in automontering för anslutna diskar
---
> ![Windows][Logo_Windows] Windows
>
> För virtuella datorer, som skapas från egna avbildningar eller diskar, är det nödvändigt att kontrol lera och eventuellt ange parametern för automontering. Genom att ange den här parametern kan den virtuella datorn efter en omstart eller omdistribution i Azure montera de anslutna/monterade enheterna igen automatiskt.
> Parametern anges för de avbildningar som tillhandahålls av Microsoft på Azure Marketplace.
>
> Information om hur du ställer in den här monterings kontrollen finns i dokumentationen för den körbara kommando rads programmet DiskPart. exe här:
>
> * [Kommando rads alternativ för DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](https://technet.microsoft.com/library/cc753703.aspx)
>
> Windows kommando rads fönster bör öppnas som administratör.
>
> Om diskarna är anslutna måste du logga in på den virtuella datorn för att öppna Windows Disk Manager. Om automount inte är aktiverat enligt rekommendationer i kapitel [inställningen automontera för anslutna diskar][planning-guide-5.5.3], måste den nyligen anslutna volymen > tas online och initieras.
>
> ![Linux][Logo_Linux] Linux
>
> Du måste initiera en nyligen ansluten tom disk enligt beskrivningen i [den här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Du måste också lägga till nya diskar i/etc/fstab.
>
>

---
### <a name="final-deployment"></a>Slutlig distribution

För den slutliga distributionen och de exakta stegen, särskilt vad gäller distributionen av Azure-tillägget för SAP, se [distributions guiden][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Åtkomst till SAP-system som körs i virtuella Azure-datorer

Följande procedurer måste tillämpas för scenarier där du vill ansluta till dessa SAP-system via det offentliga Internet som använder SAP-gränssnittet.

Senare i dokumentet diskuterar vi det andra större scenariot, ansluter till SAP-system i distributioner mellan platser, som har en plats-till-plats-anslutning (VPN-tunnel) eller en Azure ExpressRoute-anslutning mellan de lokala systemen och Azure-systemen.

### <a name="remote-access-to-sap-systems"></a>Fjärråtkomst till SAP-system

Med Azure Resource Manager finns det inte längre några standard slut punkter som i den tidigare klassiska modellen. Alla portar i en Azure Resource Manager VM är öppna så länge:

1. Ingen nätverks säkerhets grupp har definierats för under nätet eller nätverks gränssnittet. Nätverks trafik till virtuella Azure-datorer kan skyddas via så kallade "nätverks säkerhets grupper". Mer information finns i [Vad är en nätverkssäkerhetsgrupp?][virtual-networks-nsg]
2. Ingen Azure Load Balancer har definierats för nätverks gränssnittet   

Se skillnaden mellan den klassiska modellen och ARM-arkitekturen enligt beskrivningen i [den här artikeln][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguration av SAP-systemet och SAP GUI-anslutning via Internet

I den här artikeln beskrivs information i det här avsnittet: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Ändra brand Väggs inställningar i VM

Det kan vara nödvändigt att konfigurera brand väggen på dina virtuella datorer för att tillåta inkommande trafik till SAP-systemet.

---
> ![Windows][Logo_Windows] Windows
>
> Som standard är Windows-brandväggen i en distribuerad Azure-dator aktive rad. Du måste nu tillåta SAP-porten att öppnas, annars kan inte SAP-ANVÄNDARGRÄNSSNITTET ansluta.
> Gör så här:
>
> * Öppna kontroll Kontrollpanelen\system och säkerhet-brandväggen till **Avancerade inställningar**.
> * Högerklicka på regler för inkommande trafik och välj **ny regel**.
> * I följande guide väljer du att skapa en ny **port** regel.
> * I nästa steg i guiden lämnar du inställningen vid TCP och skriver in det port nummer som du vill öppna. Eftersom vårt SAP-instans-ID är 00 tog vi 3200. Om instansen har ett annat instans nummer, ska porten du definierade tidigare utifrån instans numret öppnas.
> * I nästa del av guiden måste du lämna objektet **Tillåt anslutning** markerat.
> * I nästa steg i guiden måste du definiera om regeln gäller för domän, privat och offentligt nätverk. Justera det om det behövs för dina behov. Men om du ansluter med SAP-GUI från utsidan via det offentliga nätverket måste du ha regeln tillämpad på det offentliga nätverket.
> * I det sista steget i guiden namnger du regeln och sparar genom att trycka på **Slutför**.
>
> Regeln börjar gälla omedelbart.
>
> ![Definition av port regel][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Linux-avbildningarna i Azure Marketplace aktiverar inte program varan iptables-brandväggen som standard och anslutningen till ditt SAP-system bör fungera. Om du har aktiverat program varan iptables eller en annan brand vägg, se dokumentationen för program varan iptables eller den använda brand väggen för att tillåta inkommande TCP-trafik till port 32xx (där xx är system numret för ditt SAP-system).
>
>

---
#### <a name="security-recommendations"></a>Säkerhetsrekommendationer

Det grafiska användar gränssnittet för SAP ansluter inte direkt till någon av SAP-instanserna (port 32xx) som körs, men ansluter först via porten som öppnas till SAP Message Server-processen (port 36xx). Tidigare användes samma port av meddelande servern för intern kommunikation till program instanserna. För att förhindra att lokala program servrar kommunicerar med en meddelande server i Azure kan de interna kommunikations portarna ändras. Vi rekommenderar starkt att du ändrar den interna kommunikationen mellan SAP-meddelande servern och dess program instanser till ett annat port nummer på system som har klonas från lokala system, till exempel en klon av utveckling för projekt testning osv. Detta kan göras med standard profil parametern:

> rdisp/msserv_internal
>
>

som dokumenteras i [säkerhets inställningar för SAP Message Server](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Enkel virtuell dator med SAP NetWeaver demo/utbildnings scenario

![Att köra en enda virtuell dator SAP demo system med samma VM-namn, isolerade i Azure Cloud Services][planning-guide-figure-1700]

I det här scenariot implementerar vi ett typiskt scenario för utbildning/demonstrations system där det fullständiga scenariot för utbildning/demonstration finns i en enda virtuell dator. Vi antar att distributionen görs via mallar för VM-avbildningar. Vi förutsätter också att flera av de virtuella datorerna för demo/utbildning måste distribueras med de virtuella datorerna med samma namn. Hela utbildnings systemen har ingen anslutning till dina lokala resurser och är en motsats till en hybrid distribution.

Antagandet är att du har skapat en avbildning av en virtuell dator som beskrivs i vissa avsnitt av kapitlet att [förbereda virtuella datorer med SAP för Azure][planning-guide-5.2] i det här dokumentet.

Händelse ordningen för att implementera scenariot ser ut så här:

##### <a name="powershell"></a>PowerShell

* Skapa en ny resurs grupp för varje utbildning/demo liggande

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Skapa ett nytt lagrings konto om du inte vill använda Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Skapa ett nytt virtuellt nätverk för varje utbildning/demo landskap för att aktivera användningen av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en nätverks säkerhets grupp som endast tillåter trafik till port 3389 för att aktivera åtkomst till fjärr skrivbord och port 22 för SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Skapa en ny offentlig IP-adress som kan användas för att få åtkomst till den virtuella datorn från Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Skapa ett nytt nätverks gränssnitt för den virtuella datorn

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Skapa en virtuell dator. I det här scenariot har varje virtuell dator samma namn. SAP SID för SAP NetWeaver-instanserna i de virtuella datorerna är också samma. I Azure-resurs gruppen måste namnet på den virtuella datorn vara unikt, men i olika Azure-resurs grupper kan du köra virtuella datorer med samma namn. Standard kontot administratör för Windows eller root för Linux är inte giltiga. Ett nytt administratörs användar namn måste därför definieras tillsammans med ett lösen ord. Storleken på den virtuella datorn måste också definieras.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Du kan också lägga till ytterligare diskar och återställa nödvändigt innehåll. Alla BLOB-namn (URL: er till Blobbarna) måste vara unika i Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

Följande exempel kod kan användas i Linux. För Windows kan du antingen använda PowerShell som det beskrivs ovan eller anpassa exemplet för att använda% rgName% i stället för $rgName och ange miljövariabeln med hjälp av Windows-kommando *uppsättningen*.

* Skapa en ny resurs grupp för varje utbildning/demo liggande

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Skapa ett nytt lagringskonto

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Skapa ett nytt virtuellt nätverk för varje utbildning/demo landskap för att aktivera användningen av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en nätverks säkerhets grupp som endast tillåter trafik till port 3389 för att aktivera åtkomst till fjärr skrivbord och port 22 för SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Skapa en ny offentlig IP-adress som kan användas för att få åtkomst till den virtuella datorn från Internet

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Skapa ett nytt nätverks gränssnitt för den virtuella datorn

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Skapa en virtuell dator. I det här scenariot har varje virtuell dator samma namn. SAP SID för SAP NetWeaver-instanserna i de virtuella datorerna är också samma. I Azure-resurs gruppen måste namnet på den virtuella datorn vara unikt, men i olika Azure-resurs grupper kan du köra virtuella datorer med samma namn. Standard kontot administratör för Windows eller root för Linux är inte giltiga. Ett nytt administratörs användar namn måste därför definieras tillsammans med ett lösen ord. Storleken på den virtuella datorn måste också definieras.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Du kan också lägga till ytterligare diskar och återställa nödvändigt innehåll. Alla BLOB-namn (URL: er till Blobbarna) måste vara unika i Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Mall

Du kan använda exemplen på lagrings platsen för Azure-snabb start-mallar på GitHub.

* [Enkel virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Enkel virtuell Windows-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuell dator från avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementera en uppsättning virtuella datorer som kommunicerar i Azure

Detta icke-hybrid scenario är ett typiskt scenario för utbildnings-och demo syfte där program varan som representerar demo-/utbildnings scenariot sprids över flera virtuella datorer. De olika komponenterna som installeras på de olika virtuella datorerna måste kommunicera med varandra. I det här scenariot behövs det ingen lokal nätverkskommunikation eller ett scenario mellan platser.

Det här scenariot är en utökning av den installation som beskrivs i kapitel [Single VM with demo/Training scenario för NetWeaver][planning-guide-7.1] i det här dokumentet. I det här fallet kommer fler virtuella datorer att läggas till i en befintlig resurs grupp. I följande exempel består utbildningen av en virtuell SAP ASCS/SCS-dator, en virtuell dator som kör ett DBMS och en virtuell SAP Application Server-instans.

Innan du skapar det här scenariot måste du tänka på grundläggande inställningar som redan har utnyttjats i scenariot innan.

#### <a name="resource-group-and-virtual-machine-naming"></a>Resurs grupp och namngivning av virtuella datorer

Alla resurs grupp namn måste vara unika. Utveckla ditt eget namngivnings schema för dina resurser, till exempel `<rg-name`> suffix.

Det virtuella dator namnet måste vara unikt inom resurs gruppen.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurera nätverk för kommunikation mellan olika virtuella datorer

![Uppsättning virtuella datorer i ett Azure-Virtual Network][planning-guide-figure-1900]

För att förhindra att namnge kollisioner med kloner av samma utbildning/demonstration-landskap måste du skapa ett Azure-Virtual Network för varje landskap. DNS-namnmatchning kommer att tillhandahållas av Azure eller så kan du konfigurera din egen DNS-server utanför Azure (ska inte diskuteras här). I det här scenariot konfigurerar vi inte vår egen DNS. För alla virtuella datorer i en Azure-Virtual Network aktive ras kommunikation via värdnamn.

Anledningen till att separata utbildnings-eller demonstrations landskap av virtuella nätverk och inte bara resurs grupper kan vara:

* SAP landskap som kon figurer ATS kräver sin egen AD/OpenLDAP och en domän Server måste vara en del av varje landskap.  
* SAP-landskapet som kon figurer ATS har komponenter som måste fungera med fasta IP-adresser.

Mer information om virtuella Azure-nätverk och hur du definierar dem finns i [den här artikeln][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Distribuera virtuella SAP-datorer med företags nätverks anslutning (mellan platser)

Du kör ett SAP-landskap och vill dela upp distributionen mellan Bare-Metal för avancerade DBMS-servrar, lokala virtualiserade miljöer för program lager och mindre 2-Tier konfigurerade SAP-system och Azure-IaaS. Det grundläggande antagandet är att SAP-system i ett SAP-landskap måste kommunicera med varandra och med många andra program varu komponenter som distribueras i företaget, oberoende av deras distributions formulär. Det bör inte heller finnas några skillnader i distributions formuläret för slutanvändaren som ansluter till SAP-ANVÄNDARGRÄNSSNITTET eller andra gränssnitt. Dessa villkor kan bara uppfyllas när vi har de lokala Active Directory-och OpenLDAP-och DNS-tjänsterna utökade till Azure-systemen via plats-till-plats/anslutning till flera platser eller privata anslutningar som Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenario för ett SAP-landskap

Det kors-eller hybrid scenariot kan vara grovt beskrivet som i bilden nedan:

![Plats-till-plats-anslutning mellan lokala och Azure-tillgångar][planning-guide-figure-2100]

Minimi kravet är att använda säkra kommunikations protokoll som SSL/TLS för webb läsar åtkomst eller VPN-baserade anslutningar för system åtkomst till Azure-tjänsterna. Antagandet är att företag hanterar VPN-anslutningen mellan företagets nätverk och Azure på olika sätt. Vissa företag kan öppna alla portar i ett tomt fall. Vissa andra företag kanske vill vara precis på vilka portar de behöver öppna osv.

I tabellen nedan visas typiska SAP-kommunikations portar. I princip räcker det att öppna SAP Gateway-porten.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Tjänst | Portnamn | Exempel `<nn`> = 01 | Standard intervall (min-max) | Kommentar |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` se * |3201 |3200 - 3299 |SAP dispatcher, som används av SAP GUI för Windows och Java |
| Meddelande Server |sapms`<sid`> Se * * |3600 |gratis sapms`<anySID`> |sid = SAP-system-ID |
| Gateway |sapgw`<nn`> Se * |3301 |free |SAP-Gateway, som används för CPIC-och RFC-kommunikation |
| SAP-router |sapdp99 |3299 |free |Endast CI (Central instans) tjänst namn kan omtilldelas i/etc/Services till ett godtyckligt värde efter installationen. |

*) NN = SAP instance Number

\* *) sid = SAP-system-ID

Mer detaljerad information om portar som krävs för olika SAP-produkter och-tjänster av SAP-produkter finns här <https://scn.sap.com/docs/DOC-17124>.
Med det här dokumentet bör du kunna öppna särskilda portar i VPN-enheten som krävs för särskilda SAP-produkter och-scenarier.

Andra säkerhets åtgärder när du distribuerar virtuella datorer i ett sådant scenario kan vara att skapa en [nätverks säkerhets grupp][virtual-networks-nsg] för att definiera åtkomst regler.

### <a name="dealing-with-different-virtual-machine-series"></a>Hantera olika serier för virtuella datorer

Microsoft har lagt till många fler typer av virtuella datorer som skiljer sig åt antingen i antal virtuella processorer, minne eller viktigare på maskin vara som den körs på. Alla de virtuella datorerna stöds inte med SAP (se VM-typer som stöds i SAP NOTE [1928533]). Några av de virtuella datorerna körs på olika värdars maskin varu generationer. Dessa värden för maskin varu generationer distribueras i kornig het i en Azure-Scale-enhet. Det kan uppstå fall där de olika VM-typerna du väljer inte kan köras på samma Scale-Unit. En tillgänglighets uppsättning är begränsad i möjligheten att täcka skalnings enheter baserat på olika maskin vara.  Om du till exempel kör SAP-DBMS-skiktet på en E64s_v3, en virtuell dator som finns i en tillgänglighets uppsättning tillsammans med den virtuella datorn som kör den sekundära DBMS-instansen i en HA-konfiguration, kan du inte bara stoppa och starta om den sekundära virtuella datorn som virtuell dator i M-serien, eftersom du kanske vill pgrade den virtuella datorn. Orsak är att virtuella datorer i M-serien och virtuella datorer i Ev3-serien körs på olika maskin vara och med det i olika skalnings enheter. Du måste skapa en ny tillgänglighets uppsättning, ta bort den sekundära virtuella datorn Ev3, utan att ta bort lagringen, och sedan distribuera om den virtuella datorn som M-seriens virtuella dator till den nya tillgänglighets uppsättningen.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Skriva ut på en lokal nätverks skrivare från SAP-instansen i Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Skriva ut över TCP/IP i ett scenario med flera platser

Konfiguration av lokala TCP/IP-baserade nätverks skrivare i en virtuell Azure-dator är samma som i företagets nätverk, förutsatt att du har en VPN-anslutning för plats-till-plats-tunnel eller ExpressRoute-anslutning upprättad.

---
> ![Windows][Logo_Windows] Windows
>
> Gör så här:
>
> * Vissa nätverks skrivare levereras med en konfigurations guide som gör det enkelt att konfigurera skrivaren i en virtuell Azure-dator. Om ingen guide program vara har distribuerats med skrivaren är det manuella sättet att konfigurera skrivaren att skapa en ny TCP/IP-skrivarport.
> * Öppna kontroll panelen – > enheter och skrivare – > lägga till en skrivare
> * Välj Lägg till en skrivare med en TCP/IP-adress eller ett värdnamn
> * Ange skrivarens IP-adress
> * Skrivar port standard 9100
> * Installera lämplig skrivar driv rutin manuellt om det behövs.
>
> ![Linux][Logo_Linux] Linux
>
> * precis som för Windows följer du standard proceduren för att installera en nätverks skrivare
> * Följ bara de offentliga Linux-guiderna för [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) eller [Red Hat och Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) om hur du lägger till en skrivare.
>
>

---
![Nätverks utskrift][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Värdbaserad skrivare över SMB (delad skrivare) i ett scenario med flera platser

Värdbaserade skrivare är inte nätverks kompatibla genom design. Men en värdbaserad skrivare kan delas mellan datorer i ett nätverk förutsatt att skrivaren är ansluten till en dator som är påslagen. Anslut företagets nätverk till antingen plats-till-plats eller ExpressRoute och dela din lokala skrivare. SMB-protokollet använder NetBIOS i stället för DNS as Name Service. NetBIOS-värdnamnet kan vara ett annat än DNS-värdnamnet. Standard fallet är att NetBIOS-värdnamnet och DNS-värdnamnet är identiska. DNS-domänen är inte begriplig i NetBIOS-namnområdet. Detta är att det fullständigt kvalificerade DNS-värdnamnet som består av DNS-värdnamnet och DNS-domänen inte får användas i NetBIOS-namn utrymmet.

Skrivar resursen identifieras med ett unikt namn i nätverket:

* Värd namnet för SMB-värden (alltid nödvändigt).
* Namnet på resursen (alltid nödvändigt).
* Namnet på domänen om skrivar resursen inte finns i samma domän som SAP-systemet.
* Dessutom kan ett användar namn och ett lösen ord krävas för att få åtkomst till skrivar resursen.

Anvisningar:

---
> ![Windows][Logo_Windows] Windows
>
> Dela din lokala skrivare.
> I den virtuella Azure-datorn öppnar du Utforskaren och skriver in skrivarens resurs namn.
> En installations guide för skrivare vägleder dig genom installations processen.
>
> ![Linux][Logo_Linux] Linux
>
> Här följer några exempel på dokumentation om hur du konfigurerar nätverks skrivare i Linux eller inkluderar ett kapitel angående utskrift i Linux. Den fungerar på samma sätt i en virtuell Azure Linux-dator så länge den virtuella datorn ingår i ett VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL eller Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-skrivare (vidarebefordring av skrivare)

I Azure är möjligheten för Fjärrskrivbordstjänster att ge användarna åtkomst till sina lokala skrivar enheter i en fjärrsession inte tillgänglig.

---
> ![Windows][Logo_Windows] Windows
>
> Mer information om hur du skriver ut med Windows finns här: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrering av SAP Azure-system i korrigerings-och transport system (TMS) i olika platser

TMS (SAP Change and transport system) måste konfigureras för att exportera och importera transport begär Anden mellan system i liggande format. Vi förutsätter att utvecklings instanserna för ett SAP-system (DEV) finns i Azure, medan kvalitets säkrings systemet (frågor och svar) och produktiva system (PRD) är lokalt. Dessutom förutsätter vi att det finns en central överförings katalog.

##### <a name="configuring-the-transport-domain"></a>Konfigurera transport domänen

Konfigurera transport domänen på det system som du angav som transport domän kontrol Lanterna enligt beskrivningen i [Konfigurera transport](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)domänkontrollanten. En systemuser-TMSADM skapas och den obligatoriska RFC-destinationen skapas. Du kan kontrol lera dessa RFC-anslutningar med transaktions SM59. Hostname-matchning måste vara aktiverat i din transport domän.

Anvisningar:

* I vårt scenario bestämde vi att det lokala QAS-systemet är den CTS-domänkontrollanten. Anropa transaktion STMS. Dialog rutan TMS visas. Dialog rutan konfigurera transport domän visas. (Den här dialog rutan visas bara om du ännu inte har konfigurerat en transport domän.)
* Se till att den automatiskt skapade användaren TMSADM är auktoriserad (SM59-> ABAP Connection-> TMSADM@E61.DOMAIN_E61-> information – > Utilities (M) – > Authorization test). Den första skärmen i transaktions STMS bör visa att SAP-systemet nu fungerar som styrenhet för transport domänen som visas här:

![Inledande skärm för transaktions STMS på domänkontrollanten][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inklusive SAP-system i transport domänen

Sekvensen inklusive ett SAP-system i en transport domän ser ut så här:

* I DEV-systemet i Azure går du till transport systemet (Client 000) och anropar Transaction STMS. Välj annan konfiguration i dialog rutan och fortsätt med inkludera system i domän. Ange domänkontrollanten som mål värd ([inklusive SAP-system i transport domänen](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systemet väntar nu på att tas med i transport domänen.
* Av säkerhets skäl måste du gå tillbaka till domänkontrollanten för att bekräfta din begäran. Välj system översikt och godkänn väntar på systemet. Bekräfta sedan frågan och konfigurationen kommer att distribueras.

Detta SAP-system innehåller nu nödvändig information om alla andra SAP-system i transport domänen. På samma tidpunkt skickas adress data för det nya SAP-systemet till alla andra SAP-system, och SAP-systemet anges i transport kontroll programmets transport profil. Kontrol lera om RFC: er och åtkomst till domänens transport katalog fungerar.

Fortsätt med konfigurationen av ditt transport system som vanligt enligt beskrivningen i dokumentations [ändrings-och transport systemet](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Anvisningar:

* Kontrol lera att din STMS lokalt är korrekt konfigurerad.
* Se till att värd namnet för transport domänkontrollanten kan lösas av den virtuella datorn på Azure och vice visum.
* Anropa Transaction STMS-> andra konfigurations-> inkludera system i domän.
* Bekräfta anslutningen i det lokala TMS-systemet.
* Konfigurera transport vägar, grupper och lager som vanligt.

I scenarier med plats-till-plats ansluten mellan olika platser kan svars tiden mellan lokala och Azure fortfarande vara betydande. Om vi följer ordningen för att transportera objekt via utvecklings-och test system till produktion eller om du tänker använda transporter eller support paket på olika system, inser du att, beroende på platsen för den centrala transport katalogen, vissa av systemen kommer att drabbas av hög latens vid läsning eller skrivning av data i den centrala transport katalogen. Situationen liknar konfigurationer i SAP landskap där de olika systemen sprids genom olika data Center med ett stort avstånd mellan data centren.

För att undvika svars tid och låta systemet arbeta snabbt vid läsning eller skrivning till eller från transport katalogen, kan du konfigurera två STMS transport domäner (en för lokal och en med system i Azure och länka transport domänerna. Läs den här dokumentationen, som förklarar principerna bakom det här konceptet i SAP-TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Anvisningar:

* Konfigurera en transport domän på varje plats (lokalt och Azure) med Transaction STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Länka domänerna till en domän länk och bekräfta länken mellan de två domänerna.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuera konfigurationen till det länkade systemet.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-trafik mellan SAP-instanser som finns i Azure och lokalt (mellan platser)

RFC-trafik mellan system, som är lokalt och i Azure, måste fungera. Konfigurera en SM59 för anslutnings anrop i ett käll system där du måste definiera en RFC-anslutning till mål systemet. Konfigurationen liknar standard konfigurationen av en RFC-anslutning.

Vi antar att de virtuella datorerna i det kors lokala scenariot som kör SAP-system som måste kommunicera med varandra finns i samma domän. Konfigurationen av en RFC-anslutning mellan SAP-system skiljer sig därför inte från installations stegen och indatan i lokala scenarier.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Åtkomst till lokala fil resurser från SAP-instanser som finns i Azure eller vice versa

SAP-instanser som finns i Azure behöver komma åt fil resurser som finns i företags lokal. Dessutom behöver lokala SAP-instanser åtkomst till fil resurser som finns i Azure. Om du vill aktivera fil resurserna måste du konfigurera behörigheter och delnings alternativ på det lokala systemet. Se till att öppna portarna på VPN-eller ExpressRoute-anslutningen mellan Azure och ditt data Center.

## <a name="supportability"></a>Support

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-tillägg för SAP

För att kunna mata in viss del av Azures infrastruktur information om verksamhets kritiska SAP-system till SAP-värd agent instanserna som är installerade på virtuella datorer, måste ett Azure-tillägg (VM) för SAP installeras för de distribuerade virtuella datorerna. Eftersom de krav som publicerades av SAP var speciella för SAP-program, beslutade Microsoft inte att generiskt implementera de nödvändiga funktionerna i Azure, men lämna dem för kunderna att distribuera nödvändig VM-tillägg och konfigurationer till Virtual Machines som körs i Azure. Distribution och livs cykel hantering av Azure VM-tillägget för SAP kommer dock att automatiseras av Azure.

#### <a name="solution-design"></a>Lösningsdesign

Lösningen som har utvecklats för att möjliggöra att SAP-värd agent får nödvändig information baseras på arkitekturen i Azure VM agent och tillägg ramverket. Idén med Azure VM-agenten och tillägg ramverket är att tillåta att program vara som är tillgängliga i Azures tillägg för virtuella Azure-tillägg i en virtuell dator kan installeras. Princip idén bakom detta begrepp är att tillåta (i de fall som Azure-tillägget för SAP), distributionen av särskilda funktioner till en virtuell dator och konfigurationen av sådan program vara vid distributions tillfället.

Azure VM-agenten som möjliggör hantering av vissa Azure VM-tillägg i den virtuella datorn matas in i virtuella Windows-datorer som standard när du skapar virtuella datorer i Azure Portal. Om det gäller SUSE, Red Hat eller Oracle Linux är VM-agenten redan en del av Azure Marketplace-avbildningen. Om en virtuell Linux-dator skulle överföras från lokal plats till Azure måste VM-agenten installeras manuellt.

De grundläggande Bygg stenarna i lösningen för att tillhandahålla Azure-infrastruktur information till SAP-värd agenten i Azure ser ut så här:

![Microsoft Azure tilläggs komponenter][planning-guide-figure-2400]

Som du ser i block diagrammet ovan, finns en del av lösningen i Azure VM-avbildningen och Azure Extension Gallery, som är en globalt replikerad lagrings plats som hanteras av Azure-åtgärder. Det är ansvaret för det gemensamma SAP/MS-teamet som arbetar med Azure-implementeringen av SAP för att arbeta med Azure-åtgärder för att publicera nya versioner av Azure-tillägget för SAP.

När du distribuerar en ny virtuell Windows-dator läggs Azure VM-agenten automatiskt till i den virtuella datorn. Den här agentens funktion är att samordna inläsningen och konfigurationen av de virtuella datorernas Azure-tillägg. För virtuella Linux-datorer ingår Azure VM-agenten redan i Azure Marketplace OS-avbildningen.

Det finns dock ett steg som fortfarande måste utföras av kunden. Detta är inaktiverat och konfigurationen av prestanda insamling. Processen som är relaterad till konfigurationen automatiseras av ett PowerShell-skript eller CLI-kommando. PowerShell-skriptet kan laddas ned i Microsoft Azure Script Center enligt beskrivningen i [distributions guiden][deployment-guide].

Den övergripande arkitekturen för Azure-tillägget för SAP ser ut så här:

![Azure-tillägg för SAP ][planning-guide-figure-2500]

**Om du vill ha mer information om hur du använder dessa PowerShell-cmdletar eller CLI-kommandot under distributioner följer du anvisningarna i [distributions guiden][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrering av Azure finns SAP-instans i SAProuter

SAP-instanser som körs i Azure måste också vara tillgängliga från SAProuter.

![SAP-router nätverks anslutning][planning-guide-figure-2600]

En SAProuter aktiverar TCP/IP-kommunikationen mellan deltagande system om det inte finns någon direkt IP-anslutning. Detta ger fördelen att ingen anslutning från slut punkt till slut punkt mellan kommunikations partners är nödvändig på nätverks nivå. SAProuter lyssnar på port 3299 som standard.
Om du vill ansluta SAP-instanser via en SAProuter måste du ge SAProuter-strängen och värd namnet med alla försök att ansluta.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver som Java

Hittills har dokumentet varit SAP-NetWeaver i allmänhet eller SAP NetWeaver ABAP-stacken. I det här små avsnittet visas information om de olika aspekterna för SAP Java-stacken. En av de viktigaste SAP NetWeaver Java-baserade program är SAP-Enterprise Portal. Andra SAP NetWeaver-baserade program som SAP PI och SAP Solution Manager använder både SAP NetWeaver-ABAP och Java-stackar. Därför är det verkligen nödvändigt att överväga vissa aspekter som rör SAP NetWeaver Java-stacken.

### <a name="sap-enterprise-portal"></a>SAP-Enterprise Portal

Installationen av en SAP-Portal på en virtuell Azure-dator skiljer sig inte från en lokal installation om du distribuerar i scenarier med olika platser. Eftersom DNS görs av lokalt kan port inställningarna för de enskilda instanserna göras som konfigurerade lokalt. Rekommendationerna och begränsningarna som beskrivs i det här dokumentet gäller för ett program som SAP Enterprise Portal eller SAP NetWeaver Java stack i allmänhet.

![Exponerade SAP-Portal][planning-guide-figure-2700]

Ett särskilt distributions scenario av vissa kunder är den direkta exponeringen hos SAP-Enterprise Portal till Internet medan den virtuella värden är ansluten till företagets nätverk via plats-till-plats-VPN-tunnel eller ExpressRoute. För ett sådant scenario måste du se till att vissa portar är öppna och inte blockeras av brand väggen eller nätverks säkerhets gruppen. 

Den inledande portalens URI är http (s):`<Portalserver`>: 5XX00/irj där porten bildas som dokumenterad av SAP i <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Slut punkts konfiguration][planning-guide-figure-2800]

Om du vill anpassa URL: en och/eller portarna för din SAP-Enterprise Portal, se den här dokumentationen:

* [Ändra portal-URL](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Ändra standard port nummer, Portal port nummer](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Hög tillgänglighet (HA) och haveri beredskap (DR) för SAP-NetWeaver som körs på Azure Virtual Machines

### <a name="definition-of-terminologies"></a>Definition av terminologi

Termen **hög tillgänglighet (ha)** är i allmänhet relaterad till en uppsättning tekniker som MINIMERAr IT-avbrott genom att ge IT-tjänster verksamhets kontinuitet genom redundanta, feltoleranta eller redundansväxling skyddade komponenter i **samma** Data Center. I vårt fall, inom en Azure-region.

**Haveri beredskap (Dr)** är också riktad mot att minimera IT-tjänstens avbrott och återställningen, men över **olika** Data Center, som vanligt vis finns hundratals kilo meters avstånd. I vårt fall är det vanligt vis mellan olika Azure-regioner i samma politiska region eller som du har fastställt som kund.

### <a name="overview-of-high-availability"></a>Översikt över hög tillgänglighet

Vi kan dela upp diskussionen om SAP hög tillgänglighet i Azure i två delar:

* **Azure-infrastruktur med hög tillgänglighet**, till exempel ha för beräkning (VM), nätverk, lagring osv. och dess fördelar för att öka tillgängligheten för SAP-program.
* **SAP-program hög tillgänglighet**, till exempel ha av SAP-program varu komponenter:
  * SAP-program servrar
  * SAP ASCS/SCS-instans
  * DB-server

och hur det kan kombineras med Azure-infrastruktur HA.

SAP hög tillgänglighet i Azure har vissa skillnader jämfört med SAP hög tillgänglighet i en lokal fysisk eller virtuell miljö. Följande dokument från SAP beskriver standard konfigurationerna för SAP med hög tillgänglighet i virtualiserade miljöer i Windows: <https://scn.sap.com/docs/DOC-44415>. Det finns ingen sapinst-integrerad SAP-HA-konfiguration för Linux som den finns för Windows. Om du har en lokal SAP-tillgänglighet för Linux hittar du mer information här: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Hög tillgänglighet för Azure-infrastruktur

Det finns för närvarande ett service avtal med en virtuell dator på 99,9%. För att få en uppfattning om hur tillgängligheten för en enskild virtuell dator kan se ut så kan du skapa produkten av de olika tillgängliga Azure-service avtal: <https://azure.microsoft.com/support/legal/sla/>.

Basen för beräkningen är 30 dagar per månad eller 43200 minuter. Därför motsvarar 0,05% nedtid till 21,6 minuter. Som vanligt kommer tillgängligheten för de olika tjänsterna att multipliceras på följande sätt:

(Tillgänglighets tjänst #1/100) * (tillgänglighets tjänst #2/100) * (tillgänglighets tjänst #3/100) 

Likadan

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 eller en övergripande tillgänglighet på 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Hög tillgänglighet för virtuell dator (VM)

Det finns två typer av Azure Platform-händelser som kan påverka tillgängligheten för dina virtuella datorer: planerat underhåll och oplanerat underhåll.

* Planerade underhålls händelser är periodiska uppdateringar som görs av Microsoft till den underliggande Azure-plattformen för att förbättra den övergripande tillförlitligheten, prestandan och säkerheten för den plattforms infrastruktur som dina virtuella datorer körs på.
* Oplanerade underhålls händelser inträffar när den maskin vara eller den fysiska infrastruktur som underliggande den virtuella datorn har misslyckats på något sätt. Det kan vara lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När ett sådant fel upptäcks, kommer Azure-plattformen automatiskt att migrera den virtuella datorn från den ej hälsofysiska server som är värd för den virtuella datorn till en felfri fysisk server. Den här typen av händelser kan också göra att den virtuella datorn startas om, men det är ovanligt.

Mer information finns i den här dokumentationen: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure Storage Redundancy

Data i ditt Microsoft Azure Storage-konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, vilket uppfyller Azure Storage service avtal även i de flesta tillfälliga maskin varu haverier.

Eftersom Azure Storage håller tre avbildningar av data som standard behövs inte RAID5 eller RAID1 över flera Azure-diskar.

Mer information hittar du i den här artikeln: <https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Använda en omstart av Azure Infrastructure VM för att uppnå högre tillgänglighet för SAP-program

Om du väljer att inte använda funktioner som Windows Server Failover Clustering (WSFC) eller pacemaker på Linux (för närvarande endast för SLES 12 och högre) används Azure VM-omstart för att skydda ett SAP-system mot planerad och oplanerad stillestånds tid för Azure fysisk server infrastruktur och den övergripande underliggande Azure-plattformen.

> [!NOTE]
> Det är viktigt att nämna att Azure VM-omstart främst skyddar virtuella datorer och inte program. VM-omstart erbjuder inte hög tillgänglighet för SAP-program, men det erbjuder en viss nivå av infrastruktur tillgänglighet och därför indirekt tillgänglighet för SAP-system. Det finns inte heller något service avtal för den tid det tar att starta om en virtuell dator efter ett planerat eller oplanerat värd avbrott. Därför är den här metoden för hög tillgänglighet inte lämplig för kritiska komponenter i ett SAP-system som (A) SCS eller DBMS.
>
>

Ett annat viktigt infrastruktur element för hög tillgänglighet är lagring. Till exempel är Azure Storage service avtal 99,9% tillgänglighet. Om en distribuerar alla virtuella datorer med sina diskar till ett enda Azure Storage-konto, kommer potentiella Azure Storage otillgängligheten att orsaka att alla virtuella datorer som placerats i det Azure Storage kontot och alla SAP-komponenter som körs i de virtuella datorerna är tillgängliga.  

I stället för att lägga till alla virtuella datorer i ett enda Azure Storage konto kan du också använda dedikerade lagrings konton för varje virtuell dator och på så sätt öka den övergripande VM-och SAP-programmets tillgänglighet genom att använda flera oberoende Azure Storage-konton.

Azure Managed Disks placeras automatiskt i fel domänen för den virtuella dator som de är kopplade till. Om du placerar två virtuella datorer i en tillgänglighets uppsättning och använder Managed Disks, tar plattformen hand om att distribuera Managed Disks till olika fel domäner. Om du planerar att använda Premium Storage rekommenderar vi starkt att du använder hantera diskar även.

En exempel arkitektur för ett SAP NetWeaver-system som använder Azures infrastruktur HA och lagrings konton kan se ut så här:

![Använda Azure-infrastruktur HA för att få SAP-program högre tillgänglighet][planning-guide-figure-2900]

En exempel arkitektur för ett SAP NetWeaver-system som använder Azures infrastruktur HA och Managed Disks kan se ut så här:

![Använda Azure-infrastruktur HA för att få SAP-program högre tillgänglighet][planning-guide-figure-2901]

För kritiska SAP-komponenter har vi uppnått följande hittills:

* Hög tillgänglighet för SAP-programservrar (AS)

  SAP Application Server-instanser är redundanta komponenter. Varje SAP AS-instans distribueras på en egen virtuell dator, som körs i en annan Azure-fel-och uppgraderings domän (se kapitlen [fel domäner][planning-guide-3.2.1] och [uppgraderings domäner][planning-guide-3.2.2]). Detta säkerställs med hjälp av Azures tillgänglighets uppsättningar (se kapitel [tillgänglighets uppsättningar för Azure][planning-guide-3.2.3]). Potentiell planerad eller oplanerad otillgänglig tillgänglighet för en Azure-fel eller uppgraderings domän kommer att orsaka att ett begränsat antal virtuella datorer inte är tillgängliga med SAP som-instanser.

  Varje SAP AS-instans placeras i ett eget Azure Storage konto – möjligt att inte få till gång till ett Azure Storage konto innebär att det bara finns en virtuell dator med sin SAP som instans. Tänk dock på att det finns en gräns för Azure Storage konton i en Azure-prenumeration. För att säkerställa att den automatiska starten av (A) SCS-instansen efter den virtuella datorn har startats om, måste du ange Autostart-parametern i (A) SCS-instansens start profil som beskrivs i kapitlet [använda Autostart för SAP-instanser][planning-guide-11.5]
  Läs även kapitel [hög tillgänglighet för SAP-program servrar][planning-guide-11.4.1] för mer information.

  Även om du använder Managed Disks lagras dessa diskar också i ett Azure Storage konto och kan vara otillgängliga i händelse av ett lagrings avbrott.

* *Högre* Tillgänglighet för SAP (A) SCS-instans

  Här använder vi Azure VM-omstart för att skydda den virtuella datorn med en installerad SCS-instans (A). Om du har planerat eller oplanerat avbrott i Azure-servrarna startas virtuella datorer om på en annan tillgänglig server. Som tidigare nämnts kan Azure VM-omstart främst skydda virtuella datorer och inte program, i det här fallet (A) SCS-instansen. Genom den virtuella datorn startar vi om indirekt högre tillgänglighet för SAP (A) SCS-instansen. För att försäkra dig om att den automatiska starten av (A) SCS-instansen efter den virtuella datorn har startats om måste du ange Autostart-parametern i (A) SCS-instansens start profil som beskrivs i kapitlet [använda Autostart för SAP][planning-guide-11.5] Det innebär att (A) SCS-instansen som en enskild felpunkt (SPOF) som körs i en enskild virtuell dator är den avbrotts faktor som är tillgänglig för hela SAP-landskapet.

* *Högre* Tillgänglighet för DBMS-Server

  På liknande sätt som i SAP (A) SCS-instansen använder vi Azure VM-omstart för att skydda den virtuella datorn med ett installerat DBMS-program och vi uppnår högre tillgänglighet för DBMS-programvara via omstart av virtuella datorer.
  DBMS som körs i en enda virtuell dator är också en SPOF och det är den avvecklings faktor som är tillgänglig för hela SAP-landskapet.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Hög tillgänglighet för SAP-program på Azure-IaaS

För att uppnå full tillgänglighet för SAP-systemet måste vi skydda alla viktiga SAP-system komponenter, till exempel redundanta SAP-programservrar och unika komponenter (till exempel en enskild felpunkt) som SAP (A) SCS-instans och DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hög tillgänglighet för SAP-programservrar

För instansen SAP-programservrar/-dialog är det inte nödvändigt att tänka på en specifik lösning för hög tillgänglighet. Hög tillgänglighet uppnås genom redundans och därmed tillräckligt många av dem på olika virtuella datorer. De bör placeras i samma Azures tillgänglighets uppsättning för att undvika att de virtuella datorerna kan uppdateras samtidigt under avbrott i planerat underhåll. De grundläggande funktionerna, som bygger på olika uppgraderings-och fel domäner i en Azure-Scale-enhet, har redan introducerats i kapitel [uppgraderings domäner][planning-guide-3.2.2]. Azures tillgänglighets uppsättningar presenterades i kapitel [Azures tillgänglighets uppsättningar][planning-guide-3.2.3] i det här dokumentet.

Det finns inget obegränsat antal fel-och uppgraderings domäner som kan användas av en Azure-tillgänglighets uppsättning i en Azure-Scale-enhet. Det innebär att du placerar ett antal virtuella datorer i en tillgänglighets uppsättning, förr eller senare mer än en virtuell dator i samma fel-eller uppgraderings domän.

Distribution av några SAP Application Server-instanser i sina dedikerade virtuella datorer och vi antar att vi fick fem uppgraderings domäner visas följande bild i slutet. Det faktiska max antalet fel-och uppdaterings domäner i en tillgänglighets uppsättning kan ändras i framtiden:

![HA av SAP-program servrar i Azure][planning-guide-figure-3000]

Mer information finns i den här dokumentationen: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hög tillgänglighet för SAP Central Services på Azure

För hög tillgänglighets arkitektur i SAP Central Services på Azure, se artikeln [hög tillgänglighets arkitektur och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) som information. Artikeln pekar på mer detaljerade beskrivningar för specifika operativ system.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hög tillgänglighet för SAP Database-instansen

Den typiska SAP-DBMS HA-installationen baseras på två DBMS-datorer där DBMS-funktioner med hög tillgänglighet används för att replikera data från den aktiva DBMS-instansen till den andra virtuella datorn i en passiv DBMS-instans.

Funktioner för hög tillgänglighet och haveri beredskap för DBMS i allmänhet samt vissa DBMS beskrivs i [distributions guiden för DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Hög tillgänglighet från slut punkt till slut punkt för hela SAP-systemet

Här följer två exempel på en komplett SAP NetWeaver HA-arkitektur i Azure – en för Windows och en för Linux.

Endast ohanterade diskar: begreppen som beskrivs nedan kan behöva komprometteras när du distribuerar många SAP-system och antalet distribuerade virtuella datorer överskrider max gränsen för lagrings konton per prenumeration. I sådana fall måste virtuella hård diskar i virtuella datorer kombineras inom ett lagrings konto. Vanligt vis skulle du göra detta genom att kombinera virtuella hård diskar med SAP-programlager på olika SAP-system.  Vi har också kombinerat olika virtuella hård diskar av olika DBMS-VM: er för olika SAP-system i ett Azure Storage-konto. Att bevara de IOPS-gränser som Azure Storage konton i åtanke (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA på Windows

![SAP NetWeaver Application HA-arkitektur med SQL Server i Azure IaaS][planning-guide-figure-3200]

Följande Azure-konstruktioner används för SAP NetWeaver-systemet för att minimera påverkan på infrastruktur problem och värd korrigering:

* Hela systemet distribueras på Azure (nödvändigt-DBMS-lager, (A) SCS-instans och fullständigt program lager måste köras på samma plats).
* Hela systemet körs i en Azure-prenumeration (krävs).
* Hela systemet körs i en Azure-Virtual Network (krävs).
* Det går att separera de virtuella datorerna i ett SAP-system i tre tillgänglighets uppsättningar även med alla virtuella datorer som tillhör samma Virtual Network.
* Varje lager (till exempel DBMS, ASCS, program servrar) måste använda en dedikerad tillgänglighets uppsättning.
* Alla virtuella datorer som kör DBMS-instanser av ett SAP-system finns i en tillgänglighets uppsättning. Vi förutsätter att det finns mer än en virtuell dator som kör DBMS-instanser per system sedan ursprungligt DBMS hög tillgänglighets funktioner används, till exempel SQL Server AlwaysOn eller Oracle data Guard.
* Alla virtuella datorer som kör DBMS-instanser använder sina egna lagrings konton. DBMS-data och loggfiler replikeras från ett lagrings konto till ett annat lagrings konto med hjälp av DBMS-funktioner med hög tillgänglighet som synkroniserar data. Om du inte kan få till gång till ett lagrings konto kan du inte få till gång till en SQL-klusternod i Windows, men inte hela SQL Servers tjänsten.
* Alla virtuella datorer som kör (A) SCS-instans av ett SAP-system finns i en tillgänglighets uppsättning. Ett WSFC-kluster (Windows Server failover Cluster) är konfigurerat i de virtuella datorerna för att skydda (A) SCS-instansen.
* Alla virtuella datorer som kör (A) SCS-instanser använder sina egna lagrings konton. En SCS-instanser och globala SAP-mappar replikeras från ett lagrings konto till ett annat lagrings konto med hjälp av SIOS DataKeeper-replikering. Om ett lagrings konto inte är tillgängligt kommer det att leda till att en (A) SCS-klusternod (a) inte är tillgänglig, men inte hela SCS-tjänsten (A).
* ALLA virtuella datorer som representerar SAP Application Server-lagret finns i en tredje tillgänglighets uppsättning.
* ALLA virtuella datorer som kör SAP-programservrar använder sina egna lagrings konton. Om ett lagrings konto inte är tillgängligt kan en SAP-Programserver bli otillgänglig, där andra SAP-programservrar fortsätter att köras.

Följande figur illustrerar samma liggande med Managed Disks.

![SAP NetWeaver Application HA-arkitektur med SQL Server i Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA på Linux

Arkitekturen för SAP HA på Linux på Azure är i princip samma som för Windows enligt beskrivningen ovan. Se SAP NOTE [1928533] för en lista över lösningar med hög tillgänglighet som stöds.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Använda Autostart för SAP-instanser

SAP erbjöd funktionen att starta SAP-instanser direkt efter att operativ systemet startades på den virtuella datorn. De exakta stegen dokumenterades i Knowledge Base-artikeln [1909114]i SAP. Men SAP rekommenderar inte att du använder inställningen längre eftersom det inte finns någon kontroll i ordningen på instansen som startas om, förutsatt att fler än en virtuell dator har påverkat eller flera instanser har körts per virtuell dator. Om du antar ett typiskt Azure-scenario för en SAP Application Server-instans i en virtuell dator och om en enskild virtuell dator kommer att komma igång, är den autostarten inte kritisk och kan aktive ras genom att lägga till följande parameter:

    Autostart = 1

I Start-profilen för SAP-ABAP och/eller Java-instansen.

> [!NOTE]
> Den Autostart-parametern kan också ha vissa downfalls. I detalj är parametern utlöser starten av en SAP-ABAP eller Java-instans när den relaterade Windows/Linux-tjänsten för instansen startas. Det är verkligen fallet när operativ systemet startas. Omstarter av SAP-tjänster är dock också vanliga för SAP-funktioner för program livs cykel hantering, till exempel SUM eller andra uppdateringar och uppgraderingar. Dessa funktioner förväntas inte att en instans ska startas om automatiskt. Därför bör den Autostart-parametern inaktive ras innan aktiviteter körs. Autostart-parametern bör inte heller användas för SAP-instanser som är klustrade, som ASCS/SCS/CI.
>
>

Se ytterligare information om Autostart för SAP-instanser här:

* [Starta/stoppa SAP tillsammans med din UNIX-server starta/stoppa](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starta och stoppa SAP NetWeaver-hanterings agenter](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Så här aktiverar du automatisk start av HANA-databas](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Större SAP-system på 3 nivåer
Hög tillgänglighets aspekter av SAP-konfigurationer på tre nivåer beskrivs redan i tidigare avsnitt. Men vad händer om system där DBMS-serverns krav är för stort för att det ska finnas i Azure, men SAP-programlagret kan distribueras till Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Plats för SAP-konfigurationer på 3 nivåer
Det finns inte stöd för att dela upp själva program nivån eller program-och DBMS-nivån mellan lokalt och Azure. Ett SAP-system är antingen fullständigt distribuerat lokalt eller i Azure. Det finns inte heller stöd för att vissa program servrar ska köras lokalt och andra i Azure. Det är diskussionens start punkt. Vi stöder inte heller att DBMS-komponenterna i ett SAP-system och SAP-program Server-lagret distribueras i två olika Azure-regioner. Till exempel DBMS i västra USA och SAP-program skikt i Central USA. Anledningen till att inte stödja sådana konfigurationer är fördröjnings känsligheten för SAP NetWeaver-arkitekturen.

Men under de senaste åren har data Center partners utvecklat samplaceringar till Azure-regioner. Dessa samplatser är ofta i nära närhet till de fysiska Azure-datacenterna i en Azure-region. Det korta avståndet och anslutningen till till gångarna i samplaceringen via ExpressRoute till Azure kan resultera i en latens som är mindre än 2ms. I sådana fall är det möjligt att hitta DBMS-skiktet (inklusive SAN/NAS) i en sådan samplacering och SAP-Programskiktet i Azure. [Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline-säkerhetskopiering av SAP-system
Beroende av den valda SAP-konfigurationen (2-eller 3-skikt) kan du behöva säkerhetskopiera. Innehållet i den virtuella datorn och för att ha en säkerhets kopia av databasen. De DBMS-relaterade säkerhets kopiorna förväntas göras med databas metoder. En detaljerad beskrivning av de olika databaserna finns i DBMS- [Guide][dbms-guide]. Å andra sidan kan SAP-data säkerhets kopie ras på ett offline-sätt (inklusive databas innehållet) enligt beskrivningen i det här avsnittet eller online enligt beskrivningen i nästa avsnitt.

Offline-säkerhetskopiering kräver i princip en omstart av den virtuella datorn via Azure Portal och en kopia av den virtuella bas-VM-disken plus alla anslutna diskar till den virtuella datorn. Detta bevarar en tidpunkts avbildning av den virtuella datorn och den tillhör ande disken. Vi rekommenderar att du kopierar säkerhets kopiorna till ett annat Azure Storage konto. Därför gäller proceduren som beskrivs i kapitlet [kopiera diskar mellan Azure Storage konton][planning-guide-5.4.2] i det här dokumentet.
Förutom avstängning med Azure Portal kan du också göra det via PowerShell eller CLI enligt beskrivningen här: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

En återställning av detta tillstånd skulle bestå i att ta bort den virtuella bas datorn samt de ursprungliga diskarna för den virtuella bas datorn och monterade diskar, kopiera de sparade diskarna till det ursprungliga lagrings kontot eller resurs gruppen för hanterade diskar och sedan distribuera om systemet.
Den här artikeln visar ett exempel på hur du kan skripta processen i PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Se till att installera en ny SAP-licens eftersom återställning av en VM-säkerhetskopiering enligt beskrivningen ovan skapar en ny maskin varu nyckel.

### <a name="online-backup-of-an-sap-system"></a>Online-säkerhetskopiering av ett SAP-system

Säkerhets kopiering av DBMS utförs med DBMS-/regionsspecifika metoder som beskrivs i DBMS- [guiden][dbms-guide].

Andra virtuella datorer i SAP-systemet kan säkerhets kopie ras med hjälp av funktionen för säkerhets kopiering av virtuella Azure-datorer. Säkerhets kopiering av virtuella Azure-datorer är en standard metod för att säkerhetskopiera en komplett virtuell dator i Azure. Azure Backup lagrar säkerhets kopiorna i Azure och gör det möjligt att återställa en virtuell dator igen.

> [!NOTE]
> Från och med dec 2015 behåller inte det unika VM-ID som används för SAP-licensiering med den virtuella datorns säkerhets kopiering. Det innebär att en återställning från en säkerhets kopia av en virtuell dator kräver installation av en ny SAP-licens nyckel eftersom den återställda virtuella datorn anses vara en ny virtuell dator och inte en ersättning för den tidigare sparade.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretiskt sett kan virtuella datorer som kör databaser säkerhets kopie ras på ett konsekvent sätt även om DBMS-systemet stöder Windows VSS (tjänsten Volume Shadow Copy <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) som exempelvis SQL Server gör.
> Tänk dock på att det inte går att använda en säkerhets kopia av virtuella Azure-säkerhetskopieringar för att återställa databasen. Rekommendationen är därför att säkerhetskopiera databaser med DBMS-funktioner i stället för att förlita dig på säkerhets kopiering av virtuella Azure-datorer.
>
> Om du vill bekanta dig med säkerhets kopiering av virtuella Azure-datorer här: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Andra möjligheter är att använda en kombination av Microsoft-Data Protection Manager som är installerade på en virtuell Azure-dator och Azure Backup för att säkerhetskopiera/återställa databaser. Mer information hittar du här: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Det finns ingen motsvarighet till Windows VSS i Linux. Därför är bara filkonsekventa säkerhets kopieringar möjliga, men inte programkonsekventa säkerhets kopieringar. Säkerhets kopieringen av SAP-DBMS bör göras med hjälp av DBMS-funktioner. Fil systemet som innehåller SAP-relaterade data kan sparas, till exempel genom att använda tjära som beskrivs här: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure as DR-webbplats för produktion SAP-landskap

Sedan mellan 2014 är tillägg till olika komponenter runt Hyper-V, System Center och Azure att aktivera användningen av Azure som en DR-plats för virtuella datorer som körs lokalt baserat på Hyper-V.

En blogg som beskriver hur du distribuerar den här lösningen finns dokumenterad här: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Sammanfattning

De viktigaste punkterna med hög tillgänglighet för SAP-system i Azure är:

* Vid det här tillfället går det inte att skydda SAP-felpunkten exakt på samma sätt som den kan göras i lokala distributioner. Orsaken är att delade disk kluster ännu inte har skapats i Azure utan användning av program från tredje part.
* För DBMS-skiktet måste du använda DBMS-funktioner som inte förlitar sig på delad disk kluster teknik. Information finns dokumenterad i [DBMS-guiden][dbms-guide].
* För att minimera effekten av problem inom fel domäner i Azure-infrastrukturen eller värd underhållet, bör du använda Azures tillgänglighets uppsättningar:
  * Vi rekommenderar att du har en tillgänglighets uppsättning för SAP-program skiktet.
  * Vi rekommenderar att du har en separat tillgänglighets uppsättning för SAP-DBMS-skiktet.
  * Vi rekommenderar inte att du använder samma tillgänglighets uppsättning för virtuella datorer i olika SAP-system.
  * Vi rekommenderar att du använder Premium Managed Disks.
* För säkerhets kopiering av SAP DBMS-skiktet kontrollerar du [DBMS-guiden][dbms-guide].
* Att säkerhetskopiera SAP-dialogrutor är mycket bra eftersom det vanligt vis är snabbare att omdistribuera enkla dialog instanser.
* Säkerhets kopiering av den virtuella datorn, som innehåller den globala katalogen i SAP-systemet och med alla profiler för de olika instanserna, är begriplig och bör utföras med Windows säkerhets kopiering eller, till exempel tar på Linux. Eftersom det finns skillnader mellan Windows Server 2008 (R2) och Windows Server 2012 (R2), vilket gör det enklare att säkerhetskopiera med hjälp av de senaste Windows Server-versionerna, rekommenderar vi att du kör Windows Server 2012 (R2) som Windows gäst operativ system.

## <a name="next-steps"></a>Nästa steg
Läs artiklarna:

- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA infrastruktur konfiguration och åtgärder på Azure] (https://docs.microsoft.com/
- Azure/Virtual-Machines/arbets belastningar/SAP/HANA – VM-åtgärder)
