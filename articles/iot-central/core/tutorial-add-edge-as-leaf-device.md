---
title: Lägga till en Azure IoT Edge-enhet i Azure IoT Central | Microsoft Docs
description: Som operatör lägger du till en Azure IoT Edge enhet till ditt Azure IoT Central-program
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758170"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Självstudie: Lägg till en Azure IoT Edge enhet till ditt Azure IoT Central-program

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du konfigurerar och lägger till en Azure IoT Edge-enhet i Azure IoT Central-programmet. Självstudien använder en IoT Edge-aktiverad virtuell Linux-dator (VM) från Azure Marketplace för att simulera en IoT Edge enhet. Den IoT Edge enheten använder en modul som genererar simulerad miljö telemetri. Du visar Telemetrin på en instrument panel i ditt IoT Central-program.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enhets mall för en IoT Edge enhet
> * Skapa en IoT Edge enhet i IoT Central
> * Distribuera en simulerad IoT Edge enhet till en virtuell Linux-dator

## <a name="prerequisites"></a>Krav

Slutför snabb starten [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > anpassade program** .

För att slutföra stegen i den här självstudien behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Hämta IoT Edge manifest filen från GitHub. Högerklicka på följande länk och välj sedan **Spara länk som**: [EnvironmentalSensorManifest. JSON](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Skapa enhets mall

I det här avsnittet skapar du en enhets mall för en IoT Edge enhet som ansluter till ditt IoT Central-program. Du importerar ett IoT Edge manifest för att komma igång och sedan ändra mallen för att lägga till mallar för telemetri och vyer:

### <a name="import-manifest-to-create-template"></a>Importera manifestet för att skapa mallen

Så här skapar du en enhets mal len från ett IoT Edge manifest:

1. I ditt IoT Central program, navigerar du till **enhets mallar** och väljer **+ ny**.

1. På sidan **Välj Malltyp** väljer du panelen **Azure IoT Edge** . Välj sedan **Nästa: anpassa**.

1. På sidan **överför ett Azure IoT Edge distributions manifest** väljer du **Bläddra** för att ladda upp **EnvironmentalSensorManifest. JSON** som du laddade ned tidigare. Välj sedan **Nästa: granska**.

1. På sidan **Granska** väljer du **skapa**.

1. När mallen har skapats ändrar du dess namn till *miljö sensor gräns enheten*.

1. Välj **Hantera** -gränssnittet i **SimulatedTemperatureSensor** -modulen för att visa de två egenskaperna som definierats i manifestet:

![Enhets mal len skapades från IoT Edge manifest](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Lägg till telemetri i manifestet

Ett IoT Edge manifest definierar inte Telemetrin som en modul skickar. Du måste lägga till telemetri-definitionerna i enhets mal len. **SimulatedTemperatureSensor** -modulen skickar telemetridata som ser ut som följande JSON:

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

Lägga till telemetri-definitionerna i enhets mal len:

1. Välj **Hantera** gränssnitt i enhets mal len för **miljö sensor gränsen** .

1. Välj **+ Lägg till funktion**. Ange *datorn* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **objekt** som schema typ och välj sedan **definiera**. På sidan objekt definition lägger du till *temperatur* och *tryck* som attribut av typen **Double** och väljer sedan **Använd**.

1. Välj **+ Lägg till funktion**. Ange *omgivande* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **objekt** som schema typ och välj sedan **definiera**. På sidan objekt definition lägger du till *temperatur* och *fuktighet* som attribut av typen **Double** och väljer sedan **Använd**.

1. Välj **+ Lägg till funktion**. Ange *timeCreated* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **datetime** som schema typ.

1. Välj **Spara** för att uppdatera mallen.

**Hanterings** gränssnittet innehåller nu typerna **Machine**, **omgivande**och **timeCreated** telemetri:

![Gränssnitt med typer av dator och omgivande telemetri](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Lägg till vyer i mallen

Enhets mal len har ännu ingen vy som låter en operatör se Telemetrin från den IoT Edge enheten. Så här lägger du till en vy i enhets mal len:

1. Välj **vyer** i enhets mal len för **miljö sensor gränsen** .

1. På sidan **Välj att lägga till en ny vy** väljer du den **visualisera enhets** panelen.

1. Ändra vyns namn om du vill *visa IoT Edge enhetens telemetri*.

1. Välj typer av telemetri för **omgivning** och **datorer** . Välj sedan **Lägg till panel**.

1. Klicka på **Spara** för att spara vyn **IoT Edge enhets telemetri** .

![Enhets mal len med vyn telemetri](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publicera mallen

Innan du kan lägga till en enhet som använder **enhets mal len miljö sensor gräns** måste du publicera mallen.

Navigera till enhets mal len för **miljö sensor** och välj **publicera**. Välj sedan **publicera** för att publicera mallen:

![Publicera enhets mal len](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Lägga till IoT Edge-enhet

Nu har du publicerat mallen för **miljö sensor gräns** , du kan lägga till en enhet till ditt IoT Central-program:

1. I ditt IoT Central-program går du till sidan **enheter** och väljer **miljö sensor gräns enhet** i listan över tillgängliga mallar.

1. Välj **+** om du vill lägga till en ny enhet från mallen. På sidan **Skapa ny enhet** väljer du **skapa**.

Nu har du en ny enhet med statusen **registrerad**:

![Publicera enhets mal len](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

När du distribuerar den IoT Edge enheten senare i den här självstudien behöver du de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enhet** väljer du den enhet som du skapade.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** noterar du **ID-omfånget**, **enhets-ID: t**och den **primära nyckeln**. Du använder dessa värden senare.

1. Välj **Stäng**.

Nu har du slutfört konfigurationen av IoT Central-programmet så att en IoT Edge-enhet kan ansluta.

## <a name="deploy-an-iot-edge-device"></a>Distribuera en IoT Edge-enhet

I den här självstudien använder du en Azure IoT Edge-aktiverad Linux-baserad virtuell dator som skapats på Azure för att simulera en IoT Edge-enhet. Så här skapar du den IoT Edge-aktiverade virtuella datorn:

1. Gå till [Azure IoT Edge på Ubuntu på](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) Azure Marketplace. Välj sedan **Hämta nu**.

1. På sidan **skapa den här appen på Azure** väljer du **Fortsätt**. Den här länken tar dig till Azure Portal, där du kan behöva logga in på din Azure-prenumeration.

1. På sidan **Azure IoT Edge på Ubuntu** i Azure Portal väljer du **skapa**.

1. På sidan **skapa en virtuell dator > grundläggande** information:

    - Välj din Azure-prenumeration.
    - Skapa en ny resurs grupp med namnet **IoT-Edge-Devices**.
    - Använd namnet på den virtuella datorn: **iotedgevm**.
    - Välj den region som är närmast dig.
    - Ange autentiseringstyp för **lösen ord**.
    - Välj ett användar namn och lösen ord.
    - Du kan lämna de andra alternativen till standardvärdena.
    - Välj **Granska + skapa**.

1. När verifieringen är klar väljer du **skapa**.

När distributionen är klar väljer **du gå till resurs**efter några minuter.

### <a name="provision-vm-as-an-iot-edge-device"></a>Etablera virtuell dator som en IoT Edge enhet 

Så här etablerar du en virtuell dator som en IoT Edge enhet:

1. I avsnittet **support och fel sökning** väljer du **seriell konsol**.

1. Tryck på **RETUR** för att `login:` se prompten. Logga in genom att ange ditt användar namn och lösen ord.

1. Kör följande kommando för att kontrol lera IoT Edge runtime-versionen. Vid tidpunkten för skrivning är versionen 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Använd `nano` redigeraren för att öppna filen IoT Edge config. yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Rulla nedåt tills du ser `# Manual provisioning configuration`. Kommentera ut följande tre rader som du ser i följande kodfragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Rulla nedåt tills du ser `# DPS symmetric key provisioning configuration`. Ta bort kommentaren till de efterföljande åtta raderna som visas i följande kodfragment:

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

1. Ersätt `{scope_id}` med **ID-omfånget** som du antecknade tidigare.

1. Ersätt `{registration_id}` med **enhets-ID: t** som du antecknade tidigare.

1. Ersätt `{symmetric_key}` med den **primära nyckeln** som du antecknade tidigare.

1. Spara ändringarna (**CTRL-O**) och avsluta (**CTRL-X**) `nano` redigeraren.

1. Kör följande kommando för att starta om IoT Edge daemon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Kör följande kommando för att kontrol lera status för IoT Edge moduler:

    ```bash
    iotedge list
    ```

    Utdata ser ut ungefär så här:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Visa Telemetrin

Den simulerade IoT Edge enheten körs nu på den virtuella datorn. I ditt IoT Central-program är enhets statusen nu **etablerad** på sidan **enheter** :

![Etablerad enhet](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Du kan se telemetri på sidan **visa IoT Edge enhets telemetri** :

![Telemetri för enhet](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Sidan **moduler** visar status för IoT Edge moduler:

![Telemetri för enhet](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare är det nu viktigt att du har lärt dig hur du arbetar med och hanterar IoT Edge enheter i IoT Central. ett förslag till nästa steg är att läsa:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Anslut till Azure IoT Central](./concepts-get-connected.md)
