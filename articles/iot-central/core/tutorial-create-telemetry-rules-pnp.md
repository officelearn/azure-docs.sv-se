---
title: Skapa och hantera regler i ditt Azure IoT Central-program | Microsoft Docs
description: Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande, när regeln utlöses.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94526cc586f9e70be4222597c7d9f44c4e80c281
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955818"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Självstudie: skapa en regel och konfigurera meddelanden i ditt Azure IoT Central-program (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande. Med bara några klick kan du definiera ett villkor för att övervaka telemetri från dina enheter och konfigurera en motsvarande åtgärd. Den här artikeln beskriver hur du skapar regler för att övervaka telemetri som skickas av enheten.

Enheter använder telemetri för att skicka numeriska data från enheten. En regel utlöses när den valda enhetens telemetri korsar ett angivet tröskelvärde.

I den här självstudien skapar du en regel för att skicka ett e-postmeddelande när temperaturen i en miljö sensor enhet överskrider 80&deg; F.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en regel
> * Lägg till en e-poståtgärd

## <a name="prerequisites"></a>Krav

Innan du börjar bör du fylla i [skapa ett Azure IoT Central-program](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) och [lägga till en simulerad enhet i IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) snabb starter för att skapa **miljösensorns** enhets mall att fungera med.

## <a name="create-a-rule"></a>Skapa en regel

För att skapa en telemetri-regel måste enhets mal len ha minst en definierad telemetri mått. I den här självstudien används en miljö sensor som skickar telemetri för temperatur och fuktighet. Du har lagt till den här enhets mal len och skapat en simulerad enhet i guiden [Lägg till en simulerad enhet i snabb starten för IoT Central programmet](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) . Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när det hamnar ovanför 80 grader.

1. I det vänstra fönstret väljer du **regler**.

1. Om du inte har skapat några regler än visas följande skärm:

    ![Inga regler ännu](media/tutorial-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Välj **+** för att lägga till en ny regel.

1. Ange namnet _temperatur övervakare_ för att identifiera regeln och tryck på RETUR.

1. Välj enhets mal len för **miljö sensor** . Regeln gäller som standard automatiskt för alla enheter som är associerade med enhets mal len. Om du vill filtrera efter en delmängd av enheterna väljer du **+ filtrera** och använder enhets egenskaper för att identifiera enheterna. Om du vill inaktivera regeln växlar du till den **aktiverade/inaktiverade** knappen i regel rubriken:

    ![Filter och aktivera](media/tutorial-create-telemetry-rules-pnp/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som regeln övervakar. I den här självstudien konfigurerar du regeln för att utlösa när temperaturen överskrider 80&deg; F.

1. Välj **temperatur** i list rutan för **telemetri** .

1. Sedan väljer du **är större än** som **Operator** och anger _80_ som **värde**.

    ![Tillstånd](media/tutorial-create-telemetry-rules-pnp/condition-filled-out1.png)

1. Du kan också ange en **tids agg regering**. När du väljer en tids agg regering måste du också välja en agg regerings typ, till exempel Average eller sum, från List rutan agg regering.

    * Utan agg regering utlöser regeln för varje telemetri-datapunkt som uppfyller villkoret. Om regeln till exempel har kon figurer ATS för att utlösas när temperaturen är över 80, utlöses regeln nästan omedelbart när enheten rapporterar temperatur > 80.
    * Med agg regering utlöser regeln om det sammanlagda värdet för telemetri-datapunkten i tids perioden uppfyller villkoret. Om regeln till exempel har kon figurer ATS för att utlösas när temperaturen är över 80, ställs tids agg regering in på 10 minuter, och agg regerings typen är genomsnitt, utlöses sedan regeln när enheten rapporterar en genomsnitts temperatur > 80, beräknad under 10 minuter intervall.

     ![Samlings villkor](media/tutorial-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)

Du kan lägga till flera villkor i en regel genom att välja **+ villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje villkor är anslutet av en implicit `AND`-sats. Om du använder tids agg regering med flera villkor måste alla värden för telemetri aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

När du har definierat villkoret ställer du in de åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant. För närvarande är e-post den enda tillgängliga åtgärden.

1. Välj **+ e-post** i avsnittet **åtgärder** .

1. Ange _temperatur varning_ som visnings namn för åtgärden, din e-postadress i fältet **till** och _kontrol lera enheten!_ som anteckning som ska visas i bröd texten i e-postmeddelandet.

    > [!NOTE]
    > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.

   ![Konfigurera åtgärd](media/tutorial-create-telemetry-rules-pnp/configure-action1.png)

1. Välj **Slutför**för att spara åtgärden. Du kan lägga till flera åtgärder i en regel.

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

Efter en stund får du ett e-postmeddelande när regeln utlöses:

![Exempel på e-post](media/tutorial-create-telemetry-rules-pnp/email.png)

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**.

## <a name="enable-or-disable-a-rule"></a>Aktivera eller inaktivera en regel

Välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som omfattas av regeln genom att växla knappen **Aktivera** eller **inaktivera** i regeln.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivera eller inaktivera en regel för en enhet

Välj den regel som du vill aktivera eller inaktivera. Lägg till ett filter i avsnittet **scope** för att ta med eller undanta en viss enhet i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar regler i ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig [hur du hanterar dina enheter](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Nu när du har definierat en tröskel-baserad regel är det föreslagna nästa steg att lära dig att:

> [!div class="nextstepaction"]
> [Konfigurera kontinuerlig data export](./howto-export-data-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).