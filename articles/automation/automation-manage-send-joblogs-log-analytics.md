---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar jobbet har statusen och runbook jobbströmmar till Azure Azure Monitor-loggar att ge ytterligare insikter och hantering.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1897ddf328413decdc13cffaab0fb569d8d95665
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521677"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Vidarebefordra jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar

Automation kan skicka runbook jobbet status och jobbströmmar till Log Analytics-arbetsytan. Den här processen inbegriper inte arbetsytan länkar och är helt oberoende. Jobbloggar och jobbströmmar är synliga i Azure portal eller med PowerShell, för enskilda jobb och detta gör att du kan utföra enkla undersökningar. Med Azure Monitor-loggar kan du nu:

* Få insikt i dina Automation-jobb.
* Utlösa ett e-post eller en avisering baserat på din runbook jobbstatus (till exempel växlas eller har pausats).
* Skriv avancerade frågor för dina jobbströmmar.
* Korrelera jobb för olika Automation-konton.
* Visualisera din jobbhistorik över tid.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Krav och överväganden vid distribution

Om du vill börja skicka dina Automation-loggar till Azure Monitor-loggar, behöver du:

* November 2016 eller senare versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* En Log Analytics-arbetsyta. Mer information finns i [Kom igång med Azure Monitor-loggar](../log-analytics/log-analytics-get-started.md). 
* Resurs-ID för ditt Azure Automation-konto.

Hitta resurs-ID för ditt Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Du hittar resurs-ID för Log Analytics-arbetsytan genom att köra följande PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har fler än en Automation-konton, eller arbetsytor i utdata från föregående kommandon hittar den *namn* måste du konfigurera och kopiera värdet för *ResourceId*.

Om du behöver att hitta den *namn* ditt Automation-konto i Azure-portalen väljer du ditt Automation-konto från den **Automation-konto** bladet och välj **alla inställningar** . Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Ställa in integration med Azure Monitor-loggar

1. Starta på din dator **Windows PowerShell** från den **starta** skärmen.
2. Kör följande PowerShell och redigera värdet för den `[your resource id]` och `[resource id of the log analytics workspace]` med värden från föregående steg.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Det kan ta en timme innan du börjar Se poster i Azure Monitor-loggarna för nya JobLogs eller JobStreams skrivs när du har kört det här skriptet.

Kör följande fråga i log analytics-loggsökning för att visa loggarna: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfigurationen

För att bekräfta att ditt Automation-konto skickar loggar till Log Analytics-arbetsytan, kontrollerar du att diagnostik är korrekt konfigurerade på Automation-konto med hjälp av följande PowerShell:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Se till att i utdata:

* Under *loggar*, värdet för *aktiverad* är *SANT*.
* Värdet för *WorkspaceId* är inställd på resurs-ID för Log Analytics-arbetsytan.

## <a name="azure-monitor-log-records"></a>Azure Monitor-loggposter

Diagnostik från Azure Automation skapar två typer av poster i Azure Monitor-loggar och är märkta som **AzureDiagnostics**. Följande frågor använder det uppgraderade frågespråket till Azure Monitor-loggar. Information om vanliga frågor mellan äldre frågespråket och det nya Azure Kusto-frågespråket på [äldre till nya Azure Kusto Query Language-facit](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Jobbloggar

| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klient för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| resultType |Status för runbookjobbet. Möjliga värden:<br>– Ny<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Har slutförts |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet Job. |
| Resurs | Namnet på Automation-konto |
| SourceSystem | Hur ska loggas i Azure Monitor insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Jobbströmmar
| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klient för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| resultType |Status för runbookjobbet. Möjliga värden:<br>-Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet Job. |
| Resurs | Namnet på Automation-konto |
| SourceSystem | Hur ska loggas i Azure Monitor insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResultDescription |Innehåller utdataströmmen från runbook. |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger Azure Automation-konto resurs-id för runbook. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visa loggar för Automation i Azure Monitor-loggar

Nu när du igång med att skicka dina loggar för Automation-jobb till Azure Monitor-loggar, låt oss se vad du kan göra med de här loggarna i Azure Monitor-loggar.

Kör följande fråga för att visa loggarna: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett runbook-jobb misslyckas eller pausar
En av de viktiga kunden som ber avser möjligheten att skicka ett e-postmeddelande eller ett meddelande om något går fel med ett runbook-jobb.   

Om du vill skapa en aviseringsregel, börja med att skapa en loggsökning för poster för runbook-jobb som ska anropa aviseringen. Klicka på den **avisering** knappen för att skapa och konfigurera varningsregeln.

1. På översiktssidan för Log Analytics-arbetsytan klickar du på **visa loggar**.
2. Skapa en sökfråga i loggen för aviseringen genom att skriva följande sökningen i fältet fråga: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  Du kan också gruppera efter RunbookName med hjälp av: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Du kan gruppera aviseringarna av prenumeration och Automation-konto om du ställer in loggar från mer än en Automation-konto eller prenumeration till din arbetsyta. Automation-kontonamn finns i fältet Resource i sökningen av JobLogs.
3. Öppna den **skapa regeln** klickar du på **+ ny Aviseringsregel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns [Loggaviseringar i Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel
Förutom att Varna vid fel, hittar när en runbook-jobbet har en icke-avslutande fel. I dessa fall PowerShell genererar ett fel uppstod när strömmen, men de icke-avslutande fel orsakar inte jobbet att inaktivera eller misslyckas.    

1. I Log Analytics-arbetsytan, klickar du på **loggar**.
2. Skriv i fältet fråga `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` och klicka sedan på den **Search** knappen.

### <a name="view-job-streams-for-a-job"></a>Visa jobbströmmar för ett jobb
När du felsöker ett jobb, kan du även vill söka i dataströmmar för jobbet. Följande fråga visar alla dataströmmar för ett enskilt jobb med GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historiska jobbstatus
Slutligen kan du visualisera jobbets historik över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Statusdiagram för log Analytics historiska jobb](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Ta bort diagnostikinställningar

Om du vill ta bort diagnostikinställningen från Automation-kontot, kör du följande kommandon:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Sammanfattning

Genom att skicka din Automation-jobb status och stream-data till Azure Monitor-loggar, kan du få bättre inblick i status för dina Automation-jobb genom att:
+ Ställa in aviseringar som meddelar dig när det har uppstått problem.
+ Använder anpassade vyer och sökfrågor för att visualisera dina runbook-resultat, relaterade runbook jobbstatus och andra viktiga indikatorer eller mått.  

Azure Monitor-loggar ger större ökar du synligheten för Automation-jobb och hjälper dig att hantera incidenter snabbare.  

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du konstruerar olika sökfrågor och granskar jobbloggarna i Automation med Azure Monitor-loggar finns [Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämta utdata och meddelanden från runbooks finns i [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Läs mer om Azure Monitor-loggar och datakällsamling i [insamling av Azure storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).

