---
title: Azure virtuella datorer hög tillgänglighet för SAP NetWeaver | Microsoft Docs
description: Guide för hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: goraco
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 691bb0c5ea6d84bd67b8b1b1fd5a05c25f75ba40
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437036"
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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP – flera SÄKERHETSIDENTIFIERARE konfiguration med hög tillgänglighet)


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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP produkten tillgänglighet matris)
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/resource-group-overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager
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


Azure Virtual Machines är lösningen för organisationer som behöver beräkning, lagring och nätverksresurser på minimal tid och utan sätt långa anskaffningscykler cykler. Du kan använda Azure Virtual Machines för att distribuera klassiska program som SAP NetWeaver-baserade ABAP, Java och en ABAP + Java-stack. Utöka tillförlitlighet och tillgänglighet utan ytterligare lokala resurser. Azure-datorer stöder flera platsanslutningar, så att du kan integrera Azure-datorer i organisationens lokala domäner, privata moln och SAP-landskap som system.

I den här artikeln beskriver vi de steg som du kan vidta för att distribuera SAP-system för hög tillgänglighet i Azure med hjälp av Azure Resource Manager-distributionsmodellen. Vi går du igenom dessa viktiga uppgifter:

* Hitta rätt SAP Notes och installationsguiderna, enligt den [resurser] [ sap-ha-guide-2] avsnittet. Den här artikeln kompletterar dokumentationen för installation av SAP och SAP Notes, vilka är de viktigaste resurserna som kan hjälpa dig att installera och distribuera SAP-program på vissa plattformar.
* Läs om skillnaderna mellan Azure Resource Manager-distributionsmodellen och den klassiska distributionsmodellen.
* Läs mer om Windows Server Failover Clustering kvorumlägen så du kan välja den modell som passar din Azure-distribution.
* Läs mer om Windows Server Failover Clustering delad lagring i Azure-tjänster.
* Lär dig hur du kan skydda en enskild punkt för fel komponenter som Advanced Business Application Programming (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) och system för databashantering (DBMS) och redundanta komponenter som SAP-programmet En server i Azure.
* Följ en steg för steg-exempel på en installation och konfiguration av en SAP-system för hög tillgänglighet i ett Windows Server Failover Clustering-kluster i Azure med hjälp av Azure Resource Manager.
* Läs mer om ytterligare steg som krävs för att använda Windows Server Failover Clustering i Azure, men som inte behövs i en lokal distribution.

För att förenkla distribution och konfiguration i den här artikeln använder vi SAP trelagers-hög tillgänglighet Resource Manager-mallar. Mallarna automatisera distributionen av hela infrastrukturen som du behöver för en SAP-system med hög tillgänglighet. Infrastrukturen har även stöd för SAP programmet prestanda Standard (SAP) storlek på SAP-system.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Nödvändiga komponenter
Innan du börjar bör du kontrollera att du uppfyller kraven som beskrivs i följande avsnitt. Dessutom måste du kontrollera alla resurser som finns i den [resurser] [ sap-ha-guide-2] avsnittet.

I den här artikeln använder vi Azure Resource Manager-mallar för [trelagers-SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). En bra översikt över mallar finns i [SAP Azure Resource Manager-mallar](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Resurser
De här artiklarna beskriver SAP-distributioner i Azure:

* [Azure virtuella datorer, planering och implementering av SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]
* [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver (den här guiden)][sap-ha-guide]

> [!NOTE]
> När det är möjligt, vi skickar du en länk till den refererande installationsguiden för SAP (se den [SAP-installationsguiderna][sap-installation-guides]). Krav och information om installationsprocessen är det en bra idé att läsa SAP NetWeaver-installationsguiderna noggrant. Den här artikeln beskriver endast specifika uppgifter för SAP NetWeaver-baserade system som du kan använda med Azure Virtual Machines.
>
>

Dessa SAP Notes är relaterade till avsnittet om SAP i Azure:

| Nummer | Titel |
| --- | --- |
| [1928533] |SAP-program i Azure: Produkter som stöds och storlek |
| [2015553] |SAP på Microsoft Azure: Supportkrav |
| [1999351] |Förbättrad Azure-övervakning för SAP |
| [2178632] |Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [1999351] |Virtualisering på Windows: Förbättrad övervakning |
| [2243692] |Användning av Azure Premium SSD-lagringen för SAP DBMS-instans |

Läs mer om den [begränsningar i Azure-prenumerationer][azure-subscription-service-limits-subscription], inklusive Allmänt standardbegränsningar och högsta begränsningar.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Hög tillgänglighet för SAP med Azure Resource Manager jämfört med den klassiska distributionsmodellen
Azure Resource Manager och Azure klassiska distributionsmodellen skiljer sig inom följande områden:

- Resursgrupper
- Azure intern Azure load balancer beroende på Azure-resursgrupp
- Stöd för SAP – flera SÄKERHETSIDENTIFIERARE scenarier

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Resursgrupper
I Azure Resource Manager kan du använda resursgrupper för att hantera alla resurser som programmet i Azure-prenumerationen. En integrerad lösning i en resursgrupp, alla resurser har samma livscykel. Till exempel alla resurser skapas på samma gång och de tas bort samtidigt. Läs mer om [resursgrupper](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure intern Azure load balancer beroende på Azure-resursgrupp

I den klassiska Azure-modellen har ett beroende mellan Azure intern belastningsutjämnare (Azure Load Balancer-tjänsten) och gruppen cloud service. Alla interna belastningsutjämnaren måste en cloud service-grupp.

I Azure Resource Manager kan du inte behöver en Azure-resursgrupp du använder Azure Load Balancer. Miljön är enklare och mer flexibel.

### <a name="support-for-sap-multi-sid-scenarios"></a>Stöd för SAP – flera SÄKERHETSIDENTIFIERARE scenarier

Du kan installera flera SAP system ID (SID) ASCS/SCS instanser i ett kluster i Azure Resource Manager. Multi-SID-instanser är möjliga tack vare stöd för flera IP-adresser för varje Azure intern belastningsutjämnare.

Om du vill använda den klassiska distributionsmodellen, följer du anvisningarna i [SAP NetWeaver i Azure: Clustering SAP ASCS/SCS-instanser med hjälp av Windows Server Failover Clustering i Azure med SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Azure Resource Manager-distributionsmodellen för dina SAP-installationer. Den erbjuder många fördelar som inte är tillgängliga i den klassiska distributionsmodellen. Läs mer om Azure [distributionsmodeller][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server-Redundansklustring
Windows Server Failover Clustering är grunden för en hög tillgänglighet SAP ASCS/SCS installation och DBMS i Windows.

Ett redundanskluster är en grupp 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om det inträffar ett nodfel, beräknar Windows Server Failover Clustering antalet fel som kan uppstå samtidigt som ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja från olika kvorumlägen att uppnå failover-kluster.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Kvorumlägen
Du kan välja bland fyra kvorumlägen när du använder Windows Server Failover Clustering:

* **Nodmajoritet**. Varje nod i klustret kan rösta. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Vi rekommenderar det här alternativet för kluster som har ett ojämnt antal noder. Exempelvis kan misslyckas med tre noder i ett kluster med sju noder och kluster stillbilder uppnår en majoritet och fortsätter att köras.  
* **Nod- och**. Varje nod och en avsedda disk (ett diskvittne) i klustrets lagringssystem kan rösta när de är tillgängliga och i kommunikationen. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Det här läget bra i en klustermiljö med ett jämnt tal noder. Om hälften av noderna och disken är online, förblir klustret i felfritt tillstånd.
* **Nod- och filresurs majoritet**. Varje nod plus en avsedda filresurs (ett filresursvittne) som du har skapat kan rösta, oavsett om noderna och filresursen är tillgänglig och i kommunikationen. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Det här läget bra i en klustermiljö med ett jämnt tal noder. Den liknar nod- och -läge, men den använder en vittne filresurs i stället för en vittnesdisk. Det här läget är enkla att implementera, men om filresursen själva har inte hög tillgänglighet, så kan det bli en enskild felpunkt.
* **Ingen majoritet: Endast disk**. Klustret har ett kvorum om en nod är tillgänglig och i kommunikationen med en viss disk i klusterlagringen. Endast de noder som är också vid kommunikation med disken kan ansluta till klustret. Vi rekommenderar att du inte använder det här läget.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover-kluster på plats
Bild 1 visar ett kluster med två noder. Om nätverksanslutningen mellan noder misslyckas och både noder stanna upp och drift, en kvorumdisken eller fil delar du avgör vilken nod fortsätter att tillhandahålla klustrets program och tjänster. Noden som har åtkomst till kvorum disk eller filresurs är den nod som garanterar att tjänster fortsätter.

Eftersom det här exemplet använder ett kluster med två noder kan använda vi kvorumläge för nod och filresursmajoritet. Nod- och diskmajoritet är också ett giltigt alternativ. I en produktionsmiljö rekommenderar vi att du använder en kvorumdisk. Du kan använda Nätverks- och system-teknik för att ge den hög tillgänglighet.

![Bild 1: Exempel på en Windows Server Failover Clustering-konfiguration för SAP ASCS/SCS i Azure][sap-ha-guide-figure-1000]

_**Bild 1:** Exempel på en Windows Server Failover Clustering-konfiguration för SAP ASCS/SCS i Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Delad lagring
Bild 1 visar också ett kluster med två noder delad lagring. I ett kluster med delad lagring på plats identifiera alla noder i klustret delad lagring. En mekanism för låsning skyddar data mot skador. Alla noder kan identifiera om en annan nod misslyckas. Om en nod misslyckas, den återstående noden ägarskapet till storage-resurser och garanterar tillgängligheten för tjänster.

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-program, t.ex. med SQL Server. SQL Server Always On replikerar du DBMS data och loggfiler filer från den lokala disken på en klusternod till den lokala disken för en annan klusternod. I så fall behöver klusterkonfigurationen Windows inte en delad disk.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Nätverks- och namnmatchning
Klientdatorer når klustret via en virtuell IP-adress och ett virtuellt värdnamn som DNS-servern tillhandahåller. Lokala noder och DNS-server kan hantera flera IP-adresser.

I en typisk konfiguration använder du två eller flera nätverksanslutningar:

* En dedikerad anslutning till lagring
* En kluster-internt nätverksanslutning för pulsslag
* Ett offentligt nätverk som klienter använder för att ansluta till klustret

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover-kluster i Azure
Azure Virtual Machines krävs jämfört med bare metal eller privat moln-distributioner, ytterligare steg för att konfigurera Windows Server Failover Clustering. När du skapar en delad klusterdisk kan behöva du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instans.

I den här artikeln diskuterar vi viktiga begrepp och de ytterligare stegen som krävs för att skapa ett kluster med SAP central services för hög tillgänglighet i Azure. Vi visar hur du ställer in verktyg från tredje part SIOS DataKeeper och hur du konfigurerar Azure den interna belastningsutjämnaren. Du kan använda dessa verktyg för att skapa ett redundanskluster i Windows med ett filresursvittne i Azure.

![Bild 2: Windows Server Failover Clustering konfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 2:** Windows Server Failover Clustering konfiguration i Azure utan en delad disk_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Delad disk i Azure med SIOS DataKeeper
Du behöver klustret delad lagring för en SAP ASCS/SCS-instans med hög tillgänglighet. Från September 2016 är omfattas Azure inte av delad lagring som du kan använda för att skapa ett kluster med delad lagring. Du kan använda programvara från tredje part SIOS DataKeeper Cluster Edition för att skapa ett speglat lagringsutrymme som simulerar klustrets delade lagring. SIOS-lösningen innehåller realtidsdata synkron replikering. Detta är hur du kan skapa en delad diskresurs för ett kluster:

1. Koppla en ytterligare Azure-hårddisk (VHD) till var och en av de virtuella datorerna (VM) i en Windows-klusterkonfiguration.
2. Kör SIOS DataKeeper Cluster Edition på båda noderna för virtuell dator.
3. Konfigurera SIOS DataKeeper Cluster Edition så att det speglar innehållet i ytterligare virtuell Hårddisk ansluten-volym från den virtuella källdatorn till ytterligare virtuell Hårddisk ansluten volymen för den virtuella måldatorn. SIOS DataKeeper avlägsnar käll- och lokala volymer och visar dem sedan till Windows Server Failover Clustering som en delad disk.

Hämta mer information om [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Bild 3: Windows Server Failover Clustering konfiguration i Azure med SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 3:** Windows Server Failover Clustering konfiguration i Azure med SIOS DataKeeper_

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-produkter som SQL Server. SQL Server Always On replikerar du DBMS data och loggfiler filer från den lokala disken på en klusternod till den lokala disken för en annan klusternod. I det här fallet behöver klusterkonfigurationen Windows inte en delad disk.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Namnmatchning i Azure
Möjlighet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser omfattas inte av Azure-molnplattformen. Du behöver en alternativ lösning för att konfigurera en virtuell IP-adress att nå klusterresursen i molnet.
Azure har en intern belastningsutjämnare i Azure Load Balancer-tjänsten. Med den interna belastningsutjämnaren nå klienter klustret över klustrets virtuella IP-adress.
Du måste distribuera den interna belastningsutjämnaren i resursgruppen som innehåller noder i klustret. Konfigurera sedan alla nödvändiga port vidarebefordran med avsökningen portar för den interna belastningsutjämnaren.
Klienterna kan ansluta via virtuella värdnamnet. DNS-servern löser klustrets IP-adress och interna hanterar belastningsutjämnarporten vidarebefordran till den aktiva noden i klustret.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver med hög tillgänglighet i Azure Infrastructure-as-a-Service (IaaS)
Om du vill få SAP-program med hög tillgänglighet som gäller programvarukomponenter som SAP, måste du skydda följande komponenter:

* SAP Application Server-instans
* SAP ASCS/SCS-instans
* DBMS-server

Mer information om hur du skyddar SAP-komponenter i scenarier med hög tillgänglighet finns i [Azure Virtual Machines planering och implementering av SAP NetWeaver](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Hög tillgänglighet SAP-programservern
Vanligtvis behöver du inte en specifik lösning för hög tillgänglighet för SAP-programservern och dialogrutan instanser. Du kan uppnå hög tillgänglighet genom redundans och konfigurerar du dialogrutan alla instanser i olika instanser av Azure-datorer. Du bör ha minst två instanser av SAP programmet installerat i två instanser av Azure-datorer.

![Bild 4: Hög tillgänglighet SAP-programservern][sap-ha-guide-figure-2000]

_**Bild 4:** Hög tillgänglighet SAP-programservern_

Du måste placera alla virtuella datorer som värd SAP Application Server-instanser i samma Azure-tillgänglighetsuppsättning har angetts. En Azure-tillgänglighetsuppsättning säkerställer att:

* Alla virtuella datorer är en del av samma uppgraderingsdomän. En uppgraderingsdomän, till exempel ser till att de virtuella datorerna inte är uppdaterade på samma gång under planerat underhåll avbrott.
* Alla virtuella datorer ingår i samma feldomän. En feldomän, till exempel ser till att virtuella datorer distribueras så att ingen enskild felpunkt som påverkar tillgängligheten för alla virtuella datorer.

Mer information om hur du [hantera tillgängligheten för virtuella datorer][virtual-machines-manage-availability].

Eftersom Azure storage-kontot är en potentiell felpunkt, är det viktigt att du har minst två Azure storage-konton, där minst två virtuella datorer distribueras. I en perfekt installationsprogrammet, skulle diskar på varje virtuell dator som kör en instans för SAP-dialogrutan distribueras i ett annat lagringskonto.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Hög tillgänglighet SAP ASCS/SCS-instans
Bild 5 visas ett exempel på en hög tillgänglighet SAP ASCS/SCS-instans.

![Bild 5: Hög tillgänglighet SAP ASCS/SCS-instans][sap-ha-guide-figure-2001]

_**Bild 5:** Hög tillgänglighet SAP ASCS/SCS-instans_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS-instans hög tillgänglighet med Windows Server Failover Clustering i Azure
Azure Virtual Machines krävs jämfört med bare metal eller privat moln-distributioner, ytterligare steg för att konfigurera Windows Server Failover Clustering. Om du vill skapa ett Windows-redundanskluster, behöver du en delad klusterdisk, flera IP-adresser, flera virtuella värdnamn och en Azure intern belastningsutjämnare för clustering SAP ASCS/SCS-instans. Detta beskrivs i närmare detalj i artikeln.

![Bild 6: Windows Server Failover Clustering för en SAP ASCS/SCS-konfiguration i Azure med hjälp av SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 6:** Windows Server Failover Clustering för en SAP ASCS/SCS-konfiguration i Azure med SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Hög tillgänglighet DBMS-instans
DBMS är också en enda kontaktpunkt i ett SAP-system. Du måste skydda den med hjälp av en lösning för hög tillgänglighet. Bild 7 visar en SQL Server Always On hög tillgänglighet lösning i Azure, med Windows Server Failover Clustering och intern Azure belastningsutjämnare. SQL Server Always On replikerar DBMS data och loggfiler filer med hjälp av en egen DBMS-replikering. I detta fall använder behöver du inte klustret delade diskar som förenklar hela installationen.

![Bild 7: Exempel på en hög tillgänglighet SAP DBMS, med SQL Server Always On][sap-ha-guide-figure-2003]

_**Bild 7:** Exempel på en hög tillgänglighet SAP DBMS, med SQL Server Always On_

Mer information om kluster och SQL Server i Azure med hjälp av Azure Resource Manager-distributionsmodellen finns i följande artiklar:

* [Konfigurera Always On-tillgänglighetsgrupp i Azure virtuella datorer manuellt med hjälp av Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurera en Azure intern belastningsutjämnare för en Always On-tillgänglighetsgrupp i Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Distributionsscenarier för slutpunkt till slutpunkt hög tillgänglighet

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenario för distribution med hjälp av arkitekturen mall 1

Bild 8 visar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **en** SAP-system. Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för SAP ASCS/SCS-instans.
- Ett dedikerat kluster används för DBMS-instans.
- SAP-programservern instanser har distribuerats i sina egna dedikerade virtuella datorer.

![Bild 8: SAP-arkitekturen mall 1, för hög tillgänglighet med dedikerade kluster för ASCS/SCS och DBMS][sap-ha-guide-figure-2004]

_**Bild 8:** SAP hög tillgänglighet arkitektoniska mall 1, dedikerade kluster för ASCS/SCS och DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenario för distribution med hjälp av arkitekturen mall 2

Bild 9 illustrerar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **en** SAP-system. Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS-instans och DBMS.
- SAP-programservern instanser har distribuerats i egna dedikerade virtuella datorer.

![Bild 9: SAP hög tillgänglighet arkitektoniska mall 2, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS][sap-ha-guide-figure-2005]

_**Bild 9:** SAP hög tillgänglighet arkitektoniska mall 2, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenario för distribution med hjälp av arkitekturen mall 3

Bild 10 visar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **två** SAP-system, med &lt;SID1&gt; och &lt;SID2&gt;. Det här scenariot har ställts in på följande sätt:

- Ett dedikerat kluster används för **både** SID1 för SAP ASCS/SCS-instans *och* SID2 för SAP ASCS/SCS-instans (ett kluster).
- Ett dedikerat kluster används för DBMS SID1 och ett annat dedikerade kluster används för DBMS SID2 (två kluster).
- SAP-programservern instanser för SAP-system SID1 har sina egna dedikerade virtuella datorer.
- SAP-programservern instanser för SAP-system SID2 har sina egna dedikerade virtuella datorer.

![Bild 10: SAP hög tillgänglighet arkitektoniska mall 3, med ett dedikerat kluster för olika ASCS/SCS-instanser][sap-ha-guide-figure-6003]

_**Bild 10:** SAP hög tillgänglighet arkitektoniska mall 3, med ett dedikerat kluster för olika ASCS/SCS-instanser_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Förbered infrastrukturen

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbered infrastrukturen för arkitektoniska mall 1
Azure Resource Manager-mallar för SAP underlätta distributionen av resurser som krävs.

Trelagers-mallar i Azure Resource Manager stöder även scenarier med hög tillgänglighet, till exempel i arkitekturen mall 1, som har två kluster. Varje kluster är en SAP felpunkt för SAP ASCS/SCS och DBMS.

Här är där du kan få Azure Resource Manager-mallar för exempelscenariot som beskrivs i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Så här förbereder du infrastrukturen för arkitektoniska mall 1:

- I Azure-portalen på den **parametrar** bladet i den **SYSTEMAVAILABILITY** väljer **HA**.

  ![Bild 11: Ställa in parametrar för SAP hög tillgänglighet Azure Resource Manager][sap-ha-guide-figure-3000]

_**Bild 11:** Ställa in parametrar för SAP hög tillgänglighet Azure Resource Manager_


  Skapa mallar:

  * **Virtuella datorer**:
    * SAP-programservern virtuella datorer: <*SAPSystemSID*> - di - <*tal*>
    * ASCS/SCS klustra virtuella datorer: <*SAPSystemSID*> - ascs - <*tal*>
    * DBMS-kluster: <*SAPSystemSID*> - db - <*tal*>

  * **Nätverkskort för alla virtuella datorer med tillhörande IP-adresser**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Azure storage-konton**

  * **Tillgänglighetsgrupper** för:
    * SAP-programservern virtuella datorer: <*SAPSystemSID*> - avset - di
    * SAP ASCS/SCS klustra virtuella datorer: <*SAPSystemSID*> - avset - ascs
    * DBMS klustra virtuella datorer: <*SAPSystemSID*> - avset - db

  * **Intern Azure belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instans och IP-adressen <*SAPSystemSID*> - lb - ascs
    * Med alla portar för SQL Server DBMS och IP-adress <*SAPSystemSID*> - lb - db

  * **Nätverkssäkerhetsgrupp**: <*SAPSystemSID*> - nsg - ascs-0  
    * Med en öppen externa Remote Desktop Protocol (RDP)-port till den <*SAPSystemSID*> - ascs - 0-dator

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare är **dynamisk** som standard. Ändra dem till **Statiska** IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuera virtuella datorer med anslutning till företagets nätverk (mellan lokala) ska användas i produktion
För produktion SAP-system, distribuera Azure-datorer med [anslutning till företagets nätverk (mellan lokala)] [ planning-guide-2.2] med hjälp av Azure plats-till-plats-VPN eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instans. Virtuellt nätverk och undernät har redan skapats och förberetts.
>
>

1.  I Azure-portalen på den **parametrar** bladet i den **NEWOREXISTINGSUBNET** väljer **befintliga**.
2.  I den **SUBNETID** lägger du till fullständig sträng med förberedda Azure nätverk SubnetID där du planerar att distribuera virtuella datorer i Azure.
3.  Om du vill hämta en lista över alla Azure-nätverk undernät, kör du följande PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Den **ID** fältet visar den **SUBNETID**.
4. Hämta en lista över alla **SUBNETID** värden, kör den här PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Den **SUBNETID** ser ut så här:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuera molnbaserad SAP-instanser för testning och demonstration
Du kan distribuera dina SAP-system för hög tillgänglighet i en endast molnbaserad distributionsmodell. Den här typen av distribution är främst användbart för Demonstrations- och användningsfall. Det är inte lämpligt för produktion.

- I Azure-portalen på den **parametrar** bladet i den **NEWOREXISTINGSUBNET** väljer **nya**. Lämna den **SUBNETID** fält tomma.

  SAP Azure Resource Manager-mallen skapar automatiskt Azure-nätverk och undernät.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbered infrastrukturen för arkitektoniska mall 2

Du kan använda Azure Resource Manager-mallen för SAP för att underlätta distributionen av resurser för infrastruktur som krävs för SAP arkitektoniska mall 2.

Här är där du kan få Azure Resource Manager-mallar för det här distributionsscenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbered infrastrukturen för arkitektoniska mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för **– flera SÄKERHETSIDENTIFIERARE**. Du kan till exempel lägga till en ytterligare SAP ASCS/SCS-instans i en *befintliga* klusterkonfigurationen. Mer information finns i [konfigurera en ytterligare SAP ASCS/SCS-instans i en befintliga klusterkonfigurationen och skapa en SAP – flera SÄKERHETSIDENTIFIERARE konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster kan du använda multi-SID [snabbstartsmallarna på GitHub](https://github.com/Azure/azure-quickstart-templates).
Om du vill skapa ett nytt multi-SID-kluster, måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Databasmall för](#database-template)
* [Mall för program-servrar](#application-servers-template)

I följande avsnitt innehåller mer information om mallar och de parametrar som du ska ange i mallarna.

#### <a name="ASCS-SCS-template"></a> ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett redundanskluster i Windows Server som är värd för flera ASCS/SCS-instanser.

Du ställer in mallen ASCS/SCS multi-SID i den [ASCS/SCS – flera SÄKERHETSIDENTIFIERARE mallen][sap-templates-3-tier-multisid-xscs-marketplace-image], ange värden för följande parametrar:

  - **Resurs-Prefix**.  Ange resurs-prefixet som används som prefix i alla resurser som skapas under distributionen. Eftersom resurserna som inte tillhör bara en SAP-system, är prefixet för resursen inte SID för en SAP-system.  Prefixet måste vara mellan **tre och sex tecken**.
  - **Stack typ**. Välj stack-typ av SAP-system. Beroende på vilken stack har Azure Load Balancer en (ABAP eller endast Java) eller två (ABAP + Java) privata IP-adresser per SAP-system.
  -  **OS-typ**. Välj operativsystem för de virtuella datorerna.
  -  **Antal SAP**. Välj antal SAP-system som du vill installera i det här klustret.
  -  **Systemets tillgänglighet**. Välj **HA**.
  -  **Administratörens användarnamn och lösenord för serveradministratören**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Nytt eller befintligt undernät**. Ange om ett nytt virtuellt nätverk och undernät som ska skapas eller ett befintligt undernät som ska användas. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
  -  **Undernät-Id**. Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så här: /subscriptions/ <*prenumerations-id*> /resourceGroups/ <*Resursgruppsnamn*> /providers/Microsoft.Network/virtualNetworks/ < *namn på virtuellt nätverk*> /subnets/ <*undernätsnamn*>

Mallen distribuerar en Azure Load Balancer-instans som har stöd för flera SAP-system.

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS sätta replikering Server (ERS) (endast Linux)-instanserna är konfigurerade för instansnummer 02, 12, 22...
- SCS ERS (endast Linux)-instanserna är konfigurerade för instansnummer 03, 13, 23...

Belastningsutjämnaren innehåller 1.2 för Linux VIP(s), 1 x-VIP för ASCS/SCS och 1 x-VIP för ÄNDARE (endast Linux).

Följande lista innehåller alla belastningsutjämningsregler (där x är numret på SAP-system, till exempel 1, 2, 3...).
- Windows-specifika portar för varje SAP-system: 445, 5985
- ASCS-portar (instansnummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instansnummer x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASCS ERS portar i Linux (instansnummer x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS ERS portar i Linux (instansnummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad för att använda följande portar för avsökning (där x är numret på SAP-system, till exempel 1, 2, 3...):
- ASCS/SCS intern Azure load balancer avsökningsporten: 620x0
- ÄNDARE interna läsa in belastningsutjämnare avsökningsporten (endast Linux): 621x2

#### <a name="database-template"></a> Databasmall för

Databas-mallen distribuerar en eller två virtuella datorer som du kan använda för att installera det relationella databashanteringssystemet (RDBMS) för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Du ställer in databasen – flera SÄKERHETSIDENTIFIERARE mallen, i den [databasen – flera SÄKERHETSIDENTIFIERARE mallen][sap-templates-3-tier-multisid-db-marketplace-image], ange värden för följande parametrar:

  -  **SAP-System-Id**. Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
  -  **OS-typ**. Välj operativsystem för de virtuella datorerna.
  -  **DbType**. Välj typ av databas som du vill installera på klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på virtuella datorer. Se till att välja rätt operativsystemtypen: Välj **Windows** för SQL och väljer en Linux-distribution för HANA. Azure Load Balancer som är anslutna till de virtuella datorerna kommer att konfigureras för att stödja den valda databas-typ:
    * **SQL**. Belastningsutjämnaren att belastningsutjämna-port 1433. Se till att använda den här porten för din SQL Server Always On-konfiguration.
    * **HANA**. Belastningsutjämnaren att belastningsutjämna portarna 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
    Belastningsutjämnaren använder avsökningsporten 62550.
  -  **SAP-systemstorlek**. Ange antalet SAP ger det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegratör.
  -  **Systemets tillgänglighet**. Välj **HA**.
  -  **Administratörens användarnamn och lösenord för serveradministratören**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**. Ange ID för det undernät som du använde under distributionen av ASCS/SCS-mall eller ID för det undernät som har skapats som en del av ASCS/SCS-mallsdistribution.

#### <a name="application-servers-template"></a> Mall för program-servrar

Programmet servrar mallen distribuerar två eller flera virtuella datorer som kan användas som SAP-programservern instanser för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Konfigurera servrar – flera SÄKERHETSIDENTIFIERARE programmallen, i den [programmall servrar – flera SÄKERHETSIDENTIFIERARE][sap-templates-3-tier-multisid-apps-marketplace-image], ange värden för följande parametrar:

  -  **SAP-System-Id**. Ange ID för SAP-system för SAP-system som du vill installera. ID: T används som ett prefix för de resurser som distribueras.
  -  **OS-typ**. Välj operativsystem för de virtuella datorerna.
  -  **SAP-systemstorlek**. Antal SAP ger det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegratör.
  -  **Systemets tillgänglighet**. Välj **HA**.
  -  **Administratörens användarnamn och lösenord för serveradministratören**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**. Ange ID för det undernät som du använde under distributionen av ASCS/SCS-mall eller ID för det undernät som har skapats som en del av ASCS/SCS-mallsdistribution.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure-nätverk
I vårt exempel är 10.0.0.0/16 i adressutrymmet för virtuella Azure-nätverket. Det finns ett undernät som kallas **undernät**, med adressintervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar till nätverksinställningar i gästoperativsystemet. Detta omfattar IP-adresser, DNS-servrar och undernät. Konfigurera nätverksinställningarna i Azure. DHCP Dynamic Host Configuration Protocol ()-tjänsten sprider dina inställningar.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS-IP-adresser

Utför följande steg för att ställa in den nödvändiga DNS-IP adresser.

1.  I Azure-portalen på den **DNS-servrar** bladet se till att det virtuella nätverket **DNS-servrar** alternativet är inställt på **anpassad DNS**.
2.  Välj dina inställningar beroende på vilken typ av nätverk som du har. Mer information finns i följande resurser:
    * [Anslutning till företagets nätverk (mellan lokala)][planning-guide-2.2]: Lägg till IP-adresserna för DNS-servrar på plats.  
    Du kan utöka den lokala DNS-servrar till de virtuella datorerna som körs i Azure. I det här scenariot kan du lägga till IP-adresserna för de Azure-datorerna där du kör DNS-tjänsten.
    * [Endast molnbaserad distribution][planning-guide-2.1]: Distribuera en ytterligare virtuell dator i samma virtuella nätverk-instans som fungerar som en DNS-server. Lägg till IP-adresserna för de Azure-datorerna som du har konfigurerat för att köra DNS-tjänsten.

    ![Bild 12: Konfigurera DNS-servrar för Azure Virtual Network][sap-ha-guide-figure-3001]

    _**Bild 12:** Konfigurera DNS-servrar för Azure Virtual Network_

  > [!NOTE]
  > Om du ändrar IP-adresserna för DNS-servrar kan behöva du starta om de Azure-datorerna för att tillämpa ändringen och sprida de nya DNS-servrarna.
  >
  >

I vårt exempel har DNS-tjänsten installerats och konfigurerats på dessa Windows-datorer:

| Virtuell datorroll | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Värdnamn och statiska IP-adresser för de klustrade SAP ASCS/SCS-instans och DBMS klustrad instans

För lokal distribution behöver du de här reserverade värdnamn och IP-adresser:

| Rollen för virtuell värd-namn | Virtuellt värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första virtuell värd klusternamn (för klusterhantering) |pr1-ascs-vir |10.0.0.42 |
| Virtuella värdnamn för SAP ASCS/SCS-instans |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (hantering) |pr1-dbms-vir |10.0.0.32 |

När du skapar klustret kan du skapa virtuella värdnamn **pr1-ascs-vir** och **pr1-dbms-vir** och de associerade IP-adresser som hanterar själva klustret. Information om hur du gör detta finns i [samla in klusternoder i en klusterkonfiguration][sap-ha-guide-8.12.1].

Du kan manuellt skapa de andra två virtuella värdnamn, **pr1-ascs-sap** och **pr1-dbms-sap**, och de associerade IP-adresserna på DNS-servern. Den klustrade SAP ASCS/SCS-instansen och den klustrade DBMS-instansen använder du dessa resurser. Information om hur du gör detta finns i [skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ange statiska IP-adresser för de virtuella datorerna för SAP
När du distribuerar de virtuella datorerna ska användas i ditt kluster kan behöva du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1.  I Azure-portalen väljer du **resursgrupp** > **nätverkskort** > **inställningar** > **IP-adress**.
2.  På den **IP-adresser** bladet under **tilldelning**väljer **statiska**. I den **IP-adress** anger du IP-adressen som du vill använda.

  > [!NOTE]
  > Om du ändrar IP-adressen för nätverkskortet kan behöva du starta om de Azure-datorerna för att tillämpa ändringen.  
  >
  >

  ![Bild 13: Ange statiska IP-adresser för nätverkskort på varje virtuell dator][sap-ha-guide-figure-3002]

  _**Bild 13:** Ange statiska IP-adresser för nätverkskort på varje virtuell dator_

  Upprepa det här steget för alla nätverksgränssnitt som är, för alla virtuella datorer, inklusive virtuella datorer som du vill använda för din Active Directory/DNS-tjänst.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Virtuell datorroll | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP Application Server-instansen |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP Application Server-instansen |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Andra noden i klustret för ASCS/SCS-instans |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Andra noden i klustret för DBMS-instans |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ange en statisk IP-adress för intern Azure belastningsutjämnare

SAP Azure Resource Manager-mallen skapar en Azure intern belastningsutjämnare som används för kluster för SAP ASCS/SCS-instans och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren för SAP ASCS/SCS: **pr1-lb-ascs**.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna belastningsutjämnaren för DBMS: **pr1-lb-dbms**.
>
>

Ange en statisk IP-adress för intern Azure belastningsutjämnare:

1.  Den första distributionen anger interna belastningsutjämnarens IP-adressen till **dynamisk**. I Azure-portalen på den **IP-adresser** bladet under **tilldelning**väljer **statiska**.
2.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för det virtuella värdnamnet för SAP ASCS/SCS-instans.
3.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instans.

  ![Bild 14: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans][sap-ha-guide-figure-3003]

  _**Bild 14:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans_

I vårt exempel har vi två Azure interna belastningsutjämnare som har dessa statiska IP-adresser:

| Azure interna belastningsutjämnarroll | Azure interna belastningsutjämnarens namn | Statisk IP-adress |
| --- | --- | --- |
| Intern belastningsutjämnare för SAP ASCS/SCS-instans |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med standard-instansnummer **00**
* En Java-SCS-instans med instans Standardnumret **01**

När du installerar SAP ASCS/SCS-instans, måste du använda instansen Standardnumret **00** för ASCS ABAP-instansen och instansen Standardnumret **01** för din Java SCS-instans.

Därefter skapa krävs för intern belastningsutjämning slutpunkter för SAP NetWeaver-portar.

Om du vill skapa nödvändiga intern belastningsutjämning slutpunkter först skapa dessa slutpunkter för SAP NetWeaver ABAP ASCS-portar för belastningsutjämning:

| Tjänsten/läsa in belastningsutjämning Regelnamn | Standardportnumren. | Konkreta portar för (ASCS-instansen med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Placera Server / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| ABAP Message Server / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Interna ABAP meddelande / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| Message Server HTTP / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Placera replikering / *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Win-RM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

_**Tabell 1:** Portnummer för SAP NetWeaver ABAP ASCS-instanser_

Skapa sedan dessa slutpunkter för SAP NetWeaver Java SCS-portar för belastningsutjämning:

| Tjänsten/läsa in belastningsutjämning Regelnamn | Standardportnumren. | Konkreta portar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Placera Server / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Gateway-Server / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Java Message Server / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| Message Server HTTP / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Placera replikering / *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Win-RM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

_**Tabell 2:** Portnummer för SAP NetWeaver Java SCS-instanser_

![Bild 15: Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare][sap-ha-guide-figure-3004]

_**Bild 15:** Standard ASCS/SCS belastningsutjämningsregler för intern Azure belastningsutjämnare_

Ange IP-adressen för belastningsutjämnaren **pr1-lb-dbms** till IP-adressen för det virtuella värdnamnet för DBMS-instans.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare

Om du vill använda olika antal för SAP ASCS eller SCS-instanser måste du ändra namn och värden för deras hamnar från standardvärdena.

1.  I Azure-portalen väljer du  **< *SID*> - lb - ascs belastningsutjämnare** > **läsa in belastningsutjämningsregler**.
2.  Alla regler för belastningsutjämning som hör till SAP ASCS eller SCS-instans, ändra dessa värden:

  * Namn
  * Port
  * Backend-port

  Om du vill ändra standardvärdet för ASCS-instans från 00 och 31, måste du göra ändringar för alla portar som anges i tabell 1.

  Här är ett exempel på en uppdatering för port *lbrule3200*.

  ![Bild 16: Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare][sap-ha-guide-figure-3005]

  _**Bild 16:** Ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Lägga till Windows-datorer i domänen

När du tilldelar en statisk IP-adress till de virtuella datorerna kan du lägga till de virtuella datorerna till domänen.

![Bild 17: Lägg till en virtuell dator till en domän][sap-ha-guide-figure-3006]

_**Bild 17:** Lägg till en virtuell dator till en domän_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans

Azure Load Balancer har en intern belastningsutjämnare att stängs anslutningar när anslutningar som är inaktiva för en viss tid (en timeout för inaktivitet). SAP-arbetsprocesser i dialogrutan instanser öppna anslutningar till SAP-sätta bearbeta när de första sätta/ta bort från kön för begäran måste skickas. De här anslutningarna förblir oftast upprättad förrän arbetsprocessen eller sätta processen startar om. Om den varit inaktiv under en angiven tidsperiod, stängs intern Azure belastningsutjämnare anslutningarna. Detta är inte ett problem Eftersom arbetsprocessen SAP som återupprättar anslutningen till sätta processen om det inte längre finns. Dessa aktiviteter finns dokumenterade i developer-spårning för SAP-processer, men de kan skapa en stor mängd extra innehåll i de spårningarna. Det är en bra idé att ändra TCP/IP `KeepAliveTime` och `KeepAliveInterval` på båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP-profil-parametrar som beskrivs senare i artikeln.

Om du vill lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans, Lägg först till dessa registerposter i Windows på både Windows-klusternoder för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabeltyp |REG_DWORD (Decimal) |
| Värde |120000 |
| Länkar till dokumentationen |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabell 3:** Ändra den första parametern för TCP/IP_

Lägg sedan till den här Windows-registerposter på både Windows-klusternoder för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabeltyp |REG_DWORD (Decimal) |
| Värde |120000 |
| Länkar till dokumentationen |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabell 4:** Ändra den andra parametern för TCP/IP_

**Tillämpa ändringarna genom att starta om båda klusternoderna**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurera ett redundanskluster i Windows Server-kluster för en SAP ASCS/SCS-instans

Konfigurera ett redundanskluster i Windows Server-kluster för en SAP ASCS/SCS-instans innebär att dessa uppgifter:

- Insamling av noderna i klustret i en klusterkonfiguration
- Konfigurera ett filresursvittne för kluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Samla in klusternoder i en klusterkonfiguration

1.  Lägga till redundansklustring till båda klusternoderna i Lägg till roller och funktioner som guiden.
2.  Konfigurera redundansklustret med hjälp av hanteraren för redundanskluster. I hanteraren för redundanskluster, Välj **Skapa kluster**, och Lägg sedan till bara namnet på det första klustret nod A. Lägg inte till den andra noden ännu; lägger du till den andra noden i ett senare steg.

  ![Bild 18: Lägg till server eller virtuell dator namnet på den första noden i klustret][sap-ha-guide-figure-3007]

  _**Bild 18:** Lägg till server eller virtuell dator namnet på den första noden i klustret_

3.  Ange nätverksnamn (virtuellt värdnamn) i klustret.

  ![Bild 19: Ange klusternamnet][sap-ha-guide-figure-3008]

  _**Bild 19:** Ange klusternamnet_

4.  När du har skapat klustret, kör du ett klustervalideringstest.

  ![Bild 20: Kör valideringskontrollen kluster][sap-ha-guide-figure-3009]

  _**Bild 20:** Kör valideringskontrollen kluster_

  Du kan ignorera alla varningar om diskar i det här läget i processen. Lägger du till ett filresursvittne och SIOS delade diskar senare. I det här skedet behöver du inte bekymra dig om att du har ett kvorum.

  ![Bild 21: Inga kvorumdisken hittas][sap-ha-guide-figure-3010]

  _**Bild 21:** Inga kvorumdisken hittas_

  ![Bild 22: Core-klusterresursen måste en ny IP-adress][sap-ha-guide-figure-3011]

  _**Bild 22:** Core-klusterresursen måste en ny IP-adress_

5.  Ändra IP-adressen för klustertjänsten core. Klustret kan inte starta tills du ändrar IP-adressen för klustertjänsten core eftersom IP-adressen för servern som pekar på en av noderna i virtuell dator. Gör detta på den **egenskaper** sidan i core-klustertjänsten IP-resurs.

  Exempelvis kan vi behöver tilldela en IP-adress (i vårt exempel **10.0.0.42**) för virtuell värd klusternamnet **pr1-ascs-vir**.

  ![Bild 23: I dialogrutan Egenskaper ändrar du IP-adress][sap-ha-guide-figure-3012]

  _**Bild 23:** I den **egenskaper** dialogrutan ändrar du IP-adress_

  ![Bild 24: Tilldela IP-adressen som är reserverat för klustret][sap-ha-guide-figure-3013]

  _**Bild 24:** Tilldela IP-adressen som är reserverat för klustret_

6.  Hämta virtuell värd klusternamnet.

  ![Bild 25: Core-klustertjänsten är igång och körs och med rätt IP-adress][sap-ha-guide-figure-3014]

  _**Bild 25:** Core-klustertjänsten är igång och körs och med rätt IP-adress_

7.  Lägg till den andra noden i klustret.

  Nu när klustertjänsten core är igång kan du lägga till den andra noden i klustret.

  ![Bild 26: Lägg till den andra noden i klustret][sap-ha-guide-figure-3015]

  _**Bild 26:** Lägg till den andra noden i klustret_

8.  Ange ett namn för den andra nod klustervärden.

  ![Bild 27: Ange andra värden klusternodnamnet][sap-ha-guide-figure-3016]

  _**Bild 27:** Ange andra värden klusternodnamnet_

  > [!IMPORTANT]
  > Kontrollera att den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan är **inte** valda.  
  >
  >

  ![Bild 28: Markera inte kryssrutan][sap-ha-guide-figure-3017]

  _**Bild 28:** Gör **inte** markerar du kryssrutan_

  Du kan ignorera varningar om kvorum och diskar. Du måste ange kvorum och dela disken senare, enligt beskrivningen i [installerar SIOS DataKeeper Cluster Edition för SAP ASCS/SCS klusterdisken resursen][sap-ha-guide-8.12.3].

  ![Bild 29: Ignorera varningar om disken kvorum][sap-ha-guide-figure-3018]

  _**Bild 29:** Ignorera varningar om disken kvorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurera ett filresursvittne för kluster

När du konfigurerar ett filresursvittne i kluster inkluderar dessa uppgifter:

- Skapa en filresurs
- Ange filen resurs vittne kvorum i hanteraren för redundanskluster

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Skapa en filresurs

1.  Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

  I exemplen i den här artikeln är filresursvittnet på den Active Directory/DNS-server som körs i Azure. Filresursvittnet kallas **domcontr 0**. Eftersom du skulle har konfigurerat en VPN-anslutning till Azure (via plats-till-plats-VPN eller Azure ExpressRoute) kan dela din Active Directory/DNS-tjänsten är på plats och inte är lämpliga att köra en fil vittne.

  > [!NOTE]
  > Om din Active Directory/DNS-tjänst körs bara på plats kan inte konfigurera din filresursvittne på Active Directory/DNS-Windows-operativsystemet som körs lokalt. Nätverksfördröjningen mellan noder som körs i Azure och Active Directory/DNS lokalt kan vara för stort och orsaka problem med nätverksanslutningen. Glöm inte att konfigurera filresursvittnet på en Azure-dator som körs nära noden i klustret.  
  >
  >

  Kvorum-enhet behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för enheten kvorum.

2.  Lägg till klusternamnobjekt.

  ![Bild 30: Tilldela behörigheter för resursen för klusternamnobjekt][sap-ha-guide-figure-3019]

  _**Bild 30:** Tilldela behörigheter för resursen för klusternamnobjekt_

  Se till att behörigheterna som inkluderar behörighet att ändra data i resursen för klusternamnobjekt (i vårt exempel **pr1-ascs-vir$**).

3.  Om du vill lägga till klusternamnobjekt i listan, Välj **Lägg till**. Ändra filtret för att söka efter datorobjekt, förutom de som visas i bild 31.

  ![Bild 31: Ändra objekttyper för att inkludera datorer][sap-ha-guide-figure-3020]

  _**Bild 31:** Ändra objekttyper för att inkludera datorer_

  ![Bild 32: Markera kryssrutan datorer][sap-ha-guide-figure-3021]

  _**Bild 32:** Välj den **datorer** kryssrutan_

4.  Ange klusternamnobjekt som visas i bild 31. Eftersom posten redan har skapats, kan du ändra behörigheterna som visas i bild 30.

5.  Välj den **Security** fliken för filresursen och ange sedan mer detaljerade behörigheter för klusternamnobjekt.

  ![Bild 33: Ange security attribut för klusterobjektet namn på filen resurs kvorum][sap-ha-guide-figure-3022]

  _**Bild 33:** Ange security attribut för klusterobjektet namn på filen resurs kvorum_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ange filen resurs vittne kvorum i hanteraren för redundanskluster

1.  Öppna den kvorum inställningen guiden Konfigurera.

  ![Bild 34: Börja konfigurera inställningen guiden klusterkvorum][sap-ha-guide-figure-3023]

  _**Bild 34:** Börja konfigurera inställningen guiden klusterkvorum_

2.  På den **Välj kvorumkonfigurationen** väljer **Välj kvorumvittnet**.

  ![Bild 35: Du kan välja bland kvorumkonfigurationer][sap-ha-guide-figure-3024]

  _**Bild 35:** Du kan välja bland kvorumkonfigurationer_

3.  På den **Välj Kvorumvittne** väljer **konfigurera ett filresursvittne**.

  ![Bild 36: Välj filresursvittnet][sap-ha-guide-figure-3025]

  _**Bild 36:** Välj filresursvittnet_

4.  Ange UNC-sökvägen till filresursen (i vårt exempel \\domcontr 0\FSW). Om du vill se en lista över ändringar som du kan göra **nästa**.

  ![Bild 37: Definiera filresursplats för resursen vittne][sap-ha-guide-figure-3026]

  _**Bild 37:** Definiera filresursplats för resursen vittne_

5.  Välj önskade ändringar och välj sedan **nästa**. Du måste konfigurera har om klusterkonfigurationen enligt bild 38.  

  ![Bild 38: Bekräfta att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

  _**Bild 38:** Bekräfta att du har konfigurerat om klustret_

När du har installerat Windows-redundanskluster har ändringar som behöver göras för vissa tröskelvärden för att anpassa redundans identifiering till villkoren i Azure. Parametrar som ska ändras finns dokumenterade i den här bloggen: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Om vi antar att dina två virtuella datorer som bygger Windows klusterkonfigurationen för ASCS/SCS finns i samma undernät, måste följande parametrar ändras till dessa värden:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Dessa inställningar har testats med kunder och tillhandahålls en bra kompromiss för att vara flexibel på ena sidan. Å andra sidan försåg dessa inställningar snabbt tillräckligt med redundans i verkliga fel vid SAP programvara eller nod och VM-fel. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resurs klusterdisken

Nu har du en fungerande Windows Server Failover Clustering konfiguration i Azure. Men om du vill installera en SAP ASCS/SCS-instans, måste en delad disk-resurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en lösning från tredje part som du kan använda för att skapa delade diskresurserna.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resurs klusterdisken omfattar dessa uppgifter:

- Att lägga till .NET Framework 3.5
- Installera SIOS DataKeeper
- Konfigurera SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Lägg till .NET Framework 3.5
Microsoft .NET Framework 3.5 är inte automatiskt aktiverats eller installerad på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver .NET Framework på alla noder som du installerar DataKeeper på, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows som visas i bild 39.

  ![Bild 39: Installera .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden][sap-ha-guide-figure-3028]

  _**Bild 39:** Installera .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden_

  ![Bild 40: Installationen förloppsindikator när du installerar .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden][sap-ha-guide-figure-3029]

  _**Bild 40:** Installationen förloppsindikator när du installerar .NET Framework 3.5 genom att använda Lägg till roller och funktioner som guiden_

- Använd kommandoradsverktyget dism.exe. Du behöver åtkomst till katalogen SxS på installationsmediet för Windows för den här typen av installation. Vid en upphöjd kommandotolk, skriver du:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Om du vill skapa virtuella delad lagring med SIOS DataKeeper, skapa en synkroniserad spegling och simulera klustrets delade lagring.

Innan du installerar programvaran SIOS skapa domänanvändaren **DataKeeperSvc**.

> [!NOTE]
> Lägg till den **DataKeeperSvc** användare till den **lokal administratör** på båda klusternoderna.
>
>

Så här installerar SIOS DataKeeper:

1.  Installera programvaran SIOS på båda klusternoderna.

  ![SIOS installer][sap-ha-guide-figure-3030]

  ![Bild 41: Första sidan i SIOS DataKeeper-installation][sap-ha-guide-figure-3031]

  _**Bild 41:** Första sidan i SIOS DataKeeper-installation_

2.  I dialogrutan som visas i bild 42 väljer **Ja**.

  ![Bild 42: DataKeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

  _**Bild 42:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3.  I dialogrutan som visas i bild 43 rekommenderar vi att du väljer **domän eller Server**.

  ![Bild 43: Användarens val för SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Bild 43:** Användarens val för SIOS DataKeeper_

4.  Ange domänanvändarnamn och lösenord som du skapade för SIOS DataKeeper.

  ![Bild 44: Ange domänanvändarnamn och lösenord för SIOS DataKeeper-installation][sap-ha-guide-figure-3034]

  _**Bild 44:** Ange domänanvändarnamn och lösenord för SIOS DataKeeper-installation_

5.  Installera licensnyckel för din SIOS DataKeeper-instans såsom visas på bild 45.

  ![Bild 45: Ange licensnyckeln SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Bild 45:** Ange licensnyckeln SIOS DataKeeper_

6.  När du uppmanas starta om den virtuella datorn.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Konfigurera SIOS DataKeeper

Du måste starta konfigurationen när du har installerat SIOS DataKeeper på båda noderna. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare virtuella hårddiskar anslutna till var och en av de virtuella datorerna.

1.  Starta verktyget DataKeeper hanterings- och välj sedan **Anslut Server**. (I bild 46 det här alternativet är inringat i rött.)

  ![Bild 46: SIOS DataKeeper hanterings- och verktyg][sap-ha-guide-figure-3036]

  _**Bild 46:** SIOS DataKeeper hanterings- och verktyg_

2.  Ange namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till, och i andra steget, den andra noden.

  ![Bild 47: Infoga namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till och en andra steget, den andra noden][sap-ha-guide-figure-3037]

  _**Bild 47:** Infoga namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till och en andra steget, den andra noden_

3.  Skapa replikeringen mellan de två noderna.

  ![Bild 48: Skapa ett replikeringsjobb][sap-ha-guide-figure-3038]

  _**Bild 48:** Skapa ett replikeringsjobb_

  En guide vägleder dig genom processen för att skapa ett replikeringsjobb.
4.  Definiera namn, TCP/IP-adress och diskvolymen för Källnoden.

  ![Bild 49: Ange namnet på replikeringen][sap-ha-guide-figure-3039]

  _**Bild 49:** Ange namnet på replikeringen_

  ![Bild 50: Definiera grunddata för den nod som ska vara den aktuella noden för källa][sap-ha-guide-figure-3040]

  _**Bild 50:** Definiera grunddata för den nod som ska vara den aktuella noden för källa_

5.  Definiera namn, TCP/IP-adress och diskvolymen för målnoden.

  ![Bild 51: Definiera grunddata för den nod som ska vara den aktuella målnoden][sap-ha-guide-figure-3041]

  _**Bild 51:** Definiera grunddata för den nod som ska vara den aktuella målnoden_

6.  Definiera komprimeringsalgoritmer. I vårt exempel rekommenderar vi att du komprimera dataströmmen för replikering. Särskilt i omsynkroniseringen situationer minskar komprimering av replikering stream avsevärt du tiden för omsynkronisering. Observera att komprimering använder resurser som processor och RAM-minne för en virtuell dator. När hastigheten med komprimering ökar, ökar mängden processorresurser som används. Du kan även justera inställningen senare.

7.  En annan inställning måste du kontrollera är om replikeringen sker synkront eller asynkront. *När du skyddar SAP ASCS/SCS-konfigurationer, måste du använda synkron replikering*.  

  ![Bild 52: Definiera replikeringsinformationen][sap-ha-guide-figure-3042]

  _**Bild 52:** Definiera replikeringsinformationen_

8.  Definiera om den volym som replikeras av replikeringen ska representeras som en delad disk på en Windows Server Failover Clustering klusterkonfigurationen. SAP ASCS/SCS-konfiguration, Välj **Ja** så att Windows-kluster ser replikerad volym som en delad disk som kan användas som en volym.

  ![Bild 53: Välj Ja om du vill ange replikerad volym som en volym][sap-ha-guide-figure-3043]

  _**Bild 53:** Välj **Ja** att ställa in replikerad volym som en volym_

  När volymen har skapats visas i DataKeeper hanterings- och verktyget att replikeringen är aktiv.

  ![Bild 54: DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv][sap-ha-guide-figure-3044]

  _**Bild 54:** DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv_

  Klusterhanterare för växling vid fel visas nu disken som en DataKeeper-disk som visas i bild 55.

  ![Bild 55: Hanteraren för redundanskluster visar disken som DataKeeper replikerade][sap-ha-guide-figure-3045]

  _**Bild 55:** Hanteraren för redundanskluster visar disken som DataKeeper replikerade_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installera SAP NetWeaver

Vi kommer inte att beskriva DBMS-installationen eftersom inställningar varierar beroende på DBMS system som du använder. Men förutsätter vi att få hög tillgänglighet med DBMS behandlas med funktioner som de olika DBMS-leverantörerna support för Azure. Till exempel alltid på eller databasspegling för SQL Server och Oracle Data Guard för Oracle-databaser. Vi använder i den här artikeln för scenariot lade vi inte till bättre skydd för DBMS.

Det finns inga särskilda överväganden vid olika DBMS-tjänster som interagerar med den här typen av klustrad SAP ASCS/SCS-konfiguration i Azure.

> [!NOTE]
> Installationsanvisningar för SAP NetWeaver ABAP-system, Java-system och ABAP + Java-system är nästan identiska. Den viktigaste skillnaden är att en SAP ABAP-system har en ASCS-instans. SAP-Java-systemet har en SCS-instans. SAP ABAP + Java-system har en ASCS-instans och en SCS-instans som körs i samma klustergrupp för Microsoft-redundans. Eventuella skillnader för installation för varje stack för installationen av SAP NetWeaver anges uttryckligen. Du kan anta att alla delar är samma.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installera SAP med en hög tillgänglighet ASCS/SCS-instans

> [!IMPORTANT]
> Glöm inte att placera växlingsfilen på DataKeeper speglade volymer. DataKeeper stöder inte speglade volymer. Du kan lämna växlingsfilen på den temporära enheten D Azure-dator, vilket är standard. Om den inte redan finns där, flytta växlingsfilen Windows till D-disken på din Azure-dator.
>
>

Installera SAP med en hög tillgänglighet ASCS/SCS-instans innebär att dessa uppgifter:

- Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen
- Installera den första klusternoden SAP
- Ändra SAP-profilen för ASCS/SCS-instans
- Att lägga till en avsökningsport
- Öppna avsökningsporten för Windows-brandväggen

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1.  Skapa en DNS-post för det virtuella värdnamnet på ASCS/SCS-instans i Windows DNS-hanteraren.

  > [!IMPORTANT]
  > IP-adressen som du tilldelar till virtuella namnet på ASCS/SCS-instans måste vara samma som IP-adress som tilldelats Azure Load Balancer (**<*SID*> - lb - ascs**).  
  >
  >

  IP-adressen för virtuella SAP ASCS/SCS-värdnamnet (**pr1-ascs-sap**) är samma som IP-adressen för Azure Load Balancer (**pr1-lb-ascs**).

  ![Bild 56: Definiera DNS-posten för virtuella SAP ASCS/SCS-klusternamnet och TCP/IP-adress][sap-ha-guide-figure-3046]

  _**Bild 56:** Definiera DNS-posten för virtuella SAP ASCS/SCS-klusternamnet och TCP/IP-adress_

2.  För att definiera de IP-adress som tilldelats virtuella värdnamn, Välj **DNS-hanteraren** > **domän**.

  ![Bild 57: Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration][sap-ha-guide-figure-3047]

  _**Bild 57:** Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS-klusterkonfiguration_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installera den första klusternoden SAP

1.  Kör det första klustra noden alternativet på klusternod A. Till exempel på den **pr1-ascs-0** värden.
2.  Om du vill behålla standardvärdet portar för intern Azure belastningsutjämnare, väljer du:

  * **ABAP system**: **ASCS** antal instanser **00**
  * **Java system**: **SCS** antal instanser **01**
  * **ABAP + Java system**: **ASCS** antal instanser **00** och **SCS** antal instanser **01**

  Om du vill använda instans siffror än 00 för ASCS ABAP-instansen och 01 för Java SCS-instans, måste du ändra Azure intern load balancer standard reglerna för belastningsutjämning, som beskrivs i [ändra ASCS/SCS standard reglerna för belastningsutjämning för intern Azure belastningsutjämnare][sap-ha-guide-8.9].

Nästa några uppgifter beskrivs inte i dokumentationen för standard SAP-installation.

> [!NOTE]
> SAP-installationsdokumentationen beskriver hur du installerar den första ASCS/SCS-klusternoden.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändra SAP-profilen för ASCS/SCS-instans

Du måste lägga till en ny profil-parameter. Profilparametern förhindrar att anslutningar mellan SAP arbetsprocesser och placera servern stängs när de är inaktiva för länge. Vi har nämnt det scenariot problem i [lägga till registervärden på båda klusternoderna för SAP ASCS/SCS-instans][sap-ha-guide-8.11]. I det här avsnittet lanserat vi också två ändringar till vissa grundläggande TCP/IP-anslutningsparametrar. I ett andra steg du måste ange att placera servern kan skicka en `keep_alive` signalera så att anslutningarna inte når Azure interna belastningsutjämnarens inaktiv tröskelvärdet.

Ändra SAP-profilen för ASCS/SCS-instans:

1.  Lägg till den här profilparametern i profilen för SAP ASCS/SCS-instans:

  ```
  enque/encni/set_so_keepalive = true
  ```
  I vårt exempel är sökvägen:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Till exempel till SAP SCS-instans profil och motsvarande sökväg:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Om du vill tillämpa ändringarna, SAP ASCS /SCS-instansen startas om.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Lägg till en avsökningsport

Använd funktioner för avsökning av den interna belastningsutjämnaren för att se hela klusterkonfigurationen arbeta med Azure Load Balancer. Intern Azure belastningsutjämnare distribuerar vanligtvis inkommande belastningen jämnt mellan deltagande virtuella datorer. Men fungerar det inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiva och kan inte acceptera någon av arbetsbelastningen. Det hjälper dig att en avsökning-funktionalitet när intern Azure belastningsutjämnare tilldelar fungerar endast för en aktiv instans. Med funktionen avsökningen kan den interna belastningsutjämnaren identifiera vilka instanser som är aktiva och sedan rikta endast instansen med arbetsbelastningen.

Lägga till en avsökningsport:

1.  Kontrollera aktuellt **ProbePort** inställningen genom att köra följande PowerShell-kommando. Köra den från inom någon av de virtuella datorerna i klusterkonfigurationen.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Definiera en avsökningsport. Standardporten för avsökning är **0**. I vårt exempel använder vi avsökningsporten **62000**.

  ![Bild 58: Avsökningsporten för kluster-konfiguration är 0 som standard][sap-ha-guide-figure-3048]

  _**Bild 58:** Klustret configuration avsökningen standardporten är 0_

  Portnumret är definierad i SAP Azure Resource Manager-mallar. Du kan tilldela portnumret i PowerShell.

  Ange ett nytt ProbePort värde för den **SAP <*SID*> IP** klusterresursen genom att köra följande PowerShell-skript. Uppdatera PowerShell-variabler för din miljö. När skriptet har körts uppmanas du att starta om klustergrupp SAP för att aktivera ändringarna.

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

  När du tar den **SAP <*SID*>** rollen online-kluster, kontrollerar du att **ProbePort** är inställt på det nya värdet.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Bild 59: Avsökningsport klustret när du ställer in det nya värdet][sap-ha-guide-figure-3049]

  _**Bild 59:** Avsökningsport klustret när du ställer in det nya värdet_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öppna avsökningsporten för Windows-brandväggen

Du behöver öppna en Windows-brandväggen avsökningsport på båda klusternoderna. Använd följande skript för att öppna en Windows-brandväggen avsökningsporten. Uppdatera PowerShell-variabler för din miljö.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

Den **ProbePort** är inställd på **62000**. Nu kan du komma åt filresursen  **\\\ascsha-clsap\sapmnt** från andra värdar exempel från **ascsha databasadministratörer**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installera database-instans

Följ processen som beskrivs i dokumentationen för SAP-installation för att installera databasinstansen.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installera den andra noden i klustret

Följ stegen i installationsguiden för SAP för att installera det andra klustret.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Ändra starttypen för SAP ÄNDARE Windows-tjänstinstans

Ändra starttypen för tjänsten SAP ÄNDARE Windows **automatiskt (förskjuten Start)** på båda klusternoderna.

![Bild 60: Ändra tjänsttyp för SAP ÄNDARE-instansen till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Bild 60:** Ändra tjänsttyp för SAP ÄNDARE-instansen till fördröjd automatisk_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installera den primära programservern för SAP

Installera den primära Application Server (PROVIDERADRESSER)-instansen <*SID*> - di - 0 på den virtuella datorn som du har angett som värd för PROVIDERADRESSERNA. Det finns inga beroenden på Azure eller DataKeeper-specifika inställningar.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installera ytterligare programserver för SAP

Installera en SAP ytterligare Application Server (AAS) på alla virtuella datorer som du har angett för att köra en instans av SAP-programservern. Till exempel på <*SID*> - di - 1 till <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Detta avslutar installationen av ett system för SAP NetWeaver av hög tillgänglighet. Fortsätt sedan med testning av redundans.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testa redundans för SAP ASCS/SCS-instans och SIOS replikering
Det är enkelt att testa och övervaka en växling vid fel för SAP ASCS/SCS-instans och SIOS diskreplikering med hjälp av verktyg för hanteraren för redundanskluster och SIOS DataKeeper hanteringen och konfigurationen.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS-instans som körs på nod A

Den **SAP PR1** klustergrupp körs på noden i klustret A. Till exempel på **pr1-ascs-0**. Tilldela den dela diskenheten S, vilket är en del av den **SAP PR1** klustergrupp och som använder ASCS/SCS-instans i klustret nod A.

![Bild 61: Hanteraren för redundanskluster: Klustergrupp SAP < SID > körs på nod A][sap-ha-guide-figure-5000]

_**Bild 61:** Hanteraren för redundanskluster: SAP <*SID*> klustergrupp körs på nod A_

I verktyget SIOS DataKeeper hanterings- och kan du se att delad disk-data replikeras synkront i från volymen källenheten S på nod A till volymen målenheten S på klusternod B. Till exempel de replikeras från **pr1-ascs-0 [10.0.0.40]** till **pr1-ascs-1 [10.0.0.41]**.

![Bild 62: I SIOS DataKeeper replikerar du lokala volymen från klusternoden A till klusternod B][sap-ha-guide-figure-5001]

_**Bild 62:** I SIOS DataKeeper replikerar du lokala volymen från klusternoden A till klusternod B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Redundansväxling från A-nod till nod B

1.  Välj något av dessa alternativ för att påbörja en växling av SAP <*SID*> klustergrupp från klusternoden A till klusternod B:
  - Använda hanteraren för redundanskluster  
  - Använd PowerShell för Failover-kluster

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Starta om klustret nod A i Windows-gästoperativsystemet (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från A-nod till nod B).  
3.  Starta om klustret nod A från Azure-portalen (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från A-nod till nod B).  
4.  Starta om klustret nod A med hjälp av Azure PowerShell (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från A-nod till nod B).

  Efter en redundansväxling, SAP <*SID*> klustergrupp körs på noden i klustret B. Till exempel den körs på **pr1-ascs-1**.

  ![Bild 63: I Klusterhanteraren klustergrupp SAP < SID > körs på nod B][sap-ha-guide-figure-5002]

  _**Bild 63**: I hanteraren för redundanskluster, SAP <*SID*> klustergrupp körs på nod B_

  Den delade disken är nu monterad på klustret noden B. SIOS DataKeeper replikera data från volymen källenhet S på nod B till volymen målenheten S på klusternod A. Till exempel den replikerar från **pr1-ascs-1 [10.0.0.41]** till **pr1-ascs-0 [10.0.0.40]**.

  ![Bild 64: SIOS DataKeeper replikerar den lokala volymen från klusternoden B ska ingå i klustret nod A][sap-ha-guide-figure-5003]

  _**Bild 64:** SIOS DataKeeper replikerar den lokala volymen från klusternoden B ska ingå i klustret nod A_
