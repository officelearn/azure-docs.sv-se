---
title: inkludera fil
description: inkludera fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877241"
---
### <a name="add-relationships"></a>Lägg till relationer

I enhets mal len för **lva Edge-Gateway** under **moduler/lva Edge Gateway module**väljer du **relationer**. Välj **+ Lägg till relation** och Lägg till följande två relationer:

|Visningsnamn               |Namn          |Mål |
|-------------------------- |------------- |------ |
|LVA kant rörelse detektor   |Använd standard   |LVA Edge motion detektor-enhet |
|LVA kant objekts detektor   |Använd standard   |LVA Edge objekt detektor enhet |

Välj sedan **Spara**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Lägg till relationer":::

### <a name="add-views"></a>Lägga till vyer

Enhets mal len för **lva Edge-Gateway** innehåller inte några visnings definitioner.

Så här lägger du till en vy i enhets mal len:

1. I enhets mal len för **lva Edge-Gateway** navigerar du till **vyer** och väljer **visualiseringen av enhets** panelen.

1. Ange *lva Edge gateway-enhet* som visnings namn.

1. Lägg till följande paneler i vyn:

    * En panel med egenskaper för **enhets information** : **enhets modell**, **tillverkare**, **operativ system**, **processor arkitektur**, **program varu version**, **totalt minne**och **totalt lagrings utrymme**.
    * En linje diagram panel med värdena **ledigt minne** och **system pulsslags** telemetri.
    * En händelse historik panel med följande händelser: **skapa kamera**, **ta bort kamera**, **starta om**modul, **modul startad**, **modul stoppad**.
    * En 2x1 senast kända värde-panel som visar **IoT Central klientens status** telemetri.
    * En 2x1 senast kända värde-panel som visar telemetri för **modulens tillstånd** .
    * En 1x1 senast kända värde-panel som visar **system Pulsslagets** telemetri.
    * En 1x1 senast kända värde-panel som visar de **anslutna kamerornas** telemetri.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Lägg till relationer":::

1. Välj **Spara**.

### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan lägga till en enhet i programmet måste du publicera enhets mal len:

1. I enhets mal len för **lva Edge-Gateway** väljer du **publicera**.

1. I **mallen publicera den här enheten på program** sidan väljer du **publicera**.

**Lva Edge Gateway** är nu tillgängligt som enhets typ på sidan **enheter** i programmet.

## <a name="add-a-gateway-device"></a>Lägg till en gateway-enhet

Så här lägger du till en **lva Edge Gateway** -enhet i programmet:

1. Gå till sidan **enheter** och välj enhets mal len **lva Edge Gateway** .

1. Välj **+ Ny**.

1. I dialog rutan **skapa en ny enhet** ändrar du enhets namnet till *lva Gateway 001*och ändrar enhets-ID till *lva-Gateway-001*.

    > [!NOTE]
    > Enhets-ID: t måste vara unikt i programmet.

1. Välj **Skapa**.

Enhetens status är **registrerad**.

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

Du behöver de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enheter** väljer du den **lva-Gateway-001-** enhet som du skapade.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** gör du en anteckning i *scratchpad.txt* -filen för **ID-omfånget**, **enhets-ID: t**och enhetens **primär nyckel**. Du använder dessa värden senare.

1. Se till att anslutnings metoden är inställd på **signatur för delad åtkomst**.

1. Välj **Stäng**.

## <a name="next-steps"></a>Nästa steg

Nu har du skapat ett IoT Central program med hjälp av program mal len **video analys – objekt och rörelse identifiering** , skapat en enhets mall för gateway-enheten och lagt till en gateway-enhet i programmet.

Om du vill testa programmet för video analys – objekt-och motion-identifiering med hjälp av IoT Edge moduler som kör en virtuell dator i molnet med simulerade video strömmar:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Om du vill prova program för video analys – objekt-och motion-identifiering med IoT Edge moduler som kör en riktig enhet med en riktig **ONVIF** -kamera:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
