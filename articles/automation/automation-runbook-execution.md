---
title: Runbook-körning i Azure Automation
description: Ger information om hur en runbook i Azure Automation ska bearbetas.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859ef4c28b858b00fcc9c7c73a3a706a11225113
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430729"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

När du startar en runbook i Azure Automation skapas ett jobb. Ett jobb innebär ett utförande av en runbookinstans. En Azure Automation arbetare är tilldelad att köra varje jobb. Medan arbetare som delas av många Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du har inte styra vilken Worker behandlar begäran för jobbet. En enda runbook kan ha många jobb som körs på en gång. Körningsmiljö för jobb från samma Automation-konto får återanvändas. Fler jobb körs samtidigt, desto oftare de kan skickas till samma sandbox. Jobb som körs i samma sandboxprocessen kan påverka varandra, ett exempel körs den `Disconnect-AzureRMAccount` cmdlet. Kör denna cmdlet skulle varje runbook-jobb i delade sandboxprocessen att koppla från. När du visar en lista med runbooks i Azure-portalen visar status för alla jobb som har startats för varje runbook. Du kan visa listan över jobb för varje runbook du spårar statusen för var och en. Jobbloggar lagras i högst 30 dagar. En beskrivning av olika jobbstatusar [Jobbstatusar](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Följande diagram visar livscykeln för ett runbook-jobb för [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) och [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Jobbstatus – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

Följande diagram visar livscykeln för ett runbook-jobb för [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks).

![Jobbstatus – PowerShell-skript](./media/automation-runbook-execution/job-statuses-script.png)

Dina jobb har åtkomst till dina Azure-resurser genom att göra en anslutning till din Azure-prenumeration. De har endast åtkomst till resurser i ditt datacenter om dessa resurser är tillgängliga från det offentliga molnet.

## <a name="job-statuses"></a>Jobbstatus

Följande tabell beskriver de olika statuslägen som är möjliga för ett jobb. PowerShell har två typer av fel, avslutande och icke-avslutande fel. Avslutande fel att ange runbook-status till **misslyckades** om de sker. Icke-avslutande fel kan skriptet som ska fortsätta även när de inträffar. Ett exempel på ett icke-avslutande fel med den `Get-ChildItem` cmdlet: en med en sökväg som inte finns. PowerShell ser att sökvägen finns inte, genererar ett fel och fortsätter till nästa mapp. Det här felet inte att ange runbook-status till **misslyckades** och kan markeras som **slutförd**. Om du vill tvinga en runbook för att stoppa på ett icke-avslutande fel, kan du använda `-ErrorAction Stop` på cmdlet: en.

| Status | Beskrivning |
|:--- |:--- |
| Slutfört |Jobbet har slutförts. |
| Misslyckad |För [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md), det gick inte att kompilera runbook. För [PowerShell-skript runbooks](automation-runbook-types.md), det gick inte att starta runbook eller jobbet hade ett undantag. |
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom den har nått den [rättmätiga del](#fair-share) begränsa tre gånger och startas från samma kontrollpunkten eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på att resurser från en Automation arbetare blir tillgängliga så det kan starta. |
| Startar |Jobbet har tilldelats en arbetare och systemet börjar den. |
| Fortsätter |Systemet är återuppta jobbet efter att det pausades. |
| Körs |Jobbet körs |
| Kör, väntar på resurser |Jobbet har inaktiverats eftersom den har nått den [rättmätiga del](#fair-share) gränsen. Återupptas från den senaste kontrollpunkten inom kort. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppas |Systemet håller på att stoppas jobbet. |
| Tillfälligt avbruten |Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en runbook inte har en kontrollpunkt, börjar den från början av runbooken. Om den har en kontrollpunkt för den startar igen och återupptas från den senaste kontrollpunkten. Runbook-jobbet är bara inaktiveras av systemet när ett undantag inträffar. Som standard anges ErrorActionPreference till **Fortsätt**, vilket innebär att jobbet körs på ett fel. Om inställningsvariabeln är inställt på **stoppa**, och sedan jobbet pausar på ett fel. Gäller för [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md) endast. |
| Pausar |Systemet försöker att pausa arbetet på användarens begäran. Runbooken måste nå nästa kontrollpunkt innan den pausas. Om den redan passerat den sista kontrollpunkten sedan slutförs den innan den pausas. Gäller för [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md) endast. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visa jobbstatus från Azure portal

Du kan visa en sammanfattande status för alla runbookjobb eller visa detaljer om en specifik runbook-jobb i Azure-portalen. Du kan också konfigurera integrering med Log Analytics-arbetsytan och vidarebefordra runbook jobbströmmar status och jobb. Mer information om hur du integrerar med Log Analytics finns i [vidarebefordrar jobbstatus och jobbströmmar från Automation till Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation runbook-jobb sammanfattning

Till höger om det valda Automation-kontot kan du se en sammanfattning av alla runbookjobb under **Jobbstatistik** panelen.

![Jobbstatistik panel](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Den här panelen visar antal och grafisk representation av jobbet har statusen för alla jobb som körs.

Klick på panelen presenterar den **jobb** sida med en sammanfattad lista över alla jobb som körs. Den här sidan visar status, starttider och slutförande gånger.

![Automation-kontosidan jobb](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Du kan filtrera listan över jobb genom att välja **filtrera jobb** och filtrera på en viss runbook jobbstatus, eller från den nedrullningsbara listan och tidsintervall att söka i.

![Filtrera jobbstatus](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Du kan också du kan visa sammanfattningsinformation för jobbet för en viss runbook genom att välja den runbooken från den **Runbooks** i ditt Automation-konto och välj sedan den **jobb** panelen. Den här åtgärden anger den **jobb** sidan, och därifrån kan du klicka på Jobbposten som ska visa detaljer och effekt.

![Automation-kontosidan jobb](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Jobbsammanfattning

Du kan visa en lista över alla jobb som har skapats för en viss runbook och deras senaste status. Du kan filtrera listan efter jobbstatus och tidpunkt för senaste ändring i jobbet. Om du vill se detaljerad information och effekt, klickar du på namnet på ett jobb. Jobbets detaljvy inkluderar värden för runbookparametrar som gavs av det jobbet.

Du kan använda följande steg för att se jobb för en runbook.

1. I Azure-portalen väljer du **Automation** och välj sedan namnet på ett Automation-konto.
2. Från hubben, väljer **Runbooks** och klicka sedan på den **Runbooks** väljer du en runbook i listan.
3. På sidan för den markerade runbooken klickar du på den **jobb** panelen.
4. Klicka på ett av jobben i listan och på informationssidan för runbook-jobb kan du se detaljer och effekt.

## <a name="retrieving-job-status-using-windows-powershell"></a>Hämta jobbstatus med Windows PowerShell

Du kan använda den [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) att hämta jobb som skapats för en runbook och detaljerna för ett specifikt jobb. Om du startar en runbook med Windows PowerShell med [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), och sedan den returnerar det resulterande jobbet. Använd [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) att få ett jobbs effekt.

Följande exempelkommandon hämtar det senaste jobbet för en exempel-runbook och visa dess status, de angivna värdena för runbook-parametrar och utdata från jobbet.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

I följande exempel hämtar utdata för ett specifikt jobb och returnerar varje post. I fallen skrivs att det fanns ett undantag för någon av posterna, undantaget i stället för värdet. Detta är användbart när undantag kan ge ytterligare information som kan inte loggas normalt under utdata.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Hämta information från aktivitetsloggen

Annan information, till exempel den person eller det konto som startade runbook kan hämtas från aktivitetsloggen för automation-kontot. Följande PowerShell-exempel innehåller den senaste användaren för att köra runbook i fråga:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Rättmätiga del

För att kunna dela resurser mellan alla runbooks i molnet, kommer Azure Automation tillfälligt ta bort eller stoppa alla jobb som har körts i mer än tre timmar. Jobb för [PowerShell-baserade runbooks](automation-runbook-types.md#powershell-runbooks) och [Python runbooks](automation-runbook-types.md#python-runbooks) stoppas och startas om inte, och visar stoppad för jobbets status.

För tidskrävande aktiviteter rekommenderar vi att du använder en [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Hybrid Runbook Worker begränsas inte av rättmätiga del och inte har en begränsning på hur lång tid en runbook kan köra. Det andra jobbet [gränser](../azure-subscription-service-limits.md#automation-limits) gäller både Azure sandbox-miljöer och Hybrid Runbook Worker. Även om Hybrid Runbook Worker inte begränsas av tre timmar rättmätiga del gränsen, runbooks kördes på dem fortfarande ha utvecklats för att stödja omstart beteenden från oväntat lokala infrastruktur problem.

Ett annat alternativ är att optimera runbook med hjälp av underordnade runbooks. Om din runbook igenom samma funktion på ett antal resurser, till exempel en databasåtgärd på flera databaser kan du flytta funktionen till en [underordnad runbook](automation-child-runbooks.md) och anropa det med den [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet. Var och en av dessa underordnade runbooks körs parallellt i separata processer, vilket minskar den överordnade runbookens totala körningstid. Du kan använda den [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet i din runbook för att kontrollera jobbstatus för varje underordnad om det finns åtgärder som måste utföras när den underordnade runbooken har slutförts.

## <a name="next-steps"></a>Nästa steg

* Läs mer om olika metoder som kan användas för att starta en runbook i Azure Automation i [starta en runbook i Azure Automation](automation-starting-a-runbook.md)

