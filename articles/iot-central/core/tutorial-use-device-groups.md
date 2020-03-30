---
title: Använda enhetsgrupper i ditt Azure IoT Central-program | Microsoft-dokument
description: Som operatör kan du lära dig hur du använder enhetsgrupper för att analysera telemetri från enheter i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167251"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Självstudiekurs: Använda enhetsgrupper för att analysera enhetstelemetri

I den här artikeln beskrivs hur du som operatör använder enhetsgrupper för att analysera enhetstelemetri i ditt Azure IoT Central-program.

En enhetsgrupp är en lista över enheter som grupperas tillsammans eftersom de matchar vissa angivna villkor. Enhetsgrupper hjälper dig att hantera, visualisera och analysera enheter i stor skala genom att gruppera enheter i mindre, logiska grupper. Du kan till exempel skapa en enhetsgrupp för att lista alla luftkonditioneringsenheter i Seattle så att en tekniker kan hitta de enheter som de är ansvariga för.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en enhetsgrupp.
> * Använda en enhetsgrupp för att analysera enhetstelemetri

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra [create an Azure IoT Central-programmet](./quick-deploy-iot-central.md) och Lägga till en simulerad enhet i snabbstarten för [IoT Central-programmet](./quick-create-pnp-device.md) för att skapa **MXChip IoT** DevKit-enhetsmallen som du kan arbeta med.

## <a name="create-simulated-devices"></a>Skapa simulerade enheter

Innan du skapar en enhetsgrupp lägger du till minst fem simulerade enheter från **MXChip IoT** DevKit-enhetsmallen som ska användas i den här självstudien:

![Fem simulerade sensorenheter](./media/tutorial-use-device-groups/simulated-devices.png)

För fyra av de simulerade sensorenheterna använder du vyn **Hantera enhet** för att ställa in kundnamnet på *Contoso:*

![Ange kundnamn till Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Skapa en enhetsgrupp.

Så här skapar du en enhetsgrupp:

1. Välj **Enhetsgrupper** i den vänstra rutan.

1. Välj: **+**

    ![Ny enhetsgrupp](media/tutorial-use-device-groups/image1.png)

1. Ge enhetsgruppen namnet *Contoso-enheter*. Du kan också lägga till en beskrivning. En enhetsgrupp kan bara innehålla enheter från en enda enhetsmall. Välj **den MXChip IoT** DevKit-enhetsmall som ska användas för den här gruppen.

1. Om du vill anpassa enhetsgruppen så att endast de enheter som tillhör **Contoso**innehåller väljer **du + Filter**. Välj egenskapen **Kundnamn,** jämförelseoperatören **Lika med** och **Contoso** som värde. Du kan lägga till flera filter och enheter som uppfyller **alla** filtervillkor placeras i enhetsgruppen. Enhetsgruppen som du skapar är tillgänglig för alla som har åtkomst till programmet, så att alla kan visa, ändra eller ta bort enhetsgruppen:

    ![Fråga om enhetsgrupp](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Enhetsgruppen är en dynamisk fråga. Varje gång du visar listan över enheter kan det finnas olika enheter i listan. Listan beror på vilka enheter som för närvarande uppfyller villkoren för frågan.

1. Välj **Spara**.

> [!NOTE]
> För Azure IoT Edge-enheter väljer du Azure IoT Edge-mallar för att skapa en enhetsgrupp.

## <a name="analytics"></a>Analys

Du kan använda **Analytics** med en enhetsgrupp för att analysera telemetrin från enheterna i gruppen. Du kan till exempel rita in den genomsnittliga temperaturen som rapporterats av alla Contoso-miljösensorer.

Så här analyserar du telemetrin för en enhetsgrupp:

1. Välj **Analytics** i den vänstra rutan.

1. Välj den **enhetsgrupp för Contoso-enheter** som du skapade. Lägg sedan till telemetrityperna **Temperatur** **och Luftfuktighet:**

    ![Skapa analyser](./media/tutorial-use-device-groups/create-analysis.png)

    Använd kugghjulsikonerna bredvid telemetrityperna för att välja en aggregeringstyp. Standard är **Medelvärde**. Använd **Dela efter** för att ändra hur aggregerade data visas. Om du till exempel delar efter enhets-ID ser du en ritordning för varje enhet när du väljer **Analysera**.

1. Välj **Analysera** om du vill visa de genomsnittliga telemetrivärdena:

    ![Visa analys](./media/tutorial-use-device-groups/view-analysis.png)

    Du kan anpassa vyn, ändra tidsperioden som visas och exportera data.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhetsgrupper i ditt Azure IoT Central-program, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Så här skapar du telemetriregler](tutorial-create-telemetry-rules.md)
