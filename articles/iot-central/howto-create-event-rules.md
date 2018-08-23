---
title: Skapa och hantera händelseregler i ditt program med Azure IoT Central | Microsoft Docs
description: Azure IoT Central-händelseregler hjälper dig att övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skickar ett e-postmeddelande när regeln utlöses.
author: ankitgupta
ms.author: ankitgup
ms.date: 08/14/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 40c7b2865795f8c6a5cfbabe4d59aea1715d4a57
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42060976"
---
# <a name="create-an-eevent-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en regel för eEvent och ställa in meddelanden i Azure IoT Central programmet

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Azure IoT Central-regler kan du övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande eller utlösa Microsoft Flow. Du kan definiera villkor som du vill övervaka din enhetsdata och konfigurera den motsvarande åtgärden med bara några klick. Den här artikeln beskriver hur du skapar regler för att övervaka händelser som skickas av enheten.

Enheter kan använda event mått för att skicka viktiga eller endast i informationssyfte enhetshändelser. En regel för händelsen utlöses när den valda enhet händelsen rapporteras av enheten.

## <a name="create-an-event-rule"></a>Skapa en händelseregel

Om du vill skapa en regel för händelsen måste enheten mallen ha minst en händelse mått har definierats. Det här exemplet används en kylda Varuautomat-enhet som rapporterar en fläkt meddelar felhändelse. Regeln övervakar den händelse som rapporteras av enheten och skickar ett e-postmeddelande varje gång händelsen rapporteras.

1. Använd Device Explorer och navigera till enheten mallen som du lägger till regeln för.

1. Klicka på en befintlig enhet under den valda mallen. 

    >[!TIP] 
    >Om mallen inte har några enheter och sedan lägga till en ny enhet först.

1. Om du inte skapat några regler ännu kan se du följande skärm:

    ![Inga regler ännu](media\howto-create-event-rules\Rules_Landing_Page.png)


1. På den **regler** fliken **+ ny regel** att se vilka typer av regler som du kan skapa.


1. Klicka på den **händelse** panelen för att skapa en regel.

    ![Regeltyper](media\howto-create-event-rules\Rule_Types.png)

    
1. Ange ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. Om du vill aktivera regeln för alla enheter som skapas från den här mallen omedelbart, växla **aktivera regeln för alla enheter för den här mallen**.

    ![Information om regeln](media\howto-create-event-rules\Rule_Detail.png)

    Regeln tillämpas automatiskt på alla enheter under mallen enheten.

### <a name="configure-the-rule-conditions"></a>Konfigurera villkor

Villkoret definierar de kriterier som övervakas av regeln.

1. Välj den **+** bredvid **villkor** att lägga till ett nytt villkor.

1. Välj den händelse som du vill övervaka i listrutan mätning. I det här exemplet **fläkt meddelar fel** händelse har valts.

   ![Tillstånd](media\howto-create-event-rules\Condition_Filled_Out.png) 


1. Du kan också ange **antal** som **aggregering** och ger motsvarande tröskelvärdet.

    - Utan aggregering, regeln utlöses för varje datapunkt för händelsen som uppfyller villkoret. Till exempel om du konfigurerar regelns villkor som utlöser när en fläkt meddelar fel-händelse inträffar utlöser sedan regeln nästan omedelbart när enheten rapporterar att den händelsen.
    - Om antalet används som en mängdfunktion så du måste ange en **tröskelvärdet** och en **sammanställd tidsfönster** över som villkoret måste utvärderas. I det här fallet antal händelser slås samman och regeln utlöser bara om aggregerade händelseantal matchar tröskelvärdet.
 
    Om du vill att varna när det finns fler än tre enhetshändelser inom 5 minuter, därefter händelsen och ange mängdfunktionen som ”antal”, operator som ”större än” och ”tröskelvärde” som 3. Ange ”aggregering tidsperiod” som ”5 minuter”. Regeln utlöses när fler än tre händelser skickas av enheten inom 5 minuter. Utvärderingsfrekvensen regeln är samma som den **sammanställd tidsfönster**, vilket innebär att, i det här exemplet regeln utvärderas var femte minut. 

    ![Lägg till Händelsevillkor](media\howto-create-event-rules\Aggregate_Condition_Filled_Out.png)

    >[!NOTE] 
    >Mer än en händelse mätning kan läggas till under **villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för regeln för att utlösa. Varje villkor får sällskap av en 'Och'-sats implicit. När du använder aggregering, måste varje mätning aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

Det här avsnittet visar hur du ställer in åtgärder att vidta när regeln utlöses. Åtgärder hämta anropas när de villkor som angetts i regeln utvärderas till SANT.

1. Välj den **+** bredvid **åtgärder**. Här kan du se en lista över tillgängliga åtgärder. 

    ![Lägg till åtgärd](media\howto-create-event-rules\Add_Action.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar ska visas i brödtexten i e-postmeddelandet när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Läs mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media\howto-create-event-rules\Configure_Action.png)

1. För att spara regeln, Välj **spara**. Regeln lanseras inom några minuter och börjar övervaka de händelser som skickas till ditt program. När villkoren som anges i regeln matchar utlöser regeln konfigurerade e-poståtgärden.

Du kan lägga till andra åtgärder för regeln, till exempel Microsoft Flow och webhooks. Du kan lägga till upp till 5 åtgärder per regel.

- [Microsoft Flow-åtgärden](howto-add-microsoft-flow.md) sätta igång ett arbetsflöde i Microsoft Flow när en regel utlöses 
- [Webhook-åtgärd](howto-create-webhooks.md) att meddela andra tjänster när en regel utlöses

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en enhetsegenskap så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel kan ta du bort den genom att öppna regeln och välja **ta bort**. Tar bort regeln försvinner den från mallen enhet och alla associerade enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för alla enheter av den här mallen** knappen för att aktivera eller inaktivera regeln för alla enheter som är associerad med mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för den här enheten** knappen för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i Azure IoT Central programmet, är här några nästa steg:

- [Lägg till Microsoft Flow-åtgärd i regler](howto-add-microsoft-flow.md)
- [Lägg till Webhook-åtgärd i regler](howto-create-webhooks.md)
- [Så här hanterar du dina enheter](howto-manage-devices.md)
