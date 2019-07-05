---
title: Använd enhetsuppsättningar i ditt program med Azure IoT Central | Microsoft Docs
description: Som operatör anger hur du använder enheten i Azure IoT Central programmet.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503207"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Använd enhetsuppsättningar i Azure IoT Central programmet

Den här artikeln beskrivs hur du som en operatör att använda enheten anger i programmets Azure IoT Central.

En enhet är en lista över enheter som grupperas eftersom de uppfyller vissa angivna villkor. Enheten anger hjälpa dig att hantera, visualisera och analysera enheter i stor skala genom att gruppera enheter i mindre, logiska grupper. Du kan till exempel skapa en enhet som att lista alla luftkonditionering enheter i Seattle att aktivera en tekniker att hitta de enheter som de är ansvarig. Den här artikeln visar hur du skapar och konfigurerar Enhetsuppsättningar.

## <a name="create-a-device-set"></a>Skapa en enhetsuppsättning

Skapa en enhetsuppsättning:

1. Välj **enhetsuppsättningar** på den vänstra navigeringsmenyn.

1. Välj **+ ny**.

    ![Ny enhetsuppsättning](media/howto-use-device-sets/image1.png)

1. Namnge din enhetsuppsättning som är unikt i hela programmet. Du kan också lägga till en beskrivning. En enhetsuppsättning får bara innehålla enheter från en enskild enhet-mall. Välj den enhet mallen som ska användas för den här uppsättningen.

1. Skapa fråga för att identifiera enheterna som för enheten genom att välja en egenskap, jämförelseoperator och ett värde. Du kan lägga till flera frågor och enheter som uppfyller **alla** kriterierna är placerade i enhetsuppsättningen. Enhetsuppsättning som du skapar är tillgängliga för alla som har åtkomst till programmet, så att någon kan visa, ändra eller ta bort enhetsuppsättning av.

    ![Enheten inställd fråga](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Enheten är en dynamisk fråga. Varje gång du visa en lista med enheter, kan det finnas olika enheter i listan. Listan beror på vilka enheter som för närvarande uppfyller kriterierna för frågan.

1. Välj **Spara**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurera instrumentpanel för din enhet

När du skapar din enhetsuppsättning kan du konfigurera dess **instrumentpanelen**. Den **instrumentpanelen** är startsidan där du placerar dina bilder och länkar. Du kan också lägga till rutnät som lista över enheterna i enhetsuppsättningen.

1. Välj **enhetsuppsättningar** på den vänstra navigeringsmenyn.

1. Markera din enhet.

1. Välj fliken **Instrumentpanel** .

1. Välj **Redigera**.

    ![Designläge på](media/howto-use-device-sets/image3.png)

1. Läs om hur du lägger till en bild [Förbered och ladda upp bilder till Azure IoT Central programmet](howto-prepare-images.md).

1. Lägg till en panel på länken:
    1. Välj **länken** i den högra rutan.
    1. Ge din länk ett **rubrik**.
    1. Välj en URL som ska öppnas när länken har valts.
    1. Ge din länk till en beskrivning som visas nedan i **rubrik**.
    1. Välj **Spara**.

        ![Spara länk](media/howto-use-device-sets/image7.png)

    1. Du kan flytta och ändra storlek på panelen länk på den **instrumentpanelen**.

1. Lägg till ett rutnät. Ett rutnät är en tabell med enheter i enheten med de kolumner som du väljer.
    1. Välj **Grid** i den högra rutan.
    1. Ge ditt rutnät en **rubrik**.
    1. Markera kolumnerna som ska visas genom att välja **Lägg till/ta bort**. Välj den kolumn som du vill ska visas och välj högerpilen för att välja den panelen som visas.
    1. Välj **OK**.
    1. Välj **Spara**.

        ![Spara rutnät](media/howto-use-device-sets/image9.png)

    1. Dra och släpp rutnätet om du vill placera den på den **instrumentpanelen**.

        > [!NOTE]
        > Du kan lägga till flera avbildningar, länkar och rutnät.
  
    1. Välj **Done** (Klar).

Mer information om hur du använder paneler i Azure IoT Central finns [använda instrumentpaneler](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Konfigurera en platskarta på din enhet uppsättningar instrumentpanel

Du kan lägga till en karta för att visualisera platsen för enheter i din enhet.

Om du vill lägga till en karta till din enhet anger instrumentpanelen, måste du ha konfigurerat en plats mätning eller Platsegenskapen i mallen för enheten. Mer information finns i [skapa en plats](howto-set-up-template.md) eller [skapa en Platsegenskapen](howto-set-up-template.md).

1. Ställ in på din enhet **instrumentpanelen**väljer **kartan** från biblioteket.
2. Lägg till en rubrik och välj plats mätningen eller egenskapen som du tidigare konfigurerat.
3. Välj **spara** och panelen kartan visar senaste kända platserna för enheterna i din enhet.
4. När en operatör visar instrumentpanelen för enheten uppsättningar, ser operatorn alla paneler som du har konfigurerat, inklusive location-kartan.

Du kan ändra storlek på kartan panelen på instrumentpanelen. Att välja en PIN-kod på kartan visar enhetsinformationen, namn och plats. Välj popup-fönstret att gå till egenskapssidan för enheten.

## <a name="configure-the-list-for-your-device-set"></a>Konfigurera en lista för din enhet

När du skapar din enhetsuppsättning kan du konfigurera den **lista**. Den **lista** visar alla enheter i enheten som angetts i en tabell med kolumner som du väljer.

1. Välj **enhetsuppsättningar** på den vänstra navigeringsmenyn.

1. Välj den **lista** fliken.

1. Välj **kolumnalternativ**.

    ![Kolumnalternativ för](media/howto-use-device-sets/image11.png)

1. Välj kolumnerna som ska visas genom att markera den kolumn som du vill visa och välja pilen till höger för att välja den.

    ![Välj kolumn](media/howto-use-device-sets/image12.png)

1. Välj **OK**.

## <a name="analytics"></a>Analytics

Analys i enhetsuppsättningar är samma som den huvudsakliga analytics-fliken i den vänstra navigeringsmenyn. Du kan lära dig mer om analytics i artikeln på [så här skapar du analytics](howto-use-device-sets.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhetsuppsättningar i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du skapar regler för telemetri](howto-create-telemetry-rules.md)
