---
title: Public Preview service-gränser – Azure Digitals-dubbla Microsoft Docs
description: Lär dig mer om den offentliga för hands versionen, prenumerationen, instansen och hastighets begränsningarna för Azure Digital-webbplatser.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 8658bc44e41f61e717cf228a6d8e7cee6b477022
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264958"
---
# <a name="public-preview-service-limits"></a>Tjänstbegränsningar för allmän förhandsversion

I den offentliga för hands versionen har Azure Digitals-dubbla, följande tillfälliga prenumeration, instans och hastighets begränsningar.

Dessa begränsningar finns för att förenkla inlärningen av den nya tjänsten och dess många funktioner.

> [!NOTE]
> Dessa gränser kommer att höjas eller tas bort av allmän tillgänglighet (GA).

## <a name="per-subscription-limits"></a>Begränsningar per prenumeration

Under den offentliga för hands versionen kan varje Azure-prenumeration bara skapa eller köra en Azure Digital-instans i taget.

> [!TIP]
> Om du tar bort din instans kan du skapa en ny.

## <a name="per-instance-limits"></a>Gränser per instans

I sin tur kan varje Azure Digitals dubbla instanser ha:

- Exakt en inbäddad **IoTHub** -resurs som skapas automatiskt under tjänst etablering.
- Exakt en **EventHub** -slutpunkt för händelse typen **DeviceMessage**.
- Upp till tre **EventHub**-, **Service Bus**-eller **EventGrid** -slutpunkter för händelse typen **SensorChange**, **SpaceChange**, **TopologyOperation**eller **UdfCustom**.

> [!NOTE]
> Vissa parametrar som vanligt vis definieras i att skapa ovanstående Azure IoT-entiteter krävs inte under den offentliga för hands versionen.
> - I [referens dokumentationen för Swagger](./how-to-use-swagger.md) finns de senaste API-specifikationerna.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digitals dubbla gränser för hanterings-API

Gränserna för begär ande frekvens för ditt Azure Digitals hanterings-API är:

- 100 begär Anden per sekund till Azure Digitals hanterings-API.
- Upp till 1 000 objekt som returneras av en enda Azure Digitals API-fråga för hanterings-API.

> [!IMPORTANT]
> Om du överskrider gränsen på 1 000-objekt får du ett fel meddelande och måste förenkla frågan.

## <a name="user-defined-functions-rate-limits"></a>Hastighets begränsningar för användardefinierade funktioner

Följande gränser anger det totala antalet användardefinierade funktions anrop som gjorts till din Azure Digital-instansen:

- 400 klient biblioteks anrop per sekund
- 100 **SendNotification** anrop per sekund

> [!NOTE]
> Följande åtgärder kan orsaka att ytterligare hastighets gränser tillämpas tillfälligt:
> - Ändringar som gjorts i topologins objekts metadata
> - Uppdateringar gjorda i användardefinierad funktions definition
> - Enheter som skickar telemetri för första gången

## <a name="device-telemetry-limits"></a>Enhets telemetri begränsar

Följande begränsar det totala antalet meddelanden som dina enheter kan skicka till din Azure Digital-instansen:

- 100 meddelanden per sekund över alla enheter
-   25 meddelanden per sekund per enhet

## <a name="next-steps"></a>Nästa steg

- Om du vill prova ett digitalt Azure-exempel kan du gå till [snabb start för att hitta tillgängliga rum](./quickstart-view-occupancy-dotnet.md).
