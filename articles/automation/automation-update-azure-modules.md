---
title: Uppdatera Azure PowerShell moduler i Azure Automation
description: Den här artikeln beskriver hur du nu kan uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769673"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Uppdatera Azure PowerShell moduler i Azure Automation

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [Runbook-modulerna uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), som är tillgängligt som öppen källkod. Om du vill börja använda **Update-AutomationAzureModulesForAccount-** runbooken för att uppdatera Azure-moduler, kan du ladda ned den från [Uppdatera Azure modules Runbook-lagringsplats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) på GitHub. Du kan sedan importera den till ditt Automation-konto eller köra det som ett skript. Information om hur du importerar en Runbook i ditt Automation-konto finns i [Importera en Runbook](manage-runbooks.md#importing-a-runbook).

De vanligaste PowerShell-modulerna anges som standard i varje Automation-konto. Azure-teamet uppdaterar Azure-modulerna regelbundet. För att hålla modulerna i dina Automation-konton uppdaterade bör du därför använda [AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook.

Eftersom moduler uppdateras regelbundet av produkt gruppen kan ändringar ske med de inkluderade cmdletarna. De här ändringarna, till exempel att byta namn på en parameter eller att använda en helt aktuell cmdlet, kan påverka dina runbooks negativt.

Undvik att påverka dina runbooks och de processer som de automatiserar, testa och validera innan du fortsätter. Om du inte har ett dedikerat Automation-konto som är avsett för detta ändamål kan du överväga att skapa ett så att du kan testa många olika scenarier när du utvecklar dina runbooks. Det här testet bör omfatta upprepnings ändringar, till exempel uppdatera PowerShell-modulerna.

Om du utvecklar dina skript lokalt rekommenderar vi att du har samma versioner av modulen lokalt som du har i ditt Automation-konto när du testar för att se till att du får samma resultat. När resultaten har verifierats och du har tillämpat eventuella ändringar som krävs kan du flytta ändringarna till produktionen.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

> [!NOTE]
> Du kommer inte att kunna ta bort globala moduler, som är moduler som Automation tillhandahåller i rutan.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Överväganden

Följande är några saker som du bör tänka på när du använder den här artikeln för att uppdatera dina Azure-moduler:

* Den Runbook som beskrivs i den här artikeln stöder uppdatering av modulerna Azure, AzureRM och AZ som standard. Läs igenom [uppdateringen för Azure-modulerna i Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) om du vill ha mer information om hur du uppdaterar AZ. Automation-moduler med denna Runbook. Det finns ytterligare viktiga faktorer som du måste ta med i beräkningen när du använder AZ-modulerna i ditt Automation-konto. Mer information finns [i hantera moduler i Azure Automation](shared-resources/modules.md).

* Innan du startar denna Runbook måste du kontrol lera att ditt Automation-konto har en [Azure kör som-konto-autentiseringsuppgift](manage-runas-account.md) har skapats.

* Du kan använda den här koden som ett vanligt PowerShell-skript i stället för en Runbook: Logga bara in på Azure med cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) först `-Login $false` och skicka sedan till skriptet.

* Om du vill använda denna Runbook på de suveräna molnen använder du `AzEnvironment` parametern för att skicka rätt miljö till runbooken.  Godkända värden är AzureCloud (Azures offentliga moln), AzureChinaCloud, AzureGermanCloud och Azureusgovernment eller. Dessa värden kan hämtas med hjälp `Get-AzEnvironment | select Name`av. Om du inte skickar ett värde till den här cmdleten använder Runbook standardvärdet AzureCloud.

* Om du vill använda en speciell Azure PowerShell-modul i stället för den senaste modulen som finns på PowerShell-galleriet, måste du skicka dessa versioner till `ModuleVersionOverrides` den valfria parametern för **Update-AutomationAzureModulesForAccount-** runbooken. Exempel finns i [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) -runbooken. Azure PowerShell moduler som inte nämns i `ModuleVersionOverrides` parametern uppdateras med de senaste modulerna på PowerShell-galleriet. Om du inte skickar något till `ModuleVersionOverrides` parametern, uppdateras alla moduler med de senaste modulerna på PowerShell-galleriet. Detta är samma sak som knappen **Uppdatera Azure-moduler** .

## <a name="next-steps"></a>Nästa steg

Besök Runbook-modulen med öppen källkod [Uppdatera Azure-moduler](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) om du vill veta mer om det.
