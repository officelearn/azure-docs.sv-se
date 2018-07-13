---
title: Skapa och hantera regler för telemetri i programmet Azure IoT Central | Microsoft Docs
description: Azure IoT Central telemetri regler hjälper dig att övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skickar ett e-postmeddelande när regeln utlöses.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 083410c6407ce7aa83c3829f884890561b0b44b8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008220"
---
# <a name="create-a-telemetry-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Skapa en regel för telemetri och konfigurera en åtgärd i programmet Azure IoT Central

Du kan använda Microsoft Azure IoT Central för att fjärrövervaka dina anslutna enheter. Azure IoT Central regler gör att du kan övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel e-post eller utlösa arbetsflödet i Microsoft Flow när villkor är uppfyllda. Du kan definiera villkor för att övervaka din enhetsdata och konfigurera åtgärden att anropa med bara några klick. Den här artikeln förklarar telemetri regeln i detalj.

Azure IoT Central använder [telemetri mätningar](howto-set-up-template.md) att samla in enhetsdata. Varje typ av mått har viktiga attribut som definierar måttet. Du kan skapa regler för att övervaka varje typ av enhet mått och aviseringar genereras när regeln utlöses. En telemetri-regel utlöser när den valda enhetstelemetri överskrider ett angivet tröskelvärde.

## <a name="create-a-telemetry-rule"></a>Skapa en regel för telemetri

Det här avsnittet visar hur du skapar en regel för telemetri. Det här exemplet används en ansluten luftkonditionering-enhet som skickar telemetri om temperatur och fuktighet. Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när den går över 80 grader.

1. Gå till sidan med enhetsinformation för den enhet som du lägger till regeln för att.

1. Om du inte skapat några regler ännu kan se du följande skärm:

    ![Inga regler ännu](media/howto-create-telemetry-rules/image1.png)

1. På den **regler** fliken **+ ny regel** att se vilka typer av regler som du kan skapa.

    ![Regeltyper](media/howto-create-telemetry-rules/image2.png)

1. Välj den **telemetri** panelen för att öppna formuläret för att skapa regeln.

    ![Regel för telemetri](media/howto-create-telemetry-rules/image3.png)

1. Välj ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. Om du vill aktivera regeln för alla enheter som skapas från den här mallen omedelbart, växla **Aktivera regel**.

### <a name="configure-the-rule-condition"></a>Konfigurera regelvillkoret

Det här avsnittet visar hur du lägger till ett villkor för att övervaka temperaturen som telemetri.

1. Välj den **+** bredvid **villkor**.

1. Välj den **temperatur** typ av telemetri i listrutan. Sedan väljer du operatorn och ange ett tröskelvärde. Du kan lägga till flera telemetri-villkor. När flera villkor har angetts måste alla villkor vara uppfyllda för regeln för att utlösa.

    ![Lägg till telemetri villkor](media/howto-create-telemetry-rules/image4.png)

    > [!NOTE]
    > Välj minst en telemetri-mått när du definierar ett telemetri-regelvillkor.

1. Klicka på **spara** att spara regeln. Regeln lanseras inom några minuter och börjar övervaka telemetri som skickas till ditt program.

### <a name="add-an-action"></a>Lägga till en åtgärd

hans avsnittet visar hur du lägger till en åtgärd på en regel. Detta visar hur du lägger till e-poståtgärden, men du kan också [Lägg till en åtgärd i Microsoft Flow](howto-add-microsoft-flow.md) till din regel för att sätta igång ett arbetsflöde i Microsoft Flow när regeln utlöses.

> [!NOTE]
> Endast 1 åtgärd kan kopplas till en enda regel just nu.

1. Välj den **+** bredvid **åtgärder**. Här kan du se en lista över tillgängliga åtgärder.

    ![Lägg till åtgärd](media/howto-create-telemetry-rules/image5.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar ska visas i brödtexten i e-postmeddelandet när regeln utlöses.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Läs mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/howto-create-telemetry-rules/image6.png)

1. Klicka på **Spara**. Regeln lanseras inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoren som anges i regeln matchar utlöser regeln konfigurerade e-poståtgärden.

## <a name="parameterize-the-rule"></a>Parameterisera regeln

Regler kan härleda vissa värdena från **enhetsegenskaper** som parametrar. Med parametrar är användbart i scenarier där telemetri tröskelvärden variera för olika enheter. När du skapar regeln väljer du en enhetsegenskap som anger tröskelvärdet som **perfekt maxgränsen**, i stället för att tillhandahålla ett absolut värde, till exempel 80 grader. När regeln körs, matchar enhetstelemetri med värdet som angavs i enhetsegenskapen.

Med parametrar är ett effektivt sätt att minska antalet regler för att hantera per enhet mall.

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en enhetsegenskap så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel kan ta du bort den genom att öppna regeln och välja **ta bort**. Tar bort regeln försvinner den från mallen enhet och alla associerade enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Växla den **aktivera regeln för alla enheter av den här mallen** knappen i regeln aktiverar eller inaktiverar regeln för alla enheter som hör till mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Gå till enheten och välj den regel du vill aktivera eller inaktivera. Visa/Dölj de **aktivera regeln för den här enheten** knappen för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du redigerar regler i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du lägger till en Microsoft Flow-åtgärd på en regel](howto-add-microsoft-flow.md)
> [hur du hanterar dina enheter](howto-manage-devices.md)
