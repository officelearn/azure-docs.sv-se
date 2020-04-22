---
title: Använda moduler i Azure Automation
description: I den här artikeln beskrivs hur du hanterar moduler i Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770946"
---
# <a name="manage-modules-in-azure-automation"></a>Använda moduler i Azure Automation

Med Azure Automation kan du importera PowerShell-moduler för att aktivera cmdlets i runbooks och DSC-resurser i DSC-konfigurationer. Moduler som används i Azure Automation inkluderar:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Intern `Orchestrator.AssetManagement.Cmdlets` modul för Log Analytics-agenten för Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Andra PowerShell-moduler
* Anpassade moduler som du skapar 

När du skapar ett Automation-konto importerar Azure Automation vissa moduler som standard. Se [Standardmoduler](#default-modules).

När Azure Automation kör runbook- och DSC-kompileringsjobb läser den in modulerna i sandlådor där runbooks kan köras och DSC-konfigurationerna kan kompileras. Automatisering placerar också automatiskt alla DSC-resurser i moduler på DSC-pull-servern. Datorer kan hämta resurserna när de tillämpar DSC-konfigurationerna.

>[!NOTE]
>Var noga med att importera endast de moduler som dina runbooks och DSC-konfigurationer faktiskt behöver. Importera inte den samlade modulen, till exempel Az.Automation, i alla fall.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas moduler som Azure Automation importerar som standard när du skapar ditt Automation-konto. Automation kan importera nyare versioner av dessa moduler. Du kan dock inte ta bort den ursprungliga versionen från ditt Automation-konto även om du tar bort en nyare version. Observera att dessa standardmoduler innehåller flera AzureRM-moduler. 

> [!NOTE]
> Vi rekommenderar inte att du ändrar moduler och runbooks i Automation-konton som innehåller några lösningar. 

|Modulnamn|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| DatorHanagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostik |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResurser | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |


Az.Automation-moduler är att föredra i dina runbooks- och DSC-konfigurationer. Azure Automation importerar dock inte den samlade Az-modulen automatiskt till nya eller befintliga Automation-konton. Mer information om hur du arbetar med dessa moduler finns i [Migrera till Az-moduler](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Invändiga cmdlets

I följande tabell definieras de interna cmdlets som stöds av modulen. `Orchestrator.AssetManagement.Cmdlets` Använd dessa i dina runbooks- och DSC-konfigurationer för att interagera med Automation-resurser i Automation-kontot. Cmdlets är utformade för att hämta hemligheter från krypterade variabler, autentiseringsuppgifter och krypterade anslutningar. 

> [!NOTE]
> Azure PowerShell-cmdlets kan inte hämta de tillgångshemligheter som de interna cmdlets kan hämta. 

|Namn|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Vänta-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Dessa interna cmdlets är tillgängliga på en Windows Hybrid Runbook Worker, men inte på en Linux Hybrid Runbook Worker. De körs från en Orchestrator sandlåda, som används av hybridarbetaren.  Deras användning kräver inte en implicit anslutning till Azure, som när du använder ett Run As-konto för autentisering.

I stället för att använda de interna cmdlets, använd Az eller AzureRM cmdlets för runbooks och konfigurationer som körs direkt på datorn eller mot resurser i din miljö. I dessa fall måste du implicit ansluta till Azure när du använder cmdlets, som när du använder ett Run As-konto för att autentisera till Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Moduler som stöder Get-AutomationPSCredential

För lokal utveckling med hjälp av Azure `Get-AutomationPSCredential` Automation Authoring Toolkit är cmdlet en del av sammansättningen [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). För Azure som arbetar med automationskontexten finns cmdlet i `Orchestrator.AssetManagement.Cmdlets`. Mer information om hur du använder autentiseringsuppgifter i Azure Automation [finns i Autentiseringsuppgifter i Azure Automation](credentials.md).

## <a name="migrating-to-az-modules"></a>Migrera till Az-moduler

Det finns flera saker att ta hänsyn till när du använder Az-modulerna i Azure Automation:

* Lösningar på högre nivå i ditt Automation-konto kan använda runbooks och moduler. Därför kan redigering av runbooks eller uppgraderingsmoduler potentiellt orsaka problem med dina lösningar. Du bör testa alla runbooks och lösningar noggrant i ett separat Automation-konto innan du importerar Az-modulerna. 

* Eventuella ändringar av moduler kan påverka [start/stop-start-/stopp-datorerna](../automation-solution-vm-management.md)negativt under lösningen av lediga timmar . 

* Om du importerar en Az-modul till ditt Automation-konto importeras inte modulen automatiskt i PowerShell-sessionen som runbooks använder. Moduler importeras till PowerShell-sessionen i följande situationer:

    * När en runbook anropar en cmdlet från en modul
    * När en runbook importerar `Import-Module` modulen uttryckligen med cmdlet
    * När en runbook importerar en annan beroende modul

När du har slutfört migreringen av dina moduler ska du inte försöka starta runbooks med AzureRM-moduler på Automation-kontot. Vi rekommenderar också att du inte importerar eller uppdaterar AzureRM-moduler på kontot. Tänk på kontot som migrerats till Az.Automation och fungerar endast med Az-moduler. 

>[!IMPORTANT]
>När du skapar ett nytt Automation-konto installerar Azure Automation AzureRM-modulerna som standard. Du kan fortfarande uppdatera självstudien runbooks med AzureRM cmdlets. Du bör dock inte köra dessa runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Stoppa och avmarkera alla runbooks som använder AzureRM-moduler

För att säkerställa att du inte kör några befintliga runbooks som använder AzureRM-moduler, stoppa och avmarkera alla berörda runbooks. Du kan se vilka scheman som finns och vilka scheman som ska tas bort genom att köra kod som liknar det här exemplet:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att säkerställa att du kan boka om det i framtiden för dina runbooks, om det behövs.

### <a name="import-the-az-modules"></a>Importera Az-modulerna

I det här avsnittet beskrivs hur du importerar Az-modulerna i Azure-portalen. Kom ihåg att importera endast Az-modulerna som du behöver, inte hela Az.Automation-modulen. Eftersom [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende för de andra Az-modulerna, se till att importera den här modulen före några andra.

1. Välj **Moduler** under **Delade resurser**i ditt Automation-konto . 
2. Klicka på **Bläddra i galleri** för att öppna sidan Bläddra i galleri.  
3. I sökfältet anger du modulnamnet, `Az.Accounts`till exempel . 
4. På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

    ![Importera moduler till Automation-konto](../media/modules/import-module.png)

Den här importprocessen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen som ska importeras. När du har hittat modulen väljer du den, väljer fliken **Azure Automation** och klickar på Distribuera till **Azure Automation**.

![Importera moduler direkt från galleriet](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testa dina runbooks

När du har importerat Az-modulerna till Automation-kontot kan du börja redigera runbooks för att använda de nya modulerna. Majoriteten av cmdlets har samma namn som för AzureRM-modulerna, förutom att AzureRM-prefixet (eller AzureRm) har ändrats till Az. En lista över moduler som inte följer den här namngivningskonventionen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa ändringen av en runbook för att `Enable-AzureRmAlias -Scope Process` använda de nya cmdlets är genom att använda i början av runbook. Genom att lägga till det här kommandot i runbooken kan skriptet köras utan ändringar.

## <a name="authoring-modules"></a>Redigeringsmoduler

Vi rekommenderar att du följer övervägandena i det här avsnittet när du skapar en PowerShell-modul för användning i Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Referenser till AzureRM och Az

Om du refererar till Az-modulerna i modulen, se till att du inte också refererar till AzureRM-modulerna. Du kan inte använda båda uppsättningarna av moduler samtidigt. 

### <a name="version-folder"></a>Version mapp

Ta INTE med en versionsmapp i **ZIP-paketet** för din modul.  Det här problemet är mindre ett problem för runbooks men orsakar ett problem med tjänsten State Configuration (DSC). Azure Automation skapar versionsmappen automatiskt när modulen distribueras till noder som hanteras av DSC. Om det finns en versionsmapp får du två instanser. Här är ett exempel mappstruktur för en DSC-modul:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Hjälpinformation

Inkludera en synopsis, beskrivning och hjälp URI för varje cmdlet i din modul. I PowerShell kan du definiera hjälpinformation för `Get-Help` cmdlets med hjälp av cmdlet. Följande exempel visar hur du definierar en synopsis och hjälper URI i en **PSM1-modulfil.**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Om du tillhandahåller den `Get-Help` här informationen visas hjälptext via cmdleten i PowerShell-konsolen. Den här texten visas också i Azure-portalen.

  ![Hjälp med integreringsmoduler](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Anslutningstyp

Om modulen ansluter till en extern tjänst definierar du en [anslutningstyp](#adding-a-connection-type-to-your-module). Varje cmdlet i modulen ska acceptera ett anslutningsobjekt (en instans av den anslutningstypen) som en parameter. Användare kartlägger parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. I följande runbook-exempel, baserat på exemplet i föregående avsnitt, `ContosoConnection` används en Contoso-anslutningstillgång som anropas för att komma åt Contoso-resurser och returnera data från den externa tjänsten. I det här exemplet mappas `UserName` `Password` fälten `PSCredential` till ett objekts och egenskaper och skickas sedan till cmdleten.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ett enklare och bättre sätt att närma sig detta beteende är genom att direkt skicka anslutningsobjektet till cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Du kan aktivera liknande beteende för dina cmdletar genom att låta dem acceptera ett anslutningsobjekt direkt som en parameter, i stället för bara anslutningsfält för parametrar. Vanligtvis vill du ha en parameteruppsättning för varje, så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att konstruera en hashtable för att fungera som anslutningsobjekt. Parameteruppsättningen `UserAccount` används för att skicka egenskaperna för anslutningsfält. `ConnectionObject`kan du skicka anslutningen rakt igenom.

### <a name="output-type"></a>Utdatatyp

Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan intelliSense utformas för att bestämma cmdlets utdataegenskaper under redigeringen. Den här metoden är särskilt användbar under grafisk runbook-redigering, för vilken designtidskunskap är nyckeln till en enkel användarupplevelse med din modul.

Lägg `[OutputType([<MyOutputType>])]` `MyOutputType` till var är en giltig typ. Mer information `OutputType`finns i [Om Funktioner OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett `OutputType` exempel på hur du lägger till i en cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Utdatatyp för grafiska runbooks](../media/modules/runbook-graphical-module-output-type.png)

  Det här beteendet liknar funktionen "typ framåt" för en cmdlets utdata i PowerShell ISE utan att behöva köra den.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet tillstånd

Gör alla cmdlets i modulen tillståndslösa. Flera runbook-jobb kan samtidigt `AppDomain` köras i samma och samma process och sandlåda. Om det finns någon delstat som delas på dessa nivåer kan jobb påverka varandra. Detta kan leda till återkommande och svåra att diagnostisera problem. Här är ett exempel på vad man inte ska göra:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Modulberoende

Se till att modulen är helt innesluten i ett paket som kan kopieras med hjälp av xcopy. Azure Automation-moduler distribueras till Automation sandlådor när runbooks körs. Modulerna måste fungera oberoende av den värd som kör dem. 

Du bör kunna zip upp och flytta ett modulpaket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att detta ska ske, se till att modulen inte beror på några filer utanför modulmappen som zippas upp när modulen importeras till Azure Automation. 

Modulen bör inte vara beroende av några unika registerinställningar på en värd. Exempel är de inställningar som görs när en produkt installeras. 

### <a name="module-file-paths"></a>Modulfilsökvägar

Kontrollera att alla filer i modulen har sökvägar med färre än 140 tecken. Alla sökvägar som är över 140 tecken långa orsakar problem med att importera runbooks. Azure Automation kan inte importera en fil med sökvägsstorlek över `Import-Module`140 tecken till PowerShell-sessionen med .

## <a name="importing-modules"></a>Importera moduler

Det här avsnittet definierar flera sätt som du kan importera en modul till ditt Automation-konto. 

### <a name="import-modules-in-azure-portal"></a>Importera moduler i Azure Portal

Så här importerar du en modul i Azure-portalen:

1. Navigera till ditt Automation-konto.
2. Välj **Moduler** under **Delade resurser**.
3. Klicka på **Lägg till en modul**. 
4. Välj **zip-filen** som innehåller modulen.
5. Klicka på **OK** för att starta för att importera processen.

### <a name="import-modules-using-powershell"></a>Importera moduler med PowerShell

Du kan använda cmdleten [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL för en modul .zip-paket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell Gallery direkt. Se till `ModuleName` att `ModuleVersion` ta tag i och från [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importera moduler från PowerShell-galleriet

Du kan importera [PowerShell Gallery-moduler](https://www.powershellgallery.com) antingen direkt från galleriet eller från ditt Automation-konto.

Så här importerar du en modul direkt från PowerShell-galleriet:

1. Gå https://www.powershellgallery.com till och sök efter modulen som ska importeras.
2. Klicka på **Distribuera till Azure Automation** på fliken Azure **Automation** under **Installationsalternativ**. Den här åtgärden öppnar Azure-portalen. 
3. På sidan Importera väljer du ditt Automation-konto och klickar på **OK**.

![Importmodul för PowerShell-galleri](../media/modules/powershell-gallery.png)

Så här importerar du en PowerShell Gallery-modul direkt från ditt Automation-konto:

1. Välj **Moduler** under **Delade resurser**. 
2. Klicka på Bläddra i **galleriet**på sidan Moduler och sök sedan i galleriet efter en modul. 
3. Markera den modul som ska importeras och klicka på **Importera**. 
4. Klicka på **OK** på sidan Importera för att starta importen.

![PowerShell Gallery-import från Azure-portalen](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort de ursprungliga versionerna av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som ska tas bort är en nyare version av en av [standardmodulerna](#default-modules)återställs den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från ditt Automation-konto bort.

### <a name="delete-modules-in-azure-portal"></a>Ta bort moduler i Azure Portal

Så här tar du bort en modul i Azure-portalen:

1. Navigera till ditt Automation-konto och välj **Moduler** under **Delade resurser**. 
2. Markera den modul som du vill ta bort. 
3. På sidan **Modul** väljer du **Ta bort**. Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="delete-modules-using-powershell"></a>Ta bort moduler med PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Lägga till en anslutningstyp i modulen

Du kan ange en anpassad [anslutningstyp](../automation-connections.md) som ska användas i ditt Automation-konto genom att lägga till en valfri metadatafil i modulen. Den här filen anger en Azure Automation-anslutningstyp som ska användas med modulens cmdlets i ditt Automation-konto. Mer information om hur du skapar en PowerShell-modul finns i [Så här skriver du en PowerShell-skriptmodul](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Använda en anpassad anslutning i Azure-portalen](../media/modules/connection-create-new.png)

Filen som anger egenskaper för anslutningstyp heter ** &lt;ModuleName&gt;-Automation.json** och finns i modulmappen i den komprimerade **ZIP-filen.** Den här filen innehåller fälten för en anslutning som krävs för att ansluta till det system eller den tjänst som modulen representerar. Konfigurationen gör det möjligt att skapa en anslutningstyp i Azure Automation. Med den här filen kan du ange fältnamn, typer och om fälten är krypterade eller valfria för modulens anslutningstyp. Följande exempel är en mall i **filformatet .json** som definierar egenskaper för användarnamn och lösenord:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder Azure PowerShell-moduler finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
