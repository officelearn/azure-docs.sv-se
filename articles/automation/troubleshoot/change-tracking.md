---
title: Fel sökning av problem med Azure Ändringsspårning
description: Lär dig hur du felsöker och löser problem med funktionen Azure Automation Ändringsspårning och inventering.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198538"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Felsöka ändringsspårning och inventering

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: Ändringsspårning poster visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du kan inte se några Ändringsspårning eller inventerings resultat för Windows-datorer som har registrerats för Ändringsspårning.

#### <a name="cause"></a>Orsak

Det här felet kan ha följande orsaker:

* Microsoft Monitoring Agent körs inte.
* Kommunikation tillbaka till Automation-kontot blockeras.
* Hanterings paketen för Ändringsspårning laddas inte ned.
* Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte var Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Lösning

Lösningarna som beskrivs nedan kan hjälpa dig att lösa problemet. Om du fortfarande behöver hjälp kan du samla in diagnostikinformation och kontakta supporten. På agent datorn navigerar du till C:\Program\Microsoft Monitoring Agent\Agent\Tools och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Fel spårning är aktive rad som standard. Om du vill aktivera utförliga fel meddelanden som i föregående exempel använder du parametern *ver* . För informations spår använder du *INF* när du anropar **StartTracing. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent körs inte

Kontrol lera att Microsoft Monitoring Agent (HealthService. exe) körs på datorn.

##### <a name="communication-to-automation-account-blocked"></a>Kommunikationen med Automation-kontot blockerades

Kontrol lera Loggboken på datorn och leta efter händelser som har ordet "ChangeTracking" i dem.

Se [automatisera resurser i ditt data Center eller moln med hjälp av hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig om adresser och portar som måste vara tillåtna för att ändringsspårning ska fungera.

##### <a name="management-packs-not-downloaded"></a>Hanterings paket som inte har hämtats

Kontrol lera att följande Ändringsspårning-och inventerings hanterings paket har installerats lokalt:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Virtuell dator från klonad dator som inte har Sysprep

Om du använder en klonad avbildning ska du först köra Sysprep-avbildningen och sedan installera Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: inga Ändringsspårning eller inventerings resultat på Linux-datorer

#### <a name="issue"></a>Problem

Du kan inte se inventerings-eller Ändringsspårning resultat för Linux-datorer som har registrerats för Ändringsspårning. 

#### <a name="cause"></a>Orsak
Här är möjliga orsaker som är speciella för det här problemet:
* Log Analytics agenten för Linux körs inte.
* Log Analytics agenten för Linux är inte korrekt konfigurerad.
* Det finns FIM-konflikter (File Integrity Monitoring).

#### <a name="resolution"></a>Lösning 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics agent för Linux körs inte

Kontrol lera att daemonen för Log Analytics agent för Linux (omsagent) körs på datorn. Kör följande fråga på arbets ytan Log Analytics som är länkad till ditt Automation-konto.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Om du inte ser din dator i frågeresultat har den nyligen checkats in. Det finns förmodligen ett lokalt konfigurations problem och du bör installera om agenten. Information om installation och konfiguration finns i [samla in loggdata med Log Analytics agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Verifiera omfattnings konfigurationen om datorn visas i frågeresultatet. Se [mål övervaknings lösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Mer fel sökning av det här problemet finns i [problem: du ser inga Linux-data](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agent för Linux är inte korrekt konfigurerad

Log Analytics agent för Linux kanske inte är korrekt konfigurerad för logg-och kommando rads insamlingen med verktyget OMS-logg insamlare. Se [Spåra ändringar i din miljö med ändringsspårning-lösningen](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-konflikter

Azure Security Centers FIM-funktion kanske felaktigt verifierar integriteten för Linux-filerna. Kontrol lera att FIM fungerar och är korrekt konfigurerat för övervakning av Linux-filer. Se [Spåra ändringar i din miljö med ändringsspårning-lösningen](../change-tracking.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du använda någon av följande kanaler för mer support.

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
