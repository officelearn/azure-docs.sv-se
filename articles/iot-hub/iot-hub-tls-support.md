---
title: Stöd för Azure IoT Hub TLS
description: Metod tips i använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 5b9f6b993b0d0f527d041b4ee055bf51fefa1253
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848253"
---
# <a name="tls-support-in-iot-hub"></a>TLS-stöd i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2.

TLS 1,0 och 1,1 betraktas som äldre och planeras för utfasning. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Vi rekommenderar starkt att du använder TLS 1,2 som den önskade TLS-versionen när du ansluter till IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Begränsa anslutningar till TLS 1,2 i din IoT Hub-resurs

För ökad säkerhet rekommenderar vi att du konfigurerar IoT-hubbar så att de *bara* tillåter klient anslutningar som använder TLS version 1,2 och för att framtvinga användning av [rekommenderade chiffer](#recommended-ciphers).

I detta syfte etablerar du en ny IoT Hub i någon av de [regioner som stöds](#supported-regions) och anger `minTlsVersion` egenskapen till `1.2` i Azure Resource Manager mallens resurs specifikation för IoT Hub:

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
> `minTlsVersion`Egenskapen är skrivskyddad och kan inte ändras när IoT Hub resurs har skapats. Det är därför viktigt att du testar och kontrollerar att *alla* IoT-enheter och-tjänster är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#recommended-ciphers) i förväg.

## <a name="supported-regions"></a>Regioner som stöds

IoT-hubbar som kräver användning av TLS 1,2 kan skapas i följande regioner:

* USA, östra
* USA, södra centrala
* USA, västra 2
* US Gov, Arizona
* US Gov, Virginia

> [!NOTE]
> Vid redundans `minTlsVersion` fortsätter egenskapen för dina IoT Hub att vara effektiv i den geo-kopplade regionen efter redundansväxlingen.

## <a name="recommended-ciphers"></a>Rekommenderade chiffer

IoT-hubbar som är konfigurerade för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande rekommenderade chiffer:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

För IoT-hubbar som inte har kon figurer ATS för TLS 1,2 fungerar TLS 1,2 fortfarande med följande chiffer:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Använd TLS 1,2 i dina IoT Hub SDK: er

Använd länkarna nedan för att konfigurera TLS 1,2 och tillåtna chiffer i IoT Hub klient-SDK: er.

| Språk | Versioner som stöder TLS 1,2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 eller senare            | [Länk](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 eller senare             | [Länk](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 eller senare            | [Länk](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 eller senare            | [Länk](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 eller senare            | [Länk](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Använd TLS 1,2 i installations programmet för IoT Edge

IoT Edge enheter kan konfigureras för att använda TLS 1,2 vid kommunikation med IoT Hub. För det här ändamålet använder du [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).