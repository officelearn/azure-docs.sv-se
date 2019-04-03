---
title: SAP MaxDB och liveCache innehållsserver distribution på Azure Virtual Machines | Microsoft Docs
description: SAP MaxDB och liveCache Content Server-distribution i Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83319118c778d89749b1eb5d5fd792a5200c19c5
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849848"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB, liveCache och innehållsserver distribution på Azure Virtual Machines

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




Det här dokumentet omfattar flera olika områden att tänka på när du distribuerar MaxDB och liveCache Content Server i Azure IaaS. Som ett villkor för det här dokumentet, bör du ha läsa dokumentet [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md) samt de andra guiderna i den [SAP-arbetsbelastningar på Azure-dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Informationen för för SAP MaxDB distributioner på Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Stöd för SAP-MaxDB Version på Azure
SAP har för närvarande stöd för SAP MaxDB version 7,9 eller senare för användning med SAP NetWeaver-baserade produkter i Azure. Alla uppdateringar för SAP MaxDB eller JDBC och ODBC-drivrutiner som ska användas med SAP NetWeaver-baserade produkter sker enbart via SAP Service Marketplace på <https://support.sap.com/swdc>.
Allmän information om hur du kör SAP NetWeaver på SAP MaxDB finns på <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP MaxDB DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP MaxDB DBMS på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows, vilket är Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Tillgängliga SAP MaxDB dokumentationen för MaxDB
Du hittar den uppdaterade listan med SAP MaxDB dokumentation i följande SAP-kommentar [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP MaxDB konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Lagringskonfiguration
Metodtips för Azure storage för SAP MaxDB följer du allmänna rekommendationer som nämns i kapitlet [lagringsstrukturen för en virtuell dator för RDBMS distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Liksom andra databaser har SAP MaxDB också data och loggfiler. I SAP MaxDB terminologi är dock korrekt termen ”volym” (inte ”fil”). Det finns till exempel SAP MaxDB datavolymer och loggvolymerna. Blanda inte ihop dem med OS-diskvolymer. 
> 
> 

Kort sagt behöver du:

* Om du använder Azure Storage-konton, anger du Azure storage-kontot som innehåller SAP MaxDB data och loggvolymer (data- och loggfiler-filer) till **lokalt Redundant lagring (LRS)** som angetts på [överväganden för virtuella Azure- Datorer DBMS-distribution för SAP-arbetsbelastningar](dbms_guide_general.md).
* Separata i/o-sökväg för SAP MaxDB datavolymer (datafiler) från den i/o-sökvägen för loggvolymerna (loggfiler). Det innebär att SAP MaxDB datavolymer (datafiler) måste installeras på en logisk enhet och SAP MaxDB loggvolymerna (loggfiler) måste installeras på en annan logisk enhet.
* Ange rätt cachelagringstyp för varje disk, beroende på om du använder för SAP MaxDB data- eller loggfilen volymer (data- och loggfiler-filer) och om du använder Azure Standard- eller Azure Premium Storage, enligt beskrivningen i [överväganden för DBMS i Azure virtuella datorer distribution för SAP-arbetsbelastningar](dbms_guide_general.md).
* Så länge som den aktuella kvoten för IOPS per disk uppfyller kraven, är det möjligt att lagra alla datavolymer på en enda monterade disken och även lagra alla loggvolymerna för databasen på en annan enkel monterade disken.
* Om det krävs mer IOPS och/eller blanksteg, rekommenderas att använda Microsoft fönstret lagringspooler (endast tillgängligt i Microsoft Windows Server 2012 och senare) för att skapa en stor logisk enhet över flera monterade diskar. Mer information finns även [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). Den här metoden förenklar omkostnader för administration för att hantera hur mycket diskutrymme och på så sätt undviker arbetet med att manuellt distribuera filer över flera monterade diskar.
* Vi rekommenderar starkt att använda Azure Premium Storage för MaxDB distributioner. 

![För referenskonfiguration av virtuell Azure IaaS-dator för SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Säkerhetskopiering och återställning
När du distribuerar SAP MaxDB till Azure måste du granska dina metoder för säkerhetskopiering. Även om systemet inte är en produktiv system måste SAP-databaser som hanteras av SAP MaxDB säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga för att skydda datorn mot storage problem och viktigare operativa eller administrativa fel. Den främsta orsaken för att bibehålla en korrekt säkerhetskopiering och återställning plan är så att du kan kompensera för logiska eller manuella fel genom att tillhandahålla återställningsfunktioner för point-in-time. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt eller att använda säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som för lokala system, så du kan använda standard SAP MaxDB säkerhetskopiering/återställning verktygen som beskrivs i en av de SAP MaxDB dokumentation dokument som anges i SAP-kommentar [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestandaöverväganden för säkerhetskopiering och återställning
Prestanda för säkerhetskopiering och återställning är beroende på hur många volymer kan läsas parallellt, och genomströmning på volymerna som bare metal-distributioner. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen enheterna, lägre övergripande läsgenomströmning
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till längst ned dataflödet

Om du vill öka antalet mål att skriva till finns det två alternativ som du kan använda, eventuellt i kombination, beroende på dina behov:

* Tilldela separata volymer för säkerhetskopiering
* Strimling säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS dataflödet på volymen stripe-disk
* Att ha separata dedikerade logiska diskenheter för:
  * SAP MaxDB säkerhetskopieringsvolymerna (d.v.s. filer)
  * SAP MaxDB datavolymer (d.v.s. filer)
  * SAP MaxDB loggvolymerna (d.v.s. filer)

En volym Strimling över flera monterade diskar har diskuterats tidigare i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Annat att tänka på
Andra allmänna områden, till exempel Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller även enligt beskrivningen i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md).  för distributioner av virtuella datorer med SAP MaxDB-databasen.
Andra SAP MaxDB-specifika inställningar är transparent för virtuella Azure-datorer och beskrivs i olika dokument som anges i SAP-kommentar [767598] och i de här SAP Notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Informationen för för SAP liveCache distributioner på Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache-Versionsstöd
Minimal version av SAP liveCache stöds i Azure Virtual Machines är **SAP LC/LCAPPS 10.0 SP 25** inklusive **liveCache 7.9.08.31** och **LCA-Build 25**, som släpptes för **EhP 2 för SAP SCM 7.0** och senare versioner.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP liveCache DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP liveCache på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache riktlinjerna för SAP-installationer i virtuella Azure-datorer
#### <a name="recommended-azure-vm-types-for-livecache"></a>Rekommenderas Azure VM-typer för liveCache
Har en stor påverkan på prestanda för SAP-liveCache som SAP liveCache är ett program som utför stora beräkningar, den mängd och hastighet för RAM-minne och CPU. 

För Azure VM-typer som stöds av SAP (SAP-kommentar [1928533]), alla virtuella CPU-resurser allokeras till den virtuella datorn backas upp av dedikerade fysisk CPU-resurser i hypervisor-programmet. Ingen överetablering (och därför ingen konkurrens om processorresurser) sker.

På samma sätt för alla Virtuella Azure-instanstyper som stöds av SAP, är VM-minne 100% mappad till det fysiska minnet - överetablering (över åtagande), till exempel inte används.

Från det här perspektivet rekommenderas att använda de senaste Dv2, Dv3, Ev3 och virtuella datorer i M-serien. Valet av de olika typerna av virtuell dator beror på det minne som du behöver för liveCache och CPU-resurser som du behöver. Som med alla andra DBMS-distributioner är det lämpligt att använda Azure Premium Storage för prestanda för kritiska volymer.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Lagringskonfigurationen för liveCache i Azure
Som SAP liveCache baseras på SAP MaxDB teknik kan kan alla i Azure storage rekommendationer om metodtips nämns för SAP-MaxDB som beskrivs i detta dokument användas för SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedikerad virtuell Azure-dator för liveCache scenario
Eftersom SAP liveCache använder sig mycket dataresurser, för produktiva användning rekommenderas att distribuera på en dedikerad Azure-dator. 

![Reserverad Azure VM för liveCache för produktiva användningsfall](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Säkerhetskopiering och återställning för liveCache i Azure
säkerhetskopiering och återställning, inklusive prestandaöverväganden, beskrivs redan i de relevanta SAP MaxDB kapitel i det här dokumentet. 

#### <a name="other-considerations"></a>Annat att tänka på
Andra allmänna områden beskrivs redan i det relevanta SAP MaxDB kapitlet. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Informationen för för SAP Content Server-distributionen för Windows i Azure
SAP Content Server är en separat, server-baserade komponent för att lagra innehåll, till exempel elektroniska dokument i olika format. Innehållsservern SAP tillhandahålls av utvecklingen av teknik och ska använda cross-programmet för alla SAP-program. Den är installerad på ett separat system. Vanliga innehållet är utbildningsmaterial och dokumentation från Knowledge datalager eller tekniska ritningar från mySAP PLM dokumenthanteringssystemet. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>SAP innehållsserver Version som stöds för virtuella Azure-datorer
SAP stöder för närvarande:

* **SAP innehållsserver** med version **6.50 (och senare)**
* **SAP MaxDB version 7.9**
* **Microsoft IIS (Internet Information Server) version 8.0 (och senare)**

Vi rekommenderar starkt att använda den senaste versionen av SAP innehållsservern och den senaste versionen av **Microsoft IIS**. 

Kontrollera de senaste versionerna som stöds av SAP Content Server och Microsoft IIS i den [SAP produkten tillgänglighet matris (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Microsoft Windows och Virtuella Azure-typer som stöds för SAP Content Server
Om du vill ta reda på Windows-version som stöds för SAP Content Server på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP innehållsserver konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration-for-content-server-in-azure"></a>Lagringskonfigurationen för Content Server i Azure
Om du konfigurerar SAP Content Server för att lagra filer i SAP MaxDB databasen gäller även alla Azure storage bästa praxis rekommendation för SAP MaxDB som nämns i det här dokumentet för scenariot med SAP-innehållsserver. 

Om du konfigurerar SAP Content Server för att lagra filer i filsystemet, rekommenderas att använda en dedikerad logisk enhet. Med lagringsutrymmen för Windows kan du även öka storleken för logisk disk och IOPS, dataflöde, enligt beskrivningen i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>SAP innehållsserver plats
SAP innehållsserver måste distribueras i samma Azure-region och virtuella Azure-nätverket där SAP-system har distribuerats. Du kan bestämma om du vill distribuera SAP Content Server-komponenter på en dedikerad virtuell Azure-dator eller på samma virtuella dator där du kör SAP-system. 

![Reserverad Azure VM för SAP innehållsserver](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP cacheplats för Server
SAP Cache-Server är en ytterligare serverbaserade komponent att ge åtkomst till (cachelagrade) lokalt. SAP-Cacheservern cachelagrar dokument för en SAP-innehållsserver. Det här är att optimera nätverkstrafik om dokument som har mer än en gång hämtas från olika platser. Generellt gäller att SAP Cache-Server måste vara fysiskt nära den klient som ansluter till SAP Cache-Server. 

Här har du två alternativ:

1. **Klienten är ett serverdelsystem SAP** om en serverdel SAP-system är konfigurerad för att komma åt SAP innehållsserver, det SAP-systemet är en klient. Eftersom både SAP-system och SAP Content Server distribueras i samma Azure-region, i samma Azure-datacentret, är de fysiskt nära varandra. Det är därför behöver du inte har en dedikerad SAP-cacheserver. SAP-UI-klienter (SAP-Gränssnittet eller web browser) direktåtkomst till SAP-system och SAP-system hämtar dokument från innehållsservern SAP.
2. **Klienten är en lokal webbläsare** The SAP innehållsserver kan konfigureras för att användas direkt av webbläsaren. I det här fallet är en webbläsare som körs lokalt en klient för SAP-innehållsserver. Lokala datacenter och Azure-datacenter är placerade i olika fysiska platser (helst nära varandra). Ditt lokala datacenter är ansluten till Azure via Azure plats-till-plats-VPN eller ExpressRoute. Även om båda alternativen erbjuder säker anslutning för VPN-nätverk till Azure, erbjuder nätverk plats-till-plats-anslutning inte SLA för bandbredd och latens en nätverk mellan datacenter på plats och Azure-datacentret. För att påskynda åtkomst till dokument, kan du göra något av följande:
   1. Installera SAP cacheserver lokalt, nära lokala webbläsarens (alternativ i bilden nedan)
   2. Konfigurera Azure ExpressRoute, som erbjuder en hög hastighet och låg latens dedikerad nätverksanslutning mellan lokala datacenter och Azure-datacenter.

![Alternativet för att installera SAP Cache Server lokalt](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
Om du konfigurerar SAP innehållsservern för att lagra filer i databasen SAP MaxDB beskrivs säkerhetskopiering/återställning procedur- och prestandaöverväganden redan i SAP MaxDB kapitlen i det här dokumentet. 

Om du konfigurerar SAP innehållsservern för att lagra filer i filsystemet, är ett alternativ att köra en manuell säkerhetskopiering/återställning av hela filen-struktur där dokumenten finns. Liknar SAP MaxDB säkerhetskopiering/återställning, rekommenderar vi att du har en dedikerad volym för säkerhetskopiering ändamål. 

#### <a name="other"></a>Annat
Andra innehåll till SAP-serverspecifika inställningar är transparent för virtuella Azure-datorer och beskrivs i olika dokument och SAP Notes:

* <https://service.sap.com/contentserver> 
* SAP-kommentar [1619726]  
