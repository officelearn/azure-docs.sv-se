---
title: Vanliga frågor och svar om Azure Automation | Microsoft-dokument
description: Svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925818"
---
# <a name="azure-automation-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Automation

Den här Vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Automation. Om du har några ytterligare frågor om dess kapacitet, gå till diskussionsforumet och skicka dina frågor. När en fråga ofta ställs lägger vi till den i den här artikeln så att den kan hittas snabbt och enkelt.

## <a name="update-management-solution"></a>Uppdateringshanteringslösning

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på OS-nivå?

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar på OS-nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där os-versionsnumret ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör skulle använda lokalt på Linux-datorn, är detta beteende avsiktligt.

Om du vill undvika att uppdatera OS-versionen via distributioner av uppdateringshantering använder du **uteslutningsfunktionen.**

I Red Hat Enterprise Linux är paketnamnet som ska uteslutas redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte viktiga/säkerhetsuppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdateringsklassificeringar. Det här alternativet filtrerar de uppdateringar som tillämpas på datorn som uppfyller de angivna villkoren. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdateringsanrikning i molnet kan vissa uppdateringar flaggas i Uppdateringshantering som säkerhetseffekter, även om den lokala datorn inte har den informationen. Om du installerar viktiga uppdateringar på en Linux-dator kan det därför finnas uppdateringar som inte är markerade med en säkerhetspåverkan på den datorn och därför tillämpas inte uppdateringarna. Uppdateringshantering kan dock fortfarande rapportera att datorn inte är kompatibel eftersom den har ytterligare information om den relevanta uppdateringen.

Distribuera uppdateringar efter uppdateringsklassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringar tillämpas. För SUSE kan du välja *endast* **Andra uppdateringar** eftersom klassificeringen kan göra att vissa säkerhetsuppdateringar också installeras om säkerhetsuppdateringar relaterade till zypper (pakethanteraren) eller dess beroenden krävs först. Detta är en begränsning av zypper. I vissa fall kan du behöva köra uppdateringsdistributionen igen. Kontrollera uppdateringsloggen om du vill verifiera.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar över Azure-klienter?

Om du har datorer i en annan Azure-klient som rapporterar till Uppdateringshantering som du behöver korrigera måste du använda följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema och använda cmdleten [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klienten `-NonAzureComputer` till parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du läsa följande forum för ytterligare frågor och svar.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

För allmän feedback om Update Management-lösningen, besök [feedbackforumet](https://feedback.azure.com/forums/905242-update-management).