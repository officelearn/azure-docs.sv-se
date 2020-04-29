---
title: Gränser och kvoter IoT Plug and Play Preview | Microsoft Docs
description: Förstå de gränser, kvoter och begränsningar som gäller när du använder IoT Plug and Play Preview.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518174"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play för hands versioner, kvoter och begränsningar

I den här artikeln beskrivs de IoT-Plug and Play gränser, kvoter och begränsningar som gäller för den offentliga för hands versionen. Det finns befintliga [IoT Hub kvoter och begränsning](../iot-hub/iot-hub-devguide-quotas-throttling.md) som också gäller.

## <a name="iot-hub"></a>IoT Hub

För den allmänt tillgängliga för hands versionen gäller följande gränser och kvoter för en IoT-hubb:

| Begränsningar, begränsningar och begränsning | Värde | Obs! |
|-----|-----|-----|
| Antalet enhets kapacitets modeller (DCMs) eller gränssnitt som kan registreras per hubb | 1500 ||
| Maximalt antal gränssnitt som kan registreras per enhet | 40 ||
| Maximalt antal DCMs som kan registreras per enhet | 1 ||
| Maximal storlek för gränssnitt/DCM-fil | 512 kB ||
| Maximal storlek för ett gränssnitts namn | 256 tecken ||
| Maximal storlek för ett egenskaps namn  | 64 byte, 7 nivåer i djup (och den första nivån är reserverad för `$iotin`) | Tillåtna tecken: a-z, A-Z, 0-9 (inte som det första tecknet) och under streck. |
| Maximal storlek för ett egenskaps värde | 512 byte ||
| Maximal storlek för kommando namn | 100 byte ||
| Enhetens dubbla storlek | Samma som [IoT Hub gränser](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Lösnings-API-anrop över SKU (oavsett enheter) | 100 begär Anden per sekund ||

## <a name="model-repository"></a>Modell databas

För den allmänt tillgängliga för hands versionen gäller följande gränser och kvoter för en modell lagrings plats:

| Begränsningar, begränsningar och begränsning | Värde |
|-----|-----|
| Antal företags modell databaser per Azure Active Directory klient | 1 |
| Antal auktoriseringsregler per modell databas | 10  |
| Antal modeller (DCMs eller gränssnitt) per organisations modell databas| 1500  |
| Antalet modeller (DCMs eller gränssnitt) i den offentliga modell databasen per Azure Active Directory klient| 1500  |
| Antalet DCMs eller gränssnitt som tas bort i en företags modell databas | 10 frågor per sekund (frågor per sekund)|
| Antal modell databaser som skapas/uppdateras av en klient| 1 FRÅGOR PER SEKUND |
| Antal auktoriseringsregler som skapas/uppdateras/tas bort i en modell databas | 1 FRÅGOR PER SEKUND|
| Antalet DCMs som skapas i en företags modell databas | 10 FRÅGOR PER SEKUND |
| Antal gränssnitt som skapas i en företags modell databas | 10 FRÅGOR PER SEKUND|
| Antalet DCMs som skapas i den offentliga modell databasen | 10 FRÅGOR PER SEKUND|
| Antal gränssnitt som skapas i den offentliga modellens lagrings plats | 10 FRÅGOR PER SEKUND|

## <a name="parser-library"></a>Parsar bibliotek

Parsar biblioteket följer de gränser som gäller för det [digitala, dubbla definitions språket](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Nästa steg

Ett förslag till nästa steg är att lära dig hur du [ansluter till och interagerar med en IoT plug and Play-enhet](./howto-develop-solution.md).
