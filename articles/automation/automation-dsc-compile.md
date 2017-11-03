---
title: Kompilera konfigurationer i Azure Automation DSC | Microsoft Docs
description: "Den här artikeln beskriver hur du kompilera önskad tillstånd Configuration DSC ()-konfigurationer för Azure Automation."
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 02/07/2017
ms.author: magoedte; eslesar
ms.openlocfilehash: 1aadd604e676659475f00760af3b0bdfb13a4792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Kompilera konfigurationer i Azure Automation DSC

Du kan sammanställa önskad tillstånd Configuration (DSC) konfigurationer på två sätt med Azure Automation: i Azure-portalen och med Windows PowerShell. Tabellen nedan hjälper dig att avgöra när du ska använda vilken metod på grund av egenskaper för varje:

### <a name="azure-portal"></a>Azure Portal

* Enklaste metoden med interaktivt användargränssnitt
* Formulär med enkla parametervärden
* Spåra jobbets status
* Autentisera med Azure inloggning åtkomst

### <a name="windows-powershell"></a>Windows PowerShell

* Anropa från kommandoraden med Windows PowerShell-cmdlets
* Kan ingå i automatisk lösning med flera steg
* Ange enkla och komplexa parametervärden
* Spåra jobbets status
* Klienten som krävs för stöd av PowerShell-cmdlets
* Skicka ConfigurationData
* Kompilera konfigurationer som använder autentiseringsuppgifterna

När du har valt en metod för kompilering, kan du följa respektive procedurerna nedan för att starta kompilering.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilering av DSC-konfigurationen med Azure-portalen

1. Från ditt Automation-konto klickar du på **DSC-konfigurationer**.
2. Klicka på en konfiguration för att öppna dess bladet.
3. Klicka på **Kompilera**.
4. Om konfigurationen har inga parametrar uppmanas du att bekräfta om du vill använda den. Om konfigurationen har parametrar av **kompilera konfiguration** öppnas bladet så att du kan ange parametervärden. Finns det [ **grundläggande parametrar** ](#basic-parameters) avsnittet nedan för mer information om parametrar.
5. Den **Kompileringsjobbet** öppnas bladet så att du kan spåra kompilering jobbstatus och nodkonfigurationer (MOF configuration dokument) det orsakade placeras på Azure Automation DSC Pull-servern.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilering av DSC-konfigurationen med Windows PowerShell

Du kan använda [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) starta kompilerades med Windows PowerShell. Följande exempelkod startar kompilering av en DSC-konfiguration som kallas **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob`Returnerar ett jobbobjekt som du kan använda för att spåra dess status för kompilering. Du kan sedan använda detta jobbobjekt för kompilering med [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) att avgöra status för kompileringsjobbet och [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) att visa dess dataströmmar (utdata). Följande exempelkod startar sammanställning av den **SampleConfig** konfiguration, väntar tills den har slutförts och visar sedan dess dataströmmar.

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
Parameterdeklaration i DSC-konfigurationer, inklusive parametertyper och egenskaper, fungerar på samma sätt som i Azure Automation-runbooks. Se [starta en runbook i Azure Automation](automation-starting-a-runbook.md) lära dig mer om runbook-parametrar.

I följande exempel används två parametrar **FeatureName** och **IsPresent**, för att fastställa värden för egenskaper i den **ParametersExample.sample** nodkonfiguration genereras under kompilering.

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

I portalen, kan du ange parametervärden när du klickar på **Kompilera**.

![alternativ text](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell kräver parametrar i en [hashtable](http://technet.microsoft.com/library/hh847780.aspx) där nyckeln matchar parameternamnet och värdet är lika med parametervärdet.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Information om skicka PSCredentials som parametrar finns <a href="#credential-assets"> **Inloggningstillgångar** </a> nedan.

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** kan du avgränsa strukturella konfigurationen från miljön specifik konfiguration när du använder PowerShell DSC. Se [avgränsa ”vad” från ”där” i PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) att lära dig mer om **ConfigurationData**.

> [!NOTE]
> Du kan använda **ConfigurationData** vid kompilering i Azure Automation DSC med hjälp av Azure PowerShell, men inte i Azure-portalen.

Följande DSC-exempelkonfiguration använder **ConfigurationData** via den **$ConfigurationData** och **$AllNodes** nyckelord. Du måste också den [ **xWebAdministration** modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet:

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

Du kan sammanställa DSC-konfigurationen ovan med PowerShell. Den nedan PowerShell lägger till två nodkonfigurationer i Azure Automation DSC Pull-Server: **ConfigurationDataSample.MyVM1** och **ConfigurationDataSample.MyVM3**:

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

Referenser för tillgången är samma i Azure Automation DSC-konfigurationer och runbooks. Läs följande avsnitt för mer information:

* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Variabler](automation-variables.md)

### <a name="credential-assets"></a>Inloggningstillgångar

När DSC-konfigurationer i Azure Automation kan referera till inloggningstillgångar med **Get-AzureRmAutomationCredential**, inloggningstillgångar kan också överföras via parametrar, om så önskas. Om en konfiguration tar en parameter av **PSCredential** skriver, måste du skicka sträng namnet på en Azure Automation-autentiseringsuppgiftstillgång som att parametervärdet, i stället för ett PSCredential-objekt. Azure Automation-autentiseringsuppgiftstillgång med detta namn kommer hämtas och skickades till konfigurationen i bakgrunden.

Att hålla autentiseringsuppgifter kräver säker nodkonfigurationer (MOF configuration dokument) kryptering av autentiseringsuppgifter i noden configuration MOF-filen. Azure Automation tar ytterligare ett steg och krypterar hela MOF-filen. Men måste för närvarande du ange PowerShell DSC går bra autentiseringsuppgifter mängden i klartext under noden configuration MOF generation, eftersom PowerShell DSC inte vet att Azure Automation kryptera hela MOF-filen efter dess generation via en kompileringsjobbet.

Du kan se PowerShell DSC som går bra autentiseringsuppgifter mängden i oformaterad text i den genererade nodkonfigurationen MOF-filer med hjälp av [ **ConfigurationData**](#configurationdata). Överför du `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** för varje nod block-namnet som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en Automation-autentiseringsuppgiftstillgång.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -AutomationAccountName "AutomationAcct" -Name "SomeCredentialAsset"

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

Du kan sammanställa DSC-konfigurationen ovan med PowerShell. Den nedan PowerShell lägger till två nodkonfigurationer i Azure Automation DSC Pull-Server: **CredentialSample.MyVM1** och **CredentialSample.MyVM2**.

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

## <a name="importing-node-configurations"></a>Importera nodkonfigurationer

Du kan också importera noden configuratons (MOF-filer) som du har kompilerats utanför Azure. En fördel med detta är att noden confiturations kan signeras.
En signerad nodkonfiguration verifieras lokalt på en hanterad nod av DSC-agenten säkerställer att konfigurationen tillämpas på noden kommer från en auktoriserad källa.

> [!NOTE]
> Du kan använda import signerat konfigurationer i Azure Automation-konto, men Azure Automation stöder för närvarande inte kompilera signerade konfigurationer.

> [!NOTE]
> En konfigurationsfil för noden får inte vara större än 1 MB så att det kan importeras till Azure Automation.

Du kan lära dig logga nodkonfigurationer på https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importera en konfiguration av noden i Azure-portalen

1. Från ditt Automation-konto klickar du på **DSC-nodkonfigurationer**.

    ![DSC-nodkonfigurationer](./media/automation-dsc-compile/node-config.png)
2. I den **DSC-nodkonfigurationer** bladet, klickar du på **lägga till en NodeConfiguration**.
3. I den **importera** bladet klickar du på mappikonen bredvid den **nod konfigurationsfilen** textruta och bläddrar till en nod konfigurationsfil (MOF) på den lokala datorn.

    ![Sök efter lokal fil](./media/automation-dsc-compile/import-browse.png)
4. Ange ett namn i den **Konfigurationsnamnet** textruta. Det här namnet måste matcha namnet på den konfiguration som nodkonfigurationen kompilerades.
5. Klicka på **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importera en konfiguration av noden med PowerShell

Du kan använda den [importera AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) för att importera en nodkonfiguration till ditt automation-konto.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



