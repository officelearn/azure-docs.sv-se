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
ms.openlocfilehash: c8d22e63be880c0cef0c4072e99ab85bf3250a1c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114282"
---
# <a name="manage-modules-in-azure-automation"></a>Använda moduler i Azure Automation

Med Azure Automation kan du importera PowerShell-moduler för att aktivera cmdletar i Runbooks och DSC-resurser i DSC-konfigurationer. Moduler som används i Azure Automation inkluderar:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Intern `Orchestrator.AssetManagement.Cmdlets` modul för Log Analytics agent för Windows
* Andra PowerShell-moduler
* Anpassade moduler som du skapar 

När du skapar ett Automation-konto importerar Azure Automation vissa moduler som standard. Se [standardmoduler](#default-modules).

När Azure Automation kör Runbook-och DSC-kompileringar laddas modulerna in i sand lådor där Runbooks kan köras och DSC-konfigurationerna kan kompileras. Automation placerar även DSC-resurser i moduler på DSC-pull-servern automatiskt. Datorer kan hämta resurserna när de använder DSC-konfigurationer.

>[!NOTE]
>Se till att bara importera de moduler som dina runbooks och DSC-konfigurationer faktiskt behöver. Vi rekommenderar inte att du importerar modulen root AZ eftersom den innehåller många andra moduler som du kanske inte behöver, vilket kan orsaka prestanda problem. Importera enskilda moduler, till exempel AZ. Compute, i stället.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas moduler som Azure Automation importerar som standard när du skapar ett Automation-konto. Automation kan importera nyare versioner av dessa moduler. Du kan dock inte ta bort den ursprungliga versionen från ditt Automation-konto, även om du tar bort en nyare version. Observera att dessa standardmoduler innehåller flera AzureRM-moduler. 

Azure Automation importerar inte root AZ-modulen automatiskt till nya eller befintliga Automation-konton. Mer information om hur du arbetar med dessa moduler finns i [migrera till AZ-moduler](#migrating-to-az-modules).

> [!NOTE]
> Vi rekommenderar inte att du ändrar moduler och Runbooks i Automation-konton som innehåller [Starta/stoppa virtuella datorer när de inte används-lösningen i Azure Automation](../automation-solution-vm-management.md).

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
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdletar | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-module-cmdlets"></a>Cmdletar för AZ-modul

För AZ. Automation har majoriteten av cmdletarna samma namn som för AzureRM-modulerna, förutom att AzureRm-prefixet har ändrats till AZ. En lista över AZ-moduler som inte följer denna namngivnings konvention finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Interna cmdletar

I följande tabell definieras de interna cmdletarna som stöds av `Orchestrator.AssetManagement.Cmdlets` modulen. Använd dessa cmdlets i Runbooks och DSC-konfigurationer för att interagera med Azure-tillgångar i Automation-kontot. Cmdletarna är utformade för att användas i stället för Azure PowerShell cmdlets för att hämta hemligheter från krypterade variabler, autentiseringsuppgifter och krypterade anslutningar. 

>[!NOTE]
>De interna cmdletarna är bara tillgängliga när du kör Runbooks i Azure sandbox-miljön eller på en Windows-Hybrid Runbook Worker. 

|Namn|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Vänta-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Observera att de interna cmdletarna skiljer sig från namn från AZ-och AzureRM-cmdletarna. Interna cmdlet-namn innehåller inte ord som "Azure" eller "AZ" i substantiv, men Använd ordet "Automation". Vi rekommenderar användning av AZ-eller AzureRM-cmdletar under körning av Runbook i en Azure-sandbox eller en Windows hybrid Worker. De kräver färre parametrar och körs i kontexten för ditt jobb som redan körs.

Vi rekommenderar att du använder AZ-eller AzureRM-cmdletar för att manipulera Azure Automation resurser utanför kontexten för en Runbook. 

## <a name="module-supporting-get-automationpscredential"></a>Modul som stöder get-AutomationPSCredential

`Get-AutomationPSCredential` Cmdleten är en del av modulen `Orchestrator.AssetManagement.Cmdlets`. Den här cmdleten `PSCredential` returnerar ett objekt, vilket förväntas av de flesta PowerShell-cmdletar som fungerar med autentiseringsuppgifter. Om du vill veta mer om hur du använder autentiseringsuppgifter i Azure Automation, se [inloggnings till gångar i Azure Automation](credentials.md).

## <a name="migrating-to-az-modules"></a>Migrera till AZ-moduler

### <a name="migration-considerations"></a>Överväganden vid migrering

Det här avsnittet innehåller överväganden för att ta hänsyn till när du migrerar till AZ-modulerna i Azure Automation. Se även [migrera Azure PowerShell från AzureRM till AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Användning av AzureRM-moduler och AZ-moduler i samma Automation-konto

 Vi rekommenderar inte att du kör AzureRM-moduler och AZ-moduler i samma Automation-konto. När du är säker på att du vill migrera från AzureRM till AZ, är det bäst att fullständigt genomföra en fullständig migrering. Den viktigaste orsaken till detta är att Azure Automation ofta återanvänder sandbox i Automation-kontot för att spara vid start tider. Om du inte gör en fullständig flyttning av modulen kan du starta ett jobb med endast AzureRM-moduler och sedan starta ett annat jobb med endast AZ-moduler. Sand boxen kraschar snart och du får ett allvarligt fel som anger att modulerna inte är kompatibla. Den här situationen resulterar i Slumpmässiga krascher för en specifik Runbook eller konfiguration. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Importera AZ-moduler till PowerShell-sessionen

Om du importerar en AZ-modul till ditt Automation-konto importeras modulen inte automatiskt till PowerShell-sessionen som används av Runbooks. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en Runbook anropar en cmdlet från en modul
* När en Runbook importerar modulen explicit med cmdleten [import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
* När en Runbook importerar en annan beroende modul
    
#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testa dina runbooks och DSC-konfigurationer innan du migrerar en modul

Se till att testa alla Runbooks och DSC-konfigurationer noggrant i ett separat Automation-konto innan du migrerar till AZ-modulerna. 

#### <a name="updates-for-tutorial-runbooks"></a>Uppdateringar för själv studie Runbooks 

När du skapar ett nytt Automation-konto, även efter migrering till AZ-moduler, installerar Azure Automation AzureRM-modulerna som standard. Du kan fortfarande uppdatera självstudiernas Runbooks med AzureRM-cmdletarna. Du bör dock inte köra dessa Runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Stoppa och ta bort schemat för alla Runbooks som använder AzureRM-moduler

För att se till att du inte kör några befintliga Runbooks eller DSC-konfigurationer som använder AzureRM-moduler måste du stoppa och ta bort schema för alla påverkade Runbooks och konfigurationer. Kontrol lera först att du granskar varje Runbook-eller DSC-konfiguration och dess scheman för att säkerställa att du kan schemalägga om objektet i framtiden om det behövs. 

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller cmdleten [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Se [ta bort ett schema](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Ta bort AzureRM-modulerna

Det går att ta bort AzureRM-modulerna innan du importerar AZ-modulerna. Borttagning av AzureRM-modulerna kan dock avbryta synkronisering av käll kontroll och orsaka att skript fortfarande har schemalagts för att fungera. Om du bestämmer dig för att ta bort modulerna, se [Avinstallera AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Importera AZ-modulerna

I det här avsnittet beskrivs hur du importerar AZ-modulerna i Azure Portal. Kom ihåg att endast importera de AZ-moduler som du behöver, inte hela AZ. Automation-modulen. Eftersom [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende för de andra AZ-modulerna måste du importera den här modulen innan andra.

1. Från ditt Automation-konto väljer du **moduler** under **delade resurser**. 
2. Klicka på **Bläddra Galleri** för att öppna sidan bläddra i galleriet.  
3. I Sök fältet anger du modulnamnet, till exempel `Az.Accounts`. 
4. På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

    ![Importera moduler till Automation-konto](../media/modules/import-module.png)

Den här import processen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen att importera. När du har hittat modulen väljer du den, väljer fliken **Azure Automation** och klickar på **distribuera för att Azure Automation**.

![Importera moduler direkt från galleriet](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testa dina runbooks

När du har importerat AZ-modulerna till Automation-kontot kan du börja redigera dina runbooks och DSC-konfigurationer för att använda de nya modulerna. Ett sätt att testa ändringen av en Runbook att använda de nya cmdletarna är genom att använda `Enable-AzureRmAlias -Scope Process` i början av runbooken. Genom att lägga till det här kommandot i din Runbook kan skriptet köras utan ändringar. 

## <a name="authoring-modules"></a>Redigera moduler

Vi rekommenderar att du följer de överväganden som beskrivs i det här avsnittet när du skapar en PowerShell-modul för användning i Azure Automation.

### <a name="version-folder"></a>Versions mapp

Ta inte med en versions-mapp i **. zip** -paketet för modulen.  Det här problemet är mindre viktigt för Runbooks, men orsakar ett problem med tjänsten för tillstånds konfiguration (DSC). Azure Automation skapar automatiskt mappen version när modulen distribueras till noder som hanteras av DSC. Om det finns en versions-mapp, slutar du med två instanser. Här är ett exempel på en mappstruktur för en DSC-modul:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Hjälp information

Ta med en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera hjälp information för cmdlets med hjälp av `Get-Help` cmdleten. I följande exempel visas hur du definierar en sammanfattning och hjälp-URI i en **. psm1** -modul-fil.

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

  Genom att ange den här informationen visas hjälp `Get-Help` text via cmdleten i PowerShell-konsolen. Den här texten visas också i Azure Portal.

  ![Hjälp med integreringsmoduler](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Anslutningstyp

Om modulen ansluter till en extern tjänst definierar du en [Anslutnings typ](#adding-a-connection-type-to-your-module). Varje cmdlet i modulen bör godkänna ett anslutnings objekt (en instans av anslutnings typen) som en parameter. Användarna mappar parametrarna för anslutnings till gången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet. Följande Runbook-exempel, baserat på exemplet i föregående avsnitt, använder en Contoso-anslutning till gång som `ContosoConnection` kallas för att komma åt contoso-resurser och returnera data från den externa tjänsten. I det här exemplet mappas fälten till egenskaperna `UserName` och `Password` för ett `PSCredential` objekt och skickas sedan till cmdleten.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ett enklare och bättre sätt att utnyttja det här beteendet är genom att skicka anslutningsobjektet direkt till cmdleten:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Du kan aktivera liknande beteende för dina cmdlets genom att tillåta att de accepterar ett anslutnings objekt direkt som en parameter, i stället för bara anslutnings fält för parametrar. Vanligt vis vill du ha en parameter uppsättning för var och en, så att en användare som inte använder Azure Automation kan anropa dina cmdletar utan att skapa en hash-post för att fungera som anslutnings objekt. Parameter uppsättningen `UserAccount` används för att skicka anslutnings fält egenskaper. `ConnectionObject`gör att du kan skicka anslutningen direkt genom.

### <a name="output-type"></a>Utdatatyp

Definiera utdatatypen för alla cmdletar i modulen. Genom att definiera en utdatatyp för en cmdlet kan du använda IntelliSense i design tid för att avgöra cmdletens utmatnings egenskaper under redigeringen. Den här metoden är särskilt användbar vid redigering av grafiska Runbook-jobb för vilka design tids kunskaper är en nyckel till en enkel användar upplevelse med modulen.

Lägg `[OutputType([<MyOutputType>])]` till `MyOutputType` WHERE är en giltig typ. Läs mer om `OutputType` [funktionerna i OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett exempel på att lägga `OutputType` till i en-cmdlet:

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

### <a name="cmdlet-state"></a>Cmdlet-tillstånd

Gör alla cmdlets i modulen tillstånds lösa. Flera Runbook-jobb kan samtidigt köras i samma `AppDomain` process och begränsat läge. Om det finns ett tillstånd som delas på dessa nivåer kan jobben påverka varandra. Det här beteendet kan leda till tillfälliga och svåra problem. Här är ett exempel på vad du inte ska göra:

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

### <a name="module-dependency"></a>Modul-beroende

Se till att modulen är helt i ett paket som kan kopieras med Xcopy. Azure Automation moduler distribueras till Automation-sandbox när Runbooks körs. Modulerna måste fungera oberoende av den värd som kör dem. 

Du bör kunna lägga upp och flytta ett modul-paket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att detta ska ske kontrollerar du att modulen inte är beroende av filer utanför modulen modul som är zippande när modulen importeras till Azure Automation. 

Modulen bör inte vara beroende av unika register inställningar på en värd. Exempel är de inställningar som görs när en produkt installeras. 

### <a name="module-file-paths"></a>Fil Sök vägar för modul

Se till att alla filer i modulen har sökvägar med färre än 140 tecken. Sökvägar som är över 140 tecken orsakar problem med att importera runbooks. Azure Automation kan inte importera en fil med Sök vägs storlek över 140 tecken till PowerShell- `Import-Module`sessionen med.

## <a name="importing-modules"></a>Importera moduler

I det här avsnittet definieras flera sätt att importera en modul till ditt Automation-konto. 

### <a name="import-modules-in-azure-portal"></a>Importera moduler i Azure Portal

Så här importerar du en modul i Azure Portal:

1. Navigera till ditt Automation-konto.
2. Välj **moduler** under **delade resurser**.
3. Klicka på **Lägg till en modul**. 
4. Välj den **. zip** -fil som innehåller modulen.
5. Klicka på **OK** för att börja importera processen.

### <a name="import-modules-using-powershell"></a>Importera moduler med PowerShell

Du kan använda cmdleten [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL för en module. zip-paket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell-galleriet direkt. Kom ihåg att hämta `ModuleName` och `ModuleVersion` från [PowerShell-galleriet](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importera moduler från PowerShell-galleriet

Du kan importera [PowerShell-galleriet](https://www.powershellgallery.com) moduler direkt från galleriet eller från ditt Automation-konto.

Så här importerar du en modul direkt från PowerShell-galleriet:

1. Gå till https://www.powershellgallery.com och Sök efter den modul som ska importeras.
2. Klicka på **distribuera för att Azure Automation** på fliken **Azure Automation** under **installations alternativ**. Den här åtgärden öppnar Azure Portal. 
3. På sidan Importera väljer du ditt Automation-konto och klickar på **OK**.

![PowerShell-galleriet importera modul](../media/modules/powershell-gallery.png)

Så här importerar du en PowerShell-galleriet-modul direkt från ditt Automation-konto:

1. Välj **moduler** under **delade resurser**. 
2. Klicka på **Bläddra i galleriet**på sidan moduler och Sök sedan i galleriet efter en modul. 
3. Välj den modul som ska importeras och klicka på **Importera**. 
4. På sidan Importera klickar du på **OK** för att starta import processen.

![PowerShell-galleriet importera från Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort de ursprungliga versionerna av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som ska tas bort är en nyare version av en av [standardmodulerna](#default-modules)återställs den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från Automation-kontot bort.

### <a name="delete-modules-in-azure-portal"></a>Ta bort moduler i Azure Portal

Så här tar du bort en modul i Azure Portal:

1. Navigera till ditt Automation-konto och välj **moduler** under **delade resurser**. 
2. Välj den modul som du vill ta bort. 
3. På sidan **modul** väljer du **ta bort**. Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="delete-modules-using-powershell"></a>Ta bort moduler med PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Lägga till en Anslutnings typ i modulen

Du kan ange en anpassad [Anslutnings typ](../automation-connections.md) som ska användas i ditt Automation-konto genom att lägga till en valfri metadatafil i modulen. Den här filen anger en Azure Automation Anslutnings typ som ska användas med modulens cmdlets i ditt Automation-konto. Mer information om hur du redigerar en PowerShell-modul finns i [så här skriver du en PowerShell-modul för skript](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Använd en anpassad anslutning i Azure Portal](../media/modules/connection-create-new.png)

Filen som anger egenskaper för Anslutnings typ heter ** &lt;Modulnamn&gt;-Automation. JSON** och finns i mappen module i den komprimerade **zip** -filen. Den här filen innehåller fälten i en anslutning som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Konfigurationen tillåter att en Anslutnings typ skapas i Azure Automation. Med hjälp av den här filen kan du ange fält namn, typer och om fälten är krypterade eller valfria för anslutnings typen för modulen. Följande exempel är en mall i **JSON** -filformatet som definierar användar namn och lösen ords egenskaper:

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

* Mer information om hur du använder Azure PowerShell moduler finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Mer information om hur du skapar PowerShell-moduler finns i [skriva en Windows PowerShell-modul](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
