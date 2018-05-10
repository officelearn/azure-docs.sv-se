---
title: Tillägg för virtuell Azure-dator och funktioner | Microsoft Docs
description: Lär dig vad Azure VM-tillägg anre använda dem med virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 01178995dbf9203082a6250ef256522bc1101e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Tillägg för virtuell Azure-dator och funktioner
Virtuell Azure-dator (VM)-tillägg är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines, kan du använda befintliga avbildningar och sedan anpassa dem som en del av din distribution, hämtar du out-of-business av anpassade skapande av avbildningen.

Azure-plattformen är värd för många tillägg från VM-konfiguration, övervakning, säkerhet och verktyget program. Utgivare ta ett tillämpningsprogram, sedan skriva den till ett tillägg och förenkla installationen, så att allt du behöver göra är att ange obligatoriska parametrar. 

 Det finns ett stort urval av första och tredje parts tillägg om programmet i tilläggscentrallagret inte finns och du kan använda tillägget för anpassat skript och konfigurera den virtuella datorn med dina egna skript och kommandon.

Exempel på viktiga scenarier som du använder tillägg:
* VM-konfiguration, du kan använda Powershell DSC (Desired State Configuration), Chef, Puppet och anpassade tillägg som skriptet att installera agenter för VM-konfiguration och konfigurera den virtuella datorn. 
* AV produkter, t.ex Symantec TERSTÄLL.
* Verktyg för VM säkerhetsproblem, till exempel Qualys, Rapid7, HPE.
* VM och Appövervakning verktygsuppsättning som DynaTrace, Azure Nätverksbevakaren, Site24x7 och Stackify.

Tillägg kan kopplas samman med en ny VM-distribution. De kan exempelvis vara en del av en större distribution konfigurerar program på VM etablera eller köra mot någon stöds tillägget drivas system efter distributionen.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hur hittar jag vilka tillägg som är tillgängliga?
Du kan visa tillgängliga tillägg i VM-blad i portalen under tillägg, representerar bara ett litet för en fullständig lista kan du använda CLI-verktygen, se [identifiering av VM-tillägg för Linux](features-linux.md) och [ Identifiering av VM-tillägg för Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hur kan jag installera tillägget?
Azure VM-tillägg kan hanteras med hjälp av Azure CLI 2.0, Azure PowerShell, Azure Resource Manager-mallar och Azure-portalen. Om du vill prova ett tillägg, kan du gå till Azure portal, Välj tillägget för anpassat skript, skicka in ett kommando / skript och köra tillägg.

Om du vill att samma filnamnstillägg som du lade till i den portal med CLI eller Resource Manager-mallen finns dokumentationen för olika tillägg som [Windows-tillägget för anpassat skript](custom-script-windows.md) och [tilläggetförLinuxanpassatskript](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hur hanterar livscykeln för tillägget program?
Du behöver inte att ansluta till en virtuell dator direkt för att installera eller ta bort tillägget. Eftersom Azure-tillägget programmet livscykel hanteras utanför den virtuella datorn och integrerat i Azure-plattformen, får du också integrerad status för tillägget.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Något annat jag borde överväga för tillägg?
Tillägg installera program, som alla program som det finns vissa krav för tillägg till det är en lista över Windows och Linux OSes som stöds och du behöver ha Azure VM agenterna. Vissa enskilda VM-tillägget program kan ha sina egna miljöförutsättningar, till exempel åtkomst till en slutpunkt.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur Linux-Agent och tillägg fungerar finns [Azure VM-tillägg och funktioner för Linux](features-linux.md).
* Mer information om hur Windows-Gästagenten och tillägg fungerar finns [Azure VM-tillägg och funktioner för Linux](features-windows.md).  
* Om du vill installera Windows Gästagenten [översikt över Azure Windows virtuella Agent ](agent-windows.md).  
* Om du vill installera Linux-agenten finns [översikt över Azure Linux virtuella datorer Agent ](agent-linux.md).  

