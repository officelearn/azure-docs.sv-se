---
title: Starta en runbook i Azure Automation
description: Sammanfattar de olika metoder som kan användas för att starta en runbook i Azure Automation och ger information om hur du använder både Azure-portalen och Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7f2c0dda952959db3bffba6016f48b986016c19e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679452"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starta en runbook i Azure Automation

Följande tabell hjälper dig att bestämma vilken metod du vill starta en runbook i Azure Automation som passar bäst för just ditt scenario. Den här artikeln innehåller information om hur du startar en runbook med Azure-portalen och med Windows PowerShell. Information om andra metoder finns i annan dokumentation som du kan komma åt från länkarna nedan.

| **Metod** | **Kännetecken ** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Enklaste metoden med interaktivt användargränssnitt.<br> <li>Formulär för att ange enkla parametervärden.<br> <li>Spåra enkelt jobbtillstånd.<br> <li>Åtkomst autentiseras med Azure-inloggning. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Anropa från kommandoraden med Windows PowerShell-cmdlets.<br> <li>Kan ingå i automatiserad lösning med flera steg.<br> <li>Begäran autentiseras med certifikat eller OAuth-användarens huvudnamn/tjänsthuvudnamn.<br> <li>Ange enkla och komplexa parametervärden.<br> <li>Spåra jobbtillstånd.<br> <li>Klient som krävs för att stödja PowerShell-cmdlets. |
| [Azure Automation API](/rest/api/automation/) |<li>Mest flexibel metod men också mest komplex.<br> <li>Anropa från valfri anpassad kod som kan göra HTTP-begäranden.<br> <li>Begäran autentiseras med certifikat, eller Oauth användarens huvudnamn / tjänsthuvudnamn.<br> <li>Ange enkla och komplexa parametervärden. *Om du anropar en Python-runbook med API:et måste JSON-nyttolasten serialiseras.*<br> <li>Spåra jobbtillstånd. |
| [Webhooks](automation-webhooks.md) |<li>Starta runbook från en enda HTTP-begäran.<br> <li>Autentiseras med säkerhetstoken i URL.<br> <li>Klienten kan inte åsidosätta parametervärden som anges när webhook skapas. Runbook kan definiera en parameter som fylls med information om HTTP-begäranden.<br> <li>Ingen möjlighet att spåra jobbtillstånd via webhook URL. |
| [Svara på Azure Alert](../log-analytics/log-analytics-alerts.md) |<li>Starta en runbook som svar på Azure-avisering.<br> <li>Konfigurera webhook för runbook och länka till avisering.<br> <li>Autentiseras med säkerhetstoken i URL. |
| [Schema](automation-schedules.md) |<li>Starta runbook automatiskt på tim-, daglig-, vecko- eller månadsschema.<br> <li>Manipulera schema via Azure-portal, PowerShell-cmdlets eller Azure API.<br> <li>Ange parametervärden som ska användas med schema. |
| [Från en annan runbook](automation-child-runbooks.md) |<li>Använd en runbook som en aktivitet i en annan runbook.<br> <li>Användbart för funktioner som används av flera runbooks.<br> <li>Ange parametervärden till underordnad runbook och använd utdata i överordnad runbook. |

Följande bild illustrerar detaljerad steg-för-steg-process i livscykeln för en runbook. Den innehåller olika sätt en runbook startar i Azure Automation, vilka komponenter som krävs för Hybrid Runbook Worker för att köra Azure Automation runbooks och interaktioner mellan olika komponenter. Mer information om hur du kör Automation-runbooks i ditt datacenter finns i [hybridkörningsarbetare](automation-hybrid-runbook-worker.md)

![Runbook-arkitektur](media/automation-starting-runbook/runbooks-architecture.png)

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-parameters"></a>Runbook-parametrar

När du startar en runbook från Azure-portalen eller Windows PowerShell skickas instruktionen via Azure Automation-webbtjänsten. Den här tjänsten stöder inte parametrar med komplexa datatyper. Om du behöver ange ett värde för en komplex parameter måste du anropa den infogad från en annan runbook som beskrivs i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

Azure Automation-webbtjänsten tillhandahåller särskilda funktioner för parametrar som använder vissa datatyper enligt beskrivningen i följande avsnitt:

### <a name="named-values"></a>Namngivna värden

Om parametern är datatyp [objekt] kan du använda följande JSON-format för att skicka en lista med namngivna värden: *{Name1:'Värde1', Name2:'Value2', Name3:'Value3'}*. Dessa värden måste vara enkla typer. Runbooken tar emot parametern som ett [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) med egenskaper som motsvarar varje namngivet värde.

Överväg följande test-Runbook som accepterar en parameter med namnet user.

```powershell
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

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matriser

Om parametern är en matris som [array] eller [string[]], kan du använda följande JSON-format för att skicka en lista med värden: *[Värde1, Värde2, Value3]*. Dessa värden måste vara enkla typer.

Överväg följande test-Runbook som accepterar en parameter med namnet *user*.

```powershell
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

```input
["Joe","Smith",2,true]
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Autentiseringsuppgifter

Om parametern är `PSCredential`datatyp kan du ange namnet på en Azure [Automation-referenstillgång](automation-credentials.md). Runbook hämtar autentiseringsuppgifterna med det namn som du anger. Följande testkörningsbok accepterar `credential`en parameter som kallas .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Följande text kan användas för användarparametern förutsatt att det fanns `My Credential`en referenstillgång som kallas .

```input
My Credential
```

Om du antar att användarnamnet i `jsmith`autentiseringsuppgifterna är visas följande utdata.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Starta en runbook med Azure-portalen

1. I Azure-portalen väljer du **Automatisering** och klickar sedan på namnet på ett Automation-konto.
2. Välj **Runbooks**på Hub-menyn .
3. På sidan Runbooks markerar du en runbook och klickar sedan på **Start**.
4. Om runbooken har parametrar uppmanas du att ange värden med en textruta för varje parameter. Mer information om parametrar finns i [Runbook Parameters](#runbook-parameters).
5. I jobbfönstret kan du visa status för runbook-jobbet.

## <a name="start-a-runbook-with-powershell"></a>Starta en runbook med PowerShell

Du kan använda [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) för att starta en runbook med Windows PowerShell. Följande exempelkod startar en runbook som heter **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`returnerar ett jobbobjekt som du kan använda för att spåra status när runbooken har startats. Du kan sedan använda det här jobbobjektet med [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) för att bestämma status för jobbet och [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0) för att hämta utdata. I följande exempel startar en runbook som heter **Test-Runbook**, väntar tills den har slutförts och visar sedan utdata.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Om runbook kräver parametrar måste du ange dem som en [hashtable](https://technet.microsoft.com/library/hh847780.aspx). Nyckeln till hashtable måste matcha parameternamnet och värdet är parametervärdet. I följande exempel visas hur du startar en Runbook med två strängparametrar som heter FirstName och LastName, ett heltal som heter RepeatCount och en boolesk parameter som heter Show. Mer information om parametrar finns i [Runbook Parameters](#runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du kör Automation-runbooks i ditt datacenter finns i [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md).
* Mer information om hur du skapar modulära runbooks som ska användas av andra runbooks för specifika eller vanliga funktioner finns i [Child Runbooks](automation-child-runbooks.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
