---
title: Kompilera konfigurationer i Azure Automation tillstånds konfiguration
description: Den här artikeln beskriver hur du kompilerar Desired State Configuration (DSC)-konfigurationer för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: d7f22e5042f301d7c16573318b6ddd1585f1e350
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770007"
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

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portalen

1. Från ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)** .
1. Klicka på fliken **konfigurationer** och klicka sedan på konfigurations namnet som ska kompileras.
1. Klicka på **kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta om du vill kompilera den. Om konfigurationen har parametrar öppnas bladet **kompilera konfiguration** så att du kan ange parameter värden. Se följande [**grundläggande parameter**](#basic-parameters) avsnitt om du vill ha mer information om parametrar.
1. Sidan för att **kompilera jobb** öppnas så att du kan spåra jobbets status i kompileringen och Node-konfigurationerna (MOF-konfigurationsfilen) som det gjorde för att placeras på hämtnings servern för Azure Automation tillstånds konfiguration.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempel kod startar kompilering av en DSC-konfiguration med namnet **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` returnerar ett Compilation Job-objekt som du kan använda för att spåra dess status. Du kan sedan använda det här objektet för kompilering av jobb med [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob)
för att fastställa status för Compilation-jobbet och [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration)
för att visa dess strömmar (utdata). Följande exempel kod startar kompilering av **SampleConfig** -konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Grundläggande parametrar

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

#### <a name="portal"></a>Portalen

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

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Kompilera konfigurationer i Azure Automation som innehåller sammansatta resurser

Med **sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. På så sätt kan du tillämpa flera konfigurationer på en enskild resurs. Se [sammansatta resurser: använda en DSC-konfiguration som en resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite) för att lära dig mer om **sammansatta resurser**.

> [!NOTE]
> För att konfigurationer som innehåller **sammansatta resurser** ska kunna kompileras korrekt måste du först se till att alla DSC-resurser som den sammansatta förlitar sig på först importeras i till Azure Automation.

Att lägga till en DSC- **sammansatt resurs** skiljer sig inte från att lägga till någon PowerShell-modul för Azure Automation.
Steg för steg-instruktionen för den här processen beskrivs i artikeln [Hantera moduler i Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Hantera ConfigurationData vid kompilering av konfiguration i Azure Automation

Med **ConfigurationData** kan du separera strukturell konfiguration från valfri miljö bestämd konfiguration när du använder PowerShell DSC. Se [avgränsa "vad" från "där" i POWERSHELL DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) för att lära dig mer om **ConfigurationData**.

> [!NOTE]
> Du kan använda **ConfigurationData** när du kompilerar i Azure Automation tillstånds konfiguration med Azure PowerShell men inte i Azure Portal.

I följande exempel på DSC-konfiguration används **ConfigurationData** via **$ConfigurationData** och **$AllNodes** nyckelord. Du behöver också [ **xWebAdministration** -modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet:

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

Du kan kompilera föregående DSC-konfiguration med Windows PowerShell. Följande skript lägger till två nodkonfigurationer till hämtnings tjänsten för Azure Automation tillstånds konfiguration: **ConfigurationDataSample. MyVM1** och **ConfigurationDataSample. MyVM3**:

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

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Arbeta med till gångar i Azure Automation under kompilering

Till gångs referenser är desamma i Azure Automation tillstånds konfiguration och Runbooks. Mer information finns i:

- [Certifikat](automation-certificates.md)
- [Anslutningar](automation-connections.md)
- [Autentiseringsuppgifter](automation-credentials.md)
- [Variabler](automation-variables.md)

#### <a name="credential-assets"></a>Inloggnings till gångar

DSC-konfigurationer i Azure Automation kan referera till till gångar för automatisering av autentiseringsuppgifter med hjälp av `Get-AutomationPSCredential`-cmdleten. Om en konfiguration har en parameter som har en **PSCredential** -typ kan du använda den `Get-AutomationPSCredential`-cmdleten genom att skicka sträng namnet för en Azure Automation Credential till-cmdlet: en för att hämta autentiseringsuppgifterna. Du kan sedan använda objektet för den parameter som kräver **PSCredential** -objektet. I bakgrunden hämtas den Azure Automation autentiseringsuppgiften med det namnet och skickas till konfigurationen. I exemplet nedan visas detta i praktiken.

Att bevara autentiseringsuppgifterna säkra i nodkonfigurationer (MOF-konfigurationsfilen) kräver kryptering av autentiseringsuppgifterna i MOF-filen för nodens konfiguration. För närvarande måste du meddela PowerShell DSC att det går att ange autentiseringsuppgifter som oformaterad text under MOF-genereringen av nodens konfiguration, eftersom PowerShell DSC inte vet att Azure Automation kommer att kryptera hela MOF-filen efter att den har genererats via ett Compilation-jobb.

Du kan tala om för PowerShell DSC att det går att ange autentiseringsuppgifter som oformaterad text i den genererade nodens konfigurations MOF: ar med hjälp av konfigurations data. Du bör pass `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** för varje nodnamn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

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

Du kan kompilera föregående DSC-konfiguration med PowerShell. Följande PowerShell lägger till två nodkonfigurationer till hämtnings servern för Azure Automation tillstånds konfiguration: **CredentialSample. MyVM1** och **CredentialSample. MyVM2**.

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
> När kompileringen är klar kan du få ett fel meddelande om **att modulen Microsoft. PowerShell. Management inte har importer ATS eftersom snapin-modulen Microsoft. PowerShell. Management redan har importer ATS.** Den här varningen kan ignoreras.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Kompilera konfigurationer i Windows PowerShell och publicera till Azure Automation

Du kan också importera nodkonfigurationer (MOF: ar) som har kompilerats utanför Azure.
Detta inkluderar kompilering från en arbets station för utvecklare eller i en tjänst som [Azure DevOps](https://dev.azure.com).
Det finns flera fördelar med den här metoden, inklusive prestanda och tillförlitlighet.
Kompilering i Windows PowerShell ger också möjlighet att signera konfigurations innehåll.
En konfiguration för signerad nod verifieras lokalt på en hanterad nod av DSC-agenten, vilket säkerställer att konfigurationen som tillämpas på noden kommer från en auktoriserad källa.

> [!NOTE]
> En nods konfigurations fil får inte vara större än 1 MB för att den ska kunna importeras till Azure Automation.

Mer information om hur du signerar nodkonfigurationer finns [i förbättringar i WMF 5,1 – så här signerar du konfiguration och modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Kompilera en konfiguration i Windows PowerShell

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Detta kan utföras från en arbets station för utvecklare eller i en build-tjänst, till exempel [Azure DevOps](https://dev.azure.com).

MOF-filen eller filerna som skapas genom kompilering av konfigurationen kan sedan importeras direkt till Azure State Configuration-tjänsten.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importera en Node-konfiguration i Azure Portal

1. Från ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** och sedan på **+ Lägg till**.
1. På sidan **Importera** klickar du på mappikonen bredvid text rutan för **nodens konfigurations fil** för att bläddra efter en nods konfigurations fil (MOF) på den lokala datorn.

   ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)

1. Ange ett namn i text rutan **konfigurations namn** . Det här namnet måste matcha namnet på den konfiguration från vilken nodkonfigurationer kompilerades.
1. Klicka på **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importera en nods konfiguration med Azure PowerShell

Du kan använda cmdleten [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) för att importera en konfiguration av en nod till ditt Automation-konto.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/az.automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
