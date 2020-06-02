---
title: Identifiera rörelse, spela in video på gräns enheter – Azure
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att analysera Live-videofeeden från en (simulerad) IP-kamera, identifiera om någon rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262082"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Snabb start: identifiera rörelse, spela in video på gräns enheter
 
Den här snabb starten visar hur du använder real tids analys på IoT Edge för att analysera Live-videofeeden från en (simulerad) IP-kamera, identifiera om någon rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten. Den använder en virtuell Azure-dator som en IoT Edge enhet och en simulerad direktuppspelad video ström. Den här artikeln baseras på exempel kod skriven i C#.

Den här artikeln bygger vidare på [den här](detect-motion-emit-events-quickstart.md) snabb starten. 

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) på datorn med följande fil namns tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) är installerat på datorn
* Om du inte har slutfört [den här](detect-motion-emit-events-quickstart.md) snabb starten tidigare slutför du följande steg:
     * [Ställa in Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Ställt in din utvecklingsmiljö](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Skapa och distribuera distributions manifestet för IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Förbereda för övervaknings händelser](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera det.

## <a name="review-the-sample-video"></a>Granska exempel videon
Som en del av stegen ovan för att konfigurera Azure-resurserna kopieras en (kort) video från ett parkerings parti till den virtuella Linux-datorn i Azure som används som IoT Edge enhet. Den här video filen används för att simulera en Live-ström för den här självstudien.

Du kan använda ett program som [VLC Player](https://www.videolan.org/vlc/), starta det, trycka på CTRL + N och klistra in [den här](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) länken till parkerings mediets video för att starta uppspelningen. Vid ungefär 5 sekunders märket flyttas en vit bil genom parkerings partiet.

När du har slutfört stegen nedan har du använt video analys i real tid för IoT Edge för att identifiera rörelsen i bilen och spela in ett videoklipp från och med det 5-andra märket.

## <a name="overview"></a>Översikt

![översikt](./media/quickstarts/overview-qs4.png)

Diagrammet ovan visar hur signal flödet i den här snabb starten. En Edge-modul (detaljerad [här](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simulerar en IP-kamera som är värd för en RTSP-server. En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video bild rutor till noden [motion-identifiering](media-graph-concept.md#motion-detection-processor) . RTSP-källan skickar samma video bild rutor till en [signal grind processor](media-graph-concept.md#signal-gate-processor) , som förblir stängd tills den utlöses av en händelse.

När en rörelse identifierings processor avgör att rörelsen finns i videon, skickar den en händelse till signal porten processor-noden, vilket utlöser den. Porten öppnas under den konfigurerade tiden och skickar video ramar till [filen Sink](media-graph-concept.md#file-sink) -noden. Den här Sink-noden registrerar videon som en MP4-fil i det lokala fil systemet på din Edge-enhet på den konfigurerade platsen.

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet
1. Tolka resultaten
1. Rensa resurser

## <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna
Som en del av för hands kraven skulle du ha laddat ned exempel koden till en mapp. Starta Visual Studio Code och öppna mappen.

1. Bläddra till "src/Edge" i Visual Studio Code. Du kommer att se. kuvert filen som du skapade tillsammans med några mallar för distributions mal len
    * Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen "src/Cloud-to-Device-console-app". Här visas den appSettings. JSON-fil som du skapade tillsammans med några andra filer:
    * C2D-console-app. CSPROJ – det här är projekt filen för Visual Studio Code
    * Operations. JSON – den här filen visar de olika åtgärder som du vill att programmet ska köra
    * Program.cs – detta är exempel program koden, som gör följande:

        * Läser in appinställningar
        * Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md) 
        * Pausar så att du kan granska utdata från programmet i TERMINALFÖNSTRET och de händelser som genererats av modulen i fönstret utdata
        * Anropar direkta metoder för att rensa resurser   

1. Gör följande ändringar i filen Operations. JSON
    * Ändra länken till graf-topologin:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Under GraphInstanceSet redigerar du namnet på diagram sto pol Ogin så att den matchar värdet i länken ovan`"topologyName" : "EVRToFilesOnMotionDetection"`
    * Redigera även RTSP-URL: en för att peka på önskad videofil`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Under GraphTopologyDelete redigerar du namnet`"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Granska-kontrol lera status för modulerna
I steget [generera och distribuera IoT Edge distributions manifestet](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) i Visual Studio Code, om du expanderar noden "lva-Sample-Device" i Azure IoT Hub (i det nedre vänstra avsnittet) bör du se följande moduler distribuerade

    1. Video analys-modulen i real tid, med namnet "lvaEdge"
    1. En modul med namnet "rtspsim" som simulerar en RTSP-server, som fungerar som källa till en Live-videofeed

        ![Moduler](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Granska – förbereda för övervaknings händelser
Kontrol lera att du har slutfört stegen för att [förbereda för övervaknings händelser](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Kör exempel programmet

1. Starta en felsökningssession (tryck på F5). Du kommer att börja se vissa meddelanden som skrivs ut i TERMINALFÖNSTRET.
1. Operations. JSON börjar inte med anrop till Direct-metoderna GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter tidigare snabb starter, returnerar detta tomma listor och pausar sedan för att trycka på RETUR
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
     * Ett anrop till GraphTopologySet med hjälp av topologyUrl ovan
     * Ett anrop till GraphInstanceSet med hjälp av följande text
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. Medie grafen fortsätter att köras och utskrift av resultaten – RTSP-simulatorn kommer att fortsätta att Visa käll videon. För att stoppa medie diagrammet går du tillbaka till TERMINALFÖNSTRET och trycker på "Ange". Nästa serie anrop görs för att rensa resurser:
     * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen
     * Ett anrop till GraphInstanceDelete för att ta bort instansen
     * Ett anrop till GraphTopologyDelete för att ta bort topologin
     * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom

## <a name="interpret-results"></a>Tolka resultaten 
När du kör medie diagrammet skickas resultatet från noden för motion-detektorn via noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i UTDATAFÖNSTRET i Visual Studio Code innehåller avsnittet "Body" och "applicationProperties". Läs [den här](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikeln för att förstå vad dessa avsnitt representerar.

I meddelandena nedan definieras program egenskaperna och innehållet i bröd texten av modulen live video analys.

## <a name="mediasession-established-event"></a>MediaSession-etablerad händelse

När ett medie diagram instansieras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs på behållaren rtspsim-live555. Om det lyckas skrivs den här händelsen ut:

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

* Meddelandet är en diagnostisk händelse, MediaSessionEstablished, anger att noden RTSP-källa (ämnet) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.
* "Subject" i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* "eventType" i applicationProperties anger att det är en diagnostisk händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.


## <a name="recording-started-event"></a>Händelsen inspelning startade

Som förklaras [här](#overview)är noden signal grind processor aktive rad och noden fil mottagare i medie diagrammet börjar skriva en MP4-fil. Filen Sink-noden skickar en drifts händelse. Typen har angetts till "Motion" för att visa att det är ett resultat från processen för identifiering av rörelser och att eventTime talar om vilken tid (UTC) som inträffar. Nedan visas ett exempel:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

* "subject" i applicationProperties refererar noden i det medie diagram som meddelandet genererades från. I det här fallet kommer meddelandet från noden fil mottagare.
* "eventType" i applicationProperties anger att det är en drifts händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade. Observera att den här 5-6 sekunderna efter det att MediaSessionEstablished och videon börjar flöda. Detta motsvarar det 5-6 andra märket när [bilen började flytta](#review-the-sample-video) till parkerings partiet
* "Body" innehåller data om drift händelsen, som i det här fallet är "outputType" och "outputLocation"-data.
* "outputType" anger att den här informationen avser fil Sök vägen
* "outputLocation" tillhandahåller platsen för MP4-filen, inifrån Edge-modulen

## <a name="recording-stopped-and-available-events"></a>Inspelning av stoppade och tillgängliga händelser

Om du undersöker egenskaperna för noden signal grind processor i [graf-topologin](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)ser du att aktiverings tiderna har angetts till 5 sekunder. Så cirka 5 sekunder efter att RecordingStarted-händelsen tas emot, får du en
* RecordingStopped-händelse, som anger att inspelningen har stoppats
* RecordingAvailable-händelse, som anger att MP4-filen nu kan användas för visning

De två händelserna skickas vanligt vis med sekunder av varandra.

### <a name="playing-back-the-mp4-clip"></a>Spela upp MP4-klippet

1. MP4-filerna skrivs till en katalog på gräns enheten som du konfigurerade i. miljö-filen via den här nyckel OUTPUT_VIDEO_FOLDER_ON_DEVICE. Om du lämnar standardvärdet till standardvärdet ska resultaten vara i/Home/lvaadmin/samples/output/
1. Gå till din resurs grupp, hitta den virtuella datorn och Anslut med skydds

    ![Resursgrupp](./media/quickstarts/resource-group.png)
 
    ![Virtuell dator](./media/quickstarts/virtual-machine.png)
1. När du har loggat in (med autentiseringsuppgifter som genererades under [det här](detect-motion-emit-events-quickstart.md#set-up-azure-resources) steget) går du till den relevanta katalogen (standard:/Home/lvaadmin/samples/output) i kommando tolken och ser till att MP4-filerna finns där. Du kan [SCP-filerna](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) över till din lokala dator och spela tillbaka dem via [VLC Player](https://www.videolan.org/vlc/) eller någon annan MP4-spelare.

    ![Utdata](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra snabb starterna ska du hålla på de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp under vilken du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

* Kör snabb starten av [video med din egen](use-your-model-quickstart.md) snabb start för modeller, som visar hur du använder AI för video flöden i real tid.
* Granska ytterligare utmaningar för avancerade användare:

    * Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på ONVIF-sidan [produkter](https://en.wikipedia.org/wiki/IP_camera) genom att söka efter enheter som uppfyller profilerna G, S eller T.
    * Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) och sedan följa instruktionerna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) snabb start för att registrera enheten med Azure IoT Hub.
