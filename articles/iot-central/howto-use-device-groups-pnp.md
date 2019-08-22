---
title: Använda enhets grupper i ditt Azure IoT Central-program | Microsoft Docs
description: Som operatör använder du enhets grupper i ditt Azure IoT Central-program.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879752"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Använda enhets grupper i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver hur, som en operatör, att använda enhets grupper i ditt Azure IoT Central-program.

En enhets grupp är en lista över enheter som grupperas tillsammans eftersom de matchar vissa angivna villkor. Med enhets grupper kan du hantera, visualisera och analysera enheter i skala genom att gruppera enheter i mindre logiska grupper. Du kan till exempel skapa en enhets grupp för att visa en lista över alla luftkonditionerings enheter i Seattle så att en tekniker kan hitta de enheter som de är ansvariga för. Den här artikeln visar hur du skapar och konfigurerar enhets grupper.

## <a name="create-a-device-group"></a>Skapa en enhetsgrupp.

Så här skapar du en enhets grupp:

1. Välj **enhets grupper** på den vänstra navigerings menyn.

1. Välj **+ ny**.

    ![Ny enhets grupp](media/howto-use-device-groups-pnp/image1.png)

1. Ge enhets gruppen ett namn som är unikt i hela programmet. Du kan också lägga till en beskrivning. En enhets grupp kan bara innehålla enheter från en enda enhets mall. Välj den enhets mall som ska användas för den här gruppen.

1. Skapa frågan för att identifiera enheter för enhets gruppen genom att välja en egenskap, en jämförelse operator och ett värde. Du kan lägga till flera frågor och enheter som uppfyller **alla** villkor i enhets gruppen. Den enhets grupp som du skapar är tillgänglig för alla som har åtkomst till programmet, så att vem som helst kan se, ändra eller ta bort enhets gruppen.

    ![Fråga om enhets grupp](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Enhets gruppen är en dynamisk fråga. Varje gång du visar listan över enheter kan det finnas olika enheter i listan. Listan är beroende av vilka enheter som för närvarande uppfyller villkoren i frågan.

1. Välj **Spara**.

## <a name="analytics"></a>Analytics

Analys i enhets grupper är samma som fliken Main Analytics i den vänstra navigerings menyn. Du kan lära dig mer om analys i artikeln om [hur du skapar analyser](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhets grupper i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skapa regler för telemetri](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
