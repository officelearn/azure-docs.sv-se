---
title: Skapa och hantera telemetri regler i dina Azure IoT centralt program | Microsoft Docs
description: Azure IoT Central telemetri regler kan du övervaka dina enheter i nära realtid och anropa åtgärder, till exempel skicka ett e-postmeddelande när regeln utlöser automatiskt.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629821"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Skapa en regel för telemetri och ställa in meddelanden i ditt Azure IoT centrala program

Du kan använda Microsoft Azure IoT Central fjärrövervaka dina anslutna enheter. Azure IoT Central regler kan du övervaka dina enheter i nära realtid och anropa åtgärder, till exempel skicka ett e-postmeddelande när regeln utlöser automatiskt. Du kan definiera villkoret för att övervaka dina data på enheten och konfigurera åtgärden som ska anropa i bara några klickningar. Den här artikeln förklarar telemetri regeln i detalj.

Azure IoT Central används [telemetri mätningar](howto-set-up-template.md) att samla in data på enheten. Varje typ av mått har nyckelattribut som definierar måttet. Du kan skapa regler för att övervaka varje typ av enhet mått och genererar aviseringar när regeln utlöser. En regel för telemetri startar när den valda enhetstelemetrin överskrider angiven tröskel.

## <a name="create-a-telemetry-rule"></a>Skapa en regel för telemetri

Det här avsnittet visar hur du skapar en regel för telemetri. Det här exemplet används en ansluten luftkonditionering enhet som skickar temperatur- och fuktighetskonsekvens telemetri. Regeln övervakar temperatur som rapporteras av enheten och skickar ett e-postmeddelande när den går över 80 grader.

1. Gå till enheten informationssidan för enheten som du lägger till rollen till.

1. Om du inte ännu skapat några regler, se följande skärm:

    ![Inga regler ännu](media\howto-create-telemetry-rules\image1.png)

1. På den **regler** , Välj **+ ny regel** att se vilka typer av regler som du kan skapa.

    ![Regeltyper](media\howto-create-telemetry-rules\image2.png)

1. Välj den **telemetri** rutan för att öppna formuläret för att skapa regeln.

    ![Telemetri regel](media\howto-create-telemetry-rules\image3.png)

1. Välj ett namn som hjälper dig att identifiera regeln i den här mallen för enheten.

1. För att omedelbart aktivera regeln för alla enheter som skapas från den här mallen, växla **aktivera regeln**.

### <a name="configure-the-rule-condition"></a>Konfigurera regelvillkor

Det här avsnittet visar hur du lägger till ett villkor för att övervaka temperatur telemetri.

1. Välj den **+** bredvid **villkoret**.

1. Välj den **temperatur** telemetri typ i listrutan. Sedan väljer du operatorn och ange ett tröskelvärde. Du kan lägga till flera telemetri villkor. Alla villkor som måste uppfyllas för regeln för att utlösa när flera villkor har angetts.

    ![Lägg till villkor telemetri](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Välj minst en telemetri mått när du definierar ett regelvillkor telemetri.

### <a name="configure-the-action"></a>Konfigurera åtgärden

Det här avsnittet visas hur du anger vad regeln inte när villkoret matchar genom att lägga till en åtgärd.

1. Välj den **+** bredvid **åtgärder**. Här ser du listan över tillgängliga åtgärder. Offentliga förhandsversionen **e-post** är den enda åtgärden som stöds.

    ![Lägg till åtgärd](media\howto-create-telemetry-rules\image5.png)

1. Välj den **e-post** åtgärd, ange en giltig e-postadress i den **till** fältet och ange en kommentar som visas i brödtexten i e-postmeddelandet när regeln utlöser.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till programmet och har loggat in minst en gång. Lär dig mer om [Användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media\howto-create-telemetry-rules\image6.png)

1. För att spara regeln, Välj **spara**. Regeln lanseras inom några minuter och startar övervakning av telemetri som skickas till ditt program. När villkoret i regeln matchar utlöser regeln konfigurerade e-post-åtgärd.

## <a name="parameterize-the-rule"></a>Parameterstyra regeln

Regler kan härleda vissa vales från **enhetsegenskaper** som parametrar. Med parametrar är användbart i scenarier där telemetri tröskelvärden variera för olika enheter. När du skapar regeln väljer du en enhetsegenskap som anger tröskelvärdet som **maximala perfekt tröskelvärdet**, i stället för att tillhandahålla ett absolut värde, till exempel 80 grader. När regeln utförs, matchar enhetstelemetrin med värdet som angavs i egenskapen enhet.

Med parametrar är ett effektivt sätt att minska antalet regler för att hantera per enhet mall.

Åtgärder kan också konfigureras med hjälp av **enhetsegenskap** som en parameter. Om en e-postadress lagras som en enhetsegenskap för, så den kan användas när du definierar den **till** adress.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel, tar du bort den genom att öppna regeln och väljer **ta bort**. Ta bort regeln tas den bort från mallen enhet och alla tillhörande enheter.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivera eller inaktivera en regel för en mall för enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Växla mellan den **aktivera regeln för alla enheter i den här mallen** knappen i regeln aktiverar eller inaktiverar regeln för alla enheter som är associerad med mallen för enheten.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Navigera till enheten och välj den regel som du vill aktivera eller inaktivera. Växla den **aktivera regeln för den här enheten** för att aktivera eller inaktivera regeln för enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du redigerar regler i dina Azure IoT centralt program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md).