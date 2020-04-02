---
title: Hantera moduler i Azure Automation
description: I den här artikeln beskrivs hur du hanterar moduler i Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548925"
---
# <a name="manage-modules-in-azure-automation"></a>Hantera moduler i Azure Automation

Azure Automation gör att import av PowerShell-moduler till ditt Automation-konto kan användas av PowerShell-baserade runbooks. Dessa moduler kan vara anpassade moduler som du har skapat, moduler från PowerShell-galleriet eller AzureRM- och Az-modulerna för Azure. När du skapar ett Automation-konto importeras vissa moduler som standard.

## <a name="import-modules"></a>Importera moduler

Det finns flera sätt att importera en modul till ditt Automation-konto. I följande avsnitt visas olika sätt att importera en modul.

> [!NOTE]
> Den maximala sökvägen för en fil i en modul som används i Azure Automation är 140 tecken. Det går inte att importera en fil med sökvägsstorlek över `Import-Module`140 tecken till PowerShell-sessionen med .

### <a name="powershell"></a>PowerShell

Du kan använda cmdleten [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL för en modul .zip-paket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell Gallery direkt. Se till `ModuleName` att `ModuleVersion` ta tag i och från [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

Så här importerar du en modul i Azure-portalen:

1. Navigera till ditt Automation-konto.
2. Välj **Moduler** under **Delade resurser**.
3. Klicka på **Lägg till en modul**. 
4. Välj **zip-filen** som innehåller modulen.
5. Klicka på **OK** för att starta för att importera processen.

### <a name="powershell-gallery"></a>PowerShell-galleriet

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

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort de ursprungliga versionerna av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som ska tas bort är en nyare version av en av [standardmodulerna](#default-modules)återställs den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från ditt Automation-konto bort.

### <a name="azure-portal"></a>Azure Portal

Så här tar du bort en modul i Azure-portalen:

1. Navigera till ditt Automation-konto och välj **Moduler** under **Delade resurser**. 
2. Markera den modul som du vill ta bort. 
3. På sidan **Modul** väljer du **Ta bort**. Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="powershell"></a>PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Invändiga cmdlets

I tabellen nedan visas cmdlets i den interna `Orchestrator.AssetManagement.Cmdlets` modulen som importeras till varje Automation-konto. Dessa cmdlets är tillgängliga i dina runbooks och DSC-konfigurationer och gör att du kan interagera med tillgångar i ditt Automation-konto. Dessutom kan du med de interna cmdletsna hämta hemligheter från krypterade variabler, autentiseringsuppgifter och krypterade anslutningar. Azure PowerShell-cmdlets kan inte hämta dessa hemligheter. Dessa cmdletar kräver inte att du implicit ansluter till Azure när du använder dem, som när du använder ett Run As-konto för att autentisera till Azure.

>[!NOTE]
>Dessa interna cmdlets är tillgängliga på en Windows Hybrid Runbook Worker, men inte på en Linux Hybrid Runbook Worker. Använd motsvarande [AzureRM.Automation-](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) eller [Az-modul](../az-modules.md) cmdlets för runbooks som körs direkt på datorn eller mot resurser i din miljö. 

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

Du kan ange en anpassad [anslutningstyp](../automation-connections.md) som ska användas i ditt Automation-konto genom att lägga till en valfri metadatafil i modulen. Den här filen anger en Azure Automation-anslutningstyp som ska användas med modulens cmdlets i ditt Automation-konto. För att uppnå detta måste du först veta hur man skapar en PowerShell-modul. Se [Hur man skriver en PowerShell-skriptmodul](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

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

## <a name="module-best-practices"></a>Metodtips för modul

Du kan importera PowerShell-moduler till Azure Automation för att göra deras cmdlets tillgängliga i runbooks och deras DSC-resurser tillgängliga inom DSC-konfigurationer. Bakom kulisserna lagrar Azure Automation dessa moduler. Vid runbook-jobb och DSC-kompileringsjobbkörningstid läser Automation in dem i Azure Automations sandlådor där runbooks körs och DSC-konfigurationer kompileras. Alla DSC-resurser i moduler placeras också automatiskt på Automation DSC-pull-servern. Maskiner kan dra dem när de tillämpar DSC-konfigurationer.

Vi rekommenderar att du överväger följande när du skapar en PowerShell-modul för användning i Azure Automation:

* Ta INTE med en versionsmapp i **ZIP-paketet.**  Det här problemet är mindre ett problem för runbooks men orsakar ett problem med tjänsten Tillståndskonfiguration. Azure Automation skapar versionsmappen automatiskt när modulen distribueras till noder som hanteras av DSC. Om det finns en versionsmapp får du två instanser. Här är ett exempel mappstruktur för en DSC-modul:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera hjälpinformation för `Get-Help` cmdlets med hjälp av cmdlet. I följande exempel visas hur du definierar en synopsis och hjälper URI i en **PSM1-modulfil:**

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

* Om modulen ansluter till en extern tjänst definierar du en [anslutningstyp](#add-a-connection-type-to-your-module). Varje cmdlet i modulen ska acceptera ett anslutningsobjekt (en instans av den anslutningstypen) som en parameter. Användare kartlägger parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Baserat på runbook-exemplet ovan används en contoso-anslutningstillgång som anropas `ContosoConnection` för att komma åt Contoso-resurser och returnera data från den externa tjänsten.

  I följande exempel mappas fälten `UserName` `Password` till ett `PSCredential` objekts egenskaper och skickas sedan till cmdleten.

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

  Du kan aktivera liknande beteende för dina cmdletar genom att låta dem acceptera ett anslutningsobjekt direkt som en parameter, i stället för bara anslutningsfält för parametrar. Vanligtvis vill du ha en parameteruppsättning för varje, så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att konstruera en hashtable för att fungera som anslutningsobjekt. Parameteruppsättningen `UserAccount`används för att skicka egenskaperna för anslutningsfält. `ConnectionObject`kan du skicka anslutningen rakt igenom.

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

* Modulen ska vara helt innesluten i ett xcopy-kunna paket. Azure Automation-moduler distribueras till Automation sandlådor när runbooks behöver köras. Modulerna måste fungera oberoende av den värd de kör på. Du bör kunna zip upp och flytta ett modulpaket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att detta ska ske bör modulen inte vara beroende av några filer utanför modulmappen som zippas upp när modulen importeras till Azure Automation. Modulen bör inte heller vara beroende av några unika registerinställningar på en värd, till exempel de inställningar som ställs in när en produkt installeras. Alla filer i modulen bör ha en sökväg som är färre än 140 tecken. Alla sökvägar över 140 tecken orsakar problem med att importera runbooken. Om du inte följer den här metodtipset kan modulen inte kunna tas i Azure Automation.  

* Om du refererar till [Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i modulen kontrollerar `AzureRM`du att du inte refererar till. Du kan inte `Az` använda modulen `AzureRM` tillsammans med modulen. `Az`stöds i runbooks men importeras inte som standard. Mer information `Az` om modulen och de överväganden som ska beaktas finns i [Stöd för Az-modul i Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas moduler som importerats som standard när ett Automation-konto skapas. Automation kan importera nyare versioner av dessa moduler. Du kan dock inte ta bort den ursprungliga versionen från ditt Automation-konto även om du tar bort en nyare version.

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

* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](/powershell/scripting/developer/windows-powershell).
