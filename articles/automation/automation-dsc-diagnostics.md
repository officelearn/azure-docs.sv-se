---
title: Vidarebefordra Azure Automation-Tillståndskonfiguration rapporterar data till Log Analytics
description: Den här artikeln visar hur du skickar Desired State Configuration (DSC) rapportdata från tillståndskonfigurationen för Azure Automation till Log Analytics för att ge ytterligare insikter och hantering.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 62d201e608864949c86e52d8ac8fbf9ce8538f11
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631471"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-log-analytics"></a>Vidarebefordra Azure Automation-Tillståndskonfiguration rapporterar data till Log Analytics

Azure Automation State Configuration skicka Desired State Configuration (DSC) noddata status till Log Analytics-arbetsytan. Kompatibilitetsstatus för syns i Azure portal eller med PowerShell, för noder och för enskilda DSC-resurser i nodkonfigurationer. Med Log Analytics kan du:

- Hämta information om kompatibilitet för hanterade noder och enskilda resurser
- Utlös ett e-post eller en avisering baserat på status för uppdateringskompatibilitet
- Skriva avancerade frågor för din hanterade noder
- Korrelera kompatibilitetsstatus för Automation-konton
- Visualisera dina noden kompatibilitetshistorik över tid

## <a name="prerequisites"></a>Förutsättningar

Om du vill börja skicka dina Automation State Configuration-rapporter till Log Analytics, behöver du:

- November 2016 eller senare versionen av [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Ett Azure Automation-konto. Mer information finns i [komma igång med Azure Automation](automation-offering-get-started.md)
- Log Analytics-arbetsytan med en **automatisering och kontroll** tjänst-erbjudande. Mer information finns i [Kom igång med Log Analytics](../log-analytics/log-analytics-get-started.md).
- Minst en Azure Automation State Configuration-nod. Mer information finns i [konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Ställa in integration med Log Analytics

Om du vill börja importera data från Azure Automation DSC till Log Analytics, gör du följande:

1. Logga in på ditt Azure-konto i PowerShell. Se [logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Hämta den _ResourceId_ av ditt automation-konto genom att köra följande PowerShell-kommando: (om du har fler än ett automation-konto väljer du den _ResourceID_ för det konto som du vill konfigurera).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
  ```

1. Hämta den _ResourceId_ av Log Analytics-arbetsytan genom att köra följande PowerShell-kommando: (om du har fler än en arbetsyta väljer du den _ResourceID_ för den arbetsyta som du vill konfigurera).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
  ```

1. Kör följande PowerShell-kommando ersätter `<AutomationResourceId>` och `<WorkspaceResourceId>` med den _ResourceId_ värden från var och en av de här stegen:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
  ```

Om du vill avbryta import av data från tillståndskonfigurationen för Azure Automation till Log Analytics ska du köra följande PowerShell-kommando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Visa State Configuration-loggar

När du har konfigurerat integrering med Log Analytics för dina Automation State Configuration-data, en **loggsökning** knappen visas på den **DSC-noder** bladet för ditt automation-konto. Klicka på den **Loggsökning** för att visa loggarna för DSC-nod.

![Sökknappen i loggen](media/automation-dsc-diagnostics/log-search-button.png)

Den **Loggsökning** blad öppnas och du ser en **DscNodeStatusData** åtgärden för varje State Configuration-nod och en **DscResourceStatusData** åtgärden för varje [ DSC-resurs](/powershell/dsc/resources) namnet i nodkonfigurationen som tillämpas på noden.

Den **DscResourceStatusData** åtgärden innehåller information om fel för alla DSC-resurser som misslyckades.

Klicka på varje åtgärd i listan om du vill se data för den åtgärden.

Du kan också visa loggarna genom att [söka i Log Analytics. Se [hitta data med hjälp av sökningar i loggen](../log-analytics/log-analytics-log-searches.md).
Skriv följande fråga för att hitta dina State Configuration-loggar: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Du kan också begränsa frågan genom åtgärdens namn. Exempel: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Skicka ett e-postmeddelande när en Tillståndskonfiguration kompatibilitetskontrollen misslyckas

En av våra främsta Återkopplingen är för möjligheten att skicka ett e-postmeddelande eller ett meddelande om något går fel med en DSC-konfiguration.

Om du vill skapa en aviseringsregel, börja med att skapa en loggsökning för rapporten State Configuration-poster som ska anropa aviseringen. Klicka på den **+ ny Aviseringsregel** knappen för att skapa och konfigurera varningsregeln.

1. Översikt över Log Analytics-sidan klickar du på **Loggsökning**.
1. Skapa en sökfråga i loggen för aviseringen genom att skriva följande sökningen i fältet fråga:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Om du har lagt upp loggar från mer än en Automation-konto eller prenumeration till din arbetsyta kan gruppera du aviseringar av prenumeration och Automation-konto.  
   Automation-kontonamn kan härledas från fältet Resource i sökningen av DscNodeStatusData.  
1. Öppna den **skapa regeln** klickar du på **+ ny Aviseringsregel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns [skapar en avisering rulelert](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Hitta misslyckade DSC-resurser i alla noder

En fördel med att använda Log Analytics är att du kan söka efter misslyckade kontroller över noder.
Du hittar alla instanser av DSC-resurser som misslyckades.

1. Översikt över Log Analytics-sidan klickar du på **Loggsökning**.
1. Skapa en sökfråga i loggen för aviseringen genom att skriva följande sökningen i fältet fråga:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visa historiska DSC-nodstatus

Slutligen kan du visualisera dina statushistorik för DSC-noden med tiden.  
Du kan använda den här frågan för att söka efter status för dina DSC-nodstatus över tid.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Då visas ett diagram över nodstatusen över tid.

## <a name="log-analytics-records"></a>Log Analytics-poster

Diagnostik från Azure Automation skapar två typer av poster i Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid när kompatibilitetskontrollen kördes. |
| OperationName |DscNodeStatusData |
| resultType |Om noden är kompatibla. |
| NodeName_s |Namnet på den hantera noden. |
| NodeComplianceStatus_s |Om noden är kompatibla. |
| DscReportStatus |Om kompatibilitetskontrollen har körts. |
| ConfigurationMode | Hur konfigurationen tillämpas på noden. Möjliga värden är __”ApplyOnly”__,__”ApplyandMonitior”__, och __”ApplyandAutoCorrect”__. <ul><li>__ApplyOnly__: DSC gäller konfigurationen av och gör ingenting ytterligare såvida inte en ny konfiguration skickas till målnoden eller när en ny konfiguration hämtas från en server. Efter första gången av en ny konfiguration kontrollerar inte DSC för drift från ett tidigare konfigurerade tillstånd. DSC försöker tillämpa konfigurationen tills den lyckas innan __ApplyOnly__ träder i kraft. </li><li> __ApplyAndMonitor__: Detta är standardvärdet. LCM gäller alla nya konfigurationer. Efter första gången av en ny konfiguration rapporterar DSC avvikelse i loggarna om målnoden drifts från önskat tillstånd. DSC försöker tillämpa konfigurationen tills den lyckas innan __ApplyAndMonitor__ träder i kraft.</li><li>__ApplyAndAutoCorrect__: DSC gäller alla nya konfigurationer. Efter första gången av en ny konfiguration om målnoden drifts från önskat tillstånd DSC rapporterar avvikelse i loggar och återställer den aktuella konfigurationen.</li></ul> |
| HostName_s | Namnet på den hantera noden. |
| IP-adress | IPv4-adressen för hanterad nod. |
| Kategori | DscNodeStatus |
| Resurs | Namnet på Azure Automation-konto. |
| Tenant_g | GUID som identifierar klient för anroparen. |
| NodeId_g |GUID som identifierar hanterad nod. |
| DscReportId_g |GUID som identifierar rapporten. |
| LastSeenTime_t |Datum och tid när rapporten senast granskad. |
| ReportStartTime_t |Datum och tid då rapporten startades. |
| ReportEndTime_t |Datum och tid när rapporten har slutförts. |
| NumberOfResources_d |Antalet DSC-resurser anropas i konfigurationen tillämpas på noden. |
| SourceSystem | Hur Log Analytics insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResourceId |Anger vilket Azure Automation-konto. |
| ResultDescription | Beskrivning för den här åtgärden. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID som är Korrelations-Id för rapporten om enhetsefterlevnad. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Egenskap  | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid när kompatibilitetskontrollen kördes. |
| OperationName |DscResourceStatusData|
| resultType |Om resursen är kompatibla. |
| NodeName_s |Namnet på den hantera noden. |
| Kategori | DscNodeStatus |
| Resurs | Namnet på Azure Automation-konto. |
| Tenant_g | GUID som identifierar klient för anroparen. |
| NodeId_g |GUID som identifierar hanterad nod. |
| DscReportId_g |GUID som identifierar rapporten. |
| DscResourceId_s |Namnet på instansen för DSC-resurs. |
| DscResourceName_s |Namnet på DSC-resurs. |
| DscResourceStatus_s |Om DSC-resursen är i efterlevnad. |
| DscModuleName_s |Namnet på PowerShell-modulen som innehåller DSC-resurs. |
| DscModuleVersion_s |Versionen av PowerShell-modulen som innehåller DSC-resurs. |
| DscConfigurationName_s |Namnet på den konfiguration som tillämpas på noden. |
| ErrorCode_s | Felkoden om resursen misslyckades. |
| ErrorMessage_s |Felmeddelande om resursen misslyckades. |
| DscResourceDuration_d |Tid i sekunder som kördes av DSC-resurs. |
| SourceSystem | Hur Log Analytics insamlade data. Alltid *Azure* för Azure-diagnostik. |
| ResourceId |Anger vilket Azure Automation-konto. |
| ResultDescription | Beskrivning för den här åtgärden. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID som är Korrelations-Id för rapporten om enhetsefterlevnad. |

## <a name="summary"></a>Sammanfattning

Du kan få bättre insikt i status för dina Automation-Tillståndskonfiguration noder av genom att skicka dina Automation State Configuration-data till Log Analytics:

- Ställa in aviseringar som meddelar dig när det finns ett problem
- Använder anpassade vyer och sökfrågor för att visualisera dina runbook-resultat, relaterade runbook jobbstatus och andra viktiga indikatorer eller mått.  

Log Analytics ger större operativa insyn till dina Automation State Configuration-data och kan hjälpa att hantera incidenter snabbare.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [tillståndskonfigurationen för Azure Automation](automation-dsc-overview.md)
- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
- Läs mer om hur du konstruerar olika sökfrågor och granskar Automation State Configuration-loggar med Log Analytics i [Loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Läs mer om Log Analytics och datakällsamling i [insamling av Azure storage-data i Log Analytics-översikten](../log-analytics/log-analytics-azure-storage.md)