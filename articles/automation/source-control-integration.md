---
title: Källkontrollintegrering i Azure Automation
description: I den här artikeln beskrivs källkontrollintegrering med GitHub i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132934"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

 Källkontrollintegrering i Azure Automation stöder synkronisering med en riktning från källkontrolldatabasen. Med källkontrollen kan du hålla dina runbooks i ditt Automation-konto uppdaterat med skript i din GitHub- eller Azure Repos-källkontrolldatabas. Den här funktionen gör det enkelt att marknadsföra kod som har testats i din utvecklingsmiljö till ditt automationskonto för produktion.
 
 Med källkontrollintegrering kan du enkelt samarbeta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med källkontrollen kan du till exempel synkronisera olika grenar i källkontrollen med dina utvecklings-, test- och produktionsautomatiseringskonton. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Typer av källkontroll

Azure Automation stöder tre typer av källkontroll:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Krav

* En källkontrolldatabas (GitHub eller Azure Repos)
* Ett [Kör som-konto](manage-runas-account.md)
* De [senaste Azure-modulerna](automation-update-azure-modules.md) i `Az.Accounts` ditt Automation-konto, `AzureRM.Profile`inklusive modulen (Az-modul motsvarande)

> [!NOTE]
> Källkontrollsynkroniseringsjobb körs under användarens Automation-konto och faktureras i samma takt som andra Automation-jobb.

## <a name="configuring-source-control"></a>Konfigurera källkontroll

I det här avsnittet beskrivs hur du konfigurerar källkontroll för ditt Automation-konto. Du kan använda antingen Azure-portalen eller PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurera källkontroll i Azure Portal

Använd den här proceduren för att konfigurera källkontroll med Azure-portalen.

1. I ditt Automation-konto väljer du **Källkontroll** och klickar på **Lägg till**.

    ![Välj källkontroll](./media/source-control-integration/select-source-control.png)

2. Välj **typ Av källkontroll**och klicka sedan på **Autentisera**. 

3. Ett webbläsarfönster öppnas och du uppmanas att logga in. Följ anvisningarna för att slutföra autentiseringen.

4. På sidan Sammanfattning av källkontroll använder du fälten för att fylla i de källkontrollegenskaper som definieras nedan. Klicka på **Spara** när du är klar. 

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Namn på källkontroll     | Ett eget namn för källkontrollen. Detta namn får bara innehålla bokstäver och siffror.        |
    |Typ av källkontroll     | Typ av källkontrollmekanism. De tillgängliga alternativen är:</br> * GitHub (GitHub)</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Lagringsplats     | Namn på databasen eller projektet. De första 200 databaserna hämtas. Om du vill söka efter en databas skriver du namnet i fältet och klickar **på Sök på GitHub**.|
    |Gren     | Gren som källfilerna ska hämtas från. Branchinriktning är inte tillgängligt för TFVC-källkontrolltypen.          |
    |Mappsökväg     | Mapp som innehåller runbooks som ska synkroniseras, till exempel **/Runbooks**. Endast runbooks i den angivna mappen synkroniseras. Rekursion stöds inte.        |
    |Automatisk synkronisering<sup>1</sup>     | Inställning som aktiverar eller inaktiverar automatisk synkronisering när ett åtagande görs i källkontrolldatabasen.        |
    |Publicera Runbook     | Inställning av På om runbooks publiceras automatiskt efter synkronisering från källkontrollen och Av på annat sätt.           |
    |Beskrivning     | Text som anger ytterligare information om källkontrollen.        |

    <sup>1</sup> Om du vill aktivera automatisk synkronisering när du konfigurerar källkontrollintegration med Azure Repos måste du vara projektadministratör.

   ![Sammanfattning av källkontroll](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Inloggningen för källkontrolldatabasen kan skilja sig från din inloggning för Azure-portalen. Kontrollera att du är inloggad med rätt konto för källkontrolldatabasen när du konfigurerar källkontrollen. Om det finns ett tvivel öppnar du en ny flik i webbläsaren, loggar ut från **dev.azure.com,** **visualstudio.com**eller **github.com**och försöker återansluta till källkontrollen.

### <a name="configure-source-control-in-powershell"></a>Konfigurera källkontroll i PowerShell

Du kan också använda PowerShell för att konfigurera källkontroll i Azure Automation. Om du vill använda PowerShell-cmdlets för den här åtgärden behöver du en personlig åtkomsttoken (PAT). Använd cmdleten [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) för att skapa källkontrollanslutningen. Denna cmdlet kräver en säker sträng för PAT. Mer information om hur du skapar en säker sträng finns i [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Följande underavsnitt illustrerar powershell-skapandet av källkontrollanslutningen för GitHub, Azure Repos (Git) och Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Skapa källkontrollanslutning för GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Skapa källkontrollanslutning för Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) använder en URL som kommer åt **dev.azure.com** i stället för **visualstudio.com**som används i tidigare format. Det äldre `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-formatet är inaktuellt men stöds fortfarande. Det nya formatet är att föredra.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Skapa källkontrollanslutning för Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) använder en URL som använder **dev.azure.com** i stället för **visualstudio.com**som används i tidigare format. Det äldre `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-formatet är inaktuellt men stöds fortfarande. Det nya formatet är att föredra.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>PAT-behörigheter (Personal Access Token)

Källkontroll kräver några minsta behörigheter för PATs. Följande underavsnitt innehåller de minsta behörigheter som krävs för GitHub och Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Lägsta PAT-behörigheter för GitHub

I följande tabell definieras de lägsta PAT-behörigheter som krävs för GitHub. Mer information om hur du skapar en PAT i GitHub finns i [Skapa en personlig åtkomsttoken för kommandoraden](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Omfång  |Beskrivning  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Åtkomststatus         |
|`repo_deployment`      | Åtkomstdistributionsstatus         |
|`public_repo`     | Få tillgång till offentliga databaser         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Skriv databaskrokar         |
|`read:repo_hook`|Läs databaskrokar|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minsta PAT-behörigheter för Azure Repos

Följande lista definierar de lägsta PAT-behörigheter som krävs för Azure Repos. Mer information om hur du skapar en PAT i Azure Repos finns i [Autentisera åtkomst med personliga åtkomsttoken .](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Omfång  |  Åtkomsttyp  |
|---------| ----------|
| `Code`      | Läsa  |
| `Project and team` | Läsa |
| `Identity` | Läsa     |
| `User profile` | Läsa     |
| `Work items` | Läsa    |
| `Service connections` | Läs, fråga, hantera<sup>1</sup>    |

<sup>1</sup> `Service connections` Behörigheten krävs endast om du har aktiverat autosync.

## <a name="synchronizing"></a>Synkronisera

Följ dessa steg för att synkronisera med källkontrollen. 

1. Välj källan i tabellen på kontrollsidan För källa. 

2. Klicka på **Starta synkronisering** för att starta synkroniseringsprocessen. 

3. Visa status för det aktuella synkroniseringsjobbet eller tidigare genom att klicka på fliken **Synkronisera jobb.** 

4. Välj en källkontrollmekanism på listrutan **Källkontroll.**

    ![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

5. Om du klickar på ett jobb kan du visa jobbutdata. Följande exempel är utdata från ett källkontrollsynkroniseringsjobb.

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. Ytterligare loggning är tillgänglig genom att välja **Alla loggar** på sidan Sammanfattning av källkontrollsynkroniseringsjobb. Dessa ytterligare loggposter kan hjälpa dig att felsöka problem som kan uppstå när du använder källkontroll.

## <a name="disconnecting-source-control"></a>Koppla från källkontroll

Så här kopplar du från en källkontrolldatabas:

1. Kontroll av öppen **källkod** under **Kontoinställningar** i ditt Automation-konto.

2. Välj den källkontrollmekanism som ska tas bort. 

3. Klicka på **Ta bort**på sidan Sammanfattning av källkontroll.

## <a name="handling-encoding-issues"></a>Hantera kodningsproblem

Om flera personer redigerar runbooks i källkontrolldatabasen med hjälp av olika redigerare kan kodningsproblem uppstå. Mer information om den här situationen finns i [Vanliga orsaker till kodningsproblem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Uppdatera PAT

För närvarande kan du inte använda Azure-portalen för att uppdatera PAT i källkontrollen. När din PAT har upphört att gälla eller återkallas kan du uppdatera källkontrollen med en ny åtkomsttoken på något av följande sätt:

* Använd [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Använd [cmdleten Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Nästa steg

Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation-runbooktyper](automation-runbook-types.md).
