---
title: Felsöka fel med Azure Automation delade resurser
description: Lär dig hur du felsöker och löser problem med Azure Automation delade resurser som stöder Runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278329"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Felsöka fel med delade resurser

I den här artikeln beskrivs lösningar för att lösa problem som du kan köra i när du använder delade resurser i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="module-stuck-importing"></a>Scenario: en modul har fastnat i importen

#### <a name="issue"></a>Problem

En modul fastnar i **import** tillstånd när du importerar eller uppdaterar dina moduler i Azure Automation.

#### <a name="cause"></a>Orsak

Importera PowerShell-moduler är en komplex process med flera steg. Den här processen beskriver möjligheten för en modul som inte importeras korrekt. Om det här problemet uppstår kan modulen som du importerar fastna i ett tillfälligt tillstånd. Mer information om den här processen finns i [Importera en PowerShell-modul](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa det här problemet måste du ta bort modulen som fastnar i **import** tillstånd med hjälp av cmdleten [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) . Sedan kan du försöka importera modulen igen.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler har fastnat i importen när du försöker uppdatera dem

#### <a name="issue"></a>Problem

En banderoll med följande meddelande är kvar i ditt konto när du försöker uppdatera AzureRM-modulerna:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto som finns i en resurs grupp med ett numeriskt namn som börjar med 0.

#### <a name="resolution"></a>Lösning

Om du vill uppdatera dina Azure-moduler i ditt Automation-konto måste det finnas i en resurs grupp som har ett alfanumeriskt namn. Resurs grupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler för tillfället.

### <a name="module-fails-to-import"></a>Scenario: modulen kan inte importeras eller så går det inte att köra cmdletar efter importen

#### <a name="issue"></a>Problem

En modul kan inte importeras eller importeras, men inga cmdlet: ar extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker till att en modul inte kan importeras till Azure Automation är:

* Strukturen stämmer inte överens med den struktur som behövs för Automation.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Dess beroenden saknas i mappen.
* `New-AzureRmAutomationModule` cmdlet används för att ladda upp modulen och du har inte gett den fullständiga lagrings Sök vägen eller inte har läst in modulen genom att använda en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar löser problemet:

* Se till att modulen följer följande format: modulnamn. zip **->** Modulnamn eller versions nummer **->** (modulnamn. psm1, Modulnamn. psd1)
* Öppna. psd1-filen och se om modulen har några beroenden. Om det gör det laddar du upp dessa moduler till Automation-kontot.
* Se till att alla refererade DLL-filer finns i mappen modul.

### <a name="all-modules-suspended"></a>Scenario: Update-AzureModule. ps1 pausas vid uppdatering av moduler

#### <a name="issue"></a>Problem

När du använder [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) -runbooken för att uppdatera dina Azure-moduler, uppdaterar modulen uppdaterings processen.

#### <a name="cause"></a>Orsak

Standardinställningen för att avgöra hur många moduler som uppdateras samtidigt är 10 när du använder `Update-AzureModule.ps1`-skriptet. Uppdaterings processen är känslig för fel när för många moduler uppdateras samtidigt.

#### <a name="resolution"></a>Lösning

Det är inte vanligt att alla AzureRM-moduler krävs i samma Automation-konto. Vi rekommenderar att du bara importerar de AzureRM-moduler som du behöver.

> [!NOTE]
> Undvik att importera **AzureRM** -modulen. Om du importerar **AzureRM** -modulerna kommer alla **AzureRM.\*** -moduler att importeras, detta är inte rekommenderade.

Om uppdaterings processen pausas måste du lägga till parametern `SimultaneousModuleImportJobCount` i `Update-AzureModules.ps1`-skriptet och ange ett lägre värde än standardvärdet 10. Vi rekommenderar att du implementerar den här logiken för att börja med värdet 3 eller 5. `SimultaneousModuleImportJobCount` är en parameter i `Update-AutomationAzureModulesForAccount` system Runbook som används för att uppdatera Azure-moduler. Den här ändringen gör att processen körs längre, men har en bättre chans att slutföra. I följande exempel visas parametern och var den ska läggas till i runbooken:

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

### <a name="unable-create-update"></a>Scenario: det går inte att skapa eller uppdatera ett Kör som-konto

#### <a name="issue"></a>Problem

När du försöker skapa eller uppdatera ett Kör som-konto visas ett fel meddelande som liknar följande fel meddelande:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver för att skapa eller uppdatera kör som-kontot eller så är resursen låst på en resurs grupps nivå.

#### <a name="resolution"></a>Lösning

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha nödvändig behörighet för de olika resurser som används av kör som-kontot. Mer information om de behörigheter som krävs för att skapa eller uppdatera ett Kör som-konto finns i [behörigheter för kör som-konto](../manage-runas-account.md#permissions).

Om problemet beror på ett lås kontrollerar du att låset är OK för att ta bort det. Navigera sedan till resursen som är låst, högerklicka på låset och välj **ta bort** för att ta bort låset.

### <a name="iphelper"></a>Scenario: fel meddelandet "Det gick inte att hitta en start punkt med namnet" GetPerAdapterInfo "i DLL-filen" iplpapi. dll "" visas när en runbook körs.

#### <a name="issue"></a>Problem

När du kör en Runbook får du följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet beror sannolikt på ett felaktigt konfigurerat [Kör som-konto](../manage-runas-account.md).

#### <a name="resolution"></a>Lösning

Kontrol lera att [Kör som-kontot](../manage-runas-account.md) har kon figurer ATS korrekt. När den har kon figurer ATS korrekt ser du till att du har rätt kod i din Runbook för att autentisera med Azure. I följande exempel visas ett kod avsnitt för att autentisera till Azure i en Runbook med hjälp av ett Kör som-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
