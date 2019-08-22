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
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879947"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Skapa en telemetri-regel och konfigurera meddelanden i ditt Azure IoT Central-program (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande eller utlösare Microsoft Flow. Med bara några klick kan du definiera villkoret för att övervaka enhets data och konfigurera motsvarande åtgärd. Den här artikeln beskriver hur du skapar regler för att övervaka telemetri som skickas av enheten.

Enheter kan använda telemetri mått för att skicka numeriska data från enheten. En regel för telemetri utlöses när den valda enhetens telemetri korsar ett angivet tröskelvärde.

## <a name="create-a-telemetry-rule"></a>Skapa en telemetri-regel

För att skapa en telemetri-regel måste enhets definitionen ha minst en definierad telemetri mått. I det här exemplet används en kyld Vending-enhet som skickar telemetri för temperatur och fuktighet. Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när det hamnar ovanför 80 grader.

1. Gå till sidan **regler** .

1. Om du inte har skapat några regler än visas följande skärm:

    ![Inga regler ännu](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Välj **+ ny regel** för att se vilka typer av regler som du kan skapa.

1. Välj **telemetri** för att skapa en regel för att övervaka telemetri för enheter.

    ![Regel typer](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Ange ett namn som hjälper dig att identifiera regeln och tryck på RETUR.

1. Välj den enhets definition som du vill att regeln ska omfattas av i avsnittet scope. På den här skärmen kan du också filtrera ned de enheter som regeln gäller för genom att använda **+ filter**. Regeln gäller automatiskt för alla enheter under enhets mal len. Om du vill inaktivera regeln väljer du knappen **inaktivera** i rubriken.

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som övervakas av regeln.

1. Välj om du vill **ställa in agg regering** som på eller av.

      - Aggregator är valfritt. Utan agg regering utlöser regeln för varje telemetri-datapunkt som uppfyller villkoret. Om regeln till exempel har kon figurer ATS för att utlösas när temperaturen är över 80, utlöses regeln nästan omedelbart när enheten rapporterar temperatur > 80.
      - Om en mängd funktion, till exempel genomsnitt, min, Max, antal väljs, måste användaren ange ett **tidsfönster** som villkoret måste utvärderas över. Om du till exempel ställer in perioden "5 minuter" och regeln söker efter genomsnitts temperatur över 80, utlöses regeln när genomsnitts temperaturen är över 80 i minst 5 minuter. Regel utvärderings frekvensen är samma som **tids perioden**, vilket innebär att regeln utvärderas var femte minut i det här exemplet.

1. Välj den telemetri som du vill övervaka från List rutan **mått** .

1. Välj sedan en **operator** och ange ett **värde**.

     ![Tillstånd](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>Du kan lägga till mer än en mätning av telemetri genom att välja **+ villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje villkor kopplas ihop med en-och-sats implicit. När du använder agg regering måste varje mått aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

I det här avsnittet visas hur du konfigurerar åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant.

1. Klicka på **+-åtgärden** i avsnittet **Action (åtgärd** ). Här visas en lista över tillgängliga åtgärder.  

    ![Lägg till åtgärd](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Välj **e-** poståtgärden, ange ett visnings namn för åtgärden, en giltig e-postadress i fältet **till** och ange en anteckning som visas i e-postmeddelandets brödtext när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Välj **Slutför**för att spara åtgärden.

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Regler kan härleda vissa Vales från **enhets egenskaper** som parametrar. Att använda parametrar är användbart i scenarier där tröskelvärdena för telemetri varierar för olika enheter. När du skapar regeln väljer du en enhets egenskap som anger tröskelvärdet, till exempel **maximalt idealt tröskelvärde**, i stället för att tillhandahålla ett absolut värde, till exempel 80 grader. När regeln körs matchar den telemetri för enheten med värdet som anges i egenskapen enhet.

Att använda parametrar är ett effektivt sätt att minska antalet regler som ska hanteras.

Åtgärder kan också konfigureras med **egenskapen Device** som en parameter. Om en e-postadress lagras som en egenskap kan den användas när du definierar adressen **till** .

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**. Om du tar bort regeln tas den bort från enhets mal len och alla tillhör ande enheter.

## <a name="enable-or-disable-a-rule"></a>Aktivera eller inaktivera en regel

Välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som omfattas av regeln genom att växla knappen **Aktivera** eller **inaktivera** i regeln.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Välj den regel som du vill aktivera eller inaktivera. Lägg till ett filter i avsnittet **scope** för att ta med eller undanta en viss enhet i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig [hur du hanterar dina enheter](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
