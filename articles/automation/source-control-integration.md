---
title: Käll kontrolls integrering i Azure Automation
description: I den här artikeln beskrivs integrering av käll kontroll med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996518"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

Med käll kontroll kan du hålla dina Runbooks i ditt Automation-konto uppdaterat med dina skript i din GitHub-eller Azure databaser-käll kontroll lagrings plats. Med käll kontroll kan du enkelt samar beta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med käll kontroll kan du till exempel synkronisera olika grenar i käll kontrollen med dina utvecklings-, test-eller produktions Automation-konton. Detta gör det enkelt att befordra kod som har testats i utvecklings miljön till ditt produktions Automation-konto. Käll kontroll integrering med Automation stöder synkronisering med en riktning från din lagrings plats för käll kontroll.

Azure Automation stöder tre typer av käll kontroll:

* GitHub
* Azure-databaser (git)
* Azure-databaser (TFVC)

## <a name="pre-requisites"></a>Förutsättningar

* Ett lagrings lager för käll kontroll (GitHub eller Azure databaser)
* Ett [Kör som-konto](manage-runas-account.md)
* Se till att du har de [senaste Azure-modulerna](automation-update-azure-modules.md) i ditt Automation-konto, inklusive modulen **AzureRM. Profile** . 

> [!NOTE]
> Synkroniseringsjobb för käll kontroll körs under användarnas Automation-konto och debiteras enligt samma taxa som andra Automation-jobb.

## <a name="configure-source-control---azure-portal"></a>Konfigurera käll kontroll – Azure Portal

I ditt Automation-konto väljer du **käll kontroll** och klickar på **+ Lägg till**

![Välj käll kontroll](./media/source-control-integration/select-source-control.png)

Välj **typ av käll kontroll**, klicka på **autentisera**. Ett webbläsarfönster öppnas och du blir ombedd att logga in. Följ anvisningarna för att slutföra autentiseringen.

Fyll i informationen på sidan **Översikt över käll kontroll** och klicka på **Spara**. I följande tabell visas en beskrivning av de tillgängliga fälten.

|Egenskap  |Beskrivning  |
|---------|---------|
|Namn på käll kontroll     | Ett eget namn för käll kontrollen. *Namnet får bara innehålla bokstäver och siffror.*        |
|Typ av käll kontroll     | Käll kontroll källans typ. De tillgängliga alternativen är:</br> GitHub</br>Azure-databaser (git)</br> Azure-databaser (TFVC)        |
|Lagringsplats     | Namnet på databasen eller projektet. De första 200-databaserna returneras. Om du vill söka efter en lagrings plats skriver du namnet i fältet och klickar på **Sök på GitHub**.|
|Branch     | Grenen att hämta källfilerna från. Förgrenings inriktning är inte tillgängligt för käll kontroll typen TFVC.          |
|Mappsökväg     | Den mapp som innehåller Runbooks som ska synkroniseras. Exempel:/Runbooks </br>*Endast Runbooks i den angivna mappen synkroniseras. Rekursion stöds inte.*        |
|Automatisk synkronisering<sup>1</sup>     | Aktiverar eller inaktiverar automatisk synkronisering när ett genomförande görs i käll kontrollens lagrings plats         |
|Publicera Runbook     | Om värdet är **på på**, efter att Runbooks har synkroniserats från käll kontroll, publiceras de automatiskt.         |
|Beskrivning     | Ett textfält som innehåller ytterligare information        |

<sup>1</sup> om du vill aktivera automatisk synkronisering när du konfigurerar käll kontrolls integrering med Azure databaser måste du vara projekt administratör.

![Översikt över käll kontroll](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Din inloggning för käll kontrollens lagrings plats kan skilja sig från din inloggning för Azure Portal. Se till att du är inloggad med rätt konto för käll kontrollens lagrings plats när du konfigurerar käll kontroll. Om det finns en tvivel öppnar du en ny flik i webbläsaren och loggar ut från visualstudio.com eller github.com och försöker ansluta käll kontrollen igen.

## <a name="configure-source-control---powershell"></a>Konfigurera käll kontroll – PowerShell

Du kan också använda PowerShell för att konfigurera käll kontroll i Azure Automation. Om du vill konfigurera käll kontroll med PowerShell-cmdletar behövs en personlig åtkomsttoken (PAT). Du använder [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) för att skapa käll kontroll anslutningen. För att lära dig hur du skapar en säker sträng, se [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)krävs en säker sträng med en personlig åtkomsttoken.

### <a name="azure-repos-git"></a>Azure-databaser (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure-databaser (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Behörigheter för personlig åtkomst-token

Käll kontrollen kräver vissa minimi behörigheter för personliga åtkomsttoken. Följande tabeller innehåller de lägsta behörigheter som krävs för GitHub och Azure databaser.

#### <a name="github"></a>GitHub

Mer information om hur du skapar en personlig åtkomsttoken i GitHub finns i [skapa en personlig åtkomsttoken för kommando raden](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Omfång  |Beskrivning  |
|---------|---------|
|**lagringsplats**     |         |
|lagrings platsen: status     | Status för åtkomst genomförande         |
|repo_deployment      | Åtkomst distributions status         |
|public_repo     | Åtkomst till offentliga databaser         |
|**admin:repo_hook**     |         |
|Skriv: repo_hook     | Skriv databas-hookar         |
|Läs: repo_hook|Läsa databas-hookar|

#### <a name="azure-repos"></a>Azure-lagringsplatser

Mer information om hur du skapar en personlig åtkomsttoken i Azure databaser finns i [autentisera åtkomst med personliga](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)åtkomsttoken.

|Omfång  |
|---------|
|Kod (läsa)     |
|Projekt och team (läsa)|
|Identitet (läsa)      |
|Användar profil (läsa)     |
|Arbets objekt (läsa)    |
|Tjänst anslutningar (läsa, fråga och hantera)<sup>1</sup>    |

<sup>1</sup> behörigheten tjänst anslutningar krävs endast om du har aktiverat automatisk synkronisering.

## <a name="syncing"></a>Synkning

Välj källan från tabellen på **käll kontroll** sidan. Starta synkroniseringsprocessen genom att klicka på **Starta synkronisering** .

Du kan visa statusen för det aktuella synkroniseringsschemat eller föregående genom att klicka på fliken **synkroniseringsjobb** . I list rutan **käll kontroll** väljer du en käll kontroll.

![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

Genom att klicka på ett jobb kan du Visa jobbets utdata. I följande exempel visas utdata från ett synkroniseringsjobb för käll kontroll.

```output
========================================================================================================

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



========================================================================================================
```

Ytterligare loggning är tillgängligt genom att välja **alla loggar** på sidan **Översikt över synkronisering av käll kontroll** . Dessa ytterligare logg poster kan hjälpa dig att felsöka problem som kan uppstå när du använder käll kontroll.

## <a name="disconnecting-source-control"></a>Kopplar från käll kontrollen

Om du vill koppla bort från en lagrings plats för käll kontroll, öppnar du **käll kontroll** under **konto inställningar** i ditt Automation-konto.

Välj den käll kontroll som du vill ta bort. Klicka på **ta bort**på sidan **Översikt över käll kontroll** .

## <a name="encoding"></a>Encoding

Om flera personer redigerar Runbooks i lagrings platsen för käll kontroll med olika redigerare, finns det en chans att kunna köra kodnings problem. Den här situationen kan leda till felaktiga tecken i din Runbook. Mer information om detta finns i [vanliga orsaker till kodnings problem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Uppdaterar åtkomsttoken

Det finns för närvarande inget sätt att uppdatera åtkomsttoken i käll kontrollen från portalen. När din personliga åtkomsttoken har upphört att gälla eller återkallats, kan du uppdatera käll kontrollen med en ny åtkomsttoken på följande sätt:

* Via [REST-API: et](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Med hjälp av cmdleten [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Nästa steg

Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
