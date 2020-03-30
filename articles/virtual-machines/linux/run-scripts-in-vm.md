---
title: Köra skript i en virtuell Azure Linux-dator
description: I det här avsnittet beskrivs hur du kör skript i en virtuell dator
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6175ae4263aee0ecdca5b1d768438ad20352375c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67477024"
---
# <a name="run-scripts-in-your-linux-vm"></a>Kör skript i din Virtuella Linux-dator

Om du vill automatisera uppgifter eller felsöka problem kan du behöva köra kommandon i en virtuell dator. Följande artikel ger en kort översikt över de funktioner som är tillgängliga för att köra skript och kommandon i dina virtuella datorer.

## <a name="custom-script-extension"></a>Anpassat skripttillägg

[Det anpassade skripttillägget](../extensions/custom-script-linux.md) används främst för konfiguration efter distribution och programvaruinstallation.

* Hämta och kör skript i virtuella Azure-datorer.
* Kan köras med Azure Resource Manager-mallar, Azure CLI, REST API, PowerShell eller Azure-portalen.
* Skriptfiler kan hämtas från Azure-lagring eller GitHub, eller tillhandahållas från datorn när de körs från Azure-portalen.
* Kör PowerShell-skript i Windows-maskiner och Bash-skript i Linux-datorer.
* Användbart för konfiguration efter distribution, programvaruinstallation och andra konfigurations- eller hanteringsuppgifter.

## <a name="run-command"></a>Kör kommandot 

Funktionen [Kör kommando](run-command.md) möjliggör hantering av virtuella datorer och program med skript och är tillgänglig även när datorn inte kan nås, till exempel om gästbrandväggen inte har RDP- eller SSH-porten öppen.

* Kör skript i virtuella Azure-datorer.
* Kan köras med [Azure Portal,](run-command.md) [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)eller [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Kör snabbt ett skript och visa utdata och upprepa efter behov i Azure-portalen.
* Skriptet kan skrivas direkt eller så kan du köra ett av de inbyggda skripten.
* Kör PowerShell-skript i Windows-maskiner och Bash-skript i Linux-datorer.
* Användbart för hantering av virtuella datorer och program och för att köra skript i virtuella datorer som inte kan nås.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) tillhandahåller allmän maskin-, program- och miljöhantering med användarens anpassade skript som lagras i ett Automation-konto.

* Kör skript i Azure och datorer som inte är Azure.
* Kan köras med Azure portal, Azure CLI, REST API, PowerShell, webhook.
* Skript som lagras och hanteras i ett automationskonto.
* Kör PowerShell, PowerShell-arbetsflöde, Python eller grafiska runbooks
* Ingen tidsgräns för skriptkörningstid.
* Flera skript kan köras samtidigt.
* Fullständig skriptutdata returneras och lagras.
* Jobbhistorik tillgänglig i 90 dagar.
* Skript kan köras som lokalt system eller med användarin angivna autentiseringsuppgifter.
* Kräver [manuell installation](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriekonsol

[Seriekonsolen](serial-console.md) ger direkt åtkomst till en virtuell dator, ungefär som att ha ett tangentbord anslutet till den virtuella datorn.

* Kör kommandon i virtuella Azure-datorer.
* Kan köras med en textbaserad konsol till datorn i Azure-portalen.
* Logga in på datorn med ett lokalt användarkonto.
* Användbart när åtkomst till den virtuella datorn behövs oavsett datorns nätverks- eller operativsystemtillstånd.

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika funktioner som är tillgängliga för att köra skript och kommandon i dina virtuella datorer.

* [Anpassat skripttillägg](../extensions/custom-script-linux.md)
* [Kör kommando](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriell konsol](serial-console.md)