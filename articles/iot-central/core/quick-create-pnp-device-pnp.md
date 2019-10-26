---
title: Lägga till en simulerad enhet i Azure IoT Central | Microsoft Docs
description: Skapa en enhets mall och Lägg till en simulerad enhet i IoT Central programmet.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 29d40556cb2335abf894c7fc6c0df7a6b7fbde7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958149"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Snabb start: lägga till en simulerad enhet till ditt IoT Central program (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

En enhets mall definierar funktionerna hos en enhet som ansluter till ditt IoT Central-program. Funktionerna omfattar telemetri som enheten skickar, enhets egenskaper och de kommandon som en enhet svarar på. Från en enhets mall kan ett verktyg eller en operatör lägga till både riktiga och simulerade enheter i ett program. Simulerade enheter är användbara för att testa beteendet för ditt IoT Central program innan du ansluter riktiga enheter.

I den här snabb starten skapar du en **miljö sensor** enhets mall och lägger till en simulerad enhet. En miljö sensors enhet:

* Skickar telemetri som temperatur.
* Rapporterar enhetsspecifika egenskaper, t. ex. ljus styrke nivå.
* Svarar på kommandon som aktivera och inaktivera.
* Rapporterar allmänna enhets egenskaper, t. ex. version och serie nummer för inbyggd program vara.

## <a name="prerequisites"></a>Krav

Slutför snabb starten [skapa ett Azure IoT Central-program (för hands versions funktioner)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) för att skapa ett IoT Central program med hjälp av den **anpassade appen > för hands versions program** mal len

Du behöver också en lokal kopia av filen **EnvironmentalSensorInline. capabilitymodel. JSON** som innehåller IoT- [Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) enhetens kapacitets modell. Du kan ladda ned det [här](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Högerklicka på sidan och välj **Spara som**.

När du har hämtat filen öppnar du den i en text redigerare och ersätter de två instanserna av `<YOUR_COMPANY_NAME_HERE>` med ditt namn. Använd bara tecknen a-z, A-Z, 0-9 och under streck.

## <a name="create-a-template"></a>Skapa en mall

Som ett verktyg kan du skapa och redigera enhets mallar i ditt program. När du har publicerat en enhets mall kan du generera en simulerad enhet eller ansluta riktiga enheter som implementerar enhets mal len. Med simulerade enheter kan du testa appens beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhets mall i programmet går du till sidan **mallar för enheter** . Det gör du genom att välja fliken **enhets mallar** i det vänstra fönstret.

![Sidan Enhetsmallar](./media/quick-create-pnp-device-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Lägga till en enhets kapacitets modell

Det finns flera alternativ för att redigera en enhets kapacitets modell i IoT Central. Du kan välja att skapa en anpassad modell från grunden, importera från en fil, välja från enhets katalogen eller ansluta en IoT Plug and Play-enhet via en enhets-första anslutning där enhetens kapacitets modell har publicerats i den offentliga lagrings platsen. I den här självstudien importerar du en enhets kapacitets modell från en fil.

Följande steg visar hur du importerar kapacitets modellen för en **miljö sensor** enhet. De här enheterna skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga till en ny enhets mall väljer du **+** på sidan **enhets mallar** .

1. Välj **IoT-enhet** i listan med anpassade enhets mallar, **Välj Nästa: anpassa**, Välj **Nästa: granska**och välj sedan **skapa**.

1. Ange **miljö sensor** som namn på enhets mal len.

1. Välj **import kapacitets modell** för att skapa en ny enhets kapacitets modell från en JSON-fil. Navigera till mappen där du sparade filen **EnvironmentalSensorInline. capabilitymodel. JSON** på den lokala datorn. Välj filen **EnvironmentalSensorInline. capabilitymodel. JSON** och välj sedan **Öppna**. Miljö sensorns funktions modell innehåller gränssnitten **miljö sensor** och **enhets information** :

    ![Funktions modell för miljö sensor enhet](./media/quick-create-pnp-device-pnp/newdevicecapabilitymodel.png)

    Dessa gränssnitt definierar funktionerna för en **miljö sensor** enhet. Funktionerna omfattar telemetri som en enhet skickar, egenskaper för enhets rapporter och de kommandon som en enhet svarar på.

### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kund namn     | Inget          | Sträng |

1. Välj **Spara** för att spara ändringarna:

    ![Moln egenskaper](media/quick-create-pnp-device-pnp/cloudproperties.png)

## <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att komma igång med visualisering av viktig enhets information. Du kan ha upp till tre standardvyer som genereras för din enhets mall:

* I vyn **kommandon** kan operatören skicka kommandon till din enhet.
* I vyn **Översikt** används diagram och mått för att Visa telemetri för enheter.
* I vyn **om** visas enhets egenskaper.

Välj **vyer** och **generera sedan standardvyer**.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurera en vy för att visualisera enheter

En instrument panel för enheter låter en operatör visualisera en enhet med hjälp av diagram och mått. Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. Du kan definiera flera instrument paneler för enheter. Om du vill skapa en instrument panel för att visualisera telemetri för miljö sensorer väljer du **vyer** och **visualiserar sedan enheten**:

1. Alla enhets egenskaper, moln egenskaper, telemetri och statiska alternativ visas under **Egenskaper**. Du kan dra och släppa något av dessa objekt i vyn. Dra egenskapen för **ljus styrke nivå** till vyn. Du kan konfigurera panelen med hjälp av kugg hjuls ikonen.

1. Om du vill lägga till ett diagram som ritar telemetri väljer du **fuktighet** och **temperatur**och väljer sedan **kombinera**. Om du vill visa det här diagrammet i ett annat format, t. ex. ett cirkel diagram eller ett stapeldiagram, väljer du knappen **ändra visualisering** överst i panelen.

1. Välj **Spara** för att spara vyn:

Du kan lägga till fler paneler som visar andra egenskaper eller telemetri värden. Du kan också lägga till statisk text, länkar och bilder. Om du vill flytta eller ändra storlek på en panel på instrument panelen flyttar du mus pekaren över panelen och drar panelen till en ny plats eller ändrar storlek på den.

### <a name="add-a-device-form"></a>Lägg till ett enhets formulär

Ett enhets formulär låter en operatör redigera skrivbara enhets egenskaper och moln egenskaper. Som ett verktyg kan du definiera flera formulär och välja vilka enhets-och moln egenskaper som ska visas för varje formulär. Du kan också visa skrivskyddade enhets egenskaper i ett formulär.

Skapa ett formulär för att visa och redigera egenskaper för miljö sensorer:

1. Navigera till **vyer** i **miljö sensor** mal len. Välj panelen **Redigera enhet och moln data** för att lägga till en ny vy.

1. Ange formulär namn **Egenskaper för miljö sensor**.

1. Dra egenskaperna för **kund namn** och **senaste tjänste datum** molnet till det befintliga avsnittet i formuläret.

1. Välj Egenskaper för **ljus styrke nivå** och **enhets tillstånd** . Välj sedan **Lägg till avsnitt**. Redigera titeln på avsnittet som ska vara **sensor egenskaper**. Välj **Använd**.

1. Välj **enhets modell**, **program version**, **tillverkare**och processor egenskaper för **tillverkaren** . Välj sedan **Lägg till avsnitt**. Redigera rubriken för avsnittet som ska vara **enhets egenskaper**. Välj **Använd**.

1. Välj **Spara** för att spara vyn.

## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

1. Välj **Publicera**.

1. I dialog rutan **publicera en enhets mall** väljer du **publicera**:

    ![Publicerad modell](media/quick-create-pnp-device-pnp/publishedmodel.png)

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en simulerad enhet i programmet använder du den **miljö sensor** enhets mall som du skapade.

1. Om du vill lägga till en ny enhet som en operatör väljer du **enheter** i den vänstra rutan. Fliken **enheter** visar **alla enheter** och mallen **miljö sensor** enhet. Välj **miljö sensor**.

1. Om du vill lägga till en simulerad miljö sensor väljer du **+ ny**. Använd det föreslagna **enhets-ID: t** eller ange ditt eget **enhets-ID**. Du kan också ange ett namn på den nya enheten. Ändra den **simulerade** växlingen till **på** och välj sedan **skapa**.

    ![Simulerad enhet](./media/quick-create-pnp-device-pnp/simulated-device.png)

Nu kan du interagera med de vyer som har skapats av verktyget för enhets mal len med simulerade data.

## <a name="use-a-simulated-device-to-improve-views"></a>Använd en simulerad enhet för att förbättra vyer

När du har skapat en ny simulerad enhet kan verktyget använda den här enheten för att fortsätta att förbättra och bygga på vyerna för enhets mal len.

1. Välj **mallar för enheter** i den vänstra rutan och välj mallen **miljö sensor** .

1. Välj någon av de vyer som du vill redigera eller skapa en ny vy. Klicka på **Konfigurera för hands versions enhet**och **Välj sedan från en enhet som körs**. Här kan du välja mellan att inte ha någon förhands gransknings enhet, med en riktig enhet som du kan konfigurera för testning eller från en befintlig enhet som du har lagt till i IoT Central.

1. Välj den simulerade enheten i listan. Välj sedan **Använd**. Nu kan du se samma simulerade enhet i din enhets mall för att skapa upplevelser. Den här vyn är användbar för diagram och andra visualiseringar.

    ![Konfigurera för hands versions enhet](./media/quick-create-pnp-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en **miljö sensor** enhets mall och lägger till en simulerad enhet i programmet.

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabb starten:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder](./quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
