---
title: Felsöka fel med delade Azure Automation-resurser
description: Lär dig hur du felsöker och löser problem med delade Azure Automation-resurser som stöder runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278329"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Felsöka fel med delade resurser

I den här artikeln beskrivs lösningar för att lösa problem som du kan stöta på när du använder delade resurser i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Scenario: En modul har fastnat importera

#### <a name="issue"></a>Problem

En modul har fastnat i importtillståndet när du **importerar** eller uppdaterar dina moduler i Azure automation.

#### <a name="cause"></a>Orsak

Att importera PowerShell-moduler är en komplex process i flera steg. Denna process introducerar möjligheten att en modul inte importeras korrekt. Om det här problemet uppstår kan modulen som du importerar ha fastnat i ett tillfälligt tillstånd. Mer information om den här processen finns i [Importera en PowerShell-modul](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du ta bort modulen som har fastnat i **importtillståndet** med hjälp av cmdleten [Remove-AzureRmAutomationModule.](/powershell/module/azurerm.automation/remove-azurermautomationmodule) Du kan sedan försöka importera modulen igen.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler har fastnat importera efter att ha försökt uppdatera dem

#### <a name="issue"></a>Problem

En banner med följande meddelande finns kvar i ditt konto efter att ha försökt uppdatera dina AzureRM-moduler:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto som finns i en resursgrupp med ett numeriskt namn som börjar med 0.

#### <a name="resolution"></a>Lösning

Om du vill uppdatera dina Azure-moduler i ditt Automation-konto måste den finnas i en resursgrupp som har ett alfanumeriskt namn. Resursgrupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler just nu.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Modulen kan inte importeras eller cmdlets kan inte köras efter importen

#### <a name="issue"></a>Problem

En modul kan inte importeras eller importeras, men inga cmdlets extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker till att en modul kanske inte importeras till Azure Automation är:

* Strukturen matchar inte den struktur som Automation behöver den för att vara i.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar sina beroenden i mappen.
* Cmdlet `New-AzureRmAutomationModule` används för att ladda upp modulen, och du har inte gett den fullständiga lagringssökvägen eller har inte läst modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

I någon av följande lösningar löser problemet:

* Kontrollera att modulen följer följande format: ModuleName.Zip **->** ModuleName eller Versionsnummer **->** (ModuleName.psm1, ModuleName.psd1)
* Öppna PSD1-filen och se om modulen har några beroenden. Om den gör det laddar du upp dessa moduler till Automation-kontot.
* Kontrollera att alla refererade DLL:er finns i modulmappen.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 avbryts vid uppdatering av moduler

#### <a name="issue"></a>Problem

När du använder [Update-AzureModule.ps1-runbooken](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) för att uppdatera dina Azure-moduler pausas moduluppdateringen som uppdateringsprocessen pausas.

#### <a name="cause"></a>Orsak

Standardinställningen för att avgöra hur många moduler som uppdateras `Update-AzureModule.ps1` samtidigt är 10 när skriptet ska användas. Uppdateringsprocessen är benägen att fel när för många moduler uppdateras samtidigt.

#### <a name="resolution"></a>Lösning

Det är inte vanligt att alla AzureRM-moduler krävs i samma Automation-konto. Vi rekommenderar att du bara importerar AzureRM-moduler som du behöver.

> [!NOTE]
> Undvik att importera **AzureRM-modulen.** Om du importerar **AzureRM-modulerna** rekommenderas inte alla **\* AzureRM-moduler** som ska importeras, detta rekommenderas inte.

Om uppdateringsprocessen avbryts måste du `SimultaneousModuleImportJobCount` lägga till `Update-AzureModules.ps1` parametern i skriptet och ange ett lägre värde än standardvärdet 10. Vi rekommenderar att du implementerar den här logiken, till att börja med värdet 3 eller 5. `SimultaneousModuleImportJobCount`är en parameter `Update-AutomationAzureModulesForAccount` i systemkörningsboken som används för att uppdatera Azure-moduler. Denna förändring gör processen löper längre, men har en bättre chans att slutföra. I följande exempel visas parametern och var den ska läggas i runbooken:

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

Om du vill skapa eller uppdatera ett Run As-konto måste du ha rätt behörighet för de olika resurser som används av kontot Kör som. Mer information om de behörigheter som krävs för att skapa eller uppdatera ett Run As-konto finns i [Kör som kontobehörigheter](../manage-runas-account.md#permissions).

Om problemet beror på ett lås kontrollerar du att låset är ok att ta bort det. Navigera sedan till den resurs som är låst, högerklicka på låset och välj **Ta bort** för att ta bort låset.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: Felet "Det går inte att hitta en startpunkt med namnet GetPerAdapterInfo" i DLL 'iplpapi.dll' när du kör en runbook.

#### <a name="issue"></a>Problem

När du kör en runbook får du följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet orsakas troligen av ett felaktigt konfigurerat [Kör som konto](../manage-runas-account.md).

#### <a name="resolution"></a>Lösning

Kontrollera att [ditt Run As-konto](../manage-runas-account.md) är korrekt konfigurerat. När den har konfigurerats korrekt, se till att du har rätt kod i din runbook för att autentisera med Azure. I följande exempel visas ett kodavsnitt som ska autentiseras till Azure i en runbook med hjälp av ett Run As-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
