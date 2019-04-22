---
title: Felsöka fel med Azure Automation delade resurser
description: Lär dig hur du felsöker problem med Azure Automation delade resurser
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 66165a196c8b934df948f1d88b09a5859d3e792f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804504"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Felsöka fel med delade resurser

Den här artikeln beskriver lösningar för att lösa problem som du kan köra över när du använder de delade resurserna i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="module-stuck-importing"></a>Scenario: En modul har fastnat importera

#### <a name="issue"></a>Problem

En modul har fastnat i den **importera** tillstånd när du importerar eller uppdatera dina moduler i Azure automation.

#### <a name="cause"></a>Orsak

Importera PowerShell-moduler är en komplicerad process i flera steg. Den här processen introducerar möjligheten att en modul importeras inte korrekt. Om det här problemet inträffar kan modulen som du importerar ha fastnat i ett tillfälligt tillstånd. Mer information om den här processen finns [importera en PowerShell-modul]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Upplösning

För att lösa problemet måste du ta bort den modul som har fastnat i den **importera** tillstånd med hjälp av den [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet. Du kan sedan göra om modulen importerades.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler har fastnat importera när du försöker uppdatera dem.

#### <a name="issue"></a>Problem

En banderoll med följande meddelande kvar i ditt konto när du försöker uppdatera din AzureRM-moduler:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med uppdatering AzureRM-moduler i ett Automation-konto som tillhör en resursgrupp med det numeriska namn som börjar med 0.

#### <a name="resolution"></a>Upplösning

Om du vill uppdatera din Azure-moduler i ditt Automation-konto, måste den vara i en resursgrupp som har ett alfanumeriskt namn. Resursgrupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler just nu.

### <a name="module-fails-to-import"></a>Scenario: Modulen kan inte importera eller cmdlet: ar kan inte utföras när du har importerat

#### <a name="issue"></a>Problem

En modul kan inte importera eller importeras ordentligt, men inga cmdletar extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker som en modul inte kan importera till Azure Automation är:

* Strukturen matchar inte strukturen som Automation måste det finnas i.
* Modulen beror på en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar dess beroenden i mappen.
* Den `New-AzureRmAutomationModule` cmdlet som används för att ladda upp modulen, och du har inte beviljat fullständig lagringssökväg eller har inte lästs in modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Upplösning

Någon av följande lösningar problemet på:

* Kontrollera att modulen följer följande format: ModuleName.Zip **->** ModuleName eller versionsnummer **->** (ModuleName.psm1, ModuleName.psd1)
* Öppna filen .psd1 och om modulen har några beroenden. I annat fall kan du ladda upp dessa moduler till Automation-kontot.
* Se till att alla refererade DLL-filer finns i modulmappen.

### <a name="all-modules-suspended"></a>Scenario: Uppdatera AzureModule.ps1 pausar när du uppdaterar moduler

#### <a name="issue"></a>Problem

När du använder den [uppdatering AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook för att uppdatera din Azure-moduler modulen uppdateringen uppdateringsprocessen hämtar har pausats.

#### <a name="cause"></a>Orsak

Standardinställningen för att avgöra hur många moduler uppdateras samtidigt är 10 när du använder den `Update-AzureModule.ps1` skript. Uppdateringen är känslig för fel när för många-modulerna uppdateras samtidigt.

#### <a name="resolution"></a>Upplösning

Det är inte vanligt att alla AzureRM-moduler krävs i samma Automation-kontot. Vi rekommenderar att du bara importera de AzureRM-moduler som du behöver.

> [!NOTE]
> Undvika att importera den **AzureRM** modulen. Importera den **AzureRM** moduler kommer alla **AzureRM.\***  moduler som ska importeras, detta är inte recommened.

Om uppdateringen pausar, måste du lägga till den `SimultaneousModuleImportJobCount` parametern till den `Update-AzureModules.ps1` skript och ange ett lägre värde än standardvärdet är 10. Du rekommenderas om du implementerar den här logiken för att starta med värdet 3 eller 5. `SimultaneousModuleImportJobCount` är en parameter i `Update-AutomationAzureModulesForAccount` system-runbook som används för att uppdatera Azure-moduler. Den här ändringen gör processen kör längre, men har större möjlighet att slutföra. I följande exempel visar parametern och var du vill placera den i runbook:

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

### <a name="unable-create-update"></a>Scenario: Du kan inte skapa eller uppdatera en Kör som-konto

#### <a name="issue"></a>Problem

När du försöker skapa eller uppdatera en Kör som-konto, visas ett fel som liknar följande felmeddelande visas:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver skapa eller uppdatera kör som-kontot eller resursen är låst på en resursgruppsnivå.

#### <a name="resolution"></a>Upplösning

Du måste ha behörighet till de olika resurserna som används av kör som-kontot för att skapa eller uppdatera en Kör som-konto. Läs om de behörigheter som krävs för att skapa eller uppdatera en Kör som-konto i [kör som-kontobehörighet](../manage-runas-account.md#permissions).

Om problemet är på grund av ett lås, kontrollerar du att låset är ok om du vill ta bort den. Gå sedan till den resurs som är låst och högerklicka på låset väljer **ta bort** att ta bort låset.

### <a name="iphelper"></a>Scenario: Du får felet ”Det går inte att hitta en startpunkt med namnet” GetPerAdapterInfo ”i DLL-filen 'iplpapi.dll'” när kör en runbook.

#### <a name="issue"></a>Problem

När du kör en runbook visas följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet beror sannolikt på grund av en felaktigt konfigurerad [kör som-konto](../manage-runas-account.md).

#### <a name="resolution"></a>Upplösning

Kontrollera att din [kör som-konto](../manage-runas-account.md) är korrekt konfigurerad. När den har konfigurerats korrekt kan du kontrollera att du har rätt kod i din runbook för att autentisera med Azure. I följande exempel visas ett kodfragment till att autentisera till Azure i en runbook med en Kör som-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
