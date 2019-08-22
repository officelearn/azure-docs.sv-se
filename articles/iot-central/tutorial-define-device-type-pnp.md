---
title: Definiera en ny enhetstyp i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar dig som ett verktyg för att skapa en ny enhets typ i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a622aa85d1d0a9dcd5d5ad9b2b30e7a3120ea974
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878014"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Självstudier: Definiera en ny enhets typ i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

I den här självstudien får du lära dig hur du som byggare använder en enhetsmall för att definiera en ny typ av enhet i Azure IoT Central-programmet. En enhets mall definierar funktionerna i enheten. Funktionerna omfattar telemetri som enheten skickar, enhets egenskaper och de kommandon som en enhet svarar på.

I den här självstudien skapar du en enhets mall för **miljö sensor** . En miljö sensors enhet:

* Skickar telemetri som temperatur.
* Rapporterar enhetsspecifika egenskaper, t. ex. ljus styrke nivå.
* Svarar på kommandon som aktivera och inaktivera.
* Rapporterar allmänna enhets egenskaper, t. ex. version och serie nummer för inbyggd program vara.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny enhets mall.
> * Importera en enhets kapacitets modell.
> * Skapa moln egenskaper.
> * Definiera en visualisering för enhetens telemetri.
> * Publicera din enhets mall.
> * Skapa en simulerad enhet för din enhets mall.
> * Visa din simulerade enhet.

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Om du har genomfört snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) kan du återanvända det program som du skapade i den snabbstarten. I annat fall utför du följande steg för att skapa ett tomt Azure IoT Central-program:

1. Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central.

1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars signerar du den med en Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

Så här skapar du ett nytt Azure IoT Central-program som använder för hands versions funktioner, inklusive IoT Plug and Play:

1. Välj **Utvärdering**. Du behöver ingen Azure-prenumeration för att skapa ett utvärderings program.

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Välj för **hands versions program**.

1. Om du vill kan du välja ett eget programnamn, till exempel **Contoso Air Conditioners**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.

1. Om du skapar ett utvärderings program måste du ange din kontakt information.

1. Välj **Skapa**.

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Du behöver också en lokal kopia av filen **EnvironmentalSensorInline. capabilitymodel. JSON** som innehåller IoT- [Plug and Play](https://aka.ms/iot-pnp-docs) enhetens kapacitets modell. Du kan ladda ned det [här](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Högerklicka på sidan och välj **Spara som**.

När du har hämtat filen öppnar du den i en text redigerare och ersätter de båda instanserna av `<YOUR_COMPANY_NAME_HERE>` med ditt namn. Använd bara tecknen a-z, A-Z, 0-9 och under streck.

## <a name="create-a-template"></a>Skapa en mall

Som ett verktyg kan du skapa och redigera enhets mallar i ditt program. När du har publicerat en enhets mall kan du generera en simulerad enhet eller ansluta riktiga enheter som implementerar enhets mal len. Med simulerade enheter kan du testa appens beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhets mall i programmet går du till sidan **mallar för enheter** . Det gör du genom att välja fliken **enhets mallar** i den vänstra navigerings menyn.

![Sidan Enhetsmallar](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Lägga till en enhets kapacitets modell

Det finns flera alternativ för att redigera en enhets kapacitets modell i IoT Central. Du kan välja att skapa en anpassad modell från grunden, importera från en fil, välja från enhets katalogen eller ansluta en IoT Plug and Play-enhet via en enhets-första anslutning där enhetens kapacitets modell har publicerats i den offentliga lagrings platsen. I den här självstudien importerar du en enhets kapacitets modell från en fil.

Följande steg visar hur du importerar kapacitets modellen för en **miljö sensor** enhet. De här enheterna skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga till en ny enhets mall väljer du **+ ny** på sidan **mallar för enheter** .

1. Välj **anpassad** från listan över enhets kapacitets modeller.

1. Ange **miljö sensor** som namn på enhets mal len.

1. Välj **import kapacitets modell** för att skapa en ny enhets kapacitets modell från en JSON-fil. Navigera till mappen där du sparade filen **EnvironmentalSensorInline. capabilitymodel. JSON** på den lokala datorn. Välj filen **EnvironmentalSensorInline. capabilitymodel. JSON** och välj sedan importera. Miljö sensorns funktions modell innehåller gränssnitten **miljö sensor** och **enhets information** :

    ![Funktions modell för miljö sensor enhet](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Dessa gränssnitt definierar funktionerna för en **miljö sensor** enhet. Funktionerna omfattar telemetri som en enhet skickar, egenskaper för enhets rapporter och de kommandon som en enhet svarar på.

### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantiktyp | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inga          | Date   |
    | Kundnamn     | Inga          | Sträng |

1. Välj **Spara** för att spara ändringarna:

    ![Moln egenskaper](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion som inte kräver att du har version av enhetens kapacitets modell. Du kan anpassa fält när kapacitets modellen är i ett utkast eller publicerings tillstånd. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen.


## <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att komma igång med visualisering av viktig enhets information. Du kan ha upp till tre standardvyer som genereras för din enhets mall:

* I vyn **kommandon** kan operatören skicka kommandon till din enhet.
* I vyn **Översikt** används diagram och mått för att Visa telemetri för enheter.
* I vyn **om** visas enhets egenskaper.

När du har valt **generera standardvyer**läggs de automatiskt till i avsnittet **vyer** i din enhets mall.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurera en vy för att visualisera enheter

En instrument panel för enheter låter en operatör visualisera en enhet med hjälp av diagram och mått. Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. Du kan definiera flera instrument paneler för enheter. Om du vill skapa en instrument panel för att visualisera telemetri för miljö sensorer väljer du **vyer** och visualiserar sedan **enheten**:

1. Alla enhets egenskaper, moln egenskaper, telemetri och statiska alternativ visas i den högra panelen. Du kan dra och släppa något av dessa objekt i vyn. Dra ut en egenskap till vyn. Du kan konfigurera panelen med hjälp av kugg hjuls ikonen.

1. Om du vill lägga till ett diagram som ritar telemetri väljer du **fuktighet** och **temperatur**och väljer sedan **kombinera**. Om du vill visa det här diagrammet i ett annat format, t. ex. ett cirkel diagram eller ett stapeldiagram, väljer du knappen **ändra visualisering** överst i panelen.

1. Välj **Spara** för att spara vyn:

Du kan lägga till fler paneler som visar andra egenskaper eller telemetri värden. Du kan också lägga till statisk text, länkar och bilder. Om du vill flytta eller ändra storlek på en panel på instrument panelen flyttar du mus pekaren över panelen och drar panelen till en ny plats eller ändrar storlek på den.

### <a name="add-a-device-form"></a>Lägg till ett enhets formulär

Ett enhets formulär låter en operatör redigera skrivbara enhets egenskaper och moln egenskaper. Som ett verktyg kan du definiera flera formulär och välja vilka enhets-och moln egenskaper som ska visas för varje formulär. Du kan också visa skrivskyddade enhets egenskaper i ett formulär.

Skapa ett formulär för att visa och redigera egenskaper för miljö sensorer:

1. Välj panelen **Redigera enhet och moln data** .

1. Ange formulär namn **Egenskaper för miljö sensor**.

1. Dra egenskaperna för **kund namn** och **senaste tjänste datum** molnet till det befintliga avsnittet i formuläret.

1. Välj Egenskaper för **ljus styrke nivå** och **enhets tillstånd** . Välj sedan **Lägg till avsnitt**. Redigera titeln på avsnittet som ska vara **sensor egenskaper**. Välj **Använd**.

1. Välj enhets egenskaperna **återstående batteri**, **enhets modell**, **version av inbyggd program vara**, **tillverkare**och **serie nummer** . Välj sedan **Lägg till avsnitt**. Redigera rubriken för avsnittet som ska vara **enhets egenskaper**. Välj **Använd**.

1. Välj **Spara** för att spara vyn.


## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

1. Välj **Publicera**.

1. I dialog rutan **publicera en enhets mall** väljer du **publicera**:

    ![Publicerad modell](media/tutorial-define-device-type-pnp/publishedmodel.png)

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny enhetsmall
* Importera en enhets kapacitets modell.
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Publicera din enhets mall.

Nu när du har skapat en enhets mall i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Lägg till enhet](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
