---
title: SQL Server Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft-dokument
description: DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645811"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver

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
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




Det här dokumentet täcker flera olika områden att tänka på när du distribuerar SQL Server för SAP-arbetsbelastning i Azure IaaS. Som en förutsättning för det här dokumentet bör du ha läst dokumentet [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md) samt andra guider i [SAP-arbetsbelastningen på Azure-dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> Dokumentets omfattning är Windows-versionen på SQL Server. SAP stöder inte Linux-versionen av SQL Server med någon av SAP-programvaran. Dokumentet diskuterar inte Microsoft Azure SQL Database, som är ett plattformserbjudande som ett tjänsterbjudande för Microsoft Azure-plattformen. Diskussionen i det här dokumentet handlar om att köra SQL Server-produkten som den kallas för lokala distributioner i Virtuella Azure-datorer, och utnyttja infrastrukturen som en tjänstfunktion i Azure. Databasfunktioner och funktioner mellan dessa två erbjudanden är olika och bör inte blandas ihop med varandra. Se även:<https://azure.microsoft.com/services/sql-database/>
> 
>

I allmänhet bör du överväga att använda de senaste SQL Server-versionerna för att köra SAP-arbetsbelastning i Azure IaaS. De senaste SQL Server-versionerna ger bättre integrering i några av Azure-tjänsterna och -funktionerna. Eller har ändringar som optimerar åtgärder i en Azure IaaS-infrastruktur.

Vi rekommenderar att du granskar [den här][virtual-machines-sql-server-infrastructure-services] dokumentationen innan du fortsätter.

I följande avsnitt sammanställs och nämns delar av delar av dokumentationen under länken ovan. Detaljer kring SAP nämns också och vissa begrepp beskrivs mer i detalj. Det rekommenderas dock starkt att arbeta igenom dokumentationen ovan först innan du läser den SQL Server-specifika dokumentationen.

Det finns några SQL Server i IaaS specifik information du bör veta innan du fortsätter:

* **SQL Version Support**: För SAP-kunder stöds SQL Server 2008 R2 och högre på Microsoft Azure Virtual Machine. Tidigare utgåvor stöds inte. Läs den här allmänna [supportförklaringen](https://support.microsoft.com/kb/956893) för mer information. I allmänhet stöds SQL Server 2008 även av Microsoft. Men på grund av betydande funktioner för SAP, som introducerades med SQL Server 2008 R2, SQL Server 2008 R2 är den minsta versionen för SAP. I allmänhet bör du överväga att använda de senaste SQL Server-versionerna för att köra SAP-arbetsbelastning i Azure IaaS. De senaste SQL Server-versionerna ger bättre integrering i några av Azure-tjänsterna och -funktionerna. Eller har ändringar som optimerar åtgärder i en Azure IaaS-infrastruktur. Därför är papperet begränsat till SQL Server 2016 och SQL Server 2017.
* **SQL-prestanda:** Microsoft Azure-värdbaserade virtuella datorer fungerar bra jämfört med andra offentliga molnvirtualiseringserbjudanden, men enskilda resultat kan variera. Läs artikeln [Prestanda metodtips för SQL Server i Azure Virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Använda avbildningar från Azure Marketplace:** Det snabbaste sättet att distribuera en ny Virtuell Microsoft Azure är att använda en avbildning från Azure Marketplace. Det finns avbildningar på Azure Marketplace, som innehåller de senaste SQL Server-versionerna. Avbildningarna där SQL Server redan är installerat kan inte omedelbart användas för SAP NetWeaver-program. Orsaken är att standard-SQL Server-sorteringen är installerad i dessa avbildningar och inte den sortering som krävs av SAP NetWeaver-system. Om du vill använda sådana avbildningar kontrollerar du stegen som dokumenteras i kapitlet [Använda en SQL Server-avbildning från Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Rekommendationer om VM/VHD-struktur för SAP-relaterade SQL Server-distributioner
I enlighet med den allmänna beskrivningen bör SQL Server-körbara filer placeras eller installeras i systemenheten på den virtuella datorns OS-disk (enhet C:\).  Vanligtvis används de flesta SQL Server-systemdatabaser inte på en hög nivå av SAP NetWeaver-arbetsbelastningen. Det innebär att systemdatabaserna för SQL Server (master, msdb och modell) kan finnas kvar på C:\ även köra. Ett undantag bör vara tempdb, som när det gäller SAP-arbetsbelastningar, kan kräva antingen högre datavolym eller I/O-operationer volym. I/O-arbetsbelastning, som inte ska tillämpas på virtuell hårddisk för operativsystemet. För sådana system bör följande steg utföras:


* Med alla SAP-certifierade VM-typer (se SAP Note [1928533]), utom virtuella datorer i A-serien, tempdb-data och loggfiler kan placeras på den icke-beständiga D:\ Bilresa. 
* Det rekommenderas dock att använda flera tempdb-datafiler. Var medveten om D:\ enhetsvolymerna skiljer sig från den virtuella datorn. För exakta storlekar på D:\ enhet för de olika virtuella datorerna, kontrollera artikeln [Storlekar för Windows virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Dessa konfigurationer gör det möjligt för tempdb att förbruka mer utrymme än systemenheten kan ge. Den icke-beständiga D:\ enheten erbjuder också bättre I/O-svarstid och dataflöde (med undantag för virtuella datorer i A-serien). För att bestämma rätt tempdb storlek, kan du kontrollera tempdb storlekar på befintliga system. 

>[!NOTE]
> om du placerar tempdb datafiler och loggfil i en mapp på D:\ enhet som du har skapat måste du se till att mappen finns efter en omstart av den virtuella datorn. Sedan D:\ enheten är nyligen initierad efter en virtuell dator starta om alla fil-och katalogstrukturer utplånas. En möjlighet att återskapa eventuella katalogstrukturer på D:\ innan SQL Server-tjänsten startas dokumenteras i [den här artikeln](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

En VM-konfiguration som kör SQL Server med en SAP-databas och där tempdb-data och tempdb-loggfil placeras på D:\ enheten skulle se ut:

![Diagram över enkel VM-diskkonfiguration för SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Diagrammet ovan visar ett enkelt fodral. Som undvek i artikeln [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md)är antalet och storleken på Premium Storage-diskar beroende av olika faktorer. Men i allmänhet rekommenderar vi:

- Använda lagringsutrymmen för att skapa ett eller ett litet antal volymer som innehåller SQL Server-datafilerna. Anledningen bakom denna konfiguration är att det i verkliga livet finns många SAP-databaser med olika storlek databasfiler med olika I / O-arbetsbelastning.
- Använda lagringsutrymmen för att tillhandahålla tillräckligt många IOPS och för SQL Server-transaktionsloggfilen. Potentiell IOPS-arbetsbelastning är ofta den vägledande linjen för storleksändring av transaktionsloggvolymen och inte den potentiella volymen för SQL Server-transaktionsvolymen
- Använd D:\-enheten för tempdb så länge prestanda är tillräckligt bra. Om den totala arbetsbelastningen är begränsad i prestanda genom att tmepdb finns på D:\ enhet som du kan behöva överväga att flytta tempdb till separata Premium Storage-diskar som rekommenderas i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Special för virtuella datorer i M-serien
För Azure M-seriens virtuella dator kan svarstiden i transaktionsloggen minskas med faktorer jämfört med Azure Premium Storage-prestanda när du använder Azure Write Accelerator. Därför bör du distribuera Azure Write Accelerator för den virtuella hårddiskar som utgör volymen för SQL Server-transaktionsloggen. Detaljer kan läsas i dokumentet [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatera diskarna
För SQL Server bör NTFS-blockstorleken för diskar som innehåller SQL Server-data och loggfiler vara 64 KB. Det finns ingen anledning att formatera D:\ Bilresa. Den här enheten kommer förformaterad.

För att säkerställa att återställningen eller skapandet av databaser inte initierar datafilerna genom att nollställa innehållet i filerna, bör du se till att användarkontexten som SQL Server-tjänsten körs i har en viss behörighet. Vanligtvis har användare i gruppen Windows-administratör dessa behörigheter. Om SQL Server-tjänsten körs i användarkontexten för icke-Windows-administratörsanvändare måste du tilldela användaren användarrättigheten **Utför volymunderhållsuppgifter**.  Se informationen i den här artikeln i Microsoft Knowledge Base:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Inverkan av databaskomprimering
I konfigurationer där I/O-bandbredd kan bli en begränsande faktor kan varje åtgärd, vilket minskar IOPS, hjälpa till att sträcka ut arbetsbelastningen som man kan köra i ett IaaS-scenario som Azure. Därför, om det inte är ännu gjort, är att tillämpa SQL Server PAGE-komprimering av både SAP och Microsoft innan du överför en befintlig SAP-databas till Azure.

Rekommendationen att utföra databaskomprimering innan du laddar upp till Azure ges av två skäl:

* Mängden data som ska överföras är lägre.
* Varaktigheten av komprimeringskörningen är kortare förutsatt att man kan använda starkare maskinvara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt.
* Mindre databasstorlekar kan leda till lägre kostnader för diskallokering

Databaskomprimering fungerar lika bra i en Virtuell Azure-datorer som den gör lokalt. Mer information om hur du komprimerar befintliga SAP NetWeaver SQL Server-databaser finns i artikeln [Förbättrat SAP-komprimeringsverktyg MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 och senare – Lagra databasfiler direkt på Azure Blob Storage
SQL Server 2014 och senare versioner öppnar möjligheten att lagra databasfiler direkt på Azure Blob Store utan "omslag" av en virtuell hårddisk runt dem. Speciellt med standard Azure Storage eller mindre VM-typer aktiverar den här typen av distribution scenarier där du kan övervinna gränserna för IOPS som skulle tillämpas av ett begränsat antal diskar som kan monteras på några mindre vm-typer. Det här sättet att distribuera fungerar för användardatabaser men inte för systemdatabaser för SQL Server. Det fungerar också för data och loggfiler i SQL Server. Om du vill distribuera en SAP SQL Server-databas på det här sättet i stället för att "slå in" den i virtuella hårddiskar bör du tänka på:

* Det lagringskonto som används måste finnas i samma Azure-region som den som används för att distribuera den virtuella datorn SQL Server körs i.
* Överväganden som anges tidigare när det gäller distribution av virtuella hårddiskar över olika Azure Storage-konton gäller även för den här metoden för distributioner. Innebär att I/O-åtgärder räknas mot gränserna för Azure Storage-kontot.
* I stället för att bokföra mot den virtuella datorns lagrings-I/O-kvot kommer trafiken mot lagringsblobar som representerar SQL Server-data och loggfiler att tas till den virtuella datorns nätverksbandbredd för den specifika vm-typen. Information om nätverks- och lagringsbandbredd av en viss vm-typ finns i artikeln [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Som ett resultat av att skicka fil-I/O via nätverkskvoten strandar du lagringskvoten mestadels och med den använda den totala bandbredden för den virtuella datorn endast delvis.
* Prestandamålen för IOPS och I/O-dataflöde som Azure Premium Storage har för de olika diskstorlekarna gäller inte längre. Även om blobbar du skapade finns på Azure Premium Storage. Målen dokumenteras artikeln [Högpresterande Premium Storage och hanterade diskar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Som ett resultat av att placera SQL Server-datafiler och loggfiler direkt på blobbar som lagras på Azure Premium Storage, kan prestandaegenskaperna skilja sig från virtuella hårddiskar på Azure Premium Storage.
* Värdbaserad cachelagring som tillgänglig för Azure Premium Storage-diskar är inte tillgänglig när SQL Server-datafiler placeras direkt på Azure-blobbar.
* På virtuella datorer i M-serien kan Azure Write Accelerator inte användas för att stödja sub-millisekunder skriver mot SQL Server transaktionsloggfilen. 

Information om den här funktionen finns i artikeln [SQL Server datafiler i Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Rekommendation för produktionssystem är att undvika den här konfigurationen och snarare välja placeringar av SQL Server-data och loggfiler i Azure Premium Storage VHD:er i stället för direkt på Azure-blobbar.


## <a name="sql-server-2014-buffer-pool-extension"></a>Tillägget för buffertpoolen i SQL Server 2014
SQL Server 2014 introducerade en ny funktion, som kallas [buffertpooltillägg](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Den här funktionen utökar buffertpoolen för SQL Server, som hålls i minnet med en cache på andra nivån som backas upp av lokala SSD:er på en server eller virtuell dator. Buffertpooltillägget gör det möjligt att behålla en större arbetsuppsättning data "i minnet". Jämfört med åtkomst till Azure Standard Storage är åtkomsten till tillägget av buffertpoolen, som lagras på lokala SSD:er för en Azure VM, många faktorer snabbare. Om du jämför buffertpooltillägg med Azure Premium Storage Read Cache, som rekommenderas för SQL Server-datafiler, förväntas inga betydande fördelar för tillägg för buffertpool. Anledningen är att båda cacheminnena (SQL Server Buffer Pool Extension och Premium Storage Read Cache) använder de lokala diskarna i Azure-beräkningsnoden.

Erfarenheter som gjorts under tiden med SQL Server Buffer Pool Extension med SAP-arbetsbelastning är blandade och tillåter fortfarande inte tydliga rekommendationer om huruvida du ska använda den i alla fall. Det idealiska fallet är att den arbetsuppsättning som SAP-programmet kräver passar in i huvudminnet. Med Azure under tiden erbjuder virtuella datorer som kommer med upp till 4 TB minne, bör det vara möjligt att hålla arbetsminnet i minnet. Därför användningen av Buffer Pool Extension är begränsad till vissa sällsynta fall och bör inte vara ett vanligt fall.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Säkerhetsaspekter/återställningsöverväganden för SQL Server
När du distribuerar SQL Server till Azure måste din säkerhetskopieringsmetod granskas. Även om systemet inte är ett produktionssystem måste SAP-databasen som finns i SQL Server säkerhetskopieras med jämna mellanrum. Eftersom Azure Storage behåller tre avbildningar är en säkerhetskopia nu mindre viktig för att kompensera en lagringskrasch. Prioritetsorsaken för att upprätthålla en korrekt säkerhetskopierings- och återställningsplan är mer att du kan kompensera för logiska/manuella fel genom att tillhandahålla funktioner för tidsåterställning. Så målet är att antingen använda säkerhetskopior för att återställa databasen tillbaka till en viss tidpunkt eller att använda säkerhetskopior i Azure för att så ett annat system genom att kopiera den befintliga databasen. 

För att titta på olika möjligheter till säkerhetskopiering av SQL Server i Azure, läs artikeln [Säkerhetskopiering och återställning för SQL Server i Virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Artikeln täcker flera olika möjligheter.

### <a name="manual-backups"></a>Manuella säkerhetskopieringar
Du har flera möjligheter att utföra "manuella" säkerhetskopior genom att:

1. Utföra konventionella SQL Server-säkerhetskopior på direktanslutna Azure-diskar. Den här metoden har fördelen att du har säkerhetskopior tillgängliga snabbt för systemuppdatering och uppbyggnad av nya system som kopior av befintliga SAP-system
2.  SQL Server 2012 CU4 och högre kan säkerhetskopiera databaser till en Azure-lagrings-URL.
3.  Säkerhetskopiering av filer för databasfiler i Azure Blob Storage. Den här metoden fungerar bara när dina SQL Server-data och loggfiler finns på Azure blob storage

Den första metoden är välkänd och tillämpas i många fall även i den lokala världen. Ändå lämnar det dig med uppgiften att lösa den långsiktiga säkerhetskopieringsplatsen. Eftersom du inte vill behålla dina säkerhetskopior i 30 eller fler dagar i det lokalt anslutna Azure Storage, måste du antingen använda Azure Backup Services eller ett annat verktyg för säkerhetskopiering/återställning från tredje part som innehåller åtkomst- och kvarhållningshantering för dina säkerhetskopior. Eller så bygger du ut en stor filserver i Azure med Hjälp av Windows-lagringsutrymmen.

Den andra metoden beskrivs närmare i artikeln [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Olika versioner av SQL Server har vissa variationer i den här funktionen. Därför bör du läsa dokumentationen för just din SQL Server-versionskontroll. Viktigt att notera att den här artikeln innehåller många begränsningar. Du har antingen möjlighet att utföra säkerhetskopian mot:

- En enda Azure-sidblob, som sedan begränsar säkerhetskopieringsstorleken till 1 000 GB. Detta begränsar också det dataflöde du kan uppnå.
- Flera (upp till 64) Azure-blockblobar, som möjliggör en teoretisk säkerhetskopieringsstorlek på 12 TB. Tester med kunddatabaser visade dock att den maximala säkerhetskopieringsstorleken kan vara mindre än dess teoretiska gräns. I det här fallet är du ansvarig för att hantera kvarhållning av säkerhetskopior och komma åt o säkerhetskopior också.


### <a name="automated-backup-for-sql-server"></a>Automatiserad säkerhetskopiering för SQL Server
Automatisk säkerhetskopiering tillhandahåller en automatisk säkerhetskopieringstjänst för SQL Server Standard- och Enterprise-utgåvor som körs i en Windows VM i Azure. Den här tjänsten tillhandahålls av [SQL Server IaaS Agent Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), som installeras automatiskt på virtuella SQL Server Windows-avbildningar i Azure-portalen. Om du distribuerar egna OS-avbildningar med SQL Server installerat måste du installera vm-tilläggen separat. De steg som krävs dokumenteras i den här [artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Mer information om funktionerna i denna metod finns i dessa artiklar:

- SQL Server 2014: [Automatisk säkerhetskopiering för virtuella SQL Server 2014-datorer (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatisk säkerhetskopiering v2 för virtuella Azure-datorer (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Om du tittar på dokumentationen kan du se att funktionerna med de senaste SQL Server-versionerna förbättrades. Mer information om automatiska säkerhetskopieringar av SQL Server släpps i artikeln [SQL Server Managed Backup till Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Den teoretiska storleksgränsen för säkerhetskopiering är 12 TB.  De automatiska säkerhetskopiorna kan vara en bra metod för säkerhetskopieringsstorlekar på upp till 12 TB. Eftersom flera blobbar skrivs till parallellt kan du förvänta dig ett dataflöde på större än 100 MB/sek. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Virtuella datorer med Azure-säkerhetskopiering för SQL Server
Den här nya metoden för SQL Server-säkerhetskopior erbjuds från och med juni 2018 som offentlig förhandsversion av Azure Backup-tjänster. Metoden för att säkerhetskopiera SQL Server är samma som andra verktyg från tredje part använder, nämligen SQL Server VSS/VDI-gränssnittet för att strömma säkerhetskopior till en målplats. I det här fallet är målplatsen Azure Recovery Service-valv.

En mer än detaljerad beskrivning av den här säkerhetskopieringsmetoden, som lägger till många fördelar med centrala säkerhetskopieringskonfigurationer, övervakning och administration finns [här](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Säkerhetskopieringslösningar från tredje part
För ett stort antal SAP-kunder fanns det ingen möjlighet att börja om och introducera nya säkerhetskopieringslösningar för den del av deras SAP-landskap som kördes på Azure. Därför behövde de befintliga säkerhetskopieringslösningarna användas och utökas till Azure. Att utöka befintliga säkerhetskopieringslösningar till Azure fungerade vanligtvis bra med de flesta av huvudleverantörerna i det här utrymmet. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Använda en SQL Server-avbildning från Microsoft Azure Marketplace
Microsoft erbjuder virtuella datorer på Azure Marketplace, som redan innehåller versioner av SQL Server. För SAP-kunder som behöver licenser för SQL Server och Windows kan det vara en möjlighet att använda dessa avbildningar för att täcka behovet av licenser genom att snurra upp virtuella datorer med SQL Server som redan är installerat. För att kunna använda sådana avbildningar för SAP måste följande överväganden göras:

* SQL Server-versionerna som inte är utvärderingsversioner får högre kostnader än en virtuell dator med endast Windows som distribueras från Azure Marketplace. Se dessa artiklar för <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>att jämföra priser: och . 
* Du kan bara använda SQL Server-versioner som stöds av SAP.
* Sammanställningen av SQL Server-instansen, som är installerad i de virtuella datorer som erbjuds på Azure Marketplace, är inte den sortering SAP NetWeaver kräver att SQL Server-instansen körs. Du kan ändra sortering men med anvisningarna i följande avsnitt.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändra SQL Server-sortering av en virtuell dator för Microsoft Windows/SQL Server
Eftersom SQL Server-avbildningarna på Azure Marketplace inte är konfigurerade för att använda sorteringen, som krävs av SAP NetWeaver-program, måste den ändras omedelbart efter distributionen. För SQL Server kan den här ändringen av sortering göras med följande steg så snart den virtuella datorn har distribuerats och en administratör kan logga in på den distribuerade virtuella datorn:

* Öppna ett Kommandofönster i Windows som administratör.
* Ändra katalogen till C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Kör kommandot: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> är kontot, som definierades som administratörskontot när du distribuerar den virtuella datorn för första gången via galleriet.

Processen bör bara ta några minuter. För att se till att steget slutade med rätt resultat utför du följande steg:

* Öppna SQL Server Management Studio.
* Öppna ett frågefönster.
* Kör kommandot sp_helpsort i SQL Server-huvuddatabasen.

Det önskade resultatet ska se ut:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Om resultatet är annorlunda, stoppa distribution SAP och undersöka varför installationskommandot inte fungerade som förväntat. Distribution av SAP NetWeaver-program till SQL Server-instans med olika SQL Server-tecken än de som nämns ovan stöds **INTE.**

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Hög tillgänglighet för SQL Server för SAP i Azure
Med SQL Server i Azure IaaS-distributioner för SAP har du flera olika möjligheter att lägga till för att distribuera DBMS-lagret som är mycket tillgängligt. Som diskuterats i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningen](dbms_guide_general.md) redan, azure ger olika up-time SLA för en enda virtuell dator och ett par virtuella datorer som distribueras i en Azure Availability Set. Antagandet är att du kör mot up-time SLA för dina produktionsdistributioner som kräver distributionen i Azure-tillgänglighetsuppsättningar. I så fall måste du distribuera minst två virtuella datorer i en sådan tillgänglighetsuppsättning. En virtuell dator kör den aktiva SQL Server-instansen. Den andra virtuella datorn kör den passiva instansen

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server-kluster med Windows Scale-out File Server
Med Windows Server 2016 introducerade Microsoft [Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Baserat på Storage Spaces Direct-distribution stöds SQL Server FCI-kluster. Information finns i artikeln [Konfigurera SQL Server Redundansklusterinstans på Virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Lösningen kräver en Azure belastningsutjämning samt för att hantera den virtuella IP-adressen för klusterresurser. SQL Server-databasfilerna lagras i Lagringsutrymmen. Därför är det en självklarhet att du skulle behöva bygga upp Windows Storage Spaces baserat på Azure Premium Storage. Eftersom den här lösningen har fått stöd för inte alltför länge ännu, finns det inga kända SAP-kunder som använder den här lösningen i SAP-produktionsscenarier.  

### <a name="sql-server-log-shipping"></a>Leverans av SQL Server-logg
En av metoderna för hög tillgänglighet (HA) är SQL Server Log Shipping. Om de virtuella datorerna som deltar i HA-konfigurationen har arbetsnamnsmatchning, är det inga problem och installationen i Azure skiljer sig inte från alla inställningar som görs lokalt. När det gäller att ställa in Log Shipping och principerna kring Log Shipping. Information om SQL Server Log Shipping finns i artikeln [Om loggfrakt (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

SQL Server-loggleveransfunktionen användes knappast i Azure för att uppnå hög tillgänglighet inom en Azure-region. Men i följande scenarier SAP kunder använde loggleverans lyckades tillsammans med Azure:

- Katastrofåterställningsscenarier från en Azure-region till en annan Azure-region
- Konfiguration av haveriberedskap från lokalt till en Azure-region
- Överklippta scenarier från lokalt till Azure. I sådana fall används loggleverans för att synkronisera den nya DBMS-distributionen i Azure med det pågående produktionssystemet lokalt. Vid tidpunkten för nedskärningen stängs produktionen av och det är säkert att de senaste och senaste säkerhetskopieringarna av transaktionsloggen har överförts till Azure DBMS-distributionen. Sedan öppnas Azure DBMS-distributionen för produktion.  



### <a name="database-mirroring"></a>Spegla databasen
Databasspegling som stöds av SAP (se SAP Note [965908]) förlitar sig på att definiera en redundanspartner i SAP-anslutningssträngen. För ärendena mellan lokala och lokala ärenden förutsätter vi att de två virtuella datorerna finns i samma domän och att användarkontexten de två SQL Server-instanserna körs även under en domänanvändare och har tillräckliga privilegier i de två SQL Server-instanserna. Därför skiljer sig inte inställningen av databasspegling i Azure mellan en typisk lokal konfiguration/konfiguration.

Från och med cloud-only-distributioner är den enklaste metoden att ha en annan domänkonfiguration i Azure för att ha dessa DBMS-virtuella datorer (och idealiskt dedikerade SAP-virtuella datorer) inom en domän.

Om en domän inte är möjlig kan man också använda certifikat för databasspeglingsslutpunkterna enligt beskrivningen här:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

En självstudiekurs för att konfigurera Databasspegling i Azure finns här:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Ständig aktivering av SQL Server
Eftersom Alltid på stöds för SAP lokalt (se SAP Note [1772688),]stöds det i kombination med SAP i Azure. Det finns vissa särskilda överväganden kring distribution av SQL Server Availability Group Listener (inte att förväxla med Azure Availability Set) eftersom Azure vid denna tidpunkt inte tillåter att skapa ett AD/DNS-objekt som det är möjligt lokalt. Därför är vissa olika installationssteg nödvändiga för att lösa det specifika beteendet för Azure.

Några som överväger att använda en tillgänglighetsgrupplyssnare är:

* Det går bara att använda en lyssnare för tillgänglighetsgrupp med Windows Server 2012 eller senare som gästoperativsystem för den virtuella datorn. För Windows Server 2012 måste du se till att den här korrigeringsfilen används:<https://support.microsoft.com/kb/2854082> 
* För Windows Server 2008 R2 finns inte den här korrigeringsfilen och Always On måste användas på samma sätt som databasspegling genom att ange en redundanspartner i anslutningssträngen (görs via SAP default.pfl-parametern dbs/mss/server - se SAP Note [965908]).
* När du använder en tillgänglighetsgrupplyssnare måste virtuella databasdämpare vara anslutna till en dedikerad belastningsutjämnare. För att undvika att Azure tilldelar nya IP-adresser i fall där båda virtuella datorer för övrigt stängs av, bör man tilldela statiska IP-adresser till nätverksgränssnitten för dessa virtuella datorer i konfigurationen Always On (definiera en statisk IP-adress beskrivs i [den här][virtual-networks-reserved-private-ip] artikeln)
* Det finns särskilda steg som krävs när du skapar WSFC-klusterkonfigurationen där klustret behöver en särskild IP-adress tilldela, eftersom Azure med dess aktuella funktioner skulle tilldela klusternamnet samma IP-adress som noden som klustret skapas på. Det innebär att ett manuellt steg måste utföras för att tilldela klustret en annan IP-adress.
* Tillgänglighetsgrupplyssnaren kommer att skapas i Azure med TCP/IP-slutpunkter, som tilldelas de virtuella datorerna som kör de primära och sekundära replikerna i gruppen Tillgänglighet.
* Det kan finnas ett behov av att skydda dessa slutpunkter med ACL:er.

Detaljerad dokumentation om distribution alltid på med SQL Server i Virtuella Azure-datorer visar gilla:

- [Introduktion till SQL Server Always On Availability Groups på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Konfigurera en alltid på tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Konfigurera en belastningsutjämnare för en alltid på tillgänglighetsgrupp i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Om du konfigurerar Azure-belastningsutjämnaren för den virtuella IP-adressen för lyssnaren för tillgänglighetsgruppen kontrollerar du att DirectServerReturn är konfigurerad. om du konfigurerar det här alternativet minskar nätverksfördröjningsfördröjningen mellan SAP-programlagret och DBMS-lagret. 

SQL Server Always On är den vanligaste funktioner för hög tillgänglighet och haveriberedskap som används i Azure för SAP-arbetsbelastningsdistributioner. De flesta kunder använder Alltid på för hög tillgänglighet inom en enda Azure-region. Om distributionen endast är begränsad till två noder har du två alternativ för anslutning:

- Använda tillgänglighetsgruppens lyssnare. Med tillgänglighetsgrupplyssnaren måste du distribuera en Azure-belastningsutjämnare. Detta är vanligtvis standardmetoden för distribution. SAP-program skulle konfigureras för att ansluta mot tillgänglighetsgruppens lyssnare och inte mot en enda nod
- Använda anslutningsparametrarna för SQL Server Database Mirroring. I det här fallet måste du konfigurera anslutningen av SAP-programmen på ett sätt där båda nodnamnen namnges. Exakta uppgifter om en sådan SAP-sidokonfiguration dokumenteras i SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Genom att använda det här alternativet behöver du inte konfigurera en lyssnare i tillgänglighetsgruppen. Och med det ingen Azure belastningsutjämning för SQL Server hög tillgänglighet. Därför är nätverksfördröjningen mellan SAP-programlagret och DBMS-lagret lägre eftersom inkommande trafik till SQL Server-instansen inte dirigeras via Azure-belastningsutjämnaren. Men kom ihåg det här alternativet fungerar bara om du begränsar din tillgänglighetsgrupp så att den sträcker sig över två instanser. 

En hel del kunder använder SQL Server Always On-funktionen för ytterligare funktioner för haveriberedskap mellan Azure-regioner. Flera kunder använder också möjligheten att utföra säkerhetskopior från en sekundär replik. 

## <a name="sql-server-transparent-data-encryption"></a>Transparent datakryptering för SQL Server
Det finns ett antal kunder som använder SQL Server [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) när de distribuerar sina SAP SQL Server-databaser i Azure. SQL Server TDE-funktionen stöds fullt ut av SAP (se SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Använda SQL Server TDE
I de fall där du utför en heterogen migrering från en annan DBMS, som körs lokalt, till Windows/SQL Server som körs i Azure, bör du skapa din tomma måldatabas i SQL Server i förväg. Som nästa steg skulle du använda SQL Server TDE-funktioner. Medan du fortfarande kör ditt produktionssystem lokalt. Anledningen till att du vill utföra i den här sekvensen är att processen att kryptera den tomma databasen kan ta ganska lång tid. SAP-importprocesserna importerar sedan data till den krypterade databasen under driftstoppsfasen. Omkostnaderna för import till en krypterad databas har en mycket lägre tidspåverkan än att kryptera databasen efter exportfasen i nedtidsfasen. Negativa upplevelser gjordes när du försöker tillämpa TDE med SAP-arbetsbelastning som körs ovanpå databasen. Därför är rekommendationen behandla distributionen av TDE som en aktivitet som måste göras utan SAP-arbetsbelastning på den specifika databasen.

I de fall där du flyttar SAP SQL Server-databaser från lokala till Azure rekommenderar vi att du testar på vilken infrastruktur du kan få krypteringen som tillämpas snabbast. För detta hålla dessa fakta i åtanke:

- Du kan inte definiera hur många trådar som används för att tillämpa datakryptering i databasen. Antalet trådar beror i stort grad på antalet diskvolymer som SQL Server-data och loggfiler distribueras över. Innebär att de mer distinkta volymerna (enhetsbeteckningar), desto fler trådar kommer att engageras parallellt för att utföra krypteringen. En sådan konfiguration motsäger lite med tidigare förslag på diskkonfiguration när du skapar ett eller ett mindre antal lagringsutrymmen för SQL Server-databasfilerna i virtuella Azure-datorer. En konfiguration med ett litet antal volymer skulle leda till ett litet antal trådar som kör krypteringen. En enda tråd krypterande läser 64KB omfattningar, krypterar den och sedan skriva en post i transaktionsloggfilen, berättar att omfattningen blev krypterad. Som ett resultat är belastningen på transaktionsloggen måttlig.
- I äldre SQL Server-versioner fick komprimeringen inte längre effektivitet när du krypterade SQL Server-databasen. Det här problemet kan utvecklas till ett problem när din plan var att kryptera din SQL Server-databas lokalt och sedan kopiera en säkerhetskopia till Azure för att återställa databasen i Azure. SQL Server backup komprimering uppnår vanligtvis ett komprimeringsförhållande av faktor 4.
- Med SQL Server 2016 introducerade SQL Server nya funktioner som gör det möjligt att komprimera krypterade databaser och på ett effektivt sätt. Se [denna blogg](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) för lite information.
 
Behandla tillämpningen av TDE-kryptering med nej till liten SAP-arbetsbelastning, bör du testa i din specifika konfiguration om det är bättre att tillämpa TDE på din SAP-databas lokalt eller att göra det i Azure. I Azure har du säkert större flexibilitet när det gäller infrastruktur för överetablering och krymper infrastrukturen efter att TDE har tillämpats.

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault
Azure erbjuder tjänsten för ett [Key Vault](https://azure.microsoft.com/services/key-vault/) för att lagra krypteringsnycklar. SQL Server på andra sidan erbjuder en anslutningsapp för att utnyttja Azure Key Vault som arkiv för TDE-certifikaten.

Mer information om du vill använda Azure Key Vault för SQL Server TDE-listor som:

- [Utökningsbar nyckelhantering med hjälp av SQL Server (Azure Key Vault).](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017)
- [SQL Server TDE Utökningsbar nyckelhantering med Hjälp av Azure Key Vault - Installationssteg](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Underhåll & -underhåll av SQL Server Connector](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Fler frågor från kunder om SQL Server Transparent datakryptering – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Med HJÄLP AV SQL Server TDE, särskilt med Azure key Vault, rekommenderas att använda de senaste korrigeringsfilerna i SQL Server 2014, SQL Server 2016 och SQL Server 2017. Anledningen är att baserat på kundfeedback, optimeringar och korrigeringar tillämpades på koden. Du kan till exempel kontrollera [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Allmänt SQL Server för SAP på Azure-sammanfattning
Det finns många rekommendationer i den här guiden och vi rekommenderar att du läser den mer än en gång innan du planerar din Azure-distribution. I allmänhet, dock, se till att följa den översta allmänna DBMS på Azure-specifika rekommendationer:

1. Använd den senaste DBMS-versionen, till exempel SQL Server 2017, som har flest fördelar i Azure. 
2. Planera noggrant ditt SAP-systemlandskap i Azure för att balansera datafillayouten och Azure-begränsningarna:
   * Har inte för många diskar, men har tillräckligt för att du ska kunna nå din nödvändiga IOPS.
   * Om du inte använder hanterade diskar bör du komma ihåg att IOPS också är begränsade per Azure Storage-konto och att lagringskonton är begränsade inom varje Azure-prenumeration ([mer information][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Endast rand över diskar om du behöver uppnå ett högre dataflöde.
3. Installera aldrig programvara eller placera några filer som kräver uthållighet på D:\ enheten eftersom det är icke-permanent och allt på den här enheten går förlorat vid en Windows-omstart.
4. Använd inte diskcachelagring för Azure Standard Storage.
5. Använd inte Azure geo-replikerade Azure Standard Storage-konton.  Använd Lokalt redundant för DBMS-arbetsbelastningar.
6. Använd DBMS-leverantörens HA/DR-lösning för att replikera databasdata.
7. Använd alltid namnmatchning, lita inte på IP-adresser.
8. Använd de senaste SQL Server-korrigeringarna med TDE i SQL Server.
9. Använd den högsta möjliga databaskomprimeringen. Vilket är sidkomprimering för SQL Server.
10. Var försiktig med att använda SQL Server-avbildningar från Azure Marketplace. Om du använder SQL Server en, måste du ändra instanssortering innan du installerar något SAP NetWeaver-system på den.
11. Installera och konfigurera SAP-värdövervakning för Azure enligt beskrivningen i [distributionsguiden][deployment-guide].
