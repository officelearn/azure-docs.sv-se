---
title: Vanliga frågor och svar om Azure Automation | Microsoft-dokument
description: Svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405970"
---
# <a name="azure-automation-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Automation

Den här Vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Automation. Om du har några ytterligare frågor om dess kapacitet, gå till diskussionsforumet och skicka dina frågor. När en fråga ofta ställs lägger vi till den i den här artikeln så att den kan hittas snabbt och enkelt.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Uppdateringshanteringslösning

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på OS-nivå?

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar på OS-nivå ske via paket. Detta kan leda till uppdateringshantering körs där OS-versionsnumret ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på en Linux-dator, är detta beteende avsiktligt.

Om du vill undvika att uppdatera OS-versionen via distributioner av uppdateringshantering använder du **uteslutningsfunktionen.**

I Red Hat Enterprise Linux är `redhat-release-server.x86_64`paketnamnet att utesluta .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte viktiga/säkerhetsuppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdateringsklassificeringar. Det här alternativet filtrerar de uppdateringar som uppfyller de angivna villkoren. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdateringsanrikning i molnet kan du flagga vissa uppdateringar i Uppdateringshantering som säkerhetspåverkan, även om den lokala datorn inte har den informationen. Om du installerar viktiga uppdateringar på en Linux-dator kan det finnas uppdateringar som inte är markerade som säkerhetseffekter på den datorn och därför inte tillämpas. Uppdateringshantering kan dock fortfarande rapportera den datorn som inkompatibel eftersom den har ytterligare information om den relevanta uppdateringen.

Distribuera uppdateringar efter uppdateringsklassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringar tillämpas. För SUSE kan du välja ENDAST **andra uppdateringar** som klassificering göra att vissa ytterligare säkerhetsuppdateringar installeras om säkerhetsuppdateringar relaterade till zypper (pakethanteraren) eller dess beroenden krävs först. Detta är en begränsning av zypper. I vissa fall kan du behöva köra uppdateringsdistributionen igen och sedan verifiera distributionen via uppdateringsloggen.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar över Azure-klienter?

Om du har datorer som behöver korrigeras i en annan Azure-klientrapportning till Uppdateringshantering måste du använda en följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) med parametern `ForUpdateConfiguration` angiven för att skapa ett schema. Du kan använda cmdleten [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) och skicka datorerna i den andra klienten till parametern. `NonAzureComputer` Följande exempel visar hur du gör detta.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du läsa följande källor för ytterligare frågor och svar.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Feedback-forum](https://feedback.azure.com/forums/905242-update-management)
