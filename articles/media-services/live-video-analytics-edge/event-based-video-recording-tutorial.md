---
title: Event-baserad videoinspelning till molnet och uppspelningen från Cloud-självstudie – Azure
description: I den här självstudien får du lära dig hur du använder video analys i real tid för IoT Edge för att utföra en Event-baserad videoinspelning till molnet och uppspelningen från molnet.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7ff8502688e2b507b8a576c177948f29c2248be4
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456680"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet

I den här självstudien får du lära dig att använda real tids analys på IoT Edge för att selektivt registrera delar av en video källa i real tid för att Media Services i molnet. Det här användnings fallet kallas EVR ( [Event-based Video Recording](event-based-video-recording-concept.md) ) i den här självstudien. För att åstadkomma detta använder du en AI-modell för objekt identifiering för att söka efter objekt i videon och bara spela in videoklipp när en viss typ av objekt identifieras. Du får också lära dig hur du spelar upp inspelade videoklipp med Media Services. Detta är användbart för en mängd olika scenarier där det finns behov av att hålla ett arkiv med videoklipp av intresse.

> [!div class="checklist"]
> * Konfigurera relevanta resurser
> * Granska koden som utför EVR
> * Kör exempel koden
> * Granska resultaten och visa videon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Vi rekommenderar att du läser igenom följande dokumentations sidor

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md) 
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Självstudie: utveckla en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Redigera Deployment. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Avsnitt om [hur du deklarerar vägar i IoT Edge distributions manifest](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Krav

Krav för den här självstudien är följande

* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -tillägget och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tillägget.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Du kan ignorera det här meddelandet.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Slutför [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) och [Konfigurera miljön](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

I slutet av stegen ovan kommer du att ha vissa Azure-resurser distribuerade i Azure-prenumerationen, inklusive:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installerat

## <a name="concepts"></a>Begrepp

Event-based Video Recording (EVR) syftar på att spela in videon som utlöses av en händelse. Händelsen kan genereras från bearbetning av själva video signalen (till exempel vid identifiering av ett rörligt objekt i videon) eller från en oberoende källa (till exempel öppning av en dörr). Alternativt kan du bara utlösa registrering när en inferencing-tjänst identifierar att en speciell händelse har inträffat.  I den här självstudien kommer du att använda en video om fordon som flyttas på en motor och spela in videoklipp när en Last bil upptäcks.

![Mediegraf](./media/event-based-video-recording-tutorial/overview.png)

Diagrammet ovan är en bild representation av ett [medie diagram](media-graph-concept.md) och ytterligare moduler som utför det önskade scenariot. Fyra IoT Edge-moduler ingår:

* Video analys i real tid i IoT Edge modul.
* En Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. Den här AI-modulen använder [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt.
* En anpassad modul för att räkna och filtrera objekt (kallas objekt räknare i diagrammet ovan) som du kommer att bygga och distribuera i den här självstudien.
* En [RTSP Simulator-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) för att simulera en RTSP-kamera.
    
    När diagrammet visas använder du en [RTSP-källmapp](media-graph-concept.md#rtsp-source) i medie grafen för att avbilda den simulerade Live-videon (trafik på en väg) och skicka videon till två sökvägar.

* Den första sökvägen är till en [filter processor](media-graph-concept.md#frame-rate-filter-processor) för bildskärms hastighet som visar video bild rutor med den angivna (reducerade) bild frekvensen. Dessa video bild rutor skickas till en HTTP-tilläggsprovider, som sedan vidarebefordrar ramar (som bilder) till AI-modulen (YOLO v3 – som är en objekt detektor) och tar emot resultat – som kommer att vara de objekt (fordon i trafik) som identifieras av modellen. Noden HTTP-tillägg publicerar sedan resultaten via noden IoT Hub meddelande mottagare till IoT Edge Hub.
* Modulen för objekt räknare har kon figurer ATS för att ta emot meddelanden från IoT Edge Hub – bland annat objekt identifierings resultat (fordon i trafik). Dessa meddelanden söker efter objekt av en viss typ (konfigurerade via en inställning). När ett sådant objekt hittas skickar den här modulen ett meddelande till IoT Edge Hub. De "objekt som hittas"-Meddelanden dirigeras sedan till noden IoT Hub källa i medie diagrammet. När du tar emot ett sådant meddelande, utlöser noden för [signal grind](media-graph-concept.md#signal-gate-processor) av noden IoT Hub källa, vilket gör att den senare kan öppnas under en konfigurerad tids period. Video flödar genom porten till till gångs mottagarens nod under den varaktigheten. Den delen av Live Stream registreras sedan via noden [till gångs mottagare](media-graph-concept.md#asset-sink) till en [till gång](terminology.md#asset) i ditt Azure Media Service-konto.

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Innan du börjar kontrollerar du att du har slutfört den tredje punkten i [kraven](#prerequisites). När resurs installations skriptet har slutförts klickar du på klammerparenteserna för att exponera mappstrukturen. Du kommer att se några filer som skapats under katalogen ~/clouddrive/lva-Sample.

![Appinställningar](./media/quickstarts/clouddrive.png)

Av intresse i den här självstudien är:

* ~/clouddrive/lva-Sample/Edge-Deployment/.env-innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
* ~/clouddrive/lva-Sample/AppSetting.JSON – används av Visual Studio Code för att köra exempel koden.

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

## <a name="examine-the-template-file"></a>Granska mallfilen 

I föregående steg har du lanserat Visual Studio Code och öppnat mappen som innehåller exempel koden.

Bläddra till "src/Edge" i Visual Studio Code. Du kommer att se den. kuvert-fil som du har skapat, samt några filer för distributions mal len. Den här mallen definierar vilka Edge-moduler som ska distribueras till gräns enheten (den virtuella Azure Linux-datorn). . Kuvert-filen innehåller värden för variablerna som används i dessa mallar, till exempel medie tjänstens autentiseringsuppgifter.

Öppna "src/Edge/Deployment. objectCounter. template. JSON". Observera att det finns fyra poster under avsnittet "moduler" – som motsvarar de objekt som anges ovan (i avsnittet begrepp):

* lvaEdge – det här är den direktsända video analysen i IoT Edge modul
* yolov3 – det här är AI-modulen som skapats med YOLO v3-modellen
* rtspsim – detta är RTSP-simulatorn
* objectCounter – det här är den modul som söker efter vissa objekt i resultatet från yolov3

För modulen objectCounter, se strängen ($ {MODULES. objectCounter}) som används för "bild"-värdet – detta baseras på [självstudien](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) om att utveckla en IoT Edge-modul. Visual Studio Code kommer automatiskt att identifiera att koden för modulen för objekt räknare är under "src/Edge/modules/objectCounter". 

Läs [det här](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) avsnittet om hur du deklarerar vägar i IoT Edge distributions manifestet och sedan undersöker vägarna i mall-JSON-filen. Observera hur:

* LVAToObjectCounter används för att skicka vissa händelser till en angiven slut punkt i objectCounter-modulen.
* ObjectCounterToLVA används för att skicka en Utlös ande händelse till en speciell slut punkt (som bör vara noden IoT Hub källa) i lvaEdge-modulen.
* objectCounterToIoTHub används som ett fel söknings verktyg för att hjälpa dig att se resultatet från objectCounter när du kör den här självstudien.

> [!NOTE]
> Kontrol lera de önskade egenskaperna för objectCounter-modulen, som har ställts in för att leta efter objekt som är märkta som "Truck", med en konfidensnivå på minst 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge 

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningar för dessa moduler. Följ de här stegen för att generera ett sådant manifest från mallfilen och distribuera det sedan till gräns enheten.

Med hjälp av Visual Studio Code följer du [dessa](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) instruktioner för att logga in i Docker och "Build and push IoT Edge Solution", men Använd src/Edge/Deployment. objectCounter. template. JSON för det här steget.

![Lösning för att bygga och push IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Detta skapar objectCounter-modulen för objekt inventering och push-överför avbildningen till din Azure Container Registry (ACR).

* Kontrol lera att du har miljövariabler CONTAINER_REGISTRY_USERNAME_myacr och CONTAINER_REGISTRY_PASSWORD_myacr som definierats i. miljö-filen.

I ovanstående steg skapas IoT Edge distributions manifestet i src/Edge/config/Deployment. objectCounter. amd64. JSON. Högerklicka på filen och klicka på "skapa distribution för enskild enhet".

![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)

Om det här är din första själv studie kurs med real tids analys på IoT Edge, uppmanas du att ange anslutnings strängen för IoTHub i Visual Studio Code. Du kan kopiera den från filen appSettings. JSON.

Sedan ber Visual Studio Code dig att välja en IoT Hub-enhet. Välj din IoT Edge-enhet (bör vara "lva-Sample-Device").

I det här skedet har distributionen av Edge-moduler till din IoT Edges enhet startats.
Om du är 30 sekunder uppdaterar du Azure-IoT Hub i det nedre vänstra avsnittet i Visual Studio Code, och du bör se att det finns 4 moduler distribuerade (Observera igen namnen: lvaEdge, rtspsim, yolov3 och objectCounter).

![4 moduler har distribuerats](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Följ dessa steg om du vill se händelserna från modulen objekt räknare och från video analys på IoT Edge modul:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub längst ned till vänster.
1. Expandera noden enheter.
1. Right-klinker på lva-Sample-Device och väljer alternativet **starta övervakning av inbyggd händelse övervakning**.

![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Köra programmet

1. Visual Studio Code, navigera till "src/Cloud-to-Device-console-app/Operations. JSON"

1. Under noden GraphTopologySet redigerar du följande:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Sedan går du till noderna GraphInstanceSet och GraphTopologyDelete, redigerar,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
    1. När du trycker på "retur" i TERMINALFÖNSTRET görs nästa uppsättning direkta metod anrop.
     * Ett anrop till GraphTopologySet med hjälp av topologyUrl ovan.
     * Ett anrop till GraphInstanceSet med hjälp av följande text.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. Utdata i TERMINALFÖNSTRET pausas nu vid frågan tryck på RETUR för att fortsätta. Tryck inte på RETUR för tillfället. Du kan bläddra upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.

1. Om du nu växlar till UTDATAFÖNSTRET i Visual Studio Code, visas meddelanden som skickas till IoT Hub, av live video analys i IoT Edge-modulen.

     * Dessa meddelanden beskrivs i följande avsnitt.
     
1. Graf-instansen kommer fortsätta att köras och spela in videon – RTSP-simulatorn kommer att fortsätta att spela upp käll videon. Granska meddelandena enligt beskrivningen i avsnittet nedan och stoppa sedan instansen genom att gå tillbaka till TERMINALFÖNSTRET och trycka på "Ange". Nästa serie anrop görs för att rensa resurser:

     * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen
     * Ett anrop till GraphInstanceDelete för att ta bort instansen
     * Ett anrop till GraphTopologyDelete för att ta bort topologin
     * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom

## <a name="interpret-the-results"></a>Tolka resultaten 

När du kör medie diagrammet skickar live video analys i IoT Edge-modulen vissa diagnostik-och drift händelser till IoT Edge Hub. Dessa händelser är de meddelanden som visas i UTDATAFÖNSTRET i Visual Studio Code, som innehåller avsnittet "Body" och "applicationProperties". Information om vad dessa avsnitt representerar finns i [skapa och läsa IoT Hub meddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

I meddelandena nedan definieras program egenskaperna och innehållet i bröd texten av modulen live video analys.

## <a name="diagnostic-events"></a>Diagnostiska händelser

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse 

När ett medie diagram instansieras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs på RTSP Simulator-behållaren. Om det lyckas skrivs händelsen ut. Observera att händelse typen är Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Meddelandet är en diagnostisk händelse, MediaSessionEstablished, anger att noden RTSP-källa (ämnet) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.

* "Subject" i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.

* "eventType" i applicationProperties anger att det är en diagnostisk händelse.

* "eventTime" anger den tidpunkt då händelsen inträffade, vilket är den tid som trafik videon (MKV-filen) startade för att komma in i modulen som en Live-dataström.

* "Body" innehåller data om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.


## <a name="operational-events"></a>Drift händelser

När medie grafen har körts en gång, kommer du att få en händelse från modulen objekt räknare. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

ApplicationProperties innehåller eventTime, som är den tid då modulen för objekt räknare observerade att resultaten från YOLO v3-modulen innehöll objekt av intresse (Truck).

Du kan se att fler av de här händelserna visas som andra Last bilar identifieras i videon.

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

Nästan omedelbart efter att objekt räknaren har skickat händelsen visas en händelse av typen Microsoft. Media. Graph. Operational. RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

"Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet. Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i. Anteckna det här värdet.

### <a name="recordingavailable-event"></a>RecordingAvailable-händelse

När noden till gångs mottagare har laddat upp video till till gången, genererar den här händelsen av typen Microsoft. Media. Graph. Operational. RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen anger att tillräckligt med data har skrivits till till gången för att spelarna/klienter ska kunna starta uppspelningen av videon. "Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet. Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

### <a name="recordingstopped-event"></a>RecordingStopped-händelse

Om du granskar aktiverings inställningarna (maximumActivationTime) för noden signal grind processor i [topologin](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), ser du att gaten är konfigurerad för att avslutas efter att 30 sekunders video har skickats. Så ungefär 30 sekunder efter RecordingStarted-händelsen bör du se en händelse av typen Microsoft. Media. Graph. Operational. RecordingStopped, som anger att noden till gångs mottagare har stoppat inspelningen av video till till gången.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen indikerar att inspelningen har stoppats. "Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet. Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

## <a name="media-services-asset"></a>Media Services till gång  

Du kan granska Media Services till gången som skapades av grafen genom att logga in på Azure Portal och visa videon.

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.
1. Leta upp ditt Media Services-konto bland de resurser som du har i din prenumeration och öppna bladet konto
1. Klicka på till gångar i Media Services listan.

    ![Tillgångar](./media/continuous-video-recording-tutorial/assets.png)
1. Du hittar en till gång i listan med namnet sampleAssetFromEVR-LVAEdge-{DateTime} – det här är det namn som anges i egenskapen outputLocation för händelsen RecordingStarted. AssetNamePattern i topologin avgör hur det här namnet genereras.
1. Klicka på till gången.
1. På sidan till gångs information klickar du på text rutan **Skapa ny** under strömnings-URL.

    ![Ny till gång](./media/continuous-video-recording-tutorial/new-asset.png)

1. I guiden som öppnas accepterar du standard alternativen och klickar på Lägg till. Mer information finns i [video uppspelning](video-playback-concept.md).

    > [!TIP]
    > Kontrol lera att [slut punkten för direkt uppspelning körs](../latest/streaming-endpoint-concept.md).
1. Videon ska läsas in i spelaren och du bör kunna trycka på **spela** för att visa den.

> [!NOTE]
> Eftersom källan till videon var en behållare som simulerar ett kamera flöde, är tidsstämplar i videon relaterade till när du aktiverade graf-instansen och när du inaktiverar den. Om du använder uppspelnings kontrollerna som är inbyggda i [uppspelnings kursen för flera dagar i uppspelningen](playback-multi-day-recordings-tutorial.md) kan du se tidsstämplar i videon som visas på skärmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra självstudierna bör du hålla dem till de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien för och tar bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF-sidan produkter](https://www.onvif.org/conformant-products/) genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) och sedan följa instruktionerna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) snabb start för att registrera enheten med Azure IoT Hub.
