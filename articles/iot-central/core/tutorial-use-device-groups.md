---
title: Använda enhets grupper i ditt Azure IoT Central-program | Microsoft Docs
description: Som operatör lär du dig hur du använder enhets grupper för att analysera telemetri från enheter i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 4fd05631e7f54b6258978f70fdd5dfb9705f989b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026970"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Självstudie: Använd enhets grupper för att analysera enhets telemetri



Den här artikeln beskriver hur, som en operatör, att använda enhets grupper för att analysera enhets telemetri i ditt Azure IoT Central-program.

En enhets grupp är en lista över enheter som grupperas tillsammans eftersom de matchar vissa angivna villkor. Med enhets grupper kan du hantera, visualisera och analysera enheter i skala genom att gruppera enheter i mindre logiska grupper. Du kan till exempel skapa en enhets grupp för att visa en lista över alla luftkonditionerings enheter i Seattle så att en tekniker kan hitta de enheter som de är ansvariga för.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en enhetsgrupp.
> * Använda en enhets grupp för att analysera telemetri för enheter

## <a name="prerequisites"></a>Krav

Innan du börjar bör du fylla i [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) och [lägga till en simulerad enhet i IoT Central](./quick-create-pnp-device.md) snabb starter för att skapa **miljösensorns** enhets mall att fungera med.

## <a name="create-simulated-devices"></a>Skapa simulerade enheter

Innan du skapar en enhets grupp lägger du till minst fem simulerade enheter från **Miljösensorns** enhets mall som ska användas i den här självstudien:

![Fem simulerade miljö sensor enheter](./media/tutorial-use-device-groups/simulated-devices.png)

För fyra av miljö sensor enheterna använder du vyn **Egenskaper för miljö sensor** för att ange kundens namn till **contoso**:

![Ange kund namn till contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Skapa en enhetsgrupp.

Så här skapar du en enhets grupp:

1. Välj **enhets grupper** i det vänstra fönstret.

1. Välj **+ ny**.

    ![Ny enhets grupp](media/tutorial-use-device-groups/image1.png)

1. Ge enhets gruppen ett namn som **contoso-enheter**. Du kan också lägga till en beskrivning. En enhets grupp kan bara innehålla enheter från en enda enhets mall. Välj den **miljö sensor** enhets mall som ska användas för den här gruppen.

1. Skapa frågan för att identifiera de enheter som tillhör **contoso** för enhets gruppen genom att välja egenskapen **kundnamn** , **är lika med** jämförelse operatorn och **contoso** som värde. Du kan lägga till flera frågor och enheter som uppfyller **alla** villkor i enhets gruppen. Den enhets grupp som du skapar är tillgänglig för alla som har åtkomst till programmet, så att vem som helst kan se, ändra eller ta bort enhets gruppen.

    ![Fråga om enhets grupp](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > Enhets gruppen är en dynamisk fråga. Varje gång du visar listan över enheter kan det finnas olika enheter i listan. Listan är beroende av vilka enheter som för närvarande uppfyller villkoren i frågan.

1. Välj **Spara**.

> [!NOTE]
> För Azure IoT Edge enheter väljer du Azure IoT Edge mallar för att skapa en enhets grupp.

## <a name="analytics"></a>Analyser

Du kan använda **Analytics** med en enhets grupp för att analysera Telemetrin från enheterna i gruppen. Du kan till exempel rita den genomsnittliga temperatur som rapporteras av alla Contosos miljö sensorer.

Analysera telemetri för en enhets grupp:

1. Välj **Analytics** i det vänstra fönstret.

1. Välj den enhets grupp för **contoso-enheter** som du skapade. Lägg sedan till både **typer av telemetri** och **fukt** :

    ![Skapa analys](./media/tutorial-use-device-groups/create-analysis.png)

    Använd hjul ikonerna bredvid typer av telemetri för att välja en agg regerings typ. Standardvärdet är **Average**. Använd **dela efter** för att ändra hur aggregerade data visas. Om du till exempel delar efter enhets-ID visas en rityta för varje enhet när du väljer **analysera**.

1. Välj **analysera** för att Visa medelvärdet för telemetri:

    ![Visa analys](./media/tutorial-use-device-groups/view-analysis.png)

    Du kan anpassa vyn, ändra tids perioden som visas och exportera data.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhets grupper i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skapa regler för telemetri](tutorial-create-telemetry-rules.md)
