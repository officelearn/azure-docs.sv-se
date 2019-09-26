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
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266831"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med diagnostikinställningar

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

2. Välj **diagnostikinställningar**.

3. Välj **slå på diagnostik**.

   ![Slå på diagnostik](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Ge diagnostikinställningar ett namn.

5. Välj var du vill skicka loggarna. Du kan välja en valfri kombination av de tre alternativen:

   * Arkivera till ett lagringskonto
   * Strömma till en händelsehubb
   * Skicka till Log Analytics

6. Välj vilka åtgärder som du vill övervaka och aktivera loggar för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:

   * Anslutningar
   * Telemetri för enhet
   * Meddelanden från moln till enhet
   * Enhets identitets åtgärder
   * Filöverföringar
   * Meddelanderedigering
   * Dubbla åtgärder från moln till enhet
   * Dubbla åtgärder från enhet till moln
   * Dubbla åtgärder
   * Jobb åtgärder
   * Direkta metoder  
   * Distribuerad spårning (för hands version)
   * Konfigurationer
   * Enhets strömmar
   * Enhets mått

6. Spara de nya inställningarna. 

Använd följande kod om du vill aktivera diagnostikinställningar med PowerShell:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nya inställningarna träder i kraft i cirka 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet på bladet **Inställningar för diagnostik** . Mer information om hur du konfigurerar diagnostik finns i [samla in och använda loggdata från dina Azure-resurser](../articles/azure-monitor/platform/resource-logs-overview.md).
