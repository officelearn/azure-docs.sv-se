---
title: Event-baserad videoinspelning till molnet och uppspelningen från Cloud-självstudie – Azure
description: I den här självstudien får du lära dig hur du använder video analys i real tid för IoT Edge för att utföra en Event-baserad videoinspelning till molnet och uppspelningen från molnet.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: daab1f06d8950aa7710c7e808ea6362ee3bfd626
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261932"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet

I den här självstudien får du lära dig att använda real tids analys på IoT Edge för att selektivt registrera delar av en video källa i real tid för att Media Services i molnet. Det här användnings fallet kallas EVR ( [Event-based Video Recording](event-based-video-recording-concept.md) ) i den här självstudien. För att åstadkomma detta använder du en AI-modell för objekt identifiering för att söka efter objekt i videon och bara spela in videoklipp när en viss typ av objekt identifieras. Du får också lära dig hur du spelar upp inspelade videoklipp med Media Services. Detta är användbart i en mängd olika scenarier, där det finns behov av att hålla ett arkiv med videoklipp av intresse.

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
* [Händelse-baserad videoinspelning](event-based-video-recording-concept.md)
<!--* [Quickstart: Event-based recording based on motion events]()-->
* [Självstudie: utveckla en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Redigera Deployment. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Avsnitt om [hur du deklarerar vägar i IoT Edge distributions manifest](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Krav

Krav för den här självstudien är följande

* Installera [Docker](https://docs.docker.com/desktop/) på utvecklings datorn
* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -tillägget och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tillägget.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Du kan ignorera det här meddelandet.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Slutför [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) och [Konfigurera miljön](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

I slutet av stegen ovan kommer du att ha vissa Azure-resurser distribuerade i Azure-prenumerationen, inklusive:

* IoT Hub
* Lagringskonto
* Media Services-konto
* En virtuell Linux-dator

## <a name="concepts"></a>Begrepp

![Medie diagram](./media/event-based-video-recording-tutorial/overview.png)

Event-based Video Recording (EVR) syftar på att spela in videon som utlöses av en händelse. Händelsen i fråga kunde härstamma på grund av bearbetning av själva video signalen (till exempel vid identifiering av ett rörligt objekt i videon) eller kan komma från en oberoende källa (till exempel öppning av en dörr). Alternativt kan du bara utlösa registrering när en extern inferencing-tjänst identifierar att en speciell händelse har inträffat.  I den här självstudien kommer du att använda en video om fordon som flyttas på en motor och spela in videoklipp när en Last bil upptäcks.

Diagrammet ovan är en bild representation av ett [medie diagram](media-graph-concept.md) och ytterligare moduler som utför det önskade scenariot. Fyra IoT Edge-moduler ingår:

* Video analys i real tid för IoT Edge modul
* En AI-modul som skapats med [Yolo v3-modellen](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)
* En anpassad modul för att räkna och filtrera objekt (kallas objekt räknare i diagrammet ovan) som du kommer att bygga och distribuera i den här självstudien
* En [RTSP Simulator-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) för att simulera en RTSP-kamera
    
När diagrammet visas använder du en [RTSP-källmapp](media-graph-concept.md#rtsp-source) i medie grafen för att avbilda direktsänd video och skicka videon till två sökvägar.

* Den första sökvägen är till en [filter processor](media-graph-concept.md#frame-rate-filter-processor) för bildskärms hastighet som visar video bild rutor med den angivna bild frekvensen. De här video bild rutorna är Sever som inmatade i en HTTP-tilläggsprovider. Noden HTTP-tillägg skickar ramar (som bilder) till AI-modulen (YOLO v3 – som är en objekt detektor) och tar emot resultat – som kommer att vara de objekt som identifieras av modellen. Noden HTTP-tillägg publicerar sedan resultaten via IoT Hub meddelande mottagare till IoT Edge Hub
* Modulen för objekt räknare har kon figurer ATS för att ta emot meddelanden från IoT Edge Hub – bland annat objekt identifierings resultat (fordon i trafik). Den kontrollerar meddelanden som söker efter objekt av en viss typ (konfigurerade via en delad egenskap) och matar ut ett meddelande till IoT Edge Hub. Dessa meddelanden dirigeras sedan tillbaka till IoT Hub source-noden i medie diagrammet. När IoT Hub du tar emot ett meddelande, utlöses noden för [signal grind](media-graph-concept.md#signal-gate-processor) i media grafen för att öppna porten under en konfigurerad tids period. Video flödar genom porten till till gångs mottagarens nod under den varaktigheten. Den delen av Live Stream-objektet registreras sedan via noden [till gångs mottagare](media-graph-concept.md#asset-sink) till en [till gång](terminology.md#asset) i ditt Azure Media Service-konto.

## <a name="set-up-the-environment"></a>Konfigurera miljön

1. Klona lagrings platsen härifrån https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
2. Starta Visual Studio Code (VSCode) och öppna mappen där lagrings platsen laddas ned till.
3. I VSCode bläddrar du till mappen "src/Cloud-to-Device-console-app" och skapar en fil med namnet "appSettings. JSON". Den här filen innehåller de inställningar som krävs för att köra programmet.
3. Kopiera innehållet från clouddrive/lva-Sample/appSettings. JSON-filen när du har kört konfigurations skriptet för resursen. Se [nödvändiga #4](event-based-video-recording-tutorial.md#prerequisites). När resurs installations skriptet har slutförts klickar du på klammerparenteserna för att exponera mappstrukturen. Tre filer som skapas under clouddrive/lva-Sample visas. Av intresse för närvarande är. kuvert-filerna och AppSetting. JSON. Du behöver dessa för att uppdatera filerna i Visual Studio Code senare i snabb starten. Du kanske vill kopiera dem till en lokal fil för tillfället.

    ![Appinställningar](./media/quickstarts/clouddrive.png)

    Texten från clouddrive/lva-Sample/appSettings. JSON-filen bör se ut så här:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Bläddra sedan till mappen "src/Edge" och skapa en fil med namnet ". kuvert".
1. Kopiera innehållet från clouddrive/lva-Sample/. kuvert-filen. Texten bör se ut så här:

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
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```
## <a name="examine-the-template-file"></a>Granska mallfilen 

Under installationen av miljön har du lanserat Visual Studio Code och öppnat mappen som innehåller exempel koden.

Bläddra till "src/Edge" i Visual Studio Code. Du kommer att se den. kuvert-fil som du har skapat, samt några filer för distributions mal len. Dessa mallar definierar vilka Edge-moduler som ska distribueras till den virtuella Linux-datorn. . Kuvert-filen innehåller värdena för variablerna som används i dessa mallar, till exempel den IoT Hub anslutnings sträng som gör att du kan skicka kommandon till Edge-modulerna via Azure IoT Hub.

Öppna "src/Edge/Deployment. objectCounter. template. JSON". Observera att det finns fyra poster under avsnittet "moduler" – som motsvarar de objekt som anges ovan (i avsnittet begrepp):

* lvaEdge – det här är den direktsända video analysen i IoT Edge modul
* yolov3 – detta är den härlednings tjänst som skapats med YOLO v3-modellen
* rtspsim – detta är RTSP-simulatorn
* objectCounter – det här är den modul som söker efter vissa objekt i meddelanden som returneras av yolov3

För modulen objectCounter, se den sträng som används för "bild"-värdet – detta baseras på självstudien om att utveckla en IoT Edge-modul. Visual Studio Code kommer automatiskt att identifiera att koden för modulen för objekt räknare är under "src/Edge/modules/objectCounter". Läs avsnittet om hur du deklarerar vägar i IoT Edge distributions manifest och sedan undersöker vägarna i mall-JSON-filen. Observera hur:

* LVAToObjectCounter används för att skicka vissa händelser till en angiven slut punkt i modulen objectCounter
* ObjectCounterToLVA används för att skicka en Utlös ande händelse till en speciell slut punkt (som ska vara noden IoT Hub källa) i lvaEdge-modulen
* objectCounterToIoTHub används som ett fel söknings verktyg – för att hjälpa dig att se resultatet från objectCounter när du kör den här självstudien

> [!NOTE]
> De önskade egenskaperna för objectCounter-modulen – den har kon figurer ATS för att leta efter objekt som är märkta som "Truck", med en konfidensnivå på minst 50%.

## <a name="deploy-the-edge-modules"></a>Distribuera Edge-modulerna

Med hjälp av Visual Studio Code följer du anvisningarna för att logga in i Docker och "Build and push IoT Edge Solution", men Använd src/Edge/Deployment. objectCounter. template. JSON för det här steget.

![Lösning för att bygga och push IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Detta skapar objectCounter-modulen för objekt inventering och push-överför avbildningen till din Azure Container Registry (ACR)

* Kontrol lera att du har miljövariablerna CONTAINER_REGISTRY_USERNAME_myacr och CONTAINER_REGISTRY_PASSWORD_myacr som definierats i. kuvert-filen

I ovanstående steg skapas IoT Edge distributions manifestet i src/Edge/config/Deployment. objectCounter. amd64. JSON.

I Visual Studio Code bläddrar du till src/Edge/config/Deployment. objectCounter. amd64. JSON, högerklickar på filen och väljer "skapa distribution för enskild enhet". 

Om det här är din första själv studie kurs med real tids analys på IoT Edge, uppmanas du att ange anslutnings strängen för IoTHub i Visual Studio Code. Du kan kopiera den från filen appSettings. JSON.

Sedan ber Visual Studio Code dig att välja en IoT Hub-enhet. Välj din IoT Edge-enhet (bör vara "lva-Sample-Device").

I det här skedet har distributionen av Edge-moduler till din IoT Edges enhet startats.
I cirka 30 sekunder uppdaterar du Azure IOT Hub i det nedre vänstra avsnittet i Visual Studio Code, och du bör se att det finns 4 moduler distribuerade (Observera igen namn: lvaEdge, rtspsim, yolov3 och objectCounter)

![4 moduler har distribuerats](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Högerklicka på gräns enheten ("lva-Sample-Device") och klicka på "starta övervakning av den inbyggda händelse slut punkten". Live video analys i IoT Edge-modulen genererar [operativa](#operational-events) och [diagnostiska](#diagnostic-events) händelser till IoT Edge Hub och du kan se dessa händelser i fönstret utdata i Visual Studio Code.

## <a name="run-the-program"></a>Köra programmet

1. Visual Studio Code, navigera till "src/Cloud-to-Device-console-app/Operations. JSON"
1. Under noden GraphTopologySet anger du följande: "topologyUrl": " https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json " 
1. Sedan, under noden GraphInstanceSet, redigerar du "topologyName": "EVRtoAssetsOnObjDetect"
1. Tryck på F5. Detta startar felsökningssessionen.
1. I TERMINALFÖNSTRET visas svaren på de [direkta metod](direct-methods.md) anropen som görs av programmet till video analys i IoT Edge modul, som är:

    1. GraphTopologyList – hämtar en lista över diagram-topologier som har lagts till i modulen, om några

        Tryck på RETUR för att fortsätta
    1. GraphInstanceList – hämtar en lista med diagram instanser som har skapats, om det finns några

        Tryck på RETUR för att fortsätta
    1. GraphTopologySet – lägger till ovanstående topologi med namnet "EVRtoAssetsOnObjDetect" i modulen
    1. GraphInstanceSet – skapar en instans av topologin ovan och ersätter parametrar
    1. Av intresse är rtspUrl-parametern. Den pekar på den MKV-fil som har laddats ned till den virtuella Linux-datorn, till en plats där RTSP-simulatorn läser den
    1. GraphInstanceActivate – startar medie grafen, vilket gör att video flödar genom
    1. GraphInstanceList – visar att du nu har en instans i modulen som kör

        I det här läget bör du pausa och *inte* trycka på RETUR
1. I fönstret utdata ser du operativa och diagnostiska meddelanden som skickas till IoT Hub, av live video analys i IoT Edge modul
1. Medie grafen fortsätter att köras och utskrift av händelser – RTSP-simulatorn kommer att fortsätta att upprepa käll videon. För att stoppa medie diagrammet kan du trycka på RETUR igen i TERMINALFÖNSTRET. Programmet skickas:

    1. GraphInstanceDeactivate – för att stoppa graf-instansen och stoppa video inspelningen
    1. GraphInstanceDelete – för att ta bort instansen från modulen
    1. GraphInstanceList – visar att det nu inte finns några instanser i modulen

> [!NOTE]
> Graf sto pol Ogin har inte tagits bort. Om du behöver göra det kör du det här steget med följande JSON-text:

```
{
    "@apiVersion" : "1.0",
    "name" : "EVRtoAssetsOnObjDetect"
}
```

## <a name="examine-the-output"></a>Granska utdatan
 
Live video analys i IoT Edge-modulen ger [drift](#operational-events) -och [diagnostiska](#diagnostic-events) händelser till IoT Edge Hub, som är den text som du ser i utdatafönstret i Visual Studio Code, efter den strömmande meddelande formatet som upprättats för kommunikation från enhet till moln genom att IoT Hub:

* En uppsättning program egenskaper. En ord lista med sträng egenskaper som ett program kan definiera och komma åt, utan att behöva deserialisera meddelande texten. IoT Hub ändrar aldrig dessa egenskaper
* En täckande binär brödtext

I meddelandena nedan definieras program egenskaperna och innehållet i bröd texten av den direktuppspelade video analysen i IoT Edge modul. Mer information finns i [övervakning och loggning](monitoring-logging.md). 

## <a name="diagnostic-events"></a>Diagnostiska händelser

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse 

När ett medie diagram instansieras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs på RTSP Simulator-behållaren. Om det lyckas skrivs händelsen ut. Observera att händelse typen är Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [2:02:54 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589749373980489 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T21:02:53.981Z",
    "dataVersion": "1.0"
  }
}
```

. Tänk på följande:

* "Subject" i applicationProperties refererar till noden i den MediaGraph som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* "eventType" i applicationProperties anger att det här är en diagnostisk händelse
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om Diagnostic-händelsen – det är SDP-meddelandet

Skriv ned eventTime – det här är den tid som trafik videon (MKV-filen) startade för att komma in i modulen som en Live-dataström.

## <a name="operational-events"></a>Drift händelser

När medie grafen har körts en gång, kommer du att få en händelse från modulen objekt räknare. 

```
[IoTHubMonitor] [2:03:21 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T21:03:21.062Z"
  }
}
```

ApplicationProperties innehåller bara eventTime, som är den tid då modulen observerade att resultaten från YOLO v3-modulen innehöll objekt av intresse (Truck).

Du kan se att fler av de här händelserna visas som andra Last bilar identifieras i videon.

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

Nästan omedelbart efter att objekt räknaren har skickat händelsen visas en händelse av typen Microsoft. Media. Graph. Operational. RecordingStarted

```
[IoTHubMonitor] [2:03:22 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T21:03:22.532Z",
    "dataVersion": "1.0"
  }
}
```

"Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i. Anteckna det här värdet.

### <a name="recordingavailable-event"></a>RecordingAvailable-händelse

När noden till gångs mottagare har laddat upp video till till gången, genererar den här händelsen av typen Microsoft. Media. Graph. Operational. RecordingAvailable

```
[IoTHubMonitor] [2:03:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T21:03:31.808Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen anger att tillräckligt med data har skrivits till till gången för att spelarna/klienter ska kunna starta uppspelningen av videon.

"Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

### <a name="recordingstopped-event"></a>RecordingStopped-händelse

Om du granskar aktiverings inställningarna (maximumActivationTime) för noden signal grind processor i topologin, ser du att porten är konfigurerad för att avslutas efter att 30 sekunders video har passerat. Så ungefär 30 sekunder efter RecordingStarted-händelsen bör du se en händelse av typen Microsoft. Media. Graph. Operational. RecordingStopped, som anger att noden till gångs mottagare har stoppat inspelningen av video till till gången.

```
[IoTHubMonitor] [2:03:52 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T21:03:52.040Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen indikerar att inspelningen har stoppats.

"Subject" i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

## <a name="media-services-asset"></a>Media Services till gång  

Du kan granska Media Services till gången som skapades av grafen genom att logga in på Azure Portal och visa videon.

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.
1. Leta upp ditt Media Services-konto bland de resurser som du har i din prenumeration och öppna bladet konto
1. Klicka på till gångar i Media Services listan

    ![Tillgångar](./media/continuous-video-recording-tutorial/assets.png)
1. Du hittar en till gång i listan med namnet sampleAssetFromCVR-LVAEdge-{DateTime} – det här är namngivnings mönstret som väljs i filen med medie diagram sto pol Ogin.
1. Klicka på till gången.
1. På sidan till gångs information klickar du på text rutan **Skapa ny** under strömnings-URL.

    ![Ny till gång](./media/continuous-video-recording-tutorial/new-asset.png)

1. I guiden som öppnas accepterar du standard alternativen och klickar på Lägg till. Mer information finns i [video uppspelning](video-playback-concept.md).

    > [!TIP]
    > Kontrol lera att [slut punkten för direkt uppspelning körs](../latest/streaming-endpoint-concept.md).
1. Videon ska läsas in i spelaren och du bör kunna trycka på **uppspelnings**> * * för att visa den.

> [!NOTE]
> Eftersom källan till videon var en behållare som simulerar ett kamera flöde, är tidsstämplar i videon relaterade till när du aktiverade graf-instansen och när du inaktiverar den. Mer information finns i [spela upp flera dagars inspelningar](playback-multi-day-recordings-tutorial.md) på hur du bläddrar i flera dagar och visar delar av det arkivet. I den här självstudien kan du också se tidsstämplar i videon som visas på skärmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra självstudierna bör du hålla dem till de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien för och tar bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF-sidan produkter](https://www.onvif.org/conformant-products/) genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) och sedan följa instruktionerna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) snabb start för att registrera enheten med Azure IoT Hub.
