---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar jobb status och jobb strömmar i Runbook till Azure Monitor loggar för att leverera ytterligare insikt och hantering.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2433e8193026b8aaa3cbf29eb1411c7449a4953
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849742"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar

Automation kan skicka status för Runbook-jobb och jobb strömmar till din Log Analytics-arbetsyta. Den här processen omfattar inte arbets ytans länkning och är helt oberoende. Jobb loggar och jobb strömmar visas i Azure Portal, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu kan du med Azure Monitor loggar:

* Få insikt i dina Automation-jobb.
* Utlös ett e-postmeddelande eller en avisering baserat på status för Runbook-jobb (till exempel misslyckad eller pausad).
* Skriv avancerade frågor för dina jobbströmmar.
* Korrelera jobb för olika Automation-konton.
* Visualisera din jobbhistorik över tid.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Krav och distributions överväganden

För att börja skicka dina Automation-loggar till Azure Monitor loggar behöver du:

* Den senaste versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* En Log Analytics-arbetsyta. Mer information finns i [Kom igång med Azure Monitor loggar](../log-analytics/log-analytics-get-started.md).
* ResourceId för ditt Azure Automation-konto.

Hitta ResourceId för ditt Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Kör följande PowerShell för att hitta ResourceId för din Log Analytics-arbetsyta:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har fler än ett Automation-konto, eller arbets ytor, i utdata från föregående kommandon, hittar du det *namn* du behöver för att konfigurera och kopiera värdet för *ResourceID*.

Om du behöver hitta *namnet* på ditt Automation-konto går du till Azure Portal väljer ditt Automation-konto på bladet **Automation-konto** och väljer **alla inställningar**. Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurera integration med Azure Monitor loggar

1. Starta **Windows PowerShell** från **Start** skärmen på datorn.
2. Kör följande PowerShell och redigera värdet för `[your resource id]` och `[resource id of the log analytics workspace]` med värdena från föregående steg.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

När skriptet har körts kan det ta en timme innan du börjar se poster i Azure Monitor loggar för nya JobLogs eller JobStreams som skrivs.

Om du vill se loggarna kör du följande fråga i Log Analytics-loggs ökning: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfigurationen

För att bekräfta att ditt Automation-konto skickar loggar till din Log Analytics-arbetsyta, kontrollerar du att diagnostiken är korrekt konfigurerad på Automation-kontot med hjälp av följande PowerShell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

I utdata kontrollerar du att:

* Under *loggar*är värdet för *aktiverat* värdet *Sant*.
* Värdet för *WorkspaceId* anges som ResourceID för din Log Analytics-arbetsyta.

## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster

Diagnostik från Azure Automation skapar två typer av poster i Azure Monitor loggar och märks som **AzureDiagnostics**. Följande frågor använder det uppgraderade frågespråket för att Azure Monitor loggar. Information om vanliga frågor mellan äldre frågespråk och det nya Azure Kusto Query-frågespråket finns [äldre på nytt lathund-bladet för Azure Kusto-frågespråk](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Jobb loggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>– Ny<br>– Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Slutförd |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | Hur Azure Monitor loggar in data. Always *Azure* för Azure Diagnostics. |
| ResultDescription |Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | ID för Azure-prenumerationen (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Utforskaren. AUTOMATISKA |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Jobb strömmar
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>\- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | Hur Azure Monitor loggar in data. Always *Azure* för Azure Diagnostics. |
| ResultDescription |Innehåller utdataströmmen från runbook. |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | ID för Azure-prenumerationen (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Utforskaren. AUTOMATISKA |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visa Automation-loggar i Azure Monitor loggar

Nu när du har börjat skicka loggen med automatiserings jobb till Azure Monitor loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor loggar.

Kör följande fråga för att se loggarna: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett Runbook-jobb Miss lyckas eller pausas
En av de främsta kunderna ber om möjlighet att skicka ett e-postmeddelande eller en text när något går fel med ett Runbook-jobb.

Om du vill skapa en varnings regel börjar du med att skapa en loggs ökning för de Runbook-resursposter som ska anropa aviseringen. Klicka på knappen **avisering** om du vill skapa och konfigurera varnings regeln.

1. Klicka på **Visa loggar**på översikts sidan för Log Analytics-arbetsyta.
2. Skapa en loggs öknings fråga för aviseringen genom att skriva följande sökning i fältet fråga: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` du kan också gruppera efter RunbookName genom att använda: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du ställer in loggar från fler än ett Automation-konto eller en prenumeration på din arbets yta, kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Namnet på Automation-kontot finns i resurs fältet i sökningen efter JobLogs.
3. Öppna skärmen **Skapa regel** genom att klicka på **+ ny varnings regel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns i [logg aviseringar i Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel
Förutom aviseringar vid fel kan du hitta när ett Runbook-jobb har ett icke-avslutande fel. I dessa fall genererar PowerShell en fel ström, men de icke-avslutande felen orsakar inte att jobbet pausas eller kraschar.

1. Klicka på **loggar**i arbets ytan Log Analytics.
2. I fältet fråga skriver du `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` och klickar sedan på **Sök** knappen.

### <a name="view-job-streams-for-a-job"></a>Visa jobb strömmar för ett jobb
När du felsöker ett jobb kanske du också vill titta på jobb strömmarna. Följande fråga visar alla data strömmar för ett enda jobb med GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historisk jobb status
Slutligen kanske du vill visualisera jobb historiken över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics tidigare jobb status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Ta bort diagnostikinställningar

Om du vill ta bort den diagnostiska inställningen från Automation-kontot kör du följande kommandon:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Sammanfattning

Genom att skicka status för automatiserings jobb och strömma data till Azure Monitor loggar kan du få bättre insikt i statusen för dina Automation-jobb genom att:
+ Konfigurera aviseringar som meddelar dig när det uppstår ett problem.
+ Med anpassade vyer och Sök frågor kan du visualisera dina Runbook-resultat, status för Runbook-jobb och andra relaterade nyckel indikatorer eller mått.

Azure Monitor-loggar ger bättre drifts insyn för dina automatiserings jobb och kan hjälpa dig att lösa incidenter snabbare.

## <a name="next-steps"></a>Nästa steg

* Information om hur du felsöker Log Analytics finns i [fel sökning varför Log Analytics inte längre samlar in data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Mer information om hur du skapar olika Sök frågor och granskar automatiserings jobb loggar med Azure Monitor loggar finns i [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämtar utdata och fel meddelanden från Runbooks finns i [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Mer information om Azure Monitor loggar och data insamlings källor finns i [samla in Azure Storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).

