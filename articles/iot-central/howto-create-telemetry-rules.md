---
title: Skapa och hantera regler för telemetri i programmet Azure IoT Central | Microsoft Docs
description: Azure IoT Central telemetri regler hjälper dig att övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skickar ett e-postmeddelande när regeln utlöses.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5f6bc30c318e2f5511b352f1a52f0a5360e4b6f1
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367898"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en regel för telemetri och konfigurera meddelanden med Azure IoT Central programmet

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Azure IoT Central-regler kan du övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande eller utlösa Microsoft Flow. Du kan definiera villkor som du vill övervaka din enhetsdata och konfigurera den motsvarande åtgärden med bara några klick. Den här artikeln beskriver hur du skapar regler för att övervaka telemetri som skickas av enheten.

Enheter kan använda telemetri mätning skicka numeriska data från enheten. En telemetri-regel utlöser när den valda enhetstelemetri överskrider ett angivet tröskelvärde.

## <a name="create-a-telemetry-rule"></a>Skapa en regel för telemetri

Om du vill skapa en regel för telemetri måste mallen enheten ha minst en telemetri-mätning som definierats. Det här exemplet används en kylda Varuautomat-enhet som skickar telemetri om temperatur och fuktighet. Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när den går över 80 grader.

1. Med hjälp av den **enheten mallar** sidan, gå till enheten mallen som du lägger till regeln för.

1. Om du inte skapat några regler ännu kan se du följande skärm:

    ![Inga regler ännu](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. På den **regler** fliken **+ ny regel** att se vilka typer av regler som du kan skapa.

1. Välj **telemetri** att skapa en regel för att övervaka enheternas telemetri.

    ![Regeltyper](media/howto-create-telemetry-rules/Rule_Types.png)

1. Ange ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. Om du vill aktivera regeln för alla enheter som skapats för den här mallen omedelbart, växla **aktivera regeln för alla enheter för den här mallen**.

   ![Information om regeln](media/howto-create-telemetry-rules/Rule_Detail.png)

    Regeln tillämpas automatiskt på alla enheter under mallen enheten.

### <a name="configure-the-rule-conditions"></a>Konfigurera villkor

Villkoret definierar de kriterier som övervakas av regeln.

1. Välj **+** bredvid **villkor** att lägga till ett nytt villkor.

1. Välj den telemetri som du vill övervaka den **mätning** listrutan.

1. Välj sedan **aggregering**, **operatorn**, och ange en **tröskelvärdet** värde.
   - Aggregering är valfritt. Utan aggregering, regeln utlöses för varje datapunkt för telemetri som uppfyller villkoret. Till exempel om regeln är konfigurerad att utlösaren när temperaturen är över 80 sedan regeln utlöses nästan omedelbart när enheten rapporterar temperatur > 80.
   - Om en mängdfunktion som genomsnitt, Min, Max, Count väljs sedan användaren måste ange en **sammanställd tidsfönster** över som villkoret måste utvärderas. Till exempel om du ställer in söker perioden som ”5 minuter” och regeln efter medeltemperaturen ovan 80, regeln utlöses när medeltemperaturen är över 80 för minst 5 minuter. Utvärderingsfrekvensen regeln är samma som den **sammanställd tidsfönster**, vilket innebär att, i det här exemplet regeln utvärderas var femte minut.

     ![Tillstånd](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

     >[!NOTE]
     >Mer än en telemetri-mätning kan läggas till under **villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för regeln för att utlösa. Varje villkor får sällskap av en 'Och'-sats implicit. När du använder aggregering, måste varje mätning aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

Det här avsnittet visar hur du ställer in åtgärder att vidta när regeln utlöses. Åtgärder hämta anropas när de villkor som angetts i regeln utvärderas till SANT.

1. Välj den **+** bredvid **åtgärder**. Här kan du se en lista över tillgängliga åtgärder.  

    ![Lägg till åtgärd](media/howto-create-telemetry-rules/Add_Action.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar ska visas i brödtexten i e-postmeddelandet när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Läs mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-telemetry-rules/Configure_Action.png)

1. För att spara regeln, Välj **spara**. Regeln lanseras inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoren som anges i regeln uppfylls utlöser regeln konfigurerade e poståtgärd.

Du kan lägga till andra åtgärder för regeln, till exempel Microsoft Flow och webhooks. Du kan lägga till upp till 5 åtgärder per regel.

- [Microsoft Flow-åtgärden](howto-add-microsoft-flow.md) sätta igång ett arbetsflöde i Microsoft Flow när en regel utlöses 
- [Webhook-åtgärd](howto-create-webhooks.md) att meddela andra tjänster när en regel utlöses

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Regler kan härleda vissa värdena från **enhetsegenskaper** som parametrar. Med parametrar är användbart i scenarier där telemetri tröskelvärden variera för olika enheter. När du skapar regeln väljer du en enhetsegenskap som anger tröskelvärdet som **perfekt maxgränsen**, i stället för att tillhandahålla ett absolut värde, till exempel 80 grader. När regeln körs, matchar enhetstelemetri med värdet som anges i enhetsegenskapen.

Med parametrar är ett effektivt sätt att minska antalet regler för att hantera per enhet mall.

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en egenskap, så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel kan ta du bort den genom att öppna regeln och välja **ta bort**. Tar bort regeln försvinner den från mallen enhet och alla associerade enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för alla enheter av den här mallen** knappen regeln för att aktivera eller inaktivera regeln för alla enheter som är associerad med mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för den här enheten** knappen för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i Azure IoT Central programmet, är här några nästa steg:

- [Lägg till Microsoft Flow-åtgärd i regler](howto-add-microsoft-flow.md)
- [Lägg till Webhook-åtgärd i regler](howto-create-webhooks.md)
- [Så här hanterar du dina enheter](howto-manage-devices.md)
