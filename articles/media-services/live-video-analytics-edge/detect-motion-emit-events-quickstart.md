---
title: Identifiera rörelse-och genererar händelser – Azure
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att identifiera rörelse-och utsändnings händelser genom att programmatiskt anropa direkta metoder.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fca773d0583bee3bef4e7254bcca95866b2205e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091921"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Snabb start: identifiera rörelse-och genererar händelser

Den här snabb starten vägleder dig genom stegen för att komma igång med real tids video analys på IoT Edge. Den använder en virtuell Azure-dator som en IoT Edge-enhet och en simulerad livevideoström. När du har slutfört installations stegen kan du köra en simulerad real tids video ström genom ett medie diagram som identifierar och rapporterar en rörelse i den data strömmen. Följande diagram visar en grafisk representation av det medie diagrammet.

![Real video analys baserat på rörelse identifiering](./media/analyze-live-video/motion-detection.png) 

Den här artikeln baseras på [exempel kod](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) skriven i C#.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto som har en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
* [Visual Studio Code](https://code.visualstudio.com/) med följande fil namns tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Du kan uppmanas att installera Docker när du installerar tillägget Azure IoT-verktyg. Du kan ignorera prompten.

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

I den här självstudien krävs följande Azure-resurser:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge-linux.md) installerat

I den här snabb starten rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de nödvändiga resurserna i din Azure-prenumeration. Det gör du på följande sätt:

1. Öppna [Azure Cloud Shell](https://shell.azure.com).
1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Microsoft Azure fil resurs. Välj **skapa lagring** för att skapa ett lagrings konto för din Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från det konto som skriptet kommer att skapa för att använda med ditt Azure Media Services-konto.
1. I den nedrullningsbara menyn på vänster sida av Cloud Shells fönstret väljer du **bash** som din miljö.

    ![Miljö väljare](./media/quickstarts/env-selector.png)

1. Kör följande kommando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Om skriptet har slutförts bör du se alla nödvändiga resurser i din prenumeration.

1. När skriptet har körts väljer du klammerparenteserna för att exponera mappstrukturen. Du ser några filer i katalogen *~/clouddrive/lva-Sample* . Intressanthet i den här snabb starten är:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env*** – den här filen innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
     * ***~/clouddrive/lva-sample/appsetting.jspå*** -Visual Studio Code använder den här filen för att köra exempel koden.
     
Du behöver dessa filer när du konfigurerar din utvecklings miljö i Visual Studio Code i nästa avsnitt. Du kanske vill kopiera dem till en lokal fil för tillfället.

 ![Appinställningar](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

1. Klona lagrings platsen från den här platsen: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Öppna mappen där lagrings platsen har hämtats i Visual Studio Code.
1. I Visual Studio Code går du till mappen *src/Cloud-to-Device-console-app* . Där skapar du en fil och namnger den *appsettings.js*. Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från *~/clouddrive/lva-Sample/-appsettings.jspå* filen som du skapade tidigare i den här snabb starten.

    Texten bör se ut som följande utdata.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Gå till mappen *src/Edge* och skapa en fil med namnet *. kuvert*.
1. Kopiera innehållet i */clouddrive/lva-Sample/Edge-Deployment/.env* -filen. Texten bör se ut som i följande kod.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Granska exempelfilerna

1. I Visual Studio Code går du till *src/Edge*. Du ser *. kuvert* -filen och några mallar för distributions mal len.

    Distributions mal len refererar till distributions manifestet för gräns enheten, där variabler används för vissa egenskaper. *. Kuvert* -filen innehåller värdena för variablerna.
1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:

    * ***C2D-console-app. CSPROJ*** – projekt filen för Visual Studio Code.
    * ***operations.js*** en lista över de åtgärder som du vill att programmet ska köra.
    * ***Program.cs*** – exempel koden. Den här koden:
    
      * Läser in appinställningar.
      * Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
      * Pausar så att du kan granska programmets utdata i **terminalfönstret** och granska de händelser som genererats av modulen i fönstret **utdata** .
      * Anropar direkta metoder för att rensa resurser.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generera och distribuera distributions manifestet

Distributions manifestet definierar vilka moduler som distribueras till en Edge-enhet. Den definierar också konfigurations inställningar för dessa moduler. 

Följ de här stegen för att generera manifestet från mallfilen och distribuera den sedan till gräns enheten.

1. Öppna Visual Studio Code.
1. Bredvid fönstret **Azure IoT Hub** väljer du ikonen **fler åtgärder** för att ange IoT Hub anslutnings sträng. Du kan kopiera strängen från filen *src/Cloud-to-Device-console-app/appsettings.jsi* filen. 

    ![Ange IOT-anslutningssträng](./media/quickstarts/set-iotconnection-string.png)

1. Högerklicka på **src/Edge/deployment.template.jspå** och välj **generera IoT Edge distributions manifest**.

    ![Generera distributions manifestet för IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Den här åtgärden ska skapa en manifest fil med namnet *deployment.amd64.jspå* i mappen *src/Edge/config* .
1. Högerklicka på **src/Edge/config/deployment.amd64.jspå**, Välj **skapa distribution för en enskild enhet**och välj sedan namnet på din Edge-enhet.

    ![Skapa en distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)

1. När du uppmanas att välja en IoT Hub enhet väljer du **lva – exempel-Device** på den nedrullningsbara menyn.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys i real tid för IoT Edge (Modulnamn `lvaEdge` )
    * RTSP-Simulator (Real Time Streaming Protocol) (Modulnamn `rtspsim` )

RTSP Simulator-modulen simulerar en real tids video ström med hjälp av en videofil som kopierades till din Edge-enhet när du körde [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

I det här skedet distribueras modulerna men inga medie diagram är aktiva.

## <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

Du använder live video analys i IoT Edge-modulen för att identifiera rörelser i inkommande video strömmar och skicka händelser till IoT Hub. Följ dessa steg om du vill se de här händelserna:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub i det nedre vänstra hörnet.
1. Expandera noden **enheter** .
1. Högerklicka på **lva-Sample-Device** och välj **starta övervakning inbyggd händelse slut punkt**.

    ![Starta övervakning av en inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Kör exempel programmet

Följ de här stegen för att köra exempel koden:

1. I Visual Studio Code går du till *src/Cloud-to-Device-console-app/operations.jspå*.
1. Se till att du ser följande värde på **GraphTopologySet** -noden:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. På **GraphInstanceSet** -och **GraphTopologyDelete** -noderna kontrollerar du att värdet för `topologyName` motsvarar värdet för `name` egenskapen i graf-topologin:

    `"topologyName" : "MotionDetection"`
    
1. Starta en felsökningssession genom att välja F5-nyckeln. I **terminalfönstret** visas vissa meddelanden.
1. *operations.jspå* filen börjar med anrop till `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har avslutat tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

    **Terminalfönstret** visar nästa uppsättning med direkta metod anrop:
     
     * Ett anrop till `GraphTopologySet` som använder föregående`topologyUrl`
     * Ett anrop till `GraphInstanceSet` som använder följande text:
     
         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "MotionDetection",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```
     
     * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och video flödet
     * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd
1. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:
     * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
     * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
     * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
     * Ett sista anrop till `GraphTopologyList` visar att listan är tom.

## <a name="interpret-results"></a>Tolka resultaten

När du kör medie diagrammet passerar resultatet från noden för rörelse detektorn genom IoT Hub Sink-noden till IoT Hub. De meddelanden som visas i fönstret **utdata** i Visual Studio Code innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

I föregående utdata: 
* Meddelandet är en Diagnostics-händelse `MediaSessionEstablished` . Det anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` refererar refererar noden i den diagram sto pol Ogin från vilken meddelandet genererades. I det här fallet kommer meddelandet från noden RTSP-källa.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Värdet anger den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


### <a name="motiondetection-event"></a>MotionDetection-händelse

När motion identifieras skickar live video analys i IoT Edge-modulen en händelse för nedgradering. `type`Anges till `motion` för att indikera att det är ett resultat från processen för identifiering av rörelser. `eventTime`Värdet visar när rörelsen (i UTC) ägde rum. 

Här är ett exempel på det här meddelandet:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

I det här exemplet: 

* I `applicationProperties` `subject` refererar den nod i medie diagrammet som meddelandet genererades från. I det här fallet kommer meddelandet från noden motion-identifiering.
* I `applicationProperties` `eventType` anger att den här händelsen är en analys händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Värdet är data om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse, så texten innehåller `timestamp` och `inferences` data.
* `inferences`Data indikerar att `type` är `motion` . Den innehåller ytterligare information om `motion` händelsen.
* `box`Avsnittet innehåller koordinaterna för en avgränsnings ruta runt det flyttade objektet. Värdena normaliseras av videons bredd och höjd i bild punkter. Till exempel är bredden 1920 och höjden är 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova de andra snabb starterna bör du behålla de resurser du har skapat. Annars går du till dina resurs grupper i Azure Portal, väljer den resurs grupp där du körde den här snabb starten och tar sedan bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Kör de andra snabb starterna, t. ex. identifiering av ett objekt i en direktsända video flöde.        
