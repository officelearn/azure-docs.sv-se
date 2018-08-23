---
title: Azure Virtual Machines DBMS-distribution för SAP NetWeaver | Microsoft Docs
description: Azure Virtual Machines DBMS-distribution för SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6b0ea7479910f7026974e37f8c05099453c0b26
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42059734"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Azure Virtual Machines DBMS-distribution för SAP NetWeaver
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den här guiden är en del av dokumentationen för implementering och distribution av SAP-programvara på Microsoft Azure. Innan du läser den här guiden kan du läsa den [planerings- och Implementeringsguide för][planning-guide]. Det här dokumentet beskriver distributionen av olika relationell databas Management Systems (RDBMS) och relaterade produkter i kombination med SAP på Microsoft Azure Virtual Machines (VM) med Azure-infrastruktur som en tjänst (IaaS)-funktioner.

Dokumentet kompletterar dokumentationen för Installation av SAP och SAP Notes som motsvarar de viktigaste resurserna för installationer och distributioner av SAP-program på den angivna plattformar.

## <a name="general-considerations"></a>Allmänna överväganden
I det här kapitlet introduceras överväganden för att köra SAP-relaterade DBMS system i virtuella Azure-datorer. Det finns några referenser till specifika DBMS system i det här kapitlet. I stället hanteras specifika DBMS system i det här dokumentet, efter det här kapitlet.

### <a name="definitions-upfront"></a>Definitioner förskott
Vi använder följande termer för hela dokumentet:

* IaaS: Infrastruktur som en tjänst.
* PaaS: Plattform som en tjänst.
* SaaS: Programvara som en tjänst.
* SAP-komponent: ett enskilt SAP program, till exempel ECC, BW, lösningen Manager eller EP.  SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller ett icke-NetWeaver-baserade program, till exempel företag objekt.
* SAP-miljön: en eller flera SAP-komponenter logiskt grupperade för att utföra en företagsfunktion, till exempel utveckling, QAS, utbildning, DR eller produktion.
* SAP-landskap: Detta refererar till hela SAP-tillgångar i en kunds IT-miljön. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
* SAP-System: Kombinationen av DBMS lager och programnivån av, till exempel en utvecklingssystem för SAP ERP, SAP BW testsystem, SAP CRM produktionssystemet osv. I Azure-distributioner, är det inte stöd för att dela upp de här två lager mellan lokala och Azure. Det innebär att ett SAP-system är antingen distribuerat lokalt eller det distribueras i Azure. Du kan dock distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure men SAP CRM produktionsprogrammen system på plats.
* Endast molnbaserad distribution: en distribution där Azure-prenumerationen inte är ansluten via en plats-till-plats eller ExpressRoute-anslutning i den lokala nätverksinfrastruktur. I vanliga Azure-dokumentation dessa typer av distributioner beskrivs också som ”molnbaserade” distributioner. Virtuella datorer som distribueras med den här metoden kan nås via Internet och offentliga Internet-slutpunkter som är tilldelade till de virtuella datorerna i Azure. Lokalt Active Directory (AD) och DNS inte har utökats till Azure i dessa typer av distributioner. De virtuella datorerna är därför inte en del av den lokala Active Directory. Obs: Molnbaserade distributioner i det här dokumentet har definierats som fullständig SAP-landskap som kör exklusivt i Azure utan utökning av Active Directory eller namnmatchning från en lokal plats till offentliga molnet. Endast molnbaserad konfigurationer stöds inte för produktion SAP-system eller konfigurationer där SAP STM eller andra lokala resurser behöver användas mellan SAP-system i Azure och resurser som finns på plats.
* Mellan olika platser: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, flera platser eller ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. Orsaken till att anslutningen är att utöka lokala domäner, en lokal Active Directory och lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar för prenumerationen. Med det här tillägget, kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare på den lokala domänen har åtkomst till servrarna och kan köra services på de virtuella datorerna (till exempel DBMS tjänster). Kommunikation och namnmatchning mellan virtuella datorer distribueras på plats och virtuella datorer som distribueras i Azure är möjligt. Vi räknar med att detta är det vanligaste scenariot för att distribuera SAP-tillgångar i Azure. Mer information finns i [i den här artikeln] [ vpn-gateway-cross-premises-options] och [i den här artikeln][vpn-gateway-site-to-site-create].

> [!NOTE]
> Mellan lokala distributioner av SAP-system om Azure virtuella datorer som kör SAP-system är medlemmar i en lokal domän har stöd för SAP-system för produktion. Mellan lokala konfigurationer som stöds för att distribuera delar eller slutföra SAP-landskap i Azure. Även köra fullständig SAP-landskap i Azure kräver att dessa virtuella datorer är en del av den lokala domänen och ANNONSER. I tidigare versioner av dokumentationen vi talade om Hybrid-IT-scenarier där termen *Hybrid* grundas på typsystemet i det faktum att det finns en anslutning mellan olika platser mellan lokala och Azure. I det här fallet *Hybrid* innebär också att de virtuella datorerna i Azure är en del av den lokala Active Directory.
> 
> 

Vissa Microsoft-dokumentationen beskriver mellan lokala scenarier lite annorlunda särskilt för DBMS HA konfigurationer. När det gäller SAP-relaterade-dokument fördelas mellan lokala scenariot kokar ned till en anslutning för plats-till-plats eller privat (ExpressRoute) och det faktum att SAP liggande mellan lokala och Azure.

### <a name="resources"></a>Resurser
Följande guider är tillgängliga för distribution av SAP på Azure:

* [Azure virtuella datorer, planering och implementering av SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver (det här dokumentet)][dbms-guide]

Följande SAP-information är relaterade till SAP på Azure:

| Nummer | Titel |
| --- | --- |
| [1928533] |SAP-program i Azure: produkter och Azure VM-typer |
| [2015553] |SAP på Microsoft Azure: supportkrav |
| [1999351] |Felsökning av utökad Azure övervakning för SAP |
| [2178632] |Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering på Windows: förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: förbättrad övervakning |
| [2039619] |SAP-program på Microsoft Azure med Oracle-databasen: stöd för produkter och versioner |
| [2233094] |DB6: SAP-program i Azure med IBM DB2 för Linux, UNIX- och Windows - ytterligare Information |
| [2243692] |Linux på Microsoft Azure (IaaS) VM: SAP problem |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP-Installation och uppgradering |
| [1597355] |Växlingsutrymme rekommendation för Linux |
| [2171857] |Oracle Database 12c - stöd för filer system på Linux |
| [1114181] |Oracle Database 11g - stöd för filer system på Linux |


Läs även den [Tillståndsändringsavisering Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP Notes för Linux.

Du bör ha kunskaper om arkitektur för Microsoft Azure och hur Microsoft Azure Virtual Machines distribueras och hanteras. Du hittar mer information <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Vi kan **inte** diskutera Microsoft Azure-plattformen som en tjänst (PaaS)-erbjudanden på Microsoft Azure-plattformen. Det här dokumentet handlar om att köra ett databashanteringssystem (DBMS) i Microsoft Azure Virtual Machines (IaaS) som du skulle köra DBMS i din lokala miljö. Database-funktioner och funktionalitet mellan dessa två erbjudanden skiljer sig väldigt mycket och ska inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Eftersom vi diskuterar IaaS är i allmänhet Windows, Linux och DBMS installationen och konfigurationen i stort sett samma som valfri virtuell dator eller bare metal datorn installerar lokalt. Det finns dock vissa arkitektur och system management implementering beslut, som skiljer sig när du använder IaaS. Syftet med det här dokumentet är att förklara den specifika arkitekturen och system management skillnader som du måste förberedas för när du använder IaaS.

I allmänhet är de övergripande områdena av skillnaden som beskrivs i det här dokumentet:

* Planera rätt VM/layouten på SAP-datorer så att du har rätt data filen layout och kan uppnå tillräckligt med IOPS för din arbetsbelastning.
* Nätverksöverväganden när du använder IaaS.
* Specifika databasfunktioner att använda för att optimera layouten för databasen.
* Överväganden för säkerhetskopiering och återställning i IaaS.
* Använder olika typer av avbildningar för distribution.
* Hög tillgänglighet i Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktur för en RDBMS-distribution
För att kunna följa det här kapitlet, det är nödvändigt att förstå vad som angavs i [detta] [ deployment-guide-3] kapitel i den [Distributionsguide][deployment-guide]. Information om olika VM-serien och deras skillnader och skillnaderna för Azure Standard och Premium-lagring ska tolkas och kända innan du läser det här kapitlet.

Fram till mars 2015 diskar som innehåller ett operativsystem som var begränsade till 127 GB i storlek. Den här begränsningen fick tillbaka i mars 2015 (Mer information finns <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Därifrån på diskar som innehåller operativsystemet kan ha samma storlek som alla andra diskar. Vi föredrar emellertid fortfarande en struktur för distribution där operativsystemet, DBMS och eventuell SAP-binärfiler skiljer sig från databasfilerna. Därför kan vi förväntar oss SAP-system som körs i Azure virtuella datorer har de grundläggande VM (eller disk) som installeras med operativsystemet, database management system körbara filer och SAP körbara filer. DBMS data och loggfiler filerna lagras i Azure Storage (Standard eller Premium Storage) i separata diskar och ansluten som logiska diskar på den ursprungliga Azure operativsystemavbildningen VM. 

Beroende på att utnyttja Azure Standard eller Premium Storage (till exempel med hjälp av DS-serien eller GS-seriens virtuella datorer) det finns andra kvoter i Azure, som finns dokumenterade [här (Linux)] [ virtual-machines-sizes-linux] och [ här (Windows)][virtual-machines-sizes-windows]. När du planerar din disklayouten, måste du hitta en bra balans kvoter för följande objekt:

* Antalet datafiler.
* Antalet diskar som innehåller filerna.
* IOPS-kvoter för en enda disk.
* Data dataflöde per disk.
* Antal ytterligare datadiskar som är möjliga per VM-storlek.
* Det totala genomflödet som en virtuell dator kan ge.

Azure tillämpar en kvot för IOPS per datadisk. Dessa kvoter är olika för diskar som finns på Azure Standard Storage och Premium Storage. I/o-svarstiderna är också mycket olika mellan två lagringstyper av med Premium Storage levererar faktorer bättre i/o-svarstider. Var och en av de olika typerna av virtuell dator har ett begränsat antal datadiskar som du kan ansluta. En annan begränsning är att endast vissa typer av virtuella datorer kan använda Azure Premium Storage. Det innebär att beslut för en viss typ av virtuell dator inte kan bara styras av processor och minne krav, utan även av IOPS, latens och dataflödeskrav som vanligtvis skalas med antalet diskar eller vilken typ av Premium Storage-diskar. Särskilt med Premium Storage kan storleken på en disk också avgöras av antalet IOPS och dataflöde som måste uppnås genom att varje disk.

Det faktum att den totala frekvensen för IOPS, antalet diskar som monterats och storleken på den virtuella datorn är alla kopplas ihop, kan leda till en Azure-konfiguration av ett SAP-system för att skilja sig från dess lokal distribution. IOPS-gränser per LUN kan vanligtvis konfigureras i lokala distributioner. De här gränserna är fast eller som i Premium Storage beroende på typ av disk med Azure Storage. Vi kan så se kundkonfigurationer för databasservrar som använder många olika volymer för särskilda körbara filer som SAP och DBMS eller speciella volymer för tillfälliga databaser eller blanksteg i tabellen med lokala distributioner. När sådana ett lokalt system har flyttats till Azure, kan det leda till slöseri med potentiella IOPS bandbredd genom att ha lagt en disk för körbara filer eller databaser som inte utför någon eller inte merparten av IOPS. I Azure virtuella datorer rekommenderar vi därför att DBMS och SAP körbara filer installeras på OS-disken om möjligt.

Placeringen av databasfilerna och loggfilerna och vilken typ av Azure Storage används, måste definieras av IOPS, latens och krav på dataflöde. För att få tillräckligt med IOPS för transaktionsloggen, kan du tvingas att utnyttja flera diskar för transaktionsloggfilen eller använda en större Premium Storage-disk. I sådana fall skulle en skapa en programvaru-RAID (till exempel Windows Storage Pool för Windows eller MDADM och LVM (Logical Volume Manager) för Linux) med diskar som innehåller transaktionsloggen.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Enhet D:\ i en Azure VM är en icke bestående enhet som backas upp av vissa lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehållet på D:\ enheten går förlorad när den virtuella datorn startas om eftersom den är icke-beständig. Genom att ”alla ändringar”, menar vi sparade filer, kataloger som skapas, program som har installerats, osv.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuella Linux Azure-datorer montera automatiskt en enhet på /mnt/resource som är en icke bestående enhet som backas upp av lokala diskar på Azure compute-nod. Det innebär att ändringar som görs till innehåll i /mnt/resource går förlorade när den virtuella datorn startas om eftersom den är icke-beständig. Av alla ändringar menar vi filer som har sparats, kataloger som skapas, program som har installerats, osv.
> 
> 

- - -
Beroende på Azure VM-serien, de lokala diskarna på Beräkningsnoden visa olika prestanda, vilket kan kategoriseras som:

* A0-A7: Mycket begränsade prestanda. Kan inte användas för allt utöver växlingsfil för windows
* A8 – A11: Mycket bra prestandaegenskaper med vissa tiotusen IOPS och > 1GB/s genomströmning
* D-serien: Mycket bra prestandaegenskaper med vissa tiotusen IOPS och > 1GB/s genomströmning
* DS-serien: Mycket bra prestandaegenskaper med vissa tiotusen IOPS och > 1GB/s genomströmning
* G-serien: Mycket bra prestandaegenskaper med vissa tiotusen IOPS och > 1GB/s genomströmning
* GS-serien: Mycket bra prestandaegenskaper med vissa tiotusen IOPS och > 1GB/s genomströmning

Uttryck ovanför tillämpas på VM-typer som är certifierade med SAP. VM-serien med utmärkt IOPS och dataflöde är berättigad till dra nytta av vissa DBMS funktioner, till exempel tempdb eller temporär tabellutrymme.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och datadiskar
När vi skapar datadiskar via portalen eller när vi montera överförda diskar till virtuella datorer, kan vi välja om i/o-trafiken mellan den virtuella datorn och diskarna i Azure storage cachelagras. Azure Standard och Premium Storage kan du använda två olika tekniker för den här typen av cache. I båda fallen är cachen själva disk som backas upp på samma enheter som används av den temporära disken (D:\ på Windows) eller /mnt/resource på Linux för den virtuella datorn.

För Azure Standard Storage är möjliga cache-typer:

* Ingen cachelagring
* Läsa cachelagring
* Läsa och skriva cachelagring

För att få konsekventa och deterministisk prestanda, bör du ange cachelagring på Azure standardlagring för alla diskar som innehåller **DBMS-relaterade-datafiler, loggfiler och tabellen utrymme för att ”NONE”**. Cachelagring av den virtuella datorn kan förbli med standard.

För Azure Premium Storage finns följande alternativ för cachelagring:

* Ingen cachelagring
* Läsa cachelagring

Rekommendation för Azure Premium Storage är att använda **läsa cachelagring för filer** för SAP-databasen och välja **ingen cachelagring för diskarna i befintlig(a) transaktionsloggfil(er)**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Programvaru-RAID
Som redan nämnts ovan, du behöver att balansera antalet IOPS som behövs för databasfilerna över antal diskar som du kan konfigurera och högsta IOPS en Azure-dator ger per disk- eller Premium Storage-disktyp. Enklaste sättet att hantera belastningen IOPS över diskar är att skapa en programvaru-RAID över olika diskar. Placera sedan ett antal datafiler SAP DBMS på LUN högg utanför programvaru-RAID. Beroende på vilka krav som du kanske vill överväga användning av Premium Storage samt eftersom två av tre olika Premium Storage-diskar ger högre IOPS kvot än diskar baserat på Standard-lagring. Förutom den betydande bättre i/o-svarstid tillhandahålls av Azure Premium Storage. 

Detsamma gäller för transaktionsloggen av olika DBMS system. Med många av dem att lägga till fler Tlog filer hjälper inte eftersom DBMS system skriver till en av filerna på bara en gång. Om priserna för högre IOPS krävs än en enda standardlagring baserad disk kan leverera, du kan stripe över flera Standard Storage-diskar eller du kan använda en större Premium Storage-disktyp som utöver högre IOPS priserna faktorer ger också kortare svarstider för skrivning jag / OS i transaktionsloggen.

Situationer som uppstår i Azure-distributioner skulle prioriterar med hjälp av en programvaru-RAID är:

* Log/gör om transaktionsloggen kräver fler IOPS än Azure tillhandahåller för en enskild disk. Som nämnts ovan detta kan lösas genom att skapa en logisk enhet över flera diskar med hjälp av en programvaru-RAID.
* Ojämn i/o Arbetsdistribution över de olika filerna av SAP-databasen. I sådana fall kan en uppleva en datafil som nått kvoten i stället ofta. Medan andra filer inte får även nära IOPS-kvot på en enskild disk. I sådana fall är den enklaste lösningen att skapa en LUN över flera diskar med hjälp av en programvaru-RAID. 
* Du vet inte vad den exakta i/o-arbetsbelastningen per fil och bara ungefär vet vad som är den övergripande IOPS-arbetsbelastningen mot DBMS. Enklast att göra är att skapa en LUN med hjälp av en programvaru-RAID. Summan av kvoter för flera diskar bakom denna LUN bör sedan uppfyller det kända priset IOPS.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Vi rekommenderar att du använder Windows-lagringsutrymmen om du kör på Windows Server 2012 eller senare. Det är effektivare än Windows Striping av tidigare versioner av Windows. Du kan behöva skapa Windows lagringspooler och lagringsutrymmen med PowerShell-kommandon när du använder Windows Server 2012 som operativsystem. PowerShell-kommandon finns här <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Endast MDADM och LVM (Logical Volume Manager) kan användas för att skapa en programvaru-RAID på Linux. Mer information finns i följande artiklar:
> 
> * [Konfigurera programvaru-RAID på Linux] [ virtual-machines-linux-configure-raid] (för MDADM)
> * [Konfigurera LVM på en Linux-dator i Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Överväganden för att kunna utnyttja VM-serien, som kommer arbeta med Azure Premium Storage är:

* Krav för i/o-svarstider som är nära SAN/NAS-enheter leverera.
* Behovet av faktorer bättre i/o-svarstider än Azure standardlagring kan leverera.
* Högre IOPS per virtuell dator än vad som kan uppnås med flera standardlagringsdiskar mot en viss typ av virtuell dator.

Eftersom det underliggande Azure-lagringsutrymmet replikeras varje disk till minst tre lagringsnoder, enkel RAID 0 striping kan användas. Det finns inget behov av att implementera RAID5 eller RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure Storage lagrar de grundläggande virtuell dator (med OS) och diskar eller BLOB-objekt till minst tre separata lagringsnoder. När du skapar ett lagringskonto eller en hanterad disk, finns det ett urval av skydd som visas här:

![GEO-replikering aktiverat för Azure Storage-konto][dbms-guide-figure-100]

Azure Storage lokal replikering (lokalt Redundant) ger skydd mot dataförlust på grund av fel i infrastrukturen som fåtal kunder kan kosta på dig att distribuera. Det finns fyra olika alternativ med en femte som en variant av en av de första tre för att se ovan. Titta närmare på dem. skilja vi:

* **Premium lokalt Redundant lagring (LRS)**: Azure Premium Storage tillhandahåller högpresterande och låg latens diskstöd för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Det finns tre repliker av data inom samma Azure-datacenter i en Azure-region. Kopiorna är i olika fel- och uppgradera domäner (begrepp finns i [detta] [ planning-guide-3.2] kapitel i den [Planning Guide][planning-guide]). En ny replik genereras automatiskt vid en replik av data som går ut från tjänsten på grund av ett nodfel för lagring eller diskfel.
* **Lokalt Redundant lagring (LRS)**: I det här fallet finns tre repliker av data inom samma Azure-datacenter i en Azure-region. Kopiorna är i olika fel- och uppgradera domäner (begrepp finns i [detta] [ planning-guide-3.2] kapitel i den [Planning Guide][planning-guide]). En ny replik genereras automatiskt vid en replik av data som går ut från tjänsten på grund av ett nodfel för lagring eller diskfel. 
* **Geografiskt Redundant lagring (GRS)**: I det här fallet är det en asynkron replikering som en ytterligare tre repliker av data i en annan Azure-Region som ligger i de flesta fall i samma geografiska region (till exempel Europa, norra och Europa, västra ). Detta resulterar i ytterligare tre repliker, så att det finns sex repliker i summan. En variant av det här är ett tillägg där data i geo replikerade Azure-regionen kan användas för läsning (Read-Access Geo-Redundant).
* **Zonen Redundant lagring (ZRS)**: I det här fallet tre repliker av data finns kvar i samma Azure-Region. Enligt beskrivningen i [detta] [ planning-guide-3.1] kapitel i den [Planning Guide] [ planning-guide] en Azure-region kan innehålla ett antal Datacenter nära varandra. När det gäller LRS skulle replikerna fördelas över olika datacenter som gör en Azure-region.

Mer information hittar du [här][storage-redundancy].

> [!NOTE]
> För DBMS-distributioner rekommenderas inte användning av Geo-Redundant lagring
> 
> Azure Storage-Geo-replikering är asynkron. Replikering av enskilda diskarna monteras på en enskild virtuell dator är inte synkroniserade i Lås steg. Det är därför inte lämpligt att replikera DBMS-filer som fördelas på olika diskar eller distribuerats mot en programvaru-RAID baserat på flera diskar. DBMS-programmet kräver att permanent disk-lagring är exakt synkroniserade över olika LUN och underliggande diskar/axlar. DBMS programvaran använder olika mekanismer för att sekvens-i/o-skrivning aktiviteter och en DBMS rapporterar att disklagring som mål för replikeringen är skadad om dessa varierar beroende på även några millisekunder. Därför en efterfrågar en konfiguration med en-databas utsträckta över flera diskar geo-replikerade måste sådana replikering utföras med databasen innebär och funktioner. En bör inte förlita dig på Azure Storage Geo-replikering att utföra det här jobbet. 
> 
> Problemet är enklast att förklara med en exempel-system. Vi antar att du har ett SAP-system som har överförts till Azure, vilket har åtta diskar som innehåller datafiler DBMS plus en disk som innehåller transaktionsloggfilen. Var och en av de här nio diskarna har data som skrivs till dem i ett konsekvent sätt enligt för DBMS, oavsett om data skrivs till data eller transaktionen loggfiler.
> 
> För att korrekt geo-replikera data och bibehålla en konsekvent databas-avbildning i innehållet i nio diskarna skulle behöva geo-replikeras i den ordning som de i/o-åtgärderna som utfördes mot nio olika diskar. Azure Storage geo-replikering tillåter inte att deklarera beroenden mellan diskar. Det innebär att Microsoft Azure Storage geo-replikering inte veta om det faktum att innehållet i de här nio olika diskar är relaterade till varandra och att ändringarna som är konsekventa endast när replikering i ordningen som de i/o-åtgärderna inträffade för alla de nio diskarna.
> 
> Förutom risken att hög att geo-replikerade avbildningarna i scenariot inte innehåller en konsekvent databas-avbildning, är det också en prestandaförsämring som visas med geo-redundant lagring som kan kraftigt påverka prestanda. Sammanfattningsvis ska du inte använda den här typen av lagringsredundans för DBMS typ arbetsbelastningar.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mappa virtuella hårddiskar till Azure-dator Service Storage-konton
I det här kapitlet gäller bara för Azure Storage-konton. Om du planerar att använda Managed Disks, gäller begränsningar som nämns i detta kapitel inte. Mer information om Managed Disks finns i kapitlet [Managed Disks] [ dbms-guide-managed-disks] i den här guiden.

Azure Storage-kontot är inte bara en administrativ konstruktion, utan också ett ämne med begränsningar. Medan begränsningarna beror på om vi pratar om en Azure-Standard Storage-konto eller ett Azure Premium Storage-konto. Den exakta funktioner och begränsningar finns [här][storage-scalability-targets]

Så för Azure Standard-lagring är det viktigt att Observera att det finns en gräns på IOPS per lagringskonto (rad som innehåller **totala begära antalet** i [artikeln][storage-scalability-targets]). Det finns dessutom en inledande gräns på 100 Storage-konton per Azure-prenumeration (från och med juli 2015). Vi rekommenderar därför att balansera IOPS för virtuella datorer mellan flera lagringskonton när du använder Azure standardlagring. Medan en enskild virtuell dator använder vi rekommenderar en storage-konto om det är möjligt. Om vi pratar om DBMS-distributioner där varje virtuell Hårddisk som finns på Azure standardlagring det gick att nå sin kvot, bör du därför bara distribuera 30 – 40 virtuella hårddiskar per Azure-Lagringskonto som använder Azure standardlagring. Å andra sidan, om du använder Azure Premium Storage och vill lagra stor databas volymer, kanske du bra som IOPS. Men Azure Premium Storage-kontot är vägen mer restriktiva i datavolym än Azure Standard Storage-kontot. Därför kan du bara distribuera ett begränsat antal virtuella hårddiskar i en Azure Premium Storage-konto innan du nått gränsen för data-volym. Tänk på ett Azure Storage-konto som ett ”virtuellt SAN” som har begränsade funktioner i IOPS och/eller kapaciteten i slutet. Därför uppgiften är kvar, som i den lokala distributioner, definiera layouten för de virtuella hårddiskarna till de olika SAP-system via olika 'tänkt SAN-enheter, eller Azure Storage-konton.

För Azure Standard Storage rekommenderas inte att presentera lagringsutrymmen från olika lagringskonton till en enskild virtuell dator om möjligt.

När du använder DS eller GS-serien med virtuella Azure-datorer, är det möjligt att montera VHD: er från Azure Standard Storage-konton och Premium Storage-konton. Användningsfall som att skriva säkerhetskopior till standardlagring backas upp av virtuella hårddiskar och gör att DBMS data och loggfiler på Premium Storage kommer att tänka på där sådan heterogen lagring kan utnyttjas. 

Baserat på kunddistributioner och testa cirka 30-40 virtuella hårddiskar som innehåller databasfiler för data och loggfiler kan etableras på en enda Azure Standard Storage-konto med acceptabel prestanda. Såsom nämnts tidigare troligen begränsningen av ett Azure Premium Storage-konto är datakapacitet som den kan innehålla och inte IOPS.

Som med SAN-enheter lokalt kräver delning viss övervakning för att så småningom upptäcka flaskhalsar på ett Azure Storage-konto. Azure Monitoring-tillägg för SAP och Azure portal finns verktyg som kan användas för att identifiera upptagen Azure Storage-konton som kan leverera icke-optimal i/o-prestanda.  Om den här situationen har identifierats, rekommenderas att flytta upptagna virtuella datorer till en annan Azure Storage-konto. Referera till den [Distributionsguide] [ deployment-guide] för information om hur du aktiverar SAP agera värd åt övervakningsfunktionerna.

En annan artikel sammanfatta metodtips kring Azure Standard Storage och Azure Standard Storage-konton finns här <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Hanterade diskar
Hanterade diskar är en ny resurstyp i Azure Resource Manager som kan användas i stället för virtuella hårddiskar som lagras i Azure Storage-konton. Hanterade diskar anpassas automatiskt till Tillgänglighetsuppsättningen för den virtuella datorn som de är kopplade till, och därför att öka tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översiktsartikeln](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP stöder för närvarande endast Premium Managed Disks. Läs SAP-kommentar [1928533] för mer information.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Flytta distribueras DBMS virtuella datorer från Azure standardlagring till Azure Premium Storage
Vi stöter på riktigt vissa scenarier där du som kund vill flytta en distribuerad virtuell dator från Azure standardlagring till Azure Premium Storage. Om diskarna lagras i Azure Storage-konton kan är detta inte möjligt utan att fysiskt flytta data. Det finns flera sätt att uppnå målet:

* Du kan kopiera alla virtuella hårddiskar, Virtuella bashårddisken samt data-VHD: er till en ny Azure Premium Storage-konto. Ofta valde antalet VHD: er i Azure standardlagring inte på grund av det faktum att du behövs datavolym. Men du tvungen att så många virtuella hårddiskar på grund av IOPS. Nu när du flyttar till Azure Premium Storage kan du gå med sätt att uppnå samma IOPS dataflödet på färre virtuella hårddiskar. Med tanke på att i Azure standardlagring du betalar för data som används och inte nominell diskstorleken, antalet virtuella hårddiskar inte spelar någon roll när det gäller kostnader. Med Azure Premium Storage, skulle du dock betalar för nominell diskens storlek. Därför se de flesta kunderna till att antalet virtuella Azure-hårddiskar i Premium-lagring på hur många som behövs för att uppnå IOPS dataflödet behövs. Därför väljer de flesta kunder sätt på ett enkelt 1:1 kopia.
* Om du ännu inte har monterats kan montera en enda virtuell Hårddisk som kan innehålla en säkerhetskopia av databasen för din SAP-databas. Efter säkerhetskopieringen, demontera alla virtuella hårddiskar, inklusive den virtuella Hårddisken som innehåller säkerhetskopian och kopiera Virtuella bashårddisken och den virtuella Hårddisken med säkerhetskopiering till Azure Premium Storage-kontot. Du kan sedan distribuera den virtuella datorn baserat på Virtuella bashårddisken och montera den virtuella Hårddisken med säkerhetskopian. Nu kan du skapa ytterligare tom Premium Storage-diskar för den virtuella datorn som används för att återställa databasen till. Detta förutsätter att DBMS kan du ändra sökvägar för filer för data och loggfiler som en del av återställningsprocessen.
* En annan möjlighet är en variant av tidigare processen, där du kopiera säkerhetskopian av virtuell Hårddisk till Azure Premium Storage och bifoga den mot en virtuell dator som du nyligen har distribuerats och installerats.
* Fjärde möjligheten väljer du när du kommer behöva för att ändra antalet datafiler för din databas. I sådana fall skulle du utföra en SAP homogen system kopia med hjälp av export/import. Placera de exportera filer till en virtuell Hårddisk som har kopierats till Azure Premium Storage-kontot och koppla den till en virtuell dator som används för att köra processer för import. Kunder använda den här möjligheten främst när de vill minska antalet datafiler.

Om du använder Managed Disks kan migrera du till Premium Storage genom att:

1. Frigör den virtuella datorn
1. Om det behövs, ändra storlek på den virtuella datorn till en storlek som har stöd för Premium Storage (till exempel DS eller GS)
1. Ändra typen Managed Disks till Premium (SSD)
1. Ändra cachelagring av datadiskarna enligt rekommendationerna i kapitlet [cachelagring för virtuella datorer och datadiskar][dbms-guide-2.1]
1. Starta den virtuella datorn

### <a name="deployment-of-vms-for-sap-in-azure"></a>Distribution av virtuella datorer för SAP i Azure
Microsoft Azure erbjuder flera olika sätt att distribuera virtuella datorer och associerade diskar. Därmed är det viktigt att förstå skillnaderna eftersom förberedelser för de virtuella datorerna kan variera beroende på sättet för distribution. Generellt sett är titta vi på de scenarier som beskrivs i följande kapitel.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace
Vill du ta en Microsoft eller tredje part tillhandahålls avbildning från Azure Marketplace för att distribuera den virtuella datorn. När du har distribuerat den virtuella datorn i Azure kan följa du samma riktlinjer och verktyg för att installera SAP-programvara i din virtuella dator som du skulle göra i en lokal miljö. För att installera SAP-programvara i Azure VM, SAP och Microsoft rekommenderar att du laddar upp och lagra SAP-installationsmediet i diskar eller för att skapa en Azure-dator som fungerar som en ”server”, som innehåller alla nödvändiga SAP installationsmediet.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Distribuera en virtuell dator med en kundspecifika generaliserad avbildning
På grund av angivna korrigeringsfilen om din version av Operativsystemet eller DBMS måste kan de angivna avbildningarna i Azure Marketplace inte motsvarar dina behov. Därför kan du behöva skapa en virtuell dator med din egen ”privat” OS/DBMS VM-avbildning, som kan användas flera gånger efteråt. För att förbereda en ”privat” avbildning för duplicering, måste Operativsystemet generaliseras på den lokala virtuella datorn. Referera till den [Distributionsguide] [ deployment-guide] för information om hur du generalisera en virtuell dator.

Om du redan har installerat SAP-innehåll i den lokala virtuella datorn (särskilt för nivå 2-system), kan du anpassa inställningar för SAP-system när distributionen av virtuella Azure-datorer via instansen Byt namn på proceduren som stöds av SAP Software etablering Manager (SAP Observera [1619720]). Annars kan du installera SAP-program senare efter att distributionen av Azure VM.

När databasen innehåll används av SAP-programmet och du kan antingen generera innehållet nyligen av en SAP-installation eller du kan importera ditt innehåll till Azure med hjälp av en virtuell Hårddisk med en säkerhetskopia av databasen DBMS eller genom att utnyttja funktionerna i DBMS för att säkerhetskopiera direkt i  Microsoft Azure Storage. I det här fallet kan du även förbereda virtuella hårddiskar med den DBMS data och loggfiler filer lokalt och sedan importera dem som diskar till Azure. Men överföringen av DBMS data som läses in från en lokal plats till Azure skulle fungera över VHD-diskar som måste förberedas på plats.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk
Du planerar att flytta ett särskilt SAP-system från en lokal plats till Azure (lift och SKIFT). Detta kan göras genom att ladda upp den disk som innehåller Operativsystemet, SAP-binärfiler och eventuell DBMS-binärfiler plus diskarna med filer för data och loggfiler för DBMS till Azure. I motsatt scenariot #2 ovan, behåller du värdnamnet, SAP-SID, och SAP användarkonton i Azure-VM som de har konfigurerats i den lokala miljön. Generalisera avbildningen är därför inte nödvändigt. Det här fallet används främst för mellan lokala scenarier där en del av SAP-landskap är körs lokalt och delar på Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Hög tillgänglighet och Haveriberedskap med Azure virtuella datorer
Azure erbjuder följande hög tillgänglighet (HA) och Disaster Recovery (DR) funktioner, som gäller för olika komponenter som vi använder för- och SAP DBMS-distributioner

### <a name="vms-deployed-on-azure-nodes"></a>Datorer som distribuerats i Azure-noder
Azure-plattformen erbjuder inte funktioner, till exempel Direktmigrering för distribuerade virtuella datorer. Det innebär att om det finns Underhåll nödvändiga i ett serverkluster som en virtuell dator har distribuerats, den virtuella datorn behöver få stoppas och startas om. Underhåll i Azure utförs med hjälp av så kallade uppgradera domäner i kluster med servrar. Endast en Uppgraderingsdomänen i taget underhålls. Under en omstart finns det ett avbrott i tjänsten medan den virtuella datorn är avstängd, underhåll utförs och virtuella datorn startas om. De flesta DBMS-leverantörer tillhandahåller men funktioner för hög tillgänglighet och Haveriberedskap som startar snabbt om DBMS-tjänster på en annan nod om den primära noden inte är tillgänglig. Azure-plattformen erbjuder funktioner för att distribuera virtuella datorer, lagring och andra Azure-tjänster över uppgradera domäner för att säkerställa att planerat underhåll eller infrastruktur fel bara påverkar en liten delmängd av virtuella datorer eller tjänster.  Med noggrann planering, är det möjligt att uppnå tillgänglighet jämförbara med en lokal infrastruktur.

Microsoft Azure-Tillgänglighetsuppsättningar är en logisk gruppering av virtuella datorer eller tjänster som säkerställer att virtuella datorer och andra tjänster har distribuerats till olika fel- och uppgradera domäner inom ett kluster, så att det skulle bara finnas en nodavstängning när som helst en tidpunkt (läsa [(Linux)] [ virtual-machines-manage-availability-linux] eller [(Windows)] [ virtual-machines-manage-availability-windows] nedan för mer information).

Det måste vara konfigurerad med syftet vid distribution av virtuella datorer som visas här:

![Definitionen av Tillgänglighetsuppsättningen för DBMS HA konfigurationer][dbms-guide-figure-200]

Om vi vill skapa konfigurationer för DBMS distributioner med hög tillgänglighet (oberoende av de enskilda DBMS HA funktionerna används) måste DBMS-datorer du:

* Lägg till de virtuella datorerna i samma Azure Virtual Network (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* De virtuella datorerna i konfigurationen för hög tillgänglighet bör även finnas i samma undernät. Det går inte att namnmatchningen mellan de olika undernäten i molnbaserade distributioner, endast IP-upplösning fungerar. Med hjälp av plats-till-plats eller ExpressRoute-anslutning för lokala distributioner, är ett nätverk med minst ett undernät redan upprättad. Namnmatchning görs enligt lokalt AD-principer och nätverksinfrastruktur. 



#### <a name="ip-addresses"></a>IP-adresser
Vi rekommenderar starkt att konfigurera de virtuella datorerna för konfigurationer med hög tillgänglighet på en flexibel sätt. Förlita dig på IP-adresser att HA skickat i konfigurationen för hög tillgänglighet är inte tillförlitlig i Azure såvida statiska IP-adresser som används. Det finns två ”Shutdown” begrepp i Azure:

* Avslutning av Windows Azure-portalen eller Azure PowerShell-cmdlet Stop-AzureRmVM: I det här fallet den virtuella datorn hämtar avstängning och frigörs. Ditt Azure-konto debiteras inte längre för den här virtuella datorn så att de enda tillägg som debiteras är för den lagring som används. Om den privata IP-adressen för nätverksgränssnittet inte var statiska IP-adressen frisläpps och är det inte säkert att nätverksgränssnittet får den gamla IP-adress som tilldelats igen efter en omstart av den virtuella datorn. Utför håller ned via Azure portal eller genom att anropa Stop-AzureRmVM automatiskt gör inaktivering allokering. Om du inte vill att frigöra datorn användning Stop-AzureRmVM - StayProvisioned 
* Om du stänger av den virtuella datorn från en OS-nivå, hämtar den virtuella datorn Stäng och inte avallokeras. Men i det här fallet är ditt Azure-konto fortfarande debiteras för den virtuella datorn, trots att den är avstängd. I detta fall är intakt tilldelningen av IP-adress till en stoppad virtuell dator. Stänger av den virtuella datorn från inom tvingar inte inaktivering allokering automatiskt.

Även för scenarier med flera platser, som standard innebär varit avstängd och inaktivering allokering inaktivering tilldelningen av IP-adresser från den virtuella datorn, även om lokala principer i DHCP-inställningarna är olika. 

* Undantaget är om en tilldelar en statisk IP-adress till ett nätverksgränssnitt som beskrivs [här][virtual-networks-reserved-private-ip].
* I sådana fall är den IP-adressen har åtgärdats så länge nätverksgränssnittet inte tas bort.

> [!IMPORTANT]
> För att skydda hela distributionen enkel och hanterbara, är Rensa rekommendationen att konfigurera de virtuella datorerna partnerskap DBMS HA eller DR-konfigurationen i Azure på ett sätt som det finns en fungerande namnmatchning mellan olika virtuella datorer som ingår.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Distributionen av värden övervakning
SAP kräver möjligheten att hämta övervakningsdata från de fysiska värdar som kör Azure Virtual Machines-värd för produktiva användning av SAP-program i Azure Virtual Machines. En specifik SAP Värdagenten korrigeringsnivå krävs som gör det möjligt för den här funktionen i SAPOSCOL och Värdagenten för SAP. Den exakta korrigeringsnivån dokumenteras i SAP-kommentar [1409604].

Information om distribution av komponenter som levererar värd för data att SAPOSCOL och SAP Värdagenten och Livscykelhantering för dessa komponenter i den [Distributionsguide][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Specifik information skrivs till Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server IaaS
Från och med Microsoft Azure kan migrera du enkelt dina befintliga SQL Server-program som bygger på Windows Server-plattformen till Azure Virtual Machines. SQL Server på en virtuell dator kan du minska den totala ägandekostnaden distribution, hantering och underhåll av bredden företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SQL Server i en Azure virtuell dator, kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration som är tillgängligt lokalt. 

> [!IMPORTANT]
> Vi diskuterar inte Microsoft Azure SQL Database, vilket är en plattform som en tjänsterbjudande om Microsoft Azure-plattformen. I avsnittet i det här dokumentet handlar om hur du kör SQL Server-produkten eftersom den är känt för lokala distributioner i Azure virtuella datorer, att utnyttja infrastrukturen som en tjänst-funktion i Azure. Database-funktioner och funktionalitet mellan dessa två erbjudanden är olika och ska inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Det rekommenderas att granska [detta] [ virtual-machines-sql-server-infrastructure-services] dokumentationen innan du fortsätter.

I följande avsnitt delar av delar av dokumentationen under länken ovan aggregeras och nämns. Detaljer runt SAP nämns samt och några koncept beskrivs i detalj. Vi rekommenderar dock starkt att gå igenom dokumentationen ovan först innan du läser dokumentationen för SQL Server-specifika.

Det finns några SQL-Server i IaaS specifik information som du bör känna till innan du fortsätter:

* **SERVICEAVTALET för Virtual Machine**: det finns ett serviceavtal för virtuella datorer som körs i Azure, som finns här: <https://azure.microsoft.com/support/legal/sla/>  
* **SQL-Versionsstöd**: för SAP-kunder, stöder vi SQL Server 2008 R2 eller senare på Microsoft Azure-dator. Tidigare versioner stöds inte. Granska den här allmänna [Supportmeddelande](https://support.microsoft.com/kb/956893) för mer information. Observera att i allmänhet SQL Server 2008 stöds av Microsoft samt. Men på grund av viktiga funktioner för SAP, som introducerades i SQL Server 2008 R2, SQL Server 2008 R2 är den lägsta versionen för SAP. Tänk på att SQL Server 2012 och 2014 har utökats med djupare integrering till IaaS-scenariot (till exempel säkerhetskopiera direkt mot Azure Storage). Därför begränsar vi det här dokumentet till SQL Server 2012 och 2014 med dess senaste korrigeringsnivån för Azure.
* **Stöd för SQL-funktionen**: de flesta SQL Server-funktioner stöds på Microsoft Azure-datorer med vissa undantag. **SQL Server Failover Clustering med delade diskar stöds inte**.  Distribuerade tekniker som databasspegling, AlwaysOn-Tillgänglighetsgrupper, replikering, Loggöverföring och Service Broker stöds inom en enda Azure-Region. SQL Server AlwaysOn också stöds mellan olika Azure-regioner som beskrivs här: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Granska den [Supportmeddelande](https://support.microsoft.com/kb/956893) för mer information. Ett exempel på hur du distribuerar en AlwaysOn-konfigurationen visas i [detta] [ virtual-machines-workload-template-sql-alwayson] artikeln. Dessutom Kolla in de bästa metoderna dokumenteras [här][virtual-machines-sql-server-infrastructure-services] 
* **SQL-prestanda**: vi är övertygade om att Microsoft Azure virtuella värddatorerna fungerar bra i jämförelse med andra offentliga molnerbjudanden för virtualisering, men enskilda resultaten kan variera. Kolla in [detta] [ virtual-machines-sql-server-performance-best-practices] artikeln.
* **Med hjälp av avbildningar från Azure Marketplace**: det snabbaste sättet att distribuera en ny Microsoft Azure VM är att använda en avbildning från Azure Marketplace. Det finns avbildningar i Azure Marketplace, som innehåller SQL Server. Bilderna där SQL Server redan är installerat kan inte användas direkt för SAP NetWeaver-program. Orsaken är standardsortering för SQL Server är installerat i dessa bilder och inte den sortering som krävs av SAP NetWeaver-system. För att kunna använda sådana bilder, kontrollera stegen i kapitlet [med hjälp av en SQL Server-avbildning från Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Kolla in [prisinformation om](https://azure.microsoft.com/pricing/) för mer information. Den [Licensieringsguiden för SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) och [Licensieringsguiden för SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) är också en viktig resurs.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Riktlinjer för SQL Server-konfiguration för SAP-relaterade SQL Server-installationerna i virtuella Azure-datorer
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Rekommendationer för VM/VHD-strukturen för SAP-relaterade SQL Server-distributioner
I enlighet med den allmänna beskrivningen SQLServer körbara filer finns eller installerat till enheten av den Virtuella datorns OS-disk (enhet C:\).  Normalt används inte de flesta av SQL Server system-databaser på en hög nivå av SAP NetWeaver-arbetsbelastning. Därför kan systemdatabaser av SQL Server (master, msdb och modellen) förbli på C:\-enheten. Ett undantag kan vara tempdb som när det gäller vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolymen eller i/o-åtgärder volymen, som inte får plats på den ursprungliga virtuella datorn. Följande steg ska utföras för sådana system:

* Flytta primära tempdb-datafilerna till samma logiska enhet som primär datafilerna av SAP-databasen.
* Lägg till alla ytterligare tempdb-datafiler till var och en andra logiska enheter som innehåller en datafil för SAP-användardatabasen.
* Lägg till tempdb-loggfil i den logiska enheten som innehåller användare databasloggfilen.
* **Exklusivt för VM-typer som använder lokala SSD** på beräkning noden tempdb-data och log-filer kan placeras på D:\-enhet. Det kan dock vara rekommenderar att du använder flera tempdb-datafiler. Tänk på D:\ enhetsvolymer är olika beroende på vilken virtuell dator.

De här konfigurationerna aktivera tempdb förbruka mer utrymme än systemenheten är kan tillhandahålla. För att fastställa rätt tempdb-storlek, kan en Kontrollera tempdb storlek på befintliga system som körs lokalt. En sådan konfiguration skulle dessutom aktivera IOPS-siffror mot tempdb, vilket inte kan tillhandahållas med enheten. Igen, system som körs lokalt kan användas för att övervaka i/o-arbetsbelastning mot tempdb så att du kan härleda IOPS-siffror som du förväntar dig att se på tempdb.

En VM-konfiguration, som kör SQL Server med en SAP-databas och där tempdb-data och tempdb logfile placeras på enheten D:\ ser ut som:

![För referenskonfiguration av virtuell Azure IaaS-dator för SAP][dbms-guide-figure-300]

Tänk på att D:\ enheten har olika storlekar som är beroende av typ av virtuell dator. Beroende på storleken krav på tempdb du kan tvingas att par tempdb-data och loggfiler filer med SAP data och loggfiler databasfilerna i fall där D:\ enheten är för liten.

#### <a name="formatting-the-disks"></a>Formatera diskarna
För SQL Server NTFS blockstorlek för diskar som innehåller SQL Server-data och loggfiler ska vara 64 kB. Det finns inget behov av att formatera D:\-enhet. Den här enheten kommer före formaterade.

För att se till att återställa eller skapandet av databaser som inte initieras datafilerna genom att gå till botten innehållet i filerna, kontrollera en att SQL Server-tjänsten körs i användarkontexten har en viss behörighet. Användare i gruppen Administratörer i Windows har vanligtvis dessa behörigheter. Om SQL Server-tjänsten körs i användarkontexten för icke - Windows-administratör, måste du tilldela användaren användarrättigheten **utföra underhållsaktiviteter**.  Se informationen i det här Microsoft Knowledge Base-artikel: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Om du inte redan har gjort, rekommenderas tillämpa komprimering av SQL Server-sida därför av både SAP och Microsoft innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att utföra databas komprimering innan du laddar upp till Azure ges av två skäl:

* Mängden data som ska överföras är lägre.
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats.
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Databasen komprimering fungerar också i en Azure-datorer som lokala. Mer information om hur du komprimerar en befintlig SAP SQL Server-databas hittar du här. <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQLServer 2014 - lagra databasfilerna direkt på Azure Blob Storage
SQL Server 2014 öppnas möjligheten att lagra databasfilerna direkt på Azure Blob Store utan 'wrapper' av en virtuell Hårddisk runtom. Särskilt i Standard Azure Storage eller mindre VM-typer möjliggör detta scenarier där du kan lösa gränserna för IOPS som skulle tillämpas av ett begränsat antal diskar som kan monteras till några mindre VM-typer. Detta fungerar för användardatabaser men inte för systemdatabaser av SQL Server. Den fungerar även för data och loggfiler för SQL Server. Om du vill distribuera en SAP SQL Server-databas i stället för ”radbrytning” på så sätt det till VHD: er, Tänk på följande:

* Lagringskontot används behov för att vara i samma Azure-Region som det som används för att distribuera Virtuella SQL Server körs i.
* Listan ovan om distributionen av virtuella hårddiskar via olika Azure Storage-konton gäller för den här metoden för samt distributioner. Innebär att antalet i/o-åtgärder mot gränserna för Azure Storage-konto.

[comment]: <> (MSSedusch att göra men detta använder nätverksbandbredd och inte storage bandbredd, inte SANT?)

Information om den här typen av distribution finns här: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Om du vill lagra SQL Server-datafiler direkt på Azure Premium Storage, måste du ha en minsta SQL Server 2014 patch påverkas, vilket dokumenteras här: <https://support.microsoft.com/kb/3063054>. Lagra SQL Server-datafiler på Azure Standard Storage fungerar med den utgivna versionen av SQL Server 2014. Samma uppdateringsfilerna innehåller dock ytterligare en serie korrigeringar, vilket gör det mer tillförlitlig direkt användning av Azure Blob Storage för SQL Server-datafiler och säkerhetskopieringar. Därför bör du använda dessa korrigeringar i allmänhet.

### <a name="sql-server-2014-buffer-pool-extension"></a>Buffertpooltillägget för SQL Server 2014
SQL Server 2014 introducerade en ny funktion som kallas Buffertpooltillägget. Den här funktionen utökar buffertpooltillägget för SQL Server, som sparas i minnet med en andra nivå cache som backas upp av lokal SSD-enheter på en server eller virtuell dator. Detta gör det möjligt för att lagra en större arbetsminnet för data ”i minnet”. Jämfört med åtkomst till Azure standardlagring är åtkomst till tillägg till buffertpoolen, som är lagrad på lokala SSD-enheter av en Azure VM många faktorer snabbare.  Därför kan att använda VM-typer som har utmärkt IOPS och dataflöden D:\ lokalt vara en mycket rimligt sätt att minska belastningen IOPS mot Azure Storage och förbättra svarstiderna för frågor avsevärt. Detta gäller särskilt om du inte använder Premium Storage. Vid Premium Storage och användningen av Premium Azure Läs-Cache på Beräkningsnoden, vilket rekommenderas för datafiler, förväntas några betydande skillnader. Anledningen är att båda cacheminnen (Buffertpooltillägget för SQL Server och Läs-Cache för Premium Storage) använder de lokala diskarna med compute-noder.
Mer information om den här funktionen finns i den här dokumentationen: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Överväganden för säkerhetskopiering/återställning för SQL Server
När du distribuerar SQL Server till Azure måste metoder för säkerhetskopiering granskas. Även om systemet inte är en produktiv system måste SAP-databaser som hanteras av SQL Server säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga i jämfört med kompenserande en krasch för lagring. Prioritet orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en nivå 2 SAP-konfiguration till en installation av system för 3 nivåer i samma system genom att återställa en säkerhetskopia.

Det finns tre olika sätt att säkerhetskopiera SQL Server till Azure Storage:

1. SQL Server 2012 CU4 och högre kan internt säkerhetskopiera databaserna till en URL. Detta beskrivs i bloggen [nya funktioner i SQL Server 2014 - del 5 – förbättringar för säkerhetskopiering/återställning](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Se kapitel [SQL Server 2012 SP1 CU4 och senare][dbms-guide-5.5.1].
2. SQL Server-versioner före SQL 2012 CU4 kan använda en omdirigering funktionalitet för att säkerhetskopiera till en virtuell Hårddisk och flytta i praktiken skrivning stream mot en Azure-lagringsplats som har konfigurerats. Se kapitel [SQL Server 2012 SP1 CU3 och tidigare versioner][dbms-guide-5.5.2].
3. Den sista metoden är att utföra en traditionell SQL Server-säkerhetskopiering på en disk på en diskenhet. Detta är identisk med den lokala distributionen mönstret och beskrivs inte i detalj i det här dokumentet.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 och senare
Den här funktionen kan du direkt säkerhetskopiering till Azure BLOB storage. Utan den här metoden måste du säkerhetskopiera diskar, vilket skulle använda disk och IOPS-kapacitet. Tanken är i princip detta:

 ![Med hjälp av SQL Server 2012-säkerhetskopiering till Microsoft Azure Storage BLOB][dbms-guide-figure-400]

Fördelen är i det här fallet inte behöver en spendera diskar för att lagra säkerhetskopior av SQL Server på. Du måste färre diskar som har allokerats och hela bandbredden för disk-IOPS kan användas för data och loggfiler. Observera att den maximala storleken för en säkerhetskopia är begränsad till högst 1 TB enligt beskrivningen i avsnittet **begränsningar** i den här artikeln: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Om säkerhetskopians storlek, trots att med hjälp av SQL Server-säkerhetskopieringskomprimering skulle överskrida 1 TB i storlek, funktionerna som beskrivs i kapitlet [SQL Server 2012 SP1 CU3 och tidigare versioner] [ dbms-guide-5.5.2] i det här dokumentet måste vara används.

[Relaterad dokumentation](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) som beskriver återställning av databaser från säkerhetskopior mot Azure Blob Store rekommenderar inte för att återställa direkt från Azure BLOB store om säkerhetskopieringen är > 25 GB. Rekommendationen i den här artikeln är baserad på prestandaöverväganden och inte på grund av funktionella begränsningar. Därför kan tillämpa olika villkor för en fall till fall.

Dokumentation om hur den här typen av säkerhetskopiering har konfigurerats och, med finns i [detta](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) självstudien

Ett exempel på frågornas ordningsföljd kan läsas [här](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatiserad säkerhetskopiering, är det av högsta vikt att se till att BLOB-objekt för varje säkerhetskopiering är namnet på olika sätt. Annars skrivs de över och restore-kedjan har brutits.

För att inte blanda ihop saker mellan tre olika typer av säkerhetskopior är det lämpligt att skapa olika behållare under storage-kontot som används för säkerhetskopieringar. Behållarna som kan vara av en virtuell dator bara eller av virtuell dator och typ av säkerhetskopiering. Schemat kan se ut:

 ![Med hjälp av SQL Server 2012-säkerhetskopiering till Microsoft Azure Storage BLOB - olika behållare under separat Storage-konto][dbms-guide-figure-500]

I exemplet ovan är skulle säkerhetskopiorna som inte ha utförts i samma lagringskonto där de virtuella datorerna distribueras. Det skulle vara ett nytt lagringskonto för säkerhetskopior. I storage-konton, skulle det finnas olika behållare som skapats med en matris av typen av säkerhetskopiering och VM-namnet. Sådana segmentering gör det lättare att administrera säkerhetskopieringar för olika virtuella datorer.

BLOB-objekt som en skrivåtgärd direkt säkerhetskopiering, läggs inte till antal data diskar på en virtuell dator. Därför en kan maximera maximalt antalet datadiskar som monterats av specifika VM-SKU för data och transaktionen loggfil och fortfarande köra en säkerhetskopiering mot en lagringsbehållare. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 och tidigare versioner
Det första steget du måste utföra för att uppnå en säkerhetskopia direkt mot Azure Storage är att ladda ned msi, som är länkad till [detta](https://www.microsoft.com/download/details.aspx?id=40740) KBA artikeln.

Ladda ned x64 installationsfilen och i dokumentationen. Filen installerar ett program som kallas: **Microsoft SQL Server-säkerhetskopiering till Microsoft Azure-verktyget**. Läs i dokumentationen för produkten noggrant.  Verktyget fungerar i praktiken på följande sätt:

* Från SQL Server-sida, definieras en diskplatsen för SQL Server-säkerhetskopiering (Använd inte D:\ enheten som plats).
* Verktyget kan du definiera regler som kan användas för att dirigera olika typerna av säkerhetskopieringar för olika Azure Storage-behållare.
* När reglerna är på plats, omdirigerar verktyget write-dataström med säkerhetskopian till en av de virtuella hårddiskar/diskarna till Azure Storage-plats, som definierades tidigare.
* Verktyget lämnar en liten stub-fil med ett par KB storlek på VHD/Disk som har definierats för SQL Server backup. **Den här filen ska finnas kvar på lagringsplatsen eftersom det krävs för att återställa igen från Azure Storage.**
  * Om du har tappat bort stub-filen (till exempel via förlust av lagringsmedia som innehåller stubbfil) och du har valt alternativet att säkerhetskopiera till ett Microsoft Azure Storage-konto, kan du återställa stub-filen via Microsoft Azure Storage genom att hämta den från behållaren där placerades. Placera stub-filen i en mapp på den lokala datorn där verktyget är konfigurerad för att identifiera och ladda upp till behållaren med samma krypteringslösenordet om kryptering har använts med den ursprungliga regeln. 

Det innebär att schemat enligt beskrivningen ovan för nyare versioner av SQL Server kan placeras på plats samt SQL Server-versioner som inte tillåter direkt adress en Azure-lagringsplats.

Den här metoden ska inte användas med nyare SQL Server-versioner som stöder säkerhetskopiering sig internt mot Azure Storage. Undantagen är där begränsningar av intern säkerhetskopiering till Azure blockerar interna säkerhetskopiering körning i Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Andra alternativ för att säkerhetskopiera SQL Server-databaser
Andra alternativ för att säkerhetskopiera databaser är att koppla ytterligare datadiskar till en virtuell dator som används för att lagra säkerhetskopior på. I sådana fall skulle du behöva se till att diskarna inte kör fullständig. Om så är fallet, skulle du behöva demontera diskarna och därför till speak 'Arkivera' den och Ersätt den med en ny tom disk. Om du går ned den sökvägen du vill behålla dessa virtuella hårddiskar i separata Azure Storage-konton från de som virtuella hårddiskar med databasfilerna.

En andra alternativet är att använda en stor virtuell dator som kan ha många diskar som är anslutna, till exempel en D14 med 32VHDs. Använd lagringsutrymmen för att skapa en flexibel miljö där du kan bygga resurser som sedan används som säkerhetskopieringsmål för olika DBMS-servrar.

Några av metodtipsen har dokumenterat [här](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) samt. 

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan du anta att:

* Färre på antalet diskar som används för att lagra data filer, Ju mindre det totala arbetsflödet vid läsning.
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior.
* Färre mål (Blobbar, virtuella hårddiskar eller diskar) för att skriva säkerhetskopian till den lägsta dataflödet.
* Ju mindre den virtuella datorn storlek, desto mindre dataflöde lagringskvoten skriver och läser från Azure Storage. Oberoende av om säkerhetskopiorna lagras direkt på Azure Blob eller om de lagras i virtuella hårddiskar som är lagrade i Azure Blobs igen.

När du använder en Microsoft Azure Storage BLOB som säkerhetskopieringsmål i senare versioner kan begränsas du till utse bara en URL-mål för varje specifik säkerhetskopiering.

Men när du använder ”Microsoft SQL serversäkerhetskopiering till Microsoft Azure-verktyget” i äldre versioner kan du definiera fler än en målfil. Säkerhetskopieringen kan skalas med fler än ett mål och dataflödet för säkerhetskopian är högre. Detta leder sedan flera filer även i Azure Storage-kontot. I testning, uppnå med hjälp av flera filen mål du definitivt dataflöde, vilket du kan uppnå med de säkerhetskopiera tillägg som implementeras i från SQL Server 2012 SP1 CU4 på. Du också blockeras inte av gränsen på 1TB som intern säkerhetskopiering till Azure.

Tänk dock på, dataflödet beror också på platsen för Azure Storage-konto som du använder för säkerhetskopieringen. En idé kan vara att leta upp lagringskontot i en annan region än de virtuella datorerna körs i. Till exempel du skulle köra VM-konfigurationen i västra Europa, men placerar Storage-konto som används för att säkerhetskopiera mot i Norra Europa. Det däremot påverkar säkerhetskopiering dataflödet och troligen inte att generera ett dataflöde på 150MB per sekund som verkar vara möjligt i fall där mållagring och de virtuella datorerna körs i samma regionala datacenter.

#### <a name="managing-backup-blobs"></a>Hantera säkerhetskopierade Blobbar
Det är ett krav att hantera säkerhetskopiering på egen hand. Eftersom förutsätts att många blobar skapas genom att köra säkerhetskopieringar av transaktionsloggen frekventa kan administration av dessa blobar enkelt överbelasta Azure-portalen. Därför är det recommendable att använda ett Azure storage explorer. Det finns flera bra de tillgängliga, vilket hjälper dig för att hantera ett Azure storage-konto

* Microsoft Visual Studio med Azure SDK är installerat (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Verktyg från tredje part

En fullständig beskrivning av säkerhetskopiering och SAP på Azure kan referera till [Säkerhetskopieringsguide för SAP](sap-hana-backup-guide.md) för mer information.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Med hjälp av en SQL Server-avbildning från Microsoft Azure Marketplace
Microsoft erbjuder virtuella datorer i Azure Marketplace, som redan innehåller versioner av SQL Server. För SAP-kunder som behöver licenser för SQL Server och Windows, kan det vara en möjlighet att i praktiken täcka behovet av licenser genom att skapa virtuella datorer med SQL Server redan installerat. Följande överväganden måste göras för att kunna använda sådana bilder för SAP:

* De SQL Server icke-utvärderingsversioner hämta högre kostnader för än en ”endast Windows-VM som distribueras från Azure Marketplace. Se dessa artiklar för att jämföra priser: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> och <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Du kan bara använda SQL Server-versioner som stöds av SAP, t.ex. SQL Server 2012.
* Sorteringen av SQL Server-instansen som är installerad på virtuella datorer i Azure Marketplace är inte sorteringen SAP NetWeaver kräver SQL Server-instansen ska köras. Du kan ändra sorteringen men med anvisningarna i följande avsnitt.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändra SQL Server-sorteringen för en Microsoft Windows/SQL Server-dator
Eftersom SQL Server-avbildningar på Azure Marketplace inte har ställts in att använda sortering, vilket krävs av SAP NetWeaver-program, måste den ändras direkt efter distributionen. För SQL Server 2012, kan detta göras med följande steg när den virtuella datorn har distribuerats och en administratör kan logga in på den distribuerade virtuella datorn:

* Öppna ett Windows-kommandofönster som administratör.
* Ändra katalogen till C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Kör kommandot: Setup.exe /QUIET/Action = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> är det konto som har definierats som administratörskontot när du distribuerar den virtuella datorn för första gången i galleriet.

Processen tar bara några minuter. För att se om steget till slut med rätt resultat, utför du följande steg:

* Öppna SQL Server Management Studio.
* Öppna ett frågefönster.
* Kör kommandot sp_helpsort i SQL Server-huvuddatabasen.

Det önskade resultatet bör se ut:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Om detta inte är resultatet stoppa distribuera SAP och undersöka varför installationskommandot inte fungerar som förväntat. Distribution av SAP NetWeaver-program till SQL Server-instansen med olika SQL Server-kodsidor än den som nämns ovan är **inte** stöds.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server hög tillgänglighet för SAP i Azure
Som nämnts tidigare i det här dokumentet, går det inte att skapa delad lagring, vilket är nödvändigt för användningen av den äldsta SQL Server-funktionen för hög tillgänglighet. Den här funktionen installeras två eller flera SQL Server-instanser i en redundans kluster WSFC (Windows Server) med en delad disk för användardatabaser (och så småningom tempdb). Det här är metoden länge standard hög tillgänglighet, som även stöds av SAP. Eftersom Azure inte stöder delad lagring, kan SQL Server konfigurationer med hög tillgänglighet med en delad disk klusterkonfiguration realiseras. Men många andra metoder för hög tillgänglighet är fortfarande möjliga och beskrivs i följande avsnitt.

#### <a name="sql-server-log-shipping"></a>SQLServer-Loggöverföring
En av metoderna för hög tillgänglighet (HA) är SQL Server-Loggöverföring. Om de virtuella datorerna som deltar i konfiguration för hög tillgänglighet har arbeta namnmatchning, inga problem och konfiguration i Azure skiljer sig inte från några inställningar som görs på plats. Du bör inte förlita dig på endast IP-lösning. För att skapa Loggöverföring och principerna runt Loggöverföring, kontrollera den här dokumentationen:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

För att uppnå hög tillgänglighet krävs som för att distribuera de virtuella datorerna, som ingår i en sådan Loggöverföring konfiguration ska vara i samma Azure Tillgänglighetsuppsättningen.

#### <a name="database-mirroring"></a>Databasspegling
Databasen spegling som stöds av SAP (Se SAP-kommentar [965908]) förlitar sig på Definiera redundanspartner i SAP-anslutningssträngen. I fall som flera platser förutsätter vi att de två virtuella datorerna är i samma domän och att användaren kontext två SQL Server-instanser körs under en domänanvändare och har tillräcklig behörighet i två SQL Server-instanserna som ingår. Därför skiljer inte installationen av databasspegling i Azure mellan en typisk lokala/installationskonfiguration.

Från och med molnbaserade distributioner är det enklaste sättet att installationsprogrammet ska en annan domän i Azure för att ha dessa DBMS virtuella datorer (och helst dedikerade virtuella SAP-datorer) i en domän.

Om en domän inte är möjligt kan en också använda certifikat för slutpunkter för databasspegling, enligt nedan: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

En självstudie om du vill konfigurera databasspegling i Azure finns här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQLServer Always On
Som alltid på stöds för SAP on-premises (Se SAP-kommentar [1772688]), det går för att användas i kombination med SAP i Azure. Det faktum att det inte går att skapa delade diskar i Azure innebär inte att någon inte kan skapa en alltid på Windows Server-redundanskluster (WSFC) konfiguration mellan olika virtuella datorer. Det betyder bara att du inte har möjlighet att använda en delad disk som ett kvorum i klusterkonfigurationen. Därför kan du skapa en alltid på WSFC-konfiguration i Azure och inte välja kvorum som använder delad disk. Azure-miljön dessa virtuella datorer distribueras i Lös de virtuella datorerna efter namn och de virtuella datorerna ska finnas i samma domän. Detta gäller för enbart Azure och mellan lokala distributioner. Det finns några särskilda överväganden kring distribution av SQL Server tillgänglighetsgruppens lyssnare (inte ska inte förväxlas med Azure-Tillgänglighetsuppsättning) eftersom Azure vid denna tidpunkt inte tillåter att skapa ett AD/DNS-objekt eftersom det är möjligt på plats. Därför är vissa olika installationsstegen nödvändiga för att lösa specifika beteendet för Azure.

Vissa förutsättningar med hjälp av en Tillgänglighetsgruppslyssnare är:

* Med hjälp av en Tillgänglighetsgruppslyssnare är bara möjligt med Windows Server 2012 eller senare som gästoperativsystem för den virtuella datorn. För Windows Server 2012 måste du se till att korrigeringsfilen tillämpas: <https://support.microsoft.com/kb/2854082> 
* För Windows Server 2008 R2 finns inte korrigeringen och Always On måste användas på samma sätt som databasspegling genom att ange redundanspartner i anslutningssträngen (via SAP default.pfl parametern dbs/mss/server - Se SAP-kommentar [965908]).
* När du använder en Tillgänglighetsgruppslyssnare, måste den virtuella vara anslutna till en särskild belastningsutjämnare. Namnmatchning i molnbaserade distributioner antingen kräver alla virtuella datorer i ett SAP-system (programservrar, DBMS server och (A) SCS-server) är i samma virtuella nätverk eller från en SAP-programnivån kräver underhåll av filen etc\host i ordning att hämta VM-namnen på SQL Server-datorer som löst. För att undvika att Azure tilldelar nya IP-adresser i fall där båda virtuella datorerna kanske lägger avstängning, ska en tilldela statiska IP-adresser till nätverksgränssnitt för de virtuella datorer i konfigurationen för Always On (definierar en statisk IP-adress beskrivs i [detta] [ virtual-networks-reserved-private-ip] artikeln)

[comment]: <> (Gamla bloggar)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Det finns särskilda åtgärder krävs om att skapa WSFC-klusterkonfiguration där klustret behöver en särskild IP-adress har tilldelats, eftersom Azure med dess aktuella funktionerna tilldelar klusternamnet samma IP-adress som noder i klustret har skapats på. Det innebär att ett manuellt steg måste utföras för att tilldela en annan IP-adress till klustret.
* Tillgänglighetsgruppslyssnaren kommer att skapas i Azure med TCP/IP-slutpunkter, som är tilldelade till de virtuella datorerna som kör de primära och sekundära replikerna för tillgänglighetsgruppen.
* Det kan finnas ett behov av att skydda dessa slutpunkter med ACL: er.

[comment]: <> (Gamla TODO-bloggen)
[comment]: <> (Detaljerade anvisningar och är nödvändigt för att installera en AlwaysOn-konfigurationen på Azure är enklast att när gå igenom självstudien tillgängliga [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Förkonfigurerade AlwaysOn-installationen via Azure-galleriet <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Skapa en Tillgänglighetsgruppslyssnare är den lämpligaste beskrivningen i [this][virtual-machines-windows-classic-ps-sql-int-listener] självstudien)
[comment]: <> (Att säkra nätverksslutpunkter med ACL: er beskrivs bäst här:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Det är möjligt att distribuera en SQL Server alltid tillgänglighetsgrupp via samt olika Azure-regioner. Den här funktionen utnyttjar Azure VNet-till-Vnet-anslutning ([mer][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Gamla TODO-bloggen)
[comment]: <> (Installationen av SQL Server AlwaysOn-Tillgänglighetsgrupper i ett sådant scenario som beskrivs här: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Sammanfattning på SQL Server med hög tillgänglighet i Azure
Med tanke på att Azure Storage är att skydda innehållet, finns det ett mindre skäl till att kräva en hot standby-avbildning. Det innebär att ditt scenario med hög tillgänglighet behöver bara skydda mot följande fall:

* Den virtuella datorn otillgänglig som helhet på grund av underhåll på server-kluster i Azure eller andra orsaker
* Problem med programvara i SQL Server-instansen
* Skydda från manuell fel där data tas bort och point-in-time-återställning krävs

Titta på matchande tekniker som en kan argumentera att de två första fall kan omfattas av databasspegling eller alltid på det tredje fallet bara kan omfattas av Loggöverföring.

Du måste väga mer komplexa installationen av Always On, jämfört med databasspegling med fördelarna med Always On. Dessa fördelar kan anges som:

* Läsbara sekundära repliker.
* Säkerhetskopior från sekundära repliker.
* Bättre skalbarhet.
* Mer än en sekundära repliker.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Allmänt SQL Server för SAP på Azure sammanfattning
Det finns många rekommendationer i den här guiden och vi rekommenderar att du läsa den mer än en gång innan du planerar din Azure-distribution. I allmänhet dock måste du följa de översta tio Allmänt DBMS på Azure specifika punkter:

[comment]: <> (2,3 högre dataflöde än vad? Än en virtuell Hårddisk?)
1. Använd den senaste DBMS-versionen som SQL Server 2014, som har de flesta fördelar i Azure. Detta är för SQL Server, SQL Server 2012 SP1 CU4, som omfattar funktionen säkerhetskopiering oss jämfört med Azure Storage. Men tillsammans med SAP rekommenderas att använda minst SQL Server 2014 SP1 CU1 eller SQL Server 2012 SP2 och den senaste Kapacitetsenhet.
2. Planera noggrant ditt SAP-landskap system i Azure för att balansera data filens layout och Azure-begränsningar:
   * Inte har för många diskar, men har tillräckligt för att se till att du kan nå din krävs IOPS.
   * Om du inte använder Managed Disks, Kom ihåg att IOPS är också begränsad per Azure Storage-konto och att konton begränsas i varje Azure-prenumeration ([mer][azure-subscription-service-limits]). 
   * Endast stripe över diskar om du behöver att uppnå ett högre dataflöde.
3. Aldrig installera program eller placera alla filer som kräver persistence på enheten D:\ eftersom det är flyttbara och allt på den här enheten går förlorade vid en omstart av Windows.
4. Använd inte diskcachelagring för Azure Standard Storage.
5. Använd inte Azure geo-replikerade Storage-konton.  Använd lokalt Redundant för DBMS arbetsbelastningar.
6. Använda DBMS leverantörens hr/DR-lösning för att replikera databasdata.
7. Alltid använda namnmatchning, inte förlita dig på IP-adresser.
8. Använd högsta databasen komprimering så mycket som möjligt. Vilket är sidan komprimering för SQL Server.
9. Var försiktig med hjälp av SQL Server-avbildningar från Azure Marketplace. Om du använder en SQL Server, måste du ändra instansens sortering innan du installerar alla SAP NetWeaver-system på den.
10. Installera och konfigurera SAP-värden som övervakning för Azure enligt beskrivningen i [Distributionsguide][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Specifik information skrivs till SAP ASE på Windows
Från och med Microsoft Azure kan migrera du enkelt dina befintliga SAP ASE-program till Azure Virtual Machines. SAP ASE på en virtuell dator kan du minska den totala ägandekostnaden distribution, hantering och underhåll av bredden företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en Azure virtuell dator, kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration som är tillgängligt lokalt.

Det finns ett serviceavtal för Azure Virtual Machines, som finns här: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Vi är övertygade om att Microsoft Azure virtuella värddatorerna utför även i jämförelse med andra offentliga molnerbjudanden för virtualisering, men enskilda resultaten kan variera. SAP som ändrar storlek på SAP många olika SAP-certifierad VM SKU: er finns i en separat SAP-kommentar [1928533].

Instruktioner och rekommendationer gäller användningen av Azure Storage, distribution av SAP virtuella datorer eller SAP övervakning gäller för distributioner av SAP ASE tillsammans med SAP-program som anges under de första fyra kapitlen i det här dokumentet.

### <a name="sap-ase-version-support"></a>SAP ASE-Versionsstöd
SAP för närvarande stöder SAP ASE version 16,0 för användning med SAP Business Suite-produkter. Alla uppdateringar för SAP ASE eller JDBC och ODBC-drivrutiner som ska användas med SAP Business Suite produkter sker enbart via SAP Service Marketplace på: <https://support.sap.com/swdc>.

För installationer hämta lokalt, inte uppdateringarna till SAP ASE-server, JDBC och ODBC-drivrutiner eller direkt från Sybase-webbplatser. Detaljerad information om korrigeringarna som stöds för användning med SAP Business Suite produkter lokalt och i Azure virtuella datorer finns i följande SAP-information:

* [1590719]
* [1973241]

Allmän information om hur du kör SAP Business Suite på SAP ASE finns i den [Tillståndsändringsavisering](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Riktlinjer för SAP ASE konfiguration för SAP-relaterade SAP ASE installationer i Azure virtuella datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Strukturen för SAP ASE-distribution
I enlighet med den allmänna beskrivningen SAP ASE körbara filer finns eller installerat till enheten av den Virtuella datorns OS-disk (enhet c:\). Vanligtvis används de flesta av SAP ASE system och verktyg databaserna inte hårt av SAP NetWeaver-arbetsbelastning. Därför kan system och verktyg databaserna (master, model, saptools, sybmgmtdb, sybsystemdb) förbli på C:\-enheten. 

Ett undantag kan vara tillfälliga databasen som innehåller alla tabeller för arbete och temporära tabeller som skapats av SAP ASE, som vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolymen eller i/o-åtgärder volym, vilket inte kan placeras i den ursprungliga Virtuella datorns operativsystem disk (enhet c:\).

Beroende på vilken version av SAPInst/SWPM används för att installera innehålla databasen:

* En enda SAP ASE tempdb som skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följa SAP-kommentar [1752266]) att uppfylla kraven för ERP/BW specifika tempdb

Vid specifika ERP eller alla BW arbetsbelastningar kan det vara bra, avseende prestanda för att dessutom skapade tempdb (via SWPM eller manuellt) av tempdb-enheter på en annan enhet än C:\. Om det finns inga ytterligare tempdb, rekommenderar vi att du skapar ett (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-enheten till den första enheten för SAP-databas
* Lägga till tempdb-enheter till var och en av de virtuella hårddiskarna som innehåller en enhet av SAP-databas

Den här konfigurationen gör det möjligt för tempdb kan antingen använda mer utrymme än systemenheten är kan tillhandahålla. Som referens hittar en tempdb-enheten storlek på befintliga system som körs lokalt. Eller en sådan konfiguration skulle aktivera IOPS-siffror mot tempdb, vilket inte kan tillhandahållas med enheten. Igen kan system som körs lokalt användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera aldrig några SAP ASE-enheter på D:\ enhet för den virtuella datorn. Detta gäller även för tempdb, även om de objekt som sparas i tempdb är temporära.

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att utföra komprimering innan du laddar upp till Azure om den inte redan har implementerat ges av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Data - och LOB-komprimering fungerar i en virtuell dator som finns i Azure virtuella datorer som den finns på plats. För mer information om hur du kontrollerar om komprimering finns redan i använda i en befintlig databas för SAP ASE, kontrollerar SAP-kommentar [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Database-instanser
För SAP-system som använder SAP ASE som plattform, är DBACockpit tillgänglig som embedded webbläsarfönster i transaktionen DBACockpit eller Webdynpro. Men den fullständiga funktionaliteten för att övervaka och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut så här:

> ICM/server_port_0 = port = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = port = HTTPS, PORT = 443$ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

och länkar som genereras i transaktionen DBACockpit ser ut ungefär så här:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på om och hur den Azure-datorn som är värd för SAP-system är ansluten via plats-till-plats, flera platser eller ExpressRoute (mellan lokala distribution) du måste se till att den ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du försöker öppna DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger det fullständigt kvalificerade värdnamnet beroende på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med molnbaserad utan korsanslutningar mellan lokala och Azure som du behöver definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information som rör DNS-namn finns [här][virtual-machines-azurerm-versus-azuresm].

DNS-namnet på Azure VM länken för att ställa in SAP profil parametern icm/host_name_full kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

I det här fallet måste du se till att:

* Lägga till regler för inkommande trafik i Nätverkssäkerhetsgruppen i Azure-portalen för TCP/IP-portar som används för att kommunicera med ICM
* Lägga till regler för inkommande trafik till Windows-brandväggskonfigurationen för TCP/IP-portar som används för att kommunicera med ICM

För en automatiserad importeras av alla ändringar som är tillgänglig, rekommenderas att med jämna mellanrum tillämpa korrigering samlingen SAP-kommentar som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Ytterligare information om DBA Cockpit för SAP ASE finns i följande SAP-information:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhetskopiering/återställning överväganden för SAP ASE
När du distribuerar SAP ASE till Azure, måste metoder för säkerhetskopiering granskas. Även om systemet inte är en produktiv system måste SAP-databaser som hanteras av SAP ASE säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga i jämfört med kompenserande en krasch för lagring. Den främsta orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en nivå 2 SAP-konfiguration till en installation av system för 3 nivåer i samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som på plats. Se SAP Notes:

* [1588316]
* [1585981]

Mer information om skapa dump konfigurationer och schemaläggning säkerhetskopieringar. Beroende på din strategi och dina behov kan du konfigurera Dumpar databasen och loggfiler disken till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhetskopiering. För att minska risken för dataförlust om ett fel, rekommenderar vi att du använder en disk där det finns ingen databasenhet.

Förutom data- och LOB erbjuder komprimering SAP ASE även komprimering av säkerhetskopior. Om du vill tar upp mindre utrymme med databasen och loggfiler Dumpar rekommenderar vi att du använder komprimering av säkerhetskopior. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att hämta säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till en lokal.

Använd inte enhet D:\ som databas eller logg dump-mål.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till den lägsta dataflödet

Så här ökar antalet mål att skriva till det finns två alternativ, vilket kan vara används/kombineras beroende på dina behov:

* Strimling säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS dataflödet på stripe volymen
* Skapar en dump-konfigurationen på SAP ASE-nivå, som använder mer än en målkatalogen för att skriva dumpen till

En volym Strimling över flera monterade diskar har diskuterats tidigare i den här guiden. Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen på lagringsprocedur sp_config_dump som används för att skapa den dump-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveriberedskap med Azure virtuella datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikering av data med SAP Sybase replikering-Server
Med SAP Sybase replikering Server (SRS) SAP ASE tillhandahåller en varm standby lösning att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar även samma funktioner i en virtuell dator på Azure Virtual Machine-tjänsterna som den lokala.

SAP ASE HADR kräver inte en intern belastningsutjämnare i Azure och har inte beroenden på nivån OS-klustring och fungerar på Azure Windows och Linux-datorer. Information om SAP ASE HADR finns det [SAP ASE HADR användarhandboken](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specifik information skrivs till SAP ASE i Linux
Från och med Microsoft Azure kan migrera du enkelt dina befintliga SAP ASE-program till Azure Virtual Machines. SAP ASE på en virtuell dator kan du minska den totala ägandekostnaden distribution, hantering och underhåll av bredden företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en Azure virtuell dator, kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration som är tillgängligt lokalt.

För att distribuera virtuella datorer i Azure är det viktigt att veta att officiella serviceavtalen som finns här: <https://azure.microsoft.com/support/legal/sla>

Information för SAP-storlek och en lista med SAP-certifierade VM SKU: er tillhandahålls i SAP-kommentar [1928533]. Ytterligare SAP storlek dokument för virtuella datorer med Azure finns här <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> och här <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruktioner och rekommendationer gäller användningen av Azure Storage, distribution av SAP virtuella datorer eller SAP övervakning gäller för distributioner av SAP ASE tillsammans med SAP-program som anges under de första fyra kapitlen i det här dokumentet.

Följande två SAP-information är allmän information om ASE på Linux- och ASE i molnet:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE-Versionsstöd
SAP för närvarande stöder SAP ASE version 16,0 för användning med SAP Business Suite-produkter. Alla uppdateringar för SAP ASE eller JDBC och ODBC-drivrutiner som ska användas med SAP Business Suite produkter sker enbart via SAP Service Marketplace på: <https://support.sap.com/swdc>.

För installationer hämta lokalt, inte uppdateringarna till SAP ASE-server, JDBC och ODBC-drivrutiner eller direkt från Sybase-webbplatser. Detaljerad information om korrigeringarna som stöds för användning med SAP Business Suite produkter lokalt och i Azure virtuella datorer finns i följande SAP-information:

* [1590719]
* [1973241]

Allmän information om hur du kör SAP Business Suite på SAP ASE finns i den [Tillståndsändringsavisering](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Riktlinjer för SAP ASE konfiguration för SAP-relaterade SAP ASE installationer i Azure virtuella datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Strukturen för SAP ASE-distribution
I enlighet med den allmänna beskrivningen SAP ASE körbara filer finns eller installerats i filsystemet roten för den virtuella datorn (/sybase). Vanligtvis utnyttjas de flesta av SAP ASE system och verktyg databaser inte hårt av SAP NetWeaver-arbetsbelastning. Därför system och verktyg databaserna (master, model, saptools, sybmgmtdb, sybsystemdb) kan finnas kvar på filsystemet rot. 

Ett undantag kan vara tillfälliga databasen som innehåller alla tabeller för arbete och temporära tabeller som skapats av SAP ASE, som vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolymen eller i/o-åtgärder, volym som inte får plats i den ursprungliga Virtuella datorns operativsystem disk.

Beroende på vilken version av SAPInst/SWPM används för att installera innehålla databasen:

* En enda SAP ASE tempdb som skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följa SAP-kommentar [1752266]) att uppfylla kraven för ERP/BW specifika tempdb

Vid specifika ERP eller alla BW arbetsbelastningar kan det vara bra, avseende prestanda för att dessutom skapade tempdb (via SWPM eller manuellt) av tempdb-enheter på en separat filsystem, som representeras av en enda Azure-data-disk eller en Linux-RAID-spannin g flera Azure-datadiskar. Om det finns inga ytterligare tempdb, rekommenderar vi att du skapar ett (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-katalogen till första filsystemet för SAP-databas
* Lägg till tempdb kataloger i var och en av diskarna som innehåller ett filsystem för SAP-databas

Den här konfigurationen gör det möjligt för tempdb kan antingen använda mer utrymme än systemenheten är kan tillhandahålla. Som referens hittar en tempdb directory storlek på befintliga system som körs lokalt. Eller en sådan konfiguration skulle aktivera IOPS-siffror mot tempdb, vilket inte kan tillhandahållas med enheten. Igen kan system som körs lokalt användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera inte alla SAP ASE kataloger till /mnt eller /mnt/resource för den virtuella datorn. Detta gäller även för tempdb, även om de objekt som sparas i tempdb är temporära eftersom /mnt eller /mnt/resource en standard virtuell Azure-dator temp utrymme, vilket inte är beständig. Mer information om virtuella Azure-datorn temp utrymme finns i [i den här artikeln][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att utföra komprimering innan du laddar upp till Azure om den inte redan har implementerat ges av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Data - och LOB-komprimering fungerar i en virtuell dator som finns i Azure virtuella datorer som den finns på plats. För mer information om hur du kontrollerar om komprimering finns redan i använda i en befintlig databas för SAP ASE, kontrollerar SAP-kommentar [1750510]. Ytterligare information om databasen komprimering finns i SAP-kommentar [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Database-instanser
För SAP-system som använder SAP ASE som plattform, är DBACockpit tillgänglig som embedded webbläsarfönster i transaktionen DBACockpit eller Webdynpro. Men den fullständiga funktionaliteten för att övervaka och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut så här:

> ICM/server_port_0 = port = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = port = HTTPS, PORT = 443$ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

och länkar som genereras i transaktionen DBACockpit ser ut ungefär så här:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på om och hur den Azure-datorn som är värd för SAP-system är ansluten via plats-till-plats, flera platser eller ExpressRoute (mellan lokala distribution) du måste se till att den ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du försöker öppna DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger det fullständigt kvalificerade värdnamnet beroende på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med molnbaserad utan korsanslutningar mellan lokala och Azure som du behöver definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information som rör DNS-namn finns [här][virtual-machines-azurerm-versus-azuresm].

DNS-namnet på Azure VM länken för att ställa in SAP profil parametern icm/host_name_full kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

I det här fallet måste du se till att:

* Lägga till regler för inkommande trafik i Nätverkssäkerhetsgruppen i Azure-portalen för TCP/IP-portar som används för att kommunicera med ICM
* Lägga till regler för inkommande trafik till Windows-brandväggskonfigurationen för TCP/IP-portar som används för att kommunicera med ICM

För en automatiserad importeras av alla ändringar som är tillgänglig, rekommenderas att med jämna mellanrum tillämpa korrigering samlingen SAP-kommentar som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Ytterligare information om DBA Cockpit för SAP ASE finns i följande SAP-information:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhetskopiering/återställning överväganden för SAP ASE
När du distribuerar SAP ASE Azure måste metoder för säkerhetskopiering granskas. Även om systemet inte är en produktiv system måste SAP-databaser som hanteras av SAP ASE säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga i jämfört med kompenserande en krasch för lagring. Den främsta orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en nivå 2 SAP-konfiguration till en installation av system för 3 nivåer i samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som på plats. Se SAP Notes:

* [1588316]
* [1585981]

Mer information om skapa dump konfigurationer och schemaläggning säkerhetskopieringar. Beroende på din strategi och dina behov kan du konfigurera Dumpar databasen och loggfiler disken till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhetskopiering. För att minska risken för dataförlust om ett fel, rekommenderar vi att du använder en disk där det finns ingen directory/databasfil.

Förutom data- och LOB erbjuder komprimering SAP ASE även komprimering av säkerhetskopior. Om du vill tar upp mindre utrymme med databasen och loggfiler Dumpar rekommenderar vi att du använder komprimering av säkerhetskopior. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att hämta säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till en lokal.

Använd inte den virtuella Azure-datorn temp utrymme /mnt eller /mnt/resource som databas eller logg dump mål.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Linux programvaru-RAID diskar) att skriva säkerhetskopian till den lägsta dataflödet

Så här ökar antalet mål att skriva till det finns två alternativ, vilket kan vara används/kombineras beroende på dina behov:

* Strimling säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS dataflödet på stripe volymen
* Skapar en dump-konfigurationen på SAP ASE-nivå, som använder mer än en målkatalogen för att skriva dumpen till

En volym Strimling över flera monterade diskar har diskuterats tidigare i den här guiden. Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen på lagringsprocedur sp_config_dump som används för att skapa den dump-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveriberedskap med Azure virtuella datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikering av data med SAP Sybase replikering-Server
Med SAP Sybase replikering Server (SRS) SAP ASE tillhandahåller en varm standby lösning att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar även samma funktioner i en virtuell dator på Azure Virtual Machine-tjänsterna som den lokala.

ASE HADR via SAP replikering Server stöds inte vid denna tidpunkt. Den kan testas med och är i framtiden för Microsoft Azure-plattformar.

## <a name="specifics-to-oracle-database-on-windows"></a>Specifik information skrivs till Oracle-databas på Windows
Oracle-programvara som stöds av Oracle ska köras på Microsoft Windows Hyper-V och Azure. 

Följande allmänna support, den särskilda situationen av SAP-program att använda Oracle-databaser finns även stöd för. Information om namnges i den här delen av dokumentet.

### <a name="oracle-version-support"></a>Stöd för Oracle-Version
Oracle-versioner och motsvarande OS-versioner som stöds för att köra SAP på Oracle på Azure Virtual Machines finns i SAP-kommentar [2039619].

Allmän information om att köra SAP Business Suite på Oracle kan hittas i 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för Oracle-konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration"></a>Storage-konfiguration
Endast en enskild instans som Oracle använder NTFS-formaterade diskar stöds. Alla databasfiler måste vara lagrade på NTFS-filsystemet som är baserade på virtuella hårddiskar eller Managed Disks. De här diskarna är monterade Azure-datorn och baseras på Azure Page BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som Azure Filtjänster:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

är **inte** stöd för Oracle database-filer!

Med hjälp av diskar baserat på Azure Page BLOB Storage eller Managed Disks uppgifterna i det här dokumentet i kapitlet [cachelagring för virtuella datorer och datadiskar] [ dbms-guide-2.1] och [Microsoft Azure Storage] [ dbms-guide-2.3] gäller för distributioner med Oracle-databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS-dataflödet för Azure-diskar. De exakta kvoterna används beroende på vilken typ av virtuell dator. En lista över typer av virtuella datorer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

För att identifiera de Azure VM-typerna som stöds, se SAP-kommentar [1928533].

Så länge som den aktuella kvoten för IOPS per disk uppfyller kraven, är det möjligt att lagra alla DB-filer på en enda monterade disken. 

Om fler IOPS krävs, rekommenderas att använda fönstret lagringspooler (endast tillgängligt i Windows Server 2012 och senare) eller Windows Strimling för Windows 2008 R2 att skapa en stor logisk enhet över flera monterade diskar (Se även kapitel [ Programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet). Den här metoden förenklar omkostnader för administration för att hantera hur mycket diskutrymme och undviker att manuellt distribuera filer över flera monterade diskar.

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
För säkerhetskopiering / återställningsfunktionen, SAP-BR * verktyg för Oracle stöds på samma sätt som på standard-Windows Server-operativsystem och Hyper-V. Oracle Recovery Manager (RMAN) stöds också för säkerhetskopiering till disk och återställa från disken.

#### <a name="high-availability"></a>Hög tillgänglighet
Oracle Data Guard har stöd för hög tillgänglighet och katastrofåterställning. Information finns i [detta] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentation.

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller enligt beskrivningen i de första tre kapitlen i det här dokumentet för distributioner av virtuella datorer med Oracle-databasen.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Specifik information skrivs till Oracle-databas i Oracle Linux
Oracle-programvara som stöds av Oracle ska köras på Microsoft Windows Hyper-V och Azure. 

Följande allmänna support, den särskilda situationen av SAP-program att använda Oracle-databaser finns även stöd för. Information om namnges i den här delen av dokumentet.

### <a name="oracle-version-support"></a>Stöd för Oracle-Version
Oracle-versioner och motsvarande OS-versioner som stöds för att köra SAP på Oracle på Azure Virtual Machines finns i SAP-kommentar [2039619].

Allmän information om att köra SAP Business Suite på Oracle kan hittas i 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för Oracle-konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration"></a>Storage-konfiguration
Endast stöds enskild instans Oracle med ext3, ext4 och xfs formaterade diskar. Alla databasfiler måste lagras på dessa filsystem baserat på VHD: er eller Managed Disks. De här diskarna är monterade Azure-datorn och baseras på Azure Page BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som Azure Filtjänster:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

är **inte** stöd för Oracle database-filer!

Med hjälp av diskar baserat på Azure Page BLOB Storage eller Managed Disks uppgifterna i det här dokumentet i kapitlet [cachelagring för virtuella datorer och datadiskar] [ dbms-guide-2.1] och [Microsoft Azure Storage] [ dbms-guide-2.3] gäller för distributioner med Oracle-databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS-dataflödet för Azure-diskar. De exakta kvoterna används beroende på vilken typ av virtuell dator. En lista över typer av virtuella datorer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

För att identifiera de Azure VM-typerna som stöds, se SAP-kommentar [1928533]

Så länge som den aktuella kvoten för IOPS per disk uppfyller kraven, är det möjligt att lagra alla DB-filer på en enda monterade disken. 

Om fler IOPS krävs, rekommenderas att använda LVM (Logical Volume Manager) eller MDADM för att skapa en stor logisk volym över flera monterade diskar. Se även kapitel [programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet. Den här metoden förenklar omkostnader för administration för att hantera hur mycket diskutrymme och undviker att manuellt distribuera filer över flera monterade diskar.

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
För säkerhetskopiering / återställningsfunktionen, SAP-BR * verktyg för Oracle stöds på samma sätt som på datorer utan operativsystem och Hyper-V. Oracle Recovery Manager (RMAN) stöds också för säkerhetskopiering till disk och återställa från disken.

#### <a name="high-availability"></a>Hög tillgänglighet
Oracle Data Guard har stöd för hög tillgänglighet och katastrofåterställning. Information finns i [detta] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentation.

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller enligt beskrivningen i de första tre kapitlen i det här dokumentet för distributioner av virtuella datorer med Oracle-databasen.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Informationen för för SAP MaxDB databasen på Windows
### <a name="sap-maxdb-version-support"></a>Stöd för SAP-MaxDB Version
SAP för närvarande stöder SAP MaxDB version 7,9 för användning med SAP NetWeaver-baserade produkter i Azure. Alla uppdateringar för SAP MaxDB eller JDBC och ODBC-drivrutiner som ska användas med SAP NetWeaver-baserade produkter sker enbart via SAP Service Marketplace på <https://support.sap.com/swdc>.
Allmän information om hur du kör SAP NetWeaver på SAP MaxDB finns på <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP MaxDB DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP MaxDB DBMS på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows, vilket är Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Tillgängliga SAP MaxDB dokumentation
Du hittar den uppdaterade listan med SAP MaxDB dokumentation i följande SAP-kommentar [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP MaxDB konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Lagringskonfiguration
Metodtips för Azure storage för SAP MaxDB följer du allmänna rekommendationer som nämns i kapitlet [strukturen i en RDBMS distribution][dbms-guide-2].

> [!IMPORTANT]
> Liksom andra databaser har SAP MaxDB också data och loggfiler. I SAP MaxDB terminologi är dock korrekt termen ”volym” (inte ”fil”). Det finns till exempel SAP MaxDB datavolymer och loggvolymerna. Blanda inte ihop dem med OS-diskvolymer. 
> 
> 

Kort sagt behöver du:

* Om du använder Azure Storage-konton, anger du Azure storage-kontot som innehåller SAP MaxDB data och loggvolymer (d.v.s. filer) till **lokalt Redundant lagring (LRS)** som angetts i kapitlet [Microsoft Azure Storage][dbms-guide-2.3].
* Separata i/o-sökväg för SAP MaxDB datavolymer (d.v.s. filer) från den i/o-sökvägen för loggvolymerna (t.ex. filer). Det innebär att SAP MaxDB datavolymer (d.v.s. filer) måste installeras på en logisk enhet och SAP MaxDB loggvolymerna (d.v.s. filer) måste installeras på en annan logisk enhet.
* Ange rätt cachelagringstyp för varje disk, beroende på om du använder för SAP MaxDB data- eller loggfilen volymer (t.ex. filer) och om du använder Azure Standard- eller Azure Premium Storage, enligt beskrivningen i kapitlet [cachelagring för virtuella datorer och datadiskar] [dbms-guide-2.1].
* Så länge som den aktuella kvoten för IOPS per disk uppfyller kraven, är det möjligt att lagra alla datavolymer på en enda monterade disken och även lagra alla loggvolymerna för databasen på en annan enkel monterade disken.
* Om det krävs mer IOPS och/eller blanksteg, rekommenderar vi att du använder Microsoft fönstret lagringspooler (endast tillgängligt i Microsoft Windows Server 2012 och senare) eller Microsoft Windows striping för Microsoft Windows 2008 R2 för att skapa en stor logisk enhet över flera monterade diskar. Se även kapitel [programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet. Den här metoden förenklar omkostnader för administration för att hantera hur mycket diskutrymme och på så sätt undviker arbetet med att manuellt distribuera filer över flera monterade diskar.
* För de högsta IOPS-kraven, kan du använda Azure Premium Storage, som finns tillgängligt på DS-serien och GS-serien virtuella datorer.

![För referenskonfiguration av virtuell Azure IaaS-dator för SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Säkerhetskopiering och återställning
När du distribuerar SAP MaxDB till Azure måste du granska dina metoder för säkerhetskopiering. Även om systemet inte är en produktiv system måste SAP-databaser som hanteras av SAP MaxDB säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga för att skydda datorn mot storage problem och viktigare operativa eller administrativa fel. Den främsta orsaken för att bibehålla en korrekt säkerhetskopiering och återställning plan är så att du kan kompensera för logiska eller manuella fel genom att tillhandahålla återställningsfunktioner för point-in-time. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt eller att använda säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en nivå 2 SAP-konfiguration till en installation av system för 3 nivåer i samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som för lokala system, så du kan använda standard SAP MaxDB säkerhetskopiering/återställning verktygen som beskrivs i en av de SAP MaxDB dokumentation dokument som anges i SAP-kommentar [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestandaöverväganden för säkerhetskopiering och återställning
Säkerhetskopiering och återställning prestanda är beroende på hur många volymer kan läsas parallellt, och genomströmning på volymerna som bare metal-distributioner. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen enheterna, lägre övergripande läsgenomströmning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till längst ned dataflödet

Om du vill öka antalet mål att skriva till finns det två alternativ som du kan använda, eventuellt i kombination, beroende på dina behov:

* Tilldela separata volymer för säkerhetskopiering
* Strimling säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS dataflödet på volymen stripe-disk
* Att ha separata dedikerade logiska diskenheter för:
  * SAP MaxDB säkerhetskopieringsvolymerna (d.v.s. filer)
  * SAP MaxDB datavolymer (d.v.s. filer)
  * SAP MaxDB loggvolymerna (d.v.s. filer)

En volym Strimling över flera monterade diskar har diskuterats tidigare i kapitlet [programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Andra
Andra allmänna områden, till exempel Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller även enligt beskrivningen i de första tre kapitlen i det här dokumentet för distributioner av virtuella datorer med SAP MaxDB-databasen.
Andra SAP MaxDB-specifika inställningar är transparent för virtuella Azure-datorer och beskrivs i olika dokument som anges i SAP-kommentar [767598] och i de här SAP notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Informationen för för SAP liveCache på Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache-Versionsstöd
Minimal version av SAP liveCache stöds i Azure Virtual Machines är **SAP LC/LCAPPS 10.0 SP 25** inklusive **liveCache 7.9.08.31** och **LCA-Build 25**, som släpptes för **EhP 2 för SAP SCM 7.0** och högre.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP liveCache DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP liveCache på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache riktlinjerna för SAP-installationer i virtuella Azure-datorer
#### <a name="recommended-azure-vm-types"></a>Rekommenderade Azure VM-typer
Har en stor påverkan på prestanda för SAP-liveCache som SAP liveCache är ett program som utför stora beräkningar, den mängd och hastighet för RAM-minne och CPU. 

För Azure VM-typer som stöds av SAP (SAP-kommentar [1928533]), alla virtuella CPU-resurser allokeras till den virtuella datorn backas upp av dedikerade fysisk CPU-resurser i hypervisor-programmet. Ingen överetablering (och därför ingen konkurrens om processorresurser) sker.

På samma sätt för alla Virtuella Azure-instanstyper som stöds av SAP, är VM-minne 100% mappad till det fysiska minnet - överetablering (över åtagande), till exempel inte används.

Från det här perspektivet rekommenderas att använda den nya typen för D-serien och DS-serien (i kombination med Azure Premium Storage) virtuell Azure-dator som de har 60% snabbare processorer än A-serien. För den högsta RAM-minne och CPU-belastningen kan du använda G-serien och GS-serien (i kombination med Azure Premium Storage) virtuella datorer med den senaste Intel rubrikrad? Xeon rubrikrad? processorn E5 v3, som har två gånger med minne och fyra gånger solid state drive-lagringsutrymme (solid-state drive) D/DS-serien.

#### <a name="storage-configuration"></a>Lagringskonfiguration
Eftersom SAP liveCache är baserad på SAP MaxDB teknik, Azure storage bästa metoderna för SAP MaxDB enligt kapitel [lagringskonfiguration] [ dbms-guide-8.4.1] gäller också för SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedikerad virtuell Azure-dator för liveCache
Eftersom SAP liveCache använder sig mycket dataresurser, för produktiva användning rekommenderas att distribuera på en dedikerad Azure-dator. 

![Reserverad Azure VM för liveCache för produktiva användningsfall][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Säkerhetskopiera och återställ
säkerhetskopiering och återställning, inklusive prestandaöverväganden, som beskrivs i de relevanta SAP MaxDB kapitlen [säkerhetskopierar och återställer] [ dbms-guide-8.4.2] och [prestandaöverväganden för säkerhetskopiering och återställa][dbms-guide-8.4.3]. 

#### <a name="other"></a>Annat
Andra allmänna områden redan beskrivs i den relevanta SAP-MaxDB [detta] [ dbms-guide-8.4.4] kapitel. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Informationen för för SAP innehållsservern på Windows
SAP Content Server är en separat, server-baserade komponent för att lagra innehåll, till exempel elektroniska dokument i olika format. Innehållsservern SAP tillhandahålls av utvecklingen av teknik och ska använda cross-programmet för alla SAP-program. Den är installerad på ett separat system. Vanliga innehållet är utbildningsmaterial och dokumentation från Knowledge datalager eller tekniska ritningar från mySAP PLM dokumenthanteringssystemet. 

### <a name="sap-content-server-version-support"></a>Stöd för SAP Content Server-Version
SAP stöder för närvarande:

* **SAP innehållsserver** med version **6.50 (och senare)**
* **SAP MaxDB version 7,9**
* **Microsoft IIS (Internet Information Server) version 8.0 (och senare)**

Vi rekommenderar starkt att använda den senaste versionen av SAP innehållsserver som vid tidpunkten för att skriva det här dokumentet är **6.50 SP4**, och den senaste versionen av **Microsoft IIS 8.5**. 

Kontrollera de senaste versionerna som stöds av SAP Content Server och Microsoft IIS i den [SAP produkten tillgänglighet matris (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Microsoft Windows och Virtuella Azure-typer som stöds för SAP Content Server
Om du vill ta reda på Windows-version som stöds för SAP Content Server på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP innehållsserver konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Om du konfigurerar SAP Content Server för att lagra filer i databasen SAP MaxDB alla Azure storage som bästa praxis rekommendation för SAP MaxDB enligt kapitel [lagringskonfiguration] [ dbms-guide-8.4.1] kan användas för scenariot med SAP-innehållsserver. 

Om du konfigurerar SAP Content Server för att lagra filer i filsystemet, rekommenderas att använda en dedikerad logisk enhet. Med lagringsutrymmen för Windows kan du även öka storleken för logisk disk och IOPS, dataflöde, enligt beskrivningen i kapitlet [programvaru-RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>SAP innehållsserver plats
SAP innehållsserver måste distribueras i samma Azure-region och virtuella Azure-nätverket där SAP-system har distribuerats. Du kan bestämma om du vill distribuera SAP Content Server-komponenter på en dedikerad virtuell Azure-dator eller på samma virtuella dator där du kör SAP-system. 

![Reserverad Azure VM för SAP innehållsserver][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP cacheplats för Server
SAP Cache-Server är en ytterligare serverbaserade komponent att ge åtkomst till (cachelagrade) lokalt. SAP-Cacheservern cachelagrar dokument för en SAP-innehållsserver. Det här är att optimera nätverkstrafik om dokument som har mer än en gång hämtas från olika platser. Generellt gäller att SAP Cache-Server måste vara fysiskt nära den klient som ansluter till SAP Cache-Server. 

Här har du två alternativ:

1. **Klienten är ett serverdelsystem SAP** om en serverdel SAP-system är konfigurerad för att komma åt SAP innehållsserver, det SAP-systemet är en klient. Eftersom både SAP-system och SAP Content Server distribueras i samma Azure-region, i samma Azure-datacentret, är de fysiskt nära varandra. Det är därför behöver du inte har en dedikerad SAP-cacheserver. SAP-UI-klienter (SAP-Gränssnittet eller web browser) direktåtkomst till SAP-system och SAP-system hämtar dokument från innehållsservern SAP.
2. **Klienten är en lokal webbläsare** The SAP innehållsserver kan konfigureras för att användas direkt av webbläsaren. I det här fallet är en webbläsare som körs lokalt en klient för SAP-innehållsserver. Lokala datacenter och Azure-datacenter är placerade i olika fysiska platser (helst nära varandra). Ditt lokala datacenter är ansluten till Azure via Azure plats-till-plats-VPN eller ExpressRoute. Även om båda alternativen erbjuder säker anslutning för VPN-nätverk till Azure, erbjuder nätverk plats-till-plats-anslutning inte SLA för bandbredd och latens en nätverk mellan datacenter på plats och Azure-datacentret. För att påskynda åtkomst till dokument, kan du göra något av följande:
   1. Installera SAP Cache Server lokalt, Stäng för att lokalt webbläsaren (alternativet på [detta] [ dbms-guide-900-sap-cache-server-on-premises] bild)
   2. Konfigurera Azure ExpressRoute, som erbjuder en hög hastighet och låg latens dedikerad nätverksanslutning mellan lokala datacenter och Azure-datacenter.

![Alternativet för att installera SAP Cache Server lokalt][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
Om du konfigurerar SAP innehållsservern för att lagra filer i databasen SAP MaxDB procedur- och prestandaöverväganden för säkerhetskopiering/återställning som beskrivs i SAP MaxDB kapitel [säkerhetskopierar och återställer] [ dbms-guide-8.4.2]och kapitel [prestandaöverväganden för säkerhetskopiering och återställning][dbms-guide-8.4.3]. 

Om du konfigurerar SAP innehållsservern för att lagra filer i filsystemet, är ett alternativ att köra en manuell säkerhetskopiering/återställning av hela filen-struktur där dokumenten finns. Liknar SAP MaxDB säkerhetskopiering/återställning, rekommenderar vi att du har en dedikerad volym för säkerhetskopiering ändamål. 

#### <a name="other"></a>Annat
Andra innehåll till SAP-serverspecifika inställningar är transparent för virtuella Azure-datorer och beskrivs i olika dokument och SAP Notes:

* <https://service.sap.com/contentserver> 
* SAP-kommentar [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Specifik information skrivs till IBM DB2 för LUW på Windows
Med Microsoft Azure kan migrera du enkelt dina befintliga SAP-program som körs på IBM DB2 för Linux, UNIX- och Windows (LUW) till Azure-datorer. Med SAP på IBM DB2 för LUW kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration, som är tillgängligt lokalt.
Allmän information om att köra SAP Business Suite på IBM DB2 för LUW finns i SAP Community nätverk (SCN) på <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Ytterligare information och uppdateringar om SAP på DB2 för LUW på Azure finns i SAP-kommentar [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 för Linux, UNIX och stöd för Windows-Version
SAP på IBM DB2 för LUW på Microsoft Azure Virtual Machine-tjänsterna stöds från och med DB2 version 10,5.

Information om SAP-produkter som stöds och Azure VM-typer i SAP-kommentar [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 för Linux, UNIX och riktlinjer för Windows-konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Alla databasfiler måste vara lagrade på NTFS-filsystemet som är baserat på direkt anslutna diskar. Diskarna är monterade Azure-datorn och baseras på Azure Page BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som följande Azure Filtjänster **inte** stöds för databasfiler: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Om du använder diskar baserat på Azure Page BLOB Storage eller Managed Disks, instruktionerna som gjorts i det här dokumentet i kapitlet [strukturen i en RDBMS distribution] [ dbms-guide-2] gäller även för distributioner med IBM DB2 för LUW -Databasen. 

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter för IOPS genomströmning för diskar. De exakta kvoterna är beroende av den typ av virtuell dator som används. En lista över typer av virtuella datorer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Så länge som den aktuella kvoten för IOPS per disk räcker är det möjligt att lagra alla databasfiler på en enda monterade disken. 

Prestanda finns överväganden också i kapitlet ”datasäkerheten och prestandaöverväganden för databaskataloger” i SAP-installationsguiderna.

Alternativt kan du använda lagringspooler för Windows (endast tillgängligt i Windows Server 2012 och senare) eller Windows striping för Windows 2008 R2 som beskrivs i kapitlet [programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet till Skapa en stor logisk enhet över flera diskar.
För diskar som innehåller sökvägar för DB2-lagring för dina sapdata och saptmp kataloger, måste du ange en fysisk sektorstorlek på 512 KB. När du använder lagringspooler för Windows, måste du skapa lagringspooler manuellt via kommandoradsgränssnittet med hjälp av parametern `-LogicalSectorSizeDefault`. Mer information finns i <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Säkerhetskopiera/återställa
Funktionen säkerhetskopiering/återställning för IBM DB2 för LUW stöds på samma sätt som på standard-Windows Server-operativsystem och Hyper-V.

Du måste se till att du har en giltig databas strategi för säkerhetskopiering på plats. 

Som i bare metal-distribution beror säkerhetskopiering/återställning prestanda på hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till längst ned dataflödet

Om du vill öka antalet mål att skriva till vara två alternativ används/kombineras beroende på dina behov:

* Strimling säkerhetskopiering målvolymen över flera diskar för att kunna förbättra IOPS dataflödet på stripe volymen
* Använda mer än en målkatalogen för att skriva säkerhetskopiering till

#### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och katastrofåterställning
Microsoft Cluster Server (MSCS) stöds inte.

Hög tillgänglighet för DB2-katastrofåterställning (HADR) stöds. Om de virtuella datorerna i konfigurationen för hög tillgänglighet har arbeta namnmatchning, skiljer inte inställningarna i Azure från några inställningar som görs på plats. Du bör inte förlita dig på endast IP-lösning.

Använd inte Geo-replikering för storage-konton som lagrar databasen diskarna. Mer information finns i kapitlet [Microsoft Azure Storage] [ dbms-guide-2.3] och kapitel [hög tillgänglighet och Haveriberedskap med Azure virtuella datorer] [ dbms-guide-3].

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller enligt beskrivningen i de första tre kapitlen i det här dokumentet för distributioner av virtuella datorer med IBM DB2 för LUW samt. 

Även i kapitlet [allmänna SQL Server för SAP på Azure-sammanfattning][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Specifik information skrivs till IBM DB2 för LUW på Linux
Med Microsoft Azure kan migrera du enkelt dina befintliga SAP-program som körs på IBM DB2 för Linux, UNIX- och Windows (LUW) till Azure-datorer. Med SAP på IBM DB2 för LUW kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration, som är tillgängligt lokalt. Allmän information om att köra SAP Business Suite på IBM DB2 för LUW finns i SAP Community nätverk (SCN) på <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Ytterligare information och uppdateringar om SAP på DB2 för LUW på Azure finns i SAP-kommentar [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 för Linux, UNIX och stöd för Windows-Version
SAP på IBM DB2 för LUW på Microsoft Azure Virtual Machine-tjänsterna stöds från och med DB2 version 10,5.

Information om SAP-produkter som stöds och Azure VM-typer i SAP-kommentar [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 för Linux, UNIX och riktlinjer för Windows-konfiguration för SAP-installationer i Azure virtuella datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Alla databasfiler måste lagras i ett filsystem som baseras på direkt anslutna diskar. Diskarna är monterade Azure-datorn och baseras på Azure Page BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som följande Azure Filtjänster **inte** stöds för databasfiler:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Om du använder diskar baserat på Azure Page BLOB Storage, uppgifterna i det här dokumentet i kapitlet [strukturen i en RDBMS distribution] [ dbms-guide-2] gäller även för distributioner med IBM DB2 för LUW databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter för IOPS genomströmning för diskar. De exakta kvoterna är beroende av den typ av virtuell dator som används. En lista över typer av virtuella datorer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Så länge som den aktuella kvoten för IOPS per disk räcker är det möjligt att lagra alla databasfiler på en enda disk.

Prestanda finns överväganden också i kapitlet ”datasäkerheten och prestandaöverväganden för databaskataloger” i SAP-installationsguiderna.

Du kan också använda LVM (Logical Volume Manager) eller MDADM enligt kapitel [programvaru-RAID] [ dbms-guide-2.2] i det här dokumentet för att skapa en stor logisk enhet över flera diskar.
För diskar som innehåller sökvägar för DB2-lagring för dina sapdata och saptmp kataloger, måste du ange en fysisk sektorstorlek på 512 KB.

#### <a name="backuprestore"></a>Säkerhetskopiera/återställa
Funktionen säkerhetskopiering/återställning för IBM DB2 för LUW stöds på samma sätt som på standard Linux installation lokalt.

Du måste se till att du har en giltig databas strategi för säkerhetskopiering på plats.

Som i bare metal-distribution beror säkerhetskopiering/återställning prestanda på hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara. CPU-förbrukning som används av komprimering av säkerhetskopior kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan kan en anta:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till längst ned dataflödet

Om du vill öka antalet mål att skriva till vara två alternativ används/kombineras beroende på dina behov:

* Strimling säkerhetskopiering målvolymen över flera diskar för att kunna förbättra IOPS dataflödet på stripe volymen
* Använda mer än en målkatalogen för att skriva säkerhetskopiering till

#### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och katastrofåterställning
Hög tillgänglighet för DB2-katastrofåterställning (HADR) stöds. Om de virtuella datorerna i konfigurationen för hög tillgänglighet har arbeta namnmatchning, skiljer inte inställningarna i Azure från några inställningar som görs på plats. Du bör inte förlita dig på endast IP-lösning.

Använd inte Geo-replikering för storage-konton som lagrar databasen diskarna. Mer information finns i kapitlet [Microsoft Azure Storage] [ dbms-guide-2.3] och kapitel [hög tillgänglighet och Haveriberedskap med Azure virtuella datorer] [ dbms-guide-3].

#### <a name="other"></a>Annat
Alla andra allmänna ämnen som Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller enligt beskrivningen i de första tre kapitlen i det här dokumentet för distributioner av virtuella datorer med IBM DB2 för LUW samt.

Även i kapitlet [allmänna SQL Server för SAP på Azure-sammanfattning][dbms-guide-5.8].

