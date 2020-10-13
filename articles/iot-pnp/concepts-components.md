---
title: Förstå komponenter i IoT Plug and Play-modeller | Microsoft Docs
description: Förstå skillnaden mellan IoT Plug and Play DTDL-modeller som använder komponenter och modeller som inte använder komponenter.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7b24dd9cdbd11b56545f85ac233665f8fa4adfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574287"
---
# <a name="iot-plug-and-play-components-in-models"></a>IoT Plug and Play-komponenter i modeller

Enligt IoT Plug and Play-konventionerna är en enhet en IoT Plug and Play-enhet om den presenterar ID:t för sin DTDL-modell (Digital Twins Definition Language) när den ansluter till en IoT-hubb.

Följande kodfragment innehåller exempel på några modell-ID:n:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Inga komponenter

En enkel modell använder inte inbäddade eller överlappande komponenter. Avsnittet innehåller rubrik information och ett innehåll för att definiera telemetri, egenskaper och kommandon.

I följande exempel visas en del av en enkel modell som inte använder komponenter:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Även om modellen inte uttryckligen definierar en komponent, fungerar den som om det finns en enda _standard komponent_, med alla telemetri, egenskaper och kommando definitioner.

Följande skärm bild visar hur modellen visas i Azure IoT Explorer-verktyget:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Standard komponent i Azure IoT Explorer":::

Modell-ID: t lagras i en enhets dubbla-egenskap som följande skärm bild visar:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Standard komponent i Azure IoT Explorer":::

En DTDL-modell utan komponenter är en användbar förenkling för en enhet eller IoT Edge modul med en enda uppsättning telemetri, egenskaper och kommandon. En modell som inte använder komponenter gör det enkelt att migrera en befintlig enhet eller modul till en IoT Plug and Play enhet eller modul – du skapar en DTDL-modell som beskriver din faktiska enhet eller modul utan att behöva definiera några komponenter.

> [!TIP]
> En modul kan vara en enhet [modul] (.. /IoT-Hub/IoT-Hub-DevGuide-module-Twins.MD eller en [IoT Edge-modul](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Flera komponenter

Med komponenter kan du bygga ett modell gränssnitt som en sammansättning av andra gränssnitt.

Till exempel definieras [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -gränssnittet som en modell. Du kan använda det här gränssnittet som en eller flera komponenter när du definierar [temperatur styrenhets modellen](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). I följande exempel kallas dessa komponenter `thermostat1` och `thermostat2` .

För en DTDL-modell med flera komponenter finns det två eller flera komponent avsnitt. Varje avsnitt har `@type` angetts till `Component` och uttryckligen refererar till ett schema som visas i följande kodfragment:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Den här modellen har tre komponenter som definieras i avsnittet innehåll – två `Thermostat` komponenter och en `DeviceInformation` komponent. Det finns också en standard komponent.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om modell komponenter finns här några ytterligare resurser:

- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)
- [Digitalt flätade Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell databaser](./concepts-model-repository.md)