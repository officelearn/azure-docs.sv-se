---
title: Inaktuella TLS 1.0 och 1.1 i IoT Hub and Device Provisioning Service (DPS) | Microsoft-dokument
description: Riktlinjer för utfasning av TLS 1.0 och 1.1 och chiffer som stöds i IoT Hub och DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402786"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Utfasning av TLS 1.0 och 1.1 i IoT Hub och enhetsetableringstjänst

För att tillhandahålla förstklassig kryptering flyttas DPS (IoT Hub and Device Provisioning Service) till Transport Layer Security (TLS) 1.2 som krypteringsmekanism för IoT-enheter och -tjänster. 

## <a name="supported-ciphers"></a>Chiffer som stöds

Tidslinjen för tillgänglighet av olika chiffer som används i TLS handslag är följande:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (stöds för närvarande)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (kommer att stödjas under andra halvåret 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (kommer att stödjas under andra halvåret 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (kommer att stödjas under andra halvåret 2020)


## <a name="customer-feedback"></a>Feedback från kunder

Medan TLS 1.2 verkställighet är en branschomfattande bästa i klassen kryptering val och kommer att aktiveras som planerat, vi fortfarande vill höra från kunder om deras specifika distributioner och svårigheter att anta TLS 1.2. För detta ändamål kan du [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)skicka dina kommentarer till .
