---
title: 'SAP på Azure: Planerings- och implementeringsguide'
description: Planering och implementering av virtuella Azure-datorer för SAP NetWeaver
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
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ddcc5165f5588ff9015d7fafbc2b822268ffea7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337166"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planering och implementering av virtuella Azure-datorer för SAP NetWeaver

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



Microsoft Azure gör det möjligt för företag att skaffa beräknings- och lagringsresurser på minimal tid utan långa anskaffningscykler. Azure Virtual Machine-tjänsten gör det möjligt för företag att distribuera klassiska program, till exempel SAP NetWeaver-baserade program i Azure och utöka sin tillförlitlighet och tillgänglighet utan att ha ytterligare resurser tillgängliga lokalt. Azure Virtual Machine Services stöder också anslutning mellan lokala anslutningar, vilket gör det möjligt för företag att aktivt integrera Virtuella Azure-datorer i sina lokala domäner, sina privata moln och deras SAP-systemlandskap.
Det här faktabladet beskriver grunderna i Microsoft Azure Virtual Machine och ger en genomgång av planerings- och implementeringsöverväganden för SAP NetWeaver-installationer i Azure och som sådan bör vara det dokument som ska läsas innan du påbörjar faktiska distributioner av SAP NetWeaver på Azure.
Papperet kompletterar SAP-installationsdokumentationen och SAP Notes, som representerar de primära resurserna för installationer och distributioner av SAP-programvara på givna plattformar.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Sammanfattning
Cloud Computing är en allmänt använd term, som får allt större betydelse inom IT-branschen, från små företag upp till stora och multinationella företag.

Microsoft Azure är Cloud Services-plattformen från Microsoft, som erbjuder ett brett spektrum av nya möjligheter. Nu kunder kan snabbt etablera och avetablering program som en tjänst i molnet, så de är inte begränsade till tekniska eller budgetering begränsningar. I stället för att investera tid och budget i maskinvaruinfrastruktur kan företag fokusera på programmet, affärsprocesserna och dess fördelar för kunder och användare.

Med Microsoft Azure Virtual Machine-tjänsterna, erbjuder Microsoft en omfattande plattform för infrastruktur som en tjänst (IaaS). SAP NetWeaver-baserade program stöds på virtuella Azure-datorer (IaaS). Det här faktablad beskriver hur du planerar och implementerar SAP NetWeaver-baserade program i Microsoft Azure som den plattform du väljer.

Själva dokumentet fokuserar på två huvudaspekter:

* Den första delen beskriver två distributionsmönster som stöds för SAP NetWeaver-baserade program på Azure. Den beskriver också allmän hantering av Azure med SAP-distributioner i åtanke.
* Den andra delen beskriver genomförandet av de olika scenarier som beskrivs i den första delen.

Ytterligare resurser finns i kapitel [Resurser][planning-guide-1.2] i det här dokumentet.

### <a name="definitions-upfront"></a>Definitioner i förskott
I hela dokumentet använder vi följande termer:

* IaaS: Infrastruktur som en tjänst
* PaaS: Plattform som en tjänst
* SaaS: Programvara som en tjänst
* SAP-komponent: ett enskilt SAP-program som ECC, BW, Solution Manager eller S/4HANA.  SAP-komponenter kan baseras på traditionella ABAP- eller Java-tekniker eller ett icke-NetWeaver-baserat program som affärsobjekt.
* SAP-miljö: en eller flera SAP-komponenter som logiskt grupperats för att utföra en affärsfunktion som utveckling, QAS, Utbildning, DR eller Produktion.
* SAP-landskap: Den här termen refererar till hela SAP-tillgångarna i en kunds IT-landskap. SAP-landskapet omfattar alla produktions- och icke-produktionsmiljöer.
* SAP System: Kombinationen av DBMS lager och tillämpning lager av, till exempel, en SAP ERP utvecklingssystem, SAP BW testsystem, SAP CRM produktionssystem, etc. I Azure-distributioner stöds det inte att dela upp dessa två lager mellan lokala och Azure. Innebär att ett SAP-system antingen distribueras lokalt eller så distribueras det i Azure. Du kan dock distribuera de olika systemen i ett SAP-landskap till antingen Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings- och testsystemen i Azure men SAP CRM-produktionssystemet lokalt.
* Korslokaler eller hybrid: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, multi-site- eller ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanlig Azure-dokumentation beskrivs även dessa typer av distributioner som korslokala scenarier eller hybridscenarier. Anledningen till anslutningen är att utöka lokala domäner, lokala Active Directory/OpenLDAP och lokal DNS till Azure. Det lokala landskapet utökas till Azure-tillgångarna för prenumerationen. Med det här tillägget kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare av den lokala domänen kan komma åt servrarna och kan köra tjänster på dessa virtuella datorer (till exempel DBMS-tjänster). Kommunikation och namnmatchning mellan virtuella datorer som distribueras lokalt och Azure-distribuerade virtuella datorer är möjlig. Detta är det vanligaste och nästan exklusiva fallet att distribuera SAP-resurser till Azure. Mer information finns i [den här][vpn-gateway-cross-premises-options] artikeln och [den här][vpn-gateway-site-to-site-create].
* Azure Monitoring Extension, förbättrad övervakning och Azure-tillägg för SAP: Beskriv ett och samma objekt. Den beskriver ett VM-tillägg som måste distribueras av dig för att tillhandahålla grundläggande data om Azure-infrastrukturen till SAP-värdagenten. SAP i SAP-anteckningar kan referera till det som övervakningstillägg eller förbättrad övervakning. I Azure refererar vi till det som **Azure Extension för SAP**.

> [!NOTE]
> Korslokala eller hybriddistributioner av SAP-system där Azure Virtual Machines som kör SAP-system är medlemmar i en lokal domän stöds för produktions-SAP-system. Konfigurationer över flera lokaler eller hybrider stöds för distribution av delar eller fullständiga SAP-landskap i Azure. Även kör hela SAP-landskapet i Azure kräver att dessa virtuella datorer är en del av lokala domän och ADS / OpenLDAP. 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Resurser
Startpunkten för SAP-arbetsbelastningen i Azure-dokumentationen finns [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Från och med den här startpunkten hittar du många artiklar som täcker ämnena:

- SAP NetWeaver och Business One på Azure
- SAP DBMS-guider för olika DBMS-system i Azure
- Hög tillgänglighet och haveriberedskap för SAP-arbetsbelastning på Azure
- Specifik vägledning för att köra SAP HANA på Azure
- Vägledning som är specifik för Azure HANA Stora instanser för SAP HANA DBMS 


> [!IMPORTANT]
> Om möjligt används en länk till de hänvisande SAP-installationsguiderna eller annan SAP-dokumentation <http://service.sap.com/instguides>(Reference InstGuide-01, se ). När det gäller förutsättningarna, installationsprocessen eller detaljer om specifika SAP-funktioner bör SAP-dokumentationen och guiderna alltid läsas noggrant, eftersom Microsoft-dokumenten endast täcker specifika uppgifter för SAP-programvara som installeras och drivs i en Microsoft Azure virtuell dator.
>
>

Följande SAP-anteckningar är relaterade till ämnet SAP på Azure:

| Antecknar numrerar | Titel |
| --- | --- |
| [1928533] |SAP-program på Azure: Produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Felsöka förbättrad Azure-övervakning för SAP |
| [2178632] |Viktiga övervakningsmått för SAP på Microsoft Azure |
| [1409604] |Virtualisering i Windows: Förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: Förbättrad övervakning |
| [2243692] |Linux på Microsoft Azure (IaaS) VM: SAP-licensproblem |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsanteckningar |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP Installation och uppgradering |
| [1597355] |Rekommendation om växlingsutrymme för Linux |

Läs även [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP Notes för Linux.

Allmänna standardbegränsningar och maximala begränsningar för Azure-prenumerationer finns i [den här artikeln][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Möjliga scenarier
SAP ses ofta som en av de mest verksamhetskritiska programmen inom företag. Arkitekturen och driften av dessa program är mestadels komplexa och att se till att du uppfyller kraven på tillgänglighet och prestanda är viktigt.

Således företag måste tänka noga på vilken molnleverantör att välja för att köra sådana affärskritiska affärsprocesser på. Azure är den perfekta offentliga molnplattformen för affärskritiska SAP-program och affärsprocesser. Med tanke på det stora utbudet av Azure-infrastruktur kan nästan alla befintliga SAP NetWeaver- och S/4HANA-system vara värd i Azure idag. Azure tillhandahåller virtuella datorer med många Terabyte minne och mer än 200 processorer. Utöver det Azure erbjuder [HANA stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), som tillåter skala upp HANA-distributioner på upp till 24 TB och SAP HANA-utskalningsdistributioner på upp till 120 TB. Man kan konstatera idag att nästan alla lokala SAP-scenarier kan köras i Azure också. 

En grov beskrivning av scenarierna och vissa scenarier som inte stöds finns i dokument [SAP-arbetsbelastningen på azure-scenarier som stöds](./sap-planning-supported-configurations.md)av den virtuella datorn .

Kontrollera dessa scenarier och några av de villkor som nämndes som inte stöds i den refererade dokumentationen under hela planeringen och utvecklingen av din arkitektur som du vill distribuera till Azure.

Totalt sett är det vanligaste distributionsmönstret ett scenario över flera lokala

![VPN med anslutning mellan plats och plats (korslokalitet)][planning-guide-figure-300]

Anledningen till att många kunder tillämpar ett distributionsmönster mellan lokala är det faktum att det är mest transparent för alla program att utöka lokala till Azure med Azure med Azure ExpressRoute och behandla Azure som virtuellt datacenter. I takt med att fler och fler resurser flyttas till Azure växer den Azure-distribuerade infrastrukturen och nätverksinfrastrukturen och de lokala tillgångarna minskar i enlighet med detta. Allt öppet för användare och applikationer.

För att kunna distribuera SAP-system till antingen Azure IaaS eller IaaS i allmänhet är det viktigt att förstå de betydande skillnaderna mellan erbjudanden från traditionella outsourcers eller hosters och IaaS-erbjudanden. Medan den traditionella värdbäraren eller outsourcer anpassar infrastruktur (nätverk, lagring och servertyp) till den arbetsbelastning en kund vill vara värd för, är det istället kundens eller partnerns ansvar att karakterisera arbetsbelastningen och välja rätt Azure komponenter i virtuella datorer, lagring och nätverk för IaaS-distributioner.

För att samla in data för planering av distributionen till Azure är det viktigt att:

- Utvärdera vilka SAP-produkter som stöds körs i virtuella Azure-datorer
- Utvärdera vilka specifika operativsystemversioner som stöds med specifika virtuella Azure-datorer för dessa SAP-produkter
- Utvärdera vilka DBMS-versioner som stöds för dina SAP-produkter med specifika virtuella Azure-datorer
- Utvärdera om några av de nödvändiga OS/DBMS-utgåvorna kräver att du utför SAP-utgåva, uppgradering av supportpaket och kärnuppgraderingar för att komma till en konfiguration som stöds
- Utvärdera om du behöver flytta till olika operativsystem för att distribuera på Azure.

Information om SAP-komponenter som stöds på Azure, Azure-infrastrukturenheter och relaterade operativsystemversioner och DBMS-versioner förklaras i artikeln [Vad SAP-programvara stöds för Azure-distributioner](./sap-supported-product-on-azure.md). Resultat som vunnits av utvärderingen av giltiga SAP-versioner, operativsystem och DBMS-versioner har stor inverkan på arbetet med att flytta SAP-system till Azure. Resultaten av denna utvärdering kommer att definiera om det kan finnas betydande förberedelseinsatser i de fall där SAP-utgivningsuppgraderingar eller ändringar av operativsystem behövs.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-regioner
Microsofts Azure-tjänster samlas in i Azure-regioner. En Azure-region är en eller en samling av datacenter som innehåller den maskinvara och infrastruktur som körs och är värd för de olika Azure-tjänsterna. Den här infrastrukturen innehåller ett stort antal noder som fungerar som beräkningsnoder eller lagringsnoder eller kör nätverksfunktioner. 

En lista över de olika Azure-regionerna finns i artikeln [Azure-geografiska områden](https://azure.microsoft.com/global-infrastructure/geographies/). Alla Azure-regioner erbjuder inte samma tjänster. Beroende på den SAP-produkt som du vill köra och operativsystemet och DBMS relaterade till den kan du hamna i en situation som en viss region inte erbjuder de VM-typer du behöver. Detta gäller särskilt för att köra SAP HANA, där du vanligtvis behöver virtuella datorer i M/Mv2 VM-serien. Dessa vm-familjer distribueras endast i en delmängd av regionerna. Du kan ta reda på exakt vilken virtuell dator, vilka typer, Azure-lagringstyper eller andra Azure-tjänster som är tillgängliga i vilka av regionerna med hjälp av webbplatsen [Produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/). När du börjar planera och har vissa regioner i åtanke som primär region och så småningom sekundär region, måste du först undersöka om de nödvändiga tjänsterna är tillgängliga i dessa regioner. 

### <a name="availability-zones"></a>Tillgänglighetszoner
Flera av Azure-regionerna implementerade ett koncept som kallas tillgänglighetszoner. Tillgänglighetszoner är fysiskt separata platser inom en Azure-region. Varje tillgänglighetszon består av ett eller flera datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. Om du till exempel distribuerar två virtuella datorer i två tillgänglighetszoner i Azure och implementerar ett ramverk med hög tillgänglighet för ditt SAP DBMS-system eller SAP Central Services får du det bästa serviceavtalet i Azure. För den här virtuella datorns serviceavtal i Azure kontrollerar du den senaste versionen av [SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Eftersom Azure-regioner utvecklats och utökats snabbt under de senaste åren kan topologin för Azure-regionerna, antalet fysiska datacenter, avståndet mellan dessa datacenter och avståndet mellan Azure-tillgänglighetszoner vara olika. Och med det nätverksfördröjningen.

Principen om tillgänglighetszoner gäller inte för HANA-specifika tjänst [för STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Servicenivåavtal för stora HANA-instanser finns i artikeln [SLA för SAP HANA på Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Feldomäner
Feldomäner representerar en fysisk felenhet, nära relaterad till den fysiska infrastrukturen i datacenter, och medan ett fysiskt blad eller rack kan betraktas som en feldomän, finns det ingen direkt en-till-en-mappning mellan de två.

När du distribuerar flera virtuella datorer som en del av ett SAP-system i Microsoft Azure Virtual Machine Services kan du påverka Azure Fabric Controller för att distribuera ditt program till olika feldomäner och därmed uppfylla högre krav på tillgänglighet SLA. Distribution av feldomäner över en Azure Scale Unit (samling av hundratals beräkningsnoder eller lagringsnoder och nätverk) eller tilldelning av virtuella datorer till en viss feldomän är dock något som du inte har direkt kontroll över. För att styra Azure-infrastrukturstyrenheten att distribuera en uppsättning virtuella datorer över olika feldomäner måste du tilldela en Azure-tillgänglighet inställd på de virtuella datorerna vid distributionen. Mer information om Azure-tillgänglighetsuppsättningar finns i kapitel [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3] i det här dokumentet.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uppgradera domäner
Uppgraderingsdomäner representerar en logisk enhet som hjälper till att avgöra hur en virtuell dator i ett SAP-system, som består av SAP-instanser som körs i flera virtuella datorer, uppdateras. När en uppgradering sker går Microsoft Azure igenom processen med att uppdatera dessa uppgraderingsdomäner en efter en. Genom att sprida virtuella datorer vid distributionstid över olika uppgraderingsdomäner kan du skydda ditt SAP-system delvis från potentiella driftstopp. För att tvinga Azure att distribuera virtuella datorer i ett SAP-system som är spridda över olika uppgraderingsdomäner måste du ange ett specifikt attribut vid distributionen av varje virtuell dator. I likhet med feldomäner är en Azure Scale Unit uppdelad i flera uppgraderingsdomäner. För att styra Azure-infrastrukturkontrollanten att distribuera en uppsättning virtuella datorer över olika uppgraderingsdomäner måste du tilldela en Azure-tillgänglighetsuppsättning till de virtuella datorerna vid distributionen. Mer information om Azure-tillgänglighetsuppsättningar finns i kapitel [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3] nedan.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure-tillgänglighetsuppsättningar
Virtuella Azure-datorer inom en Azure-tillgänglighetsuppsättning distribueras av Azure Fabric Controller över olika fel- och uppgraderingsdomäner. Syftet med distributionen över olika fel- och uppgraderingsdomäner är att förhindra att alla virtuella datorer i ett SAP-system stängs av vid underhåll av infrastruktur eller ett fel inom en feldomän. Som standard ingår inte virtuella datorer i en tillgänglighetsuppsättning. Deltagandet av en virtuell dator i en tillgänglighetsuppsättning definieras vid distributionen eller senare av en omkonfigurering och omdistribution av en virtuell dator.

Om du vill förstå begreppet Azure-tillgänglighetsuppsättningar och hur tillgänglighetsuppsättningar relaterar till fel- och uppgraderingsdomäner läser du den [här artikeln][virtual-machines-manage-availability]. 

När du definierar tillgänglighetsuppsättningar och försöker blanda olika virtuella datorer i olika virtuella virtuella grupper inom en tillgänglighetsuppsättning kan du stöta på problem som hindrar dig från att inkludera en viss vm-typ i en sådan tillgänglighetsuppsättning. Anledningen är att tillgänglighetsuppsättningen är bunden till skalningsenhet som innehåller en viss typ av beräkningsvärdar. Och en viss typ av beräkningsvärd kan bara köra vissa typer av vm-familjer. Om du till exempel skapar en tillgänglighetsuppsättning och distribuerar den första virtuella datorn till tillgänglighetsuppsättningen och du väljer en VM-typ av Esv3-familjen och sedan försöker distribuera en virtuell dator en virtuell dator i M-familjen som andra vm, avvisas du i den andra allokeringen. Anledningen är att Esv3-familjens virtuella datorer inte körs på samma värdmaskinvara som de virtuella datorerna i M-familjen gör. Samma problem kan uppstå när du försöker ändra storlek på virtuella datorer och försöka flytta en virtuell dator från Esv3-familjen till en VM-typ av M-familjen. När det gäller storleksändring till en virtuell datorfamilj som inte kan vara värd på samma värdmaskin måste du stänga av alla virtuella datorer i tillgänglighetsuppsättningen och ändra storlek på dem för att kunna köras på den andra värddatorns typ. För SLA:er för virtuella datorer som distribueras inom tillgänglighetsuppsättningen kontrollerar du artikeln [för SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Principen om tillgänglighetsuppsättning och relaterad uppdaterings- och feldomän gäller inte för HANA-specifika tjänsten [för STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Servicenivåavtal för stora HANA-instanser finns i artikeln [SLA för SAP HANA på Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/). 

> [!IMPORTANT]
> Begreppen Azure-tillgänglighetszoner och Azure-tillgänglighetsuppsättningar utesluter varandra. Det innebär att du antingen kan distribuera ett par eller flera virtuella datorer till en viss tillgänglighetszon eller en Azure-tillgänglighetsuppsättning. Men inte båda.


## <a name="azure-virtual-machine-services"></a>Azure-tjänster för virtuella datorer
Azure erbjuder ett stort antal virtuella datorer som du kan välja att distribuera. Det finns inget behov av up-front teknik och infrastruktur inköp. Azure VM-tjänsten erbjuder förenklar underhåll och drift av program genom att tillhandahålla beräkning och lagring på begäran för att vara värd, skala och hantera webbprogram och anslutna program. Infrastrukturhantering automatiseras med en plattform som är utformad för hög tillgänglighet och dynamisk skalning för att matcha användningsbehov med alternativet flera olika prismodeller.

![Placering av Microsoft Azure Virtual Machine Services][planning-guide-figure-400]

Med virtuella Azure-datorer kan du distribuera anpassade serveravbildningar till Azure som IaaS-instanser. Eller så kan du välja mellan ett rikt urval av förbrukningsbara operativsystemavbildningar från Azure-avbildningsgalleriet.

Ur ett operativt perspektiv erbjuder Azure Virtual Machine Service liknande upplevelser som virtuella datorer som distribueras lokalt. Du är ansvarig för administration, åtgärder och även korrigering av det aktuella operativsystemet, som körs i en Azure VM och dess program i den virtuella datorn. Microsoft tillhandahåller inte fler tjänster utöver att vara värd för den virtuella datorn på sin Azure-infrastruktur (Infrastructure as a Service - IaaS). För SAP-arbetsbelastning som du som kund distribuerar har Microsoft inga erbjudanden utöver IaaS-erbjudandena. 

Microsoft Azure-plattformen är en plattform för flera innehavare. Som ett resultat av lagring, nätverk och beräkningsresurser som är värdar för virtuella Azure-datorer delas, med några få undantag, mellan klienter. Intelligent begränsning och kvotlogik används för att förhindra att en klient påverkar prestanda för en annan klient (bullrig granne) på ett drastiskt sätt. Speciellt för att certifiera Azure-plattformen för SAP HANA måste Microsoft bevisa resursisolering för fall där flera virtuella datorer kan köras regelbundet på samma värd till SAP. Även om logiken i Azure försöker hålla avvikelser i bandbredd upplevt små, mycket delade plattformar tenderar att införa större avvikelser i resurs / bandbredd tillgänglighet än kunderna kan uppleva i sina lokala distributioner. Sannolikheten för att ett SAP-system på Azure kan uppleva större avvikelser än i ett lokalt system måste beaktas.

### <a name="azure-virtual-machines-for-sap-workload"></a>Virtuella Azure-datorer för SAP-arbetsbelastning

För SAP-arbetsbelastning har vi minskat urvalet till olika VM-familjer som är lämpliga för SAP-arbetsbelastning och SAP HANA-arbetsbelastning mer specifikt. Hur du hittar rätt vm-typ och dess förmåga att arbeta via SAP-arbetsbelastning beskrivs i dokumentet [Vad SAP-programvara stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure). 

> [!NOTE]
> Den virtuella datorn typer som är certifierade för SAP-arbetsbelastning, det finns ingen över-etablering av CPU och minnesresurser.

Utöver valet av renodlade virtuella datorer måste du också kontrollera om de är tillgängliga i en viss region baserat på webbplatsen [Produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/). Men ännu viktigare, du måste utvärdera om:

- CPU- och minnesresurser av olika vm-typer 
- IOPS-bandbredd av olika vm-typer
- Nätverksfunktioner av olika vm-typer
- Antal diskar som kan anslutas
- Möjlighet att utnyttja vissa Azure-lagringstyper

passar dina behov. De flesta av dessa data finns [här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows] för en viss VM-typ.

Som prismodell har du flera olika prisalternativ som listar som:

- Användningsbaserad betalning
- Ett år reserverat
- Tre år reserverade
- Spotpriser

Prissättningen av var och en av de olika erbjudanden med olika tjänster erbjuder runt operativsystem och olika regioner finns på webbplatsen [Linux Virtual Machines Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och Windows Virtual Machines [Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om och flexibilitet på ett år och tre års reserverade instanser finns i följande artiklar:

- [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [Flexibel storlek för virtuella datorer med reserverade VM-instanser](https://docs.microsoft.com/azure/virtual-machines/windows/reserved-vm-instance-size-flexibility)
- [Så tillämpas rabatten för Azure-reservation på virtuella datorer](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-vm-reservation-charges) 

Mer information om spotpriser finns i artikeln [Azure Spot Virtual Machines](https://azure.microsoft.com/pricing/spot/). Prissättning av samma VM-typ kan också skilja sig mellan olika Azure-regioner. För vissa kunder var det värt att distribuera till en billigare Azure-region.

Dessutom erbjuder Azure begreppen för en dedikerad värd. Det dedikerade värdkonceptet ger dig mer kontroll över korrigeringscykler som görs av Azure. Du kan tid lappa enligt dina egna scheman. Det här erbjudandet riktar sig specifikt till kunder med arbetsbelastning som kanske inte följer den normala arbetsbelastningscykeln. Om du vill läsa upp begreppen för Azure-dedikerade värderbjudanden läser du artikeln [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts). Använda det här erbjudandet stöds för SAP-arbetsbelastning och används av flera SAP-kunder som vill ha mer kontroll över korrigering av infrastruktur och eventuella underhållsplaner för Microsoft. Mer information om hur Microsoft underhåller och korrigerar Azure-infrastrukturen som är värd för virtuella datorer finns i artikeln [Underhåll för virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/maintenance-and-updates).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Virtuella datorer med generation 1 och generation 2
Microsofts hypervisor kan hantera två olika generationer av virtuella datorer. Dessa format kallas **Generation 1** och **Generation 2**. **Generation 2** introducerades år 2012 med Windows Server 2012 hypervisor. Azure började med virtuella generation 1-datorer. När du distribuerar virtuella Azure-datorer är standard fortfarande att använda formatet Generation 1. Under tiden kan du distribuera Generation 2 VM-format också. I artikeln [Support för virtuella generation 2-datorer på Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) visas de Azure VM-familjer som kan distribueras som generation 2-virtuella datorer. Den här artikeln innehåller också en lista över de mycket viktiga funktionella skillnaderna i virtuella generation 2-datorer eftersom de kan köras på Hyper-V-privat moln och Azure. Ännu viktigare den här artikeln listar också funktionella skillnader mellan generation 1 virtuella datorer och Generation 2 virtuella datorer, eftersom de som körs i Azure. 

> [!NOTE]
> Det finns funktionella skillnader mellan virtuella generation 1- och generation 2-datorer som körs i Azure. Läs artikeln [Support för generation 2 virtuella datorer på Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) för att se en lista över dessa skillnader.  
 
Det går inte att flytta en befintlig virtuell dator från en generation till den andra generationen. Om du vill ändra genereringen av virtuella datorer måste du distribuera en ny virtuell dator för den generation du önskar och installera om programvaran som du kör i den virtuella datorn för genereringen. Detta påverkar bara den virtuella datorns grundläggande VHD-avbildning och påverkar inte datadiskarna eller anslutna NFS- eller SMB-resurser. Datadiskar, NFS- eller SMB-resurser som ursprungligen tilldelades till till exempel på en virtuell generation 1-dator

För närvarande kommer du att stöta på det här problemet, särskilt mellan virtuella datorer i Azure M-serien och virtuella datorer i Mv2-serien. På grund av begränsningar i formatet Generation 1 VM kunde de stora virtuella datorerna i Mv2-familjen inte erbjudas i Generation 1-format, men måste erbjudas i generation 2 uteslutande. Å andra sidan är M-serien VM-familjen ännu inte aktiverad för att distribueras i generation 2. Därför kräver storleksändring mellan virtuella datorer i M-serien och Mv2-serien en ominstallation av programvaran i en virtuell dator som du riktar in dig på för den andra virtuella datorn. Microsoft arbetar för att du ska kunna distribuera virtuella datorer i M-serien för generation 2-distributioner. Distribuera M-serien virtuella datorer som Generation 2 virtuella datorer i framtiden, kommer att möjliggöra en till synes mindre storlek mellan M-serien och Mv2-serien virtuella datorer. I båda riktningarna, antingen upp-dimensionering från M-serien till större Mv2-serien virtuella datorer eller ned-dimensionering från större Mv2-serien virtuella datorer till mindre M-serien virtuella datorer. Dokumentationen kommer att uppdateras så snart virtuella datorer i M-serien kan distribueras som virtuella generation 2-datorer.    

 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Lagring: Microsoft Azure-lagring och datadiskar
Microsoft Azure Virtual Machines använder olika lagringstyper. När du implementerar SAP på Azure Virtual Machine Services är det viktigt att förstå skillnaderna mellan dessa två huvudtyper av lagring:

* Icke-beständig, flyktig lagring.
* Beständig lagring.

Virtuella Azure-datorer erbjuder diskar som inte är beständiga när en virtuell dator har distribuerats. Vid omstart av den virtuella datorn kommer allt innehåll på dessa enheter att raderas. Därför är det en självklarhet att datafiler och logg/gör om-filer i databaser under inga omständigheter bör finnas på dessa enheter som inte behålls. Det kan finnas undantag för vissa databaser, där dessa icke-beständiga enheter kan vara lämpliga för tempdb- och temp-tablespaces. Undvik dock att använda dessa enheter för virtuella datorer i A-serien eftersom de som inte är beständiga enheter är begränsade i dataflödet med den virtuella datorn. Mer information finns i artikeln [Förstå den tillfälliga enheten på virtuella Windows-datorer i Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Enhet D:\ i en Azure VM är en enhet som inte är beständig, som backas upp av vissa lokala diskar på Azure-beräkningsnoden. Eftersom den inte är beständig innebär det att alla ändringar som görs i innehållet på D:\ enheten går förlorad när den virtuella datorn startas om. Genom "alla ändringar", som filer lagras, kataloger som skapats, installerade program, etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuella linux Azure-datorer monterar automatiskt en enhet på /mnt/resource som är en enhet som inte sparas upp av lokala diskar på Azure-beräkningsnoden. Eftersom den inte är beständig innebär det att alla ändringar som görs i innehållet i /mnt/resource går förlorade när den virtuella datorn startas om. Genom alla ändringar, som filer som lagras, kataloger som skapats, installerade program, etc.
> 
> 

---

Microsoft Azure Storage tillhandahåller lagring som sparats och de typiska skydds- och redundansnivåerna som visas på SAN-lagring. Diskar som baseras på Azure Storage är virtuella hårddiskar (VHDs) som finns i Azure Storage Services. Den lokala OS-Disk (Windows C:\, Linux /dev/sda1) lagras på Azure Storage och ytterligare volymer/diskar som är monterade på den virtuella datorn lagras där också.

Det är möjligt att ladda upp en befintlig virtuell hårddisk från lokala eller skapa tomma från Azure och bifoga dessa virtuella hårddiskar till distribuerade virtuella datorer.

När du har skapat eller överfört en virtuell hårddisk till Azure Storage är det möjligt att montera och koppla dem till en befintlig virtuell dator och kopiera befintlig (avmonterad) virtuell hårddisk.

När dessa virtuella hårddiskar sparas är data och ändringar inom dessa virtuella hårddiskar säkra när de startas om och återskapar en virtual machine-instans. Även om en instans tas bort, dessa virtuella hårddiskar vara säker och kan distribueras om eller i händelse av icke-OS-diskar kan monteras på andra virtuella datorer.

Mer information om Azure Storage finns här:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Azure Standard-lagring var den typ av lagring som var tillgänglig när Azure IaaS släpptes. IOPS-kvoter tillämpades per enskild disk. Svarstiden som upplevdes var inte i samma klass som SAN/NAS-enheter som vanligtvis distribueras för avancerade SAP-system som finns lokalt. Azure Standard Storage visade sig dock vara tillräckligt för många hundratals SAP-system som distribueras i Azure.

Diskar som lagras på Azure Standard Storage-konton debiteras baserat på de faktiska data som lagras, volymen av lagringstransaktioner, utgående dataöverföringar och redundansalternativet som valts. Många diskar kan skapas på maximalt 1 TB i storlek, men så länge de förblir tomma finns det ingen avgift. Om du sedan fyller en virtuell hårddisk med 100GB vardera, debiteras du för att lagra 100GB och inte för den nominella storleken som DEN virtuella hårddisken skapades med.

#### <a name="azure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium-lagring
Azure Premium Storage har introducerats med målet att tillhandahålla:

* Bättre I/O-latens.
* Bättre genomströmning.
* Mindre variation i I/O-latens.

För detta ändamål infördes många förändringar, varav de två viktigaste är:

* Användning av SSD-diskar i Azure Storage-noderna
* En ny läscache som backas upp av den lokala SSD-filen för en Azure-beräkningsnod

I motsats till Standard lagring där funktionerna inte ändras beroende på storleken på disken (eller VHD), Premium Storage har för närvarande tre olika disk kategorier, som visas i den här artikeln:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Du ser att IOPS/disk- och diskdataflöde/disk är beroende av diskarnas storlekskategori

Kostnadsbas i fråga om Premium Storage är inte den faktiska datavolymen som lagras i sådana diskar, utan storlekskategorin för en sådan disk, oberoende av mängden data som lagras på disken.

Du kan också skapa diskar på Premium Storage som inte direkt mappas till de storlekskategorier som visas. Detta kan vara fallet, särskilt när du kopierar diskar från standardlagring till Premium Storage. I sådana fall utförs en mappning till det näst största alternativet för Premium Storage-disk.

De flesta azure VM-familjer som är certifierade med SAP kan arbeta med Premium Storage och eller en blandning mellan Azure-standard och Premium Storage.

Om du checkar ut den del av virtuella datorer i DS-serien i den [här artikeln (Linux)][virtual-machines-sizes-linux] och [den här artikeln (Windows)][virtual-machines-sizes-windows]inser du att det finns begränsningar av datavolym till Premium Storage-diskar på granulariteten på vm-nivån. Olika virtuella datorer i DS-serien eller GS-serien har också olika begränsningar i antalet datadiskar som kan monteras. Dessa gränser dokumenteras i artikeln som nämns ovan också. Men i huvudsak innebär det att om du till exempel montera 32 x P30-diskar till en enda DS14 VM kan du inte få 32 x det maximala genomströmningen av en P30-disk. I stället begränsar det maximala dataflödet på VM-nivå som dokumenteras i artikeln dataflödet.

Mer information om Premium Storage hittar du här:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure Storage-konton

När du distribuerar tjänster eller virtuella datorer i Azure kan distribution av virtuella hårddiskar och VM-avbildningar ordnas i enheter som kallas Azure Storage Accounts. När du planerar en Azure-distribution måste du noga överväga begränsningarna i Azure. Å ena sidan finns det ett begränsat antal lagringskonton per Azure-prenumeration. Även om varje Azure Storage-konto kan innehålla ett stort antal VHD-filer, finns det en fast gräns för den totala IOPS per Storage-konto. När du distribuerar hundratals virtuella SAP-datorer med DBMS-system som skapar betydande IO-anrop, rekommenderas att distribuera höga IOPS DBMS-virtuella datorer mellan flera Azure Storage-konton. Försiktighet måste iakttas för att inte överskrida den aktuella gränsen för Azure Storage-konton per prenumeration. Eftersom lagring är en viktig del av databasdistributionen för ett SAP-system, diskuteras det här konceptet mer i detalj i den redan refererade [DBMS Deployment Guide][dbms-guide].

Mer information om Azure Storage-konton finns i [skalbarhetsmålen för standardlagringskonton](../../../storage/common/scalability-targets-standard-account.md) och [skalbarhetsmål för premium-sidblobblagringskonton](../../../storage/blobs/scalability-targets-premium-page-blobs.md). När du läser dessa artiklar inser du att det finns skillnader i begränsningarna mellan Azure Standard Storage-konton och Premium Storage-konton. Stora skillnader är mängden data som kan lagras i ett sådant lagringskonto. I Standard storage är volymen en magnitud som är större än med Premium Storage. Å andra sidan är standardlagringskontot kraftigt begränsat i IOPS (se kolumnen **Total begärandehastighet),** medan Azure Premium Storage-kontot inte har någon sådan begränsning. Vi kommer att diskutera detaljer och resultat av dessa skillnader när vi diskuterar distributioner av SAP-system, särskilt DBMS-servrarna.

Inom ett lagringskonto har du möjlighet att skapa olika behållare i syfte att organisera och kategorisera olika virtuella hårddiskar. Dessa behållare används till exempel för separata virtuella hårddiskar för olika virtuella datorer. Det finns inga prestandakonsekvenser i att bara använda en behållare eller flera behållare under ett enda Azure Storage-konto.

Inom Azure följer ett VHD-namn följande namngivningsanslutning som måste ge ett unikt namn för den virtuella hårddisken i Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Strängen ovan måste unikt identifiera den virtuella hårddisken som lagras på Azure Storage.

#### <a name="managed-disks"></a><a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Hanterade diskar

Hanterade diskar är en ny resurstyp i Azure Resource Manager som kan användas i stället för virtuella hårddiskar som lagras i Azure Storage Accounts. Hanterade diskar justeras automatiskt med tillgänglighetsuppsättningen för den virtuella datorn som de är kopplade till och ökar därför tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översiktsartikeln](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Vi rekommenderar att du använder Hanterad disk, eftersom de förenklar distributionen och hanteringen av dina virtuella datorer.
SAP stöder för närvarande endast Premium-hanterade diskar. Mer information finns i SAP Note [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage återhämtning

Microsoft Azure Storage lagrar bas-VHD (med OS) och anslutna diskar eller BLOBs på minst tre separata lagringsnoder. Detta faktum kallas Lokal redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure. 

Det finns flera fler redundansmetoder, som alla beskrivs i artikeln [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>När det gäller Azure Premium Storage, som är den rekommenderade typen av lagring för DBMS virtuella datorer och diskar som lagrar databas- och logg-/gör om-filer, är den enda tillgängliga metoden LRS. Därför måste du konfigurera databasmetoder, till exempel SQL Server Always On, Oracle Data Guard eller HANA System Replication för att aktivera databasdatareplikering till en annan Azure-region eller en annan Azure-tillgänglighetszon.


> [!NOTE]
> För DBMS-distributioner rekommenderas inte användning av geo redundant lagring som tillgänglig med Azure Standard Storage eftersom den har allvarlig prestandapåverkan och inte respekterar skrivordningen för olika virtuella hårddiskar som är kopplade till en virtuell dator. Det faktum att inte hedra skrivordningen över olika virtuella hårddiskar har en stor potential att hamna i inkonsekventa databaser på replikeringsmålsidan om databas- och logg-/gör om-filer är spridda över flera virtuella hårddiskar (som oftast fallet) på käll-VM-sidan.


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure-nätverk

Microsoft Azure tillhandahåller en nätverksinfrastruktur som gör det möjligt att mappa alla scenarier, som vi vill förverkliga med SAP-programvara. Funktionerna är:

* Tillgång från utsidan, direkt till de virtuella datorerna via Windows Terminal Services eller ssh/VNC
* Tillgång till tjänster och specifika portar som används av program inom de virtuella datorerna
* Intern kommunikation och namnmatchning mellan en grupp virtuella datorer som distribueras som virtuella Azure-datorer
* Anslutning mellan en kunds lokala nätverk och Azure-nätverket
* Anslutning mellan Azure Region eller datacenter mellan Azure-platser

Mer information finns här: <https://azure.microsoft.com/documentation/services/virtual-network/>

Det finns många olika möjligheter att konfigurera namn- och IP-lösning i Azure. Det finns också en Azure DNS-tjänst som kan användas i stället för att konfigurera din egen DNS-server. Mer information finns i [den här artikeln][virtual-networks-manage-dns-in-vnet] och på denna [sida](https://azure.microsoft.com/services/dns/).

För scenarier mellan lokaler eller hybrider förlitar vi oss på det faktum att den lokala AD/OpenLDAP/DNS har utökats via VPN eller privat anslutning till Azure. För vissa scenarier som dokumenteras här kan det vara nödvändigt att ha en AD/OpenLDAP-replik installerad i Azure.

Eftersom nätverks- och namnmatchning är en viktig del av databasdistributionen för ett SAP-system, diskuteras det här konceptet mer i detalj i [DBMS Deployment Guide][dbms-guide].

##### <a name="azure-virtual-networks"></a> Azure Virtual Networks

Genom att skapa ett virtuellt Azure-nätverk kan du definiera adressintervallet för de privata IP-adresser som allokerats av Azure DHCP-funktioner. I scenarier över flera lokaler allokeras ip-adressintervallet som definierats fortfarande med DHCP av Azure. Domännamnsmatchning görs dock lokalt (förutsatt att de virtuella datorerna är en del av en lokal domän) och därmed kan matcha adresser utöver olika Azure Cloud Services.

Varje virtuell dator i Azure måste vara ansluten till ett virtuellt nätverk.

Mer information finns i [den här artikeln][resource-groups-networking] och på denna [sida](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> När en virtuell dator har distribuerats kan du som standard inte ändra konfigurationen för det virtuella nätverket. TCP/IP-inställningarna måste lämnas till Azure DHCP-servern. Standardbeteende är Dynamisk IP-tilldelning.
>
>

MAC-adressen för det virtuella nätverkskortet kan ändras, till exempel efter storleksändring och Windows eller Linux gäst-OS plockar upp det nya nätverkskortet och använder automatiskt DHCP för att tilldela IP- och DNS-adresserna i det här fallet.

##### <a name="static-ip-assignment"></a>Statisk IP-tilldelning
Det är möjligt att tilldela fasta eller reserverade IP-adresser till virtuella datorer i ett Virtuellt Azure-nätverk. Om du kör de virtuella datorerna i ett virtuellt Azure-nätverk öppnas en stor möjlighet att utnyttja den här funktionen om det behövs eller krävs för vissa scenarier. IP-tilldelningen förblir giltig under hela förekomsten av den virtuella datorn, oberoende av om den virtuella datorn körs eller stängs av. Därför måste du ta hänsyn till det totala antalet virtuella datorer (som körs och stoppas virtuella datorer) när du definierar intervallet för IP-adresser för det virtuella nätverket. IP-adressen förblir tilldelad antingen tills den virtuella datorn och dess nätverksgränssnitt tas bort eller tills IP-adressen avdevisas igen. Mer information finns i [den här artikeln][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure-medel till enskilda virtuella NCC:er. Du bör inte tilldela statiska IP-adresser inom gästoperativsystemet till ett vNIC-nätverk. Vissa Azure-tjänster som Azure Backup Service förlitar sig på det faktum att åtminstone det primära virtuella nätverkskortet är inställt på DHCP och inte statiska IP-adresser. Se även dokumentet [Felsöka Azure virtuell dator backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Flera nätverkskort per virtuell dator

Du kan definiera flera virtuella nätverkskort (vNIC) för en virtuell Azure-dator. Med möjligheten att ha flera virtuella NÄTVERKSKORT kan du börja ställa in nätverkstrafikseparation där till exempel klienttrafik dirigeras via ett vNIC- och backend-trafik dirigeras via ett andra vNIC-nätverk. Beroende på vilken typ av virtuell dator det finns olika begränsningar för antalet virtuella NCC som en virtuell dator kan ha tilldelat. Exakta detaljer, funktioner och begränsningar finns i dessa artiklar:

* [Skapa en Virtuell Windows-dator med flera nätverkskort][virtual-networks-multiple-nics-windows]
* [Skapa en virtuell Linux-dator med flera nätverkskort][virtual-networks-multiple-nics-linux]
* [Distribuera virtuella nätverkskort med hjälp av en mall][virtual-network-deploy-multinic-arm-template]
* [Distribuera virtuella nätverkskort med flera nätverkskort med PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Distribuera virtuella nätverkskort med flera nätverkskort med Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Anslutning mellan plats och plats

Korslokaler är virtuella Azure-datorer och lokala virtuella datorer som är kopplade till en transparent och permanent VPN-anslutning. Det förväntas bli det vanligaste SAP-distributionsmönstret i Azure. Antagandet är att operativa procedurer och processer med SAP-instanser i Azure ska fungera transparent. Det innebär att du bör kunna skriva ut dessa system samt använda SAP Transport Management System (TMS) för att transportera ändringar från ett utvecklingssystem i Azure till ett testsystem som distribueras lokalt. Mer dokumentation kring plats-till-plats finns i [den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-tunnelenhet

För att skapa en plats-till-plats-anslutning (lokalt datacenter till Azure-datacenter) måste du antingen hämta och konfigurera en VPN-enhet eller använda RRAS (Routing and Remote Access Service) som introducerades som en programvarukomponent med Windows Server 2012.

* [Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Om VPN-enheter för VPN Gateway-anslutningar från plats till plats][vpn-gateway-about-vpn-devices]
* [Vanliga frågor och svar om VPN Gateway][vpn-gateway-vpn-faq]

![Anslutning mellan plats och plats mellan lokala och Azure][planning-guide-figure-600]

Figuren ovan visar att två Azure-prenumerationer har IP-adressunderregister reserverade för användning i virtuella nätverk i Azure. Anslutningen från det lokala nätverket till Azure upprättas via VPN.

#### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Point-to-site VPN kräver att varje klientdator ansluter med sin egen VPN till Azure. För SAP-scenarier, vi tittar på, punkt-till-plats-anslutning är inte praktiskt. Därför ges inga ytterligare referenser till punkt-till-plats VPN-anslutning.

Mer information finns här
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN på flera webbplatser

Azure erbjuder också idag möjligheten att skapa VPN-anslutning på flera webbplatser för en Azure-prenumeration. Tidigare begränsades en enda prenumeration till en VPN-anslutning från plats till plats. Den här begränsningen gick bort med VPN-anslutningar på flera webbplatser för en enda prenumeration. Detta gör det möjligt att utnyttja mer än en Azure-region för en viss prenumeration via korslokala konfigurationer.

Mer information finns i [den här artikeln][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>VNet till VNet-anslutning

Med vpn med flera platser måste du konfigurera ett separat Virtuellt Azure-nätverk i var och en av regionerna. Men ofta har du kravet att programvarukomponenterna i de olika regionerna ska kommunicera med varandra. Helst bör den här kommunikationen inte dirigeras från en Azure-region till lokal och därifrån till den andra Azure-regionen. Till genvägen erbjuder Azure möjligheten att konfigurera en anslutning från ett Virtuellt Azure-nätverk i en region till ett annat Virtuellt Azure-nätverk som finns i en annan region. Den här funktionen kallas VNet-till-VNet-anslutning. Mer information om denna funktion finns <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>här: .

#### <a name="private-connection-to-azure-expressroute"></a>Privat anslutning till Azure ExpressRoute

Microsoft Azure ExpressRoute gör det möjligt att skapa privata anslutningar mellan Azure-datacenter och antingen kundens lokala infrastruktur eller i en samlokaliseringsmiljö. ExpressRoute erbjuds av olika MPLS (paketkopplade) VPN-leverantörer eller andra network service-leverantörer. ExpressRoute-anslutningar går inte via offentligt Internet. ExpressRoute-anslutningar ger högre säkerhet, mer tillförlitlighet genom flera parallella kretsar, snabbare hastigheter och lägre svarstider än vanliga anslutningar via Internet.

Här hittar du mer information om Azure ExpressRoute och erbjudanden:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route möjliggör flera Azure-prenumerationer via en ExpressRoute-krets som dokumenterats här

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Påtvingad tunnel i händelse av korslokaler
För virtuella datorer som ansluter lokala domäner via plats-till-plats, punkt-till-plats eller ExpressRoute måste du se till att proxyinställningarna för Internet distribueras även för alla användare i dessa virtuella datorer. Som standard skulle programvara som körs i dessa virtuella datorer eller användare som använder en webbläsare för att komma åt internet inte gå via företagets proxy, men skulle ansluta direkt via Azure till Internet. Men även proxy inställningen är inte en 100% lösning för att styra trafiken genom företagets proxy eftersom det är ansvar för programvara och tjänster för att kontrollera om proxy. Om programvara som körs i den virtuella datorn inte gör det eller en administratör manipulerar inställningarna, kan trafiken till Internet omvägas igen direkt via Azure till Internet.

För att undvika en sådan direkt internetanslutning kan du konfigurera Tvingad tunnel med anslutning mellan lokala och Azure. Den detaljerade beskrivningen av funktionen Tvångstunnelning publiceras här<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Forced Tunneling med ExpressRoute aktiveras av kunder som annonserar en standardväg via ExpressRoute BGP-peering-sessionerna.

#### <a name="summary-of-azure-networking"></a>Sammanfattning av Azure-nätverk

Det här kapitlet innehöll många viktiga punkter om Azure Networking. Här är en sammanfattning av de viktigaste punkterna:

* Med Azure Virtual Networks kan du placera en nätverksstruktur i din Azure-distribution. Virtuella nätverk kan isoleras mot varandra eller med hjälp av nätverkssäkerhetsgrupper kan trafik mellan virtuella nätverk styras.
* Virtuella Azure-nätverk kan utnyttjas för att tilldela IP-adressintervall till virtuella datorer eller tilldela fasta IP-adresser till virtuella datorer
* Om du vill konfigurera en plats-till-plats- eller punkt-till-plats-anslutning måste du först skapa ett virtuellt Azure-nätverk
* När en virtuell dator har distribuerats är det inte längre möjligt att ändra det virtuella nätverket som tilldelats den virtuella datorn

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvoter i Azure Virtual Machine Services
Vi måste vara tydliga med att lagrings- och nätverksinfrastrukturen delas mellan virtuella datorer som kör en mängd olika tjänster i Azure-infrastrukturen. Och precis som i kundens egna datacenter sker överutbud av en del av infrastrukturresurserna i viss utsträckning. Microsoft Azure Platform använder disk-, CPU-, nätverks- och andra kvoter för att begränsa resursförbrukningen och för att bevara konsekventa och deterministiska prestanda.  De olika vm-typerna (A5, A6 osv.) har olika kvoter för antalet diskar, CPU, RAM och Nätverk.

> [!NOTE]
> CPU- och minnesresurser för de VM-typer som stöds av SAP förallokeras på värdnoderna. Det innebär att när den virtuella datorn har distribuerats är resurserna på värden tillgängliga enligt definitionen av vm-typen.
>
>

När du planerar och dimensionerar SAP på Azure-lösningar måste kvoterna för varje virtuell datorstorlek beaktas. VM-kvoterna beskrivs [här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

De beskrivna kvoterna representerar de teoretiska maximivärdena.  Gränsen för IOPS per disk kan uppnås med små IOs (8kb) men eventuellt inte kan uppnås med stora IOs (1Mb).  IOPS-gränsen tillämpas på granulariteten för en disk.

Som ett grovt beslutsträd för att avgöra om ett SAP-system passar in i Azure Virtual Machine Services och dess funktioner eller om ett befintligt system behöver konfigureras på olika sätt för att distribuera systemet på Azure, kan beslutsträdet nedan användas:

![Beslutsträd för att bestämma möjligheten att distribuera SAP på Azure][planning-guide-figure-700]

**Steg 1**: Den viktigaste informationen som ska börja med är SAPS-kravet för ett visst SAP-system. SAPS-kraven måste delas upp i DBMS-delen och SAP-programdelen, även om SAP-systemet redan distribueras lokalt i en konfiguration på två nivåer. För befintliga system kan SAPS-relaterade till den maskinvara som används ofta bestämmas eller uppskattas baserat på befintliga SAP-riktmärken. Resultaten hittar du här: <https://sap.com/about/benchmark.html>.
För nyligen distribuerade SAP-system bör du ha gått igenom en storleksövning, som bör bestämma SAPS-kraven i systemet.
Se även den här bloggen och bifogat dokument för SAP-storlek på Azure:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Steg 2**: För befintliga system bör I/O-volymen och I/O-åtgärderna per sekund på DBMS-servern mätas. För nyligen planerade system bör storleksövningen för det nya systemet också ge grova idéer om I/O-kraven på DBMS-sidan. Om du är osäker måste du så småningom genomföra ett proof of concept.

**Steg 3:** Jämför SAPS-kravet för DBMS-servern med SAPS de olika vm-typerna av Azure kan tillhandahålla. Informationen om SAPS för de olika Azure VM-typerna dokumenteras i SAP Note [1928533]. Fokus bör ligga på DBMS VM först eftersom databaslagret är lagret i ett SAP NetWeaver-system som inte skalas ut i de flesta distributioner. Däremot kan SAP-programlagret skalas ut. Om ingen av sap-stödda Azure VM-typer kan leverera de nödvändiga SAPS- arbetsbelastningen kan inte arbetsbelastningen för det planerade SAP-systemet köras på Azure. Du måste antingen distribuera systemet lokalt eller så måste du ändra arbetsbelastningsvolymen för systemet.

**Steg 4:** Som [dokumenterats här (Linux)][virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows]tillämpar Azure en IOPS-kvot per disk oberoende oavsett om du använder Standard Storage eller Premium Storage. Beroende på den virtuella datorns typ varierar antalet datadiskar som kan monteras. Som ett resultat kan du beräkna ett maximalt IOPS-nummer som kan uppnås med var och en av de olika vm-typerna. Beroende på databasens fillayout kan du stripe-diskar bli en volym i gästoperativsystemet. Men om den aktuella IOPS-volymen för ett distribuerat SAP-system överskrider de beräknade gränserna för den största VM-typen av Azure och om det inte finns någon chans att kompensera med mer minne, kan arbetsbelastningen för SAP-systemet påverkas allvarligt. I sådana fall kan du träffa en punkt där du inte bör distribuera systemet på Azure.

**Steg 5:** Särskilt i SAP-system, som distribueras lokalt i konfigurationer på 2 nivåer, är chansen stor att systemet kan behöva konfigureras på Azure i en konfiguration på 3 nivåer. I det här steget måste du kontrollera om det finns en komponent i SAP-programlagret, som inte kan skalas ut och som inte skulle passa in i CPU- och minnesresurser som de olika Azure VM-typerna erbjuder. Om det verkligen finns en sådan komponent kan SAP-systemet och dess arbetsbelastning inte distribueras till Azure. Men om du kan skala ut SAP-programkomponenterna till flera virtuella Azure-datorer kan systemet distribueras till Azure.

**Steg 6**: Om DBMS- och SAP-programlagerkomponenterna kan köras i virtuella Azure-datorer måste konfigurationen definieras med avseende på:

* Antal virtuella Azure-datorer
* VM-typer för de enskilda komponenterna
* Antal virtuella hårddiskar i DBMS VM för att ge tillräckligt med IOPS

## <a name="managing-azure-assets"></a>Hantera Azure-tillgångar

### <a name="azure-portal"></a>Azure Portal

Azure-portalen är ett av tre gränssnitt för att hantera Azure VM-distributioner. De grundläggande hanteringsuppgifterna, som att distribuera virtuella datorer från avbildningar, kan göras via Azure-portalen. Dessutom är skapandet av lagringskonton, virtuella nätverk och andra Azure-komponenter också uppgifter som Azure-portalen kan hantera väl. Funktioner som att ladda upp virtuella hårddiskar från lokala till Azure eller kopiera en virtuell hårddisk i Azure är dock uppgifter som kräver antingen verktyg från tredje part eller administration via PowerShell eller CLI.

![Översikt över Microsoft Azure-portal – översikt över virtuella datorer][planning-guide-figure-800]


Administrations- och konfigurationsuppgifter för instansen för virtuella datorer är möjliga inifrån Azure-portalen.

Förutom att starta om och stänga av en virtuell dator kan du också koppla från, koppla bort och skapa datadiskar för instansen virtuell dator, för att fånga instansen för avbildningsförberedelse och konfigurera storleken på instansen för virtuell dator.

Azure-portalen innehåller grundläggande funktioner för att distribuera och konfigurera virtuella datorer och många andra Azure-tjänster. Alla tillgängliga funktioner omfattas dock inte av Azure-portalen. I Azure-portalen går det inte att utföra uppgifter som:

* Ladda upp virtuella hårddiskar till Azure
* Kopiera virtuella datorer


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Hantering via Microsoft Azure PowerShell-cmdlets

Windows PowerShell är ett kraftfullt och utökningsbart ramverk som har antagits av kunder som distribuerar ett större antal system i Azure. Efter installationen av PowerShell-cmdlets på en stationär, bärbar dator eller dedikerad hanteringsstation kan PowerShell-cmdlets köras på distans.

Processen för att aktivera en lokal stationär/bärbar dator för användning av Azure PowerShell-cmdlets och hur du konfigurerar dem för användning med Azure-prenumerationerna beskrivs i den [här artikeln][powershell-install-configure].

Mer detaljerade steg om hur du installerar, uppdaterar och konfigurerar Azure PowerShell-cmdlets finns också i det här kapitlet i [distributionsguiden][deployment-guide-4.1].

Kundupplevelsen hittills har varit att PowerShell (PS) är säkert det mer kraftfulla verktyget för att distribuera virtuella datorer och skapa anpassade steg i distributionen av virtuella datorer. Alla kunder som kör SAP-instanser i Azure använder PS-cmdlets för att komplettera hanteringsuppgifter som de utför i Azure-portalen eller till och med använder PS-cmdlets uteslutande för att hantera sina distributioner i Azure. Eftersom de Azure-specifika cmdlets delar samma namngivningskonvention som de mer än 2000 Windows-relaterade cmdlets, är det en lätt uppgift för Windows-administratörer att utnyttja dessa cmdlets.

Se exempel här:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Distribution av Azure Extension för SAP (se kapitel [Azure Extension för SAP][planning-guide-9.1] i det här dokumentet) är endast möjlig via PowerShell eller CLI. Därför är det obligatoriskt att konfigurera och konfigurera PowerShell eller CLI när du distribuerar eller administrerar ett SAP NetWeaver-system i Azure.  

Eftersom Azure tillhandahåller fler funktioner kommer nya PS-cmdlets att läggas till som kräver en uppdatering av cmdlets. Därför är det vettigt att kontrollera Azure Download-webbplatsen minst en gång i månaden <https://azure.microsoft.com/downloads/> för en ny version av cmdlets. Den nya versionen installeras ovanpå den äldre versionen.

En allmän lista över Azure-relaterade PowerShell-kommandon finns här: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Hantering via Microsoft Azure CLI-kommandon

För kunder som använder Linux och vill hantera Azure-resurser kanske PowerShell inte är ett alternativ. Microsoft erbjuder Azure CLI som ett alternativ.
Azure CLI tillhandahåller en uppsättning kommandon med öppen källkod, plattformsoberoende för att arbeta med Azure-plattformen. Azure CLI innehåller mycket av samma funktioner som finns i Azure-portalen.

Information om installation, konfiguration och hur du använder CLI-kommandon för att utföra Azure-uppgifter finns i

* [Installera klassiska Azure CLI][xplat-cli]
* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Använda Azure classic CLI för Mac, Linux och Windows med Azure Resource Manager][xplat-cli-azure-resource-manager]

Läs även kapitel [Azure CLI för virtuella Linux-datorer][deployment-guide-4.5.2] i [distributionsguiden][planning-guide] om hur du använder Azure CLI för att distribuera Azure Extension för SAP.


## <a name="first-steps-planning-a-deployment"></a>De första stegen planerar en distribution
Det första steget i distributionsplaneringen är INTE att söka efter virtuella datorer som är tillgängliga för att köra SAP. Det första steget kan vara ett som är tidskrävande, men viktigast av allt, är att arbeta med efterlevnads- och säkerhetsteam i ditt företag på vilka gränsvillkoren är för att distribuera vilken typ av SAP-arbetsbelastning eller affärsprocess i offentliga moln. Om ditt företag har distribuerat annan programvara tidigare i Azure kan processen vara enkel. Om ditt företag är mer i början av resan kan det finnas större diskussioner som krävs för att ta reda på gränsvillkor, säkerhetsvillkor, som gör att vissa SAP-data och SAP-affärsprocesser kan vara värd i det offentliga molnet.

Som användbar hjälp kan du peka på [Microsofts efterlevnadserbjudanden](https://docs.microsoft.com/microsoft-365/compliance/offering-home) för en lista över efterlevnadserbjudanden som Microsoft kan tillhandahålla. 

Andra problemområden som datakryptering för data i vila eller annan kryptering i Azure-tjänsten dokumenteras i [Azure-krypteringsöversikt](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview).

Underskatta inte denna fas av projektet i din planering. Endast när du har avtal och regler kring det här avsnittet måste du gå till nästa steg, vilket är planeringen av nätverksarkitekturen som du distribuerar i Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Olika sätt att distribuera virtuella datorer för SAP i Azure

I det här kapitlet lär du dig olika sätt att distribuera en virtuell dator i Azure. Ytterligare förberedelseprocedurer samt hantering av virtuella hårddiskar och virtuella datorer i Azure beskrivs i det här kapitlet.

### <a name="deployment-of-vms-for-sap"></a>Distribution av virtuella datorer för SAP

Microsoft Azure erbjuder flera sätt att distribuera virtuella datorer och associerade diskar. Det är därför viktigt att förstå skillnaderna eftersom förberedelserna för de virtuella datorerna kan variera beroende på distributionsmetoden. I allmänhet tar vi en titt på följande scenarier:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Flytta en virtuell dator från lokalt till Azure med en icke-generaliserad disk

Du planerar att flytta ett specifikt SAP-system från lokalt till Azure. Detta kan göras genom att ladda upp den virtuella hårddisken, som innehåller OS-, SAP-binärfilerna och DBMS-binärfilerna plus virtuella hårddiskar med data- och loggfilerna för DBMS till Azure. I motsats till [scenariot #2 nedan][planning-guide-5.1.2]behåller du användarkontona för värdnamn, SAP SID och SAP i Azure VM när de konfigurerades i den lokala miljön. Därför är det inte nödvändigt att generalisera bilden. Se kapitel [Förberedelse för att flytta en virtuell dator från lokala till Azure med en icke-generaliserad disk][planning-guide-5.2.1] i det här dokumentet för lokala förberedelsesteg och överföring av icke-generaliserade virtuella datorer eller virtuella hårddiskar till Azure. Läs kapitel [Scenario 3: Flytta en virtuell dator från lokalt med hjälp av en icke-generaliserad Azure VHD med SAP][deployment-guide-3.4] i [distributionsguiden][deployment-guide] för detaljerade steg för att distribuera en sådan avbildning i Azure.

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Distribuera en virtuell dator med en kundspecifik avbildning

På grund av specifika korrigeringskrav för din OS- eller DBMS-version kanske de medföljande avbildningarna på Azure Marketplace inte passar dina behov. Därför kan du behöva skapa en virtuell dator med din egen privata OS/DBMS VM-avbildning, som kan distribueras flera gånger efteråt. För att förbereda en sådan privat bild för dubbelarbete måste följande objekt beaktas:

---
> ![Windows][Logo_Windows] Windows
>
> Se mer information <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> här: Windows-inställningarna (som Windows SID och värdnamn) måste vara abstrakta/generaliserade på den lokala virtuella datorn via kommandot sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Följ stegen som beskrivs i de här artiklarna för [SUSE,][virtual-machines-linux-create-upload-vhd-suse] [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]eller [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle]för att förbereda en virtuell hårddisk som ska överföras till Azure.
>
>

---
Om du redan har installerat SAP-innehåll i den lokala virtuella datorn (särskilt för 2-tier-system) kan du anpassa SAP-systeminställningarna efter distributionen av den virtuella Azure-datorn genom instansbytesproceduren som stöds av SAP Software Provisioning Manager (SAP Note [1619720]). Se kapitel Förberedelse för distribution av [en virtuell dator med en kundspecifik avbildning för SAP][planning-guide-5.2.2] och ladda upp en virtuell hårddisk från lokala till [Azure][planning-guide-5.3.2] i det här dokumentet för lokala förberedelsesteg och överföring av en generaliserad virtuell dator till Azure. Läs kapitel [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3] i [distributionsguiden][deployment-guide] för detaljerade steg för att distribuera en sådan avbildning i Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

Du vill använda en microsoft- eller tredjepartsavbildning från Azure Marketplace för att distribuera din virtuella dator. När du har distribuerat den virtuella datorn i Azure följer du samma riktlinjer och verktyg för att installera SAP-programvaran och/eller DBMS i den virtuella datorn som du skulle göra i en lokal miljö. Mer detaljerad distributionsbeskrivning finns i kapitel [Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP][deployment-guide-3.2] i [distributionsguiden][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Förbereda virtuella datorer med SAP för Azure

Innan du överför virtuella datorer till Azure måste du se till att virtuella datorer och virtuella hårddiskar uppfyller vissa krav. Det finns små skillnader beroende på distributionsmetoden som används.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Förberedelser för att flytta en virtuell dator från lokalt till Azure med en icke-generaliserad disk

En vanlig distributionsmetod är att flytta en befintlig virtuell dator, som kör ett SAP-system från lokalt till Azure. Den virtuella datorn och SAP-systemet i den virtuella datorn ska bara köras i Azure med samma värdnamn och troligen samma SAP SID. I det här fallet bör gästoperativsystemet för virtuell dator inte generaliseras för flera distributioner. Om det lokala nätverket har utökats till Azure kan även samma domänkonton användas inom den virtuella datorn som de användes innan lokalt.

Kraven när du förbereder din egen Azure VM Disk är:

* Ursprungligen VHD som innehåller operativsystemet kan ha en maximal storlek på 127GB bara. Denna begränsning eliminerades i slutet av mars 2015. Nu vhd som innehåller operativsystemet kan vara upp till 1 TB i storlek som alla andra Azure Storage värd VHD också.
* Det måste vara i fast VHD-format. Dynamiska virtuella hårddiskar eller virtuella hårddiskar i VHDx-format stöds ännu inte på Azure. Dynamiska virtuella hårddiskar konverteras till statiska virtuella hårddiskar när du laddar upp den virtuella hårddisken med PowerShell-kommando eller CLI
* VIRTUELLA hårddiskar, som är monterade på den virtuella datorn och bör monteras igen i Azure till den virtuella datorn måste också vara i ett fast VHD-format. Läs [den här artikeln (Linux)](../../linux/managed-disks-overview.md) och den här artikeln [(Windows)](../../windows/managed-disks-overview.md)) för storleksbegränsningar för datadiskar. Dynamiska virtuella hårddiskar konverteras till statiska virtuella hårddiskar när du laddar upp den virtuella hårddisken med PowerShell-kommando eller CLI
* Lägg till ett annat lokalt konto med administratörsbehörighet, som kan användas av Microsoft-stöd eller som kan tilldelas som kontext för tjänster och program som ska köras i tills den virtuella datorn har distribuerats och lämpligare användare kan användas.
* Lägg till andra lokala konton eftersom de kan behövas för det specifika distributionsscenariot.

---
> ![Windows][Logo_Windows] Windows
>
> I det här scenariot krävs ingen generalisering (sysprep) av den virtuella datorn för att överföra och distribuera den virtuella datorn på Azure.
> Kontrollera att enheten D:\ används inte.
> Ange automatisk diskmontering för anslutna diskar enligt beskrivningen i kapitlet [Ange automatisk montering för anslutna diskar][planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> I det här scenariot krävs ingen generalisering (waagent -deetavision) av den virtuella datorn för att överföra och distribuera den virtuella datorn på Azure.
> Kontrollera att /mnt/resource inte används och att ALLA diskar är monterade via uuid. För OS-disken kontrollerar du att starthanterarens post också återspeglar det uuid-baserade fästet.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Förberedelser för att distribuera en virtuell dator med en kundspecifik avbildning för SAP

VHD-filer som innehåller ett generaliserat operativsystem lagras i behållare på Azure Storage-konton eller som hanterade diskavbildningar. Du kan distribuera en ny virtuell dator från en sådan avbildning genom att referera till VHD- eller Managed Disk-avbildningen som en källa i distributionsmallfilerna enligt beskrivningen i kapitel [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3] i [distributionsguiden][deployment-guide].

Kraven när du förbereder din egen Azure VM-avbildning är:

* Ursprungligen VHD som innehåller operativsystemet kan ha en maximal storlek på 127GB bara. Denna begränsning eliminerades i slutet av mars 2015. Nu vhd som innehåller operativsystemet kan vara upp till 1 TB i storlek som alla andra Azure Storage värd VHD också.
* Det måste vara i fast VHD-format. Dynamiska virtuella hårddiskar eller virtuella hårddiskar i VHDx-format stöds ännu inte på Azure. Dynamiska virtuella hårddiskar konverteras till statiska virtuella hårddiskar när du laddar upp den virtuella hårddisken med PowerShell-kommando eller CLI
* VIRTUELLA hårddiskar, som är monterade på den virtuella datorn och bör monteras igen i Azure till den virtuella datorn måste också vara i ett fast VHD-format. Läs [den här artikeln (Linux)](../../windows/managed-disks-overview.md) och [den här artikeln (Windows)](../../linux/managed-disks-overview.md) för storleksbegränsningar för datadiskar. Dynamiska virtuella hårddiskar konverteras till statiska virtuella hårddiskar när du laddar upp den virtuella hårddisken med PowerShell-kommando eller CLI
* Lägg till andra lokala konton eftersom de kan behövas för det specifika distributionsscenariot.
* Om avbildningen innehåller en installation av SAP NetWeaver och namnbyte av värdnamnet från det ursprungliga namnet vid punkten för Azure-distributionen är sannolikt, rekommenderas det att kopiera de senaste versionerna av SAP Software Provisioning Manager DVD till mallen. Detta gör att du enkelt kan använda SAP som byter namn funktionalitet för att anpassa det ändrade värdnamnet och / eller ändra SID för SAP-systemet i den distribuerade VM-avbildningen så snart en ny kopia startas.

---
> ![Windows][Logo_Windows] Windows
>
> Kontrollera att enheten D:\ Används inte Ange disk automatiskt för anslutna diskar enligt beskrivningen i kapitlet [Inställning av automatisk montering för anslutna diskar][planning-guide-5.5.3] i det här dokumentet.
>
> ![Linux][Logo_Linux] Linux
>
> Kontrollera att /mnt/resource inte används och att ALLA diskar är monterade via uuid. För OS-disken kontrollerar du att starthanterarens post också återspeglar det uuid-baserade fästet.
>
>

---
* SAP GUI (för administrativa och installationsändamål) kan förinstalleras i en sådan mall.
* Andra program som krävs för att köra de virtuella datorerna framgångsrikt i lokala scenarier kan installeras så länge den här programvaran kan fungera med namnbytet av den virtuella datorn.

Om den virtuella datorn är tillräckligt förberedd för att vara allmän och så småningom oberoende av konton/användare som inte är tillgängliga i det riktade Azure-distributionsscenariot, utförs det sista förberedelsesteget för att generalisera en sådan avbildning.

##### <a name="generalizing-a-vm"></a>Generalisera en virtuell dator
---
> ![Windows][Logo_Windows] Windows
>
> Det sista steget är att logga in på en virtuell dator med ett administratörskonto. Öppna ett Windows-kommandofönster som *administratör*. Gå till %windir%\windows\system32\sysprep och kör sysprep.exe.
> Ett litet fönster visas. Det är viktigt att kontrollera **alternativet Generalize** (standardalternativet är avmarkerat) och ändra avstängningsalternativet från standardvärdet "Starta om" till "avstängning". Den här proceduren förutsätter att sysprep-processen körs lokalt i gästoperativsystemet för en virtuell dator.
> Om du vill utföra proceduren med en virtuell dator som redan körs i Azure följer du stegen som beskrivs i den [här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Avbilda en virtuell Linux-dator som ska användas som mall för Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Överföra virtuella datorer och virtuella hårddiskar mellan lokala till Azure
Eftersom det inte går att överföra VM-avbildningar och diskar till Azure via Azure-portalen måste du använda Azure PowerShell-cmdlets eller CLI. En annan möjlighet är användningen av verktyget "AzCopy". Verktyget kan kopiera virtuella hårddiskar mellan lokala och Azure (i båda riktningarna). Den kan också kopiera virtuella hårddiskar mellan Azure-regioner. Läs [denna dokumentation][storage-use-azcopy] för nedladdning och användning av AzCopy.

Ett tredje alternativ skulle vara att använda olika gui-orienterade verktyg från tredje part. Kontrollera dock att dessa verktyg stöder Azure Page Blobbar. För våra ändamål måste vi använda Azure Page Blob Store <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>(skillnaderna beskrivs här: ). Även de verktyg som tillhandahålls av Azure är effektiva för att komprimera virtuella datorer och virtuella hårddiskar, som måste överföras. Detta är viktigt eftersom den här effektiviteten i komprimeringen minskar uppladdningstiden (som varierar ändå beroende på överföringslänken till internet från den lokala anläggningen och azure-distributionsregionen som är inriktad). Det är ett rimligt antagande att det tar längre tid att överföra en virtuell dator eller virtuell hårddisk från europeisk plats till de USA-baserade Azure-datacenter som tar längre tid än att överföra samma virtuella datorer/virtuella hårddiskar till de europeiska Azure-datacenter.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Ladda upp en virtuell hårddisk från lokalt till Azure
Om du vill överföra en befintlig virtuell dator eller virtuell dator från det lokala nätverket måste en virtuell dator eller virtuell hårddisk uppfylla kraven i kapitlet Förberedelse för att [flytta en virtuell dator från lokalt till Azure med en icke-generaliserad disk][planning-guide-5.2.1] i det här dokumentet.

En sådan virtuell dator behöver INTE generaliseras och kan laddas upp i det tillstånd och den form den har efter avstängning på den lokala sidan. Detsamma gäller för ytterligare virtuella hårddiskar, som inte innehåller något operativsystem.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Ladda upp en virtuell hårddisk och göra den till en Azure-disk
I det här fallet vill vi ladda upp en virtuell hårddisk, antingen med eller utan ett OS i den, och montera den på en virtuell dator som en datadisk eller använda den som OS-disk. Detta är en process i flera steg

**Powershell**

* Logga in på din prenumeration med *Connect-AzAccount*
* Ange prenumerationen för din kontext med *Set-AzContext* och parameter SubscriptionId eller SubscriptionName - se<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Ladda upp den virtuella hårddisken med *Tillägg AzVhd* till ett Azure Storage-konto – se<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Valfritt) Skapa en hanterad disk från den virtuella hårddisken med *New-AzDisk* - se<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Ange OS-disken för en ny VM-konfiguration till VHD eller Hanterad disk med *Set-AzVMOSDisk* - se<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Skapa en ny virtuell dator från VM-konfigurationen med *New-AzVM* - se<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Lägga till en datadisk i en ny virtuell dator med *Add-AzVMDataDisk* - se<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Logga in på din prenumeration med *az login*
* Välj din prenumeration med *az-kontouppsättning --prenumeration `<subscription name or id` *
* Ladda upp den virtuella hårddisken med *az storage blob upload* – se Använda Azure CLI med Azure [Storage][storage-azure-cli]
* (Valfritt) Skapa en hanterad disk från den virtuella hårddisken med *az disk create* - sehttps://docs.microsoft.com/cli/azure/disk
* Skapa en ny virtuell dator som anger den uppladdade VHD- eller Managed Disk som OS-disk med *az vm create* och parameter *--attach-os-disk*
* Lägga till en datadisk i en ny virtuell dator med *az vm-diskanklag och* parameter *--new*

**Mall**

* Ladda upp den virtuella hårddisken med PowerShell eller Azure CLI
* (Valfritt) Skapa en hanterad disk från den virtuella hårddisken med PowerShell, Azure CLI eller Azure-portalen
* Distribuera den virtuella datorn med en JSON-mall som refererar till den virtuella hårddisken som visas i [det här exemplet JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller med hanterade diskar som visas i [det här exemplet JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Distribution av en vm-avbildning
Om du vill överföra en befintlig virtuell dator eller virtuell hårddisk från det lokala nätverket måste en virtuell dator för att kunna använda den som en Azure VM-avbildning, till exempel en virtuell dator eller virtuell hårddisk, uppfylla kraven i kapitlet Förberedelse för distribution av [en virtuell dator med en kundspecifik avbildning för SAP][planning-guide-5.2.2] för det här dokumentet.

* Använd *sysprep* på Windows eller *waagent -deetavision* på Linux för att generalisera din virtuella dator - se [Sysprep Teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller Så här fångar du en virtuell [Linux-dator som ska användas som Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *Connect-AzAccount*
* Ange prenumerationen för din kontext med *Set-AzContext* och parameter SubscriptionId eller SubscriptionName - se<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Ladda upp den virtuella hårddisken med *Tillägg AzVhd* till ett Azure Storage-konto – se<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Valfritt) Skapa en hanterad diskavbildning från den virtuella hårddisken med *New-AzImage* - se<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Ställ in OS-disken för en ny VM-konfiguration till
  * VHD med *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - se<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * *Hanterad diskavbildning Set-AzVMSourceImage* - se<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Skapa en ny virtuell dator från VM-konfigurationen med *New-AzVM* - se<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* Använd *sysprep* på Windows eller *waagent -deetavision* på Linux för att generalisera din virtuella dator - se [Sysprep Teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller Så här fångar du en virtuell [Linux-dator som ska användas som Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Logga in på din prenumeration med *az login*
* Välj din prenumeration med *az-kontouppsättning --prenumeration `<subscription name or id` *
* Ladda upp den virtuella hårddisken med *az storage blob upload* – se Använda Azure CLI med Azure [Storage][storage-azure-cli]
* (Valfritt) Skapa en hanterad diskavbildning från den virtuella hårddisken med *az image create* - sehttps://docs.microsoft.com/cli/azure/image
* Skapa en ny virtuell dator som anger den uppladdade VHD- eller Hanterade diskavbildningen som OS-disk med *az vm create* och parameter *--image*

**Mall**

* Använd *sysprep* på Windows eller *waagent -deetavision* på Linux för att generalisera din virtuella dator - se [Sysprep Teknisk referens](https://technet.microsoft.com/library/cc766049.aspx) för Windows eller Så här fångar du en virtuell [Linux-dator som ska användas som Resource Manager-mall][capture-image-linux-step-2-create-vm-image] för Linux
* Ladda upp den virtuella hårddisken med PowerShell eller Azure CLI
* (Valfritt) Skapa en hanterad diskavbildning från den virtuella hårddisken med PowerShell, Azure CLI eller Azure-portalen
* Distribuera den virtuella datorn med en JSON-mall som refererar till bildens virtuella hårddisk som visas i [det här exemplet JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) eller med hjälp av den hanterade diskavbildningen som visas i [det här exemplet JSON-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Hämta virtuella hårddiskar eller hanterade diskar till lokala
Azure Infrastructure as a Service är inte en enkelriktad gata för att bara kunna ladda upp virtuella hårddiskar och SAP-system. Du kan också flytta SAP-system från Azure till den lokala världen.

Under tiden för hämtningen kan inte virtuella hårddiskar eller hanterade diskar vara aktiva. Även när du hämtar diskar, som är monterade på virtuella datorer, måste den virtuella datorn stängas av och frigöras. Om du bara vill hämta databasinnehållet, som sedan bör användas för att konfigurera ett nytt system lokalt och om det är acceptabelt att under tiden för hämtningen och installationen av det nya systemet att systemet i Azure fortfarande kan vara i drift kan du undvika en lång driftstopp genom att utföra en komprimerad databassäkerhetskopiering till en disk och bara hämta den disken i stället för att också hämta den virtuella datorn för os-basen.

#### <a name="powershell"></a>PowerShell

* Hämta en hanterad disk  
  Du måste först få åtkomst till den underliggande bloben för den hanterade disken. Sedan kan du kopiera den underliggande blobben till ett nytt lagringskonto och hämta blobben från det här lagringskontot.

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

* Ladda ned en virtuell hårddisk  
  När SAP-systemet har stoppats och den virtuella datorn har stängts av kan du använda PowerShell-cmdlet Save-AzVhd på det lokala målet för att hämta VHD-diskarna tillbaka till den lokala världen. För att göra det behöver du URL:en för den virtuella hårddisken, som du hittar i "lagringsavsnittet" i Azure-portalen (måste navigera till lagringskontot och lagringsbehållaren där den virtuella hårddisken skapades) och du måste veta var den virtuella hårddisken ska kopieras till.

  Sedan kan du utnyttja kommandot genom att definiera parametern SourceUri som URL för den virtuella hårddisken att hämta och LocalFilePath som den fysiska platsen för den virtuella hårddisken (inklusive dess namn). Kommandot kan se ut så här:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  För mer information om Save-AzVhd cmdlet, kolla här <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>Azure CLI
* Hämta en hanterad disk  
  Du måste först få åtkomst till den underliggande bloben för den hanterade disken. Sedan kan du kopiera den underliggande blobben till ett nytt lagringskonto och hämta blobben från det här lagringskontot.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Ladda ned en virtuell hårddisk   
  När SAP-systemet har stoppats och den virtuella datorn har stängts av kan du använda Azure CLI-kommandot _azure storage blob download_ på det lokala målet för att hämta VHD-diskarna tillbaka till den lokala världen. För att göra det behöver du namnet och behållaren för den virtuella hårddisken, som du hittar i "Storage Section" i Azure-portalen (måste navigera till lagringskontot och lagringsbehållaren där den virtuella hårddisken skapades) och du måste veta var den virtuella hårddisken ska kopieras Att.

  Sedan kan du utnyttja kommandot genom att definiera parametrarna blob och behållare för DEN virtuella hårddisken att ladda ner och målet som den fysiska målplatsen för DEN VIRTUELLAD (inklusive dess namn). Kommandot kan se ut så här:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Överföra virtuella datorer och diskar i Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopiera SAP-system i Azure

Ett SAP-system eller till och med en dedikerad DBMS-server som stöder ett SAP-programlager kommer sannolikt att bestå av flera diskar, som innehåller antingen operativsystemet med binärfilerna eller data- och loggfilerna i SAP-databasen. Varken Azure-funktionen för att kopiera diskar eller Azure-funktionen för att spara diskar till en lokal disk har en synkroniseringsmekanism som ögonblicksbilder flera diskar på ett konsekvent sätt. Därför skulle tillståndet för de kopierade eller sparade diskarna även om de är monterade mot samma virtuella dator vara annorlunda. Detta innebär att i det konkreta fallet med att ha olika data och loggfiler som finns i de olika diskarna, skulle databasen i slutändan vara inkonsekvent.

**Slutsats: För att kopiera eller spara diskar, som ingår i en SAP-systemkonfiguration måste du stoppa SAP-systemet och även stänga av den distribuerade virtuella datorn. Först då kan du kopiera eller hämta uppsättningen diskar för att antingen skapa en kopia av SAP-systemet i Azure eller lokalt.**

Datadiskar kan lagras som VHD-filer i ett Azure Storage-konto och kan kopplas direkt till en virtuell dator eller användas som en avbildning. I det här fallet kopieras den virtuella hårddisken till en annan plats innan den kopplas till den virtuella datorn. Det fullständiga namnet på VHD-filen i Azure måste vara unikt i Azure. Som tidigare nämnts är namnet typ av ett tredelat namn som ser ut som:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Datadiskar kan också hanteras diskar. I det här fallet används den hanterade disken för att skapa en ny hanterad disk innan den kopplas till den virtuella datorn. Namnet på den hanterade disken måste vara unikt inom en resursgrupp.

##### <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell-cmdletar för att kopiera en virtuell hårddisk som visas i [den här artikeln][storage-powershell-guide-full-copy-vhd]. Om du vill skapa en ny hanterad disk använder du New-AzDiskConfig och New-AzDisk som visas i följande exempel.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Du kan använda Azure CLI för att kopiera en virtuell hårddisk. Om du vill skapa en ny hanterad disk använder du *az-disken* som visas i följande exempel.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-verktyg

* <https://storageexplorer.com/>

Professionella utgåvor av Azure Storage Explorers hittar du här:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Kopian av en virtuell hårddisk själv inom ett lagringskonto är en process, som tar bara några sekunder (liknande SAN-hårdvara skapa ögonblicksbilder med lat kopia och kopiera på skrivning). När du har en kopia av VHD-filen kan du bifoga den till en virtuell dator eller använda den som en avbildning för att bifoga kopior av den virtuella hårddisken till virtuella datorer.

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

```azurecli

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

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopiera diskar mellan Azure Storage-konton
Det går inte att utföra den här uppgiften på Azure-portalen. Du kan använda Azure PowerShell-cmdlets, Azure CLI eller en lagringswebbläsare från tredje part. PowerShell-cmdlets- eller CLI-kommandona kan skapa och hantera blobbar, vilket inkluderar möjligheten att asynkront kopiera blobbar över lagringskonton och mellan regioner inom Azure-prenumerationen.

##### <a name="powershell"></a>PowerShell
Du kan också kopiera virtuella hårddiskar mellan prenumerationer. Mer information finns i [den här artikeln][storage-powershell-guide-full-copy-vhd].

Det grundläggande flödet av PS cmdlet logik ser ut så här:

* Skapa en lagringskontokontext för källlagringskontot med *New-AzStorageContext* - se **source**<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Skapa en lagringskontokontext för mållagringskontot med *New-AzStorageContext* - se **target**<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Starta kopian med

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Kontrollera status för kopian i en slinga med

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Koppla den nya virtuella hårddisken till en virtuell dator enligt beskrivningen ovan.

Exempel på exempel finns [i den här artikeln][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure CLI
* Starta kopian med

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Kontrollera statusen om kopian fortfarande finns i en loop med

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Koppla den nya virtuella hårddisken till en virtuell dator enligt beskrivningen ovan.

### <a name="disk-handling"></a>Diskhantering

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM/diskstruktur för SAP-distributioner

Helst hanteringen av strukturen för en virtuell dator och tillhörande diskar bör vara enkel. I lokala installationer utvecklade kunderna många sätt att strukturera en serverinstallation.

* En basdisk, som innehåller operativsystemet och alla binärfiler för DBMS och/eller SAP. Sedan mars 2015 kan den här disken vara upp till 1 TB i storlek i stället för tidigare begränsningar som begränsade den till 127 GB.
* En eller flera diskar, som innehåller DBMS-loggfilen i SAP-databasen och loggfilen för DBMS temp-lagringsområdet (om DBMS stöder detta). Om IOPS-kraven för databasloggen är höga måste du stripe-flera diskar för att nå den IOPS-volym som krävs.
* Ett antal diskar som innehåller en eller två databasfiler i SAP-databasen och DBMS temp datafiler också (om DBMS stöder detta).

![Referenskonfiguration för Azure IaaS VM för SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Med många kunder såg vi konfigurationer där till exempel SAP- och DBMS-binärfiler inte installerades på c:\ enhet där operativsystemet installerades. Det fanns olika orsaker till detta, men när vi gick tillbaka till roten, var det oftast att enheterna var små och OS uppgraderingar behövs ytterligare utrymme 10-15 år sedan. Båda villkoren gäller inte dessa dagar alltför ofta längre. Idag c:\ kan mappas på stora volymdiskar eller virtuella datorer. För att hålla distributionerna enkla i sin struktur rekommenderar vi att du följer följande distributionsmönster för SAP NetWeaver-system i Azure
>
> Windows-operativsystemets växlingsfil ska finnas på D:-enheten (icke-beständig disk)
>
> ![Linux][Logo_Linux] Linux
>
> Placera Linux swapfile under /mnt /mnt/resource på Linux enligt beskrivningen i [den här artikeln][virtual-machines-linux-agent-user-guide]. Växlingsfilen kan konfigureras i konfigurationsfilen för Linux Agent /etc/waagent.conf. Lägg till eller ändra följande inställningar:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Om du vill aktivera ändringarna måste du starta om Linux-agenten med

```console
sudo service waagent restart
```

Läs SAP Note [1597355] för mer information om den rekommenderade växlingsfilstorleken

---
Antalet diskar som används för DBMS-datafilerna och vilken typ av Azure Storage dessa diskar finns på bör bestämmas av IOPS-kraven och den svarstid som krävs. Exakta kvoter beskrivs i [den här artikeln (Linux)][virtual-machines-sizes-linux] och [den här artikeln (Windows)][virtual-machines-sizes-windows].

Erfarenhet av SAP-distributioner under de senaste två åren har lärt oss några lektioner, som kan sammanfattas som:

* IOPS-trafik till olika datafiler är inte alltid densamma eftersom befintliga kundsystem kan ha olika storlek datafiler som representerar deras SAP-databaser(er). Som ett resultat visade det sig vara bättre att använda en RAID-konfiguration över flera diskar för att placera datafiler LUNs huggen ur dessa. Det fanns situationer, särskilt med Azure Standard Storage där en IOPS-kurs träffade kvoten för en enda disk mot DBMS-transaktionsloggen. I sådana fall rekommenderas användning av Premium Storage eller alternativt aggregera flera Standard Storage-diskar med en programvaruremsa.

---
> ![Windows][Logo_Windows] Windows
>
> * [Prestandametodtips för SQL Server i Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurera Software RAID på Linux][virtual-machines-linux-configure-raid]
> * [Konfigurera LVM på en Virtuell Linux-dator i Azure][virtual-machines-linux-configure-lvm]
> * [Azure Storage hemligheter och Linux I / O-optimeringar](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Premium Storage visar betydande bättre prestanda, särskilt för kritiska transaktionsloggskrivningar. För SAP-scenarier som förväntas leverera produktion som prestanda, rekommenderas det starkt att använda VM-serien som kan utnyttja Azure Premium Storage.

Tänk på att disken, som innehåller operativsystemet, och som vi rekommenderar, binärfilerna för SAP och databasen (bas-VM) också, inte längre är begränsad till 127 GB. Det kan nu ha upp till 1 TB i storlek. Detta bör vara tillräckligt med utrymme för att behålla alla nödvändiga filer, inklusive till exempel SAP-batchjobbloggar.

Fler förslag och mer information, särskilt för virtuella DBMS-datorer, finns i [DBMS Deployment Guide][dbms-guide]

#### <a name="disk-handling"></a>Diskhantering

I de flesta fall måste du skapa ytterligare diskar för att distribuera SAP-databasen till den virtuella datorn. Vi talade om överväganden om antalet diskar i kapitel [VM / disk struktur för SAP-distributioner][planning-guide-5.5.1] av detta dokument. Azure-portalen gör det möjligt att bifoga och koppla från diskar när en bas-VM har distribuerats. Diskarna kan anslutas/tas bort när den virtuella datorn är igång samt när den stoppas. När du ansluter en disk erbjuder Azure-portalen att bifoga en tom disk eller en befintlig disk, som vid denna tidpunkt inte är ansluten till en annan virtuell dator.

**Diskar**kan bara anslutas till en virtuell dator vid en given tidpunkt.

![Bifoga/koppla bort diskar med Azure Standard Storage][planning-guide-figure-1400]

Under distributionen av en ny virtuell dator kan du bestämma om du vill använda hanterade diskar eller placera diskarna på Azure Storage Accounts. Om du vill använda Premium Storage rekommenderar vi att du använder hanterade diskar.

Därefter måste du bestämma om du vill skapa en ny och tom disk eller om du vill välja en befintlig disk som har överförts tidigare och ska kopplas till den virtuella datorn nu.

**VIKTIGT:** **Du** vill INTE använda värdcache med Azure Standard Storage. Du bör lämna inställningen Värdcachen som standard för NONE. Med Azure Premium Storage bör du aktivera LäsCaching om I/O-egenskapen oftast läss som vanlig I/O-trafik mot databasdatafiler. Vid databastransaktionsloggfil rekommenderas ingen cachelagring.

---
> ![Windows][Logo_Windows] Windows
>
> [Bifoga en datadisk i Azure-portalen][virtual-machines-linux-attach-disk-portal]
>
> Om diskar är anslutna måste du logga in på den virtuella datorn för att öppna Windows Disk Manager. Om automatisk montering inte är aktiverat enligt rekommendationen i kapitlet [Ställa in automatisk montering för anslutna diskar][planning-guide-5.5.3]måste den nyligen anslutna volymen tas online och initieras.
>
> ![Linux][Logo_Linux] Linux
>
> Om diskar är anslutna måste du logga in på den virtuella datorn och initiera diskarna enligt beskrivningen i den [här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Om den nya disken är en tom disk måste du också formatera disken. För formatering, särskilt för DBMS-data och loggfiler, gäller samma rekommendationer som för bare-metal-distributioner av DBMS.

Ett Azure Storage-konto ger inte oändliga resurser när det gäller I/O-volym, IOPS och datavolym. Vanligtvis dbms virtuella datorer påverkas mest av detta. Det kan vara bäst att använda ett separat lagringskonto för varje virtuell dator om du har några virtuella datorer med hög I/O-volym att distribuera för att hålla sig inom gränsen för Azure Storage Account-volymen. Annars måste du se hur du kan balansera dessa virtuella datorer mellan olika lagringskonton utan att nå gränsen för varje enskilt lagringskonto. Mer information beskrivs i [DBMS Deployment Guide][dbms-guide]. Du bör också ha dessa begränsningar i åtanke för rena virtuella SAP-programserver eller andra virtuella datorer, som så småningom kan kräva ytterligare virtuella hårddiskar. Dessa begränsningar gäller inte om du använder Hanterad disk. Om du planerar att använda Premium Storage rekommenderar vi att du använder Hanterad disk.

Ett annat ämne, som är relevant för lagringskonton, är om de virtuella hårddiskarna i ett lagringskonto får Geo-replikerade. Geo-replikering är aktiverat eller inaktiverat på lagringskontonivå och inte på vm-nivå. Om geo-replikering är aktiverat replikeras de virtuella hårddiskarna i lagringskontot till ett annat Azure-datacenter inom samma region. Innan du beslutar om detta bör du tänka på följande begränsning:

Azure Geo-replication fungerar lokalt på varje virtuell hårddisk i en virtuell dator och replikerar inte IOs i kronologisk ordning över flera virtuella hårddiskar i en virtuell dator. Därför replikeras den virtuella hårddisken som representerar den virtuella datorn och eventuella ytterligare virtuella hårddiskar som är kopplade till den virtuella datorn oberoende av varandra. Det innebär att det inte finns någon synkronisering mellan ändringarna i de olika virtuella hårddiskarna. Det faktum att IOs replikeras oberoende av i vilken ordning de skrivs innebär att geo-replikering inte är av värde för databasservrar som har sina databaser distribueras över flera virtuella hårddiskar. Förutom DBMS kan det också finnas andra program där processer skriver eller manipulerar data i olika virtuella hårddiskar och där det är viktigt att hålla ordningen på ändringarna. Om det är ett krav bör geo-replikering i Azure inte aktiveras. Beroende på om du behöver eller vill ha geo-replikering för en uppsättning virtuella datorer, men inte för en annan uppsättning, kan du redan kategorisera virtuella datorer och deras relaterade virtuella hårddiskar i olika lagringskonton som har geo-replikering aktiverat eller inaktiverat.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ställa in automatiskmontering för anslutna diskar
---
> ![Windows][Logo_Windows] Windows
>
> För virtuella datorer, som skapas från egna bilder eller diskar, är det nödvändigt att kontrollera och eventuellt ställa in parametern automount. Om du ställer in den här parametern kan den virtuella datorn efter en omstart eller omdistribution i Azure montera de anslutna/monterade enheterna igen automatiskt.
> Parametern är inställd för avbildningar som tillhandahålls av Microsoft på Azure Marketplace.
>
> Om du vill ställa in automounten kan du läsa dokumentationen för kommandoradens körbara diskpart.exe här:
>
> * [Kommandoradsalternativ för DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](https://technet.microsoft.com/library/cc753703.aspx)
>
> Kommandoradsfönstret i Windows ska öppnas som administratör.
>
> Om diskar är anslutna måste du logga in på den virtuella datorn för att öppna Windows Disk Manager. Om automatisk montering inte är aktiverat enligt rekommendationen i kapitlet [Ställa in automatisk montering för anslutna diskar][planning-guide-5.5.3]måste den nyligen anslutna volymen >tas online och initieras.
>
> ![Linux][Logo_Linux] Linux
>
> Du måste initiera en nyligen ansluten tom disk enligt beskrivningen i [den här artikeln][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Du måste också lägga till nya diskar till / etc / fstab.
>
>

---
### <a name="final-deployment"></a>Slutlig distribution

Den slutliga distributionen och de exakta stegen, särskilt när det gäller distributionen av Azure Extension för SAP, finns i [distributionsguiden][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Komma åt SAP-system som körs i virtuella Azure-datorer

För scenarier där du vill ansluta till dessa SAP-system över det offentliga internet med HJÄLP AV SAP GUI, måste följande procedurer tillämpas.

Senare i dokumentet kommer vi att diskutera det andra stora scenariot, ansluta till SAP-system i korslokala distributioner, som har en plats-till-plats-anslutning (VPN-tunnel) eller Azure ExpressRoute-anslutning mellan lokala system och Azure-system.

### <a name="remote-access-to-sap-systems"></a>Fjärråtkomst till SAP-system

Med Azure Resource Manager finns det inga standardslutpunkter längre som i den tidigare klassiska modellen. Alla portar för en virtuell Azure Resource Manager-dator är öppna så länge:

1. Ingen nätverkssäkerhetsgrupp har definierats för undernätet eller nätverksgränssnittet. Nätverkstrafik till virtuella Azure-datorer kan skyddas via så kallade "Nätverkssäkerhetsgrupper". Mer information finns i [Vad är en nätverkssäkerhetsgrupp?][virtual-networks-nsg]
2. Ingen Azure Load Balancer har definierats för nätverksgränssnittet   

Se arkitekturskillnaden mellan klassisk modell och ARM enligt beskrivningen i [den här artikeln][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguration av SAP-system- och SAP GUI-anslutningen via internet

Se den här artikeln, som beskriver detaljer för detta ämne:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Ändra brandväggsinställningar inom den virtuella datorn

Det kan vara nödvändigt att konfigurera brandväggen på dina virtuella datorer för att tillåta inkommande trafik till ditt SAP-system.

---
> ![Windows][Logo_Windows] Windows
>
> Som standard är Windows-brandväggen i en Azure-distribuerad virtuell dator aktiverad. Du måste nu tillåta att SAP-porten öppnas, annars kommer SAP-gränssnittet inte att kunna ansluta.
> Gör så här:
>
> * Öppna Kontrollpanelen\System och säkerhet\Windows-brandväggen till **avancerade inställningar**.
> * Högerklicka nu på Inkommande regler och valde **Ny regel**.
> * I följande guide valde du att skapa en ny **portregel.**
> * I nästa steg i guiden lämnar du inställningen vid TCP och skriver in det portnummer som du vill öppna. Eftersom vårt SAP-instans-ID är 00 tog vi 3200. Om din instans har ett annat instansnummer ska porten som du definierade tidigare baserat på instansnumret öppnas.
> * I nästa del av guiden måste du lämna objektet **Tillåt anslutning** markerat.
> * I nästa steg i guiden måste du definiera om regeln gäller för domän, privat och offentligt nätverk. Justera den om det behövs efter dina behov. Men ansluta med SAP GUI från utsidan via det offentliga nätverket, måste du ha regeln tillämpas på det offentliga nätverket.
> * I det sista steget i guiden namnger du regeln och sparar genom att trycka på **Slutför**.
>
> Regeln träder i kraft omedelbart.
>
> ![Definition av portregel][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Linux-avbildningarna på Azure Marketplace aktiverar inte iptables-brandväggen som standard och anslutningen till ditt SAP-system ska fungera. Om du har aktiverat iptables eller en annan brandvägg läser du dokumentationen av iptables eller den använda brandväggen för att tillåta inkommande tcp-trafik till port 32xx (där xx är systemnumret för ditt SAP-system).
>
>

---
#### <a name="security-recommendations"></a>Säkerhetsrekommendationer

SAP-gränssnittet ansluter inte omedelbart till någon av SAP-instanserna (port 32xx) som körs, men ansluter först via porten som öppnas till SAP-meddelandeserverprocessen (port 36xx). Tidigare användes samma port av meddelandeservern för intern kommunikation till programinstanserna. För att förhindra att lokala programservrar oavsiktligt kommunicerar med en meddelandeserver i Azure kan de interna kommunikationsportarna ändras. Det rekommenderas starkt att ändra den interna kommunikationen mellan SAP-meddelandeservern och dess programinstanser till ett annat portnummer på system som har klonats från lokala system, till exempel en klon av utveckling för projekttestning etc. Detta kan göras med standardprofilparametern:

> rdisp/msserv_internal
>
>

som dokumenterats i [säkerhetsinställningar för SAP-meddelandeservern](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Enkel virtuell dator med SAP NetWeaver demo /utbildning scenario

![Köra enstaka VM SAP-demosystem med samma VM-namn, isolerade i Azure Cloud Services][planning-guide-figure-1700]

I det här scenariot implementerar vi ett typiskt scenario för utbildning/demosystem där det fullständiga tränings-/demoscenariot finns i en enda virtuell dator. Vi antar att distributionen sker via VM-avbildningsmallar. Vi antar också att flera av dessa virtuella demo/utbildningar måste distribueras med de virtuella datorerna med samma namn. Hela utbildningssystemen har inte anslutning till dina lokala tillgångar och är motsatsen till en hybriddistribution.

Antagandet är att du har skapat en vm-avbildning enligt beskrivningen i vissa avsnitt i kapitlet [Förbereda virtuella datorer med SAP för Azure][planning-guide-5.2] i det här dokumentet.

Händelseförloppet för att implementera scenariot ser ut så här:

##### <a name="powershell"></a>PowerShell

* Skapa en ny resursgrupp för varje utbildnings-/demolandskap

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Skapa ett nytt lagringskonto om du inte vill använda hanterade diskar

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Skapa ett nytt virtuellt nätverk för varje utbildnings-/demolandskap för att möjliggöra användning av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en nätverkssäkerhetsgrupp som endast tillåter trafik till port 3389 för att aktivera åtkomst till fjärrskrivbord och port 22 för SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Skapa en ny offentlig IP-adress som kan användas för att komma åt den virtuella datorn från Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Skapa ett nytt nätverksgränssnitt för den virtuella datorn

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Skapa en virtuell dator. I det här fallet har varje virtuell dator samma namn. SAP SID för SAP NetWeaver-instanserna i dessa virtuella datorer kommer också att vara desamma. Inom Azure Resource Group måste namnet på den virtuella datorn vara unikt, men i olika Azure Resource Groups kan du köra virtuella datorer med samma namn. Standardkontot "Administratör" för Windows eller "root" för Linux är ogiltigt. Därför måste ett nytt administratörsanvändarnamn definieras tillsammans med ett lösenord. Storleken på den virtuella datorn måste också definieras.

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

* Du kan också lägga till ytterligare diskar och återställa nödvändigt innehåll. Alla blobnamn (URL:er till blobbar) måste vara unika i Azure.

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

Följande exempelkod kan användas på Linux. För Windows använder du powershell enligt beskrivningen ovan eller anpassar exemplet så att %rgName% används i stället för $rgName och anger miljövariabeln med kommandot *Windows*.

* Skapa en ny resursgrupp för varje utbildnings-/demolandskap

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Skapa ett nytt lagringskonto

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Skapa ett nytt virtuellt nätverk för varje utbildnings-/demolandskap för att möjliggöra användning av samma värdnamn och IP-adresser. Det virtuella nätverket skyddas av en nätverkssäkerhetsgrupp som endast tillåter trafik till port 3389 för att aktivera åtkomst till fjärrskrivbord och port 22 för SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Skapa en ny offentlig IP-adress som kan användas för att komma åt den virtuella datorn från Internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Skapa ett nytt nätverksgränssnitt för den virtuella datorn

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Skapa en virtuell dator. I det här fallet har varje virtuell dator samma namn. SAP SID för SAP NetWeaver-instanserna i dessa virtuella datorer kommer också att vara desamma. Inom Azure Resource Group måste namnet på den virtuella datorn vara unikt, men i olika Azure Resource Groups kan du köra virtuella datorer med samma namn. Standardkontot "Administratör" för Windows eller "root" för Linux är ogiltigt. Därför måste ett nytt administratörsanvändarnamn definieras tillsammans med ett lösenord. Storleken på den virtuella datorn måste också definieras.

```azurecli
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

```azurecli
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

* Du kan också lägga till ytterligare diskar och återställa nödvändigt innehåll. Alla blobnamn (URL:er till blobbar) måste vara unika i Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Mall

Du kan använda exempelmallarna i databasen för azure-quickstart-templates på GitHub.

* [Enkel Virtuella Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Enkel Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VIRTUELL dator från avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementera en uppsättning virtuella datorer som kommunicerar inom Azure

Det här icke-hybridscenariot är ett typiskt scenario för utbildnings- och demoändamål där programvaran som representerar demo/training-scenariot är spridd över flera virtuella datorer. De olika komponenterna som installeras i de olika virtuella datorerna måste kommunicera med varandra. Återigen, i det här scenariot behövs ingen lokal nätverkskommunikation eller lokalt scenario.

Det här scenariot är ett tillägg av installationen som beskrivs i kapitel [Single VM med SAP NetWeaver demo /training scenario][planning-guide-7.1] för det här dokumentet. I det här fallet läggs fler virtuella datorer till i en befintlig resursgrupp. I följande exempel består utbildningslandskapet av en SAP ASCS/SCS VM, en virtuell dator som kör en DBMS och en VIRTUELL SAP Application Server-instans.

Innan du skapar det här scenariot måste du tänka på grundläggande inställningar som redan utnyttjats i scenariot tidigare.

#### <a name="resource-group-and-virtual-machine-naming"></a>Namn på resursgrupp och virtuell dator

Alla resursgruppsnamn måste vara unika. Utveckla ditt eget namngivningsschema för `<rg-name` dina resurser, till exempel>-suffixet.

Namnet på den virtuella datorn måste vara unikt inom resursgruppen.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurera Nätverk för kommunikation mellan de olika virtuella datorerna

![Uppsättning virtuella datorer i ett virtuellt Azure-nätverk][planning-guide-figure-1900]

För att förhindra namngivning av kollisioner med kloner av samma utbildnings-/demolandskap måste du skapa ett virtuellt Azure-nätverk för varje landskap. DNS-namnmatchning kommer att tillhandahållas av Azure eller så kan du konfigurera din egen DNS-server utanför Azure (inte diskuteras vidare här). I det här fallet konfigurerar vi inte vår egen DNS. För alla virtuella datorer i ett Virtuellt Azure-nätverk aktiveras kommunikation via värdnamn.

Skälen till att separera utbildning eller demo landskap av virtuella nätverk och inte bara resursgrupper kan vara:

* SAP-landskapet som ställts in behöver sin egen AD/OpenLDAP och en domänserver måste ingå i var och en av landskapen.  
* SAP-landskapet som ställts in har komponenter som behöver arbeta med fasta IP-adresser.

Mer information om Virtuella Azure-nätverk och hur du definierar dem finns i [den här artikeln][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Distribuera virtuella SAP-datorer med företagsnätverksanslutning (korslokalt)

Du kör ett SAP-landskap och vill dela upp distributionen mellan bare-metal för avancerade DBMS-servrar, lokala virtualiserade miljöer för programlager och mindre 2-nivå konfigurerade SAP-system och Azure IaaS. Grundantagandet är att SAP-system inom ett SAP-landskap måste kommunicera med varandra och med många andra programvarukomponenter som distribueras i företaget, oberoende av deras distributionsformulär. Det bör inte heller finnas några skillnader som introduceras av distributionsformuläret för slutanvändaren som ansluter till SAP GUI eller andra gränssnitt. Dessa villkor kan endast uppfyllas när vi har de lokala Active Directory/OpenLDAP- och DNS-tjänsterna utsträckta till Azure-system via anslutning från plats till plats/flera platser eller privata anslutningar som Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenario för ett SAP-landskap

Den cross-premises eller hybrid scenario kan grovt beskrivas som i grafiken nedan:

![Anslutning mellan lokala resurser och Azure-resurser på plats till plats][planning-guide-figure-2100]

Minimikravet är användningen av säkra kommunikationsprotokoll som SSL/TLS för webbläsaråtkomst eller VPN-baserade anslutningar för systemåtkomst till Azure-tjänsterna. Antagandet är att företag hanterar VPN-anslutningen mellan sitt företagsnätverk och Azure på olika sätt. Vissa företag kan öppna alla portar blankt. Vissa andra företag kanske vill vara exakt i vilka hamnar de behöver för att öppna, etc.

I tabellen nedan listas typiska SAP-kommunikationsportar. I grund och botten är det tillräckligt att öppna SAP gateway-porten.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Tjänst | Portnamn | Exempel `<nn`> = 01 | Standardintervall (min-max) | Kommentar |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` se * |3201 |3200 - 3299 |SAP Dispatcher, som används av SAP GUI för Windows och Java |
| Meddelandeserver |sapms`<sid`> se ** |3600 |gratis sapms`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> se * |3301 |Gratis |SAP gateway, som används för CPIC- och RFC-kommunikation |
| SAP-router |sapdp99 (sapdp9) |3299 |Gratis |Endast CI (central instans) Servicenamn kan tilldelas i /etc/services till ett godtyckligt värde efter installationen. |

*) nn = SAP-instansnummer

**) sid = SAP-System-ID

Mer detaljerad information om portar som krävs för olika <https://scn.sap.com/docs/DOC-17124>SAP-produkter eller tjänster av SAP-produkter finns här .
Med det här dokumentet bör du kunna öppna dedikerade portar i VPN-enheten som krävs för specifika SAP-produkter och scenarier.

Andra säkerhetsåtgärder vid distribution av virtuella datorer i ett sådant scenario kan vara att skapa en [nätverkssäkerhetsgrupp][virtual-networks-nsg] för att definiera åtkomstregler.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Skriva ut på en lokal nätverksskrivare från SAP-instans i Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Skriva ut över TCP/IP i scenario över flera platser

Att konfigurera lokala TCP/IP-baserade nätverksskrivare i en virtuell Azure-dator är totalt sett detsamma som i företagets nätverk, förutsatt att du har en VPN-site-to-Site-tunnel eller ExpressRoute-anslutning upprättad.

---
> ![Windows][Logo_Windows] Windows
>
> Gör så här:
>
> * Vissa nätverksskrivare levereras med en konfigurationsguide som gör det enkelt att konfigurera skrivaren i en virtuell Azure-dator. Om inget guideprogram har distribuerats med skrivaren är det manuella sättet att konfigurera skrivaren att skapa en ny TCP/IP-skrivarport.
> * Öppna Kontrollpanelen -> enheter och skrivare –> Lägga till en skrivare
> * Välj Lägga till en skrivare med en TCP/IP-adress eller värdnamn
> * Skriv in skrivarens IP-adress
> * Standard 9100 av skrivarport
> * Installera vid behov lämplig skrivardrivrutin manuellt.
>
> ![Linux][Logo_Linux] Linux
>
> * som för Windows följ bara standardproceduren för att installera en nätverksskrivare
> * följ bara den offentliga Linux guider för [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) eller [Red Hat och Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) om hur man lägger till en skrivare.
>
>

---
![Utskrift av nätverk][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Värdbaserad skrivare över SMB (delad skrivare) i scenario med flera lokala objekt

Värdbaserade skrivare är inte nätverkskompatibla avsiktligt. Men en värdbaserad skrivare kan delas mellan datorer i ett nätverk så länge skrivaren är ansluten till en dator som är påslagen. Anslut ditt företagsnätverk antingen Site-To-Site eller ExpressRoute och dela din lokala skrivare. SMB-protokollet använder NetBIOS i stället för DNS som namntjänst. NetBIOS-värdnamnet kan skilja sig från DNS-värdnamnet. Standardfallet är att NetBIOS-värdnamnet och DNS-värdnamnet är identiska. DNS-domänen är inte meningsfull i NetBIOS-namnutrymmet. Det fullständigt kvalificerade DNS-värdnamnet som består av DNS-värdnamnet och DNS-domänen får därför inte användas i NetBIOS-namnutrymmet.

Skrivarresursen identifieras med ett unikt namn i nätverket:

* Värdnamn för SMB-värden (behövs alltid).
* Aktiens namn (alltid nödvändigt).
* Namnet på domänen om skrivarresursen inte finns i samma domän som SAP-systemet.
* Dessutom kan ett användarnamn och ett lösenord krävas för att komma åt skrivarresursen.

Anvisningar:

---
> ![Windows][Logo_Windows] Windows
>
> Dela din lokala skrivare.
> Öppna Utforskaren i Azure-datorn och skriv in skrivarens resursnamn.
> En guide för skrivarinstallation hjälper dig genom installationsprocessen.
>
> ![Linux][Logo_Linux] Linux
>
> Här är några exempel på dokumentation om hur du konfigurerar nätverksskrivare i Linux eller inkluderar ett kapitel om utskrift i Linux. Det fungerar på samma sätt i en Azure Linux-vm så länge den virtuella datorn är en del av en VPN:
>
> * Sles<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL eller Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-skrivare (vidarebefordran av skrivare)

I Azure är möjligheten för fjärrskrivbordstjänsterna att ge användarna åtkomst till sina lokala skrivarenheter i en fjärrsession inte tillgänglig.

---
> ![Windows][Logo_Windows] Windows
>
> Mer information om utskrift med Windows <https://technet.microsoft.com/library/jj590748.aspx>finns här: .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrering av SAP Azure Systems i Correction and Transport System (TMS) i korslokaler

SAP Change and Transport System (TMS) måste konfigureras för att exportera och importera transportbegäran över system i landskapet. Vi antar att utvecklingsinstanserna för ett SAP-system (DEV) finns i Azure medan kvalitetssäkring (QA) och produktiva system (PRD) är lokala. Dessutom utgår vi från att det finns en central transportkatalog.

##### <a name="configuring-the-transport-domain"></a>Konfigurera transportdomänen

Konfigurera transportdomänen på det system som du har angett som transportdomänkontrollant enligt beskrivningen i [Konfigurera transportdomänkontrollanten](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). En systemanvändare TMSADM skapas och önskat RFC-mål genereras. Du kan kontrollera dessa RFC-anslutningar med hjälp av transaktionen SM59. Värdnamnsmatchning måste vara aktiverad i hela transportdomänen.

Anvisningar:

* I vårt scenario bestämde vi att det lokala QAS-systemet kommer att vara CTS-domänkontrollanten. Anropstransaktion STMS. Dialogrutan TMS visas. Dialogrutan Konfigurera transportdomän visas. (Den här dialogrutan visas bara om du ännu inte har konfigurerat en transportdomän.)
* Kontrollera att den automatiskt skapade användaren TMSADM är auktoriserad (SM59 -> ABAP TMSADM@E61.DOMAIN_E61 Connection -> -> Information -> Utilities(M) -> Auktoriseringstest). Den första skärmen för transaktionen STMS bör visa att detta SAP-system nu fungerar som kontrollant för transportdomänen som visas här:

![Inledande skärm för transaktion STMS på domänkontrollanten][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inklusive SAP-system i transportdomänen

Sekvensen av att inkludera ett SAP-system i en transportdomän ser ut som följer:

* På DEV-systemet i Azure går du till transportsystemet (Klient 000) och anropar transaktion STMS. Välj Annan konfiguration i dialogrutan och fortsätt med Inkludera system i domänen. Ange domänkontrollanten som målvärd ([Inklusive SAP-system i transportdomänen](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systemet väntar nu på att ingå i transportområdet.
* Av säkerhetsskäl måste du sedan gå tillbaka till domänkontrollanten för att bekräfta din begäran. Välj Systemöversikt och Godkänn väntesystemet. Bekräfta sedan uppmaningen och konfigurationen kommer att distribueras.

Detta SAP-system innehåller nu nödvändig information om alla andra SAP-system i transportdomänen. Samtidigt skickas adressdata för det nya SAP-systemet till alla andra SAP-system och SAP-systemet anges i transportprofilen för transportkontrollprogrammet. Kontrollera om RFC:er och åtkomst till domänens transportkatalog fungerar.

Fortsätt med konfigurationen av ditt transportsystem som vanligt enligt beskrivningen i dokumentationen [Förändrings- och transportsystem](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Anvisningar:

* Kontrollera att stms-datorn är korrekt konfigurerad.
* Kontrollera att värdnamnet för transportdomänkontrollanten kan lösas av din virtuella dator på Azure och vice visum.
* Anropstransaktionen STMS -> annan konfiguration -> inkludera system i domänen.
* Bekräfta anslutningen i det lokala TMS-systemet.
* Konfigurera transportvägar, grupper och lager som vanligt.

I plats-till-plats-anslutna korslokala scenarier kan svarstiden mellan lokala och Azure fortfarande vara betydande. Om vi följer sekvensen av att transportera objekt genom utvecklings- och testsystem till produktion eller funderar på att tillämpa transporter eller stödpaket på de olika systemen, inser du att beroende på placeringen av den centrala transportkatalogen, vissa av systemen kommer att stöta på hög latens läsning eller skriva data i den centrala transportkatalogen. Situationen liknar SAP-landskapskonfigurationer där de olika systemen sprids via olika datacenter med stort avstånd mellan datacenter.

För att undvika sådan latens och låta systemen fungera snabbt när du läser eller skriver till eller från transportkatalogen kan du ställa in två STMS-transportdomäner (en för lokala och en med systemen i Azure och länka transportdomänerna. Läs den här dokumentationen, som förklarar principerna bakom <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>detta koncept i SAP TMS: .

Anvisningar:

* Konfigurera en transportdomän på varje plats (lokalt och Azure) med hjälp av transaktion STMS<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Länka domänerna med en domänlänk och bekräfta länken mellan de två domänerna.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuera konfigurationen till det länkade systemet.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-trafik mellan SAP-instanser som finns i Azure och lokalt (Lokalt)

RFC-trafik mellan system, som är lokala och i Azure, måste fungera. Om du vill ställa in en anslutningsanropstransaktion SM59 i ett källsystem där du måste definiera en RFC-anslutning till målsystemet. Konfigurationen liknar standardinställningen för en RFC-anslutning.

Vi antar att i scenariot mellan lokala och lokala datorer finns de virtuella datorerna, som kör SAP-system som behöver kommunicera med varandra, i samma domän. Därför skiljer sig inte installationen av en RFC-anslutning mellan SAP-system från inställningsstegen och indata i lokala scenarier.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Komma åt lokala filaktier från SAP-instanser som finns i Azure eller vice versa

SAP-instanser som finns i Azure måste komma åt filresurser, som finns inom företagets lokaler. Dessutom måste lokala SAP-instanser komma åt filresurser som finns i Azure. Om du vill aktivera filresurserna måste du konfigurera behörigheterna och delningsalternativen för det lokala systemet. Se till att öppna portarna på VPN- eller ExpressRoute-anslutningen mellan Azure och ditt datacenter.

## <a name="supportability"></a>Support

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-tillägg för SAP

För att kunna mata en del av Azure-infrastrukturinformationen för verksamhetskritiska SAP-system till SAP Host Agent-instanserna, installerade i virtuella datorer, måste ett Azure-tillägg (VM) för SAP installeras för de distribuerade virtuella datorerna. Eftersom kraven från SAP var specifika för SAP-program, beslutade Microsoft att inte allmänt implementera de nödvändiga funktionerna i Azure, men lämna det för kunder att distribuera nödvändiga VM-tillägg och konfigurationer till sina virtuella datorer som körs i Azure. Distribution och livscykelhantering av Azure VM-tillägget för SAP kommer dock att automatiseras mestadels av Azure.

#### <a name="solution-design"></a>Lösningsdesign

Lösningen som utvecklats för att sap-värdagenten ska kunna hämta den information som krävs baseras på arkitekturen för Azure VM-agent- och tilläggsramverk. Tanken med Azure VM Agent and Extension framework är att tillåta installation av program som är tillgängliga i Azure VM Extension-galleriet i en virtuell dator. Principidén bakom det här konceptet är att tillåta (i fall som Azure Extension för SAP), distribution av särskilda funktioner till en virtuell dator och konfigurationen av sådan programvara vid distributionen.

Den "Azure VM-agent" som möjliggör hantering av specifika Azure VM-tillägg inom den virtuella datorn injiceras i Windows virtuella datorer som standard när den virtuella datorn skapas i Azure-portalen. När det gäller SUSE, Red Hat eller Oracle Linux är VM-agenten redan en del av Azure Marketplace-avbildningen. Om en skulle ladda upp en Linux-vm från lokalt till Azure vm-agenten måste installeras manuellt.

De grundläggande byggstenarna i lösningen för att tillhandahålla Azure-infrastrukturinformation till SAP Host-agenten i Azure ser ut så här:

![Komponenter i Microsoft Azure-tillägg][planning-guide-figure-2400]

Som visas i blockdiagrammet ovan finns en del av lösningen i Azure VM Image och Azure Extension Gallery, som är en globalt replikerad databas som hanteras av Azure Operations. Det är ansvaret för det gemensamma SAP/MS-teamet som arbetar med Azure-implementeringen av SAP för att arbeta med Azure Operations för att publicera nya versioner av Azure Extension för SAP.

När du distribuerar en ny Windows-virtuell dator läggs Azure VM-agenten automatiskt till i den virtuella datorn. Funktionen för den här agenten är att samordna inläsning och konfiguration av Azure-tillägg för de virtuella datorerna. För virtuella Linux-datorer är Azure VM-agenten redan en del av Azure Marketplace OS-avbildningen.

Det finns dock ett steg som fortfarande måste utföras av kunden. Detta är aktivering och konfiguration av prestandasamlingen. Processen som är relaterad till konfigurationen automatiseras av ett PowerShell-skript eller CLI-kommando. PowerShell-skriptet kan hämtas i Microsoft Azure Script Center enligt beskrivningen i [distributionsguiden][deployment-guide].

Den övergripande arkitekturen för Azure-tillägget för SAP ser ut:

![Azure-tillägg för SAP ][planning-guide-figure-2500]

**Om du vill ha exakt hur du kan och för detaljerade steg med att använda dessa PowerShell-cmdlets eller CLI-kommandon under distributionar följer du instruktionerna i [distributionsguiden][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrering av Azure-lokaliserad SAP-instans i SAProuter

SAP-instanser som körs i Azure måste också vara tillgängliga från SAProuter.

![Nätverksanslutning för SAP-router][planning-guide-figure-2600]

En SAProuter möjliggör TCP/IP-kommunikation mellan deltagande system om det inte finns någon direkt IP-anslutning. Detta ger fördelen att ingen heltäckande anslutning mellan kommunikationspartnerna är nödvändig på nätverksnivå. SAProuter lyssnar som standard på port 3299.
Om du vill ansluta SAP-instanser via en SAProuter måste du ge SAProuter-strängen och värdnamnet med alla försök att ansluta.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver SOM Java

Hittills har fokus för dokumentet varit SAP NetWeaver i allmänhet eller SAP NetWeaver ABAP stacken. I det här lilla avsnittet visas särskilda överväganden för SAP Java-stacken. En av de viktigaste SAP NetWeaver Java uteslutande baserade program är SAP Enterprise Portal. Andra SAP NetWeaver-baserade program som SAP PI och SAP Solution Manager använder både SAP NetWeaver ABAP och Java stackar. Därför finns det verkligen ett behov av att överväga specifika aspekter relaterade till SAP NetWeaver Java stacken också.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Installationen av en SAP-portal i en virtuell Azure-dator skiljer sig inte från en lokal installation om du distribuerar i scenarier över flera lokala. Eftersom DNS görs av lokala, kan portinställningarna för de enskilda instanserna göras som konfigurerade lokalt. De rekommendationer och begränsningar som beskrivs i det här dokumentet hittills gäller för ett program som SAP Enterprise Portal eller SAP NetWeaver Java stacken i allmänhet.

![Exponerad SAP-portal][planning-guide-figure-2700]

Ett speciellt distributionsscenario av vissa kunder är den direkta exponeringen av SAP Enterprise Portal till Internet medan den virtuella datorns värd är ansluten till företagets nätverk via plats-till-plats VPN-tunnel eller ExpressRoute. För ett sådant scenario måste du se till att specifika portar är öppna och inte blockeras av brandvägg eller nätverkssäkerhetsgrupp. 

Den ursprungliga portalen URI`<Portalserver` är http(s):>:5XX00/irj där porten bildas <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>som dokumenteras av SAP i .

![Konfiguration av slutpunkt][planning-guide-figure-2800]

Om du vill anpassa URL:en och/eller portarna till SAP Enterprise Portal kan du läsa den här dokumentationen:

* [Ändra url till portalen](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Ändra standardportnummer, Portalportnummer](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>HÖG tillgänglighet (HA) och DISASTER Recovery (DR) för SAP NetWeaver som körs på virtuella Azure-datorer

### <a name="definition-of-terminologies"></a>Definition av terminologier

Termen **hög tillgänglighet (HA)** är i allmänhet relaterad till en uppsättning tekniker som minimerar IT-störningar genom att tillhandahålla affärskontinuitet för IT-tjänster via redundanta, feltoleranta eller redundansskyddade komponenter i **samma** datacenter. I vårt fall inom en Azure-region.

**Haveriberedskap (DR)** är också inriktad på att minimera IT-tjänster störningar, och deras återhämtning men över **olika** datacenter, som vanligtvis ligger hundratals kilometer bort. I vårt fall vanligtvis mellan olika Azure-regioner inom samma geopolitiska region eller som fastställts av dig som kund.

### <a name="overview-of-high-availability"></a>Översikt över hög tillgänglighet

Vi kan dela upp diskussionen om SAP-hög tillgänglighet i Azure i två delar:

* **Azure-infrastruktur hög tillgänglighet**, till exempel HA av beräkning (virtuella datorer), nätverk, lagring etc. och dess fördelar för att öka SAP-programtillgänglighet.
* **SAP-program hög tillgänglighet**, till exempel HA av SAP-programvarukomponenter:
  * SAP-programservrar
  * SAP ASCS/SCS-instans
  * DB-server

och hur det kan kombineras med Azure infrastructure HA.

SAP Hög tillgänglighet i Azure har vissa skillnader jämfört med SAP hög tillgänglighet i en lokal fysisk eller virtuell miljö. I följande dokument från SAP beskrivs standardkonfigurationer för hög tillgänglighet <https://scn.sap.com/docs/DOC-44415>för SAP i virtualiserade miljöer i Windows: . Det finns ingen sapinst-integrerad SAP-HA-konfiguration för Linux som det finns för Windows. När det gäller SAP HA lokalt för <https://scn.sap.com/docs/DOC-8541>Linux hitta mer information här: .

### <a name="azure-infrastructure-high-availability"></a>Hög tillgänglighet för Azure Infrastructure

Det finns för närvarande ett SLA med en virtuell dator på 99,9 %. Om du vill veta hur tillgängligheten för en enda virtuell dator kan se ut kan <https://azure.microsoft.com/support/legal/sla/>du skapa produkten av de olika tillgängliga Azure SLA:erna: .

Beräkningsgrunden är 30 dagar i månaden, eller 43200 minuter. Därför motsvarar 0,05% driftstopp till 21,6 minuter. Som vanligt kommer tillgängligheten för de olika tjänsterna att multipliceras på följande sätt:

(Tillgänglighetstjänst #1/100) * (tillgänglighetsservice #2/100) * (tillgänglighetsservice #3/100) 

Som:

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 eller en total tillgänglighet på 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuell dator (VM) hög tillgänglighet

Det finns två typer av Azure-plattformshändelser som kan påverka tillgängligheten för dina virtuella datorer: planerat underhåll och oplanerat underhåll.

* Planerat underhåll är periodiska uppdateringar som Microsoft utför i syfte att förbättra tillförlitligheten, prestandan och säkerheten för den plattformsinfrastruktur som dina virtuella datorer körs i.
* Oplanerat underhåll utförs när det uppstått något fel på den underliggande maskinvaran eller fysiska infrastrukturen. Det kan vara lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När ett sådant fel upptäcks migrerar Azure-plattformen automatiskt din virtuella dator från den felaktiga fysiska servern som är värd för din virtuella dator till en felfri fysisk server. Den här typen av händelser kan också göra att den virtuella datorn startas om, men det är ovanligt.

Mer information finns i den här dokumentationen:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundans för Azure-lagring

Data i ditt Microsoft Azure Storage-konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, och uppfyller Azure Storage SLA även inför tillfälliga maskinvarufel.

Eftersom Azure Storage behåller tre avbildningar av data som standard är RAID5 eller RAID1 på flera Azure-diskar inte nödvändiga.

Mer information finns i den här artikeln:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Använda omstart av virtuell azure-infrastruktur för att uppnå högre tillgänglighet för SAP-program

Om du bestämmer dig för att inte använda funktioner som WSFC (Redundanskluster) eller Pacemaker på Linux (för närvarande endast stöds för SLES 12 och högre), används Azure VM-omstart för att skydda ett SAP-system mot planerade och oplanerade driftstopp för Azure fysisk serverinfrastruktur och övergripande underliggande Azure-plattform.

> [!NOTE]
> Det är viktigt att nämna att Azure VM-omstart i första hand skyddar virtuella datorer och INTE-program. Vm-omstart erbjuder inte hög tillgänglighet för SAP-program, men det erbjuder en viss nivå av infrastrukturtillgänglighet och därför indirekt högre tillgänglighet för SAP-system. Det finns inte heller något serviceavtal för den tid det tar att starta om en virtuell dator efter ett planerat eller oplanerat värd avbrott. Därför är den här metoden med hög tillgänglighet inte lämplig för kritiska komponenter i ett SAP-system som (A)SCS eller DBMS.
>
>

Ett annat viktigt infrastrukturelement för hög tillgänglighet är lagring. Azure Storage SLA är till exempel 99,9 % tillgänglighet. Om man distribuerar alla virtuella datorer med sina diskar till ett enda Azure Storage-konto, kommer potentiell Azure Storage otillgänglighet att orsaka otillgänglighet för alla virtuella datorer som är placerade i det Azure Storage-kontot, och även alla SAP-komponenter som körs inuti dessa virtuella datorer.  

I stället för att placera alla virtuella datorer i ett enda Azure Storage-konto kan du också använda dedikerade lagringskonton för varje virtuell dator och på så sätt öka den totala virtuella datorn och SAP-programtillgängligheten med hjälp av flera oberoende Azure Storage-konton.

Azure Managed Disks placeras automatiskt i feldomänen för den virtuella datorn som de är kopplade till. Om du placerar två virtuella datorer i en tillgänglighetsuppsättning och använder hanterade diskar, kommer plattformen att ta hand om distributionen av hanterade diskar till olika feldomäner. Om du planerar att använda Premium Storage rekommenderar vi starkt att du även använder Hantera diskar.

En exempelarkitektur för ett SAP NetWeaver-system som använder Azure Infrastructure HA och lagringskonton kan se ut så här:

![Använda Azure-infrastruktur HA för att uppnå SAP-program högre tillgänglighet][planning-guide-figure-2900]

En exempelarkitektur för ett SAP NetWeaver-system som använder Azure Infrastructure HA och Managed Disks kan se ut så här:

![Använda Azure-infrastruktur HA för att uppnå SAP-program högre tillgänglighet][planning-guide-figure-2901]

För kritiska SAP-komponenter har vi uppnått följande hittills:

* Hög tillgänglighet för SAP-programservrar (AS)

  SAP-programserverinstanser är redundanta komponenter. Varje SAP AS-instans distribueras på sin egen virtuella dator, som körs i en annan Azure-fel- och uppgraderingsdomän (se kapitel [Feldomäner][planning-guide-3.2.1] och [uppgraderingsdomäner][planning-guide-3.2.2]). Detta säkerställs med hjälp av Azure-tillgänglighetsuppsättningar (se kapitlet [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3]). Potentiell planerad eller oplanerad otillgänglighet för en Azure-fel- eller uppgraderingsdomän kommer att orsaka otillgänglighet för ett begränsat antal virtuella datorer med sina SAP AS-instanser.

  Varje SAP AS-instans placeras i sitt eget Azure Storage-konto - potentiell otillgänglighet för ett Azure Storage-konto kommer att orsaka otillgänglighet för endast en virtuell dator med sin SAP AS-instans. Tänk dock på att det finns en gräns för Azure Storage-konton inom en Azure-prenumeration. Om du vill säkerställa automatisk start av (A)SCS-instans efter omstarten av den virtuella datorn kontrollerar du att parametern Autostart i (A)SCS-instansens startprofil som beskrivs i kapitlet [Använda Autostart för SAP-instanser][planning-guide-11.5].
  Läs även kapitlet [Hög tillgänglighet för SAP-programservrar][planning-guide-11.4.1] för mer information.

  Även om du använder hanterade diskar lagras dessa diskar också i ett Azure Storage-konto och kan inte vara tillgängliga i händelse av ett lagringsavbrott.

* *Högre* Tillgänglighet för SAP (A)SCS-instans

  Här använder vi Azure VM Restart för att skydda den virtuella datorn med installerad SAP (A)SCS-instans. Vid planerade eller oplanerade driftstopp för Azure severs startas virtuella datorer om på en annan tillgänglig server. Som tidigare nämnts skyddar Azure VM-omstart främst virtuella datorer och INTE-program, i det här fallet (A)SCS-instansen. Genom omstart av den virtuella datorn når vi indirekt högre tillgänglighet för SAP (A)SCS-instans. Om du vill försäkra automatisk start av (A)SCS-instans efter omstarten av den virtuella datorn kontrollerar du att parametern Autostart i (A)SCS-instansens startprofil som beskrivs i kapitlet [Använda Autostart för SAP-instanser][planning-guide-11.5]. Det innebär att (A)SCS-instansen som en enda felpunkt (SPOF) som körs i en enda virtuell dator kommer att vara den avgörande faktorn för tillgängligheten för hela SAP-landskapet.

* *Högre* Tillgänglighet för DBMS-server

  Här, i likhet med SAP (A) SCS-instans användningsfallet, använder vi Azure VM-omstart för att skydda den virtuella datorn med installerad DBMS-programvara, och vi uppnår högre tillgänglighet av DBMS-programvara via omstart av virtuella datorer.
  DBMS körs i en enda virtuell dator är också en SPOF, och det är den avgörande faktorn för tillgängligheten för hela SAP landskapet.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP-program hög tillgänglighet på Azure IaaS

För att uppnå full SAP-system hög tillgänglighet, måste vi skydda alla kritiska SAP-systemkomponenter, till exempel redundanta SAP-programservrar, och unika komponenter (till exempel Single Point of Failure) som SAP (A)SCS-instans och DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hög tillgänglighet för SAP-programservrar

För SAP-programservrar/dialoginstanser är det inte nödvändigt att tänka på en specifik lösning med hög tillgänglighet. Hög tillgänglighet uppnås genom redundans och därmed ha tillräckligt av dem i olika virtuella datorer. De bör alla placeras i samma Azure-tillgänglighet inställd för att undvika att de virtuella datorerna kan uppdateras samtidigt under planerade underhållsavstämning. Den grundläggande funktionen, som bygger på olika uppgraderings- och feldomäner i en Azure Scale Unit, introducerades redan i kapitel [Uppgraderingsdomäner][planning-guide-3.2.2]. Azure-tillgänglighetsuppsättningar presenterades i kapitel [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3] för det här dokumentet.

Det finns inget oändligt antal fel- och uppgraderingsdomäner som kan användas av en Azure-tillgänglighetsuppsättning i en Azure Scale Unit. Det innebär att om ett antal virtuella datorer hamnar i en tillgänglighetsuppsättning, förr eller senare hamnar mer än en virtuell dator i samma fel- eller uppgraderingsdomän.

Distribuera några SAP-programserver instanser i sina dedikerade virtuella datorer och förutsatt att vi fick fem uppgradera domäner, framträder följande bild i slutet. Det faktiska maxantalet fel- och uppdateringsdomäner i en tillgänglighetsuppsättning kan ändras i framtiden:

![HA för SAP-programservrar i Azure][planning-guide-figure-3000]

Mer information finns i den här dokumentationen:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hög tillgänglighet för SAP Central Services på Azure

För arkitektur med hög tillgänglighet för SAP Central Services på Azure kontrollerar du artikeln [Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) som inmatningsinformation. Artikeln pekar på mer detaljerade beskrivningar för de särskilda operativsystemen.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hög tillgänglighet för SAP-databasinstansen

Den typiska SAP DBMS HA-installationen baseras på två DBMS-virtuella datorer där DBMS-funktioner med hög tillgänglighet används för att replikera data från den aktiva DBMS-instansen till den andra virtuella datorn till en passiv DBMS-instans.

Funktioner för hög tillgänglighet och haveriberedskap för DBMS i allmänhet samt specifika DBMS beskrivs i [DBMS Deployment Guide][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Fullständig hög tillgänglighet för det kompletta SAP-systemet

Här är två exempel på en komplett SAP NetWeaver HA-arkitektur i Azure – en för Windows och en för Linux.

Endast ohanterade diskar: Begreppen som förklaras nedan kan behöva komprometteras lite när du distribuerar många SAP-system och antalet virtuella datorer som distribueras överskrider den maximala gränsen för lagringskonton per prenumeration. I sådana fall måste virtuella hårddiskar för virtuella datorer kombineras inom ett lagringskonto. Vanligtvis skulle du göra det genom att kombinera virtuella hårddiskar av SAP-program lager virtuella datorer i olika SAP-system.  Vi kombinerade också olika virtuella hårddiskar av olika DBMS-virtuella datorer med olika SAP-system i ett Azure Storage-konto. Därmed hålla IOPS-gränserna för Azure<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>Storage-konton i åtanke ( )


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA i Windows

![HA-arkitektur för SAP NetWeaver-program med SQL Server i Azure IaaS][planning-guide-figure-3200]

Följande Azure-konstruktioner används för SAP NetWeaver-systemet för att minimera påverkan av infrastrukturproblem och värdkorrigering:

* Hela systemet distribueras på Azure (obligatoriskt - DBMS-lager, (A)SCS-instans och fullständigt programlager måste köras på samma plats).
* Det kompletta systemet körs inom en Azure-prenumeration (obligatoriskt).
* Det kompletta systemet körs inom ett Virtuellt Azure-nätverk (obligatoriskt).
* Separationen av de virtuella datorerna i ett SAP-system i tre tillgänglighetsuppsättningar är möjlig även med alla virtuella datorer som tillhör samma virtuella nätverk.
* Varje lager (till exempel DBMS, ASCS, Application Servers) måste använda en dedikerad tillgänglighetsuppsättning.
* Alla virtuella datorer som kör DBMS-instanser av ett SAP-system har en tillgänglighetsuppsättning. Vi antar att det finns mer än en virtuell dator som kör DBMS-instanser per system eftersom inbyggda DBMS-funktioner med hög tillgänglighet används, till exempel SQL Server AlwaysOn eller Oracle Data Guard.
* Alla virtuella datorer som kör DBMS-instanser använder sitt eget lagringskonto. DBMS-data och loggfiler replikeras från ett lagringskonto till ett annat lagringskonto med dbms funktioner med hög tillgänglighet som synkroniserar data. Om ett lagringskonto inte kan utnyttjas kan du inte ha någon SQL Windows-klusternod, men inte hela SQL Server-tjänsten.
* Alla virtuella datorer som kör (A)SCS-instans av ett SAP-system finns i en tillgänglighetsuppsättning. Ett WSFC -kluster (Windows Server Redundans) konfigureras inuti dessa virtuella datorer för att skydda (A)SCS-instansen.
* Alla virtuella datorer som kör (A)SCS-instanser använder sitt eget lagringskonto. A. SCS-instansfiler och den globala SAP-mappen replikeras från ett lagringskonto till ett annat lagringskonto med SIOS DataKeeper-replikering. Om ett lagringskonto inte är tillgängliga kan det leda till att en (A)SCS Windows-klusternod inte är tillgängliga, men inte hela (A)SCS-tjänsten.
* Alla virtuella datorer som representerar SAP-programserverlagret är i en tredje tillgänglighetsuppsättning.
* Alla virtuella datorer som kör SAP-programservrar använder sitt eget lagringskonto. Om det inte finns någon nytta av ett lagringskonto kan det inte finnas någon SOM HELST för en SAP-programserver, där andra SAP-programservrar fortsätter att köras.

Följande bild illustrerade samma liggande med hanterade diskar.

![HA-arkitektur för SAP NetWeaver-program med SQL Server i Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA på Linux

Arkitekturen för SAP HA på Linux på Azure är i stort sett densamma som för Windows som beskrivs ovan. Se SAP Note [1928533] för en lista över lösningar med hög tillgänglighet som stöds.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Använda Autostart för SAP-instanser

SAP erbjöd funktionen för att starta SAP-instanser direkt efter starten av operativsystemet i den virtuella datorn. De exakta stegen dokumenterades i SAP Knowledge Base artikel [1909114]. SAP rekommenderar dock inte att du använder inställningen längre eftersom det inte finns någon kontroll i ordningen för instans omstarter, förutsatt att mer än en virtuell dator påverkades eller flera instanser kördes per virtuell dator. Om man antar ett typiskt Azure-scenario med en SAP-programserverinstans i en virtuell dator och att en enda virtuell dator så småningom startas om, är Autostart inte kritiskt och kan aktiveras genom att lägga till den här parametern:

    Autostart = 1

In i början profilera av SAPEN ABAP och/eller Java förekomst.

> [!NOTE]
> Parametern Autostart kan också ha vissa nedgångar. Mer i detalj utlöser parametern början av en SAP ABAP- eller Java-instans när instansens relaterade Windows/Linux-tjänst startas. Det är verkligen fallet när operativsystemet startar upp. Omstarter av SAP-tjänster är dock också en vanlig sak för SAP Software Lifecycle Management-funktioner som SUMMA eller andra uppdateringar eller uppgraderingar. Dessa funktioner förväntar sig inte att en instans ska startas om automatiskt alls. Därför bör parametern Autostart inaktiveras innan sådana aktiviteter körs. Parametern Autostart bör inte heller användas för SAP-instanser som är klustrade, till exempel ASCS/SCS/CI.
>
>

Se ytterligare information om automatisk start för SAP-instanser här:

* [Starta/stoppa SAP tillsammans med unix-serverns start/stopp](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starta och stoppa SAP NetWeaver-hanteringsagenter](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Aktivera automatisk start av HANA-databasen](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Större 3-tier SAP-system
Hög tillgänglighet aspekter av 3-Tier SAP-konfigurationer har diskuterats i tidigare avsnitt redan. Men hur är det med system där DBMS-serverkraven är för stora för att ha den i Azure, men SAP-programlagret kan distribueras till Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Plats för SAP-konfigurationer på 3 nivåer
Det stöds inte att dela upp själva programnivån eller program- och DBMS-nivån mellan lokala och Azure. Ett SAP-system distribueras antingen helt lokalt ELLER i Azure. Det stöds inte heller att vissa programservrar körs lokalt och några andra i Azure. Det är utgångspunkten för diskussionen. Vi stöder inte heller att DBMS-komponenterna i ett SAP-system och SAP-programserverlagret distribueras i två olika Azure-regioner. Till exempel DBMS i västra USA och SAP-programlager i centrala USA. Orsaken till att inte stödja sådana konfigurationer är latenskänsligheten för SAP NetWeaver-arkitekturen.

Under förra året utvecklade dock datacenterpartner samarbetsplatser till Azure-regioner. Dessa samplatser ligger ofta i närheten av de fysiska Azure-datacenter inom en Azure-region. Det korta avståndet och anslutningen av tillgångar i samlokaliseringen via ExpressRoute till Azure kan resultera i en svarstid som är mindre än 2 ms. I sådana fall är det möjligt att hitta DBMS-lagret (inklusive lagring SAN/NAS) på en sådan samlokalisering och SAP-programlagret i Azure. [Stora HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline säkerhetskopiering av SAP-system
Beroende på den SAP-konfiguration som valts (2-tier eller 3-tier) kan det finnas ett behov av att säkerhetskopiera. Innehållet i den virtuella datorn själv plus att ha en säkerhetskopia av databasen. Dbms-relaterade säkerhetskopior förväntas göras med databasmetoder. En detaljerad beskrivning av de olika databaserna finns i [DBMS Guide][dbms-guide]. Å andra sidan kan SAP-data säkerhetskopieras offline (inklusive databasinnehållet också) enligt beskrivningen i det här avsnittet eller online enligt beskrivningen i nästa avsnitt.

Offline säkerhetskopiering skulle i princip kräva en avstängning av den virtuella datorn via Azure-portalen och en kopia av basen VM-disk plus alla anslutna diskar till den virtuella datorn. Detta skulle bevara en tidsavbildning av den virtuella datorn och dess associerade disk. Vi rekommenderar att du kopierar säkerhetskopiorna till ett annat Azure Storage-konto. Därför skulle proceduren som beskrivs i kapitlet [Kopiera diskar mellan Azure Storage-konton][planning-guide-5.4.2] i det här dokumentet gälla.
Förutom avstängningen med Azure-portalen kan man också göra det via Powershell eller CLI enligt beskrivningen här:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

En återställning av det tillståndet skulle bestå av att ta bort den grundläggande virtuella datorn samt de ursprungliga diskarna för den virtuella basdatorn och monterade diskar, kopiera tillbaka de sparade diskarna till det ursprungliga lagringskontot eller resursgruppen för hanterade diskar och sedan distribuera om systemet.
Den här artikeln visar ett exempel på skript den här processen i PowerShell:<http://www.westerndevs.com/azure-snapshots/>

Se till att installera en ny SAP-licens eftersom återställning av en vm-säkerhetskopia enligt beskrivningen ovan skapar en ny maskinvarunyckel.

### <a name="online-backup-of-an-sap-system"></a>Online backup av ett SAP-system

Säkerhetskopiering av DBMS utförs med DBMS-specifika metoder som beskrivs i [DBMS Guide][dbms-guide].

Andra virtuella datorer i SAP-systemet kan säkerhetskopieras med hjälp av Azure Virtual Machine Backup-funktioner. Azure Virtual Machine Backup är en standardmetod för att säkerhetskopiera en komplett virtuell dator i Azure. Azure Backup lagrar säkerhetskopiorna i Azure och tillåter återställning av en virtuell dator igen.

> [!NOTE]
> Från och med december 2015 med VM Backup inte hålla den unika VM-ID som används för SAP-licensiering. Det innebär att en återställning från en vm-säkerhetskopia kräver installation av en ny SAP-licensnyckel eftersom den återställda virtuella datorn anses vara en ny virtuell dator och inte en ersättning av den tidigare som sparades.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretiskt kan virtuella datorer som kör databaser säkerhetskopieras på ett konsekvent sätt samt om DBMS-systemet stöder Windows VSS (Volume Shadow Copy Service) <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>som till exempel SQL Server gör.
> Tänk dock på att det inte är möjligt att baseras på Azure VM-säkerhetskopior för point-in-time-återställningar av databaser. Därför är rekommendationen att utföra säkerhetskopior av databaser med DBMS-funktioner i stället för att förlita sig på Azure VM Backup.
>
> Så här bekantar du dig <https://docs.microsoft.com/azure/backup/backup-azure-vms>med Azure Virtual Machine Backup: .
>
> Andra möjligheter är att använda en kombination av Microsoft Data Protection Manager som är installerad i en Azure VM och Azure Backup för säkerhetskopiering/återställning av databaser. Mer information hittar du <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>här: .  
>
> ![Linux][Logo_Linux] Linux
>
> Det finns ingen motsvarighet till Windows VSS i Linux. Därför är endast filkonsekventa säkerhetskopior möjliga men inte programkonsekventa säkerhetskopior. SAP DBMS-säkerhetskopian bör göras med DBMS-funktionen. Filsystemet som innehåller SAP-relaterade data kan sparas, till exempel med tjära som beskrivs här:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure som DR-plats för produktions-SAP-landskap

Sedan mitten av 2014 har tillägg till olika komponenter runt Hyper-V, System Center och Azure aktiverat användningen av Azure som DR-plats för virtuella datorer som körs lokalt baserat på Hyper-V.

En blogg som beskriver hur du distribuerar <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>den här lösningen dokumenteras här: .

## <a name="summary"></a>Sammanfattning

De viktigaste punkterna i hög tillgänglighet för SAP-system i Azure är:

* Vid denna tidpunkt kan SAP enda felpunkt inte säkras exakt på samma sätt som det kan göras i lokala distributioner. Anledningen är att klustr för delad disk ännu inte kan byggas i Azure utan användning av programvara från tredje part.
* För DBMS-lagret måste du använda DBMS-funktioner som inte är beroende av delad diskklusterteknik. Detaljer dokumenteras i [DBMS Guide][dbms-guide].
* För att minimera effekten av problem inom feldomäner i Azure-infrastrukturen eller värdunderhållet bör du använda Azure-tillgänglighetsuppsättningar:
  * Vi rekommenderar att du har en tillgänglighet inställd för SAP-programlagret.
  * Vi rekommenderar att du har en separat tillgänglighet inställd för SAP DBMS-lagret.
  * Det rekommenderas INTE att tillämpa samma tillgänglighetsuppsättning för virtuella datorer i olika SAP-system.
  * Vi rekommenderar att du använder Premium-hanterade diskar.
* För säkerhetskopiering av SAP DBMS-lagret, kontrollera [DBMS-guiden][dbms-guide].
* Säkerhetskopiering av SAP Dialog-instanser är föga meningsfullt eftersom det oftast är snabbare att distribuera om enkla dialoginstanser.
* Säkerhetskopiera den virtuella datorn, som innehåller den globala katalogen för SAP-systemet och med det alla profiler i de olika instanserna, är vettigt och bör utföras med Windows Backup eller till exempel tjära på Linux. Eftersom det finns skillnader mellan Windows Server 2008 (R2) och Windows Server 2012 (R2), vilket gör det enklare att säkerhetskopiera med de nyare Windows Server-versionerna, rekommenderar vi att du kör Windows Server 2012 (R2) som Windows gästoperativsystem.

## <a name="next-steps"></a>Nästa steg
Läs artiklarna:

- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operationer)
