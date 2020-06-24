---
title: Analysera direktsänd video med din egen modell – Azure
description: I den här snabb starten ska du använda dator vision för att analysera direktsända video flöden från en (simulerad) IP-kamera.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 73b8f83b9e3c450612c742a831ac800343b3bbbc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816688"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Snabb start: analysera direktsänd video med din egen modell

Den här snabb starten visar hur du använder video analys i real tid för IoT Edge för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera. Du får se hur du använder en modell för dator vision för att identifiera objekt. En delmängd av bild rutorna i direktsänd video-feed skickas till en tjänst för drift störningar. Resultaten skickas till IoT Edge Hub. 

Den här snabb starten använder en virtuell Azure-dator som en IoT Edge enhet och använder en simulerad direktuppspelad video ström. Den baseras på exempel kod som skrivits i C# och bygger på snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

## <a name="prerequisites"></a>Krav

* Ett Azure-konto som innehåller en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
* [Visual Studio Code](https://code.visualstudio.com/)med följande tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Om du inte slutför snabb starten för att [identifiera rörelse-och genererar händelser](detect-motion-emit-events-quickstart.md) måste du [Konfigurera Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera prompten.

## <a name="review-the-sample-video"></a>Granska exempel videon
När du konfigurerar Azure-resurserna kopieras en kort video med väg trafik till den virtuella Linux-dator i Azure som du använder som IoT Edge enhet. I den här snabb starten används video filen för att simulera en Live-dataström.

Öppna ett program som [VLC Media Player](https://www.videolan.org/vlc/). Välj CTRL + N och klistra sedan in en länk till [videon](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) för att starta uppspelningen. Du ser hur många fordon som flyttas i väg trafiken.

I den här snabb starten ska du använda real tids analys på IoT Edge för att identifiera objekt som till exempel fordon och personer. Du publicerar associerade härlednings händelser till IoT Edge Hub.

## <a name="overview"></a>Översikt

![Översikt](./media/quickstarts/overview-qs5.png)

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar videofeeden från den här servern och skickar video bild rutor till den [RAM hastighet filter processor](media-graph-concept.md#frame-rate-filter-processor) noden. Den här processorn begränsar bild hastigheten för video strömmen som når noden för [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) . 

Noden HTTP-tillägg spelar rollen för en proxy. Den konverterar video bild rutorna till den angivna bild typen. Sedan vidarebefordrar avbildningen över REST till en annan Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. I det här exemplet skapas en Edge-modul med hjälp av [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.



## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet
    
### <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

Som en del av förutsättningarna hämtade du exempel koden till en mapp. Följ dessa steg om du vill granska och redigera exempelfilerna.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten. Den innehåller några värden för plats hållare. *. Miljö* filen innehåller värdena för variablerna.

1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:

    * ***C2D-console-app. CSPROJ*** – projekt filen för Visual Studio Code.
    * ***operations.js*** en lista över de åtgärder som du vill att programmet ska köra.
    * ***Program.cs*** – exempel koden. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
        * Pausar så att du kan granska programmets utdata i **terminalfönstret** och granska de händelser som har genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.


1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Under `GraphTopologyDelete` redigerar du namnet:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

1. Högerklicka på filen *src/Edge/deployment.yolov3.template.js* och välj sedan **skapa IoT Edge distributions manifest**.

    ![Generera IoT Edge distributions manifest](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Filen *deployment.yolov3.amd64.jspå* manifest filen skapas i mappen *src/Edge/config* .

1. Hoppa över det här steget om du har slutfört snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

    Annars, nära **Azure IoT Hub** -fönstret i det nedre vänstra hörnet, väljer du ikonen **fler åtgärder** och väljer sedan **Ange IoT Hub anslutnings sträng**. Du kan kopiera strängen från *appsettings.jsi* filen. Eller, för att se till att du har konfigurerat rätt IoT-hubb i Visual Studio Code, använder du [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Ange IoT-hubbens anslutningssträng](./media/quickstarts/set-iotconnection-string.png)

1. Högerklicka på *src/Edge/config/deployment.yolov3.amd64.jspå* och välj **skapa distribution för en enskild enhet**. 

    ![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)

1. När du uppmanas att välja en IoT Hub-enhet väljer du **lva-Sample-Device**.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys-modulen i real tid med namnet **lvaEdge**
    * Modulen **rtspsim** , som simulerar en RTSP-server och fungerar som källa för en Live-videofeed
    * **Yolov3** -modulen, som är den yolov3 objekt identifierings modell som tillämpar dator syn på avbildningarna och returnerar flera klasser av objekt typer
 
      ![Moduler som distribueras i gräns enheten](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret **utdata** i Visual Studio Code. 

![Starta övervakning](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Kör exempel programmet

1. Om du vill starta en felsökningssession väljer du F5-tangenten. Du ser meddelanden som skrivs ut i **terminalfönstret** .
1. *operations.jsvid* kod börjar med anrop till direkta metoder `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har slutfört tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.

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

     * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och video flödet
     * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd
1. Utdata i **terminalfönstret** pausas vid en `Press Enter to continue` prompt. Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:
      * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
      * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
      * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
      * Ett sista anrop till `GraphTopologyList` visar att listan är tom.

## <a name="interpret-results"></a>Tolka resultaten

När du kör medie diagrammet passerar resultatet från noden för HTTP-tillägget processor genom noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i fönstret **utdata** innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live55. Om anslutningen lyckas skrivs följande händelse ut. Händelse typen är `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

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

Lägg märke till följande information i det här meddelandet:

* Meddelandet är en diagnostisk händelse. `MediaSessionEstablished`anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Anger den tidpunkt då händelsen inträffade.
* `body`Innehåller data om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

Noden för HTTP-tilläggsbegäranden tar emot härlednings resultat från yolov3-modulen. Den genererar sedan resultaten genom IoT Hub Sink-noden som utöknings händelser. 

I dessa händelser anges typen till `entity` för att ange att den är en enhet, till exempel en bil eller Truck. `eventTime`Värdet är UTC-tiden då objektet identifierades. 

I följande exempel upptäcktes två bilar i samma video RAM, med varierande nivåer av förtroende.

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

Observera följande information i meddelandena:

* I `applicationProperties` `subject` refererar den nod i den graf-topologi från vilken meddelandet genererades. 
* I `applicationProperties` `eventType` anger att den här händelsen är en analys händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Analytics-händelsen. I det här fallet är händelsen en utmatnings händelse, så att texten innehåller `inferences` data.
* `inferences`Avsnittet visar att `type` är `entity` . Det här avsnittet innehåller ytterligare information om entiteten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova andra snabb starter behåller du de resurser som du har skapat. Annars går du till Azure Portal, går till dina resurs grupper, väljer den resurs grupp där du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

