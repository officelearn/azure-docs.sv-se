---
title: Hantera moduler i Azure Automation
description: I den här artikeln beskrivs hur du hanterar moduler i Azure Automation
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 492dd182c782b0f6375c2f857cfa4921b065c546
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231582"
---
# <a name="manage-modules-in-azure-automation"></a>Hantera moduler i Azure Automation

Azure Automation ger möjlighet att importera PowerShell-moduler till ditt Automation-konto som ska användas av PowerShell-baserade Runbooks. Dessa moduler kan vara anpassade moduler som du har skapat, från PowerShell-galleriet eller AzureRM-och AZ-modulerna för Azure. När du skapar ett Automation-konto importeras vissa moduler som standard.

## <a name="import-modules"></a>Importera moduler

Du kan importera en modul till ditt Automation-konto på flera sätt. I följande avsnitt visas hur du importerar en modul på olika sätt.

> [!NOTE]
> Den maximala sökvägen till en fil i en modul som ska användas i Azure Automation är 140 tecken. Alla sökvägar över 140 tecken kommer inte att kunna importeras till PowerShell-sessionen med `Import-Module`.

### <a name="powershell"></a>PowerShell

Du kan använda [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL till ett zip-paket för en modul.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure Portal

I Azure Portal navigerar du till ditt Automation-konto och väljer **moduler** under **delade resurser**. Klicka på **+ Lägg till en modul**. Välj en **. zip** -fil som innehåller modulen och klicka på **OK** för att börja importera processen.

### <a name="powershell-gallery"></a>PowerShell-galleriet

Moduler från PowerShell-galleriet kan antingen importeras från [PowerShell-galleriet](https://www.powershellgallery.com) direkt eller från ditt Automation-konto.

Om du vill importera en modul från PowerShell-galleriet går du till https://www.powershellgallery.com och söker efter den modul som du vill importera. Klicka på **distribuera för att Azure Automation** på fliken **Azure Automation** under **installations alternativ**. Den här åtgärden öppnar Azure Portal. På sidan **Importera** väljer du ditt Automation-konto och klickar på **OK**.

![PowerShell-galleriet importera modul](../media/modules/powershell-gallery.png)

Du kan också importera moduler från PowerShell-galleriet direkt från ditt Automation-konto. I ditt Automation-konto väljer du **moduler** under **delade resurser**. Klicka på **Bläddra i galleriet**på sidan moduler och Sök sedan i PowerShell-galleriet efter en modul. Välj den modul som du vill importera och klicka på **Importera**. På sidan **Importera** klickar du på **OK** för att starta import processen.

![PowerShell-galleriet importera från Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort den ursprungliga versionen av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om den modul som du vill ta bort är en nyare version av en av [standardmodulerna](#default-modules) som är installerad, kommer den att återställas till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från ditt Automation-konto bort.

### <a name="azure-portal"></a>Azure Portal

I Azure Portal navigerar du till ditt Automation-konto och väljer **moduler** under **delade resurser**. Välj den modul som du vill ta bort. **Ta bort**clcick på sidan **modul** . Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="powershell"></a>PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Interna cmdletar

Följande är en lista över cmdletar i den interna `Orchestrator.AssetManagement.Cmdlets`-modulen som importeras till varje Automation-konto. Dessa cmdletar är tillgängliga i dina runbooks och DSC-konfigurationer och gör att du kan interagera med dina till gångar i ditt Automation-konto. Dessutom kan du med interna cmdlet: ar Hämta hemligheter från krypterade **variabel** värden, **autentiseringsuppgifter**och krypterade **anslutnings** fält. Det går inte att hämta dessa hemligheter i Azure PowerShell-cmdletar. Dessa cmdletar kräver inte att du implicit ansluter till Azure när du använder dem, t. ex. genom att använda ett Kör som-konto för att autentisera till Azure.

|Namn|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-automationvariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Lägg till en Anslutnings typ i modulen

Du kan ange en anpassad [Anslutnings typ](../automation-connections.md) som du kan använda i ditt Automation-konto genom att lägga till en valfri fil i modulen. Den här filen är en metadatafil som anger en Azure Automation Anslutnings typ som ska användas med modulens cmdlets i ditt Automation-konto. För att uppnå detta måste du först veta hur du skapar en PowerShell-modul. Mer information om hur du redigerar modulen finns i [så här skriver du en PowerShell-modul för skript](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Använd en anpassad anslutning i Azure Portal](../media/modules/connection-create-new.png)

Om du vill lägga till en Azure Automation Anslutnings typ måste modulen innehålla en fil med namnet `<ModuleName>-Automation.json` som anger egenskaperna för anslutnings typen. JSON-filen placeras i mappen module i den komprimerade ZIP-filen. Den här filen innehåller fälten i en anslutning som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Konfigurationen Slutför skapandet av en Anslutnings typ i Azure Automation. Med den här filen kan du ange fält namn, typer och om fälten ska vara krypterade eller valfria, för modulens Anslutnings typ. Följande exempel är en mall i JSON-filformatet som definierar en egenskap för användar namn och lösen ord:

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

## <a name="module-best-practices"></a>Metod tips för modul

PowerShell-moduler kan importeras till Azure Automation för att göra att deras cmdlets är tillgängliga för användning i Runbooks och deras DSC-resurser som är tillgängliga för användning i DSC-konfigurationer. Azure Automation lagrar de här modulerna i bakgrunden, och i Runbook-jobbet och körnings tiden för DSC-kompilering läses de in i Azure Automation sand lådor där Runbooks kör och DSC-konfigurationer kompileras. DSC-resurser i moduler placeras också automatiskt på Automation DSC pull-servern. De kan hämtas av datorer när de använder DSC-konfigurationer.

Vi rekommenderar att du funderar på följande när du skapar en PowerShell-modul för användning i Azure Automation:

* Ta inte med en versions-mapp i. zip-paketet.  Det här problemet är mindre viktigt för Runbooks, men orsakar ett problem med tillstånds konfigurations tjänsten.  Azure Automation skapar automatiskt mappen version när modulen distribueras till noder som hanteras av DSC, och om en versions katalog finns kommer du att få två instanser.  Exempel på mappstruktur för en DSC-modul:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera viss hjälpinformation för cmdlets så att användaren kan få hjälp med att använda dem genom att köra cmdleten **Get-Help**. I följande exempel visas hur du definierar en sammanfattning och hjälp-URI för i en. psm1-modul fil:

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

  Genom att ange den här informationen visas den här hjälpen med cmdleten **Get-Help** i PowerShell-konsolen. Den här beskrivningen visas också i Azure Portal.

  ![Hjälp med integreringsmoduler](../media/modules/module-activity-description.png)

* Om modulen ansluter till en extern tjänst bör den innehålla en [Anslutnings typ](#add-a-connection-type-to-your-module). Varje cmdlet i modulen ska kunna använda ett anslutningsobjekt (en instans av anslutningstypen) som en parameter. Användarna mappar parametrarna för anslutnings till gången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Baserat på ovanstående Runbook-exempel används ett exempel på Contoso-ContosoConnection som heter för att komma åt contoso-resurser och returnera data från den externa tjänsten.

  I följande exempel mappas fälten till egenskaperna för användar namn och lösen ord för ett `PSCredential`-objekt och skickas sedan till cmdlet: en.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ett enklare och bättre sätt att närma sig detta beteende är att skicka anslutningsobjektet direkt till cmdleten:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Du kan aktivera beteende som i föregående exempel för dina cmdlets genom att tillåta att de accepterar ett anslutnings objekt direkt som en parameter, i stället för bara anslutnings fält för parametrar. Vanligt vis vill du ha en parameter uppsättning för var och en, så att en användare som inte använder Azure Automation kan anropa dina cmdletar utan att skapa en hash-post för att fungera som anslutnings objekt. Parametern set `UserAccount`används för att skicka egenskaperna för anslutnings fältet. med `ConnectionObject` kan du skicka anslutningen direkt genom.

* Definiera utdatatypen för alla cmdletar i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense hjälpa dig under designfasen med att fastställa utdataegenskaperna för cmdleten, för användning under redigeringar. Det är särskilt användbart vid grafisk redigering av Automation-Runbook, där kunskap om design tid är en nyckel till en enkel användar upplevelse med modulen.

Lägg till `[OutputType([<MyOutputType>])]` där MyOutputType är en giltig typ. Mer information om OutputType finns i [om functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett exempel på hur du lägger till `OutputType` i en-cmdlet:

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

  Det här beteendet liknar funktionen "Type Ahead" för cmdletens utdata i PowerShell ISE utan att behöva köra den.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Gör alla cmdlets i modulen tillståndslösa. Flera Runbook-jobb kan samtidigt köras i samma AppDomain och samma process och sandbox. Om det finns ett tillstånd som delas på dessa nivåer kan jobben påverka varandra. Det här beteendet kan leda till intermittent och svårt att diagnostisera problem.  Här är ett exempel på vad du inte ska göra.

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

* Modulen bör helt ingå i ett xcopy-möjligt paket. Azure Automation moduler distribueras till Automation-sandbox när Runbooks måste köras. Modulerna måste fungera oberoende av värden som de körs på. Du bör kunna lägga upp och flytta ett modul-paket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att detta ska ske bör modulen vara beroende av filer utanför modulen modul. Den här mappen är den mapp som hämtas när modulen importeras till Azure Automation. Modulen bör inte heller vara beroende av unika register inställningar på en värd, till exempel de inställningar som ställts in när en produkt installeras. Alla filer i modulen måste ha en sökväg som är kortare än 140 tecken. Alla sökvägar över 140 tecken orsakar problem med att importera din Runbook. Om du inte följer den här rekommenderade metoden går det inte att använda modulen i Azure Automation.  

* Om du refererar till [Azure PowerShell AZ-moduler](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i modulen kontrollerar du att du inte också refererar till `AzureRM`. `Az`-modulen kan inte användas tillsammans med `AzureRM`-modulerna. `Az` stöds i Runbooks men importeras inte som standard. Mer information om `Az` moduler och överväganden för att ta hänsyn till finns [i stöd för AZ-modulen i Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas de moduler som importeras som standard när ett Automation-konto skapas. De moduler som anges nedan kan ha nyare versioner av dem importerade, men den ursprungliga versionen kan inte tas bort från ditt Automation-konto även om du tar bort en nyare version av dem.

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
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](/powershell/scripting/developer/windows-powershell)
