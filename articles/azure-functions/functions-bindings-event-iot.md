---
title: Azure IoT Hub-bindningar för Azure-funktioner
description: Lär dig att använda IoT Hub-utlösare och bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586145"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub-bindningar för Azure-funktioner

Den här uppsättningen artiklar förklarar hur du arbetar med Azure Functions-bindningar för IoT Hub. IoT Hub-supporten baseras på Bindningen av [Azure Event Hubs](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Medan följande kodexempel använder Api:et för händelsehubbar, gäller den angivna syntaxen för IoT Hub-funktioner.

| Åtgärd | Typ |
|--------|------|
| Svara på händelser som skickas till en IoT-hubbhändelseström. | [Utlösare](./functions-bindings-event-iot-trigger.md) |
| Skriva händelser till en IoT-händelseström | [Utdatabindning](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Nästa steg

- [Svara på händelser som skickas till en händelsehubbhändelseström (utlösare)](./functions-bindings-event-iot-trigger.md)
- [Skriv händelser till en händelseström (utdatabindning)](./functions-bindings-event-iot-output.md)
