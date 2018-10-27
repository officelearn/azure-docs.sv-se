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
ms.openlocfilehash: 0039c4eff3571a96cba1ab36136e0a588d78eb75
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159136"
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

Nya inställningarna träder i kraft i cirka 10 minuter. Efter det loggar visas i konfigurerade mål-arkivering på den **diagnostikinställningar** bladet. Läs mer om hur du konfigurerar diagnostik [samla in och använda loggdata från resurserna i azure](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).