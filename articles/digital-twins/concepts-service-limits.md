---
title: Azure Digital Twins som offentlig förhandsversion tjänstbegränsningar | Microsoft Docs
description: Förstå Azure Digital Twins offentliga förhandsversion tjänstbegränsningar
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212275"
---
# <a name="public-preview-service-limits"></a>Tjänstbegränsningar för allmän förhandsversion

Under **Public Preview**, Azure Digital Twins har tillfälliga prenumeration, instans och hastighetsbegränsningar som beskrivs nedan.

Dessa begränsningar finns för att förenkla lära dig mer om den nya tjänsten och dess många funktioner.

> [!NOTE]
> De här gränserna kommer att ökat och/eller tas bort av **allmänt tillgängliga** (**GA**).

## <a name="per-subscription-limits"></a>Gränserna per prenumeration

Under **Public Preview**, varje Azure-prenumeration kan skapa eller körs exakt en Digital Twins för Azure-instans i taget.

> [!TIP]
> Tar bort din instans kan du skapa en ny.

## <a name="per-instance-limits"></a>Gränserna per instans

I sin tur kan varje Azure Digital Twins-instans ha:

- En **IoTHub** resurs
- En **EventHub** slutpunkt för händelsetypen **DeviceMessage**
- Upp till tre **EventHub**, **ServiceBus**, eller **EventGrid** slutpunkter typ av händelse **SensorChange**, **SpaceChange** , **TopologyOperation**, eller **UdfCustom**

## <a name="management-api-limits"></a>Hanterings-API-gränser

Hastighetsbegränsningar för begäran för API Management är:

- 100 begäranden per sekund till Management-API
- En enda API Management-fråga kan gå tillbaka upp till 1 000 objekt

> [!IMPORTANT]
> Om du överskrider gränsen på 1000-objektet, du får ett felmeddelande och behöver du förenkla frågan.

## <a name="udf-rate-limits"></a>UDF hastighetsbegränsningar

Det totala antalet alla användardefinierad funktion-anrop som görs till din Azure Digital Twins-instans anger du följande begränsningar:

- 400 biblioteket klientanrop per sekund
- 100 **SendNotification** anrop per sekund

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