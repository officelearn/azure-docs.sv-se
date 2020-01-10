---
title: Stöd för Azure IoT Hub TLS
description: Metod tips i använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.custom: Azure IoT Hub TLS
ms.openlocfilehash: 62fdfc4277b44a502206eb37466e6409521186a5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75839535"
---
# <a name="tls-support-in-iot-hub"></a>TLS-stöd i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2.

TLS 1,0 och 1,1 betraktas som äldre och [planeras för utfasning](./tls-1.2-everywhere.md). Vi rekommenderar därför att du använder TLS 1,2 som den önskade TLS-versionen när du ansluter till IoT Hub.


## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Begränsa anslutningar till TLS 1,2 i din IoT Hub-resurs

För ökad säkerhet rekommenderar vi att du konfigurerar IoT-hubbar så att de _bara_ tillåter klient anslutningar som använder TLS version 1,2 och för att framtvinga användning av [rekommenderade chiffer](#recommended-ciphers).

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

Observera att egenskapen `minTlsVersion` är skrivskyddad och kan inte ändras när din IoT Hub-resurs har skapats. Det är därför viktigt att du testar och kontrollerar att _alla_ IoT-enheter och-tjänster är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#recommended-ciphers) i förväg.


### <a name="supported-regions"></a>Regioner som stöds

IoT-hubbar som kräver användning av TLS 1,2 kan skapas i följande regioner:

* USA, östra
* USA, södra centrala
* USA, västra 2

> [!NOTE]
> Vid redundans fortsätter egenskapen `minTlsVersion` för IoT Hub att gälla i den geo-kopplade regionen efter redundansväxlingen.



### <a name="recommended-ciphers"></a>Rekommenderade chiffer

IoT-hubbar som är konfigurerade för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande rekommenderade chiffer:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Använd TLS 1,2 i installations programmet för IoT Edge

IoT Edge enheter kan konfigureras för att använda TLS 1,2 vid kommunikation med IoT Hub. För det här ändamålet använder du [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).