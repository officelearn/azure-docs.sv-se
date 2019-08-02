---
title: Fel sökning av problem med Azure Ändringsspårning
description: Den här artikeln innehåller information om hur du felsöker Ändringsspårning
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564853"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Felsöka ändringsspårning och inventering

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Situationen Ändringsspårning poster visas inte för Windows-datorer

#### <a name="issue"></a>Problem

Du kan inte se inventerings-eller Ändringsspårning resultat för Windows-datorer som har registrerats för Ändringsspårning.

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. **Microsoft Monitoring Agent** körs inte
2. Kommunikation tillbaka till Automation-kontot blockeras.
3. Hanterings paketen för Ändringsspårning laddas inte ned.
4. Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte var Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Lösning

1. Kontrol lera att **Microsoft Monitoring Agent** (HealthService. exe) körs på datorn.
1. Kontrol lera **Loggboken** på datorn och leta efter händelser som har ordet `changetracking` i dem.
1. Besök, [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig om vilka adresser och portar som måste tillåtas för att ändringsspårning ska fungera.
1. Kontrol lera att följande Ändringsspårning-och inventerings hanterings paket finns lokalt:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Om du använder en klonad avbildning måste du först köra Sysprep-avbildningen och installera Microsoft Monitoring Agent-agenten efter faktumet.

Om dessa lösningar inte löser problemet och du kontaktar supporten kan du köra följande kommandon för att samla in diagnostiken på agenten

På agent datorn navigerar du till `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` och kör följande kommandon:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Som standard är fel spårning aktive rad, om du vill aktivera utförliga fel meddelanden som i föregående exempel använder `VER` du parameter. För informations spår använder `INF` du när du anropar. `StartTracing.cmd`

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
