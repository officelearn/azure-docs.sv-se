---
title: Azure virtuella datorer, planering och implementering av SAP NetWeaver | Microsoft Docs
description: Azure virtuella datorer, planering och implementering av SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91ed29b100e0fab0f0e386f771dc6f71d7b424c6
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163421"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure virtuella datorer, planering och implementering av SAP NetWeaver
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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
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
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
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
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure gör det möjligt för företag för att hämta beräknings- och lagringsresurser på minimal tid utan sätt långa anskaffningscykler cykler. Tjänsten Azure Virtual Machines kan företag för att distribuera klassisk program som SAP NetWeaver-baserade program till Azure och utöka sina tillförlitlighet och tillgänglighet utan att behöva ytterligare resurser tillgängliga lokalt. Azure VM-tjänster stöder också flera platsanslutningar, som gör det möjligt att integrera aktivt Azure-datorer i sina lokala domäner sina privata moln och sina SAP-landskap som System.
Detta white paper beskriver grunderna för Microsoft Azure-dator och ger en genomgång av planerings- och implementeringsöverväganden för SAP NetWeaver-installationer i Azure och därför bör vara dokumentet för att läsa innan du startar faktiska distributioner av SAP NetWeaver på Azure.
Dokumentet kompletterar dokumentationen för Installation av SAP och SAP Notes som motsvarar de viktigaste resurserna för installationer och distributioner av SAP-program på den angivna plattformar.

## <a name="summary"></a>Sammanfattning
Molnbaserad datahantering är en välanvänd term som ökat i vikt inom IT-branschen, från småföretag upp till stora multinationella bolag.

Microsoft Azure är den plattform för molntjänster från Microsoft, som erbjuder ett brett utbud av nya möjligheter. Nu kan kunder snabbt etablera och användares program som en tjänst i molnet, så att de inte är begränsad till tekniska eller budgetbegränsningar. I stället för att investera tid och budget i maskinvaruinfrastruktur, kan företag fokusera på programmet, verksamhetsprocesserna och dess fördelar för kunder och användare.

Med Microsoft Azure Virtual Machine-tjänsterna, erbjuder Microsoft en omfattande plattform för infrastruktur som en tjänst (IaaS). SAP NetWeaver-baserade program stöds på virtuella Azure-datorer (IaaS). Det här faktabladet om hur du planerar och implementerar SAP NetWeaver-baserade program i Microsoft Azure som föredragen plattform.

Dokumentet själva fokuserar på två huvudsakliga delar:

* Den första delen beskriver två mönster för distribution som stöds för SAP NetWeaver-baserade program på Azure. Här beskrivs också allmänna hantering av Azure med SAP-distributioner i åtanke.
* En del andra information implementera två olika scenarier som beskrivs i den första delen.

Ytterligare resurser finns i kapitlet [resurser] [ planning-guide-1.2] i det här dokumentet.

### <a name="definitions-upfront"></a>Definitioner förskott
Vi använder följande termer för hela dokumentet:

* IaaS: Infrastruktur som en tjänst
* PaaS: Plattform som en tjänst
* SaaS: Software as a Service
* SAP-komponent: ett enskilt SAP program, till exempel ECC, BW, lösningen Manager eller EP.  SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller ett icke-NetWeaver-baserade program, till exempel företag objekt.
* SAP-miljön: en eller flera SAP-komponenter logiskt grupperade för att utföra en företagsfunktion, till exempel utveckling, QAS, utbildning, DR eller produktion.
* SAP-landskap: Den här termen avser hela SAP-tillgångar i en kunds IT-miljön. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
* SAP-System: Kombinationen av DBMS lager och programnivån av, till exempel en utvecklingssystem för SAP ERP, SAP BW testsystem, SAP CRM produktionssystemet osv. I Azure-distributioner, är det inte stöd för att dela upp de här två lager mellan lokala och Azure. Innebär att ett SAP-system är antingen distribuerat lokalt eller det distribueras i Azure. Du kan dock distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure men SAP CRM produktionsprogrammen system på plats.
* Endast molnbaserad distribution: en distribution där Azure-prenumerationen inte är ansluten via en plats-till-plats eller ExpressRoute-anslutning i den lokala nätverksinfrastruktur. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som ”molnbaserade” distributioner. Virtuella datorer som distribueras med den här metoden kan nås via internet och en offentlig IP-adress och/eller en offentlig DNS-namn som tilldelats de virtuella datorerna i Azure. För Microsoft Windows, lokalt Active Directory (AD) och DNS inte har utökats till Azure i dessa typer av distributioner. De virtuella datorerna är därför inte en del av den lokala Active Directory. Detsamma gäller för Linux-implementeringar med hjälp av till exempel OpenLDAP + Kerberos.

> [!NOTE]
> Endast molnbaserad distribution i det här dokumentet är definierade som fullständig SAP-landskap körs endast i Azure utan utökning av Active Directory / OpenLDAP eller namnmatchning från en lokal plats till offentliga molnet. Endast molnbaserad konfigurationer stöds inte för produktion SAP-system eller konfigurationer där SAP STM eller andra lokala resurser behöver användas mellan SAP-system i Azure och resurser som finns på plats.
>
>

* Mellan olika platser: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, flera platser eller ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. Orsaken till att anslutningen är att utöka lokala domäner, en lokal Active Directory/OpenLDAP och den lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar för prenumerationen. Med det här tillägget, kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare på den lokala domänen har åtkomst till servrarna och kan köra services på de virtuella datorerna (till exempel DBMS tjänster). Kommunikation och namnmatchning mellan virtuella datorer som distribueras på en plats och distribuerade virtuella Azure-datorer är möjligt. Detta gäller de vanligaste och nästan exklusivt distribuera SAP-tillgångar i Azure. Mer information finns i [detta] [ vpn-gateway-cross-premises-options] artikeln och [detta][vpn-gateway-site-to-site-create].

> [!NOTE]
> Mellan lokala distributioner av SAP-system om Azure virtuella datorer som kör SAP-system är medlemmar i en lokal domän har stöd för SAP-system för produktion. Mellan lokala konfigurationer som stöds för att distribuera delar eller slutföra SAP-landskap i Azure. Även köra fullständig SAP-landskap i Azure kräver att dessa virtuella datorer är en del av den lokala domänen och ANNONSER/OpenLDAP. I tidigare versioner av dokumentationen vi talade om Hybrid-IT-scenarier där termen *Hybrid* grundas på typsystemet i det faktum att det finns en anslutning mellan olika platser mellan lokala och Azure. Dessutom är det faktum att de virtuella datorerna i Azure är en del av den lokala Active Directory / OpenLDAP.
>
>

Vissa Microsoft-dokumentationen beskriver mellan lokala scenarier lite annorlunda särskilt för DBMS HA konfigurationer. När det gäller SAP-relaterade-dokument handlar mellan lokala scenariot bara att ha plats-till-plats eller privat (ExpressRoute)-anslutning och det faktum att SAP-landskap fördelas mellan lokala och Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Resurser
Följande ytterligare guider finns tillgängliga för avsnittet om SAP-distributioner på Azure:

* [Azure virtuella datorer, planering och implementering av SAP NetWeaver (det här dokumentet)][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]

> [!IMPORTANT]
> Var möjligt en länk till den refererande SAP-installationsguiden används (referens InstGuide-01, se <http://service.sap.com/instguides>). När det gäller krav och installationsprocessen, bör SAP NetWeaver-Installationsguiderna alltid att läsa noggrant, eftersom det här dokumentet beskriver endast specifika uppgifter för SAP NetWeaver-system som är installerad på en Microsoft Azure-dator.
>
>

Följande SAP-information är relaterade till ämnet med SAP på Azure:

| Nummer | Titel |
| --- | --- |
| [1928533] |SAP-program i Azure: produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: supportkrav |
| [1999351] |Felsökning av utökad Azure övervakning för SAP |
| [2178632] |Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering på Windows: förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: förbättrad övervakning |
| [2243692] |Linux på Microsoft Azure (IaaS) VM: SAP problem |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP-Installation och uppgradering |
| [1597355] |Växlingsutrymme rekommendation för Linux |

Läs även den [Tillståndsändringsavisering Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP Notes för Linux.

Allmän standardbegränsningar och högsta begränsningar i Azure-prenumerationer kan hittas i [i den här artikeln][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Möjliga scenarier
SAP ses ofta som en av de mest verksamhetskritiska program i företag. Arkitektur och driften av dessa program gäller mest komplexa och se till att du uppfyller kraven på tillgänglighet och prestanda är viktigt.

Företag har därför att tänka noga om vilka program kan köras i en offentlig molnmiljö, oberoende av den valda molnleverantören.

Möjliga typer för att distribuera SAP NetWeaver-baserade program i offentligt moln miljöer finns nedan:

1. Medelstora produktionssystem
2. Utvecklingssystem
3. Testa system
4. Skapa prototyper system
5. Learning / Demonstration system

För att distribuera SAP-system i Azure IaaS eller IaaS i allmänhet, är det viktigt att förstå de viktiga skillnaderna mellan erbjudandena för traditionella outsourcers eller värdar och IaaS-erbjudanden. Medan traditionella värden eller outsourcer behovsanpassad infrastruktur (nätverk, lagring och server-typ) på arbetsbelastningen som en kund vill vara värd för, men det är i stället kundens ansvar att välja rätt arbetsbelastningen för IaaS-distributioner.

Som ett första steg måste kunder du kontrollera följande:

* SAP stöds typer av virtuella datorer i Azure
* SAP-produkter/versioner som stöds på Azure
* Stöds OS och DBMS släpper för specifika SAP-versioner i Azure
* SAP-dataflöde från olika Azure SKU: er

Svaren på dessa frågor kan läsas på SAP-kommentar [1928533].

Som ett andra steg måste begränsningar för Azure-resurs och bandbredd som ska jämföras med faktiska resurser som används för lokala system. Kunder måste känna till de olika funktionerna i Azure typer som stöds med SAP i området:

* Processor och minne resurser av olika typer av virtuella datorer och
* IOPS-bandbredd för olika typer av virtuella datorer och
* Nätverksfunktioner för olika typer av virtuella datorer.

De flesta av dessa data kan hittas [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Tänk på att gränserna som anges i länken ovan finns övre gräns. Det inte innebär som gränserna för någon av resurserna, kan till exempel IOPS anges i alla fall. Undantagen är dock processor- och resurser på en vald typ av virtuell dator. För VM-typer som stöds av SAP, är processor- och resurserna reserveras och är därför tillgängliga när som helst i tid för användning i den virtuella datorn.

Microsoft Azure-plattformen som andra IaaS-plattformar är en plattform för flera innehavare. Därmed som lagring, nätverk och andra resurser delas mellan klienter. Intelligent begränsningar och kvoter logik används till att förhindra att en klient kan påverka prestanda för en annan klient (resursfördelningen) på ett drastiskt sätt. Även om logiken i Azure försöker hålla avvikelser i bandbredd stötte på brukar små, mycket delad plattformar introducera större avvikelser i resursen/bandbreddstillgänglighet än många kunder till i sina lokala distributioner. Därför kan uppleva du olika nivåer av bandbredd om nätverks- eller lagringsinställningarna i/o (den volym som svarstid) från en minut till en minut. Sannolikheten att ett SAP-system på Azure kan uppleva större avvikelser än i ett lokalt system måste beaktas.

Sista steget är att utvärdera kraven på tillgänglighet. Det kan inträffa att de underliggande Azure-infrastrukturen behöver uppdateras och kräver värdar som kör virtuella datorer startas om. I dessa fall skulle virtuella datorer som körs på dessa värdar stängs och startas om samt. Tidsinställningen för sådana Underhåll görs under kontorstid för icke-kärnor för en viss region men fönstret potentiella på några timmar då en omstart utförs är relativt brett. Det finns olika tekniker i Azure-plattformen som kan konfigureras för att åtgärda vissa eller alla effekten av dessa uppdateringar. Framtida förbättringar av Azure-plattformen, DBMS och SAP program är utformade för att minimera effekten av sådana omstarter.

För att kunna distribuera ett SAP-system till Azure, lokala SAP-system operativsystem, databas, och SAP-program måste visas på stödmatris SAP Azure passar med resurserna i Azure-infrastruktur kan ge och som fungerar med tillgänglighet serviceavtal i Microsoft Azure-erbjudanden. Eftersom dessa system identifieras måste du välja något av följande två scenarier.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Molnbaserad - distributioner av virtuella datorer till Azure utan beroenden på den lokala kundens nätverk
![Enstaka virtuell dator med SAP-demo eller utbildning scenario distribueras i Azure][planning-guide-figure-100]

Det här scenariot är vanligt för utbildningar eller demo-system, där alla komponenter i SAP och icke-SAP-program är installerade i en enda virtuell dator. SAP produktionssystem stöds inte i det här distributionsscenariot. I allmänhet är uppfyller det här scenariot följande krav:

* De virtuella datorerna själva är tillgängliga via det offentliga nätverket. Direkt nätverksanslutning för programmen som körs inifrån de virtuella datorerna till det lokala nätverket antingen företagets ägande demonstrationer eller utbildningar innehållet eller kunden är inte nödvändigt.
* När det gäller flera virtuella datorer som representerar utbildningar eller demo-scenario, måste nätverket kommunikation och namnmatchning fungerar mellan de virtuella datorerna. Men kommunikation mellan uppsättningen av virtuella datorer behöver vara isolerade så att flera uppsättningar av virtuella datorer kan distribueras sida vid sida utan störningar.  
* Internetanslutning krävs för att slutanvändaren kan fjärrinloggning till till de virtuella datorerna i Azure. Beroende på gästen används OS, Terminal Services/RDS eller VNC/ssh för att få åtkomst till den virtuella datorn att utföra uppgifter för utbildning eller utföra demonstrationerna. Om SAP-portarna som 3200, 3300 och 3600 kan också exponeras programinstansen SAP kan nås från valfri Internet-anslutna desktop.
* SAP system (och VM(s)) representerar ett fristående scenario i Azure, som endast kräver en offentlig Internetanslutning för slutanvändarnas åtkomst och inte kräver en anslutning till andra virtuella datorer i Azure.
* SAPGUI och en webbläsare är installerat och körs direkt på den virtuella datorn.
* Det krävs en snabb återställning av en virtuell dator till det ursprungliga tillståndet och ny distribution av det ursprungliga tillståndet igen.
* När det gäller Demonstrations- och utbildning scenarier som realiseras i flera virtuella datorer, en Active Directory / OpenLDAP och/eller DNS-tjänsten krävs för varje uppsättning virtuella datorer.

![Grupp med Virtuella datorer som representerar en demo eller utbildning scenariot i en Azure-molntjänst][planning-guide-figure-200]

Det är viktigt att tänka på att virtuella datorer i varje typ måste distribueras parallellt, där de Virtuella datorerna i varje uppsättningen är desamma.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Flera platser – distribution av en eller flera SAP-datorer till Azure, utan krav på att integreras helt i det lokala nätverket
![VPN-anslutningar med plats-till-plats-anslutning (mellan lokala)][planning-guide-figure-300]

Det här scenariot är ett scenario med flera platser med många möjlig distribution mönster. Det kan beskrivas som kör vissa delar av SAP landscape lokalt och andra delar av SAP liggande på Azure. Alla aspekter av det faktum som en del av SAP-komponenter körs på Azure ska vara transparenta för slutanvändare. Därför fungerar SAP Transport korrigering System (STM), RFC kommunikation, utskrift, säkerhet (till exempel SSO), etc. sömlöst för SAP-system som körs på Azure. Men mellan lokala-scenariot beskriver även ett scenario där fullständig SAP-landskap som körs i Azure med kundens domän och DNS utökad till Azure.

> [!NOTE]
> Det här är det distributionsscenario som stöds för att köra produktiva SAP-system.
>
>

Läs [i den här artikeln] [ vpn-gateway-create-site-to-site-rm-powershell] för mer information om hur du ansluter ditt lokala nätverk till Microsoft Azure

> [!IMPORTANT]
> När vi pratar om mellan lokala scenarier mellan Azure och lokala distributioner, tittar vi på Granulariteten för hela SAP-system. Scenarierna som *stöds inte* mellan flera platser scenarier är:
>
> * Kör olika lager av SAP-program i olika distributionsmetoder. Till exempel köra DBMS layer lokala platser, men SAP-programnivån i virtuella datorer som distribueras som virtuella Azure-datorer eller vice versa.
> * Vissa komponenter i ett SAP-lager i Azure och vissa lokalt. Till exempel dela upp instanser av SAP-programnivån mellan lokala och virtuella Azure-datorer.
> * Distribution av virtuella datorer som kör SAP-instanser av ett system över flera Azure-regioner stöds inte.
>
> Orsaken till dessa begränsningar är krav för ett nätverk med låg latens höga prestanda i ett SAP-system, särskilt mellan programinstanserna och DBMS-lager med ett SAP-system.
>
>

### <a name="supported-os-and-database-releases"></a>OS- och databas-versioner som stöds
* Microsoft-serverprogramvara stöds för Azure Virtual Machine-tjänsterna visas i den här artikeln: <http://support.microsoft.com/kb/2721672>.
* Operativsystem som system versioner, database system-versioner som stöds på Azure Virtual Machine-tjänsterna tillsammans med SAP-program finns dokumenterade i SAP-kommentar [1928533].
* SAP-program och versioner som stöds på Azure Virtual Machine-tjänsterna som finns dokumenterade i SAP-kommentar [1928533].
* Endast 64-bitars-avbildningar stöds om du vill köra som gäst virtuella datorer i Azure för SAP-scenarier. Därför stöds endast 64-bitars SAP-program och databaser.

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure Virtual Machine-tjänsterna
Microsoft Azure-plattformen är en internetanpassade plattform för molntjänster som värd och drivs i Microsoft-datacenter. Plattformen har Microsoft Azure VM-tjänster (infrastruktur som en tjänst eller IaaS) och en uppsättning omfattande plattform som en tjänst (PaaS)-funktioner.

Azure-plattformen minskar behovet för direkta teknik och infrastruktur inköp. Det förenklar underhålla och driva program genom att tillhandahålla på begäran beräkning och lagring för att vara värd för, skala och hantera webbprogram och anslutna program. Hantering av infrastruktur är automatiserade med en plattform som är utformat för hög tillgänglighet och dynamisk skalning som passar behoven för användning med alternativet att en användningsbaserad prismodell.

![Placeringen av Microsoft Azure Virtual Machine-tjänsterna][planning-guide-figure-400]

Med Azure VM-tjänster, Microsoft för att du kan distribuera anpassade server-avbildningar till Azure som IaaS-instanser (se bild 4). De virtuella datorerna i Azure baseras på Hyper-V virtuella hårddiskar (VHD) och kan köra olika operativsystem som Gästoperativsystem.

Från en operational perspektiv erbjuder tjänsten Azure Virtual Machine liknande som virtuella datorer som distribueras lokalt. Men har det stor fördel som du inte behöver köpa in, administrera och hantera infrastrukturen. Utvecklare och administratörer har fullständig kontroll över en angiven operativsystemavbildning i dessa virtuella datorer. Administratörer kan logga in via fjärranslutning på de virtuella datorerna att utföra underhåll och felsöka aktiviteter samt distributionsuppgifter för programvara. Avseende distribution är de enda begränsningarna storlekar och funktionerna i virtuella Azure-datorer. Dessa storlekar kanske inte så bra detaljerad konfiguration eftersom det kan göras lokalt. Det finns olika typer av virtuella datorer som representerar en kombination av:

* Antal virtuella processorer
* Minne
* Antalet virtuella hårddiskar som kan kopplas
* Nätverks- och bandbredder

Storlek och begränsningar i olika storlekar för olika virtuella datorer som erbjuds kan ses i en tabell i [i den här artikeln (Linux)] [ virtual-machines-sizes-linux] och [i den här artikeln (Windows)] [virtual-machines-sizes-windows].

Inte alla olika VM-serien tillhandahålls i var och en av de Azure-regioner (för Azure-regioner finns i nästa kapitel). Tänk också att inte alla virtuella datorer eller VM-serien är certifierade för SAP.

> [!IMPORTANT]
> För användning av SAP NetWeaver-baserade program, visas endast del av VM-typer och konfigurationer i SAP-kommentar [1928533] stöds.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-regioner
Virtuella datorer distribueras i så kallade *Azure-regionerna*. En Azure-Region kanske en eller flera datacenter som finns nära varandra. För de flesta geopolitiska regioner i världen kan har Microsoft minst två Azure-regioner. Till exempel i Europa det finns ett Azure-Region för *Nordeuropa* och en av *Västeuropa*. Dessa två Azure-regioner inom en geopolitisk region avgränsas med tillräckligt betydande avståndet så att naturliga eller tekniska katastrofer inte påverkar både Azure-regioner i samma geopolitiska region. Eftersom Microsoft är stadigt att bygga ut nya Azure-regioner i olika geopolitiska regioner globalt, hur många av dessa regioner ökande stadigt och nått antalet 20 Azure-regioner med ytterligare regioner har tillkännagivits redan från och med december 2015. Du som kund kan distribuera SAP-system i alla dessa regioner, inklusive två Azure-regionerna i Kina. Aktuell uppdaterad information om Azure-regioner finns i den här webbplatsen: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure VM-koncept
Microsoft Azure tillhandahåller en infrastruktur som en tjänst (IaaS)-lösning till värd virtuella datorer med liknande funktioner som en lokal virtualiseringslösning. Du kan skapa virtuella datorer från Azure Portal, PowerShell eller CLI, som också ger distribution och hanteringsfunktioner.

Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du kan använda samma mall för att upprepade gånger distribuera ditt program i varje fas av programmets hela livscykel.

Mer information om hur du använder Resource Manager-mallar finns här:

* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI] [../../linux/create-ssh-secured-vm-from-template.md]
* [Hantera virtuella datorer med Azure Resource Manager och PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

En annan intressant funktion är möjligheten att skapa avbildningar från virtuella datorer, där du kan förbereda vissa databaser som du kan snabbt distribuera instanser av virtuella datorer som uppfyller dina krav.

Mer information om hur du skapar avbildningar från virtuella datorer finns i [i den här artikeln (Linux)] [ virtual-machines-linux-capture-image-resource-manager] och [i den här artikeln (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Feldomäner
Feldomäner representerar en fysisk enhet med fel, nära förknippat med den fysiska infrastrukturen i datacenter, och ett fysiskt blad eller ett rack kan ses som en Feldomän, men det finns ingen direkt mappning mellan två.

När du distribuerar flera virtuella datorer som en del av en SAP-system i Microsoft Azure Virtual Machine-tjänsterna, du kan påverka Azure-Infrastrukturkontrollanten att distribuera ditt program i olika Feldomäner, vilket uppfyller kraven i den Microsoft Azure serviceavtal. Distribution av Feldomäner i en Azure-skalenhet (insamling av hundratals Compute-noder eller lagringsnoder och nätverk) eller tilldelningen av virtuella datorer till en specifik Feldomän är dock något som du inte har direkt kontroll. För att dirigera Azure-infrastrukturkontrollanten att distribuera en uppsättning virtuella datorer via olika Feldomäner, måste du tilldela en Azure-Tillgänglighetsuppsättning till de virtuella datorerna vid tidpunkten för distribution. Mer information om Azure Tillgänglighetsuppsättningar finns i kapitlet [Azure-Tillgänglighetsuppsättningar] [ planning-guide-3.2.3] i det här dokumentet.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uppgradera domäner
Uppgraderingsdomäner representerar en logisk enhet som hjälper dig för att avgöra hur en virtuell dator i ett SAP-system som består av SAP-instanser som körs på flera virtuella datorer, uppdateras. När en uppgradering sker, hela Microsoft Azure processen för att uppdatera dessa Uppgraderingsdomäner i taget. Genom att sprida virtuella datorer vid tidpunkten för distribution över olika uppgradera domäner, kan du skydda din SAP-system delvis från potentiella driftstopp. Du måste ange ett specifikt attribut vid tidpunkten för distribution av varje virtuell dator för att tvinga Azure för att distribuera de virtuella datorerna i ett SAP-system som sprider sig över olika uppgradera domäner. Liknar Feldomäner, en Azure-skalenhet indelade i flera uppgradera domäner. För att dirigera Azure-infrastrukturkontrollanten att distribuera en uppsättning virtuella datorer via olika uppgradera domäner, måste du tilldela en Azure-Tillgänglighetsuppsättning till de virtuella datorerna vid tidpunkten för distribution. Mer information om Azure Tillgänglighetsuppsättningar finns i kapitlet [Azure-Tillgänglighetsuppsättningar] [ planning-guide-3.2.3] nedan.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azures Tillgänglighetsuppsättningar
Azure virtuella datorer i en Azure-Tillgänglighetsuppsättning distribueras av Azure-Infrastrukturkontrollanten över olika fel- och uppgradera domäner. Syftet med distributionen via olika fel- och uppgradera domäner är att förhindra att alla virtuella datorer i ett SAP-system avslutas när det gäller infrastrukturunderhåll eller ett fel i en Feldomän. Som standard ingår virtuella datorer inte i en Tillgänglighetsuppsättning. Medverkan av en virtuell dator i en Tillgänglighetsuppsättning definieras vid tidpunkten för distribution eller senare på vid en omkonfiguration omdistribution av en virtuell dator.

För att förstå begreppet Tillgänglighetsuppsättningar i Azure och det sätt som Tillgänglighetsuppsättningar är relaterade till fel- och uppgradera domäner kan läsa [i den här artikeln][virtual-machines-manage-availability]

För att definiera tillgänglighetsuppsättningar för Azure Resource Manager via en json-mall, se [rest-api-specifikationer](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) och Sök efter ”tillgänglighet”.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Lagring: Microsoft Azure Storage- och Datadiskar
Microsoft Azure Virtual Machines använder olika lagringstyper. När du implementerar SAP på Azure Virtual Machine-tjänsterna, är det viktigt att förstå skillnaderna mellan dessa två typer av lagring:

* Icke-permanent och temporär lagring.
* Beständig lagring.

Virtuella Azure-datorer erbjuder icke-beständiga diskar när en virtuell dator har distribuerats. Allt innehåll på dessa enheter kommer att rensas vid omstart av en virtuell dator. Det är därför en tanke på att data och log/gör om-filer med databaser under inga omständigheter finns på dessa icke bestående enheter. Det kan finnas undantag för några av databaserna, om enheterna icke bestående skulle kunna vara lämplig för tempdb och temp registerutrymmen. Men Undvik att använda dessa enheter för virtuella datorer i A-serien eftersom dessa icke bestående enheter är begränsade i dataflöde med den Virtuella datorserien. Mer information finns i artikeln [förstå den temporära enheten på Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> Enhet D:\ i en Azure VM är en icke bestående enhet som backas upp av vissa lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehållet på D:\ enheten går förlorad när den virtuella datorn startas om eftersom den är icke-beständig. Genom att ”ändringar”, till exempel filer lagras, kataloger som skapas, program som har installerats, osv.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuella Linux Azure-datorer montera automatiskt en enhet på /mnt/resource som är en icke bestående enhet som backas upp av lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehåll i /mnt/resource går förlorade när den virtuella datorn startas om eftersom den är icke-beständig. Av alla ändringar, till exempel filer lagras, kataloger som skapas, program som har installerats, osv.
> 
> 

- - -

Microsoft Azure Storage tillhandahåller bestående lagring och vanliga skyddsnivåer och redundans som visas på SAN-lagring. Diskar baserat på Azure Storage är virtuell hårddisk (VHD) finns i Azure Storage-tjänster. Lokal OS-Disk (C: Windows\, Linux/dev/sda1) lagras på Azure-lagring och ytterligare volymer/diskar som monterats till den virtuella datorn lagras där för.

Det är möjligt att ladda upp en befintlig virtuell Hårddisk från en lokal plats eller skapa tomma värden från i Azure och koppla dem till distribuerade virtuella datorerna.

När du skapar eller laddar upp en virtuell Hårddisk till Azure Storage, går det att montera och koppla dem till en befintlig virtuell dator och kopiera befintlig (demonterats) virtuell Hårddisk.

Data och ändringar i de är säkra när du startar om och återskapa en virtuell datorinstans som de virtuella hårddiskarna har sparats. Även om en instans tas bort kan hålla dig säker dessa VHD: er och kan distribueras eller när det gäller icke-operativsystemdiskar kan monteras på andra virtuella datorer.

Mer information om Azure Storage finns här:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard-lagring
Azure standardlagring var typ av lagring som är tillgänglig när Azure IaaS släpptes. Det fanns IOPS kvoter tillämpas per enskild disk. Svarstiden var inte i samma klass som SAN/NAS-enheter som normalt distribuerat för avancerade SAP-datorer som finns på plats. Dock Azure standardlagring visade sig räcker för många hundratals SAP-system under tiden distribueras i Azure.

Diskar som är lagrade på Azure Standard Storage-konton debiteras baserat på de faktiska data som lagras, mängden lagringstransaktioner och utgående dataöverföring för valt alternativ för dataredundans. Många diskar kan skapas på maximalt 1TB i storlek, men så länge de är tom är gratis. Om du sedan fyller med 100GB vardera på en virtuell Hårddisk, debiteras du för att lagra 100GB och inte för den virtuella Hårddisken har skapats med liten storlek.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
Azure Premium Storage har introducerades med målet att tillhandahålla:

* Bättre i/o-svarstid.
* Bättre genomströmning.
* Mindre variationer i i/o-svarstider.

För detta ändamål har många ändringar gjorts av två viktigaste är:

* Användning av SSD-diskar i Azure Storage-noder
* En ny läsa uppbackning av den lokala SSD-DISKEN på en Azure-beräkningsnod

I motsatt standardlagring där funktioner inte ändras beroende på storleken på disken (eller virtuell Hårddisk) Premium Storage har för närvarande tre olika disk kategorier som visas i den här artikeln: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Du ser att IOPS-disken och disk dataflöde/disk är beroende av kategorin storlek på diskarna

Kostnad basen för Premium Storage är inte den faktiska datavolym som lagras i dessa diskar, men kategorin storleken för en disk, oberoende av mängden data som lagras i disken.

Du kan också skapa diskar på Premium-lagring som inte direkt mappning till storlek kategorier visas. Detta kan vara fallet, särskilt när du kopierar diskar från standardlagring till Premium Storage. I sådana fall utförs en mappning till nästa största Premium Storage disk alternativ.

De flesta Azure VM-familjer certifierad via SAP är fungerar med Premium Storage och eller en blandning mellan Azure standard och Premium Storage.

Om du checkar ut en del av de virtuella datorerna i DS-serien i [i den här artikeln (Linux)] [ virtual-machines-sizes-linux] och [i den här artikeln (Windows)][virtual-machines-sizes-windows], Tänk på att Det finns data volym begränsningar för Premium Storage-diskar på Granulariteten för VM-nivå. Olika DS-serien eller GS-seriens virtuella datorer måste du också ha olika begränsningar när det kommer till antalet datadiskar som kan monteras. Dessa gränser dokumenteras i artikeln som nämns ovan samt. Men i princip innebär det att du, om du exempelvis monterar 32 x P30 diskar till en enda DS14 virtuell dator inte kan få 32 x maximalt dataflöde för en P30-disk. I stället begränsar maximalt dataflöde på VM-nivå som beskrivs i artikeln dataflödet.

Mer information om Premium Storage finns här: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>


#### <a name="azure-storage-accounts"></a>Azure Storage-konton
När du distribuerar tjänster eller virtuella datorer i Azure, kan distribution av virtuella hårddiskar och VM-avbildningar ordnas i enheter som kallas Azure Storage-konton. När du planerar en Azure-distribution, måste du noga överväga begränsningarna med Azure. Det finns ett begränsat antal Storage-konton per Azure-prenumeration på ena sidan. Även om varje Azure-Lagringskonto kan innehålla ett stort antal VHD-filer, finns det en fast gräns på det totala antalet IOPS per Lagringskonto. När du distribuerar hundratals SAP virtuella datorer med DBMS system skapar betydande i/o-anrop, rekommenderas att distribuera hög IOPS DBMS-datorer mellan flera Azure Storage-konton. Måste du vara noggrann inte att överskrida den aktuella gränsen för Azure Storage-konton per prenumeration. Eftersom lagringen är en viktig del av databasen distributionen för en SAP-system kan detta begrepp beskrivs mer ingående i den refererade redan [DBMS Distributionsguide][dbms-guide].

Mer information om Azure Storage-konton finns i [i den här artikeln][storage-scalability-targets]. Du Tänk läser den här artikeln, på att det finns skillnader i begränsningar mellan Azure Standard Storage-konton och Premium Storage-konton. Viktiga skillnader är mängden data som kan lagras i ett Lagringskonto. I Standard-lagring är volymen en omfattning som är större än med Premium Storage. Å andra sidan Standard Storage-konto är kraftigt begränsad i IOPS (se kolumnen **totala begära antalet**), medan Azure Premium Storage-konto har ingen sådan begränsning. Vi kommer att diskutera information och resultatet av dessa skillnader när det handlar om distributionerna av SAP-system, särskilt DBMS-servrar.

Du har möjlighet att skapa olika behållare i syfte att ordna och kategorisera olika virtuella hårddiskar i ett Lagringskonto. De här behållarna används för att till exempel separata virtuella hårddiskar på olika virtuella datorer. Det finns inga prestandaskäl in med bara en behållare eller flera behållare under en enda Azure Storage-konto.

Namn på en virtuell Hårddisk följer följande namngivning anslutning som måste ange ett unikt namn för den virtuella Hårddisken i Azure i Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Strängen ovan måste identifiera den virtuella Hårddisken som lagras på Azure Storage.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Hanterade diskar
Hanterade diskar är en ny resurstyp i Azure Resource Manager som kan användas i stället för virtuella hårddiskar som lagras i Azure Storage-konton. Hanterade diskar anpassas automatiskt till Tillgänglighetsuppsättningen för den virtuella datorn som de är kopplade till, och därför att öka tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översiktsartikeln](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Vi rekommenderar att du använder hanterade diskar, eftersom de förenkla distributionen och hanteringen av dina virtuella datorer.
SAP stöder för närvarande endast Premium Managed Disks. Mer information finns i SAP-kommentar [1928533].


#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage återhämtning
Microsoft Azure Storage lagrar bas-VHD (med OS) och anslutna diskar eller Blobar på minst tre separata lagringsnoder. Detta kallas för lokalt Redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure. 

Det finns flera mer redundans metoder, som beskrivs i artikeln [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Från och med Azure Premium Storage, vilket är den rekommenderade typen av lagring för DBMS virtuella datorer och diskar som lagrar databasen och log/gör om, är den enda tillgängliga metoden LRS. Därför måste du konfigurera databasen metoder, som SQL Server Always On, Oracle Data Guard eller HANA System Replication att aktivera databasreplikering för data i en annan Azure-Region eller en annan Azure-Tillgänglighetszon.


> [!NOTE]
> Användning av Geo-Redundant lagring som är tillgänglig med Azure standardlagring rekommenderas inte för DBMS-distributioner, eftersom det mycket hög prestanda och respekterar inte skriva ordningen mellan olika virtuella hårddiskar som är kopplade till en virtuell dator. Fakta för inte respekterar skrivning ordningen mellan olika virtuella hårddiskar har en hög potentiell till hamnar i inkonsekventa databaser på målsidan replikering om databas och logg/gör om filer är fördelade på flera virtuella hårddiskar (som huvudsakligen fallet) allt på käll-VM på klientsidan.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure-nätverk
Microsoft Azure tillhandahåller en nätverksinfrastruktur, vilket gör att mappningen för alla scenarier som vi vill att upptäcka med SAP-program. Funktionerna är:

* Åtkomst från utsidan, direkt till de virtuella datorerna via Windows Terminal Services eller ssh/VNC
* Åtkomst till tjänster och specifika portar som används av program inom de virtuella datorerna
* Intern kommunikation och namnmatchning mellan en grupp virtuella datorer som distribueras som virtuella Azure-datorer
* Flera platsanslutningar mellan en kunds lokala nätverk och Azure-nätverk
* Mellan Azure-Region eller data center anslutning mellan Azure webbplatser

Mer information finns här: <https://azure.microsoft.com/documentation/services/virtual-network/>

Det finns många olika möjligheter att konfigurera namn och IP-lösning i Azure. I detta dokument, endast molnbaserad scenarier som förlitar sig på i stället för att använda Azure DNS (till skillnad från att definiera en egen DNS-tjänst). Det finns också en ny Azure DNS-tjänst, som kan användas i stället för att konfigurera DNS-servern. Mer information finns i [i den här artikeln] [ virtual-networks-manage-dns-in-vnet] på [den här sidan](https://azure.microsoft.com/services/dns/).

Scenarier med flera platser vi lita på faktumet som lokalt AD/OpenLDAP/DNS har utökats via VPN eller privat anslutning till Azure. För vissa scenarier som beskrivs här, det kan vara nödvändigt att ha en replik av en AD/OpenLDAP installeras i Azure.

Eftersom nätverk och namnmatchning är en viktig del av databasen distributionen för en SAP-system, detta begrepp beskrivs mer ingående i den [DBMS Distributionsguide][dbms-guide].

##### <a name="azure-virtual-networks"></a>Azure Virtual Networks
Du kan bygga upp ett Azure Virtual Network, för att definiera adressintervallet för de privata IP-adresser som allokerats av Azure DHCP-funktioner. I scenarier med flera platser tilldelas IP-adressintervall som definierats fortfarande använder DHCP av Azure. Domän-namnmatchning görs en lokal (förutsatt att de virtuella datorerna är en del av en lokal domän) och därför kan matcha adresser utöver olika Azure-molntjänster.

Varje virtuell dator i Azure måste vara ansluten till ett virtuellt nätverk.

Mer information finns i [i den här artikeln] [ resource-groups-networking] på [den här sidan](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> När en virtuell dator distribueras som standard kan inte du ändra den virtuella nätverkskonfigurationen. TCP/IP-inställningar måste finnas kvar i Azure DHCP-server. Standardbeteendet är dynamisk IP-tilldelning.
>
>

MAC-adressen för det virtuella nätverkskortet kan ändras, till exempel efter storleksändring och gästen Windows eller Linux OS tar upp nya nätverkskortet och automatiskt använder DHCP för att tilldela IP-adress och DNS-adresser i det här fallet.

##### <a name="static-ip-assignment"></a>Statisk IP-tilldelning
Det går att tilldela fast eller reserverade IP-adresser till virtuella datorer i Azure Virtual Network. Köra de virtuella datorerna i Azure Virtual Network öppnas ett bra alternativ för att använda den här funktionen om behövs eller som krävs för vissa scenarier. IP-adresstilldelning förblir giltigt i hela förekomsten av den virtuella datorn, oberoende av om den virtuella datorn är igång eller avstängning. Därför kan behöva du ta det totala antalet virtuella datorer (datorer som körs och stoppad) hänsyn till när du definierar IP-adressintervall för det virtuella nätverket. IP-adressen förblir tilldelad antingen tills den virtuella datorn och dess nätverksgränssnittet har tagits bort eller IP-adressen blir ta bort tilldelad igen. Mer information finns i [i den här artikeln][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure innebär att enskilda virtuella nätverkskort. Du bör inte tilldela statiska IP-adresser i gästoperativsystemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup-tjänsten är beroende av faktumet att vid minst det primära virtuella nätverkskortet är inställd på DHCP- och inte på statiska IP-adresser. Se även dokumentet [felsöka Azure VM backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Om du vill tilldela flera statiska IP-adresser till en virtuell dator måste du tilldela flera virtuella nätverkskort till en virtuell dator.
>
>

##### <a name="multiple-nics-per-vm"></a>Flera nätverkskort per virtuell dator
Du kan definiera flera virtuella nätverkskort (vNIC) för en Azure virtuell dator. Möjlighet att ha flera virtuella nätverkskort kan du börja konfigurera nätverkstrafik dirigeras uppdelning där, till exempel klienttrafiken dirigeras via ett virtuellt nätverkskort och backend-trafik via ett andra virtuellt nätverkskort. Beroende på vilken typ av virtuell dator det finns olika begränsningar när det kommer till antalet virtuella nätverkskort. Mer information, funktioner och begränsningar finns i följande artiklar:

* [Skapa en virtuell Windows-dator med flera nätverkskort][virtual-networks-multiple-nics-windows]
* [Skapa en Linux-VM med flera nätverkskort][virtual-networks-multiple-nics-linux]
* [Distribuera flera NIC virtuella datorer med hjälp av en mall][virtual-network-deploy-multinic-arm-template]
* [Distribuera flera NIC virtuella datorer med hjälp av PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Distribuera flera NIC virtuella datorer med hjälp av Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Plats-till-plats-anslutning
Flera platser är virtuella datorer i Azure och lokalt som är kopplad till en transparent och permanenta VPN-anslutning. Det förväntas bli mönstret vanligaste SAP-distribution i Azure. Antas att operativa metoder och processer med SAP-instanser i Azure bör fungera transparent. Det här innebär att du ska kunna skriva ut från de här systemen samt använda SAP Transport Management System (Proportionerlig) att transportera ändras från ett utvecklingssystem i Azure till ett testsystem som är distribuerat lokalt. Mer dokumentation om plats-till-plats finns i [i den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-Tunnel-enhet
För att skapa en plats-till-plats-anslutning (lokala Datacenter till Azure-datacenter), som du behöver hämta och konfigurera en VPN-enhet eller använda Routing and Remote Access Service (RRAS) som introducerades som en programvarukomponent med Windows Server 2012.

* [Skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med hjälp av PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Om VPN-enheter för plats-till-plats VPN-Gateway-anslutningar][vpn-gateway-about-vpn-devices]
* [VPN-Gateway vanliga frågor och svar][vpn-gateway-vpn-faq]

![Plats-till-plats-anslutning mellan lokala och Azure][planning-guide-figure-600]

Ovanstående bild visar två Azure-prenumerationer har IP-adress underintervall reserverade för användning i virtuella nätverk i Azure. Är upprätta en anslutning från lokala nätverk till Azure via VPN.

#### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN
Punkt-till-plats-VPN måste varje klientdatorn ansluter med sin egen VPN till Azure. Vi tittar på, punkt-till-plats-anslutning är inte praktiskt för SAP-scenarier. Därför kan hänvisningar inga ytterligare till punkt-till-plats VPN-anslutning.

Mer information finns här
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Flera plats-VPN
Azure erbjuder också Nuförtiden risken för att skapa flera plats-VPN-anslutning för en Azure-prenumeration. Tidigare var en enda prenumeration begränsad till en plats-till-plats VPN-anslutning. Den här begränsningen försvann med flera plats-VPN-anslutningar för en enskild prenumeration. Detta gör det möjligt att använda fler än en Azure-Region för en viss prenumeration via mellan lokala konfigurationer.

Mer dokumentation finns [i den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]



#### <a name="vnet-to-vnet-connection"></a>Anslutning mellan virtuella nätverk
Med hjälp av VPN för flera platser, måste du konfigurera ett separat virtuellt Azure-nätverk i var och en av regionerna. Du har dock ofta kravet att programkomponenterna i olika regioner ska kommunicera med varandra. Den här kommunikationen ska vi rekommenderar inte att dirigeras från en Azure-Region till både lokala och därifrån till den andra Azure-Region. Azure erbjuder kortkommando, möjlighet att konfigurera en anslutning från ett virtuellt Azure-nätverk i en region till en annan Azure-nätverk finns i en annan region. Den här funktionen kallas VNet-till-VNet-anslutning. Mer information om den här funktionen finns här: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Privat anslutning till Azure ExpressRoute
Microsoft Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och antingen kundens lokala infrastruktur eller i en samplaceringsmiljö. ExpressRoute erbjuds av olika MPLS (paketväxlat) VPN-leverantörer eller andra leverantörer. ExpressRoute-anslutningar går inte via offentligt Internet. ExpressRoute-anslutningar är högre säkerhet, bättre tillförlitlighet via flera parallella kretsar, snabbare hastigheter och kortare svarstider än vanliga anslutningar via Internet.

Hitta mer information om Azure ExpressRoute och erbjudanden här:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Expressroute kan flera Azure-prenumerationer via en ExpressRoute-krets som dokumenteras här

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Tvingad tunneltrafik vid flera platser
Du måste se till att internetproxyinställningarna komma att distribueras för alla användare i dessa virtuella datorer samt för virtuella datorer ansluta till den lokala domäner via plats-till-plats, punkt-till-plats eller ExpressRoute. Som standard programvara som körs på dessa virtuella datorer eller användare som använder en webbläsare för att ansluta till internet inte skulle gå igenom företagets proxy, men ska ansluta direkt via Azure till internet. Men även Proxyinställningen är inte en 100%-lösning för att dirigera trafik via proxy företagets eftersom det är ansvar i programvara och tjänster för att söka efter proxyn. Om programvara som körs på den virtuella datorn inte gör detta eller en administratör ändrar inställningarna kan trafik till Internet kan vara detoured igen direkt via Azure till Internet.

För att undvika sådana en direkt Internetanslutning, kan du konfigurera Tvingad tunneltrafik med plats-till-plats-anslutning mellan lokala och Azure. Detaljerad beskrivning av funktionen Tvingad tunneltrafik publiceras här <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Framtvingad Tunneling med ExpressRoute aktiveras av kunder annonserar en standardväg via ExpressRoute BGP-peeringsessioner.

#### <a name="summary-of-azure-networking"></a>Översikt över Azure-nätverk
I det här kapitlet innehåller många viktiga saker om Azure-nätverk. Här följer en sammanfattning av de viktigaste aspekterna:

* Azure-nätverk kan du placera en nätverksinfrastruktur i din Azure-distribution. Virtuella nätverk kan isoleras mot varandra eller med hjälp av Nätverkssäkerhetsgrupper trafik mellan virtuella nätverk kan kontrolleras.
* Azure-nätverk kan användas för att tilldela IP-adressintervall till virtuella datorer eller tilldela fasta IP-adresser till virtuella datorer
* Om du vill konfigurera en plats-till-plats eller punkt-till-plats-anslutning måste du först skapa ett Azure Virtual Network
* När en virtuell dator har distribuerats, är det inte längre går att ändra det virtuella nätverket som tilldelas den virtuella datorn

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvoter i Azure Virtual Machine-tjänsterna
Vi behöver vara tydlig med det faktum att lagrings- och infrastrukturen delas mellan virtuella datorer som kör en mängd olika tjänster i Azure-infrastrukturen. Och precis som för kundens eget datacenter, behöver etablera på några av infrastrukturresurser utförs i utsträckning. Microsoft Azure-plattformen använder disk, processor, nätverk och andra kvoter för att begränsa resursförbrukningen och att bevara konsekventa och deterministisk prestanda.  Olika VM-typer (A5, A6 osv.) har olika kvoter för antalet diskar, processor, RAM-minne, och nätverk.

> [!NOTE]
> Processor och minne resurser av VM-typer som stöds av SAP är förallokerade på värdnoder. Det innebär att resurser på värden är tillgängliga som definieras av typ av virtuell dator när den virtuella datorn har distribuerats.
>
>

När du planerar och ändra storlek på SAP på Azure-lösningar, måste kvoter för varje virtuell datorstorlek beaktas. VM-kvoter beskrivs [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Kvoter som beskrivs representerar teoretisk högsta värden.  Gränsen på IOPS per disk kan uppnås med små IOs (8kb), men de kan eventuellt inte uppnås med stora IOs (1Mb).  IOPS-gränsen tillämpas på Granulariteten för enskild disk.

Som en ungefärlig beslutsträd att bestämma om ett SAP-system som passar in i Azure Virtual Machine-tjänsterna och dess funktioner eller om ett befintligt system måste konfigureras på olika sätt för att distribuera system på Azure, kan du använda beslutsträdet nedan:

![Beslutsträd för att bestämma möjligheten att distribuera SAP på Azure][planning-guide-figure-700]

**Steg 1**: den viktigaste informationen är att börja med SAP-krav för ett visst SAP-system. SAP-krav måste delas in den DBMS och SAP-program delen, även om SAP-system är redan distribueras lokalt i en nivå 2-konfiguration. För befintliga system SAP relaterad till maskinvara som används ofta fastställt eller beräknat baserat på befintliga SAP prestandamått. Resultaten finns här: <https://sap.com/about/benchmark.html>.
För nyligen distribuerade SAP-system, bör du har gått igenom en storlek Övning som bestämmer SAP-kraven i systemet.
Se även den här bloggen och bifogade dokumentet för SAP storlek på Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Steg 2**: för befintliga system i/o-volym och i/o-åtgärder per sekund på DBMS-servern ska mätas. För nyligen planerad system bör storlek Övning för det nya systemet även ge grov idéer av i/o-kraven på DBMS-sida. Om du är osäker, måste du så småningom att genomföra en Proof of Concept.

**Steg 3**: jämför SAP-krav för DBMS-server med SAP kan ge olika VM-typer av Azure. Information om SAP Azure VM-typer dokumenteras i SAP-kommentar [1928533]. Fokus bör finnas på DBMS VM först eftersom databasen lagret är lagret i ett SAP NetWeaver-system som inte skalar ut i flesta distributioner. SAP-programnivån kan däremot skalas ut. Om ingen av SAP stöds Azure VM-typer kan leverera de nödvändiga SAP, arbetsbelastningen för planerat SAP-system kan inte köras på Azure. Antingen måste du distribuera systemet lokalt eller du behöver ändra volymen arbetsbelastning för systemet.

**Steg 4**: enligt beskrivningen [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows], Azure tillämpar en kvot för IOPS per disk oberoende om du använder standardlagring eller Premium Storage. Beroende på vilken typ av virtuell dator, hur många datadiskar som kan monteras varierar. Därför kan du beräkna en högsta IOPS-antalet som kan uppnås med var och en av de olika typerna av virtuell dator. Beroende på databasen filens layout, du kan stripe-diskar för att bli en volym i gästoperativsystemet. Men om den aktuella IOPS-volymen för ett distribuerat SAP-system överskrider gränsvärdena största VM-typ för Azure och om det finns ingen risk för att kompensera med mer minne, kan arbetsbelastningen för SAP-system påverkas allvarligt. I sådana fall kan når du en punkt där inte bör du distribuera system på Azure.

**Steg 5**: särskilt i SAP-system som är distribuerade lokalt i nivå 2-konfigurationer, är risken att systemet kan behöva konfigureras på Azure i en konfiguration med 3 nivåer. I det här steget behöver du kontrollera om det finns en komponent i SAP-programnivån, vilket inte skalas ut och som inte skulle passa in i processor och minne resurser som de olika typerna av virtuell dator i Azure erbjuder. Om det verkligen finns en sådan komponent, kan inte SAP-system och dess arbetsbelastning distribueras till Azure. Men om du kan skala ut SAP-programkomponenter i flera virtuella Azure-datorer, systemet kan distribueras till Azure.

**Steg 6**: om DBMS och SAP layer programkomponenter kan köras i virtuella Azure-datorer, konfigurationen måste definieras med avseende på:

* Antalet virtuella Azure-datorer
* VM-typer för enskilda komponenter
* Antalet virtuella hårddiskar i DBMS VM att tillhandahålla tillräckligt med IOPS

## <a name="managing-azure-assets"></a>Hantera Azure-tillgångar
### <a name="azure-portal"></a>Azure Portal
Azure-portalen är ett av tre gränssnitt för att hantera Virtuella Azure-distributioner. De grundläggande hanteringsåtgärder, som distribuerar virtuella datorer från avbildningar, kan göras via Azure portal. Skapandet av Lagringskonton, virtuella nätverk och andra Azure-komponenter är dessutom också uppgifter som kan hantera utan problem i Azure-portalen. Funktioner som att överföra virtuella hårddiskar från en lokal plats till Azure eller kopiera en virtuell Hårddisk i Azure är dock åtgärder, som kräver verktyg från tredje part eller administration via PowerShell eller CLI.

![Microsoft Azure portal – översikt över virtuella datorer][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Uppgifter för administration och konfiguration för den virtuella instansen är möjliga från Azure Portal.

Förutom att starta om och stänga av en virtuell dator kan du också koppla, koppla bort, och skapa datadiskar för den virtuella datorinstansen, avbilda instansen för förberedelse av avbildningen, och konfigurera storleken på den virtuella datorinstansen.

Azure-portalen tillhandahåller grundläggande funktioner för att distribuera och konfigurera virtuella datorer och många andra Azure-tjänster. Men inte alla tillgängliga funktioner omfattas av Azure-portalen. I Azure-portalen går inte att utföra uppgifter, t.ex.:

* Ladda upp VHD till Azure
* Kopiera virtuella datorer

[comment]: <> (MShermannd TODO vad gäller för automation-tjänsten för SAP-datorer? )
[comment]: <> (MSSedusch distribution av os under tiden möjligt på flera virtuella datorer)
[comment]: <> (MSSedusch även alla typer av automation om distributionen är inte möjligt med Azure portal. Det går inte att uppgifter, till exempel skriptdistributionen av flera virtuella datorer via Azure portal.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Hantering via Microsoft Azure PowerShell-cmdlets
Windows PowerShell är ett kraftfullt och utökningsbart ramverk som har varit brett bland kunder som distribuerar större antal system i Azure. PowerShell-cmdlet: ar kan köras via fjärranslutning efter installationen av PowerShell-cmdletar på en stationär, bärbar dator eller hantering av dedicerade station.

Processen för att aktivera en lokal desktop/bärbara för användningen av Azure PowerShell-cmdlets och hur du konfigurerar dem för användning med Azure-prenumerationerna beskrivs i [i den här artikeln][powershell-install-configure].

Mer detaljerade anvisningar om hur du installerar, uppdatera och konfigurera Azure PowerShell cmdlet: ar finns också i [i det här kapitlet i distributionsguiden för][deployment-guide-4.1].

Kundupplevelsen har hittills varit att PowerShell (PS) är däremot mer kraftfulla verktyget för att distribuera virtuella datorer och skapa anpassade steg i distributionen av virtuella datorer. Alla kunder som kör SAP-instanser i Azure använder PS-cmdletar för att komplettera hanteringsuppgifter de i Azure-portalen eller använder även PS-cmdletar exklusivt för att hantera sina distributioner i Azure. Eftersom Azure-specifika cmdletar delar samma namngivningskonvention som över 2 000 Windows-relaterade cmdlets, är en enkel åtgärd för Windows-administratörer kan använda dessa cmdletar.

Se exempel här: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO Beskriver nya CLI-kommando när testas )
Distribution av Azure Monitoring-tillägg för SAP (finns i kapitlet [Övervakningslösningen för Azure för SAP] [ planning-guide-9.1] i det här dokumentet) är bara kan utföras via PowerShell eller CLI. Därför är det obligatoriskt att installera och konfigurera PowerShell eller CLI när du distribuerar eller administrera ett SAP NetWeaver-system i Azure.  

Azure ger fler funktioner, kommer nya PS-cmdletar som ska läggas till som kräver en uppdatering av cmdletar. Därför är det praktiskt att kontrollera webbplatsen Azure hämta minst en gång i månaden <https://azure.microsoft.com/downloads/> för en ny version av cmdletar. Den nya versionen installeras på den äldre versionen.

En allmän lista över Azure-relaterade PowerShell kommandon finns här: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Hantering via Microsoft Azure CLI-kommandon
För kunder som använder Linux och vill hantera Azure kanske resurser Powershell inte är ett alternativ. Microsoft erbjuder Azure CLI som ett alternativ.
Azure CLI innehåller en uppsättning med öppen källkod plattformsoberoende kommandon för att arbeta med Azure-plattformen. Azure CLI tillhandahåller i stort sett samma funktioner som finns i Azure-portalen.

Information om installation, konfiguration och hur du använder CLI-kommandon för att utföra uppgifter för Azure finns i

* [Installera Azure CLI][xplat-cli]
* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI] [../../linux/create-ssh-secured-vm-from-template.md]
* [Använd Azure CLI för Mac, Linux och Windows med Azure Resource Manager][xplat-cli-azure-resource-manager]

Läs även kapitel [Azure CLI för virtuella Linux-datorer] [ deployment-guide-4.5.2] i den [Distributionsguide] [ planning-guide] om hur du använder Azure CLI för att distribuera Azure-övervakning Tillägg för SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Olika sätt att distribuera virtuella datorer för SAP i Azure
I detta kapitel du lära dig de olika sätten att distribuera en virtuell dator i Azure. I det här kapitlet beskrivs ytterligare förberedelse procedurer, samt hantering av virtuella hårddiskar och virtuella datorer i Azure.

### <a name="deployment-of-vms-for-sap"></a>Distribution av virtuella datorer för SAP
Microsoft Azure erbjuder flera olika sätt att distribuera virtuella datorer och associerade diskar. Det är därför viktigt att förstå skillnaderna eftersom förberedelser för de virtuella datorerna kan variera beroende på vilken distribution. Generellt sett är ta vi en titt på följande scenarier:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk
Du planerar att flytta ett särskilt SAP-system från en lokal plats till Azure. Detta kan göras genom att ladda upp den virtuella Hårddisken som innehåller Operativsystemet, SAP-binärfiler och DBMS binärfiler plus de virtuella hårddiskarna med filer för data och loggfiler för DBMS till Azure. Jämfört med [scenariot #2 nedan][planning-guide-5.1.2], behåller du värdnamnet, SAP-SID och SAP användarkonton i Azure-VM som de har konfigurerats i den lokala miljön. Generalisera avbildningen är därför inte nödvändigt. Se kapitel [förberedelse för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk] [ planning-guide-5.2.1] i det här dokumentet för steg för förberedelse av lokala och överföra ej generaliserad datorer eller virtuella hårddiskar till Azure. Läs kapitel [Scenario 3: flytta en virtuell dator från en lokal plats med en icke-generaliserad Azure i virtuell Hårddisk med SAP] [ deployment-guide-3.4] i den [Distributionsguide] [ deployment-guide] för detaljerade anvisningar för att distribuera sådana en avbildning i Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Distribuera en virtuell dator med en kundspecifika-avbildning
På grund av angivna korrigeringsfilen av din OS- eller DBMS-version måste kan de angivna avbildningarna i Azure Marketplace inte motsvarar dina behov. Därför kan du behöva skapa en virtuell dator med din egen privata OS/DBMS VM-avbildning, som kan användas flera gånger efteråt. För att förbereda en privat avbildning för duplicering, har följande objekt för att anses vara:

- - -
> ![Windows][Logo_Windows] Windows
>
> Läs mer här: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> The Windows-inställningar (t.ex. Windows-SID och värdnamn) måste vara sådant/generaliserad på den lokala virtuella datorn via kommandot sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Följ stegen som beskrivs i följande artiklar för [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], eller [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], för att förbereda en virtuell Hårddisk som ska överföras till Azure.
>
>

- - -
Om du redan har installerat SAP-innehåll i den lokala virtuella datorn (särskilt för nivå 2-system), kan du anpassa inställningar för SAP-system när distributionen av virtuella Azure-datorer via instansen Byt namn på proceduren som stöds av SAP Software etablering Manager (SAP Observera [1619720]). Se kapitel [förberedelse för att distribuera en virtuell dator med en kundspecifika avbildning för SAP] [ planning-guide-5.2.2] och [ladda upp en VHD från en lokal plats till Azure] [ planning-guide-5.3.2]i det här dokumentet för steg för förberedelse av lokala och överföring av en generaliserad virtuell dator till Azure. Läs kapitel [Scenario 2: distribuera en virtuell dator med en anpassad avbildning för SAP] [ deployment-guide-3.3] i den [Distributionsguide] [ deployment-guide] detaljerade anvisningar för att distribuera sådana en bild i Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace
Du vill använda ett Microsoft eller tredje parts tillhandahållits VM-avbildning från Azure Marketplace för att distribuera den virtuella datorn. När du har distribuerat den virtuella datorn i Azure kan följa du samma riktlinjer och verktyg för att installera SAP-program och/eller DBMS i din virtuella dator som du skulle göra i en lokal miljö. Mer detaljerad beskrivning av distribution finns i kapitlet [Scenario 1: distribuera en virtuell dator från Azure Marketplace för SAP] [ deployment-guide-3.2] i den [Distributionsguide] [ deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Förbereda virtuella datorer med SAP för Azure
Du måste se till att de virtuella datorerna och virtuella hårddiskar uppfylla vissa krav innan du laddar upp virtuella datorer till Azure. Det finns mindre skillnader beroende på vilken distributionsmetod som används.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk
En vanlig distributionsmetod är att flytta en befintlig virtuell dator, som kör ett SAP-system från en lokal plats till Azure. Den virtuella datorn och SAP-system i den virtuella datorn ska bara köras i Azure med hjälp av samma värdnamn och förmodligen samma SAP-SID. I det här fallet generaliseras gästen OS den virtuella datorn inte för flera distributioner. Om det lokala nätverket har utökats till Azure (finns i kapitlet [mellan lokala - distribution av en eller flera SAP-datorer till Azure, utan krav på att integreras helt i det lokala nätverket] [ planning-guide-2.2] i det här dokumentet), och sedan även samma domänkonton kan användas i den virtuella datorn som de som användes innan lokalt.

Kraven när du förbereder din egen Azure VM-disken är:

* Den virtuella Hårddisken som innehåller operativsystemet kan ursprungligen ha endast en maximal storlek på 127GB. Den här begränsningen har eliminerats i slutet av mars 2015. Den virtuella Hårddisken som innehåller operativsystemet kan nu vara upp till 1TB i storlek som VHD finns också i Azure Storage.
* Det måste vara i formatet fast virtuell Hårddisk. Dynamiska virtuella hårddiskar eller virtuella hårddiskar i VHDx-format stöds inte ännu i Azure. Dynamiska virtuella hårddiskar kommer att konverteras till statisk VHD: er när du överför den virtuella Hårddisken med PowerShell-kommandon eller CLI
* VHD: er, som är monterade till den virtuella datorn och ska monteras igen i Azure VM behovet av att ha en fast VHD-format samt. Läs [i den här artikeln (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) och [i den här artikeln (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) för storleksbegränsningar för datadiskar. Dynamiska virtuella hårddiskar kommer att konverteras till statisk VHD: er när du överför den virtuella Hårddisken med PowerShell-kommandon eller CLI
* Lägg till en annan lokalt konto med administratörsbehörighet som kan användas av Microsoft-supporten eller som kan tilldelas som kontext för tjänster och program att köras tills den virtuella datorn har distribuerats och lämpligare användare kan användas.
* För att använda en molnbaserad distributionsscenariot (finns i kapitlet [molnbaserad - distributioner av virtuella datorer till Azure utan beroenden på den lokala kundnätverk] [ planning-guide-2.1] i det här dokumentet) i tillsammans med den här distributionsmetoden domänkonton kanske inte fungerar när Azure-Disk har distribuerats i Azure. Detta gäller särskilt för konton som används för att köra tjänster som DBMS eller SAP-program. Därför måste du ersätta sådana domänkonton med lokala konton för virtuell dator och ta bort den lokala domänkonton i den virtuella datorn. Gör att den lokala domänanvändare i avbildningen är inte ett problem när den virtuella datorn har distribuerats i mellan lokala scenariot som beskrivs i kapitlet [mellan lokala - distribution av en eller flera SAP-datorer till Azure, utan krav på att helt integrerat i det lokala nätverket] [ planning-guide-2.2] i det här dokumentet.
* Om domänkonton användes som DBMS inloggningar eller användare som kör system lokala platser och dessa virtuella datorer är avsedda för att distribueras i scenarier med endast molnbaserad, måste domänanvändare som ska tas bort. Du måste se till att den lokala administratören plus en annan virtuell dator lokal användare har lagts till som en inloggning/användare till DBMS som administratörer.
* Lägga till andra lokala konton som de som kan behövas för det specifika distributionsscenariot.

- - -
> ![Windows][Logo_Windows] Windows
>
> I det här scenariot krävs ingen generalisering (sysprep) för den virtuella datorn för att överföra och distribuera den virtuella datorn på Azure.
> Kontrollera att den här enheten D:\ inte används.
> Ange disken automount för anslutna diskar som beskrivs i kapitlet [inställningen automount för anslutna diskar] [ planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> I det här scenariot utan generalisering (waagent-avetablering) för den virtuella datorn för att kunna överföra och distribuera den virtuella datorn på Azure.
> Se till att resursen/mnt/inte används och att alla diskar är monterade via uuid. Kontrollera att posten startprogrammet också återspeglar monterings uuid-baserade för OS-disken.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Förberedelser för att distribuera en virtuell dator med en kundspecifika avbildning för SAP
VHD-filer som innehåller en generaliserad OS lagras i behållare på Azure Storage-konton eller som hanteras diskavbildningar. Du kan distribuera en ny virtuell dator från en sådan avbildning genom att referera till VHD- eller hanterad Disk-avbildning som en källa i din distribution mallfiler enligt kapitel [Scenario 2: distribuera en virtuell dator med en anpassad avbildning för SAP] [ deployment-guide-3.3]av den [Distributionsguide][deployment-guide].

Kraven när du förbereder din egen Azure VM-avbildning är:

* Den virtuella Hårddisken som innehåller operativsystemet kan ursprungligen ha endast en maximal storlek på 127GB. Den här begränsningen har eliminerats i slutet av mars 2015. Den virtuella Hårddisken som innehåller operativsystemet kan nu vara upp till 1TB i storlek som VHD finns också i Azure Storage.
* Det måste vara i formatet fast virtuell Hårddisk. Dynamiska virtuella hårddiskar eller virtuella hårddiskar i VHDx-format stöds inte ännu i Azure. Dynamiska virtuella hårddiskar kommer att konverteras till statisk VHD: er när du överför den virtuella Hårddisken med PowerShell-kommandon eller CLI
* VHD: er, som är monterade till den virtuella datorn och ska monteras igen i Azure VM behovet av att ha en fast VHD-format samt. Läs [i den här artikeln (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) och [i den här artikeln (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) för storleksbegränsningar för datadiskar. Dynamiska virtuella hårddiskar kommer att konverteras till statisk VHD: er när du överför den virtuella Hårddisken med PowerShell-kommandon eller CLI
* Eftersom alla domänanvändare registreras som användare i den virtuella datorn inte kommer att finnas i ett scenario med molnbaserad (finns i kapitlet [molnbaserad - distributioner av virtuella datorer till Azure utan beroenden på den lokala kundnätverk] [ planning-guide-2.1] i det här dokumentet), med hjälp av den angivna domänen konton inte kanske fungerar när avbildningen har distribuerats i Azure-tjänster. Detta gäller särskilt för konton som används för att köra tjänster som DBMS eller SAP-program. Därför måste du ersätta sådana domänkonton med lokala konton för virtuell dator och ta bort den lokala domänkonton i den virtuella datorn. Gör att den lokala domänanvändare i VM-avbildning kanske inte är ett problem när den virtuella datorn har distribuerats i mellan lokala scenariot som beskrivs i kapitlet [mellan lokala - distribution av en eller flera SAP-datorer till Azure, utan krav på att fullständigt integrerat i det lokala nätverket] [ planning-guide-2.2] i det här dokumentet.
* Lägg till en annan lokalt konto med administratörsbehörighet som kan användas av Microsoft-supporten i problem undersökningar eller som kan tilldelas som kontext för tjänster och program att köras tills den virtuella datorn har distribuerats och lämpligare användare kan användas.
* I molnbaserade distributioner och där domänkonton användes som DBMS inloggningar eller användare när du kör system lokala platser, bör användarna tas bort. Du måste se till att den lokala administratören plus en annan virtuell dator lokal användare har lagts till som en inloggning/användare av DBMS som administratörer.
* Lägga till andra lokala konton som de som kan behövas för det specifika distributionsscenariot.
* Om bilden innehåller en installation av SAP NetWeaver och namnbyte av värdnamnet från det ursprungliga namnet distributionspunkten Azure troligtvis, rekommenderar vi att du kopiera de senaste versionerna av SAP Software etablering Manager DVD till mallen. Detta kan du enkelt använda SAP tillhandahålls rename-funktionen för att anpassa ändrade värdnamnet och/eller ändra SID för SAP-system i distribuerade avbildningen så fort en ny kopia har startats.

- - -
> ![Windows][Logo_Windows] Windows
>
> Kontrollera att enheten D:\ inte är används Set disk automount för anslutna diskar som beskrivs i kapitlet [inställningen automount för anslutna diskar] [ planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> Se till att resursen/mnt/inte används och att alla diskar är monterade via uuid. Kontrollera att posten startprogrammet visar också montera uuid-baserad för OS-disken.
>
>

- - -
* SAP-Gränssnittet (för administrativa och konfigurera syften) kan vara förinstallerade i en sådan mall.
* Annan programvara som krävs för att köra de virtuella datorerna har i lokala scenarier kan installeras så länge som den här programvaran kan arbeta med det nya namnet på den virtuella datorn.

Om den virtuella datorn är tillräckligt beredd att vara Allmänt och så småningom oberoende av konton/användare inte är tillgängliga i det aktuella Azure distributionsscenariot, utförs det sista steget förberedelse för att generalisera sådana en bild.

##### <a name="generalizing-a-vm"></a>Generalisera en virtuell dator
- - -
> ![Windows][Logo_Windows] Windows
>
> Det sista steget är att logga in på en virtuell dator med ett administratörskonto. Öppna ett kommandofönster i Windows som *administratör*. Gå till %windir%\windows\system32\sysprep och köra sysprep.exe.
> Ett litet fönster visas. Det är viktigt att kontrollera den **Generalize** alternativet (standardvärdet är avmarkerat) och ändra Avslutningsalternativ från ”omstart” standard ”avstängning av”. Den här proceduren förutsätter att sysprep-processen är utförda lokalt i Gästoperativsystemet för en virtuell dator.
> Om du vill utföra proceduren med en virtuell dator som redan körs i Azure, följer du stegen som beskrivs i [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Så här avbildar du en Linux-dator ska användas som en Resource Manager-mall][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Överföring av virtuella datorer och virtuella hårddiskar mellan en lokal plats till Azure
Eftersom det inte går att ladda upp avbildningar av Virtuella datorer och diskar till Azure via Azure portal, behöver du använda Azure PowerShell-cmdletar eller CLI. En annan möjlighet är att använda verktyget ”AzCopy”. Verktyget kan kopiera virtuella hårddiskar mellan lokala och Azure (i båda riktningarna). Det kan också kopiera virtuella hårddiskar mellan Azure-regioner. Kontakta [den här dokumentationen] [ storage-use-azcopy] om hämtning och användning av AzCopy.

Ett tredje alternativ är att använda olika GUI-orienterade tredjepartsverktyg. Se dock till att de här verktygen stöder Azure Page Blobs. För våra syften behöver vi behöver använda Azure Page Blob store (skillnaderna beskrivs här: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Verktygen i Azure är också effektivt när det gäller komprimering av virtuella datorer och VHD-filer som behöver överföras. Detta är viktigt eftersom den här effektiviteten i komprimering minskar den tid (som varierar ändå beroende på länken ladda upp till internet från den lokala anläggningen och av Azure-distributionsregion som mål). Det är en rättvis förutsätta att ladda upp en virtuell dator eller virtuell Hårddisk från Europeiska plats i USA-baserade Azure data Center tar längre tid än ladda upp samma virtuella datorer/virtuella hårddiskar till Europeiska Azure datacenter.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Ladda upp en VHD från en lokal plats till Azure
Att ladda upp en befintlig virtuell dator eller virtuell Hårddisk från det lokala nätverket sådana en virtuell dator eller virtuell Hårddisk måste uppfylla kraven som anges i kapitlet [förberedelse för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk] [ planning-guide-5.2.1]i det här dokumentet.

Sådana en virtuell dator behöver inte kan generaliseras och kan laddas upp i tillstånd och forma den har efter avstängning på en lokal sida. Detsamma gäller för ytterligare virtuella hårddiskar, vilket inte innehåller något operativsystem.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Ladda upp en virtuell Hårddisk och gör det till en Azure-Disk
I det här fallet vill vi att ladda upp en virtuell Hårddisk, med eller utan ett operativsystem, och montera den till en virtuell dator som en datadisk eller använda den som operativsystemsdisk. Det här är en process i flera steg

**PowerShell**

* Logga in på din prenumeration med *Connect-AzureRmAccount*
* Ställ in prenumerationen av din kontext med *Set-AzureRmContext* och parametern prenumerations-ID eller SubscriptionName - finns i <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Ladda upp den virtuella Hårddisken med *Add-AzureRmVhd* till ett Azure Storage-konto – finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Valfritt) Skapa en hanterad Disk från den virtuella Hårddisken med *New-AzureRmDisk* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Ange en ny VM-konfiguration OS-disk till den virtuella Hårddisken eller hanterad Disk med *Set-AzureRmVMOSDisk* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Skapa en ny virtuell dator från VM-konfigurationen med *New-AzureRmVM* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Lägga till en datadisk till en ny virtuell dator med *Add-AzureRmVMDataDisk* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Logga in på din prenumeration med *az-inloggning*
* Välj din prenumeration med *az-kontogrupper--subscription `<subscription name or id`>*
* Ladda upp den virtuella Hårddisken med *az storage blob uppladdning* -finns i [med hjälp av Azure CLI med Azure Storage][storage-azure-cli]
* (Valfritt) Skapa en hanterad Disk från den virtuella Hårddisken med *az disk skapa* -finns i https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Skapa en ny virtuell dator att ange den överförda virtuella Hårddisken eller hanterad Disk som OS-disk med *az vm skapa* och parametern *– koppla-os-disk*
* Lägga till en datadisk till en ny virtuell dator med *az vm disk attach* och parametern *– ny*

**Mall**

* Ladda upp den virtuella Hårddisken med Powershell eller Azure CLI
* (Valfritt) Skapa en hanterad Disk från virtuell Hårddisk med Powershell, Azure CLI eller Azure-portalen
* Distribuera den virtuella datorn med en JSON-mall som refererar till den virtuella Hårddisken som visas i [det här exemplet JSON-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller använda Managed Disks enligt [det här exemplet JSON-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Distribution av en VM-avbildning
Ladda upp en befintlig virtuell dator eller virtuell Hårddisk från det lokala nätverket, för att kunna använda den som en Azure VM-avbildning sådana en virtuell dator eller virtuell Hårddisk måste uppfylla de krav som anges i kapitlet [förberedelse för att distribuera en virtuell dator med en kundspecifika avbildning för SAP] [ planning-guide-5.2.2] i det här dokumentet.

* Använd *sysprep* på Windows eller *waagent-avetablering* på Linux för att generalisera den virtuella datorn – Se [Teknisk referens för Sysprep](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en Linux-dator ska användas som en Resource Manager-mall] [ capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *Connect-AzureRmAccount*
* Ställ in prenumerationen av din kontext med *Set-AzureRmContext* och parametern prenumerations-ID eller SubscriptionName - finns i <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Ladda upp den virtuella Hårddisken med *Add-AzureRmVhd* till ett Azure Storage-konto – finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Valfritt) Skapa en avbildning av hanterad Disk från den virtuella Hårddisken med *New-AzureRmImage* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Ange en ny VM-konfiguration till OS-disk på
  * Virtuell Hårddisk med *Set-AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Hanterade diskavbildning *Set-AzureRmVMSourceImage* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Skapa en ny virtuell dator från VM-konfigurationen med *New-AzureRmVM* -finns i <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Använd *sysprep* på Windows eller *waagent-avetablering* på Linux för att generalisera den virtuella datorn – Se [Teknisk referens för Sysprep](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en Linux-dator ska användas som en Resource Manager-mall] [ capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *az-inloggning*
* Välj din prenumeration med *az-kontogrupper--subscription `<subscription name or id`>*
* Ladda upp den virtuella Hårddisken med *az storage blob uppladdning* -finns i [med hjälp av Azure CLI med Azure Storage][storage-azure-cli]
* (Valfritt) Skapa en avbildning av hanterad Disk från den virtuella Hårddisken med *az bild skapa* -finns i https://docs.microsoft.com/cli/azure/image#az_image_create
* Skapa en ny virtuell dator anger att den överförda virtuella Hårddisken eller hanterade diskavbildning OS-disken med *az vm skapa* och parametern *--bild*

**Mall**

* Använd *sysprep* på Windows eller *waagent-avetablering* på Linux för att generalisera den virtuella datorn – Se [Teknisk referens för Sysprep](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller [så här avbildar du en Linux-dator ska användas som en Resource Manager-mall] [ capture-image-linux-step-2-create-vm-image] för Linux
* Ladda upp den virtuella Hårddisken med Powershell eller Azure CLI
* (Valfritt) Skapa en avbildning av hanterad Disk från den virtuella Hårddisken med Powershell, Azure CLI eller Azure-portalen
* Distribuera den virtuella datorn med en JSON-mall som refererar till avbildningen VHD enligt [det här exemplet JSON-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller med hjälp av hanterad Disk Image enligt [det här exemplet JSON-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Ladda ned VHD: er eller hanterade diskar till en lokal
Azure-infrastruktur som en tjänst är inte en enkelriktad gata endast att kunna ladda upp VHD: er och SAP system. Du kan flytta SAP-system från Azure tillbaka till den lokala miljön samt.

Under tiden för hämtningen kan inte virtuella hårddiskar eller hanterade diskar aktiveras. Även vid hämtning av diskar som är monterade till virtuella datorer, måste den virtuella datorn stängs av och frigörs. Om du bara vill ladda ned databasinnehållet som ska användas för att ställa in ett nytt system på plats och om du accepterar den under tiden för hämtningen och installationen av det nya systemet som systemet i Azure kan fortfarande finnas operativa , du kan undvika ett långt avbrott genom att utföra en komprimerad databassäkerhetskopia till en disk och bara hämta disken istället för att också hämta den grundläggande OS-VM.

#### <a name="powershell"></a>PowerShell

  * Ladda ned en hanterad Disk  
  Du måste först få åtkomst till den underliggande blobben för den hanterade disken. Du kan sedan kopiera underliggande bloben till ett nytt lagringskonto och ladda ned bloben från det här lagringskontot.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Ladda ned en virtuell Hårddisk  
  När SAP-system har stoppats och den virtuella datorn är avstängd, kan du använda PowerShell-cmdleten Save-AzureRmVhd på den lokala målet för att ladda ned VHD-diskarna till den lokala miljön. Om du vill göra det, måste URL: en för den virtuella Hårddisken som du hittar i ”storage avsnittet” Azure-portalen (du behöver att navigera till Lagringskontot och behållaren där den virtuella Hårddisken har skapats) och du behöver veta var den virtuella Hårddisken ska kopieras till.

  Du kan sedan använda kommandot genom att definiera parametern SourceUri som en URL för den virtuella Hårddisken ska ladda ned och LocalFilePath som den fysiska platsen för den virtuella Hårddisken (inklusive filnamnet). Kommandot kunde se ut:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Mer information om cmdleten Save-AzureRmVhd finns här <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Ladda ned en hanterad Disk  
  Du måste först få åtkomst till den underliggande blobben för den hanterade disken. Du kan sedan kopiera underliggande bloben till ett nytt lagringskonto och ladda ned bloben från det här lagringskontot.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Ladda ned en virtuell Hårddisk   
  När SAP-system har stoppats och den virtuella datorn är avstängd, du kan använda Azure CLI-kommando _azure storage blob download_ på den lokala målet att ladda ned den virtuella Hårddisken diskar tillbaka till den lokala miljön. För att kunna göra det behöver du namnet och en behållare för den virtuella Hårddisken som du hittar i Storage avsnitt av Azure portal (du behöver att navigera till Lagringskontot och behållaren där den virtuella Hårddisken har skapats) och du behöver veta var den virtuella Hårddisken ska kopiera g till.

  Du kan sedan använda kommandot genom att definiera parametrar blob och behållare på den virtuella Hårddisken att ladda ned och målet som fysiska platsen för den virtuella Hårddisken (inklusive filnamnet). Kommandot kunde se ut:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Överföring av virtuella datorer och diskar i Azure
#### <a name="copying-sap-systems-within-azure"></a>Kopiera SAP-system i Azure
Ett SAP-system eller till och med en dedikerad DBMS server stöd för en SAP-programnivån sannolikt består av flera diskar som innehåller antingen OS med binärfilerna eller filen data och loggfiler på SAP-databasen. Varken Azure-funktioner för att kopiera diskar eller Azure-funktioner för att spara diskar till en lokal disk har du en mekanism för synkronisering, vilka ögonblicksbilder flera diskar i ett konsekvent sätt. Status för diskarna kopierade eller sparas skulle även om de är monterade mot samma virtuella dator därför olika. Detta innebär att i konkreta fall för att ha olika data och logfile(s) finns i olika diskar databasen i slutet skulle vara inkonsekvent.

**Slutsats: För att kunna kopiera eller spara diskar, vilket är en del av en konfiguration för SAP-system du måste stoppa SAP-system och också behöva stänga av den distribuerade virtuella datorn. Du kan bara kopiera eller ladda ned uppsättningen diskar att antingen skapa en kopia av SAP-system i Azure eller lokalt.**

Datadiskar kan sparas som VHD-filer i ett Azure Storage-konto och kan kopplas direkt till en virtuell dator eller användas som en bild. I det här fallet kopieras den virtuella Hårddisken till en annan plats innan som kopplas till den virtuella datorn. Det fullständiga namnet på VHD-filen i Azure måste vara unikt i Azure. Som nämnts tidigare redan är namnet på typ av ett namn som ser ut som:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Datadiskar kan också vara Managed Disks. I det här fallet används den hanterade disken för att skapa en ny hanterad Disk innan som kopplas till den virtuella datorn. Namnet på den hanterade disken måste vara unika inom en resursgrupp.

##### <a name="powershell"></a>PowerShell
Du kan använda Azure PowerShell-cmdletar för att kopiera en virtuell Hårddisk som du ser i [i den här artikeln][storage-powershell-guide-full-copy-vhd]. Använda New-AzureRmDiskConfig och New-AzureRmDisk för att skapa en ny hanterad Disk, som visas i följande exempel.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Du kan använda Azure CLI för att kopiera en virtuell Hårddisk som du ser i [i den här artikeln][storage-azure-cli-copy-blobs]. Du kan skapa en ny hanterad Disk med *az disk skapa* som visas i följande exempel.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-verktyg
* <http://storageexplorer.com/>

Professionella utgåvor av Azure Storage-Utforskare finns här:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Kopia av en virtuell Hårddisk själva i ett lagringskonto är en process som tar bara några sekunder (liknar SAN-maskinvaran skapa ögonblicksbilder med lazy kopia och kopiering vid skrivning). När du har en kopia av VHD-filen kan du koppla den till en virtuell dator eller använda den som en bild för att bifoga kopior av den virtuella Hårddisken till virtuella datorer.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>CLI 2.0
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

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopierar diskar mellan Azure Storage-konton
Den här uppgiften kan inte utföras på Azure portal. Du kan använda Azure PowerShell-cmdletar, Azure CLI eller en webbläsare för lagringssystem från tredje part. PowerShell-cmdlets eller CLI-kommandon kan skapa och hantera blobbar, bland annat möjligheten att kopiera BLOB asynkront mellan Lagringskonton och mellan regioner inom samma Azure-prenumeration.

##### <a name="powershell"></a>PowerShell
Du kan också kopiera virtuella hårddiskar mellan prenumerationer. Mer information finns i [i den här artikeln][storage-powershell-guide-full-copy-vhd].

Det grundläggande flödet av logik för PS-cmdlet: en ut så här:

* Skapa en kontexten för lagringskontot för den **källa** storage-konto med *New-AzureStorageContext* -finns i <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Skapa en kontexten för lagringskontot för den **target** storage-konto med *New-AzureStorageContext* -finns i <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Starta kopieringen med

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Kontrollera status för kopian i en slinga med

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anslut den nya virtuella Hårddisken till en virtuell dator enligt beskrivningen ovan.

Exempel finns i [i den här artikeln][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Starta kopieringen med

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Kontrollera status om den är fortfarande i en slinga med

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Anslut den nya virtuella Hårddisken till en virtuell dator enligt beskrivningen ovan.

Exempel finns i [i den här artikeln][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Hantering av disk
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuella datorn/disken struktur för SAP-distributioner
Vi är hanteringen av strukturen för en virtuell dator och de associera diskarna enkel. I den lokala installationer kunder som har utvecklats många sätt att strukturera en serverinstallation.

* En grundläggande disk, som innehåller Operativsystemet och alla binärfiler av DBMS och/eller SAP. Sedan mars 2015 kommer kan den här disken vara upp till 1TB i storlek i stället för tidigare begränsningar som begränsat till 127 GB.
* En eller flera diskar, som innehåller loggfilen DBMS på SAP-databasen och loggfilen i området DBMS temporär lagring (om DBMS stöder detta). Om databasen IOPS loggkraven är högt, måste du stripe-flera diskar för att nå IOPS-volymen som krävs.
* Ett antal diskar som innehåller en eller två databasfiler på SAP-databasen och DBMS temp datafilerna samt (om DBMS stöder detta).

![För referenskonfiguration av virtuell Azure IaaS-dator för SAP][planning-guide-figure-1300]


- - -
> ![Windows][Logo_Windows] Windows
>
> Med många kunder såg vi konfigurationer där, till exempel SAP och DBMS binärfiler inte har installerats på enhet c:\ där Operativsystemet installerades. Det fanns olika orsaker till detta, men när vi gick tillbaka till roten, det var vanligtvis att enheterna har liten och OS-uppgraderingar behövs ytterligare utrymme 10 – 15 år sedan. Båda villkoren gäller inte dessa dagar för ofta längre. Idag kan c:\ enheten mappas i stort diskar eller virtuella datorer. För att skydda distributioner enkel i deras struktur, rekommenderar vi att du följa följande mönster för distribution för SAP NetWeaver-system i Azure
>
> Växlingsfil för Windows-operativsystem måste finnas på enheten D: (icke-beständiga disk)
>
> ![Linux][Logo_Linux] Linux
>
> Placera Linux växlingsfil under /mnt/mnt/resurs på Linux enligt beskrivningen i [i den här artikeln][virtual-machines-linux-agent-user-guide]. Växlingsfilen kan konfigureras i konfigurationsfilen för den Linux-agenten/etc/waagent.conf. Lägg till eller ändra följande inställningar:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Om du vill aktivera ändringarna, måste du starta om Linux-agenten med

```
sudo service waagent restart
```

Läs SAP-kommentar [1597355] för mer information om rekommenderade växling filstorlek

- - -
Antalet diskar som används för DBMS datafiler och vilken typ av dessa diskar finns på Azure-lagring beror på IOPS-krav och svarstid krävs. Exakta kvoter beskrivs i [i den här artikeln (Linux)] [ virtual-machines-sizes-linux] och [i den här artikeln (Windows)][virtual-machines-sizes-windows].

Upplevelse av SAP-distributioner under de senaste två åren undervisats oss vissa lektioner som kan sammanfattas som:

* IOPS-trafik till olika filer är inte alltid samma eftersom befintliga kundsystem kan ha annorlunda storlek datafiler som representerar sina SAP-databaserna. Därmed visade den sig vara bättre för att använda en RAID-konfiguration över flera diskar för att placera datafilerna LUN högg utanför de. Det fanns situationer, särskilt i Azure där en IOPS-hastighet når kvoten för en enskild disk mot DBMS-transaktionsloggen för standardlagring. I sådana situationer bör du använda Premium Storage eller också sammanställa flera standardlagring stripe-diskar med en programuppdateringsplats.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Prestandametodtips för SQL Server i Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurera programvaru-RAID på Linux][virtual-machines-linux-configure-raid]
> * [Konfigurera LVM på en Linux-dator i Azure][virtual-machines-linux-configure-lvm]
> * [Azure Storage-hemligheter och Linux-i/o-optimering](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Premium Storage visar betydande bättre prestanda, särskilt för kritiska transaction log skrivningar. För SAP-scenarier som förväntas leverera produktionsliknande prestanda, rekommenderas att använda VM-serien som kan använda Azure Premium Storage.

Tänk på att disken som innehåller Operativsystemet, och vi rekommenderar om binärfilerna för SAP och databasen (grundläggande VM), är inte längre begränsad till 127GB. Det kan nu ha upp till 1TB i storlek. Det bör vara tillräckligt med utrymme för att hålla alla nödvändig fil, inklusive till exempel SAP batch-jobbloggar.

Fler förslag och mer specifikt för DBMS virtuella datorer, i den [DBMS Distributionsguide][dbms-guide]

#### <a name="disk-handling"></a>Hantering av disk
I de flesta fall måste du skapa ytterligare diskar för att distribuera SAP-databas på den virtuella datorn. Vi talade om överväganden på antalet diskar i kapitlet [virtuella datorn/disken struktur för SAP-distributioner] [ planning-guide-5.5.1] i det här dokumentet. Azure-portalen kan ansluta och koppla från diskar när en grundläggande virtuell dator har distribuerats. Diskarna kan vara ansluten/kopplas från när den virtuella datorn är igång och körs samt när den är stoppad. När du bifogar en disk, erbjuder Azure-portalen att koppla en tom disk eller en befintlig disk, som vid denna tidpunkt inte är ansluten till en annan virtuell dator.

**Obs**: diskar kan endast kopplas till en virtuell dator vid en given tidpunkt.

![Koppla / koppla från diskar med Azure standardlagring][planning-guide-figure-1400]

Under distributionen av en ny virtuell dator, kan du bestämma om du vill använda Managed Disks eller placera dina diskar på Azure Storage-konton. Om du vill använda Premium Storage, bör du använda Managed Disks.

Därefter måste du bestämma om du vill skapa en ny och tom disk eller om du vill välja en befintlig disk som har laddats upp tidigare och ska kopplas till den virtuella datorn nu.

**VIKTIGA**: du **inte** vill använda värd cachelagring med Azure standardlagring. Inställningen värden bör du lämna standardinställningen på Ingen. Med Azure Premium Storage, bör du aktivera cachelagring av Läs om i/o-egenskap är främst läsa samma sätt som vanliga i/o-trafik mot databasen datafiler. Om databasen transaktionsloggfilen rekommenderas ingen cachelagring.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Hur du ansluter en datadisk i Azure portal][virtual-machines-linux-attach-disk-portal]
>
> Om diskar är anslutna, måste du logga in på den virtuella datorn att öppna hanteraren för Windows-Disk. Om automount inte är aktiverad som rekommenderas i kapitlet [inställningen automount för anslutna diskar][planning-guide-5.5.3], nyligen anslutna enheten behöver tas online och initierade.
>
> ![Linux][Logo_Linux] Linux
>
> Om diskar är anslutna, måste du logga in på den virtuella datorn och initiera om dem enligt beskrivningen i [i den här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Om den nya disken är en tom disk kan behöva du formatera disken samt. För formatering, särskilt för DBMS data och loggfiler på samma som för bare metal-distributioner av DBMS gäller rekommendationer.

Som redan nämnts i kapitlet [The Microsoft Azure VM konceptet][planning-guide-3.2], ett Azure Storage-konto tillhandahåller inte oändlig resurser när det gäller i/o-volym, IOPS och datavolym. Vanligtvis som DBMS virtuella datorer påverkas mest av detta. Det kan vara bäst att använda ett separat Lagringskonto för varje virtuell dator om du har några hög i/o-volym virtuella datorer för att distribuera för att hålla sig inom gränsen på volymen för Azure Storage-konto. I annat fall måste du se hur du kan balansera dessa virtuella datorer mellan olika lagringskonton utan nått gränsen för varje enskild Storage-konto. Mer information finns i avsnittet den [DBMS Distributionsguide][dbms-guide]. Du bör också tänka dessa begränsningar på för ren SAP-programmet server-datorer eller andra virtuella datorer som så småningom kan kräva ytterligare virtuella hårddiskar. Dessa begränsningar gäller inte om du använder hanterade diskar. Om du tänker använda Premium Storage rekommenderar vi använder hanterade diskar.

Ett annat ämne som är relevanta för Storage-konton är om de virtuella hårddiskarna i ett Lagringskonto får Geo-replikerade. GEO-replikering är aktiverat eller inaktiverat på kontonivå för lagring och inte på VM-nivå. Om geo-replikering är aktiverad, skulle de virtuella hårddiskarna i Lagringskonto replikeras till en annan Azure-datacenter i samma region. Innan du bestämmer dig för den här, bör du tänka på följande begränsningar:

Azure Geo-replikering fungerar lokalt på varje virtuell Hårddisk på en virtuell dator och replikerar inte IOs i kronologisk ordning över flera virtuella hårddiskar i en virtuell dator. Därför kan replikeras den virtuella Hårddisken som representerar den grundläggande virtuell datorn, samt de ytterligare virtuella hårddiskar anslutna till den virtuella datorn oberoende av varandra. Det innebär att det finns ingen synkronisering mellan ändringarna i de olika virtuella hårddiskarna. Det faktum att replikeras IOs oberoende av den ordning som de är skrivna innebär att geo-replikering är inte av värdet för databasservrar som har sina databaser fördelas på flera virtuella hårddiskar. Förutom för DBMS finnas det också andra program där processer skriva eller manipulera data i olika virtuella hårddiskar och där det är viktigt att hålla ordning på ändringar. Om det är ett krav bör det inte att aktivera geo-replikering i Azure. Beroende på om du behöver eller vill geo-replikering för en uppsättning virtuella datorer, men inte för en annan uppsättning, du kan redan kategorisera virtuella datorer och deras relaterade virtuella hårddiskar i olika Lagringskonton som har geo-replikering aktiverad eller inaktiverad.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ange automount för anslutna diskar
- - -
> ![Windows][Logo_Windows] Windows
>
> För virtuella datorer som skapas från egna avbildningar eller diskar, är det nödvändigt att kontrollera och eventuellt ange parametern automount. Den här parametern gör att den virtuella datorn efter en omstart eller omdistribution i Azure för att montera enheter anslutna/monterad igen automatiskt.
> Parametern har angetts för avbildningarna som tillhandahålls av Microsoft i Azure Marketplace.
>
> För att ange automount i dokumentationen för kommandorad körbara diskpart.exe här:
>
> * [DiskPart kommandoradsalternativ](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](http://technet.microsoft.com/library/cc753703.aspx)
>
> Ska öppnas fönstret för Windows-Kommandotolken som administratör.
>
> Om diskar är anslutna, måste du logga in på den virtuella datorn att öppna hanteraren för Windows-Disk. Om automount inte är aktiverad som rekommenderas i kapitlet [inställningen automount för anslutna diskar][planning-guide-5.5.3], den nyligen kopplade volym > måste utföras online och initierade.
>
> ![Linux][Logo_Linux] Linux
>
> Du behöver att initiera en nyligen anslutna tom disk enligt beskrivningen i [i den här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Du måste också lägga till nya diskar på/etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Slutlig distribution
Sista distributionen och specifika stegen, särskilt för distribution av SAP utökad övervakning, finns i den [Distributionsguide][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Åtkomst till SAP-system som körs i virtuella Azure-datorer
Scenarier med endast molnbaserad kanske du vill ansluta till dessa SAP-system via det offentliga internet använda SAP-Gränssnittet. De här fallen måste följande procedurer som ska användas.

Senare i dokumentet diskuteras de andra viktiga scenariot, ansluter till SAP-system i mellan lokala distributioner som har en plats-till-plats-anslutning (VPN-tunnel) eller Azure ExpressRoute-anslutning mellan lokala system och Azure-system.

### <a name="remote-access-to-sap-systems"></a>Fjärråtkomst till SAP-system
Med Azure Resource Manager, det finns inga standardslutpunkterna längre som i den klassiska modellen som tidigare. Alla portar för en Azure Resource Manager-VM är öppna så länge som:

1. Ingen Nätverkssäkerhetsgrupp har definierats för undernätet eller nätverksgränssnittet. Trafik till virtuella Azure-datorer kan vara skyddad via så kallade ”Nätverkssäkerhetsgrupper”. Mer information finns i [vad är en Nätverkssäkerhetsgrupp (NSG)?][virtual-networks-nsg]
2. Inga Azure Load Balancer har definierats för nätverksgränssnittet   

Se arkitektur skillnaden mellan klassiska modellen och ARM enligt beskrivningen i [i den här artikeln][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Konfigurationen av SAP-System och SAP GUI-anslutning för endast molnbaserad scenario
Den här artikeln som beskriver information till det här avsnittet finns på: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Ändra brandväggsinställningar på virtuell dator
Det kan vara nödvändigt att konfigurera brandväggen på dina virtuella datorer som tillåter inkommande trafik till din SAP-system.

- - -
> ![Windows][Logo_Windows] Windows
>
> Windows-brandväggen i en distribuerad virtuell Azure-dator är aktiverad som standard. Nu måste du tillåta SAP-Port som ska öppnas, annars SAP-Gränssnittet kan inte ansluta.
> Gör så här:
>
> * Öppna kontrollpanelen\system och säkerhet\windows-brandväggen till **avancerade inställningar**.
> * Högerklicka på regler för inkommande trafik och valde **ny regel**.
> * I följande guide valde att skapa en ny **Port** regeln.
> * Lämna inställningen vid TCP och Skriv det portnumret som du vill öppna i nästa steg i guiden. Eftersom vår SAP instans-ID är 00, tog vi 3200. Om din instans har en annan instans., bör den port som du definierade tidigare baserat på antalet instans öppnas.
> * I nästa del av guiden, måste du behålla objektet **Tillåt anslutningen** kontrolleras.
> * I nästa steg i guiden behöver du definiera om regeln ska tillämpas för domänen, privata och offentliga nätverk. Justera det om det behövs efter dina behov. Men måste ansluter med SAP-Gränssnittet från utsidan via det offentliga nätverket, du ha regeln ska gälla för det offentliga nätverket.
> * I det sista steget i guiden, ett namn för regeln och spara genom att trycka på **Slutför**.
>
> Regeln träder i kraft omedelbart.
>
> ![Port Regeldefinitionen][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Linux-avbildningar i Azure Marketplace gör inte iptables-brandväggen som standard och anslutningen till din SAP-system ska fungera. Om du har aktiverat iptables eller en annan brandvägg finns i dokumentationen för iptables eller använda brandväggen att tillåta inkommande tcp-trafik till port 32xx (där xx är systemnummer SAP-system).
>
>

- - -
#### <a name="security-recommendations"></a>Säkerhetsrekommendationer
SAP-Gränssnittet kan inte anslutas direkt till någon av SAP-instanser (port 32xx) som körs, men först ansluter via den port som öppnas till SAP-meddelande serverprocessen (port 36xx). Tidigare användes samma port av meddelandeservern för den interna kommunikationen till programinstanserna. Om du vill förhindra att den lokala programservrar oavsiktligt kommunicerar med en message server i Azure, kan intern kommunikationsportar ändras. Vi rekommenderar starkt att ändra den interna kommunikationen mellan SAP-meddelandeservern och dess instanser av programmet till ett annat portnummer på system som har varit klonas från lokala system, t.ex en klon av utveckling för projektet testning osv. Detta kan göras med profil:

> rdisp/msserv_internal
>
>

enligt beskrivningen i [säkerhetsinställningar för SAP Message Server ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Begreppet molnbaserad distribution av SAP-instanser
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Enstaka virtuell dator med SAP NetWeaver demo/utbildning scenario
![Kör enskild VM SAP demo-system med samma namn på virtuella datorer, separat i Azure Cloud Services][planning-guide-figure-1700]

I det här scenariot (finns i kapitlet [molnbaserad] [ planning-guide-2.1] i det här dokumentet) vi implementerar en typisk utbildning/demo system scenario där fullständig utbildning/demo-scenariot finns i en enda virtuell dator. Vi förutsätter att distributionen görs via mallar för VM-avbildning. Vi förutsätter också att flera av dessa demo/utbildningar virtuella datorer behöver distribueras med de virtuella datorerna har samma namn.

Antas att du skapat en VM-avbildning som beskrivs i vissa avsnitt i kapitel [förbereda virtuella datorer med SAP för Azure] [ planning-guide-5.2] i det här dokumentet.

En händelsesekvens att implementera scenariot ser ut så här:

##### <a name="powershell"></a>PowerShell
* Skapa en ny resursgrupp för varje utbildning/demo liggande

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Skapa ett nytt lagringskonto om du inte vill använda Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Skapa ett nytt virtuellt nätverk för varje utbildning/demo liggande om du vill aktivera användningen av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en Nätverkssäkerhetsgrupp som bara tillåter trafik till port 3389 för att aktivera Remote Desktop åtkomst och port 22 för SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Skapa en ny offentlig IP-adress som kan användas för att få åtkomst till den virtuella datorn från internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Skapa ett nytt nätverksgränssnitt för den virtuella datorn

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Skapa en virtuell dator. För scenariot molnbaserad har varje virtuell dator samma namn. SAP-SID för SAP NetWeaver-instanser i dessa virtuella datorer är samma även. Namnet på den virtuella datorn måste vara unikt inom Azure-resursgrupp, men i olika Azure-resursgrupper kan du köra virtuella datorer med samma namn. ”Administratör”-standardkontot för Windows eller 'root' för Linux är inte giltiga. Ett nytt användarnamn för administratör måste anges tillsammans med ett lösenord. Storleken på den virtuella datorn måste också definieras.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Du kan också lägga till fler diskar och återställa innehåll som krävs. Alla blobnamn (URL: er till blobarna) måste vara unikt i Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>CLI
Följande exempelkod kan användas i Linux. För Windows, Använd PowerShell enligt beskrivningen ovan eller anpassa exempel för att använda % rgName % i stället för $rgName och ställa in miljövariabeln med hjälp av kommandot Windows *ange*.

* Skapa en ny resursgrupp för varje utbildning/demo liggande

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Skapa ett nytt lagringskonto

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Skapa ett nytt virtuellt nätverk för varje utbildning/demo liggande om du vill aktivera användningen av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en Nätverkssäkerhetsgrupp som bara tillåter trafik till port 3389 för att aktivera Remote Desktop åtkomst och port 22 för SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Skapa en ny offentlig IP-adress som kan användas för att få åtkomst till den virtuella datorn från internet

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Skapa ett nytt nätverksgränssnitt för den virtuella datorn

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Skapa en virtuell dator. För scenariot molnbaserad har varje virtuell dator samma namn. SAP-SID för SAP NetWeaver-instanser i dessa virtuella datorer är samma även. Namnet på den virtuella datorn måste vara unikt inom Azure-resursgrupp, men i olika Azure-resursgrupper kan du köra virtuella datorer med samma namn. ”Administratör”-standardkontot för Windows eller 'root' för Linux är inte giltiga. Ett nytt användarnamn för administratör måste anges tillsammans med ett lösenord. Storleken på den virtuella datorn måste också definieras.

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

* Du kan också lägga till fler diskar och återställa innehåll som krävs. Alla blobnamn (URL: er till blobarna) måste vara unikt i Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Mall
Du kan använda exempelmallarna på azure-snabbstartsmallar-arkivet på github.

* [Enkel virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Enkla Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuell dator från avbildningen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementera en uppsättning virtuella datorer som kommunicerar i Azure
Det här scenariot med endast molnbaserad är ett vanligt scenario för utbildning och demonstration syfte var programvaran som representerar demo/utbildning scenariot är utspridd på flera virtuella datorer. De olika komponenterna som är installerade på de olika virtuella datorerna måste kommunicera med varandra. Igen, i det här scenariot utan lokal nätverkskommunikation eller scenario med flera platser krävs.

Det här scenariot är en utökning av installationen som beskrivs i kapitlet [enskild virtuell dator med SAP NetWeaver demo/utbildning scenariot] [ planning-guide-7.1] i det här dokumentet. I det här fallet läggs fler virtuella datorer till en befintlig resursgrupp. I följande exempel består utbildning liggande av en SAP ASCS/SCS virtuell dator, en virtuell dator som kör en DBMS och en SAP-programservern instans VM.

Innan du skapar det här scenariot måste du tänka grundläggande inställningar som redan fått i scenariot innan.

#### <a name="resource-group-and-virtual-machine-naming"></a>Resursgruppens namn och namngivning av virtuella datorer
Alla resursgruppnamn måste vara unikt. Utveckla dina egna namngivningsschemat för dina resurser, till exempel `<rg-name`>-suffix.

Namnet på virtuella datorn måste vara unikt inom resursgruppen.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurera nätverk för kommunikation mellan olika virtuella datorer
![Uppsättningen av virtuella datorer inom en Azure-nätverk][planning-guide-figure-1900]

Att förhindra namnkonflikter med kloner av samma utbildning/demo-landskap, måste du skapa ett virtuellt Azure-nätverk för varje liggande. DNS-namnmatchningen kommer att tillhandahållas av Azure eller du kan konfigurera en egen DNS-server utanför Azure (inte till ytterligare diskuteras här). I det här scenariot kan konfigurerar vi inte vår egen DNS. För alla virtuella datorer i en Azure virtuellt nätverk aktiveras kommunikation via värdnamn.

Skäl för att avgränsa utbildning eller demo landskap genom virtuella nätverk och inte bara resursgrupper kan vara följande:

* SAP-landskap som ställs in måste en egen AD/OpenLDAP och en domänserver måste vara en del av var och en av landskap.  
* SAP-landskap enligt inställningen har komponenter som behöver arbeta med fasta IP-adresserna.

Mer information om virtuella Azure-nätverk och hur du definierar dem finns i [i den här artikeln][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Distribuera SAP virtuella datorer med anslutning till företagets nätverk (mellan lokala)
Du kan köra ett SAP-landskap och vill dela upp distributionen mellan utan operativsystem för avancerad DBMS-servrar, lokala virtualiserade miljöer för programskikt och mindre på nivå 2 konfigurerat SAP-system och Azure IaaS. Grundläggande antas att SAP-system i en SAP-landskap måste kommunicera med varandra och med många andra programkomponenter som distribueras i företaget, oberoende av deras distribution form. Det bör även finnas några skillnader som introducerades av formuläret distribution för slutanvändaren att ansluta med SAP-Gränssnittet eller andra gränssnitt. Dessa villkor kan bara vara uppfyllda när vi har en lokal Active Directory/OpenLDAP och DNS-tjänster som utökats till Azure-system via plats-till-plats/flera plats-anslutning eller privat Azure ExpressRoute-anslutningar.

För att visa mer bakgrundsinformation om implementeringsdetaljer för SAP på Azure kan vi uppmuntrar dig att läsa kapitel [begrepp Cloud-Only distribution av SAP instanser] [ planning-guide-7] i det här dokumentet som beskriver några Grunderna konstruktioner för Azure och hur dessa ska användas med SAP-program i Azure.

### <a name="scenario-of-an-sap-landscape"></a>Scenario där ett SAP-landskap
Scenario för flera platser kan beskrivas ungefär som i bilderna nedan:

![Plats-till-plats-anslutning mellan lokala och Azure-tillgångar][planning-guide-figure-2100]

I scenariot ovan beskriver ett scenario där lokalt AD/OpenLDAP och DNS har utökats till Azure. På den lokala sidan reserveras en viss IP-adressintervall per Azure-prenumeration. IP-adressintervallet kommer att tilldelas till ett virtuellt Azure-nätverk på Azure-sidan.

#### <a name="security-considerations"></a>Säkerhetsöverväganden
Minimikravet är användningen av säker kommunikationsprotokoll, till exempel SSL/TLS för webbläsaråtkomst eller VPN-baserade anslutningar för systemåtkomst till Azure-tjänster. Antas att företag hantera VPN-anslutning mellan sina företagets nätverk och Azure på olika sätt. Vissa företag kan blankly öppna alla portar. Vissa andra företag kanske vill ha exakt vilka portar som de behöver för att öppna osv.

I tabellen nedan vanliga SAP visas kommunikationsportar. Är tillräckliga för att öppna port för SAP-gateway.

| Tjänst | Portnamn | Exempel `<nn`> = 01 | Standardintervall (min – max) | Kommentar |
| --- | --- | --- | --- | --- |
| Dispatchern |sapdp`<nn>` se * |3201 |3200 - 3299 |SAP-Dispatcher, används av SAP-Gränssnittet för Windows och Java |
| Meddelande-server |sapms`<sid`> se ** |3600 |kostnadsfria sapms`<anySID`> |SID = SAP-System-ID |
| Gateway |sapgw`<nn`> se * |3301 |kostnadsfri |SAP-gateway som används för CPIC och RFC-kommunikation |
| SAP-router |sapdp99 |3299 |kostnadsfri |Endast CI (centrala instans) Tjänstnamn kan omtilldelas i /etc/services till ett godtyckligt värde efter installationen. |

*) nn = SAP-instansnummer

*) sid = SAP-System-ID

Mer detaljerad information om portar som krävs för olika SAP-produkter eller tjänster efter SAP-produkter finns här <http://scn.sap.com/docs/DOC-17124>.
Med det här dokumentet, bör du kunna öppna dedikerade portar i VPN-enhet krävs för specifika SAP-produkter och -scenarier.

Andra säkerhetsmetoder när du distribuerar virtuella datorer i ett sådant scenario kan vara att skapa en [Nätverkssäkerhetsgrupp] [ virtual-networks-nsg] att definiera regler för åtkomst.

### <a name="dealing-with-different-virtual-machine-series"></a>Ta itu med olika serier för virtuella datorer
Microsoft har lagt till många fler VM-typer som skiljer sig åt i antal virtuella processorer, minne eller viktigare på maskinvara som den körs på. Inte alla dessa virtuella datorer stöds med SAP (se stöds VM-typer i SAP-kommentar [1928533]). Några av de virtuella datorerna körs på olika värden maskinvarugenerationer. Dessa värden maskinvarugenerationer distribueras om i Granulariteten för en Azure-Skalningsenhet. Fall uppstå där olika VM-typer som du har valt kan inte köras på samma Skalningsenheten. En Tillgänglighetsuppsättning är begränsad i möjligheten att omfatta Skalningsenheter baserat på olika maskinvara.  Till exempel om du kör SAP DBMS-lagret på en E64s_v3 virtuell dator som är i en Tillgänglighetsuppsättning som ska användas tillsammans med den virtuella datorn som kör den sekundära DBMS-instansen i en konfiguration för hög tillgänglighet kan du helt enkelt det går inte att stoppa och starta om den sekundära virtuella datorn som virtuell dator i M-serien eftersom du kanske vill upg rade den virtuella datorn. Anledningen är att virtuella datorer i M-serien och Ev3-serien virtuella datorer körs på en annan maskinvara och med den i olika Skalningsenheter. Du skulle behöva skapa en ny Tillgänglighetsuppsättning, ta bort den sekundära Ev3-serien virtuella datorn, utan att ta bort lagring och distribuera om den virtuella datorn som virtuell dator i M-serien i den nya Tillgänglighetsuppsättningen.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Utskrift på en lokal nätverksskrivare från SAP-instans i Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Utskrift över TCP/IP i scenariot för flera platser
Att konfigurera en lokal TCP/IP-baserat nätverksskrivare i en Azure VM är den totala samma som i företagets nätverk, förutsatt att du har en VPN plats-till-plats-tunneln eller ExpressRoute-anslutning upprättas.

- - -
> ![Windows][Logo_Windows] Windows
>
> Gör så här:
>
> * Vissa nätverksskrivare levereras med en konfigurationsguide som gör det enkelt att konfigurera din skrivare i en Azure-dator. Om ingen guiden programvara har distribuerats med skrivaren, är manuell sättet att ställa in skrivaren att skapa en ny TCP/IP-skrivarport.
> * Öppna Kontrollpanelen -> enheter och skrivare > Lägg till en skrivare
> * Välj Lägg till en skrivare med hjälp av en TCP/IP-adress eller värdnamn
> * Ange IP-adressen för skrivaren
> * Skrivarport standard 9100
> * Om det behövs installerar du rätt skrivardrivrutin manuellt.
>
> ![Linux][Logo_Linux] Linux
>
> * som standard proceduren för att installera en skrivare i nätverket för Windows bara följa
> * Följ bara offentliga Linux-guider för [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) eller [Red Hat och Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) på hur du lägger till en skrivare.
>
>

- - -
![Nätverket utskrift][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Värdbaserad skrivare över SMB (delad skrivare) i scenariot för flera platser
Värdbaserad skrivare är inte nätverks-kompatibla avsiktligt. Men en värdbaserad skrivare kan delas mellan datorer i ett nätverk som skrivaren är ansluten till en dator som stängts av. Ansluta ditt företags nätverk plats-till-plats eller ExpressRoute och delar en lokal skrivare. SMB-protokollet använder NetBIOS i stället för DNS som på namntjänsten. NetBIOS-värdnamn kan skilja sig från DNS-värdnamn. Standard fallet är att NetBIOS-värdnamn och DNS-värdnamnet är identiska. DNS-domän visas ingen meningsfull i utrymmet för NetBIOS-namn. Fullständigt kvalificerade DNS-värdnamn som består av DNS-värdnamn och DNS-domän måste därför inte användas i området NetBIOS-namn.

Skrivarresursen identifieras med ett unikt namn i nätverket:

* Värdnamnet för den SMB-Server (alltid behövs).
* Namnet på resursen (alltid behövs).
* Namnet på domänen är om skrivaren delar inte i samma domän som SAP-system.
* Ett användarnamn och ett lösenord kan också krävas för att komma åt skrivarresursen.

Anvisningar:

- - -
> ![Windows][Logo_Windows] Windows
>
> Dela en lokal skrivare.
> Öppna Windows Explorer och ange resursnamnet för skrivaren i Azure-VM.
> En skrivare installationsguiden vägleder dig genom installationsprocessen.
>
> ![Linux][Logo_Linux] Linux
>
> Här följer några exempel på dokumentation om hur du konfigurerar nätverksskrivare i Linux eller, inklusive ett kapitel angående utskrift i Linux. Den fungerar på samma sätt i en virtuell Linux-dator så länge som den virtuella datorn är en del av en VPN-anslutning:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL eller Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>USB-skrivare (vidarebefordran skrivare)
Remote Desktop Services förmåga att ge användare åtkomst till sina enheter för lokal skrivare i en fjärrsession är inte tillgänglig i Azure.

- - -
> ![Windows][Logo_Windows] Windows
>
> Mer information om utskrift med Windows finns här: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrering av SAP Azure-system i korrigering och transportsystem (Proportionerlig) på flera platser
SAP ändrings- och Transport System (Proportionerlig) måste konfigureras för att exportera och importera transport begäran över system i miljön. Vi förutsätter att utveckling instanser av ett SAP-system (utveckling) finns i Azure medan kvalitetssäkring (kvalitetskontroll) och produktiva system (PRD) finns på plats. Vi förutsätter dessutom att det finns en central transport-katalog.

##### <a name="configuring-the-transport-domain"></a>Konfigurera Transport-domänen
Konfigurera din Transport-domän på datorn som du angav som domänkontrollant Transport enligt beskrivningen i [konfigurerar domänkontrollanten Transport](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). En systemanvändare TMSADM kommer att skapas och nödvändiga RFC-mål ska skapas. Du kan kontrollera dessa RFC-anslutningar med transaktionen SM59. Värdnamnsmatchning måste aktiveras i transport-domän.

Anvisningar:

* I vårt scenario beslutat lokala QAS systemet blir CTS-domänkontrollanten. Anropa transaktion STM. Dialogrutan Proportionerlig visas. En dialogruta för Konfigurera Transport domänen visas. (Den här dialogrutan visas bara om du ännu inte har konfigurerat en transport-domän.)
* Kontrollera att den automatiskt skapade användaren TMSADM har auktoriserats (SM59 -> ABAP anslutning -> TMSADM@E61.DOMAIN_E61 -> information -> Utilities(M) auktorisering Test ->). Den första skärmen i transaktionen STM ska visa att den här SAP-System nu fungerar som domänkontrollant för transport-domän som visas här:

![Första skärmen i transaktionen STM på domänkontrollanten][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inklusive SAP-system i Transport-domän
Sekvens med bland annat ett SAP-system i en domän för transport ser ut så här:

* Gå till transportsystem (klient 000) och anropa transaktion STM på DEV-system i Azure. Välj andra konfiguration från dialogrutan och fortsätter med inkludera System i domänen. Ange den domänkontrollant som målvärden ([inklusive SAP-system i domänen Transport](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systemet väntar nu som ska ingå i transport-domänen.
* Av säkerhetsskäl måste du sedan gå tillbaka till domänkontrollanten för att bekräfta din begäran. Välj Systemöversikt och Godkänn av systemet väntar. Bekräfta sedan Kommandotolken och konfigurationen som ska distribueras.

Den här SAP-system innehåller nu information om alla de andra SAP-system i transport-domän. Dessa data för det nya systemet SAP skickas till alla andra SAP-system på samma gång, och SAP-system har angetts i profilen för transport av kontrollprogrammet transport. Kontrollera om RFC: er och åtkomst till Transportkatalogen av domänen fungerar.

Fortsätta med konfigurationen av transportsystemet som vanligt som beskrivs i dokumentationen [ändrings- och Transport System](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Anvisningar:

* Kontrollera att din STM lokalt är rätt konfigurerad.
* Se till att värdnamnet för domänkontrollanten Transport kan lösas genom den virtuella datorn på Azure och vice visa.
* Anropet transaktion STM -> andra konfiguration -> Inkludera System i domänen.
* Bekräfta anslutningen i systemet Proportionerlig på plats.
* Konfigurera transport vägar, grupper och lager som vanligt.

I plats-till-plats anslutna mellan lokala vara scenarier, fördröjning mellan lokala och Azure fortfarande betydande. Om vi följa de för att transportera objekt via system för utveckling och testning till produktion eller Tänk dig tillämpa transporter eller supportpaket på olika system, som du Tänk på att, beroende på platsen för central transport-katalog några av systemen uppstår lång svarstid för läsning eller skrivning av data i transportkatalogen centrala. Situationen liknar SAP-landskap konfigurationer där olika system sprids via olika datacenter med betydande avståndet mellan datacentra.

För att undvika sådana svarstid och har de system som fungerar snabbt med att läsa eller skriva till eller från Transportkatalogen, som du kan ställa in två STM transport domäner (en för en lokal och en med system i Azure och länka transport-domäner. Kontrollera den här dokumentationen som beskriver principerna bakom detta begrepp i SAP-Proportionerlig: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Anvisningar:

* Konfigurera en transport domän på varje plats (lokalt och Azure) med hjälp av transaktionen STM <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Länka domänerna med en länk för domänen och bekräfta länken mellan de två domänerna.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuera konfigurationen av det länkade systemet.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC trafik mellan SAP-instanser i Azure och lokalt (mellan lokala)
RFC-trafik mellan system, som finns lokalt och i Azure måste fungera. Konfigurera en anslutning anrop transaktion SM59 i ett källsystem där du behöver definiera en RFC-anslutning på målsystemet. Konfigurationen liknar standardinställningen av en RFC-anslutning.

Vi förutsätter som flera platser för scenariot de virtuella datorerna som kör SAP-system som behöver kommunicera med varandra i samma domän. Därför skiljer inte installationen av en RFC-anslutning mellan SAP-system från steg för konfiguration och indata i scenarier på plats.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Åtkomst till lokala filresurser är från SAP-instanser i Azure eller tvärtom
SAP-instanser i Azure behöver åtkomst till filresurser som ingår i företagets lokaler. Dessutom måste en lokal SAP-instanser att komma åt filresurser som finns i Azure. Om du vill aktivera filresurserna, måste du konfigurera behörigheter och delningsalternativ på det lokala systemet. Se till att öppna portar i VPN eller ExpressRoute-anslutning mellan Azure och ditt datacenter.

## <a name="supportability"></a>Support
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure övervakningslösning för SAP
För att aktivera övervakning av viktiga SAP system på Azure SAP övervakningsverktyg SAPOSCOL eller SAP Värdagenten hämta data från tjänsten för Azure Virtual Machines värden via ett Azure Monitoring-tillägg för SAP. Eftersom kraven av SAP var specifik för SAP-program, valt Microsoft att inte Allmänt implementera funktioner i Azure, men lämna det så att kunder kan distribuera de nödvändiga komponenterna för övervakning och konfigurationer till sina virtuella Datorer som körs i Azure. Dock automatiskt distribution och livscykeln för hantering av övervakning komponenterna huvudsakligen av Azure.

#### <a name="solution-design"></a>Lösningsdesign
Lösningen har utvecklats för att aktivera övervakning för SAP baseras på arkitekturen i Azure VM-agenten och tillägg framework. Uppfattning om ramen för Azure VM-agenten och tillägg är att tillåta installation av programvara program tillgängliga i Azure VM-tillägg-galleriet på en virtuell dator. Principen tanken bakom det här konceptet är att tillåta (i fall Azure Monitoring-tillägg för SAP) distribution av särskilda funktioner till en virtuell dator och konfiguration av sådan programvara vid tidpunkten för distribution.

”Azure VM-agenten, som möjliggör hantering av specifika Azure VM-tillägg i den virtuella datorn är införs i Windows-datorer som standard på Skapa en virtuell dator i Azure-portalen. Vid SUSE, Red Hat eller Oracle Linux ingår VM-agenten redan i Azure Marketplace-avbildning. Om något skulle ladda upp en Linux VM från en lokal plats till Azure måste VM-agenten installeras manuellt.

De grundläggande byggstenarna i övervakning-lösning i Azure för SAP ser ut så här:

![Tillägget för Microsoft Azure-komponenter][planning-guide-figure-2400]

I block diagrammet ovan visas en del av lösningen för fjärrövervakning för SAP finns i Azure VM-avbildning och Azure-tillägg-galleriet, som är en globalt replikerade lagringsplats som hanteras av Azure-åtgärder. Det är ansvar för gemensamma SAP/MS-teamet arbetar med Azure implementering av SAP för att arbeta med Azure-åtgärder för att publicera nya versioner av Azure Monitoring-tillägg för SAP.

När du distribuerar en ny virtuell Windows-dator, läggs Azure VM-agenten automatiskt på den virtuella datorn. Funktionen för den här agenten är att samordna den läser in och konfigurationen av Azure-tillägg för övervakning av SAP NetWeaver-system. Virtuella datorer med Linux ingår Azure VM-agenten redan i Azure Marketplace OS-avbildningen.

Det är dock ett steg som måste utföras av kunden. Det här är aktivering och konfiguration av insamling av prestanda. Processen som rör konfigurationen automatiskt av en PowerShell-skript eller CLI-kommando. PowerShell-skript kan laddas ned i Microsoft Azure Script Center enligt beskrivningen i den [Distributionsguide][deployment-guide].

Det ser ut som den övergripande arkitekturen för den Azure övervakningslösningen för SAP:

![Azure övervakningslösning för SAP NetWeaver][planning-guide-figure-2500]

**För exakta instruktioner och detaljerade anvisningar om att använda dessa PowerShell-cmdletar eller CLI-kommando under distributioner, följ instruktionerna den [Distributionsguide][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrering av Azure finns SAP-instansen till SAProuter
SAP-instanser som körs i Azure måste vara tillgänglig från SAProuter samt.

![SAP-Router-nätverksanslutning][planning-guide-figure-2600]

En SAProuter möjliggör TCP/IP-kommunikation mellan deltagande system om det finns ingen direkt IP-anslutning. Detta ger fördelen att ingen slutpunkt till slutpunkt-anslutning mellan partner för kommunikation krävs på nätverksnivå. SAProuter lyssnar på port 3299 som standard.
Om du vill ansluta SAP-instanser via en SAProuter måste du ge SAProuter sträng och värden namnet med alla försök att ansluta.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Så här långt fokus i dokumentet har SAP NetWeaver i allmänhet eller SAP NetWeaver ABAP stack. I det här små avsnittet listas specifika överväganden för SAP-Java-stack. En av de viktigaste SAP NetWeaver Java-baserade exklusivt program är SAP Enterprise Portal. Andra SAP NetWeaver-baserade program som SAP PI och SAP-lösning Manager använder både SAP NetWeaver ABAP och Java-stacks. Därför finns det däremot inte behöver tänka på specifika aspekter relaterade till SAP NetWeaver Java-stacken.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
Installationen av en SAP-Portal i en Azure-dator skiljer sig inte från en på plats-installation om du distribuerar i scenarier med flera platser. Eftersom DNS görs av den lokala, kan du göra inställningar för enskilda instanser som konfigurerats lokalt. Rekommendationer och begränsningar som beskrivs i det här dokumentet hittills gäller generellt för ett program som SAP Enterprise Portal eller SAP NetWeaver Java-stack.

![Exponerade SAP-portalen][planning-guide-figure-2700]

En särskild distributionsscenariot av vissa kunder är direkt exponering av SAP Enterprise Portal till Internet när den virtuella värden är ansluten till företagets nätverk via plats-till-plats VPN-tunnel eller ExpressRoute. Du måste se till att specifika portar är öppna och inte blockeras av brandvägg eller nätverket säkerhetsgrupp för sådana situationer. Samma mechanics måste tillämpas när du vill ansluta till en SAP-Java-instans från en lokal plats i ett scenario med molnbaserad.

Den inledande URI är http (s):`<Portalserver`>: 5XX00/irj där porten som bildas av 50000 plus (Systemnumber rubrikrad? 100). Standard portal URI SAP systemet 00 är `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. Mer information, har du en titt på <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Konfiguration av tjänstslutpunkt][planning-guide-figure-2800]

Om du vill anpassa URL och/eller portar för din SAP Enterprise Portal kontrollerar du den här dokumentationen:

* [Ändra Portal-URL](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Ändra standardportnumren, Portal-portnummer](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Hög tillgänglighet (HA) och Disaster Recovery (DR) för SAP NetWeaver på Azure virtuella datorer som körs
### <a name="definition-of-terminologies"></a>Definition av termer
Termen **hög tillgänglighet (HA)** beror vanligtvis på en uppsättning tekniker som minimerar avbrott IT genom att tillhandahålla affärskontinuitet för IT-tjänster via redundanta, feltoleranta eller redundans skyddade komponenter i den **samma** datacenter. I vårt fall inom en Azure-Region.

**Haveriberedskap (DR)** är också mål genom att minimera störningar för IT-tjänster och deras återställning men över **olika** datacenter, som vanligtvis finns hundratals kilometer bort. I vårt fall vanligtvis mellan olika Azure-regioner inom samma geopolitiska region eller som skapats av dig som kund.

### <a name="overview-of-high-availability"></a>Översikt över hög tillgänglighet
Vi kan avgränsa diskussion om SAP hög tillgänglighet i Azure i två delar:

* **Hög tillgänglighet för Azure-infrastrukturen**, till exempel HA av beräkning (VM), nätverk, lagring osv och dess fördelar för att öka tillgängligheten för SAP-program.
* **Hög tillgänglighet för SAP-program**, till exempel programvarukomponenter som hög tillgänglighet för SAP:
  * SAP-programservrar
  * SAP ASCS/SCS-instans
  * Databasservrar

och hur den kan kombineras med Azure-infrastrukturen hög tillgänglighet.

Hög tillgänglighet för SAP i Azure har vissa skillnader i jämförelse med hög tillgänglighet för SAP i en lokal fysisk eller virtuell miljö. Följande dokument från SAP beskriver vanliga hög tillgänglighet för SAP-konfigurationer i virtualiserade miljöer på Windows: <http://scn.sap.com/docs/DOC-44415>. Det finns ingen sapinst-integrerade SAP-HA konfiguration för Linux som om det finns för Windows. Om SAP hög tillgänglighet lokalt för Linux finns mer information här: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Hög tillgänglighet för Azure-infrastrukturen
Det finns för närvarande en enda VM-serviceavtal på 99,9%. Du kan skapa produkten av de olika tillgängliga Azure-serviceavtal för att få en uppfattning om hur tillgängligheten för en enskild virtuell dator kan se ut: <https://azure.microsoft.com/support/legal/sla/>.

Basen för beräkningen är 30 dagar per månad eller 43200 minuter. Därför motsvarar 0,05% avbrottstid 21,6 minuter. Tillgängligheten för de olika tjänsterna kommer som vanligt multiplicera på följande sätt:

(Tillgänglighet Service #1/100) * (tillgänglighet Service #2/100) * (tillgänglighet Service #3/100) 

Exempel:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 eller en övergripande tillgänglighet 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Hög tillgänglighet för virtuell dator (VM)
Det finns två typer av händelser i Azure-plattformen som kan påverka tillgängligheten för dina virtuella datorer: planerat och oplanerat underhåll.

* Planerat underhåll är periodiska uppdateringar av Microsoft den underliggande Azure-plattformen att förbättra tillförlitligheten, prestanda och säkerhet för den plattformsinfrastruktur som dina virtuella datorer som körs på.
* Oplanerat underhållshändelser inträffar när ett fel i maskinvaran eller fysiska infrastrukturen som den virtuella datorn har uppstått på något sätt. Det kan vara lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När sådant fel upptäcks migrerar Azure-plattformen automatiskt den virtuella datorn från den felaktiga fysiska servern som är värd för den virtuella datorn till en felfri fysisk server. Den här typen av händelser kan också göra att den virtuella datorn startas om, men det är ovanligt.

Mer information finns i den här dokumentationen: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure lagringsredundans
Data i Microsoft Azure Storage-konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, vilket uppfyller SLA för Azure Storage som även körs vid tillfälliga maskinvarufel.

Eftersom Azure Storage är att ha tre bilder av data som standard, RAID5 eller RAID1 över flera Azure-diskar är inte nödvändigt.

Mer information finns i den här artikeln: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Använda Azure-infrastrukturen omstart av virtuella datorer för att uppnå högre tillgänglighet för SAP-program
Om du inte väljer att använda funktioner som t.ex. Windows Server Failover Clustering (WSFC) eller Pacemaker på Linux (för närvarande stöds endast för SLES 12 och senare), starta om Azure VM används för att skydda ett SAP-System mot planerade och oplanerade driftavbrott på Azure fysisk infrastruktur och övergripande underliggande Azure-plattformen.

> [!NOTE]
> Det är viktigt att nämna att starta om Azure VM främst skyddar virtuella datorer och inte program. Starta om VM inte erbjuder hög tillgänglighet för SAP-program, men den erbjuder en viss nivå av infrastrukturens tillgänglighet och därför indirekt högre tillgänglighet för SAP-system. Det finns också inget serviceavtal för den tid det tar att starta om en virtuell dator efter en planerad eller oplanerad värd-avbrott. Den här metoden för hög tillgänglighet är därför inte lämpligt för viktiga komponenter i ett SAP-system som (A) SCS eller DBMS.
>
>

Ett annat viktigt infrastruktur-element för hög tillgänglighet är ett lagringsutrymme. SERVICEAVTAL för Azure Storage är till exempel 99,9% tillgänglighet. Om en distribuerar alla virtuella datorer med dess diskar till en enda Azure Storage-konto, potentiella Azure Storage otillgänglighet leder till att alla virtuella datorer som placeras i den Azure Storage-konto och även alla SAP komponenter som körs i dessa virtuella datorer.  

I stället för att placera alla virtuella datorer i en enda Azure Storage-konto, kan du också använda dedikerade konton för varje virtuell dator och på så vis öka övergripande tillgänglighet för virtuell dator och SAP-program med hjälp av flera oberoende Azure Storage-konton.

Azure Managed Disks placeras automatiskt i Feldomän av den virtuella datorn som de är kopplade till. Om du placerar två virtuella datorer i en tillgänglighetsuppsättning och använda Managed Disks, plattformen tar hand om distribuera Managed Disks i olika Feldomäner samt. Om du tänker använda Premium Storage rekommenderar vi använder hantera diskar samt.

En exempel-arkitekturen för en SAP NetWeaver-system som använder Azure-infrastrukturen hög tillgänglighet och storage-konton kan se ut så här:

![Använda Azure-infrastrukturen hög tillgänglighet att uppnå högre tillgänglighet för SAP-program][planning-guide-figure-2900]

En exempel-arkitekturen för en SAP NetWeaver-system som använder Azure-infrastrukturen hög tillgänglighet och hanterade diskar kan se ut så här:

![Använda Azure-infrastrukturen hög tillgänglighet att uppnå högre tillgänglighet för SAP-program][planning-guide-figure-2901]

För viktiga SAP-komponenter uppnått vi följande hittills:

* Hög tillgänglighet för SAP-programservrar (AS)

  SAP-programserverinstanser är redundanta komponenter. Var och en SAP som instans distribueras på en egen virtuell dator som körs i en annan Azure-fel- och Uppgraderingsdomänen (se kapitel [Feldomäner] [ planning-guide-3.2.1] och [uppgradera domäner] [ planning-guide-3.2.2]). Detta säkerställs genom att använda Azure-Tillgänglighetsuppsättningar (finns i kapitlet [Azure-Tillgänglighetsuppsättningar][planning-guide-3.2.3]). Potentiella planerad eller oplanerad otillgänglig en Azure-fel eller uppgradera domän leder otillgänglighet för ett begränsat antal virtuella datorer med sina SAP-AS instanser.

  Var och en SAP eftersom instansen placeras i en egen Azure Storage-konto – potentiella otillgängliga för en Azure Storage-konto medför att endast en virtuell dator med sin SAP-AS instans. Men tänk på att det finns en gräns på Azure Storage-konton inom en Azure-prenumeration. För att säkerställa automatisk start av (A) SCS-instans efter omstart av virtuell dator, se till att ange Autostart-parametern i (A) SCS-instans starta profil som beskrivs i kapitlet [med Autostart för SAP instanser][planning-guide-11.5].
  Läs också kapitel [hög tillgänglighet för SAP-programservrar] [ planning-guide-11.4.1] för mer information.

  Även om du använder Managed Disks diskarna lagras också i ett Azure Storage-konto och kan vara otillgänglig i händelse av driftstörningar.

* *Högre* tillgänglighet för SAP (A) SCS-instans

  Här använder vi Azure virtuell dator startas om för att skydda den virtuella datorn med installerade SAP (A) SCS-instans. När det gäller planerade eller oplanerade driftstopp för Azure servrar, virtuella datorer kommer att startas om på en annan server. Såsom nämnts tidigare skyddar Azure VM starta om främst virtuella datorer och inte program i det här fallet (A) SCS-instans. Via den virtuella datorn startar om kontaktar vi indirekt högre tillgänglighet för SAP (A) SCS-instans. För att försäkra dig om automatisk start av (A) SCS-instans efter omstart av virtuell dator, se till att ange Autostart-parametern i (A) SCS-instans start-profil som beskrivs i kapitlet [med Autostart för SAP instanser][planning-guide-11.5]. Det innebär att (A) SCS-instans som en enskilda felpunkter (SPOF) som körs i en enda virtuell dator är bestämmande faktor för tillgängligheten för hela SAP-landskap.

* *Högre* tillgängligheten för DBMS-Server

  Här kan liknar användningsfall för SAP (A) SCS-instans, vi använda Azure virtuella datorer startas om för att skydda den virtuella datorn med installerad programvara för DBMS och vi uppnå högre tillgänglighet för DBMS-programvara genom att starta om virtuell dator.
  DBMS som körs i en enda virtuell dator är också en SPOF och det är den bestämmande faktorn för tillgängligheten för hela SAP-landskap.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Tillgängligheten av SAP-program på Azure IaaS
Vi behöver skydda alla viktiga SAP-systemkomponenter, för exempel redundanta SAP-programservrar och unika komponenter (till exempel enskilda felpunkter) som SAP (A) SCS-instans och DBMS för att uppnå full SAP system hög tillgänglighet.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hög tillgänglighet för SAP-programservrar
För SAP-servrar/dialogrutan programinstanserna behöver du inte tycker om lösning för specifika hög tillgänglighet. Hög tillgänglighet uppnås genom redundans och därmed att ha tillräckligt med dem i olika virtuella datorer. De ska alla placeras i samma Azure Tillgänglighetsuppsättning att undvika att de virtuella datorerna kan uppdateras samtidigt under planerat underhåll avbrott. Grundläggande funktioner, som bygger på olika uppgraderingen och Feldomäner i en Azure-skalenhet introducerades redan i kapitlet [uppgradera domäner][planning-guide-3.2.2]. Azure Tillgänglighetsuppsättningar presenteras i kapitlet [Azure-Tillgänglighetsuppsättningar] [ planning-guide-3.2.3] i det här dokumentet.

Det finns inga obegränsat antal fel- och uppgradera domäner som kan användas av en Azure-Tillgänglighetsuppsättning i en Skalningsenhet för Azure. Det innebär att placera ett antal virtuella datorer i en Tillgänglighetsuppsättning, förr eller senare mer än en virtuell dator som hamnar i samma fel eller Uppgraderingsdomänen.

Distribuera några SAP programserverinstanser i sina dedikerade virtuella datorer och förutsatt att fem uppgradera domäner vi finns växer på följande bild i slutet. Det maximala antalet fel- och uppdateringsdomäner i en tillgänglighetsuppsättning kan ändras i framtiden:

![Hög tillgänglighet för SAP-programservrar i Azure][planning-guide-figure-3000]

Mer information finns i den här dokumentationen: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hög tillgänglighet för SAP Central Services på Azure
Arkitektur med hög tillgänglighet i SAP Central Services på Azure, finns i artikeln [arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) som informationen. Artikeln pekar på mer detaljerade beskrivningar för specifika operativsystem.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hög tillgänglighet för SAP-databasinstans
SAP DBMS HA brukar baseras på två DBMS-datorer där DBMS hög tillgänglighet funktioner används för att replikera data från den aktiva DBMS-instansen till den andra virtuella datorn till en passiv DBMS-instans.

Hög tillgänglighet och Disaster recovery-funktionen för DBMS i allmänhet samt så specifik DBMS beskrivs i den [DBMS Distributionsguide][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Hög tillgänglighet från slutpunkt till slutpunkt för fullständig SAP-System
Här följer två exempel på en fullständig SAP NetWeaver HA arkitektur i Azure – en för Windows och en för Linux.

Ohanterade diskar endast: koncept som beskrivs nedan kan behöva äventyras lite när du distribuerar flera SAP-system och antalet virtuella datorer som distribueras överskrider den maximala gränsen på Storage-konton per prenumeration. I sådana fall måste virtuella hårddiskar för virtuella datorer kan kombineras i ett Lagringskonto. Vanligtvis vill du göra det genom att kombinera virtuella hårddiskar för SAP-programnivån virtuella datorer i olika SAP-system.  Vi kan också kombineras olika virtuella hårddiskar på olika DBMS virtuella datorer av olika SAP-system i en Azure Storage-konto. Därmed Tänk på att IOPS-gränserna för Azure Storage-konton (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Hög tillgänglighet på Windows
![SAP NetWeaver programarkitektur hög tillgänglighet med SQLServer på Azure IaaS][planning-guide-figure-3200]

Följande Azure konstruktioner används för SAP NetWeaver-systemet för att minimera inverkan problem för infrastruktur och hantera korrigeringar:

* Hela systemet distribueras på Azure (obligatoriskt – DBMS layer, (A) SCS-instans och fullständig programnivån måste köra på samma plats).
* Hela systemet körs inom en Azure-prenumeration (krävs).
* Hela systemet körs inom ett virtuellt Azure-nätverk (krävs).
* Uppdelning av de virtuella datorerna i ett SAP-system i tre Tillgänglighetsuppsättningar är möjligt även med alla de virtuella datorerna som hör till samma virtuella nätverk.
* Alla virtuella datorer som kör DBMS instanser av en SAP-system är i en Tillgänglighetsuppsättning. Vi förutsätter att det finns fler än en virtuell dator som kör DBMS instanser per system eftersom interna DBMS-hög tillgänglighet funktioner används som SQL Server AlwaysOn- eller Oracle Data Guard.
* Alla virtuella datorer som kör DBMS instanser använder sina egna storage-konto. DBMS filer för data och loggfiler replikeras till ett annat lagringskonto med hjälp av funktioner för DBMS hög tillgänglighet som synkroniserar data från ett lagringskonto. Otillgänglig för en storage-kontot genereras otillgänglig för en SQL-Windows-klusternod, men inte hela SQL Server-tjänsten.
* Alla virtuella datorer som kör en SAP-system (A) SCS-instans är i en Tillgänglighetsuppsättning. Ett Windows Server Failover kluster (WSFC) har konfigurerats i dessa virtuella datorer för att skydda (A) SCS-instans.
* Alla virtuella datorer (A) SCS-instanser körs använder sina egna storage-konto. (A) SCS-instansfiler och mappar som SAP globala replikeras från ett lagringskonto till ett annat lagringskonto med SIOS DataKeeper-replikering. Otillgänglig för ett lagringskonto leder otillgänglig för en nod i klustret (A) SCS-Windows, men inte hela (A) SCS-tjänsten.
* ALLA virtuella datorer som representerar SAP-programnivån för server i en tredje anges tillgänglighet.
* ALLA virtuella datorer som kör SAP-programservrar använder sina egna storage-konto. Otillgänglig för ett lagringskonto innebär att en server för SAP-program, där andra SAP-programservrar fortsätter att köras.

Följande bild illustreras samma liggande med Managed Disks.

![SAP NetWeaver programarkitektur hög tillgänglighet med SQLServer på Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Hög tillgänglighet på Linux
Arkitektur för SAP hög tillgänglighet på Linux på Azure är i princip samma som för Windows som beskrivs ovan. Referera till SAP-kommentar [1928533] för en lista över lösningar för hög tillgänglighet som stöds.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Med hjälp av Autostart för SAP-instanser
SAP erbjuder funktioner för att starta SAP instanser omedelbart efter början av Operativsystemet i den virtuella datorn. De exakta stegen finns dokumenterade i SAP Knowledge Base-artikeln [1909114]. Men SAP inte rekommenderar använder inställningen längre eftersom det finns ingen kontroll i den ordning som omstarter av instans, förutsatt att mer än en virtuell dator har påverkas eller flera instanser körde per virtuell dator. Under förutsättning att ett typiskt Azure scenario där en SAP application server-instans i en virtuell dator och en enda virtuell dator så småningom få startas om, Autostart är inte viktigt och kan aktiveras genom att lägga till den här parametern:

    Autostart = 1

I start-profilen för SAP ABAP och/eller Java-instans.

> [!NOTE]
> Autostart-parameter kan ha vissa downfalls samt. I detalj utlöser parametern i början av en SAP ABAP eller Java-instans när relaterade Windows/Linux-tjänsten för instansen har startats. Det är däremot fallet när operativsystemet startas. Dock omstarter av SAP-tjänster är också en gemensam sak för livscykelhantering för SAP Software funktioner som summa eller andra uppdateringar eller uppgraderar. Dessa funktioner inte förväntar sig en instans startas automatiskt alls. Autostart-parametern ska vara inaktiverat innan du kör sådana uppgifter. Autostart-parameter bör också inte användas för SAP-instanser som är klustrade visas som ASCS/SCS/CI.
>
>

Se ytterligare information om autostart för SAP-instanser här:

* [Starta/Stoppa SAP tillsammans med din Unix Server Starta/Stoppa](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starta och stoppa Hanteringsagenter för SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Så här aktiverar du automatisk Start av HANA-databas](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Större 3 nivåer SAP-system
Hög tillgänglighet aspekter av 3 nivåer SAP-konfigurationer har beskrivs i tidigare avsnitt redan. Men vad gäller system där DBMS-serverkrav är för stort för den finns i Azure, men SAP-programnivån kan distribueras till Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Platsen för 3 nivåer SAP-konfigurationer
Det går inte att dela upp programnivån själva eller program och DBMS på nivån mellan lokala och Azure. Ett SAP-system är helt distribuerade lokalt eller i Azure. Det stöds inte heller om du vill att vissa av de programservrar som kör lokalt och vissa andra i Azure. Det är startpunkten för diskussionen. Vi inte finns stöd för DBMS-komponenterna i ett SAP-system och SAP server programnivån distribueras i två olika Azure-regioner. Till exempel DBMS i västra USA och SAP-programnivån i centrala USA. Orsaken till inte har stöd för sådana konfigurationer är svarstid känslighet för SAP NetWeaver-arkitekturen.

Men under loppet av förra året data center partner som har utvecklats delad platser till Azure-regioner. De här delade platserna är ofta sig nära den fysiska Azure data datacenter i en Azure-Region. Korta avstånd och anslutning av tillgångar i samplacering via ExpressRoute i Azure kan resultera i en svarstid som är mindre än 2 MS. I sådana fall för att hitta DBMS-lagret (inklusive storage SAN/NAS) i en delad plats och SAP är programnivån i Azure möjligt. [HANA stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline säkerhetskopiering av SAP-system
Beroende på hur SAP valt (nivå 2 eller 3 nivåer) det kan vara nödvändigt att säkerhetskopiera. Innehållet i Virtuellt datorn plus ha en säkerhetskopia av databasen. DBMS-relaterade säkerhetskopieringarna förväntas göras med metoderna i databasen. En detaljerad beskrivning för olika databaser finns i [DBMS-Guide][dbms-guide]. Å andra sidan, kan SAP-data säkerhetskopieras i ett offline sätt (inklusive databasinnehåll samt) enligt beskrivningen i det här avsnittet eller online enligt beskrivningen i nästa avsnitt.

Offlinesäkerhetskopiering kräver i praktiken stänga av den virtuella datorn via Azure portal och en kopia av grundläggande VM-disken plus alla anslutna diskar till den virtuella datorn. Detta skulle bevarar en tidpunkt i tid bild av den virtuella datorn och dess associerade diskar. Det rekommenderas att kopiera säkerhetskopior av till en annan Azure Storage-konto. Därför proceduren som beskrivs i kapitlet [kopierar diskar mellan Azure Storage-konton] [ planning-guide-5.4.2] i det här dokumentet skulle tillämpas.
Förutom avstängningen kan med Azure-portalen en också göra det via Powershell eller CLI enligt nedan: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

En återställning av det aktuella tillståndet skulle består av att ta bort grundläggande VM samt de ursprungliga diskarna för den grundläggande virtuella datorn och monterade diskar, kopiera sparade diskarna till den ursprungliga Storage-konto eller resurs-gruppen för hanterade diskar och omdistribuera på systemet.
Den här artikeln visar ett exempel så den här processen i Powershell-skript: <http://www.westerndevs.com/azure-snapshots/>

Kontrollera att du installerar en ny licens för SAP eftersom när du återställer en säkerhetskopiering av virtuella datorer enligt beskrivningen ovan skapas en ny maskinvarunyckel.

### <a name="online-backup-of-an-sap-system"></a>Onlinesäkerhetskopieringen av ett SAP-system
Säkerhetskopiering av DBMS utförs med DBMS-specifika metoder som beskrivs i den [DBMS-Guide][dbms-guide].

Andra virtuella datorer i SAP-system kan säkerhetskopieras med hjälp av Azure VM Backup-funktionerna. Azure VM Backup är en standardmetod för att säkerhetskopiera en hel virtuell dator i Azure. Azure Backup lagrar säkerhetskopior i Azure och gör en återställning av en virtuell dator igen.

> [!NOTE]
> Från och med december 2015 med säkerhetskopiering av virtuella datorer sparar inte unikt ID för virtuell dator som används för SAP-licensiering. Det innebär att en återställning från en säkerhetskopiering av virtuella datorer kräver installation av en ny SAP-licensnyckel som den återställda virtuella datorn anses vara en ny virtuell dator och inte en ersättning av den förstnämnda som har sparats.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretiskt sett kan virtuella datorer som kör databaser kan säkerhetskopieras på ett konsekvent sätt om DBMS-systemet har stöd för Windows-VSS (Volume Shadow Copy-tjänsten <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) som, till exempel SQL Server gör.
> Tänk dock som baseras på Virtuella Azure-säkerhetskopieringar point-in-time återställer databaser inte är möjligt. Rekommendationen är därför att säkerhetskopiera databaser med funktioner för DBMS i stället för en säkerhetskopiering av Azure virtuella datorer.
>
> Att bekanta dig med Azure VM Backup börja här: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Andra möjligheterna är att använda en kombination av Microsoft Data Protection Manager har installerats i en virtuell dator i Azure och Azure Backup till databaser för säkerhetskopiering/återställning. Mer information finns här: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Det finns ingen motsvarighet till Windows VSS i Linux. Endast filkonsekvent säkerhetskopiering är därför möjligt men inte programkonsekventa säkerhetskopior. SAP DBMS-säkerhetskopieringen ska göras med hjälp av DBMS-funktioner. Filsystem som omfattar alla SAP-relaterade data kan sparas, till exempel använder tar som beskrivs här: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure som DR-plats för produktion SAP-landskap
Sedan Mid 2014 aktivera tillägg till olika komponenterna för Hyper-V, System Center och Azure användningen av Azure som DR-plats för virtuella datorer som körs lokalt baserat på Hyper-V.

En blogg med information om hur du distribuerar den här lösningen dokumenteras här: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Sammanfattning
De viktigaste syftena med hög tillgänglighet för SAP-system i Azure är:

* Vid denna tidpunkt, kan inte den SAP felpunkt skyddas exakt samma sätt som det kan göras i lokala distributioner. Det beror på den delade disken kluster inte kan ännu skapas i Azure utan att använda 3 tillverkare.
* Du måste använda DBMS-funktioner som inte är beroende av delad disk kluster teknik för DBMS-lager. Information som finns dokumenterade i den [DBMS-Guide][dbms-guide].
* Om du vill minimera effekten av problem inom Feldomäner i Azure-infrastruktur eller värden underhållet, använder du Azure-Tillgänglighetsuppsättningar:
  * Vi rekommenderar att ha en Tillgänglighetsuppsättning för SAP-programnivån.
  * Vi rekommenderar att ha en separat Tillgänglighetsuppsättning för SAP DBMS-lagret.
  * Det rekommenderas inte att tillämpa samma Tillgänglighetsuppsättning för virtuella datorer i olika SAP-system.
  * Det rekommenderas att använda Premium Managed Disks.
* För säkerhetskopiering av SAP DBMS-lagret, kontrollera den [DBMS-Guide][dbms-guide].
* Säkerhetskopiering av SAP-dialogrutan instanser är lite meningsfullt eftersom det går snabbare att distribuera om enkel dialogruta instanser.
* Säkerhetskopiera den virtuella datorn som innehåller den globala katalogen för SAP-system och med det alla profiler för olika instanser meningsfullt och bör utföras med Windows Säkerhetskopiering eller, till exempel tar-filen på Linux. Eftersom det finns skillnader mellan Windows Server 2008 (R2) och Windows Server 2012 (R2), som gör det enklare att säkerhetskopiera med nyare Windows-Server versioner, vi rekommenderar att du kör Windows Server 2012 (R2) som gästoperativsystem för Windows.

##<a name="next-steps"></a>Nästa steg
Läs artiklarna:

- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure] (https://docs.microsoft.com/
- Azure/virtuella-datorer/arbetsbelastningar/sap/hana-vm-åtgärder)