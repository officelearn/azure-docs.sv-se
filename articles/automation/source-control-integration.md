---
title: Källkontrollsintegration i Azure Automation
description: Den här artikeln beskriver källkontrollsintegrering med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/26/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bbf3582da2664b6e6429677d47aad4d69a7c1bb
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785332"
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation

Källkontroll kan du behålla dina runbooks i ditt Automation-konto är uppdaterade med skripten i centrallagret för källkontroll GitHub eller Azure Dev Ops. Källkontroll kan du enkelt kan samarbeta med ditt team, spåra ändringar och återställa tidigare versioner av dina runbooks. Till exempel kan källkontroll du synkronisera olika grenar i källkontrollen till ditt Automation-konton som utveckling, testning eller produktion. Detta gör det enkelt att flytta upp kod som har testats i din utvecklingsmiljö till din Automation-konto i produktionsmiljön.

Azure Automation har stöd för 3 typer av källkontroll:

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>Förutsättningar

* Ett källkontrollscentrallager (GitHub eller Visual Studio Team Services)
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
|Källkontrolltyp     | Typ av kontroll av källa. De tillgängliga alternativen är:</br> GitHub</br>Visual Studio Team Services (Git)</br> Visual Studio Team Services (TFVC)        |
|Lagringsplats     | Namnet på databasen eller projekt. Det här värdet hämtas från källkontroll. Exempel: $/ ContosoFinanceTFVCExample         |
|Gren     | Den gren som ska hämta källfiler från. Gren mål är inte tillgänglig för TFVC-källkontrollstypen.          |
|Mappsökväg     | Den mapp som innehåller runbooks för att synkronisera. Exempel: /Runbooks         |
|Automatisk synkronisering     | Aktiverar eller inaktivera automatisk synkronisering när ett genomförande görs i källkontroll         |
|Publicera Runbook     | Om inställd **på**, när runbooks har synkroniserats från källkontroll som de kommer att publiceras automatiskt.         |
|Beskrivning     | Ett textfält för att ge mer information        |

![Sammanfattning för kontroll av källa](./media/source-control-integration/source-control-summary.png)

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

|Omfång  |Beskrivning  |
|---------|---------|
|**lagringsplats**     |         |
|lagringsplats: status     | Åtkomststatus för genomförande         |
|repo_deployment      | Distributionsstatus för åtkomst         |
|public_repo     | Offentliga lagringsplatserna för åtkomst         |
|**Admin: repo_hook**     |         |
|Skriv: repo_hook     | Skriva lagringsplats hookar         |
|Läs: repo_hook|Läs lagringsplats hookar|

### <a name="azure-devops"></a>Azure DevOps

|Omfång  |
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

## <a name="next-steps"></a>Nästa steg

Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
