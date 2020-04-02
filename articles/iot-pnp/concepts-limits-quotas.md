---
title: Förhandsgranskning av begränsningar och kvoter IoT Plug and Play | Microsoft-dokument
description: Förstå de gränser, kvoter och begränsning som gäller när du använder IoT Plug and Play Preview.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518174"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play Preview gränser, kvoter och begränsningar

I den här artikeln beskrivs de IoT Plug- och Play-specifika gränser, kvoter och begränsning som gäller i den offentliga förhandsversionen. Det finns befintliga [IoT Hub-kvoter och begränsning](../iot-hub/iot-hub-devguide-quotas-throttling.md) som också gäller.

## <a name="iot-hub"></a>IoT Hub

För den offentliga förhandsversionen gäller följande begränsningar och kvoter för en IoT-hubb:

| Begränsningar, begränsningar och gasreglage | Värde | Anteckningar |
|-----|-----|-----|
| Antal enhetsfunktioner (DCMs) eller gränssnitt som kan registreras per hubb | 1500 ||
| Maximalt antal gränssnitt som kan registreras per enhet | 40 ||
| Maximalt antal DCM:er som kan registreras per enhet | 1 ||
| Maximal storlek på gränssnitt/DCM-fil | 512 kB ||
| Maximal storlek på ett gränssnittsnamn | 256 tecken ||
| Maximal storlek på ett egenskapsnamn  | 64 byte, 7 nivåer på djupet (och `$iotin`den första nivån är reserverad för) | Tillåtna tecken: a-z, A-Ö, 0-9 (inte som det första tecknet) och understreck. |
| Maximal storlek för ett egenskapsvärde | 512 byte ||
| Maximal storlek för ett kommandonamn | 100 byte ||
| Enhetens dubbla storlek | Samma som [IoT Hub Limits](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Lösning API-anrop över SKU (oavsett enheter) | 100 begäranden/sekund ||

## <a name="model-repository"></a>Modelldatabas

För den offentliga förhandsversionen gäller följande begränsningar och kvoter för en modelldatabas:

| Begränsningar, begränsningar och gasreglage | Värde |
|-----|-----|
| Antal företagsmodelldatabaser per Azure Active Directory-klient | 1 |
| Antal auktoriseringsnycklar per modelldatabas | 10  |
| Antal modeller (DCMs eller gränssnitt) per företagsmodelldatabas| 1500  |
| Antal modeller (DCMs eller gränssnitt) i den offentliga modelldatabasen per Azure Active Directory-klient| 1500  |
| Antal DCMs eller gränssnitt som tas bort i en företagsmodelldatabas | 10 frågor per sekund (QPS)|
| Antal modelldatabaser som skapas/uppdateras av en klient| 1 QPS (1 QPS) |
| Antal auktoriseringsnycklar som skapas/uppdateras/tas bort i en modelldatabas | 1 QPS (1 QPS)|
| Antal DCM:er som skapas i en företagsmodelldatabas | 10 QPS (10 QPS) |
| Antal gränssnitt som skapas i en företagsmodelldatabas | 10 QPS (10 QPS)|
| Antal dcms som skapas i den offentliga modelldatabasen | 10 QPS (10 QPS)|
| Antal gränssnitt som skapas i den offentliga modelldatabasen | 10 QPS (10 QPS)|

## <a name="parser-library"></a>Parserbibliotek

Tolkbiblioteket följer de gränser som gäller för det [digitala tvillingdefinitionsspråket](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Nästa steg

Ett förslag på nästa steg är att lära sig att [ansluta till och interagera med en IoT Plug and Play-enhet](./howto-develop-solution.md).
