---
title: Använd dynamisk telemetri | Microsoft Docs
description: Följ den här självstudiekursen lär du dig hur du använder dynamisk telemetri med förkonfigurerade övervakningslösningen för Azure IoT Suite.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096624"
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Använd dynamisk telemetri med den förkonfigurerade lösningen för fjärrövervakning

Dynamisk telemetri kan du visualisera någon telemetri som skickas till den förkonfigurerade lösningen för fjärrövervakning. De simulerade enheterna som distribuerar med den förkonfigurerade lösningen skickar temperatur och fuktighet telemetri som du kan visualisera på instrumentpanelen. Om du anpassa befintliga simulerade enheter, skapa nya simulerade enheter eller ansluter fysiska enheter till den förkonfigurerade lösningen skickar du andra telemetrivärden som extern temperatur, RPM eller vindhastigheten. Sedan kan du visualisera den här ytterligare telemetri på instrumentpanelen.

Den här självstudien använder en enkel Node.js simulerad enhet som du kan enkelt ändra för att experimentera med dynamisk telemetri.

Den här kursen behöver du:

* En aktiv Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
* [Node.js] [ lnk-node] version 0.12.x eller senare.

Du kan slutföra den här självstudiekursen på alla operativsystem, till exempel Windows eller Linux, där du kan installera Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Lägg till en typ av telemetri

Nästa steg är att ersätta telemetri som genererats av den simulerade enheten Node.js med en ny uppsättning värden:

1. Stoppa den simulerade enheten Node.js genom att skriva **Ctrl + C** i Kommandotolken eller shell.
2. Du kan se grunddata värden för befintliga temperatur, fuktighet och extern temperatur telemetri i filen remote_monitoring.js. Lägg till ett värde för basdata för **rpm** på följande sätt:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js simulerade enheten använder de **generateRandomIncrement** funktion i filen remote_monitoring.js att lägga till en slumpmässig ökning i grundläggande datavärdena. Slumpgenerera den **rpm** värde genom att lägga till en rad med kod efter befintliga randomizations på följande sätt:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Lägg till det nya rpm-värdet i JSON-nyttolast som enheten skickar till IoT Hub:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Kör den simulerade enheten för Node.js med följande kommando:

    `node remote_monitoring.js`

6. Se den nya typen av RPM telemetri som visas på diagrammet på instrumentpanelen:

![Lägga till RPM på instrumentpanelen][image3]

> [!NOTE]
> Du kan behöva inaktivera och sedan aktivera Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="customize-the-dashboard-display"></a>Anpassa visningen instrumentpanel

Den **enhetsinformation** meddelandet kan innehålla metadata om telemetri som enheten kan skicka till IoT Hub. Dessa metadata kan ange de typer av telemetri som enheten skickar. Ändra den **deviceMetaData** värdet i filen remote_monitoring.js att inkludera en **telemetri** definition följande den **kommandon** definition. Följande kod kodfragmentet visar den **kommandon** definition (se till att lägga till en `,` när den **kommandon** definition):

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
> Lösningen för fjärrövervakning använder en icke-skiftlägeskänsliga matchningen för att jämföra metadatadefinition med data i telemetriströmmen.


Att lägga till en **telemetri** definition som visas i föregående kodfragment inte ändra beteendet för instrumentpanelen. Metadata kan dock även innehålla en **DisplayName** attribut för att anpassa visningen i instrumentpanelen. Uppdatera den **telemetri** metadatadefinition enligt följande kodavsnitt:

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

Skärmbilden nedan visar hur den här ändringen ändrar diagramförklaringen på instrumentpanelen:

![Anpassa diagrammets förklaring][image4]

> [!NOTE]
> Du kan behöva inaktivera och sedan aktivera Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="filter-the-telemetry-types"></a>Filtrera typerna av telemetri

Som standard visar diagrammet på instrumentpanelen för varje serie i telemetriströmmen. Du kan använda den **enhetsinformation** metadata för att förhindra visning av specifika telemetrityper i diagrammet. 

Om du vill göra diagram som visar bara telemetri om temperatur och fuktighet utelämna **ExternalTemperature** från den **enhetsinformation** **telemetri** metadata på följande sätt:

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

Den **utomhus temperatur** visas inte längre i diagrammet:

![Filtrera telemetri på instrumentpanelen][image5]

Den här ändringen påverkar endast diagrammet visningen. Den **ExternalTemperature** datavärden fortfarande lagras och blir tillgängliga för alla backend-bearbetning.

> [!NOTE]
> Du kan behöva inaktivera och sedan aktivera Node.js-enhet på den **enheter** sida i instrumentpanelen för att se ändringen direkt.

## <a name="handle-errors"></a>Hantera fel

För en dataström som ska visas i diagrammet, dess **typ** i den **enhetsinformation** metadata måste matcha datatypen för telemetrivärden. Exempel: om metadata som anger att den **typ** för fuktighet data är **int** och en **dubbla** finns i telemetriströmmen och sedan fuktighet telemetrin inte visas i diagrammet. Men den **fuktighet** värden fortfarande lagras och blir tillgänglig för alla backend-bearbetning.

## <a name="next-steps"></a>Nästa steg

Nu när du har sett hur du använder dynamisk telemetri, kan du läsa mer om hur de förkonfigurerade lösningarna använder enhetsinformation: [förkonfigurerad lösning för enhetsmetadata information i den fjärrövervakning] [ lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
