---
title: Skapa och hantera händelseregler i ditt Azure IoT centrala program | Microsoft Docs
description: Azure IoT Central händelseregler kan du övervaka dina enheter i nära realtid och anropa åtgärder, till exempel skicka ett e-postmeddelande när regeln utlöser automatiskt.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 30223fdca9d848ddc407981bf4a3ca683a10575a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628376"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en regel för händelsen och ställa in meddelanden i ditt Azure IoT centrala program

Du kan använda Microsoft Azure IoT Central fjärrövervaka dina anslutna enheter. Azure IoT Central regler kan du övervaka dina enheter i nära realtid och anropa åtgärder, till exempel skicka ett e-postmeddelande när regeln utlöser automatiskt. Du kan definiera villkoret för att övervaka dina data på enheten och konfigurera åtgärden som ska anropa i bara några klickningar. Den här artikeln förklarar händelseövervakning regeln i detalj.

Azure IoT Central används [händelse mätning](howto-set-up-template.md) att samla in data på enheten. Varje typ av mått har nyckelattribut som definierar måttet. Du kan skapa regler för att övervaka varje typ av enhet mått och genererar aviseringar när regeln utlöser. En regel för händelsen utlöses när den valda enhet händelsen rapporteras av enheten.

## <a name="create-an-event-rule"></a>Skapa en händelseregel

Det här avsnittet visar hur du skapar en regel för händelsen. Det här exemplet används en kylda Varuautomat enhet rapporter fläkt meddelar felhändelsen. Regeln övervakar den händelse som rapporterats av enheten och skickar ett e-postmeddelande när händelsen rapporteras.

1. Gå till enheten informationssidan för enheten som du lägger till rollen till.

1. Om du inte ännu skapat några regler, se följande skärm:

    ![Inga regler ännu](media\howto-create-event-rules\image1.png)

1. På den **regler** , Välj **+ ny regel** att se vilka typer av regler som du kan skapa.

    ![Regeltyper](media\howto-create-event-rules\image2.png)

1. Klicka på **händelse** att öppna formuläret för att skapa regeln.

    ![Regel för händelser](media\howto-create-event-rules\image3.png)

1. Välj ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. För att omedelbart aktivera regeln för alla enheter som skapas från den här mallen, växla **aktivera regeln**.

### <a name="configure-the-rule-condition"></a>Konfigurera regelvillkor

Det här avsnittet visar hur du lägger till ett villkor för att övervaka fläkt meddelar fel händelse mått.

1. Välj den **+** bredvid **villkoret**.

1. Välj händelse mått i listrutan som du vill övervaka. I det här exemplet **fläkt meddelar fel** händelse har valts.

1. Du kan du också ange ett värde om du vill övervaka ett visst värde för den händelse som rapporterats av enheten. Till exempel om enheten rapporterar samma händelse med olika felkoder sedan ange felkoden som ett värde i regelns villkor säkerställer att regeln utlösare endast när enheten skickar specifika värdet som händelsenyttolasten. Lämna detta tomt innebär att regeln ska utlösa när enheten skickar händelsen oavsett Händelsevärde.

    ![Lägg till Händelsevillkor](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Du måste välja minst en händelse mätning när du definierar ett villkor för händelsen.

### <a name="configure-the-action"></a>Konfigurera åtgärden

Det här avsnittet visas hur du anger vad regeln inte när villkoret matchar genom att lägga till en åtgärd.

1. Välj den **+** bredvid **åtgärder**. Här ser du listan över tillgängliga åtgärder. Offentliga förhandsversionen **e-post** är den enda åtgärden som stöds.

    ![Lägg till åtgärd](media\howto-create-event-rules\image5.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar som visas i brödtexten i e-postmeddelandet när regeln utlöser.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Lär dig mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media\howto-create-event-rules\image6.png)

1. För att spara regeln, Välj **spara**. Regeln lanseras inom några minuter och startar övervakning av händelser som skickas till ditt program. När villkoret i regeln matchar utlöser regeln konfigurerade e-post-åtgärd.

## <a name="parameterize-the-rule"></a>Parameterstyra regeln

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en enhetsegenskap för, så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel, tar du bort den genom att öppna regeln och väljer **ta bort**. Ta bort regeln tas den bort från mallen enhet och alla tillhörande enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Växla mellan den **aktivera regeln för alla enheter i den här mallen** knappen i regeln aktiverar eller inaktiverar regeln för alla enheter som är associerad med mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Växla den **aktivera regeln för den här enheten** för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT centrala program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md).