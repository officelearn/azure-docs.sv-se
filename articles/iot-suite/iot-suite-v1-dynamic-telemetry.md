---
title: "Använd dynamiska telemetri | Microsoft Docs"
description: "Den här kursen om du vill veta hur du använder dynamiska telemetri med Azure IoT Suite remote förkonfigurerade övervakningslösning."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Använd dynamiska telemetri med fjärråtkomst övervakning förkonfigurerade lösningen

Dynamisk telemetri kan du visualisera alla telemetri som skickas till den fjärranslutna förkonfigurerade övervakningslösning. Simulerade enheter som distribueras med förkonfigurerade lösningen skicka temperatur- och fuktighetskonsekvens telemetri som du kan visualisera på instrumentpanelen. Om du anpassa befintliga simulerade enheter, skapa nya simulerade enheter eller ansluta fysiska enheter till den förkonfigurerade lösningen kan du skicka andra telemetri värden som externa temperatur, RPM eller vindhastigheten. Sedan kan du visualisera detta ytterligare telemetri på instrumentpanelen.

Den här kursen använder en enkel Node.js simulerade enhet som du kan enkelt ändra om du vill experimentera med dynamiska telemetri.

Den här kursen behöver du:

* En aktiv Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
* [Node.js] [ lnk-node] version 0.12.x eller senare.

Du kan slutföra den här självstudiekursen på alla operativsystem, till exempel Windows eller Linux, där du kan installera Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Lägga till en telemetri typ.

Nästa steg är att ersätta telemetri som genererats av Node.js simulerade enheten med en ny uppsättning värden:

1. Stoppa Node.js simulerade enheten genom att skriva **Ctrl + C** i Kommandotolken eller shell.
2. Du kan se grunddata värdena för befintliga temperatur, fuktighet och externa temperatur telemetri i filen remote_monitoring.js. Lägg till ett värde för basdata för **rpm** på följande sätt:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js simulerade enheten använder den **generateRandomIncrement** funktion i filen remote_monitoring.js att lägga till en slumpmässig ökning i grunddata-värden. Slumpa den **rpm** värde genom att lägga till en rad med kod efter befintliga randomizations på följande sätt:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Lägg till det nya rpm-värdet i JSON-nyttolast enheten skickar till IoT-hubb:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Kör Node.js simulerade enheten med följande kommando:

    `node remote_monitoring.js`

6. Se den nya typen av RPM telemetri som visas i diagram på instrumentpanelen:

![Lägg till RPM på instrumentpanelen][image3]

> [!NOTE]
> Du kan behöva inaktivera och aktivera sedan Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="customize-the-dashboard-display"></a>Anpassa instrumentpanelsvy

Den **enhetsinformation** meddelandet kan innehålla metadata om den telemetriska enheten kan skicka till IoT-hubb. Dessa metadata kan ange vilka telemetri enheten skickar. Ändra den **deviceMetaData** värdet i filen remote_monitoring.js för att inkludera en **telemetri** definition följande den **kommandon** definition. I följande kod fragment visas den **kommandon** definition (se till att lägga till en `,` när den **kommandon** definition):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> Fjärråtkomst övervakningslösning använder en skiftlägeskänslig matchning för att jämföra metadatadefinitionen med data i dataströmmen telemetri.


Lägga till en **telemetri** definition som visas i föregående kodfragment ändrar inte beteendet för instrumentpanelen. Metadata kan dock också inkludera en **DisplayName** attribut för att anpassa visningen i instrumentpanelen. Uppdatering av **telemetri** metadatadefinitionen som visas i följande utdrag:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Följande skärmbild visar hur den här ändringen ändrar diagramförklaringen på instrumentpanelen:

![Anpassa diagrammets förklaring][image4]

> [!NOTE]
> Du kan behöva inaktivera och aktivera sedan Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="filter-the-telemetry-types"></a>Filtrera telemetri-typer

Som standard visar diagram på instrumentpanelen varje dataserie i telemetri dataströmmen. Du kan använda den **enhetsinformation** metadata för visningen av specifika telemetri typer i diagrammet. 

Att visa endast temperatur- och Fuktighetskonsekvens telemetri, utelämna **ExternalTemperature** från den **enhetsinformation** **telemetri** metadata på följande sätt:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

Den **utomhus temperatur** inte längre visas i diagrammet:

![Filtrera telemetri på instrumentpanelen][image5]

Den här ändringen påverkar bara diagramvyn. Den **ExternalTemperature** datavärden fortfarande lagras och blir tillgänglig för alla backend-bearbetning.

> [!NOTE]
> Du kan behöva inaktivera och aktivera sedan Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="handle-errors"></a>Hantera fel

För en dataström som ska visas i diagrammet, dess **typen** i den **enhetsinformation** metadata måste matcha värdena telemetri datatyp. Till exempel om metadata som anger att den **typen** för fuktighet data är **int** och en **dubbla** hittas i dataströmmen telemetri och sedan fuktighet telemetri inte visas i diagrammet. Men den **fuktighet** värden fortfarande lagras och blir tillgänglig för alla backend-bearbetning.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder dynamiska telemetri, kan du läsa mer om hur förkonfigurerade lösningar använder enhetsinformation: [enhetens information metadata för fjärranslutna övervakningen förkonfigurerade lösningen] [ lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
