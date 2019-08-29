---
title: Azure IoT Hub-bindningar för Azure Functions
description: Lär dig hur du använder IoT Hub-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8b749a294da379e99e61072ff7a3415c508d2ac
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097636"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Functions-bindningar för IoT Hub. IoT Hub-stödet baseras på [Azure Event Hubs](functions-bindings-event-hubs.md)-bindningen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

För Azure Functions version 1. x finns IoT Hub bindningarna i [Microsoft. Azure. WebJobs. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

För funktioner 2. x, använder du [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) -paketet, version 3. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> I följande kod exempel används Event Hub API, men den aktuella syntaxen gäller för IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
