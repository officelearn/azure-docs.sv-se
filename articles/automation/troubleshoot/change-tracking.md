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
ms.openlocfilehash: 51a9dbf8be6538534c05a4b8b6fcd913ef8c6ae3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769939"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Felsöka ändringsspårning och inventering

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: Ändringsspårning poster visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du kan inte se inventerings-eller Ändringsspårning resultat för Windows-datorer som har registrerats för Ändringsspårning.

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. **Microsoft Monitoring Agent** körs inte
2. Kommunikation tillbaka till Automation-kontot blockeras.
3. Hanterings paketen för Ändringsspårning laddas inte ned.
4. Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte var Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Upplösning

1. Kontrol lera att **Microsoft Monitoring Agent** (HealthService. exe) körs på datorn.
1. Kontrol lera **Loggboken** på datorn och leta efter händelser som har ordet `changetracking` i dem.
1. Besök, [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig om vilka adresser och portar som måste tillåtas för att ändringsspårning ska fungera.
1. Kontrol lera att följande Ändringsspårning-och inventerings hanterings paket finns lokalt:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Om du använder en klonad avbildning måste du först köra Sysprep-avbildningen och installera Microsoft Monitoring Agent-agenten efter faktumet.

Om dessa lösningar inte löser problemet och du kontaktar supporten kan du köra följande kommandon för att samla in diagnostiken på agenten

Gå till `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` på agent datorn och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Som standard är fel spårning aktive rad. om du vill aktivera utförliga fel meddelanden som i föregående exempel använder du `VER` parameter. Använd `INF` när du anropar `StartTracing.cmd`för informations spår.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
