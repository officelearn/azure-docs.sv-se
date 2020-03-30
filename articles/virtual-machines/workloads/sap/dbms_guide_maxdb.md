---
title: SAP MaxDB-, liveCache- och Content Server-distribution på virtuella Azure-datorer | Microsoft-dokument
description: SAP MaxDB-, liveCache- och Content Server-distribution på Azure
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90de49ae3137735683bae6a18b5f7c8951b021ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645879"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB-, liveCache- och Content Server-distribution på virtuella Azure-datorer

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




Det här dokumentet täcker flera olika områden att tänka på när du distribuerar MaxDB, liveCache och Content Server i Azure IaaS. Som en förutsättning för det här dokumentet bör du ha läst dokumentet [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md) samt andra guider i [SAP-arbetsbelastningen på Azure-dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Detaljer för SAP MaxDB-distributionerna i Windows
### <a name="sap-maxdb-version-support-on-azure"></a>SAP MaxDB-versionssupport på Azure
SAP stöder för närvarande SAP MaxDB version 7.9 eller senare för användning med SAP NetWeaver-baserade produkter i Azure. Alla uppdateringar för SAP MaxDB-servern, eller JDBC- och ODBC-drivrutiner som ska användas med SAP <https://support.sap.com/swdc>NetWeaver-baserade produkter tillhandahålls endast via SAP Service Marketplace på .
Allmän information om hur du kör SAP NetWeaver <https://www.sap.com/community/topic/maxdb.html>på SAP MaxDB finns på .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Microsoft Windows-versioner och Azure VM-typer som stöds för SAP MaxDB DBMS
Information om hur du hittar microsoft Windows-versionen som stöds för SAP MaxDB DBMS på Azure finns i:

* [MATRIS FÖR SAP-produkttillgänglighet (PAM)][sap-pam]
* SAP Anmärkning [1928533]

Det rekommenderas starkt att använda den senaste versionen av operativsystemet Microsoft Windows, som är Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Tillgänglig SAP MaxDB-dokumentation för MaxDB
Du hittar den uppdaterade listan över SAP MaxDB-dokumentation i följande SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB-konfigurationsriktlinjer för SAP-installationer i virtuella Azure-datorer
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Storage-konfiguration
Metodtips för Azure-lagring för SAP MaxDB följer de allmänna rekommendationerna som nämns i kapitel [Lagringsstrukturen för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Liksom andra databaser har SAP MaxDB också data- och loggfiler. I SAP MaxDB-terminologin är dock den korrekta termen "volym" (inte "fil"). Det finns till exempel SAP MaxDB-datavolymer och loggvolymer. Förväxla inte dessa med OS-diskvolymer. 
> 
> 

Kort sagt måste du:

* Om du använder Azure Storage-konton anger du azure storage-kontot som innehåller SAP MaxDB-data och loggvolymer (data och loggfiler) till **LRS (Local Redundant Storage)** enligt vad som anges i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md).
* Separera IO-sökvägen för SAP MaxDB-datavolymer (datafiler) från IO-sökvägen för loggvolymer (loggfiler). Det innebär att SAP MaxDB datavolymer (datafiler) måste installeras på en logisk enhet och SAP MaxDB-loggvolymer (loggfiler) måste installeras på en annan loge.
* Ange rätt cachelagringstyp för varje disk, beroende på om du använder den för SAP MaxDB-data eller loggvolymer (data och loggfiler) och om du använder Azure Standard eller Azure Premium Storage, enligt beskrivningen i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md).
* Så länge den aktuella IOPS-kvoten per disk uppfyller kraven är det möjligt att lagra alla datavolymer på en enda monterad disk och även lagra alla databasloggvolymer på en annan enda monterad disk.
* Om det krävs mer IOPS och/eller utrymme rekommenderar vi att du använder Microsoft Window Storage Pools (endast tillgängliga i Microsoft Windows Server 2012 och senare) för att skapa en stor logisk enhet över flera monterade diskar. Mer information finns i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md). Den här metoden förenklar administrationen för att hantera diskutrymmet och undviker ansträngningen att manuellt distribuera filer över flera monterade diskar.
* Vi rekommenderar starkt att du använder Azure Premium Storage för MaxDB-distributioner. 

![Referenskonfiguration för Azure IaaS VM för SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Säkerhetskopiera och återställ
När du distribuerar SAP MaxDB till Azure måste du granska din säkerhetskopieringsmetod. Även om systemet inte är ett produktivt system måste SAP-databasen som tillhandahålls av SAP MaxDB säkerhetskopieras med jämna mellanrum. Eftersom Azure Storage behåller tre avbildningar är en säkerhetskopia nu mindre viktig när det gäller att skydda ditt system mot lagringsfel och viktigare driftfel eller administrativa fel. Den främsta orsaken till att upprätthålla en korrekt säkerhetskopierings- och återställningsplan är så att du kan kompensera för logiska eller manuella fel genom att tillhandahålla återställningsfunktioner i point-in-time. Så målet är att antingen använda säkerhetskopior för att återställa databasen till en viss tidpunkt eller att använda säkerhetskopior i Azure för att så ett annat system genom att kopiera den befintliga databasen. 

Säkerhetskopiering och återställning av en databas i Azure fungerar på samma sätt som för lokala system, så du kan använda standardverktyg för SAP MaxDB-säkerhetskopiering/återställning, som beskrivs i ett av SAP MaxDB-dokumentationsdokumenten som anges i SAP Note [767598]. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestandaöverväganden för säkerhetskopiering och återställning
Precis som i bare-metal-distributioner är säkerhetskopierings- och återställningsprestanda beroende av hur många volymer som kan läsas parallellt och dataflödet för dessa volymer. Därför kan man anta:

* Ju färre antal diskar som används för att lagra databasenheterna, desto lägre är det totala läsflödet
* Ju färre mål (Stripe-kataloger, diskar) att skriva säkerhetskopian till, desto lägre dataflöde

Om du vill öka antalet mål att skriva till finns det två alternativ som du kan använda, eventuellt i kombination, beroende på dina behov:

* Ägna separata volymer för säkerhetskopiering
* Striping säkerhetskopiering målvolymen över flera monterade diskar för att förbättra IOPS-dataflödet på den randiga diskvolymen
* Med separata dedikerade logiska diskenheter för:
  * SAP MaxDB-säkerhetskopieringsvolymer (t.ex. filer)
  * SAP MaxDB-datavolymer (dvs. filer)
  * SAP MaxDB-loggvolymer (t.ex. filer)

Striping en volym över flera monterade diskar har diskuterats tidigare i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Andra överväganden
Alla andra allmänna områden, till exempel Azure-tillgänglighetsuppsättningar eller SAP-övervakning, gäller också enligt beskrivningen i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md).  för distribution av virtuella datorer med SAP MaxDB-databasen.
Andra SAP MaxDB-specifika inställningar är transparenta för virtuella Azure-datorer och beskrivs i olika dokument som anges i SAP Note [767598] och i dessa SAP Notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Specifika för SAP liveCache-distributioner i Windows
### <a name="sap-livecache-version-support"></a>Stöd för SAP liveCache-version
Minimal version av SAP liveCache som stöds i Azure Virtual Machines är **SAP LC/LCAPPS 10.0 SP 25** inklusive **liveCache 7.9.08.31** och **LCA-Build 25**, som släpps för **EhP 2 för SAP SCM 7.0** och senare versioner.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Microsoft Windows-versioner och Azure VM-typer som stöds för SAP liveCache DBMS
Information om hur du hittar microsoft Windows-versionen för SAP liveCache på Azure som stöds finns i:

* [MATRIS FÖR SAP-produkttillgänglighet (PAM)][sap-pam]
* SAP Anmärkning [1928533]

Vi rekommenderar starkt att du använder den senaste versionen av operativsystemet Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Riktlinjer för KONFIGURATION AV SAP liveCache för SAP-installationer i virtuella Azure-datorer
#### <a name="recommended-azure-vm-types-for-livecache"></a>Rekommenderade Azure VM-typer för liveCache
Som SAP liveCache är ett program som utför enorma beräkningar, mängden och hastigheten på RAM och CPU har en stor inverkan på SAP liveCache prestanda. 

För azure VM-typer som stöds av SAP (SAP Note [1928533]) backas alla virtuella CPU-resurser som allokerats till den virtuella datorn av dedikerade fysiska CPU-resurser i hypervisorn. Ingen överetablering (och därför ingen konkurrens om CPU-resurser) äger rum.

På samma sätt, för alla Azure VM-instanstyper som stöds av SAP, är vm-minnet 100% mappat till det fysiska minnet - överetablering (över-åtagande), till exempel, används inte.

Ur detta perspektiv rekommenderas det starkt att använda de senaste virtuella datorerna i Dv2, Dv3, Ev3 och M-serien. Valet av olika typer av virtuella datorer beror på det minne du behöver för liveCache och de CPU-resurser du behöver. Precis som med alla andra DBMS-distributioner är det lämpligt att utnyttja Azure Premium Storage för prestandakritiska volymer.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Lagringskonfiguration för liveCache i Azure
Eftersom SAP liveCache baseras på SAP MaxDB-teknik gäller även alla rekommendationer för bästa praxis för Azure-lagring som nämns för SAP MaxDB som beskrivs i det här dokumentet för SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedikerad Azure VM för liveCache-scenario
Eftersom SAP liveCache intensivt använder beräkningskraft, rekommenderas det för produktiv användning att distribuera på en dedikerad Virtuell Azure-dator. 

![Dedikerad Azure VM för liveCache för produktivt användningsfall](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Säkerhetskopiering och återställning för liveCache i Azure
säkerhetskopiering och återställning, inklusive prestandaöverväganden, beskrivs redan i relevanta SAP MaxDB-kapitel i det här dokumentet. 

#### <a name="other-considerations"></a>Andra överväganden
Alla andra allmänna områden beskrivs redan i det relevanta SAP MaxDB-kapitlet. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Detaljer för SAP Content Server-distributionen i Windows i Azure
SAP Content Server är en separat, serverbaserad komponent för att lagra innehåll, till exempel elektroniska dokument i olika format. SAP Content Server tillhandahålls av teknikutveckling och ska användas för alla SAP-program. Den är installerad på ett separat system. Typiskt innehåll är utbildningsmaterial och dokumentation från Knowledge Warehouse eller tekniska ritningar som kommer från mySAP PLM Document Management System. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Stöd för VERSION AV SAP-innehållsserver för virtuella Azure-datorer
SAP stöder för närvarande:

* **SAP Content Server** med version **6.50 (och högre)**
* **SAP MaxDB version 7.9**
* **Microsoft IIS(Internet Information Server) version 8.0 (och senare)**

Vi rekommenderar starkt att du använder den senaste versionen av SAP Content Server och den senaste versionen av **Microsoft IIS**. 

Kontrollera de senaste versionerna av SAP Content Server och Microsoft IIS i [PAM (SAP Product Availability Matrix).][sap-pam]

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Microsoft Windows- och Azure VM-typer som stöds för SAP-innehållsserver
Information om hur du hittar windowsversionen för SAP Content Server på Azure som stöds finns i:

* [MATRIS FÖR SAP-produkttillgänglighet (PAM)][sap-pam]
* SAP Anmärkning [1928533]

Vi rekommenderar starkt att du använder den senaste versionen av Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Konfigurationsriktlinjer för SAP-innehållsserver för SAP-installationer i virtuella Azure-datorer
#### <a name="storage-configuration-for-content-server-in-azure"></a>Lagringskonfiguration för Content Server i Azure
Om du konfigurerar SAP Content Server för att lagra filer i SAP MaxDB-databasen gäller även alla azure-lagringstipsrekommendationer som nämns för SAP MaxDB i det här dokumentet för SAP Content Server-scenariot. 

Om du konfigurerar SAP Content Server för att lagra filer i filsystemet rekommenderar vi att du använder en dedikerad logisk enhet. Med hjälp av Windows Storage Spaces kan du också öka den logiska diskstorleken och IOPS-dataflödet, enligt beskrivningen i [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Plats för SAP-innehållsserver
SAP Content Server måste distribueras i samma Azure-region och Azure VNET där SAP-systemet distribueras. Du kan bestämma om du vill distribuera SAP Content Server-komponenter på en dedikerad Azure-virtuell dator eller på samma virtuella dator där SAP-systemet körs. 

![Dedikerad Azure VM för SAP-innehållsserver](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Plats för SAP-cacheserver
SAP-cacheservern är ytterligare en serverbaserad komponent som ger åtkomst till (cachelagrade) dokument lokalt. SAP-cacheservern cachelagrar dokumenten för en SAP-innehållsserver. Detta för att optimera nätverkstrafiken om dokument måste hämtas mer än en gång från olika platser. Den allmänna regeln är att SAP-cacheservern måste vara fysiskt nära klienten som kommer åt SAP-cacheservern. 

Här har du två alternativ:

1. **Klienten är ett backend SAP-system** Om ett serverd SAP-system är konfigurerat för att komma åt SAP Content Server är det SAP-systemet en klient. Eftersom både SAP-system och SAP Content Server distribueras i samma Azure-region, i samma Azure-datacenter, är de fysiskt nära varandra. Därför finns det ingen anledning att ha en dedikerad SAP Cache Server. SAP UI-klienter (SAP GUI eller webbläsare) ansluter direkt till SAP-systemet och SAP-systemet hämtar dokument från SAP Content Server.
2. **Klienten är en lokal webbläsare** SAP Content Server kan konfigureras för att nås direkt av webbläsaren. I det här fallet är en webbläsare som kör lokalt en klient till SAP Content Server. Lokala datacenter och Azure-datacenter placeras på olika fysiska platser (helst nära varandra). Ditt lokala datacenter är anslutet till Azure via Azure Site-to-Site VPN eller ExpressRoute. Även om båda alternativen erbjuder säker VPN-nätverksanslutning till Azure, erbjuder plats-till-plats-nätverksanslutning inte en nätverksbandbredd och svarstidS-SLA mellan det lokala datacentret och Azure-datacentret. Om du vill öka åtkomsten till dokument kan du göra något av följande:
   1. Installera SAP Cache Server lokalt, nära den lokala webbläsaren (alternativ i bild nedan)
   2. Konfigurera Azure ExpressRoute, som erbjuder en dedikerad nätverksanslutning med hög hastighet och låg latens mellan lokala datacenter och Azure-datacenter.

![Möjlighet att installera SAP Cache Server lokalt](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Säkerhetskopiering / Återställning
Om du konfigurerar SAP Content Server för att lagra filer i SAP MaxDB-databasen beskrivs redan säkerhetskopierings-/återställningsproceduren och prestandaövervägandena i SAP MaxDB-kapitel i det här dokumentet. 

Om du konfigurerar SAP Content Server för att lagra filer i filsystemet är ett alternativ att köra manuell säkerhetskopiering/återställning av hela filstrukturen där dokumenten finns. I likhet med SAP MaxDB backup /restore, rekommenderas att ha en dedikerad diskvolym för säkerhetskopiering. 

#### <a name="other"></a>Annat
Andra SAP Content Server-specifika inställningar är transparenta för virtuella Azure-datorer och beskrivs i olika dokument och SAP Notes:

* <https://service.sap.com/contentserver> 
* SAP Anmärkning [1619726]  
