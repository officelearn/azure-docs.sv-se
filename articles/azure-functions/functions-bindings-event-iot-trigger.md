---
title: Azure IoT Hub-utlösare för Azure Functions
description: Lär dig att svara på händelser som skickas till en händelse ström i IoT Hub i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041642"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub-utlösare för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Functions-bindningar för IoT Hub. IoT Hub-stödet baseras på [Azure Event Hubs-bindningen](functions-bindings-event-hubs.md).

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-event-iot.md).

> [!IMPORTANT]
> I följande kod exempel används Event Hub API, men den aktuella syntaxen gäller för IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriv händelser till en händelse ström (utgående bindning)](./functions-bindings-event-iot-output.md)
