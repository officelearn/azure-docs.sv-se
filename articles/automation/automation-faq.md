---
title: Azure Automation vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 46786ff5bd158804ea5d93377fbbcc39a9c8af26
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712884"
---
# <a name="azure-automation-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Automation

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Automation. Om du har ytterligare frågor om dess funktioner går du till diskussions forumet och publicerar dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="update-management"></a>Uppdateringshantering

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på äldre operativ system.

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar av operativ Systems nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där operativ systemets versions nummer ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på en Linux-dator är detta avsiktligt avsiktligt.

Använd **undantags** funktionen för att undvika att uppdatera operativ system versionen genom uppdateringshantering distributioner.

I Red Hat Enterprise Linux, det paket namn som ska undantas `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte kritiska/säkerhets uppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdaterings klassificeringar. Med det här alternativet filtreras de uppdateringar som uppfyller de angivna kriterierna. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdaterings berikning i molnet kan du flagga vissa uppdateringar i Uppdateringshantering som en säkerhets påverkan, även om den lokala datorn inte har den informationen. Om du använder kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har marker ATS som en säkerhets påverkan på den datorn och som därför inte tillämpas. Uppdateringshantering kan dock fortfarande rapportera datorn som inkompatibel, eftersom den innehåller ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdaterings klassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. För SUSE väljer du bara **andra uppdateringar** som klassificering kan orsaka att vissa ytterligare säkerhets uppdateringar installeras om säkerhets uppdateringar som rör zypper (paket hanteraren) eller dess beroenden krävs först. Det här beteendet är en begränsning i zypper. I vissa fall kan du behöva köra uppdaterings distributionen igen och sedan verifiera distributionen via uppdaterings loggen.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar i Azure-klienter?

Om du har datorer som behöver korrigeringar i en annan Azure-klient rapporterar till Uppdateringshantering måste du använda en lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) med `ForUpdateConfiguration` parametern som anges för att skapa ett schema. Du kan använda cmdleten [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) och skicka datorerna i den andra klienten till- `NonAzureComputer` parametern. I följande exempel visas hur du gör detta.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du referera till följande källor för ytterligare frågor och svar.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Feedback-forum](https://feedback.azure.com/forums/905242-update-management)
