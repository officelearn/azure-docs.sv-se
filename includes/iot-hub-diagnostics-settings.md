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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750698"
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
