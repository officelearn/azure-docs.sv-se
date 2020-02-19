---
title: Kompilera konfigurationer i Azure Automation tillstånds konfiguration
description: Den här artikeln beskriver hur du kompilerar Desired State Configuration (DSC)-konfigurationer för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462130"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration

Du kan kompilera DSC-konfigurationer (Desired State Configuration) på två sätt med Azure Automation tillstånds konfiguration: i Azure och i Windows PowerShell. I följande tabell får du hjälp att avgöra när du ska använda vilken metod som baseras på egenskaperna för var och en:

- Tjänsten Azure State Configuration Compilation
  - Nybörjar metod med interaktivt användar gränssnitt
   - Spåra jobb status enkelt

- Windows PowerShell
  - Anropa från Windows PowerShell på lokal arbets Station eller build-tjänst
  - Integrera med pipeline för utvecklings test
  - Ange komplexa parameter värden
  - Arbeta med nod-och icke-nods data i skala
  - Betydande prestanda förbättringar

Information om kompilering finns i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portal

1. Från ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)** .
1. Klicka på fliken **konfigurationer** och klicka sedan på konfigurations namnet som ska kompileras.
1. Klicka på **kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta att du vill kompilera den. Om konfigurationen har parametrar öppnas bladet **kompilera konfiguration** så att du kan ange parameter värden.
1. Sidan för att kompilera jobb öppnas så att du kan spåra jobbets status. Du kan också använda den här sidan för att spåra nodkonfigurationer (MOF-konfigurationsfilen) som jobbet placerar på hämtnings servern för Azure Automation tillstånds konfiguration.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempel kod börjar kompilera en DSC-konfiguration med namnet SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** returnerar ett Compilation Job-objekt som du kan använda för att spåra dess status. Du kan sedan använda det här konfigurationsobjektet för kompilering med [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) för att fastställa status för compile-jobbet och med [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) för att visa dess strömmar (utdata). Följande exempel kod startar kompilering av SampleConfig-konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

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

Parameter deklaration i DSC-konfigurationer, inklusive parameter typer och egenskaper, fungerar på samma sätt som i Azure Automation runbooks. Mer information om Runbook-parametrar finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md) .

I följande exempel används två parametrar som kallas **featureName** och **IsPresent**för att fastställa värdena för egenskaperna i noden **ParametersExample. Sample** Node som genereras under kompileringen.

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

Du kan kompilera DSC-konfigurationer som använder grundläggande parametrar i konfigurations portalen för Azure Automation tillstånd eller med Azure PowerShell:

#### <a name="portal"></a>Portal

I portalen kan du ange parameter värden efter att du klickat på **kompilera**.

![Parametrar för att kompilera konfiguration](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell kräver parametrar i en [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) -nyckel där nyckeln matchar parameter namnet och värdet är lika med parametervärdet.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Information om hur du skickar PSCredentials som parametrar finns i [behörighets till gångar](#credential-assets) nedan.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilera konfigurationer som innehåller sammansatta resurser i Azure Automation

Med funktionen **sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. På så sätt kan du tillämpa flera konfigurationer på en enskild resurs. Se [sammansatta resurser: använda en DSC-konfiguration som en resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite) för att lära dig mer om sammansatta resurser.

> [!NOTE]
> För konfigurationer som innehåller sammansatta resurser som ska kompileras korrekt måste du först se till att alla DSC-resurser som den sammansatta filen använder importeras till Azure Automation.

Att lägga till en DSC-sammansatt resurs skiljer sig inte från att lägga till en PowerShell-modul i Azure Automation. Processen dokumenteras i [Hantera moduler i Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Hantera ConfigurationData när du kompilerar konfigurationer i Azure Automation

Med funktionen **ConfigurationData** kan du separera strukturell konfiguration från valfri miljö bestämd konfiguration när du använder PowerShell DSC. Se [avgränsa "vad" från "där" i POWERSHELL DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) för att lära dig mer om ConfigurationData.

> [!NOTE]
> Du kan använda ConfigurationData när du kompilerar i Azure Automation tillstånds konfiguration med Azure PowerShell, men inte i Azure Portal.

I följande exempel på DSC-konfiguration används ConfigurationData via $ConfigurationData och $AllNodes nyckelord. Du behöver också [xWebAdministration-modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet:

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

Du kan kompilera föregående DSC-konfiguration med Windows PowerShell. Följande skript lägger till två nodkonfigurationer till pull-tjänsten för Azure Automation tillstånds konfiguration: ConfigurationDataSample. MyVM1 och ConfigurationDataSample. MyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Arbeta med till gångar i Azure Automation under kompileringen

Till gångs referenser är desamma i både Azure Automation tillstånds konfiguration och Runbooks. Mer information finns i följande avsnitt:

- [Certifikat](automation-certificates.md)
- [Anslutningar](automation-connections.md)
- [Autentiseringsuppgifter](automation-credentials.md)
- [Variabler](automation-variables.md)

#### <a name="credential-assets"></a>Inloggnings till gångar

DSC-konfigurationer i Azure Automation kan referera till till gångar för automatisering av autentiseringsuppgifter med hjälp av cmdleten **Get-AutomationPSCredential** . Om en konfiguration har en parameter som har en PSCredential-typ kan du använda **Get-AutomationPSCredential** genom att skicka in sträng namnet för en Azure Automation Credential-till gång för att hämta autentiseringsuppgifterna. Du kan sedan använda objektet för den parameter som kräver PSCredential-objektet. I bakgrunden hämtas den Azure Automation autentiseringsuppgiften med det namnet och skickas till konfigurationen. Exemplet nedan visar hur åtgärden fungerar.

Att bevara autentiseringsuppgifterna säker i en nods konfiguration kräver att autentiseringsuppgifterna krypteras i MOF-filen för nodens konfiguration. Du måste informera PowerShell DSC särskilt om att det har behörighet att skriva autentiseringsuppgifter i klartext under MOF-genereringen av nodens konfiguration. PowerShell DSC vet inte att Azure Automation krypterar hela MOF-filen efter att den har genererats via ett kompilerings jobb.

För att ge PowerShell DSC behörighet att skriva autentiseringsuppgifter i klartext i den genererade nodens konfiguration MOF: ar med hjälp av konfigurations data, pass `PSDscAllowPlainTextPassword = $true`. Du kan skicka den här informationen via ConfigurationData för varje Node block-namn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en till gång för Automation-autentiseringsuppgifter.

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

Du kan kompilera föregående DSC-konfiguration med PowerShell. 

Följande PowerShell lägger till två nodkonfigurationer till hämtnings servern för Azure Automation tillstånds konfiguration: CredentialSample. MyVM1 och CredentialSample. MyVM2.

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

>[!NOTE]
>När kompileringen är klar kan ett fel meddelande visas: **modulen "Microsoft. PowerShell. Management" importerades inte eftersom snapin-modulen Microsoft. PowerShell. Management redan har importer ATS.** Du kan ignorera den här varningen.

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>Kompilera en DSC-konfiguration i Windows PowerShell

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Du kan köra processen från en arbets station för utvecklare eller i en build-tjänst, till exempel [Azure DevOps](https://dev.azure.com).
Du kan sedan importera MOF: ar för de resulterande Node-konfigurationerna direkt i Azure State Configuration-tjänsten. 

>[!NOTE]
>En nods konfigurations fil får inte vara större än 1 MB för att den ska kunna importeras till Azure Automation.

Du kan också importera nodkonfigurationer (MOF: ar) som har kompilerats utanför Azure. Det finns flera fördelar med den här metoden, inklusive prestanda och tillförlitlighet.

Kompilering i Windows PowerShell ger möjlighet att signera konfigurations innehåll, med DSC-agenten som verifierar en signerad nods konfiguration lokalt på en hanterad nod. Verifiering säkerställer att konfigurationen som används på noden kommer från en auktoriserad källa. Mer information om hur du signerar nodkonfigurationer finns [i förbättringar i WMF 5,1 – så här signerar du konfiguration och modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importera en Node-konfiguration i Azure Portal

1. Från ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **+ Lägg till**.
1. På sidan Importera klickar du på mappikonen bredvid text rutan för **nodens konfigurations fil** för att bläddra efter en nods konfigurations fil (MOF) på den lokala datorn.

   ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)

1. Ange ett namn i text rutan **konfigurations namn** . Det här namnet måste matcha namnet på den konfiguration från vilken nodkonfigurationer kompilerades.
1. Klicka på **OK**

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importera en nods konfiguration med Azure PowerShell

Du kan använda cmdleten [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) för att importera en konfiguration av en nod till ditt Automation-konto.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/az.automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md).
