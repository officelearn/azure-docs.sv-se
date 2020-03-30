---
title: Snabbstart – Lägga till en simulerad enhet i Azure IoT Central
description: Den här snabbstarten visar hur du skapar en enhetsmall och lägger till en simulerad enhet i ditt IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169622"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snabbstart: Lägga till en simulerad enhet i ditt IoT Central-program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

En enhetsmall definierar funktionerna för en enhet som ansluter till ditt IoT Central-program. Funktionerna omfattar telemetri som enheten skickar, enhetsegenskaper och de kommandon som en enhet svarar på. Från en enhetsmall kan en byggare eller operatör lägga till både riktiga och simulerade enheter i ett program. Simulerade enheter är användbara för att testa beteendet hos ditt IoT Central-program innan du ansluter riktiga enheter.

I den här snabbstarten lägger du till en enhetsmall för en *MXChip IoT* DevKit-anslagstavla (DevKit) och skapar en simulerad enhet. För att slutföra den här snabbstarten behöver du inte en riktig enhet, du arbetar med en simulering av enheten. En DevKit-enhet:

* Skickar telemetri som temperatur.
* Rapporterar enhetsspecifika egenskaper, till exempel ljusstyrka.
* Svarar på kommandon som aktivera och inaktivera.
* Rapporterar generiska enhetsegenskaper som firmware-version och serienummer.

## <a name="prerequisites"></a>Krav

Slutför snabbstarten [Skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central-program med hjälp av **den anpassade appen > anpassad programmall.**

## <a name="create-a-template"></a>Skapa en mall

Som byggare kan du skapa och redigera enhetsmallar i ditt IoT Central-program. När du har publicerat en enhetsmall kan du skapa simulerad enhet eller ansluta riktiga enheter från enhetsmallen. Med simulerade enheter kan du testa programmets beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhetsmall i programmet väljer du fliken **Enhetsmallar** i den vänstra rutan.

![Sidan Enhetsmallar](./media/quick-create-pnp-device/devicedefinitions.png)

En enhetsmall innehåller en enhetskapacitetsmodell som definierar den telemetri som enheten skickar, enhetsegenskaper och de kommandon som enheten svarar på.

### <a name="add-a-device-capability-model"></a>Lägga till en enhetskapacitetsmodell

Det finns flera alternativ för att lägga till en enhetskapacitetsmodell i ditt IoT Central-program. Du kan skapa en modell från grunden, importera en modell från en fil eller välja en enhet i enhetskatalogen. IoT Central stöder också en *enhetsförsta* metod där den automatiskt importerar en modell från en databas när en enhet ansluter för första gången. I den här snabbstarten väljer du en enhet från enhetskatalogen för att importera dess enhetskapacitetsmodell.

Följande steg visar hur du använder enhetskatalogen för att importera kapacitetsmodellen för en **MXChip IoT DevKit-enhet.** Dessa enheter skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga **+** till en ny enhetsmall markerar du på sidan **Enhetsmallar.**

1. På sidan **Välj malltyp** bläddrar du nedåt tills du hittar panelen **MXChip IoT DevKit.**

1. Välj **panelen MXChip IoT DevKit** och välj sedan **Nästa: Anpassa**.

1. På sidan **Granska** väljer du **Skapa**.

1. Efter några sekunder kan du se den nya enhetsmallen:

    ![MXChip IoT DevKit-enhetsmall](./media/quick-create-pnp-device/devkit-template.png)

    MXChip IoT DevKit-kapacitetsmodellen innehåller gränssnitt som **mxchip_sensor,** **mxchip_settings**och **Enhetsinformation**. Gränssnitt definierar funktionerna för en MXChip IoT DevKit-enhet. Funktionerna omfattar telemetri som en enhet skickar, vilka egenskaper en enhet rapporterar och vilka kommandon en enhet svarar på.

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En enhetsmall kan innehålla molnegenskaper. Molnegenskaper finns bara i IoT Central-programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **Molnegenskaper** och sedan **+ Lägg till molnegenskap**. Använd informationen i följande tabell för att lägga till två molnegenskaper i enhetsmallen:

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kundens namn     | Inget          | String |

1. Välj **Spara** för att spara ändringarna:

    ![Molnegenskaper](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Vyer

Som byggare kan du anpassa programmet så att relevant information om enheten visas för en operatör. Dina anpassningar gör det möjligt för operatören att hantera de enheter som är anslutna till programmet. Du kan skapa två typer av vyer som en operatör kan använda för att interagera med enheter:

* Formulär för att visa och redigera enhets- och molnegenskaper.
* Instrumentpaneler för att visualisera enheter, inklusive den telemetri de skickar.

### <a name="default-views"></a>Standardvyer

Standardvyer är ett snabbt sätt att komma igång med att visualisera din viktiga enhetsinformation. Du kan ha upp till tre standardvyer som genererats för enhetsmallen:

* I **vyn Kommandon** kan operatören skicka kommandon till enheten.
* **Översiktsvyn** använder diagram och mått för att visa enhetstelemetri.
* **Vyn Om** visar enhetsegenskaper.

Markera noden **Vyer** i enhetsmallen. Du kan se att IoT Central genererade en **översikt** och en **Om-vy** åt dig när du lade till mallen.

Så här lägger du till ett nytt **hanterad enhetsformulär** som en operatör kan använda för att hantera enheten:

1. Markera noden **Vyer** och välj sedan panelen **Redigera enhet och molndata** för att lägga till en ny vy.

1. Ändra formulärnamnet till **Hantera enhet**.

1. Välj molnegenskaperna **Kundnamn** och **Senaste servicedatum** och egenskapen **Fan Speed.** Välj sedan **Lägg till avsnitt:**

    ![Skapa nytt formulär](media/quick-create-pnp-device/new-form.png)

1. Välj **Spara** om du vill spara det nya formuläret.

## <a name="publish-device-template"></a>Publicera enhetsmall

Innan du kan skapa en simulerad enhet eller ansluta en riktig enhet måste du publicera enhetsmallen. Även om IoT Central publicerade mallen när du först skapade den, måste du publicera den uppdaterade versionen.

Så här publicerar du en enhetsmall:

1. Gå till enhetsmallen från sidan **Enhetsmallar.**

1. Välj **Publicera:**

    ![Publicerad modell](media/quick-create-pnp-device/publishedmodel.png)

1. Välj Publicera i **dialogrutan Publicera den här enheten i programdialogrutan** . **Publish** 

När du har publicerat en enhetsmall visas den på sidan **Enheter.** I en publicerad enhetsmall kan du inte redigera en enhetskapacitetsmodell utan att skapa en ny version. Du kan dock göra uppdateringar av molnegenskaper, anpassningar och vyer i en publicerad enhetsmall utan versionshantering. När du har gjort några ändringar väljer du **Publicera** för att skicka ut ändringarna till operatören.

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en simulerad enhet i ditt program använder du **mxchip IoT** DevKit-enhetsmallen som du skapade.

1. Om du vill lägga till en ny enhet som operatör väljer du **Enheter** i den vänstra rutan. På fliken **Enheter** visas **alla enheter** och **MXChip IoT** DevKit-enhetsmallen. Välj **MXChip IoT DevKit**.

1. Om du vill lägga till **+** en simulerad DevKit-enhet väljer du . Använd det föreslagna **enhets-ID:t** eller ange ditt eget gemener **enhets-ID**. Du kan också ange ett namn på den nya enheten. Kontrollera att den **simulerade växlingsknappen** är **På** och välj sedan **Skapa**.

    ![Simulerad enhet](./media/quick-create-pnp-device/simulated-device.png)

Nu kan du interagera med de vyer som skapades av byggaren för enhetsmallen med hjälp av simulerade data:

1. Välj den simulerade enheten på sidan **Enheter.**

1. **Översiktsvyn** visar en ritplan för den simulerade telemetrin:

    ![Översiktsvy](./media/quick-create-pnp-device/simulated-telemetry.png)

1. Vyn **Om** visar egenskapsvärden, inklusive de molnegenskaper som du har lagt till i vyn.

1. Med **vyn Kommandon** kan du köra kommandon, till exempel **blinka** på enheten.

1. Vyn **Hantera enheter** är det formulär som du har skapat för att operatören ska kunna hantera enheten.

## <a name="use-a-simulated-device-to-improve-views"></a>Använda en simulerad enhet för att förbättra vyer

När du har skapat en ny simulerad enhet kan byggaren använda den här enheten för att fortsätta att förbättra och bygga vidare på vyerna för enhetsmallen.

1. Välj **Enhetsmallar** i den vänstra rutan och välj mallen **MXChip IoT DevKit.**

1. Markera någon av de vyer som du vill redigera eller skapa en ny vy. Välj **Konfigurera förhandsgranskningsenhet**och välj sedan **från en enhet som körs**. Här kan du välja att inte ha någon förhandsgranskningsenhet, en riktig enhet konfigurerad för testning eller en befintlig enhet som du har lagt till i IoT Central.

1. Välj den simulerade enheten i listan. Välj därefter **Tillämpa**. Nu kan du se samma simulerade enhet i enhetsmallens vyer för att skapa. Den här vyn är användbar för diagram och andra visualiseringar.

    ![Konfigurera förhandsgranskningsenhet](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en **MXChip IoT** DevKit-enhetsmall och lägger till en simulerad enhet i ditt program.

Om du vill veta mer om hur du övervakar enheter som är anslutna till ditt program fortsätter du till snabbstarten:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder](./quick-configure-rules.md)
