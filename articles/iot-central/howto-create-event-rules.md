---
title: Skapa och hantera händelse regler i ditt Azure IoT Central-program | Microsoft Docs
description: Med händelse regler för Azure IoT Central kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande, när regeln utlöses.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c931cfbcff750d96828641669c4aaa15e7932970
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877416"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en händelse regel och konfigurera aviseringar i ditt Azure IoT Central-program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande eller utlösare Microsoft Flow. Med bara några klick kan du definiera villkoret för att övervaka enhets data och konfigurera motsvarande åtgärd. Den här artikeln beskriver hur du skapar regler för att övervaka händelser som skickas av enheten.

Enheter kan använda händelse mätningar för att skicka viktiga eller informations enhets händelser. En händelse regel utlöses när den valda enhets händelsen rapporteras av enheten.

## <a name="create-an-event-rule"></a>Skapa en händelseregel

Om du vill skapa en händelse regel måste enhets mal len ha minst ett definierat händelse mått. I det här exemplet används en dator som har en kyld Vending som rapporterar en fel händelse för fläkt motor. Regeln övervakar händelsen som rapporteras av enheten och skickar ett e-postmeddelande varje gång händelsen rapporteras.

1. Använd sidan **enhetsspecifika** och navigera till den enhets mall som du lägger till regeln för.

1. Om du inte har skapat några regler än visas följande skärm:

    ![Inga regler ännu](media/howto-create-event-rules/rules_landing_page1.png)

1. På fliken **regler** väljer du **+ ny regel** för att se vilka typer av regler som du kan skapa.

1. Välj **händelse** panelen för att skapa en händelse övervaknings regel.

    ![Regel typer](media/howto-create-event-rules/rule_types1.png)

1. Ange ett namn som hjälper dig att identifiera regeln i den här enhets mal len.

1. Om du vill aktivera regeln direkt för alla enheter som skapats från den här mallen växlar du **Aktivera regel för alla enheter i den här mallen**.

    ![Regel information](media/howto-create-event-rules/rule_detail1.png)

    Regeln gäller automatiskt för alla enheter under enhets mal len.

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som övervakas av regeln.

1. Välj bredvid villkor för att lägga till ett nytt villkor. **+**

1. Välj den händelse som du vill övervaka från List rutan mått. I det här exemplet har **fläkt motor fel** händelsen valts.

   ![Tillstånd](media/howto-create-event-rules/condition_filled_out1.png)

1. Alternativt kan du också ange **Count** som **agg regering** och ange motsvarande tröskelvärde.

   - Utan agg regering utlöser regeln för varje händelse data punkt som uppfyller villkoret. Om du till exempel konfigurerar regelns villkor till Utlös när en **fläkt motor fel** händelse inträffar, utlöser regeln nästan omedelbart när enheten rapporterar händelsen.
   - Om Count används som en mängd funktion måste du ange ett **tröskelvärde** och ett sammanställd **tids** period som villkoret måste utvärderas för. I det här fallet aggregeras antalet händelser och regeln utlöser endast om det sammanlagda antalet händelser matchar tröskelvärdet.

     Om du till exempel vill Avisera när det finns fler än tre enhets händelser inom 5 minuter väljer du händelsen och ställer in mängd funktionen som "antal", operatorn som "större än" och "Threshold" som 3. Ange "agg regerings tids period" som "5 minuter". Regeln utlöses när fler än tre händelser skickas av enheten inom 5 minuter. Regel utvärderings frekvensen är samma som den **sammanställda tids perioden**, vilket innebär att regeln utvärderas var femte minut i det här exemplet.

     ![Lägg till händelse villkor](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Fler än en händelse mätning kan läggas till under **villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje villkor kopplas ihop med en-och-sats implicit. När du använder agg regering måste varje mått aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

I det här avsnittet visas hur du konfigurerar åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant.

1. Välj bredvid åtgärder. **+** Här visas en lista över tillgängliga åtgärder.

    ![Lägg till åtgärd](media/howto-create-event-rules/add_action1.png)

1. Välj **e-** poståtgärden, ange en giltig e-postadress i fältet **till** och ange en anteckning som visas i e-postmeddelandets brödtext när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-event-rules/configure_action1.png)

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka de händelser som skickas till ditt program. När villkoret som anges i regeln matchar, utlöser regeln den konfigurerade e-poståtgärden.

Du kan lägga till andra åtgärder till regeln, till exempel Microsoft Flow och Webhooks. Du kan lägga till upp till 5 åtgärder per regel.

- [Microsoft Flow åtgärd](howto-add-microsoft-flow.md) för att starta ett arbets flöde i Microsoft Flow när en regel utlöses 
- [Webhook-åtgärd](howto-create-webhooks.md) för att meddela andra tjänster när en regel utlöses

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Åtgärder kan också konfigureras med **egenskapen Device** som en parameter. Om en e-postadress lagras som en enhets egenskap kan den användas när du definierar adressen **till** .

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**. Om du tar bort regeln tas den bort från enhets mal len och alla tillhör ande enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en enhets mall

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som är associerade med enhets mal len genom att aktivera eller inaktivera regeln för **alla enheter med den här mallen** .

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för enheten genom att växla till knappen **Aktivera regel för den här enheten** .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT Central-program kan du använda följande steg:

- [Lägg till Microsoft Flow åtgärd i regler](howto-add-microsoft-flow.md)
- [Lägg till webhook-åtgärd i regler](howto-create-webhooks.md)
- [Gruppera flera åtgärder för att köra från en eller flera regler](howto-use-action-groups.md)
- [Hantera dina enheter](howto-manage-devices.md)
