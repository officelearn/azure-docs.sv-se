---
title: Konfigurera en anpassad enhetsmodell – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar en anpassad enhet-modell i Enhetssimulering lösningsaccelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967552"
---
# <a name="device-models"></a>Enhetsmodeller

När du konfigurerar en simulering som du kan välja att använda en befintlig enhetsmodell med en fördefinierad uppsättning sensorer eller skapa en anpassad enhetsmodell med ditt val av simulerade sensorer. Anpassade sensorer kan du nära efterlikna din verkliga enheter.

## <a name="pre-configured-device-models"></a>Förkonfigurerade enhetsmodeller

Lösningsaccelerator Enhetssimulering tillhandahåller tre förkonfigurerade enhetsmodeller: Chillers och hissar lastbilar.

Förkonfigurerade enhetsmodeller ha flera sensorer med avancerad beteenden som definierats i en JavaScript-fil. Du kan inte konfigurera dessa beteenden i webbgränssnittet.

I följande tabell visas konfigurationerna för varje förkonfigurerad enhetsmodell:

| Enhetsmodell  | Sensor           | Enhet  |
| ------------- | ---------------- | ----- |
| Kylaggregat       | fuktighet         | %     |
|               | tryck         | psig  |
|               | temperatur      | F     |
| Elevator      | Våning            |       |
|               | Vibrationer        | mm    |
|               | Temperatur      | F     |
| Lastbil         | Latitud         |       |
|               | Longitud        |       |
|               | hastighet            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Anpassade enhetsmodeller

Anpassade modeller kan du modellera sensorer som närmare modellera dina egna enheter. En anpassad enhet kan ha upp till 10 anpassade sensorer.

När du väljer modelltypen anpassad enhet kan du lägga till nya sensorer genom att klicka på **+ Lägg till sensorn**.

[![Konfigurera sensorer](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Anpassade sensorer har följande egenskaper:

| Fält     | Beskrivning |
| --------- | ----------- |
| Sensornamnet | Ett eget namn för sensor som **temperatur** eller **hastighet**.  |
| Beteende  | Beteenden aktivera telemetridata att skilja sig från ett meddelande till nästa att simulera verkliga data. **Öka** ökar värdet genom en i varje meddelande som skickas från det lägsta värdet. När det maximala värdet är uppfyllt, sedan startar den igen på det minsta värdet. **Minska** fungerar på samma sätt som **ökningen** men antal. Den **slumpmässiga** beteende genererar ett slumpmässigt värde mellan det minsta värdet och högsta värden. |
| Minvärde | Det lägsta numret i din godkända intervallet. |
| Maxvärde | Det högsta värdet i din godkända intervallet. |
| Enhet      | Måttenheten för sensorn, till exempel ° F eller MPH. |

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar en anpassad enhetsmodell för en simulering. Därefter kan du utforska några av de andra [IoT-Lösningsacceleratorer](about-iot-accelerators.md).