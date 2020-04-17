---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar jobbstatus och runbook jobbströmmar till Azure Monitor-loggar för att ge ytterligare insikt och hantering.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457696"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Vidarebefordra jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar

Automatisering kan skicka runbook-jobbstatus och jobbströmmar till din Log Analytics-arbetsyta. Den här processen innebär inte att arbetsytan länkas och är helt oberoende. Jobbloggar och jobbströmmar visas i Azure-portalen, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu med Azure Monitor loggar kan du:

* Få insikt i status för dina Automation-jobb.
* Utlösa ett e-postmeddelande eller en avisering baserat på din runbook-jobbstatus (till exempel misslyckades eller pausades).
* Skriv avancerade frågor över dina jobbströmmar.
* Korrelera jobb över Automation-konton.
* Använd anpassade vyer och sökfrågor för att visualisera runbookresultaten, körboksjobbstatusen och andra relaterade nyckelindikatorer eller mått.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Förutsättningar och distributionsöverväganden

Om du vill börja skicka dina Automation-loggar till Azure Monitor-loggar behöver du:

* Den senaste versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* En Log Analytics-arbetsyta. Mer information finns i [Komma igång med Azure Monitor-loggar](../log-analytics/log-analytics-get-started.md).
* Resurs-ID:et för ditt Azure Automation-konto.

Använd följande kommando för att hitta resurs-ID:et för ditt Azure Automation-konto:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Om du vill hitta resurs-ID:et för din Log Analytics-arbetsyta kör du följande PowerShell-kommando:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Om du har mer än ett Automation-konto eller arbetsyta i utdata för föregående kommandon letar du reda på namnet som du behöver för att konfigurera och kopiera värdet för resurs-ID: t.

1. I Azure-portalen väljer du ditt Automation-konto från **automationskontobladet** och väljer **Alla inställningar**. 
2. Välj **Egenskaper**under **Kontoinställningar**i bladet **Alla inställningar** .  
3. I bladet **Egenskaper** bör du notera de egenskaper som visas nedan.

    ![Egenskaper för automationskonto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Loggposter för Azure Monitor

Azure Automation-diagnostik skapar två typer av poster i `AzureDiagnostics`Azure Monitor-loggar, taggade som . Tabellerna i nästa avsnitt är exempel på poster som Azure Automation genererar och de datatyper som visas i loggsökresultat.

### <a name="job-logs"></a>Jobbloggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar runbook-jobbet. |
| Resultattyp |Status för runbookjobbet. Möjliga värden:<br>- Ny<br>- Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Klar |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Den typ av åtgärd som utförs i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | System som Azure Monitor loggar använder för att samla in data. Värdet är alltid Azure för Azure-diagnostik. |
| ResultatBeskrivning |Resultattillståndet för runbook-jobb. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |Korrelations-GUID för runbook-jobbet. |
| ResourceId |Azure Automation-kontoresurs-ID för runbooken. |
| SubscriptionId | Azure-prenumerationen GUID för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Resursleverantören. Värdet är MICROSOFT. Automation. |
| ResourceType | Resurstypen. Värdet är AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Arbetsflöden
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar runbook-jobbet. |
| Resultattyp |Status för runbookjobbet. Möjliga värden:<br>- Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på Automation-kontot. |
| SourceSystem | System som Azure Monitor loggar använder för att samla in data. Värdet är alltid Azure för Azure-diagnostik. |
| ResultatBeskrivning |Beskrivning som innehåller utdataströmmen från runbooken. |
| CorrelationId |Korrelations-GUID för runbook-jobbet. |
| ResourceId |Azure Automation-kontoresurs-ID för runbooken. |
| SubscriptionId | Azure-prenumerationen GUID för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Resursleverantören. Värdet är MICROSOFT. Automation. |
| ResourceType | Resurstypen. Värdet är AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Konfigurera integrering med Azure Monitor-loggar

1. Starta Windows PowerShell från **Startskärmen** på datorn.
2. Kör följande PowerShell-kommandon och redigera `[your resource ID]` värdena för och `[resource ID of the log analytics workspace]` med värdena från föregående avsnitt.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

När du har kört det här skriptet kan det ta en `JobLogs` timme `JobStreams` innan du börjar se poster i Azure Monitor-loggar över nya eller skrivna.

Om du vill se loggarna kör du följande fråga i logganalysloggsökning:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifiera konfiguration

Kontrollera att diagnostiken är korrekt konfigurerad på Automation-kontot med hjälp av följande PowerShell-kommando för att bekräfta att ditt Automation-konto skickar loggar till din Log Analytics-arbetsyta.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Se till att:

* Under `Logs`är värdet `Enabled` för Sant.
* `WorkspaceId`är inställt `ResourceId` på värdet för din Log Analytics-arbetsyta.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visa automatiseringsloggar i Azure Monitor-loggar

Nu när du har börjat skicka dina Automation-jobbloggar till Azure Monitor-loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor-loggarna.

Kör följande fråga om du vill visa loggarna:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett runbook-jobb misslyckas eller pausas

En av de bästa kund frågar är möjligheten att skicka ett mail eller en text när något går fel med en runbook jobb.

Om du vill skapa en varningsregel börjar du med att skapa en loggsökning för de runbook-jobbposter som ska anropa aviseringen. Klicka på knappen **Avisering** om du vill skapa och konfigurera varningsregeln.

1. Klicka på **Visa loggar**på sidan Översikt över Logganalys.
2. Skapa en loggsökfråga för aviseringen genom att skriva in följande sökning i frågefältet:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Du kan också gruppera efter runbooknamnet med hjälp av:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du konfigurerar loggar från mer än ett Automation-konto eller en prenumeration på din arbetsyta kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Namn på automationskonto `Resource` finns i fältet `JobLogs`i sökningen av .
3. Om du vill öppna skärmen **Skapa regel** klickar du på **Ny varningsregel** högst upp på sidan. Mer information om alternativen för att konfigurera aviseringen finns [i Loggaviseringar i Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel

Förutom att varna om fel kan du hitta när ett runbook-jobb har ett fel som inte avslutas. I dessa fall skapar PowerShell en felström, men de fel som inte avslutas gör att jobbet pausas eller misslyckas.

1. Klicka på **Loggar**på logganalysarbetsytan.
2. Skriv i `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`frågefältet .
3. Klicka på knappen **Sök.**

### <a name="view-job-streams-for-a-job"></a>Visa jobbströmmar för ett jobb

När du felsöker ett jobb kanske du också vill titta på jobbströmmarna. Följande fråga visar alla strömmar för ett enda jobb med GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visa historisk jobbstatus

Slutligen kanske du vill visualisera din jobbhistorik över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Historiskt jobbstatusdiagram för logganalys](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Ta bort diagnostikinställningar

Om du vill ta bort diagnostikinställningen från Automation-kontot kör du följande kommando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Nästa steg

* Mer information om felsökning av Log Analytics finns i [Felsöka varför Log Analytics inte längre samlar in data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Mer information om hur du skapar olika sökfrågor och granskar automation-jobbloggarna med Azure Monitor-loggar finns [i Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
* Information om hur du skapar och hämtar utdata och felmeddelanden från runbooks finns i [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Mer information om Azure Monitor-loggar och datainsamlingskällor finns [i Samla in Azure-lagringsdata i Azure Monitor-loggloggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).