---
title: Stöd för Azure IoT Hub TLS
description: Metod tips i använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: jlian
ms.openlocfilehash: c9dd66fe9d71f0a857e4b0821190bceb5d6d4680
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628806"
---
# <a name="tls-support-in-iot-hub"></a>TLS-stöd i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2.

TLS 1,0 och 1,1 betraktas som äldre och planeras för utfasning. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Vi rekommenderar starkt att du använder TLS 1,2 som den önskade TLS-versionen när du ansluter till IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>TLS 1,2-tvång är tillgängligt i utvalda regioner

För ytterligare säkerhet konfigurerar du IoT-hubbarna så att de *bara* tillåter klient anslutningar som använder TLS version 1,2 och för att framtvinga användningen av [chiffersviter](#cipher-suites). Den här funktionen stöds bara i följande regioner:

* East US
* USA, södra centrala
* USA, västra 2
* US Gov, Arizona
* US Gov, Virginia

I detta syfte etablerar du en ny IoT Hub i någon av de regioner som stöds och anger `minTlsVersion` egenskapen till `1.2` i Azure Resource Manager mallens resurs specifikation för IoT Hub:

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

Den skapade IoT Hub-resursen som använder den här konfigurationen kommer att neka enhets-och tjänst klienter som försöker ansluta med TLS-versionerna 1,0 och 1,1. På samma sätt kommer TLS-handskakningen att nekas om `ClientHello` meddelandet inte listar några av de [rekommenderade chifferna](#cipher-suites).

> [!NOTE]
> `minTlsVersion`Egenskapen är skrivskyddad och kan inte ändras när IoT Hub resurs har skapats. Det är därför viktigt att du testar och kontrollerar att *alla* IoT-enheter och-tjänster är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#cipher-suites) i förväg.
> 
> Vid redundans `minTlsVersion` fortsätter egenskapen för dina IoT Hub att vara effektiv i den geo-kopplade regionen efter redundansväxlingen.

## <a name="cipher-suites"></a>Chiffersviter

IoT-hubbar som är konfigurerade för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande rekommenderade chiffersviter:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

För IoT-hubbar som inte har kon figurer ATS för TLS 1,2 fungerar TLS 1,2 fortfarande med följande chiffersviter:

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

En-klient kan föreslå en lista över höga chiffersviter som ska användas under `ClientHello` . Men vissa av dem kanske inte stöds av IoT Hub (till exempel `ECDHE-ECDSA-AES256-GCM-SHA384` ). I det här fallet försöker IoT Hub följa prioriteten för klienten, men kan till och med förhandla ned cipher Suite med `ServerHello` .

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Använd TLS 1,2 i dina IoT Hub SDK: er

Använd länkarna nedan för att konfigurera TLS 1,2 och tillåtna chiffer i IoT Hub klient-SDK: er.

| Språk | Versioner som stöder TLS 1,2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 eller senare            | [Operationsföljdslänkkod](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 eller senare             | [Operationsföljdslänkkod](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 eller senare            | [Operationsföljdslänkkod](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 eller senare            | [Operationsföljdslänkkod](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 eller senare            | [Operationsföljdslänkkod](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Använd TLS 1,2 i installations programmet för IoT Edge

IoT Edge enheter kan konfigureras för att använda TLS 1,2 vid kommunikation med IoT Hub. För det här ändamålet använder du [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Enhetsautentisering

Efter en lyckad TLS-handskakning kan IoT Hub autentisera en enhet med hjälp av en symmetrisk nyckel eller ett X. 509-certifikat. För certifikatbaserad autentisering kan detta vara alla X. 509-certifikat, inklusive ECC. IoT Hub validerar certifikatet mot det tumavtryck eller den certifikat utfärdare (CA) som du anger. Mer information finns i [X. 509-certifikat som stöds](iot-hub-devguide-security.md#supported-x509-certificates).
