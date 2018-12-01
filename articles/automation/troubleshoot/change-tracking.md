---
title: Felsökning av problem med Azure ändringsspårning
description: Den här artikeln innehåller information om hur du felsöker ändringsspårning
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 10/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 686a2c0ef0766176658d42d5ca53ea1ce6d3241c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722946"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Felsöka ändringsspårning och inventering

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: Ändringsspårning poster visas inte i Azure portal

#### <a name="issue"></a>Problem

Du ser inte några inventering eller ändringsspårning resultat för datorer som publiceras för ändringsspårning.

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. Den **Microsoft Monitoring Agent** inte körs
2. Kommunikation till Automation-kontot blockeras.
3. Hanteringspaketen för ändringsspårning är inte laddas ned.
4. Den virtuella datorn som det gäller kanske har kommit från en klonad dator som inte är Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Lösning

1. Kontrollera den **Microsoft Monitoring Agent** (HealthService.exe) körs på datorn.
2. Besök, [nätverksplanering](../automation-hybrid-runbook-worker.md#network-planning) att lära dig om vilka adresser och portar måste vara tillgängliga för ändringsspårning ska fungera.
3. Kontrollera att följande ändringsspårning och inventering hanteringspaket finns lokalt:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
4. Om med sysprep-avbildningen en klonade avbildningen först och installera Microsoft Monitoring Agent-agenten i efterhand.

Om lösningarna inte går att lösa ditt problem och du kontaktar supporten, kan du köra följande kommandon för att samla in diagnostiken på agenten

Navigera till på agentdatorn `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` och kör följande kommandon:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Standardfelet spårning är aktiverad, om du vill aktivera utförlig felmeddelanden som i föregående exempel, Använd `VER` parametern. Information-spårning, använda `INF` vid `StartTracing.cmd`.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.