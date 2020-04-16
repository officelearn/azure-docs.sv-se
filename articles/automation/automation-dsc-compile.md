---
title: Kompilera DSC-konfigurationer i Azure Automation State Configuration
description: I den här artikeln beskrivs hur du kompilerar DSC-konfigurationer (Desired State Configuration) för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: f7558745442ac26fc33a063ff66fe170d08487ac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392076"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilera DSC-konfigurationer i Azure Automation State Configuration

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

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portalen

1. Klicka på **Tillståndskonfiguration (DSC)** i ditt Automation-konto .
1. Klicka på fliken **Konfigurationer** och klicka sedan på konfigurationsnamnet för att kompilera.
1. Klicka på **Kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta om du vill kompilera den. Om konfigurationen har parametrar öppnas **bladet Kompilera konfiguration** så att du kan ange parametervärden.
1. Sidan Kompileringsjobb öppnas så att du kan spåra kompileringsjobbstatus. Du kan också använda den här sidan för att spåra nodkonfigurationerna (MOF-konfigurationsdokument) som placeras på pull-servern för Azure Automation State Configuration.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempelkod börjar sammanställa en DSC-konfiguration som heter **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`returnerar ett kompileringsjobbobjekt som du kan använda för att spåra jobbstatus. Du kan sedan använda det här kompileringsjobbobjektet med [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) för att bestämma status för kompileringsjobbet och [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) för att visa dess strömmar (utdata). Följande exempel startar kompileringen av SampleConfig-konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

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

I följande `FeatureName` exempel `IsPresent` används och parametrar för att fastställa värdena för egenskaper i nodkonfigurationen **ParametersExample.sample,** som genereras under kompileringen.

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

Information om `PSCredential` hur du skickar objekt som parametrar finns i [Autentiseringsuppgifter.](#credential-assets)

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilera konfigurationer som innehåller sammansatta resurser i Azure Automation

Med funktionen **Sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. Den här funktionen gör det möjligt att tillämpa flera konfigurationer till en enda resurs. Se [Sammansatta resurser: Använda en DSC-konfiguration som en resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite) för att lära dig mer om sammansatta resurser.

> [!NOTE]
> Så att konfigurationer som innehåller sammansatta resurser kompileras korrekt måste du först importera alla DSC-resurser som kompositerna förlitar sig på till Azure Automation. Att lägga till en DSC-sammansatt resurs skiljer sig inte från att lägga till en PowerShell-modul i Azure Automation. Den här processen dokumenteras i [Hantera moduler i Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Hantera ConfigurationData vid kompilering av konfigurationer i Azure Automation

`ConfigurationData`är en inbyggd DSC-parameter som gör att du kan separera strukturell konfiguration från alla miljöspecifika konfigurationer när du använder PowerShell DSC. Mer information finns i [Separera "Vad" från "Var" i PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> När du kompilerar i Azure `ConfigurationData` Automation State Configuration kan du använda i Azure PowerShell men inte i Azure-portalen.

Följande exempel DSC-konfiguration använder `ConfigurationData` via nyckelorden `$ConfigurationData` och. `$AllNodes` Du behöver också [xWebAdministration-modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet.

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

Du kan kompilera den föregående DSC-konfigurationen med Windows PowerShell. Följande skript lägger till två nodkonfigurationer i pull-tjänsten För Azure Automation State Configuration: **ConfigurationDataSample.MyVM1** och **ConfigurationDataSample.MyVM3**.

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

DSC-konfigurationer i Azure Automation kan referera till `Get-AutomationPSCredential` resurser för Automation-autentiseringsuppgifter med hjälp av cmdleten. Om en konfiguration har en `PSCredential` parameter som `Get-AutomationPSCredential` anger ett objekt, använd genom att skicka strängnamnet för en Azure Automation-referenstillgång till cmdleten för att hämta autentiseringsuppgifterna. Använd sedan objektet för den parameter `PSCredential` som kräver objektet. Bakom kulisserna hämtas Azure Automation-autentiseringstillgången med det namnet och skickas till konfigurationen. Exemplet nedan visar det här scenariot i praktiken.

För att autentiseringsuppgifterna ska vara säkra i nodkonfigurationer (MOF-konfigurationsdokument) måste autentiseringsuppgifterna krypteras i nodkonfigurationen MOF-fil. För närvarande måste du ge PowerShell DSC behörighet till utdataautentiseringsuppgifter i oformaterad text under nodkonfiguration MOF generation. PowerShell DSC är inte medveten om att Azure Automation krypterar hela MOF-filen efter genereringen via ett kompileringsjobb.

Du kan tala om för PowerShell DSC att det är okej att autentiseringsuppgifter matas ut i oformaterad text i de genererade nodkonfigurations-MOFsna med hjälp av konfigurationsdata. Du bör `PSDscAllowPlainTextPassword = $true` `ConfigurationData` skicka via för varje nodblocknamn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

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

Du kan kompilera föregående DSC-konfiguration med PowerShell. Följande PowerShell-kod lägger till två nodkonfigurationer till pullservern för Azure Automation State Configuration: **CredentialSample.MyVM1** och **CredentialSample.MyVM2**.

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
> När kompileringen är klar kan `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` felmeddelandet Du kan ignorera det här meddelandet på ett säkert sätt.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Kompilera DSC-konfigurationen i Windows PowerShell

Du kan också importera nodkonfigurationer (MOF-filer) som har kompilerats utanför Azure. Importen omfattar kompilering från en utvecklararbetsstation eller i en tjänst som [Azure DevOps](https://dev.azure.com). Den här metoden har flera fördelar, inklusive prestanda och tillförlitlighet.

Kompilering i Windows PowerShell ger också möjlighet att signera konfigurationsinnehåll. DSC-agenten verifierar en signerad nodkonfiguration lokalt på en hanterad nod. Verifieringen säkerställer att konfigurationen som tillämpas på noden kommer från en auktoriserad källa.

> [!NOTE]
> En nodkonfigurationsfil får inte vara större än 1 MB för att Azure Automation ska kunna importera den.

Mer information om signering av nodkonfigurationer finns [i Förbättringar i WMF 5.1 - Så här signerar och modul .](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)

### <a name="compile-the-dsc-configuration"></a>Kompilera DSC-konfigurationen

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [Skriv, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Du kan köra den här processen från en utvecklararbetsstation eller inom en byggtjänst, till exempel [Azure DevOps](https://dev.azure.com). Du kan sedan importera MOF-filer som produceras genom att kompilera konfigurationen till Azure State Configuration-tjänsten.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importera en nodkonfiguration i Azure-portalen

1. Klicka på **Tillståndskonfiguration (DSC)** i ditt Automation-konto under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration** (DSC) klickar du på fliken Konfigurationer och sedan **på Lägg till**.
1. Klicka på mappikonen bredvid fältet **Nod Configuration File** på sidan Importera för att söka efter en MOF-fil för nodkonfiguration på den lokala datorn.

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
- En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution till virtuella datorer med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
