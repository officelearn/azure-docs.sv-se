---
title: Azure virtuella datorer DBMS-distribution för SAP NetWeaver | Microsoft Docs
description: Azure virtuella datorer DBMS-distribution för SAP NetWeaver
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
ms.openlocfilehash: 79e77aa067cbb7262a945d94ce8ac1750e80b2d5
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054797"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Azure virtuella datorer DBMS-distribution för SAP NetWeaver
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/en-us/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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

Den här handboken är en del av dokumentationen för implementering och distribuera SAP-programvara i Microsoft Azure. Innan du läser den här guiden kan du läsa den [Implementeringsguide för planering och][planning-guide]. Det här dokumentet beskriver distributionen av olika relationell databas Management Systems (RDBMS) och relaterade produkter i kombination med SAP på Microsoft Azure virtuella datorer (VM) med hjälp av Azure-infrastrukturen som en tjänst (IaaS)-funktioner.

Dokumentet kompletterar SAP dokumentationen och SAP anteckningar, som representerar primära resurser för installation och distribution av program på den angivna plattformar.

## <a name="general-considerations"></a>Allmänna överväganden
I det här kapitlet introduceras överväganden SAP-relaterade DBMS system som körs i virtuella Azure-datorer. Det finns några referenser till specifika DBMS system i det här kapitlet. I stället hanteras specifika DBMS-system i det här dokumentet, efter det här kapitlet.

### <a name="definitions-upfront"></a>Definitioner förskott
Vi använder följande termer för hela dokumentet:

* IaaS: Infrastruktur som en tjänst.
* PaaS: Plattform som en tjänst.
* SaaS: Programvara som en tjänst.
* SAP komponent: ett enskilt SAP program, till exempel ECC BW, lösning Manager eller EP.  SAP-komponenter kan baseras på traditionell ABAP eller Java-teknik eller icke-NetWeaver baserat program, till exempel Business-objekt.
* SAP-miljö: en eller flera komponenter för SAP logiskt grupperade för att utföra en business-funktion, till exempel utveckling, QAS, träning, DR eller produktion.
* SAP liggande: Avser hela SAP tillgångarna i kundens IT-miljön. SAP-liggande innehåller alla produktions- och icke-produktionsmiljöer.
* SAP System: Kombinationen av DBMS lager- och programnivå av, till exempel en SAP ERP-utvecklingssystemet, SAP BW testsystemet, SAP CRM produktionssystem osv. I Azure-distributioner går det inte för att dela dessa två lager mellan lokala och Azure. Detta innebär en SAP-systemet är antingen distribueras lokalt eller den har distribuerats i Azure. Du kan dock distribuera olika system för en SAP liggande i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testsystem i Azure, men den SAP CRM produktion system lokalt.
* Endast molnbaserad distribution: en distribution där Azure-prenumerationen inte är ansluten via ett plats-till-plats eller en ExpressRoute-anslutning till lokala nätverkets infrastruktur. Gemensam dokumentation för Azure dessa typer av distributioner beskrivs också som ”endast molnbaserad” distributioner. Virtuella datorer distribueras med den här metoden kan nås via Internet och offentliga Internet-slutpunkter som tilldelats virtuella datorer i Azure. Lokalt Active Directory (AD) och DNS inte har utökats till Azure i dessa typer av distributioner. De virtuella datorerna är därför inte en del av Active Directory lokalt. Obs: Endast molnbaserad distributioner i det här dokumentet har definierats som slutförts SAP natur, som körs i Azure utan utökning av Active Directory eller namnmatchning från lokala i offentliga moln. Endast molnbaserad konfigurationer stöds inte för SAP produktionssystem eller konfigurationer där SAP STM eller andra lokala resurser måste användas mellan SAP-system som finns på Azure och resurser som finns lokalt.
* Anslutningar mellan lokala: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats, flera platser eller ExpressRoute-anslutningen mellan lokala datacenter(s) och Azure. Gemensamma Azure-dokumentationen dessa typer av distributioner beskrivs också som mellan lokala scenarier. Anslutningen orsaken är att utöka lokala domäner, lokala Active Directory och lokala DNS i Azure. Lokala liggande utökas till Azure tillgångar i prenumerationen. Med det här tillägget, kan de virtuella datorerna vara en del av den lokala domänen. Domänanvändare på den lokala domänen kan komma åt servrar och tjänster kan köra på dessa virtuella datorer (till exempel DBMS services). Kommunikation och namnmatchning mellan virtuella datorer distribueras lokala och virtuella datorer som distribueras i Azure är möjligt. Vi räknar med att det vanligaste scenariot för att distribuera SAP tillgångar på Azure. Mer information finns i [i den här artikeln] [ vpn-gateway-cross-premises-options] och [i den här artikeln][vpn-gateway-site-to-site-create].

> [!NOTE]
> Anslutningar mellan lokala distributioner av SAP-system där Azure virtuella datorer som kör SAP-system är medlemmar i en lokal domän stöds för produktion SAP-system. Anslutningar mellan lokala konfigurationer stöds för att distribuera delar eller slutföra SAP landskap till Azure. Även kör fullständig SAP liggande i Azure kräver att dessa virtuella datorer som en del av lokal domän och Active Directory. I tidigare versioner av dokumentationen hittills har diskuterats Hybrid-IT scenarier där termen *Hybrid* är rotad i det faktum att det finns en anslutning mellan Azure och lokalt. I det här fallet *Hybrid* också innebär att virtuella datorer i Azure är en del av Active Directory lokalt.
> 
> 

Vissa Microsoft-dokumentationen beskriver anslutningar mellan lokala scenarier lite annorlunda särskilt för DBMS HA konfigurationer. Vid SAP-relaterade-dokument delas mellan lokala scenariot kokar ned till en plats-till-plats eller privat (ExpressRoute)-anslutning och att SAP liggande mellan lokalt och Azure.

### <a name="resources"></a>Resurser
Följande guider är tillgängliga för SAP-distribution på Azure:

* [Azure virtuella datorer planering och implementering för SAP NetWeaver][planning-guide]
* [Distribution av Azure virtuella datorer för SAP NetWeaver][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP NetWeaver (det här dokumentet)][dbms-guide]

Följande information för SAP är relaterade till SAP i Azure:

| Nummer | Titel |
| --- | --- |
| [1928533] |SAP-program i Azure: produkter och Virtuella Azure-typer |
| [2015553] |SAP på Microsoft Azure: stöd för krav |
| [1999351] |Felsökning av utökad Azure övervakning för SAP |
| [2178632] |Key som övervakning mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering på Windows: förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: förbättrad övervakning |
| [2039619] |SAP-program i Microsoft Azure med hjälp av Oracle-databasen: produkter och versioner |
| [2233094] |DB6: SAP-program på Azure med hjälp av IBM DB2 för Linux, UNIX- och Windows - ytterligare Information |
| [2243692] |Linux på Microsoft Azure (IaaS) VM: SAP problem |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2069760] |Oracle Linux 7.x SAP Installation och uppgradering |
| [1597355] |Växlingsutrymme rekommendation för Linux |
| [2171857] |Oracle Database 12c - filen stöd på Linux |
| [1114181] |Oracle Database 11g - filen stöd på Linux |


Läs även den [Tillståndsändringsavisering Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) som innehåller alla SAP anteckningar för Linux.

Du bör ha kunskaper om Microsoft Azure-arkitekturen och hur Microsoft Azure virtuella datorer distribueras och drivas. Du hittar mer information <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Vi kan **inte** diskutera Microsoft Azure-plattformen som en tjänst (PaaS)-erbjudanden för Microsoft Azure-plattformen. Det här dokumentet handlar om hur du kör ett databashanteringssystem (DBMS) i Microsoft Azure Virtual Machines (IaaS) som du vill köra DBMS i din lokala miljö. Databasen funktioner och funktionalitet mellan dessa två erbjudanden skiljer sig väldigt mycket och bör inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Eftersom vi diskuterar IaaS är i allmänhet Windows, Linux och DBMS installationen och konfigurationen i stort sett desamma som en virtuell dator eller bare metal datorn installerar du lokal. Det finns dock vissa arkitektur och systemhantering implementering beslut, som skiljer sig när du använder IaaS. Syftet med det här dokumentet är att förklara specifika arkitektur och system management skillnader som du måste förberedas för när du använder IaaS.

I allmänhet är de övergripande delarna av skillnaden som beskrivs i det här dokumentet:

* Planera rätt VM/disklayouten för SAP-datorer så att du har rätt data filen layout och kan få tillräckligt med IOPS för din arbetsbelastning.
* Nätverk överväganden när du använder IaaS.
* Specifika databasfunktioner ska användas för att optimera layout för databasen.
* Överväganden för säkerhetskopiering och återställning i IaaS.
* Använda olika typer av avbildningar för distribution.
* Hög tillgänglighet i Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktur för en RDBMS-distribution
För att följa det här kapitlet, är det nödvändigt att förstå vad som angavs i [detta] [ deployment-guide-3] kapitel i den [Deployment Guide][deployment-guide]. Information om annan VM-serien och deras skillnader och skillnader i Azure Standard och Premium-lagring ska förstått och kända innan du läser det här kapitlet.

Diskar som innehåller ett operativsystem har begränsad till 127 GB i storlek tills mars 2015. Den här begränsningen har lyfts i mars 2015 (för mer information kontrollen <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Därifrån på diskar som innehåller operativsystemet kan ha samma storlek som någon annan disk. Vi föredrar dock fortfarande en struktur för distribution där operativsystemet, DBMS och eventuell SAP-binärfiler skiljer sig från databasfilerna. Vi förvänta dig därför SAP-system som körs i Azure Virtual Machines har den grundläggande VM (eller disk) som installerats med operativsystemet, databasen management system körbara filer och SAP körbara filer. DBMS data och loggfilen filerna lagras i Azure-lagring (Standard eller Premium-lagring) i separata diskar och anslutna som logiska diskar på den ursprungliga Azure operativsystemavbildningen VM. 

Beroende på utnyttja Azure Standard eller Premium-lagring (till exempel med hjälp av DS-serien eller GS-serien VMs) det finns andra kvoter i Azure, som finns dokumenterade [här (Linux)] [ virtual-machines-sizes-linux] och [ här (Windows)][virtual-machines-sizes-windows]. När du planerar din disklayouten, måste du hitta en bra balans av kvoter för följande objekt:

* Antalet datafiler.
* Antal diskar som innehåller filerna.
* IOPS kvoter för en enskild disk.
* Datagenomströmning per disk.
* Antal ytterligare hårddiskar som är möjligt per VM-storlek.
* Det totala genomflödet som en virtuell dator kan ge.

Azure tillämpar en kvot för IOPS per datadisk. Dessa kvoter är olika för diskar som finns i Azure standardlagring och Premium-lagring. I/o-svarstiderna är också mycket olika mellan de två typerna av lagring med Premium-lagring och leverera faktorer bättre svarstider för i/o. Var och en av de olika typerna av virtuell dator har ett begränsat antal datadiskar som du ska kunna ansluta. En annan begränsning är att bara vissa VM-typer kan utnyttja Azure Premium-lagring. Det innebär att beslut för en viss typ av virtuell dator inte kan endast drivas av CPU och minne krav, utan även av IOPS, svarstid och disk genomströmning krav som vanligtvis skalas med antalet diskar eller typen av diskar för Premium-lagring. Särskilt med Premium-lagring kan storleken på en disk också avgöras av antalet IOPS och dataflöde som behöver uppnås av varje disk.

Det faktum att den övergripande IOPS-hastigheten, antalet diskar monterade och storleken på den virtuella datorn är alla knutna tillsammans, kan leda till en Azure-konfiguration om en SAP-system som skiljer sig från den lokala distributionen. IOPS-gränser för LUN kan vanligtvis konfigureras i lokala distributioner. Med Azure Storage dessa gränser är fast eller som ett Premium-lagring beroende på typ av disk. Med lokala distributioner visas så kundkonfigurationer för databasservrar som använder många olika volymer för särskilda körbara filer som SAP och DBMS eller särskilda volymer för tillfälliga databaser eller tabell blanksteg. När sådana ett lokalt system har flyttats till Azure kan det leda till slöseri med potentiella IOPS bandbredd genom att spara en disk för körbara filer eller databaser som inte utför någon eller inte mycket IOPS. I Azure Virtual Machines rekommenderar vi därför att DBMS och SAP körbara filer installeras på OS-disk om möjligt.

Placeringen av databasfilerna och loggfilerna och vilken typ av Azure Storage används, ska definieras av IOPS, svarstid och genomströmning krav. För att du har tillräckligt med IOPS för transaktionsloggen kan du tvingas att använda flera diskar för transaktionsloggfilen eller Använd en större disk i Premium-lagring. I så fall skulle en bygga en programvarubaserad RAID (till exempel Windows Storage Pool för Windows eller MDADM och LVM (logisk volym Manager) för Linux) med diskar som innehåller transaktionsloggen.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Enhet D:\ i en Azure VM är en icke-beständig enhet som backas upp av vissa lokala diskar på Azure compute-nod. Eftersom det är icke-beständig, innebär det att ändringar som görs till innehåll på D:\ enheten går förlorad när den virtuella datorn har startats om. Med ”ändringar” menar vi sparade filer, kataloger som skapas, program och så vidare.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux virtuella Azure-datorer ansluta automatiskt en enhet i /mnt/resource som är en icke-beständig enhet som backas upp av lokala diskar på Azure compute-nod. Detta innebär att ändringar som görs till innehåll i /mnt/resource försvinner när den virtuella datorn startas om eftersom den är icke-beständig. Vi menar av ändringar, filer som sparats, kataloger som skapas, program och så vidare.
> 
> 

- - -
Beroende på Azure VM-serien, lokala diskar på Beräkningsnoden visar olika prestanda, vilket kan kategoriseras som:

* A0 A7: Mycket begränsad prestanda. Inte användas för något utöver växlingsfil för windows
* A8-A11: Goda prestandaegenskaper med vissa tiotusen IOPS och > 1 GBIT/s genomströmning
* D-serien: Goda prestandaegenskaper med vissa tiotusen IOPS och > 1 GBIT/s genomströmning
* DS-serien: Goda prestandaegenskaper med vissa tiotusen IOPS och > 1 GBIT/s genomströmning
* G-serien: Goda prestandaegenskaper med vissa tiotusen IOPS och > 1 GBIT/s genomströmning
* GS-serien: Goda prestandaegenskaper med vissa tiotusen IOPS och > 1 GBIT/s genomströmning

Uttryck ovanför tillämpas för VM-typer som är certifierade med SAP. VM-serien med utmärkt IOPS och genomströmning uppfyller kraven för att dra nytta av vissa DBMS-funktioner, t.ex. tempdb eller temporärt tabellutrymme.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och diskar
När vi skapar datadiskar via portalen eller när vi montera överförda diskar till virtuella datorer, kan vi välja om i/o-trafik mellan den virtuella datorn och de diskar som finns i Azure storage cachelagras. Azure Standard och Premium-lagring kan du använda två olika tekniker för den här typen av cachen. I båda fallen är cachen själva disken säkerhetskopieras på samma enheter som används av den virtuella datorn tillfälliga disk (D:\ i Windows) eller /mnt/resource på Linux.

För Azure standardlagring är möjliga cache-typer:

* Ingen cachelagring
* Läsa cachelagring
* Läsa och skriva cachelagring

För att få konsekvent och deterministisk prestanda bör du ange cachelagring på Azure standardlagring för alla diskar som innehåller **DBMS-relaterade datafiler, loggfiler och tabellutrymme till ”NONE”**. Cachelagring av den virtuella datorn kan förbli med standardinställningarna.

För Azure Premium Storage finns följande alternativ för cachelagring

* Ingen cachelagring
* Läsa cachelagring

Rekommendation för Azure Premium-lagring är att utnyttja **läsa cachelagring för filer** för SAP-databasen och valt **ingen cachelagring för diskar loggfilerna**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Programvarubaserad RAID
Angivits redan ovan, måste du väga antalet IOPS som behövs för databasfilerna över antalet diskar som du kan konfigurera och högsta IOPS en Azure VM ger per disk- eller Premium-lagring disktyp. Hantera IOPS belastningen över diskar enklast att skapa en programvarubaserad RAID över olika diskar. Placera sedan ett antal filer i SAP-DBMS på LUN högg utanför programvarubaserad RAID. Beroende på vilka krav som du kanske vill överväga användning av Premium-lagring samt sedan två av tre olika Premium-lagring diskar har högre IOPS kvot än diskar baserat på standardlagring. Förutom den betydande bättre i/o-svarstid som tillhandahålls av Azure Premium-lagring. 

Detsamma gäller för transaktionsloggen för de olika DBMS-system. I många av dem att lägga till fler Tlog filer hjälper inte eftersom DBMS-system att skriva till en av filerna på bara en gång. Om högre IOPS priser behövs än en enda standardlagring baserad disk kan leverera, du kan stripe över flera standardlagring diskar eller du kan använda en större disktyp för Premium-lagring som utöver högre IOPS priser faktorer ger också kortare svarstid för skrivning jag / OS i transaktionsloggen.

Situationer som uppstått i Azure-distributioner som skulle ge företräde åt med en programvarubaserad RAID är:

* Loggen/gör om transaktionsloggen kräver fler IOPS än Azure tillhandahåller för en enskild disk. Som nämnts ovan detta kan lösas genom att skapa ett LUN över flera diskar med hjälp av en programvarubaserad RAID.
* En ojämn i/o arbetsbelastning distribution över de olika filerna av SAP-databasen. I sådana fall kan en uppleva en datafil träffa kvoten snarare ofta. Medan andra filer inte får även nära IOPS kvoten för en enskild disk. I sådana fall är den enklaste lösningen att skapa en LUN över flera diskar med hjälp av en programvarubaserad RAID. 
* Du vet inte vad exakt i/o-arbetsbelastning per fil och bara ungefär övergripande IOPS arbetsbelastningen mot DBMS är okänt. Enklast att göra är att skapa en LUN med hjälp av en programvarubaserad RAID. Summan av kvoter för flera diskar bakom denna LUN bör sedan uppfylla kända IOPS hastighet.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Vi rekommenderar att du använder Windows lagringsutrymmen om du kör på Windows Server 2012 eller senare. Det är effektivare än Windows Striping av tidigare versioner av Windows. Du kan behöva skapa Windows lagringspooler och lagringsutrymmen med PowerShell-kommandon när du använder Windows Server 2012 som operativsystem. PowerShell-kommandon finns här <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Endast MDADM och LVM (logisk volym Manager) kan användas för att skapa en programvarubaserad RAID på Linux. Mer information finns i följande artiklar:
> 
> * [Konfigurera programvarubaserad RAID på Linux] [ virtual-machines-linux-configure-raid] (för MDADM)
> * [Konfigurera LVM på en virtuell Linux-dator i Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Överväganden för att kunna utnyttja VM-serien, vilket kommer arbeta med Azure Premium-lagring vanligtvis är:

* Krav för i/o-latens som är nära SAN/NAS-enheter leverera.
* Begäran för faktorer bättre i/o-svarstid än Azure standardlagring kan ge.
* Högre IOPS per virtuell dator än vad som kan uppnås med flera standardlagring diskar mot en viss typ av virtuell dator.

Eftersom den underliggande Azure Storage replikerar varje disk till minst tre lagringsnoder enkel RAID 0 striping kan användas. Det finns inget behov av att implementera RAID5 eller RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure Storage lagrar grundläggande VM (med OS) och diskar eller BLOB till minst tre separata lagringsnoder. När du skapar ett lagringskonto eller hanterade diskar, är det ett val av skydd som visas här:

![GEO-replikering är aktiverat för Azure Storage-konto][dbms-guide-figure-100]

Azure Storage lokal replikering (lokalt Redundant) ger skydd mot dataförlust på grund av fel i infrastruktur som fåtal kunder kan råd att distribuera. Som visas ovanför det finns fyra olika alternativ med en femte som en variant av en av de första tre. Titta närmare på dem kan vi skilja:

* **Premium lokalt Redundant lagring (LRS)**: Azure Premium Storage ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Det finns tre kopior av data i samma Azure-datacentret för en Azure-region. Kopiorna är i olika fel och uppgradera domäner (begrepp finns [detta] [ planning-guide-3.2] kapitlet i den [Planeringshandboken][planning-guide]). En ny replik genereras automatiskt vid en replik av data som skickas från tjänsten på grund av ett nodfel för lagring eller diskfel.
* **Lokalt Redundant lagring (LRS)**: I det här fallet finns tre kopior av data i samma Azure-datacentret för en Azure-region. Kopiorna är i olika fel och uppgradera domäner (begrepp finns [detta] [ planning-guide-3.2] kapitlet i den [Planeringshandboken][planning-guide]). En ny replik genereras automatiskt vid en replik av data som skickas från tjänsten på grund av ett nodfel för lagring eller diskfel. 
* **GEO-Redundant lagring (GRS)**: I det här fallet är det en asynkron replikering som en ytterligare tre kopior av data i en annan Azure-Region som är i de flesta fall i samma geografiska region (till exempel Norra Europa och västra Europa ). Detta resulterar i tre ytterligare repliker, så att det finns sex repliker i summan. En variant av det här är ett tillägg där data i geo-replikerade Azure-regionen kan användas för skrivskyddade (läsbehörighet Geo-Redundant).
* **Zonen Redundant lagring (ZRS)**: I det här fallet tre kopior av data finns kvar i samma Azure-Region. Enligt beskrivningen i [detta] [ planning-guide-3.1] kapitel i den [Planeringshandboken] [ planning-guide] en Azure-region kan vara ett tal för datacenter i närheten. När det gäller LRS skulle replikerna distribueras över olika datacenter som gör en Azure-region.

Mer information hittar du [här][storage-redundancy].

> [!NOTE]
> För DBMS distributioner rekommenderas inte användning av Geo-Redundant lagring
> 
> Azure Storage Geo-replikering är asynkron. Replikering av enskilda diskarna monteras på en enda virtuell dator har inte synkroniserats i Lås steg. Det är därför inte lämpligt att replikera DBMS-filer som är fördelade över olika diskar eller distribueras mot en programvarubaserad RAID baserat på flera diskar. DBMS-programmet kräver att beständiga disklagring exakt är synkroniserad över olika LUN och underliggande diskar/axlar. DBMS programvaran använder olika metoder för sekvens-i/o skrivåtgärder aktiviteter och ett DBMS rapporterar att disklagring mål för replikering är skadad om dessa varierar även av några millisekunder. Därför om en vill ha en konfiguration med en-databas har sträckts ut över flera diskar georeplikerad måste sådana replikering utföras med databasen innebär och funktioner. En bör inte lita på Azure Storage Geo-replikering att utföra jobbet. 
> 
> Problemet är enklast att förklara med ett system som exempel. Anta att du har ett SAP-system som överförts till Azure, vilket har åtta diskar som innehåller datafiler i DBMS plus en disk som innehåller transaktionsloggfilen. Var och en av diskarna nio har data som skrivs till dem i ett konsekvent sätt enligt DBMS om data skrivs till data eller transaktionen loggfiler.
> 
> För att korrekt geo-replikering av data och bibehålla en konsekvent databasen avbildning i innehållet i alla nio diskar måste vara georeplikerad i angiven ordning i/o-åtgärder har utförts mot nio olika diskar. Azure Storage geo-replikering tillåter inte för att deklarera beroenden mellan diskar. Det innebär att Microsoft Azure Storage geo-replikering inte vet om det faktum att innehållet i dessa nio olika diskar är relaterade till varandra och att ändringarna som är konsekventa endast när replikering av i/o-åtgärder i ordningen som inträffade för alla de nio diskarna.
> 
> Förutom risken att hög att georeplikerad bilderna i scenariot inte ger en konsekvent databasen avbildning, är det också en prestandaförsämring som visas med geo-redundant lagring som kan påverka prestanda negativt. Sammanfattningsvis: Använd inte den här typen av lagring redundans för DBMS typen arbetsbelastningar.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mappningen av virtuella hårddiskar till virtuella Azure-datorn tjänstkonton för lagring
Det här kapitlet gäller bara för Azure Storage-konton. Om du planerar att använda hanterade diskar gäller inte begränsningarna som nämns i detta kapitel. Mer information om hanterade diskar finns i kapitlet [hanterade diskar] [ dbms-guide-managed-disks] i den här handboken.

Ett Azure Storage-konto är en administrativ konstruktion utan också ett ämne begränsningar. Medan begränsningarna beror på om vi pratar om ett Azure-Standard Storage-konto eller ett Azure Premium Storage-konto. De exakta möjligheter och begränsningar finns [här][storage-scalability-targets]

För Azure standardlagring är det viktigt att Observera att det finns en gräns på IOPS per storage-konto (som innehåller **totala hastigheten för begäran** i [artikeln][storage-scalability-targets]). Det finns också en inledande högst 100 Lagringskonton per Azure-prenumeration (från och med juli 2015). Vi rekommenderar därför att balansera IOPS för virtuella datorer mellan flera lagringskonton när du använder Azure standardlagring. Medan en enda virtuell dator använder vi ett lagringskonto om möjligt. Om vi pratar om DBMS-distributioner där varje virtuell Hårddisk som finns på Azure standardlagring kunde nå sin kvot bör du därför bara distribuera 30-40 virtuella hårddiskar per Azure Storage-konto som använder Azure standardlagring. Å andra sidan, om du använder Azure Premium-lagring och vill lagra stor databas volymer, kanske du bra som IOPS. Men en Azure Premium Storage-konto är sätt mer restriktiva i datavolym än en Azure-Standard Storage-konto. Därför kan du bara distribuera ett begränsat antal virtuella hårddiskar inom Azure Premium Storage-konto innan träffa data Volymgränsen. Se ett Azure Storage-konto som en ”virtuell SAN” som har begränsade möjligheter i IOPS och/eller kapaciteten i slutet. Därför kan aktiviteten finns kvar, som lokala distributioner att definiera layouten för de virtuella hårddiskarna till de olika SAP-system via olika 'tänkt SAN-enheter, eller Azure Storage-konton.

För Azure standardlagring rekommenderas inte presentera lagringen från olika lagringskonton till en enda virtuell dator om möjligt.

När du använder DS eller GS-serien av virtuella Azure-datorer, går det att montera VHD: er från Lagringskonton i Azure-Standard och Premium-Lagringskonton. Användningsfall som skrivning säkerhetskopieringar till standardlagring backas upp av virtuella hårddiskar och med DBMS data och loggfiler på Premium-lagring som kommer att tänka på där sådan heterogen lagring kan utnyttjas. 

Baserat på kunddistributioner och testa cirka 30-40 virtuella hårddiskar som innehåller data databasfilerna och loggfilerna kan etableras på ett enda Azure Standard Storage-konto med acceptabel prestanda. Som nämnts tidigare är begränsningar i Azure Premium Storage-konto troligt att den kan innehålla data kapaciteten och inte IOPS.

Som med SAN-enheter lokalt kräver delning viss övervakning för att så småningom upptäcka flaskhalsar på ett Azure Storage-konto. Azure övervakning tillägget för SAP och Azure portal är verktyg som kan användas för att identifiera upptagen Azure Storage-konton som kan leverera något sämre i/o-prestanda.  Om den här situationen upptäcks, rekommenderas att flytta upptagen virtuella datorer till en annan Azure Storage-konto. Referera till den [Deployment Guide] [ deployment-guide] för information om hur du aktiverar SAP värd övervakningsfunktionerna.

En annan artikel sammanfattning metodtips runt Azure standardlagring och Azure Storage-standardkonton hittar du här <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Hanterade diskar
Hanterade diskar är en ny resurstyp i Azure Resource Manager som kan användas i stället för virtuella hårddiskar som lagras i Azure Storage-konton. Hanterade diskar justeras automatiskt med Tillgänglighetsuppsättningen för den virtuella datorn som de är kopplade till och därför att öka tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översiktsartikel](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP stöder för närvarande endast hanteras Premiumdiskar. Läs SAP-kommentar [1928533] för mer information.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Flytta distribueras DBMS virtuella datorer från Azure standardlagring till Azure Premium-lagring
Vi får ganska vissa scenarier där du som kund vill flytta en distribuerad virtuell dator från Azure standardlagring till Azure Premium-lagring. Om diskarna lagras i Azure Storage-konton kan är det inte möjligt utan att fysiskt flytta data. Det finns flera sätt att uppnå målet:

* Du kan kopiera alla virtuella hårddiskar, bas-VHD som virtuella hårddiskar data till en ny Azure Premium Storage-konto. Ofta du har valt antalet virtuella hårddiskar i Azure standardlagring inte på grund av det faktum att du behövs datavolym. Dock du så många virtuella hårddiskar på grund av IOPS. Nu när du flyttar till Azure Premium-lagring kan du gå sätt färre virtuella hårddiskar att uppnå samma IOPS genomflöde. Med tanke på att i Azure standardlagring du betalar för data som används och inte nominell diskens storlek, antalet virtuella hårddiskar ingen roll vad gäller kostnader. Med Azure Premium Storage kan du dock betalar för nominell diskens storlek. Därför se de flesta kunder till att antalet virtuella hårddiskar Azure i Premium-lagring på det numret som behövs för att uppnå det IOPS genomflödet behövs. Därför Bestäm merparten av kunderna mot sätt på ett enkelt 1:1 kopia.
* Om du ännu inte monterade montera en enda virtuell Hårddisk som kan innehålla en säkerhetskopia av databasen för din SAP-databas. När säkerhetskopieringen, demontera inklusive den virtuella Hårddisken som innehåller säkerhetskopian på alla virtuella hårddiskar och kopiera en bas-VHD och den virtuella Hårddisken med backup till ett Azure Premium Storage-konto. Du kan sedan distribuera den virtuella datorn baserat på den virtuella Bashårddisken och Montera VHD: N med backup. Nu kan du skapa ytterligare tom Premiumdiskar med lagringsutrymme för den virtuella datorn som används för att återställa databasen till. Detta förutsätter att DBMS kan du ändra sökvägar till data och loggfilen filer som en del av återställningsprocessen.
* En annan möjlighet är en variation av tidigare processen, där du kan kopiera säkerhetskopian VHD till Azure Premium-lagring och bifoga den mot en virtuell dator som du nyligen har distribuerats och installerats.
* Fjärde möjligheten väljer du när du ska behöva ändra antalet datafiler i databasen. I så fall skulle du utföra en SAP homogen kopian med hjälp av export/import. Placera de exportera filer till en virtuell Hårddisk som kopieras till Azure Premium Storage-konto och koppla den till en virtuell dator som du använder för att köra processer för import. Kunder som använder denna möjlighet huvudsakligen när de vill minska antalet datafiler.

Om du använder hanterade diskar kan du migrera till Premium-lagring genom att:

1. Frigör virtuell dator
1. Om det behövs, ändra storlek på den virtuella datorn till en storlek som har stöd för Premium-lagring (till exempel DS eller GS)
1. Ändra kontotyp hanterade disken till Premium (SSD)
1. Ändra cachelagring av datadiskar som rekommenderas i kapitel [cachelagring för virtuella datorer och datadiskar][dbms-guide-2.1]
1. Starta den virtuella datorn

### <a name="deployment-of-vms-for-sap-in-azure"></a>Distribution av virtuella datorer för SAP i Azure
Microsoft Azure tillhandahåller flera olika sätt att distribuera virtuella datorer och associerade diskar. Därmed är det viktigt att förstå skillnaderna eftersom förberedelser för de virtuella datorerna kan variera beroende på hur för distributionen. I allmänhet titta vi i scenarier som beskrivs i följande kapitel.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace
Vill du koppla ifrån en Microsoft eller tredje part som avbildning från Azure Marketplace för att distribuera den virtuella datorn. När du har distribuerat den virtuella datorn i Azure måste följa du samma riktlinjer och verktyg för att installera programvaran SAP inuti den virtuella datorn som du gör i en lokal miljö. För att installera SAP-program i Azure VM, SAP och Microsoft rekommenderar överföring och lagra SAP-installationsmediet i diskar eller att skapa en Azure-dator som fungerar som en ”server”, som innehåller alla nödvändiga SAP installationsmediet.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Distribuera en virtuell dator med en kundspecifika generaliserad avbildning
På grund av specifika krav om ditt operativsystem eller DBMS-version, kan de tillhandahållna avbildningarna i Azure Marketplace inte passar dina behov. Därför kan du behöva skapa en virtuell dator med hjälp av en egen ”privat” OS/DBMS VM-avbildning som kan distribueras efteråt flera gånger. Operativsystemet måste vara generaliserad på den lokala virtuella datorn för att förbereda en ”privat” avbildning för dubblering. Referera till den [Deployment Guide] [ deployment-guide] för information om hur du generalisera en virtuell dator.

Om du redan har installerat SAP innehållet i den lokala virtuella datorn (särskilt för nivå 2-system), kan du anpassa systeminställningar SAP när distributionen av virtuella Azure-datorn via instansen proceduren som stöds av SAP programvara etablering Manager (SAP Observera [1619720]). Annars kan du installera SAP-program senare efter distributionen av Azure VM.

Eftersom av databasen innehåll används av SAP-programmet, du kan antingen generera innehållet nyligen av en SAP-installation eller du kan importera ditt innehåll till Azure med hjälp av en virtuell Hårddisk med en säkerhetskopia av databasen DBMS eller genom att utnyttja funktionerna i DBMS att säkerhetskopiera direkt till  Microsoft Azure-lagring. I det här fallet kan du även förbereda virtuella hårddiskar med den DBMS data och loggfilen filer lokalt och sedan importera dem som diskar till Azure. Men överföring av DBMS-data som läses in från lokal till Azure skulle fungera över VHD-diskar som måste förberedas lokalt.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Flytta en virtuell dator från lokal till Azure med en icke-generaliserad disk
Du planerar att flytta ett särskilt SAP-system från lokal till Azure (lift och SKIFT). Detta kan göras genom att överföra den disken som innehåller Operativsystemet, SAP-binärfiler och eventuell DBMS-binärfiler plus diskarna med data och loggfilen filer med DBMS till Azure. I motsatt scenario #2 ovan, behåller du värdnamn, SAP-SID och SAP användarkonton i Azure VM som de har konfigurerats i den lokala miljön. Därför är att generalisera bilden inte nödvändigt. Det här fallet gäller främst för anslutningar mellan lokala scenarier där en del av SAP-liggande kör lokalt och delar för på Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Hög tillgänglighet och katastrofåterställning med virtuella Azure-datorer
Azure erbjuder följande hög tillgänglighet och Disaster Recovery (DR) funktioner, som gäller för olika komponenter som vi använder för SAP och DBMS-distributioner

### <a name="vms-deployed-on-azure-nodes"></a>Virtuella datorer som distribueras på Azure-noder
Azure-plattformen erbjuder inte funktioner, till exempel Direktmigrering för distribuerade virtuella datorer. Det innebär att om det krävs Underhåll på ett serverkluster som är distribuerad på en virtuell dator, den virtuella datorn behöver hämta stoppas och startas om. Underhåll i Azure utförs med så kallade uppgradera domäner inom kluster med servrar. Endast en uppgradera domän i taget bevaras. Under en omstart finns det ett avbrott i tjänsten medan den virtuella datorn är avstängd och underhåll utförs virtuella datorn startas om. De flesta DBMS-leverantörer men ger hög tillgänglighet och katastrofåterställning funktioner som startar snabbt om DBMS-tjänster på en annan nod om den primära noden är tillgänglig. Azure-plattformen ger funktioner för att distribuera virtuella datorer, lagring och andra Azure-tjänster uppgradera domäner så att endast planerat underhåll eller infrastruktur fel påverkar en liten del av virtuella datorer eller tjänster.  Med noggrann planering, är det möjligt att uppnå tillgänglighet jämförbar med lokal infrastruktur.

Microsoft Azure-Tillgänglighetsuppsättningar är en logisk gruppering av virtuella datorer eller tjänster som säkerställer att virtuella datorer och andra tjänster som har distribuerats till olika fel och uppgradera domäner inom ett kluster så att skulle det bara finnas en nodavstängning på någon punkt i tiden (läsa [(Linux)] [ virtual-machines-manage-availability-linux] eller [(Windows)] [ virtual-machines-manage-availability-windows] mer information).

Den måste konfigureras med syftet vid distribution av virtuella datorer som visas här:

![Definition av Tillgänglighetsuppsättning för DBMS HA konfigurationer][dbms-guide-figure-200]

Om vi vill skapa hög tillgänglighet konfigurationer för DBMS-distributioner (oberoende av de enskilda DBMS HA funktioner som används) måste de virtuella datorerna DBMS du:

* Lägg till de virtuella datorerna i samma virtuella Azure-nätverk (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* De virtuella datorerna i konfigurationen för hög tillgänglighet bör också vara i samma undernät. Det går inte att namnmatchning mellan olika undernät i endast molnbaserad distributioner, endast IP-upplösning fungerar. Med hjälp av plats-till-plats eller ExpressRoute-anslutning för anslutningar mellan lokala distributioner, redan ett nätverk med minst ett undernät. Namnmatchning utförs enligt lokalt AD-principer och nätverksinfrastruktur. 



#### <a name="ip-addresses"></a>IP-adresser
Vi rekommenderar starkt att konfigurera virtuella datorer för hög tillgänglighet konfigurationer på ett flexibelt sätt. Förlita dig på IP-adresser för att adressera HA samarbetspartners inom konfiguration för hög tillgänglighet är inte tillförlitligt i Azure om statiska IP-adresser som används. Det finns två ”Stäng” koncept i Azure:

* Stänga ned via Azure-portalen eller Azure PowerShell-cmdlet Stop-AzureRmVM: I det här fallet den virtuella datorn hämtar avstängning och Frigör allokerade. Azure-konto debiteras inte längre för den här virtuella datorn så att endast avgifterna innebära för lagring som används. Om den privata IP-adressen för nätverksgränssnittet inte var statiska IP-adressen frisläpps och det är inte säkert att nätverksgränssnittet får den gamla IP-adress som tilldelats igen efter en omstart av den virtuella datorn. Utför stängs ned via Azure-portalen eller genom att anropa stoppa AzureRmVM automatiskt medför inaktiveringen allokering. Om du inte vill att frigöra datorn att stoppa AzureRmVM - StayProvisioned 
* Om du stänger av den virtuella datorn från en OS-nivå, hämtar den virtuella datorn stängs av och inte Frigör allokeras. Men i det här fallet är ditt Azure-konto fortfarande debiteras för den virtuella datorn, trots att den stängs. I sådana fall intakt tilldelning av IP-adressen till en stoppad virtuell dator. Stänger av den virtuella datorn i tvingar inte inaktiveringen allokering automatiskt.

Även för anslutningar mellan lokala scenarier som standard innebär en avstängning och avinstallation tilldelning inaktiveringen tilldelning av IP-adresser från den virtuella datorn, även om lokala principer i DHCP-inställningarna är olika. 

* Undantaget är om en tilldelas en statisk IP-adress till ett nätverksgränssnitt som beskrivs [här][virtual-networks-reserved-private-ip].
* I sådana fall IP-adressen ligger kvar så länge som nätverksgränssnittet inte tas bort.

> [!IMPORTANT]
> För att skydda hela distributionen enkel och hanterbar, vi Rensa rekommenderar att konfigurera de virtuella datorerna som samarbetar med DBMS HA eller DR-konfigurationen i Azure så att det finns en fungerande namnmatchning mellan olika virtuella datorer ingår.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Distribution av värden övervakning
För effektiv användning av SAP-program i Azure Virtual Machines kräver SAP möjlighet att hämta värden övervakningsdata från de fysiska värdar som kör Azure virtuella datorer. En specifik SAP Värdagenten korrigeringsnivå krävs som gör att den här funktionen i SAPOSCOL och SAP värden Agent. Den exakta korrigeringsnivå dokumenteras i SAP-kommentar [1409604].

Information om distribution av komponenter som skickar värden data SAPOSCOL och SAP Värdagenten och Livscykelhantering för dessa komponenter finns i den [Deployment Guide][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Närmare information till Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server IaaS
Från och med Microsoft Azure kan migrera du enkelt dina befintliga SQL Server-program som bygger på Windows Server-plattformen till virtuella datorer i Azure. SQL Server på en virtuell dator kan du minska den totala ägandekostnaden för distribution, hantering och underhåll av breda företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SQL Server i en virtuell dator i Azure, kan administratörer och utvecklare fortfarande använda samma utveckling och Administrationsverktyg som är tillgängliga lokalt. 

> [!IMPORTANT]
> Vi diskuterar inte Microsoft Azure SQL Database, vilket är en plattform som en tjänsterbjudande av Microsoft Azure-plattformen. Diskussion i det här dokumentet handlar om hur du kör SQL Server-produkt som de kallas för lokala distributioner i Azure Virtual Machines, utnyttja infrastrukturen som en tjänst-funktion i Azure. Databasen funktioner och funktionalitet mellan dessa två erbjudanden är olika och bör inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Det rekommenderas att granska [detta] [ virtual-machines-sql-server-infrastructure-services] dokumentationen innan du fortsätter.

I följande avsnitt samman och nämns delar av delar av dokumentationen under länken ovan. Närmare information kring SAP nämns samt och några koncept som beskrivs i detalj. Men rekommenderar vi starkt att arbeta igenom dokumentationen ovan första innan du läser du i dokumentationen till SQL Server-specifik.

Det finns några SQL-Server i IaaS specifik information som du bör känna till innan du fortsätter:

* **Virtual Machine SLA**: det finns ett SLA för virtuella datorer som körs i Azure som finns här: <https://azure.microsoft.com/support/legal/sla/>  
* **Stöd för SQL-Version**: för SAP-kunder vi stöder SQL Server 2008 R2 och högre på Microsoft Azure-dator. Tidigare versioner stöds inte. Granska övergripande [Support Statement](https://support.microsoft.com/kb/956893) för mer information. Observera att i allmänhet SQL Server 2008 stöds av Microsoft samt. Men på grund av viktiga funktioner för SAP, som introducerades i SQL Server 2008 R2, SQL Server 2008 R2 är den minsta versionen för SAP. Tänk på att SQL Server 2012 och 2014 har utökats med djupare integrering IaaS-scenario (till exempel säkerhetskopiera direkt mot Azure Storage). Därför begränsar vi det här dokumentet till SQL Server 2012 och 2014 med den senaste korrigeringsnivå för Azure.
* **Stöd för SQL-funktionen**: mest SQL Server-funktioner stöds på Microsoft Azure-datorer med vissa undantag. **SQL Server Failover Clustering med hjälp av delade diskar stöds inte**.  Distribuerade tekniker som databasspegling, AlwaysOn Availability Groups, replikering, Loggöverföring och Service Broker stöds i en enda Azure-Region. SQL Server AlwaysOn också stöds mellan olika Azure-regioner som dokumenteras här: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Granska de [Support Statement](https://support.microsoft.com/kb/956893) för mer information. Ett exempel på hur du distribuerar en AlwaysOn-konfiguration visas i [detta] [ virtual-machines-workload-template-sql-alwayson] artikel. Dessutom kolla metodtips dokumenterade [här][virtual-machines-sql-server-infrastructure-services] 
* **Prestanda för SQL**: vi är säker på att Microsoft Azure virtuella värddatorerna utföra mycket bra jämförelse med andra virtualiseringslösningar för offentliga moln, men enskilda resultaten kan variera. Checka ut [detta] [ virtual-machines-sql-server-performance-best-practices] artikel.
* **Med hjälp av avbildningar från Azure Marketplace**: det snabbaste sättet att distribuera en ny Microsoft Azure VM är att använda en avbildning från Azure Marketplace. Det finns avbildningar i Azure Marketplace, som innehåller SQL Server. Bilderna där SQL Server redan är installerad kan inte användas direkt för SAP NetWeaver program. Orsaken är standardsortering för SQL Server är installerade på bilderna och inte den sortering som krävs av SAP NetWeaver system. För att kunna använda dessa bilder, kontrollera stegen i kapitel [med hjälp av en SQL Server-avbildning utanför Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Checka ut [prisinformation](https://azure.microsoft.com/pricing/) för mer information. Den [SQL Server 2012 Licensing Guide](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) och [SQL Server 2014 licensiering Guide](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) är också en viktig resurs.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Riktlinjer för SQL Server-konfiguration för SAP-relaterade SQL Server-installationerna i virtuella Azure-datorer
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Rekommendationer för VM/VHD-strukturen för SAP-relaterade SQL Server-distributioner
I enlighet med den allmänna beskrivningen körbara filer i SQL Server belägen eller installerats i operativsystemdisken för den Virtuella datorns systemenhet (enhet C:\).  De flesta av SQL Server-systemdatabaserna används vanligtvis inte på en hög nivå av SAP NetWeaver arbetsbelastning. Därför kan systemdatabaser av SQL Server (master, msdb och modell) finns kvar på enhetens C:\. Ett undantag kan vara tempdb som när det gäller vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolym eller i/o-åtgärder volymen, som inte kan placeras i den ursprungliga virtuella datorn. Följande steg ska utföras för sådana system:

* Flytta primära tempdb-datafiler till samma logiska enhet som den primära datafiler SAP-databasen.
* Lägga till alla ytterligare tempdb-datafiler i alla andra logiska enheter som innehåller en datafil för SAP-användardatabasen.
* Lägg till tempdb-loggfilen i den logiska enheten som innehåller användare databasloggfilen.
* **Endast för VM-typer som använder lokala SSD** på compute-nod tempdb data och loggfilen filer kan placeras på D:\-enhet. Det kan dock vara bör du använda flera tempdb-datafiler. Tänk på D:\ enhetsvolymer är olika beroende på vilken typ av virtuell dator.

De här konfigurationerna aktivera tempdb förbruka mer utrymme än systemenheten kan ge. För att fastställa rätt tempdb-storlek, kontrollera en tempdb-storlekar på befintliga system som kör lokalt. En sådan konfiguration skulle dessutom aktivera IOPS siffror mot tempdb som kan tillhandahållas med systemenheten. Igen och kan system som kör lokalt användas för att övervaka i/o-arbetsbelastning mot tempdb så att du kan härleda IOPS-siffror som du förväntar dig att se på tempdb.

En VM-konfiguration, som kör SQL Server med en SAP-databas och där tempdb data och tempdb logfile placeras på enheten D:\ ser ut som:

![För referenskonfiguration av Azure IaaS-VM för SAP][dbms-guide-figure-300]

Tänk på att enheten D:\ har olika storlekar, beroende på vilken typ av virtuell dator. Beroende på kraven för storlek på tempdb du kan tvingas att par tempdb data och loggfilen filer med SAP data och loggfilen databasfilerna i fall där D:\ enheten är för liten.

#### <a name="formatting-the-disks"></a>Formatera diskarna
För SQL Server NTFS blockstorlek för diskar som innehåller SQL Server-data och loggfiler ska vara 64 kB. Det finns inget behov formatera D:\ enheten. Den här enheten kommer före formaterad.

För att se till att återställa eller skapandet av databaser inte initieras datafilerna av nollställning innehållet i filerna, bör en Kontrollera att SQL Server-tjänsten körs i användarkontexten har en viss behörighet. Användare i gruppen Windows-administratör har vanligtvis dessa behörigheter. Om SQL Server-tjänsten körs i en användarkontext för icke - Windows administratör, måste du tilldela användaren användarrättigheten **utföra underhållsaktiviteter**.  Se informationen i det här Microsoft Knowledge Base-artikel: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
Alla åtgärder, vilket minskar IOPS kan hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure i konfigurationer där i/o-bandbredd kan bli en begränsande faktor. Därför rekommenderas har ännu inte klar tillämpa SQL Server-sidan komprimering om både SAP och Microsoft innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att utföra databas komprimering innan du laddar upp till Azure får slut på två skäl:

* Mängden data som ska överföras är lägre.
* Varaktighet för komprimering körningen är kortare förutsatt att något kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid lokalt.
* Databasen är mindre kan leda till lägre kostnader för diskallokering

Databasen komprimering fungerar också i ett Azure Virtual Machines som lokalt. Mer information om hur du komprimera en befintlig SAP SQL Server-databas kontrollerar du här: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQLServer 2014 - lagra databasfilerna direkt på Azure Blob Storage
SQL Server 2014 öppnas möjlighet att lagra databasfiler direkt på Azure Blob Store utan omslutning av en virtuell Hårddisk runtom. Särskilt med hjälp av Azure standardlagring eller mindre VM-typer kan detta scenarier där du kan lösa gränserna för IOPS skulle tillämpas av ett begränsat antal diskar som kan monteras till vissa mindre VM-typer. Detta fungerar för användardatabaser men inte för systemdatabaser av SQL Server. Den fungerar även för data och loggfiler för SQL Server. Om du vill distribuera en SAP SQL Server-databas sätt i stället för ”radbrytning' till virtuella hårddiskar, Tänk på följande:

* Storage-konto för måste finnas i samma Azure-Region som det som används för att distribuera Virtuella SQL Server körs i.
* Överväganden avseende som angavs tidigare distribution av virtuella hårddiskar över olika Azure Storage-konton gäller för den här metoden samt distributioner. Innebär att antalet i/o-åtgärder mot gränserna för Azure Storage-konto.

[comment]: <> (MSSedusch TODO men det här använder nätverket och inte lagring bandbredd, inte den?)

Information om den här typen av distribution finns här: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

För att kunna lagra filer för SQL Server-data direkt på Azure Premium-lagring måste du ha ett minsta SQL Server 2014 korrigering version som dokumenteras här: <https://support.microsoft.com/kb/3063054>. Lagra filer för SQL Server-data på Azure standardlagring fungerar med den utgivna versionen av SQL Server 2014. Samma uppdateringsfilerna innehåller dock en annan serie korrigeringar som gör det mer tillförlitlig direkt användning av Azure Blob Storage för SQL Server-datafiler och säkerhetskopieringar. Därför bör du använda dessa korrigeringar i allmänhet.

### <a name="sql-server-2014-buffer-pool-extension"></a>Buffertpooltillägget för SQL Server 2014
SQL Server 2014 introducerade en ny funktion som kallas Buffertpooltillägget. Den här funktionen utökar buffertpooltillägget för SQL Server som är kvar i minnet med en andra nivå cache som backas upp av lokala SSD-enheter på en server eller virtuell dator. Detta gör att en större arbetsminnet för data ”i minnet'. Jämfört med att komma åt Azure standardlagring är åtkomst till tillägget på den buffertpool som är lagrad på lokala SSD av en Azure VM många faktorer snabbare.  Utnyttja VM-typer som har utmärkt IOPS och genomströmning D:\ lokalt kunde därför inte en mycket rimligt sätt att minska belastningen IOPS mot Azure Storage och förbättra svarstiden för frågor dramatiskt. Detta gäller särskilt om du inte använder Premium-lagring. Premium-lagring och användning av Läs-Cache Premium Azure på Beräkningsnoden förväntas några betydande skillnader som rekommenderas för datafiler. Orsaken är att båda cacheminnen (Buffertpooltillägget för SQL Server- och Premium-lagring Läs-Cache) använder lokala diskar på datornoderna.
Kontrollera den här dokumentationen för mer information om den här funktionen: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Säkerhetskopiering/återställning överväganden för SQL Server
Vid distribution av SQL Server i Azure måste din backup metod granskas. Även om systemet inte är en produktiv system måste SAP-databasen med SQL Server som värd säkerhetskopieras regelbundet. Eftersom Azure Storage håller tre bilder, är nu en säkerhetskopia mindre viktiga med avseende på kompenserande en krasch lagring. Prioritet orsaken för att bibehålla en korrekt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Målet är så att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tid eller använda säkerhetskopieringar i Azure som startvärde för ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en SAP nivå 2-konfiguration till en 3-skikts systeminställningarna för samma system genom att återställa en säkerhetskopia.

Det finns tre olika sätt att säkerhetskopiera SQL Server till Azure Storage:

1. SQL Server 2012 CU4 och högre kan internt säkerhetskopiera databaser till en URL. Detta beskrivs i bloggen [nya funktioner i SQL Server 2014 - del 5 - säkerhetskopiering/återställning förbättringar](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Se kapitel [SQL Server 2012 SP1 CU4 och senare][dbms-guide-5.5.1].
2. SQL Server-versioner före SQL 2012 CU4 kan använda en omdirigering funktionalitet för att säkerhetskopiera till en virtuell Hårddisk och övergå i princip write-dataströmmen till en Azure-lagringsplats som har konfigurerats. Se kapitel [SQL Server 2012 SP1 CU3 och tidigare versioner][dbms-guide-5.5.2].
3. Den sista metoden är att utföra en säkerhetskopiering av konventionella SQL Server på en disk på en diskenhet. Detta är identisk med den lokala distributionen mönstret och beskrivs inte i detalj i detta dokument.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 och senare
Den här funktionen kan du direkt säkerhetskopiering till Azure BLOB storage. Utan den här metoden måste du säkerhetskopiera till andra diskar som ska använda disk- och IOPS kapacitet. Tanken är i princip:

 ![Säkerhetskopiering av SQL Server 2012 till Microsoft Azure Storage BLOB][dbms-guide-figure-400]

Fördelen är i det här fallet inte behöver en tillbringar diskar för att lagra säkerhetskopior av SQL Server på. Så har du färre diskar som är allokerade och hela bandbredden för disk-IOPS kan användas för data och loggfiler. Observera att den maximala storleken för en säkerhetskopia är begränsad till högst 1 TB enligt beskrivningen i avsnittet **begränsningar** i den här artikeln: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Om storleken på säkerhetskopian, trots att med hjälp av SQL Server-säkerhetskopieringskomprimering skulle överskrida 1 TB i storlek, funktionerna beskrivs i kapitlet [SQL Server 2012 SP1 CU3 och tidigare versioner] [ dbms-guide-5.5.2] i det här dokumentet måste vara används.

[Relaterad dokumentation](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) som beskriver återställning av databaser från säkerhetskopior mot Azure Blob Store rekommenderar inte för att återställa direkt från Azure BLOB store om säkerhetskopian finns > 25 GB. Rekommendationerna i den här artikeln är baserad på prestandaöverväganden och inte på grund av funktionella begränsningar. Därför kan olika villkor tillämpas på en fall till fall.

Dokumentation om hur den här typen av säkerhetskopiering har konfigurerats och utnyttjas kan hittas i [detta](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) självstudiekursen

Ett exempel på ordningsföljden kan läsas [här](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatiserad säkerhetskopiering, är det av högsta vikt att se till att BLOB för varje säkerhetskopiering namn på olika sätt. Annars de skrivs över och restore-kedjan har brutits.

För att inte blanda ihop saker mellan tre olika typer av säkerhetskopior är det lämpligt att skapa olika behållare under storage-konto som används för säkerhetskopieringen. Behållarna som kan vara av virtuell dator endast eller av virtuell dator och typ av säkerhetskopiering. Schemat kan se ut så:

 ![Med hjälp av SQL Server 2012 säkerhetskopiering till Microsoft Azure Storage BLOB - olika behållare under separat Storage-konto][dbms-guide-figure-500]

I exemplet ovan skulle du inte utföra säkerhetskopieringar i samma lagringskonto där de virtuella datorerna har distribuerats. Det är ett nytt lagringskonto för säkerhetskopior. I storage-konton kan det vara olika behållare som skapats med en matris av typen av säkerhetskopiering och namnet på virtuella datorn. Sådana segmentering gör det lättare att administrera säkerhetskopieringar för olika virtuella datorer.

BLOB som en direkt skriver säkerhetskopieringar till, lägger inte till antalet data diskar på en virtuell dator. Därför kan en Maximera maximalt antalet datadiskar som monterats av specifika VM SKU för data och transaktion loggfilen och fortfarande köra en säkerhetskopiering mot en lagringsbehållare. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 och tidigare versioner
Det första steget när du måste utföra för att uppnå en säkerhetskopia direkt mot Azure Storage är att hämta msi, som är kopplad till [detta](https://www.microsoft.com/download/details.aspx?id=40740) KBA artikel.

Hämta x64 installationsfilen och i dokumentationen. Filen installerar ett program som kallas: **Microsoft SQL Server-säkerhetskopiering till Microsoft Azure-verktyget**. Läs i dokumentationen för produkten noggrant.  Verktyget fungerar i princip på följande sätt:

* En plats för SQL Server-säkerhetskopiering har definierats från SQL Server-sida (Använd inte D:\ enheten som plats).
* Verktyget kan du definiera regler som kan användas för att styra olika typer av säkerhetskopieringar för olika Azure Storage-behållare.
* När reglerna är på plats, omdirigerar verktyget skrivdataström av säkerhetskopian till en av de virtuella hårddiskar/diskarna till Azure Storage-plats, som definierades tidigare.
* Verktyget lämnar en liten stub-fil med några KB storleken på VHD/disken som har definierats för SQL Server säkerhetskopiering. **Den här filen ska lämnas på lagringsplatsen eftersom det krävs för att återställa igen från Azure Storage.**
  * Om du har tappat bort stubbfil (till exempel genom förlust av lagringsmedia som innehöll stub-filen) och du har valt alternativet för att säkerhetskopiera till ett Microsoft Azure Storage-konto, kan du återställa stub-filen via Microsoft Azure Storage genom att hämta den från vilken lagringsbehållare som den släpptes. Placera stub-filen i en mapp på den lokala datorn där verktyget är konfigurerad för att identifiera och ladda upp till behållaren med samma Krypteringslösenord om kryptering användes med den ursprungliga regeln. 

Det innebär att schemat som beskrivs ovan för nyare versioner av SQL Server kan placeras på plats samt SQL Server-versioner som inte tillåter att direkt adressen en Azure-lagringsplats.

Den här metoden ska inte användas med nyare SQL Server-versioner som stöder stödjande in internt mot Azure Storage. Undantag är där begränsningar av interna säkerhetskopiering till Azure blockerar interna säkerhetskopiering körning i Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Andra möjligheter att säkerhetskopiera SQL Server-databaser
Andra möjligheter att säkerhetskopiera databaser är att koppla ytterligare datadiskar till en virtuell dator som du använder för att lagra säkerhetskopior på. I så fall behöver du se till att diskarna inte kör fullständig. Om så är fallet, skulle du behöva demontera diskarna så till speak 'Arkivera' den och Ersätt den med en ny tom disk. Om du går ned den sökvägen du vill behålla dessa virtuella hårddiskar i separata Azure Storage-konton från de som virtuella hårddiskar med databasfilerna.

En andra alternativet är att använda en stor virtuell dator som kan ha många diskar som är anslutna, till exempel en D14 med 32VHDs. Använd lagringsutrymmen för att bygga en flexibel miljö där du kan skapa resurser som sedan används som mål för säkerhetskopiering för olika DBMS-servrar.

Metodtips fick dokumenterade [här](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) samt. 

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vilka genomflödet av dessa volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan du anta:

* Det färre antalet diskar som används för att lagra data filer, desto mindre totala genomflödet i läsning.
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering.
* Färre mål (BLOB, virtuella hårddiskar eller diskar) för att skriva säkerhetskopian till den lägsta genomflödet.
* Ju mindre VM storlek, desto mindre genomströmning lagringskvoten skrivning och läsning från Azure Storage. Oberoende av om säkerhetskopiorna lagras direkt på Azure Blob eller om de lagras i virtuella hårddiskar som är lagrade i Azure BLOB igen.

När du använder Microsoft Azure Storage BLOB som mål för säkerhetskopian i senare versioner begränsas du till att ange endast en URL-mål för varje specifik säkerhetskopiering.

Men när du använder ”Microsoft SQL Server säkerhetskopiering till Microsoft Azure-verktyget” i äldre versioner, kan du definiera mer än en målfil. Säkerhetskopieringen kan skala med fler än ett mål och dataflöde på säkerhetskopian som är högre. Detta leder sedan flera filer samt i Azure Storage-konto. Vid testning, uppnå med hjälp av flera mål i filen du definitivt dataflöde, vilket du kan uppnå med säkerhetskopiering tillägg som implementeras i från SQL Server 2012 SP1 CU4 på. Du också blockeras inte av 1TB gränsen som interna säkerhetskopiering till Azure.

Tänk dock på, genomflödet beror också på platsen för Azure Storage-konto du använder för säkerhetskopieringen. En idé kan vara att hitta lagringskontot i en annan region än de virtuella datorerna körs i. Du skulle till exempel köra VM-konfiguration i västra Europa men placera Storage-konto som används för att säkerhetskopiera mot i Norra Europa. Som har påverkar säkerhetskopiering genomflöde och är inte troligt att generera en genomströmning på 150MB per sekund som verkar möjligt i fall där mål-lagringskontot och de virtuella datorerna körs i samma regionala datacenter.

#### <a name="managing-backup-blobs"></a>Hanterar säkerhetskopierade BLOB
Det är ett krav att hantera säkerhetskopiering på egen hand. Eftersom förutsättningen att många blobbar skapas genom att köra säkerhetskopieringar av transaktionsloggen ofta, kan administration av dessa blobbar enkelt överbelasta Azure-portalen. Därför är det recommendable utnyttjar en Azure Lagringsutforskaren. Det finns flera bra de tillgängliga, vilket hjälper dig för att hantera ett Azure storage-konto

* Microsoft Visual Studio med Azure SDK är installerat (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Lagringsutforskaren (<https://azure.microsoft.com/downloads/>)
* Verktyg från tredje part

En fullständig beskrivning av säkerhetskopiering och SAP på Azure, referera till [guiden SAP säkerhetskopiering](sap-hana-backup-guide.md) för mer information.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Med hjälp av en SQL Server-avbildning utanför Microsoft Azure Marketplace
Microsoft erbjuder virtuella datorer i Azure Marketplace som redan innehåller versioner av SQL Server. För SAP-kunder som kräver licenser för SQL Server och Windows, kan det vara en möjlighet att i praktiken behovet av licenser av snurrande upp virtuella datorer med SQL Server redan har installerats. Följande överväganden måste göras för att kunna använda dessa bilder för SAP:

* De SQL Server icke-utvärderingsversioner hämta högre kostnader än en virtuell dator ”endast för Windows, som distribueras från Azure Marketplace. Se följande artiklar för att jämföra priser: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> och <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Du kan bara använda SQL Server-versioner som stöds av SAP, t.ex. SQL Server 2012.
* Sorteringen av SQL Server-instans som är installerad på de virtuella datorerna som erbjuds i Azure Marketplace är inte sorteringen SAP NetWeaver kräver SQL Server-instansen ska köras. Du kan ändra sorteringen men med instruktionerna i följande avsnitt.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändra SQL Server-sorteringen för en Microsoft Windows/SQL Server-dator
Eftersom SQL Server-avbildningar i Azure Marketplace inte är konfigurerade att använda sortering, vilket krävs av SAP NetWeaver program behöver ändras direkt efter distributionen. För SQL Server 2012, kan detta göras med följande steg så snart den virtuella datorn har distribuerats och en administratör kan logga in på den distribuerade virtuella datorn:

* Öppna Kommandotolken Windows som administratör.
* Ändra katalogen till C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Kör kommandot: Setup.exe /QUIET/Action = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> är det konto som har definierats som administratörskonto när du distribuerar den virtuella datorn för första gången igenom galleriet.

Processen tar bara några minuter. För att kontrollera om steget därför har rätt resultat, utför du följande steg:

* Öppna SQL Server Management Studio.
* Öppna ett frågefönster.
* Kör kommandot sp_helpsort i SQL Server-huvuddatabasen.

Det önskade resultatet bör se ut som:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Om detta inte är resultatet stoppa distribuera SAP och undersöka varför installationskommandot inte fungerar som förväntat. Distribution av SAP NetWeaver program på SQL Server-instansen med olika SQL Server-kodsidor än det som nämns ovan **inte** stöds.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server hög tillgänglighet för SAP i Azure
Som nämnts tidigare i det här dokumentet, går det inte att skapa delade lagringen, vilket är nödvändigt för användning av den äldsta SQL Server-funktionen för hög tillgänglighet. Den här funktionen installerar två eller flera SQL Server-instanser i ett Failover Cluster WSFC (Windows Server) med en delad disk för användardatabaser (och slutligen tempdb). Det här är metoden länge standard hög tillgänglighet, som även stöds av SAP. Eftersom Azure inte stöder delad lagring, kan SQL Server med hög tillgänglighet konfigurationer med en delad disk klusterkonfiguration realiseras. Många andra metoder för hög tillgänglighet är fortfarande möjligt och beskrivs i följande avsnitt.

#### <a name="sql-server-log-shipping"></a>SQLServer-Loggöverföring
En av metoderna för hög tillgänglighet (HA) är SQL Server-Loggöverföring. Om de virtuella datorerna som ingår i konfigurationen för hög tillgänglighet har fungerar namnmatchning, utan problem och inställningarna i Azure skilja sig inte från alla inställningar som görs lokalt. Det rekommenderas inte kan förlita sig på endast IP-lösning. Kontrollera den här dokumentationen med avseende på Konfigurera Loggöverföring och principerna runt Loggöverföring:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

För att uppnå inställningarna för hög tillgänglighet, måste en att distribuera virtuella datorer, som är inom en sådan Loggöverföring konfiguration som ligger inom samma Azure Tillgänglighetsuppsättningen.

#### <a name="database-mirroring"></a>Databasspegling
Databasen spegling som stöds av SAP (Se SAP-kommentar [965908]) förlitar sig på definierar redundanspartner i anslutningssträngen SAP. Anslutningar mellan lokala-fall antar vi att två virtuella datorer finns i samma domän och att användaren kontext två SQL Server-instanser körs under en domänanvändare och har behörighet i två SQL Server-instanser ingår. Därför inställningarna för databasspegling i Azure skiljer sig inte mellan en typisk lokalt/installationsprogrammet.

Från och med endast molnbaserad distributioner är det enklaste sättet att ha en annan domän installationen i Azure för att ha dessa DBMS virtuella datorer (och helst dedikerade SAP virtuella datorer) inom en domän.

Om en domän inte är möjligt, kan en också använda certifikat för slutpunkter för databasspegling, som beskrivs här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Självstudier för att konfigurera databasspegling i Azure finns här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQLServer Always On
Som alltid på stöds för SAP lokal (Se SAP-kommentar [1772688]), det går för att användas tillsammans med SAP i Azure. Det faktum att det inte går att skapa delade diskar i Azure innebär inte att en går inte att skapa en alltid på Failover Cluster WSFC (Windows Server) konfiguration mellan olika virtuella datorer. Det innebär bara att du inte har möjlighet att använda en delad disk som ett kvorum i klusterkonfigurationen. Därför kan du skapa en alltid på WSFC-konfiguration i Azure och välja inte kvorum som använder delad disk. Azure-miljön dessa virtuella datorer distribueras i ska åtgärda virtuella datorer efter namn och de virtuella datorerna ska vara i samma domän. Detta gäller endast Azure och mellan lokala distributioner. Det finns vissa saker runt distribuera SQL Server tillgänglighetsgruppens lyssnare (inte förväxlas med Azure Tillgänglighetsuppsättningen) eftersom Azure på vid denna tidpunkt inte tillåter för att skapa ett AD/DNS-objekt eftersom det är möjligt lokalt. Därför krävs vissa olika installationssteg för att lösa specifika beteendet för Azure.

Vissa aspekter med hjälp av en Tillgänglighetsgruppslyssnare är:

* Med hjälp av en Tillgänglighetsgruppslyssnare är bara möjligt med Windows Server 2012 eller högre som gästoperativsystemet på den virtuella datorn. För Windows Server 2012 måste du se till att korrigeringsfilen tillämpas: <https://support.microsoft.com/kb/2854082> 
* För Windows Server 2008 R2 finns inte den här korrigeringsfilen och alltid på måste användas på samma sätt som databasspegling genom att ange redundanspartner i anslutningssträngen (göra SAP default.pfl parametern dbs/mss/server - Se SAP-kommentar [965908]).
* När du använder en Tillgänglighetsgruppslyssnare, måste de virtuella datorerna databasen måste vara ansluten till en särskild belastningsutjämnare. Namnmatchning i distributioner för endast molnbaserad antingen kräver ett SAP-system (programservrar, DBMS-servern och server (A) SCS) på alla virtuella datorer finns i samma virtuella nätverk eller från en SAP programnivå kräver underhåll av filen etc\host i ordning att hämta VM namnen på SQL Server-datorer som löst. För att undvika att Azure tilldelar nya IP-adresser i de fall där båda VM: ar tillfälligtvis avstängning bör en tilldela statiska IP-adresser för nätverksgränssnitten i de virtuella datorer i konfigurationen för Always On (definierar en statisk IP-adress beskrivs i [detta] [ virtual-networks-reserved-private-ip] artikel)

[comment]: <> (Gamla bloggar)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Det finns särskilda steg krävs när du skapar WSFC klusterkonfigurationen där klustret måste en särskild IP-adress tilldelas eftersom Azure med dess aktuella funktioner ska tilldela klusternamnet samma IP-adress som noder i klustret har skapats på. Det innebär att ett manuellt steg måste utföras för att tilldela en annan IP-adress till klustret.
* Tillgänglighetsgruppslyssnaren kommer att skapas i Azure med TCP/IP-slutpunkter som är tilldelade till de virtuella datorerna kör primära och sekundära replikerna för tillgänglighetsgruppen.
* Det kan vara nödvändigt att skydda dessa slutpunkter med åtkomstkontrollistor.

[comment]: <> (Gamla TODO-blogg)
[comment]: <> (Detaljerade anvisningar och är nödvändigt att installera en AlwaysOn-konfigurationen på Azure är enklast att när du går igenom kursen tillgängliga [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Förkonfigurerade AlwaysOn-installationen via Azure-galleriet <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Skapa en Tillgänglighetsgruppslyssnare är bäst beskrivs i kursen [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comment]: <> (Att säkra nätverksslutpunkter med åtkomstkontrollistor beskrivs bästa här:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Det är möjligt att distribuera en SQL Server alltid på tillgänglighetsgrupp över samt olika Azure-regioner. Den här funktionen utnyttjar Azure VNet-till-Vnet-anslutningen ([mer][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Gamla TODO-blogg)
[comment]: <> (Installationen av SQL Server AlwaysOn-Tillgänglighetsgrupper i ett sådant scenario som beskrivs här: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>En sammanfattning av SQL Server med hög tillgänglighet i Azure
Med tanke på att innehållet skyddas av Azure Storage, är det ett mindre skäl till att kräva en hot standby-avbildning. Detta innebär att ditt scenario för hög tillgänglighet behöver bara skydda mot följande fall:

* Den virtuella datorn att hela på grund av underhåll på server-kluster i Azure eller andra orsaker
* Problem med programvara i SQL Server-instansen
* Skydda från manuell fel där data tas bort och punkten i tidsåterställningen krävs

Titta på matchande tekniker en kan argumentera att de två första fall kan omfattas av databasspegling eller alltid på det tredje fallet endast kan omfattas av Loggöverföring.

Du måste väga mer komplexa installationen av Always On, jämfört med databasspegling med fördelarna med att alltid på. Dessa fördelar kan anges som:

* Läsbara sekundära repliker.
* Säkerhetskopiering från sekundära repliker.
* Bättre skalbarhet.
* Mer än en sekundära repliker.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Allmänna SQL Server för SAP på Azure sammanfattning
Det finns många rekommendationerna i den här guiden och vi rekommenderar att du läsa den mer än en gång innan du planerar distributionen av Azure. I allmänhet, måste du följa tio översta allmänna DBMS på Azure specifika punkter:

[comment]: <> (2.3 högre genomströmning än vad? Än en VHD?)
1. Använd den senaste DBMS-versionen som SQL Server 2014 som har de flesta fördelar i Azure. För SQL Server är SQL Server 2012 SP1 CU4, vilket omfattar funktionen stödjande visa Azure Storage. Dock tillsammans med SAP rekommenderas att använda minst SQL Server 2014 SP1 CU1 eller SQL Server 2012 SP2 och den senaste CU.
2. Planera noggrant liggande din SAP-system i Azure för att balansera data filens layout och Azure begränsningar:
   * Inte har för många diskar, men tillräckligt för att se till att du kan nå dina krävs IOPS.
   * Kom ihåg IOPS är också begränsad per Azure Storage-konto och att Storage-konton är begränsade i varje Azure-prenumeration om du inte använder hanterade diskar ([mer][azure-subscription-service-limits]). 
   * Endast stripe över diskar om du behöver kunna uppnå ett högre genomflöde.
3. Installera aldrig programvara eller placera filer som kräver beständiga på D:\ enheten eftersom det är inte permanent och allt på den här enheten går förlorade vid en omstart av Windows.
4. Använd inte cachelagring på disk för Azure standardlagring.
5. Använd inte Azure georeplikerad Storage-konton.  Använd lokalt Redundant för DBMS-arbetsbelastningar.
6. Använda DBMS leverantörens hr/DR lösning för att replikera data från databasen.
7. Alltid använda namnmatchning, Använd inte IP-adresser.
8. Använd den högsta möjliga komprimeringen av databasen. Vilket är sidan komprimering för SQL Server.
9. Var försiktig med hjälp av SQL Server-avbildningar från Azure Marketplace. Om du använder en SQL Server, måste du ändra sorteringen för instansen innan du installerar alla SAP NetWeaver system på den.
10. Installera och konfigurera SAP-värden som övervakning för Azure som beskrivs i [Deployment Guide][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Specifik information skrivs till SAP ASE i Windows
Från och med Microsoft Azure kan migrera du enkelt befintliga SAP ASE program till Azure-datorer. SAP ASE i en virtuell dator kan du minska den totala ägandekostnaden för distribution, hantering och underhåll av breda företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en virtuell dator i Azure, kan administratörer och utvecklare fortfarande använda samma utveckling och Administrationsverktyg som är tillgängliga lokalt.

Det finns ett SLA för Azure Virtual Machines, som finns här: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Vi är säker på att Microsoft Azure virtuella värddatorerna utför bra jämförelse med andra virtualiseringslösningar för offentliga moln, men enskilda resultaten kan variera. SAP storleksanpassa SAP antal olika SAP certifierade VM SKU: er finns i en separat SAP-kommentar [1928533].

Instruktioner och rekommendationer om användningen av Azure Storage, distribution av SAP virtuella datorer eller SAP övervakning gäller för distributioner av SAP ASE tillsammans med SAP-program som anges i de fyra första kapitlen i det här dokumentet.

### <a name="sap-ase-version-support"></a>Stöd för SAP-ASE Version
SAP för närvarande stöder SAP ASE version 16,0 för SAP Business Suite. Alla uppdateringar för SAP ASE server eller JDBC och ODBC-drivrutiner som ska användas med produkter SAP Business Suite tillhandahålls enbart på marknadsplatsen SAP Service på: <https://support.sap.com/swdc>.

För installationer lokalt, att inte hämta uppdateringar för SAP ASE servern eller för JDBC och ODBC-drivrutiner direkt från Sybase-webbplatser. Mer detaljerad information om korrigeringsprogram som stöds för användning med SAP Business Suite produkter lokalt och i Azure Virtual Machines finns följande SAP-information:

* [1590719]
* [1973241]

Allmän information om hur du kör SAP Business Suite på SAP ASE kan hittas i den [Tillståndsändringsavisering](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Riktlinjer för SAP ASE konfiguration för SAP-relaterade SAP ASE installationer i virtuella Azure-datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur för SAP ASE-distribution
I enlighet med den allmänna beskrivningen SAP ASE körbara filer finns eller installerats i operativsystemdisken för den Virtuella datorns systemenhet (enhet c:\). Vanligtvis används de flesta av SAP ASE system och verktyg databaserna inte hårddisk av SAP NetWeaver arbetsbelastning. Därför kan de verktyg och system databaserna (master, model, saptools, sybmgmtdb, sybsystemdb) finns kvar på enhetens C:\. 

Ett undantag kan vara tillfälliga databasen som innehåller alla arbetsobjekt tabeller och temporära tabeller som skapats av SAP ASE som vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolym eller i/o-åtgärder volymen, som inte kan placeras i den ursprungliga VM OS disken (enhet c:\).

Beroende på vilken version av SAPInst/SWPM används för att installera innehålla databasen:

* En enda SAP ASE tempdb som skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följande SAP-kommentar [1752266]) till ERP/BW specifika tempdb-krav

Vid specifika ERP eller alla BW arbetsbelastningar klokt det, gäller prestanda, så Håll tempdb-enheter efter dessutom skapade tempdb (via SWPM eller manuellt) på en annan enhet än C:\. Om det finns inga ytterligare tempdb, rekommenderas att skapa en (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-enheten till den första enheten för SAP-databasen
* Lägga till tempdb enheter till var och en av de virtuella hårddiskarna som innehåller en enhet för SAP-databasen

Den här konfigurationen kan tempdb antingen förbruka mer utrymme än systemenheten kan ge. Som referens Kontrollera en storlekar för tempdb-enheten på befintliga system som kör lokalt. Eller en sådan konfiguration skulle aktivera IOPS siffror mot tempdb som kan tillhandahållas med systemenheten. Igen kan system som kör lokalt användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera inte alla enheter som SAP ASE på D:\ enhet på den virtuella datorn. Detta gäller även för tempdb, även om de objekt som lagras i tempdb är temporära.

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
Alla åtgärder, vilket minskar IOPS kan hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure i konfigurationer där i/o-bandbredd kan bli en begränsande faktor. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att utföra komprimering innan du laddar upp till Azure om den inte redan har implementerats får slut på flera orsaker:

* Mängden data som skulle överföras till Azure är lägre
* Varaktighet för komprimering körningen är kortare förutsatt att något kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid lokalt
* Databasen är mindre kan leda till lägre kostnader för diskallokering

Data och LOB komprimering fungerar på en virtuell dator som finns i Azure Virtual Machines som sker lokalt. För mer information om hur du kontrollerar om komprimering är redan i använda i en befintlig databas för SAP ASE, kontrollera SAP-kommentar [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Databasinstanser
För SAP-system som använder SAP ASE som databasplattform, är DBACockpit tillgänglig som inbäddade webbläsarfönster i transaktion DBACockpit eller Webdynpro. Men alla funktioner för övervakning och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokalt system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användning av webdynpros och generera de nödvändiga. När följa anvisningarna i ovanstående anteckningar konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut så här:

> ICM/server_port_0 = skydd = HTTP, PORT = 8000 PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = skydd = HTTPS, PORT = 443$ $PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

och länkar som har genererats i transaktionen DBACockpit ser ut ungefär så här:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på om och hur den virtuella datorn för Azure som värd för SAP-systemet är ansluten via plats-till-plats, flera platser eller ExpressRoute (mellan lokala distribution) måste du kontrollera att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du försöker öppna DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger fullständigt kvalificerat värdnamn beroende på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med endast molnbaserad utan anslutning mellan lokala och Azure måste du definiera en offentlig IP-adress och en domainlabel. Formatet för det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information för DNS-namnet kan hittas [här][virtual-machines-azurerm-versus-azuresm].

Ställa in SAP profil parametern icm/host_name_full till DNS-namnet på Azure VM länken kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

I det här fallet måste du se till att:

* Lägg till regler för inkommande trafik till Nätverkssäkerhetsgruppen i Azure-portalen för TCP/IP-portar som används för att kommunicera med ICM
* Lägg till regler för inkommande trafik i Windows-brandväggskonfigurationen för TCP/IP-portar som används för att kommunicera med ICM

För en automatiserad importeras av alla ändringar som är tillgänglig, rekommenderas att med jämna mellanrum tillämpa korrigering samlingen SAP-kommentar som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Mer information om DBA Cockpit för SAP ASE finns i följande SAP-information:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhetskopiering/återställning överväganden för SAP ASE
När du distribuerar SAP ASE till Azure kan måste din backup metod granskas. Även om systemet inte är en produktiv system måste SAP-databasen hos SAP ASE säkerhetskopieras regelbundet. Eftersom Azure Storage håller tre bilder, är nu en säkerhetskopia mindre viktiga med avseende på kompenserande en krasch lagring. Det främsta skälet för att bibehålla en korrekt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Målet är så att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tid eller använda säkerhetskopieringar i Azure som startvärde för ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en SAP nivå 2-konfiguration till en 3-skikts systeminställningarna för samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som lokalt. Se SAP-information:

* [1588316]
* [1585981]

Mer information om att skapa dumpen konfigurationer och schemalägga säkerhetskopieringar. Beroende på din strategi och dina behov kan du konfigurera Dumpar databasen och loggfilerna du disken till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhetskopiering. Om du vill minska risken för dataförlust om ett fel, rekommenderas att använda en disk där det finns ingen databasenhet.

Förutom data och LOB erbjuder också komprimering SAP ASE säkerhetskopieringskomprimering. Om du vill ta mindre plats med databasen och loggfilerna Dumpar rekommenderas att använda säkerhetskopieringskomprimering. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att ladda ned säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till lokalt.

Använd inte enhet D:\ som mål för databas eller logg dumpen.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vilka genomflödet av dessa volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan anta en:

* Det färre antalet diskar som används för att lagra databasen-enheter mindre totala genomflödet i läsning
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till den lägsta genomflödet

Att öka antalet mål att skriva till det finns två alternativ som kan vara används kombinerad beroende på dina behov:

* Striping säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS genomströmning på stripe volymen
* Skapa en dump konfiguration på SAP ASE nivå, som använder mer än en målkatalogen för att skriva dumpen till

Striping av en volym över flera monterade diskar har diskuterats tidigare i den här guiden. Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen för lagrad procedur sp_config_dump som används för att skapa dumpen-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Katastrofåterställning med virtuella Azure-datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datareplikering med SAP Sybase replikering av Server
Med SAP-ASE SAP Sybase replikering Server (SRS) tillhandahåller en varmt vänteläge lösning för att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar samt funktionellt i en virtuell dator som finns i Azure-tjänster för virtuella datorer som lokalt.

SAP ASE HADR kräver inte en intern belastningsutjämnare i Azure och har inte beroenden på nivån OS-kluster och fungerar i Windows Azure och virtuella Linux-datorer. För information om SAP ASE HADR läsa den [användarhandboken för SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specifik information skrivs till SAP ASE på Linux
Från och med Microsoft Azure kan migrera du enkelt befintliga SAP ASE program till Azure-datorer. SAP ASE i en virtuell dator kan du minska den totala ägandekostnaden för distribution, hantering och underhåll av breda företagsprogram genom att enkelt migrera dessa program till Microsoft Azure. Med SAP ASE i en virtuell dator i Azure, kan administratörer och utvecklare fortfarande använda samma utveckling och Administrationsverktyg som är tillgängliga lokalt.

För att distribuera virtuella Azure-datorer är det viktigt att veta officiella SLA: er, som finns här: <https://azure.microsoft.com/support/legal/sla>

SAP mätinformationen och en lista över SAP certifierade VM SKU: er finns i SAP-kommentar [1928533]. Ytterligare SAP storleksanpassa dokument för Azure virtuella datorer finns här <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> och här <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruktioner och rekommendationer om användningen av Azure Storage, distribution av SAP virtuella datorer eller SAP övervakning gäller för distributioner av SAP ASE tillsammans med SAP-program som anges i de fyra första kapitlen i det här dokumentet.

Följande två SAP information innehåller allmän information om ASE på Linux- och ASE i molnet:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Stöd för SAP-ASE Version
SAP för närvarande stöder SAP ASE version 16,0 för SAP Business Suite. Alla uppdateringar för SAP ASE server eller JDBC och ODBC-drivrutiner som ska användas med produkter SAP Business Suite tillhandahålls enbart på marknadsplatsen SAP Service på: <https://support.sap.com/swdc>.

För installationer lokalt, att inte hämta uppdateringar för SAP ASE servern eller för JDBC och ODBC-drivrutiner direkt från Sybase-webbplatser. Mer detaljerad information om korrigeringsprogram som stöds för användning med SAP Business Suite produkter lokalt och i Azure Virtual Machines finns följande SAP-information:

* [1590719]
* [1973241]

Allmän information om hur du kör SAP Business Suite på SAP ASE kan hittas i den [Tillståndsändringsavisering](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Riktlinjer för SAP ASE konfiguration för SAP-relaterade SAP ASE installationer i virtuella Azure-datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur för SAP ASE-distribution
I enlighet med den allmänna beskrivningen SAP ASE körbara filer finns eller installerats i filsystemet roten för den virtuella datorn (/sybase). Vanligtvis utnyttjas de flesta av databaser för SAP ASE system och verktyg inte hårddisk av SAP NetWeaver arbetsbelastning. Därför kan de verktyg och system databaserna (master, model, saptools, sybmgmtdb, sybsystemdb) finns kvar på filsystemet rot. 

Ett undantag kan vara tillfälliga databasen som innehåller alla arbetsobjekt tabeller och temporära tabeller som skapats av SAP ASE som vid vissa SAP ERP och alla BW arbetsbelastningar kan kräva högre datavolym eller i/o-åtgärder, volym som inte får plats i den ursprungliga VM OS disk.

Beroende på vilken version av SAPInst/SWPM används för att installera innehålla databasen:

* En enda SAP ASE tempdb som skapas när du installerar SAP ASE
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare saptempdb som skapats av rutinen SAP-installation
* En SAP ASE tempdb som skapats genom att installera SAP ASE och en ytterligare tempdb som har skapats manuellt (till exempel följande SAP-kommentar [1752266]) till ERP/BW specifika tempdb-krav

Vid specifika ERP eller alla BW arbetsbelastningar kan det vara bra om prestanda för att tempdb-enheter efter dessutom skapade tempdb (via SWPM eller manuellt) på en separat filsystem, som representeras av en enda Azure datadisk eller en Linux-RAID-spannin g flera diskar i Azure data. Om det finns inga ytterligare tempdb, rekommenderas att skapa en (SAP-kommentar [1752266]).

Följande steg ska utföras för dessutom skapade tempdb för sådana system:

* Flytta den första tempdb-katalogen till SAP-databasen första filsystem
* Lägga till tempdb kataloger till var och en av diskarna med ett filsystem SAP-databasen

Den här konfigurationen kan tempdb antingen förbruka mer utrymme än systemenheten kan ge. Som referens Kontrollera en tempdb directory storlekar på befintliga system som kör lokalt. Eller en sådan konfiguration skulle aktivera IOPS siffror mot tempdb som kan tillhandahållas med systemenheten. Igen kan system som kör lokalt användas för att övervaka i/o-arbetsbelastning mot tempdb.

Placera inte några kataloger som SAP ASE till /mnt eller /mnt/resource av den virtuella datorn. Detta gäller även för tempdb, även om de objekt som lagras i tempdb är temporära eftersom /mnt eller /mnt/resource är en standard Azure VM temp utrymme, vilket inte är beständig. Mer information om det tillfälliga utrymmet Azure VM finns i [i den här artikeln][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Effekten av databasen komprimering
Alla åtgärder, vilket minskar IOPS kan hjälpa till att sträcka ut arbetsbelastningen kan köra i ett IaaS-scenario som Azure i konfigurationer där i/o-bandbredd kan bli en begränsande faktor. Vi rekommenderar därför att se till att SAP ASE komprimering används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att utföra komprimering innan du laddar upp till Azure om den inte redan har implementerats får slut på flera orsaker:

* Mängden data som skulle överföras till Azure är lägre
* Varaktighet för komprimering körningen är kortare förutsatt att något kan använda starkare maskinvara med flera processorer eller högre i/o-bandbredd eller mindre i/o-svarstid lokalt
* Databasen är mindre kan leda till lägre kostnader för diskallokering

Data och LOB komprimering fungerar på en virtuell dator som finns i Azure Virtual Machines som sker lokalt. För mer information om hur du kontrollerar om komprimering är redan i använda i en befintlig databas för SAP ASE, kontrollera SAP-kommentar [1750510]. För ytterligare information om databasen komprimering Se SAP-kommentar [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka Databasinstanser
För SAP-system som använder SAP ASE som databasplattform, är DBACockpit tillgänglig som inbäddade webbläsarfönster i transaktion DBACockpit eller Webdynpro. Men alla funktioner för övervakning och administrera databasen är tillgänglig i Webdynpro implementeringen av DBACockpit endast.

Som med lokalt system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av Webdynpro implementeringen av DBACockpit. Följ SAP-kommentar [1245200] att aktivera användning av webdynpros och generera de nödvändiga. När följa anvisningarna i ovanstående anteckningar konfigurera du också hanteraren för Internet-kommunikation (icm) tillsammans med portarna som ska användas för http och https-anslutningar. Standardinställningen för HTTP-ser ut så här:

> ICM/server_port_0 = skydd = HTTP, PORT = 8000 PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = skydd = HTTPS, PORT = 443$ $PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

och länkar som har genererats i transaktionen DBACockpit ser ut ungefär så här:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Beroende på om och hur den virtuella datorn för Azure som värd för SAP-systemet är ansluten via plats-till-plats, flera platser eller ExpressRoute (mellan lokala distribution) måste du kontrollera att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på datorn där du försöker öppna DBACockpit från. Se SAP-kommentar [773830] att förstå hur ICM anger fullständigt kvalificerat värdnamn beroende på profilen parametrar och ange parametern icm/host_name_full uttryckligen om det behövs.

Om du har distribuerat den virtuella datorn i ett scenario med endast molnbaserad utan anslutning mellan lokala och Azure måste du definiera en offentlig IP-adress och en domainlabel. Formatet för det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mer information för DNS-namnet kan hittas [här][virtual-machines-azurerm-versus-azuresm].

Ställa in SAP profil parametern icm/host_name_full till DNS-namnet på Azure VM länken kan se ut:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

I det här fallet måste du se till att:

* Lägg till regler för inkommande trafik till Nätverkssäkerhetsgruppen i Azure-portalen för TCP/IP-portar som används för att kommunicera med ICM
* Lägg till regler för inkommande trafik i Windows-brandväggskonfigurationen för TCP/IP-portar som används för att kommunicera med ICM

För en automatiserad importeras av alla ändringar som är tillgänglig, rekommenderas att med jämna mellanrum tillämpa korrigering samlingen SAP-kommentar som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Mer information om DBA Cockpit för SAP ASE finns i följande SAP-information:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhetskopiering/återställning överväganden för SAP ASE
När du distribuerar SAP ASE till Azure måste din backup metod granskas. Även om systemet inte är en produktiv system måste SAP-databasen hos SAP ASE säkerhetskopieras regelbundet. Eftersom Azure Storage håller tre bilder, är nu en säkerhetskopia mindre viktiga med avseende på kompenserande en krasch lagring. Det främsta skälet för att bibehålla en korrekt plan för säkerhetskopiering och återställning är mer som du kan kompensera för logiska/manuell fel genom att tillhandahålla punkt i tiden återställningsfunktioner. Målet är så att antingen använda säkerhetskopieringar att återställa databasen till en viss punkt i tid eller använda säkerhetskopieringar i Azure som startvärde för ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en SAP nivå 2-konfiguration till en 3-skikts systeminställningarna för samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som lokalt. Se SAP-information:

* [1588316]
* [1585981]

Mer information om att skapa dumpen konfigurationer och schemalägga säkerhetskopieringar. Beroende på din strategi och dina behov kan du konfigurera Dumpar databasen och loggfilerna du disken till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhetskopiering. Om du vill minska risken för dataförlust om ett fel, rekommenderas att använda en disk där det finns ingen directory/databasfil.

Förutom data och LOB erbjuder också komprimering SAP ASE säkerhetskopieringskomprimering. Om du vill ta mindre plats med databasen och loggfilerna Dumpar rekommenderas att använda säkerhetskopieringskomprimering. Mer information finns i SAP-kommentar [1588316]. Komprimera säkerhetskopian är också viktigt att minska mängden data som ska överföras om du planerar att ladda ned säkerhetskopior eller virtuella hårddiskar som innehåller säkerhetskopiering Dumpar från Azure-dator till lokalt.

Använd inte Azure VM temp utrymme /mnt eller /mnt/resource som mål för databas eller logg dumpen.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestandaöverväganden för säkerhetskopiering/återställning
Säkerhetskopiering/återställning prestanda är beroende av hur många volymer kan läsas parallellt och vilka genomflödet av dessa volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan anta en:

* Det färre antalet diskar som används för att lagra databasen-enheter mindre totala genomflödet i läsning
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering
* Färre mål (Linux programvarubaserad RAID, diskar) att skriva säkerhetskopian till den lägsta genomflödet

Att öka antalet mål att skriva till det finns två alternativ som kan vara används kombinerad beroende på dina behov:

* Striping säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS genomströmning på stripe volymen
* Skapa en dump konfiguration på SAP ASE nivå, som använder mer än en målkatalogen för att skriva dumpen till

Striping av en volym över flera monterade diskar har diskuterats tidigare i den här guiden. Mer information om hur du använder flera kataloger i SAP ASE dump konfigurationen finns i dokumentationen för lagrad procedur sp_config_dump som används för att skapa dumpen-konfigurationen på den [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Katastrofåterställning med virtuella Azure-datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datareplikering med SAP Sybase replikering av Server
Med SAP-ASE SAP Sybase replikering Server (SRS) tillhandahåller en varmt vänteläge lösning för att överföra databastransaktioner asynkront till en fjärrplats. 

Installationen och driften av SRS fungerar samt funktionellt i en virtuell dator som finns i Azure-tjänster för virtuella datorer som lokalt.

ASE HADR via SAP replikering Server stöds inte vid denna tidpunkt. Den kan testas med och släpps i framtiden för Microsoft Azure-plattformar.

## <a name="specifics-to-oracle-database-on-windows"></a>Specifik information skrivs till Oracle-databas i Windows
Oracle programvaran stöds av Oracle körs på Microsoft Windows Hyper-V och Azure. Mer information om allmänna stöd för Windows Hyper-V och Azure, kontrollerar du: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Följande allmänna stöd situation för SAP-program som utnyttjar Oracle-databaser stöds också. Information om namnet i den här delen av dokumentet.

### <a name="oracle-version-support"></a>Stöd för Oracle-Version
Oracle och motsvarande OS-versioner som stöds för körs SAP på Oracle på Azure Virtual Machines kan hittas i SAP-kommentar [2039619].

Allmän information om hur du kör SAP Business Suite på Oracle kan hittas i 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för Oracle-konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a>Storage-konfiguration
Bara instans Oracle med NTFS-formaterade diskar stöds. Alla databasfiler måste lagras i NTFS-filsystemet som är baserat på virtuella hårddiskar eller hanterade diskar. Diskarna monteras på Azure-dator och baseras på Azure sidan BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller hanterade diskar (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som Azure Filtjänster:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

är **inte** stöd för Oracle-databasfiler!

Med hjälp av diskar baserat på Azure sidan BLOB Storage eller hanterade diskar uppgifterna i det här dokumentet i kapitlet [cachelagring för virtuella datorer och datadiskar] [ dbms-guide-2.1] och [Microsoft Azure Storage] [ dbms-guide-2.3] gäller för distributioner med Oracle-databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS genomströmning för Azure-diskarna. De exakta kvoterna används beroende på vilken typ av VM. En lista över Virtuella typer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Om du vill identifiera de Virtuella Azure-typerna som stöds, referera till SAP-kommentar [1928533].

Så länge kvoten IOPS per disk uppfyller kraven, är det möjligt att lagra alla DB-filer på en enda monterade disken. 

Om fler IOPS krävs, rekommenderas att använda fönstret lagringspooler (endast tillgängligt i Windows Server 2012 och senare) eller Windows striping för Windows 2008 R2 att skapa en logisk enhet för stora över flera monterade diskar (Se även kapitel [ Programvara RAID] [ dbms-guide-2.2] i det här dokumentet). Den här metoden förenklar omkostnader för administration för att hantera diskutrymmet och undviker arbete för att manuellt distribuera filer över flera monterade diskar.

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
För säkerhetskopiering / återställa funktioner, SAP BR * verktyg för Oracle stöds på samma sätt som på standard operativsystemen Windows Server och Hyper-V. Oracle Recovery Manager (RMAN) har också stöd för säkerhetskopiering till disk- och återställa från disken.

#### <a name="high-availability"></a>Hög tillgänglighet
Oracle Data Guard stöds för hög tillgänglighet och katastrofåterställning återställning. Information finns i [detta] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentation.

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning tillämpas enligt beskrivningen i de tre första kapitlen i det här dokumentet för distributioner av virtuella datorer med Oracle-databasen.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Specifik information skrivs till Oracle-databas på Oracle Linux
Oracle programvaran stöds av Oracle körs på Microsoft Windows Hyper-V och Azure. Mer information om allmänna stöd för Windows Hyper-V och Azure, kontrollerar du: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Följande allmänna stöd situation för SAP-program som utnyttjar Oracle-databaser stöds också. Information om namnet i den här delen av dokumentet.

### <a name="oracle-version-support"></a>Stöd för Oracle-Version
Oracle och motsvarande OS-versioner som stöds för körs SAP på Oracle på Azure Virtual Machines kan hittas i SAP-kommentar [2039619].

Allmän information om hur du kör SAP Business Suite på Oracle kan hittas i 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för Oracle-konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a>Storage-konfiguration
Endast stöds instans Oracle med ext3, ext4 och xfs formaterade diskar. Alla databasfiler måste lagras i dessa filsystem som baseras på virtuella hårddiskar eller hanterade diskar. Diskarna monteras på Azure-dator och baseras på Azure sidan BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller hanterade diskar (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som Azure Filtjänster:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

är **inte** stöd för Oracle-databasfiler!

Med hjälp av diskar baserat på Azure sidan BLOB Storage eller hanterade diskar uppgifterna i det här dokumentet i kapitlet [cachelagring för virtuella datorer och datadiskar] [ dbms-guide-2.1] och [Microsoft Azure Storage] [ dbms-guide-2.3] gäller för distributioner med Oracle-databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS genomströmning för Azure-diskarna. De exakta kvoterna används beroende på vilken typ av VM. En lista över Virtuella typer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Om du vill identifiera de Virtuella Azure-typerna som stöds, referera till SAP-kommentar [1928533]

Så länge kvoten IOPS per disk uppfyller kraven, är det möjligt att lagra alla DB-filer på en enda monterade disken. 

Om fler IOPS krävs, rekommenderas att använda LVM (logisk volymhanterare) eller MDADM för att skapa en stor logisk volym över flera monterade diskar. Se även kapitel [programvara RAID] [ dbms-guide-2.2] i det här dokumentet. Den här metoden förenklar omkostnader för administration för att hantera diskutrymmet och undviker arbete för att manuellt distribuera filer över flera monterade diskar.

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
För säkerhetskopiering / återställa funktioner, SAP BR * verktyg för Oracle stöds på samma sätt som på utan operativsystem och Hyper-V. Oracle Recovery Manager (RMAN) har också stöd för säkerhetskopiering till disk- och återställa från disken.

#### <a name="high-availability"></a>Hög tillgänglighet
Oracle Data Guard stöds för hög tillgänglighet och katastrofåterställning återställning. Information finns i [detta] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentation.

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning tillämpas enligt beskrivningen i de tre första kapitlen i det här dokumentet för distributioner av virtuella datorer med Oracle-databasen.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Programvarukrav för SAP MaxDB databas i Windows
### <a name="sap-maxdb-version-support"></a>Stöd för SAP-MaxDB Version
SAP för närvarande stöder SAP MaxDB version 7,9 för användning med SAP NetWeaver-baserade produkter i Azure. Alla uppdateringar för SAP MaxDB server eller JDBC och ODBC-drivrutiner som ska användas med SAP NetWeaver-baserade produkter tillhandahålls enbart på marknadsplatsen SAP-tjänsten på <https://support.sap.com/swdc>.
Allmän information om hur du kör SAP NetWeaver på SAP MaxDB finns på <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP MaxDB DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP MaxDB DBMS på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows, vilket är Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Tillgängliga SAP MaxDB dokumentation
Du hittar den uppdaterade listan med SAP MaxDB dokumentationen i följande SAP-kommentar [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP MaxDB konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Lagringskonfigurationen
Metodtips för Azure-lagring för SAP MaxDB följer allmänna rekommendationer som anges i kapitel [struktur för en distribution av RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Precis som andra databaser har SAP MaxDB också data och loggfiler. SAP MaxDB terminologi är dock rätt termen ”volym” (inte ”fil”). Det finns till exempel SAP MaxDB datavolymer och loggvolymer. Blanda inte ihop dem med OS-volymer. 
> 
> 

Kort sagt behöver du:

* Om du använder Azure Storage-konton, anger du Azure storage-konto som innehåller SAP MaxDB data och loggfilen volymer (d.v.s. filer) till **lokalt Redundant lagring (LRS)** som anges i kapitel [Microsoft Azure Storage][dbms-guide-2.3].
* Separata i/o-sökväg för SAP MaxDB datavolymer (d.v.s. filer) från i/o-sökväg för loggvolymer (d.v.s. filer). Detta innebär att SAP MaxDB datavolymer (d.v.s. filer) måste installeras på en logisk enhet och SAP MaxDB loggvolymer (d.v.s. filer) måste installeras på en annan logisk enhet.
* Ange rätt cachelagring typ för varje disk, beroende på om du använder för SAP MaxDB data- eller loggfilen volymer (d.v.s. filer) och om du använder Azure-Standard eller Azure Premium-lagring, enligt beskrivningen i kapitel [cachelagring för virtuella datorer och datadiskar] [dbms-guide-2.1].
* Så länge kvoten IOPS per disk uppfyller kraven, är det möjligt att lagra alla datavolymer som på en monterad disk och även lagra alla loggvolymer för databasen på en annan enkel monterade disken.
* Om det krävs mer IOPS och/eller blanksteg, rekommenderas att använda lagringspooler i Microsoft Windows (endast tillgängligt i Microsoft Windows Server 2012 och senare) eller Microsoft Windows striping för Microsoft Windows 2008 R2 för att skapa en logisk enhet för stora över flera monterade diskar. Se även kapitel [programvara RAID] [ dbms-guide-2.2] i det här dokumentet. Den här metoden förenklar omkostnader för administration för att hantera diskutrymmet och undviker att behöva distribuera manuellt filer över flera monterade diskar.
* För de högsta IOPS-kraven, kan du använda Azure Premium-lagring som är tillgänglig på DS-serien och GS-serien virtuella datorer.

![För referenskonfiguration av Azure IaaS-VM för SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Säkerhetskopiering och återställning
När du distribuerar SAP MaxDB till Azure måste du granska din backup metoder. Även om systemet inte är en produktiv system måste SAP-databasen hos SAP MaxDB säkerhetskopieras regelbundet. Eftersom Azure Storage håller tre bilder, är nu en säkerhetskopia mindre viktiga för att skydda datorn mot lagring problem och viktigare operativa och administrativa fel. Det främsta skälet för att bibehålla en korrekt säkerhetskopiering och återställning plan är så att du kan kompensera för logiska eller manuell fel genom att tillhandahålla tidpunkt i återställningsfunktioner. Målet är så att antingen använda säkerhetskopieringar att återställa databasen till en viss tidpunkt eller att använda säkerhetskopieringar i Azure som startvärde för ett annat system genom att kopiera den befintliga databasen. Till exempel kunde du överföra från en SAP nivå 2-konfiguration till en 3-skikts systeminställningarna för samma system genom att återställa en säkerhetskopia.

Säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som för lokala datorer, så du kan använda standard SAP MaxDB säkerhetskopiering/återställning verktygen som beskrivs i ett av de SAP MaxDB dokumentationen dokument som anges i SAP-kommentar [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestandaöverväganden för säkerhetskopiering och återställning
Prestanda för säkerhetskopiering och återställning är beroende av hur många volymer kan läsas parallellt och genomströmning av volymerna som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan anta en:

* Det färre antalet diskar som används för att lagra databasen enheter, nedre övergripande läsgenomströmning
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till nedre genomflödet

Om du vill öka antalet mål att skriva till finns det två alternativ som du kan använda, eventuellt i kombination, beroende på dina behov:

* Trafikklass separata volymer för säkerhetskopiering
* Striping säkerhetskopiering målvolymen över flera monterade diskar för att kunna förbättra IOPS genomströmning på volymen stripe-diskar
* Med separata dedikerade logiska diskenheter för:
  * SAP MaxDB Säkerhetskopiera volymer (d.v.s. filer)
  * SAP MaxDB datavolymer (d.v.s. filer)
  * SAP MaxDB loggvolymer (d.v.s. filer)

Striping av en volym över flera monterade diskar har diskuterats tidigare i kapitlet [programvara RAID] [ dbms-guide-2.2] i det här dokumentet. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Andra
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning gäller även enligt beskrivningen i de tre första kapitlen i det här dokumentet för distributioner av virtuella datorer med MaxDB SAP-databasen.
Andra SAP MaxDB-specifika inställningar är transparent för virtuella datorer i Azure och beskrivs i olika dokument som anges i SAP-kommentar [767598] och i anteckningarna SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Programvarukrav för SAP liveCache i Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache stöd för Version
Minimal version av SAP liveCache stöds i Azure Virtual Machines är **SAP LC/LCAPPS 10.0 SP 25** inklusive **liveCache 7.9.08.31** och **LCA Build 25**, släppt för **EhP 2 för SAP SCM 7.0** och högre.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Microsoft Windows-versioner och Virtuella Azure-typer som stöds för SAP liveCache DBMS
För att hitta den Microsoft Windows-versionen som stöds för SAP liveCache på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av operativsystemet Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache riktlinjer för konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="recommended-azure-vm-types"></a>Rekommenderade Azure VM-typer
Har en stor påverkan på prestanda för SAP liveCache som SAP liveCache är ett program som utför enorma beräkningar, mängd och hastighet för RAM-minne och CPU. 

För de Virtuella Azure-typer som stöds av SAP (SAP-kommentar [1928533]), alla virtuella processorresurser allokeras till den virtuella datorn backas upp av dedikerade fysiska processorresurser av hypervisor-programmet. Ingen överetablering (och därför ingen konkurrens om processorresurser) sker.

På samma sätt för alla Virtuella Azure-instans typer som stöds av SAP, är det Virtuella minnet 100% mappas till det fysiska minnet - överetablering (överdrivet åtagande), till exempel inte används.

Från det här perspektivet rekommenderas att använda den nya typen D-serien eller DS-serien (i kombination med Azure Premium Storage) Azure VM, eftersom de har 60% snabbare processorer än A-serien. För den högsta RAM-minne och CPU-belastningen kan du använda G-serien och GS-serien (i kombination med Azure Premium Storage) virtuella datorer med den senaste Intel? Xeon? processor E5 v3-familjen, som har två gånger med minne och fyra gånger solid-state drive-lagring (SSD) D/DS-serien.

#### <a name="storage-configuration"></a>Lagringskonfiguration
Eftersom SAP liveCache är baserad på SAP MaxDB teknik, Azure storage bästa praxis rekommendationer för SAP MaxDB enligt kapitel [lagringskonfiguration] [ dbms-guide-8.4.1] gäller också för SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedikerade virtuella Azure-datorn för liveCache
Eftersom SAP liveCache använder sig mycket dataresurser, för effektiv användning rekommenderas att distribuera på en dedikerad Azure-dator. 

![Dedikerade virtuella Azure-datorn liveCache för produktiva användningsfall][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Säkerhetskopiera och återställ
säkerhetskopiering och återställning, inklusive prestandaöverväganden, som beskrivs i de relevanta kapitlen SAP MaxDB [säkerhetskopierar och återställer] [ dbms-guide-8.4.2] och [prestandaöverväganden för säkerhetskopiering och återställa][dbms-guide-8.4.3]. 

#### <a name="other"></a>Annat
Andra allmänna områden som beskrivs i den relevanta SAP-MaxDB [detta] [ dbms-guide-8.4.4] kapitel. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Programvarukrav för SAP innehållsservern på Windows
SAP Content Server är en separat, server-baserade komponent för att lagra innehåll, till exempel elektroniska dokument i olika format. SAP innehållsservern tillhandahålls av utvecklingen av teknik och kommer att använda flera program för SAP-program. Den installeras på ett separat system. Vanliga innehållet är utbildningsmaterial och dokumentation från Knowledge datalager eller tekniska ritningar från mySAP PLM dokumenthanteringssystem. 

### <a name="sap-content-server-version-support"></a>Stöd för SAP innehållsserver Version
SAP stöder för närvarande:

* **SAP innehållsserver** med version **6.50 (och senare)**
* **SAP MaxDB version 7,9**
* **Microsoft IIS (Internet Information Server) version 8.0 (och senare)**

Det rekommenderas att använda den senaste versionen av SAP innehållsserver som vid tidpunkten för med det här dokumentet är **6.50 SP4**, och den senaste versionen av **Microsoft IIS 8.5**. 

Kontrollera de senaste versionerna som stöds av SAP innehållsservern och Microsoft IIS i den [SAP produkten tillgänglighet matris PAM-][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Microsoft Windows och Virtuella Azure-typer som stöds för SAP innehållsserver
Om du vill ta reda på Windows-version som stöds för SAP Content Server på Azure, se:

* [SAP produkten tillgänglighet matris (PAM)][sap-pam]
* SAP-kommentar [1928533]

Vi rekommenderar starkt att använda den senaste versionen av Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för SAP innehållsserver konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Om du konfigurerar SAP innehållsservern för att lagra filer i databasen SAP MaxDB alla Azure-lagring best practices rekommendation för SAP MaxDB enligt kapitel [lagringskonfiguration] [ dbms-guide-8.4.1] också är giltiga för scenariot SAP innehållsserver. 

Om du konfigurerar SAP innehållsservern för att lagra filer i filsystemet, rekommenderas att använda en dedikerad logisk enhet. Med lagringsutrymmen för Windows kan du också öka logiska diskens storlek och IOPS genomströmning som beskrivs i kapitlet [programvara RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>SAP innehållsserver plats
SAP Content Server måste distribueras i samma Azure-region och Azure VNET där SAP-systemet har distribuerats. Du kan bestämma om du vill distribuera innehåll SAP-Server-komponenter på en dedikerad virtuell Azure-dator eller av samma virtuella dator där SAP-systemet körs. 

![Dedikerad Azure VM för SAP Content Server][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP cacheplats Server
SAP Cache-Server är en ytterligare server-baserade komponent att ge åtkomst till (cachelagrade) lokalt. SAP cacheserver cachelagrar dokument för en SAP-innehållsserver. Detta är att optimera nätverkstrafik om dokument har mer än en gång hämtas från olika platser. Allmän regel är att SAP Cache-Server måste vara fysiskt nära den klient som har åtkomst till SAP Cache-servern. 

Här har du två alternativ:

1. **Klienten är ett system för backend-SAP** om ett backend SAP-system är konfigurerad för att komma åt SAP innehållsserver, SAP systemet är en klient. Eftersom både SAP-systemet och SAP innehållsserver distribueras i samma Azure-region, i samma Azure datacenter, är de fysiskt nära varandra. Det finns därför ingen måste ha en dedikerad SAP Cache-Server. SAP UI-klienter (SAP GUI eller en webbläsare) åtkomst direkt SAP-systemet och SAP-systemet hämtar dokument från innehållsservern SAP.
2. **Klienten är en lokal webbläsare** SAP innehållsservern kan konfigureras för att användas direkt av webbläsaren. I det här fallet är en webbläsare som körs lokalt en innehållsserver SAP-klient. Lokala datacenter och Azure-datacenter placeras på olika fysiska platser (helst nära varandra). Ditt lokala datacenter är ansluten till Azure via Azure plats-till-plats-VPN eller ExpressRoute. Även om båda alternativen erbjuder säker anslutning för VPN-nätverk till Azure, erbjuder inte ett nätverk bandbredd och fördröjning SERVICENIVÅAVTAL mellan lokala datacenter och Azure-datacentret nätverksanslutning för plats-till-plats. För att påskynda åtkomst till dokument, kan du göra något av följande:
   1. Installera SAP Cache Server lokalt, Stäng webbläsaren för lokalt (alternativet på [detta] [ dbms-guide-900-sap-cache-server-on-premises] bild)
   2. Konfigurera Azure ExpressRoute, vilket ger en snabb och låg latens dedikerad nätverksanslutning mellan lokala datacenter och Azure-datacenter.

![Alternativet att installera SAP Cache Server lokalt][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Säkerhetskopiering/återställning
Om du konfigurerar SAP innehållsservern för att lagra filer i databasen SAP MaxDB säkerhetskopiering/återställning proceduren- och prestandaöverväganden som beskrivs i SAP MaxDB kapitel [säkerhetskopierar och återställer] [ dbms-guide-8.4.2]och kapitel [prestandaöverväganden för säkerhetskopiering och återställning][dbms-guide-8.4.3]. 

Om du konfigurerar SAP innehållsservern för att lagra filer i filsystemet, är ett alternativ att köra en manuell säkerhetskopiering/återställning av hela filstrukturen där dokumenten finns. Liknar SAP MaxDB säkerhetskopiering/återställning, bör ha en dedikerad volym för säkerhetskopiering ändamål. 

#### <a name="other"></a>Annat
Andra innehåll till SAP-serverspecifika inställningar är transparent för virtuella datorer i Azure och beskrivs i olika dokument och SAP anteckningar:

* <https://service.sap.com/contentserver> 
* SAP-kommentar [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Specifik information skrivs till IBM DB2 för LUW i Windows
Du kan enkelt migrera dina befintliga SAP-program som körs på IBM DB2 för Linux, UNIX- och Windows (LUW) till virtuella Azure-datorer med Microsoft Azure. Med SAP på IBM DB2 för LUW kan administratörer och utvecklare fortfarande använda samma utveckling och Administrationsverktyg som finns tillgängliga lokalt.
Allmän information om hur du kör SAP Business Suite på IBM DB2 för LUW kan hittas i SAP Community nätverk (SCN) på <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

För ytterligare information och uppdateringar om SAP på DB2 för LUW på Azure Se SAP-kommentar [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 för Linux och UNIX stöd för Windows-Version
SAP på IBM DB2 för LUW på virtuella Microsoft Azure-tjänster stöds från och med DB2 version 10.5.

Mer information om stöds SAP-produkter och Virtuella Azure-typer finns i SAP-kommentar [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 för Linux, UNIX- och riktlinjer för Windows-konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Alla databasfiler måste lagras i NTFS-filsystemet som är baserat på direkt anslutna diskar. Diskarna monteras på Azure-dator och baseras i Azure sidan BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller hanterade diskar (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som följande Azure Filtjänster är **inte** stöds för databasfilerna: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Om du använder diskar baserat på Azure sidan BLOB Storage eller hanterade diskar instruktionerna som gjorts i det här dokumentet i kapitlet [struktur för en distribution av RDBMS] [ dbms-guide-2] gäller även för distributioner med IBM DB2 för LUW Databas. 

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS genomströmning för diskar. De exakta kvoterna beror på VM-typ som används. En lista över Virtuella typer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Så länge kvoten IOPS per disk räcker är det möjligt att lagra alla databasfiler på en enda monterade disken. 

Prestanda finns överväganden även i kapitlet ”datasäkerhet och prestandaöverväganden för databaskataloger” i guider för SAP-installationen.

Alternativt kan du använda Windows lagringspooler (endast tillgängligt i Windows Server 2012 och senare) eller Windows striping för Windows 2008 R2 som beskrivs i kapitel [programvara RAID] [ dbms-guide-2.2] i detta dokument Skapa en logisk enhet för stora över flera diskar.
För diskarna med DB2 lagringssökvägar för dina sapdata och saptmp kataloger, måste du ange en fysisk disk sektorstorlek på 512 KB. När du använder lagringspooler för Windows, måste du skapa lagringspooler manuellt via kommandoradsgränssnittet med parametern `-LogicalSectorSizeDefault`. Mer information finns i <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Säkerhetskopiera/återställa
Funktionen säkerhetskopiering/återställning för IBM DB2 för LUW stöds på samma sätt som på standard operativsystemen Windows Server och Hyper-V.

Du måste se till att du har en giltig databas strategi för säkerhetskopiering på plats. 

Säkerhetskopiering/återställning prestanda beror på hur många volymer kan läsas parallellt och vilka genomflödet av dessa volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan anta en:

* Det färre antalet diskar som används för att lagra databasen-enheter mindre totala genomflödet i läsning
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till nedre genomflödet

Om du vill öka antalet mål att skriva till att två alternativ använda kombinerad beroende på dina behov:

* Striping säkerhetskopiering målvolymen över flera diskar för att kunna förbättra IOPS genomströmning på stripe volymen
* Med mer än en målkatalogen för att skriva säkerhetskopiering till

#### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och katastrofåterställning
Microsoft Cluster Server (MSCS) stöds inte.

DB2 katastrofåterställning för hög tillgänglighet (HADR) stöds. Om de virtuella datorerna med hög tillgänglighet konfigurationen har fungerar namnmatchning, skilja inte inställningarna i Azure från alla inställningar som görs lokalt. Det rekommenderas inte kan förlita sig på endast IP-lösning.

Använd inte Geo-replikering för lagringskonton som lagrar databasen diskar. Mer information finns i kapitlet [Microsoft Azure Storage] [ dbms-guide-2.3] och kapitel [hög tillgänglighet och katastrofåterställning med virtuella Azure-datorer] [ dbms-guide-3].

#### <a name="other"></a>Annat
Andra allmänna områden som Azure-Tillgänglighetsuppsättningar eller SAP övervakning tillämpas enligt beskrivningen i de tre första kapitlen i det här dokumentet för distributioner av virtuella datorer med IBM DB2 för LUW samt. 

Även gå till kapitel [allmänna SQL Server för SAP på Azure sammanfattning][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Specifik information skrivs till IBM DB2 för LUW på Linux
Du kan enkelt migrera dina befintliga SAP-program som körs på IBM DB2 för Linux, UNIX- och Windows (LUW) till virtuella Azure-datorer med Microsoft Azure. Med SAP på IBM DB2 för LUW kan administratörer och utvecklare fortfarande använda samma utveckling och Administrationsverktyg som finns tillgängliga lokalt. Allmän information om hur du kör SAP Business Suite på IBM DB2 för LUW kan hittas i SAP Community nätverk (SCN) på <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

För ytterligare information och uppdateringar om SAP på DB2 för LUW på Azure Se SAP-kommentar [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 för Linux och UNIX stöd för Windows-Version
SAP på IBM DB2 för LUW på virtuella Microsoft Azure-tjänster stöds från och med DB2 version 10.5.

Mer information om stöds SAP-produkter och Virtuella Azure-typer finns i SAP-kommentar [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 för Linux, UNIX- och riktlinjer för Windows-konfiguration för SAP installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a>Lagringskonfiguration
Alla databasfiler måste vara lagrad i ett filsystem som baseras på direkt anslutna diskar. Diskarna monteras på Azure-dator och baseras i Azure sidan BLOB Storage (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) eller hanterade diskar (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Alla typer av nätverksenheter eller fjärresurser som följande Azure Filtjänster är **inte** stöds för databasfilerna:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Om du använder diskar baserat på Azure sidan BLOB Storage, uppgifterna i det här dokumentet i kapitlet [struktur för en distribution av RDBMS] [ dbms-guide-2] gäller även för distributioner med IBM DB2 för LUW databasen.

Enligt beskrivningen tidigare i den allmänna delen av dokumentet finns kvoter på IOPS genomströmning för diskar. De exakta kvoterna beror på VM-typ som används. En lista över Virtuella typer med sina kvoter hittar [här (Linux)] [ virtual-machines-sizes-linux] och [här (Windows)][virtual-machines-sizes-windows].

Så länge kvoten IOPS per disk räcker är det möjligt att lagra alla databasfiler på en enda disk.

Prestanda finns överväganden även i kapitlet ”datasäkerhet och prestandaöverväganden för databaskataloger” i guider för SAP-installationen.

Du kan också använda LVM (logisk volymhanterare) eller MDADM som beskrivs i kapitlet [programvara RAID] [ dbms-guide-2.2] av det här dokumentet för att skapa en logisk enhet för stora över flera diskar.
För diskarna med DB2 lagringssökvägar för dina sapdata och saptmp kataloger, måste du ange en fysisk disk sektorstorlek på 512 KB.

#### <a name="backuprestore"></a>Säkerhetskopiera/återställa
Funktionen säkerhetskopiering/återställning för IBM DB2 för LUW stöds på samma sätt som standard Linux installation på plats.

Du måste se till att du har en giltig databas strategi för säkerhetskopiering på plats.

Säkerhetskopiering/återställning prestanda beror på hur många volymer kan läsas parallellt och vilka genomflödet av dessa volymer kan vara som bare metal-distributioner. CPU-förbrukning som används av säkerhetskopieringskomprimering kan dessutom spela en viktig roll på virtuella datorer med upp till åtta CPU-trådar. Därför kan anta en:

* Det färre antalet diskar som används för att lagra databasen-enheter mindre totala genomflödet i läsning
* Mindre antal CPU trådar i den virtuella datorn på allvarligare effekten av säkerhetskopieringskomprimering
* Färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till nedre genomflödet

Om du vill öka antalet mål att skriva till att två alternativ använda kombinerad beroende på dina behov:

* Striping säkerhetskopiering målvolymen över flera diskar för att kunna förbättra IOPS genomströmning på stripe volymen
* Med mer än en målkatalogen för att skriva säkerhetskopiering till

#### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och katastrofåterställning
DB2 katastrofåterställning för hög tillgänglighet (HADR) stöds. Om de virtuella datorerna med hög tillgänglighet konfigurationen har fungerar namnmatchning, skilja inte inställningarna i Azure från alla inställningar som görs lokalt. Det rekommenderas inte kan förlita sig på endast IP-lösning.

Använd inte Geo-replikering för lagringskonton som lagrar databasen diskar. Mer information finns i kapitlet [Microsoft Azure Storage] [ dbms-guide-2.3] och kapitel [hög tillgänglighet och katastrofåterställning med virtuella Azure-datorer] [ dbms-guide-3].

#### <a name="other"></a>Annat
Andra allmänna avsnitt som Azure-Tillgänglighetsuppsättningar eller SAP övervakning tillämpas enligt beskrivningen i de tre första kapitlen i det här dokumentet för distributioner av virtuella datorer med IBM DB2 för LUW samt.

Även gå till kapitel [allmänna SQL Server för SAP på Azure sammanfattning][dbms-guide-5.8].

