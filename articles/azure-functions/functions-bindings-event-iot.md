---
title: Azure IoT Hub-bindningar för Azure Functions
description: Förstå hur du använder IoT Hub-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e71e102a5a6df44e6bdd6a845540de3cbbef98f3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456988"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Functions-bindningar för IoT Hub. Stöd för IoT Hub är baserad på den [bindning för Azure Event Hubs](link to event hub doc).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

För Azure Functions-version 1.x, IoT Hub-bindningar finns i den [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

För Functions 2.x, Använd den [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) paketet, version 3.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Följande kodexempel använda API: et för Event Hub, kan viss syntax användas för IoT Hub-funktioner.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
