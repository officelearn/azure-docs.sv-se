---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75750698"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med diagnostikinställningar

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

2. Välj **diagnostikinställningar**.

3. Välj **Aktivera diagnostik**.

   ![Slå på diagnostik](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Ge diagnostikinställningarna ett namn.

5. Välj var du vill skicka loggarna. Du kan välja valfri kombination av de tre alternativen:

   * Arkivera till ett lagringskonto
   * Strömma till en händelsehubb
   * Skicka till Log Analytics

6. Välj vilka åtgärder du vill övervaka och aktivera loggar för dessa åtgärder. De åtgärder som diagnostikinställningarna kan rapportera om är:

   * Anslutningar
   * Enhetstelemetri
   * Meddelanden från molnet till enheten
   * Åtgärder för enhetsidentitet
   * Filuppladdningar
   * Meddelanderoutning
   * Dubbla operationer mellan molnet och enheten
   * Dubbla åtgärder mellan enheter och moln
   * Dubbla operationer
   * Jobboperationer
   * Direkta metoder  
   * Distribuerad kalkering (förhandsgranskning)
   * Konfigurationer
   * Enhetsströmmar
   * Enhetsmått

6. Spara de nya inställningarna. 

Om du vill aktivera diagnostikinställningar med PowerShell använder du följande kod:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nya inställningar träder i kraft om cirka 10 minuter. Därefter visas loggar i det konfigurerade arkiveringsmålet på **diagnostikinställningsbladet.** Mer information om hur du konfigurerar diagnostik finns i [Samla in och använda loggdata från dina azure-resurser](../articles/azure-monitor/platform/platform-logs-overview.md).
