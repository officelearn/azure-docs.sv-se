---
title: Vidarebefordra Azure Automation DSC rapporterar data till OMS Log Analytics | Microsoft Docs
description: "Den här artikeln visar hur du skickar önskad tillstånd Configuration (DSC) rapporterar data till Microsoft Operations Management Suite Log Analytics för att ge ytterligare insikter och hantering."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: eslesar
ms.openlocfilehash: 316031c5297a0201c8db4a9e177298c78962c673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="forward-azure-automation-dsc-reporting-data-to-oms-log-analytics"></a>Vidarebefordra Azure Automation DSC rapporterar data till OMS logganalys

Automatisering kan skicka DSC-noden statusdata till Microsoft Operations Management Suite (OMS) logganalys-arbetsytan.  
Kompatibilitetsstatus är synlig i Azure-portalen eller PowerShell för noderna och för enskilda DSC-resurser i nodkonfigurationer. Med Log Analytics kan du:

* Hämta information om kompatibilitet för hanterade noder och enskilda resurser
* Utlös en e-post eller en avisering baserat på status för efterlevnad
* Skriva avancerade frågor på dina hanterade noder
* Korrelera kompatibilitetsstatus över Automation-konton
* Visualisera dina nod kompatibilitetshistorik över tid

## <a name="prerequisites"></a>Krav

Om du vill börja skicka Automation DSC-rapporter till logganalys, behöver du:

* November 2016 eller senare versionen av [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Ett Azure Automation-konto. Mer information finns i [komma igång med Azure Automation](automation-offering-get-started.md)
* Logganalys-arbetsytan med en **Automation- och kontrollservern** tjänsterbjudande. Mer information finns i [Kom igång med logganalys](../log-analytics/log-analytics-get-started.md).
* Minst en Azure Automation DSC-nod. Mer information finns i [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md) 

## <a name="set-up-integration-with-log-analytics"></a>Ställa in integration med logganalys

För att börja importera data från Azure Automation DSC till logganalys, gör du följande:

1. Logga in på ditt Azure-konto i PowerShell. Se [logga in med Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Hämta den _ResourceId_ för automation-konto genom att köra följande PowerShell-kommando: (om du har mer än en automation-kontot väljer du den _ResourceID_ för det konto som du vill konfigurera).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Hämta den _ResourceId_ för logganalys-arbetsytan genom att köra följande PowerShell-kommando: (om du har mer än en arbetsytan väljer du den _ResourceID_ för arbetsytan som du vill konfigurera).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Kör följande PowerShell-kommando ersätter `<AutomationResourceId>` och `<WorkspaceResourceId>` med den _ResourceId_ värden från var och en av de här stegen:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Kör följande PowerShell-kommando om du vill stoppa importera data från Azure Automation DSC till logganalys.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Visa DSC-loggar

När du har ställt in integration med Log Analytics för Automation DSC-data en **loggen Sök** knappen ska visas på den **DSC-noder** bladet för ditt automation-konto. Klicka på den **loggen Sök** för att visa loggar för DSC-noden data.

![Loggen sökknappen](media/automation-dsc-diagnostics/log-search-button.png)

Den **loggen Sök** blad öppnas och du ser en **DscNodeStatusData** åtgärden för varje DSC-nod och en **DscResourceStatusData** åtgärden för varje [DSC resurs](https://msdn.microsoft.com/powershell/dsc/resources) med namnet på nodkonfigurationen som tillämpas på noden.

Den **DscResourceStatusData** åtgärden innehåller information om fel för DSC resurser som inte godkänts.

Klicka på varje åtgärd i listan om du vill se data för denna åtgärd.

Du kan också visa loggarna [sökning i logganalys. Se [söka efter data med hjälp av loggen sökningar](../log-analytics/log-analytics-log-searches.md).
Skriv följande fråga för att hitta DSC-loggar:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Du kan också begränsa frågan med Åtgärdsnamnet. Till exempel: ' Type = AzureDiagnostics ResourceProvider = ”MICROSOFT. Kategori för AUTOMATION ”=” DscNodeStatus ”OperationName =” DscNodeStatusData ”

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Skicka ett e-postmeddelande när en DSC-kompatibilitetskontrollen misslyckas

En av våra främsta kundönskemål är möjligheten att skicka ett e-postmeddelande eller en text om något går fel med en DSC-konfiguration.   

Om du vill skapa en aviseringsregel, börja med att skapa en logg Sök efter poster för DSC-rapport som ska anropa aviseringen.  Klicka på den **avisering** för att skapa och konfigurera varningsregeln.

1. Översikt över Log Analytics-sidan klickar du på **loggen Sök**.
1. Skapa en logg sökfråga för aviseringen genom att skriva följande sökningen i fältet fråga:`Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Om du har lagt upp loggar från mer än en Automation-konto eller prenumeration på din arbetsyta kan gruppera du aviseringar av prenumeration och Automation-konto.  
  Automation-kontonamnet kan härledas från fältet resurs i sökningen i DscNodeStatusData.  
1. Öppna den **lägga till Varningsregeln** klickar du på **avisering** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns [aviseringar i logganalys](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Sök efter misslyckade DSC-resurser på alla noder

Fördelen med att använda Log Analytics är att du kan söka efter misslyckade kontroller mellan noder.
Hitta alla instanser av DSC-resurser som har misslyckats.

1. Översikt över Log Analytics-sidan klickar du på **loggen Sök**.
1. Skapa en logg sökfråga för aviseringen genom att skriva följande sökningen i fältet fråga:`Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Visa historiska DSC-nod status

Slutligen kan du visualisera dina DSC-nod statushistorik över tid.  
Du kan använda den här frågan för att söka efter status för din DSC-noden status över tid.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Då visas ett diagram över nod status över tid.

## <a name="log-analytics-records"></a>Log Analytics-poster

Diagnostik från Azure Automation skapar två typer av poster i logganalys.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid när kompatibilitetskontrollen kördes. |
| OperationName |DscNodeStatusData |
| ResultType |Om noden är kompatibel. |
| NodeName_s |Namnet på den hantera noden. |
| NodeComplianceStatus_s |Om noden är kompatibel. |
| DscReportStatus |Om kompatibilitetskontrollen har körts. |
| ConfigurationMode | Hur används konfigurationen på noden. Möjliga värden är __”ApplyOnly”__,__”ApplyandMonitior”__, och __”ApplyandAutoCorrect”__. <ul><li>__ApplyOnly__: DSC gäller konfigurationen av och inget ytterligare såvida inte en ny konfiguration flyttas till målnoden eller när en ny konfiguration hämtas från en server. DSC kontrollerar inte om inte ett tidigare konfigurerade tillstånd efter första gången för en ny konfiguration. DSC försöker tillämpa konfigurationen tills den lyckas innan __ApplyOnly__ träder i kraft. </li><li> __ApplyAndMonitor__: Detta är standardvärdet. MGM gäller alla nya konfigurationer. Efter första gången för en ny konfiguration om målnoden drifts från det önskade läget rapporterar DSC diskrepans i loggarna. DSC försöker tillämpa konfigurationen tills den lyckas innan __ApplyAndMonitor__ träder i kraft.</li><li>__ApplyAndAutoCorrect__: DSC gäller alla nya konfigurationer. Efter första gången för en ny konfiguration om målnoden drifts från det önskade läget DSC rapporterar diskrepans i loggarna och återställer den aktuella konfigurationen.</li></ul> |
| HostName_s | Namnet på den hantera noden. |
| IP-adress | IPv4-adressen för den hantera noden. |
| Kategori | DscNodeStatus |
| Resurs | Namnet på Azure Automation-konto. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g |GUID som identifierar den hantera noden. |
| DscReportId_g |GUID som identifierar rapporten. |
| LastSeenTime_t |Datum och tid då rapporten senast visade. |
| ReportStartTime_t |Datum och tid då rapporten startades. |
| ReportEndTime_t |Datum och tid då rapporten slutförd. |
| NumberOfResources_d |Antalet resurser som DSC anropas i konfigurationen tillämpas på noden. |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| Resurs-ID |Anger Azure Automation-konto. |
| ResultDescription | Beskrivning för den här åtgärden. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID som är Korrelations-Id för rapporten. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid när kompatibilitetskontrollen kördes. |
| OperationName |DscResourceStatusData|
| ResultType |Om resursen är kompatibla. |
| NodeName_s |Namnet på den hantera noden. |
| Kategori | DscNodeStatus |
| Resurs | Namnet på Azure Automation-konto. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g |GUID som identifierar den hantera noden. |
| DscReportId_g |GUID som identifierar rapporten. |
| DscResourceId_s |Namnet på instansen för DSC-resurs. |
| DscResourceName_s |Namnet på DSC-resursen. |
| DscResourceStatus_s |Om DSC-resursen är kompatibel. |
| DscModuleName_s |Namnet på PowerShell-modulen som innehåller DSC-resursen. |
| DscModuleVersion_s |Versionen av PowerShell-modulen som innehåller DSC-resursen. |
| DscConfigurationName_s |Namnet på den konfiguration som tillämpas på noden. |
| ErrorCode_s | Felkoden om resursen misslyckades. |
| ErrorMessage_s |Felmeddelande om resursen misslyckades. |
| DscResourceDuration_d |Tid i sekunder som körde DSC-resursen. |
| SourceSystem | Hur logganalys insamlade data. Alltid *Azure* för Azure-diagnostik. |
| Resurs-ID |Anger Azure Automation-konto. |
| ResultDescription | Beskrivning för den här åtgärden. |
| SubscriptionId | Azure-prenumerationen Id (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | MICROSOFT. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID som är Korrelations-Id för rapporten. |

## <a name="summary"></a>Sammanfattning

Du kan få bättre inblick i status för Automation DSC-noder av genom att skicka Automation DSC-data till logganalys:

* Ställa in aviseringar för att meddela dig när det uppstår problem
* Använda anpassade vyer och sökfrågor visualisera dina runbook-resultat, relaterade runbook jobbstatus och andra viktiga indikatorer eller mått.  

Log Analytics ger bättre operativa synlighet Automation DSC-data och kan hjälpa adress incidenter snabbare.  

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar olika sökfrågor och granska loggarna för Automation DSC med Log Analytics finns [logga sökningar i logganalys](../log-analytics/log-analytics-log-searches.md)
* Mer information om hur du använder Azure Automation DSC finns [komma igång med Azure Automation DSC](automation-dsc-getting-started.md)
* Läs mer om OMS Log Analytics och datakällsamling i [Samla in data om Azure-lagring i Log Analytics-översikten](../log-analytics/log-analytics-azure-storage.md)

