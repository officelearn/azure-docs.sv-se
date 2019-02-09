---
title: Köra skript i en virtuell Linux-dator
description: Det här avsnittet beskriver hur du kör skript i en virtuell dator
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3881a6d0ee1139fa481908f09b6e96efda24a5cd
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979011"
---
# <a name="run-scripts-in-your-linux-vm"></a>Köra skript i din Linux-VM

För att automatisera uppgifter eller felsöka problem, kan du behöva köra kommandon i en virtuell dator. I följande artikel ger en kort översikt över de funktioner som är tillgängliga att köra skript och -kommandon inom dina virtuella datorer.

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Den [tillägget för anpassat skript](../extensions/custom-script-linux.md) används främst för efter distribution konfigurations- och installationen.

* Ladda ned och köra skript i Azure-datorer.
* Kan köras med Azure Resource Manager-mallar, Azure CLI, REST API, PowerShell eller Azure-portalen.
* Skriptfiler kan hämtas från Azure storage eller GitHub, eller tillhandahålls från datorn när den körs från Azure-portalen.
* Kör PowerShell-skript i Windows-datorer och Bash-skript i Linux-datorer.
* Användbart för konfiguration efter distribution, Programvaruinstallation, och andra konfiguration och hanteringsaktiviteter.

## <a name="run-command"></a>Kör kommando

Den [kör kommandot](run-command.md) funktionen gör det möjligt för virtuell dator och programhantering och felsökning med hjälp av skript och är tillgänglig även när datorn kan inte nås, till exempel om gästdatorns brandvägg inte har RDP eller SSH-porten Öppna.

* Köra skript i Azure virtual machines.
* Kan köras med [Azure-portalen](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), eller [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Snabbt köra ett skript och visa utdata och upprepa efter behov i Azure-portalen.
* Skriptet kan skrivas direkt eller kan du köra en av de inbyggda skript.
* Kör PowerShell-skript i Windows-datorer och Bash-skript i Linux-datorer.
* Användbart för virtuell dator och programhantering och för att köra skript på virtuella datorer som inte kan nås.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) tillhandahåller allmänna dator, program och miljö med användarens anpassade skript som lagras i ett Automation-konto.

* Köra skript i Azure och Azure-datorer.
* Kan köras med hjälp av Azure portal, Azure CLI, REST API, PowerShell, webhook.
* Skript lagras och hanteras i ett Automation-konto.
* Köra PowerShell, PowerShell-arbetsflöde, Python och grafiska runbooks
* Ingen tidsgräns på skriptet körtid.
* Flera skript kan köras samtidigt.
* Fullständigt skriptutdata returneras och lagras.
* Jobbhistorik är tillgängliga i 90 dagar.
* Skript kan köras som lokalt System eller med informationen.
* Kräver [manuell installation](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriekonsol

Den [seriekonsolen](serial-console.md) ger direktåtkomst till en virtuell dator, ungefär som med ett tangentbord som är anslutna till den virtuella datorn.

* Kör kommandon i Azure virtual machines.
* Kan köras med hjälp av en textbaserad konsol till datorn i Azure-portalen.
* Logga in på datorn med ett lokalt användarkonto.
* Användbart åtkomst till den virtuella datorn krävs oavsett den datorns tillstånd för nätverk eller operativsystem.

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika funktionerna som är tillgängliga att köra skript och -kommandon inom dina virtuella datorer.

* [Anpassat skripttillägg](../extensions/custom-script-linux.md)
* [Kör kommando](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriekonsol](serial-console.md)