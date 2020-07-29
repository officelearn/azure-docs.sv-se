---
title: Gränser och kvoter IoT Plug and Play Preview | Microsoft Docs
description: Förstå de gränser, kvoter och begränsningar som gäller när du använder IoT Plug and Play Preview.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337406"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play för hands versioner, kvoter och begränsningar

I den här artikeln beskrivs de IoT-Plug and Play gränser, kvoter och begränsningar som gäller för den offentliga för hands versionen. Det finns befintliga [IoT Hub kvoter och begränsning](../iot-hub/iot-hub-devguide-quotas-throttling.md) som också gäller.

## <a name="iot-hub"></a>IoT Hub

För den allmänt tillgängliga för hands versionen gäller följande gränser och kvoter för en IoT-hubb:

| Begränsningar, begränsningar och begränsning | Värde | Obs! |
|-----|-----|-----|
| Antal gränssnitt som kan registreras per hubb | 1500 ||
| Maximal storlek för ett komponent namn | 1-64 tecken | Tillåtna tecken: a-z, A-Z, 0-9 (inte som det första tecknet) och under streck (inte som det första eller sista tecknet). |
| Maximal storlek för ett egenskaps namn | 1-64 tecken | Tillåtna tecken: a-z, A-Z, 0-9 (inte som det första tecknet) och under streck (inte som det första eller sista tecknet). |
| Maximal storlek för ett egenskaps värde | Samma som digital flätad Definition Language- [egenskap](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) | 5 nivåer i djup och får inte vara en matris eller ett komplext schema som innehåller en matris |
| Maximal storlek för kommando namn | 1-64 tecken | Tillåtna tecken: a-z, A-Z, 0-9 (inte som det första tecknet) och under streck (inte som det första eller sista tecknet).|
| Enhetens dubbla storlek | Samma som [IoT Hub gränser](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Parsar bibliotek

Parser-biblioteket följer de gränser som gäller för [digitala dubbla definitions språk](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att granska [IoT plug and Play-arkitekturen](concepts-architecture.md).
