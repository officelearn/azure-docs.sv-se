---
title: Fel sökning av Azure IoT Hub-fel 504101 GatewayTimeout
description: Förstå hur du åtgärdar fel 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960677"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

I den här artikeln beskrivs orsaker och lösningar för **504101 GatewayTimeout** -fel.

## <a name="symptoms"></a>Symtom

Vid försök att anropa en direkt metod från IoT Hub till en enhet Miss lyckas begäran med felet **504101 GatewayTimeout**.

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

IoT Hub påträffade ett fel och kunde inte bekräfta om direkt metoden slutfördes innan tids gränsen nåddes.

### <a name="cause-2"></a>Orsak 2

När du använder en tidigare version av Azure IoT C# SDK (< 1.19.0) kan AMQP-länken mellan enheten och IoT Hub släppas tyst på grund av ett fel.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Skicka ett nytt försök.

### <a name="solution-2"></a>Lösning 2

Uppgradera till den senaste versionen av Azure IOT C# SDK.