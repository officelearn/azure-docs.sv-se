---
title: Skapa och hantera händelseregler i ditt program med Azure IoT Central | Microsoft Docs
description: Azure IoT Central-händelseregler hjälper dig att övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skickar ett e-postmeddelande när regeln utlöses.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: ede7748b1471136cf792c2b30b7c90e12b0b274a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006856"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Skapa en regel för händelser och konfigurera en åtgärd i programmet Azure IoT Central

Du kan använda Microsoft Azure IoT Central för att fjärrövervaka dina anslutna enheter. Azure IoT Central regler gör att du kan övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel e-post eller utlösa arbetsflödet i Microsoft Flow när villkor är uppfyllda. Du kan definiera villkor för att övervaka din enhetsdata och konfigurera åtgärden att anropa med bara några klick. Den här artikeln förklarar händelse övervakningsregel i detalj.

Azure IoT Central använder [händelse mätning](howto-set-up-template.md) att samla in enhetsdata. Varje typ av mått har viktiga attribut som definierar måttet. Du kan skapa regler för att övervaka varje typ av enhet mått och aviseringar genereras när regeln utlöses. En regel för händelsen utlöses när den valda enhet händelsen rapporteras av enheten.

## <a name="create-an-event-rule"></a>Skapa en händelseregel

Det här avsnittet visar hur du skapar en regel för händelsen. Det här exemplet används en kylda Varuautomat enhet rapporter fläkt meddelar felhändelsen. Regeln övervakar den händelse som rapporteras av enheten och skickar ett e-postmeddelande varje gång händelsen rapporteras.

1. Gå till sidan med enhetsinformation för den enhet som du lägger till regeln för att.

1. Om du inte skapat några regler ännu kan se du följande skärm:

    ![Inga regler ännu](media/howto-create-event-rules/image1.png)

1. På den **regler** fliken **+ ny regel** att se vilka typer av regler som du kan skapa.

    ![Regeltyper](media/howto-create-event-rules/image2.png)

1. Klicka på **händelse** att öppna formuläret för att skapa regeln.

    ![Regel för händelser](media/howto-create-event-rules/image3.png)

1. Välj ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. Om du vill aktivera regeln för alla enheter som skapas från den här mallen omedelbart, växla **Aktivera regel**.

### <a name="configure-the-rule-condition"></a>Konfigurera regelvillkoret

Det här avsnittet visar hur du lägger till ett villkor för att övervaka fläkt meddelar fel händelse mätning.

1. Välj den **+** bredvid **villkor**.

1. Välj event-mått i listrutan som du vill övervaka. I det här exemplet **fläkt meddelar fel** händelse har valts.

1. Du kan också kan du också ange ett värde om du vill övervaka ett visst värde för den händelse som rapporteras av enheten. Exempelvis om enheten rapporterar samma händelse med olika felkoder sedan tillhandahålla felkoden som ett värde i regelns villkor kommer att regeln utlöses endast när enheten skickar det specifika värdet som händelsenyttolast. Lämna tomt innebär att regeln aktiveras när enheten skickar händelsen oavsett Händelsevärde.

    ![Lägg till Händelsevillkor](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > Du måste välja minst en händelse mätning när du definierar ett villkor för händelsen.

1. Klicka på **spara** att spara regeln. Regeln lanseras inom några minuter och börjar övervaka händelser som skickas till ditt program.

### <a name="add-an-action"></a>Lägga till en åtgärd

Det här avsnittet visar hur du lägger till en åtgärd på en regel. Detta visar hur du lägger till e-poståtgärden, men du kan också [Lägg till en åtgärd i Microsoft Flow](howto-add-microsoft-flow.md) till din regel för att sätta igång ett arbetsflöde i Microsoft Flow när regeln utlöses.

> [!NOTE]
> Endast 1 åtgärd kan kopplas till en enda regel just nu.

1. Välj den **+** bredvid **åtgärder**. Här kan du se en lista över tillgängliga åtgärder.

    ![Lägg till åtgärd](media/howto-create-event-rules/image5.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar ska visas i brödtexten i e-postmeddelandet när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Läs mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-event-rules/image6.png)

1. Klicka på **Spara**. Regeln lanseras inom några minuter och börjar övervaka de händelser som skickas till ditt program. När villkoren som anges i regeln matchar utlöser regeln konfigurerade e-poståtgärden.

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en enhetsegenskap så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel kan ta du bort den genom att öppna regeln och välja **ta bort**. Tar bort regeln försvinner den från mallen enhet och alla associerade enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Växla den **aktivera regeln för alla enheter av den här mallen** knappen i regeln aktiverar eller inaktiverar regeln för alla enheter som hör till mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för den här enheten** knappen för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du lägger till en Microsoft Flow-åtgärd på en regel](howto-add-microsoft-flow.md)
> [hur du hanterar dina enheter](howto-manage-devices.md).
