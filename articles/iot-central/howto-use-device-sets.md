---
title: Använd enhetsuppsättningar i ditt program med Azure IoT Central | Microsoft Docs
description: Som operatör anger hur du använder enheten i Azure IoT Central programmet.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: b24c7c6d08b7d7914c7bdef6d2a55eb88689b6ad
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315205"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Använd enhetsuppsättningar i Azure IoT Central programmet

Den här artikeln beskrivs hur du som en operatör att använda enheten anger i programmets Azure IoT Central.

En enhet är en lista över enheter som grupperas eftersom de alla matchar vissa angivna villkor. Enheten anger hjälpa dig att hantera, visualisera och analysera enheter i stor skala genom att gruppera enheter i mindre, logiska grupper. Exempelvis kan skapa du en lista över alla enheter som luftkonditionering i Seattle att aktivera Seattle-tekniker att hitta alla enheter som teknikern ansvarar. Den här artikeln visar hur du skapar och konfigurerar Enhetsuppsättningar.

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

När du skapar din enhetsuppsättning kan du konfigurera dess **instrumentpanelen**. Den **instrumentpanelen** är startsidan där du kan placera bilder och länkar. Du kan också lägga till rutnät som lista över enheterna i enhetsuppsättningen.

1. Välj **enhetsuppsättningar** på den vänstra navigeringsmenyn.

1. Välj den **instrumentpanelen** fliken.

1. Välj **Redigera mall**.

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

        ![Välj rutnät](media/howto-use-device-sets/image8.png)

    1. Ge ditt rutnät en **rubrik**.
    1. Markera kolumnerna som ska visas genom att välja **Lägg till/ta bort**. Välj den kolumn som du vill ska visas och välj högerpilen för att välja den panelen som visas.
    1. Välj **OK**.
    1. Välj **Spara**.

        ![Spara rutnät](media/howto-use-device-sets/image9.png)

    1. Dra och släpp rutnätet om du vill placera den på den **instrumentpanelen**.

    > [!NOTE]
    > Du kan lägga till flera avbildningar, länkar och rutnät.
  
    1. Välj **Done** (Klar).

    ![Designläge av](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Konfigurera platskarta i enheten anger instrumentpanel 
Du kan lägga till en platskarta som visualiserar platsen för dina enheter anger på en karta.

För att lägga till en platskarta till enheten som anger instrumentpanelen som du måste ha konfigurerade Platsegenskapen i mallen för enhet, se [skapa en plats-egenskap som drivs av Azure Maps](howto-set-up-template.md).


1. Välj kartan på instrumentpanelen ställa in enheten från biblioteket.

    ![Enhetsuppsättningar instrumentpanelen Maps](media/howto-use-device-sets/LocationMaps1.png)

2. Ge en rubrik och Välj egenskapen location som du tidigare har konfigurerat som en del av din enhetsegenskap.
3. Spara och du ser kartan panelen Visa platsen för dina enheter i enheten inställd.
4. Nu när en operatör vyer enhetsuppsättningar instrumentpanelen, operatören kan se alla paneler som du har konfigurerat, inklusive plats karta som visualiserar enheter plats på ett ögonblick! 
    
> [!NOTE] 
> Du kommer att kunna ändra storlek på kartan för att den har önskad storleken. Att välja en PIN-kod i kartan visas information om enhet, namn och plats. Du kan välja popup-fönstret att gå till egenskapssidan för enheten.  


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

Analys i enhetsuppsättningar är samma som den huvudsakliga analytics-fliken i den vänstra navigeringsmenyn. Du kan lära dig mer om analytics i artikeln på [så här skapar du analytics](howto-create-analytics.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhetsuppsättningar i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du skapar regler för telemetri](howto-create-telemetry-rules.md)
