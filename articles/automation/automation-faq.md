---
title: Azure Automation vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925818"
---
# <a name="azure-automation-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Automation

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Automation. Om du har ytterligare frågor om dess funktioner går du till diskussions forumet och publicerar dina frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

## <a name="update-management-solution"></a>Uppdateringshantering lösning

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på äldre operativ system.

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar av operativ Systems nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där versions numret för operativ systemet ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör skulle använda lokalt på Linux-datorn är detta avsiktligt.

Använd **undantags** funktionen för att undvika att uppdatera operativ system versionen genom uppdateringshantering distributioner.

I Red Hat Enterprise Linux är paket namnet som ska undantas RedHat-release-Server. x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte kritiska/säkerhets uppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdaterings klassificeringar. Med det här alternativet filtreras de uppdateringar som tillämpas på den dator som uppfyller de angivna villkoren. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdaterings berikning i molnet, kan vissa uppdateringar flaggas i Uppdateringshantering som en säkerhets påverkan, även om den lokala datorn inte har den informationen. Det innebär att om du använder kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har marker ATS som en säkerhets påverkan på den datorn och därför tillämpas inte uppdateringarna. Uppdateringshantering kan dock fortfarande rapportera datorn som icke-kompatibel, eftersom den innehåller ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdaterings klassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. För SUSE väljer du *bara* **andra uppdateringar** som klassificering kan orsaka att vissa säkerhets uppdateringar också installeras om säkerhets uppdateringar som rör zypper (paket hanteraren) eller dess beroenden krävs först. Det här beteendet är en begränsning i zypper. I vissa fall kan du behöva köra uppdaterings distributionen igen. Verifiera uppdaterings loggen genom att kontrol lera uppdaterings loggen.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar i Azure-klienter?

Om du har datorer i en annan Azure-klient rapporterar för att Uppdateringshantering att du behöver korrigera, måste du använda följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema och använda cmdleten [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klienten till `-NonAzureComputer`-parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du referera till följande forum för ytterligare frågor och svar.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Om du vill ha allmän feedback om Uppdateringshantering lösning kan du besöka [feedback-forumet](https://feedback.azure.com/forums/905242-update-management).