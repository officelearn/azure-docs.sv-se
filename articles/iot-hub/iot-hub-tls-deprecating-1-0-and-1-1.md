---
title: Deprecating TLS 1.0 och 1.1 i IoT Hub | Microsoft-dokument
description: Riktlinjer för utfasning av TLS 1.0 och 1.1 och chiffer som stöds i IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381302"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Utfasning av TLS 1.0 och 1.1 i IoT Hub

För att tillhandahålla förstklassig kryptering flyttar IoT Hub till Transport Layer Security (TLS) 1.2 som krypteringsmekanism för IoT-enheter och -tjänster. 

## <a name="timeline"></a>Tidslinje

IoT Hub kommer att fortsätta att stödja TLS 1.0/1.1 tills vidare. Vi rekommenderar dock att alla kunder migrerar till TLS 1.2 så snart som möjligt.

## <a name="supported-ciphers"></a>Chiffer som stöds

Tidslinjen för tillgänglighet av olika chiffer som används i TLS handslag är följande:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (stöds för närvarande)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (kommer att stödjas under andra halvåret 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (kommer att stödjas under andra halvåret 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (kommer att stödjas under andra halvåret 2020)

## <a name="customer-feedback"></a>Feedback från kunder

Medan TLS 1.2 verkställighet är en branschomfattande bästa i klassen kryptering val och kommer att aktiveras som planerat, vi fortfarande vill höra från kunder om deras specifika distributioner och svårigheter att anta TLS 1.2. För detta ändamål kan du [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)skicka dina kommentarer till .
