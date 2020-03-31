---
title: Azure-tillägg och -funktioner för virtuella datorer
description: Lär dig vad Azure VM-tillägg är och hur du använder dem med virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072966"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure-tillägg och -funktioner för virtuella datorer
Azure virtual machine(VM) tillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen på virtuella Azure-datorer, du kan använda befintliga avbildningar och sedan anpassa dem som en del av dina distributioner, vilket gör att du får ut av anpassade bildbyggnad.

Azure-plattformen är värd för många tillägg som sträcker sig från VM-konfiguration, övervakning, säkerhet och verktygsprogram. Publishers tar ett program, sedan radbryta det i ett tillägg och förenkla installationen, så allt du behöver göra är att tillhandahålla obligatoriska parametrar. 

 Det finns ett stort urval av första och tredje part tillägg, om programmet i tillägget lagringsplatsen inte finns, kan du använda custom script förlängning och konfigurera din virtuella dator med dina egna skript och kommandon.

Exempel på viktiga scenarier som tillägg används för:
* VM-konfiguration kan du använda Powershell DSC (Önskad tillståndskonfiguration), Chef, Puppet och Custom Script Extensions för att installera VM-konfigurationsagenter och konfigurera din virtuella dator. 
* AV-produkter som Symantec, ESET.
* Vm sårbarhet verktyg, såsom Qualys, Rapid7, HPE.
* Verktyg för vm- och appövervakning, till exempel DynaTrace, Azure Network Watcher, Site24x7 och Stackify.

Tillägg kan paketeras med en ny VM-distribution. De kan till exempel vara en del av en större distribution, konfigurera program på vm-etablering eller köra mot alla system som drivs efter distribution som stöds.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hur hittar jag Vilka tillägg som är tillgängliga?
Du kan visa tillgängliga tillägg i vm-bladet i portalen, under tillägg, detta representerar bara en liten mängd, för hela listan kan du använda CLI-verktyg, se [Identifiera VM-tillägg för Linux](features-linux.md) och Upptäcka [VM-tillägg för Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hur installerar jag ett tillägg?
Azure VM-tillägg kan hanteras med antingen Azure CLI, Azure PowerShell, Azure Resource Manager-mallar och Azure-portalen. Om du vill prova ett tillägg kan du gå till Azure-portalen, välja anpassat skripttillägg och sedan skicka in ett kommando/skript och köra tilläggen.

Om du vill samma tillägg som du har lagt till i portalen via CLI- eller Resource Manager-mallen läser du olika tilläggsdokumentationer, till exempel [Windows Custom Script Extension](custom-script-windows.md) och Linux Custom Script [Extension](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hur hanterar jag livscykeln för tilläggsprogram?
Du behöver inte ansluta till en virtuell dator direkt för att installera eller ta bort tillägget. Eftersom livscykeln för Azure-tilläggsprogrammet hanteras utanför den virtuella datorn och integreras i Azure-plattformen får du också integrerad status för tillägget.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Något annat jag borde tänka på för förlängningar?
Tillägg installerar program, precis som alla program som finns vissa krav, för tillägg finns en lista över Windows och Linux-operativsystem som stöds, och du måste ha Azure VM-agenter installerade. Vissa enskilda VM-tilläggsprogram kan ha sina egna miljökrav, till exempel åtkomst till en slutpunkt.

## <a name="troubleshoot-extensions"></a>Felsökning av tillägg

Felsökningsinformation för varje tillägg finns i avsnittet **Felsök och support** i översikten för tillägget. Här är en lista över den felsökningsinformation som finns tillgänglig:

| Namnområde | Felsökning |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Beroende för Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Beroende av Azure Monitor för Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure Diskkryptering för Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption microsoft.azure.security.azurediskencryption microsoft.azure.security.azurediskencryption microsoft. | [Azure Diskkryptering för Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Anpassat skript för Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Önskad tillståndskonfiguration för Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Önskad tillståndskonfiguration för Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA GPU Drivrutinstillägg för Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows microsoft.hpccompute.nvidiagpudriverwindows microsoft.hpccompute.nvidiagpudriverwindows microsoft. | [NVIDIA GPU-drivrutinstillägg för Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Tillägg mot skadlig kod för Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure-övervakare för Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor för Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace för Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions vmaccessforlinux.microsoft.ostcextensions vmaccessforlinux.microsoft.ostcextensions vmaccess | [Återställ lösenord (VMAccess) för Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Ögonblicksbild för Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Ögonblicksbild för Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Nästa steg
* Mer information om hur Linux-agenten och tilläggen fungerar finns i [Azure VM-tillägg och funktioner för Linux](features-linux.md).
* Mer information om hur Windows Guest Agent och Extensions fungerar finns i [Azure VM-tillägg och funktioner för Windows](features-windows.md).  
* Information om hur du installerar Windows-gästagenten finns i [Översikt över Azure Windows Virtual Machine Agent](agent-windows.md).  
* Information om hur du installerar Linux-agenten finns i [Azure Linux Virtual Machine Agent Overview](agent-linux.md).  

