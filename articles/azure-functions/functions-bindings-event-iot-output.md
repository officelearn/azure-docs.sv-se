---
title: Azure IoT Hub-utdatabindning för Azure-funktioner
description: Lär dig att skriva meddelanden till Azure IoT Hubs-strömmar med Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277432"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure IoT Hub-utdatabindning för Azure-funktioner

I den här artikeln beskrivs hur du arbetar med Azure Functions-utdatabindningar för IoT Hub. IoT Hub-supporten baseras på Bindningen av [Azure Event Hubs](functions-bindings-event-hubs.md).

Information om inställnings- och konfigurationsinformation finns i [översikten](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Medan följande kodexempel använder Api:et för händelsehubbar, gäller den angivna syntaxen för IoT Hub-funktioner.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Nästa steg

- [Svara på händelser som skickas till en händelsehubbhändelseström (utlösare)](./functions-bindings-event-iot-trigger.md)
