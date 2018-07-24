---
title: Runbook-körning i Azure Automation
description: Ger information om hur en runbook i Azure Automation ska bearbetas.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3dfe16cc09f0453aef8adf8bf87a00aebd2054bc
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214643"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

När du startar en runbook i Azure Automation skapas ett jobb. Ett jobb är en enskild körningsinstans av en runbook. En Azure Automation arbetare är tilldelad att köra varje jobb. Medan arbetare som delas av flera Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du kan inte ha styra vilken Worker behandlar begäran för jobbet. En enda runbook kan ha flera jobb som körs på en gång. Körningsmiljö för jobb från samma Automation-konto får återanvändas. När du visar en lista med runbooks i Azure-portalen visar status för alla jobb som har startats för varje runbook. Du kan visa listan över jobb för varje runbook för att kunna spåra status för var och en. En beskrivning av olika jobbstatusar [Jobbstatusar](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Följande diagram visar livscykeln för ett runbook-jobb för [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) och [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Jobbstatus – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

Följande diagram visar livscykeln för ett runbook-jobb för [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks).

![Jobbstatus – PowerShell-skript](./media/automation-runbook-execution/job-statuses-script.png)

Dina jobb har åtkomst till dina Azure-resurser genom att göra en anslutning till din Azure-prenumeration. De har endast åtkomst till resurser i ditt datacenter om dessa resurser är tillgängliga från det offentliga molnet.

## <a name="job-statuses"></a>Jobbstatus

I följande tabell beskrivs de olika statuslägen som är möjliga för ett jobb.

| Status | Beskrivning |
|:--- |:--- |
| Slutfört |Jobbet har slutförts. |
| Misslyckad |För [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md), det gick inte att kompilera runbook. För [PowerShell-skript runbooks](automation-runbook-types.md), det gick inte att starta runbook eller jobbet påträffade ett undantag. |
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom den har nått den [rättmätiga del](#fair-share) begränsa tre gånger och startas från samma kontrollpunkten eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på resurser på en Automation arbetare blir tillgängliga så att den kan startas. |
| Startar |Jobbet har tilldelats en arbetare och systemet håller startar om den. |
| Fortsätter |Systemet håller på att återuppta jobbet efter att det pausades. |
| Körs |Jobbet körs. |
| Kör, väntar på resurser |Jobbet har inaktiverats eftersom den har nått den [rättmätiga del](#fair-share) gränsen. Återupptas från den senaste kontrollpunkten inom kort. |
| Stoppad |Jobbet stoppades av användaren innan den slutfördes. |
| Stoppas |Systemet håller på att stoppa arbetet. |
| Tillfälligt avbruten |Jobbet pausades av användaren, av systemet eller av ett kommando i runbook. Ett jobb som pausats kan startas igen och återupptas från den senaste kontrollpunkten eller från början av runbooken om den inte redan har kontrollpunkter. Runbook-jobbet är bara inaktiveras av systemet när ett undantag inträffar. Som standard anges ErrorActionPreference till **Fortsätt**, vilket innebär att jobbet körs på ett fel. Om inställningsvariabeln är inställt på **stoppa**, och sedan jobbet pausar på ett fel. Gäller för [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md) endast. |
| Pausar |Systemet försöker att pausa arbetet på användarens begäran. Runbooken måste nå nästa kontrollpunkt innan den pausas. Om den redan passerat den sista kontrollpunkten sedan slutförs den innan den pausas. Gäller för [grafisk och PowerShell Workflow-runbooks](automation-runbook-types.md) endast. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visa jobbstatus från Azure portal

Du kan visa en sammanfattande status för alla runbookjobb eller visa detaljer om specifika runbook-jobb i Azure-portalen eller genom att konfigurera integration med Log Analytics-arbetsytan och vidarebefordra runbook jobbströmmar status och jobb. Mer information om hur du integrerar med Log Analytics finns i [vidarebefordrar jobbstatus och jobbströmmar från Automation till Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation runbook-jobb sammanfattning

Till höger om det valda Automation-kontot kan du se en sammanfattning av alla runbookjobb för ett valt Automation-konto under **Jobbstatistik** panelen.

![Jobbstatistik panel](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Den här panelen visar antal och grafisk representation av jobbet har statusen för alla jobb som körs.

Klick på panelen presenterar den **jobb** blad som innehåller en sammanfattad lista över alla jobb som körs, med status, jobbkörningen och start- och sluttider.

![Jobb-bladet för Automation-kontot](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Du kan filtrera listan över jobb genom att välja **filtrera jobb** och filtrera på en viss runbook jobbstatus, eller från den nedrullningsbara listan datum/tid-intervallet för att söka i.

![Filtrera jobbstatus](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Du kan också du kan visa sammanfattningsinformation för jobbet för en viss runbook genom att välja den runbooken från den **Runbooks** blad i ditt Automation-konto och välj sedan den **jobb** panelen. Detta medför det **jobb** bladet och därifrån kan du klicka på Jobbposten som ska visa detaljer och effekt.

![Jobb-bladet för Automation-kontot](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Jobbsammanfattning

Du kan visa en lista över alla jobb som har skapats för en viss runbook och deras senaste status. Du kan filtrera listan efter jobbstatus och tidpunkt för senaste ändring i jobbet. Om du vill se detaljerad information och effekt, klickar du på namnet på ett jobb. Den detaljerade vyn av jobbet innehåller värden för runbookparametrar som har angetts för jobbet.

Du kan använda följande steg för att visa jobb för en runbook.

1. I Azure-portalen väljer du **Automation** och välj sedan namnet på ett Automation-konto.
2. Från hubben, väljer **Runbooks** och klicka sedan på den **Runbooks** bladet Välj en runbook i listan.
3. På bladet för den markerade runbooken, klickar du på den **jobb** panelen.
4. Klicka på ett av jobben i listan och på bladet med runbook-jobb kan du se detaljer och effekt.

## <a name="retrieving-job-status-using-windows-powershell"></a>Hämta jobbstatus med Windows PowerShell

Du kan använda den [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) att hämta jobb som skapats för en runbook och detaljerna för ett specifikt jobb. Om du startar en runbook med Windows PowerShell med [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), och sedan den returnerar det resulterande jobbet. Använd [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)utdata för att få ett jobbs effekt.

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

För att kunna dela resurser mellan alla runbooks i molnet, kommer Azure Automation tillfälligt bort jobb efter det har körts i tre timmar. Under denna tid jobb för [PowerShell-baserade runbooks](automation-runbook-types.md#powershell-runbooks) stoppas och är inte startas. Jobbet status visas **stoppad**. Den här typen av runbook startas alltid om från början, eftersom de inte stöder kontrollpunkter.

[PowerShell-Arbetsflödesbaserade runbooks](automation-runbook-types.md#powershell-workflow-runbooks) återupptas från deras senaste [kontrollpunkt](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). När du har kört tre timmar, runbook-jobbet har pausats av tjänsten och dess status visas **som körs, väntar på resurser**. När en sandbox blir tillgängligt runbook sker en automatisk omstart av Automation-tjänsten och återupptar från den senaste kontrollpunkten. Detta är normalt PowerShell-arbetsflöde att pausa/starta om. Om runbook igen överskrider tre timmars körning, processen upprepas, upp till tre gånger. Efter den tredje omstarten, om runbooken fortfarande inte har slutförts inom tre timmar sedan runbook-jobbet misslyckades och jobbstatusen visas **misslyckades, väntar på resurser**. I så fall får du följande undantag fel.

*Jobbet kan inte fortsätta att köra eftersom den upprepade gånger har avlägsnats från samma kontrollpunkten. Kontrollera att din Runbook inte utför tidskrävande operationer utan att spara sitt tillstånd.*

Detta är att skydda tjänsten från runbooks som körs på obestämd tid utan att slutföra, eftersom de inte göra det i nästa kontrollpunkt utan att tas bort från minnet igen.

Om runbooken har inga kontrollpunkter eller jobbet hade inte nå den första kontrollpunkten innan tas bort från minnet, startas sedan om från början.

För att långa köra aktiviteter, rekommenderar vi att du använder en [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Hybrid Runbook Worker begränsas inte av rättmätiga del och inte har en begränsning på hur lång tid en runbook kan köra.

Om du använder en PowerShell Workflow-runbook i Azure, när du skapar en runbook, bör du kontrollera att tiden för att köra alla aktiviteter mellan två kontrollpunkter inte överskrider tre timmar. Du kan behöva lägga till kontrollpunkter till din runbook så att den inte uppnår sin gräns för den här tre timmar eller dela upp långa åtgärder. Din runbook kan till exempel utföra en reindex på en stor SQL-databas. Om den här enda åtgärden inte slutförs inom gränsen på rättmätiga del, tas bort från minnet jobbet och startas om från början. I det här fallet bör du dela upp reindex åtgärden i flera steg, t.ex omindexering en tabell i taget, och infoga en kontrollpunkt efter varje åtgärd så att jobbet återupptas efter den senaste åtgärden har slutförts.

## <a name="next-steps"></a>Nästa steg

* Läs mer om olika metoder som kan användas för att starta en runbook i Azure Automation i [starta en runbook i Azure Automation](automation-starting-a-runbook.md)
