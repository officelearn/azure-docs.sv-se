---
title: Azure Virtual Machines hög tillgänglighet för SAP NetWeaver | Microsoft-dokument
description: Guide för hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa0810818bf7cfea21f925ee639b4b5a50dcb23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246128"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer

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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md
[dbms-guide-2.1]:../../virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:../../virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:../../virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:../../virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:../../virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:../../virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:../../virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:../../virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:../../virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:../../virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:../../virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:../../virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:../../virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:../../virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:../../virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:../../virtual-machines-windows-sap-deployment-guide.md
[deployment-guide-2.2]:../../virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:../../virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:../../virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:../../virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:../../virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:../../virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:../../virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:../../virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:../../virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:../../virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:../../virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:../../virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:../../virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:../../virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:../../virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:../../virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:../../virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:../../virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:../../virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:../../virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:../../virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:../../virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:../../virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:../../virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:../../virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:../../virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:../../virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:../../virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (KONFIGURATION med flera SID med hög tillgänglighet)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Sap-matris för produkttillgänglighet)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Virtuella Azure-datorer är lösningen för organisationer som behöver beräknings-, lagrings- och nätverksresurser, på minimal tid och utan långa anskaffningscykler. Du kan använda virtuella Azure-datorer för att distribuera klassiska program som SAP NetWeaver-baserade ABAP, Java och en ABAP+Java-stack. Utöka tillförlitligheten och tillgängligheten utan ytterligare lokala resurser. Azure Virtual Machines stöder anslutning mellan lokala anslutningar, så att du kan integrera virtuella Azure-datorer i organisationens lokala domäner, privata moln och SAP-systemlandskap.

I den här artikeln täcker vi de åtgärder som du kan vidta för att distribuera SAP-system med hög tillgänglighet i Azure med hjälp av distributionsmodellen för Azure Resource Manager. Vi går igenom dessa viktiga uppgifter:

* Hitta rätt SAP Notes och installationsguider, som visas i avsnittet [Resurser.][sap-ha-guide-2] Den här artikeln kompletterar SAP-installationsdokumentation och SAP Notes, som är de primära resurser som kan hjälpa dig att installera och distribuera SAP-programvara på specifika plattformar.
* Lär dig skillnaderna mellan Distributionsmodellen för Azure Resource Manager och azure classic deployment model.
* Lär dig mer om kvorumlägen för Windows Server Redundanskluster, så att du kan välja den modell som är rätt för din Azure-distribution.
* Lär dig mer om windows server redundanskluster kluster delad lagring i Azure-tjänster.
* Lär dig hur du skyddar komponenter med en punkt i felet, till exempel ADVANCED Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCMS) och DBMS (Database Management Systems) och redundanta komponenter som SAP Application Server i Azure.
* Följ ett steg-för-steg-exempel på en installation och konfiguration av ett SAP-system med hög tillgänglighet i ett Klusterkluster för Windows Server-redundanskluster i Azure med hjälp av Azure Resource Manager.
* Lär dig mer om ytterligare steg som krävs för att använda Windows Server Redundanskluster i Azure, men som inte behövs i en lokal distribution.

För att förenkla distribution och konfiguration använder vi i den här artikeln sap-mallarna för resurshanteraren med tre nivåer med hög tillgänglighet. Mallarna automatiserar distributionen av hela infrastrukturen som du behöver för ett SAP-system med hög tillgänglighet. Infrastrukturen stöder också SAP Application Performance Standard (SAPS) dimensionering av ditt SAP-system.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Förutsättningar
Innan du börjar kontrollerar du att du uppfyller de förutsättningar som beskrivs i följande avsnitt. Kontrollera också alla resurser som anges i avsnittet [Resurser.][sap-ha-guide-2]

I den här artikeln använder vi Azure Resource Manager-mallar för [SAP NetWeaver på tre nivåer](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). En användbar översikt över mallar finns i [SAP Azure Resource Manager-mallar](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Resurser
De här artiklarna omfattar SAP-distributioner i Azure:

* [Planering och implementering av virtuella Azure-datorer för SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver (den här guiden)][sap-ha-guide]

> [!NOTE]
> När det är möjligt ger vi dig en länk till den refererande SAP installationsguiden (se [SAP installationsguider).][sap-installation-guides] För förutsättningar och information om installationsprocessen är det en bra idé att läsa SAP NetWeaver installationsguider noggrant. Den här artikeln innehåller endast specifika uppgifter för SAP NetWeaver-baserade system som du kan använda med virtuella Azure-datorer.
>
>

Dessa SAP-anteckningar är relaterade till ämnet SAP i Azure:

| Antecknar numrerar | Titel |
| --- | --- |
| [1928533] |SAP-program på Azure: Produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Förbättrad Azure-övervakning för SAP |
| [2178632] |Viktiga övervakningsmått för SAP på Microsoft Azure |
| [1999351] |Virtualisering i Windows: Förbättrad övervakning |
| [2243692] |Användning av Azure Premium SSD-lagring för SAP DBMS-instans |

Läs mer om begränsningarna för [Azure-prenumerationer][azure-resource-manager/management/azure-subscription-service-limits-subscription], inklusive allmänna standardbegränsningar och maximala begränsningar.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP med hög tillgänglighet med Azure Resource Manager jämfört med den klassiska Azure-distributionsmodellen
De klassiska distributionsmodellerna för Azure Resource Manager och Azure skiljer sig från följande områden:

- Resursgrupper
- Azure internt belastningsutjämnare beroende av Azure-resursgruppen
- Stöd för SAP multi-SID-scenarier

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Resursgrupper
I Azure Resource Manager kan du använda resursgrupper för att hantera alla programresurser i din Azure-prenumeration. En integrerad metod, i en resursgrupp, alla resurser har samma livscykel. Till exempel skapas alla resurser samtidigt och de tas bort samtidigt. Läs mer om [resursgrupper](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Azure internt belastningsutjämnare beroende av Azure-resursgruppen

I azure classic deployment-modellen finns det ett beroende mellan Azures interna belastningsutjämnare (Azure Load Balancer-tjänst) och molntjänstgruppen. Varje intern belastningsutjämnare behöver en molntjänstgrupp.

I Azure Resource Manager behöver du inte en Azure-resursgrupp för att använda Azure Load Balancer. Miljön är enklare och mer flexibel.

### <a name="support-for-sap-multi-sid-scenarios"></a>Stöd för SAP multi-SID-scenarier

I Azure Resource Manager kan du installera flera SAP-systemidentifierare (SID) ASCS/SCS-instanser i ett kluster. Multi-SID-instanser är möjliga på grund av stöd för flera IP-adresser för varje Azure intern belastningsutjämnare.

Om du vill använda azure classic deployment model följer du procedurerna som beskrivs i [SAP NetWeaver i Azure: Clustering SAP ASCS/SCS-instanser med hjälp av Windows Server Failover Clustering i Azure med SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Distributionsmodellen för Azure Resource Manager för dina SAP-installationer. Det erbjuder många fördelar som inte är tillgängliga i den klassiska distributionsmodellen. Läs mer om [Azure-distributionsmodeller][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server Redundanskluster
Windows Server Redundanskluster är grunden för en SAP ASCS/SCS-installation med hög tillgänglighet och DBMS i Windows.

Ett redundanskluster är en grupp på 1+n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om ett nodfel inträffar beräknar Windows Server Redundanskluster antalet fel som kan uppstå samtidigt som ett felfritt kluster underhålls för att tillhandahålla program och tjänster. Du kan välja mellan olika kvorumlägen för att uppnå redundanskluster.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Kvorumlägen
Du kan välja mellan fyra kvorumlägen när du använder Windows Server Redundanskluster:

* **Nod majoritet**. Varje nod i klustret kan rösta. Klustret fungerar endast med en majoritet av rösterna, det vill än mer än hälften av rösterna. Vi rekommenderar det här alternativet för kluster som har ett ojämnt antal noder. Till exempel kan tre noder i ett kluster med sju noder misslyckas och klustret stillbilder uppnår en majoritet och fortsätter att köras.  
* **Nod och diskmajoritet**. Varje nod och en utsedd disk (ett diskvittne) i klusterlagringen kan rösta när de är tillgängliga och i kommunikation. Klustret fungerar endast med en majoritet av rösterna, det vill än mer än hälften av rösterna. Det här läget är meningsfullt i en klustermiljö med ett jämnt antal noder. Om hälften av noderna och disken är online förblir klustret i felfritt tillstånd.
* **Nod- och filresursmajoritet**. Varje nod plus en angiven filresurs (ett filresursvittne) som administratören skapar kan rösta, oavsett om noderna och filresursen är tillgängliga och i kommunikationen. Klustret fungerar endast med en majoritet av rösterna, det vill än mer än hälften av rösterna. Det här läget är meningsfullt i en klustermiljö med ett jämnt antal noder. Det liknar noden och diskmajoritetsläget, men den använder en vittnesfilresurs i stället för en vittnesdisk. Det här läget är enkelt att implementera, men om själva filresursen inte är mycket tillgänglig kan det bli en enda felpunkt.
* **Ingen majoritet: Endast disk**. Klustret har kvorum om en nod är tillgänglig och i kommunikation med en specifik disk i klusterlagringen. Endast noderna som också kommunicerar med disken kan ansluta till klustret. Vi rekommenderar att du inte använder det här läget.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Windows Server Redundanskluster lokalt
Bild 1 visar ett kluster med två noder. Om nätverksanslutningen mellan noderna misslyckas och båda noderna förblir igång avgör en kvorumdisk eller filresurs vilken nod som ska fortsätta att tillhandahålla klustrets program och tjänster. Noden som har åtkomst till kvorumdisken eller filresursen är den nod som säkerställer att tjänsterna fortsätter.

Eftersom det här exemplet använder ett kluster med två noder använder vi kvorumläget Node och Filresursmajoritet. Noden och diskmajoriteten är också ett giltigt alternativ. I en produktionsmiljö rekommenderar vi att du använder en kvorumdiskett. Du kan använda nätverk och lagringssystem teknik för att göra den mycket tillgänglig.

![Bild 1: Exempel på en Windows Server Redundansklusterkonfiguration för SAP ASCS/SCS i Azure][sap-ha-guide-figure-1000]

_**Figur 1:** Exempel på en Windows Server Redundansklusterkonfiguration för SAP ASCS/SCS i Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Delad lagring
Bild 1 visar också ett delat lagringskluster med två noder. I ett lokalt delat lagringskluster identifierar alla noder i klustret delad lagring. En låsmekanism skyddar data från skador. Alla noder kan identifiera om en annan nod misslyckas. Om en nod misslyckas tar den återstående noden över ägandet av lagringsresurserna och säkerställer tillgängligheten för tjänster.

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-program, till exempel med SQL Server. SQL Server På replikerar alltid DBMS-data och loggfiler från den lokala disken i en klusternod till den lokala disken i en annan klusternod. I så fall behöver Windows-klusterkonfigurationen ingen delad disk.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Nätverks- och namnmatchning
Klientdatorer når klustret via en virtuell IP-adress och ett virtuellt värdnamn som DNS-servern tillhandahåller. De lokala noderna och DNS-servern kan hantera flera IP-adresser.

I en vanlig installation använder du två eller flera nätverksanslutningar:

* En särskild anslutning till lagringen
* En klusterintern nätverksanslutning för pulsslag
* Ett offentligt nätverk som klienter använder för att ansluta till klustret

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Windows Server Redundanskluster i Azure
Jämfört med bare metal- eller privata molndistributioner kräver Virtuella Azure-datorer ytterligare steg för att konfigurera Windows Server Redundanskluster. När du skapar en delad klusterdisk måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

I den här artikeln diskuterar vi viktiga begrepp och ytterligare steg som krävs för att skapa ett centraltjänstkluster med hög tillgänglighet i Azure. Vi visar dig hur du konfigurerar verktyget SIOS DataKeeper från tredje part och hur du konfigurerar Azures interna belastningsutjämnare. Du kan använda dessa verktyg för att skapa ett Windows redundanskluster med ett filresursvittne i Azure.

![Bild 2: Windows Server Redundansklusterkonfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Figur 2:** Windows Server Redundansklusterkonfiguration i Azure utan en delad disk_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Delad disk i Azure med SIOS DataKeeper
Du behöver klusterdelad lagring för en SAP ASCS/SCS-instans med hög tillgänglighet. Från och med september 2016 erbjuder Azure inte delad lagring som du kan använda för att skapa ett delat lagringskluster. Du kan använda programvara från tredje part SIOS DataKeeper Cluster Edition för att skapa en speglad lagring som simulerar klusterdelad lagring. SIOS-lösningen tillhandahåller synkron datareplikering i realtid. Så här kan du skapa en delad diskresurs för ett kluster:

1. Bifoga ytterligare en virtuell Azure-hårddisk (VHD) till var och en av de virtuella datorerna (VMs) i en Windows-klusterkonfiguration.
2. Kör SIOS DataKeeper Cluster Edition på båda noderna för virtuella datorer.
3. Konfigurera SIOS DataKeeper Cluster Edition så att den speglar innehållet i den ytterligare VHD-anslutna volymen från den virtuella källdatorn till den ytterligare VHD-anslutna volymen för målvirvatorn. SIOS DataKeeper abstraherar källan och riktar in sig på lokala volymer och presenterar dem sedan för Windows Server Redundanskluster som en delad disk.

Få mer information om [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Bild 3: Windows Server Redundansklusterkonfiguration i Azure med SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figur 3:** Windows Server Redundansklusterkonfiguration i Azure med SIOS DataKeeper_

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-produkter, till exempel SQL Server. SQL Server På replikerar alltid DBMS-data och loggfiler från den lokala disken i en klusternod till den lokala disken i en annan klusternod. I det här fallet behöver Windows-klusterkonfigurationen inte en delad disk.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Namnmatchning i Azure
Azure-molnplattformen erbjuder inte möjlighet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser. Du behöver en alternativ lösning för att konfigurera en virtuell IP-adress för att nå klusterresursen i molnet.
Azure har en intern belastningsutjämnare i Azure Load Balancer-tjänsten. Med den interna belastningsutjämnaren når klienter klustret via klustrets virtuella IP-adress.
Du måste distribuera den interna belastningsutjämnaren i resursgruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga regler för vidarebefordran av portar med avsökningsportarna för den interna belastningsutjämnaren.
Klienterna kan ansluta via det virtuella värdnamnet. DNS-servern matchar klustrets IP-adress och den interna belastningsutjämnarhanteraren hanterar port vidarebefordran till den aktiva noden i klustret.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver hög tillgänglighet i Azure Infrastructure-as-a-Service (IaaS)
För att uppnå SAP-program med hög tillgänglighet, till exempel för SAP-programvarukomponenter, måste du skydda följande komponenter:

* SAP-programserverinstans
* SAP ASCS/SCS-instans
* DBMS-server

Mer information om hur du skyddar SAP-komponenter i scenarier med hög tillgänglighet finns i [Planering och implementering av Virtuella Azure-datorer för SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>SAP-programserver med hög tillgänglighet
Du behöver vanligtvis inte en specifik lösning med hög tillgänglighet för SAP Application Server och dialoginstanser. Du uppnår hög tillgänglighet genom redundans och konfigurerar flera dialoginstanser i olika instanser av Virtuella Azure-datorer. Du bör ha minst två SAP-programinstanser installerade i två instanser av Virtuella Azure-datorer.

![Bild 4: SAP-programserver med hög tillgänglighet][sap-ha-guide-figure-2000]

_**Figur 4:** SAP-programserver med hög tillgänglighet_

Du måste placera alla virtuella datorer som är värdar för SAP Application Server-instanser i samma Azure-tillgänglighetsuppsättning. En Azure-tillgänglighetsuppsättning säkerställer att:

* Alla virtuella datorer ingår i samma uppgraderingsdomän. En uppgraderingsdomän, till exempel, ser till att de virtuella datorerna inte uppdateras samtidigt under planerade underhållsavstativ tid.
* Alla virtuella datorer är en del av samma feldomän. En feldomän, till exempel, ser till att virtuella datorer distribueras så att ingen enskild felpunkt påverkar tillgängligheten för alla virtuella datorer.

Läs mer om hur du [hanterar tillgängligheten för virtuella datorer][virtual-machines-manage-availability].

Eftersom Azure-lagringskontot är en potentiell enskild felpunkt är det viktigt att ha minst två Azure-lagringskonton, där minst två virtuella datorer distribueras. I en idealisk installation distribueras diskarna för varje virtuell dator som kör en SAP-dialoginstans i ett annat lagringskonto.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>SAP ASCS/SCS-instans med hög tillgänglighet
Figur 5 är ett exempel på en SAP ASCS/SCS-instans med hög tillgänglighet.

![Bild 5: SAP ASCS/SCS-instans med hög tillgänglighet][sap-ha-guide-figure-2001]

_**Figur 5:** SAP ASCS/SCS-instans med hög tillgänglighet_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>SAP ASCS/SCS-instans hög tillgänglighet med Windows Server Redundanskluster i Azure
Jämfört med bare metal- eller privata molndistributioner kräver Virtuella Azure-datorer ytterligare steg för att konfigurera Windows Server Redundanskluster. Om du vill skapa ett Windows-redundanskluster behöver du en delad klusterdisk, flera IP-adresser, flera virtuella värdnamn och en intern Azure-belastningsutjämnare för klustring av en SAP ASCS/SCS-instans. Vi diskuterar detta mer i detalj senare i artikeln.

![Bild 6: Windows Server Redundanskluster för en SAP ASCS/SCS-konfiguration i Azure med hjälp av SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figur 6:** Windows Server Redundanskluster för en SAP ASCS/SCS-konfiguration i Azure med SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>DBMS-instans med hög tillgänglighet
DBMS är också en enda kontaktpunkt i ett SAP-system. Du måste skydda den med hjälp av en lösning med hög tillgänglighet. Bild 7 visar en SQL Server Always On high-availability-lösning i Azure, med Windows Server Redundanskluster och Azures interna belastningsutjämnare. SQL Server Replikerar alltid DBMS-data och loggfiler med hjälp av sin egen DBMS-replikering. I det här fallet behöver du inte klusterdelade diskar, vilket förenklar hela installationen.

![Bild 7: Exempel på en SAP DBMS med hög tillgänglighet, med SQL Server Always On][sap-ha-guide-figure-2003]

_**Figur 7:** Exempel på en SAP DBMS med hög tillgänglighet, med SQL Server Always On_

Mer information om klustring av SQL Server i Azure med hjälp av distributionsmodellen för Azure Resource Manager finns i följande artiklar:

* [Konfigurera gruppen Alltid på tillgänglighet i Virtuella Azure-datorer manuellt med hjälp av Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurera en intern azure-belastningsutjämningsfaktor för en alltid på tillgänglighetsgrupp i Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Distributionsscenarier med hög tillgänglighet

### <a name="deployment-scenario-using-architectural-template-1"></a>Distributionsscenario med arkitekturmall 1

Bild 8 visar ett exempel på en SAP NetWeaver-arkitektur med hög tillgänglighet i Azure för **ett** SAP-system. Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för SAP ASCS/SCS-instansen.
- Ett dedikerat kluster används för DBMS-instansen.
- SAP Application Server-instanser distribueras i sina egna dedikerade virtuella datorer.

![Bild 8: SAP arkitektonisk mall 1 med hög tillgänglighet, med dedikerat kluster för ASCS/SCS och DBMS][sap-ha-guide-figure-2004]

_**Figur 8:** SAP hög tillgänglighet Arkitektonisk mall 1, dedikerade kluster för ASCS / SCS och DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Distributionsscenario med arkitekturmall 2

Bild 9 visar ett exempel på en SAP NetWeaver-arkitektur med hög tillgänglighet i Azure för **ett** SAP-system. Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS-instansen och DBMS.
- SAP Application Server-instanser distribueras i egna dedikerade virtuella datorer.

![Bild 9: SAP arkitektonisk mall 2 med hög tillgänglighet, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS][sap-ha-guide-figure-2005]

_**Figur 9:** SAP arkitekturmall 2 med hög tillgänglighet, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Distributionsscenario med arkitekturmall 3

Bild 10 visar ett exempel på en SAP NetWeaver-arkitektur med hög&gt; tillgänglighet &lt;i&gt;Azure för **två** SAP-system, med &lt;SID1 och SID2 . Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS SID1-instansen *och* SAP ASCS/SCS SID2-instansen (ett kluster).
- Ett dedikerat kluster används för DBMS SID1 och ett annat dedikerat kluster används för DBMS SID2 (två kluster).
- SAP Application Server-instanser för SAP-systemet SID1 har sina egna dedikerade virtuella datorer.
- SAP Application Server-instanser för SAP-systemet SID2 har sina egna dedikerade virtuella datorer.

![Bild 10: SAP arkitektonisk mall med hög tillgänglighet 3, med ett dedikerat kluster för olika ASCS/SCS-instanser][sap-ha-guide-figure-6003]

_**Figur 10:** SAP hög tillgänglighet Arkitektonisk mall 3, med ett dedikerat kluster för olika ASCS / SCS instanser_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Förbereda infrastrukturen

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbereda infrastrukturen för arkitektonisk mall 1
Azure Resource Manager-mallar för SAP hjälper till att förenkla distributionen av nödvändiga resurser.

Mallarna på tre nivåer i Azure Resource Manager stöder också scenarier med hög tillgänglighet, till exempel i arkitekturmall 1, som har två kluster. Varje kluster är en SAP enda felpunkt för SAP ASCS/SCS och DBMS.

Här kan du hämta Azure Resource Manager-mallar för det exempelscenario som vi beskriver i den här artikeln:

* [Avbildning av Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Så här förbereder du infrastrukturen för arkitektonisk mall 1:

- I Azure-portalen, på **bladet Parametrar,** i rutan **SYSTEMAVAILABILITY** väljer du **HA**.

  ![Bild 11: Ange Azure Resource Manager-parametrar med hög tillgänglighet][sap-ha-guide-figure-3000]

_**Figur 11:** Ange Azure Resource Manager-parametrar med hög tillgänglighet_


  Mallarna skapar:

  * **Virtuella datorer:**
    * Virtuella datorer för SAP-programserver: <*SAPSystemSID*>-di-<-nummer *Number*>
    * Virtuella datorer i ASCS/SCS-kluster: <SAPSystemSID->-<-nummer *Number* *SAPSystemSID*>
    * DBMS-kluster: <*SAPSystemSID*>-db-<-nummer *Number*>

  * **Nätverkskort för alla virtuella datorer, med tillhörande IP-adresser:**
    * <*SAPSystemSID*>-nic-di-<*nummer*>
    * <*SAPSystemSID*>-nic-ascs-<*nummer*>
    * <*SAPSystemSID*>-nic-db-<*tal*>

  * **Azure-lagringskonton**

  * **Tillgänglighetsgrupper** för:
    * Virtuella sap-programserverdatorer: <*SAPSystemSID*>-avset-di
    * Virtuella SAP ASCS/SCS-klusterdatorer: <SAPSystemSID->-avset-ascs *SAPSystemSID*
    * Virtuella datorer i DBMS-kluster: <*SAPSystemSID*>-avset-db

  * **Azure intern belastningsutjämnare:**
    * Med alla portar för ASCS/SCS-instansen och IP-adressen <*SAPSystemSID*>-lb-ascs
    * Med alla portar för SQL Server DBMS och IP-adress <*SAPSystemSID*>-lb-db

  * **Nätverkssäkerhetsgrupp:**<*SAPSystemSID*>-nsg-ascs-0  
    * Med en port för fjärrskrivbordsprotokoll (RDP) till <SAPSystemSID->-ascs-0 virtuell dator *SAPSystemSID*

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare är **dynamiska** som standard. Ändra dem till **statiska** IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Distribuera virtuella datorer med företagsnätverksanslutning (korslokalisering) som ska användas i produktion
För sap-system för produktion distribuerar du virtuella Azure-datorer med företagsnätverksanslutning med hjälp av Azure Site-to-Site VPN eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda din Azure Virtual Network-instans. Det virtuella nätverket och undernätet har redan skapats och förberetts.
>
>

1. Välj **befintliga**i rutan **NEWOREXISTINGSUBNET** i azure-portalen på bladet **Parametrar.**
2. Lägg till hela strängen i ditt förberedda Azure-nätverksundernätid i rutan **SUBNETID** där du planerar att distribuera dina virtuella Azure-datorer.
3. Om du vill hämta en lista över alla Undernät i Azure-nätverket kör du det här PowerShell-kommandot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Id-fältet** visar **SUBNETID**.
4. Om du vill hämta en lista över alla **UNDERTID-värden** kör du det här PowerShell-kommandot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** ser ut så här:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Distribuera SAP-instanser endast för molnet för test och demo
Du kan distribuera ditt SAP-system med hög tillgänglighet i en distributionsmodell för endast molnet. Den här typen av distribution är främst användbar för demo- och testanvändningsfall. Det är inte lämpligt för produktionsanvändningsfall.

- I azure-portalen, på **bladet Parametrar,** i rutan **NEWOREXISTINGSUBNET** väljer du **nya**. Lämna **SUBNETID-fältet** tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt det virtuella Azure-nätverket och undernätet.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbereda infrastrukturen för arkitektonisk mall 2

Du kan använda den här Azure Resource Manager-mallen för SAP för att förenkla distributionen av nödvändiga infrastrukturresurser för SAP Architectural Template 2.

Här kan du hämta Azure Resource Manager-mallar för det här distributionsscenariot:

* [Avbildning av Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Anpassad bild](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbereda infrastrukturen för arkitektonisk mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för **flera SID**. Du kan till exempel lägga till ytterligare en SAP ASCS/SCS-instans i en *befintlig* klusterkonfiguration. Mer information finns i [Konfigurera ytterligare en SAP ASCS/SCS-instans i en befintlig klusterkonfiguration för att skapa en SAP multi-SID-konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster kan du använda [snabbstartsmallarna för](https://github.com/Azure/azure-quickstart-templates)flera SID på GitHub .
Om du vill skapa ett nytt multi-SID-kluster måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Databasmall](#database-template)
* [Mall för programservrar](#application-servers-template)

Följande avsnitt innehåller mer information om mallarna och de parametrar som du behöver ange i mallarna.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett Windows Server-redundanskluster som är värd för flera ASCS/SCS-instanser.

Om du vill konfigurera ASCS/SCS multi-SID-mallen anger du värden för följande parametrar i [ASCS/SCS multi-SID-mallen:][sap-templates-3-tier-multisid-xscs-marketplace-image]

  - **Resursprefix**.  Ange resursprefixet, som används för att prefixa alla resurser som skapas under distributionen. Eftersom resurserna inte bara tillhör ett SAP-system är resursprefixet inte SID för ett SAP-system.  Prefixet måste vara mellan **tre och sex tecken**.
  - **Stack typ**. Välj stacktyp för SAP-systemet. Beroende på stacktypen har Azure Load Balancer en (endast ABAP eller Java) eller två (ABAP+Java) privata IP-adresser per SAP-system.
  -  **OS-typ**. Välj operativsystemet för de virtuella datorerna.
  -  **ANTAL SAP-system**. Välj antalet SAP-system som du vill installera i det här klustret.
  -  **Systemtillgänglighet**. Välj **HA**.
  -  **Administratörsanvändarnamn och administratörslösenord**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Nytt eller befintligt undernät**. Ange om ett nytt virtuellt nätverk och ett nytt undernät ska skapas eller om ett befintligt undernät ska användas. Om du redan har ett virtuellt nätverk som är anslutet till det lokala nätverket väljer du **befintlig**.
  -  **Undernäts-ID**. Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät som definierats ska den virtuella datorn tilldelas, namnge ID:t för det specifika undernätet. ID:t ser vanligtvis ut så här: /subscriptions/<*prenumerations-ID*>/resourceGroups/<*resursgruppsnamn*>/providers/Microsoft.Network/virtualNetworks/<*virtuellt nätverksnamn*>/undernät/<*undernätnamn*>

Mallen distribuerar en Azure Load Balancer-instans, som stöder flera SAP-system.

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS Enqueue Replication Server (endast Linux) instanser är konfigurerade till exempel nummer 02, 12, 22...
- Scs ERS-instanserna (endast Linux) är konfigurerade till exempel nummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 (2 för Linux) VIP(s), 1x VIP för ASCS/SCS och 1x VIP för ERS (endast Linux).

Följande lista innehåller alla belastningsutjämningsregler (där x är sap-systemets nummer, till exempel 1, 2, 3...):
- Windows-specifika portar för alla SAP-system: 445, 5985
- ASCS-portar (instansnummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instansnummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-portar på Linux (instansnummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portar på Linux (instansnummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad för att använda följande avsökningsportar (där x är sap-systemets nummer, till exempel 1, 2, 3...):
- ASCS/SCS interna lastutjämningsprodlarport: 620x0
- ERS interna lastutjämnare sond port (Endast Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Databasmall

Databasmallen distribuerar en eller två virtuella datorer som du kan använda för att installera relationsdatabashanteringssystemet (RDBMS) för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill konfigurera databasens multi-SID-mall anger du värden för följande parametrar i [databasens multi-SID-mall:][sap-templates-3-tier-multisid-db-marketplace-image]

- **Sap-system-ID**. Ange SAP-system-ID för det SAP-system som du vill installera. ID:et används som prefix för de resurser som distribueras.
- **Os-typ**. Välj operativsystemet för de virtuella datorerna.
- **Dbtype**. Välj vilken typ av databas du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på de virtuella datorerna. Se till att välja rätt operativsystemtyp: välj **Windows** för SQL och välj en Linux-distribution för HANA. Den Azure Load Balancer som är ansluten till de virtuella datorerna konfigureras för att stödja den valda databastypen:
  * **SQL**. Belastningsutjämnaren lastsaldoport 1433. Se till att använda den här porten för din SQL Server Always On setup.
  * **HANA.** Belastningsutjämnaren lastsaldoportar 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
  Belastningsutjämnaren använder sondport 62550.
- **Sap-systemstorlek**. Ange antalet SAPS som det nya systemet ska tillhandahålla. Om du är osäker på hur många SAPS systemet kommer att kräva, fråga din SAP Technology Partner eller System Integrator.
- **Systemtillgänglighet**. Välj **HA**.
- **Administratörsanvändarnamn och administratörslösenord**. Skapa en ny användare som kan användas för att logga in på datorn.
- **Undernäts-ID**. Ange ID:t för det undernät som du använde under distributionen av ASCS/SCS-mallen eller ID:t för undernätet som skapades som en del av ASCS/SCS-malldistributionen.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Mall för programservrar

Programservermallen distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill konfigurera multi-SID-mallen för programservrar anger du värden för följande parametrar i [programservrarnas multi-SID-mall:][sap-templates-3-tier-multisid-apps-marketplace-image]

  -  **Sap-system-ID**. Ange SAP-system-ID för det SAP-system som du vill installera. ID:et används som prefix för de resurser som distribueras.
  -  **Os-typ**. Välj operativsystemet för de virtuella datorerna.
  -  **Sap-systemstorlek**. Antalet SAPS det nya systemet kommer att ge. Om du är osäker på hur många SAPS systemet kommer att kräva, fråga din SAP Technology Partner eller System Integrator.
  -  **Systemtillgänglighet**. Välj **HA**.
  -  **Administratörsanvändarnamn och administratörslösenord**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernäts-ID**. Ange ID:t för det undernät som du använde under distributionen av ASCS/SCS-mallen eller ID:t för undernätet som skapades som en del av ASCS/SCS-malldistributionen.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuella Azure-nätverk
I vårt exempel är adressutrymmet för det virtuella Azure-nätverket 10.0.0.0/16. Det finns ett undernät som heter **Undernät**, med ett adressintervall på 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar i nätverksinställningarna i gästoperativsystemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera alla nätverksinställningar i Azure. DHCP-tjänsten (Dynamic Host Configuration Protocol) sprider dina inställningar.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-adresser

Så här anger du de DNS-IP-adresser som krävs och gör följande.

1. I Azure-portalen, på **DNS-servrarna** blad, se till att ditt virtuella nätverk **DNS-servrar** alternativet är inställt på **Anpassad DNS**.
2. Välj dina inställningar baserat på vilken typ av nätverk du har. Mer information finns i följande resurser:
   * Lägg till IP-adresserna för de lokala DNS-servrarna.  
   Du kan utöka lokala DNS-servrar till de virtuella datorer som körs i Azure. I det scenariot kan du lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten på.
   * För distributioner isolerade i Azure: Distribuera ytterligare en virtuell dator i samma instans för virtuellt nätverk som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har konfigurerat för att köra DNS-tjänsten.

   ![Bild 12: Konfigurera DNS-servrar för Virtuella Azure-nätverk][sap-ha-guide-figure-3001]

   _**Figur 12:** Konfigurera DNS-servrar för Azure Virtual Network_

   > [!NOTE]
   > Om du ändrar DNS-servrarnas IP-adresser måste du starta om virtuella Azure-datorer för att tillämpa ändringen och sprida de nya DNS-servrarna.
   >
   >

I vårt exempel installeras och konfigureras DNS-tjänsten på dessa virtuella Windows-datorer:

| Rollen för den virtuella datorn | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-servern |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Värdnamn och statiska IP-adresser för klustrade SAP ASCS/SCS-instans och DBMS-klustrade instans

För lokal distribution behöver du dessa reserverade värdnamn och IP-adresser:

| Roll för namn på virtuell värd | Namn på virtuellt värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första virtuella värdnamn för kluster (för klusterhantering) |pr1-ascs-vir |10.0.0.42 |
| Virtuellt värdnamn för SAP ASCS/SCS-instans |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (klusterhantering) |pr1-dbms-vir |10.0.0.32 |

När du skapar klustret skapar du de virtuella värdnamnen **pr1-ascs-vir** och **pr1-dbms-vir** och tillhörande IP-adresser som hanterar själva klustret. Information om hur du gör detta finns i [Samla in klusternoder i en klusterkonfiguration][sap-ha-guide-8.12.1].

Du kan manuellt skapa de andra två virtuella värdnamnen, **pr1-ascs-sap** och **pr1-dbms-sap**och tillhörande IP-adresser på DNS-servern. Den klustrade SAP ASCS/SCS-instansen och den klustrade DBMS-instansen använder dessa resurser. Information om hur du gör detta finns i [Skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ange statiska IP-adresser för virtuella SAP-datorer
När du har distribuerat de virtuella datorer som ska användas i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1. Välj**IP-adress**för >  > **nätverkskortsinställningar för** >  **resursgrupp****i**Azure-portalen .
2. Välj **Statisk**under **Tilldelning**på **bladet IP-adresser** . Ange den IP-adress som du vill använda i rutan **IP-adress.**

   > [!NOTE]
   > Om du ändrar IP-adressen för nätverkskortet måste du starta om virtuella Azure-datorer för att tillämpa ändringen.  
   >
   >

   ![Bild 13: Ange statiska IP-adresser för nätverkskortet för varje virtuell dator][sap-ha-guide-figure-3002]

   _**Figur 13:** Ange statiska IP-adresser för nätverkskortet för varje virtuell dator_

   Upprepa det här steget för alla nätverksgränssnitt, det vill säga för alla virtuella datorer, inklusive virtuella datorer som du vill använda för Active Directory/DNS-tjänsten.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen för den virtuella datorn | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP-programserverinstans |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Andra SAP-programserverinstans |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP-programserverinstans |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Andra klusternod för ASCS/SCS-instans |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Den andra klusternoden för DBMS-instans |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ange en statisk IP-adress för Den interna lastbalanseraren i Azure

SAP Azure Resource Manager-mallen skapar en intern Azure-belastningsutjämningsfaktor som används för SAP ASCS/SCS-instansklustret och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för SAP ASCS/SCS interna belastningsutjämnaren: **pr1-lb-ascs**.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för DBMS interna belastningsutjämnare: **pr1-lb-dbms**.
>
>

Så här anger du en statisk IP-adress för den interna belastningsutjämnren i Azure:

1. Den första distributionen anger ip-adressen för intern belastningsutjämnare till **Dynamisk**. I Azure-portalen, på **IP-adressbladet,** under **Tilldelning**väljer du **Statisk**.
2. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS-instansen.
3. Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instansen.

   ![Bild 14: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen][sap-ha-guide-figure-3003]

   _**Figur 14:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen_

I vårt exempel har vi två interna Azure-belastningsutjämnare som har dessa statiska IP-adresser:

| Azure-rollen intern belastningsutjämning | Namn på Azure-internalsbalans | Statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS-instans intern belastningsutjämnare |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar du behöver:
* En ABAP ASCS-instans med standardinstansnummer **00**
* En Java SCS-instans med standardinstansnummer **01**

När du installerar SAP ASCS/SCS-instansen måste du använda standardinstansnummer **00** för DIN ABAP ASCS-instans och standardinstansnummer **01** för Java SCS-instansen.

Skapa sedan obligatoriska interna belastningsutjämningsslutpunkter för SAP NetWeaver-portarna.

Skapa först dessa belastningsutjämningsslutpunkter för SAP NetWeaver ABAP ASCS-portarna för att skapa obligatoriska interna belastningsutjämningsslutpunkter:

| Regelnamn för tjänst-/belastningsutjämning | Standardportnummer | Betongportar för (ASCS-instans med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32<*instansnummer*> |3200 |
| ABAP-meddelandeserver / *lbrule3600* |36<*instansnummer*> |3600 |
| Internt ABAP-meddelande / *lbrule3900* |39<*instansnummer*> |3900 |
| Meddelandeserver HTTP / *Lbrule8100* |81<*instansnummer*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5<*InstansNummer*>13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5<*InstansNummer*>14 |50014 |
| Enqueue Replikering / *Lbrule50016* |5<*InstansNummer*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*InstansNummer*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*InstansNummer*>14 |51014 |
| Vinn RM *Lbrule5985* | |5985 |
| Fildelning *Lbrule445* | |445 |

_**Tabell 1:** Portnummer för SAP NetWeaver ABAP ASCS-instanser_

Skapa sedan dessa belastningsutjämningsslutpunkter för SAP NetWeaver Java SCS-portarna:

| Regelnamn för tjänst-/belastningsutjämning | Standardportnummer | Betongportar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32<*instansnummer*> |3201 |
| Gateway Server / *lbrule3301* |33<*instansnummer*> |3301 |
| Java Meddelande Server / *lbrule3900* |39<*instansnummer*> |3901 |
| Meddelandeserver HTTP / *Lbrule8101* |81<*instansnummer*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5<*InstansNummer*>13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5<*InstansNummer*>14 |50114 |
| Enqueue Replikering / *Lbrule50116* |5<*InstansNummer*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*InstansNummer*>13 |51113 |
| SAP Start service ERS HTTP *Lbrule51114* |5<*InstansNummer*>14 |51114 |
| Vinn RM *Lbrule5985* | |5985 |
| Fildelning *Lbrule445* | |445 |

_**Tabell 2:** Portnummer för SAP NetWeaver Java SCS-instanser_

![Bild 15: Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare][sap-ha-guide-figure-3004]

_**Figur 15:** Standardregler för ASCS/SCS-belastningsutjämning för Azures interna belastningsutjämnare_

Ange IP-adressen för belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instansen.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Ändra standardregler för belastningsutjämning för ASCS/SCS för den interna belastningsutjämnaren i Azure

Om du vill använda olika tal för SAP ASCS- eller SCS-instanserna måste du ändra namn och värden för deras portar från standardvärden.

1. I Azure-portalen väljer du >  ** < *SID*>-lb-ascs belastningsutjämningsreglerna för belastningsutjämning**.**Load Balancing Rules**
2. För alla belastningsutjämningsregler som tillhör SAP ASCS- eller SCS-instansen ändrar du dessa värden:

   * Namn
   * Port
   * Backend-port

   Om du till exempel vill ändra standardnummer för ASCS-instans från 00 till 31 måste du göra ändringarna för alla portar som visas i tabell 1.

   Här är ett exempel på en uppdatering för port *lbrule3200*.

   ![Bild 16: Ändra standardbelastningsutjämningsreglerna för ASCS/SCS för standardbelastningsutjämning för Azures interna belastningsutjämnare][sap-ha-guide-figure-3005]

   _**Figur 16:** Ändra standardregler för belastningsutjämning för ASCS/SCS för den interna belastningsutjämnaren i Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Lägga till virtuella Windows-datorer i domänen

När du har tilldelat en statisk IP-adress till de virtuella datorerna lägger du till de virtuella datorerna i domänen.

![Bild 17: Lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Figur 17:** Lägga till en virtuell dator i en domän_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Lägga till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen

Azure Load Balancer har en intern belastningsutjämnare som stänger anslutningar när anslutningarna är inaktiva under en viss tidsperiod (en inaktiv timeout). SAP-arbetsprocesser i dialoginstanser öppnar anslutningar till SAP-enqueue-processen så snart den första enqueue/dequeue-begäran måste skickas. Dessa anslutningar förblir vanligtvis etablerade tills arbetsprocessen eller efterföljande processen startas om. Men om anslutningen är inaktiv under en viss tidsperiod, stänger Azure interna belastningsutjämnaren anslutningarna. Detta är inte ett problem eftersom SAP-arbetsprocessen återupprättar anslutningen till enqueue-processen om den inte längre finns. Dessa aktiviteter dokumenteras i utvecklarspårningarna av SAP-processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra `KeepAliveTime` TCP/IP och `KeepAliveInterval` på båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP-profilparametrar som beskrivs senare i artikeln.

Om du vill lägga till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen lägger du först till dessa Windows-registerposter i båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabell 3:** Ändra den första TCP/IP-parametern_

Lägg sedan till de här Windows-registerposterna i båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabell 4:** Ändra den andra TCP/IP-parametern_

**Om du vill använda ändringarna startar du om båda klusternoderna**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurera ett kluster för Windows Server-redundanskluster för en SAP ASCS/SCS-instans

Konfigurera ett klusterkluster för Windows Server-redundanskluster för en SAP ASCS/SCS-instans innebär följande uppgifter:

- Samla in klusternoderna i en klusterkonfiguration
- Konfigurera ett klusterfilresursvittne

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Samla in klusternoderna i en klusterkonfiguration

1. Lägg till redundanskluster i båda klusternoderna i guiden Lägg till roll och funktioner.
2. Konfigurera redundansklustret med redundansklusterhanteraren. I Redundansklusterhanteraren väljer du **Skapa kluster**och lägger sedan bara till namnet på det första klustret, noden A. Lägg inte till den andra noden ännu. Du lägger till den andra noden i ett senare steg.

   ![Bild 18: Lägg till server- eller virtuellt datornamn för den första klusternoden][sap-ha-guide-figure-3007]

   _**Figur 18:** Lägga till server- eller virtuellt datornamn för den första klusternoden_

3. Ange nätverkets namn (virtuellt värdnamn) för klustret.

   ![Bild 19: Ange klusternamnet][sap-ha-guide-figure-3008]

   _**Figur 19:** Ange klusternamnet_

4. När du har skapat klustret kör du ett klusterverifieringstest.

   ![Bild 20: Kör klustervalideringskontrollen][sap-ha-guide-figure-3009]

   _**Figur 20:** Kör klusterverifieringskontrollen_

   Du kan ignorera alla varningar om diskar just nu i processen. Du lägger till ett filresursvittne och SIOS delade diskar senare. I detta skede behöver du inte oroa dig för att ha ett kvorum.

   ![Bild 21: Ingen kvorumdisk hittades][sap-ha-guide-figure-3010]

   _**Figur 21:** Ingen kvorumdisk hittades_

   ![Bild 22: Kärnklusterresursen behöver en ny IP-adress][sap-ha-guide-figure-3011]

   _**Figur 22:** Kärnklusterresursen behöver en ny IP-adress_

5. Ändra IP-adressen för kärnklustertjänsten. Klustret kan inte starta förrän du ändrar IP-adressen för kärnklustertjänsten, eftersom serverns IP-adress pekar på en av noderna för den virtuella datorn. Gör detta på sidan **Egenskaper** för kärnklustertjänstens IP-resurs.

   Vi måste till exempel tilldela en IP-adress (i vårt exempel **10.0.0.42)** för klustrets virtuella värdnamn **pr1-ascs-vir**.

   ![Bild 23: Ändra IP-adressen i dialogrutan Egenskaper][sap-ha-guide-figure-3012]

   _**Figur 23:** Ändra IP-adressen i dialogrutan **Egenskaper**_

   ![Bild 24: Tilldela DEN IP-adress som är reserverad för klustret][sap-ha-guide-figure-3013]

   _**Figur 24:** Tilldela IP-adressen som är reserverad för klustret_

6. Ta med det virtuella värdnamnet för klustret online.

   ![Bild 25: Klusterkärntjänsten är igång och med rätt IP-adress][sap-ha-guide-figure-3014]

   _**Figur 25:** Klusterkärntjänsten är igång och med rätt IP-adress_

7. Lägg till den andra klusternoden.

   Nu när kärnklustertjänsten är igång kan du lägga till den andra klusternoden.

   ![Bild 26: Lägg till den andra klusternoden][sap-ha-guide-figure-3015]

   _**Figur 26:** Lägga till den andra klusternoden_

8. Ange ett namn för den andra klusternodvärden.

   ![Bild 27: Ange värdnamnet för den andra klusternoden][sap-ha-guide-figure-3016]

   _**Figur 27:** Ange värdnamnet för den andra klusternoden_

   > [!IMPORTANT]
   > Kontrollera att kryssrutan **Lägg till all kvalificerad lagring i klustret** **inte** är markerad.  
   >
   >

   ![Bild 28: Markera inte kryssrutan][sap-ha-guide-figure-3017]

   _**Figur 28:** Markera **inte** kryssrutan_

   Du kan ignorera varningar om kvorum och diskar. Du anger kvorumet och delar disken senare, enligt beskrivningen i [Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klusterresursdisk .][sap-ha-guide-8.12.3]

   ![Bild 29: Ignorera varningar om diskkvorum][sap-ha-guide-figure-3018]

   _**Figur 29:** Ignorera varningar om diskkvorum_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurera ett vittne om klusterfilresurs

Konfigurera en klusterfil aktie vittne innebär dessa uppgifter:

- Skapa en filresurs
- Ange kvorum för filresursvittnet i Klusterhanteraren för redundans

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Skapa en filresurs

1. Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

   I exemplen i den här artikeln finns filresursvittnet på Active Directory/DNS-servern som körs i Azure. Filresursvittnet kallas **domcontr-0**. Eftersom du skulle ha konfigurerat en VPN-anslutning till Azure (via Plats-till-plats VPN eller Azure ExpressRoute) är din Active Directory/DNS-tjänst lokal och är inte lämplig att köra ett filresursvittne.

   > [!NOTE]
   > Om Active Directory/DNS-tjänsten bara körs lokalt ska du inte konfigurera filresursvittnet i Active Directory/DNS Windows-operativsystemet som körs lokalt. Nätverksfördröjning mellan klusternoder som körs i Azure och Active Directory/DNS lokalt kan vara för stor och orsaka anslutningsproblem. Var noga med att konfigurera filresursvittnet på en virtuell Azure-dator som körs nära klusternoden.  
   >
   >

   Kvorumenheten behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för kvorumenheten.

2. Lägg till klusternamnsobjektet.

   ![Bild 30: Tilldela behörigheterna för resursen för klusternamnsobjektet][sap-ha-guide-figure-3019]

   _**Figur 30:** Tilldela behörigheterna för resursen för klusternamnsobjektet_

   Se till att behörigheterna innehåller behörigheten att ändra data i resursen för klusternamnsobjektet (i vårt exempel **pr1-ascs-vir$**).

3. Om du vill lägga till klusternamnsobjektet i listan väljer du **Lägg till**. Ändra filtret för att söka efter datorobjekt, utöver de som visas i figur 31.

   ![Bild 31: Ändra objekttyperna så att de innehåller datorer][sap-ha-guide-figure-3020]

   _**Figur 31:** Ändra objekttyper så att de innehåller datorer_

   ![Bild 32: Markera kryssrutan Datorer][sap-ha-guide-figure-3021]

   _**Figur 32:** Markera kryssrutan **Datorer**_

4. Ange klusternamnsobjektet enligt figur 31. Eftersom posten redan har skapats kan du ändra behörigheterna, som visas i figur 30.

5. Välj fliken **Säkerhet** för resursen och ange sedan mer detaljerade behörigheter för klusternamnsobjektet.

   ![Bild 33: Ange säkerhetsattribut för klusternamnsobjektet i filresurskvorumet][sap-ha-guide-figure-3022]

   _**Figur 33:** Ange säkerhetsattribut för klusternamnsobjektet i filresurskvorumet_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ange kvorum för filresursvittnet i Klusterhanteraren för redundans

1. Öppna guiden Konfigurera kvoruminställning.

   ![Bild 34: Starta guiden Konfigurera klusterkvorum][sap-ha-guide-figure-3023]

   _**Figur 34:** Starta guiden Konfigurera klusterkvorum_

2. På sidan **Välj kvorumkonfiguration** väljer du **Välj kvorumvittne**.

   ![Bild 35: Kvorumkonfigurationer du kan välja mellan][sap-ha-guide-figure-3024]

   _**Figur 35:** Kvorumkonfigurationer du kan välja mellan_

3. På sidan **Välj kvorumvittne** väljer du **Konfigurera ett filresursvittne**.

   ![Bild 36: Välj vittne om fildelning][sap-ha-guide-figure-3025]

   _**Figur 36:** Markera vittnet om filresurs_

4. Ange UNC-sökvägen till filresursen \\(i vårt exempel domcontr-0\FSW). Om du vill visa en lista över de ändringar du kan göra väljer du **Nästa**.

   ![Bild 37: Definiera platsen för filresursen för vittnesresursen][sap-ha-guide-figure-3026]

   _**Figur 37:** Definiera fildelningsplatsen för vittnesresursen_

5. Markera önskade ändringar och välj sedan **Nästa**. Du måste konfigurera om klusterkonfigurationen enligt figur 38.  

   ![Bild 38: Bekräftelse på att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

   _**Figur 38:** Bekräftelse på att du har konfigurerat om klustret_

När du har installerat Windows Redundansklustret måste ändringar göras i vissa tröskelvärden för att anpassa redundansidentifiering till villkor i Azure. De parametrar som ska ändras dokumenteras i den här bloggen: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Om du antar att dina två virtuella datorer som skapar Windows-klusterkonfigurationen för ASCS/SCS finns i samma undernät måste följande parametrar ändras till dessa värden:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Dessa inställningar testades med kunder och gav en bra kompromiss för att vara tillräckligt motståndskraftig på ena sidan. Å andra sidan dessa inställningar gav tillräckligt snabb redundans i verkliga felförhållanden på SAP-programvara eller nod / VM fel. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klusterresursdisken

Du har nu en fungerande Windows Server Redundansklusterkonfiguration i Azure. Men för att installera en SAP ASCS/SCS-instans behöver du en delad diskresurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en tredjepartslösning som du kan använda för att skapa delade diskresurser.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS klusterresurs disk innebär dessa uppgifter:

- Lägga till .NET Framework 3.5
- Installera SIOS DataKeeper
- Ställa in SIOS DataKeeper

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Lägg till .NET Framework 3.5
Microsoft .NET Framework 3.5 aktiveras eller installeras inte automatiskt på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver att .NET Framework ska finnas på alla noder som du installerar DataKeeper på, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows enligt bild 39.

  ![Bild 39: Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Figur 39:** Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 40: Förloppsindikator för installation när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Figur 40:** Installationsförloppsindikatorn när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommandoradsverktyget dism.exe. För den här typen av installation måste du komma åt SxS-katalogen i Windows-installationsmediet. Vid en upphöjd kommandotolk skriver du:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Om du vill skapa virtuell delad lagring med SIOS DataKeeper skapar du en synkroniserad spegel och simulerar sedan klusterdelad lagring.

Innan du installerar SIOS-programvaran skapar du domänanvändaren **DataKeeperSvc**.

> [!NOTE]
> Lägg till **DataKeeperSvc-användaren** i gruppen **Lokal administratör** på båda klusternoderna.
>
>

Så här installerar du SIOS DataKeeper:

1. Installera SIOS-programvaran på båda klusternoderna.

   ![SIOS-installationsprogrammet][sap-ha-guide-figure-3030]

   ![Figur 41: Första sidan i SIOS DataKeeper-installationen][sap-ha-guide-figure-3031]

   _**Figur 41:** Första sidan i SIOS DataKeeper-installationen_

2. Välj **Ja**i dialogrutan som visas i bild 42 .

   ![Bild 42: Datakeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

   _**Figur 42:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3. I dialogrutan som visas i bild 43 rekommenderar vi att du väljer **Domän- eller Serverkonto**.

   ![Bild 43: Användarval för SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figur 43:** Användarval för SIOS DataKeeper_

4. Ange användarnamn och lösenord för domänkontot som du har skapat för SIOS DataKeeper.

   ![Bild 44: Ange domännamn och lösenord för SIOS DataKeeper-installationen][sap-ha-guide-figure-3034]

   _**Figur 44:** Ange domännamn och lösenord för SIOS DataKeeper-installationen_

5. Installera licensnyckeln för din SIOS DataKeeper-instans enligt figur 45.

   ![Bild 45: Ange licensnyckeln för SIOS-datakeepern][sap-ha-guide-figure-3035]

   _**Figur 45:** Ange din SIOS-licensnyckel för DataKeeper_

6. Starta om den virtuella datorn när du uppmanas att göra det.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Konfigurera SIOS DataKeeper

När du har installerat SIOS DataKeeper på båda noderna måste du starta konfigurationen. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare virtuella hårddiskar som är kopplade till var och en av de virtuella datorerna.

1. Starta verktyget DataKeeper Management and Configuration och välj sedan **Anslut server**. (I figur 46 är det här alternativet inringat i rött.)

   ![Bild 46: Verktyget för hantering och konfiguration av SIOS-datakeeper][sap-ha-guide-figure-3036]

   _**Figur 46:** Verktyg för hantering och konfiguration av SIOS-datakeeper_

2. Ange namnet eller TCP/IP-adressen för den första noden som hanterings- och konfigurationsverktyget ska ansluta till och, i ett andra steg, den andra noden.

   ![Bild 47: Infoga namnet eller TCP/IP-adressen för den första noden som verktyget Hantering och konfiguration ska ansluta till, och i ett andra steg ska den andra noden ansluta][sap-ha-guide-figure-3037]

   _**Figur 47:** Infoga namnet eller TCP/IP-adressen för den första noden som verktyget Hantering och konfiguration ska ansluta till, och i ett andra steg den andra noden_

3. Skapa replikeringsjobbet mellan de två noderna.

   ![Bild 48: Skapa ett replikeringsjobb][sap-ha-guide-figure-3038]

   _**Figur 48:** Skapa ett replikeringsjobb_

   En guide guidar dig genom processen att skapa ett replikeringsjobb.
4. Definiera namn, TCP/IP-adress och diskvolym för källnoden.

   ![Bild 49: Definiera namnet på replikeringsjobbet][sap-ha-guide-figure-3039]

   _**Figur 49:** Definiera namnet på replikeringsjobbet_

   ![Bild 50: Definiera basdata för noden, som ska vara den aktuella källnoden][sap-ha-guide-figure-3040]

   _**Figur 50:** Definiera basdata för noden, som ska vara den aktuella källnoden_

5. Definiera namn, TCP/IP-adress och diskvolym för målnoden.

   ![Bild 51: Definiera basdata för noden, vilket ska vara den aktuella målnoden][sap-ha-guide-figure-3041]

   _**Figur 51:** Definiera basdata för noden, som ska vara den aktuella målnoden_

6. Definiera komprimeringsalgoritmerna. I vårt exempel rekommenderar vi att du komprimerar replikeringsströmmen. Särskilt i omsynkroniseringssituationer minskar komprimeringen av replikeringsströmmen dramatiskt omsynkroniseringstiden. Observera att komprimering använder CPU- och RAM-resurserna för en virtuell dator. När komprimeringshastigheten ökar, så gör volymen av CPU-resurser som används. Du kan också justera den här inställningen senare.

7. En annan inställning som du behöver kontrollera är om replikeringen sker asynkront eller synkront. *När du skyddar SAP ASCS/SCS-konfigurationer måste du använda synkron replikering*.  

   ![Bild 52: Definiera replikeringsinformation][sap-ha-guide-figure-3042]

   _**Figur 52:** Definiera replikeringsinformation_

8. Definiera om volymen som replikeras av replikeringsjobbet ska representeras till en klusterkonfiguration för Kluster för Windows Server-redundanskluster som en delad disk. För SAP ASCS/SCS-konfigurationen väljer du **Ja** så att Windows-klustret ser den replikerade volymen som en delad disk som den kan använda som klustervolym.

   ![Bild 53: Välj Ja om du vill ställa in den replikerade volymen som en klustervolym][sap-ha-guide-figure-3043]

   _**Figur 53:** Välj **Ja** om du vill ange den replikerade volymen som en klustervolym_

   När volymen har skapats visar verktyget DataKeeper Management and Configuration att replikeringsjobbet är aktivt.

   ![Bild 54: DataKeeper synkron spegling för SAP ASCS/SCS-resursdisken är aktiv][sap-ha-guide-figure-3044]

   _**Figur 54:** DataKeeper synkron spegling för SAP ASCS /SCS-resursdisken är aktiv_

   Redundansklusterhanteraren visar nu disken som en DataKeeper-disk, som visas i figur 55.

   ![Bild 55: Redundansklusterhanteraren visar disken som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _**Figur 55:** Redundansklusterhanteraren visar disken som DataKeeper replikerade_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Installera SAP NetWeaver-systemet

Vi beskriver inte DBMS-konfigurationen eftersom installationerna varierar beroende på vilket DBMS-system du använder. Vi antar dock att problem med hög tillgänglighet med DBMS åtgärdas med de funktioner som de olika DBMS-leverantörerna stöder för Azure. Till exempel Alltid på eller databasspegling för SQL Server och Oracle Data Guard för Oracle-databaser. I det scenario vi använder i den här artikeln har vi inte lagt till mer skydd till DBMS.

Det finns inga särskilda överväganden när olika DBMS-tjänster interagerar med den här typen av klustrade SAP ASCS/SCS-konfiguration i Azure.

> [!NOTE]
> Installationsförfarandena för SAP NetWeaver ABAP-system, Java-system och ABAP+Java-system är nästan identiska. Den största skillnaden är att ett SAP ABAP-system har en ASCS-instans. SAP-Java-systemet har en SCS-instans. SAP ABAP+Java-systemet har en ASCS-instans och en SCS-instans som körs i samma Microsoft redundansklustergrupp. Eventuella installationsskillnader för varje SAP NetWeaver-installationsstack nämns uttryckligen. Du kan anta att alla andra delar är desamma.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installera SAP med en ASCS/SCS-instans med hög tillgänglighet

> [!IMPORTANT]
> Se till att inte placera din sidfil på DataKeeper speglade volymer. DataKeeper stöder inte speglade volymer. Du kan lämna din sidfil på den tillfälliga enheten D för en virtuell Azure-dator, vilket är standard. Om den inte redan finns där flyttar du Windows-sidfilen för att köra D för din virtuella Azure-dator.
>
>

Installera SAP med en hög tillgänglighet ASCS/SCS instans innebär dessa uppgifter:

- Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen
- Installera den första klusternoden FÖR SAP
- Ändra SAP-profilen för ASCS/SCS-instansen
- Lägga till en avsökningsport
- Öppna avsökningsporten för Windows-brandväggen

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1. Skapa en DNS-post för det virtuella värdnamnet för ASCS/SCS-instansen i Windows DNS-hanteraren.

   > [!IMPORTANT]
   > IP-adressen som du tilldelar det virtuella värdnamnet för ASCS/SCS-instansen måste vara samma som ip-adressen som du har tilldelat Azure Load Balancer**<*(SID*>-lb-ascs**).  
   >
   >

   IP-adressen för det virtuella SAP ASCS/SCS-värdnamnet **(pr1-ascs-sap)** är samma som IP-adressen för Azure Load Balancer **(pr1-lb-ascs**).

   ![Bild 56: Definiera DNS-posten för virtuellt SAP ASCS/SCS-kluster-kluster och TCP/IP-adress][sap-ha-guide-figure-3046]

   _**Figur 56:** Definiera DNS-posten för virtuellt SAP ASCS/SCS-kluster-kluster och TCP/IP-adress_

2. Om du vill definiera den IP-adress som tilldelats det virtuella värdnamnet väljer du **DNS Manager** > **Domain**.

   ![Bild 57: Nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration][sap-ha-guide-figure-3047]

   _**Figur 57:** Nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Installera den första klusternoden FÖR SAP

1. Kör det första klusternodalternativet på klusternod A. Till exempel på **pr1-ascs-0** värd.
2. Om du vill behålla standardportarna för Azures interna belastningsutjämnare väljer du:

   * **ABAP-system**: **ASCS-instansnummer** **00**
   * **Java-system**: **SCS-instansnummer** **01**
   * **ABAP+Java-system**: **ASCS-instansnummer** **00** och **SCS-instansnummer** **01**

   Om du vill använda andra instansnummer än 00 för ABAP ASCS-instansen och 01 för Java SCS-instansen måste du först ändra standardbelastningsreglerna för Azure-utjämning av belastningsutjämnare, som beskrivs i [Ändra standardbelastningsutjämningsreglerna för Azures interna belastningsutjämnar][sap-ha-guide-8.9].

De följande uppgifterna beskrivs inte i standarddokumentationen för SAP-installation.

> [!NOTE]
> Sap-installationsdokumentationen beskriver hur du installerar den första ASCS/SCS-klusternoden.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Ändra SAP-profilen för ASCS/SCS-instansen

Du måste lägga till en ny profilparameter. Profilparametern förhindrar anslutningar mellan SAP-arbetsprocesser och enqueue-servern från att stängas när de är inaktiva för länge. Vi nämnde problemscenariot i [Lägg till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen][sap-ha-guide-8.11]. I det avsnittet introducerade vi också två ändringar i vissa grundläggande TCP/IP-anslutningsparametrar. I ett andra steg måste du ställa in enqueue-servern så att `keep_alive` anslutningarna inte når Azures interna belastningsutjämnad tröskel.

Så här ändrar du SAP-profilen för ASCS/SCS-instansen:

1. Lägg till den här profilparametern i SAP ASCS/SCS-instansprofilen:

   ```
   enque/encni/set_so_keepalive = true
   ```
   I vårt exempel är vägen:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Till exempel till SAP SCS-instansprofilen och motsvarande sökväg:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Om du vill tillämpa ändringarna startar du om SAP ASCS /SCS-instansen.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Lägga till en avsökningsport

Använd den interna belastningsutjämnarens avsökningsfunktioner för att få hela klusterkonfigurationen att fungera med Azure Load Balancer. Azure interna belastningsutjämnar vanligtvis den inkommande arbetsbelastningen lika mellan deltagande virtuella datorer. Detta fungerar dock inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiv och kan inte acceptera någon av arbetsbelastningen. En avsökningsfunktion hjälper när Azures interna belastningsutjämnare tilldelar arbete endast till en aktiv instans. Med avsökningsfunktionen kan den interna belastningsutjämnaren identifiera vilka instanser som är aktiva och sedan bara rikta instansen med arbetsbelastningen.

Så här lägger du till en avsökningsport:

1. Kontrollera den aktuella **ProbePort-inställningen** genom att köra följande PowerShell-kommando. Kör den inifrån en av de virtuella datorerna i klusterkonfigurationen.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definiera en avsökningsport. Standardnumret för avsökningsporten är **0**. I vårt exempel använder vi sondport **62000**.

   ![Bild 58: Avsökningsporten för klusterkonfiguration är 0 som standard][sap-ha-guide-figure-3048]

   _**Figur 58:** Standardavsökningsporten för klusterkonfiguration är 0_

   Portnumret definieras i SAP Azure Resource Manager-mallar. Du kan tilldela portnumret i PowerShell.

   Om du vill ange ett nytt ProbePort-värde för **SAP-<*SID*> IP-klusterresurs** kör du följande PowerShell-skript. Uppdatera PowerShell-variablerna för din miljö. När skriptet har körts uppmanas du att starta om SAP-klustergruppen för att aktivera ändringarna.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   När du har ansluta **rollen SAP <*SID-kluster* > ** kontrollerar du att **ProbePort** är inställt på det nya värdet.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Bild 59: Avsök klusterporten när du har angett det nya värdet][sap-ha-guide-figure-3049]

   _**Figur 59:** Avsök klusterporten när du har angett det nya värdet_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Öppna avsökningsporten för Windows-brandväggen

Du måste öppna en Avsökningsport för Windows-brandväggen på båda klusternoderna. Använd följande skript för att öppna en avsökningsport för Windows-brandväggen. Uppdatera PowerShell-variablerna för din miljö.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** är inställd på **62000**. Nu kan du komma åt filresursen ** \\\ascsha-clsap\sapmnt** från andra värdar, till exempel från **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Installera databasinstansen

Om du vill installera databasinstansen följer du processen som beskrivs i SAP-installationsdokumentationen.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Installera den andra klusternoden

Om du vill installera det andra klustret följer du stegen i SAP-installationsguiden.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Ändra starttypen för TJÄNSTEN SAP ERS Windows

Ändra starttypen för TJÄNSTEN SAP ERS Windows till **Automatisk (Fördröjd start)** på båda klusternoderna.

![Bild 60: Ändra tjänsttypen för SAP ERS-instansen till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Figur 60:** Ändra tjänsttypen för SAP ERS-instansen till fördröjd automatisk_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Installera SAP Primary Application Server

Installera PAS-instansen (Primary Application Server) <*SID*>-di-0 på den virtuella datorn som du har utsett som värd för PAS. Det finns inga beroenden på Azure- eller DataKeeper-specifika inställningar.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Installera den ytterligare programservern för SAP

Installera en SAP Additional Application Server (AAS) på alla virtuella datorer som du har utsett att vara värd för en SAP Application Server-instans. Till exempel på <*SID*>-di-1 till <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> Detta avslutar installationen av ett SAP NetWeaver-system med hög tillgänglighet. Fortsätt sedan med redundanstestning.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testa REDUNDAns-/SIOS-instansen SAP ASCS/SCS-instansen och SIOS-replikering
Det är enkelt att testa och övervaka en SAP ASCS/SCS-instans redundans- och SIOS-diskreplikering med hjälp av Redundansklusterhanteraren och verktyget SIOS DataKeeper Management and Configuration.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS-instans körs på klusternod A

**KLUSTERGRUPPEN SAP PR1** körs på klusternod A. Till exempel på **pr1-ascs-0**. Tilldela den delade diskenheten S, som ingår i **SAP PR1-klustergruppen** och som ASCS/SCS-instansen använder, till klusternod A.

![Bild 61: Redundansklusterhanteraren: SAP-<SID> klustergrupp körs på klusternod A][sap-ha-guide-figure-5000]

_**Figur 61:** Redundansklusterhanteraren: SAP-<*SID*> klustergruppen körs på klusternod A_

I verktyget SIOS DataKeeper Management and Configuration kan du se att delade diskdata är synkront replikerade från källvolymenheten S på klusternoden A till målvolymenheten S på klusternoden B. Till exempel replikeras den från **pr1-ascs-0 [10.0.0.40]** till **pr1-ascs-1 [10.0.0.41]**.

![Bild 62: I SIOS DataKeeper replikerar du den lokala volymen från klusternod A till klusternod B][sap-ha-guide-figure-5001]

_**Figur 62:** I SIOS DataKeeper replikerar du den lokala volymen från klusternod A till klusternod B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Redundans från nod A till nod B

1. Välj ett av dessa alternativ för att initiera en redundans av SAP-<*SID->* klustergrupp från klusternod A till klusternod B:
   - Använda Redundansklusterhanteraren  
   - Använda Redundanskluster PowerShell

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Starta om klusternoden A i Windows gästoperativsystem (detta initierar en automatisk redundans av SAP-<*SID->* klustergrupp från nod A till nod B).  
3. Starta om klusternoden A från Azure-portalen (detta initierar en automatisk redundans av SAP-<*SID->* klustergrupp från nod A till nod B).  
4. Starta om klusternoden A med hjälp av Azure PowerShell (detta initierar en automatisk redundans av SAP-<*SID->* klustergrupp från nod A till nod B).

   Efter redundans körs SAP-<*SID*> klustergrupp på klusternod B. Till exempel, det körs på **pr1-ascs-1**.

   ![Bild 63: I Redundansklusterhanteraren körs SAP-<SID> klustergrupp på klusternod B][sap-ha-guide-figure-5002]

   _**Bild 63**: I Redundansklusterhanteraren körs SAP-<*SID*> klustergrupp på klusternod B_

   Den delade disken är nu monterad på klusternoden B. SIOS DataKeeper replikerar data från källvolymenhet S på klusternoden B för att rikta volymenhet S på klusternod A. Till exempel replikerar från **pr1-ascs-1 [10.0.0.41]** till **pr1-ascs-0 [10.0.0.40]**.

   ![Bild 64: SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A][sap-ha-guide-figure-5003]

   _**Figur 64:** SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A_
