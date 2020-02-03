---
title: Stöd för Azure IoT Hub TLS
description: Metod tips i använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722894"
---
# <a name="tls-support-in-iot-hub"></a>TLS-stöd i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2.

TLS 1,0 och 1,1 betraktas som äldre och planeras för utfasning. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Vi rekommenderar starkt att du använder TLS 1,2 som den önskade TLS-versionen när du ansluter till IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Begränsa anslutningar till TLS 1,2 i din IoT Hub-resurs

För ökad säkerhet rekommenderar vi att du konfigurerar IoT-hubbar så att de *bara* tillåter klient anslutningar som använder TLS version 1,2 och för att framtvinga användning av [rekommenderade chiffer](#recommended-ciphers).

I detta syfte etablerar du en ny IoT Hub i någon av de [regioner som stöds](#supported-regions) och anger egenskapen `minTlsVersion` till `1.2` i Azure Resource Manager mallens resurs specifikation för IoT Hub:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Den skapade IoT Hub-resursen som använder den här konfigurationen kommer att neka enhets-och tjänst klienter som försöker ansluta med TLS-versionerna 1,0 och 1,1. På samma sätt kommer TLS-handskakningen att nekas om klientens HÄLSNINGs meddelande inte listar några av de [rekommenderade chiffer](#recommended-ciphers).

> [!NOTE]
> Egenskapen `minTlsVersion` är skrivskyddad och kan inte ändras när din IoT Hub-resurs har skapats. Det är därför viktigt att du testar och kontrollerar att *alla* IoT-enheter och-tjänster är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#recommended-ciphers) i förväg.

### <a name="supported-regions"></a>Regioner som stöds

IoT-hubbar som kräver användning av TLS 1,2 kan skapas i följande regioner:

* Östra USA
* Södra centrala USA
* Västra USA 2

> [!NOTE]
> Vid redundans fortsätter egenskapen `minTlsVersion` för IoT Hub att gälla i den geo-kopplade regionen efter redundansväxlingen.

### <a name="recommended-ciphers"></a>Rekommenderade chiffer

IoT-hubbar som är konfigurerade för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande rekommenderade chiffer:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Använd TLS 1,2 i dina IoT Hub SDK: er

Använd länkarna nedan för att konfigurera TLS 1,2 och tillåtna chiffer i IoT Hub klient-SDK: er.

| Språk | TLS 1,2 stöds | Dokumentation |
|----------|-------------------|---------------|
| C        | Ja               | [Länk](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Ja               | [Länk](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Ja               | [Länk](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Ja               | [Länk](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Ja               | [Länk](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Använd TLS 1,2 i installations programmet för IoT Edge

IoT Edge enheter kan konfigureras för att använda TLS 1,2 vid kommunikation med IoT Hub. För det här ändamålet använder du [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).