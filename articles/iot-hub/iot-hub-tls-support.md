---
title: Support för Azure IoT Hub TLS
description: Metodtips för att använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409515"
---
# <a name="tls-support-in-iot-hub"></a>TLS-stöd i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och -tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen versionerna 1.0, 1.1 och 1.2.

TLS 1.0 och 1.1 anses vara äldre och planeras för utfasning. Mer information finns i [Deprecating TLS 1.0 och 1.1 för IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Vi rekommenderar starkt att du använder TLS 1.2 som önskad TLS-version när du ansluter till IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Begränsa anslutningar till TLS 1.2 i IoT Hub-resursen

För ökad säkerhet rekommenderas att konfigurera IoT Hubs *så* att klientanslutningar som använder TLS version 1.2 tillåts och att använda [rekommenderade chiffer](#recommended-ciphers).

För detta ändamål, etablera en ny IoT Hub i `minTlsVersion` någon `1.2` av de regioner [som stöds](#supported-regions) och ange egenskapen till i din Azure Resource Manager mall IoT hub resource specifikation:

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

Den skapade IoT Hub-resursen med den här konfigurationen kommer att avvisa enhets- och tjänstklienter som försöker ansluta med TLS-versionerna 1.0 och 1.1. På samma sätt kommer TLS-handskakningen att vägras om klienten HELLO-meddelandet inte listar något av de [rekommenderade chifferna](#recommended-ciphers).

> [!NOTE]
> Egenskapen `minTlsVersion` är skrivskyddad och kan inte ändras när IoT Hub-resursen har skapats. Det är därför viktigt att du testar och verifierar att *alla* dina IoT-enheter och -tjänster är kompatibla med TLS 1.2 och de [rekommenderade chifferna](#recommended-ciphers) i förväg.

### <a name="supported-regions"></a>Regioner som stöds

IoT Hubs som kräver användning av TLS 1.2 kan skapas i följande regioner:

* USA, östra
* USA, södra centrala
* USA, västra 2
* US Gov, Arizona
* US Gov, Virginia

> [!NOTE]
> Vid redundans `minTlsVersion` förblir egenskapen för din IoT Hub effektiv i den geoparerade regionen efter redundans.

### <a name="recommended-ciphers"></a>Rekommenderade chiffer

IoT Hubs som är konfigurerade för att acceptera endast TLS 1.2 kommer också att genomdriva användningen av följande rekommenderade chiffer:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Använd TLS 1.2 i IoT Hub-SDK:er

Använd länkarna nedan för att konfigurera TLS 1.2 och tillåtna chiffer i IoT Hub-klient-SDK:er.

| Språk | Versioner med stöd för TLS 1.2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 eller nyare            | [Länk](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 eller nyare             | [Länk](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 eller nyare            | [Länk](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 eller nyare            | [Länk](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 eller nyare            | [Länk](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Använd TLS 1.2 i ioT Edge-konfigurationen

IoT Edge-enheter kan konfigureras för att använda TLS 1.2 när du kommunicerar med IoT Hub. Använd därför [dokumentationssidan för IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).