---
title: Uppdatera Azure-moduler i Azure Automation
description: I den här artikeln beskrivs hur du nu kan uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617482"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [runbooken Uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), som är tillgänglig som öppen källkod. Om du vill börja använda runbooken **Update-AutomationAzureModulesForAccount** för att uppdatera dina Azure-moduler hämtar du den från [runbook-databasen Update Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) på GitHub. Du kan sedan importera det till ditt Automation-konto eller köra det som ett skript. Mer information om hur du importerar en runbook i ditt Automation-konto finns i [Importera en runbook](manage-runbooks.md#importing-a-runbook).

De vanligaste PowerShell-modulerna tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar Azure-modulerna regelbundet. Om du vill hålla modulerna i dina Automation-konton uppdaterade bör du därför använda runbooken [Update-AutomationAzureModulesForAccount.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)

Eftersom moduler uppdateras regelbundet av produktgruppen kan ändringar ske med de medföljande cmdlets. Dessa ändringar, till exempel byta namn på en parameter eller inaktuella en cmdlet helt, kan påverka dina runbooks negativt.

Testa och validera innan du fortsätter för att undvika att påverka dina runbooks och de processer som de automatiserar. Om du inte har ett dedikerat Automation-konto avsett för detta ändamål kan du överväga att skapa ett så att du kan testa många olika scenarier under utvecklingen av dina runbooks. Den här testningen bör omfatta iterativa ändringar, till exempel uppdatera PowerShell-modulerna.

Om du utvecklar skript lokalt rekommenderar vi att du har samma modulversioner lokalt som du har i ditt Automation-konto när du testar för att säkerställa att du får samma resultat. När resultaten har validerats och du har tillämpat de ändringar som krävs kan du flytta ändringarna till produktionen.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

> [!NOTE]
> Du kommer inte att kunna ta bort globala moduler, som är moduler som Automation tillhandahåller ur lådan.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Överväganden

Följande är några faktorer att ta hänsyn till när du använder den här artikeln för att uppdatera dina Azure-moduler:

* Runbooken som beskrivs i den här artikeln stöder uppdatering av Azure-, AzureRM- och Az-modulerna som standard. Läs [update Azure-modulernas runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) för mer information om hur du uppdaterar Az.Automation-moduler med den här runbooken. Det finns ytterligare viktiga faktorer som du måste ta hänsyn till när du använder Az-modulerna i ditt Automation-konto. Mer information finns [i Använda Az-moduler i ditt Automation-konto](az-modules.md).

* Innan du startar den här runbooken kontrollerar du att ditt Automation-konto har en [Azure Run As-kontoautentiseringsautentisering skapad.](manage-runas-account.md)

* Du kan använda den här koden som ett vanligt PowerShell-skript i stället för en runbook: logga `-Login $false` bara in på Azure med cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) först och sedan gå vidare till skriptet.

* Om du vill använda den här `AzEnvironment` runbooken på suveräna moln använder du parametern för att skicka rätt miljö till runbooken.  Godtagbara värden är AzureCloud (Offentligt Azure-moln), AzureChinaCloud, AzureGermanCloud och AzureUSGovernment. Dessa värden kan hämtas med `Get-AzEnvironment | select Name`. Om du inte skickar ett värde till den här cmdleten visas standardkörningsboken till AzureCloud.

* Om du vill använda en specifik Azure PowerShell-modulversion i stället för den senaste modulen som finns i PowerShell-galleriet skickar du dessa versioner till den valfria `ModuleVersionOverrides` parametern för runbooken **Update-AutomationAzureModulesForAccount.** Exempel på exempel finns i runbooken [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Azure PowerShell-moduler som inte nämns `ModuleVersionOverrides` i parametern uppdateras med de senaste modulversionerna i PowerShell-galleriet. Om du inte `ModuleVersionOverrides` skickar något till parametern uppdateras alla moduler med de senaste modulversionerna i PowerShell-galleriet. Det här beteendet är samma som knappen **Uppdatera Azure-moduler.**

## <a name="next-steps"></a>Nästa steg

Besök open source [Update Azure-moduler runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) för att lära dig mer om det.
