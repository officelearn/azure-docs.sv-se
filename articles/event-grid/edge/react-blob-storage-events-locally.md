---
title: Reagera på Blob Storage-modulhändelser – Azure Event Grid IoT Edge | Microsoft-dokument
description: Reagera på Blob Storage-modulhändelser
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086600"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Självstudiekurs: Reagera på Blob Storage-händelser på IoT Edge (förhandsversion)
Den här artikeln visar hur du distribuerar Azure Blob Storage på IoT-modulen, som skulle fungera som en Event Grid-utgivare för att skicka händelser om Blob-skapande och Blob-borttagning till Event Grid.  

En översikt över Azure Blob Storage på IoT Edge finns i [Azure Blob Storage på IoT Edge](../../iot-edge/how-to-store-data-blob.md) och dess funktioner.

> [!WARNING]
> Azure Blob Storage på IoT Edge-integrering med Event Grid är i förhandsversion

För att slutföra den här guiden behöver du:

* **Azure-prenumeration** - Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub och IoT Edge-enhet** – Följ stegen i snabbstarten för [Linux-](../../iot-edge/quickstart-linux.md) eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har någon.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera IoT Edge-module för händelserutnät

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure-portalen.

>[!NOTE]
> I den här självstudien distribuerar du modulen Event Grid utan att vara beständig. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistens finns i följande artiklar: [Beständigt tillstånd i Linux](persist-state-linux.md) eller [Kvarstår i Windows](persist-state-windows.md). För produktionsarbetsbelastningar rekommenderar vi att du installerar modulen Event Grid med uthållighet.


### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge-enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhetshantering.** 
1. Klicka på målenhetens ID från listan över enheter
1. Välj **Ange moduler**. Håll sidan öppen. Du fortsätter med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Azure-portalen har en guide som hjälper dig att skapa ett distributionsmanifest i stället för att skapa JSON-dokumentet manuellt.  Den har tre steg: **Lägg till moduler,** **Ange vägar**och Granska **distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **Distributionsmoduler** väljer du **Lägg till**
1. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
1. Ange alternativ för att skapa namn, avbildning, behållare för behållaren:

   * **Namn**: eventgridmodule
   * **Bild URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Skapa alternativ för att skapa behållare:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Klicka på **Spara**
 1. Fortsätt till nästa avsnitt för att lägga till Azure Event Grid Subscriber-modulen innan du distribuerar dem tillsammans.

    >[!IMPORTANT]
    > I den här självstudien får du lära dig att distribuera modulen Event Grid så att både HTTP/HTTPs-begäranden, klientautentisering inaktiveras. För produktionsarbetsbelastningar rekommenderar vi att du endast aktiverar HTTPs-begäranden och prenumeranter med klientautentisering aktiverad. Mer information om hur du konfigurerar Modulen Event Grid på ett säkert sätt finns i [Säkerhet och autentisering](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuera IoT Edge-modulen För händelserutnät

I det här avsnittet visas hur du distribuerar en annan IoT-modul som skulle fungera som en händelsehanterare till vilken händelser som kan levereras.

### <a name="add-modules"></a>Lägga till moduler

1. Välj **Lägg till** igen i avsnittet **Distributionsmoduler.** 
1. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
1. Ange alternativ för att skapa namn, avbildning och behållare för behållaren:

   * **Namn**: abonnent
   * **Bild URI:**`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Alternativ för att skapa behållare:** Inga
1. Klicka på **Spara**
1. Fortsätt till nästa avsnitt för att lägga till Azure Blob Storage-modulen

## <a name="deploy-azure-blob-storage-module"></a>Distribuera Azure Blob Storage-modul

I det här avsnittet visas hur du distribuerar Azure Blob Storage-modulen, som skulle fungera som en Event Grid-utgivare som publicerar Blob skapade och tagit bort händelser.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **Distributionsmoduler** väljer du **Lägg till**
2. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
3. Ange alternativ för att skapa namn, avbildning och behållare för behållaren:

   * **Namn**: azureblobstorageoniotedge
   * **Bild URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Skapa alternativ för att skapa behållare:**

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob Storage module kan publicera händelser med både HTTPS och HTTP. 
   > - Om du har aktiverat klientbaserad autentisering för EventGrid kontrollerar du att du uppdaterar värdet `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`för EVENTGRID_ENDPOINT för att tillåta https, så här: .
   > - Lägg också till `AllowUnknownCertificateAuthority=true` en annan miljövariabel till ovanstående Json. När du pratar med EventGrid via HTTPS tillåter **AllowUnknownCertificateAuthority** lagringsmodulen att lita på självsignerade EventGrid-servercertifikat.

4. Uppdatera JSON som du kopierade med följande information:

   - Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamnen ska vara 3 till 24 tecken långa, med gemener och siffror. Inga mellanslag.

   - Ersätt `<your storage account key>` med en 64-byte base64 nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att komma åt blob-lagringen från andra moduler.

   - Ersätt `<event grid module name>` med namnet på modulen Event Grid.
   - Byt ut `<storage mount>` enligt ditt behållaroperativsystem.
     - För **Linux-behållare, min volym:/blobroot**
     - För**Windows-behållare, my-volume:C:/BlobRoot**

5. Klicka på **Spara**
6. Klicka på **Nästa** om du vill fortsätta till flödet

    > [!NOTE]
    > Om du använder en Virtuell Azure-dator som kantenhet lägger du till en inkommande portregel för att tillåta inkommande trafik på värdportarna som används i den här självstudien: 4438, 5888, 8080 och 11002. Instruktioner om hur du lägger till regeln finns i [Så här öppnar du portar till en virtuell dator](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Installationsvägar

Behåll standardvägarna och välj **Nästa** om du vill fortsätta till granskningsavsnittet

### <a name="review-deployment"></a>Granska distribution

1. Granskningsavsnittet visar det JSON-distributionsmanifest som skapades baserat på dina val i föregående avsnitt. Bekräfta att du ser följande fyra moduler: **$edgeAgent**, **$edgeHub**, **eventgridmodule,** **prenumerant** och **azureblobstorageoniotedge** som alla distribueras.
2. Granska distributionsinformationen och välj sedan **Skicka**.

## <a name="verify-your-deployment"></a>Verifiera distributionen

1. När du har skickat distributionen går du tillbaka till IoT Edge-sidan i din IoT-hubb.
2. Välj den **IoT Edge-enhet** som du riktade in dig på med distributionen för att öppna dess information.
3. I enhetsinformationen kontrollerar du att modulerna eventgridmodule, abonnent och azureblobstorageoniotedge visas som både **angivna i distributionen** och **Rapporterad per enhet**.

   Det kan ta en stund innan modulen startas på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan för att se en uppdaterad status.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicera BlobCreated och BlobDeleted händelser

1. Den här modulen skapar automatiskt ämnet **MicrosoftStorage**. Kontrollera att den finns
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Om klientautentiseringen är aktiverad via SAS-tangenten för HTTPS-flödet ska SAS-nyckeln som angetts tidigare läggas till som ett huvud. Därför curl begäran kommer att vara:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Om klientautentiseringen aktiveras via certifikat för HTTPS-flödet kommer den curl-begäran att vara:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Prenumeranter kan registrera sig för evenemang som publiceras i ett ämne. Om du vill ta emot alla händelser måste du skapa en Event Grid-prenumeration för **MicrosoftStorage-avsnittet.**
    1. Skapa blobsubscription.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Egenskapen **endpointType** anger att prenumeranten är en **Webhook**.  **SlutpunktenUrl** anger webbadressen där prenumeranten lyssnar efter händelser. Den här URL:en motsvarar exemplet på Azure-funktionen som du har distribuerat tidigare.

    2. Kör följande kommando för att skapa en prenumeration för ämnet. Bekräfta att HTTP-statuskoden `200 OK`är .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Om klientautentiseringen är aktiverad via SAS-tangenten för HTTPS-flödet ska SAS-nyckeln som angetts tidigare läggas till som ett huvud. Därför curl begäran kommer att vara:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Om klientautentiseringen aktiveras via certifikat för HTTPS-flödet kommer den curl-begäran att vara:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Kör följande kommando för att verifiera att prenumerationen har skapats. HTTP-statuskoden för 200 OK ska returneras.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Exempel på utdata:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Om klientautentiseringen är aktiverad via SAS-tangenten för HTTPS-flödet ska SAS-nyckeln som angetts tidigare läggas till som ett huvud. Därför curl begäran kommer att vara:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Om klientautentiseringen aktiveras via certifikat för HTTPS-flödet kommer den curl-begäran att vara:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Ladda ned [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och ansluta den till ditt lokala [lagringsutrymme](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verifiera händelseleverans

### <a name="verify-blobcreated-event-delivery"></a>Verifiera BlobCreated händelseleverans

1. Ladda upp filer som blockblobar till den lokala lagringen från Azure Storage Explorer och modulen publicerar automatiskt skapa händelser. 
2. Kolla in prenumerantloggarna för att skapa evenemang. Följ stegen för att [verifiera händelseleveransen](pub-sub-events-webhook-local.md#verify-event-delivery)

    Exempel på utdata:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verifiera BlobDeleted händelseleverans

1. Ta bort blobbar från det lokala lagringsutrymmet med Azure Storage Explorer och modulen publicerar automatiskt borttagningshändelser. 
2. Kolla in prenumerantloggarna för borttagningshändelse. Följ stegen för att [verifiera händelseleveransen](pub-sub-events-webhook-local.md#verify-event-delivery)

    Exempel på utdata:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Grattis! Du har slutfört handledningen. I följande avsnitt finns information om händelseegenskaperna.

### <a name="event-properties"></a>Händelseegenskaper

Här är listan över händelseegenskaper som stöds och deras typer och beskrivningar. 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för bloblagring. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | sträng | Åtgärden som utlöste händelsen. Det kan vara något av följande värden: <ul><li>BlobCreated - tillåtna `PutBlob` värden är: och`PutBlockList`</li><li>BlobDeleted - tillåtna `DeleteAfterUpload` `AutoDelete`värden är `DeleteBlob`och . <p>Händelsen `DeleteAfterUpload` genereras när blob automatiskt tas bort eftersom egenskapen deleteAfterUpload desired property är inställd på true. </p><p>`AutoDelete`händelse genereras när blob tas bort automatiskt eftersom deleteAfterMinutes önskat egenskapsvärde har upphört att gälla.</p></li></ul>|
| klientRequestId | sträng | ett klienttillsyn på begäran för lagrings-API-åtgärden. Det här ID:t kan användas för att korrelera till Azure Storage-diagnostikloggar med hjälp av fältet "client-request-id" i loggarna och kan tillhandahållas i klientbegäranden med huvudet "x-ms-client-request-id". Mer information finns i [Loggformat](/rest/api/storageservices/storage-analytics-log-format). |
| Id | sträng | Tjänstgenererat begärande-ID för lagrings-API-åtgärden. Kan användas för att korrelera till Azure Storage-diagnostikloggar med hjälp av fältet "request-id-header" i loggarna och returneras från att initiera API-anrop i huvudet "x-ms-request-id". Se [Loggformat](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | sträng | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| Contenttype | sträng | Den innehållstyp som angetts för blobben. |
| innehållLängd | heltal | Storleken på blobben i byte. |
| blobType (blobType) | sträng | Typ av blob. Giltiga värden är antingen "BlockBlob" eller "PageBlob". |
| url | sträng | Sökvägen till blobben. <br>Om klienten använder ett Blob REST API har url:en den här strukturen: * \<\>storage-account-name\<.blob.core.windows.net/-filnamn för behållarnamn\>/\<\>*. <br>Om klienten använder ett REST-API för datasjölagring har url:en den här strukturen: * \<\>storage-account-name\<.dfs.core.windows.net/ filnamnsfilnamn för filnamnet\>/\<\>*. |


## <a name="next-steps"></a>Nästa steg

Se följande artiklar i dokumentationen för Blob Storage:

- [Filtrera bloblagringshändelser](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Rekommenderade metoder för att använda Blob Storage-händelser](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

I den här självstudien publicerade du händelser genom att skapa eller ta bort blobbar i en Azure Blob Storage. Se de andra självstudierna för att lära dig hur du vidarebefordrar händelser till molnet (Azure Event Hub eller Azure IoT Hub): 

- [Vidarekoppla händelser till Azure Event Grid](forward-events-event-grid-cloud.md)
- [Vidarekoppla händelser till Azure IoT Hub](forward-events-iothub.md)
