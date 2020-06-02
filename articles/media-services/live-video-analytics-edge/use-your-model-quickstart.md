---
title: Analysera direktsänd video med din egen modell – Azure
description: I den här snabb starten ska du använda en dator vision för att analysera direktsända video flöden från en (simulerad) IP-kamera.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261974"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Snabb start: analysera direktsänd video med din egen modell

Den här snabb starten visar hur du använder real tids analys på IoT Edge för att analysera Live-videofeeden från en (simulerad) IP-kamera genom att använda en modell för visuellt innehåll för att identifiera objekt. En delmängd av bild rutorna i Live-videofeeden skickas till en tjänst för en tjänst och resultatet skickas till IoT Edge Hub. Den använder en virtuell Azure-dator som en IoT Edge enhet och en simulerad direktuppspelad video ström. Den här artikeln baseras på exempel kod skriven i C#.

Den här artikeln bygger vidare på [den här](detect-motion-emit-events-quickstart.md) snabb starten. 

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) på datorn med följande fil namns tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) är installerat på datorn
* Om du inte har slutfört [den här](detect-motion-emit-events-quickstart.md) snabb starten tidigare slutför du följande steg:
     * [Ställa in Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera det.

## <a name="review-the-sample-video"></a>Granska exempel videon
Som en del av stegen ovan för att konfigurera Azure-resurserna kopieras en (kort) video från en väg trafik till den virtuella Linux-datorn i Azure som används som IoT Edge enhet. Den här video filen används för att simulera en Live-ström för den här självstudien.

Du kan använda ett program som [VLC Player](https://www.videolan.org/vlc/), starta det, trycka på CTRL + N och klistra in [den här](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) länken till videon för att starta uppspelningen. Du ser att tagningen är av trafik på en väg och att många fordon flyttar på den.

När du slutför stegen nedan har du använt video analys på IoT Edge för att identifiera objekt som t. ex. fordon, personer osv. och publicera associerade härlednings händelser till IoT Edge Hub.

## <a name="overview"></a>Översikt

![Översikt](./media/quickstarts/overview-qs5.png)

Diagrammet ovan visar hur signal flödet i den här snabb starten. En Edge-modul (detaljerad [här](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simulerar en IP-kamera som är värd för en RTSP-server. En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar videofeeden från den här servern och skickar video bild rutor till noden för [överspridnings filtrets processor](media-graph-concept.md#frame-rate-filter-processor) . Den här processorn begränsar bild hastigheten för video strömmen som når noden för [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) . 

Noden HTTP-tillägg spelar rollen som en proxy genom att konvertera video bild rutorna till den angivna bild typen och vidarebefordra avbildningen över REST till en annan Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. I det här exemplet skapas en Edge-modul med [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](media-graph-concept.md#iot-hub-message-sink ) , som sedan skickar händelsen till [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.



## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet
    
### <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

Som en del av för hands kraven skulle du ha laddat ned exempel koden till en mapp. Starta Visual Studio Code och öppna mappen.

1. Bläddra till "src/Edge" i Visual Studio Code. Du kommer att se. kuvert filen som du skapade tillsammans med några filer för distributions mal len.

    * Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen "src/Cloud-to-Device-console-app". Här visas den appSettings. JSON-fil som du skapade tillsammans med några andra filer:

    * C2D-console-app. CSPROJ – det här är projekt filen för Visual Studio Code.
    * Operations. JSON – den här filen visar en lista över de olika åtgärder som du vill att programmet ska köra.
    * Program.cs – detta är exempel program koden, som gör följande:

        * Läser in appinställningar.
        *  Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md) 
        * Pausar så att du kan granska utdata från programmet i TERMINALFÖNSTRET och de händelser som genererats av modulen i fönstret utdata
        * Anropar direkta metoder för att rensa resurser   


1. Gör följande ändringar i filen Operations. JSON
    * Ändra länken till graf-topologin:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * Under GraphInstanceSet redigerar du namnet på diagram sto pol Ogin så att den matchar värdet i länken ovan`"topologyName" : "InferencingWithHttpExtension"`
    * Under GraphTopologyDelete redigerar du namnet`"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

1. Högerklicka på filen "src/Edge/Deployment. yolov3. template. JSON" och klicka på generera IoT Edge distributions manifest.

    ![Generera IoT Edge distributions manifest](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Detta bör skapa en manifest fil i src/Edge/config-mappen med namnet "Deployment. yolov3. amd64. JSON".
1. Om du tidigare har slutfört [snabb](detect-motion-emit-events-quickstart.md)starten kan du hoppa över det här steget. Annars anger du anslutnings strängen IoTHub genom att klicka på ikonen "fler åtgärder" bredvid fönstret AZURE IOT HUB i det nedre vänstra hörnet. Du kan kopiera strängen från filen appSettings. JSON. (Här är en annan metod som rekommenderas för att se till att du har rätt IoT Hub konfigurerad i Visual Studio Code via [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![IoTHub anslutnings sträng](./media/quickstarts/set-iotconnection-string.png)
1. Högerklicka sedan på "src/Edge/config/Deployment. yolov3. amd64. JSON" och klicka på "skapa distribution för en enskild enhet". 

    ![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)
1. Du uppmanas sedan att välja en IoT Hub enhet. Välj lva-Sample-Device i list rutan.
1. I cirka 30 sekunder uppdaterar du Azure IOT Hub i det nedre vänstra avsnittet och du bör ha gräns enheten med följande moduler distribuerade:

    1. Video analys-modulen i real tid, med namnet "lvaEdge".
    1. En modul med namnet "rtspsim" som simulerar en RTSP-server, som fungerar som källa till en Live-videofeed.
    1. En modul med namnet "yolov3", som namnet föreslår, är den YOLOv3 objekt identifierings modell som tillämpar dator syn på avbildningarna och returnerar flera klasser av objekt typer.
 
        ![YOLOv3 objekt identifierings modell](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Högerklicka på Live Video Analytics-enheten och klicka på "starta övervakning av den inbyggda händelse slut punkten". Det här steget krävs för att övervaka IoT Hub händelser och se det i fönstret utdata i Visual Studio Code. 

![Starta övervakning](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Kör exempel programmet

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
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
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
1. Medie grafen fortsätter att köras och utskrift av resultaten – RTSP-simulatorn kommer att fortsätta att Visa käll videon. För att stoppa medie diagrammet går du tillbaka till TERMINALFÖNSTRET och trycker på "Ange". Nästa serie anrop görs för att rensa resurser:
     * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen
     * Ett anrop till GraphInstanceDelete för att ta bort instansen
     * Ett anrop till GraphTopologyDelete för att ta bort topologin
     * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom

## <a name="interpret-results"></a>Tolka resultaten

När du kör medie diagrammet skickas resultatet från noden för http-tillägget processor via noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i UTDATAFÖNSTRET i Visual Studio Code innehåller avsnittet "Body" och "applicationProperties". Läs [den här](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikeln för att förstå vad dessa avsnitt representerar.

I meddelandena nedan definieras program egenskaperna och innehållet i bröd texten av modulen live video analys. 



### <a name="mediasession-established-event"></a>MediaSession-etablerad händelse

När ett medie diagram instansieras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs på behållaren rtspsim-live55. Om det lyckas skrivs händelsen ut. Händelse typen är Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
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

Observera följande i ovanstående meddelande:
* Meddelandet är en diagnostisk händelse, MediaSessionEstablished, anger att noden RTSP-källa (ämnet) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.
* "subject" i applicationProperties anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* "eventType" i applicationProperties anger att detta är en diagnostisk händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.

### <a name="inference-event"></a>Händelsen härledning

Noden för HTTP-tilläggsbegäranden tar emot härlednings resultat från yolov3-modulen och genererar dem via noden IoT Hub mottagare som utöknings händelser. I dessa händelser anges typen till "entitet" för att ange att den är en enhet, till exempel en bil eller en Last bil, och att eventTime visar dig hur lång tid (UTC) objektet identifierades. Nedan visas ett exempel där två bilar har identifierats med varierande konfidensnivå i samma video bild ruta.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Observera följande i ovanstående meddelanden:

* "subject" i applicationProperties hänvisar till noden i den graf-topologi som meddelandet genererades från. 
* "eventType" i applicationProperties anger att det är en analys händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse och texten innehåller därför "inferences"-data.
* avsnittet "inferences" visar att "typ" är "entitet" och innehåller ytterligare information om entiteten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra snabb starterna ska du hålla på de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp under vilken du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF](https://www.onvif.org/conformant-products/) -sidan produkter genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) och följa instruktionerna i den här [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) snabb start för att registrera enheten med Azure-IoT Hub.

