---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar jobb status och jobb strömmar i Runbook till Azure Monitor loggar för att leverera ytterligare insikt och hantering.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457696"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar

Automation kan skicka status för Runbook-jobb och jobb strömmar till din Log Analytics-arbetsyta. Den här processen omfattar inte arbets ytans länkning och är helt oberoende. Jobb loggar och jobb strömmar visas i Azure Portal, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu kan du med Azure Monitor loggar:

* Få inblick i status för dina Automation-jobb.
* Utlös ett e-postmeddelande eller en avisering baserat på status för Runbook-jobb (till exempel misslyckad eller pausad).
* Skriv avancerade frågor i dina jobb strömmar.
* Korrelera jobb mellan Automation-konton.
* Använd anpassade vyer och Sök frågor för att visualisera dina Runbook-resultat, status för Runbook-jobb och andra relaterade nyckel indikatorer eller mått.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Krav och distributions överväganden

För att börja skicka dina Automation-loggar till Azure Monitor loggar behöver du:

* Den senaste versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* En Log Analytics-arbetsyta. Mer information finns i [Kom igång med Azure Monitor loggar](../log-analytics/log-analytics-get-started.md).
* Resurs-ID för ditt Azure Automation-konto.

Använd följande kommando för att hitta resurs-ID: t för ditt Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Kör följande PowerShell-kommando för att hitta resurs-ID för din Log Analytics arbets yta:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har mer än ett Automation-konto eller en arbets yta i resultatet av föregående kommandon hittar du det namn som du behöver konfigurera och kopierar värdet för resurs-ID: t.

1. I Azure Portal väljer du ditt Automation-konto på bladet **Automation-konto** och väljer **alla inställningar**. 
2. Från bladet **alla inställningar** väljer du **Egenskaper**under **konto inställningar**.  
3. På bladet **Egenskaper** noterar du egenskaperna som visas nedan.

    ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster

Azure Automation diagnostik skapar du två typer av poster i Azure Monitor loggar, taggade `AzureDiagnostics`som. Tabellerna i nästa avsnitt är exempel på poster som Azure Automation genererar och de data typer som visas i loggs öknings resultat.

### <a name="job-logs"></a>Jobbloggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar Runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>– Ny<br>– Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Slutförd |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | System som Azure Monitor loggar används för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Jobbets resultat tillstånd för Runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |Runbook-jobbets korrelations-GUID. |
| ResourceId |Resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | GUID för Azure-prenumerationen för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Resurs leverantören. Värdet är MICROSOFT. Automatiska. |
| ResourceType | Resurs typen. Värdet är AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Arbetsflöden
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar Runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot. |
| SourceSystem | System som Azure Monitor loggar används för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Beskrivning som innehåller utdataströmmen från runbooken. |
| CorrelationId |Runbook-jobbets korrelations-GUID. |
| ResourceId |Resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | GUID för Azure-prenumerationen för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Resurs leverantören. Värdet är MICROSOFT. Automatiska. |
| ResourceType | Resurs typen. Värdet är AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Konfigurera integrering med Azure Monitor loggar

1. Starta Windows PowerShell från **Start** skärmen på datorn.
2. Kör följande PowerShell-kommandon och redigera värdena för `[your resource ID]` och `[resource ID of the log analytics workspace]` med värdena från föregående avsnitt.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

När skriptet har körts kan det ta en timme innan du börjar se poster i Azure Monitor loggar för nya `JobLogs` eller `JobStreams` skrivna.

Om du vill se loggarna kör du följande fråga i Log Analytics-loggs ökning:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfigurationen

För att bekräfta att ditt Automation-konto skickar loggar till din Log Analytics-arbetsyta, kontrollerar du att diagnostiken är korrekt konfigurerad på Automation-kontot med hjälp av följande PowerShell-kommando.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

I utdata kontrollerar du att:

* Under `Logs` `Enabled` är värdet sant.
* `WorkspaceId`anges till `ResourceId` värdet för din Log Analytics-arbetsyta.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visa Automation-loggar i Azure Monitor loggar

Nu när du har börjat skicka loggen med automatiserings jobb till Azure Monitor loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor loggar.

Kör följande fråga för att se loggarna:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett Runbook-jobb Miss lyckas eller pausas

En av de främsta kunderna ber om möjlighet att skicka ett e-postmeddelande eller en text när något går fel med ett Runbook-jobb.

Om du vill skapa en varnings regel börjar du med att skapa en loggs ökning för de Runbook-jobb poster som ska anropa aviseringen. Klicka på knappen **avisering** om du vill skapa och konfigurera varnings regeln.

1. Klicka på **Visa loggar**på översikts sidan för Log Analytics-arbetsyta.
2. Skapa en loggs öknings fråga för aviseringen genom att skriva följande sökning i fältet fråga:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Du kan också gruppera efter Runbook-namnet genom att använda:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du ställer in loggar från fler än ett Automation-konto eller en prenumeration på din arbets yta, kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Du hittar namnet på Automation-kontot i `Resource` fältet i sökningen `JobLogs`.
3. Öppna skärmen **Skapa regel** genom att klicka på **ny varnings regel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns i [logg aviseringar i Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel

Förutom aviseringar vid fel kan du hitta när ett Runbook-jobb har ett icke-avslutande fel. I dessa fall genererar PowerShell en fel ström, men de icke-avslutande felen orsakar inte att jobbet pausas eller kraschar.

1. Klicka på **loggar**i arbets ytan Log Analytics.
2. I fältet fråga skriver `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`du.
3. Klicka på knappen **Sök** .

### <a name="view-job-streams-for-a-job"></a>Visa jobb strömmar för ett jobb

När du felsöker ett jobb kanske du också vill titta på jobb strömmarna. Följande fråga visar alla data strömmar för ett enda jobb med GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historisk jobb status

Slutligen kanske du vill visualisera jobb historiken över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics historik jobb status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Tar bort diagnostikinställningar

Om du vill ta bort den diagnostiska inställningen från Automation-kontot kör du följande kommando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Nästa steg

* Information om hur du felsöker Log Analytics finns i [fel sökning varför Log Analytics inte längre samlar in data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Mer information om hur du skapar olika Sök frågor och granskar automatiserings jobb loggar med Azure Monitor loggar finns i [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämtar utdata och fel meddelanden från Runbooks finns i [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Mer information om Azure Monitor loggar och data insamlings källor finns i [samla in Azure Storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).