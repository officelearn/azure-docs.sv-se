---
title: Vidarebefordra jobbdata från Azure Automation till Log Analytics
description: Den här artikeln visar hur du skickar jobbstatus och runbook-jobbet dataströmmar till Azure Log Analytics för att ge ytterligare insikter och hantering.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562b1f1371133a1da8d24ebbb9c588f0597dda7f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Vidarebefordra jobbstatus och jobbet strömmar från Automation till logganalys
Automatisering kan skicka runbook jobbet status och jobbstatus strömmar till logganalys-arbetsytan. Jobbet loggar och dataströmmar för jobbet är synliga i Azure-portalen eller med PowerShell, för enskilda jobb och detta kan du utföra enkla undersökningar. Med Log Analytics kan du nu:

* Skaffa dig insikter om dina Automation-jobb.
* Utlösare som en e-post eller en avisering baserat på din runbook jobbets status (till exempel misslyckades eller pausas).
* Avancerade frågor Skriv över jobb-dataströmmar.
* Korrelera jobb över Automation-konton.
* Visualisera dina jobbhistorik över tid.

## <a name="prerequisites-and-deployment-considerations"></a>Krav och överväganden vid distribution
Om du vill börja skicka Automation-loggar till logganalys, behöver du:

* November 2016 eller senare versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Logganalys-arbetsytan. Mer information finns i [Kom igång med logganalys](../log-analytics/log-analytics-get-started.md). 
* Resurs-ID för Azure Automation-konto.


Du hittar resurs-ID för Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Kör följande PowerShell för att hitta ResourceId för logganalys-arbetsytan:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har fler än en Automation-konton eller arbetsytor i utdata från de föregående kommandona hittar den *namn* måste du konfigurera och kopiera värdet för *ResourceId*.

Om du vill söka efter den *namn* ditt Automation-konto i Azure portal väljer du ditt Automation-konto från den **Automation-konto** och välj **alla inställningar** . Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Ställa in integration med logganalys

1. På datorn, startar **Windows PowerShell** från den **starta** skärmen.
2. Kör följande PowerShell och redigera värdet för den `[your resource id]` och `[resource id of the log analytics workspace]` med värden från föregående steg.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

När du har kört skriptet visas poster i logganalys inom 10 minuter efter nya JobLogs eller JobStreams skrivs.

Kör följande fråga om du vill se loggarna i logganalys loggen sökning: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfigurationen
Kontrollera att diagnostik är korrekt konfigurerade för Automation-konto med hjälp av följande PowerShell för att bekräfta att ditt Automation-konto är skicka loggar till logganalys-arbetsytan:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Se till att i utdata:
+ Under *loggar*, värdet för *aktiverad* är *SANT*.
+ Värdet för *WorkspaceId* är inställd på ResourceId för logganalys-arbetsytan.

## <a name="log-analytics-records"></a>Log Analytics-poster

Diagnostik från Azure Automation skapar två typer av poster i logganalys och märks **AzureDiagnostics**. Följande frågor använda uppgraderade frågespråket till logganalys. Information om vanliga frågor mellan äldre frågespråket och nya Azure Log Analytics-frågespråket [äldre till nya Azure Log Analytics-frågespråket fusklapp](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Jobbloggar
| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>-Nya<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Slutförd |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Anger åtgärdstypen i Azure. Värdet är jobb för automatisering. |
| Resurs | Namnet på det Automation-kontot |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| Resurs-ID |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Dataströmmar för jobbet
| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>-Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Anger åtgärdstypen i Azure. Värdet är jobb för automatisering. |
| Resurs | Namnet på det Automation-kontot |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Innehåller utdataströmmen från runbook. |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| Resurs-ID |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visa Automation loggar i logganalys
Nu när du började skicka dina Automation-jobbloggar till logganalys, låt oss se vad du kan göra med dessa loggar i logganalys.

Kör följande fråga om du vill visa loggarna: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när en runbook-jobb misslyckas eller pausar
En övre kunden frågar avser möjlighet att skicka ett e-postmeddelande eller en text om något går fel med ett runbook-jobb.   

Om du vill skapa en aviseringsregel, börja med att skapa en logg Sök efter poster för runbook-jobb som ska anropa aviseringen. Klicka på den **avisering** för att skapa och konfigurera varningsregeln.

1. Översikt över Log Analytics-sidan klickar du på **loggen Sök**.
2. Skapa en logg sökfråga för aviseringen genom att skriva följande sökningen i fältet fråga: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` du kan också gruppera efter RunbookName med hjälp av: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Du kan gruppera aviseringarna genom prenumerationen och Automation-konto om du ställt in loggar från mer än en Automation-konto eller prenumeration på din arbetsyta. Automation-kontonamnet finns i fältet resurs i sökningen i JobLogs.
1. Öppna den **lägga till Varningsregeln** klickar du på **avisering** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns [aviseringar i logganalys](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel
Du hittar förutom Varna vid fel när en runbook-jobbet har en icke-avslutande fel. I dessa fall PowerShell genererar ett fel uppstod när strömmen, men inte avslutar felen orsakar inte ditt jobb att pausa eller misslyckas.    

1. Klicka på i logganalys-arbetsytan **loggen Sök**.
2. Ange i fältet fråga `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` och klicka sedan på den **Sök** knappen.

### <a name="view-job-streams-for-a-job"></a>Visa jobb dataströmmar för ett jobb
När du felsöker ett jobb kan du även vill söka i dataströmmar för jobbet. Följande fråga visar alla dataströmmar för ett enda projekt med GUID 2ebd22ea e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historiska jobbstatus
Slutligen kan du visualisera dina jobbhistorik över tid. Du kan använda den här frågan för att söka efter status för jobb med tiden.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Log Analytics historiska jobbet Status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Sammanfattning
Du kan få bättre inblick i ditt Automation-jobb efter status genom att skicka ditt Automation jobbdata status och dataströmmen till logganalys:
+ Konfigurera aviseringar för att meddela dig när det uppstår problem.
+ Använda anpassade vyer och sökfrågor visualisera dina runbook-resultat, relaterade runbook jobbstatus och andra viktiga indikatorer eller mått.  

Log Analytics ger bättre operativa synlighet till Automation-jobb och kan hjälpa adress incidenter snabbare.  

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar olika sökfrågor och granska loggarna för Automation-jobb med Log Analytics finns [logga sökningar i logganalys](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämta utdata och felmeddelanden från runbooks finns [Runbook utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Läs mer om logganalys och samling datakällor i [insamling av Azure storage-data i logganalys översikt](../log-analytics/log-analytics-azure-storage.md).
