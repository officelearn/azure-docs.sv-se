---
title: Förstå komponenter i IoT Plug and Play-modeller | Microsoft Docs
description: Förstå skillnaden mellan IoT Plug and Play DTDL-modeller som använder komponenter och modeller som inte använder komponenter.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c4a32a5c929e74332e85ceb6f4cff787e237e385
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069653"
---
# <a name="iot-plug-and-play-components-in-models"></a>IoT Plug and Play-komponenter i modeller

I IoT Plug and Play-konventioner är en enhet en IoT Plug and Play-enhet om den presenterar sitt digitala DTDL-modell-ID när den ansluter till en IoT-hubb.

I följande kodfragment visas några exempel på modell-ID: n:

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

Även om modellen inte uttryckligen definierar en komponent, fungerar den som om det finns en enda komponent med alla telemetri, egenskaper och kommando definitioner.

Följande skärm bild visar hur modellen visas i Azure IoT Explorer-verktyget:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Standard komponent i Azure IoT Explorer":::

Modell-ID: t lagras i en enhets dubbla-egenskap som följande skärm bild visar:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Modell-ID i digital, dubbel egenskap":::

En DTDL-modell utan komponenter är en användbar förenkling för en enhet med en enda uppsättning telemetri, egenskaper och kommandon. En modell som inte använder komponenter gör det enkelt att migrera en befintlig enhet till en IoT Plug and Play-enhet – du skapar en DTDL-modell som beskriver din faktiska enhet utan att behöva definiera några komponenter.

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

Den här modellen har tre komponenter som definieras i avsnittet innehåll – två `Thermostat` komponenter och en `DeviceInformation` komponent. Det finns även en standard rot komponent.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om modell komponenter finns här några ytterligare resurser:

- [Digitalt flätade Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell databaser](./concepts-model-repository.md)