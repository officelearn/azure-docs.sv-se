---
title: Kompilera konfigurationer i Azure Automation State Configuration
description: I den här artikeln beskrivs hur du kompilerar DSC-konfigurationer (Desired State Configuration) för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b366777cce4dd4212a2e3ec0cdcd180e0d7164f4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757185"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilera DSC-konfigurationer i Azure Automation State Configuration

Du kan kompilera DSC-konfigurationer (Desired State Configuration) i Azure Automation State Configuration på följande sätt:

- Samlingstjänst för Azure State Configuration
  - Nybörjarmetod med interaktivt användargränssnitt
   - Spåra enkelt jobbtillstånd

- Windows PowerShell
  - Anropa från Windows PowerShell på lokal arbetsstation eller byggtjänst
  - Integrera med utvecklingstestpipeline
  - Ange komplexa parametervärden
  - Arbeta med nod- och icke-noddata i skala
  - Betydande prestandaförbättringar

Du kan också använda Azure Resource Manager-mallar med DSC-tillägg (Azure Desired State Configuration) för att skicka konfigurationer till dina virtuella Azure-datorer. Azure DSC-tillägget använder Azure VM Agent-ramverket för att leverera, anta och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. Information om kompilering med Azure Resource Manager-mallar finns i [Tillägget För önskad tillståndskonfiguration med Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portalen

1. Klicka på **Tillståndskonfiguration (DSC) från**ditt Automation-konto .
1. Klicka på fliken **Konfigurationer** och klicka sedan på konfigurationsnamnet för att kompilera.
1. Klicka på **Kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta om du vill kompilera den. Om konfigurationen har parametrar öppnas **bladet Kompilera konfiguration** så att du kan ange parametervärden.
1. Sidan Kompileringsjobb öppnas så att du kan spåra kompileringsjobbstatus. Du kan också använda den här sidan för att spåra nodkonfigurationerna (MOF-konfigurationsdokument) som placeras på pull-servern för Azure Automation State Configuration.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempelkod börjar sammanställa en DSC-konfiguration som heter SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** returnerar ett kompileringsjobbobjekt som du kan använda för att spåra jobbstatus. Du kan sedan använda det här kompileringsjobbobjektet med [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) för att bestämma status för kompileringsjobbet och [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) för att visa dess strömmar (utdata). Följande exempel startar kompileringen av SampleConfig-konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Deklarera grundläggande parametrar

Parameterdeklaration i DSC-konfigurationer, inklusive parametertyper och egenskaper, fungerar på samma sätt som i Azure Automation-runbooks. Se [Starta en runbook i Azure Automation](automation-starting-a-runbook.md) om du vill veta mer om runbook-parametrar.

I följande exempel används två parametrar som kallas *FeatureName* och *IsPresent*för att fastställa värdena för egenskaper i konfigurationen ParametersExample.sample, som genereras under kompileringen.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Du kan kompilera DSC-konfigurationer som använder grundläggande parametrar i Azure Automation State Configuration-portalen eller med Azure PowerShell.

#### <a name="portal"></a>Portalen

I portalen kan du ange parametervärden när du har klickat på **Kompilera**.

![Konfigurationskompileringsparametrar](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell kräver parametrar i en [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables), där nyckeln matchar parameternamnet och värdet är lika med parametervärdet.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Information om hur du skickar PSCredentials som parametrar finns i [Referenstillgångar](#credential-assets) nedan.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilera konfigurationer som innehåller sammansatta resurser i Azure Automation

Med funktionen **Sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. Den här funktionen gör det möjligt att tillämpa flera konfigurationer till en enda resurs. Se [Sammansatta resurser: Använda en DSC-konfiguration som en resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite) för att lära dig mer om sammansatta resurser.

> [!NOTE]
> Så att konfigurationer som innehåller sammansatta resurser kompileras korrekt måste du först importera alla DSC-resurser som kompositerna förlitar sig på till Azure Automation. Att lägga till en DSC-sammansatt resurs skiljer sig inte från att lägga till en PowerShell-modul i Azure Automation. Den här processen dokumenteras i [Hantera moduler i Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Hantera ConfigurationData vid kompilering av konfigurationer i Azure Automation

**Med ConfigurationData** kan du separera strukturell konfiguration från valfri miljöspecifik konfiguration när du använder PowerShell DSC. Mer information finns i [Separera "Vad" från "Var" i PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> När du kompilerar i Azure Automation State Configuration kan du använda **ConfigurationData** i Azure PowerShell men inte i Azure-portalen.

I följande exempel använder **DSC-konfiguration ConfigurationData** via nyckelorden $ConfigurationData och $AllNodes. Du behöver också [xWebAdministration-modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Du kan kompilera den föregående DSC-konfigurationen med Windows PowerShell. Följande skript lägger till två nodkonfigurationer till pull-tjänsten För Azure Automation State Configuration: ConfigurationDataSample.MyVM1 och ConfigurationDataSample.MyVM3.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Arbeta med resurser i Azure Automation under kompileringen

Tillgångsreferenser är desamma i både Azure Automation State Configuration och runbooks. Mer information finns i följande:

- [Certifikat](automation-certificates.md)
- [Anslutningar](automation-connections.md)
- [Autentiseringsuppgifter](automation-credentials.md)
- [Variabler](automation-variables.md)

#### <a name="credential-assets"></a>Autentiseringsuppgifter tillgångar

DSC-konfigurationer i Azure Automation kan referera till resurser för Automation-autentiseringsuppgifter med hjälp av cmdleten **Get-AutomationPSCredential.** Om en konfiguration har en parameter som anger ett **PSCredential-objekt** använder du **Get-AutomationPSCredential** genom att skicka strängnamnet för en Azure Automation-autentiseringstillgång till cmdleten för att hämta autentiseringsuppgifterna. Använd sedan objektet för parametern som kräver **PSCredential-objektet.** Bakom kulisserna hämtas Azure Automation-autentiseringstillgången med det namnet och skickas till konfigurationen. Exemplet nedan visar det här scenariot i praktiken.

För att autentiseringsuppgifterna ska vara säkra i nodkonfigurationer (MOF-konfigurationsdokument) måste autentiseringsuppgifterna krypteras i nodkonfigurationen MOF-fil. För närvarande måste du ge PowerShell DSC behörighet till utdataautentiseringsuppgifter i oformaterad text under nodkonfiguration MOF generation. PowerShell DSC är inte medveten om att Azure Automation krypterar hela MOF-filen efter genereringen via ett kompileringsjobb.

Du kan tala om för PowerShell DSC att det är okej att autentiseringsuppgifter matas ut i oformaterad text i de genererade nodkonfigurations-MOFsna med hjälp av konfigurationsdata. Du bör `PSDscAllowPlainTextPassword = $true` passera via **ConfigurationData** för varje nodblocknamn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en automationsautentiseringstillgång.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Du kan kompilera föregående DSC-konfiguration med PowerShell. Följande PowerShell-kod lägger till två nodkonfigurationer till pullservern för Azure Automation State Configuration: CredentialSample.MyVM1 och CredentialSample.MyVM2.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> När kompileringen är klar kan felmeddelandet "Microsoft.PowerShell.Management"-modulen inte importerades eftersom snapin-modulen Microsoft.PowerShell.Management redan har importerats. Du kan ignorera det här meddelandet.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Kompilera DSC-konfigurationen i Windows PowerShell

Du kan också importera nodkonfigurationer (MOFs) som har kompilerats utanför Azure. Importen omfattar kompilering från en utvecklararbetsstation eller i en tjänst som [Azure DevOps](https://dev.azure.com). Den här metoden har flera fördelar, inklusive prestanda och tillförlitlighet.

Kompilering i Windows PowerShell ger också möjlighet att signera konfigurationsinnehåll. DSC-agenten verifierar en signerad nodkonfiguration lokalt på en hanterad nod. Verifieringen säkerställer att konfigurationen som tillämpas på noden kommer från en auktoriserad källa.

> [!NOTE]
> En nodkonfigurationsfil får inte vara större än 1 MB för att den ska kunna importeras till Azure Automation.

Mer information om signering av nodkonfigurationer finns [i Förbättringar i WMF 5.1 - Så här signerar och modul .](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)

### <a name="compile-the-dsc-configuration"></a>Kompilera DSC-konfigurationen

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [Skriv, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Du kan köra den här processen från en utvecklararbetsstation eller inom en byggtjänst, till exempel [Azure DevOps](https://dev.azure.com). Du kan sedan importera MOF-filer som produceras genom att kompilera konfigurationen till Azure State Configuration-tjänsten.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importera en nodkonfiguration i Azure-portalen

1. Klicka på **Tillståndskonfiguration (DSC)** i ditt Automation-konto under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration** (DSC) klickar du på fliken Konfigurationer och sedan på **+ Lägg till**.
1. Klicka på mappikonen bredvid textrutan **Nod Configuration File** på sidan Importera för att bläddra efter en nodkonfigurationsfil (MOF) på den lokala datorn.

   ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)

1. Ange ett namn i fältet **Konfigurationsnamn.** Det här namnet måste matcha namnet på den konfiguration som nodkonfigurationen kompilerades från.
1. Klicka på **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importera en nodkonfiguration med Azure PowerShell

Du kan använda cmdleten [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) för att importera en nodkonfiguration till ditt Automation-konto.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/az.automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution till virtuella datorer med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
