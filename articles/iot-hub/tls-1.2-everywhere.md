---
title: Utfasnings-TLS 1,0 och 1,1 i IoT Hub-och Device Provisioning-tjänsten (DPS) | Microsoft Docs
description: Rikt linjer angående utfasning av TLS 1,0 och 1,1 och stöd för chiffrering i IoT Hub och DPS.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485915"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>TLS 1,0 och 1,1-utfasning i IoT Hub-och Device Provisioning-tjänsten

För att tillhandahålla den bästa krypteringen, IoT Hub och Device Provisioning-tjänsten (DPS) flyttas till Transport Layer Security (TLS) 1,2 som krypterings metod för IoT-enheter och-tjänster. Därför kommer äldre stöd för TLS 1,0 och TLS 1,1 samt flera icke-rekommenderade äldre chiffer att bli föråldrade den **1 juli 2020**.


## <a name="impact"></a>Påverkan
Beroende på kundernas särskilda omständigheter och konfigurationer kan utfasningen av TLS 1,0 och 1,1 och icke-rekommenderade äldre chiffer vara en påverkan på dina IoT-enheter och-tjänster som kommunicerar med IoT Hub eller DPS. I vissa fall kommer enheter och tjänster som inte är kompatibla med dessa ändringar inte att kunna ansluta till IoT Hub eller DPS efter det datum då detta kapades.


## <a name="supported-ciphers"></a>Chiffer som stöds

Endast följande chiffer kommer att tillåtas under TLS-handskakning:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Kundfeedback

Även om TLS 1.2-framtvingande är ett branschledande krypteringsalternativ och kommer att aktiveras som planerat vill vi fortfarande höra från kunder om deras olika distributioner och problem med att införa TLS 1.2. För det här ändamålet kan du skicka dina kommentarer till [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).