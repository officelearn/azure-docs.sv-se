---
title: Felsöka delade resurser i Azure Automation
description: Lär dig hur du felsöker och löser problem med delade Azure Automation-resurser.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733577"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Felsöka delade resurser i Azure Automation

I den här artikeln beskrivs lösningar på problem som du kan stöta på när du använder [delade resurser](../automation-intro.md#shared-resources) i Azure Automation.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Moduler

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: En modul har fastnat under importen

#### <a name="issue"></a>Problem

En modul har fastnat i importtillståndet när du importerar eller uppdaterar dina Azure Automation-moduler.

#### <a name="cause"></a>Orsak

Eftersom import av PowerShell-moduler är en komplex flerstegsprocess kanske en modul inte importeras korrekt och kan ha fastnat i ett tillfälligt tillstånd. Mer information om importprocessen finns i [Importera en PowerShell-modul](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du ta bort modulen som har fastnat i importtillståndet med cmdleten [Ta bort-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Du kan sedan försöka importera modulen igen.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler har fastnat under importen efter ett uppdateringsförsök

#### <a name="issue"></a>Problem

En banner med följande meddelande finns kvar i ditt konto efter att ha försökt uppdatera dina AzureRM-moduler:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto som finns i en resursgrupp med ett numeriskt namn som börjar med 0.

#### <a name="resolution"></a>Lösning

Om du vill uppdatera dina AzureRM-moduler i ditt Automation-konto måste kontot finnas i en resursgrupp med ett alfanumeriskt namn. Resursgrupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler just nu.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Modulen kan inte importeras eller cmdlets kan inte köras efter importen

#### <a name="issue"></a>Problem

En modul kan inte importeras eller importeras, men inga cmdlets extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker till att en modul kanske inte importeras till Azure Automation är:

* Strukturen matchar inte den struktur som Automation behöver.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar sina beroenden i mappen.
* [Cmdleten New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) används för att ladda upp modulen och du har inte angett den fullständiga lagringssökvägen eller inte har läst modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Använd någon av dessa lösningar för att åtgärda problemet.

* Kontrollera att modulen följer formatet: ModuleName.zip -> ModuleName eller Versionsnummer -> (ModuleName.psm1, ModuleName.psd1).
* Öppna **PSD1-filen** och se om modulen har några beroenden. Om den gör det laddar du upp dessa moduler till Automation-kontot.
* Kontrollera att alla refererade **DLL-filer** finns i modulmappen.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 avbryts vid uppdatering av moduler

#### <a name="issue"></a>Problem

När du använder [update-AzureModule.ps1-runbooken](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) för att uppdatera dina Azure-moduler pausas moduluppdateringsprocessen.

#### <a name="cause"></a>Orsak

Standardinställningen för att avgöra hur många moduler som uppdateras samtidigt är 10 när du använder **Update-AzureModule.ps1**. Uppdateringsprocessen är benägen att fel när för många moduler uppdateras samtidigt.

#### <a name="resolution"></a>Lösning

Det är inte vanligt att alla AzureRM- eller Az-moduler krävs i samma Automation-konto. Vi rekommenderar att du bara importerar de specifika moduler som du behöver.

> [!NOTE]
> Undvik att importera `Az.Automation` `AzureRM.Automation` hela eller modulen, som importerar alla moduler som finns.

Om uppdateringsprocessen avbryts lägger du till `SimultaneousModuleImportJobCount` parametern i **skriptet Update-AzureModules.ps1** och ger ett lägre värde än standardvärdet 10. Om du implementerar den här logiken rekommenderas att börja med värdet 3 eller 5. `SimultaneousModuleImportJobCount`är en parameter för systemkörningsboken **Update-AutomationAzureModulesForAccount** som används för att uppdatera Azure-moduler. Om du gör den här justeringen körs uppdateringsprocessen längre, men har större chans att slutföra. I följande exempel visas parametern och var den ska läggas i runbooken:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Kör som-konton

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: Du kan inte skapa eller uppdatera ett Run As-konto

#### <a name="issue"></a>Problem

NÃ¤r du fÃ¶rsÃ¶ks fÃ¶rsÃ¶k att skapa eller uppdatera ett Run As-konto visas ett felmeddelande som liknar fÃ¶1 1:00:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver för att skapa eller uppdatera kontot Kör som eller så är resursen låst på resursgruppsnivå.

#### <a name="resolution"></a>Lösning

Om du vill skapa eller uppdatera ett Run As-konto måste du ha rätt [behörighet](../manage-runas-account.md#permissions) för de olika resurser som används av kontot Kör som. 

Om problemet beror på ett lås kontrollerar du att låset kan tas bort. Navigera sedan till resursen som är låst i Azure-portalen, högerklicka på låset och klicka på **Ta bort**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: Felet "Det går inte att hitta en startpunkt med namnet GetPerAdapterInfo" i DLL 'iplpapi.dll' när du kör en runbook

#### <a name="issue"></a>Problem

När du kör en runbook får du följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet orsakas troligen av ett felaktigt konfigurerat [Kör som konto](../manage-runas-account.md).

#### <a name="resolution"></a>Lösning

Kontrollera att ditt Run As-konto är korrekt konfigurerat. Kontrollera sedan att du har rätt kod i din runbook för att autentisera med Azure. I följande exempel visas ett kodavsnitt som ska autentiseras till Azure i en runbook med hjälp av ett Run As-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
