---
title: Begränsningar för tjänsten för offentlig visning – Azure Digital Twins | Microsoft-dokument
description: Läs mer om offentlig förhandsversion, prenumeration, instans och prisgränser för Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370395"
---
# <a name="public-preview-service-limits"></a>Tjänstbegränsningar för allmän förhandsversion

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Under den offentliga förhandsversionen har Azure Digital Twins följande tillfälliga prenumerations-, instans- och prisgränser för befintliga kunder. Dessa begränsningar finns för att förenkla inlärningen av den nya tjänsten och dess många funktioner, och kommer att ökas eller tas bort av allmän tillgänglighet (GA).

## <a name="per-subscription-limits"></a>Gränser för per prenumeration

Under den offentliga förhandsversionen kan varje Azure-prenumeration bara skapa eller köra en Azure Digital Twins-instans åt gången. Om du tar bort instansen kan du skapa en ny.

## <a name="per-instance-limits"></a>Gränser per instans

Varje Azure Digital Twins-instans kan i sin tur ha:

- Exakt en inbäddad **IoTHub-resurs** som skapas automatiskt under tjänstetablering.
- Exakt En **EventHub-slutpunkt** för händelsetypen **DeviceMessage**.
- Upp till tre **EventHub-** **servicebus-** eller **EventGrid-slutpunkter** för händelsetypen **SensorChange**, **SpaceChange**, **TopologyOperation**eller **UdfCustom**.

> [!NOTE]
> Vissa parametrar som vanligtvis definieras för att skapa ovanstående Azure IoT-entiteter krävs inte under offentlig förhandsversion.
> - Mer information om [Swagger-referensdokumentationen](./how-to-use-swagger.md) finns i de senaste API-specifikationerna.

## <a name="azure-digital-twins-management-api-limits"></a>API-gränser för Azure Digital Twins Management

Begränsningarna för begäranden för azure digital twins management-API:et är:

- 100 begäranden per sekund till Azure Digital Twins Management API.
- Upp till 1 000 objekt som returneras av en enda Azure Digital Twins Management API-fråga.

> [!IMPORTANT]
> Om du överskrider gränsen på 1 000 objekt visas ett felmeddelande och måste förenkla frågan.

## <a name="user-defined-functions-rate-limits"></a>Hastighetsbegränsningar för användardefinierade funktioner

I följande gränser anges det totala antalet användardefinierade funktionsanrop som görs till din Azure Digital Twins-instans:

- 400 klientbibliotekssamtal per sekund
- 100 **SendNotification-samtal** per sekund

> [!NOTE]
> Följande åtgärder kan orsaka att ytterligare hastighetsbegränsningar tillämpas tillfälligt:
> - Ändringar som gjorts i metadata för topologiobjektet
> - Uppdateringar som gjorts i den användardefinierade funktionsdefinitionen
> - Enheter som skickar telemetri för första gången

## <a name="device-telemetry-limits"></a>Begränsningar för enhetstelemetri

Följande gränser begränsar det totala antalet meddelanden som dina enheter kan skicka till din Azure Digital Twins-instans:

- 100 meddelanden per sekund på alla enheter
-    25 meddelanden per sekund per enhet

## <a name="next-steps"></a>Nästa steg

- Om du vill prova ett azure digital twins-exempel går du till [Snabbstart för att hitta tillgängliga rum](./quickstart-view-occupancy-dotnet.md).
