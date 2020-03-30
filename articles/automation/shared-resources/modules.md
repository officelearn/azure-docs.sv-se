---
title: Hantera moduler i Azure Automation
description: I den här artikeln beskrivs hur du hanterar moduler i Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278342"
---
# <a name="manage-modules-in-azure-automation"></a>Hantera moduler i Azure Automation

Azure Automation ger möjlighet att importera PowerShell-moduler till ditt Automation-konto som ska användas av PowerShell-baserade runbooks. Dessa moduler kan vara anpassade moduler som du har skapat, från PowerShell-galleriet eller AzureRM- och Az-modulerna för Azure. När du skapar ett automationskonto importeras vissa moduler som standard.

## <a name="import-modules"></a>Importera moduler

Det finns flera sätt att importera en modul till ditt Automation-konto. I följande avsnitt visas olika sätt att importera en modul.

> [!NOTE]
> Den maximala sökvägen till en fil i en modul som ska användas i Azure Automation är 140 tecken. En sökväg över 140 tecken kan inte importeras `Import-Module`till PowerShell-sessionen med .

### <a name="powershell"></a>PowerShell

Du kan använda [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) för att importera en modul till ditt Automation-konto. Cmdlet tar en url till en modul zip-paket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell Gallery direkt. Se till att ta **ModuleName** och **ModuleVersion** från [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

I Azure-portalen navigerar du till ditt Automation-konto och väljer **Moduler** under **Delade resurser**. Klicka på **+ Lägg till en modul**. Välj en **ZIP-fil** som innehåller modulen och klicka på **Ok** för att starta importen.

### <a name="powershell-gallery"></a>PowerShell-galleriet

Moduler från PowerShell-galleriet kan antingen importeras direkt från [PowerShell-galleriet](https://www.powershellgallery.com) eller från ditt Automation-konto.

Om du vill importera en modul https://www.powershellgallery.com från PowerShell-galleriet går du till och söker efter den modul som du vill importera. Klicka på **Distribuera till Azure Automation** på fliken Azure **Automation** under **Installationsalternativ**. Den här åtgärden öppnar Azure-portalen. På sidan **Importera** väljer du ditt Automation-konto och klickar på **OK**.

![Importmodul för PowerShell-galleri](../media/modules/powershell-gallery.png)

Du kan också importera moduler från PowerShell-galleriet direkt från ditt Automation-konto. I ditt Automation-konto väljer du **Moduler** under **Delade resurser**. Klicka på Bläddra i **galleriet bläddra**på modulens sida och sök sedan i PowerShell-galleriet efter en modul. Markera den modul som du vill importera och klicka på **Importera**. Klicka på **OK** på sidan **Importera** för att starta importen.

![PowerShell Gallery-import från Azure-portalen](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort den ursprungliga versionen av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som du vill ta bort är en nyare version av en av [standardmodulerna som installerats återställs](#default-modules) den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från ditt Automation-konto bort.

### <a name="azure-portal"></a>Azure Portal

I Azure-portalen navigerar du till ditt Automation-konto och väljer **Moduler** under **Delade resurser**. Markera den modul som du vill ta bort. På sidan **Modul** väljer du **Ta bort**. Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="powershell"></a>PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Invändiga cmdlets

Följande är en lista över cmdlets i den interna `Orchestrator.AssetManagement.Cmdlets` modulen som importeras till varje Automation-konto. Dessa cmdlets är tillgängliga i dina runbooks och DSC-konfigurationer och gör att du kan interagera med dina tillgångar i ditt Automation-konto. Dessutom kan du med de interna cmdletsna hämta hemligheter från krypterade **variabelvärden,** **autentiseringsuppgifter**och krypterade **anslutningsfält.** Azure PowerShell-cmdlets kan inte hämta dessa hemligheter. Dessa cmdletar kräver inte att du implicit ansluter till Azure när du använder dem, till exempel med hjälp av ett Run As-konto för att autentisera till Azure.

>[!NOTE]
>Dessa interna cmdlets är tillgängliga på en Windows Hybrid Runbook Worker, de är inte tillgängliga på en Linux Hybrid Runbook Worker. Använd motsvarande [AzureRM.Automation-](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) eller [Az-moduler](../az-modules.md) för runbooks som körs direkt på datorn eller mot resurser i din miljö. 
>

|Namn|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Vänta-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Lägga till en anslutningstyp i modulen

Du kan ange en anpassad [anslutningstyp](../automation-connections.md) som du kan använda i ditt Automation-konto genom att lägga till en valfri fil i modulen. Den här filen är en metadatafil som anger en Azure Automation-anslutningstyp som ska användas med modulens cmdlets i ditt Automation-konto. För att uppnå detta måste du först veta hur man skapar en PowerShell-modul. Mer information om modulförfattare finns i [Så här skriver du en PowerShell-skriptmodul](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Använda en anpassad anslutning i Azure-portalen](../media/modules/connection-create-new.png)

Om du vill lägga till en Azure Automation-anslutningstyp måste modulen innehålla en fil med namnet `<ModuleName>-Automation.json` som anger egenskaperna för anslutningstypen. Json-filen placeras i modulmappen för den komprimerade ZIP-filen. Den här filen innehåller fälten för en anslutning som krävs för att ansluta till det system eller den tjänst som modulen representerar. Konfigurationen slutar med att skapa en anslutningstyp i Azure Automation. Med den här filen kan du ange fältnamn, typer och om fälten ska krypteras eller vara valfria för modulens anslutningstyp. Följande exempel är en mall i json-filformatet som definierar en egenskap för användarnamn och lösenord:

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

## <a name="module-best-practices"></a>Metodtips för modul

PowerShell-moduler kan importeras till Azure Automation för att göra sina cmdlets tillgängliga för användning i runbooks och deras DSC-resurser tillgängliga för användning i DSC-konfigurationer. Bakom kulisserna lagrar Azure Automation dessa moduler och vid körningsjobb och DSC-kompileringsjobbkörningstid läser de in dem i Azure Automations sandlådor där runbooks körs och DSC-konfigurationer kompileras. Alla DSC-resurser i moduler placeras också automatiskt på Automation DSC-pull-servern. De kan dras av maskiner när de tillämpar DSC-konfigurationer.

Vi rekommenderar att du överväger följande när du skapar en PowerShell-modul för användning i Azure Automation:

* Ta INTE med en versionsmapp i ZIP-paketet.  Det här problemet är mindre ett problem för runbooks men kommer att orsaka ett problem med tjänsten Tillståndskonfiguration.  Azure Automation skapar versionsmappen automatiskt när modulen distribueras till noder som hanteras av DSC, och om det finns en versionsmapp kommer du att få två instanser.  Exempel på mappstruktur för en DSC-modul:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera viss hjälpinformation för cmdlets så att användaren kan få hjälp med att använda dem genom att köra cmdleten **Get-Help**. I följande exempel visas hur du definierar en synopsis och hjälper URI för i en PSM1-modulfil:

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

  Om du tillhandahåller den här informationen visas den här hjälpen med hjälp av **get-help-cmdlet** i PowerShell-konsolen. Den här beskrivningen visas också i Azure-portalen.

  ![Hjälp med integreringsmoduler](../media/modules/module-activity-description.png)

* Om modulen ansluter till en extern tjänst bör den innehålla en [anslutningstyp](#add-a-connection-type-to-your-module). Varje cmdlet i modulen ska kunna använda ett anslutningsobjekt (en instans av anslutningstypen) som en parameter. Användare kartlägger parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Baserat på runbook-exemplet ovan används en exempelkontosanslutningstillgång som heter ContosoConnection för att komma åt Contoso-resurser och returnera data från den externa tjänsten.

  I följande exempel mappas fälten till egenskaperna Användarnamn `PSCredential` och Lösenord för ett objekt och skickas sedan till cmdleten.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ett enklare och bättre sätt att närma sig detta beteende är direkt passerar anslutningsobjektet till cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Du kan aktivera beteenden som föregående exempel för dina cmdletar genom att låta dem acceptera ett anslutningsobjekt direkt som en parameter, i stället för bara anslutningsfält för parametrar. Vanligtvis vill du ha en parameteruppsättning för varje, så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att konstruera en hashtable för att fungera som anslutningsobjekt. Parameteruppsättningen `UserAccount`används för att skicka egenskaperna för anslutningsfält. `ConnectionObject`kan du skicka anslutningen rakt igenom.

* Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense hjälpa dig under designfasen med att fastställa utdataegenskaperna för cmdleten, för användning under redigeringar. Det är särskilt användbart under Automation runbook grafisk redigering, där design tid kunskap är nyckeln till en enkel användarupplevelse med din modul.

Lägg `[OutputType([<MyOutputType>])]` till där MyOutputType är en giltig typ. Mer information om OutputType finns i [Om Funktioner OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett `OutputType` exempel på hur du lägger till i en cmdlet:

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

* Gör alla cmdlets i modulen tillståndslösa. Flera runbook-jobb kan samtidigt köras i samma AppDomain och samma process och sandlåda. Om det finns någon delstat som delas på dessa nivåer kan jobb påverka varandra. Detta kan leda till återkommande och svåra att diagnostisera problem.  Här är ett exempel på vad du inte ska göra.

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

* Modulen ska vara helt innesluten i ett xcopy-kunna paket. Azure Automation-moduler distribueras till Automation sandlådor när runbooks behöver köras. Modulerna måste fungera oberoende av den värd de kör på. Du bör kunna zip upp och flytta ett modulpaket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att det ska ske bör modulen inte vara beroende av några filer utanför modulmappen. Den här mappen är den mapp som zippas upp när modulen importeras till Azure Automation. Modulen bör inte heller vara beroende av några unika registerinställningar på en värd, till exempel de inställningar som ställs in när en produkt installeras. Alla filer i modulen bör ha en sökväg som är färre än 140 tecken. Alla sökvägar över 140 tecken orsakar problem med att importera runbooken. Om den här metodtipset inte följs kan modulen inte användas i Azure Automation.  

* Om du refererar till [Azure Powershell Az-moduler](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i modulen, se `AzureRM`till att du inte också refererar till . Modulen `Az` kan inte användas tillsammans `AzureRM` med modulerna. `Az`stöds i runbooks men importeras inte som standard. Mer information `Az` om moduler och överväganden som ska beaktas finns i [Stöd för Az-modul i Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas de moduler som importeras som standard när ett Automation-konto skapas. Modulerna nedan kan importera nyare versioner av dem, men den ursprungliga versionen kan inte tas bort från ditt Automation-konto även om du tar bort en nyare version av dem.

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

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](/powershell/scripting/developer/windows-powershell)
