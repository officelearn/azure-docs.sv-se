---
title: Fel sökning av problem med Ändringsspårning och inventering
description: Lär dig hur du felsöker och löser problem med Azure Automation Ändringsspårning-och inventerings lösning.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582143"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Felsöka Ändringsspårning-och inventerings problem

Den här artikeln beskriver hur du felsöker Azure Automation Ändringsspårning-och inventerings problem.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Ändringsspårning-och lager poster visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du kan inte se några Ändringsspårning-och inventerings resultat för Windows-datorer som har publicerats.

#### <a name="cause"></a>Orsak

Det här felet kan ha följande orsaker:

* Azure Log Analytics-agenten för Windows körs inte.
* Kommunikation tillbaka till Automation-kontot blockeras.
* Ändringsspårning-och inventerings hanterings paketen har inte laddats ned.
* Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte gick att Sysprep med Log Analytics-agenten för Windows installerad.

#### <a name="resolution"></a>Lösning

På den Log Analytics agent-datorn går du till **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Om du fortfarande behöver hjälp kan du samla in diagnostikinformation och kontakta supporten.

> [!NOTE]
> Log Analytics-agenten aktiverar fel spårning som standard. Om du vill aktivera utförliga fel meddelanden som i föregående exempel använder du `VER` parametern. För informations spår använder `INF` du när du anropar. `StartTracing.cmd`

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics agent för Windows körs inte

Verifiera att Log Analytics-agenten för Windows (**HealthService. exe**) körs på datorn.

##### <a name="communication-to-automation-account-blocked"></a>Kommunikationen med Automation-kontot blockerades

Kontrol lera Loggboken på datorn och leta efter händelser som har ordet `changetracking` i dem.

Om du vill veta mer om adresser och portar som måste vara tillåtna för att Ändringsspårning och inventering ska fungera, se [automatisera resurser i ditt data Center eller moln med hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Hanterings paket som inte har hämtats

Kontrol lera att följande Ändringsspårning-och inventerings hanterings paket har installerats lokalt:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Virtuell dator från klonad dator som inte har Sysprep

Om du använder en klonad avbildning ska du först köra Sysprep-avbildningen och sedan installera Log Analytics agent för Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: inga Ändringsspårning-och inventerings resultat på Linux-datorer

#### <a name="issue"></a>Problem

Du kan inte se några Ändringsspårning-och inventerings resultat för Linux-datorer som har publicerats för lösningen. 

#### <a name="cause"></a>Orsak
Här är möjliga orsaker som är speciella för det här problemet:
* Log Analytics agenten för Linux körs inte.
* Log Analytics agenten för Linux är inte korrekt konfigurerad.
* Det finns FIM-konflikter (File Integrity Monitoring).

#### <a name="resolution"></a>Lösning 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics agent för Linux körs inte

Kontrol lera att daemonen för Log Analytics agent för Linux (**omsagent**) körs på datorn. Kör följande fråga på arbets ytan Log Analytics som är länkad till ditt Automation-konto.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Om du inte ser din dator i frågeresultat har den nyligen checkats in. Det finns förmodligen ett lokalt konfigurations problem och du bör installera om agenten. Information om installation och konfiguration finns i [samla in loggdata med Log Analytics agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Verifiera omfattnings konfigurationen om datorn visas i frågeresultatet. Se [mål övervaknings lösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Mer fel sökning av det här problemet finns i [problem: du ser inga Linux-data](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agent för Linux är inte korrekt konfigurerad

Log Analytics-agenten för Linux kanske inte är korrekt konfigurerad för logg-och kommando rads utsamlings insamling med hjälp av verktyget OMS-logg insamlare. Se [Spåra ändringar i din miljö med ändringsspårning-och inventerings lösningen](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-konflikter

Azure Security Centers FIM-funktion kanske felaktigt verifierar integriteten för Linux-filerna. Kontrol lera att FIM fungerar och är korrekt konfigurerat för övervakning av Linux-filer. Se [Spåra ändringar i din miljö med ändringsspårning-och inventerings lösningen](../change-tracking.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
