---
title: Källkontrollintegrering i Azure Automation
description: I den här artikeln beskrivs integrering av käll kontroll med GitHub i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253733"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

 Käll kontroll integrering i Azure Automation stöder synkronisering med en riktning från din lagrings plats för käll kontroll. Med käll kontroll kan du hålla dina Runbooks i ditt Automation-konto uppdaterat med skript i din GitHub-eller Azure databaser-käll kontroll lagrings plats. Den här funktionen gör det enkelt att befordra kod som har testats i utvecklings miljön till ditt produktions Automation-konto.
 
 Med hjälp av käll kontroll integrering kan du enkelt samar beta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med käll kontroll kan du till exempel synkronisera olika grenar i käll kontrollen med dina Automation-konton för utveckling, testning och produktion. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Käll kontroll typer

Azure Automation stöder tre typer av käll kontroll:

* GitHub
* Azure-databaser (git)
* Azure-databaser (TFVC)

## <a name="prerequisites"></a>Förutsättningar

* Ett lagrings lager för käll kontroll (GitHub eller Azure databaser)
* Ett [Kör som-konto](manage-runas-account.md)
* De [senaste Azure-modulerna](automation-update-azure-modules.md) i ditt Automation-konto, inklusive modulen **AZ. Accounts** (AZ-modul motsvarande AzureRM. Profile)

> [!NOTE]
> Jobb för synkronisering av käll kontroll körs under användarens Automation-konto och debiteras enligt samma taxa som andra Automation-jobb.

## <a name="configuring-source-control"></a>Konfigurera käll kontroll

Det här avsnittet beskriver hur du konfigurerar käll kontroll för ditt Automation-konto. Du kan använda antingen Azure Portal eller PowerShell.

### <a name="configure-source-control----azure-portal"></a>Konfigurera käll kontroll--Azure Portal

Använd den här proceduren för att konfigurera käll kontroll med Azure Portal.

1. I ditt Automation-konto väljer du **käll kontroll** och klickar på **+ Lägg till**.

    ![Välj käll kontroll](./media/source-control-integration/select-source-control.png)

2. Välj **typ av käll kontroll**och klicka sedan på **autentisera**. 

3. Ett webbläsarfönster öppnas och du blir ombedd att logga in. Följ anvisningarna för att slutföra autentiseringen.

4. På sidan **käll kontroll sammanfattning** använder du fälten för att fylla i egenskaperna för käll kontroll som definierats nedan. Klicka på **Spara** när du är färdig. 

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Namn på käll kontroll     | Ett eget namn för käll kontrollen. Namnet får bara innehålla bokstäver och siffror.        |
    |Typ av käll kontroll     | Typ av käll kontroll mekanism. De tillgängliga alternativen är:</br> GitHub</br>Azure-databaser (git)</br> Azure-databaser (TFVC)        |
    |Databas     | Namn på databasen eller projektet. De första 200-databaserna hämtas. Om du vill söka efter en lagrings plats skriver du namnet i fältet och klickar på **Sök på GitHub**.|
    |Ligger     | Gren att hämta källfilerna från. Förgrenings inriktning är inte tillgängligt för käll kontroll typen TFVC.          |
    |Mappsökväg     | Mapp som innehåller Runbooks som ska synkroniseras, till exempel/Runbooks. Endast Runbooks i den angivna mappen synkroniseras. Rekursion stöds inte.        |
    |Automatisk synkronisering<sup>1</sup>     | Inställning som aktiverar eller inaktiverar automatisk synkronisering när ett genomförande görs i käll kontrollens lagrings plats.        |
    |Publicera Runbook     | Inställning av på om Runbooks publiceras automatiskt efter synkronisering från käll kontroll och av annat sätt.           |
    |Beskrivning     | Text som anger ytterligare information om käll kontrollen.        |

    <sup>1</sup> om du vill aktivera automatisk synkronisering när du konfigurerar käll kontrolls integrering med Azure databaser måste du vara projekt administratör.

   ![Översikt över käll kontroll](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Din inloggning för käll kontrollens lagrings plats kan skilja sig från din inloggning för Azure Portal. Se till att du är inloggad med rätt konto för käll kontrollens lagrings plats när du konfigurerar käll kontroll. Om det finns en tvivel öppnar du en ny flik i webbläsaren, loggar ut från visualstudio.com eller github.com och försöker ansluta till käll kontrollen igen.

### <a name="configure-source-control----powershell"></a>Konfigurera käll kontroll – PowerShell

Du kan också använda PowerShell för att konfigurera käll kontroll i Azure Automation. Om du vill använda PowerShell-cmdletar för den här åtgärden behöver du en personlig åtkomsttoken (PAT). Använd cmdleten [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) för att skapa käll kontroll anslutningen. Denna cmdlet kräver en säker sträng för PAT. Information om hur du skapar en säker sträng finns i [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Följande underavsnitt illustrerar PowerShell-skapande av käll kontroll anslutningen för GitHub, Azure databaser (git) och Azure databaser (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Skapa käll kontroll anslutning för GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Skapa käll kontroll anslutning för Azure databaser (git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Skapa käll kontroll anslutning för Azure databaser (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>PAT-behörigheter (personal Access token)

Käll kontrollen kräver vissa minimi behörigheter för PATs. Följande underavsnitt innehåller de lägsta behörigheter som krävs för GitHub och Azure databaser.

##### <a name="minimum-pat-permissions-for-github"></a>Lägsta PAT-behörigheter för GitHub

I följande tabell definieras de lägsta PAT-behörigheter som krävs för GitHub. Mer information om hur du skapar en PAT i GitHub finns i [skapa en personlig åtkomsttoken för kommando raden](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Omfång  |Beskrivning  |
|---------|---------|
|**lagringsplats**     |         |
|lagrings platsen: status     | Status för åtkomst genomförande         |
|repo_deployment      | Åtkomst distributions status         |
|public_repo     | Åtkomst till offentliga databaser         |
|**administratör: repo_hook**     |         |
|Skriv: repo_hook     | Skriv databas-hookar         |
|Läs: repo_hook|Läsa databas-hookar|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Lägsta PAT-behörigheter för Azure databaser

I följande lista definieras de lägsta PAT-behörigheter som krävs för Azure databaser. Mer information om hur du skapar en PAT i Azure databaser finns i [autentisera åtkomst med personliga](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)åtkomsttoken.

| Omfång  |  Åtkomst typ  |
|---------| ----------|
| Kod      | Läsa  |
| Projekt och team | Läsa |
| Identitet | Läsa     |
| Användarprofil | Läsa     |
| Arbets objekt | Läsa    |
| Tjänst anslutningar | Läsa, fråga, hantera<sup>1</sup>    |

<sup>1</sup> behörigheten tjänst anslutningar krävs endast om du har aktiverat automatisk synkronisering.

## <a name="synchronizing"></a>Synkronisera

Gör följande för att synkronisera med käll kontroll. 

1. Välj källan från tabellen på **käll kontroll** sidan. 

2. Starta synkroniseringsprocessen genom att klicka på **Starta synkronisering** . 

3. Visa status för det aktuella synkroniseringsjobb eller föregående genom att klicka på fliken **synkroniseringsjobb** . 

4. Välj en mekanism för käll kontroll i list Rute menyn för **käll kontroll** .

    ![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

5. Genom att klicka på ett jobb kan du Visa jobbets utdata. I följande exempel visas utdata från ett synkroniseringsjobb för käll kontroll.

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

     =========================================================================

    ```

6. Ytterligare loggning är tillgängligt genom att välja **alla loggar** på sidan **Översikt över synkronisering av käll kontroll** . Dessa ytterligare logg poster kan hjälpa dig att felsöka problem som kan uppstå när du använder käll kontroll.

## <a name="disconnecting-source-control"></a>Kopplar från käll kontrollen

Så här kopplar du bort från en lagrings plats för käll kontroll:

1. Kontroll med öppen **källkod** under **konto inställningar** i ditt Automation-konto.

2. Välj den metod för käll kontroll som ska tas bort. 

3. Klicka på **ta bort**på sidan **Översikt över käll kontroll** .

## <a name="handling-encoding-issues"></a>Hantera kodnings problem

Om flera personer redigerar Runbooks i lagrings platsen för käll kontroll med olika redigerare kan kodnings problem uppstå. Läs mer om den här situationen i [vanliga orsaker till kodnings problem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Uppdaterar PAT

För närvarande finns det inget sätt att använda Azure Portal för att uppdatera PAT i käll kontrollen. När din PAT har gått ut eller återkallats kan du uppdatera käll kontrollen med en ny åtkomsttoken på något av följande sätt:

* Använd [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Använd cmdleten [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Nästa steg

Mer information om Runbook-typer och deras fördelar och begränsningar finns i [Azure Automation Runbook-typer](automation-runbook-types.md).
