---
title: Snabb start – Lägg till en simulerad enhet i Azure IoT Central
description: Den här snabb starten visar hur du skapar en enhets mall och lägger till en simulerad enhet i IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 1c37742b7ed31d8253f7908c3adac35ca0b4d5d8
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376561"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snabb start: lägga till en simulerad enhet till ditt IoT Central program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

En enhets mall definierar funktionerna hos en enhet som ansluter till ditt IoT Central-program. Funktionerna omfattar telemetri som enheten skickar, enhets egenskaper och de kommandon som en enhet svarar på. Från en enhets mall kan ett verktyg eller en operatör lägga till både riktiga och simulerade enheter i ett program. Simulerade enheter är användbara för att testa beteendet för ditt IoT Central program innan du ansluter riktiga enheter.

I den här snabb starten lägger du till en enhets mall för ett [DevKit-kort ( *MXChip IoT DevKit* )](https://aka.ms/iot-devkit-purchase) och skapar en simulerad enhet. För att slutföra den här snabb starten behöver du inte en riktig enhet, men du arbetar med en simulering av enheten. En DevKit-enhet:

* Skickar telemetri som temperatur.
* Rapporterar enhetsspecifika egenskaper, t. ex. ljus styrke nivå.
* Svarar på kommandon som aktivera och inaktivera.
* Rapporterar allmänna enhets egenskaper, t. ex. version och serie nummer för inbyggd program vara.

## <a name="prerequisites"></a>Förutsättningar

Slutför snabb starten [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > anpassade program** .

## <a name="create-a-template"></a>Skapa en mall

Som ett verktyg kan du skapa och redigera mallar för enheter i IoT Central program. När du har publicerat en enhets mall kan du generera en simulerad enhet eller ansluta riktiga enheter från enhets mal len. Med simulerade enheter kan du testa appens beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhets mall i programmet väljer du fliken **enhets mallar** i den vänstra rutan.

![Sidan Enhetsmallar](./media/quick-create-simulated-device/device-definitions.png)

En enhets mall innehåller en modell för enhets kapacitet som definierar den telemetri som enheten skickar, enhets egenskaper och de kommandon som enheten svarar på.

### <a name="add-a-device-capability-model"></a>Lägga till en enhets kapacitets modell

Det finns flera alternativ för att lägga till en enhets kapacitets modell till ditt IoT Central-program. Du kan skapa en modell från grunden, importera en modell från en fil eller välja en enhet från enhets katalogen. IoT Central också stöd för en *enhets-första* metod där den automatiskt importerar en modell från en lagrings plats när en enhet ansluter för första gången. I den här snabb starten väljer du en enhet från enhets katalogen för att importera enhetens kapacitets modell.

Följande steg visar hur du använder enhets katalogen för att importera kapacitets modellen för en **MXChip IoT DevKit** -enhet. De här enheterna skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga till en ny enhets mall väljer du **+** på sidan **enhets mallar** .

1. Rulla nedåt tills du hittar **MXChip IoT DevKit** -panelen på sidan **Välj Malltyp** .

1. Välj **MXChip IoT DevKit** -panelen och välj sedan **Nästa: anpassa**.

1. På sidan **Granska** väljer du **skapa**.

1. Efter några sekunder kan du se din nya enhets mall:

    ![MXChip IoT DevKit Device-mall](./media/quick-create-simulated-device/devkit-template.png)

    MXChip IoT DevKit Capability-modellen innehåller gränssnitt som **mxchip_sensor** , **mxchip_settings** och **enhets information**. Gränssnitt definierar funktionerna för en MXChip IoT DevKit-enhet. Funktionerna omfattar telemetri som en enhet skickar, egenskaper för enhets rapporter och de kommandon som en enhet svarar på.

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet. Så här lägger du till en moln egenskap:

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till två moln egenskaper till din enhets mall:

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Ingen          | Date   |
    | Kundnamn     | Ingen          | Sträng |

1. Välj **Spara** för att spara ändringarna:

    ![Molnegenskaper](media/quick-create-simulated-device/cloud-properties.png)

## <a name="views"></a>Vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om enheten till en operatör. Anpassningarna gör att operatören kan hantera enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter, inklusive telemetri som de skickar.

### <a name="default-views"></a>Standardvyer

Standardvyer är ett snabbt sätt att komma igång med att visualisera viktig enhets information. Du kan ha upp till tre standardvyer som genereras för din enhets mall:

* I vyn **kommandon** kan operatören skicka kommandon till din enhet.
* I vyn **Översikt** används diagram och mått för att Visa telemetri för enheter.
* I vyn **om** visas enhets egenskaper.

Välj noden **vyer** i enhets mal len. Du kan se att IoT Central genererade en **Översikt** och en **om** -vy åt dig när du lade till mallen.

För att lägga till ett nytt **hanterat enhets** formulär som en operatör kan använda för att hantera enheten:

1. Välj noden **vyer** och välj sedan panelen **Redigera enhet och moln data** för att lägga till en ny vy.

1. Ändra formulär namnet för att **hantera enheten**.

1. Välj **kundens namn** och det **senaste service datumets** moln egenskaper och egenskapen **fläkt hastighet** . Välj sedan **Lägg till avsnitt** :

    ![Skapa nytt formulär](media/quick-create-simulated-device/new-form.png)

1. Spara ditt nya formulär genom att välja **Spara** .

## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad enhet eller ansluta en riktig enhet måste du publicera din enhets mall. Även om IoT Central har publicerat mallen när du först skapade den, måste du publicera den uppdaterade versionen.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

1. Välj **publicera** :

    ![Publicerad modell](media/quick-create-simulated-device/published-model.png)

1. I den **publicera den här enhets mal len i program** dialog rutan väljer du **publicera**. 

När du har publicerat en enhets mall visas den på sidan **enheter** . I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera**  för att skicka ut ändringarna till din operatör.

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en simulerad enhet i programmet använder du **MXChip IoT DevKit** Device-mallen som du skapade.

1. Om du vill lägga till en ny enhet som en operatör väljer du **enheter** i den vänstra rutan. Fliken **enheter** visar **alla enheter** och enhets mal len **MXChip IoT DevKit** . Välj **MXChip IoT DevKit**.

1. Välj om du vill lägga till en simulerad DevKit-enhet **+** . Använd det föreslagna **enhets-ID: t** eller ange ditt eget. Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken. Du kan också ange ett namn på den nya enheten. Kontrol lera att den **simulerade** växlingen är **på** och välj sedan **skapa**.

    ![Simulerad enhet](./media/quick-create-simulated-device/simulated-device.png)

Nu kan du interagera med de vyer som har skapats av verktyget för enhets mal len med simulerade data:

1. Välj den simulerade enheten på sidan **enheter**

    * I vyn **Översikt** visas ett observations område för den simulerade Telemetrin:

        ![Översikt över vy](./media/quick-create-simulated-device/simulated-telemetry.png)

    * Vyn **om** visar egenskaps värden, inklusive de moln egenskaper som du har lagt till i vyn.

    * I vyn **kommandon** kan du köra kommandon, till exempel **blinka** på enheten.

    * Vyn **Hantera enheter** är det formulär som du skapade för operatören för att hantera enheten.

    * I vyn **rå data** kan du Visa de obehandlade telemetri-och egenskaps värden som skickas av enheten. Den här vyn är användbar för fel sökning av enheter.

## <a name="use-a-simulated-device-to-improve-views"></a>Använd en simulerad enhet för att förbättra vyer

När du har skapat en ny simulerad enhet kan verktyget använda den här enheten för att fortsätta att förbättra och bygga på vyerna för enhets mal len.

1. Välj **enhets mallar** i den vänstra rutan och välj **MXChip IoT DevKit** -mallen.

1. Välj någon av de vyer som du vill redigera eller skapa en ny vy. Välj **Konfigurera för hands versions enhet** och **Välj sedan från en enhet som körs**. Här kan du välja att inte ha någon förhands gransknings enhet, en riktig enhet som kon figurer ATS för testning eller en befintlig enhet som du har lagt till i IoT Central.

1. Välj den simulerade enheten i listan. Välj sedan **Använd**. Nu kan du se samma simulerade enhet i din enhets mall för att skapa upplevelser. Den här vyn är användbar för diagram och andra visualiseringar.

    ![Konfigurera för hands versions enhet](./media/quick-create-simulated-device/configure-preview.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en **MXChip IoT DevKit** Device-mall och lägger till en simulerad enhet i programmet.

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabb starten:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder](./quick-configure-rules.md)
