---
title: Kompilera konfigurationer i Azure Automation DSC
description: Den här artikeln beskriver hur du kompilera Desired State Configuration (DSC) konfigurationer för Azure Automation.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76b2b1983cc5a6cedfcff204871e0b0f985fef95
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900792"
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Kompilera konfigurationer i Azure Automation DSC

Du kan sammanställa Desired State Configuration (DSC) konfigurationer på två sätt med Azure Automation: i Azure-portalen och med Windows PowerShell. Tabellen nedan hjälper dig att avgöra när du ska använda vilken metod baserat på egenskaperna för var och en:

### <a name="azure-portal"></a>Azure Portal

* Enklaste metoden med interaktivt användargränssnitt
* Formulär med enkla parametervärden
* Spåra jobbstatus
* Åtkomst som autentiseras med Azure-inloggningsförfrågningar

### <a name="windows-powershell"></a>Windows PowerShell

* Anropa från kommandoraden med Windows PowerShell-cmdlets
* Kan ingå i automatiserad lösning med flera steg
* Ange enkla och komplexa parametervärden
* Spåra jobbstatus
* Klienten som krävs för stöd av PowerShell-cmdletar
* Skicka ConfigurationData
* Kompilera konfigurationer som använder autentiseringsuppgifterna

När du har valt en metod för kompilering, Använd följande procedurer för att starta kompilering.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilera en DSC-konfiguration med Azure portal

1. Från ditt Automation-konto klickar du på **DSC-konfigurationer**.
2. Klicka på en konfiguration för att öppna tillhörande blad.
3. Klicka på **Kompilera**.
4. Om konfigurationen har inga parametrar, uppmanas att bekräfta om du vill använda den. Om konfigurationen har parametrar, den **kompilera konfigurationen** öppnas bladet så att du kan ange parametervärden. Se följande [ **grundläggande parametrar** ](#basic-parameters) avsnitt finns mer information om parametrar.
5. Den **Kompileringsjobbet** bladet öppnas så att du kan spåra kompilering jobbstatus och nodkonfigurationer (MOF configuration dokument) det orsakas ska kunna placeras på den Azure Automation DSC-Hämtningsserver.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilera en DSC-konfiguration med Windows PowerShell

Du kan använda [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) att starta kompilering med Windows PowerShell. Följande exempelkod startar kompileringen av en DSC-konfiguration som kallas **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` Returnerar ett jobbobjekt som du kan använda för att spåra dess status för kompilering. Du kan sedan använda detta jobbobjekt för kompilering med [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) att bestämma status för kompileringsjobbet, och [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) att visa dess strömmar (utdata). Följande exempelkod startar kompileringen av den **SampleConfig** konfiguration, väntar tills den har slutförts och visar sedan dess strömmar.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Grundläggande parametrar
Parameterdeklaration i DSC-konfigurationer, inklusive typer och egenskaper, fungerar på samma sätt som i Azure Automation-runbooks. Se [starta en runbook i Azure Automation](automation-starting-a-runbook.md) mer information om runbook-parametrar.

I följande exempel används två parametrar **FeatureName** och **IsPresent**, för att fastställa värden för egenskaper i den **ParametersExample.sample** nod konfiguration, som genereras under kompilering.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Du kan sammanställa DSC-konfigurationer som använder grundläggande parametrar i Azure Automation DSC-portalen eller med Azure PowerShell:

### <a name="portal"></a>Portalen

I portalen kan du ange parametervärden när du klickar på **Kompilera**.

![alternativ text](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell kräver parametrar i en [hash-tabell](http://technet.microsoft.com/library/hh847780.aspx) där nyckeln matchar parameternamnet och värdet är lika med värdet för parametern.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Information om skicka PSCredentials som parametrar finns i <a href="#credential-assets"> **Inloggningstillgångar** </a> nedan.

## <a name="composite-resources"></a>Sammansatta resurser

**Sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser inuti en konfiguration. Detta gör att du kan använda flera konfigurationer för en enskild resurs. Se [sammansatta resurser: använda en DSC-konfiguration som en resurs](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) mer information om **sammansatta resurser**

> [!NOTE]
> För att **sammansatta resurser** för att kompilera korrekt, måste du först kontrollera att alla DSC-resurser som sammansatt förlitar sig på installeras först i Azure Automation-konto moduler databasen eller den inte importera korrekt.

Att lägga till en DSC **sammansatta Resource**, måste du lägga till modulen resursen till ett arkiv (* .zip). Gå till lagringsplatsen moduler på ditt Azure Automation-konto. Klicka på knappen ”Lägg till en modul”.

![Lägg till modul](./media/automation-dsc-compile/add_module.png)

Gå till katalogen där arkivet finns. Välj arkivfilen och klicka på OK.

![Välj modul](./media/automation-dsc-compile/select_dscresource.png)

Du kommer tillbaka till katalogen moduler där du kan övervaka statusen för din **sammansatta Resource** medan den har packats upp och registreras med Azure Automation.

![Importera sammansatta resurs](./media/automation-dsc-compile/register_composite_resource.png)

När modulen har registrerats är du kan klicka på den för att kontrollera att den **sammansatta resurser** finns nu som ska användas i en konfiguration.

![Verifiera sammansatta resurser](./media/automation-dsc-compile/validate_composite_resource.png)

Du kan anropa den **sammansatta Resource** i din konfiguration t.ex:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** kan du avgränsa strukturella konfigurationen från miljöspecifika konfiguration när du använder PowerShell DSC. Se [att separera ”vad” från ”Where” i PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) mer information om **ConfigurationData**.

> [!NOTE]
> Du kan använda **ConfigurationData** vid kompilering i Azure Automation DSC med Azure PowerShell, men inte i Azure-portalen.

Följande DSC-exempelkonfiguration använder **ConfigurationData** via den **$ConfigurationData** och **$AllNodes** nyckelord. Du måste också den [ **xWebAdministration** modulen](https://www.powershellgallery.com/packages/xWebAdministration/) i det här exemplet:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Du kan sammanställa ovanstående DSC-konfiguration med PowerShell. Följande PowerShell lägger till två nodkonfigurationer i Azure Automation DSC-Hämtningsserver: **ConfigurationDataSample.MyVM1** och **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Tillgångar

Referenser för tillgången är desamma i Azure Automation DSC-konfigurationer och runbooks. Se följande för mer information:

* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Variabler](automation-variables.md)

### <a name="credential-assets"></a>Inloggningstillgångar

När DSC-konfigurationer i Azure Automation kan referera till inloggningstillgångar med **Get-AutomationPSCredential**, inloggningstillgångar kan också skickas via parametrar, om så önskas. Om en konfiguration som tar en parameter av **PSCredential** skriver, måste du skicka sträng namnet på en Azure Automation-autentiseringsuppgiftstillgång som den parametervärdet i stället för ett PSCredential-objekt. I bakgrunden är Azure Automation-autentiseringsuppgiftstillgång med det namnet hämtas och skickas till konfigurationen.

Att hålla autentiseringsuppgifter kräver säker nodkonfigurationer (MOF configuration dokument) kryptering av autentiseringsuppgifter i noden configuration MOF-filen. Men tala för närvarande du om för PowerShell DSC det är okej om autentiseringsuppgifter för att vara för utdata i klartext under noden configuration MOF-generering, eftersom PowerShell DSC inte vet att Azure Automation kommer att kryptera hela MOF-filen efter dess generering via en Kompileringsjobb.

Du kan se PowerShell DSC som det är okej om autentiseringsuppgifter för att vara för utdata i oformaterad text i den genererade nodkonfigurationen MOF: ar med [ **ConfigurationData**](#configurationdata). Överför du `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** för varje nod block-namn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en Automation-autentiseringsuppgiftstillgång.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Du kan sammanställa ovanstående DSC-konfiguration med PowerShell. Följande PowerShell lägger till två nodkonfigurationer i Azure Automation DSC-Hämtningsserver: **CredentialSample.MyVM1** och **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> När kompileringen är klar kan du få ett felmeddelande om: **'Microsoft.PowerShell.Management' modulen importerades inte eftersom snapin-modulen 'Microsoft.PowerShell.Management' har redan importerats.** Den här varningen kan ignoreras.

## <a name="importing-node-configurations"></a>Importera nodkonfigurationer

Du kan också importera nodkonfigurationer (MOF: ar) som du har skapat utanför Azure. En fördel med detta är att nodkonfigurationer kan signeras.
En signerad nodkonfiguration har verifierats lokalt på en hanterad nod av DSC-agenten säkerställer att konfigurationen att använda noden kommer från en auktoriserad källa.

> [!NOTE]
> Du kan använda import signerade konfigurationer i Azure Automation-konto, men Azure Automation stöder för närvarande inte kompilera signerade konfigurationer.

> [!NOTE]
> En konfigurationsfil för noden får inte vara större än 1 MB att den kan importeras till Azure Automation.

Du kan lära dig hur du registrerar nodkonfigurationer på https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importera en nodkonfiguration i Azure portal

1. Från ditt Automation-konto klickar du på **DSC-nodkonfigurationer** under **konfigurationshantering**.

    ![DSC-nodkonfigurationer](./media/automation-dsc-compile/node-config.png)
2. I den **DSC-nodkonfigurationer** bladet klickar du på **Lägg till en NodeConfiguration**.
3. I den **Import** bladet Klicka på mappikonen bredvid den **Nodkonfigurationsfil** textrutan och bläddrar till en nodkonfigurationsfil (MOF) på den lokala datorn.

    ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)
4. Ange ett namn i den **Konfigurationsnamnet** textrutan. Det här namnet måste matcha namnet på den konfiguration som nodkonfigurationen kompilerades.
5. Klicka på **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importera en nodkonfiguration med PowerShell

Du kan använda den [Import AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet för att importera en nodkonfiguration till ditt automation-konto.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



