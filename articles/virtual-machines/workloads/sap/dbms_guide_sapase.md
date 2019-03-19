---
title: SAP ASE Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar | Microsoft Docs
description: SAP ASE Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9c37c5339dedb3db68ed9bd23cd28e16a195bbde
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005778"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ASE Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
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



I det här dokumentet beskriver flera olika områden att tänka på när du distribuerar SAP ASE i Azure IaaS. Som ett villkor för det här dokumentet, bör du ha läsa dokumentet [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md) och andra guider i den [SAP-arbetsbelastningar på Azure-dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Specifik information skrivs till SAP ASE på Windows
Från och med Microsoft Azure kan migrera du dina befintliga SAP ASE-program till Azure Virtual Machines. SAP ASE i en Azure virtuell dator kan du minska den totala ägandekostnaden distribution, hantering och underhåll av bredden företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en Azure virtuell dator, kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration som är tillgängligt lokalt.

Serviceavtal för Azure Virtual Machines finns här: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure erbjuder flera olika typer av annan virtuell dator så att du kan köra minsta SAP-system och landskap upp till stora SAP-system och landskap med tusentals användare. SAP som ändrar storlek på SAP många olika SAP-certifierad VM SKU: er finns i SAP-kommentar [1928533].

Instruktioner och rekommendationer om att användningen av Azure Storage, distribution av SAP virtuella datorer eller SAP övervakning i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md) avser distributioner av SAP ASE för.

### <a name="sap-ase-version-support"></a>SAP ASE-Versionsstöd
SAP för närvarande stöder SAP ASE version 16,0 för användning med SAP Business Suite-produkter. Alla uppdateringar för SAP ASE eller JDBC och ODBC-drivrutiner som ska användas med SAP Business Suite produkter sker enbart via SAP Service Marketplace på: <https://support.sap.com/swdc>.

Hämta inte uppdateringarna till SAP ASE-server, JDBC och ODBC-drivrutiner eller direkt från Sybase-webbplatser. Detaljerad information om korrigeringarna som stöds för användning med SAP-produkter lokalt och i Azure virtuella datorer finns i följande SAP-information:

* [1590719]
* [1973241]

Allmän information om hur du kör SAP Business Suite på SAP ASE finns i den [Tillståndsändringsavisering](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Riktlinjer för SAP ASE konfiguration för SAP-relaterade SAP ASE installationer i Azure virtuella datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Strukturen för SAP ASE-distribution
SAP ASE körbara filer bör finns eller installerat till enheten av den Virtuella datorns OS-disk (enhet c:\). De flesta av databaser för SAP ASE system och verktyg som normalt inte har hög arbetsbelastning. Därför kan system och verktyg databaserna (master, model, saptools, sybmgmtdb, sybsystemdb) förbli på enhet C:\. 

Ett undantag kan vara tillfälliga databasen, vilket vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolymen eller volymen för i/o-åtgärder. Volymer eller IOPS som kan tillhandahållas med den Virtuella datorns OS-disken (enhet C:\).

Beroende på vilken version av SAPInst/SWPM används för att installera, SAP ASE instanskonfiguration kan se ut:

* En enda SAP ASE tempdb som skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följa SAP-kommentar [1752266]) att uppfylla kraven för ERP/BW specifika tempdb

Av prestandaskäl för specifika ERP eller alla BW arbetsbelastningar, kan det vara klokt att lagra dessutom skapade tempdb av tempdb-enheter på en annan enhet än C:\. Om det finns inga ytterligare tempdb, rekommenderar vi att du skapar ett (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-enheten till den första enheten för SAP-databas
* Lägga till tempdb-enheter till var och en av de virtuella hårddiskarna som innehåller en enhet av SAP-databas

Den här konfigurationen möjliggör tempdb förbruka mer utrymme än vad som systemenheten. Som referens hittar en tempdb-enheten storlek på befintliga system som körs lokalt. Eller en sådan konfiguration gör det möjligt för IOPS-siffror mot tempdb, vilket inte kan tillhandahållas med enheten. System som körs lokalt kan användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera aldrig några SAP ASE-enheter på D:\ enhet för den virtuella datorn. För SAP ASE gäller det här rådet även för tempdb, även om de objekt som sparas i tempdb är temporära.

För data och transaction log file distributioner, instruktioner och förslag som gjorts i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). I fallet med Windows-baserade distributioner bör användningen av Windows-lagringsutrymmen använda för att skapa stripe-uppsättningar med tillräckligt med IOPS, dataflöde och volym.  

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att tillämpa komprimering innan du laddar upp till Azure ges av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Data - och LOB-komprimering fungerar i en virtuell dator som finns i Azure virtuella datorer som den finns på plats. För mer information om hur du kontrollerar om komprimering finns redan i använda i en befintlig databas för SAP ASE, kontrollerar SAP-kommentar [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Database-instanser
För SAP-system som använder SAP ASE som plattform, är DBACockpit tillgänglig som embedded webbläsarfönster i transaktionen DBACockpit eller Webdynpro. Men den fullständiga funktionaliteten för att övervaka och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut som:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

och länkar som genereras i transaktionen DBACockpit liknar:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på hur den som är värd för SAP-system i Azure-datorn är ansluten till din AD och DNS kan du behöva se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du öppnar DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger den fullständigt kvalificerade värdnamn baserat på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med molnbaserad utan korsanslutningar mellan lokala och Azure som du behöver definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut som:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information som rör DNS-namn finns [här][virtual-machines-azurerm-versus-azuresm].

DNS-namnet på Azure VM länken för att ställa in SAP profil parametern icm/host_name_full kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

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
När du distribuerar SAP ASE till Azure, måste metoder för säkerhetskopiering granskas. Även för icke-produktionssystem, måste SAP-databaserna säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, kan en säkerhetskopia vara mindre viktiga i jämfört med kompenserande en krasch för lagring. Den främsta orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som på plats. Se SAP Notes:

* [1588316]
* [1585981]

Mer information om skapa dump konfigurationer och schemaläggning säkerhetskopieringar. Beroende på din strategi och dina behov kan du konfigurera databasen och loggfiler minnesdumpar till disk på ett av de befintliga diskarna eller Lägg till ytterligare en disk för säkerhetskopiering. För att minska risken för dataförlust om ett fel, rekommenderar vi att du använder en disk där det finns inga databasfiler.

Förutom data- och LOB-komprimering erbjuder även SAP ASE komprimering av säkerhetskopior. Om du vill använda mindre utrymme med databasen och loggfiler Dumpar rekommenderar vi att du använder komprimering av säkerhetskopior. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att hämta säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till en lokal.

Använd inte enhet D:\ som databas eller logg dump-mål.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara som bare metal-distributioner. Tänk på att säkerhetskopiera komprimering förbrukar CPU-resurser. Den här processorförbrukningen för komprimering av säkerhetskopior kan spela upp en viktig roll på virtuella datorer med litet antal CPU-trådar. Därför kan du anta att:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till den lägsta dataflödet

Så här ökar antalet mål att skriva till det finns två alternativ, vilket kan vara används/kombineras beroende på dina behov:

* Säkerhetskopiering målvolymen Strimling över flera monterade diskar för att förbättra IOPS dataflödet på stripe volymen
* Skapar en dump-konfigurationen på SAP ASE-nivå, som använder mer än en målkatalogen för att skriva dumpen till

En volym Strimling över flera monterade diskar har diskuterats i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen på lagringsprocedur sp_config_dump som används för att skapa den dump-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveriberedskap med Azure virtuella datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikering av data med SAP Sybase replikering-Server
Med den SAP Sybase replikering Server (SRS), är SAP ASE en varm standby lösning för att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar även samma funktioner i en virtuell dator på Azure Virtual Machine-tjänsterna som den lokala.

SAP ASE HADR kräver inte en intern belastningsutjämnare i Azure och har inga beroenden på nivån OS-klustring. Det fungerar på Azure Windows och Linux-datorer. Information om SAP ASE HADR finns det [SAP ASE HADR användarhandboken](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specifik information skrivs till SAP ASE i Linux
Från och med Microsoft Azure kan migrera du enkelt dina befintliga SAP ASE-program till Azure Virtual Machines. SAP ASE på en virtuell dator kan du minska den totala ägandekostnaden distribution, hantering och underhåll av bredden företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en Azure virtuell dator, kan administratörer och utvecklare fortfarande använda samma utvecklings- och verktyg för fjärrserveradministration som är tillgängligt lokalt.

För att distribuera virtuella datorer i Azure är det viktigt att veta att officiella serviceavtalen som finns här: <https://azure.microsoft.com/support/legal/sla>

Information för SAP-storlek och en lista med SAP-certifierade VM SKU: er tillhandahålls i SAP-kommentar [1928533]. Ytterligare SAP storlek dokument för virtuella datorer med Azure finns här <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> och här <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruktioner och rekommendationer om till användningen av Azure Storage, distribution av SAP-datorer eller SAP övervakning som gäller för distributioner av SAP ASE tillsammans med SAP-program som anges under de första fyra kapitlen i det här dokumentet.

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
SAP ASE körbara filer bör finns eller har installerats i filsystemet roten för den virtuella datorn (/sybase). De flesta av databaser för SAP ASE system och verktyg som normalt inte har hög arbetsbelastning. System- och verktyg-databaser (master, model, saptools, sybmgmtdb, sybsystemdb) kan därför lagras på filsystemet rot. 

Ett undantag kan vara tillfälliga databasen, vilket vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolymen eller volymen för i/o-åtgärder. Volymer eller IOPS som kan tillhandahållas med den Virtuella datorns OS-disk 

Beroende på vilken version av SAPInst/SWPM används för att installera innehålla databasen:

* En enda SAP ASE tempdb skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följa SAP-kommentar [1752266]) att uppfylla kraven för ERP/BW specifika tempdb

Av prestandaskäl för specifika ERP eller alla BW arbetsbelastningar kan vara det klokt att lagra av tempdb-enheter dessutom skapade tempdb (via SWPM eller manuellt) på en separat filsystem, som representeras av en enda Azure-data-disk eller en Linux-RAID utsträckning m flera Azure-datadiskar. Om det finns inga ytterligare tempdb, rekommenderar vi att du skapar ett (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-katalogen till första filsystemet för SAP-databas
* Lägg till tempdb kataloger i var och en av diskarna som innehåller ett filsystem för SAP-databas

Den här konfigurationen möjliggör tempdb förbruka mer utrymme än vad som systemenheten. Som referens hittar en tempdb-enheten storlek på befintliga system som körs lokalt. Eller en sådan konfiguration gör det möjligt för IOPS-siffror mot tempdb, vilket inte kan tillhandahållas med enheten. System som körs lokalt kan användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera inte alla SAP ASE kataloger till /mnt eller /mnt/resource för den virtuella datorn. För SAP ASE gäller det här rådet även för tempdb, även om de objekt som sparas i tempdb är temporära. Eftersom /mnt eller /mnt/resource är en standard virtuell Azure-dator tillfällig plats, som inte är beständig. Mer information om virtuella Azure-datorn temp utrymme finns i [i den här artikeln][virtual-machines-linux-how-to-attach-disk]

För data och transaction log file distributioner, instruktioner och förslag som gjorts i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). När det gäller Linux-baserade distributioner bör användningen av LVM eller MDADM använda för att skapa stripe-uppsättningar med tillräckligt med IOPS, dataflöde och volym. 

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att tillämpa komprimering innan du laddar upp till Azure ges av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Data - och LOB-komprimering fungerar i en virtuell dator som finns i Azure virtuella datorer som den finns på plats. För mer information om hur du kontrollerar om komprimering finns redan i använda i en befintlig databas för SAP ASE, kontrollerar SAP-kommentar [1750510]. Mer information om databasen komprimering, finns i SAP-kommentar [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Database-instanser
För SAP-system som använder SAP ASE som plattform, är DBACockpit tillgänglig som embedded webbläsarfönster i transaktionen DBACockpit eller Webdynpro. Men den fullständiga funktionaliteten för att övervaka och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut så här:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

och länkar som genereras i transaktionen DBACockpit ser ut ungefär så här:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på hur den som är värd för SAP-system i Azure-datorn är ansluten till din AD och DNS kan du behöva se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du öppnar DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger det fullständigt kvalificerade värdnamnet beroende på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med molnbaserad utan korsanslutningar mellan lokala och Azure som du behöver definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut som:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information som rör DNS-namn finns [här][virtual-machines-azurerm-versus-azuresm].

DNS-namnet på Azure VM länken för att ställa in SAP profil parametern icm/host_name_full kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

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
När du distribuerar SAP ASE till Azure, måste metoder för säkerhetskopiering granskas. Även för icke-produktionssystem, måste SAP-databaserna säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, kan en säkerhetskopia vara mindre viktiga i jämfört med kompenserande en krasch för lagring. Den främsta orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som på plats. Se SAP Notes:

* [1588316]
* [1585981]

Mer information om skapa dump konfigurationer och schemaläggning säkerhetskopieringar. Beroende på din strategi och dina behov, kan du konfigurera databasen och loggfiler Dumpar disken in på en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhetskopiering. För att minska risken för dataförlust om ett fel, rekommenderar vi att du använder en disk där det finns ingen directory/databasfil.

Förutom data- och LOB-komprimering erbjuder även SAP ASE komprimering av säkerhetskopior. Om du vill använda mindre utrymme med databasen och loggfiler Dumpar rekommenderar vi att du använder komprimering av säkerhetskopior. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att hämta säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till en lokal.

Använd inte den virtuella Azure-datorn temp utrymme /mnt eller /mnt/resource som databas eller logg dump mål.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vad dataflödet volymer kan vara som bare metal-distributioner. Tänk på att säkerhetskopiera komprimering förbrukar CPU-resurser. Den här processorförbrukningen för komprimering av säkerhetskopior kan spela upp en viktig roll på virtuella datorer med litet antal CPU-trådar.  Därför kan du anta att:

* Färre på antalet diskar som används för att lagra databasen-enheter, desto mindre totala genomflödet i läsning
* Ju mindre antalet trådar i den virtuella datorn, den allvarligare effekten av komprimering av säkerhetskopior
* Färre mål (Linux programvaru-RAID diskar) att skriva säkerhetskopian till den lägsta dataflödet

Så här ökar antalet mål att skriva till det finns två alternativ, vilket kan vara används/kombineras beroende på dina behov:

* Säkerhetskopiering målvolymen Strimling över flera monterade diskar för att förbättra IOPS dataflödet på stripe volymen
* Skapar en dump-konfigurationen på SAP ASE-nivå, som använder mer än en målkatalogen för att skriva dumpen till

En volym Strimling över flera monterade diskar har diskuterats i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md). Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen på lagringsprocedur sp_config_dump som används för att skapa den dump-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveriberedskap med Azure virtuella datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikering av data med SAP Sybase replikering-Server
Med den SAP Sybase replikering Server (SRS), är SAP ASE en varm standby lösning för att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar även samma funktioner i en virtuell dator på Azure Virtual Machine-tjänsterna som den lokala.

ASE HADR via SAP replikering Server stöds. Vi rekommenderar starkt att använda SAP ASE 16.03 för att försöka en sådan konfiguration. Mer detaljerade anvisningar för att installera sådana konfigurationer finns i detalj i detta [blogg](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).