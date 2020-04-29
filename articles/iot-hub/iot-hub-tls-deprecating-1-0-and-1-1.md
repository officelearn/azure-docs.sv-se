---
title: Inaktuell TLS 1,0 och 1,1 i IoT Hub | Microsoft Docs
description: Rikt linjer angående utfasning av TLS 1,0 och 1,1 och stöd för chiffrering i IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381302"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Utfasning av TLS 1,0 och 1,1 i IoT Hub

IoT Hub flyttar till Transport Layer Security (TLS) 1,2 som krypterings metod för IoT-enheter och-tjänster för att tillhandahålla den bästa krypteringen. 

## <a name="timeline"></a>Tidslinje

IoT Hub fortsätter att stödja TLS 1.0/1.1 innan ytterligare meddelande visas. Vi rekommenderar dock att alla kunder migrerar till TLS 1,2 så snart som möjligt.

## <a name="supported-ciphers"></a>Chiffer som stöds

Tids linjen för tillgänglighet för olika chiffer som används i TLS-handskakning är följande:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (stöds för närvarande)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (stöds i andra halvan av 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (stöds i andra halvan av 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (stöds i andra halvan av 2020)

## <a name="customer-feedback"></a>Kundfeedback

Även om TLS 1,2-tvång är ett branschledande krypterings alternativ och kommer att aktive ras som planerat, vill vi fortfarande höra från kunder om sina olika distributioner och problem som inför TLS 1,2. För det här ändamålet kan du skicka dina kommentarer [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)till.
