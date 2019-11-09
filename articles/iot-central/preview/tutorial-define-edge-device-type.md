---
title: Definiera en ny Azure IoT Edge enhets typ i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar dig som ett verktyg för att skapa en ny Azure IoT Edge-enhet i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893055"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Självstudie: definiera en ny Azure IoT Edge enhets typ i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar dig som ett verktyg som beskriver hur du använder en mall för att definiera en ny typ av Azure IoT Edge enhet i ditt Azure IoT Central-program. 

[Se den här artikeln](overview-iot-central.md)för att få en översikt över Azure IoT Edge. 

Azure IoT Edge består av tre komponenter:
* **IoT Edge moduler** är behållare som kör Azure-tjänster, tjänster från tredje part eller din egen kod. Moduler distribueras till IoT Edge-enheter och körs lokalt på enheterna.
* **IoT Edge runtime** körs på varje IoT Edge enhet och hanterar de moduler som distribueras till varje enhet.
* Med ett **moln baserat gränssnitt** kan du fjärrövervaka och hantera IoT Edge enheter. IoT Central är moln gränssnittet.

En **Azure IoT Edge** enhet kan vara en gateway-enhet med underordnade enheter som ansluter till Azure IoT Edge enheten. Underordnade enhets anslutnings mönster kommer att diskuteras i den här självstudien.

En **enhets mall** definierar funktionerna i enheten & IoT Edge moduler. Funktionerna omfattar telemetri som modulen skickar, modul egenskaper och de kommandon som en modul svarar på.

I den här självstudien skapar du en enhets mall för **miljö sensor** . En miljö sensors enhet:

* Skickar telemetri som temperatur.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon som att återställa temperatur.

I den här självstudien skapar du också en mall för **miljögateway** -enheter. En miljö-gateway-enhet:

* Skickar telemetri som temperatur.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon som att återställa temperatur.
* Tillåter relationer till andra enhets kapacitets modeller


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny Azure IoT Edge enhets enhets mall.
> * Ladda upp ett distributions manifest.
> * Skapa funktioner, inklusive telemetri, egenskaper och kommandon för varje modul
> * Definiera en visualisering för modulen telemetri.
> * Lägg till relationer till mallar för underordnade enheter
> * Publicera din enhets mall.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Följ den här snabb starten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Underordnade enhets relationer med Gateway & moduler

Underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via modulen $edgeHub. Den här Azure IoT Edge enheten blir en transparent gateway i det här scenariot

![Sidan Central program](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via en anpassad modul. I scenariot nedan visas underordnade enheter som ansluter via en anpassad Modbus-modul och underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via $edgeHub-modulen.

![Sidan Central program](./media/tutorial-define-edge-device-type/gateway-module.png)

Underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via en anpassad modul. I scenariot nedan visas underordnade enheter som ansluter via en anpassad Modbus-modul. 

![Sidan Central program](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via flera anpassade moduler. I scenariot nedan visas underordnade enheter som ansluter via en anpassad modul i Modbus, och underordnade enheter kan ansluta till Azure IoT Edge gateway-enhet via $edgeHub-modulen. 

![Sidan Central program](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Skapa en mall

Som ett verktyg kan du skapa och redigera Azure IoT Edge enhets mallar i ditt program. När du har publicerat en enhets mall kan du ansluta riktiga enheter som implementerar enhets mal len.

### <a name="select-device-template-type"></a>Välj typ av enhets mal len 

Om du vill lägga till en ny enhets mall i programmet går du till sidan **mallar för enheter** . Det gör du genom att välja fliken **enhets mallar** i det vänstra fönstret.

![Sidan Central program](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Klicka på **+ ny** för att börja skapa en ny enhets mall.

![Enhets mallar – ny](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Du kommer att hamna på sidan Val av Malltyp på enheten. Välj **Azure IoT Edge** panel och klicka på **Nästa: knappen anpassa** längst ned

![Val av enhets mallar – Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Anpassa enhets mal len

Med Azure IoT Edge kan du distribuera och hantera affärs logik i form av moduler. **Azure IoT Edge moduler** är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösnings-specifika kod. För att förstå hur moduler utvecklas, distribueras och bibehålls Läs [IoT Edge moduler](../../iot-edge/iot-edge-modules.md).

På en hög nivå är ett distributions manifest en lista över moduler som kon figurer ATS med deras önskade egenskaper. Ett distributions manifest talar om för en IoT Edge enhet (eller en grupp av enheter) vilka moduler som ska installeras och hur de ska konfigureras. Distributions manifest innehåller de önskade egenskaperna för varje modul med dubbla. IoT Edge enheterna rapporterar de rapporterade egenskaperna för varje modul.

Använd Visual Studio Code för att skapa ett distributions manifest. Följ dokumentationen om hur du skapar ett [distributions manifest](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Här är ett grundläggande distributions manifest med en modul som ett exempel på hur du kan använda den här självstudien. Kopiera nedanstående JSON och spara den som. JSON-fil. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Ladda upp ett distributions manifest för Azure IoT Edge**

Klicka på knappen **Bläddra** 

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Om du planerar att skapa en enhets mal len för Azure IoT Edge Gateway, se till att markera kryss rutan **gateway-enhet med underordnade enheter**

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

En dialog ruta för val av filer visas. Välj distributions manifest filen och klicka på knappen **Öppna** .

Distributions manifest filen val IDE ras mot ett schema. När verifieringen är klar klickar du på knappen **Granska**

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Nedan visas flödet av en livs cykel för distributions manifest i IoT Central.

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

Sidan granska visas med information om distributions manifestet. Lista över moduler från distributions manifestet kommer att visas på sidan Granska. I den här självstudien kommer du att se SimulatedTemperatureSensor-modulen listad. Klicka på knappen **skapa** .

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Om du har valt en gateway-enhet visas den här gransknings Sidan

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


När du skapar en ny enhets mal len visas en rotations ruta som enhets mal len skapas i IoT Central.

Enhets mal len skapas med funktions modeller för moduler. I den här självstudien får du se SimulatedTemperatureSensor-modulens kapacitets modell skapa. 

Ändra rubrik för enhets mal len till enhets mal len för miljö sensor.

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Plug-and-Play för Azure IoT Edge Device-enhet görs enligt följande
* Varje Azure IoT Edge enhets mal len har en **enhets kapacitets modell**
* För varje anpassad modul som anges i distributions manifestet skapas en **modul för modul kapacitets modell**
* En **relation** upprättas mellan varje moduls funktions modell och en enhets kapacitets modell
* Modulens kapacitets modell implementerar **modulens gränssnitt**
* Varje modul-gränssnitt innehåller
   - Telemetri
   - Egenskaper
   - Kommandon

![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Lägg till funktioner i modulens kapacitets modell**

Här är ett exempel på utdata från modulen SimulatedTemperatureSensor
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Lägg till funktioner i SimulatedTemperatureSensor-modulen som visar ovanstående JSON. 

* Klicka på **Hantera** ett gränssnitt för kapacitets modellen SimulatedTemperatureSensor-modul. Klicka på **Lägg till funktion**. 

    ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Lägg till datorn som en objekt typ eftersom det är en komplex typ
  
    ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Klicka på **definiera**. I popup-fönstrets modala ändring av objekt namn till dator och skapa egenskaper temperatur, tryck och klicka på **tillämpa**
  
    ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Lägg till omgivande som en objekt typ eftersom det är en komplex typ

    Klicka på **definiera**. I popup-fönstret för modala ändrings objekt till omgivande och skapa egenskaper temperatur, fuktighet och klicka på **tillämpa**
  
    ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Lägg till timeCreated som en DateTime-typ och klicka på **Spara**
  
    ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Lägg till relationer

Om du har valt Azure IoT Edge enhet som en gateway-enhet kan du lägga till underordnade relationer till enhets kapacitets modeller för enheter som du ansluter till gateway-enheten.
  
  ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Relationen kan läggas till på en enhet eller i en modul.
  
  ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Du kan välja en modell för en underordnad enhets kapacitet eller välja asterisken. 
  
  ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  I den här självstudien väljer vi asterisken vilket innebär att alla underordnade relationer kommer att tillåtas. Klicka på **Spara**

  ![Enhets mall – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Ingen          | Date   |
    | Kund namn     | Ingen          | Sträng |

2. Välj **Spara** för att spara ändringarna:

  
    ![Moln egenskaper – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion som inte kräver att du har version av enhetens kapacitets modell. Du kan anpassa fält när kapacitets modellen är i ett utkast eller publicerings tillstånd. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Klicka på **Spara**
  
![Anpassningar – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurera en vy för att visualisera enheter

En instrument panel för enheter låter en operatör visualisera en enhet med hjälp av diagram och mått. Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. Du kan definiera flera instrument paneler för enheter. Om du vill skapa en instrument panel för att visualisera telemetri för miljö sensorer väljer du **vyer** och **visualiserar sedan enheten**:

  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Den omgivande Telemetrin och telemetri av datorer är komplexa objekt, för att skapa diagram gör du följande

Dra den omgivande Telemetrin och välj linje diagram. 
  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Klicka på Konfigurera ikon och välj temperatur och fuktighet för att visualisera data och klicka på knappen **Uppdatera konfiguration** . 
  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Välj **Spara** för att spara vyn:

Du kan lägga till fler paneler som visar andra egenskaper eller telemetri värden. Du kan också lägga till statisk text, länkar och bilder. Om du vill flytta eller ändra storlek på en panel på instrument panelen flyttar du mus pekaren över panelen och drar panelen till en ny plats eller ändrar storlek på den.
  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Lägg till ett enhets formulär

Ett enhets formulär låter en operatör redigera skrivbara enhets egenskaper och moln egenskaper. Som ett verktyg kan du definiera flera formulär och välja vilka enhets-och moln egenskaper som ska visas för varje formulär. Du kan också visa skrivskyddade enhets egenskaper i ett formulär.

Skapa ett formulär för att visa och redigera egenskaper för miljö sensorer:

Navigera till **vyer** i **miljö sensor** mal len. Välj panelen **Redigera enhet och moln data** för att lägga till en ny vy.
  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Ange formulär namn **Egenskaper för miljö sensor**.

Dra egenskaperna för **kund namn** och **senaste tjänste datum** molnet till det befintliga avsnittet i formuläret.
  
![Vyer – Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Välj **Spara** för att spara vyn.

### <a name="generate-default-views"></a>Generera standardvyer

Det finns inte stöd för att generera standardvyer-funktioner för Azure IoT Edge mallar 

## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

2. Välj **Publicera**.
  
    ![Vyer – publicera](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. I dialog rutan **publicera en enhets mall** väljer du **publicera**:
  
    ![Vyer – publicera](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.
  
![Vyer – publicera](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny kant som en mall för löv enheter
* Generera moduler från ett uppladdat distributions manifest
* Lägg till telemetri och egenskaper för komplex typ
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Publicera din Edge-enhets mall.

Nu när du har skapat en enhets mall i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Anslut enhet](./tutorial-connect-pnp-device.md)
