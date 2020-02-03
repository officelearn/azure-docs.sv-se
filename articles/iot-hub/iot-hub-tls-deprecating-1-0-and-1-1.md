---
title: Inaktuell TLS 1,0 och 1,1 i IoT Hub och enhets etablerings tjänsten (DPS) | Microsoft Docs
description: Rikt linjer angående utfasning av TLS 1,0 och 1,1 och stöd för chiffrering i IoT Hub och DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722790"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Utfasningen av TLS 1,0 och 1,1 i IoT Hub-och Device Provisioning-tjänsten

För att tillhandahålla den bästa krypteringen, IoT Hub och Device Provisioning-tjänsten (DPS) flyttas till Transport Layer Security (TLS) 1,2 som krypterings metod för IoT-enheter och-tjänster. Därför kommer äldre stöd för TLS 1,0 och TLS 1,1 samt flera icke-rekommenderade äldre chiffer att bli föråldrade den **1 juli 2020**.


## <a name="impact"></a>Påverkan
Beroende på kundernas särskilda omständigheter och konfigurationer kan utfasningen av TLS 1,0 och 1,1 och icke-rekommenderade äldre chiffer vara en påverkan på dina IoT-enheter och-tjänster som kommunicerar med IoT Hub eller DPS. I vissa fall kommer enheter och tjänster som inte är kompatibla med dessa ändringar inte att kunna ansluta till IoT Hub eller DPS efter det datum då detta kapades.


## <a name="supported-ciphers"></a>Chiffer som stöds

Endast följande chiffer tillåts under TLS-handskakning:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Kundfeedback

Även om TLS 1,2-tvång är ett branschledande krypterings alternativ och kommer att aktive ras som planerat, vill vi fortfarande höra från kunder om sina olika distributioner och problem som inför TLS 1,2. För det här ändamålet kan du skicka dina kommentarer till [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).