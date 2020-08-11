---
title: Identifiera rörelse & spela in video på gräns enheter – Azure
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att analysera Live-videofeeden från en (simulerad) IP-kamera, identifiera om någon rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 796def7cad3632dd50184bea751dc9f348569216
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067749"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Snabb start: identifiera rörelse-och inspelnings video på gräns enheter
 
Den här snabb starten visar hur du använder video analys i real tid för IoT Edge för att analysera direktsända video flöden från en (simulerad) IP-kamera. Det visar hur du kan identifiera om en rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten. Snabb starten använder en virtuell Azure-dator som en IoT Edge-enhet och använder också en simulerad direktuppspelad video ström. 

Den här artikeln baseras på exempel kod skriven i C#. Det bygger på snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
* [Visual Studio Code](https://code.visualstudio.com/)med följande tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Om du inte har slutfört snabb starten [identifiera rörelse-och genererar händelser](detect-motion-emit-events-quickstart.md) följer du dessa steg:
     1. [Ställa in Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Ställt in din utvecklingsmiljö](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Skapa och distribuera distributions manifestet för IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Förbereda övervakning av händelser](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera prompten.

## <a name="review-the-sample-video"></a>Granska exempel videon
När du konfigurerar Azure-resurser för den här snabb starten kopieras en kort video om ett parkerings parti till den virtuella Linux-datorn i Azure som används som IoT Edge enhet. Den här video filen används för att simulera en Live-ström för den här självstudien.

Öppna ett program som [VLC Media Player](https://www.videolan.org/vlc/), Välj CTRL + N och klistra in [den här länken](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) till parkerings sidans video för att starta uppspelningen. Vid ungefär 5 sekunders märket flyttas en vit bil genom parkerings partiet.

Utför följande steg för att använda video analys i real tid för IoT Edge för att identifiera rörelsen i bilen och spela in ett videoklipp som börjar runt det 5-andra märket.

## <a name="overview"></a>Översikt

![översikt](./media/quickstarts/overview-qs4.png)

Föregående diagram visar hur signal flödet i den här snabb starten. [En Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video bild rutor till noden för [avkänning av rörelser](media-graph-concept.md#motion-detection-processor) . RTSP-källan skickar samma video bild rutor till en [signal grind processor](media-graph-concept.md#signal-gate-processor) , som förblir stängd tills den utlöses av en händelse.

När processen för rörelse identifiering identifierar rörelse i videon, skickar den en händelse till signal porten processor, vilket utlöser den. Porten öppnas under den konfigurerade tiden och skickar video ramar till [filen Sink](media-graph-concept.md#file-sink) -noden. Den här Sink-noden registrerar videon som en MP4-fil på din gräns enhets lokala fil system. Filen sparas på den konfigurerade platsen.

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.

## <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna
Som en del av kraven för den här snabb starten laddade du ned exempel koden till en mapp. Följ dessa steg om du vill undersöka och redigera exempel koden.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten, där variabler används för vissa egenskaper. *. Miljö* filen innehåller värdena för variablerna.
1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:
    * ***C2D-console-app. CSPROJ*** – projekt filen för Visual Studio Code.
    * ***operations.jsi*** listan över åtgärder som du vill att programmet ska köra.
    * ***Program.cs*** – exempel koden. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md). 
        * Pausar så att du kan granska utdata från programmet i **terminalfönstret** och granska de händelser som genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.

1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Redigera RTSP-URL: en för att peka på video filen:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * Under `GraphTopologyDelete` redigerar du namnet:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Granska-kontrol lera modulernas status

I steget [generera och distribuera IoT Edge distributions manifestet](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) i Visual Studio Code, expanderar du noden **lva-Sample-Device** i **Azure IoT Hub** (i det nedre vänstra avsnittet). Du bör se följande moduler distribuerade:

* Modulen live video analys med namnet`lvaEdge`
* `rtspsim`Modulen, som simulerar en RTSP-server som fungerar som källa för en Live-videofeed

  ![Moduler](./media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Om du använder en egen Edge-enhet i stället för den som har skapats av vårt installations skript, går du till din Edge-enhet och kör följande kommandon med **administratörs behörighet**för att hämta och lagra exempel video filen som används för den här snabb starten:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

## <a name="review---prepare-for-monitoring-events"></a>Granska – förbereda för övervaknings händelser
Se till att du har slutfört stegen för [att förbereda övervakningen av händelser](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Kör exempel programmet

1. Starta en felsökningssession genom att välja F5-nyckeln. **Terminalfönstret** skriver ut vissa meddelanden.
1. *operations.js* koden anropar de direkta metoderna `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter tidigare snabb starter kommer den här processen att returnera tomma listor och sedan pausa. Välj Retur.

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
  * Ett anrop till `GraphTopologySet` som använder`topologyUrl` 
  * Ett anrop till `GraphInstanceSet` som använder följande text:

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

  * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och video flödet
  * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd  

3. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder som du har anropat.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.

1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurserna:
     * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
     * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
     * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
     * Ett sista anrop till `GraphTopologyList` visar att listan nu är tom.

## <a name="interpret-results"></a>Tolka resultaten 
När du kör medie diagrammet passerar resultatet från noden för rörelse detektorn genom IoT Hub Sink-noden till IoT Hub. De meddelanden som visas i fönstret **utdata** i Visual Studio Code innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut.

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

I föregående utdata: 

* Meddelandet är en Diagnostics-händelse `MediaSessionEstablished` . Det anger att noden RTSP-källa (ämnet) upprättade en anslutning till RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` refererar den nod i den graf-topologi från vilken meddelandet genererades. I det här fallet kommer meddelandet från noden RTSP-källa.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

När motion identifieras aktive ras noden signal grind processor och noden fil mottagare i medie diagrammet börjar skriva en MP4-fil. Filen Sink-noden skickar en drifts händelse. `type`Anges till `motion` för att indikera att det är ett resultat från processen för identifiering av rörelser. `eventTime`Värdet är den UTC-tid då rörelsen ägde rum. Mer information om den här processen finns i avsnittet [Översikt](#overview) i den här snabb starten.

Här är ett exempel på det här meddelandet:

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

I föregående meddelande: 

* I `applicationProperties` `subject` refererar den nod i medie diagrammet som meddelandet genererades från. I det här fallet kommer meddelandet från noden fil mottagare.
* I `applicationProperties` , `eventType` anger att den här händelsen fungerar.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade. Den här tiden är 5 till 6 sekunder efter `MediaSessionEstablished` och efter att videon har börjat flöda. Den här tiden motsvarar den 5-till-6-andra markeringen när [bilen började flytta](#review-the-sample-video) till parkerings partiet.
* `body`Avsnittet innehåller information om drift händelsen. I det här fallet består data `outputType` och `outputLocation` .
* `outputType`Variabeln anger att den här informationen avser fil Sök vägen.
* `outputLocation`Värdet är platsen för MP4-filen i Edge-modulen.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped-och RecordingAvailable-händelser

Om du undersöker egenskaperna för noden signal grind processor i [graf-topologin](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)ser du att aktiverings tiderna är inställda på 5 sekunder. Så cirka 5 sekunder efter att `RecordingStarted` händelsen har tagits emot får du:

* En `RecordingStopped` händelse som anger att inspelningen har stoppats.
* En `RecordingAvailable` händelse som anger att MP4-filen nu kan användas för visning.

De två händelserna genereras vanligt vis inom några sekunder.

## <a name="play-the-mp4-clip"></a>Spela upp MP4-klippet

MP4-filerna skrivs till en katalog på gräns enheten som du konfigurerade i *. miljö* -filen med hjälp av OUTPUT_VIDEO_FOLDER_ON_DEVICE nyckeln. Om du använde standardvärdet ska resultaten finnas i */var/Media/* -mappen.

För att spela upp MP4-klippet:

1. Gå till din resurs grupp, leta upp den virtuella datorn och Anslut sedan med hjälp av Azure-skydds.

    ![Resursgrupp](./media/quickstarts/resource-group.png)
    
    ![Virtuell dator](./media/quickstarts/virtual-machine.png)

1. Logga in med de autentiseringsuppgifter som genererades när du konfigurerade [dina Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Gå till den relevanta katalogen i kommando tolken. Standard platsen är */var/Media*. Du bör se MP4-filerna i katalogen.

    ![Utdata](./media/quickstarts/samples-output.png) 

1. Använd [säker kopia (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) för att kopiera filerna till den lokala datorn. 
1. Spela upp filerna med hjälp av [VLC Media Player](https://www.videolan.org/vlc/) eller någon annan MP4-spelare.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker testa de andra snabb starterna ska du behålla de resurser som du har skapat. Annars går du till dina resurs grupper i Azure Portal, väljer den resurs grupp där du körde den här snabb starten och tar sedan bort alla resurser.

## <a name="next-steps"></a>Nästa steg

* Följ snabb starten för [video analys med din egen modell](use-your-model-quickstart.md) snabb start för att applicera AI på direktsända video flöden.
* Granska ytterligare utmaningar för avancerade användare:

    * Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som stöder RTSP i stället för att använda RTSP-simulatorn. Du hittar IP-kameror som har stöd för RTSP på sidan [ONVIF-produkter](https://www.onvif.org/conformant-products) . Sök efter enheter som uppfyller profilerna G, S eller T.
    * Använd en AMD64-eller x64 Linux-enhet i stället för att använda en virtuell Linux-dator i Azure. Enheten måste finnas i samma nätverk som IP-kameran. Följ anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Följ sedan anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md) för att registrera enheten med Azure IoT Hub.
