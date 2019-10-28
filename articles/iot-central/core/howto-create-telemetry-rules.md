---
title: Skapa och hantera regler för telemetri i ditt Azure IoT Central-program | Microsoft Docs
description: Med regler för Azure IoT Central telemetri kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande, när regeln utlöses.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d586f899a46a1404af6e798eda4d783a5a7ead85
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953790"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en regel för telemetri och konfigurera aviseringar i ditt Azure IoT Central-program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande eller utlösare Microsoft Flow. Med bara några klick kan du definiera villkoret för att övervaka enhets data och konfigurera motsvarande åtgärd. Den här artikeln beskriver hur du skapar regler för att övervaka telemetri som skickas av enheten.

Enheter kan använda telemetri mått för att skicka numeriska data från enheten. En regel för telemetri utlöses när den valda enhetens telemetri korsar ett angivet tröskelvärde.

## <a name="create-a-telemetry-rule"></a>Skapa en telemetri-regel

För att skapa en telemetri-regel måste enhets mal len ha minst en definierad telemetri mått. I det här exemplet används en kyld Vending-enhet som skickar telemetri för temperatur och fuktighet. Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när det hamnar ovanför 80 grader.

1. Använd sidan **enhetsspecifika** och navigera till den enhets mall som du lägger till regeln för.

1. Om du inte har skapat några regler än visas följande skärm:

    ![Inga regler ännu](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. På fliken **regler** väljer du **+ ny regel** för att se vilka typer av regler som du kan skapa.

1. Välj **telemetri** för att skapa en regel för att övervaka telemetri för enheter.

    ![Regel typer](media/howto-create-telemetry-rules/rule_types1.png)

1. Ange ett namn som hjälper dig att identifiera regeln i den här enhets mal len.

1. Om du vill aktivera regeln direkt för alla enheter som skapats för den här mallen kan du växla **Aktivera regel för alla enheter för den här mallen**.

   ![Regel information](media/howto-create-telemetry-rules/rule_detail1.png)

    Regeln gäller automatiskt för alla enheter under enhets mal len.

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som övervakas av regeln.

1. Välj **+** bredvid **villkor** för att lägga till ett nytt villkor.

1. Välj den telemetri som du vill övervaka från List rutan **mått** .

1. Välj sedan **agg regering**, **operator**och ange ett **tröskelvärde** .
   - Aggregator är valfritt. Utan agg regering utlöser regeln för varje telemetri-datapunkt som uppfyller villkoret. Om regeln till exempel har kon figurer ATS för att utlösas när temperaturen är över 80, utlöses regeln nästan omedelbart när enheten rapporterar temperatur > 80.
   - Om en mängd funktion som till exempel Average, min, Max, antal väljs, måste användaren ange ett **sammanställt tids fönster** som villkoret måste utvärderas över. Om du till exempel ställer in perioden "5 minuter" och regeln söker efter genomsnitts temperatur över 80, utlöses regeln när genomsnitts temperaturen är över 80 i minst 5 minuter. Regel utvärderings frekvensen är samma som den **sammanställda tids perioden**, vilket innebär att regeln utvärderas var femte minut i det här exemplet.

     ![Tillstånd](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Mer än en mätning av telemetri kan läggas till under **villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje conditon kopplas ihop med en AND-sats implicit. När du använder agg regering måste varje mått aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

I det här avsnittet visas hur du konfigurerar åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant.

1. Välj **+** bredvid **åtgärder**. Här visas en lista över tillgängliga åtgärder.  

    ![Lägg till åtgärd](media/howto-create-telemetry-rules/add_action1.png)

1. Välj **e-** poståtgärden, ange en giltig e-postadress i fältet **till** och ange en anteckning som visas i e-postmeddelandets brödtext när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-telemetry-rules/configure_action1.png)

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

Du kan lägga till andra åtgärder till regeln, till exempel Microsoft Flow och Webhooks. Du kan lägga till upp till 5 åtgärder per regel.

- [Microsoft Flow åtgärd](howto-add-microsoft-flow.md) för att starta ett arbets flöde i Microsoft Flow när en regel utlöses 
- [Webhook-åtgärd](howto-create-webhooks.md) för att meddela andra tjänster när en regel utlöses

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Regler kan härleda vissa Vales från **enhets egenskaper** som parametrar. Att använda parametrar är användbart i scenarier där tröskelvärdena för telemetri varierar för olika enheter. När du skapar regeln väljer du en enhets egenskap som anger tröskelvärdet, till exempel **maximalt idealt tröskelvärde**, i stället för att tillhandahålla ett absolut värde, till exempel 80 grader. När regeln körs matchar den telemetri för enheten med värdet som anges i egenskapen enhet.

Att använda parametrar är ett effektivt sätt att minska antalet regler som ska hanteras per enhets mall.

Åtgärder kan också konfigureras med **egenskapen Device** som en parameter. Om en e-postadress lagras som en egenskap kan den användas när du definierar adressen **till** .

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**. Om du tar bort regeln tas den bort från enhets mal len och alla tillhör ande enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en enhets mall

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som är associerade med enhets mal len genom att växla regeln **Aktivera regel för alla enheter med den här mallen** .

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för enheten genom att växla till knappen **Aktivera regel för den här enheten** .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT Central-program kan du använda följande steg:

- [Lägg till Microsoft Flow åtgärd i regler](howto-add-microsoft-flow.md)
- [Lägg till webhook-åtgärd i regler](howto-create-webhooks.md)
- [Gruppera flera åtgärder för att köra från en eller flera regler](howto-use-action-groups.md)
- [Hantera dina enheter](howto-manage-devices.md)
