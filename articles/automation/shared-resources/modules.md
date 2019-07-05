---
title: Hantera moduler i Azure Automation
description: Den här artikeln beskriver hur du hanterar moduler i Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-resources
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69817d1412aa13d0e7983aa3ad27c15e59185432
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478162"
---
# <a name="manage-modules-in-azure-automation"></a>Hantera moduler i Azure Automation

Azure Automation tillhandahåller möjligheten att importera PowerShell-moduler till ditt Automation-konto som ska användas av PowerShell-baserade runbooks. Dessa moduler kan vara anpassade moduler som du har skapat från PowerShell-galleriet eller AzureRM och Az-moduler för Azure. När du skapar ett Automation-konto importeras vissa moduler som standard.

## <a name="import-modules"></a>Importera moduler

Det finns flera sätt att du kan importera en modul till ditt Automation-konto. I följande avsnitt visas de olika sätten att importera en modul.

> [!NOTE]
> Den maximala sökvägen till en fil i en modul som ska användas i Azure Automation är 140 tecken. Valfri sökväg över 140 tecken kan inte importeras till PowerShell-session med `Import-Module`.

### <a name="powershell"></a>PowerShell

Du kan använda den [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) att importera en modul till ditt Automation-konto. Cmdlet: en tar en url till en modul zip-paketet.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure Portal

Gå till ditt Automation-konto i Azure-portalen och välj **moduler** under **delade resurser**. Klicka på **+ Lägg till en modul**. Välj en **.zip** -fil som innehåller din modul och klicka på **Ok** att importprocessen.

### <a name="powershell-gallery"></a>PowerShell-galleriet

Moduler från PowerShell-galleriet kan antingen vara importerade från den [PowerShell-galleriet](https://www.powershellgallery.com) direkt eller från ditt Automation-konto.

Om du vill importera en modul från PowerShell-galleriet, gå till https://www.powershellgallery.com och Sök efter den modul som du vill importera. Klicka på **distribuera till Azure Automation** på den **Azure Automation** fliken **installationsalternativ**. Den här åtgärden öppnar Azure Portal. På den **Import** väljer du ditt Automation-konto och klicka på **OK**.

![Importera modulen för PowerShell-galleriet](../media/modules/powershell-gallery.png)

Du kan även importera moduler från PowerShell-galleriet direkt från ditt Automation-konto. I ditt Automation-konto väljer **moduler** under **delade resurser**. På sidan moduler **bläddringsgalleriegenskapen**. Gör det öppnas den **Bläddringsgalleriegenskapen** sidan. Du kan använda den här sidan för att söka i PowerShell-galleriet för en modul. Välj den modul som du vill importera och klicka på **importera**. På den **importera** klickar du på **OK** att starta importen.

![Importera PowerShell-galleriet från Azure-portalen](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul, kan du ta bort den från ditt Automation-konto. Du kan inte ta bort den ursprungliga versionen av den [standard moduler](#default-modules) som importeras när du skapar ett Automation-konto. Om du vill ta bort modulen är en nyare version av en av de [standard moduler](#default-modules) installerad, det ska återställa till den version som installerades med ditt Automation-konto. I annat fall tas alla moduler som du tar bort från ditt Automation-konto.

### <a name="azure-portal"></a>Azure Portal

Gå till ditt Automation-konto i Azure-portalen och välj **moduler** under **delade resurser**. Välj den modul som du vill ta bort. På den **modulen** sida, clcick **ta bort**. Om den här modulen är en av de [standard moduler](#default-modules) det kommer att återställas till den version som var tillgänglig när Automation-kontot har skapats.

### <a name="powershell"></a>PowerShell

Ta bort en modul via PowerShell genom att köra följande kommando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Intern-cmdletar

Följande är en lista över cmdletar i den interna `Orchestrator.AssetManagement.Cmdlets` modul som har importerats till varje Automation-konto. Dessa cmdletar är tillgängliga i dina runbooks och DSC-konfigurationer och att du kan interagera med dina resurser i ditt Automation-konto. Dessutom interna cmdletar kan du hämta hemligheter från krypterad **variabeln** värden, **autentiseringsuppgifter**, och krypterad **anslutning** fält. Azure PowerShell-cmdlets går inte att hämta dessa hemligheter. Dessa cmdletar kräver inte implicit ansluta till Azure när du använder dem. Detta är fördelaktigt för scenarier där du har en anslutning, till exempel ett kör som-konto som du vill använda för att autentisera till Azure.

|Namn|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-automationvariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Lägg till en anslutningstyp i din modul

Du kan ange en anpassad [anslutningstypen](../automation-connections.md) som du kan använda i ditt Automation-konto genom att lägga till en valfri fil i din modul. Den här filen är en metadatafil som anger en Azure Automation-anslutningstyp som ska användas med modulens cmdlets i ditt Automation-konto. Du måste veta hur du skapar en PowerShell-modul för att uppnå detta. Mer information om redigering av modulen finns i [hur du skriver en PowerShell-skript-modul](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Använda en anpassad anslutning i Azure portal](../media/modules/connection-create-new.png)

Om du vill lägga till en Azure Automation-anslutningstyp måste din modul måste innehålla en fil med namnet `<ModuleName>-Automation.json` som anger egenskaperna för anslutningstypen. Det här är en json-fil som placeras i modulmappen för komprimerade ZIP-fil. Den här filen innehåller fälten för en anslutning som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Den här konfigurationen identisk att en anslutningstyp skapas i Azure Automation. Med den här filen kan du ange fältnamn, typer och huruvida fälten ska vara krypterade eller valfri för en typ av modulen. I följande exempel är en mall i json-format som definierar en egenskap för användarnamn och lösenord:

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

## <a name="module-best-practices"></a>Metodtips för modulen

PowerShell-moduler kan importeras till Azure Automation så att deras cmdlets tillgängliga för användning i runbooks och deras DSC-resurser tillgängliga för användning i DSC-konfigurationer. I bakgrunden, Azure Automation lagrar dessa moduler och när runbook-jobben och DSC-kompileringsjobben in dem i Azure Automation-sandboxar där köra runbooks och DSC-konfigurationer kompileras. Alla DSC-resurser i moduler placeras också automatiskt på Automation DSC-hämtningsservern. De kan hämtas av datorer när de använder DSC-konfigurationer.

Vi rekommenderar att du Tänk på följande när du redigerar en PowerShell-modul för användning i Azure Automation:

* Lägg till en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera viss hjälpinformation för cmdlets så att användaren kan få hjälp med att använda dem genom att köra cmdleten **Get-Help**. I följande exempel visas hur du definierar en sammanfattning och hjälp-URI för i en .psm1-filen för modulen:

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

  Tillhandahåller den här informationen visas den med den **Get-Help** cmdlet i PowerShell-konsolen. Den här beskrivningen visas också i Azure-portalen.

  ![Hjälp med integreringsmoduler](../media/modules/module-activity-description.png)

* Om modulen ansluter till en extern tjänst, den ska innehålla en [anslutningstypen](#add-a-connection-type-to-your-module). Varje cmdlet i modulen ska kunna använda ett anslutningsobjekt (en instans av anslutningstypen) som en parameter. På så sätt kan användarna mappa parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Baserat på runbook-exemplet ovan, används ett exempel Contoso-anslutningstillgång kallad ContosoConnection att komma åt resurser för Contoso och returnera data från den externa tjänsten.

  I följande exempel fälten mappas till egenskaperna användarnamn och lösenord för en `PSCredential` objektet och sedan skickas till cmdleten.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ett enklare och bättre sätt att använda det här beteendet direkt skicka anslutningsobjektet till cmdleten:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Du kan aktivera beteenden som i föregående exempel för dina cmdlets genom att tillåta att de accepterar ett anslutningsobjekt direkt som en parameter i stället för bara Anslutningsfält för parametrar. Vanligtvis vill du en parameteruppsättning för var och en, så att en användare som inte använder Azure Automation kan anropa dina cmdlets utan att skapa en hash-tabell som fungerar som anslutningsobjektet. Parameteruppsättningen `UserAccount`, används för att skicka fältegenskaper. `ConnectionObject` kan du skicka anslutningen rakt igenom.

* Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense hjälpa dig under designfasen med att fastställa utdataegenskaperna för cmdleten, för användning under redigeringar. Det är särskilt användbart under runbook grafisk redigering av Automation, där goda kunskaper om designfasen är nyckeln till en enkel användarupplevelse med modulen.

  Detta kan uppnås genom att lägga till `[OutputType([<MyOutputType>])]` där MyOutputType är en giltig typ. Läs mer om OutputType i [om Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett exempel på att lägga till `OutputType` för en cmdlet:

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

  Detta liknar funktionen ”type-ahead” för en cmdlet-utdata i PowerShell ISE utan att behöva köra den.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Gör alla cmdlets i modulen tillståndslösa. Flera runbook-jobb kan köras samtidigt i samma AppDomain och samma process och sandbox-miljön. Om det finns några tillstånd som delas på dessa nivåer, kan jobb påverka varandra. Detta kan leda till tillfällig och svårt att diagnostisera problem.  Här är ett exempel på vad du inte ska göra.

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

* Modulen bör finnas i ett xcopy-kan paket. Azure Automation-moduler distribueras till begränsat Automation-läge när runbooks behöver köra. Modulerna måste fungera oberoende av värden som de körs på. Du bör kunna Zip upp och flytta ett paket för modulen och låta den fungera som vanligt när de importeras till en annan värd PowerShell-miljö. För att det ska ske, bör inte modulen beror på några filer utanför modulmappen. Den här mappen är den mapp som dekomprimeras vid modulen importeras till Azure Automation. Modulen bör också inte beroende av unika registerinställningar på en värd som dessa inställningar anges när en produkt installeras. Alla filer i modulen ska ha en sökväg färre än 140 tecken. Alla sökvägar över 140 tecken kommer orsaka problem när du importerar en runbook. Om denna bästa praxis inte följs användas modulen inte i Azure Automation.  

* Om du refererar till [Az för Azure Powershell-moduler](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i din modul, se till att du inte också refererar till `AzureRM`. Den `Az` modulen kan inte användas tillsammans med den `AzureRM` moduler. `Az` har stöd för runbooks men inte har importerat som standard. Vill veta mer om den `Az` moduler och saker att ta hänsyn till finns i [Az modulen stöd i Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas de moduler som importeras som standard när ett Automation-konto har skapats. Moduler som anges nedan kan med nyare versioner av dem importeras, men den ursprungliga versionen kan inte tas bort från ditt Automation-konto även om du tar bort en nyare version av dem.

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

* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell-modul](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)