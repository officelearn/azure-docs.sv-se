---
title: Azure Digital Twins som offentlig förhandsversion tjänstbegränsningar | Microsoft Docs
description: Förstå Azure Digital Twins offentliga förhandsversion tjänstbegränsningar.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalthermsft
ms.openlocfilehash: 7d9686b9bcc6cb89fabf4fdaa79bf5b8c6c45ddc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020633"
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

- Exakt en **IoTHub** resurs.
- Exakt en **EventHub** slutpunkt för händelsetypen **DeviceMessage**.
- Upp till tre **EventHub**, **ServiceBus**, eller **EventGrid** slutpunkter på händelsetypen **SensorChange**, **SpaceChange** , **TopologyOperation**, eller **UdfCustom**.

> [!NOTE]
> Vissa parametrar som definieras vanligtvis skapa ovan Azure IoT-enheter krävs inte allmänt tillgängliga förhandsversionen.
> - Läs den [Swagger-referensdokumentation](./how-to-use-swagger.md) för de senaste API-specifikationerna.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Twins Management API-gränser

Begäran hastighetsbegränsningar för ditt Azure-API för hantering av digitala Twins är:

- 100 begäranden per sekund till Azure-API för hantering av digitala Twins.
- Upp till 1 000 objekt som returneras av en enda Azure Digital Twins Management-API-fråga.

> [!IMPORTANT]
> Om du överskrider gränsen på 1 000 objekt, du får ett fel och måste förenkla frågan.

## <a name="user-defined-functions-rate-limits"></a>Användardefinierade funktioner hastighetsbegränsningar

Det totala antalet alla användardefinierad funktion-anrop som görs till din Azure Digital Twins-instans anger du följande begränsningar:

- 400 biblioteket klientanrop per sekund
- 100 **SendNotification** anrop per sekund

> [!NOTE]
> Följande åtgärder kan orsaka ytterligare hastighetsbegränsningar för att tillfälligt tillämpas:
> - Ändringar av metadata för objekt av topologi
> - Uppdateringar som görs till den användardefinierade funktionen-definitionen
> - Enheter som skickar telemetri för första gången

## <a name="device-telemetry-limits"></a>Enhetsgränser telemetri

Det totala antalet alla meddelanden som dina enheter kan skicka till din instans av Azure Digital Twins gräns om följande begränsningar:

- 100 meddelanden per sekund

## <a name="next-steps"></a>Nästa steg

Om du vill prova ett exempel på Azure Digital Twins, gå till [Snabbstart för att hitta tillgängliga rum](./quickstart-view-occupancy-dotnet.md).