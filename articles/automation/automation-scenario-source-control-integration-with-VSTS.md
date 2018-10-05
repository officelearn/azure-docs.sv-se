---
title: Integrera Azure Automation med Azure DevOps-tjänsterna källkontroll
description: Scenario vägleder dig genom att ställa in integration med en Azure Automation-konto och källkontroll för Azure DevOps-tjänsterna.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: Azure powershell, Azure DevOps-tjänsterna, källkontroll, automation
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801445"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Azure Automation-scenario – Automation källkontrollsintegrering med Azure DevOps

> [!NOTE]
> Det finns en ny upplevelse för källkontroll. Läs mer om den nya upplevelsen i [källkontroll (förhandsversion)](source-control-integration.md).

I det här scenariot har du ett Azure DevOps-projekt som du använder för att hantera Azure Automation-runbooks eller DSC-konfigurationer i källkontrollen.

Den här artikeln beskriver hur du integrerar Azure DevOps med Azure Automation-miljön så att kontinuerlig integrering sker för varje incheckning.

## <a name="getting-the-scenario"></a>Hämta scenariot

Det här scenariot består av två PowerShell-runbooks som du kan importera direkt från den [Runbook-galleriet](automation-runbook-gallery.md) på Azure portal eller nedladdning från den [PowerShell-galleriet](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Beskrivning|
--------|------------|
Synkronisera VSTS | Importera runbooks eller konfigurationer från Azure DevOps-källkontroll när en incheckningen är klar. Om du kör manuellt, importerar och publicerar alla runbooks eller konfigurationer till Automation-kontot.| 
Sync-VSTSGit | Importera runbooks eller konfigurationer från Azure DevOps Git för källkontroll när en incheckningen är klar. Om du kör manuellt, importerar och publicerar alla runbooks eller konfigurationer till Automation-kontot.|

### <a name="variables"></a>Variabler

Variabel | Beskrivning|
-----------|------------|
VSToken | Skydda variabel tillgång som du skapar och som innehåller Azure DevOps personligt åtkomsttoken. Du kan lära dig hur du skapar en personlig åtkomsttoken för Azure DevOps på den [Azure DevOps-autentiseringssidan](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>Installera och konfigurera det här scenariot

Skapa en [personlig åtkomsttoken](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) i Azure DevOps som används för att synkronisera runbooks eller konfigurationer i ditt automation-konto.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Skapa en [säker variabeln](automation-variables.md) i ditt automation-konto för att lagra personliga åtkomsttoken så att runbook kan autentisera till Azure DevOps och synkronisera runbooks eller konfigurationer till Automation-kontot. Du kan kalla den här VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importera runbook som synkroniserar dina runbooks eller konfigurationer till automation-kontot. Du kan använda den [Azure DevOps exempelrunbooken](https://www.powershellgallery.com/packages/Sync-VSTS) eller [Azure DevOps med Git exempelrunbooken](https://www.powershellgallery.com/packages/Sync-VSTSGit) från den [PowerShell-galleriet](https://www.powershellgallery.com) beroende på om du använder Azure DevOps-källa kontroll eller Azure DevOps med Git och distribuera till ditt automation-konto.

![PowerShell-galleriet](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Du kan nu [publicera](automation-creating-importing-runbook.md#publishing-a-runbook) denna runbook så att du kan skapa en webhook.

![Pulish runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Skapa en [webhook](automation-webhooks.md) för den här synkroniseringen VSTS runbook och Fyll i parametrarna som visas nedan. Kontrollera att du kopierar en webhook-url som du behöver det för en tjänsthook i Azure DevOps. VSAccessTokenVariableName är namn (VSToken) för den säkra variabel som du skapade tidigare för att lagra personliga åtkomsttoken.

Integrera med Azure DevOps (Sync-VSTS.ps1) använder följande parametrar:

### <a name="sync-vsts-parameters"></a>Synkronisera VSTS-parametrar

Parameter | Beskrivning|
--------|------------|
WebhookData | Checka in informationen som skickas från Azure DevOps-tjänsthook innehåller. Den här parametern bör lämna tomt.| 
ResourceGroup | Det här är namnet på den resursgrupp som automation-kontot.|
AutomationAccountName | Namnet på automation-kontot som synkroniseras med Azure DevOps.|
VSFolder | Namnet på mappen i Azure DevOps där runbooks och konfigurationer finns.|
VSAccount | Namnet på Azure DevOps-organisation.|
VSAccessTokenVariableName | Namnet på säker variabeln (VSToken) som innehåller Azure DevOps personligt åtkomsttoken.|

![Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Om du använder Azure DevOps med GIT (Sync-VSTSGit.ps1) tar följande parametrar.

Parameter | Beskrivning|
--------|------------|
WebhookData | Den innehåller checka in informationen som skickas från hook för Azure DevOps-tjänst. Den här parametern bör lämna tomt.|
ResourceGroup | Det här namnet på den resursgrupp som automation-kontot.|
AutomationAccountName | Namnet på automation-kontot som synkroniseras med Azure DevOps.|
VSAccount | Namnet på Azure DevOps-organisation.|
VSProject | Namnet på projektet i Azure DevOps där runbooks och konfigurationer finns.|
GitRepo | Namnet på Git-lagringsplats.|
GitBranch | Namnet på grenen i Azure DevOps Git-lagringsplatsen.|
Mapp | Namnet på mappen i Azure DevOps Git-gren.|
VSAccessTokenVariableName | Namnet på säker variabeln (VSToken) som innehåller Azure DevOps personligt åtkomsttoken.|

![GIT-Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Skapa en tjänsthook i Azure DevOps för incheckningar till den mapp som utlöser denna webhook på checkar in ny kod. Välj **Webhooks** som tjänst för att integrera med när du skapar en ny prenumeration. Du kan lära dig mer om specialegenskaper på [dokumentation för Azure DevOps-specialegenskaper](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Tjänsthook](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Du bör nu att kunna göra alla incheckningar med dina runbooks och konfigurationer till Azure DevOps och har dessa automatiskt synkroniserad i ditt automation-konto.

![Synkronisering av runbook-utdata](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Om du kör denna runbook manuellt utan som utlöses av Azure DevOps kan du lämna den webhookdata-parametern tom och en fullständig synkronisering sker från den angivna Azure DevOps-mappen.

Om du vill avinstallera scenariot, ta bort tjänsthook från Azure DevOps kan du ta bort runbook och VSToken variabeln.
