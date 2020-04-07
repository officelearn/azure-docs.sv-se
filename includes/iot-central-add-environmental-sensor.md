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
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673987"
---
## <a name="create-a-device-template"></a>Skapa en enhetsmall

Skapa en `environmental-sensor` mapp som anropas på den lokala datorn.

Ladda ner [miljösensor kapacitet modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON `environmental-sensor` fil och spara den i mappen.

Använd en textredigerare för `{YOUR_COMPANY_NAME_HERE}` att ersätta de `EnvironmentalSensorInline.capabilitymodel.json` två instanserna med ditt företagsnamn i filen som du hämtade.

Skapa en enhetsmall som heter *Miljösensor* i azure IoT Central-programmet genom att importera `EnvironmentalSensorInline.capabilitymodel.json` enhetsfunktionsmodellfilen:

![Enhetsmall med standardmodell för importerad enhetskapacitet](./media/iot-central-add-environmental-sensor/device-template.png)

Enhetskapacitetsmodellen innehåller två gränssnitt: **standardgränssnittet för enhetsinformation** och det anpassade **miljösensorgränssnittet.** **Gränssnittet för miljösensor** definierar följande funktioner:

| Typ | Visningsnamn | Beskrivning |
| ---- | ------------ | ----------- |
| Egenskap | Enhetstillstånd     | Enhetens tillstånd. Två tillstånd online/offline är tillgängliga. |
| Egenskap (skrivbar) | Kundens namn    | Namnet på den kund som för närvarande använder enheten. |
| Egenskap (skrivbar) | Ljusstyrka | Ljusstyrkan för lampan på enheten. Kan anges som 1 (hög), 2 (medium), 3 (låg). |
| Telemetri | Temperatur | Aktuell temperatur som detekteras av enheten. |
| Telemetri | Fuktighet    | Aktuell fuktighet som upptäcks av enheten. |
| Kommando | Blinka          | Börja blinka lysdioden på enheten under givet tidsintervall. |
| Kommando | Turnon         | Slå på lysdioden på enheten. |
| Kommando | Avfarten        | Stäng av lysdioden på enheten. |
| Kommando | rundiagnostics (rundiagnostics) | Det här asynkrona kommandot startar en diagnostik som körs på enheten. |

Om du vill anpassa hur egenskapen **Enhetstillstånd** visas i i ditt IoT Central-program väljer du **Anpassa** i enhetsmallen. Expandera **enhetstillståndsposten,** ange _Online_ som **sant namn** och _Offline_ som **falskt namn**. Spara sedan ändringarna:

![Anpassa enhetsmallen](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Skapa vyer

Med vyer kan du interagera med enheter som är anslutna till ditt IoT Central-program. Du kan till exempel ha vyer som visar telemetri, vyer som visar egenskaper och vyer som gör att du kan redigera skrivbara och molnegenskaper. Vyer är en del av en enhetsmall.

Om du vill lägga till några standardvyer i **mallen Miljösensorenhet** navigerar du till enhetsmallen, väljer **Vyer**och väljer panelen **Generera standardvyer.** Kontrollera att **Översikt** och **Om** är **På**och välj sedan **Generera standardinstrumentpanelsvyer.** Nu har du definierat två standardvyer i mallen.

**Gränssnittet för miljösensor** innehåller två skrivbara egenskaper - **Kundnamn** och **ljusstyrka**. Om du vill skapa en vy kan du använda för att redigera dessa egenskaper:

1. Välj **Vyer** och välj sedan panelen **Redigera enhet och molndata.**

1. Ange _egenskaper_ som formulärnamn.

1. Välj egenskaperna **Ljusstyrka** och **Kundnamn.** Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

![Lägga till en vy för att aktivera egenskapsredigering](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publicera mallen

Innan du kan lägga till en enhet som använder mallen **miljösensorenhet** måste du publicera den.

Välj **Publicera**i enhetsmallen . Välj **Publicera**i **mallen Publicera den här enheten på programpanelen** .

Om du vill kontrollera att mallen är klar att användas navigerar du till sidan **Enheter** i ditt IoT Central-program. Avsnittet **Enheter** visar en lista över de publicerade enheterna i programmet:

![Publicerade mallar på sidan enheter](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet i enhetsmallen som du skapade i föregående avsnitt:

1. På sidan **Enheter** väljer du mallen **miljösensorenhet.**

    > [!TIP]
    > Var noga med att välja den mall som ska användas innan du väljer **+ Ny**, annars skapar du en enhet som inte är associerad.

1. Välj **+ Nytt**.

1. Kontrollera att **simulerad** är **Av**. Välj sedan **Skapa**.

Klicka på enhetens namn och välj sedan **Anslut**. Anteckna enhetsanslutningsinformationen på sidan **Enhetsanslutning** - **ID-scope,** **Enhets-ID**och **Primärnyckel**. Du behöver dessa värden när du skapar enhetskoden:

![Information om enhetsanslutning](./media/iot-central-add-environmental-sensor/device-connection.png)
