---
title: Azure Virtual Machines-distribution för SAP NetWeaver | Microsoft-dokument
description: Lär dig hur du distribuerar SAP-programvara på virtuella Linux-datorer i Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239887"
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
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS-distribution för SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Cachelagring för virtuella datorer och virtuella hårddiskar)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Programvara RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure-lagring)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktur för en RDBMS-distribution)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hög tillgänglighet och haveriberedskap med virtuella Azure-datorer)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 och senare)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 och tidigare versioner)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Använda en SQL Server-avbildning från Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Allmänt SQL Server för SAP i Azure-sammanfattning)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifika för SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfiguration av lagring)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Säkerhetskopiera och återställa)
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
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Flytta en virtuell dator från lokalt med en icke-generaliserad Azure VHD med SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Distributionsscenarier för virtuella datorer för SAP på Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuera Azure PowerShell-cmdlets)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Ladda ned och importera SAP-relevanta PowerShell-cmdlets)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ansluta till en virtuell dator till en lokal domän – endast Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Ladda ned, installera och aktivera Azure VM-agenten)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurera Azure-tillägget för SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Beredskapskontroll för Azure Extension för SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Hälsokontroll av Azure Extension för SAP-konfiguration)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Felsöka Azure-tillägg för SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurera VM-tillägg)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontrollerar och felsöker för datainsamling från på till slutna data för SAP-värdagent)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Planering och implementering av virtuella Azure-datorer för SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resurser)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hög tillgänglighet och haveriberedskap för SAP NetWeaver som körs på virtuella Azure-datorer)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hög tillgänglighet för SAP-programservrar)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Använda Autostart för SAP-instanser)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Endast molnet – distributioner av virtuella datorer i Azure utan beroenden i det lokala kundnätverket)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Korslokaler – distribution av virtuella sap-datorer i Azure helt integrerade med det lokala nätverket)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-regioner)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Feldomäner)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uppgradera domäner)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure-tillgänglighetsuppsättningar)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure-koncept för virtuella datorer)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium-lagring)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Flytta en virtuell dator från lokalt till Azure med en icke-generaliserad disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuera en virtuell dator med en kundspecifik avbildning)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Förberedelser för att flytta en virtuell dator från lokalt till Azure med en icke-generaliserad disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Förberedelser för att distribuera en virtuell dator med en kundspecifik avbildning för SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Förbereda virtuella datorer med SAP för Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Skillnaden mellan en Azure-disk och en Azure-avbildning)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Ladda upp en virtuell hårddisk från lokalt till Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiera diskar mellan Azure Storage-konton)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD-struktur för SAP-distributioner)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ställa in automatiskmontering för anslutna diskar)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Enkel virtuell dator med SAP NetWeaver demo /utbildning scenario)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Begrepp för cloud-only-distribution av SAP-instanser)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure-övervakningslösning för SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium-lagring)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Hanterade diskar)
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
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Lagring: Microsoft Azure Storage och datadiskar)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Sap-matris för produkttillgänglighet)
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
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
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

Virtuella Azure-datorer är lösningen för organisationer som behöver beräknings- och lagringsresurser, på minimal tid och utan långa anskaffningscykler. Du kan använda Virtuella Azure-datorer för att distribuera klassiska program, till exempel SAP NetWeaver-baserade program, i Azure. Utöka ett programs tillförlitlighet och tillgänglighet utan ytterligare lokala resurser. Azure Virtual Machines stöder anslutning mellan lokala anslutningar, så att du kan integrera virtuella Azure-datorer i organisationens lokala domäner, privata moln och SAP-systemlandskap.

I den här artikeln beskriver vi stegen för att distribuera SAP-program på virtuella datorer (VMs) i Azure, inklusive alternativa distributionsalternativ och felsökning. Den här artikeln bygger på informationen i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]. Det kompletterar också SAP-installationsdokumentation och SAP Notes, som är de primära resurserna för att installera och distribuera SAP-programvara.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Att konfigurera en virtuell Azure-dator för DISTRIBUTION AV SAP-programvara innebär flera steg och resurser. Innan du börjar, se till att du uppfyller kraven för att installera SAP-programvara på virtuella datorer i Azure.

### <a name="local-computer"></a>Lokal dator

Om du vill hantera virtuella Windows- eller Linux-datorer kan du använda ett PowerShell-skript och Azure-portalen. För båda verktygen behöver du en lokal dator med Windows 7 eller en senare version av Windows. Om du bara vill hantera virtuella Linux-datorer och du vill använda en Linux-dator för den här uppgiften kan du använda Azure CLI.

### <a name="internet-connection"></a>Internetuppkoppling

Om du vill hämta och köra de verktyg och skript som krävs för distribution av SAP-programvara måste du vara ansluten till Internet. Den virtuella Azure-datorn som kör Azure Extension för SAP behöver också åtkomst till Internet. Om den virtuella Azure-datorn är en del av ett virtuellt Azure-nätverk eller en lokal domän kontrollerar du att de relevanta proxyinställningarna är [inställda,][deployment-guide-configure-proxy]enligt beskrivningen i Konfigurera proxyn .

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-prenumeration

Du behöver ett aktivt Azure-konto.

### <a name="topology-and-networking"></a>Topologi och nätverk

Du måste definiera topologin och arkitekturen för SAP-distributionen i Azure:

* Azure-lagringskonton som ska användas
* Virtuellt nätverk där du vill distribuera SAP-systemet
* Resursgrupp som du vill distribuera SAP-systemet till
* Azure-region där du vill distribuera SAP-systemet
* SAP-konfiguration (två-nivå eller tre nivåer)
* VM-storlekar och antalet ytterligare datadiskar som ska monteras på de virtuella datorerna
* KONFIGURATION AV SAP-korrigerings- och transportsystem (CTS)

Skapa och konfigurera Azure-lagringskonton (om det behövs) eller virtuella Azure-nätverk innan du påbörjar distributionsprocessen för SAP-programvara. Information om hur du skapar och konfigurerar dessa resurser finns i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP-storlek

Känn till följande information, för SAP-storlek:

* Beräknad SAP-arbetsbelastning, till exempel med hjälp av SAP Quick Sizer-verktyget och SAP Application Performance Standard (SAPS) nummer
* Nödvändig CPU-resurs och minnesförbrukning för SAP-systemet
* Nödvändiga in-/utdataåtgärder (I/O) per sekund
* Krävs nätverksbandbredd för eventuell kommunikation mellan virtuella datorer i Azure
* Krävs nätverksbandbredd mellan lokala resurser och Azure-distribuerade SAP-system

### <a name="resource-groups"></a>Resursgrupper

I Azure Resource Manager kan du använda resursgrupper för att hantera alla programresurser i din Azure-prenumeration. Mer information finns i [Översikt över Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Resurser

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-resurser

När du konfigurerar distributionen av SAP-programvaran behöver du följande SAP-resurser:

* SAP Note [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows och Linux på Microsoft Azure

* SAP Note [2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [1409604] har den nödvändiga SAP Host Agent-versionen för Windows i Azure.
* SAP Note [2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1984787] har allmän information om SUSE Linux Enterprise Server 12.
* SAP Note [2002167] har allmän information om Red Hat Enterprise Linux 7.x.
* SAP Note [2069760] har allmän information om Oracle Linux 7.x.
* SAP Note [1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* SAP Note [1597355] har allmän information om växlingsutrymme för Linux.
* [SAP på Azure SCN-sidan](https://wiki.scn.sap.com/wiki/x/Pia7Gg) har nyheter och en samling användbara resurser.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* SAP-specifika PowerShell-cmdlets som ingår i [Azure PowerShell][azure-ps].
* SAP-specifika Azure CLI-kommandon som ingår i [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-resurser

De här Microsoft-artiklarna täcker SAP-distributioner i Azure:

* [Planering och implementering av virtuella Azure-datorer för SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Distributionsscenarier för SAP-programvara på virtuella Azure-datorer

Du har flera alternativ för att distribuera virtuella datorer och associerade diskar i Azure. Det är viktigt att förstå skillnaderna mellan distributionsalternativ, eftersom du kan vidta olika åtgärder för att förbereda dina virtuella datorer för distribution baserat på den distributionstyp du väljer.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP

Du kan använda en avbildning som tillhandahålls av Microsoft eller en tredje part på Azure Marketplace för att distribuera din virtuella dator. Marketplace erbjuder några vanliga OS-avbildningar av Windows Server och olika Linux-distributioner. Du kan också distribuera en avbildning som innehåller DBMS-SKU:er (Database Management System), till exempel Microsoft SQL Server. Mer information om hur du använder avbildningar med DBMS SKU:er finns i [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide].

Följande flödesschema visar den SAP-specifika sekvensen av steg för distribution av en virtuell dator från Azure Marketplace:

![Flödesschema för VM-distribution för SAP-system med hjälp av en VM-avbildning från Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure-portalen

Det enklaste sättet att skapa en ny virtuell dator med en avbildning från Azure Marketplace är att använda Azure-portalen.

1.  Gå till <https://portal.azure.com/#create/hub>.  Eller välj **+ Nytt**på Azure-portalmenyn .
1.  Välj **Beräkna**och välj sedan den typ av operativsystem som du vill distribuera. Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) eller Oracle Linux 7.2. Standardlistvyn visar inte alla operativsystem som stöds. Välj **se alla** för en fullständig lista. Mer information om operativsystem som stöds för distribution av SAP-programvara finns i SAP Note [1928533].
1.  På nästa sida, granska villkor.
1.  Välj **Resurshanteraren**i rutan **Välj en distributionsmodell** .
1.  Välj **Skapa**.

Guiden guidar dig genom att ange de parametrar som krävs för att skapa den virtuella datorn, utöver alla nödvändiga resurser, till exempel nätverksgränssnitt och lagringskonton. Några av dessa parametrar är:

1. **Grunderna:**
   * **Namn**: Namnet på resursen (namnet på den virtuella datorn).
   * **VM-disktyp:** Välj disktyp för OS-disken. Om du vill använda Premium Storage för dina datadiskar rekommenderar vi att du även använder Premium Storage för OS-disken.
   * **Användarnamn och lösenord** eller **SSH offentlig nyckel:** Ange användarnamn och lösenord för den användare som skapas under etableringen. För en virtuell Linux-dator kan du ange den offentliga SSH-nyckeln (Secure Shell) som du använder för att logga in på datorn.
   * **Prenumeration:** Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
   * **Resursgrupp**: Namnet på resursgruppen för den virtuella datorn. Du kan ange antingen namnet på en ny resursgrupp eller namnet på en resursgrupp som redan finns.
   * **Plats**: Var du kan distribuera den nya virtuella datorn. Om du vill ansluta den virtuella datorn till ditt lokala nätverk kontrollerar du att du väljer platsen för det virtuella nätverk som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure-nätverk][planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide].
1. **Storlek:**

     En lista över vm-typer som stöds finns i SAP Note [1928533]. Se till att du väljer rätt VM-typ om du vill använda Azure Premium Storage. Alla vm-typer stöder inte Premium Storage. Mer information finns i [Lagring: Microsoft Azure Storage och datadiskar][planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Premium Storage][planning-guide-azure-premium-storage] i Azure Virtual Machines planering och implementering för SAP [NetWeaver][planning-guide].

1. **Inställningar:**
   * **Lagring**
     * **Disktyp:** Välj disktyp för OS-disken. Om du vill använda Premium Storage för dina datadiskar rekommenderar vi att du även använder Premium Storage för OS-disken.
     * **Använd hanterade diskar**: Om du vill använda Hanterade diskar väljer du Ja. Mer information om hanterade diskar finns i kapitlet Hanterade diskar i [planeringsguiden.][planning-guide-managed-disks]
     * **Lagringskonto**: Välj ett befintligt lagringskonto eller skapa ett nytt. Alla lagringstyper fungerar inte för att köra SAP-program. Mer information om lagringstyper finns i [Lagringsstruktur för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Nätverk**
     * **Virtuellt nätverk** och **undernät:** Om du vill integrera den virtuella datorn med intranätet väljer du det virtuella nätverk som är anslutet till det lokala nätverket.
     * **Offentlig IP-adress**: Välj den offentliga IP-adress som du vill använda eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att komma åt din virtuella dator via Internet. Se till att du också skapar en nätverkssäkerhetsgrupp för att skydda åtkomsten till den virtuella datorn.
     * **Nätverkssäkerhetsgrupp**: Mer information finns i [Kontrollera nätverkstrafikflödet med nätverkssäkerhetsgrupper][virtual-networks-nsg].
   * **Tillägg:** Du kan installera tillägg för virtuella datorer genom att lägga till dem i distributionen. Du behöver inte lägga till tillägg i det här steget. De tillägg som krävs för SAP-stöd installeras senare. Se kapitel [Konfigurera Azure Extension för SAP][deployment-guide-4.5] i den här guiden.
   * **Hög tillgänglighet**: Välj en tillgänglighetsuppsättning eller ange parametrarna för att skapa en ny tillgänglighetsuppsättning. Mer information finns i [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3].
   * **Övervakning**
     * **Startdiagnostik:** Du kan välja **Inaktivera** för startdiagnostik.
     * **Gäst OS diagnostik:** Du kan välja **Inaktivera** för övervakning diagnostik.

1. **Sammanfattning**:

   Granska dina val och välj sedan **OK**.

Den virtuella datorn distribueras i den resursgrupp du valde.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Du kan skapa en virtuell dator med hjälp av en av de SAP-mallar som publiceras i [azure-quickstart-mallarna GitHub-databasen][azure-quickstart-templates-github]. Du kan också manuellt skapa en virtuell dator med hjälp av [Azure-portalen,][virtual-machines-windows-tutorial] [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]eller [Azure CLI][virtual-machines-linux-tutorial].

* [**Konfigurationsmall på två nivåer (endast en virtuell dator)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Om du vill skapa ett system med två nivåer med bara en virtuell dator använder du den här mallen.
* [**Konfigurationsmall på två nivåer (endast en virtuell dator) – hanterade diskar** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Om du vill skapa ett system på två nivåer med endast en virtuell dator och hanterade diskar använder du den här mallen.
* [**Konfigurationsmall på tre nivåer (flera virtuella datorer)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Om du vill skapa ett system med tre nivåer med hjälp av flera virtuella datorer använder du den här mallen.
* [**Konfigurationsmall på tre nivåer (flera virtuella datorer) – hanterade diskar** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Om du vill skapa ett system med tre nivåer med hjälp av flera virtuella datorer och hanterade diskar använder du den här mallen.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna:**
   * **Prenumeration**: Prenumerationen som ska användas för att distribuera mallen.
   * **Resursgrupp**: Den resursgrupp som ska användas för att distribuera mallen. Du kan skapa en ny resursgrupp eller välja en befintlig resursgrupp i prenumerationen.
   * **Plats**: Var mallen ska distribueras. Om du har markerat en befintlig resursgrupp används platsen för resursgruppen.

1. **Inställningar:**
   * **SAP-system-ID**: SAP-system-ID (SID).
   * **OS-typ**: Operativsystemet som du vill distribuera, till exempel Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) eller Oracle Linux 7.2.

     Listvyn visar inte alla operativsystem som stöds. Mer information om operativsystem som stöds för distribution av SAP-programvara finns i SAP Note [1928533].
   * **SAP-systemstorlek:** SAP-systemets storlek.

     Antalet SAPS det nya systemet tillhandahåller. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator.
   * **Systemtillgänglighet** (endast mall på tre nivåer): Systemtillgängligheten.

     Välj **HA** för en konfiguration som är lämplig för en installation med hög tillgänglighet. Två databasservrar och två servrar för ABAP SAP Central Services (ASCS) skapas.
   * **Lagringstyp** (endast mall med två nivåer): Vilken typ av lagringsutrymme som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagringstyper finns i följande resurser:
      * [Användning av Azure Premium SSD-lagring för SAP DBMS-instans][2367194]
      * [Lagringsstruktur för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium storage: Högpresterande lagring för Azure Virtual Machine-arbetsbelastningar][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **Admin användarnamn** och **Admin lösenord:** Ett användarnamn och lösenord.
     En ny användare skapas för att logga in på den virtuella datorn.
   * **Nytt eller befintligt undernät**: Avgör om ett nytt virtuellt nätverk och undernät skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till det lokala nätverket väljer du **Befintlig**.
   * **Undernäts-ID:** Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat att den virtuella datorn ska tilldelas, namnger du ID:t för det specifika undernätet. ID:t ser vanligtvis ut så&lt;här: /subscriptions/subscription id&lt;>/resourceGroups/resource group name>/providers/Microsoft.Network/virtualNetworks/&lt;virtual network&lt;name>/subnets/ subnet name>

1. **Villkor:**  
    Granska och acceptera de juridiska villkoren.

1. Välj **Köp**.

Azure VM-agenten distribueras som standard när du använder en avbildning från Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur det lokala nätverket är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte kan komma åt Internet och kan inte hämta de vm-tillägg som krävs eller samla in Azure-infrastrukturinformation för SAP-värdagenten via SAP-tillägget för Azure. Mer information finns i [Konfigurera proxyn][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Ansluta till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory- eller DNS-instans via en Azure-site-till-site VPN-anslutning eller ExpressRoute (detta kallas *korslokalisering* i [Azure Virtual Machines planering och implementering för SAP NetWeaver),][planning-guide]förväntas det att den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för den här uppgiften finns i [Ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurera VM-tillägg

För att vara säker på att SAP stöder din miljö konfigurerar du Azure Extension för SAP enligt beskrivningen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5]. Kontrollera förutsättningarna för SAP och krävs minimiversioner av SAP Kernel och SAP Host Agent, i de resurser som anges i [SAP-resurser][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>VM-tillägg för SAP-kontroll

Kontrollera om VM-tillägget för SAP fungerar, enligt beskrivningen i [Checkar och felsökning för datainsamling från på till slutna data för SAP-värdagenten][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Steg efter distribution

När du har skapat den virtuella datorn och den virtuella datorn har distribuerats måste du installera de nödvändiga programvarukomponenterna i den virtuella datorn. På grund av installationssekvensen för distribution/programvara i den här typen av vm-distribution måste programvaran som ska installeras redan vara tillgänglig, antingen i Azure, på en annan virtuell dator eller som en disk som kan kopplas. Eller överväg att använda ett scenario över flera lokala, där anslutning till lokala tillgångar (installationsresurser) anges.

När du har distribuerat den virtuella datorn i Azure följer du samma riktlinjer och verktyg för att installera SAP-programvaran på den virtuella datorn som i en lokal miljö. Om du vill installera SAP-programvara på en virtuell Azure-dator rekommenderar både SAP och Microsoft att du överför och lagrar SAP-installationsmedia på Azure VHDs eller Managed Disks, eller att du skapar en Azure VM som fungerar som en filserver som har alla nödvändiga SAP-installationsmedia.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP

Eftersom olika versioner av ett operativsystem eller DBMS har olika korrigeringskrav kanske avbildningarna du hittar på Azure Marketplace inte uppfyller dina behov. Du kanske i stället vill skapa en virtuell dator med hjälp av din egen OS/DBMS VM-avbildning, som du kan distribuera igen senare.
Du kan använda olika steg för att skapa en privat avbildning för Linux än att skapa en för Windows.

---
> ![Windows][Logo_Windows] Windows
>
> Om du vill förbereda en Windows-avbildning som du kan använda för att distribuera flera virtuella datorer måste Windows-inställningarna (som Windows SID och värdnamnet) vara abstrakta eller generaliserade på den lokala virtuella datorn. Du kan använda [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) för att göra detta.
>
> ![Linux][Logo_Linux] Linux
>
> För att förbereda en Linux-avbildning som du kan använda för att distribuera flera virtuella datorer måste vissa Linux-inställningar vara abstrakta eller generaliserade på den lokala virtuella datorn. Du kan `waagent -deprovision` använda för att göra detta. Mer information finns i [Fånga en virtuell Linux-dator som körs på Azure][virtual-machines-linux-capture-image] och [användarhandboken för Azure Linux-agenten][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Du kan förbereda och skapa en anpassad avbildning och sedan använda den för att skapa flera nya virtuella datorer. Detta beskrivs i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]. Konfigurera databasinnehållet antingen genom att använda SAP Software Provisioning Manager för att installera ett nytt SAP-system (återställer en databassäkerhetskopiering från en disk som är ansluten till den virtuella datorn) eller genom att direkt återställa en databassäkerhetskopiering från Azure-lagring, om din DBMS stöder den. Mer information finns i [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]. Om du redan har installerat ett SAP-system på den lokala virtuella datorn (särskilt för system på två nivåer) kan du anpassa SAP-systeminställningarna efter distributionen av den virtuella Azure-datorn med hjälp av proceduren Systembyte som stöds av SAP Software Provisioning Manager (SAP Note [1619720]). Annars kan du installera SAP-programvaran när du har distribuerat Den virtuella Azure-datorn.

Följande flödesschema visar den SAP-specifika sekvensen av steg för att distribuera en virtuell dator från en anpassad avbildning:

![Flödesschema för VM-distribution för SAP-system med hjälp av en VM-avbildning på privat Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure-portalen

Det enklaste sättet att skapa en ny virtuell dator från en hanterad diskavbildning är att använda Azure-portalen. Mer information om hur du skapar en hanteravbildning finns i [Ta en hanterad avbildning av en generaliserad virtuell dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Gå till <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Eller välj **Bilder**på Portal-menyn i Azure-portalen .
1.  Välj den hanterade diskavbildning som du vill distribuera och klicka på **Skapa virtuell dator**

Guiden guidar dig genom att ange de parametrar som krävs för att skapa den virtuella datorn, utöver alla nödvändiga resurser, till exempel nätverksgränssnitt och lagringskonton. Några av dessa parametrar är:

1. **Grunderna:**
   * **Namn**: Namnet på resursen (namnet på den virtuella datorn).
   * **VM-disktyp:** Välj disktyp för OS-disken. Om du vill använda Premium Storage för dina datadiskar rekommenderar vi att du även använder Premium Storage för OS-disken.
   * **Användarnamn och lösenord** eller **SSH offentlig nyckel:** Ange användarnamn och lösenord för den användare som skapas under etableringen. För en virtuell Linux-dator kan du ange den offentliga SSH-nyckeln (Secure Shell) som du använder för att logga in på datorn.
   * **Prenumeration:** Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
   * **Resursgrupp**: Namnet på resursgruppen för den virtuella datorn. Du kan ange antingen namnet på en ny resursgrupp eller namnet på en resursgrupp som redan finns.
   * **Plats**: Var du kan distribuera den nya virtuella datorn. Om du vill ansluta den virtuella datorn till ditt lokala nätverk kontrollerar du att du väljer platsen för det virtuella nätverk som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure-nätverk][planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide].
1. **Storlek:**

     En lista över vm-typer som stöds finns i SAP Note [1928533]. Se till att du väljer rätt VM-typ om du vill använda Azure Premium Storage. Alla vm-typer stöder inte Premium Storage. Mer information finns i [Lagring: Microsoft Azure Storage och datadiskar][planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Premium Storage][planning-guide-azure-premium-storage] i Azure Virtual Machines planering och implementering för SAP [NetWeaver][planning-guide].

1. **Inställningar:**
   * **Lagring**
     * **Disktyp:** Välj disktyp för OS-disken. Om du vill använda Premium Storage för dina datadiskar rekommenderar vi att du även använder Premium Storage för OS-disken.
     * **Använd hanterade diskar**: Om du vill använda Hanterade diskar väljer du Ja. Mer information om hanterade diskar finns i kapitlet Hanterade diskar i [planeringsguiden.][planning-guide-managed-disks]
   * **Nätverk**
     * **Virtuellt nätverk** och **undernät:** Om du vill integrera den virtuella datorn med intranätet väljer du det virtuella nätverk som är anslutet till det lokala nätverket.
     * **Offentlig IP-adress**: Välj den offentliga IP-adress som du vill använda eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att komma åt din virtuella dator via Internet. Se till att du också skapar en nätverkssäkerhetsgrupp för att skydda åtkomsten till den virtuella datorn.
     * **Nätverkssäkerhetsgrupp**: Mer information finns i [Kontrollera nätverkstrafikflödet med nätverkssäkerhetsgrupper][virtual-networks-nsg].
   * **Tillägg:** Du kan installera tillägg för virtuella datorer genom att lägga till dem i distributionen. Du behöver inte lägga till tillägg i det här steget. De tillägg som krävs för SAP-stöd installeras senare. Se kapitel [Konfigurera Azure Extension för SAP][deployment-guide-4.5] i den här guiden.
   * **Hög tillgänglighet**: Välj en tillgänglighetsuppsättning eller ange parametrarna för att skapa en ny tillgänglighetsuppsättning. Mer information finns i [Azure-tillgänglighetsuppsättningar][planning-guide-3.2.3].
   * **Övervakning**
     * **Startdiagnostik:** Du kan välja **Inaktivera** för startdiagnostik.
     * **Gäst OS diagnostik:** Du kan välja **Inaktivera** för övervakning diagnostik.

1. **Sammanfattning**:

   Granska dina val och välj sedan **OK**.

Den virtuella datorn distribueras i den resursgrupp du valde.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Om du vill skapa en distribution med hjälp av en privat OS-avbildning från Azure-portalen använder du någon av följande SAP-mallar. Dessa mallar publiceras i [GitHub-databasen för azure-quickstart-mallar][azure-quickstart-templates-github]. Du kan också manuellt skapa en virtuell dator med hjälp av [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Konfigurationsmall på två nivåer (endast en virtuell dator)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Om du vill skapa ett system med två nivåer med bara en virtuell dator använder du den här mallen.
* [**Konfigurationsmall på två nivåer (endast en virtuell dator) – Hanterad diskavbildning** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Om du vill skapa ett system med två nivåer med endast en virtuell dator och en avbildning av hanterad disk använder du den här mallen.
* [**Konfigurationsmall på tre nivåer (flera virtuella datorer)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Om du vill skapa ett system med tre nivåer med hjälp av flera virtuella datorer eller en egen OS-avbildning använder du den här mallen.
* [**Konfigurationsmall på tre nivåer (flera virtuella datorer) – Hanterad diskavbildning** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Om du vill skapa ett system med tre nivåer med hjälp av flera virtuella datorer eller en egen OS-avbildning och en avbildning av hanterad disk använder du den här mallen.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna:**
   * **Prenumeration**: Prenumerationen som ska användas för att distribuera mallen.
   * **Resursgrupp**: Den resursgrupp som ska användas för att distribuera mallen. Du kan skapa en ny resursgrupp eller välja en befintlig resursgrupp i prenumerationen.
   * **Plats**: Var mallen ska distribueras. Om du har markerat en befintlig resursgrupp används platsen för resursgruppen.
1. **Inställningar:**
   * **SAP-system-ID**: SAP-system-ID.
   * **OS-typ:** Den operativsystemtyp som du vill distribuera (Windows eller Linux).
   * **SAP-systemstorlek:** SAP-systemets storlek.

     Antalet SAPS det nya systemet tillhandahåller. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator.
   * **Systemtillgänglighet** (endast mall på tre nivåer): Systemtillgängligheten.

     Välj **HA** för en konfiguration som är lämplig för en installation med hög tillgänglighet. Två databasservrar och två servrar för ASCS skapas.
   * **Lagringstyp** (endast mall med två nivåer): Vilken typ av lagringsutrymme som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagringstyper finns i följande resurser:
      * [Användning av Azure Premium SSD-lagring för SAP DBMS-instans][2367194]
      * [Lagringsstruktur för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Högpresterande lagring för Azure-arbetsbelastningar för virtuella datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **Vhd-uri för användaravbildning** (endast ohanterade diskavbildningsmallar): URI för den&lt;privata OS-avbildningen VHD, till exempel https:// accountname>.blob.core.windows.net/vhds/userimage.vhd.
   * Användarkonto för **användaravbildning** (endast ohanterade diskavbildningsmall): Namnet på lagringskontot &lt;där den privata OS-avbildningen lagras, till exempel kontonamn> i https://&lt;kontonamn>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (endast hanterad diskavbildningsmall): Id för den hanterade diskavbildning som du vill använda
   * **Admin användarnamn** och **Admin lösenord:** Användarnamn och lösenord.

     En ny användare skapas för att logga in på den virtuella datorn.
   * **Nytt eller befintligt undernät**: Avgör om ett nytt virtuellt nätverk och undernät skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till det lokala nätverket väljer du **Befintlig**.
   * **Undernäts-ID:** Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat att den virtuella datorn ska tilldelas, namnger du ID:t för det specifika undernätet. ID:t ser vanligtvis ut så&lt;här: /subscriptions/subscription id&lt;>/resourceGroups/resource group name>/providers/Microsoft.Network/virtualNetworks/&lt;virtual network&lt;name>/subnets/ subnet name>

1. **Villkor:**  
    Granska och acceptera de juridiska villkoren.

1. Välj **Köp**.

#### <a name="install-the-vm-agent-linux-only"></a>Installera VM-agenten (endast Linux)

Om du vill använda mallarna som beskrivs i föregående avsnitt måste Linux-agenten redan vara installerad i användaravbildningen, annars misslyckas distributionen. Hämta och installera VM-agenten i användaravbildningen enligt beskrivningen i [Hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4]. Om du inte använder mallarna kan du också installera VM-agenten senare.

#### <a name="join-a-domain-windows-only"></a>Ansluta till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory- eller DNS-instans via en Azure-site-till-plats VPN-anslutning eller Azure ExpressRoute (detta kallas *korslokalisering* i [Azure Virtual Machines planering och implementering för SAP NetWeaver),][planning-guide]förväntas den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för det här steget finns i [Ansluta till en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur det lokala nätverket är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte kan komma åt Internet och kan inte hämta de vm-tillägg som krävs eller samla in Azure-infrastrukturinformation för SAP-värdagenten via SAP-tillägget för Azure, se [Konfigurera proxyn][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurera Azure VM-tillägg för SAP

För att vara säker på att SAP stöder din miljö konfigurerar du Azure Extension för SAP enligt beskrivningen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5]. Kontrollera förutsättningarna för SAP och krävs minimiversioner av SAP Kernel och SAP Host Agent, i de resurser som anges i [SAP-resurser][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>KONTROLL AV VM-tillägg för SAP

Kontrollera om VM-tillägget för SAP fungerar, enligt beskrivningen i [Checkar och felsökning för datainsamling från på till slutna data för SAP-värdagenten][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Flytta en lokal virtuell dator med hjälp av en icke-generaliserad Azure VHD med SAP

I det här fallet planerar du att flytta ett specifikt SAP-system från en lokal miljö till Azure. Du kan göra detta genom att ladda upp den virtuella hårddisken som har operativsystemet, SAP-binärfilerna och så småningom DBMS-binärfilerna, plus virtuella hårddiskar med data och loggfiler i DBMS, till Azure. Till skillnad från scenariot som beskrivs i [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3], i det här fallet behåller du användarkontona för värdnamn, SAP SID och SAP i Den virtuella Azure-datorn, eftersom de har konfigurerats i den lokala miljön. Du behöver inte generalisera operativsystemet. Det här scenariot gäller oftast för scenarier mellan lokala scenarier där en del av SAP-landskapet körs lokalt och en del av det körs på Azure.

I det här fallet installeras vm-agenten **inte** automatiskt under distributionen. Eftersom VM-agenten och Azure Extension för SAP krävs för att köra SAP NetWeaver på Azure måste du hämta, installera och aktivera båda komponenterna manuellt när du har skapat den virtuella datorn.

Mer information om Azure VM-agenten finns i följande resurser.

---
> ![Windows][Logo_Windows] Windows
>
> [Översikt över Azure Virtual Machine Agent][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Användarguide för Azure Linux-agenten][virtual-machines-linux-agent-user-guide]
>
>

---

Följande flödesschema visar stegsekvensen för att flytta en lokal virtuell dator med hjälp av en icke-generaliserad Azure VHD:

![Flödesschema för VM-distribution för SAP-system med hjälp av en VM-disk][deployment-guide-figure-400]

Om disken redan har laddats upp och definierats i Azure (se [Azure Virtual Machines planering och implementering för SAP NetWeaver)][planning-guide]utför du de uppgifter som beskrivs i de närmaste avsnitten.

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en distribution med hjälp av en privat OS-disk via Azure-portalen använder du SAP-mallen som publiceras i [GitHub-databasen för azure-quickstart-mallar][azure-quickstart-templates-github]. Du kan också skapa en virtuell dator manuellt med hjälp av PowerShell.

* [**Konfigurationsmall på två nivåer (endast en virtuell dator)** (sap-2-nivå-user-disk)][sap-templates-2-tier-os-disk]

  Om du vill skapa ett system med två nivåer med bara en virtuell dator använder du den här mallen.
* [**Konfigurationsmall på två nivåer (endast en virtuell dator) – Hanterad disk** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Om du vill skapa ett system med två nivåer med bara en virtuell dator och en hanterad disk använder du den här mallen.

Ange följande parametrar för mallen i Azure-portalen:

1. **Grunderna:**
   * **Prenumeration**: Prenumerationen som ska användas för att distribuera mallen.
   * **Resursgrupp**: Den resursgrupp som ska användas för att distribuera mallen. Du kan skapa en ny resursgrupp eller välja en befintlig resursgrupp i prenumerationen.
   * **Plats**: Var mallen ska distribueras. Om du har markerat en befintlig resursgrupp används platsen för resursgruppen.
1. **Inställningar:**
   * **SAP-system-ID**: SAP-system-ID.
   * **OS-typ:** Den operativsystemtyp som du vill distribuera (Windows eller Linux).
   * **SAP-systemstorlek:** SAP-systemets storlek.

     Antalet SAPS det nya systemet tillhandahåller. Om du är osäker på hur många SAPS systemet kräver, fråga din SAP Technology Partner eller System Integrator.
   * **Lagringstyp** (endast mall med två nivåer): Vilken typ av lagringsutrymme som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagringstyper finns i följande resurser:
      * [Användning av Azure Premium SSD-lagring för SAP DBMS-instans][2367194]
      * [Lagringsstruktur för en virtuell dator för RDBMS-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium storage: Högpresterande lagring för Azure Virtual Machine-arbetsbelastningar][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **OS-disk VHD URI** (endast ohanterade diskmallar): URI för den privata&lt;OS-disken, till exempel https:// accountname>.blob.core.windows.net/vhds/osdisk.vhd.
   * **OS-diskhanterad disk-ID** (endast hanterad diskmall): Id för disken Hanterad disk OS-disk, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Nytt eller befintligt undernät**: Avgör om ett nytt virtuellt nätverk och undernät skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till det lokala nätverket väljer du **Befintlig**.
   * **Undernäts-ID:** Om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat att den virtuella datorn ska tilldelas, namnger du ID:t för det specifika undernätet. ID:t ser vanligtvis ut så&lt;här: /subscriptions/subscription id&lt;>/resourceGroups/resource group name>/providers/Microsoft.Network/virtualNetworks/&lt;virtual network&lt;name>/subnets/ subnet name>

1. **Villkor:**  
    Granska och acceptera de juridiska villkoren.

1. Välj **Köp**.

#### <a name="install-the-vm-agent"></a>Installera VM-agenten

Om du vill använda mallarna som beskrivs i föregående avsnitt måste VM-agenten installeras på OS-disken, annars misslyckas distributionen. Hämta och installera VM-agenten i den virtuella datorn, enligt beskrivningen i [Hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4].

Om du inte använder mallarna som beskrivs i föregående avsnitt kan du också installera VM-agenten efteråt.

#### <a name="join-a-domain-windows-only"></a>Ansluta till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory- eller DNS-instans via en Azure-site-till-site VPN-anslutning eller ExpressRoute (detta kallas *korslokalisering* i [Azure Virtual Machines planering och implementering för SAP NetWeaver),][planning-guide]förväntas det att den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för den här uppgiften finns i [Ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur det lokala nätverket är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte kan komma åt Internet och kan inte hämta de vm-tillägg som krävs eller samla in Azure-infrastrukturinformation för SAP-värdagenten via SAP-tillägget för Azure, se [Konfigurera proxyn][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurera Azure VM-tillägg för SAP

För att vara säker på att SAP stöder din miljö konfigurerar du Azure Extension för SAP enligt beskrivningen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5]. Kontrollera förutsättningarna för SAP och krävs minimiversioner av SAP Kernel och SAP Host Agent, i de resurser som anges i [SAP-resurser][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>SAP VM-kontroll

Kontrollera om VM-tillägget för SAP fungerar, enligt beskrivningen i [Checkar och felsökning för datainsamling från på till slutna data för SAP-värdagenten][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Uppdatera konfigurationen av Azure Extension för SAP

Uppdatera konfigurationen av Azure Extension för SAP i något av följande scenarier:
* Det gemensamma Microsoft/SAP-teamet utökar funktionerna i vm-tillägget och begär fler eller färre räknare.
* Microsoft introducerar en ny version av den underliggande Azure-infrastrukturen som levererar data, och Azure Extension för SAP måste anpassas till dessa ändringar.
* Du monterar ytterligare datadiskar på din Virtuella Azure-dator eller tar bort en datadisk. I det här fallet uppdatera insamlingen av lagringsrelaterade data. Att ändra konfigurationen genom att lägga till eller ta bort slutpunkter eller genom att tilldela IP-adresser till en virtuell dator påverkar inte tilläggskonfigurationen.
* Du ändrar storleken på din Virtuella Azure-dator, till exempel från storlek A5 till någon annan vm-storlek.
* Du lägger till nya nätverksgränssnitt i din Virtuella Azure-dator.

Om du vill uppdatera inställningar uppdaterar du konfigurationen av Azure Extension för SAP genom att följa stegen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Detaljerade uppgifter för distribution av SAP-programvara

Det här avsnittet innehåller detaljerade steg för att utföra specifika uppgifter i konfigurations- och distributionsprocessen.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Distribuera Azure PowerShell-cmdlets

1. Gå till [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Under Kommandoradsverktyg väljer du **Windows installera** **under** **Kommandoradsverktyg**.
1. Välj Kör för den nedladdade filen (till exempel WindowsAzurePowershellGet.3f.3f.3fnew.exe) i dialogrutan Microsoft Download Manager (till exempel WindowsAzurePowershellGet.3f.3fnew.exe) i dialogrutan Microsoft Download Manager ( **till**exempel WindowsAzurePowershellGet.3f.3fnew.exe).
1. Om du vill köra Microsoft Web Platform Installer (Microsoft Web PI) väljer du **Ja**.
1. En sida som ser ut så här visas:

   ![Installationssida för Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Välj **Installera**och acceptera sedan licensvillkoren för programvara från Microsoft.
1. PowerShell är installerat. Välj **Slutför** om du vill stänga installationsguiden.

Kontrollera ofta om det finns uppdateringar av PowerShell-cmdlets, som vanligtvis uppdateras varje månad. Det enklaste sättet att söka efter uppdateringar är att göra föregående installationssteg, upp till installationssidan som visas i steg 5. Frisläppningsdatum och utgivningsnummer för cmdlets finns på sidan som visas i steg 5. Om inget annat anges i SAP Note [1928533] eller SAP Note [2015553]rekommenderar vi att du arbetar med den senaste versionen av Azure PowerShell-cmdlets.

Om du vill kontrollera versionen av Azure PowerShell-cmdletar som är installerade på datorn kör du det här PowerShell-kommandot:

```powershell
(Get-Module Az.Compute).Version
```

Resultatet ser ut så här:

![Resultat av Azure PowerShell cmdlet version check][deployment-guide-figure-600]
<a name="figure-6"></a>

Om Azure cmdlet-versionen som är installerad på datorn är den aktuella versionen, anger den första sidan i installationsguiden den genom att lägga till **(Installerad)** i produkttiteln (se följande skärmbild). Dina PowerShell Azure-cmdlets är uppdaterade. Om du vill stänga installationsguiden väljer du **Avsluta**.

![Installationssida för Azure PowerShell-cmdletar som anger att den senaste versionen av Azure PowerShell-cmdletar är installerade][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuera Azure CLI

1. Gå till [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Under Kommandoradsverktyg väljer du länken **Installera** för ditt operativsystem under **Kommandoradsverktyg.** **Azure command-line interface**
1. Välj Kör för den nedladdade filen (till exempel WindowsAzureXPlatCLI.3f.3f.3fnew.exe) för den nedladdade filen (till exempel WindowsAzureXPlatCLI.3f.3f.3fnew.exe) i dialogrutan **Microsoft**Download Manager ( till exempel WindowsAzureXPlatCLI.3f.3fnew.exe).
1. Om du vill köra Microsoft Web Platform Installer (Microsoft Web PI) väljer du **Ja**.
1. En sida som ser ut så här visas:

   ![Installationssida för Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Välj **Installera**och acceptera sedan licensvillkoren för programvara från Microsoft.
1. Azure CLI är installerat. Välj **Slutför** om du vill stänga installationsguiden.

Kontrollera ofta om det finns uppdateringar till Azure CLI, som vanligtvis uppdateras varje månad. Det enklaste sättet att söka efter uppdateringar är att göra föregående installationssteg, upp till installationssidan som visas i steg 5.

Om du vill kontrollera vilken version av Azure CLI som är installerad på datorn kör du det här kommandot:

```console
azure --version
```

Resultatet ser ut så här:

![Resultat av Azure CLI-versionskontrollen][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Ansluta till en virtuell dator till en lokal domän (endast Windows)

Om du distribuerar virtuella SAP-datorer i ett lokalt scenario, där lokala Active Directory och DNS utökas i Azure, förväntas de virtuella datorerna ansluta till en lokal domän. De detaljerade åtgärder du vidtar för att ansluta till en virtuell dator till en lokal domän och den ytterligare programvara som krävs för att vara medlem i en lokal domän varierar beroende på kund. Om du vill ansluta till en virtuell dator med en lokal domän måste du vanligtvis installera ytterligare programvara, till exempel programvara mot skadlig kod, och säkerhetskopierings- eller övervakningsprogramvara.

I det här fallet måste du också se till att om Internet-proxyinställningar tvingas när en virtuell dator ansluter till en domän i din miljö, har Windows Local System Account (S-1-5-18) i den virtuella gästdatorn samma proxyinställningar. Det enklaste alternativet är att tvinga proxyn med hjälp av en domängrupprincip, som gäller för system i domänen.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Ladda ned, installera och aktivera Azure VM-agenten

För virtuella datorer som distribueras från en OS-avbildning som inte är generaliserad (till exempel en avbildning som inte har sitt ursprung i Verktyget För systemförberedelse i Windows system, eller sysprep, måste du hämta, installera och aktivera Azure VM-agenten manuellt).

Om du distribuerar en virtuell dator från Azure Marketplace krävs inte det här steget. Avbildningar från Azure Marketplace har redan Azure VM-agenten.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Ladda ned Azure VM-agenten:
   1.  Hämta [installationspaketet](https://go.microsoft.com/fwlink/?LinkId=394789)för Azure VM Agent .
   1.  Lagra VM-agentENs MSI-paket lokalt på en persondator eller server.
1. Installera Azure VM-agenten:
   1.  Anslut till den distribuerade Virtuella Azure-datorn med hjälp av RdP (Remote Desktop Protocol).
   1.  Öppna ett Utforskarfönster på den virtuella datorn och välj målkatalogen för MSI-filen för VM-agenten.
   1.  Dra AZURE VM Agent Installer MSI-filen från din lokala dator/server till målkatalogen för VM-agenten på den virtuella datorn.
   1.  Dubbelklicka på MSI-filen på den virtuella datorn.
1. För virtuella datorer som är anslutna till lokala domäner kontrollerar du att eventuella Internet-proxyinställningar även gäller för Windows Local System-kontot (S-1-5-18) i den virtuella datorn, enligt beskrivningen i [Konfigurera proxyn][deployment-guide-configure-proxy]. VM-agenten körs i den här kontexten och måste kunna ansluta till Azure.

Ingen användarinteraktion krävs för att uppdatera Azure VM-agenten. VM-agenten uppdateras automatiskt och kräver ingen omstart av den virtuella datorn.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Använd följande kommandon för att installera VM-agenten för Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) eller Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Om agenten redan är installerad, för att uppdatera Azure Linux Agent, gör stegen som beskrivs i [Uppdatera Azure Linux-agenten på en virtuell dator till den senaste versionen från GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurera proxyn

De åtgärder du vidtar för att konfigurera proxyn i Windows skiljer sig från hur du konfigurerar proxyn i Linux.

#### <a name="windows"></a>Windows

Proxyinställningarna måste vara korrekt inställda för att kontot lokalt system ska kunna ansluta till Internet. Om proxyinställningarna inte anges av grupprincipen kan du konfigurera inställningarna för kontot Lokalt system.

1. Gå till **Start**, ange **gpedit.msc**och välj sedan **Retur**.
1. Välj**Administrativa mallar för** >  **datorkonfiguration** > **i Windows Components** > **Internet Explorer**. Kontrollera att inställningen **Gör proxyinställningar per dator (i stället för per användare)** är inaktiverade eller inte konfigurerade.
1. Gå till**Internetalternativ**i **Nätverket och delningscenter** > på **Kontrollpanelen**.
1. Välj knappen **LAN-inställningar på** fliken **Anslutningar.**
1. Avmarkera kryssrutan **Automatisk identifiering av inställningar**.
1. Markera kryssrutan **Använd en proxyserver för ditt LAN** och ange sedan proxyadressen och porten.
1. Välj knappen **Avancerat.**
1. Ange IP-adressen **168.63.129.16**i rutan **Undantag** . Välj **OK**.

#### <a name="linux"></a>Linux

Konfigurera rätt proxy i konfigurationsfilen för Microsoft Azure Guest \\\\Agent, som finns på etc waagent.conf.

Ange följande parametrar:

1. **HTTP-proxyvärd**. Ställ till exempel in den på **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP-proxyport**. Ställ till exempel in den på **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Starta om agenten.

   ```console
   sudo service waagent restart
   ```

Proxyinställningarna \\i\\etc waagent.conf gäller även för de vm-tillägg som krävs. Om du vill använda Azure-databaserna kontrollerar du att trafiken till dessa databaser inte går via ditt lokala intranät. Om du har skapat användardefinierade vägar för att aktivera tvångstunneler kontrollerar du att du lägger till en rutt som dirigerar trafik till databaserna direkt till Internet och inte via vpn-anslutningen för plats till plats.

* **SLES**

  Du måste också lägga till vägar för \\IP-adresser som anges i etc\\regionserverclnt.cfg. Följande bild visar ett exempel:

  ![Tvingad tunneltrafik][deployment-guide-figure-50]


* **RHEL**

  Du måste också lägga till vägar för IP-adresserna\\för värdarna som\\anges i \\etc yum.repos.d rhui-load-balancers. Ett exempel finns i föregående bild.

* **Oracle Linux**

  Det finns inga databaser för Oracle Linux på Azure. Du måste konfigurera dina egna databaser för Oracle Linux eller använda de offentliga databaserna.

Mer information om användardefinierade vägar finns i [Användardefinierade vägar och IP-vidarebefordran][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurera Azure-tillägget för SAP

När du har förberett den virtuella datorn enligt beskrivningen i [distributionsscenarier för virtuella datorer för SAP på Azure][deployment-guide-3]installeras Azure VM-agenten på den virtuella datorn. Nästa steg är att distribuera Azure-tillägget för SAP, som är tillgängligt i Azure Extension Repository i de globala Azure-datacenter. Mer information finns i [Planering och implementering av Virtuella Azure-datorer för SAP NetWeaver][planning-guide-9.1].

Du kan använda PowerShell eller Azure CLI för att installera och konfigurera Azure Extension för SAP. Information om hur du installerar tillägget på en Virtuell Windows eller Linux med hjälp av en Windows-dator finns i [Azure PowerShell][deployment-guide-4.5.1]. Information om hur du installerar tillägget på en Virtuell Linux-dator med hjälp av ett Linux-skrivbord finns i [Azure CLI][deployment-guide-4.5.2].

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Virtuella Azure PowerShell för Linux och Windows

Så här installerar du Azure Extension för SAP med PowerShell:

1. Kontrollera att du har installerat den senaste versionen av Azure PowerShell-cmdleten. Mer information finns i [Distribuera Azure PowerShell-cmdlets][deployment-guide-4.1].  
1. Kör följande PowerShell-cmdlet:
    En lista över tillgängliga miljöer `commandlet Get-AzEnvironment`finns i . Om du vill använda global Azure är din miljö **AzureCloud**. För Azure i Kina väljer du **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

När du har angett dina kontodata och identifierar den virtuella Azure-datorn distribuerar skriptet de nödvändiga tilläggen och aktiverar de nödvändiga funktionerna. Det kan ta flera minuter.
Mer information `Set-AzVMAEMExtension`om finns i [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Framgångsrik körning av SAP-specifika Azure cmdlet Set-AzVMAEMExtension][deployment-guide-figure-900]

Konfigurationen `Set-AzVMAEMExtension` gör alla steg för att konfigurera värddatainsamling för SAP.

Skriptutdata innehåller följande information:

* Bekräftelse på att datainsamling för OS-disken och alla ytterligare datadiskar har konfigurerats.
* De följande två meddelandena bekräftar konfigurationen av lagringsmått för ett visst lagringskonto.
* En utdatarad ger status för den faktiska uppdateringen av VM-tillägget för SAP-konfiguration.
* En annan utdatarad bekräftar att konfigurationen har distribuerats eller uppdaterats.
* Den sista utdataraden är informativ. Den visar dina alternativ för att testa VM-tillägget för SAP-konfiguration.
* Om du vill kontrollera att alla steg i Azure VM-tillägget för SAP-konfiguration har körts och att Azure-infrastrukturen tillhandahåller nödvändiga data, fortsätter du med beredskapskontrollen för Azure Extension för SAP, enligt beskrivningen i [Beredskapskontroll för Azure Extension för SAP][deployment-guide-5.1].
* Vänta 15-30 minuter på att Azure Diagnostics samlar in relevanta data.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Virtuella Azure CLI för virtuella Linux-datorer

Så här installerar du Azure Extension för SAP med hjälp av Azure CLI:

   1. Installera Azure classic CLI enligt beskrivningen i [Installera Azure classic CLI][azure-cli].
   1. Logga in med ditt Azure-konto:

      ```console
      azure login
      ```

   1. Växla till Azure Resource Manager-läge:

      ```console
      azure config mode arm
      ```

   1. Aktivera Azure-tillägg för SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Installera med Azure CLI 2.0

   1. Installera Azure CLI 2.0 enligt beskrivningen i [Installera Azure CLI 2.0][azure-cli-2].
   1. Logga in med ditt Azure-konto:

      ```azurecli
      az login
      ```

   1. Installera Azure CLI AEM-tillägg
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installera tillägget med
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Kontrollera att Azure Extension för SAP är aktivt på Den virtuella Azure Linux-datorn. Kontrollera om \\filen\\\\var lib AzureEnhancedMonitor\\PerfCounters finns. Om det finns, i en kommandotolk, kör du det här kommandot för att visa information som samlats in av Azure Extension för SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Utdata ser ut så här:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontrollerar och felsöker för datainsamling från på till slutna data för SAP-värdagent

När du har distribuerat din virtuella Azure-dator och konfigurerat relevant Azure-tillägg för SAP kontrollerar du om alla komponenter i tillägget fungerar som förväntat.

Kör beredskapskontrollen för Azure Extension för SAP enligt beskrivningen i [Beredskapskontroll för Azure Extension för SAP][deployment-guide-5.1]. Om alla resultat för beredskapskontroll är positiva och alla relevanta prestandaräknare visas OK har Azure Extension för SAP konfigurerats. Du kan fortsätta med installationen av SAP-värdagenten enligt beskrivningen i SAP Notes i [SAP-resurser][deployment-guide-2.2]. Om beredskapskontrollen anger att räknar saknas kör du hälsokontrollen för Azure Extension för SAP, enligt beskrivningen i [Hälsokontroll för Azure Extension för SAP-konfiguration][deployment-guide-5.2]. Fler felsökningsalternativ finns i [Felsöka Azure Extension för SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Beredskapskontroll för Azure-tillägget för SAP

Den här kontrollen säkerställer att alla prestandamått som visas i ditt SAP-program tillhandahålls av det underliggande Azure Extension för SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Kör beredskapskontrollen på en Virtuell Windows-dator

1. Logga in på den virtuella Azure-datorn (med ett administratörskonto är inte nödvändigt).
1. Öppna ett kommandotolksfönster.
1. I kommandotolken ändrar du katalogen till installationsmappen för\\Azure\\Extension\\för SAP: C: Paket plugins Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\släpp

   *Versionen* i sökvägen till tillägget kan variera. Om du ser mappar för flera versioner av tillägget i installationsmappen kontrollerar du konfigurationen av AzureEnhancedMonitoring Windows-tjänsten och växlar sedan till mappen som anges som *Sökväg till körbar*.

   ![Egenskaper för tjänsten som kör Azure Extension för SAP][deployment-guide-figure-1000]

1. Kör **azperflib.exe** utan några parametrar i kommandotolken.

   > [!NOTE]
   > Azperflib.exe körs i en slinga och uppdaterar de insamlade räknarna var 60:e sekund. Avsluta loopen genom att stänga kommandotolksfönstret.
   >
   >

Om Azure Extension för SAP inte är installerat eller azureenhancedMonitoring-tjänsten inte körs, har tillägget inte konfigurerats korrekt. Detaljerad information om hur du distribuerar tillägget finns i [Felsöka Azure Extension för SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe är en komponent som inte kan användas för egna ändamål. Det är en komponent som levererar Azure-infrastrukturdata relaterade till den virtuella datorn för SAP-värdagenten uteslutande.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Kontrollera produktionen av azperflib.exe

Azperflib.exe-utdata visar alla ifyllda Azure-prestandaräknare för SAP. Längst ned i listan över insamlade räknare visar en sammanfattnings- och hälsoindikator status för Azure Extension för SAP.

![Utdata från hälsokontrollen genom att köra azperflib.exe, vilket tyder på att det inte finns några problem][deployment-guide-figure-1100]
<a name="figure-11"></a>

Kontrollera resultatet som returneras för totalutdata för **Räknare,** som rapporteras som tom, och för **hälsostatus**, som visas i föregående bild.

Tolka de resulterande värdena enligt följande:

| Azperflib.exe-resultatvärden | Azure-tillägg för SAP-hälsostatus |
| --- | --- |
| **API-anrop – är inte tillgängliga** | Räknare som inte är tillgängliga kanske antingen inte är tillämpliga på konfigurationen för den virtuella datorn eller fel. Se **Hälsostatus**. |
| **Räknare totalt - tom** |Följande två Azure-lagringsräknare kan vara tomma: <ul><li>Läs op-svarstidsserver msec för lagring</li><li>Lagring Läs Op Latency E2E msec</li></ul>Alla andra räknare måste ha värden. |
| **Hälsostatus** |Endast OK om returstatus visar **OK**. |
| **Diagnostik** |Detaljerad information om hälsostatus. |

Om **hälsostatusvärdet** inte är **OK**följer du instruktionerna i [Hälsokontroll för Azure Extension för SAP-konfiguration][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Kör beredskapskontrollen på en Virtuell Linux-dator

1. Anslut till den virtuella Azure-datorn med hjälp av SSH.

1. Kontrollera utdata från Azure Extension för SAP.

   a.  Kör `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Förväntat resultat**: Returnerar lista över prestandaräknare. Filen ska inte vara tom.

   b. Kör `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Förväntat resultat**: Returnerar en rad där felet **inte**finns, till exempel **3;config; Fel;;0;0; none;0;1456416792;tst-servercs;**

   c. Kör `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Förväntat resultat**: Returnerar som tomt eller finns inte.

Om föregående kontroll inte lyckades kör du dessa ytterligare kontroller:

1. Kontrollera att waagenten är installerad och aktiverad.

   a.  Kör `sudo ls -al /var/lib/waagent/`

     **Förväntat resultat**: Visar innehållet i katalogen waagent.

   b.  Kör `ps -ax | grep waagent`

   **Förväntat resultat**: Visar en post som liknar:`python /usr/sbin/waagent -daemon`

1. Kontrollera att Azure Extension för SAP är installerat och körs.

   a.  Kör `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Förväntat resultat**: Visar innehållet i Azure Extension för SAP-katalogen.

   b. Kör `ps -ax | grep AzureEnhanced`

   **Förväntat resultat**: Visar en post som liknar:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installera SAP-värdagent enligt beskrivningen i SAP Note [1031096]och kontrollera utdata från `saposcol`.

   a.  Kör `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Kör `dump ccm`

   c.  Kontrollera om måttet **Virtualization_Configuration\Enhanced Monitoring Access** är **sant**.

Om du redan har en SAP NetWeaver ABAP-programserver installerad öppnar du transaktionen ST06 och kontrollerar om förbättrad övervakning är aktiverad.

Om någon av dessa kontroller misslyckas och detaljerad information om hur du distribuerar om tillägget finns i [Felsöka Azure Extension för SAP][deployment-guide-5.3].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Hälsokontroll av Azure Extension för SAP-konfiguration

Om vissa av infrastrukturdata inte levereras korrekt enligt testet som beskrivs i [Beredskapskontroll för Azure Extension för SAP,][deployment-guide-5.1]kör cmdleten `Test-AzVMAEMExtension` för att kontrollera om Azure-infrastrukturen och Azure Extension för SAP är korrekt konfigurerade.

1. Kontrollera att du har installerat den senaste versionen av Azure PowerShell-cmdleten enligt beskrivningen i [Distribuera Azure PowerShell-cmdlets][deployment-guide-4.1].
1. Kör följande PowerShell-cmdlet: Om du vill ha en lista över `Get-AzEnvironment`tillgängliga miljöer kör du cmdleten . Om du vill använda global Azure väljer du **AzureCloud-miljön.** För Azure i Kina väljer du **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Ange dina kontodata och identifiera den virtuella Azure-datorn.

   ![Indatasida för SAP-specifika Azure cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skriptet testar konfigurationen av den virtuella datorn du väljer.

   ![Utdata från lyckat test av Azure Extension för SAP][deployment-guide-figure-1300]

Se till att varje hälsokontroll resultatet är **OK**. Om vissa kontroller inte visar **OK**kör du uppdaterings-cmdlet enligt beskrivningen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5]. Vänta 15 minuter och upprepa de kontroller som beskrivs i [Beredskapskontroll för Azure Extension för SAP][deployment-guide-5.1] och [hälsokontroll för Azure Extension för SAP-konfiguration][deployment-guide-5.2]. Om kontrollerna fortfarande tyder på ett problem med vissa eller alla räknare läser [du Felsöka Azure Extension för SAP][deployment-guide-5.3].

> [!Note]
> Du kan uppleva vissa varningar i fall där du använder hanterade azure-diskar. Varningar visas i stället för de tester som returnerar "OK". Detta är normalt och avsett för den disktypen. Se även [Felsökning av Azure Extension för SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Felsöka Azure-tillägg för SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure-prestandaräknare visas inte alls

AzureEnhancedMonitoring Windows-tjänsten samlar in prestandamått i Azure. Om tjänsten inte har installerats korrekt eller om den inte körs i den virtuella datorn kan inga prestandamått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installationskatalogen för Azure Extension för SAP är tom

###### <a name="issue"></a>Problem

\\Installationskatalogen C:\\Paket\\plugins Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt; \\version>släpp är tom.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om det är ett proxyproblem (som beskrivits tidigare). Du kan behöva starta om datorn `Set-AzVMAEMExtension` eller köra konfigurationsskriptet igen.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Tjänsten för Azure Extension för SAP finns inte

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns inte.

Azperflib.exe-utdata genererar ett fel:

![Körning av azperflib.exe anger att tjänsten för Azure Extension för SAP inte körs][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Lösning

Om tjänsten inte finns har Azure Extension för SAP inte installerats korrekt. Distribuera om tillägget med hjälp av de steg som beskrivs för distributionsscenariot i [distributionsscenarier för virtuella datorer för SAP i Azure][deployment-guide-3].

När du har distribuerat tillägget, efter en timme, kontrollera igen om Azure-prestandaräknarna finns i Azure VM.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Tjänsten för Azure Extension för SAP finns, men det går inte att starta

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns och är aktiverad, men kan inte starta. Mer information finns i programhändelseloggen.

###### <a name="solution"></a>Lösning

Konfigurationen är felaktig. Starta om Azure-tillägget för SAP i den virtuella datorn, enligt beskrivningen i [Konfigurera Azure Extension för SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Vissa Azure-prestandaräknare saknas

AzureEnhancedMonitoring Windows-tjänsten samlar in prestandamått i Azure. Tjänsten hämtar data från flera källor. Vissa konfigurationsdata samlas in lokalt och vissa prestandamått läss från Azure Diagnostics. Lagringsräknare används från din loggning på lagringsprenumerationsnivån.

Om felsökning med SAP Note [1999351] inte löser problemet kör `Set-AzVMAEMExtension` du konfigurationsskriptet igen. Du kanske måste vänta en timme eftersom lagringsanalys- eller diagnostikräknare kanske inte skapas direkt efter att de har aktiverats. Om problemet kvarstår öppnar du ett SAP-kundsupportmeddelande på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en virtuell Linux-dator.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure-prestandaräknare visas inte alls

Prestandamått i Azure samlas in av en demon. Om demonen inte körs kan inga prestandamått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installationskatalogen för Azure Extension för SAP är tom

###### <a name="issue"></a>Problem

Katalogen \\\\var\\lib\\ waagent har ingen underkatalog för Azure Extension för SAP.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om det är ett proxyproblem (som beskrivits tidigare). Du kan behöva starta om datorn och/eller köra konfigurationsskriptet `Set-AzVMAEMExtension` igen.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Körningen av Set-AzVMAEMExtension och Test-AzVMAEMExtension visar varningsmeddelanden om att standardhanterade diskar inte stöds

###### <a name="issue"></a>Problem

När du kör Set-AzVMAEMExtension eller Test-AzVMAEMExtension meddelanden som dessa visas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Köra azperfli.exe som beskrivits tidigare kan du få ett resultat som indikerar ett icke-felfritt tillstånd. 

###### <a name="solution"></a>Lösning

Meddelandena orsakas av det faktum att standardhanterade diskar inte levererar DE API:er som används av SAP-tillägget för SAP för att kontrollera statistik för Standard Azure Storage-konton. Detta är inte en fråga om oro. Anledningen till att införa insamlingsdata för standarddisklagringskonton var begränsning av I/Os som inträffade ofta. Hanterade diskar undviker sådana begränsningar genom att begränsa antalet diskar i ett lagringskonto. Att inte ha den typen av data är därför inte kritiskt.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Vissa Azure-prestandaräknare saknas

Prestandamått i Azure samlas in av en demon, som hämtar data från flera källor. Vissa konfigurationsdata samlas in lokalt och vissa prestandamått läss från Azure Diagnostics. Lagringsräknare kommer från loggarna i din lagringsprenumeration.

En komplett och uppdaterad lista över kända problem finns i SAP Note [1999351], som har ytterligare felsökningsinformation för Azure Extension för SAP.

Om felsökning med SAP Note [1999351] inte löser problemet, `Set-AzVMAEMExtension` kör konfigurationsskriptet igen enligt beskrivningen i [Konfigurera Azure-tillägg för SAP][deployment-guide-4.5]. Du kanske måste vänta i en timme eftersom lagringsanalys- eller diagnostikräknare kanske inte skapas direkt efter att de har aktiverats. Om problemet kvarstår öppnar du ett SAP-kundsupportmeddelande på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en virtuell Linux-dator.
