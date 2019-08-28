---
title: SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft Docs
description: DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101327"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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



I det här dokumentet omfattar flera olika områden att tänka på när du distribuerar SAP-ASE i Azure IaaS. Som ett villkor för det här dokumentet bör du läsa dokument överväganden [för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md) -arbetsbelastningar och andra guider i SAP-arbetsbelastningen i [Azure-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Information om SAP-ASE i Windows
Från och med Microsoft Azure kan du migrera dina befintliga SAP ASE-program till Azure Virtual Machines. Med SAP ASE på en virtuell Azure-dator kan du minska den totala ägande kostnaden för distribution, hantering och underhåll av företags bredd program genom att enkelt migrera programmen till Microsoft Azure. Med SAP ASE på en virtuell Azure-dator kan administratörer och utvecklare fortfarande använda samma utvecklings-och administrations verktyg som finns lokalt.

Service avtal för Azure Virtual Machines hittar du här:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure erbjuder flera olika typer av virtuella datorer som gör att du kan köra minsta SAP-system och lagra upp till stora SAP-system och landskap med tusentals användare. SAP-storlek SAPS nummer för de olika SAP-certifierade VM SKU: er finns i SAP NOTE [1928533].

Instruktioner och rekommendationer angående användningen av Azure Storage, distribution av virtuella SAP-datorer eller SAP-övervakning som görs i [överväganden för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md) -arbetsbelastningar gäller även för distribution av SAP ASE.

### <a name="sap-ase-version-support"></a>Versions stöd för SAP ASE
SAP stöder för närvarande SAP ASE version 16,0 för användning med SAP Business Suite-produkter. Alla uppdateringar för SAP ASE-servern eller JDBC-och ODBC-drivrutinerna som ska användas med SAP Business Suite-produkter tillhandahålls enbart via SAP Service Marketplace på <https://support.sap.com/swdc>:.

Hämta inte uppdateringar för SAP ASE-servern eller för JDBC-och ODBC-drivrutinerna direkt från Sybase-webbplatser. Detaljerad information om korrigeringar, som stöds för användning med SAP-produkter lokalt och i Azure Virtual Machines finns i följande SAP-anteckningar:

* [1590719]
* [1973241]

Allmän information om att köra SAP Business Suite på SAP ASE finns i [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>ASE konfigurations rikt linjer för SAP-relaterade SAP ASE-installationer i virtuella Azure-datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur för SAP ASE-distributionen
SAP ASE-körbara filer ska finnas eller installeras i system enheten för den virtuella datorns OS-disk (enhet\)c:. De flesta av SAP ASE-system och tools-databaser har vanligt vis inte hög belastning. Därför kan system-och verktygs databaserna (Master, Model, saptools, sybmgmtdb, sybsystemdb) finnas kvar på C:\ kombinationsenhet. 

Ett undantag kan vara den temporära databasen, vilket i händelse av en del SAP ERP och alla BW-arbetsbelastningar kan kräva antingen högre data volym eller I/O-åtgärdernas volym. Volymer eller IOPS som inte kan tillhandahållas av den virtuella datorns OS-disk (\)enhet C:.

Beroende av vilken version av SAPInst/SWPM som används för att installera kan SAP ASE instance-konfigurationen se ut så här:

* En enda SAP ASE tempdb, som skapas när du installerar SAP ASE
* En SAP ASE tempdb skapade genom att installera SAP ASE och ytterligare en saptempdb som skapats av installations rutinen för SAP
* En SAP ASE tempdb skapade genom att installera SAP ASE och en ytterligare tempdb som skapats manuellt (till exempel följande SAP anmärkning [1752266]) för att uppfylla ERP/BW-särskilda tempdb-krav

Av prestanda skäl för vissa ERP-eller BW-arbetsbelastningar kan det vara bra att lagra tempdb-enheterna för den skapade tempdb-enheten på en annan enhet än C:\. Om det inte finns någon ytterligare tempdb rekommenderar vi att du skapar ett (SAP NOTE [1752266]).

För sådana system ska följande steg utföras för den skapade tempdb:

* Flytta den första tempdb-enheten till den första enheten i SAP-databasen
* Lägg till tempdb-enheter till var och en av de virtuella hård diskarna som innehåller en enhet i SAP-databasen

Den här konfigurationen aktiverar tempdb förbrukar mer utrymme än system enheten kan tillhandahålla. Som referens kan du kontrol lera tempdb-enhetens storlekar på befintliga system, som körs lokalt. Eller en sådan konfiguration möjliggör IOPS-nummer mot tempdb, som inte kan tillhandahållas med system enheten. System som körs lokalt kan användas för att övervaka I/O-arbetsbelastningar mot tempdb.

Placera aldrig SAP ASE-enheter på D:\ den virtuella datorns enhet. För SAP-ASE gäller även den här typen av tempdb, även om de objekt som lagras i tempdb endast är temporära.

För data-och transaktions logg fils distributioner har de instruktioner och förslag som gjorts i [överväganden för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md)-arbetsbelastningar. I händelse av Windows-baserade rekommenderar vi att användningen av Windows Storage Spaces används för att bygga stripe-uppsättningar med tillräckligt IOPS, data flöde och volym.  

#### <a name="impact-of-database-compression"></a>Påverkan av databas komprimering
I konfigurationer där I/O-bandbredden kan bli en begränsnings faktor kan varje mått minska IOPS för att öka den arbets belastning som kan köras i ett IaaS-scenario som Azure. Därför rekommenderar vi att du kontrollerar att SAP ASE Compression används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att tillämpa komprimering innan överföring till Azure erhålls av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Komprimerings Körningens varaktighet är kortare än att en kan använda starkare maskin vara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt
* Mindre storlek på databaser kan leda till lägre kostnader för diskallokering

Data-och LOB-komprimering fungerar på en virtuell dator som finns i Azure Virtual Machines eftersom det sker lokalt. Mer information om hur du kontrollerar om komprimering redan används i en befintlig SAP ASE-databas finns i SAP NOTE [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka databas instanser
För SAP-system, som använder SAP ASE som databas plattform, är DBACockpit tillgängligt som inbäddad webbläsare i transaktion DBACockpit eller som WebDynpro. Alla funktioner för att övervaka och administrera databasen är dock bara tillgängliga i WebDynpro-implementeringen av DBACockpit.

Precis som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av WebDynpro-implementeringen av DBACockpit. Följ SAP NOTE [1245200] för att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående anmärkningar konfigurerar du även Internet Communication Manager (ICM) tillsammans med de portar som ska användas för http-och HTTPS-anslutningar. Standardinställningen för http ser ut så här:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

och länkarna som genereras i Transaction DBACockpit ser ut ungefär så här:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/WebDynpro\</SAP/dba_cockpit
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/WebDynpro\</SAP/dba_cockpit
> 
> 

Beroende på hur den virtuella Azure-datorn som är värd för SAP-systemet är ansluten till AD och DNS måste du se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på den dator där du öppnar DBACockpit från. Se SAP NOTE [773830] för att förstå hur ICM fastställer det fullständigt kvalificerade värd namnet baserat på profil parametrar och ange parametern ICM/host_name_full explicit om det behövs.

Om du har distribuerat den virtuella datorn i ett moln scenario utan anslutning mellan olika platser och Azure måste du definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Du hittar mer information om DNS-namnet [här][virtual-machines-azurerm-versus-azuresm].

Ange SAP Profile-parametern ICM/host_name_full till DNS-namnet på den virtuella Azure-datorn som länken kan se ut ungefär så här:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

I detta fall måste du se till att:

* Lägg till regler för inkommande trafik i nätverks säkerhets gruppen i Azure Portal för TCP/IP-portarna som används för att kommunicera med ICM
* Lägg till inkommande regler i konfigurationen för Windows-brandväggen för TCP/IP-portarna som används för att kommunicera med ICM

För en automatiserad import av alla korrigeringar som är tillgängliga rekommenderar vi att du regelbundet använder korrigerings samlingen SAP-notering som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Mer information om ASE-cockpit för SAP finns i följande SAP-anteckningar:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhets kopierings-/återställnings överväganden för SAP ASE
När du distribuerar SAP ASE till Azure måste du granska din säkerhets kopierings metod. Även för icke-produktions system måste SAP-databaserna säkerhets kopie ras regelbundet. Eftersom Azure Storage behåller tre avbildningar kan en säkerhets kopia vara mindre viktig när det gäller att kompensera en lagrings krasch. Den främsta anledningen till att upprätthålla en lämplig säkerhets kopierings-och återställnings plan är mer att du kan kompensera för logiska/manuella fel genom att tillhandahålla återställnings funktioner för tidpunkt. Målet är att antingen använda säkerhets kopieringar för att återställa databasen till en viss tidpunkt eller använda säkerhets kopieringarna i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

Att säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som lokalt. Se SAP-anteckningar:

* [1588316]
* [1585981]

Mer information om hur du skapar dumpfiler och schemalägger säkerhets kopieringar. Beroende på din strategi och behov kan du konfigurera databas-och logg dum par på disk till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhets kopieringen. För att minska risken för data förlust i händelse av ett fel rekommenderar vi att du använder en disk där inga databasfiler finns.

Förutom data-och LOB-komprimering erbjuder SAP ASE även komprimering av säkerhets kopiering. Om du vill använda mindre utrymme med databasen och logg dum par rekommenderar vi att du använder säkerhets kopierings komprimering. Mer information finns i SAP NOTE [1588316]. Komprimering av säkerhets kopian är också viktigt för att minska mängden data som ska överföras om du planerar att hämta säkerhets kopior eller virtuella hård diskar som innehåller säkerhets kopior från den virtuella Azure-datorn till lokala platser.

Använd inte enhets D:\ som databas eller logg dumpnings mål.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestanda överväganden för säkerhets kopieringar/återställningar
Som i distributioner utan operativ system är säkerhets kopiering/återställning av prestanda beroende av hur många volymer som kan läsas parallellt och hur data flödet för dessa volymer kan vara. Tänk på att säkerhets kopierings komprimering förbrukar processor resurser. Den här processor förbrukningen för säkerhets kopierings komprimering kan spela en betydande roll på virtuella datorer med ett litet antal CPU-trådar. Därför kan du anta följande:

* Ju färre diskar som används för att lagra databas enheterna, vilket minskar det totala data flödet vid läsning
* Ju mindre antal processor trådar som finns på den virtuella datorn, desto mer allvarliga påverkar komprimeringen av säkerhets kopieringen
* Ju färre mål (stripe-kataloger, diskar) som säkerhets kopieringen ska skrivas till, ju lägre data flöde

För att öka antalet mål som ska skrivas till finns det två alternativ som kan användas/kombineras beroende på dina behov:

* Skikta säkerhets kopierings mål volymen över flera monterade diskar för att förbättra IOPS-dataflödet på den stripe volymen
* Skapa en dump-konfiguration på SAP ASE-nivå, som använder mer än en mål katalog för att skriva dumpen till

Att Stripa en disk volym över flera monterade diskar har diskuterats i [överväganden för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md)-arbetsbelastningar. Mer information om hur du använder flera kataloger i ASE dump-konfiguration finns i dokumentationen om den lagrade proceduren sp_config_dump, som används för att skapa dump-konfigurationen på Sybase- [Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveri beredskap med virtuella Azure-datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datareplikering med SAP Sybase-Processerver
Med SAP Sybase Replication Server (SRS) tillhandahåller SAP ASE en lösning för varm vänte läge för att överföra databas transaktioner till en avlägsen plats asynkront. 

Installation och drift av SRS fungerar lika bra i en virtuell dator som finns i Azure Virtual Machine-tjänster, eftersom det sker lokalt.

SAP ASE-HADR kräver inte ett internt Azure-Load Balancer och har inga beroenden för kluster på operativ system nivå. Det fungerar på virtuella datorer med Azure Windows och Linux. Mer information om SAP ASE HADR finns i [användar handboken för SAP ASE hadr](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Information om SAP ASE på Linux
Från och med Microsoft Azure kan du enkelt migrera dina befintliga SAP ASE-program till Azure Virtual Machines. Med SAP ASE på en virtuell dator kan du minska den totala ägande kostnaden för distribution, hantering och underhåll av företags bredd program genom att enkelt migrera programmen till Microsoft Azure. Med SAP ASE på en virtuell Azure-dator kan administratörer och utvecklare fortfarande använda samma utvecklings-och administrations verktyg som finns lokalt.

För att distribuera virtuella Azure-datorer är det viktigt att känna till den officiella service avtal som finns här:<https://azure.microsoft.com/support/legal/sla>

Information om SAP-storlek och en lista över SAP-certifierade VM SKU: er finns i SAP NOTE [1928533]. Ytterligare dokument för SAP-storlek för Azure virtuella datorer finns här <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> och här<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruktioner och rekommendationer angående användningen av Azure Storage, distribution av virtuella SAP-datorer eller SAP-övervakning gäller för distributioner av SAP-ASE tillsammans med SAP-program såsom de anges i de fyra första kapitlen i det här dokumentet.

Följande två SAP-anteckningar innehåller allmän information om ASE i Linux och ASE i molnet:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Versions stöd för SAP ASE
SAP stöder för närvarande SAP ASE version 16,0 för användning med SAP Business Suite-produkter. Alla uppdateringar för SAP ASE-servern eller JDBC-och ODBC-drivrutinerna som ska användas med SAP Business Suite-produkter tillhandahålls enbart via SAP Service Marketplace på <https://support.sap.com/swdc>:.

För installationer lokalt ska du inte hämta uppdateringar för SAP ASE-servern eller för JDBC-och ODBC-drivrutinerna direkt från Sybase-webbplatser. Detaljerad information om korrigeringar, som stöds för användning med SAP Business Suite-produkter lokalt och i Azure Virtual Machines finns i följande SAP-anteckningar:

* [1590719]
* [1973241]

Allmän information om att köra SAP Business Suite på SAP ASE finns i [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>ASE konfigurations rikt linjer för SAP-relaterade SAP ASE-installationer i virtuella Azure-datorer
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur för SAP ASE-distributionen
SAP ASE-körbara filer ska finnas eller installeras i rot fil systemet på den virtuella datorn (/Sybase). De flesta av SAP ASE-system och tools-databaser har vanligt vis inte hög belastning. Därför kan system-och verktygs databaser (Master, Model, saptools, sybmgmtdb, sybsystemdb) lagras i rot fil systemet. 

Ett undantag kan vara den temporära databasen, vilket i händelse av en del SAP ERP och alla BW-arbetsbelastningar kan kräva antingen högre data volym eller I/O-åtgärdernas volym. Volymer eller IOPS som inte kan tillhandahållas av den virtuella datorns OS-disk 

Beroende på vilken version av SAPInst/SWPM som används för att installera systemet kan databasen innehålla:

* En enda SAP ASE tempdb som skapats när du installerade SAP ASE
* En SAP ASE tempdb skapade genom att installera SAP ASE och ytterligare en saptempdb som skapats av installations rutinen för SAP
* En SAP ASE tempdb skapade genom att installera SAP ASE och en ytterligare tempdb som skapats manuellt (till exempel följande SAP anmärkning [1752266]) för att uppfylla ERP/BW-särskilda tempdb-krav

Av prestanda skäl för särskilda ERP-eller BW-arbetsbelastningar kan det vara bra att lagra tempdb-enheterna för den skapade tempdb-enheten (med SWPM eller manuellt) i ett separat fil system som kan representeras av en enskild Azure-datadisk eller en Linux RAID-utsträckning m ultiple Azure-datadiskar. Om det inte finns någon ytterligare tempdb rekommenderar vi att du skapar ett (SAP NOTE [1752266]).

Följande steg bör utföras för sådana system för att skapa tempdb:

* Flytta den första tempdb-katalogen till det första fil systemet i SAP-databasen
* Lägg till tempdb-kataloger till var och en av diskarna som innehåller ett fil system med SAP-databasen

Den här konfigurationen aktiverar tempdb förbrukar mer utrymme än system enheten kan tillhandahålla. Som referens kan du kontrol lera tempdb-enhetens storlekar på befintliga system, som körs lokalt. Eller en sådan konfiguration möjliggör IOPS-nummer mot tempdb, som inte kan tillhandahållas med system enheten. System som körs lokalt kan användas för att övervaka I/O-arbetsbelastningar mot tempdb.

Placera aldrig SAP ASE-kataloger på/mnt eller/mnt/Resource för den virtuella datorn. För SAP-ASE gäller även den här typen av tempdb, även om de objekt som lagras i tempdb endast är temporära. Eftersom/mnt eller/mnt/Resource är ett standard utrymme för Azure VM temp, som inte är beständigt. Mer information om Azure VM Temp-utrymmet finns i [den här artikeln][virtual-machines-linux-how-to-attach-disk]

För data-och transaktions logg fils distributioner har de instruktioner och förslag som gjorts i [överväganden för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md)-arbetsbelastningar. I händelse av Linux-baserade distributioner rekommenderas användningen av LVM eller MDADM att använda för att bygga stripe-uppsättningar med tillräckligt IOPS, data flöde och volym. 

#### <a name="impact-of-database-compression"></a>Påverkan av databas komprimering
I konfigurationer där I/O-bandbredden kan bli en begränsnings faktor kan varje mått minska IOPS för att öka den arbets belastning som kan köras i ett IaaS-scenario som Azure. Därför rekommenderar vi att du kontrollerar att SAP ASE Compression används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att tillämpa komprimering innan överföring till Azure erhålls av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Komprimerings Körningens varaktighet är kortare än att en kan använda starkare maskin vara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt
* Mindre storlek på databaser kan leda till lägre kostnader för diskallokering

Data-och LOB-komprimering fungerar på en virtuell dator som finns i Azure Virtual Machines eftersom det sker lokalt. Mer information om hur du kontrollerar om komprimering redan används i en befintlig SAP ASE-databas finns i SAP NOTE [1750510]. Mer information om databas komprimering finns i SAP anmärkning [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka databas instanser
För SAP-system, som använder SAP ASE som databas plattform, är DBACockpit tillgängligt som inbäddad webbläsare i transaktion DBACockpit eller som WebDynpro. Alla funktioner för att övervaka och administrera databasen är dock bara tillgängliga i WebDynpro-implementeringen av DBACockpit.

Precis som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av WebDynpro-implementeringen av DBACockpit. Följ SAP NOTE [1245200] för att aktivera användningen av webdynpros och generera de nödvändiga. När du följer anvisningarna i ovanstående anmärkningar konfigurerar du även Internet Communication Manager (ICM) tillsammans med de portar som ska användas för http-och HTTPS-anslutningar. Standardinställningen för http ser ut så här:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

länkarna som genereras i Transaction DBACockpit ser ut ungefär så här:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/WebDynpro\</SAP/dba_cockpit
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/WebDynpro\</SAP/dba_cockpit
> 
> 

Beroende på hur den virtuella Azure-datorn som är värd för SAP-systemet är ansluten till AD och DNS måste du se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på den dator där du öppnar DBACockpit från. Se SAP NOTE [773830] för att förstå hur ICM fastställer det fullständigt kvalificerade värd namnet beroende på profil parametrar och ange parametern ICM/host_name_full explicit vid behov.

Om du har distribuerat den virtuella datorn i ett moln scenario utan anslutning mellan olika platser och Azure måste du definiera en offentlig IP-adress och en domainlabel. Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Du hittar mer information om DNS-namnet [här][virtual-machines-azurerm-versus-azuresm].

Ange SAP Profile-parametern ICM/host_name_full till DNS-namnet på den virtuella Azure-datorn som länken kan se ut ungefär så här:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

I detta fall måste du se till att:

* Lägg till regler för inkommande trafik i nätverks säkerhets gruppen i Azure Portal för TCP/IP-portarna som används för att kommunicera med ICM
* Lägg till inkommande regler i konfigurationen för Windows-brandväggen för TCP/IP-portarna som används för att kommunicera med ICM

För en automatiserad import av alla korrigeringar som är tillgängliga rekommenderar vi att du regelbundet använder korrigerings samlingen SAP-notering som gäller för din SAP-version:

* [1558958]
* [1619967]
* [1882376]

Mer information om ASE-cockpit för SAP finns i följande SAP-anteckningar:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Säkerhets kopierings-/återställnings överväganden för SAP ASE
När du distribuerar SAP ASE till Azure måste du granska din säkerhets kopierings metod. Även för icke-produktions system måste SAP-databaserna säkerhets kopie ras regelbundet. Eftersom Azure Storage behåller tre avbildningar kan en säkerhets kopia vara mindre viktig när det gäller att kompensera en lagrings krasch. Den främsta anledningen till att upprätthålla en lämplig säkerhets kopierings-och återställnings plan är mer att du kan kompensera för logiska/manuella fel genom att tillhandahålla återställnings funktioner för tidpunkt. Målet är att antingen använda säkerhets kopieringar för att återställa databasen till en viss tidpunkt eller använda säkerhets kopieringarna i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

Att säkerhetskopiera och återställa en databas i Azure fungerar på samma sätt som lokalt. Se SAP-anteckningar:

* [1588316]
* [1585981]

Mer information om hur du skapar dumpfiler och schemalägger säkerhets kopieringar. Beroende på din strategi och dina behov kan du konfigurera databas-och logg dum par på disk till en av de befintliga diskarna eller lägga till ytterligare en disk för säkerhets kopieringen. För att minska risken för data förlust i händelse av ett fel rekommenderar vi att du använder en disk där ingen databas katalog/fil finns.

Förutom data-och LOB-komprimering erbjuder SAP ASE även komprimering av säkerhets kopiering. Om du vill använda mindre utrymme med databasen och logg dum par rekommenderar vi att du använder säkerhets kopierings komprimering. Mer information finns i SAP NOTE [1588316]. Komprimering av säkerhets kopian är också viktigt för att minska mängden data som ska överföras om du planerar att hämta säkerhets kopior eller virtuella hård diskar som innehåller säkerhets kopior från den virtuella Azure-datorn till lokala platser.

Använd inte Azure VM Temp Space/mnt eller/mnt/Resource som databas eller logg dumpnings destination.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestanda överväganden för säkerhets kopieringar/återställningar
Som i distributioner utan operativ system är säkerhets kopiering/återställning av prestanda beroende av hur många volymer som kan läsas parallellt och hur data flödet för dessa volymer kan vara. Tänk på att säkerhets kopierings komprimering förbrukar processor resurser. Den här processor förbrukningen för säkerhets kopierings komprimering kan spela en betydande roll på virtuella datorer med ett litet antal CPU-trådar.  Därför kan du anta följande:

* Ju färre diskar som används för att lagra databas enheterna, vilket minskar det totala data flödet vid läsning
* Ju mindre antal processor trådar som finns på den virtuella datorn, desto mer allvarliga påverkar komprimeringen av säkerhets kopieringen
* Ju färre mål (Linux-programvaru-RAID, diskar) som säkerhets kopieringen ska skrivas till, ju lägre data flöde

För att öka antalet mål som ska skrivas till finns det två alternativ som kan användas/kombineras beroende på dina behov:

* Skikta säkerhets kopierings mål volymen över flera monterade diskar för att förbättra IOPS-dataflödet på den stripe volymen
* Skapa en dump-konfiguration på SAP ASE-nivå, som använder mer än en mål katalog för att skriva dumpen till

Att Stripa en disk volym över flera monterade diskar har diskuterats i [överväganden för Azure Virtual Machines DBMS-distribution för SAP](dbms_guide_general.md)-arbetsbelastningar. Mer information om hur du använder flera kataloger i ASE dump-konfiguration finns i dokumentationen om den lagrade proceduren sp_config_dump, som används för att skapa dump-konfigurationen på Sybase- [Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Haveri beredskap med virtuella Azure-datorer
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datareplikering med SAP Sybase-Processerver
Med SAP Sybase Replication Server (SRS) tillhandahåller SAP ASE en lösning för varm vänte läge för att överföra databas transaktioner till en avlägsen plats asynkront. 

Installation och drift av SRS fungerar lika bra i en virtuell dator som finns i Azure Virtual Machine-tjänster, eftersom det sker lokalt.

ASE HADR via SAP Replication Server stöds. Vi rekommenderar starkt att du använder SAP ASE 16,03 för att försöka med en sådan konfiguration. Mer detaljerade instruktioner om hur du installerar sådana konfigurationer finns i detalj i den här [bloggen](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
