---
title: Självstudie – definiera en ny Azure IoT Edge enhets typ i Azure IoT Central
description: Den här självstudien visar dig som ett verktyg för att skapa en ny Azure IoT Edge-enhet i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702793"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Självstudie: definiera en ny Azure IoT Edge enhets typ i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar dig som ett verktyg som beskriver hur du använder en mall för att definiera en ny typ av Azure IoT Edge enhet i ditt Azure IoT Central-program. 

En översikt finns i [Vad är Azure IoT Central (för hands versions funktioner)?](overview-iot-central.md). 

IoT Edge består av tre komponenter:
* **IoT Edge moduler** är behållare som kör Azure-tjänster, partner tjänster eller din egen kod. Moduler distribueras till IoT Edge enheter och körs lokalt på dessa enheter.
* **IoT Edge runtime** körs på varje IoT Edge enhet och hanterar de moduler som distribueras till varje enhet.
* Med ett **moln baserat gränssnitt** kan du fjärrövervaka och hantera IoT Edge enheter. IoT Central är moln gränssnittet.

En **Azure IoT Edge** enhet kan vara en gateway-enhet, med underordnade enheter som ansluter till IoT Edge enheten. Den här självstudien delar mer information om underordnade enhets anslutnings mönster.

En **enhets mall** definierar funktionerna i enheten och IoT Edge moduler. Funktionerna omfattar telemetri som modulen skickar, modul egenskaper och de kommandon som en modul svarar på.

I den här självstudien skapar du en enhets mall för miljö sensor. En miljö sensors enhet:

* Skickar telemetri, till exempel temperatur.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon, t. ex. återställning av temperatur.

I den här självstudien skapar du också en mall för miljögateway-enheter. En miljö-gateway-enhet:

* Skickar telemetri, till exempel temperatur.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon, t. ex. återställning av temperatur.
* Tillåter relationer till andra enhets kapacitets modeller.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny Azure IoT Edge enhets enhets mall.
> * Ladda upp ett distributions manifest.
> * Skapa funktioner, inklusive telemetri, egenskaper och kommandon för varje modul.
> * Definiera en visualisering för modulen telemetri.
> * Lägg till relationer till underordnad enhets mallar.
> * Publicera din enhets mall.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Underordnade enhets relationer med en gateway och moduler

Underordnade enheter kan ansluta till en IoT Edge gateway-enhet via modulen `$edgeHub`. Den här IoT Edge enheten blir en transparent gateway i det här scenariot.

![Diagram över transparent Gateway](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Underordnade enheter kan också ansluta till en IoT Edge gateway-enhet via en anpassad modul. I följande scenario ansluter underordnade enheter till en anpassad Modbus-modul.

![Diagram över anslutning till anpassad modul](./media/tutorial-define-edge-device-type/gateway-module.png)

Följande diagram visar anslutning till en IoT Edge-gatewayenhet via båda typerna av moduler (anpassade och `$edgeHub`).  

![Diagram över anslutning via båda nätmodulerna](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Slutligen kan underordnade enheter ansluta till en IoT Edge gateway-enhet via flera anpassade moduler. Följande diagram visar underordnade enheter som ansluter via en anpassad Modbus-modul, en modul för anpassad tabell och `$edgeHub`-modulen. 

![Diagram över anslutning via flera anpassade moduler](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Skapa en mall

Som ett verktyg kan du skapa och redigera IoT Edge enhets mallar i ditt program. När du har publicerat en enhets mall kan du ansluta riktiga enheter som implementerar enhets mal len.

### <a name="select-device-template-type"></a>Välj typ av enhets mal len 

Om du vill lägga till en ny enhets mall i programmet väljer du **enhetsspecifika** i det vänstra fönstret.

![Skärm bild av för hands versions program, med enhetsspecifika markerade](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Välj **+ nytt** för att börja skapa en ny enhets mall.

![Skärm bild av sidan enhets mallar med ny markerad](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

På sidan **Välj Malltyp** väljer du **Azure IoT Edge**och väljer **sedan Nästa: anpassa**.

![Skärm bild av sidan Välj Malltyp](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Anpassa enhets mal len

I IoT Edge kan du distribuera och hantera affärs logik i form av moduler. IoT Edge moduler är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösnings specifika kod. Information om hur moduler utvecklas, distribueras och underhålls finns i [IoT Edge moduler](../../iot-edge/iot-edge-modules.md).

På en hög nivå är ett distributions manifest en lista över moduler som kon figurer ATS med deras önskade egenskaper. Ett distributions manifest talar om för en IoT Edge enhet (eller en grupp av enheter) vilka moduler som ska installeras och hur du konfigurerar dem. Distributions manifest innehåller de önskade egenskaperna för varje modul med dubbla. IoT Edge enheterna rapporterar de rapporterade egenskaperna för varje modul.

Använd Visual Studio Code för att skapa ett distributions manifest. Mer information finns i [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Här är ett grundläggande distributions manifest, med en modul som exempel som ska användas för den här självstudien. Kopiera följande JSON och spara den som en. JSON-fil. 

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

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Ladda upp ett distributions manifest för IoT Edge

På sidan **Anpassa enhet** under **överför ett Azure IoT Edge distributions manifest**väljer du **Bläddra**. 

![Skärm bild av sidan Anpassa enhet med bläddra markerad](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Om du planerar att skapa en enhets mal len för IoT Edge Gateway, se till att välja **gateway-enhet med underordnade enheter**.

![Skärm bild av sidan Anpassa enhet med gateway-enhet med underordnade enheter markerade](./media/tutorial-define-edge-device-type/gateway-upload.png)

I dialog rutan fil val väljer du distributions manifest filen och väljer **Öppna**.

IoT Edge verifierar distributions manifest filen mot ett schema. Om verifieringen lyckas väljer du **Granska**.

![Skärm bild av sidan Anpassa enhet, med distributions manifest och granskning markerad](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Följande flödes schema visar livs cykeln för distributions manifest i IoT Central.

![Flödes schema för distributions Manifestets livs cykel](./media/tutorial-define-edge-device-type/dmflow.png)

Sedan visas en gransknings sida med information om distributions manifestet. Den här sidan visar en lista över moduler från distributions manifestet. I den här självstudien noterar du att `SimulatedTemperatureSensor`-modulen visas. Välj **Skapa**.

![Skärm bild av sidan Granska med modul och skapa markerad](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Om du har valt en gateway-enhet visas följande gransknings sida.

![Skärm bild av sidan Granska med Azure IoT Edge Gateway markerad](./media/tutorial-define-edge-device-type/gateway-review.png)


Du skapar en enhets mall med funktions modeller för moduler. I den här självstudien skapar du en enhets mall med kapacitets modellen för `SimulatedTemperatureSensor` modulen. 

Ändra rubrik för enhets mal len till **enhets mal len för miljö sensor**.

![Skärm bild av enhets mal len med uppdaterad rubrik markerad](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

I IoT Edge enhet, modell IoT Plug and Play på följande sätt:
* Varje IoT Edge enhets mal len har en enhets kapacitets modell.
* För varje anpassad modul som anges i distributions manifestet genereras en moduls funktions modell.
* En relation upprättas mellan varje moduls funktions modell och en enhets kapacitets modell.
* En modul kapacitets modell implementerar modulens gränssnitt.
* Varje modul gränssnitt innehåller telemetri, egenskaper och kommandon.

![Diagram över IoT Edge modellering](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Lägga till funktioner i en modul kapacitets modell

Här är ett exempel på utdata från `SimulatedTemperatureSensor`-modulen:
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

Du kan lägga till funktioner i `SimulatedTemperatureSensor`-modulen, som visar föregående utdata. 

1. Välj **hantera** > **Lägg till funktioner**för att hantera ett gränssnitt för kapacitets modellen för `SimulatedTemperatureSensor` modulen. 

    ![Skärm bild av miljö sensor mal len med Lägg till funktion markerat](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Lägg till en dator som objekt typ.
  
    ![Skärm bild av sidan funktions funktioner för miljö sensor med schema markerat](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Välj **definiera**. I dialog rutan som visas ändrar du objekt namnet till **Machine**. Skapa egenskaper för temperatur och tryck och välj **Använd**.
  
    ![Skärm bild av dialog rutan attribut med olika alternativ markerade](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Lägg till **omgivande** som en objekt typ.

1. Välj **definiera**. I dialog rutan som visas ändrar du objekt namnet till **omgivande**. Skapa egenskaper för temperatur och fuktighet och välj **Använd**.
  
    ![Skärm bild av dialog rutan attribut med olika alternativ markerade](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Lägg till `timeCreated` som en `DateTime` typ och välj **Spara**.
  
    ![Skärm bild av miljö sensor mal len med Spara markerat](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Lägg till relationer

Om du har valt en IoT Edge enhet som en gateway-enhet kan du lägga till underordnade relationer till enhets kapacitets modeller för enheter som du vill ansluta till gateway-enheten.
  
  ![Skärm bild av mallen för miljö-Gateway, med Lägg till relation markerad](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Du kan lägga till en relation på en enhet eller i en modul.
  
  ![Skärm bild av mallen för en miljö-Gateway, med enhets-och modulnivå-relationer markerade](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Du kan välja en modell med en underordnad enhets kapacitet eller så kan du välja en asterisk. 
  
  ![Skärm bild av mallen för miljö-Gateway, med målet markerat](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  I den här självstudien väljer du asterisken. Det här alternativet tillåter alla underordnade relationer. Välj sedan **Spara**.

  ![Skärm bild av mallen för miljö-Gateway, med målet markerat](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** >  **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kund namn     | Inget          | Sträng |

2. Välj **Spara**.

  
    ![Skärm bild av miljö sensor mal len med Spara markerat](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar för att ändra ett gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion som inte kräver att du har version av enhetens kapacitets modell. Du kan anpassa fält när kapacitets modellen är i ett utkast eller publicerings tillstånd. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen.

När du är klar med anpassningen väljer du **Spara**.
  
![Skärm bild av mall för att anpassa miljö sensorn](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurera en vy för att visualisera enheter

En instrument panel för enheter låter en operatör visualisera en enhet med hjälp av diagram och mått. Som ett verktyg kan du definiera vilken information som visas på en enhets instrument panel. Du kan definiera flera instrument paneler för enheter. Om du vill skapa en instrument panel för att visualisera telemetri för miljö sensorer väljer du **vyer** > **visualisering av enheten**:

  
![Skärm bild av sidan vyer för miljö sensor med visualisering av enheten markerad](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Den omgivande Telemetrin och telemetri av datorer är komplexa objekt. Skapa diagram:

1. Dra den **omgivande Telemetrin**och välj **linje diagram**. 
  
   ![Skärm bild av miljö sensor mal len med omgivande telemetri och linje diagram markerat](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Välj ikonen konfigurera. Välj **temperatur** och **fuktighet** för att visualisera data och välj **Uppdatera konfiguration**. 
  
   ![Skärm bild av miljö sensor mal len med olika alternativ markerade](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Välj **Spara**.

Du kan lägga till fler paneler som visar andra egenskaper eller telemetri värden. Du kan också lägga till statisk text, länkar och bilder. Om du vill flytta eller ändra storlek på en panel på instrument panelen flyttar du mus pekaren över panelen och drar panelen till en ny plats eller ändrar storlek på den.
  
![Skärm bild av instrument panels mal len för miljö sensor](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Lägg till ett enhets formulär

Ett enhets formulär låter en operatör redigera skrivbara enhets egenskaper och moln egenskaper. Som ett verktyg kan du definiera flera formulär och välja vilka enhets-och moln egenskaper som ska visas för varje formulär. Du kan också visa skrivskyddade enhets egenskaper i ett formulär.

Skapa ett formulär för att visa och redigera egenskaper för miljö sensorer:

1. I **miljö sensor mal len**går du till **vyer**. Välj panelen **Redigera enhet och moln data** för att lägga till en ny vy.
  
   ![Skärm bild av sidan vyer för miljö sensor, med redigering av enhets-och moln data markerade](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Ange formulär namn **Egenskaper för miljö sensor**.

1. Dra egenskaperna för **kund namn** och **senaste tjänste datum** molnet till det befintliga avsnittet i formuläret.
  
   ![Skärm bild av sidan vyer för miljö sensor med olika alternativ markerade](./media/tutorial-define-edge-device-type/views-properties.png)

1. Välj **Spara**.

## <a name="publish-a-device-template"></a>Publicera en enhets mall

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

2. Välj **Publicera**.
  
    ![Skärm bild av miljö sensor mal len med publicera markerad](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. I dialog rutan **publicera en enhets mall** väljer du **publicera**.
  
    ![Skärm bild av dialog rutan publicera en enhets mall med publicera markerad](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall. De här uppdateringarna innebär inte att en ny version skapas. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.
  
![Skärm bild av listan med mallar för publicerade mallar](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny kant som en mall för löv enheter.
* Generera moduler från ett uppladdat distributions manifest.
* Lägg till telemetri och egenskaper för komplex typ.
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Publicera din Edge-enhets mall.

Nu när du har skapat en enhets mall i ditt Azure IoT Central-program kan du göra följande:

> [!div class="nextstepaction"]
> [Anslut enhet](./tutorial-connect-pnp-device.md)
