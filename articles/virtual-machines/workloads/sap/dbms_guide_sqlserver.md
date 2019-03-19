---
title: SQL Server Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar | Microsoft Docs
description: SQL Server Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aac7ca7aa67143f89d9247da879a6fad2cfbb7b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992483"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure virtuella datorer DBMS-distribution för SAP NetWeaver

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




Det här dokumentet omfattar flera olika områden att tänka på vid distribution av SQL Server för SAP-arbetsbelastningar i Azure IaaS. Som ett villkor för det här dokumentet, bör du ha läsa dokumentet [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md) samt de andra guiderna i den [SAP-arbetsbelastningar på Azure-dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> Omfattningen för det här dokumentet är Windows-versionen på SQL Server. SAP inte stöd för Linux-versionen av SQL Server med någon av SAP-program. Dokumentet är inte diskutera Microsoft Azure SQL Database, vilket är en plattform som en tjänsterbjudande om Microsoft Azure-plattformen. I avsnittet i det här dokumentet handlar om hur du kör SQL Server-produkten eftersom den är känt för lokala distributioner i Azure virtuella datorer, att utnyttja infrastrukturen som en tjänst-funktion i Azure. Database-funktioner och funktionalitet mellan dessa två erbjudanden är olika och ska inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
>

I allmänhet bör du använda den senaste SQL Server versioner om du vill köra SAP-arbetsbelastningen i Azure IaaS. De senaste versionerna av SQL Server erbjuder bättre integrering i några av de Azure-tjänster och funktioner. Eller har ändrats och därmed optimerar åtgärder i en Azure IaaS-infrastruktur.

Det rekommenderas att granska [detta] [ virtual-machines-sql-server-infrastructure-services] dokumentationen innan du fortsätter.

I följande avsnitt, aggregeras och nämns delar av delar av dokumentationen under länken ovan. Detaljer runt SAP nämns samt och några koncept beskrivs i detalj. Vi rekommenderar dock starkt att gå igenom dokumentationen ovan först innan du läser dokumentationen för SQL Server-specifika.

Det finns några SQL-Server i IaaS specifik information som du bör känna till innan du fortsätter:

* **Stöd för SQL-Version**: För SAP-kunder, SQL Server 2008 R2 och senare stöds på Microsoft Azure-dator. Tidigare versioner stöds inte. Granska den här allmänna [Supportmeddelande](https://support.microsoft.com/kb/956893) för mer information. I allmänhet SQL Server 2008 finns även stöd av Microsoft. Men på grund av viktiga funktioner för SAP, som introducerades i SQL Server 2008 R2, SQL Server 2008 R2 är den lägsta versionen för SAP. I allmänhet bör du använda den senaste SQL Server versioner om du vill köra SAP-arbetsbelastningen i Azure IaaS. De senaste versionerna av SQL Server erbjuder bättre integrering i några av de Azure-tjänster och funktioner. Eller har ändrats och därmed optimerar åtgärder i en Azure IaaS-infrastruktur. Dokumentet är därför begränsade till SQL Server 2016 och SQL Server 2017.
* **SQL-prestanda**: Microsoft Azure virtuella värddatorerna utför även i jämförelse med andra offentliga molnerbjudanden för virtualisering, men enskilda resultaten kan variera. Läs artikeln [prestandametodtips för SQL Server i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Med hjälp av avbildningar från Azure Marketplace**: Det snabbaste sättet att distribuera en ny Microsoft Azure VM är att använda en avbildning från Azure Marketplace. Det finns avbildningar i Azure Marketplace, som innehåller de senaste versionerna av SQL Server. Bilderna där SQL Server redan är installerat kan inte användas direkt för SAP NetWeaver-program. Orsaken är standardsortering för SQL Server är installerat i dessa bilder och inte den sortering som krävs av SAP NetWeaver-system. För att kunna använda sådana bilder, kontrollera stegen i kapitlet [med hjälp av en SQL Server-avbildning från Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Rekommendationer för VM/VHD-strukturen för SAP-relaterade SQL Server-distributioner
I enlighet med den allmänna beskrivningen SQLServer körbara filer finns eller installerat till enheten av den Virtuella datorns OS-disk (enhet C:\).  Normalt används inte de flesta av SQL Server system-databaser på en hög nivå av SAP NetWeaver-arbetsbelastning. Därmed kan systemdatabaser av SQL Server (master, msdb och modellen) förbli på C:\-enheten. Ett undantag ska vara tempdb som SAP-arbetsbelastningar, kan kräva högre datavolymen eller volymen för i/o-åtgärder. I/o-arbetsbelastningen, vilket inte bör tillämpas på OS-VHD. Följande steg ska utföras för sådana system:


* Med alla SAP certifierade VM-typer (Se SAP-kommentar [1928533]), förutom för virtuella datorer i A-serien, tempdb-data och loggfiler kan placeras på den icke-beständig D:\ enheten. 
* Vi rekommenderar dock att använda flera tempdb-datafiler. Tänk på D:\ enhetsvolymer är olika beroende på vilken virtuell dator. Exakta storlekarna på D:\-enhet på olika virtuella datorer finns i artikeln [storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

De här konfigurationerna aktivera tempdb förbruka mer utrymme än systemenheten är kan tillhandahålla. Den icke-beständiga D:\ enheten erbjuder även bättre i/o-svarstid och dataflöde (med undantag för virtuella datorer i A-serien). Du kan kontrollera tempdb storlek på befintliga system för att fastställa rätt tempdb-storlek. 

>[!NOTE]
> Om du placera tempdb-datafiler och loggfilen i en mapp på D:\ enhet som du skapade, måste du kontrollera att mappen finns efter en omstart av virtuell dator. Eftersom D:\ enheten nyligen har initierats när en virtuell dator startas om raderas alla fil- och strukturer. En möjlighet att återskapa eventuell katalogstrukturer på D:\ enhet innan början av SQL Server-tjänsten dokumenteras i [i den här artikeln](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

En VM-konfiguration, som kör SQL Server med en SAP-databas och där tempdb-data och tempdb logfile placeras på enheten D:\ ser ut som:

![Diagram över enkel VM-diskkonfigurationen för SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Diagrammet ovan visar ett enkelt ärende. Eftersom eluded till i artikeln [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md), number, och storleken på Premium Storage-diskar är beroende från olika faktorer. Men i allmänhet rekommenderar vi att:

- Med hjälp av lagringsutrymmen för att bilda en eller ett litet antal volymer som innehåller SQL Server-datafiler. Orsaken bakom den här konfigurationen är att i verkliga livet är vanligtvis det finns flera SAP-databaser med olika storlekar databasfiler med olika i/o-arbetsbelastning.
- Använda lagringsutrymmen för att tillhandahålla tillräckligt med IOPS och för SQL Server-transaktionsloggfilen. Potentiella IOPS-arbetsbelastningen är ofta guida raden för storlek på loggvolymen transaktion och inte möjliga mängden transaktionsvolymer SQL Server
- Använd D:\drive för tempdb så länge prestanda är tillräckligt. Om den övergripande arbetsbelastningen är begränsat i prestanda av tmepdb som finns på D:\ enheten kanske måste du överväga för att flytta tempdb på separata Premium Storage-diskar som rekommenderas i [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Särskilda för virtuella datorer i M-serien
För M-serien i Azure-dator, kan du minska svarstiden skrivningen till transaktionsloggen av faktorer, jämfört med Azure Premium Storage-prestanda när du använder Azure Write Accelerator. Därför bör du distribuera Azure Write Accelerator för de virtuella hårddiskarna som utgör volymen för SQL Server-transaktionsloggen. Kan läsa information i dokumentet [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatera diskarna
NTFS-blockstorlek för diskar som innehåller SQL Server-data och loggfiler filer ska vara 64KB för SQL Server. Det finns inget behov av att formatera D:\-enhet. Den här enheten kommer före formaterade.

För att se till att återställa eller skapandet av databaser som inte initieras datafilerna genom att gå till botten innehållet i filerna, bör du se till att SQL Server-tjänsten körs i användarkontexten har en viss behörighet. Användare i gruppen Administratörer i Windows har vanligtvis dessa behörigheter. Om SQL Server-tjänsten körs i användarkontexten för icke - Windows-administratör, måste du tilldela användaren användarrättigheten **utföra underhållsaktiviteter**.  Se informationen i det här Microsoft Knowledge Base-artikel: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
I konfigurationer där i/o-bandbredd kan bli en begränsande faktor, kan varje mått, vilket minskar IOPS hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure. Om du inte redan har gjort, rekommenderas tillämpa komprimering av SQL Server-sida därför av både SAP och Microsoft innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen för att utföra databas komprimering innan du laddar upp till Azure ges av två skäl:

* Mängden data som ska överföras är lägre.
* Varaktigheten för komprimering körningen är kortare om vi antar att en kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid på plats.
* Mindre databasernas storlek kan leda till lägre kostnader för diskallokering

Databasen komprimering fungerar också i en Azure-datorer som lokala. Mer information om hur du komprimera befintliga SAP NetWeaver, SQL Server-databaser finns i artikeln [förbättrad SAP komprimeringsverktyg MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 och nyare - lagra databasfilerna direkt på Azure Blob Storage
SQL Server 2014 och senare versioner öppen möjligheten att lagra databasfilerna direkt på Azure Blob Store utan 'wrapper' av en virtuell Hårddisk runtom. Särskilt i Standard Azure Storage eller mindre VM-typer kan den här typen av distribution scenarier där du kan lösa gränserna för IOPS som skulle tillämpas av ett begränsat antal diskar som kan monteras till några mindre VM-typer. Det här sättet att distributionen fungerar för användardatabaser men inte för systemdatabaser av SQL Server. Den fungerar även för data och loggfiler för SQL Server. Om du vill distribuera en SAP SQL Server-databas i stället för ”radbrytning” på så sätt det till VHD: er, ha i åtanke:

* Lagringskontot används behov för att vara i samma Azure-Region som det som används för att distribuera Virtuella SQL Server körs i.
* Listan ovan om distributionen av virtuella hårddiskar via olika Azure Storage-konton gäller för den här metoden för samt distributioner. Innebär att antalet i/o-åtgärder mot gränserna för Azure Storage-konto.
* I stället för redovisning mot den Virtuella datorns i/o lagringskvoten redovisas trafiken mot storage-blobbar som representerar SQL Server data- och loggfiler filer, till den Virtuella datorns bandbredd på en viss typ av virtuell dator. Nätverks- och bandbredden för en viss typ av virtuell dator, finns i artikeln [storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Till följd av push-Överför fil-i/o via nätverket kvoten, strandning lagringskvoten huvudsakligen och med den använda den övergripande bandbredden för den virtuella datorn endast delvis.
* IOPS och i/o-dataflöde prestandamålen som Azure Premium Storage har för olika diskstorlekar gäller inte längre. Även om blobarna som du skapade finns på Azure Premium Storage. Den är riktad mot dokumenteras i artikeln [högpresterande Premium Storage och hanterade diskar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Till följd av att placera SQL Server-datafiler och loggfiler direkt på BLOB-objekt som lagras på Azure Premium Storage, kan prestandaegenskaperna vara olika jämfört med virtuella hårddiskar på Azure Premium Storage.
* Värdcachelagring baserat som är tillgänglig för Azure Premium Storage-diskar är inte tillgänglig när du monterar SQL Server-datafiler direkt på Azure blobs.
* Azure Write Accelerator kan inte användas för under Millisekunden skrivningar till transaktionsloggfilen för SQL Server på virtuella datorer i M-serien. 

Information om den här funktionen finns i artikeln [SQL Server-datafiler i Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Rekommendation för produktionssystem är att undvika den här konfigurationen och i stället välja placeringarna av SQL Server-data och loggfiler på Azure Premium Storage virtuella hårddiskar i stället för direkt på Azure blobs.


## <a name="sql-server-2014-buffer-pool-extension"></a>Buffertpooltillägget för SQL Server 2014
SQL Server 2014 introduceras en ny funktion som kallas [Buffertpooltillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Den här funktionen utökar buffertpooltillägget för SQL Server, som sparas i minnet med andra nivån cache som backas upp av lokal SSD-enheter på en server eller virtuell dator. Buffertpooltillägget gör det möjligt att hålla en större arbetsminnet för data ”i minnet”. Jämfört med åtkomst till Azure standardlagring är åtkomst till tillägg till buffertpoolen, som är lagrad på lokala SSD-enheter av en Azure VM många faktorer snabbare. Jämföra Buffertpooltillägget till Azure Premium Storage Läs Cache, enligt rekommendationer för SQL Server-datafiler, förväntas inte några betydande fördelar för Buffertpooltillägg. Anledningen är att båda cacheminnen (Buffertpooltillägget för SQL Server och Läs-Cache för Premium Storage) använder de lokala diskarna på Azure-beräkningsnod.

Erfarenheterna under tiden Buffertpooltillägget för SQL Server med SAP-arbetsbelastning är blandad och fortfarande tillåter inte att rensa rekommendationer på om du vill använda i samtliga fall. Perfekt fallet är att arbetsminnet SAP-programmet kräver passar in i RAM-minne. Med Azure erbjuder virtuella datorer som levereras med upp till 4 TB minne under tiden kan vara det möjligt att lagra arbetsminnet i minnet. Därför användningen av Buffertpooltillägget är begränsad till vissa sällsynta fall och får inte vara vanliga fall.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Överväganden för säkerhetskopiering/återställning för SQL Server
När du distribuerar SQL Server till Azure, måste metoder för säkerhetskopiering granskas. Även om systemet inte är ett produktionssystem måste SAP-databaser som hanteras av SQL Server säkerhetskopieras regelbundet. Eftersom Azure Storage behåller tre avbildningar, är en backup nu mindre viktiga i jämfört med kompenserande en krasch för lagring. Prioritet orsaken för att upprätthålla en rätt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Så att målet är att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tiden eller du använder säkerhetskopieringar i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

För att kunna titta på olika SQL Server-säkerhetskopiering möjligheterna i Azure finns i artikeln [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Artikeln beskriver flera olika möjligheter.

### <a name="manual-backups"></a>Manuell säkerhetskopiering
Du har flera möjligheter att utföra ”manuellt” säkerhetskopieringar av:

1. Utför vanliga SQL Server-säkerhetskopior på direkt anslutna Azure-diskar. Den här metoden har fördelen att du har säkerhetskopiorna som är tillgängliga snabbt för systemuppdateringar och bygga upp för nya system som kopior av befintliga SAP-system
2.  SQL Server 2012 CU4 och högre kan säkerhetskopiera databaserna till en Azure storage-URL.
3.  Säkerhetskopior av Filögonblicksbilder för databasfiler i Azure Blob Storage. Den här metoden fungerar bara om dina SQL Server-filer i data och loggfiler finns i Azure blob-lagring

Den första metoden är välkänd och används i många fall i en lokal världen samt. Dock lämnar den du med uppgiften att lösa längre sikt säkerhetskopieringsplatsen. Eftersom du inte vill behålla dina säkerhetskopior i 30 eller fler dagar i Azure Storage som lokalt anslutna, har du behovet av att antingen använda Azure Backup-tjänster eller något annat verktyg för tredje parts säkerhetskopiering/återställning som inkluderar hantering av åtkomst och kvarhållning för dina säkerhetskopior. Eller skapa en stor filserver i Azure med hjälp av Windows-lagringsutrymmen.

Den andra metoden som beskrivs närmare i artikeln [SQL Server-säkerhetskopiering till URL: en](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Olika versioner av SQL Server har vissa varianter i den här funktionen. Därför bör du först läsa dokumentationen för din viss kontroll för SQL Server-versionen. Viktigt att notera att den här artikeln innehåller en massa begränsningar. Antingen har du möjlighet att utföra säkerhetskopieringen mot:

- En enda Azure sidblob, vilket begränsar säkerhetskopians storlek till 1 000 GB. Detta begränsar också det dataflöde som du kan uppnå.
- Flera (upp till 64) Azure blockblob-objekt, vilket möjliggör en teoretisk säkerhetskopians storlek på 12 TB. Tester med kunddatabaser framkommer dock att maxstorleken för säkerhetskopiering kan vara mindre än dess teoretisk gräns. I detta fall använder ansvarar du för att hantera kvarhållning av säkerhetskopior och åtkomst-o säkerhetskopieringar samt.


### <a name="automated-backup-for-sql-server"></a>Automatiserad säkerhetskopiering för SQL Server
Automatisk säkerhetskopiering innehåller en automatisk säkerhetskopiering för SQL Server Standard och Enterprise-versioner som körs i en Windows-dator i Azure. Den här tjänsten tillhandahålls av den [SQL Server IaaS Agent-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), som installeras automatiskt på SQL Server Windows-avbildningar i Azure-portalen. Om du distribuerar dina egna OS-avbildningar med SQL Server installerat kan behöva du installera VM-tillägg separat. Steg som krävs finns dokumenterade i den här [artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Mer information om funktionerna i den här metoden finns i följande artiklar:

- SQL Server 2014: [Automatiserad säkerhetskopiering för SQL Server 2014-datorer (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatisk säkerhetskopiering v2 för virtuella Azure-datorer (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Titta i dokumentationen, ser du att det förbättrade funktioner med de nyare versionerna av SQLServer. Visa mer information på SQL Server automatiska säkerhetskopieringar släpps i artikeln [SQL Server-hanterad säkerhetskopiering till Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Teoretisk säkerhetskopiering storleksgränsen är 12 TB.  Automatiska säkerhetskopior kan vara en bra metod för säkerhetskopiering storlekar på upp till 12 TB. Eftersom flera blobar skrivs till parallellt, du kan förvänta dig en genomströmning på större än 100 MB per sekund. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup för SQL Server-datorer
Den nya metoden för SQL Server-säkerhetskopieringar erbjuds från och med juni 2018 som offentlig förhandsversion av Azure Backup-tjänster. Metoden för att säkerhetskopiera SQL Server är densamma som andra verktyg från tredje part använder, nämligen SQL Server VSS/VDI gränssnittet till stream säkerhetskopieringar till en målplats. I det här fallet är målplatsen Azure Recovery Service-valv.

En mer än detaljerad beskrivning av den här metoden, vilket ger flera fördelar central säkerhetskopiering konfigurationer, övervakning, och administration är tillgänglig [här](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Lösningar för säkerhetskopiering från tredje part
Det uppstod en antalet SAP-kunder kan ingen risk för att börja om från början och introducera fullständig nya säkerhetskopieringslösningar för del av sina SAP-landskap som kördes på Azure. Därför kan de befintliga säkerhetskopieringslösningar behövs för att användas och utökad till Azure. Utöka befintliga lösningar för säkerhetskopiering till Azure som vanligtvis fungerade bra med de flesta av de viktigaste leverantörerna inom detta område. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Med hjälp av en SQL Server-avbildning från Microsoft Azure Marketplace
Microsoft erbjuder virtuella datorer i Azure Marketplace, som redan innehåller versioner av SQL Server. För SAP-kunder som behöver licenser för SQL Server och Windows, kanske med hjälp av dessa avbildningar en möjlighet att täcka behovet av licenser genom att skapa virtuella datorer med SQL Server redan installerat. Följande överväganden måste göras för att kunna använda sådana bilder för SAP:

* De SQL Server icke-utvärderingsversioner hämta högre kostnader för än en ”endast Windows-VM som distribueras från Azure Marketplace. Se dessa artiklar för att jämföra priser: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> och <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Du kan bara använda SQL Server-versioner som stöds av SAP.
* Sorteringen av SQL Server-instansen som är installerad på virtuella datorer i Azure Marketplace är inte sorteringen SAP NetWeaver kräver SQL Server-instansen ska köras. Du kan ändra sorteringen men med anvisningarna i följande avsnitt.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändra SQL Server-sorteringen för en Microsoft Windows/SQL Server-dator
Eftersom SQL Server-avbildningar på Azure Marketplace inte har ställts in att använda sortering, vilket krävs av SAP NetWeaver-program, måste den ändras direkt efter distributionen. För SQL Server, kan den här ändringen av sortering göras med följande steg när den virtuella datorn har distribuerats och en administratör kan logga in på den distribuerade virtuella datorn:

* Öppna ett Windows-kommandofönster som administratör.
* Ändra katalogen till C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Kör kommandot: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> är det konto som har definierats som administratörskontot när du distribuerar den virtuella datorn för första gången i galleriet.

Processen tar bara några minuter. För att se om steget till slut med rätt resultat, utför du följande steg:

* Öppna SQL Server Management Studio.
* Öppna ett frågefönster.
* Kör kommandot sp_helpsort i SQL Server-huvuddatabasen.

Det önskade resultatet bör se ut:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Om resultatet är olika, stoppa distribuera SAP och undersöka varför installationskommandot inte fungerar som förväntat. Distribution av SAP NetWeaver-program till SQL Server-instansen med olika SQL Server-kodsidor än den som nämns ovan är **inte** stöds.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server hög tillgänglighet för SAP i Azure
Använder SQL Server i Azure IaaS-distributioner för SAP kan ha du flera olika alternativ för att lägga till för att distribuera DBMS-lagret med hög tillgänglighet. Enligt beskrivningen i [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](dbms_guide_general.md) redan Azure har olika serviceavtal för drifttid för en enskild virtuell dator och ett par med virtuella datorer som distribueras i en Azure-Tillgänglighetsuppsättning. Antas att du kör mot det drifttid serviceavtal för dina distributioner av produktion som kräver distribution i Azure-Tillgänglighetsuppsättningar. I sådana fall behöver du distribuera minst två virtuella datorer i sådana en Tillgänglighetsuppsättning. En virtuell dator körs den aktiva instansen av SQL Server. Den andra virtuella datorn körs den passiva instansen

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server Clustering med hjälp av Windows Scale-out filserver
Med Windows Server 2016 introducerade Microsoft [Lagringsdirigering](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Baserat på Storage Spaces Direktdistribution, stöds SQL Server FCI klustring. Information finns i artikeln [Konfigurera SQL Server Redundansklusterinstans på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Lösningen kräver en Azure load balancer för att hantera den virtuella IP-adressen för klusterresurserna. SQL Server-databasfiler lagras i lagringsutrymmen. Det är därför en tanke på att du skulle krävas för att bygga upp Windows-lagringsutrymmen som är baserat på Azure Premium Storage. Eftersom den här lösningen har stöd för för länge har ännu inte, finns det inga kända SAP-kunder som använder den här lösningen i SAP-produktionsscenarier.  

### <a name="sql-server-log-shipping"></a>SQLServer-Loggöverföring
En av metoderna för hög tillgänglighet (HA) är SQL Server-Loggöverföring. Om de virtuella datorerna som deltar i konfiguration för hög tillgänglighet har arbeta namnmatchning, inga problem och konfiguration i Azure skiljer sig inte från några inställningar som görs på plats. För att konfigurera Loggöverföring och principerna runt Loggöverföring. Information om SQL Server-Loggöverföring finns i artikeln [om Loggöverföring (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

SQL Server-funktioner för loggöverföring användes knappt i Azure för att uppnå hög tillgänglighet inom en Azure-region. I följande scenarier SAP-kunder men använder loggöverföring lyckad tillsammans med Azure:

- Scenarier för haveriberedskap från en Azure-region till en annan Azure-region
- Haveriberedskapskonfiguration från lokalt till en Azure-region
- Klipp ut över scenarier från lokalt till Azure. I sådana fall används loggöverföring för att synkronisera den nya distributionen DBMS i Azure med den pågående produktion system på plats. Vid tidpunkten för övergripande över produktion är avstängd och det görs till att säkerhetskopieringarna av transaktionsloggen för senaste och de senaste fick överföras till Azure DBMS-distributionen. Sedan är DBMS i Azure-distribution öppet för produktion.  



### <a name="database-mirroring"></a>Databasspegling
Databasen spegling som stöds av SAP (Se SAP-kommentar [965908]) förlitar sig på Definiera redundanspartner i SAP-anslutningssträngen. I fall som flera platser förutsätter vi att de två virtuella datorerna är i samma domän och att användaren kontext två SQL Server-instanser körs under en domänanvändare och har tillräcklig behörighet i två SQL Server-instanserna som ingår. Därför skiljer inte installationen av databasspegling i Azure mellan en typisk lokala/installationskonfiguration.

Från och med molnbaserade distributioner är det enklaste sättet att installationsprogrammet ska en annan domän i Azure för att ha dessa DBMS virtuella datorer (och helst dedikerade virtuella SAP-datorer) i en domän.

Om en domän inte är möjligt kan en också använda certifikat för slutpunkter för databasspegling, enligt nedan: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

En självstudie om du vill konfigurera databasspegling i Azure finns här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQLServer Always On
Som alltid på stöds för SAP on-premises (Se SAP-kommentar [1772688]), det stöds i kombination med SAP i Azure. Det finns några särskilda överväganden kring distribution av SQL Server tillgänglighetsgruppens lyssnare (inte ska inte förväxlas med Azure-Tillgänglighetsuppsättning) eftersom Azure vid denna tidpunkt inte tillåter att skapa ett AD/DNS-objekt eftersom det är möjligt på plats. Därför är vissa olika installationsstegen nödvändiga för att lösa specifika beteendet för Azure.

Vissa förutsättningar med hjälp av en Tillgänglighetsgruppslyssnare är:

* Med hjälp av en Tillgänglighetsgruppslyssnare är bara möjligt med Windows Server 2012 eller senare som gästoperativsystem för den virtuella datorn. För Windows Server 2012 måste du se till att korrigeringsfilen tillämpas: <https://support.microsoft.com/kb/2854082> 
* För Windows Server 2008 R2 finns inte korrigeringen och Always On måste användas på samma sätt som databasspegling genom att ange redundanspartner i anslutningssträngen (via SAP default.pfl parametern dbs/mss/server - Se SAP-kommentar [965908]).
* När du använder en Tillgänglighetsgruppslyssnare, måste den virtuella vara anslutna till en särskild belastningsutjämnare. För att undvika att Azure tilldelar nya IP-adresser i fall där båda virtuella datorerna kanske lägger är stänga av ska en tilldela statiska IP-adresser till nätverksgränssnitt för de virtuella datorer i konfigurationen för Always On (definierar en statisk IP-adress beskrivs i [detta] [ virtual-networks-reserved-private-ip] artikeln)
* Det finns särskilda åtgärder krävs om att skapa WSFC-klusterkonfiguration där klustret behöver en särskild IP-adress har tilldelats, eftersom Azure med dess aktuella funktionerna tilldelar klusternamnet samma IP-adress som noder i klustret har skapats på. Det innebär att ett manuellt steg måste utföras för att tilldela en annan IP-adress till klustret.
* Tillgänglighetsgruppslyssnaren kommer att skapas i Azure med TCP/IP-slutpunkter, som är tilldelade till de virtuella datorerna som kör de primära och sekundära replikerna för tillgänglighetsgruppen.
* Det kan finnas ett behov av att skydda dessa slutpunkter med ACL: er.

Utförlig information om hur du distribuerar alltid är aktiverat med SQL Server i virtuella datorer i Azure listar som:

- [Introduktion till SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Konfigurera en Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Konfigurera en belastningsutjämnare för en Always On-tillgänglighetsgrupp i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Kontrollera att DirectServerReturn konfigureras om du konfigurerar Azure-belastningsutjämnaren för den virtuella IP-adressen på tillgänglighetsgruppens lyssnare. Konfigurera det här alternativet minskar nätverket avrunda resans fördröjning mellan SAP-programnivån och DBMS-lagret. 

SQL Server Always On är de vanligaste används hög tillgänglighet och funktioner för haveriberedskap som används i Azure för distribution av SAP. De flesta kunder använda Always On för hög tillgänglighet inom en enda Azure-Region. Om distributionen är begränsad till två noder, har du två val för anslutning:

- Med tillgänglighetsgruppens lyssnare. Med tillgänglighetsgruppens lyssnare måste du distribuera en Azure load balancer. Detta är vanligtvis standardmetoden för distribution. SAP-program skulle konfigureras för att ansluta mot tillgänglighetsgruppens lyssnare och inte mot en enskild nod
- Med hjälp av parametrarna anslutningen för databasspegling i SQL Server. I så fall måste du konfigurera anslutningen av SAP-program på ett sätt där båda nodnamn är namngivna. Mer information om sådana en SAP-sida-konfigurationen dokumenteras i SAP-kommentar [#965908](https://launchpad.support.sap.com/#/notes/965908). Med hjälp av det här alternativet behöver du behöver inte konfigurera en lyssningsfunktion. Och med som inga Azure belastningsutjämnare för SQL Server hög tillgänglighet. Nätverksfördröjningen mellan SAP-programnivån och DBMS-lagret är därför lägre eftersom den inkommande trafiken till SQL Server-instansen inte dirigeras via Azure-belastningsutjämnaren. Men kom ihåg att det här alternativet fungerar bara om du begränsar din tillgänglighetsgrupp som sträcker sig över två instanser. 

Ett ganska stort antal kunder använder sig av SQL Server Always On-funktionerna för funktioner för ytterligare haveriberedskap mellan Azure-regioner. Flera användare kan du även använda möjligheten för att utföra säkerhetskopieringar från en sekundär replik. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Transparent datakryptering
Det finns ett antal kunder som använder SQL Server [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) när distribuera sina SAP SQL Server-databaser i Azure. SQL Server TDE-funktioner stöds fullt ut av SAP (Se SAP-kommentar [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Använda SQLServer transparent Datakryptering
I fall där du genomför en heterogen migrering från en annan DBMS, som körs lokalt till Windows/SQL Server som körs i Azure, bör du skapa tom måldatabasen i SQL Server i tid. Du skulle använda SQL Server TDE-funktioner som nästa steg. Medan du fortfarande kör din produktion system på plats. Du vill utföra i den här sekvensen beror på att processen för att kryptera den tomma databasen kan ta ett tag. Processer för SAP-import skulle sedan att importera data till den krypterade databasen under fasen för driftstopp. Arbetet med att importera till en krypterad databas påverkar vägen lägre tid än att kryptera databasen efter exportfas i äldre tid fas. Negativt upplevelser med vid försök att använda transparent Datakryptering med SAP-arbetsbelastningar som körs ovanpå databasen. Rekommendation därför behandlar distributionen av TDE som en aktivitet som måste göras utan SAP-arbetsbelastningen på databasen.

I fall där du flyttar SAP SQL Server-databaser från en lokal till Azure kan rekommenderar vi testning på vilken infrastruktur som du kan hämta krypteringen tillämpas snabbaste. Ha dessa uppgifter för detta i åtanke:

- Du kan inte definiera hur många trådar som används för att tillämpa kryptering av data till databasen. Antalet trådar är majorly beroende på antalet volymer på diskar i SQL Server-data och loggfiler filer ska distribueras över. Innebär fler olika volymer (enhetsbeteckningar), kommer engagerade flera trådar parallellt för krypteringen. En sådan konfiguration strider mot lite med tidigare disk configuration förslag på att skapa en eller ett mindre antal lagringsutrymmen för SQL Server-databasfiler i Azure virtuella datorer. En konfiguration med ett litet antal volymer skulle leda till ett litet antal trådar som körs krypteringen. En tråd som krypterar läser 64KB utrymmen, krypterar dem och sedan skriva en post i transaktionsloggfilen som talar om att utsträckning som har krypterats. Därför är belastningen på transaktionsloggen begränsad.
- I äldre versioner av SQL Server, komprimering av säkerhetskopior inte att hämta effektivitet längre när du krypterade din SQL Server-databas. Det här beteendet kan utveckla får problem när din plan var att kryptera dina SQL Server-databas på plats och kopierar sedan en säkerhetskopia till Azure för att återställa databasen i Azure. Komprimering av säkerhetskopior i SQL Server ger vanligtvis en komprimeringsgrad på faktor 4.
- Med SQL Server 2016 introducerades SQL Server de nya funktioner som gör att komprimera krypterade databaser samt på ett effektivt sätt. Se [den här bloggar](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) för vissa detaljer.
 
Behandla tillämpningen av TDE-kryptering med Nej om du vill bara lite SAP-arbetsbelastningar, bör du testa i din specifika konfiguration på om det är bättre att tillämpa TDE på din SAP-databas lokalt eller för att göra det i Azure. I Azure, du däremot mer flexibilitet i fråga över etablering infrastruktur och minska infrastrukturen när TDE har tillämpats.

### <a name="using-azure-key-vault"></a>Med Azure Key Vault
Azure erbjuder tjänsten av en [Key Vault](https://azure.microsoft.com/services/key-vault/) krypteringsnycklar. SQL Server på den andra sidan erbjuder en anslutning för att dra nytta av Azure Key Vault som store för TDE-certifikat.

Mer information om du vill använda Azure Key Vault för SQL Server transparent Datakryptering visas som:

- [Utökningsbar nyckelhantering med Azure Key Vault (SQLServer)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE utökningsbar nyckelhantering med Azure Key Vault – steg för konfiguration av](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Connector underhåll och felsökning](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Fler frågor från kunder om SQL Server Transparent datakryptering – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Med hjälp av SQL Server transparent Datakryptering, särskilt i Azure key Vault, rekommenderar vi att du använder de senaste uppdateringarna av SQL Server 2014 och SQL Server 2016 SQL Server 2017. Anledningen är att baserat på feedback från kunder, optimeringar och korrigeringar läggs till i koden. Till exempel kontrollera [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Allmänt SQL Server för SAP på Azure sammanfattning
Det finns många rekommendationer i den här guiden och vi rekommenderar att du läsa den mer än en gång innan du planerar din Azure-distribution. I allmänhet dock måste du följa den övre Allmänt DBMS på Azure-specifika rekommendationer:

1. Använd den senaste DBMS-versionen som SQL Server 2017, som har de flesta fördelar i Azure. 
2. Planera noggrant ditt SAP-landskap system i Azure för att balansera data filens layout och Azure-begränsningar:
   * Inte har för många diskar, men har tillräckligt för att se till att du kan nå din krävs IOPS.
   * Om du inte använder Managed Disks, Kom ihåg att IOPS är också begränsad per Azure Storage-konto och att konton begränsas i varje Azure-prenumeration ([mer][azure-subscription-service-limits]). 
   * Endast stripe över diskar om du behöver att uppnå ett högre dataflöde.
3. Aldrig installera program eller placera alla filer som kräver persistence på enheten D:\ eftersom det är flyttbara och allt på den här enheten går förlorade vid en omstart av Windows.
4. Använd inte diskcachelagring för Azure Standard Storage.
5. Använd inte Azure geo-replikerat Azure Standard Storage-konton.  Använd lokalt Redundant för DBMS arbetsbelastningar.
6. Använda DBMS leverantörens hr/DR-lösning för att replikera databasdata.
7. Alltid använda namnmatchning, inte förlita dig på IP-adresser.
8. Med SQL Server TDE kan tillämpa de senaste uppdateringarna för SQL Server.
9. Använd högsta databasen komprimering så mycket som möjligt. Vilket är sidan komprimering för SQL Server.
10. Var försiktig med hjälp av SQL Server-avbildningar från Azure Marketplace. Om du använder en SQL Server, måste du ändra instansens sortering innan du installerar alla SAP NetWeaver-system på den.
11. Installera och konfigurera SAP-värden som övervakning för Azure enligt beskrivningen i [Distributionsguide][deployment-guide].
