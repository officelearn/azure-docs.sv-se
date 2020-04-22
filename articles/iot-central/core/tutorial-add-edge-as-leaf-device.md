---
title: Lägga till en Azure IoT Edge-enhet i Azure IoT Central | Microsoft-dokument
description: Som operatör lägger du till en Azure IoT Edge-enhet i ditt Azure IoT Central-program
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758170"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Självstudiekurs: Lägga till en Azure IoT Edge-enhet i ditt Azure IoT Central-program

*Den här artikeln gäller lösningsbyggare och enhetsutvecklare.*

Den här självstudien visar hur du konfigurerar och lägger till en Azure IoT Edge-enhet i ditt Azure IoT Central-program. Självstudien använder en IoT Edge-aktiverad virtuell Linux-dator (VM) från Azure Marketplace för att simulera en IoT Edge-enhet. IoT Edge-enheten använder en modul som genererar simulerad miljötelemetri. Du kan visa telemetrin på en instrumentpanel i ditt IoT Central-program.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enhetsmall för en IoT Edge-enhet
> * Skapa en IoT Edge-enhet i IoT Central
> * Distribuera en simulerad IoT Edge-enhet till en Virtuell Linux-dator

## <a name="prerequisites"></a>Krav

Slutför snabbstarten [Skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central-program med hjälp av **den anpassade appen > anpassad programmall.**

För att slutföra stegen i den här självstudien behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Hämta IoT Edge manifestfilen från GitHub. Högerklicka på följande länk och välj sedan **Spara länk som:** [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Skapa enhetsmall

I det här avsnittet skapar du en enhetsmall för en IoT Edge-enhet som ansluter till ditt IoT Central-program. Du importerar ett IoT Edge-manifest för att komma igång och ändrar sedan mallen för att lägga till telemetridefinitioner och vyer:

### <a name="import-manifest-to-create-template"></a>Importera manifest för att skapa mall

Så här skapar du en enhetsmall från ett IoT Edge-manifest:

1. I ditt IoT Central-program navigerar du till **Enhetsmallar** och väljer **+ Nytt**.

1. På sidan **Välj malltyp** väljer du panelen **Azure IoT Edge.** Välj sedan **Nästa: Anpassa**.

1. På sidan **Ladda upp ett Azure IoT Edge-distributionsmaniter** väljer du **Bläddra** för att ladda upp **EnvironmentalSensorManifest.json** som du hämtade tidigare. Välj sedan **Nästa: Granska**.

1. På sidan **Granska** väljer du **Skapa**.

1. När mallen har skapats ändrar du namnet till *Environmental Sensor Edge Device*.

1. Välj **gränssnittet Hantera** i modulen **SimuleradTemperatureSensor** om du vill visa de två egenskaper som definierats i manifestet:

![Enhetsmall skapad från IoT Edge-manifest](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Lägg till telemetri i manifestet

Ett IoT Edge-manifest definierar inte den telemetri som en modul skickar. Du måste lägga till telemetridefinitionerna i enhetsmallen. Modulen **SimulatedTemperatureSensor** skickar telemetrimeddelanden som ser ut så här JSON:

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

Så här lägger du till telemetridefinitionerna i enhetsmallen:

1. Välj **gränssnittet Hantera** i mallen **Miljösensorkantenhet.**

1. Välj **+ Lägg till kapacitet**. Ange *datorn* som **visningsnamn** och kontrollera att **funktionstypen** är **Telemetri**.

1. Markera **Objekt** som schematyp och välj sedan **Definiera**. På sidan objektdefinition lägger du till *temperatur* och *tryck* som attribut av typen **Dubbel** och väljer sedan **Använd**.

1. Välj **+ Lägg till kapacitet**. Ange *omgivande* som **visningsnamn** och kontrollera att **funktionstypen** är **Telemetri**.

1. Markera **Objekt** som schematyp och välj sedan **Definiera**. På sidan objektdefinition lägger du till *temperatur* och *fuktighet* som attribut av typen **Dubbel** och väljer sedan **Använd**.

1. Välj **+ Lägg till kapacitet**. Ange *tidSkapad* som **visningsnamn** och kontrollera att **funktionstypen** är **Telemetri**.

1. Välj **DateTime** som schematyp.

1. Välj **Spara** om du vill uppdatera mallen.

**Hanteringsgränssnittet** innehåller nu **typerna av maskin,** **ambient**och **timeCreated** telemetri:

![Gränssnitt med maskin- och omgivningstelemetrityper](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Lägga till vyer i mallen

Enhetsmallen har ännu inte en vy som gör att en operatör kan se telemetrin från IoT Edge-enheten. Så här lägger du till en vy i enhetsmallen:

1. Välj **Vyer** i mallen **Miljösensorkantenhet.**

1. På sidan **Välj om du vill lägga till en ny vy** väljer du panelen **Visualisera enheten.**

1. Ändra vynamnet till *Visa IoT Edge-enhetstelemetri*.

1. Välj typer av **omgivande** och maskintelemetri. **machine** Välj sedan **Lägg till panel**.

1. Välj **Spara** om du vill spara **telemetrivyn Visa IoT Edge-enhet.**

![Enhetsmall med telemetrivy](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publicera mallen

Innan du kan lägga till en enhet som använder mallen **Miljösensor edgeenhet** måste du publicera mallen.

Navigera till mallen **Miljösensorkantenhet** och välj **Publicera**. Välj sedan **Publicera** för att publicera mallen:

![Publicera enhetsmallen](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Lägga till IoT Edge-enhet

Nu när du har publicerat mallen **Miljösensor edge device** kan du lägga till en enhet i ditt IoT Central-program:

1. I ditt IoT Central-program navigerar du till sidan **Enheter** och väljer **Miljösensor edge-enhet** i listan över tillgängliga mallar.

1. Välj **+** om du vill lägga till en ny enhet i mallen. På sidan **Skapa ny enhet** väljer du **Skapa**.

Du har nu en ny enhet med status **registrerad:**

![Publicera enhetsmallen](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Hämta enhetsautentiseringsuppgifterna

När du distribuerar IoT Edge-enheten senare i den här självstudien behöver du de autentiseringsuppgifter som gör att enheten kan ansluta till ditt IoT Central-program. Hämta enhetsautentiseringsuppgifterna:

1. På sidan **Enhet** väljer du den enhet du skapade.

1. Välj **Anslut**.

1. På **sidan Enhetsanslutning** antecknar du **ID-scopet,** **enhets-ID:t**och **primärnyckeln**. Du använder dessa värden senare.

1. Välj **Stäng**.

Du har nu konfigurerat ditt IoT Central-program för att aktivera en IoT Edge-enhet för att ansluta.

## <a name="deploy-an-iot-edge-device"></a>Distribuera en IoT Edge-enhet

I den här självstudien använder du en Azure IoT Edge-aktiverad Linux-virtuell dator som skapats på Azure för att simulera en IoT Edge-enhet. Så här skapar du den IoT Edge-aktiverade virtuella datorn:

1. Navigera till [Azure IoT Edge på Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) på Azure Marketplace. Välj **sedan Hämta det nu**.

1. På sidan **Skapa den här appen på Azure** väljer du **Fortsätt**. Den här länken tar dig till Azure-portalen, där du kan behöva logga in på din Azure-prenumeration.

1. På **sidan Azure IoT Edge på Ubuntu** i Azure-portalen väljer du **Skapa**.

1. På sidan **Skapa en virtuell dator > Basics:**

    - Välj din Azure-prenumeration.
    - Skapa en ny resursgrupp med namnet **iot-edge-devices**.
    - Använd namnet på den virtuella datorn: **iotedgevm**.
    - Välj den närmaste regionen till dig.
    - Ange autentiseringstypen till **Lösenord**.
    - Välj ett användarnamn och lösenord.
    - Du kan lämna de andra alternativen till deras standardvärden.
    - Välj **Granska + skapa**.

1. När valideringen är klar väljer du **Skapa**.

Efter några minuter, när distributionen är klar, väljer du **Gå till resurs**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Etablera virtuell dator som en IoT Edge-enhet 

Så här etablerar du virtuell dator som en IoT Edge-enhet:

1. I avsnittet **Support + felsökning** väljer du **Seriell konsol**.

1. Tryck **Enter** på Retur `login:` för att se uppmaningen. Ange ditt användarnamn och lösenord för att logga in.

1. Kör följande kommando för att kontrollera IoT Edge-körningsversionen. I skrivande stund är versionen 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Öppna `nano` filen IoT Edge config.yaml med redigeraren:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Bläddra nedåt tills du ser `# Manual provisioning configuration`. Kommentera de följande tre raderna som visas i följande utdrag:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Bläddra nedåt tills du ser `# DPS symmetric key provisioning configuration`. Ta av dig de följande åtta raderna enligt följande utdrag:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Ersätt `{scope_id}` med **ID-scopet** för dig har gjort en anteckning om tidigare.

1. Ersätt `{registration_id}` med **enhets-ID:t** som du har noterat tidigare.

1. Ersätt `{symmetric_key}` med **primärnyckeln** som du har noterat tidigare.

1. Spara ändringarna (**Ctrl-O**) och avsluta ( `nano` **Ctrl-X**) redigeraren.

1. Kör följande kommando för att starta om IoT Edge-demonen:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Om du vill kontrollera status för IoT Edge-modulerna kör du följande kommando:

    ```bash
    iotedge list
    ```

    Utdata ser ut så här:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Visa telemetrin

Den simulerade IoT Edge-enheten körs nu i den virtuella datorn. I ditt IoT Central-program **är enhetsstatusen** nu etablerad på sidan **Enheter:**

![Etablerad enhet](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Du kan se telemetrin på sidan **Visa IoT Edge-enhetstelemetri:**

![Enhetstelemetri](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Sidan **Moduler** visar status för IoT Edge-modulerna:

![Enhetstelemetri](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Nästa steg

Som enhetsutvecklare, nu när du har lärt dig att arbeta med och hantera IoT Edge-enheter i IoT Central, är ett förslag till nästa steg att läsa:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Ansluta till Azure IoT Central](./concepts-get-connected.md)
