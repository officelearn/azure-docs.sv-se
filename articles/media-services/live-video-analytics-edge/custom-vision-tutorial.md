---
title: Analysera direktsänd video med real tids video analys på IoT Edge och Azure Custom Vision
description: Lär dig hur du använder Custom Vision för att bygga en behållar modell som kan identifiera en leksaks Truck och använda AI-utöknings möjligheter för video analys på IoT Edge (LVA) för att distribuera modellen på gränsen för att identifiera leksaks truckar från en real tids video ström.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 97aa514e2347b3398aab6293d8543afcdc239bdb
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776603"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Självstudie: analysera direktsänd video med real tids video analys på IoT Edge och Azure Custom Vision

I den här självstudien får du lära dig hur du använder [Custom vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) för att bygga en behållar modell som kan identifiera en leksaks Last bil och använda [AI-utöknings möjligheter](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) för video analys på IoT Edge för att distribuera modellen på gränsen för att identifiera leksaks truckar från en live video ström.

Vi visar dig hur du sammanställer kraften hos Custom Vision – som gör det möjligt för vem som helst att bygga och träna en modell för visuellt innehåll genom att helt enkelt ladda upp och märka några bilder, utan någon kunskap om data vetenskap, ML eller AI, tillsammans med funktioner i live video analys som gör det enkelt att distribuera en anpassad modell som en behållare på gränsen och analysera en simulerad video-feed I den här självstudien används en virtuell Azure-dator som en IoT Edge enhet, baserat på exempel kod som skrivits i C# och den bygger på snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) .

Självstudien visar hur du:

> [!div class="checklist"]
> * Konfigurera relevanta resurser.
> * Bygg en Custom Vision modell i molnet för att identifiera leksaks truckar och distribuera dem på gränsen
> * Skapa och distribuera ett medie diagram med http-tillägg till anpassad vision-modell
> * Kör exempel koden.
> * Granska och tolka resultatet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Vi rekommenderar att du läser igenom följande artiklar innan du börjar: 

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Översikt över Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md)
* [Videoanalys i realtid utan videoinspelning](analyze-live-video-concept.md)
* [Köra Live Video Analytics med din egen modell](use-your-model-quickstart.md)
* [Självstudie: utveckla en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Så här redigerar du Deployment. * .template.jspå](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Krav

Krav för den här självstudien är:

* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tillägg.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Ignorera den här varningen.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Se till att du har:
    
    * [Konfigurera Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Ställt in din utvecklingsmiljö](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Granska exempel videon

I den här självstudien används en video fil för [leksaks bilar](https://lvamedia.blob.core.windows.net/public/t2.mkv/) för att simulera en Live-dataström. Du kan granska videon via ett program, till exempel [VLC Media Player](https://www.videolan.org/vlc/). Välj CTRL + N och klistra sedan in en länk till [videon för leksaks bilen](https://lvamedia.blob.core.windows.net/public/t2.mkv) för att starta uppspelningen. När du tittar på video noten visas en leksaks Truck i videon vid den 36-andra markören. Den anpassade modellen har tränats för att identifiera denna speciella leksaks Truck. I den här självstudien använder du video analys på IoT Edge för att identifiera sådana leksaks truckar och publicera associerade härlednings händelser till IoT Edge Hub.

## <a name="overview"></a>Översikt

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Översikt över Custom Vision":::

Det här diagrammet visar hur signal flödet i den här självstudien. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar videofeeden från den här servern och skickar video bild rutor till den [RAM hastighet filter processor](media-graph-concept.md#frame-rate-filter-processor) noden. Den här processorn begränsar bild hastigheten för video strömmen som når noden för [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) .
Noden HTTP-tillägg spelar rollen för en proxy. Den konverterar video bild rutorna till den angivna bild typen. Sedan vidarebefordrar avbildningen över REST till en annan Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. I det här exemplet är den Edge-modulen leksakens detektor modell som skapats med hjälp av Custom Vision. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Bygg och distribuera en identifierings modell för Custom Vision leksak 

Som namnet Custom Vision föreslår kan du utnyttja det för att bygga en egen anpassad objekt detektor eller klassificerare i molnet. Det ger ett enkelt, användarvänligt och intuitivt gränssnitt för att bygga anpassade vision modeller som kan distribueras i molnet eller på gränsen via behållare. 

För att skapa en leksaks Last bils detektor rekommenderar vi att du följer den här anpassade insikten skapa en objekt detektor via [snabb starts artikeln](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) för webb portalen.

Ytterligare kommentarer:
 
* I den här självstudien ska du inte använda de exempel bilder som finns i [avsnittet om förutsättningar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)för snabb start. I stället har vi använt en viss avbildnings uppsättning för att skapa en anpassad vision modell för leksaks igenkänning, vi rekommenderar att du använder [de här avbildningarna](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) när du uppmanas att [välja dina utbildnings avbildningar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) i snabb starten.
* I avsnittet tagga avbildning i snabb start ser du till att du taggar leksaks bilen som visas i bilden med taggen – "leverans Last bil".

När den är klar kan du exportera den till en Docker-behållare med hjälp av knappen Exportera på fliken prestanda om modellen är klar. Kontrol lera att du väljer Linux som behållar plattforms typ. Detta är den plattform som containern ska köras på. Datorn som du hämtar behållaren på kan vara antingen Windows eller Linux. Anvisningarna nedan baseras på den behållar fil som hämtats till en Windows-dator.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Översikt över Custom Vision"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Det här kommandot testar behållaren på den lokala datorn och om avbildningen har samma Last bil när vi tränade modellen på, bör utdata vara något som liknar följande, vilket tyder på att leverans trucken har upptäckts med 90,12% sannolikhet.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Granska exempelfilerna

1. I VSCode, bläddrar du till "src/Edge". Du kommer att se. kuvert filen som du skapade tillsammans med några filer för distributions mal len.

    Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen "src/Cloud-to-Device-console-app". Här visas appsettings.jspå filen som du skapade tillsammans med några andra filer:

    * C2D-console-app. CSPROJ – det här är projekt filen för VSCode.
    * operations.jsden här filen visar en lista över de olika åtgärder som du vill att programmet ska köra.
    * Program.cs – det här är exempel program koden som gör följande:

        * Läser in appinställningar.
        * Anropa Live Video Analytics på IoT Edge modulens direkta metoder för att skapa topologi, instansiera grafen och aktivera grafen.
        * Pausar så att du kan granska diagrammets utdata i TERMINALFÖNSTRET och de händelser som skickas till IoT Hub i fönstret utdata.
        * Inaktivera graf-instansen, ta bort graf-instansen och ta bort graf-topologin.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Generera och distribuera distributions manifestet

1. I VSCode navigerar du till "src/Cloud-to-Device-console-app/operations.jspå"

1. Under GraphTopologySet kontrollerar du att följande stämmer:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Under GraphInstanceSet, se till att: 
    1. "topologyName" : "InferencingWithHttpExtension"
    1. Lägg till följande längst upp i Parameters-matrisen – `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Ändra värdet för parametern rtspUrl till – "rtsp://rtspsim:554/media/t2.mkv"    
1. Under GraphTopologyDelete, se till att "name": "InferencingWithHttpExtension"
1. Högerklicka på filen "src/Edge/deployment.customvision.template.json" och klicka på **generera IoT Edge distributions manifest**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Översikt över Custom Vision" bredvid fönstret AZURE IOT HUB i det nedre vänstra hörnet. Du kan kopiera strängen från appsettings.jsi filen. (Här är en annan metod som rekommenderas för att se till att du har rätt IoT Hub konfigurerad i VSCode via [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Översikt över Custom Vision" och klicka på **skapa distribution för en enskild enhet**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Översikt över Custom Vision":::
1. Du uppmanas sedan att välja en IoT Hub enhet. Välj lva-Sample-Device i list rutan.
1. I cirka 30 sekunder uppdaterar du Azure IOT Hub i det nedre vänstra avsnittet och du bör ha gräns enheten med följande moduler distribuerade:

    * Video analys i real tid på IoT Edge modul, med namnet "lvaEdge".
    * En modul med namnet `rtspsim` som simulerar en RTSP-server, som fungerar som källa till en Live-videofeed.
    * En modul med namnet `cv` , som namnet antyder, är den Custom vision leksakens identifierings modell som tillämpar anpassad syn på avbildningarna och returnerar flera tag-typer. (Vår modell tränades bara på en tagg – "leverans Last bil").

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret utdata i Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Översikt över Custom Vision":::

## <a name="run-the-sample-program"></a>Kör exempel programmet

Om du öppnar graf-topologin för den här självstudien i en webbläsare ser du att värdet för inferencingUrl har ställts in på http://cv:80/image , vilket innebär att en härlednings server returnerar resultat efter att ha identifierat leksaks truckar, om sådana finns, i Live-videon.

1. Om du vill starta en felsökningssession väljer du F5-tangenten. Du ser meddelanden som skrivs ut i TERMINALFÖNSTRET.
1. operations.jsvid kod börjar med anrop till Direct-metoderna GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter att du har slutfört tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.
    
   TERMINALFÖNSTRET visar nästa uppsättning med direkta metod anrop:
    
   * Ett anrop till GraphTopologySet som använder föregående topologyUrl.
   * Ett anrop till GraphInstanceSet som använder följande text:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Ett anrop till GraphInstanceActivate som startar graf-instansen och film flödet.
   * Ett andra anrop till GraphInstanceList som visar att graf-instansen är i körnings läge.
    
1. Utdata i TERMINALFÖNSTRET pausas vid en tryck RETUR för att fortsätta prompten. Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret utdata i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här självstudien beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till TERMINALFÖNSTRET och väljer RETUR.
Nästa serie anrop rensar resurser:
    
   * Ett anrop till GraphInstanceDeactivate inaktiverar graf-instansen.
   * Ett anrop till GraphInstanceDelete tar bort instansen.
   * Ett anrop till GraphTopologyDelete tar bort topologin.
   * Ett sista anrop till GraphTopologyList visar att listan är tom.
    
## <a name="interpret-the-results"></a>Tolka resultaten

När du kör medie diagrammet passerar resultatet från noden för HTTP-tillägget processor genom noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i fönstret utdata innehåller ett text avsnitt och ett applicationProperties-avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut. Händelse typen är Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Lägg märke till följande information i det här meddelandet:

* Meddelandet är en diagnostisk händelse. MediaSessionEstablished anger att noden RTSP-källa (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I applicationProperties anger ämnet att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I applicationProperties anger eventType att händelsen är en diagnostisk händelse.
* EventTime anger den tidpunkt då händelsen inträffade.
* Texten innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

Noden för HTTP-tilläggsbegäranden tar emot resultat från Custom Vision containern och genererar resultatet via noden IoT Hub mottagare som utöknings händelser.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Observera följande i ovanstående meddelanden:

* Ämnet i applicationProperties refererar till noden i den MediaGraph som meddelandet genererades från. I det här fallet har meddelandet sitt ursprung i http-tilläggs processorn.
* EventType i applicationProperties anger att detta är en händelse för analys av en analys.
* EventTime anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse och texten innehåller en matris med inferences som kallas "förutsägelser".
* avsnittet förutsägelses innehåller en lista över förutsägelser där leksaks Last bil (tag = Delivery Last bil) finns i ramen. Som du återkallar är leverans Last bil den anpassade taggen som du tillhandahöll för din anpassade utbildade modell för leksaks trucken och modellen är inferencing och identifierar leksaks trucken i Indataporten med olika sannolikhets konfidens resultat.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra självstudierna eller snabb starterna, bör du hålla kvar på de resurser som du har skapat. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien under och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). 

Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

