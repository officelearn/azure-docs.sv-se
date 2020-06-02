---
title: Kontinuerlig video inspelning till molnet och uppspelningen från Cloud-självstudie – Azure
description: I den här självstudien får du lära dig hur du använder video analys i real tid för IoT Edge för att kontinuerligt spela in video i molnet och strömma vilken del som helst av videon med hjälp av Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261860"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Självstudie: kontinuerlig video inspelning till molnet och uppspelningen från molnet  

I den här självstudien får du lära dig hur du använder video analys i real tid för IoT Edge för att utföra [kontinuerlig videoinspelning](continuous-video-recording-concept.md) (CVR) till molnet och strömma vilken del som helst av videon med Media Services. Detta är användbart för scenarier som säkerhet, efterlevnad och andra, där det finns behov av att underhålla ett arkiv av tagningen från en kamera i flera dagar (eller veckor).

> [!div class="checklist"]
> * Konfigurera relevanta resurser
> * Granska koden som utför CVR
> * Kör exempel koden
> * Granska resultaten och visa videon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Vi rekommenderar att du läser igenom följande dokumentations sidor

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md) 
* [Scenarier för kontinuerlig video inspelning](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Krav

Krav för den här självstudien är följande

* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -tillägget och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tillägget.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Du kan ignorera det här meddelandet.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Slutför [installations skript för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

I slutet av stegen ovan kommer du att ha vissa Azure-resurser distribuerade i Azure-prenumerationen, inklusive:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installerat

## <a name="concepts"></a>Begrepp

Som förklaras [här](media-graph-concept.md)kan du med ett medie diagram definiera var mediet ska samlas in, hur det ska bearbetas och var resultatet ska levereras. För att kunna utföra CVR måste du samla in videon från en RTSP-kompatibel kamera och kontinuerligt registrera den på en [Azure Media Services till gång](terminology.md#asset). Diagrammet nedan visar en grafisk representation av det medie diagrammet.

![Medie diagram](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

I den här självstudien kommer du att använda en Edge-modul som skapats med [Live555 Media-servern](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) för att simulera en RTSP-kamera. I medie grafen använder du en RTSP- [källnod](media-graph-concept.md#rtsp-source) för att hämta Live-flödet och skicka videon till [till gångs mottagar-noden](media-graph-concept.md#asset-sink) som kommer att spela in videon till en till gång.

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Innan du börjar kontrollerar du att du har slutfört den tredje punkten i [kraven](#prerequisites). När resurs installations skriptet har slutförts klickar du på klammerparenteserna för att exponera mappstrukturen. Du kommer att se några filer som skapats under katalogen ~/clouddrive/lva-Sample.

![Appinställningar](./media/quickstarts/clouddrive.png)

Av intresse i den här självstudien är:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

Du kommer att behöva de här filerna för stegen nedan.

1. Klona lagrings platsen härifrån https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Starta Visual Studio Code och öppna mappen där du laddade ned lagrings platsen.
1. I Visual Studio Code bläddrar du till mappen "src/Cloud-to-Device-console-app" och skapar en fil med namnet "appSettings. JSON". Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från ~/clouddrive/lva-Sample/appSettings.JSON-filen. Texten bör se ut så här:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    Med anslutnings strängen IoT Hub kan du använda Visual Studio Code för att skicka kommandon till Edge-modulerna via Azure IoT Hub.
    
1. Bläddra sedan till mappen "src/Edge" och skapa en fil med namnet ". kuvert".
1. Kopiera innehållet från ~/clouddrive/lva-Sample/.env-filen. Texten bör se ut så här:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Granska exempelfilerna

I Visual Studio Code öppnar du "src/Edge/Deployment. template. JSON". Den här mallen definierar vilka Edge-moduler som ska distribueras till gräns enheten (den virtuella Azure Linux-datorn). Observera att det finns två poster i avsnittet moduler, med följande namn:

* lvaEdge – det här är den direktsända video analysen i IoT Edge modul
* rtspsim – detta är RTSP-simulatorn

Bläddra sedan till mappen "src/Cloud-to-Device-console-app". Här visas den appSettings. JSON-fil som du skapade tillsammans med några andra filer:

* C2D-console-app. CSPROJ – projekt filen för Visual Studio Code.
* Operations. JSON – den här filen visar en lista över de olika åtgärder som du skulle köra
* Program.cs – exempel koden som gör följande:
    * Läser in appinställningar
    * Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md)
    * Pausar så att du kan granska utdata från programmet i TERMINALFÖNSTRET och de händelser som genererats av modulen i fönstret utdata
    * Anropar direkta metoder för att rensa resurser

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge 

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningar för dessa moduler. Följ de här stegen för att generera ett sådant manifest från mallfilen och distribuera det sedan till gräns enheten.

1. Starta Visual Studio-kod
1. Ange anslutnings strängen IoTHub genom att klicka på ikonen "fler åtgärder" bredvid fönstret AZURE IOT HUB i det nedre vänstra hörnet. Du kan kopiera strängen från filen src/Cloud-to-Device-console-app/appSettings. JSON. 

    ![Ange IOT-anslutningssträng](./media/quickstarts/set-iotconnection-string.png)
1. Högerklicka sedan på filen "src/Edge/Deployment. template. JSON" och klicka på "generera IoT Edge distributions manifest". Visual Studio Code använder värdena från. kuvert-filen för att ersätta variablerna som finns i distributions mal Lav ställnings filen. Detta bör skapa en manifest fil i src/Edge/config-mappen med namnet "Deployment. amd64. JSON".

   ![Generera IoT Edge distributions manifest](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Högerklicka på "src/Edge/config/Deployment. amd64. JSON" och klicka på "skapa distribution för en enskild enhet".

   ![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)
1. Du uppmanas sedan att välja en IoT Hub enhet. Välj lva – exempel-Device i list rutan.
1. I cirka 30 sekunder uppdaterar du Azure-IoT Hub i det nedre vänstra avsnittet och ser till att gräns enheten har följande moduler distribuerade:
    * Video analys i real tid för IoT Edge (Modulnamn "lvaEdge")
    * RTSP Simulator (Modulnamn "rtspsim")
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Förbered för att övervaka modulerna 

När du använder live video analys i IoT Edge-modulen för att spela in direktuppspelad video ström skickas händelser till IoT Hub. Följ dessa steg för att se de här händelserna:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub längst ned till vänster.
1. Expandera noden enheter.
1. Högerklicka på lva-Sample-Device och välj alternativet "starta övervakning av inbyggd händelse övervakning".

    ![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Köra programmet 

1. Visual Studio Code, navigera till "src/Cloud-to-Device-console-app/Operations. JSON"
1. Under noden GraphTopologySet redigerar du följande:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Under noderna GraphInstanceSet och GraphTopologyDelete kontrollerar du att värdet för topologyName matchar värdet för egenskapen "namn" i ovanstående Graph-topologi:

    `"topologyName" : "CVRToAMSAsset"`  
1. Öppna [topologin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) i en webbläsare och titta på assetNamePattern. För att se till att du har en till gång med ett unikt namn kanske du vill ändra graf-instansen i Operations. JSON-filen (från standardvärdet "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Starta en felsökningssession (tryck på F5). Du kommer att börja se vissa meddelanden som skrivs ut i TERMINALFÖNSTRET.
1. Operations. JSON börjar inte med anrop till GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter tidigare snabb starter eller självstudier returnerar detta tomma listor och pausar sedan för att trycka på RETUR, till exempel nedan:

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
1. När du trycker på "retur" i TERMINALFÖNSTRET görs nästa uppsättning direkta metod anrop
     * Ett anrop till GraphTopologySet med hjälp av topologyUrl ovan.
     * Ett anrop till GraphInstanceSet med hjälp av följande text.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
     * Ett anrop till GraphInstanceActivate för att starta graf-instansen och starta video flödet
     * Ett andra anrop till GraphInstanceList för att visa att graf-instansen faktiskt är i körnings läge  
1. Utdata i TERMINALFÖNSTRET pausas nu vid frågan tryck på RETUR för att fortsätta. Tryck inte på RETUR för tillfället. Du kan rulla upp för att se nytto laster för JSON-svar för de direkta metoder som du har anropat
1. Om du nu växlar till UTDATAFÖNSTRET i Visual Studio Code, visas meddelanden som skickas till IoT Hub, av live video analys i IoT Edge-modulen.

     * Dessa meddelanden beskrivs i avsnittet nedan
1. Graf-instansen kommer fortsätta att köras och spela in videon – RTSP-simulatorn kommer att fortsätta att spela upp käll videon. För att stoppa inspelningen går du tillbaka till TERMINALFÖNSTRET och trycker på "Ange". Nästa serie anrop görs för att rensa resurser:

     * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen
     * Ett anrop till GraphInstanceDelete för att ta bort instansen
     * Ett anrop till GraphTopologyDelete för att ta bort topologin
     * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom

## <a name="interpret-the-results"></a>Tolka resultaten 

När du kör medie diagrammet skickar live video analys i IoT Edge-modulen vissa diagnostik-och drift händelser till IoT Edge Hub. Dessa händelser är de meddelanden som visas i UTDATAFÖNSTRET i Visual Studio Code, som innehåller avsnittet "Body" och "applicationProperties". Läs [den här](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikeln för att förstå vad dessa avsnitt representerar.

I meddelandena nedan definieras program egenskaperna och innehållet i bröd texten av modulen live video analys.

## <a name="diagnostic-events"></a>Diagnostiska händelser 

### <a name="mediasession-established-event"></a>MediaSession-etablerad händelse

När graf-instansen aktive ras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs i rtspsim-modulen. Om det lyckas skrivs den här händelsen ut:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Meddelandet är en diagnostisk händelse, MediaSessionEstablished, anger att noden RTSP-källa (ämnet) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.
* "Subject" i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* "eventType" i applicationProperties anger att det är en diagnostisk händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.

## <a name="operational-events"></a>Drift händelser 

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

När noden till gångs mottagare börjar spela in video, avger den här händelsen av typen Microsoft. Media. Graph. Operational. RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

"Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i. Anteckna det här värdet.

### <a name="recordingavailable-event"></a>RecordingAvailable-händelse

Som namnet antyder skickas händelsen RecordingStarted när inspelningen har startat – men video data kanske inte har laddats upp till till gången ännu. När noden till gångs mottagare har överfört video data till till gången, genererar den här händelsen av typen Microsoft. Media. Graph. Operational. RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen anger att tillräckligt med data har skrivits till till gången för att spelarna/klienter ska kunna starta uppspelningen av videon.

"Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

### <a name="recordingstopped-event"></a>RecordingStopped-händelse

När du inaktiverar graf-instansen slutar noden till gångs mottagare att spela in video till till gången, den genererar den här händelsen av typen Microsoft. Media. Graph. Operational. RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen indikerar att inspelningen har stoppats.

"Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

## <a name="media-services-asset"></a>Media Services till gång  

Du kan granska Media Services till gången som skapades av medie diagrammet genom att logga in på Azure Portal och visa videon.

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.
1. Leta upp ditt Media Services-konto bland de resurser som du har i din prenumeration och öppna bladet konto
1. Klicka på till gångar i Media Services listan

    ![Tillgångar](./media/continuous-video-recording-tutorial/assets.png)
1. Du hittar en till gång i listan med namnet sampleAsset-CVRToAMSAsset-Sample-Graph-1 – det här är det namn mönster som väljs i din graf-Topology-fil.
1. Klicka på till gången.
1. På sidan till gångs information klickar du på text rutan **Skapa ny** under strömnings-URL.

    ![Ny till gång](./media/continuous-video-recording-tutorial/new-asset.png)

1. I guiden som öppnas accepterar du standard alternativen och klickar på Lägg till. Mer information finns i [video uppspelning](video-playback-concept.md).

    > [!TIP]
    > Kontrol lera att [slut punkten för direkt uppspelning körs](../latest/streaming-endpoint-concept.md).
1. Videon ska läsas in i spelaren och du bör kunna trycka på **uppspelnings**> * * för att visa den.

> [!NOTE]
> Eftersom källan till videon var en behållare som simulerar ett kamera flöde, är tidsstämplar i videon relaterade till när du aktiverade graf-instansen och när du inaktiverar den. Du kan se [den här](playback-multi-day-recordings-tutorial.md) självstudien för att se hur du bläddrar i flera dagar och visar delar av det arkivet. I den här självstudien kommer du också att kunna se tidsstämplar i videon som visas på skärmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra självstudierna bör du hålla dem till de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien för och tar bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF-sidan produkter](https://www.onvif.org/conformant-products/) genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) och sedan följa instruktionerna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) snabb start för att registrera enheten med Azure IoT Hub.
