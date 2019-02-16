---
title: Azure Virtual Machines-distribution för SAP NetWeaver | Microsoft Docs
description: Lär dig mer om att distribuera SAP-program på Linux-datorer i Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: 6eb81e7fff8df547f905e169ac72a78848d2583f
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330774"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines-distribution för SAP NetWeaver

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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS-distribution för SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Cachelagring för virtuella datorer och virtuella hårddiskar)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Programvaru-RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktur för en RDBMS-distribution)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hög tillgänglighet och katastrofåterställning återställning med virtuella Azure-datorer)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 och senare)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 och tidigare versioner)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Med hjälp av en SQL Server-avbildning från Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Allmänt SQL Server för SAP på Azure sammanfattning)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifik information skrivs till SQLServer RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Lagringskonfiguration)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Säkerhetskopiering och återställning)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Prestandaöverväganden för säkerhetskopiering och återställning)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Andra)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (Azure Virtual Machines-distribution för SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-resurser)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuera en virtuell dator med hjälp av en anpassad avbildning)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Flytta en virtuell dator från en lokal plats med en icke-generaliserad Azure i virtuell Hårddisk med SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Distributionsscenarier för virtuella datorer för SAP på Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuera Azure PowerShell-cmdlets)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Hämta och importera SAP-relevanta PowerShell-cmdletar)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ansluta en virtuell dator till en lokal domän - endast Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Hämta, installera och aktivera Azure VM-Agent)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurera Azure förbättrad Övervakningstillägget för SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Beredskapskontrollen för Azure förbättrad övervakning för SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Hälsokontrollen för Azure-övervakning infrastrukturen)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Felsöka Azure övervakning för SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurera övervakning)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurera proxyn)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontroller och felsökning för att konfigurera övervakning av slutpunkt till slutpunkt)

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

[msdn-set-azurermvmaemextension]:https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmaemextension

[planning-guide]:planning-guide.md (Azure virtuella datorer, planering och implementering av SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resurser)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hög tillgänglighet och katastrofåterställning återställning för SAP NetWeaver på Azure virtuella datorer som körs)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hög tillgänglighet för SAP-programservrar)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Med hjälp av Autostart för SAP-instanser)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Molnbaserad - distributioner av virtuella datorer i Azure utan beroenden på den lokala kundens nätverk)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Flera platser – distribution av en eller flera SAP-datorer i Azure som är helt integrerat med det lokala nätverket)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-regioner)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Feldomäner)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uppgradera domäner)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azures tillgänglighetsuppsättningar)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept för Microsoft Azure-datorer)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuera en virtuell dator med en viss kund-avbildning)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Förberedelser för att distribuera en virtuell dator med en viss kund-avbildning för SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Förbereda virtuella datorer med SAP för Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Skillnaden mellan en Azure-disk och en Azure-avbildning)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Ladda upp en VHD från en lokal plats till Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopierar diskar mellan Azure Storage-konton)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD-strukturen för SAP-distributioner)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ange automount för anslutna diskar)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Enstaka virtuell dator med SAP NetWeaver demo/utbildning scenario)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Begreppet molnbaserad distribution av SAP-instanser)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure övervakningslösning för SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Managed Disks)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure-nätverk)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Lagring: Microsoft Azure Storage- och datadiskar)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP produkten tillgänglighet matris)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Hantera virtuella datorer med hjälp av Azure Resource Manager och PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines är lösningen för organisationer som behöver beräknings- och lagringsresurser på minimal tid och utan sätt långa anskaffningscykler cykler. Du kan använda Azure Virtual Machines för att distribuera klassisk program, till exempel SAP NetWeaver-baserade program i Azure. Utöka programmets tillförlitlighet och tillgänglighet utan ytterligare lokala resurser. Azure-datorer stöder flera platsanslutningar, så att du kan integrera Azure-datorer i organisationens lokala domäner, privata moln och SAP-landskap som system.

I den här artikeln beskriver vi hur du distribuerar SAP-program på virtuella datorer (VM) i Azure, inklusive alternativa distributionsalternativ och felsökning. Den här artikeln bygger på informationen i [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide]. Den kompletterar dokumentationen för installation av SAP och SAP Notes, vilka är de viktigaste resurserna för att installera och distribuera SAP-program.

## <a name="prerequisites"></a>Förutsättningar

Konfigurera en Azure-dator för distribution av SAP omfattar flera steg och resurser. Innan du börjar bör du kontrollera att du uppfyller kraven för att installera SAP-program på virtuella datorer i Azure.

### <a name="local-computer"></a>Lokal dator

För att hantera Windows- eller Linux-datorer, kan du använda ett PowerShell-skript och Azure-portalen. För båda verktygen behöver du en lokal dator som kör Windows 7 eller en senare version av Windows. Om du vill hantera Linux-datorer och du vill använda en Linux-dator för den här uppgiften, kan du använda Azure CLI.

### <a name="internet-connection"></a>Internet-anslutning

Om du vill ladda ned och kör verktyg och skript som krävs för distribution av programvara för SAP, måste du vara ansluten till Internet. Den virtuella Azure-datorer som kör Azure förbättrad övervakning av tillägget för SAP måste också åtkomst till Internet. Om den virtuella Azure-datorn är en del av en Azure-nätverk eller en lokal domän, kontrollerar du att proxyinställningarna är inställda, enligt beskrivningen i [konfigurerar du proxyservern][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-prenumeration

Du behöver ett Azure-konto.

### <a name="topology-and-networking"></a>Topologi och nätverk

Du behöver definiera topologi och arkitektur för SAP-distributionen i Azure:

* Azure storage-konton som ska användas
* Virtuellt nätverk där du vill distribuera SAP-system
* Resursgruppen som du vill distribuera SAP-system
* Azure-region där du vill distribuera SAP-system
* SAP-konfigurationen (två eller tre nivåer)
* VM-storlekar och antalet diskar som ytterligare data som ska monteras till de virtuella datorerna
* SAP-korrigering och Transport System (CTS)

Skapa och konfigurera Azure storage-konton (vid behov) eller Azure-nätverk innan du börjar Distributionsprocess för programvara för SAP. Information om hur du skapar och konfigurerar de här resurserna finns i [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP-storlek

Känna till följande information för SAP storlek:

* Planerat SAP-arbetsbelastningar, till exempel med hjälp av verktyget SAP snabb Ange storlek och de SAP-program prestanda Standard (SAP) nummer
* Nödvändiga CPU-resurs- och minnesförbrukningen av SAP-system
* Nödvändiga indata/utdata (I/O)-åtgärder per sekund
* Krävs bandbredd på eventuell kommunikation mellan virtuella datorer i Azure
* Nödvändig nätverksbandbredd mellan lokala resurser och Azure-distribuerade SAP-system

### <a name="resource-groups"></a>Resursgrupper

I Azure Resource Manager kan du använda resursgrupper för att hantera alla resurser som programmet i Azure-prenumerationen. Mer information finns i [översikt över Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Resurser

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-resurser

När du ställer in din SAP-programvarudistribution, behöver du följande SAP-resurser:

* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program
  * Viktiga kapacitetsinformation för Azure VM-storlekar
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databas
  * Nödvändiga SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP-kommentar [2015553] visar en lista över kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2178632] mer information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [1409604] har Värdagenten för SAP-version som krävs för Windows i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP-kommentar [2002167] har allmän information om Red Hat Enterprise Linux 7.x.
* SAP-kommentar [2069760] har allmän information om Oracle Linux 7.x.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* SAP-kommentar [1597355] har allmän information om växlingsutrymme i procent för Linux.
* [SAP på Azure Tillståndsändringsavisering sidan](https://wiki.scn.sap.com/wiki/x/Pia7Gg) har nyheter och en samling av användbara resurser.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* SAP-specifika PowerShell-cmdletar som ingår i [Azure PowerShell][azure-ps].
* SAP-specifika Azure CLI-kommandon som ingår i [Azure CLI][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-resurser

Dessa Microsoft-artiklar beskriver SAP-distributioner i Azure:

* [Azure virtuella datorer, planering och implementering av SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Distributionsscenarier för SAP-program på Azure Virtual Machines

Du har flera alternativ för att distribuera virtuella datorer och associerade diskar i Azure. Det är viktigt att förstå skillnaderna mellan distributionsalternativen, eftersom du kan ta olika steg för att förbereda dina virtuella datorer för distribution av baserat på vilken distributionstyp som du väljer.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP

Du kan använda en avbildning av Microsoft eller tredje part i Azure Marketplace för att distribuera den virtuella datorn. Marketplace erbjuder vissa standard OS-avbildningar av Windows Server och olika Linux-distributioner. Du kan även distribuera en avbildning som innehåller databashantering system (DBMS) SKU: er, till exempel Microsoft SQL Server. Mer information om hur du använder avbildningar med DBMS SKU: er finns i [DBMS i Azure Virtual Machines-distribution för SAP NetWeaver][dbms-guide].

I följande flödesschema visas de SAP-specifika stegen för att distribuera en virtuell dator från Azure Marketplace:

![Flödesdiagram för distribution av virtuella datorer för SAP-system med hjälp av en VM-avbildning från Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure portal

Det enklaste sättet att skapa en ny virtuell dator med en avbildning från Azure Marketplace är med hjälp av Azure portal.

1.  Gå till <https://portal.azure.com/#create/hub>.  Eller i Azure portal-menyn, Välj **+ ny**.
1.  Välj **Compute**, och välj sedan typ av operativsystem som du vill distribuera. Till exempel Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) eller Oracle Linux 7.2. Standardlistvyn visar inte alla operativsystem som stöds. Välj **se alla** en fullständig lista. Mer information om operativsystem som stöds för SAP-programdistribution finns i SAP-kommentar [1928533].
1.  Granska villkoren på nästa sida.
1.  I den **Välj en distributionsmodell** väljer **Resource Manager**.
1.  Välj **Skapa**.

Guiden vägleder dig genom att ange de obligatoriska parametrarna för att skapa den virtuella datorn, förutom att alla nödvändiga resurser, t.ex. nätverksgränssnitt och storage-konton. Några av dessa parametrar är:

1. **Grunderna**:
 * **Namn**: Namnet på resursen (virtuella datornamn).
 * **Typ av virtuell datordisk**: Välj typ av disk av OS-disken. Om du vill använda Premium Storage för din datadiskar bör du använda Premium Storage för OS-disken.
 * **Användarnamn och lösenord** eller **offentlig SSH-nyckel**: Ange användarnamnet och lösenordet för den användare som har skapats under etableringen. För en Linux-dator, kan du ange den offentliga nyckeln för SSH (Secure Shell) som används för att logga in på datorn.
 * **Prenumeration**: Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
 * **Resursgrupp**: Namnet på resursgruppen för den virtuella datorn. Du kan ange namnet på en ny resursgrupp eller namnet på en resursgrupp som redan finns.
 * **Plats**: Var du vill distribuera den nya virtuella datorn. Om du vill ansluta den virtuella datorn till ditt lokala nätverk kan du kontrollera att du väljer plats för det virtuella nätverket som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure-nätverk] [ planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planering och implementering av SAP NetWeaver] [ planning-guide].
1. **Storlek**:

     En lista över VM-typer som stöds finns i SAP-kommentar [1928533]. Var noga med att du väljer rätt typ av virtuell dator om du vill använda Azure Premium Storage. Inte alla typer av virtuella datorer stöd för Premium Storage. Mer information finns i [lagring: Microsoft Azure Storage- och datadiskar] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Premium Storage] [ planning-guide-azure-premium-storage] i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide].

1. **Inställningar för**:
  * **Storage**
    * **Disktyp**: Välj typ av disk av OS-disken. Om du vill använda Premium Storage för din datadiskar bör du använda Premium Storage för OS-disken.
    * **Använda hanterade diskar**: Välj Ja om du vill använda Managed Disks. Mer information om Managed Disks finns i kapitlet [Managed Disks] [ planning-guide-managed-disks] i Planeringsguiden.
    * **Storage-konto**: Välj ett befintligt lagringskonto eller skapa en ny. Inte alla lagringstyper av fungerar för att köra SAP-program. Mer information om lagringskontotyper finns i [lagringsstrukturen för en virtuell dator för RDBMS distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
  * **Nätverk**
    * **Virtuellt nätverk** och **undernät**: Välj det virtuella nätverket som är ansluten till ditt lokala nätverk om du vill integrera den virtuella datorn med intranätet.
    * **Offentlig IP-adress**: Välj den offentliga IP-adressen som du vill använda eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att få åtkomst till den virtuella datorn via Internet. Se till att du också skapa en nätverkssäkerhetsgrupp för att säkra åtkomsten till den virtuella datorn.
    * **Nätverkssäkerhetsgrupp**: Mer information finns i [styra flödet i nätverkstrafiken med nätverkssäkerhetsgrupper][virtual-networks-nsg].
  * **Tillägg**: Du kan installera tillägg för virtuell dator genom att läggas till i distributionen. Du behöver inte lägga till tillägg i det här steget. De tillägg som krävs för SAP support installeras senare. Se kapitel [konfigurera Azure förbättrad övervakning av tillägget för SAP] [ deployment-guide-4.5] i den här guiden.
  * **Hög tillgänglighet**: Välj en tillgänglighetsuppsättning eller ange parametrar för att skapa en ny tillgänglighetsuppsättning. Mer information finns i [Azures tillgänglighetsuppsättningar][planning-guide-3.2.3].
  * **Övervakning**
    * **Starta diagnostik**: Du kan välja **inaktivera** för startdiagnostik.
    * **Diagnostik för Gästoperativsystem**: Du kan välja **inaktivera** för övervakning av diagnostik.

1. **Sammanfattning av**:

  Granska dina val och väljer sedan **OK**.

Den virtuella datorn har distribuerats i resursgruppen som du har valt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Du kan skapa en virtuell dator med hjälp av någon av de SAP-mallarna publicerats i den [GitHub-lagringsplatsen för azure-snabbstartsmallar][azure-quickstart-templates-github]. Du kan också manuellt skapa en virtuell dator med hjälp av den [Azure-portalen][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], eller [Azure CLI] [virtual-machines-linux-tutorial].

* [**Tvålagers-konfigurationsmall (endast en virtuell dator)** (sap-2-nivå-marketplace-avbildning)][sap-templates-2-tier-marketplace-image]

  Använd den här mallen om du vill skapa en tvålagers-system med hjälp av enbart en virtuell dator.
* [**Tvålagers-(endast en virtuell dator) konfigurationsmall – Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Använd den här mallen om du vill skapa en tvålagers-system genom att använda endast en virtuell dator och Managed Disks.
* [**(Flera virtuella datorer) för trelagers-konfigurationsmall** (sap-3-nivå-marketplace-avbildning)][sap-templates-3-tier-marketplace-image]

  Använd den här mallen om du vill skapa en trelagers-system med hjälp av flera virtuella datorer.
* [**Trelagers-(flera virtuella datorer) konfigurationsmall – Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Använd den här mallen om du vill skapa en trelagers-system med hjälp av flera virtuella datorer och hanterade diskar.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna**:
  * **Prenumeration**: Prenumerationen du använder för att distribuera mallen.
  * **Resursgrupp**: Resursgruppen som du använder för att distribuera mallen. Du kan skapa en ny resursgrupp eller du kan välja en befintlig resursgrupp i prenumerationen.
  * **Plats**: Var du vill distribuera mallen. Om du har valt en befintlig resursgrupp används för platsen för resursgruppen.

1. **Inställningar för**:
  * **SAP System-ID**: SAP System-ID (SID).
  * **OS-typ**: Operativsystem som du vill distribuera, till exempel Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) eller Oracle Linux 7.2.

    Listvyn visas inte alla operativsystem som stöds. Mer information om operativsystem som stöds för SAP-programdistribution finns i SAP-kommentar [1928533].
  * **Storlek för SAP-system**: Storleken på SAP-system.

    Antal SAP som innehåller det nya systemet. Om du inte vet hur många SAP kräver att systemet, be din SAP-teknikpartner eller systemintegratör.
  * **Systemets tillgänglighet** (endast trelagers-mall): Systemets tillgänglighet.

    Välj **HA** för en konfiguration som passar för en installation för hög tillgänglighet. Två databasservrar och två servrar för ABAP SAP Central Services (ASCS) skapas.
  * **Lagringstyp** (endast tvålagers-mall): Typ av lagring du använder.

    För stora system vi rekommenderar starkt att använda Azure Premium Storage. Mer information om lagringskontotyper finns i följande källor:
      * [Användning av Azure Premium SSD-lagringen för SAP DBMS-instans][2367194]
      * [Lagringsstrukturen för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på Azure virtuella datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
  * **Administratörens användarnamn** och **adminlösenord**: Ett användarnamn och lösenord.
    En ny användare skapas för att logga in till den virtuella datorn.
  * **Nytt eller befintligt undernät**: Anger om ett nytt virtuellt nätverk och undernät skapas eller ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
  * **Undernät-ID**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så här: /subscriptions/&lt;prenumerations-id > /resourceGroups/&lt;resursgruppens namn > /providers/Microsoft.Network/virtualNetworks/&lt;virtuellt nätverksnamn > /subnets/&lt;undernätets namn >

1. **Allmänna villkor**:  
    Granska och Godkänn de juridiska villkoren.

1.  Välj **Köp**.

Azure VM-agenten distribueras som standard när du använder en avbildning från Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan behöva du konfigurera proxy på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute, den virtuella datorn kanske inte kan få åtkomst till Internet och kommer inte att hämta nödvändiga tillägg eller samla in övervakningsdata. Mer information finns i [konfigurerar du proxyservern][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure VPN-anslutning för plats-till-plats eller ExpressRoute (Detta kallas *mellan lokala* i [planering för Azure Virtual Machines och implementering av SAP NetWeaver][planning-guide]), det förväntas att den virtuella datorn ska anslutas till en lokal domän. Mer information om överväganden för den här uppgiften finns [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurera övervakning

Att SAP har stöd för din miljö, ställa in Azure Monitoring-tillägg för SAP, enligt beskrivningen i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5]. Kontrollera krav för övervakning av SAP och nödvändiga minimiversioner av SAP Kernel och SAP Värdagenten, i resurserna som anges i [SAP resurser][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Övervakning av kontroll

Kontrollera om fungerar övervakning, enligt beskrivningen i [kontroller och felsökning för att konfigurera övervakning av slutpunkt till slutpunkt][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Steg efter distribution

När du skapar den virtuella datorn och den virtuella datorn har distribuerats, måste du installera de nödvändiga programvarukomponenter i den virtuella datorn. På grund av installation distribution/programvara sekvensen i den här typen av distribution av virtuella datorer, måste programvara ska kunna installeras redan finnas, antingen i Azure, på en annan virtuell dator eller som en disk som kan kopplas. Eller så kan du överväga att använda ett scenario med flera platser i vilka anslutningen till lokalerna tillgångar (Installationsresurser) ges.

Följ samma riktlinjer och verktyg för att installera SAP-program på den virtuella datorn precis som i en lokal miljö när du har distribuerat den virtuella datorn i Azure. Om du vill installera SAP-program på en Azure VM, rekommenderar både SAP och Microsoft att du överföra och lagra installationsmediet SAP på virtuella Azure-hårddiskar eller Managed Disks, eller som du skapar en Azure-dator som fungerar som en filserver som har alla nödvändiga SAP installationsmediet.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP

Eftersom olika versioner av ett operativsystem eller DBMS har olika krav, kan de avbildningar som du hittar i Azure Marketplace inte uppfyller dina behov. Du kan i stället vill skapa en virtuell dator genom att använda din egen OS/DBMS VM-avbildning, vilka du kan distribuera igen senare.
Du kan använda olika steg för att skapa en privat avbildning för Linux än om du vill skapa en för Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> För att förbereda en Windows-avbildning som du kan använda för att distribuera flera virtuella datorer, vara Windows-inställningar (t.ex. Windows-SID och värdnamn) sådant eller generaliserad på den lokala virtuella datorn. Du kan använda [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) att göra detta.
>
> ![Linux][Logo_Linux] Linux
>
> För att förbereda en Linux-avbildning som du kan använda för att distribuera flera virtuella datorer, måste vissa Linux-inställningar sådant eller generaliserad på den lokala virtuella datorn. Du kan använda `waagent -deprovision` att göra detta. Mer information finns i [avbilda en Linux-dator som körs på Azure] [ virtual-machines-linux-capture-image] och [Användarguide för Azure Linux agent][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Du kan förbereda och skapa en anpassad avbildning och sedan använda den för att skapa flera nya virtuella datorer. Detta beskrivs i [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide]. Ställa in innehållet i databasen med hjälp av SAP Software etablering Manager för att installera en ny SAP-system (återställer en säkerhetskopia av databasen från en disk som är kopplad till den virtuella datorn) eller genom att återställa en säkerhetskopia av databasen direkt från Azure storage, om din DBMS stöder den. Mer information finns i [DBMS i Azure Virtual Machines-distribution för SAP NetWeaver][dbms-guide]. Om du redan har installerat ett SAP-system på den lokala virtuella datorn (särskilt för tvålagers-system), kan du anpassa SAP-systeminställningar efter distributionen av den virtuella Azure-datorn med hjälp av proceduren för Byt namn på System som stöds av SAP Software etablering Manager (SAP Observera [1619720]). Annars kan installera du SAP-program när du har distribuerat Azure VM.

I följande flödesschema visas de SAP-specifika stegen för att distribuera en virtuell dator från en anpassad avbildning:

![Flödesdiagram för distribution av virtuella datorer för SAP-system med hjälp av en VM-avbildning i privata Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure portal

Det är det enklaste sättet att skapa en ny virtuell dator från en avbildning av en hanterad Disk med hjälp av Azure portal. Mer information om hur du skapar en hantera diskavbildning [avbilda en hanterad avbildning av en generaliserad virtuell dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Gå till <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Eller i Azure portal-menyn, Välj **avbildningar**.
1.  Välj en hanterad Disk-avbildning som du vill distribuera och klicka på **Skapa virtuell dator**

Guiden vägleder dig genom att ange de obligatoriska parametrarna för att skapa den virtuella datorn, förutom att alla nödvändiga resurser, t.ex. nätverksgränssnitt och storage-konton. Några av dessa parametrar är:

1. **Grunderna**:
 * **Namn**: Namnet på resursen (virtuella datornamn).
 * **Typ av virtuell datordisk**: Välj typ av disk av OS-disken. Om du vill använda Premium Storage för din datadiskar bör du använda Premium Storage för OS-disken.
 * **Användarnamn och lösenord** eller **offentlig SSH-nyckel**: Ange användarnamnet och lösenordet för den användare som har skapats under etableringen. För en Linux-dator, kan du ange den offentliga nyckeln för SSH (Secure Shell) som används för att logga in på datorn.
 * **Prenumeration**: Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
 * **Resursgrupp**: Namnet på resursgruppen för den virtuella datorn. Du kan ange namnet på en ny resursgrupp eller namnet på en resursgrupp som redan finns.
 * **Plats**: Var du vill distribuera den nya virtuella datorn. Om du vill ansluta den virtuella datorn till ditt lokala nätverk kan du kontrollera att du väljer plats för det virtuella nätverket som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure-nätverk] [ planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planering och implementering av SAP NetWeaver] [ planning-guide].
1. **Storlek**:

     En lista över VM-typer som stöds finns i SAP-kommentar [1928533]. Var noga med att du väljer rätt typ av virtuell dator om du vill använda Azure Premium Storage. Inte alla typer av virtuella datorer stöd för Premium Storage. Mer information finns i [lagring: Microsoft Azure Storage- och datadiskar] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Premium Storage] [ planning-guide-azure-premium-storage] i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide].

1. **Inställningar för**:
  * **Storage**
    * **Disktyp**: Välj typ av disk av OS-disken. Om du vill använda Premium Storage för din datadiskar bör du använda Premium Storage för OS-disken.
    * **Använda hanterade diskar**: Välj Ja om du vill använda Managed Disks. Mer information om Managed Disks finns i kapitlet [Managed Disks] [ planning-guide-managed-disks] i Planeringsguiden.
  * **Nätverk**
    * **Virtuellt nätverk** och **undernät**: Välj det virtuella nätverket som är ansluten till ditt lokala nätverk om du vill integrera den virtuella datorn med intranätet.
    * **Offentlig IP-adress**: Välj den offentliga IP-adressen som du vill använda eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att få åtkomst till den virtuella datorn via Internet. Se till att du också skapa en nätverkssäkerhetsgrupp för att säkra åtkomsten till den virtuella datorn.
    * **Nätverkssäkerhetsgrupp**: Mer information finns i [styra flödet i nätverkstrafiken med nätverkssäkerhetsgrupper][virtual-networks-nsg].
  * **Tillägg**: Du kan installera tillägg för virtuell dator genom att läggas till i distributionen. Du behöver inte att lägga till tillägget i det här steget. De tillägg som krävs för SAP support installeras senare. Se kapitel [konfigurera Azure förbättrad övervakning av tillägget för SAP] [ deployment-guide-4.5] i den här guiden.
  * **Hög tillgänglighet**: Välj en tillgänglighetsuppsättning eller ange parametrar för att skapa en ny tillgänglighetsuppsättning. Mer information finns i [Azures tillgänglighetsuppsättningar][planning-guide-3.2.3].
  * **Övervakning**
    * **Starta diagnostik**: Du kan välja **inaktivera** för startdiagnostik.
    * **Diagnostik för Gästoperativsystem**: Du kan välja **inaktivera** för övervakning av diagnostik.

1. **Sammanfattning av**:

  Granska dina val och väljer sedan **OK**.

Den virtuella datorn har distribuerats i resursgruppen som du har valt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Använd någon av följande SAP-mallar för att skapa en distribution med hjälp av en privat OS-avbildning från Azure-portalen. Dessa mallar har publicerats i den [GitHub-lagringsplatsen för azure-snabbstartsmallar][azure-quickstart-templates-github]. Du kan också manuellt skapa en virtuell dator med hjälp av [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Tvålagers-konfigurationsmall (endast en virtuell dator)** (sap-2-nivå-användare-bild)][sap-templates-2-tier-user-image]

  Använd den här mallen om du vill skapa en tvålagers-system med hjälp av enbart en virtuell dator.
* [**Tvålagers-(endast en virtuell dator) konfigurationsmall - hanterade diskavbildning** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Använd den här mallen om du vill skapa en tvålagers-system med hjälp av enbart en virtuell dator och en avbildning av en hanterad Disk.
* [**(Flera virtuella datorer) för trelagers-konfigurationsmall** (sap-3-nivå-användare-bild)][sap-templates-3-tier-user-image]

  Använd den här mallen om du vill skapa en trelagers-system med hjälp av flera virtuella datorer eller en egen OS-avbildning.
* [**Trelagers-(flera virtuella datorer) konfigurationsmall - hanterade diskavbildning** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Använd den här mallen om du vill skapa en trelagers-system med hjälp av flera virtuella datorer eller en egen OS-avbildning och en avbildning av en hanterad Disk.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna**:
  * **Prenumeration**: Prenumerationen du använder för att distribuera mallen.
  * **Resursgrupp**: Resursgruppen som du använder för att distribuera mallen. Du kan skapa en ny resursgrupp eller välj en befintlig resursgrupp i prenumerationen.
  * **Plats**: Var du vill distribuera mallen. Om du har valt en befintlig resursgrupp används för platsen för resursgruppen.
1. **Inställningar för**:
  * **SAP System-ID**: SAP System-ID.
  * **OS-typ**: Den typ av operativsystem som du vill distribuera (Windows eller Linux).
  * **Storlek för SAP-system**: Storleken på SAP-system.

    Antal SAP som innehåller det nya systemet. Om du inte vet hur många SAP kräver att systemet, be din SAP-teknikpartner eller systemintegratör.
  * **Systemets tillgänglighet** (endast trelagers-mall): Systemets tillgänglighet.

    Välj **HA** för en konfiguration som passar för en installation för hög tillgänglighet. Två databasservrar och två servrar för ASCS skapas.
  * **Lagringstyp** (endast tvålagers-mall): Typ av lagring du använder.

    För stora system vi rekommenderar starkt att använda Azure Premium Storage. Mer information om lagringskontotyper finns i följande resurser:
      * [Användning av Azure Premium SSD-lagringen för SAP DBMS-instans][2367194]
      * [Lagringsstrukturen för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på Azure virtuella datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
  * **Användaravbildningen VHD-URI** (endast ohanterad disk bild mall): URI för den privata OS bild virtuell Hårddisk, till exempel https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Användaren avbildningens lagringskonto** (endast ohanterad disk bild mall): Namnet på det lagringskonto där den privata OS-avbildningen lagras, till exempel &lt;accountname > i https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (endast hanterade diskar bild mall): ID för den hanterade diskavbildningen som du vill använda
  * **Administratörens användarnamn** och **adminlösenord**: Det användarnamn och lösenord.

    En ny användare skapas för att logga in till den virtuella datorn.
  * **Nytt eller befintligt undernät**: Anger om ett nytt virtuellt nätverk och undernät skapas eller ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
  * **Undernät-ID**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så här: /subscriptions/&lt;prenumerations-id > /resourceGroups/&lt;resursgruppens namn > /providers/Microsoft.Network/virtualNetworks/&lt;virtuellt nätverksnamn > /subnets/&lt;undernätets namn >

1. **Allmänna villkor**:  
    Granska och Godkänn de juridiska villkoren.

1.  Välj **Köp**.

#### <a name="install-the-vm-agent-linux-only"></a>Installera VM-agenten (endast Linux)

Om du vill använda mallar som beskrivs i föregående avsnitt, Linux-agenten måste vara installerad i användaravbildningen eller distributionen kommer att misslyckas. Ladda ned och installera VM-agenten i användaravbildningen enligt beskrivningen i [hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4]. Om du inte använder mallarna kan kan du också installera VM-agenten senare.

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure VPN-anslutning för plats-till-plats eller Azure ExpressRoute (Detta kallas *mellan lokala* i [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide]), det förväntas att den virtuella datorn ska anslutas till en lokal domän. Mer information om överväganden för det här steget finns i [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan behöva du konfigurera proxy på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute, den virtuella datorn kanske inte kan få åtkomst till Internet och kommer inte att hämta nödvändiga tillägg eller samla in övervakningsdata. Mer information finns i [konfigurerar du proxyservern][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurera övervakning

Att SAP har stöd för din miljö, ställa in Azure Monitoring-tillägg för SAP, enligt beskrivningen i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5]. Kontrollera krav för övervakning av SAP och nödvändiga minimiversioner av SAP Kernel och SAP Värdagenten, i resurserna som anges i [SAP resurser][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Övervakning av kontroll

Kontrollera om fungerar övervakning, enligt beskrivningen i [kontroller och felsökning för att konfigurera övervakning av slutpunkt till slutpunkt][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Flytta en lokal virtuell dator med hjälp av en icke-generaliserad Azure VHD med SAP

I det här scenariot planerar du att flytta ett särskilt SAP-system från en lokal miljö till Azure. Du kan göra detta genom att ladda upp den virtuella Hårddisken som Operativsystemet, SAP-binärfiler och så småningom DBMS-binärfiler, plus de virtuella hårddiskarna med filer för data och loggfiler för DBMS till Azure. Till skillnad från det scenario som beskrivs i [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3], i detta fall använder du behåller värdnamn, SAP-SID och SAP användarkonton i Azure-dator, eftersom de har konfigurerats i den lokala miljön. Du behöver inte att generalisera Operativsystemet. Det här scenariot gäller oftast mellan lokala scenarier där en del av SAP-landskap körs lokalt och en del av den körs på Azure.

I det här scenariot VM-agenten är **inte** automatiskt installerade under distributionen. Eftersom VM-agenten och Azure förbättrad övervakning av tillägget för SAP krävs för att köra SAP NetWeaver på Azure, måste du hämta, installera och aktivera båda komponenterna manuellt när du har skapat den virtuella datorn.

Mer information om Azure VM-agenten finns i följande resurser.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Översikt över Azure VM-agenten][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Användarhandbok för Azure Linux-Agent][virtual-machines-linux-agent-user-guide]
>
>

- - -

Flödesschemat visar stegen för att flytta en lokal virtuell dator med hjälp av en icke-generaliserad Azure-VHD:

![Flödesdiagram för distribution av virtuella datorer för SAP-system med hjälp av en virtuell datordisk][deployment-guide-figure-400]

Om disken redan har laddats upp och definieras i Azure (se [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide]), gör uppgifterna som beskrivs i nästa avsnitt.

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en distribution med hjälp av en privat OS-disk via Azure portal, använda SAP-mallen som publicerats i den [GitHub-lagringsplatsen för azure-snabbstartsmallar][azure-quickstart-templates-github]. Du kan också manuellt skapa en virtuell dator med hjälp av PowerShell.

* [**Tvålagers-konfigurationsmall (endast en virtuell dator)** (sap-2-nivå-användare-disk)][sap-templates-2-tier-os-disk]

  Använd den här mallen om du vill skapa en tvålagers-system med hjälp av enbart en virtuell dator.
* [**Tvålagers-(endast en virtuell dator) konfigurationsmall – hanterad Disk** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Använd den här mallen om du vill skapa en tvålagers-system med hjälp av enbart en virtuell dator och en hanterad Disk.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna**:
  * **Prenumeration**: Prenumerationen du använder för att distribuera mallen.
  * **Resursgrupp**: Resursgruppen som du använder för att distribuera mallen. Du kan skapa en ny resursgrupp eller välj en befintlig resursgrupp i prenumerationen.
  * **Plats**: Var du vill distribuera mallen. Om du har valt en befintlig resursgrupp används för platsen för resursgruppen.
1. **Inställningar för**:
  * **SAP System-ID**: SAP System-ID.
  * **OS-typ**: Den typ av operativsystem som du vill distribuera (Windows eller Linux).
  * **Storlek för SAP-system**: Storleken på SAP-system.

    Antal SAP som innehåller det nya systemet. Om du inte vet hur många SAP kräver att systemet, be din SAP-teknikpartner eller systemintegratör.
  * **Lagringstyp** (endast tvålagers-mall): Typ av lagring du använder.

    För stora system vi rekommenderar starkt att använda Azure Premium Storage. Mer information om lagringskontotyper finns i följande resurser:
      * [Användning av Azure Premium SSD-lagringen för SAP DBMS-instans][2367194]
      * [Lagringsstrukturen för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på Azure virtuella datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
  * **VHD-URI för operativsystemdisk** (endast mall ohanterad disk): URI för den privata OS-disken, till exempel https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Managed Disk-Id på operativsystemdisk** (endast mall hanterade diskar): Id för hanterad Disk OS-disken /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Nytt eller befintligt undernät**: Anger om ett nytt virtuellt nätverk och undernät skapas eller ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är ansluten till ditt lokala nätverk, Välj **befintliga**.
  * **Undernät-ID**: Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har en undernätet som definierades när den virtuella datorn ska tilldelas att namnge ID för det specifika undernätet. ID: T ser oftast ut så här: /subscriptions/&lt;prenumerations-id > /resourceGroups/&lt;resursgruppens namn > /providers/Microsoft.Network/virtualNetworks/&lt;virtuellt nätverksnamn > /subnets/&lt;undernätets namn >

1. **Allmänna villkor**:  
    Granska och Godkänn de juridiska villkoren.

1.  Välj **Köp**.

#### <a name="install-the-vm-agent"></a>Installera VM-agenten

Om du vill använda mallar som beskrivs i föregående avsnitt, VM-agenten måste installeras på OS-disken eller distributionen kommer att misslyckas. Ladda ned och installera VM-agenten på den virtuella datorn, enligt beskrivningen i [hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4].

Om du inte använder mallar som beskrivs i föregående avsnitt, kan du även installera VM-agenten efteråt.

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure VPN-anslutning för plats-till-plats eller ExpressRoute (Detta kallas *mellan lokala* i [planering för Azure Virtual Machines och implementering av SAP NetWeaver][planning-guide]), det förväntas att den virtuella datorn ska anslutas till en lokal domän. Mer information om överväganden för den här uppgiften finns [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan behöva du konfigurera proxy på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute, den virtuella datorn kanske inte kan få åtkomst till Internet och kommer inte att hämta nödvändiga tillägg eller samla in övervakningsdata. Mer information finns i [konfigurerar du proxyservern][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurera övervakning

Att SAP har stöd för din miljö, ställa in Azure Monitoring-tillägg för SAP, enligt beskrivningen i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5]. Kontrollera krav för övervakning av SAP och nödvändiga minimiversioner av SAP Kernel och SAP Värdagenten, i resurserna som anges i [SAP resurser][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Övervakning av kontroll

Kontrollera om fungerar övervakning, enligt beskrivningen i [kontroller och felsökning för att konfigurera övervakning av slutpunkt till slutpunkt][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Uppdatera konfigurationen av övervakningen för SAP

Uppdatera övervakningskonfigurationen för SAP i någon av följande scenarier:
* Gemensamma Microsoft/SAP-teamet utökar övervakningsfunktionerna och begär fler eller färre räknare.
* Microsoft presenterar en ny version av den underliggande Azure-infrastrukturen som levererar övervakningsdata och Azure förbättrad övervakning av tillägget för SAP måste anpassas till dessa ändringar.
* Du har monterat ytterligare datadiskar till din Azure-VM eller du tar bort en datadisk. Uppdatera insamlingen av storage-relaterade data i det här scenariot. Konfigurationen av övervakningen påverkas inte om du ändrar konfigurationen genom att lägga till eller ta bort slutpunkter eller genom att tilldela IP-adresser till en virtuell dator.
* Du kan ändra storleken på din Azure-dator, till exempel från storlek A5 till andra VM-storlek.
* Du kan lägga till nya nätverksgränssnitt till den virtuella Azure-datorn.

Uppdatera inställningarna för övervakning genom att uppdatera den övervaka infrastrukturen genom att följa stegen i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Detaljerad information om SAP-programvarudistribution

Det här avsnittet har detaljerade steg för att göra specifika uppgifter i processen för konfiguration och distribution.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Distribuera Azure PowerShell-cmdlets

1.  Gå till [Microsoft Azure hämtar](https://azure.microsoft.com/downloads/).
1.  Under **kommandoradsverktyg**under **PowerShell**väljer **Windows installerar**.
1.  I dialogrutan Microsoft Download Manager för den nedladdade filen (till exempel WindowsAzurePowershellGet.3f.3f.3fnew.exe), Välj **kör**.
1.  För att köra Microsoft Web Platform Installer (Microsoft Web PI), Välj **Ja**.
1.  En sida som ser ut som det visas:

  ![Installationssidan för Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Välj **installera**, och acceptera licensvillkoren för programvara från Microsoft.
1.  PowerShell har installerats. Välj **Slutför** att stänga installationsguiden.

Efter uppdateringar för PowerShell-cmdletar, som vanligtvis uppdateras varje månad ofta. Det enklaste sättet att söka efter uppdateringar som gör det föregående installation, upp till installationssidan visas i steg 5. Versionsnumret för datum och versionen av cmdletarna finns på sidan som visas i steg 5. Om inget annat anges i SAP-kommentar [1928533] eller SAP-kommentar [2015553], vi rekommenderar att du arbetar med den senaste versionen av Azure PowerShell-cmdlets.

Om du vill kontrollera vilken version av Azure PowerShell-cmdlets som är installerade på datorn, kör du följande PowerShell-kommando:
```powershell
(Get-Module AzureRm.Compute).Version
```
Resultatet ser ut så här:

![Resultatet av versionskontroll för Azure PowerShell-cmdlet][deployment-guide-figure-600]
<a name="figure-6"></a>

Om Azure cmdlet-versionen som installerats på datorn är den aktuella versionen, första sidan i guiden anger den genom att lägga till **(installerat)** till produkttiteln (se följande skärmbild). PowerShell Azure-cmdletar är uppdaterade. Om du vill stänga installationsguiden väljer **avsluta**.

![Installationssidan för Azure PowerShell-cmdlets som indikerar att den senaste versionen av Azure PowerShell-cmdletar har installerats][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuera Azure CLI

1.  Gå till [Microsoft Azure hämtar](https://azure.microsoft.com/downloads/).
1.  Under **kommandoradsverktyg**under **kommandoradsgränssnittet**väljer den **installera** länk för ditt operativsystem.
1.  I dialogrutan Microsoft Download Manager för den nedladdade filen (till exempel WindowsAzureXPlatCLI.3f.3f.3fnew.exe), Välj **kör**.
1.  För att köra Microsoft Web Platform Installer (Microsoft Web PI), Välj **Ja**.
1.  En sida som ser ut som det visas:

  ![Installationssidan för Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Välj **installera**, och acceptera licensvillkoren för programvara från Microsoft.
1.  Azure CLI är installerat. Välj **Slutför** att stänga installationsguiden.

Efter uppdateringar för Azure CLI, som vanligtvis uppdateras varje månad ofta. Det enklaste sättet att söka efter uppdateringar som gör det föregående installation, upp till installationssidan visas i steg 5.

Om du vill kontrollera vilken version av Azure CLI som är installerad på datorn, kör du följande kommando:
```
azure --version
```

Resultatet ser ut så här:

![Resultatet av kontroll av Azure CLI-version][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Ansluta en virtuell dator till en lokal domän (endast Windows)

Om du distribuerar SAP-datorer i ett scenario med flera platser där den lokala Active Directory och DNS har utökats i Azure, förväntas det att de virtuella datorerna ansluter till en lokal domän. Detaljerade anvisningar du vidta för att ansluta en dator till en lokal domän och ytterligare programvara som krävs för att vara medlem i en lokal domän, varierar beroende på kunden. Vanligtvis för att ansluta en virtuell dator till en lokal domän, måste du installera ytterligare programvara, t.ex. program mot skadlig kod och programvara för säkerhetskopiering eller övervakning.

I det här scenariot måste du också se till att Windows lokala System-kontot (S-1-5-18) i den Virtuella gästdatorn om internetproxyinställningarna tvingas när en virtuell dator ansluter till en domän i din miljö, har samma proxyinställningar. Det enklaste alternativet är att tvinga proxyn med hjälp av en domän gruppolicy som gäller för system i domänen.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Hämta, installera och aktivera Azure VM-Agent

För virtuella datorer som distribueras från en OS-avbildning som inte är generaliserad (t.ex, en avbildning som inte härrör från Windows-systemförberedelse eller sysprep-verktyget), som du behöver hämta, installera och aktivera Azure VM-agenten manuellt.

Om du distribuerar en virtuell dator från Azure Marketplace kan det här steget krävs inte. Avbildningar från Azure Marketplace har redan Azure VM-agenten.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1.  Ladda ned Azure VM-agenten:
  1.  Ladda ned den [installationspaketet för Azure VM-agenten](https://go.microsoft.com/fwlink/?LinkId=394789).
  1.  Store VM-agenten MSI-paketet lokalt på en personlig dator eller server.
1.  Installera Azure VM-agenten:
  1.  Anslut till den distribuerade virtuella Azure-datorn genom att använda Remote Desktop Protocol (RDP).
  1.  Öppna en Windows Explorer-fönstret på den virtuella datorn och välj målkatalogen för MSI-filen för VM-agenten.
  1.  Dra Azure VM Agent Installer MSI-filen från din lokala dator/server till målkatalogen för VM-agenten på den virtuella datorn.
  1.  Dubbelklicka på MSI-filen på den virtuella datorn.
1.  För virtuella datorer som är anslutna till den lokala domäner, se till att eventuell internetproxyinställningarna också gäller för kontot Lokalt System i Windows (S-1-5-18) i den virtuella datorn, enligt beskrivningen i [konfigurerar du proxyservern][deployment-guide-configure-proxy]. VM-agenten körs i den här kontexten och måste kunna ansluta till Azure.

Inga åtgärder från användaren krävs för att uppdatera Azure VM-agenten. VM-agenten uppdateras automatiskt och kräver inte en omstart av virtuella datorer.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Använd följande kommandon för att installera VM-agenten för Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) eller Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Om agenten redan är installerad, om du vill uppdatera Azure Linux Agent, gör stegen som beskrivs i [uppdatera Azure Linux Agent på en virtuell dator till den senaste versionen från GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurera proxyn

Vilka steg du utför för att konfigurera proxyn i Windows skiljer sig från hur du konfigurerar du proxyservern i Linux.

#### <a name="windows"></a>Windows

Proxyinställningar måste ställas in korrekt för det lokala systemkontot för åtkomst till Internet. Om proxyinställningarna inte har angetts av en Grupprincip kan du konfigurera inställningarna för det lokala systemkontot.

1. Gå till **starta**, ange **gpedit.msc**, och välj sedan **RETUR**.
1. Välj **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer**. Se till att inställningen **Kontrollera proxy inställningar per dator (snarare än per användare)** är inaktiverad eller inte konfigurerad.
1. I **Kontrollpanelen**går du till **nätverks- och delningscenter** > **Internetalternativ**.
1. På den **anslutningar** fliken den **LAN-inställningar** knappen.
1. Rensa den **automatisk identifiering av inställningar** markerar du kryssrutan.
1. Välj den **använder en proxyserver för ditt lokala nätverk** kryssrutan och ange sedan proxyadress och port.
1. Välj den **Avancerat** knappen.
1. I den **undantag** anger du IP-adressen **168.63.129.16**. Välj **OK**.

#### <a name="linux"></a>Linux

Konfigurera rätt proxy i konfigurationsfilen för den Microsoft Azure-Gästagentversionen, som finns i \\osv\\waagent.conf.

Ange följande parametrar:

1.  **HTTP-proxyvärd**. Till exempel ange **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
1.  **HTTP-Proxyport**. Till exempel ange **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
1.  Starta om agenten.

  ```
  sudo service waagent restart
  ```

Proxyinställningarna i \\osv\\waagent.conf gäller även för nödvändig VM-tillägg. Om du vill använda Azure-lagringsplatser, se till att genomgår trafik till dessa databaser inte lokala intranätet. Om du har skapat användardefinierade vägar för att aktivera Tvingad tunneltrafik, se till att du lägger till en väg dirigerar som trafik till databaser direkt till Internet och inte via plats-till-plats VPN-anslutningen.

* **SLES**

  Du måste också lägga till vägar för IP-adresser som finns i \\osv\\regionserverclnt.cfg. Följande bild visar ett exempel:

  ![Tvingad tunneltrafik][deployment-guide-figure-50]


* **RHEL**

  Du måste också lägga till vägar för IP-adresserna för de värdar som anges i \\osv\\yum.repos.d\\rhui-belastningsutjämnare. Ett exempel finns i föregående bild.

* **Oracle Linux**

  Det finns inga databaser för Oracle Linux på Azure. Du måste konfigurera dina egna lagringsplatser för Oracle Linux eller använda de offentliga lagringsplatserna.

Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurera Azure förbättrad Övervakningstillägget för SAP

När du har förberett den virtuella datorn enligt beskrivningen i [distributionsscenarier för virtuella datorer för SAP på Azure][deployment-guide-3], Azure VM-agenten är installerad på den virtuella datorn. Nästa steg är att distribuera Azure förbättrad övervakning av tillägget för SAP, som är tillgänglig i Azure-Tilläggscentrallagret i globala Azure-datacenter. Mer information finns i [Azure Virtual Machines planering och implementering av SAP NetWeaver][planning-guide-9.1].

Du kan använda PowerShell eller Azure CLI för att installera och konfigurera Azure förbättrad övervakning av tillägget för SAP. För att installera tillägget på en Windows- eller Linux VM med hjälp av en Windows-dator, se [Azure PowerShell][deployment-guide-4.5.1]. För att installera tillägget på en Linux VM med hjälp av en Linux-dator, se [Azure CLI][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell för Linux- och Windows-datorer

Installera Azure förbättrad övervakning av tillägget för SAP med hjälp av PowerShell:

1. Kontrollera att du har installerat den senaste versionen av Azure PowerShell-cmdleten. Mer information finns i [distribution av Azure PowerShell-cmdlets][deployment-guide-4.1].  
1. Kör följande PowerShell-cmdlet:
    En lista över tillgängliga miljöer, kör `commandlet Get-AzureRmEnvironment`. Om du vill använda globala Azure din miljö är **AzureCloud**. Azure i Kina, Välj **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

När du har angett dina kontodata och identifiera Azure-dator skriptet distribuerar tillägg som krävs och gör att de nödvändiga funktionerna. Det kan ta flera minuter.
Mer information om `Set-AzureRmVMAEMExtension`, se [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Lyckad körning av SAP-specifika Azure cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Den `Set-AzureRmVMAEMExtension` konfiguration har alla steg för att konfigurera övervakning för SAP-värd.

Utdata från skriptet innehåller följande information:

* Bekräfta att övervakning av OS-disken och alla ytterligare datadiskar har konfigurerats.
* De kommande två meddelandena bekräfta konfigurationen av Storage-mätvärden för ett specifikt lagringskonto.
* En rad med utdata ger status för den faktiska uppdateringen av konfigurationen av övervakningen.
* Ytterligare en rad i utdata bekräftar att konfigurationen har distribuerats eller uppdateras.
* Den sista raden i utdata visas endast i informationssyfte. Den visar dina alternativ för att testa konfigurationen av övervakningen.
* Om du vill kontrollera att alla steg i Azure-förbättrad övervakning har körts har och att Azure-infrastrukturen innehåller nödvändiga data, fortsätter du med beredskapskontrollen för Azure förbättrad övervakning av tillägget för SAP, enligt beskrivningen i [Beredskapskontrollen för Azure förbättrad övervakning för SAP][deployment-guide-5.1].
* Vänta 15 – 30 minuter för Azure-diagnostik att samla in relevanta data.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI för virtuella Linux-datorer

Installera Azure förbättrad övervakning av tillägget för SAP med hjälp av Azure CLI:

   1. Installera Azure klassiska CLI, enligt beskrivningen i [installera Azure CLI för klassiska][azure-cli].
   1. Logga in med ditt Azure-konto:

      ```
      azure login
      ```

   1. Växla till läget Azure Resource Manager:

      ```
      azure config mode arm
      ```

   1. Aktivera Azure utökad övervakning:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Installera med hjälp av Azure CLI 2.0

   1. Installera Azure CLI 2.0, enligt beskrivningen i [installera Azure CLI 2.0][azure-cli-2].
   1. Logga in med ditt Azure-konto:

      ```
      az login
      ```

   1. Installera Azure CLI-AEM-tillägget
  
      ```
      az extension add --name aem
      ```
  
   1. Installera tillägget med
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Kontrollera att Azure förbättrad övervakning av tillägget är aktiv på virtuell Linux-dator. Kontrollera om filen \\var\\lib\\AzureEnhancedMonitor\\PerfCounters finns. Om den finns i en kommandotolk kör du följande kommando för att visa information som samlas in av Azure förbättrad Monitor:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Utdata ser ut så här:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroller och felsökning för övervakning av slutpunkt till slutpunkt

När du har distribuerat den virtuella Azure-datorn och konfigurera relevanta Azure-övervakning infrastrukturen, kontrollerar du om alla komponenter i Azure förbättrad övervakning tillägg fungerar som förväntat.

Kör beredskapskontrollen för Azure förbättrad övervakning av tillägget för SAP som beskrivs i [beredskapskontrollen för Azure förbättrad övervakning av tillägget för SAP][deployment-guide-5.1]. Om alla resultat av beredskap är positiva och alla relevanta prestandaräknare visas OK, har Azure-övervakning ställts in korrekt. Du kan fortsätta med installationen av SAP Värdagenten enligt beskrivningen i SAP Notes i [SAP resurser][deployment-guide-2.2]. Om kontrollen visar att räknare saknas måste köra hälsokontroller för Azure-övervakning infrastrukturen enligt beskrivningen i [hälsokontrollen för Azure infrastruktur övervakningskonfigurationen] [ deployment-guide-5.2]. Mer felsökning alternativ finns i [felsökning av Azure-övervakning för SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Beredskapskontrollen för Azure förbättrad övervakning av tillägget för SAP

Den här kontrollen ser till att alla prestandamått som visas i din SAP-programmet tillhandahålls av den underliggande infrastrukturen för Azure övervakning.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Kör beredskapskontrollen på en virtuell Windows-dator

1.  Logga in på Azure-datorer (med ett administratörskonto är inte nödvändigt).
1.  Öppna ett kommandotolksfönster.
1.  I Kommandotolken, ändra katalogen till installationsmappen för Azure förbättrad övervakning av tillägget för SAP: C:\\paket\\plugin-program\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version >\\släppa

  Den *version* i sökvägen till övervakningstillägget kan variera. Om du ser mappar för flera versioner av övervakningstillägget i installationsmappen för Kontrollera konfigurationen för AzureEnhancedMonitoring Windows-tjänsten och sedan växla till den mapp som anges som *sökvägen till körbara filen* .

  ![Egenskaper för tjänst som körs på Azure förbättrad övervakning av tillägget för SAP][deployment-guide-figure-1000]

1.  I Kommandotolken, kör **azperflib.exe** utan några parametrar.

  > [!NOTE]
  > Azperflib.exe körs i en loop och uppdaterar de insamlade räknarna var 60: e sekund. Stäng Kommandotolkens fönster om du vill avsluta slingan.
  >
  >

Om Azure förbättrad övervakning av tillägget inte har installerats eller AzureEnhancedMonitoring-tjänsten inte körs, har tillägget inte konfigurerats korrekt. Detaljerad information om hur du distribuerar tillägget finns i [felsökning Azure övervakade infrastrukturen för SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe är en komponent som inte kan användas för egna ändamål. Det är en komponent som levererar data som är relaterade till den virtuella datorn för SAP-Värdagenten för Azure.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Kontrollera resultatet av azperflib.exe

Azperflib.exe utdata visar all ifylld Azure prestandaräknare för SAP. Längst ned i listan över insamlade räknare visar en sammanfattning och hälsa indikator status för Azure-övervakning.

![Utdata för hälsokontroll genom att köra azperflib.exe, vilket betyder att det inte finns några problem][deployment-guide-figure-1100]
<a name="figure-11"></a>

Kontrollera resultatet som returneras för den **räknare totalt** utdata som rapporteras som tom och för **hälsostatus**, visas i bilden ovan.

Tolka resultatvärdena på följande sätt:

| Azperflib.exe resultatvärden | Hälsostatus för Azure |
| --- | --- |
| **API-anrop – inte tillgänglig** | Räknare som inte är tillgängliga kan vara antingen inte gäller för den virtuella datorkonfigurationen eller är fel. Se **hälsostatus**. |
| **Prestandaräknare för totala – tom** |Följande två Azure storage-räknare kan vara tom: <ul><li>Storage Op Läsfördröjning Server MS.</li><li>Storage Op Läsfördröjning E2E MS.</li></ul>Alla andra räknare måste ha värden. |
| **Hälsostatus** |Endast OK om returnera statusen **OK**. |
| **Diagnostik** |Detaljerad information om hälsostatus. |

Om den **hälsostatus** värdet är inte **OK**, följer du anvisningarna i [hälsokontrollen för Azure infrastruktur övervakningskonfigurationen] [ deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Kör beredskapskontrollen på en Linux VM

1.  Anslut till Azure-dator med hjälp av SSH.

1.  Kontrollera utdata från Azure förbättrad övervakning av tillägget.

  a.  Kör `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Förväntat resultat**: Returnerar lista över prestandaräknare. Filen får inte vara tomt.

 b. Kör `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Förväntat resultat**: Returnerar en rad där felet är **ingen**, till exempel **3; config; Error; 0, 0. Ingen; 0; 1456416792; tst-servercs;**

  c. Kör `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

    **Förväntat resultat**: Returnerar som tom eller finns inte.

Om föregående kontrollen inte lyckades, kör du dessa ytterligare kontroller:

1.  Kontrollera att waagent är installerat och aktiverat.

  a.  Kör `sudo ls -al /var/lib/waagent/`

      **Förväntat resultat**: Visar innehållet i katalogen waagent.

  b.  Kör `ps -ax | grep waagent`

   **Förväntat resultat**: Visar en post som liknar: `python /usr/sbin/waagent -daemon`

1.   Se till att Azure förbättrad övervakning av tillägget är installerat och körs.

  a.  Kör `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

    **Förväntat resultat**: Visar innehållet i katalogen Azure förbättrad övervakning av tillägget.

  b. Kör `ps -ax | grep AzureEnhanced`

     **Förväntat resultat**: Visar en post som liknar: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installera SAP Värdagenten enligt beskrivningen i SAP-kommentar [1031096], och kontrollera resultatet av `saposcol`.

  a.  Kör `/usr/sap/hostctrl/exe/saposcol -d`

  b.  Kör `dump ccm`

  c.  Kontrollera om den **Virtualization_Configuration\Enhanced övervakning åtkomst** mått är **SANT**.

Om du redan har en SAP NetWeaver ABAP-programserver installerat, öppna transaktion ST06 och kontrollera om utökad övervakning är aktiverad.

Om någon av de här kontrollerna misslyckas och detaljerad information om hur du distribuerar om tillägget finns i [felsökning Azure övervakade infrastrukturen för SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Hälsokontrollen för Azure konfigurationen av övervakningen infrastruktur

Om några av övervakningen data levereras inte korrekt som anges av det prov som beskrivs i [beredskapskontrollen för Azure förbättrad övervakning för SAP][deployment-guide-5.1], kör den `Test-AzureRmVMAEMExtension` cmdlet för att kontrollera om den Azure-övervakning av infrastruktur och övervakningstillägget för SAP har konfigurerats korrekt.

1.  Se till att du har installerat den senaste versionen av Azure PowerShell-cmdlet, enligt beskrivningen i [distribution av Azure PowerShell-cmdlets][deployment-guide-4.1].
1.  Kör följande PowerShell-cmdlet: En lista över tillgängliga miljöer, kör du cmdleten `Get-AzureRmEnvironment`. Om du vill använda globala Azure, Välj den **AzureCloud** miljö. Azure i Kina, Välj **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

1.  Ange dina kontodata och identifiera virtuella Azure-datorer.

  ![Inkommande sidan i SAP-specifika Azure cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skriptet testar konfigurationen av den virtuella datorn som du väljer.

  ![Utdata från lyckad test av Azure-övervakning infrastrukturen för SAP][deployment-guide-figure-1300]

Se till att varje hälsotillstånd resultat är **OK**. Om vissa kontroller inte visas **OK**, kör cmdleten uppdateringen som beskrivs i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5]. Vänta 15 minuter och upprepa de kontroller som beskrivs i [beredskapskontrollen för Azure förbättrad övervakning för SAP] [ deployment-guide-5.1] och [hälsokontrollen för Azure övervakning av infrastruktur konfigurationen] [deployment-guide-5.2]. Om kontrollerna tyda på ett problem med vissa eller alla räknare, se [felsökning Azure övervakade infrastrukturen för SAP][deployment-guide-5.3].

> [!Note]
> Du kan använda vissa varningar i fall där du använder Managed Disks i Standard Azure. Varningar visas i stället för att testerna returnerar ”OK”. Detta är normalt och avsedda när det gäller den typ av disk. Se även se [felsökning Azure övervakade infrastrukturen för SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Felsökning Azure övervakade infrastrukturen för SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure prestandaräknare visas inte alls

AzureEnhancedMonitoring Windows-tjänsten samlar in prestandamått i Azure. Om tjänsten inte har installerats korrekt, eller om den inte körs i den virtuella datorn, kan ingen prestandamått samlas in.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Installationskatalogen för Azure förbättrad övervakning av tillägget är tom

###### <a name="issue"></a>Problem

Installationskatalogen C:\\paket\\plugin-program\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version >\\avlämning är tom.

###### <a name="solution"></a>Lösning

Tillägget har inte installerats. Avgöra om det finns en proxy-problemet (enligt beskrivningen ovan). Du kan behöva starta om datorn eller köra den `Set-AzureRmVMAEMExtension` konfigurationsskript.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Tjänsten för Azure förbättrad övervakning finns inte

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns inte.

Azperflib.exe utdata genererar ett fel:

![Körningen av azperflib.exe indikerar att tjänsten Azure förbättrad övervakning av tillägget för SAP inte är igång][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Lösning

Om tjänsten inte finns, har Azure förbättrad övervakning av tillägget för SAP inte installerats korrekt. Distribuera om tillägget med hjälp av stegen som beskrivs för distributionsscenariot i [distributionsscenarier för virtuella datorer för SAP i Azure][deployment-guide-3].

När du har distribuerat tillägg, efter en timme, Kontrollera igen om Azure prestandaräknare finns i Azure VM.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Tjänsten för Azure förbättrad övervakning finns, men går inte att starta

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns och är aktiverad, men inte startar. Mer information finns i programmets händelselogg.

###### <a name="solution"></a>Lösning

Konfigurationen är felaktig. Starta om övervakningstillägget för den virtuella datorn, enligt beskrivningen i [konfigurera Azure förbättrad övervakning av tillägget för SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Vissa Azure prestandaräknare saknas

AzureEnhancedMonitoring Windows-tjänsten samlar in prestandamått i Azure. Tjänsten hämtar data från flera källor. Vissa configuration-data som samlas in lokalt och vissa prestandamått läses från Azure-diagnostik. Räknare för lagring som används från din loggning på prenumerationsnivå för lagring.

Om felsökning med hjälp av SAP-kommentar [1999351] inte löser problemet, kör den `Set-AzureRmVMAEMExtension` konfigurationsskript. Du måste kanske vänta en timme eftersom storage analytics eller diagnostik räknare inte kan skapas omedelbart efter att de är aktiverade. Om problemet kvarstår öppnar du meddelandet SAP customer support på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en Linux-dator.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure prestandaräknare visas inte alls

Prestandamått i Azure samlas in av en daemon. Om bakgrundsprogrammet inte körs kan inga prestandamått samlas in.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Installationskatalogen för Azure Enhanced Monitoring-tillägget är tom

###### <a name="issue"></a>Problem

Katalogen \\var\\lib\\waagent\\ har inte en underkatalog för Azure Enhanced Monitoring-tillägget.

###### <a name="solution"></a>Lösning

Tillägget har inte installerats. Avgöra om det finns en proxy-problemet (enligt beskrivningen ovan). Du kan behöva starta om datorn och/eller köra den `Set-AzureRmVMAEMExtension` konfigurationsskript.

##### <a name="the-execution-of-set-azurermvmaemextension-and-test-azurermvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Körning av Set-AzureRmVMAEMExtension och Test-AzureRmVMAEMExtension visa varningsmeddelanden som talar om att Standard Managed Disks inte stöds

###### <a name="issue"></a>Problem

När köra Set-AzureRmVMAEMExtension eller Test-AzureRmVMAEMExtension meddelanden som dessa visas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Du kan köra azperfli.exe enligt beskrivningen ovan för att få ett resultat som som indikerar icke felfritt tillstånd. 

###### <a name="solution"></a>Lösning

Meddelandena som orsakas av det faktum att Standard Managed Disks inte levererar API: er som används av övervakningstillägget för att kontrollera statistik för Standard Azure Storage-konton. Detta är inte bara några problem. Orsaken till introduktion till övervakning för Standard Disk Storage-konton begränsning av I/o som inträffat ofta. Hanterade diskar på så sätt undviker sådan begränsning genom att begränsa antalet diskar i ett lagringskonto. Därför är inte med den typen av övervakning av data inte viktigt.


#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Vissa Azure prestandaräknare saknas

Prestandamått i Azure samlas in av en daemon som hämtar data från flera källor. Vissa configuration-data som samlas in lokalt och vissa prestandamått läses från Azure-diagnostik. Storage räknare kommer från loggarna i ditt storage-prenumeration.

En fullständig och uppdaterad lista över kända problem finns i SAP-kommentar [1999351], som innehåller ytterligare felsökningsinformation för förbättrad Azure övervakning för SAP.

Om felsökning med hjälp av SAP-kommentar [1999351] inte löser problemet, kör den `Set-AzureRmVMAEMExtension` konfigurationsskript enligt beskrivningen i [konfigurera Azure förbättrad övervakning av tillägget för SAP] [deployment-guide-4.5]. Du kan behöva vänta en timme eftersom storage analytics eller diagnostik räknare inte kan skapas omedelbart efter att de är aktiverade. Om problemet kvarstår öppnar du meddelandet SAP customer support på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en Linux-dator.
