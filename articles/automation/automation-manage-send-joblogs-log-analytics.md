---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar jobbstatus och runbook jobbströmmar till Azure Monitor-loggar för att ge ytterligare insikt och hantering.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421902"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Vidarebefordra jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar

Automatisering kan skicka runbook-jobbstatus och jobbströmmar till din Log Analytics-arbetsyta. Den här processen innebär inte att arbetsytan länkas och är helt oberoende. Jobbloggar och jobbströmmar visas i Azure-portalen, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu med Azure Monitor loggar kan du:

* Få insikt om dina Automation-jobb.
* Utlösa ett e-postmeddelande eller en avisering baserat på din runbook-jobbstatus (till exempel misslyckades eller pausades).
* Skriv avancerade frågor över dina jobbströmmar.
* Korrelera jobb över Automation-konton.
* Visualisera din jobbhistorik över tid.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Förutsättningar och distributionsöverväganden

Om du vill börja skicka dina Automation-loggar till Azure Monitor-loggar behöver du:

* Den senaste versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* En Log Analytics-arbetsyta. Mer information finns i [Komma igång med Azure Monitor-loggar](../log-analytics/log-analytics-get-started.md).
* ResourceId för ditt Azure Automation-konto.

Så här hittar du ResourceId för ditt Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Om du vill hitta ResourceId för din Log Analytics-arbetsyta kör du följande PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har fler än ett Automation-konton, eller arbetsytor, hittar du det *namn* du behöver för att konfigurera och kopiera värdet för *ResourceId*i utdata från föregående kommandon .

Om du behöver hitta *namnet* på ditt Automation-konto väljer du ditt Automation-konto i Azure-portalen från **bladet Automation-konto** och väljer **Alla inställningar**. Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![Egenskaper](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)för automationskonto .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurera integrering med Azure Monitor-loggar

1. Starta **Windows PowerShell** från **Startskärmen** på datorn.
2. Kör följande PowerShell och redigera värdet `[your resource id]` `[resource id of the log analytics workspace]` för och med värdena från föregående steg.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

När du har kört det här skriptet kan det ta en timme innan du börjar se poster i Azure Monitor-loggar för nya JobLogs eller JobStreams som skrivs.

Om du vill se loggarna kör du följande fråga i logganalysloggsökning:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfiguration

Kontrollera att diagnostiken är korrekt konfigurerad på Automation-kontot med hjälp av följande PowerShell för att bekräfta att ditt Automation-konto skickar loggar till din Log Analytics-arbetsyta:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

I utdata se till att:

* Under *Loggar*är värdet för *Aktiverad* *Sant*.
* Värdet *för WorkspaceId* är inställt på ResourceId för din Log Analytics-arbetsyta.

## <a name="azure-monitor-log-records"></a>Loggposter för Azure Monitor

Diagnostik från Azure Automation skapar två typer av poster i Azure Monitor-loggar och taggas som **AzureDiagnostics**. Följande frågor använder det uppgraderade frågespråket till Azure Monitor-loggar. Information om vanliga frågor mellan äldre frågespråk och det nya Azure Kusto-frågespråket finns i [Legacy till den nya lathunden för frågespråk för Azure Kusto-frågespråk](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Jobbloggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| Resultattyp |Status för runbookjobbet. Möjliga värden:<br>- Ny<br>- Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Klar |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på automationskontot |
| SourceSystem | Hur Azure Monitor loggar samlade in data. Alltid *Azure* för Azure-diagnostik. |
| ResultatBeskrivning |Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger azure automation-kontoresurs-ID för runbooken. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namn på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation |
| ResourceType | AUTOMATIONSKONTON |


### <a name="job-streams"></a>Jobbströmmar
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som är Id för runbook-jobbet. |
| Resultattyp |Status för runbookjobbet. Möjliga värden:<br>- Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på automationskontot |
| SourceSystem | Hur Azure Monitor loggar samlade in data. Alltid *Azure* för Azure-diagnostik. |
| ResultatBeskrivning |Innehåller utdataströmmen från runbook. |
| CorrelationId |GUID som är korrelations-Id för runbook-jobbet. |
| ResourceId |Anger azure automation-kontoresurs-ID för runbooken. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namn på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation |
| ResourceType | AUTOMATIONSKONTON |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visa automatiseringsloggar i Azure Monitor-loggar

Nu när du har börjat skicka dina Automation-jobbloggar till Azure Monitor-loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor-loggarna.

Kör följande fråga om du vill visa loggarna:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett runbook-jobb misslyckas eller pausas
En av de bästa kund frågar är möjligheten att skicka ett mail eller en text när något går fel med en runbook jobb.

Om du vill skapa en aviseringsregel börjar du med att skapa en loggsökning för de runbook-jobbposter som ska anropa aviseringen. Klicka på knappen **Avisering** om du vill skapa och konfigurera varningsregeln.

1. Klicka på **Visa loggar**på sidan Översikt över Logganalys.
2. Skapa en loggsökfråga för aviseringen genom att skriva `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` in följande sökning i frågefältet: Du kan också gruppera efter RunbookName med hjälp av:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du konfigurerar loggar från mer än ett Automation-konto eller en prenumeration på din arbetsyta kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Automation-kontonamn finns i fältet Resurs i sökningen av Jobbloggar.
3. Om du vill öppna skärmen **Skapa regel** klickar du på + **Ny varningsregel** högst upp på sidan. Mer information om alternativen för att konfigurera aviseringen finns [i Loggaviseringar i Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel
Förutom att varna om fel kan du hitta när ett runbook-jobb har ett fel som inte avslutas. I dessa fall producerar PowerShell en felström, men de fel som inte avslutas gör att jobbet pausas eller misslyckas.

1. Klicka på **Loggar**på logganalysarbetsytan.
2. Skriv och klicka `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` sedan på knappen **Sök** i frågefältet.

### <a name="view-job-streams-for-a-job"></a>Visa jobbströmmar för ett jobb
När du felsöker ett jobb kanske du också vill titta på jobbströmmarna. Följande fråga visar alla strömmar för ett enda jobb med GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historisk jobbstatus
Slutligen kanske du vill visualisera din jobbhistorik över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Historiskt jobbstatusdiagram för logganalys](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Ta bort diagnostikinställningar

Om du vill ta bort diagnostikinställningen från Automation-kontot kör du följande kommandon:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Sammanfattning

Genom att skicka din Automation-jobbstatus och strömma data till Azure Monitor-loggar kan du få bättre insikt i status för dina Automation-jobb genom att:
+ Ställa in aviseringar för att meddela dig när det finns ett problem.
+ Använda anpassade vyer och sökfrågor för att visualisera runbookresultaten, körboksjobbstatus och andra relaterade nyckelindikatorer eller mått.

Azure Monitor-loggar ger större operativ synlighet för dina Automation-jobb och kan hjälpa till att hantera incidenter snabbare.

## <a name="next-steps"></a>Nästa steg

* Mer information om felsökning av Log Analytics finns i [Felsöka varför Log Analytics inte längre samlar in data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Mer information om hur du skapar olika sökfrågor och granskar automation-jobbloggarna med Azure Monitor-loggar finns [i Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämtar utdata och felmeddelanden från runbooks finns i [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Mer information om Azure Monitor-loggar och datainsamlingskällor finns [i Samla in Azure-lagringsdata i Azure Monitor-loggloggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).

