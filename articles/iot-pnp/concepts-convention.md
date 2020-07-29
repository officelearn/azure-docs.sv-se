---
title: IoT Plug and Play-konventioner | Microsoft Docs
description: Beskrivning av konventionerna IoT Plug and Play förväntar sig att enheter ska användas när de skickar telemetri och egenskaper och hanterar kommandon och egenskaps uppdateringar.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b06edb83e85ef6d56cf8bf8f91551fe99535eba1
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352420"
---
# <a name="iot-plug-and-play-conventions"></a>IoT Plug and Play-konventioner

IoT Plug and Play Preview-enheter bör följa en uppsättning konventioner när de utbyter meddelanden med en IoT-hubb. IoT Plug and Play Preview-enheter använder MQTT-protokollet för att kommunicera med IoT Hub.

Du beskriver telemetri, egenskaper och kommandon som en IoT Plug and Play-enhet implementerar med en digital- _modell_med en [definitions språk v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) . Det finns två typer av modeller som anges i den här artikeln:

- **Ingen komponent** -en modell utan komponenter. Modellen förklarar telemetri, egenskaper och kommandon som egenskaper på den högsta nivån i avsnittet innehåll i huvud gränssnittet.
- **Flera komponenter** – en modell som består av två eller flera gränssnitt. Ett huvud gränssnitt med telemetri, egenskaper och kommandon. Ett eller flera gränssnitt som har deklarerats som komponenter med ytterligare telemetri, egenskaper och kommandon.

Mer information finns i [IoT plug and Play-komponenter i modeller](concepts-components.md).

## <a name="model-discovery"></a>Modellidentifiering

För att meddela att modellen den implementerar, innehåller en IoT Plug and Play-enhet modell-ID: t i anslutnings paketet MQTT genom att lägga till `model-id` i `USERNAME` fältet.

För att identifiera vilken modell en enhet implementerar kan en tjänst Hämta modell-ID: t från:

- Enhetens dubbla `modelId` fält.
- Det digitala dubbla `$metadata.$model` fältet.
- Ett digitalt meddelande om ändring av två meddelanden.

## <a name="telemetry"></a>Telemetri

Telemetri som skickas från ingen komponent enhet kräver inga extra metadata. Systemet lägger till `dt-dataschema` egenskapen.

Telemetri som skickas från en enhet med flera komponenter måste läggas till `$.sub` som en meddelande egenskap. Systemet lägger till `dt-subject` egenskaperna och `dt-dataschema` .

## <a name="read-only-properties"></a>Skrivskyddade egenskaper

### <a name="sample-no-component-read-only-property"></a>Exempel på en skrivskyddad komponent egenskap

En enhet kan skicka alla giltiga JSON-enheter som följer DTDL v2-reglerna.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Exempel på nytto Last**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Exempel på en skrivskyddad egenskap med flera komponenter

Enheten måste lägga till `{"__t": "c"}` markören för att indikera att elementet refererar till en komponent.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Rapporterad egenskap**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Skrivbara egenskaper

Enheten bör bekräfta att den har fått egenskapen genom att skicka en rapporterad egenskap. Den rapporterade egenskapen ska innehålla:

- `value`– värdet som enheten har tagit emot.
- `ac`– en bekräftelse kod som använder en HTTP-statuskod.
- `av`– en bekräftelse version som refererar till den `$version` önskade egenskapen.
- `ad`– en valfri bekräftelse beskrivning.

### <a name="sample-no-component-writable-property"></a>Exempel på ingen skrivbar komponent egenskap

En enhet kan skicka en giltig JSON som följer DTDL v2-reglerna:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Önskad egenskap**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Rapporterad egenskap**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Exempel på en skrivbar egenskap med flera komponenter

Enheten måste lägga till `{"__t": "c"}` markören för att indikera att elementet refererar till en komponent.

Markören skickas bara för uppdateringar på komponent nivå, så enheter får inte söka efter den här flaggan.

Enheten bör bekräfta att den har fått egenskapen genom att skicka en rapporterad egenskap:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Önskad egenskap**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Rapporterad egenskap**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Kommandon

Inga komponent gränssnitt använder kommando namnet utan ett prefix.

På en enhet använder flera komponent gränssnitt kommando namn med följande format: `componentName*commandName` .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om IoT Plug and Play-konventioner finns här några ytterligare resurser:

- [Digitalt flätat definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell komponenter](./concepts-components.md)
