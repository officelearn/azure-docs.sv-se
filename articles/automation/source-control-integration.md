---
title: Källkontrollsintegration i Azure Automation
description: Den här artikeln beskriver källkontrollsintegrering med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 49a28901e2ea471f97270c0407e2f6c0a4a533fd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58169161"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

Källkontroll kan du behålla dina runbooks i ditt Automation-konto är uppdaterade med skripten i centrallagret för källkontroll GitHub eller Azure DevOps. Källkontroll kan du enkelt kan samarbeta med ditt team, spåra ändringar och återställa tidigare versioner av dina runbooks. Till exempel kan källkontroll du synkronisera olika grenar i källkontrollen till ditt Automation-konton som utveckling, testning eller produktion. Detta gör det enkelt att flytta upp kod som har testats i din utvecklingsmiljö till din Automation-konto i produktionsmiljön.

Azure Automation har stöd för 3 typer av källkontroll:

* GitHub
* Azure DevOps (Git)
* Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Förutsättningar

* Ett källkontrollscentrallager (GitHub eller Azure DevOps)
* Rätt [behörigheter](#personal-access-token-permissions) till källkontroll
* En [Run-As konto och anslutning](manage-runas-account.md)

> [!NOTE]
> Synkronisering för källkontrolljobb körs under användare Automation-konto och debiteras enligt samma taxa som andra Automation-jobb.

## <a name="configure-source-control"></a>Konfigurera källkontroll

I ditt Automation-konto, Välj **källkontroll (förhandsversion)** och klicka på **+ Lägg till**

![Välj källkontroll](./media/source-control-integration/select-source-control.png)

Välj **källkontroll typ**, klickar du på **autentisera**.

Granska sidan programbehörigheter begäran och klicka på **acceptera**.

På den **källa kontroll sammanfattning** sidan, Fyll i informationen och klickar på **spara**. I följande tabell visas en beskrivning av tillgängliga fält.

|Egenskap   |Beskrivning  |
|---------|---------|
|Namn på datakälla kontroll     | Ett eget namn för källkontrollen        |
|Källkontrolltyp     | Typ av kontroll av källa. De tillgängliga alternativen är:</br> GitHub</br>Azure DevOps (Git)</br> Azure DevOps (TFVC)        |
|Lagringsplats     | Namnet på databasen eller projekt. Det här värdet hämtas från källkontroll. Exempel: $/ ContosoFinanceTFVCExample         |
|Branch     | Den gren som ska hämta källfiler från. Gren mål är inte tillgänglig för TFVC-källkontrollstypen.          |
|Mappsökväg     | Den mapp som innehåller runbooks för att synkronisera. Exempel: /Runbooks         |
|Automatisk synkronisering     | Aktiverar eller inaktivera automatisk synkronisering när ett genomförande görs i källkontroll         |
|Publicera Runbook     | Om inställd **på**, när runbooks har synkroniserats från källkontroll som de kommer att publiceras automatiskt.         |
|Beskrivning     | Ett textfält för att ge mer information        |

![Sammanfattning för kontroll av källa](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Kontrollera att du har loggat in med rätt konto när du konfigurerar källkontroll. Om det finns en osäkra, öppna en ny flik i webbläsaren och logga ut från visualstudio.com eller github.com och försök ansluta källkontroll igen.

## <a name="syncing"></a>Synkroniserar

Konfigurera automatisk synkronisering när du konfigurerar integrering av källkontroll, startar den första synkroniseringen automatiskt. Om automatisk synkronisering inte har angetts, väljer du den från tabellen på den **källkontroll (förhandsversion)** sidan. Klicka på **Starta synkronisering** att starta synkroniseringen.

Du kan visa statusen för den aktuella synkroniseringsjobb eller tidigare inställningarna genom att klicka på den **Synkronisera jobb** fliken. På den **källkontroll** listrutan, Välj en källkontroll.

![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

När du klickar på ett jobb kan du visa jobbutdata. I följande exempel är utdata från ett Synkroniseringsjobb för kontroll av källa.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
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

## <a name="personal-access-token-permissions"></a>Behörigheter för personlig åtkomst-token

Källkontroll kräver vissa minsta möjliga behörigheter för personliga åtkomsttoken. Följande tabeller innehåller de minsta behörigheter som krävs för GitHub och Azure DevOps.

### <a name="github"></a>GitHub

|Scope  |Beskrivning  |
|---------|---------|
|**lagringsplats**     |         |
|repo:status     | Åtkomststatus för genomförande         |
|repo_deployment      | Distributionsstatus för åtkomst         |
|public_repo     | Offentliga lagringsplatserna för åtkomst         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Skriva lagringsplats hookar         |
|read:repo_hook|Läs lagringsplats hookar|

### <a name="azure-devops"></a>Azure DevOps

|Scope  |
|---------|
|Kod (läsa)     |
|Projekt och team (läsa)|
|Identitet (läsa)      |
|Användarprofil (läsa)     |
|Arbetsobjekt (läsa)    |
|Tjänstanslutningar (läsa, fråga och hantera)<sup>1</sup>    |

<sup>1</sup>Tjänstanslutningar behörigheten är endast krävs om du har aktiverat automatisk synkronisering.

## <a name="disconnecting-source-control"></a>Kopplar från källkontrollen

Om du vill koppla från ett källkontrollscentrallager, öppna **källkontroll (förhandsversion)** under **kontoinställningar** i ditt Automation-konto.

Välj källkontroll som du vill ta bort. På den **källa kontroll sammanfattning** klickar du på **ta bort**.

## <a name="encoding"></a>Kodning

Om flera personer redigerar runbooks i centrallagret för källkontroll med olika redigerare finns en risk att köra i kodningsproblem. Detta kan infoga felaktiga tecken i din runbook. Mer information om detta finns [vanliga orsaker till kodningsproblem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Nästa steg

Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)

