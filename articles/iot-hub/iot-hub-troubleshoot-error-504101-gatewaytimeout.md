---
title: Felsöka Azure IoT Hub-fel 504101 GatewayTimeout
description: Förstå hur du åtgärdar fel 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759565"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

I den här artikeln beskrivs orsakerna och lösningarna för **504101 GatewayTimeout-fel.**

## <a name="symptoms"></a>Symtom

NÃ¤s nÃ¤s fÃ¤ndes fÃ¤ndesã¤nstÃ¤nste du anropa en direkt metod frÃ¥n IoT Hub till en enhet misslyckas begäran med felet **504101 GatewayTimeout**.

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

IoT Hub påträffade ett fel och kunde inte bekräfta om den direkta metoden slutfördes innan tidsgränsen gick ut.

### <a name="cause-2"></a>Orsak 2

När du använder en tidigare version av Azure IoT C# SDK (<1.19.0) kan AMQP-länken mellan enheten och IoT Hub släppas tyst på grund av ett fel.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Utfärda ett nytt försök.

### <a name="solution-2"></a>Lösning 2

Uppgradera till den senaste versionen av Azure IOT C# SDK.