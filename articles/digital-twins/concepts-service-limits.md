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
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961763"
---
# <a name="public-preview-service-limits"></a>Tjänstbegränsningar för allmän förhandsversion

Under den offentliga förhandsversionen har Azure Digital Twins följande temporära prenumeration, instans och hastighetsbegränsningar.

Dessa begränsningar finns för att förenkla lära dig mer om den nya tjänsten och dess många funktioner.

> [!NOTE]
> De här gränserna kommer ökar eller tas bort vid allmän tillgänglighet (GA).

## <a name="per-subscription-limits"></a>Gränserna per prenumeration

Den offentliga förhandsversionen, kan varje Azure-prenumeration skapa eller kör endast en Digital Twins för Azure-instans i taget.

> [!TIP]
> Om du tar bort din instans kan du skapa en ny.

## <a name="per-instance-limits"></a>Gränserna per instans

I sin tur kan varje Azure Digital Twins-instans ha:

- En **IoTHub** resurs.
- En **EventHub** slutpunkt för händelsetypen **DeviceMessage**.
- Upp till tre **EventHub**, **ServiceBus**, eller **EventGrid** slutpunkter på händelsetypen **SensorChange**, **SpaceChange** , **TopologyOperation**, eller **UdfCustom**.

## <a name="management-api-limits"></a>Hanterings-API-gränser

Hastighetsbegränsningar för begäran för API Management är:

- 100 förfrågningar i sekunden Management-API: et.
- Upp till 1 000 objekt som returneras av en fråga som API: et. 

> [!IMPORTANT]
> Om du överskrider gränsen på 1 000 objekt, du får ett fel och måste förenkla frågan.

## <a name="udf-rate-limits"></a>UDF hastighetsbegränsningar

Det totala antalet alla användardefinierad funktion-anrop som görs till din Azure Digital Twins-instans anger du följande begränsningar:

- 400 biblioteket klientanrop per sekund
- 100 **SendNotification** anrop per sekund

> [!NOTE]
> Följande åtgärder kan orsaka ytterligare hastighetsbegränsningar för att tillfälligt tillämpas:
> - Ändringar av metadata för objekt av topologi
> - Uppdateringar som görs UDF-definitionen
> - Enheter som skickar telemetri för första gången

## <a name="device-telemetry-limits"></a>Enhetsgränser telemetri

Det totala antalet alla meddelanden som dina enheter kan skicka till din instans av Azure Digital Twins gräns om följande begränsningar:

- 100 meddelanden per sekund

## <a name="next-steps"></a>Nästa steg

Om du vill prova ett exempel på Azure Digital Twins, gå till [Snabbstart för att hitta tillgängliga rum](./quickstart-view-occupancy-dotnet.md).