---
title: Uppdatera Azure-moduler i Azure Automation
description: I den här artikeln beskrivs hur du nu kan uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420476"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [runbooken Uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), som är tillgänglig som öppen källkod. Om du vill börja använda runbooken **Update-AutomationAzureModulesForAccount** för att uppdatera dina Azure-moduler hämtar du den från [runbook-databasen Update Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) på GitHub. Du kan sedan importera det till ditt Automation-konto eller köra det som ett skript. Mer information om hur du importerar en runbook i ditt Automation-konto finns i [Importera en runbook](manage-runbooks.md#import-a-runbook).

De vanligaste AzureRM PowerShell-modulerna tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar Azure-modulerna regelbundet, därför för att hålla dig uppdaterad vill du använda [update-automationAzureModulesForAccount-runbooken](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) för att uppdatera modulerna i dina Automation-konton.

Eftersom moduler uppdateras regelbundet av produktgruppen kan ändringar ske med de medföljande cmdlets. Den här åtgärden kan påverka runbooks negativt beroende på typen av ändring, till exempel byta namn på en parameter eller att helt flytta en cmdlet.

Testa och validera innan du fortsätter för att undvika att påverka dina runbooks och de processer som de automatiserar. Om du inte har ett dedikerat Automation-konto avsett för detta ändamål kan du överväga att skapa ett så att du kan testa många olika scenarier under utvecklingen av dina runbooks. Den här testningen bör omfatta iterativa ändringar, till exempel uppdatering av PowerShell-moduler.

Om du utvecklar skript lokalt rekommenderar vi att du har samma modulversioner lokalt som du har i ditt Automation-konto när du testar för att säkerställa att du får samma resultat. När resultaten har validerats och du har tillämpat de ändringar som krävs kan du flytta ändringarna till produktionen.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

> [!NOTE]
> Du kommer inte att kunna ta bort globala moduler - moduler som Automation tillhandahåller ur lådan.

## <a name="considerations"></a>Överväganden

Följande är några faktorer att ta hänsyn till när du använder den här processen för att uppdatera dina Azure-moduler:

* Den här runbooken stöder uppdatering av **Azure-** och **AzureRm-modulerna** som standard. Den här runbooken stöder uppdatering av **Az-modulerna** också. Läs [update Azure-modulernas runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) för mer information om hur du uppdaterar `Az` moduler med den här runbooken. Det finns ytterligare viktiga faktorer som du måste `Az` ta hänsyn till när du använder modulerna i ditt Automation-konto, för att lära dig mer, se [Använda Az-moduler i ditt Automation-konto](az-modules.md).

* Innan du startar den här runbooken kontrollerar du att ditt Automation-konto har en [Azure Run As-kontoautentiseringsautentisering skapad.](manage-runas-account.md)

* Du kan använda den här koden som ett vanligt PowerShell-skript i stället för en runbook: `-Login $false` logga bara in på Azure med kommandot [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) först och sedan gå vidare till skriptet.

* Om du vill använda den här `AzureRmEnvironment` runbooken på suveräna moln använder du parametern för att skicka rätt miljö till runbooken.  Godtagbara värden är **AzureCloud,** **AzureChinaCloud,** **AzureGermanCloud**och **AzureUSGovernment**. Dessa värden kan hämtas `Get-AzureRmEnvironment | select Name`från att använda . Om du inte skickar ett värde till den här parametern kommer runbooken som standard att Azure offentliga molnet **AzureCloud**

* Om du vill använda en specifik Azure PowerShell-modulversion i stället för den `ModuleVersionOverrides` senaste tillgängliga i PowerShell-galleriet skickar du dessa versioner till den valfria parametern för **update-automationAzureModulesForAccount-runbooken.** Exempel på exempel finns i runbooken [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Azure PowerShell-moduler som inte nämns `ModuleVersionOverrides` i parametern uppdateras med de senaste modulversionerna i PowerShell-galleriet. Om du inte `ModuleVersionOverrides` skickar något till parametern uppdateras alla moduler med de senaste modulversionerna i PowerShell-galleriet. Det här beteendet är samma som knappen **Uppdatera Azure-moduler.**

## <a name="next-steps"></a>Nästa steg

Besök open source [Update Azure-moduler runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) för att lära dig mer om det.
