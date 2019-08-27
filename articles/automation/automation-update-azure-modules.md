---
title: Uppdatera Azure-moduler i Azure Automation
description: Den här artikeln beskriver hur du nu kan uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a71eb00ce4d4ace6ccc17e050946b39debed929c
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034888"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell moduler i Azure Automation

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [Runbook-modulerna uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), som är tillgängligt som öppen källkod. Om du vill börja använda **Update-AutomationAzureModulesForAccount-** runbooken för att uppdatera Azure-moduler, kan du ladda ned den från [Uppdatera Azure modules Runbook-lagringsplats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) på GitHub. Du kan sedan importera den till ditt Automation-konto eller köra det som ett skript. Information om hur du importerar en Runbook i ditt Automation-konto finns i [Importera en Runbook](manage-runbooks.md#import-a-runbook).

De vanligaste AzureRM PowerShell-modulerna anges som standard i varje Automation-konto. Azure-teamet uppdaterar Azure-modulerna regelbundet, och därför bör du fortsätta att använda AutomationAzureModulesForAccount Runbook för att uppdatera modulerna i dina Automation [-](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) konton.

Eftersom moduler uppdateras regelbundet av produkt gruppen kan ändringar ske med de inkluderade cmdletarna. Den här åtgärden kan påverka dina runbooks negativt beroende på typ av ändring, till exempel att byta namn på en parameter eller att den är helt och hållet en cmdlet.

Undvik att påverka dina runbooks och de processer som de automatiserar, testa och validera innan du fortsätter. Om du inte har ett dedikerat Automation-konto som är avsett för detta ändamål kan du överväga att skapa ett så att du kan testa många olika scenarier när du utvecklar dina runbooks. Den här testningen bör omfatta iterativa ändringar, till exempel uppdatera PowerShell-modulerna.

Om du utvecklar dina skript lokalt rekommenderar vi att du har samma versioner av modulen lokalt som du har i ditt Automation-konto när du testar för att se till att du får samma resultat. När resultaten har verifierats och du har tillämpat eventuella ändringar som krävs kan du flytta ändringarna till produktionen.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

> [!NOTE]
> Du kommer inte att kunna ta bort globala moduler – moduler som Automation tillhandahåller direkt.

## <a name="considerations"></a>Överväganden

Följande är några saker som du bör tänka på när du använder den här processen för att uppdatera dina Azure-moduler:

* Denna Runbook stöder uppdatering av **Azure** -och **AzureRm** -moduler som standard. Denna Runbook stöder även uppdatering av **AZ** -moduler. Mer information om hur du uppdaterar `Az` moduler med denna Runbook finns i avsnittet [Uppdatera Azure-moduler för Runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) . Det finns ytterligare viktiga faktorer som du måste ta hänsyn till när du använder `Az` modulerna i ditt Automation-konto. mer information finns i [använda AZ-moduler i ditt Automation-konto](az-modules.md).

* Innan du startar denna Runbook måste du kontrol lera att ditt Automation-konto har en [Azure kör som-konto-autentiseringsuppgift](manage-runas-account.md) har skapats.

* Du kan använda den här koden som ett vanligt PowerShell-skript i stället för en Runbook: Logga bara in på Azure med kommandot [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) först och `-Login $false` skicka sedan till skriptet.

* Om du vill använda denna Runbook på de suveräna molnen använder `AzureRmEnvironment` du parametern för att skicka rätt miljö till runbooken.  Godkända värden är **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**och **azureusgovernment eller**. Dessa värden kan hämtas från med hjälp `Get-AzureRmEnvironment | select Name`av. Om du inte skickar ett värde till den här parametern kommer runbooken att standardvärdet för Azures offentliga moln **AzureCloud**

* Om du vill använda en speciell Azure PowerShell-modul i stället för den senaste tillgängliga på PowerShell-galleriet, måste du skicka dessa versioner till den `ModuleVersionOverrides` valfria parametern för **Update-AutomationAzureModulesForAccount-** runbooken. Exempel finns i [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) -runbooken. Azure PowerShell moduler som inte nämns i `ModuleVersionOverrides` parametern uppdateras med de senaste modulerna på PowerShell-galleriet. Om du inte skickar något till `ModuleVersionOverrides` parametern, uppdateras alla moduler med de senaste modulerna på PowerShell-galleriet. Detta är samma sak som knappen **Uppdatera Azure-moduler** .

## <a name="known-issues"></a>Kända problem

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto som finns i en resurs grupp med ett numeriskt namn som börjar med 0. Om du vill uppdatera dina Azure-moduler i ditt Automation-konto måste det finnas i en resurs grupp som har ett alfanumeriskt namn. Resurs grupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler för tillfället.

## <a name="next-steps"></a>Nästa steg

Besök Runbook-modulen med öppen källkod [Uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) om du vill veta mer om det.
