---
title: Azure Digital Twins som offentlig förhandsversion tjänstbegränsningar | Microsoft Docs
description: Förstå Azure Digital Twins offentliga förhandsversion tjänstbegränsningar
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324185"
---
# <a name="public-preview-service-limits"></a>Tjänstbegränsningar i offentlig förhandsversion

Allmänt tillgängliga förhandsversionen har Azure Digital Twins tillfälliga prenumeration, instans och hastighetsbegränsningar som beskrivs nedan.

Dessa begränsningar finns för att förenkla lära dig mer om den nya tjänsten och dess många funktioner.

> [!NOTE]
> Gränserna kommer att öka och/eller tas bort av allmän tillgänglighet (GA).

## <a name="per-subscription-limits"></a>Gränserna per prenumeration

Allmänt tillgängliga förhandsversionen kan varje Azure-prenumeration skapa eller körs exakt en Digital Twins för Azure-instans i taget.

> [!TIP]
> Tar bort din instans kan du skapa en ny.

## <a name="per-instance-limits"></a>Gränserna per instans

I sin tur kan varje Azure Digital Twins-instans ha:

- En `IoTHub` resurs
- En `EventHub` slutpunkt för händelsetypen DeviceMessage
- Upp till tre `EventHub`, `ServiceBus`, eller `EventGrid` slutpunkter typ av händelse `SensorChange`, `SpaceChange`, `TopologyOperation`, eller `UdfCustom`

## <a name="management-api-limits"></a>Hanterings-API-gränser

Hastighetsbegränsningar för begäran för API Management är:

- 100 begäranden per sekund till Management-API
- En enda API Management-fråga kan gå tillbaka upp till 1 000 objekt

> [!IMPORTANT]
> Om du överskrider gränsen på 1000-objektet, du får ett felmeddelande och behöver du förenkla frågan.

## <a name="udf-rate-limits"></a>UDF hastighetsbegränsningar

Det totala antalet alla användardefinierad funktion-anrop som görs till din Azure Digital Twins-instans anger du följande begränsningar:

- 400 biblioteket klientanrop per sekund
- 100 SendNotification anrop per sekund

> [!NOTE]
> Följande åtgärder kan orsaka ytterligare hastighetsbegränsningar för att tillfälligt tillämpas:
> - Topologi objektet metadata redigeringar
> - UDF-definitionsuppdateringar
> - Enheter som skickar telemetri för första gången

## <a name="device-telemetry-limits"></a>Enhetsgränser telemetri

Gränserna nedan taket för det totala antalet alla meddelanden dina enheter kan skicka till din Azure Digital Twins-instans:

- 100 meddelanden per sekund

## <a name="next-steps"></a>Nästa steg

Om du vill prova ett exempel på Azure Digital Twins, gå till [Snabbstart för att hitta tillgängliga rum](./quickstart-view-occupancy-dotnet.md).