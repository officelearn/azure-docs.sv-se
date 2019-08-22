---
title: Skapa och hantera händelse regler i ditt Azure IoT Central-program | Microsoft Docs
description: Med händelse regler för Azure IoT Central kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande, när regeln utlöses.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879960"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Skapa en händelse regel och konfigurera aviseringar i ditt Azure IoT Central-program (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande. Med bara några klick kan du definiera villkoret för att övervaka enhets data och konfigurera motsvarande åtgärd. Den här artikeln beskriver hur du skapar regler för att övervaka händelser som skickas av enheten.

Enheter kan använda händelse mätningar för att skicka viktiga eller informations enhets händelser. En händelse regel utlöses när den valda enhets händelsen rapporteras av enheten.

## <a name="create-an-event-rule"></a>Skapa en händelseregel

Om du vill skapa en händelse regel måste enhets mal len ha minst ett definierat händelse mått. I det här exemplet används en dator som har en kyld Vending som rapporterar en fel händelse för fläkt motor. Regeln övervakar händelsen som rapporteras av enheten och skickar ett e-postmeddelande varje gång händelsen rapporteras.

1. Gå till sidan **regler** .

1. Om du inte har skapat några regler än visas följande skärm:

   ![Inga regler ännu](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Välj **+ ny regel** för att se vilka typer av regler som du kan skapa.

1. Välj **händelse** för att skapa en händelse övervaknings regel.

   ![Regel typer](media/howto-create-event-rules-pnp/rule-types1.png)

1. Ange ett namn som hjälper dig att identifiera regeln och tryck på RETUR.

1. Välj den enhets definition som regeln ska omfattas av i avsnittet **scope** . På den här skärmen kan du också filtrera ned de enheter som regeln gäller för genom att använda **+ filter**. Regeln gäller automatiskt för alla enheter under enhets mal len. Om du vill inaktivera regeln väljer du knappen **inaktivera** i rubriken.

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som övervakas av regeln.

1. Välj om du vill **ställa in agg regering** som på eller av.

   - Utan agg regering utlöser regeln för varje händelse data punkt som uppfyller villkoret. Om du till exempel konfigurerar regelns villkor till Utlös när en **fläkt motor fel** händelse inträffar, utlöser regeln nästan omedelbart när enheten rapporterar händelsen.
   - Om **Count** används som en mängd funktion måste du ange ett **värde** och ett **tidsintervall** under vilket villkoret måste utvärderas. I det här fallet aggregeras antalet händelser och regeln utlöser endast om det sammanlagda antalet händelser matchar värdet.

1. Välj den händelse som du vill övervaka från List rutan **mått** . I det här exemplet har **fläkt motor fel** händelsen valts.

1. Alternativt kan du också ange **Count** som **agg regering** och ange det värde som regeln ska utlösas av.

     Om du till exempel vill Avisera när det finns fler än tre enhets händelser inom 5 minuter väljer du händelsen och anger mängd funktionen **antal**, operatorn som är **större än**och **värdet** som 3. Ställ in **tids perioden** på **5 minuter**. Regeln utlöses när fler än tre händelser skickas av enheten inom 5 minuter. Regel utvärderings frekvensen är samma som **tids perioden**, vilket innebär att regeln utvärderas var femte minut i det här exemplet.

 ![Tillstånd](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> Fler än en händelse mätning kan läggas till under **villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje villkor kopplas ihop med en-och-sats implicit. När du använder agg regering måste varje mått aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

I det här avsnittet visas hur du konfigurerar åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant.

1. Klicka på **+-åtgärden** i avsnittet **Action (åtgärd** ). Här visas en lista över tillgängliga åtgärder.  

   ![Lägg till åtgärd](media/howto-create-event-rules-pnp/add-action1.png)

1. Välj **e-** poståtgärden, ange ett visnings namn för åtgärden, en giltig e-postadress i fältet **till** och ange en anteckning som visas i e-postmeddelandets brödtext när regeln utlöses.

   > [!NOTE]
   > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-event-rules-pnp/configure-action1.png)

1. Välj **Slutför**för att spara åtgärden.

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka händelser som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Regler kan härleda vissa Vales från **enhets egenskaper** som parametrar. Att använda parametrar är användbart i scenarier där händelse trösklar varierar för olika enheter. När du skapar regeln väljer du en enhets egenskap som anger tröskelvärdet, till exempel **maximalt idealt tröskelvärde**, i stället för att tillhandahålla ett absolut värde, till exempel tre händelser. När regeln körs matchar den enhets händelser med värdet som anges i egenskapen enhet.

Att använda parametrar är ett effektivt sätt att minska antalet regler som ska hanteras.

Åtgärder kan också konfigureras med **egenskapen Device** som en parameter. Om en e-postadress lagras som en enhets egenskap kan den användas när du definierar adressen **till** .

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**. Om du tar bort regeln tas den bort från enhets mal len och alla tillhör ande enheter.

## <a name="enable-or-disable-a-rule"></a>Aktivera eller inaktivera en regel

Välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som omfattas av regeln genom att växla knappen **Aktivera** eller **inaktivera** i regeln.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Välj den regel som du vill aktivera eller inaktivera. Lägg till ett filter i avsnittet **scope** för att ta med eller undanta en viss enhet i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig [hur du hanterar dina enheter](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
