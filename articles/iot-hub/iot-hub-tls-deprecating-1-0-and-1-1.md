---
title: Inaktuell TLS 1,0 och 1,1 i IoT Hub och enhets etablerings tjänsten (DPS) | Microsoft Docs
description: Rikt linjer angående utfasning av TLS 1,0 och 1,1 och stöd för chiffrering i IoT Hub och DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402786"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Utfasningen av TLS 1,0 och 1,1 i IoT Hub-och Device Provisioning-tjänsten

För att tillhandahålla den bästa krypteringen, IoT Hub och Device Provisioning-tjänsten (DPS) flyttas till Transport Layer Security (TLS) 1,2 som krypterings metod för IoT-enheter och-tjänster. 

## <a name="supported-ciphers"></a>Chiffer som stöds

Tids linjen för tillgänglighet för olika chiffer som används i TLS-handskakning är följande:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (stöds för närvarande)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (stöds i andra halvan av 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (stöds i andra halvan av 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (stöds i andra halvan av 2020)


## <a name="customer-feedback"></a>Kundfeedback

Även om TLS 1,2-tvång är ett branschledande krypterings alternativ och kommer att aktive ras som planerat, vill vi fortfarande höra från kunder om sina olika distributioner och problem som inför TLS 1,2. För det här ändamålet kan du skicka dina kommentarer till [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
