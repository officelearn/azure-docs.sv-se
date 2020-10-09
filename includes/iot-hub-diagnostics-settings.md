---
title: inkludera fil
description: inkludera fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792053"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med diagnostikinställningar

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

2. Välj **diagnostikinställningar**.

3. Välj **Aktivera diagnostik**.

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
   * Fil överföringar
   * Meddelanderoutning
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

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet på bladet **Inställningar för diagnostik** . Mer information om hur du konfigurerar diagnostik finns i [samla in och använda loggdata från dina Azure-resurser](../articles/azure-monitor/platform/platform-logs-overview.md).
