---
title: Källkontrollsintegration i Azure Automation
description: Den här artikeln beskriver källkontrollsintegrering med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c689a8fe35133456c476106e96336420640ebf66
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335988"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

Källkontroll kan du behålla dina runbooks i ditt Automation-konto är uppdaterade med skripten i centrallagret för källkontroll GitHub eller Azure-databaser. Källkontroll kan du enkelt kan samarbeta med ditt team, spåra ändringar och återställa tidigare versioner av dina runbooks. Till exempel kan källkontroll du synkronisera olika grenar i källkontrollen till ditt Automation-konton som utveckling, testning eller produktion. Detta gör det enkelt att flytta upp kod som har testats i din utvecklingsmiljö till din Automation-konto i produktionsmiljön. Källkontrollsintegrering med automation har stöd för en riktning som synkroniseras från centrallagret för källkontroll.

Azure Automation stöder tre typer av källkontroll:

* GitHub
* Azure-lagringsplatser (Git)
* Azure-lagringsplatser (TFVC)

## <a name="pre-requisites"></a>Förutsättningar

* Ett källkontrollscentrallager (GitHub eller Azure-databaser)
* En [kör som-konto](manage-runas-account.md)
* Kontrollera att du har den [senaste Azure-modulerna](automation-update-azure-modules.md) i ditt Automation-konto

> [!NOTE]
> Synkronisering för källkontrolljobb körs under användare Automation-konto och debiteras enligt samma taxa som andra Automation-jobb.

## <a name="configure-source-control---azure-portal"></a>Konfigurera källkontroll - Azure-portalen

I ditt Automation-konto, Välj **källkontroll** och klicka på **+ Lägg till**

![Välj källkontroll](./media/source-control-integration/select-source-control.png)

Välj **källkontroll typ**, klickar du på **autentisera**. Ett webbläsarfönster öppnas och du uppmanas att logga in, följ anvisningarna för att slutföra autentisering.

På den **källa kontroll sammanfattning** sidan, Fyll i informationen och klickar på **spara**. I följande tabell visas en beskrivning av tillgängliga fält.

|Egenskap   |Beskrivning  |
|---------|---------|
|Namn på datakälla kontroll     | Ett eget namn för källkontrollen        |
|Källkontrolltyp     | Typ av kontroll av källa. De tillgängliga alternativen är:</br> GitHub</br>Azure-lagringsplatser (Git)</br> Azure-lagringsplatser (TFVC)        |
|Lagringsplats     | Namnet på databasen eller projekt. De första 200 lagringsplatserna returneras. För att söka efter en lagringsplats, skriver du namnet i fältet och klickar på **Sök på GitHub**.|
|Branch     | Den gren som ska hämta källfiler från. Gren mål är inte tillgängligt för TFVC-källkontrollstypen.          |
|Mappsökväg     | Den mapp som innehåller runbooks för att synkronisera. Exempel: /Runbooks </br>*Endast runbooks i mappen som specificerats synkroniseras. Rekursion stöds inte.*        |
|Automatisk synkronisering     | Aktiverar eller inaktivera automatisk synkronisering när ett genomförande görs i källkontroll         |
|Publicera Runbook     | Om inställd **på**, när runbooks har synkroniserats från källkontroll som de kommer att publiceras automatiskt.         |
|Beskrivning     | Ett textfält för att ge mer information        |

![Sammanfattning för kontroll av källa](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Kontrollera att du har loggat in med rätt konto när du konfigurerar källkontroll. Om det finns en osäkra, öppna en ny flik i webbläsaren och logga ut från visualstudio.com eller github.com och försök ansluta källkontroll igen.

## <a name="configure-source-control---powershell"></a>Konfigurera källkontroll – PowerShell

Du kan också använda PowerShell för att konfigurera källkontroll i Azure Automation. Om du vill konfigurera källkontroll med PowerShell-cmdletar, krävs en personlig åtkomsttoken (PAT). Du använder den [New AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) att skapa källkontrollanslutning. Cmdlet: en kräver en säker sträng för din personliga åtkomsttoken, att lära dig hur du skapar en säker sträng finns [ConvertTo SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure-lagringsplatser (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<account>.visualstudio.com/DefaultCollection/<project>/_git/<repository> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure-lagringsplatser (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<account>.visualstudio.com/<projectName>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<account>/<repoName>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Behörigheter för personlig åtkomst-token

Källkontroll kräver vissa minsta möjliga behörigheter för personliga åtkomsttoken. Följande tabeller innehåller de minsta behörigheter som krävs för GitHub och Azure-databaser.

#### <a name="github"></a>GitHub

Mer information om hur du skapar en personlig åtkomsttoken i GitHub finns [skapar en personlig åtkomsttoken för kommandoraden](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Scope  |Beskrivning  |
|---------|---------|
|**lagringsplats**     |         |
|repo:status     | Åtkomststatus för genomförande         |
|repo_deployment      | Distributionsstatus för åtkomst         |
|public_repo     | Offentliga lagringsplatserna för åtkomst         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Skriva lagringsplats hookar         |
|read:repo_hook|Läs lagringsplats hookar|

#### <a name="azure-repos"></a>Azure Repos

Mer information om hur du skapar en personlig åtkomsttoken i Azure-lagringsplatser finns [autentisera åtkomsten med personliga åtkomsttoken](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Scope  |
|---------|
|Kod (läsa)     |
|Projekt och team (läsa)|
|Identitet (läsa)      |
|Användarprofil (läsa)     |
|Arbetsobjekt (läsa)    |
|Tjänstanslutningar (läsa, fråga och hantera)<sup>1</sup>    |

<sup>1</sup> the Tjänstanslutningar behörigheten är endast krävs om du har aktiverat automatisk synkronisering.

## <a name="syncing"></a>Synkroniserar

Välj källa från tabellen på den **källkontroll** sidan. Klicka på **Starta synkronisering** att starta synkroniseringen.

Du kan visa statusen för den aktuella synkroniseringsjobb eller tidigare inställningarna genom att klicka på den **Synkronisera jobb** fliken. På den **källkontroll** listrutan, Välj en källkontroll.

![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

När du klickar på ett jobb kan du visa jobbutdata. I följande exempel är utdata från ett Synkroniseringsjobb för kontroll av källa.

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

Ytterligare loggning är tillgänglig genom att välja **alla loggar** på den **källa Control Sync jobbsammanfattning** sidan. Dessa ytterligare poster kan hjälpa dig att felsöka problem som kan uppstå när du använder källkontroll.

## <a name="disconnecting-source-control"></a>Kopplar från källkontrollen

Om du vill koppla från ett källkontrollscentrallager, öppna **källkontroll** under **kontoinställningar** i ditt Automation-konto.

Välj källkontroll som du vill ta bort. På den **källa kontroll sammanfattning** klickar du på **ta bort**.

## <a name="encoding"></a>Kodning

Om flera personer redigerar runbooks i centrallagret för källkontroll med olika redigerare, finns en risk att köra i kodningsproblem. Den här situationen kan leda till felaktiga tecken i din runbook. Mer information om detta finns [vanliga orsaker till kodningsproblem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Nästa steg

Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
