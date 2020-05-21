---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9717c76b42a63479c77f862057bfb141954eacff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673415"
---
## <a name="create-a-device-template"></a>Skapa en enhetsmall

Skapa en mapp som heter `environmental-sensor` på den lokala datorn.

Ladda ned JSON-filen för [miljö sensorns kapacitets modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) och spara den i `environmental-sensor` mappen.

Använd en text redigerare för att ersätta de två instanserna av `{YOUR_COMPANY_NAME_HERE}` med företags namnet i den `EnvironmentalSensorInline.capabilitymodel.json` fil som du laddade ned.

I ditt Azure IoT Central-program skapar du en enhets mall som kallas *miljö sensor* genom att importera `EnvironmentalSensorInline.capabilitymodel.json` enhetens kapacitets modell fil:

![Enhets mal len med importerad enhets kapacitets modell](./media/iot-central-add-environmental-sensor/device-template.png)

Enhetens kapacitets modell innehåller två gränssnitt: standard gränssnittet för **enhets information** och det anpassade **miljö sensor** gränssnittet. Gränssnittet för **miljö sensor** definierar följande funktioner:

| Typ | Visningsnamn | Beskrivning |
| ---- | ------------ | ----------- |
| Egenskap | Enhetstillstånd     | Enhetens status. Två tillstånd online/offline är tillgängligt. |
| Egenskap (skrivbar) | Kundnamn    | Namnet på kunden som har bearbetat enheten. |
| Egenskap (skrivbar) | Ljus styrke nivå | Ljus styrke nivån för ljuset på enheten. Kan anges som 1 (hög), 2 (medel), 3 (låg). |
| Telemetri | Temperatur | Aktuell temperatur identifierad av enheten. |
| Telemetri | Fuktighet    | Aktuell fuktighet upptäckt av enheten. |
| Kommando | Blink          | Börja blinka LYSDIODen på enheten under angivet tidsintervall. |
| Kommando | turnon         | Aktivera LYSDIODen på enheten. |
| Kommando | turnoff        | Stäng av LYSDIODen på enheten. |
| Kommando | rundiagnostics | Detta asynkrona kommando startar en diagnostisk körning på enheten. |

Om du vill anpassa hur egenskapen **enhets tillstånd** visas i IoT Central programmet väljer du **Anpassa** i enhets mal len. Expandera **enhets tillstånds** posten, ange _online_ som det **sanna namnet** och _offline_ som det **falska namnet**. Spara sedan ändringarna:

![Anpassa enhets mal len](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Skapa vyer

Med vyer kan du interagera med enheter som är anslutna till ditt IoT Central-program. Du kan till exempel ha vyer som visar telemetri, vyer som visar egenskaper och vyer som låter dig redigera skrivbara och moln egenskaper. Vyer ingår i en enhets mall.

Om du vill lägga till några standardvyer i din **miljö sensors** enhets mall, navigerar du till din enhets mall, väljer **vyer**och väljer sedan panelen **generera standardvyer** . Se till att **Översikt** och **om** är **på**och välj sedan **skapa standard visnings instrument panel (er)**. Nu har du två standardvyer som definierats i mallen.

**Miljö sensor** gränssnittet innehåller två skrivbara egenskaper – **kundens namn** och **ljus styrke nivå**. Om du vill skapa en vy kan du använda för att redigera dessa egenskaper:

1. Välj **vyer** och välj sedan panelen **Redigera enhet och moln data** .

1. Ange _Egenskaper_ som formulär namn.

1. Välj Egenskaper för **ljus styrke nivå** och **kundnamn** . Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

![Lägga till en vy för att aktivera redigering av egenskaper](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publicera mallen

Innan du kan lägga till en enhet som använder en **miljö sensor** enhets mall måste du publicera den.

I enhets mal len väljer du **publicera**. I **mallen publicera den här enheten i program** panelen väljer du **publicera**.

Kontrol lera att mallen är redo att användas genom att gå till sidan **enheter** i IoT Central programmet. Avsnittet **enheter** visar en lista över de publicerade enheterna i programmet:

![Publicerade mallar på sidan enheter](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet i enhets mal len som du skapade i föregående avsnitt:

1. På sidan **enheter** väljer du mallen **miljö sensor** enhet.

    > [!TIP]
    > Se till att välja den mall som ska användas innan du väljer **+ ny**, annars skapar du en enhet som inte är associerad.

1. Välj **+ ny**.

1. Se till att den **simulerade** är **avstängd**. Välj sedan **skapa**.

Klicka på enhetens namn och välj sedan **Anslut**. Anteckna anslutnings informationen för enheten på sidan för **enhets anslutningens** ID- **omfång**, **enhets-ID**och **primär nyckel**. Du behöver dessa värden när du skapar din enhets kod:

![Information om enhets anslutning](./media/iot-central-add-environmental-sensor/device-connection.png)
