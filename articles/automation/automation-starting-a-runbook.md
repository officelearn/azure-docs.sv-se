---
title: Starta en runbook i Azure Automation
description: Sammanfattar de olika metoderna som kan användas för att starta en runbook i Azure Automation och ger information om hur du använder både Azure-portalen och Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 020923a76c94b10165e95bb4c5950419595dff0b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252351"
---
# <a name="starting-a-runbook-in-azure-automation"></a>Starta en runbook i Azure Automation
Tabellen nedan hjälper dig att fastställa metod för att starta en runbook i Azure Automation som är mest lämpliga för ditt specifika scenario. Den här artikeln innehåller mer information om hur du startar en runbook med Azure-portalen och med Windows PowerShell. Information om de andra metoderna finns i annan dokumentation som du kan komma åt via länkarna nedan.

| **METODEN** | **EGENSKAPER** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Enklaste metoden med interaktivt användargränssnitt.<br> <li>Formulär med enkla parametervärden.<br> <li>Spåra jobbets status.<br> <li>Åtkomst som autentiseras med Azure-inloggningsförfrågningar. |
| [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/start-azureautomationrunbook) |<li>Anropa från kommandoraden med Windows PowerShell-cmdlets.<br> <li>Kan ingå i automatiserad lösning med flera steg.<br> <li>Begäran har autentiserats med certifikat eller OAuth användarens huvudnamn / service principal.<br> <li>Ange enkla och komplexa parametervärden.<br> <li>Spåra jobbets status.<br> <li>Klient krävs för stöd av PowerShell-cmdletar. |
| [Azure Automation-API](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Mest flexibla metoden men även de flesta komplexa.<br> <li>Anropa från anpassad kod som kan göra HTTP-förfrågningar.<br> <li>Begäran som autentiserats med certifikat eller Oauth användarens huvudnamn / service principal.<br> <li>Ange enkla och komplexa parametervärden. *Om du anropar en Python-runbook med hjälp av API måste JSON-nyttolasten serialiseras.*<br> <li>Spåra jobbets status. |
| [Webhooks](automation-webhooks.md) |<li>Starta runbook från HTTP-begäran.<br> <li>Autentiserade med säkerhetstoken i URL: en.<br> <li>Klienten kan inte åsidosätta parametervärden har angetts när webhooken skapas. Runbook kan definiera en enda parameter som fylls i med information för HTTP-begäran.<br> <li>Ingen möjlighet att spåra jobbstatus via webhook-URL. |
| [Svara på Azure avisering](../log-analytics/log-analytics-alerts.md) |<li>Starta en runbook i svar på Azure-avisering.<br> <li>Konfigurera webhook för runbook och länk till avisering.<br> <li>Autentiserade med säkerhetstoken i URL: en. |
| [Schema](automation-schedules.md) |<li>Automatiskt starta runbook på varje timme, dag, varje vecka eller månad schema.<br> <li>Ändra schema via Azure portal, PowerShell-cmdletar eller Azure API.<br> <li>Ange parametervärden som ska användas med schema. |
| [Från en annan Runbook](automation-child-runbooks.md) |<li>Använd en runbook som en aktivitet i en annan runbook.<br> <li>Användbart för funktioner som används av flera runbooks.<br> <li>Ange parametervärden till underordnad runbook och använda utdata i överordnad runbook. |

Följande bild illustrerar detaljerade steg i livscykeln för en runbook. Den innehåller olika sätt som en runbook startas i Azure Automation, komponenter som krävs för Hybrid Runbook Worker att köra Azure Automation-runbooks och samverkan mellan olika komponenter. Läs om hur du kör Automation-runbooks i datacentret, [hybrid runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook-arkitektur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Starta en runbook med Azure portal
1. I Azure-portalen väljer du **Automation** och klicka sedan på namnet på ett automation-konto.
2. På navmenyn väljer **Runbooks**.
3. På den **Runbooks** sidan, Välj en runbook och klicka sedan på **starta**.
4. Om runbooken har parametrar uppmanas du att ange värden med en textruta för varje parameter. Se [Runbookparametrar](#Runbook-parameters) nedan för mer information om parametrar.
5. På den **jobbet** kan du kan visa statusen för runbook-jobbet.

## <a name="starting-a-runbook-with-windows-powershell"></a>Starta en runbook med Windows PowerShell
Du kan använda den [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) att starta en runbook med Windows PowerShell. Följande exempelkod startar en runbook med namnet Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook returnerar ett jobbobjekt som du kan använda för att spåra dess status när runbooken startar. Du kan sedan använda detta jobbobjekt med [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) att fastställa statusen för jobbet och [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) att få dess utdata. Följande exempelkod startar en runbook med namnet Test-Runbook, väntar tills den har slutförts och visar sedan dess utdata.

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Om runbook kräver parametrar och sedan måste du ange dem som en [hash-tabell](https://technet.microsoft.com/library/hh847780.aspx) där nyckeln för hash-tabellen matchar parameternamnet och värdet är parametervärdet. I följande exempel visas hur du startar en runbook med två strängparametrar som heter FirstName och LastName, ett heltal som heter RepeatCount och en boolesk parameter som heter Show. Mer information om parametrar finns i [Runbookparametrar](#Runbook-parameters) nedan.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-parametrar
När du startar en runbook från Azure portal eller Windows PowerShell skickas instruktionen via Azure Automation-webbtjänsten. Den här tjänsten stöder inte parametrar med komplexa datatyper. Om du måste ange ett värde för en komplex parameter så måste du anropa den infogad från en annan runbook enligt beskrivningen i [underordnade runbooks i Azure Automation](automation-child-runbooks.md).

Azure Automation-webbtjänsten tillhandahåller särskilda funktioner för parametrar med vissa datatyper enligt beskrivningen i följande avsnitt:

### <a name="named-values"></a>Namngivna värden
Om parametern är datatypen [objekt], så du kan använda följande JSON-format för att skicka en lista över namngivna värden: *{Name1: 'Value1', Name2: 'Value2', Name3: 'Value3'}*. Dessa värden måste vara enkla typer. Runbooken får parametern som ett [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) med egenskaper som motsvarar varje namngivet värde.

Överväg följande test-runbook som accepterar en parameter med namnet användare.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Följande text kan användas för user-parameter.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Detta resulterar i följande utdata:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>matriser
Om parametern är en matris som t.ex. [array] eller [string []], du kan använda följande JSON-format för att skicka en lista med värden: *[Value1, Value2, Value3]*. Dessa värden måste vara enkla typer.

Överväg följande test-runbook som accepterar en parameter med namnet *användaren*.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Följande text kan användas för user-parameter.

```
["Joe","Smith",2,true]
```

Detta resulterar i följande utdata:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Autentiseringsuppgifter
Om parametern är datatypen **PSCredential**, kan du ange namnet på en Azure Automation [autentiseringstillgång](automation-credentials.md). Runbook hämtar autentiseringsuppgift med det namn som du anger.

Överväg följande test-runbook som accepterar en parameter med namnet autentiseringsuppgifter.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Följande text kan användas för användarparametern förutsatt att det fanns en autentiseringsuppgifttillgång med namnet *mina autentiseringsuppgifter*.

```
My Credential
```

Om användarnamnet i autentiseringsuppgiften var *jsmith*, detta resulterar i följande utdata:

```
jsmith
```

## <a name="next-steps"></a>Nästa steg
* Runbook-arkitekturen i aktuella artikeln ger en översikt över runbooks som hanterar resurser i Azure och lokalt med Hybrid Runbook Worker. Läs om hur du kör Automation-runbooks i datacentret, [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Läs mer om skapa modulbaserade runbooks som ska användas av andra runbooks för specifika eller vanliga funktioner [underordnade Runbooks](automation-child-runbooks.md).

