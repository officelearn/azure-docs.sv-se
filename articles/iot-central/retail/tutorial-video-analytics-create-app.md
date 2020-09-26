---
title: Självstudie – Skapa ett program för video analys – identifiering av objekt och motion i Azure IoT Central
description: I den här självstudien visas hur du skapar ett video analys program i IoT Central. Du skapar den, anpassar den och ansluter den till andra Azure-tjänster.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369577"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Självstudie: skapa ett program för video analys – identifiering av objekt och motion i Azure IoT Central

Som Solution Builder lär du dig hur du skapar ett video analys program med programmallen IoT Central *video analys – objekt och rörelse identifiering* , Azure IoT Edge enheter och Azure Media Services. I lösningen används en detalj handels butik för att visa hur du kan möta de vanliga affärs behov för att övervaka säkerhets kameror. Lösningen använder automatisk objekt identifiering i ett video flöde för att snabbt identifiera och hitta intressanta händelser.

Exempel programmet innehåller två simulerade enheter och en IoT Edge Gateway. Följande självstudier visar två metoder för att experimentera med och förstå funktionerna i gatewayen:

* Skapa IoT Edge gateway i en virtuell Azure-dator och Anslut en simulerad kamera.
* Skapa IoT Edge gateway på en riktig enhet, till exempel en Intel-NUC och Anslut en riktig kamera.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd program mal len Azure IoT Central video analys för att skapa ett program för åter försäljnings lager
> * Anpassa program inställningarna
> * Skapa en enhets mall för en IoT Edge gateway-enhet
> * Lägga till en gateway-enhet i IoT Central-programmet

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här själv studie serien behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).
* Om du använder en riktig kamera måste du ha anslutning mellan IoT Edge-enheten och kameran och du behöver **real tids strömnings protokoll** kanal.

## <a name="initial-setup"></a>Första konfigurationen

I de här självstudierna uppdaterar och använder du flera konfigurationsfiler. De ursprungliga versionerna av dessa filer är tillgängliga i [lva-Gateway GitHub-](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) lagringsplatsen. Databasen innehåller också en Scratchpad-textfil där du kan hämta och använda för att registrera konfigurations värden från de tjänster som du distribuerar.

Skapa en mapp med namnet *lva-Configuration* på den lokala datorn för att spara kopior av filerna. Högerklicka på var och en av följande länkar och välj **Spara som** för att spara filen i mappen *lva* :

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> GitHub-lagringsplatsen innehåller också käll koden för modulerna **LvaEdgeGatewayModule** och **lvaYolov3** IoT Edge. Mer information om hur du arbetar med käll koden finns i avsnittet [bygga lva Gateway-moduler](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Distribuera och konfigurera Azure Media Services

Lösningen använder ett Azure Media Services-konto för att lagra videoklipp om objekt identifiering som skapats av IoT Edge gateway-enheten.

När du skapar Media Services kontot:

- Du måste ange ett konto namn, en Azure-prenumeration, en plats, en resurs grupp och ett lagrings konto. Skapa ett nytt lagrings konto med hjälp av standardinställningarna när du skapar Media Services-kontot.

- Välj regionen **USA, östra** för platsen.

- Skapa en ny resurs grupp med namnet *lva-RG* i regionen **USA, östra** för Media Services-och lagrings konton. När du har slutfört självstudierna är det enkelt att ta bort alla resurser genom att ta bort resurs gruppen *lva-RG* .

Skapa [Media Services kontot i Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> De här självstudierna använder regionen **USA, östra** i alla exempel. Du kan använda din närmaste region om du vill.

Anteckna ditt **Media Services** konto namn i *scratchpad.txt* -filen.

När distributionen är klar går du till sidan **Egenskaper** för ditt **Media Services** konto. Anteckna **resurs-ID: t** i *scratchpad.txt* -filen, du använder det här värdet senare när du konfigurerar IoT Edge-modulen.

Konfigurera sedan en Azure Active Directory tjänstens huvud namn för din Media Services-resurs. Välj **API-åtkomst** och sedan **tjänstens huvud namns autentisering**. Skapa en ny Azure Active Directory-app med samma namn som din Media Services resurs och skapa en hemlighet med en beskrivning *IoT Edge åtkomst*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Konfigurera AAD-app för AMS":::

När hemligheten har genererats rullar du ned till avsnittet **Kopiera dina autentiseringsuppgifter för att ansluta ditt tjänst huvud program** . Välj sedan **JSON**. Du kan kopiera all inloggnings information från en enda go. Anteckna den här informationen i *scratchpad.txt* -filen, du använder den senare när du konfigurerar IoT Edge enheten.

> [!WARNING]
> Det här är din enda chans att visa och spara hemligheten. Om du förlorar det måste du generera en annan hemlighet.

## <a name="create-the-azure-iot-central-application"></a>Skapa Azure IoT Central-programmet

I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du använder det här programmet i själv studie serien för att bygga en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Logga in med de autentiseringsuppgifter du använder för att få åtkomst till din Azure-prenumeration.

1. Om du vill börja skapa ett nytt Azure IoT Central-program väljer du **nytt program** på sidan **skapa** .

1. Välj **åter försäljning**. På sidan detalj handel visas flera mallar för åter försäljning.

Så här skapar du ett nytt video analys program:

1. Välj program mal len **video analys-objekt och rörelse identifiering** . Den här mallen innehåller enhetsspecifika för de enheter som används i självstudien. Mallen innehåller exempel på instrument paneler som operatörer kan använda för att utföra åtgärder som att övervaka och hantera kameror.

1. Du kan också välja ett eget **program namn**. Det här programmet baseras på ett fiktivt detalj handels lager med namnet Northwind Traders. I självstudien används **program namnet** *Northwind Traders video analys*.

    > [!NOTE]
    > Om du använder ett eget **program namn**måste du fortfarande använda ett unikt värde för programmets **URL**.

1. Om du har en Azure-prenumeration väljer du **katalog**, **Azure-prenumeration**och **USA** som **plats**. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs. I den här självstudien används tre enheter – två kameror och en IoT Edge enhet – så om du inte använder den kostnads fria utvärderings versionen debiteras du för användning.

    Mer information om kataloger, prenumerationer och platser finns i [snabb start för att skapa ett program](../core/quick-deploy-iot-central.md).

1. Välj **Skapa**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Sidan Skapa program i Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Hämta konfigurations data

Senare i den här självstudien när du konfigurerar IoT Edge Gateway, behöver du vissa konfigurations data från IoT Central programmet. Den IoT Edge enheten behöver den här informationen för att registrera dig hos och ansluta till programmet.

I avsnittet **Administration** väljer **du ditt program** och noterar **programmets URL** och **program-ID: t** i *scratchpad.txt* -filen:

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Skärm bild som visar fönstret Administration på en video analys sida med programmet U R L och program I D markerat.":::

Välj **API-token** och generera en ny token med namnet **LVAEdgeToken** för **operatörs** rollen:

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Generera token":::

Anteckna token i *scratchpad.txt* -filen för senare. När dialog rutan stängs kan du inte Visa token igen.

I avsnittet **Administration** väljer du **enhets anslutning**och väljer sedan **SAS-IoT-Devices**.

Anteckna **primär nyckeln** för enheter i *scratchpad.txts * filen. Du använder den här *primära gruppen signatur för delad åtkomst* senare när du konfigurerar IoT Edge-enheten.

## <a name="edit-the-deployment-manifest"></a>Redigera distributions manifestet

Du distribuerar en IoT Edge-modul med ett distributions manifest. I IoT Central kan du importera manifestet som en enhets mall och sedan låta IoT Central hantera modul distributionen.

Så här förbereder du distributions manifestet:

1. Öppna filen *deployment.amd64.js* som du sparade i mappen *lva-konfiguration* med hjälp av en text redigerare.

1. Hitta `LvaEdgeGatewayModule` inställningarna och ändra avbildnings namnet enligt följande kodfragment:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Lägg till namnet på ditt Media Services-konto i `env` noden i `LvaEdgeGatewayModule` avsnittet. Du har gjort en anteckning om det här konto namnet i *scratchpad.txt* -filen:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Mallen visar inte dessa egenskaper i IoT Central, så du måste lägga till Media Services-konfigurations värden i distributions manifestet. Leta upp `lvaEdge` modulen och ersätt plats hållarna med de värden som du antecknade i *scratchpad.txt* -filen när du skapade ditt Media Services-konto.

    `azureMediaServicesArmId`Är **resurs-ID: t** som du antecknade i *scratchpad.txt* -filen när du skapade Media Services-kontot.

    Du har gjort en anteckning om `aadTenantId` , `aadServicePrincipalAppId` och `aadServicePrincipalSecret` i *scratchpad.txt* -filen när du skapade tjänstens huvud namn för ditt Media Services-konto:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Spara ändringarna.

Om du vill kan du ersätta Yolov3-modulen med den maskinvarubaserade MultiServer- [ &trade; modellen Server – Edge AI-tillägget](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) från Intel. Du kan hämta ett exempel på distributions manifest [deployment.openvino.amd64.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Det här manifestet ersätter konfigurations inställningarna för `lvaYolov3` modulen med följande konfiguration:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Skapa Azure IoT Edge gateway-enhet

Program för video analys – objekt-och motion-identifiering innehåller en **lva kant objekt detektor** och en **lva mall för kant rörelse avkänning** . I det här avsnittet skapar du en mall för en gateway-enhet med hjälp av distributions manifestet och lägger till gateway-enheten i IoT Central-programmet.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Skapa en enhets mall för LVA Edge-gatewayen

Så här importerar du distributions manifestet och skapar **lva Edge Gateway** -enhetens mall:

1. I ditt IoT Central program, navigerar du till **enhets mallar**och väljer **+ ny**.

1. På sidan **Välj Malltyp** väljer du panelen **Azure IoT Edge** . Välj sedan **Nästa: anpassa**.

1. På sidan **överför ett Azure IoT Edge distributions manifest** anger du *lva Edge Gateway* som Mallnamn och kontrollerar **gateway-enheten med underordnade enheter**.

    Sök inte efter distributions manifestet ännu. Om du gör det förväntar sig distributions guiden ett gränssnitt för varje modul, men du behöver bara exponera gränssnittet för **LvaEdgeGatewayModule**. Du överför manifestet i ett senare steg.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Ladda inte upp distributions manifest":::

    Välj **Nästa: Granskning**.

1. På sidan **Granska** väljer du **skapa**.

### <a name="import-the-device-capability-model"></a>Importera enhetens kapacitets modell

Enhets mal len måste innehålla en modell för enhets kapacitet. På sidan **lva Edge Gateway** väljer du panelen **Importera kapacitets modell** . Navigera till mappen *lva* som du skapade tidigare och välj *LvaEdgeGatewayDcm.jspå* filen.

Enhets mal len **lva Edge Gateway** innehåller nu **modulen lva Edge Gateway** tillsammans med tre gränssnitt: **enhets information**, **lva Edge Gateway-inställningar**och **lva Edge Gateway-gränssnitt**.

### <a name="replace-the-manifest"></a>Ersätt manifestet

På sidan **lva Edge Gateway** väljer du **+ Ersätt manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Ersätt manifest":::

Navigera till mappen *lva-Configuration* och välj *deployment.amd64.jspå* manifest filen som du redigerade tidigare. Välj **Överför**. När verifieringen är klar väljer du **Ersätt**.

### <a name="add-relationships"></a>Lägg till relationer

I enhets mal len för **lva Edge-Gateway** under **moduler/lva Edge Gateway module**väljer du **relationer**. Välj **+ Lägg till relation** och Lägg till följande två relationer:

|Visningsnamn               |Name          |Mål |
|-------------------------- |------------- |------ |
|LVA kant rörelse detektor   |Använd standard   |LVA Edge motion detektor-enhet |
|LVA kant objekts detektor   |Använd standard   |LVA Edge objekt detektor enhet |

Välj sedan **Spara**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Lägg till relationer":::

### <a name="add-views"></a>Lägga till vyer

Enhets mal len för **lva Edge-Gateway** innehåller inte några visnings definitioner.

Så här lägger du till en vy i enhets mal len:

1. I enhets mal len för **lva Edge-Gateway** navigerar du till **vyer** och väljer **visualiseringen av enhets** panelen.

1. Ange *lva Edge gateway-enhet* som visnings namn.

1. Lägg till följande paneler i vyn:

    * En panel med egenskaper för **enhets information** : **enhets modell**, **tillverkare**, **operativ system**, **processor arkitektur**, **program varu version**, **totalt minne**och **totalt lagrings utrymme**.
    * En linje diagram panel med värdena **ledigt minne** och **system pulsslags** telemetri.
    * En händelse historik panel med följande händelser: **skapa kamera**, **ta bort kamera**, **starta om**modul, **modul startad**, **modul stoppad**.
    * En 2x1 senast kända värde-panel som visar **IoT Central klientens status** telemetri.
    * En 2x1 senast kända värde-panel som visar telemetri för **modulens tillstånd** .
    * En 1x1 senast kända värde-panel som visar **system Pulsslagets** telemetri.
    * En 1x1 senast kända värde-panel som visar de **anslutna kamerornas** telemetri.

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Instrumentpanel":::

1. Välj **Spara**.

### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan lägga till en enhet i programmet måste du publicera enhets mal len:

1. I enhets mal len för **lva Edge-Gateway** väljer du **publicera**.

1. I **mallen publicera den här enheten på program** sidan väljer du **publicera**.

**Lva Edge Gateway** är nu tillgängligt som enhets typ på sidan **enheter** i programmet.

## <a name="add-a-gateway-device"></a>Lägg till en gateway-enhet

Så här lägger du till en **lva Edge Gateway** -enhet i programmet:

1. Gå till sidan **enheter** och välj enhets mal len **lva Edge Gateway** .

1. Välj **+ Ny**.

1. I dialog rutan **skapa en ny enhet** ändrar du enhets namnet till *lva Gateway 001*och ändrar enhets-ID till *lva-Gateway-001*.

    > [!NOTE]
    > Enhets-ID: t måste vara unikt i programmet.

1. Välj **Skapa**.

Enhetens status är **registrerad**.

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

Du behöver de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enheter** väljer du den **lva-Gateway-001-** enhet som du skapade.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** gör du en anteckning i *scratchpad.txt* -filen för **ID-omfånget**, **enhets-ID: t**och enhetens **primär nyckel**. Du använder dessa värden senare.

1. Se till att anslutnings metoden är inställd på **signatur för delad åtkomst**.

1. Välj **Stäng**.

## <a name="next-steps"></a>Nästa steg

Nu har du skapat ett IoT Central program med hjälp av program mal len **video analys – objekt och rörelse identifiering** , skapat en enhets mall för gateway-enheten och lagt till en gateway-enhet i programmet.

Om du vill testa programmet för video analys – objekt-och motion-identifiering med hjälp av IoT Edge moduler som kör en virtuell dator i molnet med simulerade video strömmar:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

Om du vill prova program för video analys – objekt-och motion-identifiering med IoT Edge moduler som kör en riktig enhet med en riktig **ONVIF** -kamera:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
