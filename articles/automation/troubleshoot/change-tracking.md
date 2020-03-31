---
title: Felsöka problem med Azure Change Tracking
description: Lär dig hur du felsöker och löser problem med azure automationsspårning och lagerfunktion.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198538"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Felsöka ändringsspårning och inventering

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Ändringsspårningsposter visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du ser inga ändringsspårnings- eller lagerresultat för Windows-datorer som är inbyggda för ändringsspårning.

#### <a name="cause"></a>Orsak

Det här felet kan ha följande orsaker:

* Microsoft Monitoring Agent körs inte.
* Kommunikationen tillbaka till Automation-kontot blockeras.
* Hanteringspaketen för ändringsspårning hämtas inte.
* Den virtuella datorn som är inboardad kan ha kommit från en klonad dator som inte var sysprepped med Microsoft Monitoring Agent installerat.

#### <a name="resolution"></a>Lösning

Lösningarna som beskrivs nedan kan hjälpa dig att lösa problemet. Om du fortfarande behöver hjälp kan du samla in diagnostikinformation och kontaktsupport. På agentdatorn navigerar du till C:\Program\Microsoft Monitoring Agent\Agent\Tools och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Som standard är felspårning aktiverat. Om du vill aktivera utförliga felmeddelanden som i föregående exempel använder du *parametern VER.* Om du vill ha informationsspårningar använder du *INF* när **starttracing.cmd**anropas .

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent körs inte

Kontrollera att Microsoft Monitoring Agent (HealthService.exe) körs på datorn.

##### <a name="communication-to-automation-account-blocked"></a>Meddelandet till automationskontot har blockerats

Kontrollera Loggboken på datorn och leta efter händelser som har ordet "changetracking" i dem.

Se [Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig mer om adresser och portar som måste tillåtas för att ändringsspårning ska fungera.

##### <a name="management-packs-not-downloaded"></a>Hanteringspaket har inte hämtats

Kontrollera att följande paket för ändringsspårning och lagerhantering är installerade lokalt:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VIRTUELL från klonad dator som inte har sysprepped

Om du använder en klonad avbildning sysprep avbildningen först och sedan installera Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: Inga ändringsspårning eller lagerresultat på Linux-datorer

#### <a name="issue"></a>Problem

Du ser inga resultat för lager- eller ändringsspårning för Linux-datorer som finns ombord för ändringsspårning. 

#### <a name="cause"></a>Orsak
Här är möjliga orsaker som är specifika för det här problemet:
* Log Analytics-agenten för Linux körs inte.
* Log Analytics-agenten för Linux är inte korrekt konfigurerad.
* Det finns FIM-konflikter (File Integrity Monitoring).

#### <a name="resolution"></a>Lösning 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent för Linux körs inte

Kontrollera att demonen för Log Analytics-agenten för Linux (omsagent) körs på din dator. Kör följande fråga på arbetsytan Log Analytics som är länkad till ditt Automation-konto.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Om du inte ser datorn i frågeresultat har den inte checkat in nyligen. Det finns förmodligen ett lokalt konfigurationsproblem och du bör installera om agenten. Information om installation och konfiguration finns i [Samla in loggdata med Log Analytics-agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Om datorn visas i frågeresultaten kontrollerar du scopekonfigurationen. Se [Inriktningsövervakningslösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Mer felsökning av problemet finns i [Problem: Du ser inga Linux-data](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent för Linux har inte konfigurerats korrekt

Log Analytics-agenten för Linux kanske inte är korrekt konfigurerad för utdatasamling för logg- och kommandoraden med hjälp av oms-logginsamlaren. Se [Spåra ändringar i din miljö med lösningen Ändra spårning](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM konflikter

Azure Security Centers FIM-funktion kan vara felaktigt validera integriteten för dina Linux-filer. Kontrollera att FIM är i drift och korrekt konfigurerad för Linux-filövervakning. Se [Spåra ändringar i din miljö med lösningen Ändra spårning](../change-tracking.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet använder du någon av följande kanaler för mer support.

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
