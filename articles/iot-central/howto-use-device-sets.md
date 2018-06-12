---
title: Använd enheten anger i tillämpningsprogrammet Azure IoT centrala | Microsoft Docs
description: Som en operator anger hur du använder enheten i Azure IoT centrala programmet.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: ef1fa64a276926a35dbf98646317bfe29200bb22
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261686"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Använd enheten i Azure IoT centrala programmet

Den här artikeln beskriver hur, som en operator för att använda enheten anger i Microsoft Azure IoT Central programmet.

En enhet är en lista över enheter som grupperas eftersom de alla uppfyller vissa angivna villkor. Enheten anger hjälper dig att hantera, visualisera och analysera enheter i skala genom att gruppera enheter i mindre, logiska grupper. Exempelvis kan du skapa en lista över alla enheter som luftkonditionering i Seattle att aktivera Seattle-tekniker att söka efter alla enheter som hon ansvarar. Den här artikeln visar hur du skapar och konfigurerar enheten anger.

## <a name="create-a-device-set"></a>Skapa en enhetsuppsättning

Skapa en enhetsuppsättning:

1. Välj **enheten anger** på den vänstra navigeringsmenyn.

1. Klicka på **+ Ny**.

    ![Ny enhetsuppsättning](media/howto-use-device-sets/image1.png)

1. Namnge din enhetsuppsättning som är unik i hela programmet. Du kan också lägga till en beskrivning. En enhetsuppsättning kan bara innehålla enheter från en enda enhet mall. Välj enhet mallen ska användas för den här uppsättningen.

1. Skapa fråga för att identifiera enheter för enheten genom att välja en egenskap, jämförelseoperator och ett värde. Du kan lägga till flera frågor och enheter som uppfyller **alla** kriterierna är placerade i enhetsuppsättningen av. Enhetsuppsättning som du skapar är tillgänglig för alla som har åtkomst till programmet, så vem som helst kan visa, ändra eller ta bort den enheten.

    ![Enheten Ange fråga](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Enheten är en dynamisk fråga. Varje gång du visar en lista med enheter kan finnas det olika enheter i listan. Listan beror på vilka enheter som uppfyller kriterierna för frågan.

1. Välj **Spara**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurera instrumentpanelen för din enhet

När du har skapat din enhetsuppsättning kan du konfigurera dess **instrumentpanelen**. Den **instrumentpanelen** är startsidan där du kan placera bilder och länkar. Du kan också lägga till rutnät som visar enheterna i enhetsuppsättningen av.

1. Välj **enheten anger** på den vänstra navigeringsmenyn.

1. Välj den **instrumentpanelen** fliken.

1. Aktivera **designläge**.

    ![Designläge på](media/howto-use-device-sets/image3.png)

1. Information om att lägga till en avbildning finns [Förbered och ladda upp bilder till din Azure IoT centralt program](howto-prepare-images.md).

1. Lägg till en länk panel:
    1. Välj **länken** i den högra rutan.

        ![Välj länk](media/howto-use-device-sets/image6.png)

    1. Ge din länk en **rubrik**.
    1. Välj en URL som ska öppnas när du klickar på länken.
    1. Ge en beskrivning som visas nedan för länken i **rubrik**.
    1. Välj **Spara**.

        ![Spara länk](media/howto-use-device-sets/image7.png)

    1. Du kan flytta och ändra storlek på panelen länk på den **instrumentpanelen**.

1. Lägg till ett rutnät. Ett rutnät är en tabell med enheter i enheten med de kolumner som du väljer.
    1. Välj **rutnätet** i den högra rutan.

        ![Välj rutnätet](media/howto-use-device-sets/image8.png)

    1. Ge din rutnätet en **rubrik**.
    1. Markera kolumnerna som ska visas genom att välja inställningsknappen. Välj kolumnen visas och välj högerpilen för att markera den i panelen som visas.
    1. Välj **OK**.
    1. Välj **Spara**.

        ![Spara rutnätet](media/howto-use-device-sets/image9.png)

    1. Dra och släpp rutnätet för att placera den på den **instrumentpanelen**.

    > [!NOTE]
    > Du kan lägga till flera avbildningar, länkar och rutnät.
  
    1. Inaktivera **designläge**.

    ![Designläge ut](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Konfigurera platskarta i enheten anger instrumentpanelen 
Du kan lägga till en platskarta som visualiserar platsen för dina enheter anger i en karta. 

För att lägga till en platskarta till enheten som anger instrumentpanelen måste du ha konfigurerade Platsegenskapen i mallen för enheten, se [skapa en plats-egenskap som tillhandahålls av Azure Maps](howto-set-up-template.md).


1. På enheten anger instrumentpanelen, väljer du mappning från biblioteket. 

    ![Enheten anger instrumentpanelen Maps](media/howto-use-device-sets/LocationMaps1.png)


2. Ge ett namn och Välj egenskapen location som du tidigare har konfigurerat som en del av din enhetsegenskap.

    ![Konfigurera instrumentpanelen Maps](media/howto-use-device-sets/LocationMaps2.png)

3. Spara och du ser kartan panelen Visa platsen för dina enheter i den enhetsuppsättning.

    ![Spara instrumentpanelen Maps](media/howto-use-device-sets/LocationMaps3.png)


5. Nu när en operator visar enheten anger instrumentpanelen, hon kan se alla rutor har du konfigurerat inklusive plats karta som visualiserar enheter plats i korthet!

    ![Maps-operatorn instrumentpanelsvy](media/howto-use-device-sets/LocationMaps4.png)

    Du kommer att kunna ändra storlek på kartan för att den har önskad storleken.




## <a name="configure-the-list-for-your-device-set"></a>Konfigurera en lista för din enhet

När du har skapat din enhetsuppsättning kan du konfigurera den **listan**. Den **listan** visas alla enheter i enheten som angetts i en tabell med kolumner som du väljer.

1. Välj **enheten anger** på den vänstra navigeringsmenyn.

1. Välj den **lista** fliken.

1. Välj **kolumnalternativ**.

    ![Kolumnalternativ för](media/howto-use-device-sets/image11.png)

1. Välj kolumnerna som visas genom att markera den kolumn som du vill visa och välja högerpilen för att välja den.

    ![Välj kolumn](media/howto-use-device-sets/image12.png)

1. Välj **OK**.

## <a name="analytics"></a>Analytics

Analyser i enheten anger är samma som huvudsakliga analytics-fliken i den vänstra navigeringsmenyn. Du kan lära dig mer om analytics i artikeln på [hur du skapar analytics](howto-create-analytics.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enheten ställer in i tillämpningsprogrammet Azure IoT Central, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hur du skapar regler för telemetri](howto-create-telemetry-rules.md)
