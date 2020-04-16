---
title: Vidarebefordra rapporteringsdata för Azure Automation State Configuration till Azure Monitor-loggar
description: Den här artikeln visar hur du skickar DSC-rapporteringsdata (Desired State Configuration) från Azure Automation State Configuration till Azure Monitor-loggar för att ge ytterligare insikt och hantering.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392101"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Vidarebefordra rapporteringsdata för Azure Automation State Configuration till Azure Monitor-loggar

Azure Automation State Configuration behåller nodstatusdata i 30 dagar. Du kan skicka nodstatusdata till din Log Analytics-arbetsyta om du föredrar att behålla dessa data under en längre period. Efterlevnadsstatus visas i Azure-portalen eller med PowerShell, för noder och för enskilda DSC-resurser i nodkonfigurationer. 

Azure Monitor-loggar ger större operativ synlighet för dina Automation State Configuration-data och kan hjälpa till att hantera incidenter snabbare. Med Azure Monitor-loggar kan du:

- Hämta efterlevnadsinformation för hanterade noder och enskilda resurser.
- Utlösa ett e-postmeddelande eller en avisering baserat på efterlevnadsstatus.
- Skriv avancerade frågor över dina hanterade noder.
- Korrelera efterlevnadsstatus för Automation-konton.
- Använd anpassade vyer och sökfrågor för att visualisera runbookresultaten, körboksjobbstatusen och andra relaterade nyckelindikatorer eller mått.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

Om du vill börja skicka dina automationstillståndskonfigurationsrapporter till Azure Monitor-loggar behöver du:

- November 2016 eller senare versionen av [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Ett Azure Automation-konto. Mer information finns i [En introduktion till Azure Automation](automation-intro.md).
- En Log Analytics-arbetsyta med ett automations- &-tjänsterbjudande. Mer information finns i [Komma igång med Logganalys i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Minst en Azure Automation State Configuration-nod. Mer information finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).
- [XDscDiagnostics-modulen,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) version 2.7.0.0 eller senare. Installationssteg finns i [Felsöka Azure Automation Önskad tillståndskonfiguration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurera integrering med Azure Monitor-loggar

Så här börjar du importera data från Azure Automation State Configuration till Azure Monitor-loggar:

1. Logga in på ditt Azure-konto i PowerShell. Se [Logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Hämta resurs-ID:n för ditt Automation-konto genom att köra följande PowerShell-cmdlet. Om du har mer än ett automatiseringskonto väljer du resurs-ID för det konto som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Hämta resurs-ID:n för din Log Analytics-arbetsyta genom att köra följande PowerShell-cmdlet. Om du har mer än en arbetsyta väljer du resurs-ID för den arbetsyta som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Kör följande PowerShell-cmdlet `<AutomationResourceId>` `<WorkspaceResourceId>` och `ResourceId` ersätt och med värdena från vart och ett av föregående steg.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Om du vill sluta importera data från Azure Automation State Configuration till Azure Monitor-loggar kör du följande PowerShell-cmdlet.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visa tillståndskonfigurationsloggarna

När du har konfigurerat integrering med Azure Monitor-loggar för dina Automation State Configuration-data kan du visa dem genom att välja **Loggar** i avsnittet **Övervakning** i den vänstra rutan på sidan Tillståndskonfiguration (DSC).

![Loggar](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Fönstret Loggsökning öppnas med ett frågeområde som är begränsat till din Automation-kontoresurs. Du kan söka i tillståndskonfigurationsloggarna för DSC-åtgärder genom att söka i Azure Monitor-loggar. Posterna för DSC-åtgärder `AzureDiagnostics` lagras i tabellen. Om du till exempel vill hitta noder som inte är kompatibla skriver du följande fråga.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filtrera detaljer:

* Filtrera `DscNodeStatusData` på för att returnera åtgärder för varje tillståndskonfigurationsnod.
* Filtrera `DscResourceStatusData` på för att returnera åtgärder för varje DSC-resurs som anropas i nodkonfigurationen som tillämpas på den resursen. 
* Filtrera `DscResourceStatusData` på för att returnera felinformation för alla DSC-resurser som misslyckas.

Mer information om hur du skapar loggfrågor för att hitta data finns [i Översikt över loggfrågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Skicka ett e-postmeddelande när en efterlevnadskontroll av tillståndskonfiguration misslyckas

En av våra bästa kundförfrågningar är möjligheten att skicka ett e-postmeddelande eller en text när något går fel med en DSC-konfiguration.

Om du vill skapa en varningsregel börjar du med att skapa en loggsökning för rapportposterna för tillståndskonfiguration som ska anropa aviseringen. Klicka på knappen **Ny varningsregel** om du vill skapa och konfigurera varningsregeln.

1. Klicka på **Loggar**på sidan Översikt över Logganalysarbetsyta.
1. Skapa en loggsökfråga för aviseringen genom att skriva följande sökning i frågefältet:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Om du har konfigurerat loggar från mer än ett Automation-konto eller en prenumeration på din arbetsyta kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Härleda automationskontonamnet från fältet `Resource` `DscNodeStatusData` i sökningen av posterna.
1. Om du vill öppna skärmen **Skapa regel** klickar du på **Ny varningsregel** högst upp på sidan. 

Mer information om alternativen för att konfigurera aviseringen finns i [Skapa en varningsregel](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Hitta misslyckade DSC-resurser för alla noder

En fördel med att använda Azure Monitor-loggar är att du kan söka efter misslyckade kontroller över noder. Så här hittar du alla instanser av DSC-resurser som har misslyckats:

1. Klicka på **Loggar**på sidan Översikt över Logganalys.
1. Skapa en loggsökfråga för aviseringen genom att skriva in följande sökning i frågefältet:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visa historisk DSC-nodstatus

Om du vill visualisera statushistoriken för DSC-noden över tid kan du använda den här frågan:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Den här frågan visar ett diagram över nodstatus över tid.

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Azure Automation-diagnostik skapar två kategorier av poster i Azure Monitor-loggar:

* Nodstatusdata`DscNodeStatusData`( )
* Resursstatusdata`DscResourceStatusData`( )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då efterlevnadskontrollen kördes. |
| OperationName |`DscNodeStatusData`. |
| Resultattyp |Värde som anger om noden är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| NodeComplianceStatus_s |Statusvärde som anger om noden är kompatibel. |
| DscReportStatus |Statusvärde som anger om efterlevnadskontrollen kördes. |
| ConfigurationMode | Det läge som används för att tillämpa konfigurationen på noden. Möjliga värden: <ul><li>`ApplyOnly`: DSC tillämpar konfigurationen och gör ingenting längre om inte en ny konfiguration skjuts till målnoden eller när en ny konfiguration hämtas från en server. Efter det första tillämpningen av en ny konfiguration söker DSC inte efter drift från ett tidigare konfigurerat tillstånd. DSC försöker tillämpa konfigurationen tills den `ApplyOnly` lyckas innan värdet börjar gälla. </li><li>`ApplyAndMonitor`: Detta är standardvärdet. LCM tillämpar alla nya konfigurationer. Efter den första tillämpningen av en ny konfiguration, om målnoden driver från önskat tillstånd, rapporterar DSC avvikelsen i loggar. DSC försöker tillämpa konfigurationen tills den `ApplyAndMonitor` lyckas innan värdet börjar gälla.</li><li>`ApplyAndAutoCorrect`: DSC tillämpar alla nya konfigurationer. Efter det första tillämpningen av en ny konfiguration, om målnoden driver från önskat tillstånd, rapporterar DSC avvikelsen i loggar och sedan den aktuella konfigurationen.</li></ul> |
| HostName_s | Namnet på den hanterade noden. |
| IP-adress | IPv4-adressen för den hanterade noden. |
| Kategori | `DscNodeStatus`. |
| Resurs | Namnet på Azure Automation-kontot. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g | GUID som identifierar den hanterade noden. |
| DscReportId_g | GUID som identifierar rapporten. |
| LastSeenTime_t | Datum och tid då rapporten senast visades. |
| ReportStartTime_t | Datum och tid då rapporten startades. |
| ReportEndTime_t | Datum och tid då rapporten slutfördes. |
| NumberOfResources_d | Antalet DSC-resurser som anropas i konfigurationen som tillämpas på noden. |
| SourceSystem | Källsystemet som identifierar hur Azure Monitor-loggar har samlat in data. Alltid `Azure` för Azure-diagnostik. |
| ResourceId |Resursidentifieraren för Azure Automation-kontot. |
| ResultatBeskrivning | Resursbeskrivningen för den här åtgärden. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONSKONTO. |
| CorrelationId | Ett GUID som är korrelationsidentifieraren för efterlevnadsrapporten. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då efterlevnadskontrollen kördes. |
| OperationName |`DscResourceStatusData`.|
| Resultattyp |Om resursen är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| Kategori | DscNodeStatus. |
| Resurs | Namnet på Azure Automation-kontot. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g |GUID som identifierar den hanterade noden. |
| DscReportId_g |GUID som identifierar rapporten. |
| DscResourceId_s |Namnet på DSC-resursinstansen. |
| DscResourceName_s |Namnet på DSC-resursen. |
| DscResourceStatus_s |Om DSC-resursen är i överensstämmelse. |
| DscModuleName_s |Namnet på PowerShell-modulen som innehåller DSC-resursen. |
| DscModuleVersion_s |Den version av PowerShell-modulen som innehåller DSC-resursen. |
| DscConfigurationName_s |Namnet på konfigurationen som tillämpas på noden. |
| ErrorCode_s | Felkoden om resursen misslyckades. |
| ErrorMessage_s |Felmeddelandet om resursen misslyckades. |
| DscResourceDuration_d |Den tid, i sekunder, som DSC-resursen kördes. |
| SourceSystem | Hur Azure Monitor loggar samlade in data. Alltid `Azure` för Azure-diagnostik. |
| ResourceId |Identifieraren för Azure Automation-kontot. |
| ResultatBeskrivning | Beskrivningen för den här åtgärden. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONSKONTO. |
| CorrelationId |GUID som är korrelations-ID för efterlevnadsrapporten. |


## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Azure Automation State Configuration](automation-dsc-overview.md).
- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
- Mer information om hur du skapar olika sökfrågor och granskar konfigurationsloggarna för Automation-tillstånd med Azure Monitor-loggar finns [i Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
- Mer information om Azure Monitor-loggar och datainsamlingskällor finns [i Samla in Azure-lagringsdata i Azure Monitor-loggloggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).
