---
title: Azure virtuella datorer, tillägg och funktioner | Microsoft Docs
description: Lär dig vad Azure VM-tillägg är och hur du använder dem med Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: a35cba0ab7df80596ba1403765980809635c0249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618004"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure virtuella datorer, tillägg och funktioner
Azure-dator (VM)-tillägg är litet program som ger efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines, du kan använda befintliga avbildningar och sedan anpassa dem som en del av distributionen, hämta din verksamhet för anpassad att skapa avbildningen.

Azure-plattformen är värd för många tillägg mellan VM-konfiguration, övervakning, säkerhet och verktyget program. Utgivare ta ett program, sedan skriva det till ett tillägg och förenkla installationen, så allt du behöver göra är att tillhandahålla obligatoriska parametrar. 

 Det finns ett stort urval av första och tredje part-tillägg, om programmet i tilläggscentrallagret inte finns och du kan använda tillägget för anpassat skript och konfigurera din virtuella dator med dina egna skript och kommandon.

Exempel på viktiga scenarier som tillägg används för:
* Konfiguration av virtuell dator, du kan använda Powershell DSC (Desired State Configuration), Chef, Puppet och anpassade skripttillägg installera VM-agenter för konfiguration och konfigurera den virtuella datorn. 
* AV produkter, t.ex Symantec, ESET.
* Verktyg för virtuell dator säkerhetsproblem, till exempel Qualys, Rapid7, HPE.
* Virtuell dator och Appövervakning, verktyg, till exempel DynaTrace, Azure Network Watcher, Site24x7 och Stackify.

Tillägg kan kopplas samman med en ny VM-distribution. De kan till exempel vara en del av en större distribution, konfigurera program på VM-etablera eller kör mot alla stöds tillägget drivs system efter distribution.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hur hittar jag vilka tillägg som finns tillgängliga?
Du kan visa tillgängliga tillägg i VM-bladet i portalen under tillägg, representerar bara lite, för en fullständig lista kan du använda CLI-verktyg, se [identifiering av VM-tillägg för Linux](features-linux.md) och [ Identifiering av VM-tillägg för Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hur kan jag installera ett tillägg?
Azure VM-tillägg kan hanteras med hjälp av Azure CLI, Azure PowerShell, Azure Resource Manager-mallar och Azure-portalen. Om du vill prova ett tillägg, kan du gå till Azure portal, Välj det anpassade Skripttillägget, skicka in ett kommando / skript och köra tillägg.

Om du vill att samma filnamnstillägg som du lade till i portalen med CLI eller Resource Manager-mall, se annat tillägg dokumentation, till exempel [Windows-tillägget för anpassat skript](custom-script-windows.md) och [Linux anpassade skripttillägg](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hur hanterar livscykeln för tillägget?
Du behöver inte att ansluta till en virtuell dator direkt för att installera eller ta bort tillägget. När Azure-tillägget programmets hela livscykel hanteras utanför den virtuella datorn och integreras i Azure-plattformen kan få du också integrerad status för tillägget.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Något annat jag borde överväga för tillägg?
Tillägg installera program, som alla program som det finns vissa krav för tillägg till det är en lista över stöds Windows och Linux-OSes och du måste ha Azure VM-agenter som installerats. Vissa enskilda VM-tillägg-program kan ha sina egna miljöförutsättningar, till exempel åtkomst till en slutpunkt.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur Linux-agenten och tillägg fungerar finns i [Azure VM-tillägg och funktioner i Linux](features-linux.md).
* Mer information om hur Windows-Gästagenten och tillägg fungerar finns i [Azure VM-tillägg och funktioner i Windows](features-windows.md).  
* Om du vill installera Windows-Gästagenten, se [översikt över Azure Windows VM-agenten](agent-windows.md).  
* Om du vill installera Linux-agenten finns i [översikt över Azure Linux VM-agenten](agent-linux.md).  

