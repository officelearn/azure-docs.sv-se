---
title: Tillägg och funktioner för virtuella Azure-datorer | Microsoft Docs
description: Lär dig vad Azure VM-tillägg är och hur du använder dem med Azure Virtual Machines
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
ms.openlocfilehash: deb49267a262705370e48e150cc5ed6c4dc04247
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168883"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Tillägg och funktioner för virtuella Azure-datorer
Tillägg för virtuella Azure-datorer (VM) är små program som tillhandahåller konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. du kan använda befintliga avbildningar och sedan anpassa dem som en del av dina distributioner, så att du får ut så mycket som möjligt av egna Skapa bild.

Azure-plattformen är värd för många tillägg som sträcker sig från konfigurations-, övervaknings-, säkerhets-och verktygs program för virtuella datorer. Utgivare tar ett program och omsluter den till ett tillägg och fören klar installationen, så allt du behöver göra är att tillhandahålla obligatoriska parametrar. 

 Det finns ett stort urval av de första och tredje parts tilläggen, om programmet i tilläggs lagrings platsen inte finns kan du använda det anpassade skript tillägget och konfigurera den virtuella datorn med dina egna skript och kommandon.

Exempel på nyckel scenarier som tillägg används för:
* Konfiguration av virtuell dator. du kan använda PowerShell DSC (önskad tillstånds konfiguration), chef, Puppet och anpassade skript tillägg för att installera konfigurations agenter för virtuella datorer och konfigurera din virtuella dator. 
* AV-produkter, till exempel Symantec, ESET.
* Verktyget sårbarhets sårbarhet, till exempel Qualys, Rapid7, HPE.
* Verktyg för övervakning av virtuella datorer och appar, till exempel DynaTrace, Azure Network Watcher, Site24x7 och Stackify.

Tillägg kan paketeras med en ny VM-distribution. De kan till exempel vara en del av en större distribution, konfigurera program på VM-etablering eller köra mot alla tillägg som stöds och som hanteras av system efter distribution.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hur ser jag vilka tillägg som är tillgängliga?
Du kan visa tillgängliga tillägg på bladet VM i portalen, under tillägg, Detta representerar bara en liten mängd, för den fullständiga listan kan du använda CLI-verktygen, se [identifiera VM-tillägg för Linux](features-linux.md) och [identifiera VM-tillägg för Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hur kan jag installera ett tillägg?
Azures tillägg för virtuella datorer kan hanteras med hjälp av Azure CLI, Azure PowerShell, Azure Resource Manager mallar och Azure Portal. Om du vill testa ett tillägg kan du gå till Azure Portal, välja det anpassade skript tillägget och sedan skicka in ett kommando/skript och köra tilläggen.

Om du vill använda samma tillägg som du har lagt till i portalen via CLI eller Resource Manager-mallen, se olika tilläggs dokumentation, till exempel [Windows anpassat skript tillägg](custom-script-windows.md) och [anpassat skript tillägg för Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hur gör jag för att hantera tilläggs programmets livs cykel?
Du behöver inte ansluta till en virtuell dator direkt för att installera eller ta bort tillägget. När livs cykeln för Azure Extensions-programmet hanteras utanför den virtuella datorn och integreras i Azure-plattformen får du också integrerad status för tillägget.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Allt annat jag bör tänka på vid tillägg?
Tillägg installerar program, som alla program det finns vissa krav för tillägg finns det en lista över Windows-och Linux-operativ system som stöds och du måste ha installerat Azure VM-agenter. Vissa enskilda program för VM-tillägg kan ha sina egna miljö krav, till exempel åtkomst till en slut punkt.

## <a name="troubleshoot-extensions"></a>Felsökning av tillägg

Felsöknings information för varje tillägg finns i avsnittet **fel sökning och support** i översikten för tillägget. Här är en lista över den felsöknings information som är tillgänglig:

| Namnrymd | Felsökning |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Azure Monitor beroende för Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor beroende för Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption för Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Azure Disk Encryption för Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. Compute. CustomScriptExtension | [Anpassat skript för Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Önskad tillstånds konfiguration för Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Önskad tillstånds konfiguration för Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [NVIDIA GPU-drivrutins tillägg för Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [NVIDIA GPU-drivrutins tillägg för Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Tillägg för program mot skadlig kod för Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Azure Monitor för Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Azure Monitor för Windows](oms-windows.md#troubleshoot-and-support) |
| Stackify. linuxagent. extension. stackifylinuxagentextension | [Stackify-omspårning för Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| tillägget vmaccessforlinux. Microsoft. ostcextensions | [Återställ lösen ord (VMAccess) för Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Ögonblicks bild för Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Ögonblicks bild för Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Nästa steg
* Mer information om hur Linux-agenten och-tillägg fungerar finns i [tillägg och funktioner för Azure VM i Linux](features-linux.md).
* Mer information om hur Windows gästa Gent och tillägg fungerar finns i [tillägg för virtuella Azure-datorer och Windows-funktioner](features-windows.md).  
* Information om hur du installerar gäst agenten för Windows finns i [Översikt över Azure Windows Virtual Machine agent](agent-windows.md).  
* Information om hur du installerar Linux-agenten finns i [Översikt över Azure Linux Virtual Machine agent](agent-linux.md).  

