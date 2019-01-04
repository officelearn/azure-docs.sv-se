---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e518263fc7f427ade4943f91fdc840b62a235205
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550031"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med inställningarna för startdiagnostik

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

2. Välj **diagnostikinställningar**.

3. Välj **slå på diagnostik**.

   ![Slå på diagnostik](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Namnge diagnostikinställningarna.

5. Välj var du vill skicka loggarna. Du kan välja valfri kombination av de tre alternativen:

   * Arkivera till ett lagringskonto
   * Strömma till en händelsehubb
   * Skicka till Log Analytics

6. Välja vilka åtgärder som du vill övervaka och aktivera loggar för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:

   * Anslutningar
   * Enhetstelemetri
   * Meddelanden från moln till enhet
   * Identitet åtgärder
   * Filöverföringar
   * Meddelanderedigering
   * Moln till enhet twin åtgärder
   * Enhet-till-moln-twin-åtgärder
   * Åtgärder för enhetstvilling
   * Jobbåtgärder
   * Direkta metoder  

6. Spara de nya inställningarna. 

Om du vill aktivera diagnostikinställningar med PowerShell använder du följande kod:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nya inställningarna träder i kraft i cirka 10 minuter. Efter det loggar visas i konfigurerade mål-arkivering på den **diagnostikinställningar** bladet. Läs mer om hur du konfigurerar diagnostik [samla in och använda loggdata från resurserna i azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).