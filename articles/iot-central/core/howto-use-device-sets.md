---
title: Använda enhets uppsättningar i ditt Azure IoT Central-program | Microsoft Docs
description: Som operatör använder du enhets uppsättningar i ditt Azure IoT Central-program.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 87bbab041405a085d405707ff419fbad55fdcd09
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952763"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Använda enhets uppsättningar i ditt Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som en operatör, att använda enhets uppsättningar i ditt Azure IoT Central-program.

En enhets uppsättning är en lista över enheter som är grupperade tillsammans eftersom de matchar vissa angivna villkor. Enhets uppsättningar hjälper dig att hantera, visualisera och analysera enheter i skala genom att gruppera enheter i mindre logiska grupper. Du kan till exempel skapa en enhets uppsättning för att visa en lista över alla luftkonditionerings enheter i Seattle så att en tekniker kan hitta de enheter som de är ansvariga för. Den här artikeln visar hur du skapar och konfigurerar enhets uppsättningar.

## <a name="create-a-device-set"></a>Skapa en enhets uppsättning

Så här skapar du en enhets uppsättning:

1. Välj **enhets uppsättningar** i det vänstra fönstret.

1. Välj **+ ny**.

    ![Ny enhets uppsättning](media/howto-use-device-sets/image1.png)

1. Namnge din enhet och ange ett namn som är unikt för hela programmet. Du kan också lägga till en beskrivning. En enhets uppsättning kan bara innehålla enheter från en enda enhets mall. Välj den enhets mall som ska användas för den här uppsättningen.

1. Skapa frågan för att identifiera enheter för enhets uppsättningen genom att välja en egenskap, en jämförelse operator och ett värde. Du kan lägga till flera frågor och enheter som uppfyller **alla** villkor i enhets uppsättningen. Den enhets uppsättning som du skapar är tillgänglig för alla som har åtkomst till programmet, så att vem som helst kan se, ändra eller ta bort enhets uppsättningen.

    ![Enhets uppsättnings fråga](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Enhets uppsättningen är en dynamisk fråga. Varje gång du visar listan över enheter kan det finnas olika enheter i listan. Listan är beroende av vilka enheter som för närvarande uppfyller villkoren i frågan.

1. Välj **Spara**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurera instrument panelen för din enhets uppsättning

När du har skapat din enhets uppsättning kan du konfigurera dess **instrument panel**. **Instrument panelen** är den Start sida där du placerar bilder och länkar. Du kan också lägga till rutnät som visar en lista över enheterna i enhets uppsättningen.

1. Välj **enhets uppsättningar** i det vänstra fönstret.

1. Välj din enhets uppsättning.

1. Välj fliken **Instrumentpanel**.

1. Välj **Redigera**.

    ![Design läge på](media/howto-use-device-sets/image3.png)

1. Information om hur du lägger till en avbildning finns i [förbereda och ladda upp bilder till ditt Azure IoT Central-program](howto-prepare-images.md).

1. Lägg till en länk panel:
    1. Välj **länk** i den högra rutan.
    1. Ge länken en **rubrik**.
    1. Välj en URL som ska öppnas när länken är markerad.
    1. Ge länken en beskrivning som visas under **rubriken**.
    1. Välj **Spara**.

        ![Spara länk](media/howto-use-device-sets/image7.png)

    1. Du kan flytta och ändra storlek på länk panelen på **instrument panelen**.

1. Lägg till ett rutnät. Ett rutnät är en tabell med enheter i enhets uppsättningen med de kolumner du väljer.
    1. Välj **rutnät** i den högra rutan.
    1. Ge ditt rutnät en **rubrik**.
    1. Välj de kolumner som ska visas genom att välja **Lägg till/ta bort**. I panelen som öppnas väljer du den kolumn som du vill visa och väljer högerpilen för att markera den.
    1. Välj **OK**.
    1. Välj **Spara**.

        ![Spara rutnät](media/howto-use-device-sets/image9.png)

    1. Dra och släpp rutnätet för att placera det på **instrument panelen**.

        > [!NOTE]
        > Du kan lägga till flera bilder, länkar och rutnät.
  
    1. Välj **Done** (Klar).

Mer information om hur du använder paneler i Azure IoT Central finns i [använda paneler i instrument paneler](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Konfigurera en plats karta i instrument panelen för enhets uppsättningar

Du kan lägga till en karta för att visualisera enheternas plats i enhets uppsättningen.

Om du vill lägga till en karta på instrument panelen för enhets uppsättningar måste du ha konfigurerat en plats mätnings-eller plats egenskap i din enhets mall. Mer information finns i [skapa en plats mätning](howto-set-up-template.md) eller [skapa en plats egenskap](howto-set-up-template.md).

1. På **instrument panelen**för enhets uppsättningar väljer du **Mappa** från biblioteket.
2. Lägg till en rubrik och välj den plats mätning eller egenskap som du konfigurerade tidigare.
3. Välj **Spara** och kart panelen visar de senaste kända platserna för enheterna i enhets uppsättningen.
4. När en operator visar instrument panelen för enhets uppsättningar, ser operatorn alla paneler som du har konfigurerat, inklusive plats kartan.

Du kan ändra storlek på kart panelen på instrument panelen. Om du väljer en PIN-kod på kartan visas enhets information, namn och plats. Välj popup-fönstret för att gå till egenskaps sidan för enheten.

## <a name="configure-the-list-for-your-device-set"></a>Konfigurera listan för din enhets uppsättning

När du har skapat din enhets uppsättning kan du konfigurera **listan**. I **listan** visas alla enheter i enheten som angetts i en tabell med de kolumner du väljer.

1. Välj **enhets uppsättningar** i det vänstra fönstret.

1. Välj fliken **lista** .

1. Välj **kolumnalternativ**.

    ![Kolumn alternativ](media/howto-use-device-sets/image11.png)

1. Välj de kolumner som ska visas genom att markera den kolumn som du vill visa och välja den högra pilen för att markera den.

    ![Välj kolumn](media/howto-use-device-sets/image12.png)

1. Välj **OK**.

## <a name="analytics"></a>Analyser

Analys i enhets uppsättningar är samma som fliken Main Analytics i det vänstra fönstret. Du kan lära dig mer om analys i artikeln om [hur du skapar analyser](howto-use-device-sets.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhets uppsättningar i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skapa regler för telemetri](howto-create-telemetry-rules.md)
