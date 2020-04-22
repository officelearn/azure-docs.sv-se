---
title: Felsöka problem med ändringsspårning och lager
description: Lär dig hur du felsöker och löser problem med Azure Automation Change Tracking and Inventory-lösningen.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679357"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Felsöka problem med spårning och lager av ändringar

I den här artikeln beskrivs felsöka problem med ändringsspårning och lager.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Ändringsspårning och lagerposter visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du ser inga resultat för ändringsspårning och lager för Windows-datorer som är inbyggda.

#### <a name="cause"></a>Orsak

Det här felet kan ha följande orsaker:

* Log Analytics-agenten för Windows körs inte.
* Kommunikationen tillbaka till Automation-kontot blockeras.
* Paketen för ändringsspårning och lagerhantering hämtas inte.
* Den virtuella datorn som är inboarded kan ha kommit från en klonad dator som inte var sysprepped med Log Analytics-agenten för Windows installerat.

#### <a name="resolution"></a>Lösning

På logganalysagentens dator navigerar du till **C:\Program\Microsoft Monitoring Agent\Agent\Tools** och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Om du fortfarande behöver hjälp kan du samla in diagnostikinformation och kontaktsupport. 

> [!NOTE]
> Log Analyticss-agenten aktiverar felspårning som standard. Om du vill aktivera utförliga felmeddelanden som `VER` i föregående exempel använder du parametern. Om du vill `INF` ha informationsspårningar använder du när du anropar `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics-agent för Windows körs inte

Kontrollera att Log Analytics-agenten för Windows (**HealthService.exe**) körs på datorn.

##### <a name="communication-to-automation-account-blocked"></a>Meddelandet till automationskontot har blockerats

Kontrollera Loggboken på datorn och leta efter `changetracking` händelser som har ordet i dem.

Se [Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig mer om adresser och portar som måste tillåtas för att ändringsspårning och lager ska fungera.

##### <a name="management-packs-not-downloaded"></a>Hanteringspaket har inte hämtats

Kontrollera att följande paket för ändringsspårning och lagerhantering är installerade lokalt:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VIRTUELL från klonad dator som inte har sysprepped

Om du använder en klonad avbildning ber vi först om avbildningen och installerar sedan Log Analytics-agenten för Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: Inga resultat för ändringsspårning och lager på Linux-datorer

#### <a name="issue"></a>Problem

Du ser inga resultat för lager- och ändringsspårning för Linux-datorer som är inbyggda för lösningen. 

#### <a name="cause"></a>Orsak
Här är möjliga orsaker som är specifika för det här problemet:
* Log Analytics-agenten för Linux körs inte.
* Log Analytics-agenten för Linux är inte korrekt konfigurerad.
* Det finns FIM-konflikter (File Integrity Monitoring).

#### <a name="resolution"></a>Lösning 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent för Linux körs inte

Kontrollera att demonen för Log Analytics-agenten för Linux (**omsagent**) körs på din dator. Kör följande fråga på arbetsytan Log Analytics som är länkad till ditt Automation-konto.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Om du inte ser datorn i frågeresultat har den inte checkat in nyligen. Det finns förmodligen ett lokalt konfigurationsproblem och du bör installera om agenten. Information om installation och konfiguration finns i [Samla in loggdata med Log Analytics-agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Om datorn visas i frågeresultaten kontrollerar du scopekonfigurationen. Se [Inriktningsövervakningslösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Mer felsökning av problemet finns i [Problem: Du ser inga Linux-data](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent för Linux har inte konfigurerats korrekt

Log Analytics-agenten för Linux kanske inte är korrekt konfigurerad för utdatasamling för logg- och kommandoraden med hjälp av oms-logginsamlaren. Se [Spåra ändringar i din miljö med lösningen Ändra spårning och lager](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM konflikter

Azure Security Centers FIM-funktion kan vara felaktigt validera integriteten för dina Linux-filer. Kontrollera att FIM är i drift och korrekt konfigurerad för Linux-filövervakning. Se [Spåra ändringar i din miljö med lösningen Ändra spårning och lager](../change-tracking.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
