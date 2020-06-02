---
title: Identifiera rörelse-och genererar händelser – Azure
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att identifiera rörelse-och utsändnings händelser genom att programmatiskt anropa direkta metoder.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262028"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Snabb start: identifiera rörelse-och genererar händelser

Den här snabb starten vägleder dig genom stegen för att komma igång med real tids video analys på IoT Edge. Den använder en virtuell Azure-dator som en IoT Edge enhet och en simulerad direktuppspelad video ström. När du har slutfört installations stegen kan du köra en simulerad real tids video ström genom ett medie diagram som identifierar och rapporterar en rörelse i den data strömmen. Diagrammet nedan visar en grafisk representation av det medie diagrammet.

![Real video analys baserat på rörelse identifiering](./media/analyze-live-video/motion-detection.png) 

Den här artikeln baseras på [exempel kod](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) skriven i C#.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) på datorn med följande fil namns tillägg:
    1. [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) är installerat på datorn

> [!TIP]
> Du kan uppmanas att installera Docker när du installerar tillägget Azure IoT tools. Du kan ignorera det.

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

Följande Azure-resurser krävs för den här självstudien.

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installerat

I den här snabb starten rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de Azure-resurser som nämns ovan i din Azure-prenumeration. Följ bara stegen nedan.

1. Bläddra till https://shell.azure.com.
1. Om det här är första gången du använder Cloud Shell uppmanas du att välja en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs. Välj "skapa lagring" för att skapa ett lagrings konto för att lagra Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från det som skriptet skapar för användning med ditt Azure Media Services-konto.
1. Välj "bash" som din miljö i list rutan till vänster i Shell-fönstret.

    ![Miljö väljare](./media/quickstarts/env-selector.png)

1. Kör följande kommando

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Om skriptet har slutförts bör du se alla resurser som nämns ovan i din prenumeration.

1. När skriptet är klart klickar du på klammerparenteserna för att exponera mappstrukturen. Du kommer att se några filer som skapats under katalogen ~/clouddrive/lva-Sample. Intressanthet i den här snabb starten är:

     * ~/clouddrive/lva-Sample/Edge-Deployment/.env-innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en gräns enhet
     * ~/clouddrive/lva-Sample/AppSetting.JSON – används av Visual Studio Code för att köra exempel koden
     
Du behöver dessa för att uppdatera filerna i Visual Studio Code senare i snabb starten. Du kanske vill kopiera dem till en lokal fil för tillfället.


 ![Appinställningar](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

1. Klona lagrings platsen härifrån https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Starta Visual Studio Code och öppna mappen där lagrings platsen har laddats ned till.
1. I Visual Studio Code bläddrar du till mappen "src/Cloud-to-Device-console-app" och skapar en fil med namnet "appSettings. JSON". Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från ~/clouddrive/lva-Sample/appSettings.JSON-filen som genererades i föregående avsnitt (se steg 5)

    Texten bör se ut så här:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Bläddra sedan till mappen "src/Edge" och skapa en fil med namnet ". kuvert".
1. Kopiera innehållet från/clouddrive/lva-Sample/Edge-Deployment/.env-filen. Texten bör se ut så här:

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

1. Bläddra till "src/Edge" i Visual Studio Code. Du kommer att se. kuvert filen som du skapade tillsammans med några filer för distributions mal len.

    Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen "src/Cloud-to-Device-console-app". Här visas den appSettings. JSON-fil som du skapade tillsammans med några andra filer:

    * C2D-console-app. CSPROJ – projekt filen för Visual Studio Code.
    * Operations. JSON – den här filen listar de olika åtgärder som du vill att programmet ska köra.
    * Program.cs – exempel koden, som gör följande:
    
        * Läser in appinställningar
        * Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md) 
        * Pausar så att du kan granska utdata från programmet i TERMINALFÖNSTRET och de händelser som genererats av modulen i fönstret utdata
        * Anropar direkta metoder för att rensa resurser   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningar för dessa moduler. Följ de här stegen för att generera ett sådant manifest från mallfilen och distribuera det sedan till gräns enheten.

1. Öppna Visual Studio Code
1. Ange anslutnings strängen IoTHub genom att klicka på ikonen "fler åtgärder" bredvid fönstret AZURE IOT HUB i det nedre vänstra hörnet. Du kan kopiera strängen från filen src/Cloud-to-Device-console-app/appSettings. JSON. 

    ![Ange IOT-anslutningssträng](./media/quickstarts/set-iotconnection-string.png)
1. Högerklicka sedan på filen "src/Edge/Deployment. template. JSON" och klicka på "generera IoT Edge distributions manifest".
    ![Generera IoT Edge distributions manifest](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Detta bör skapa en manifest fil i src/Edge/config-mappen med namnet "Deployment. amd64. JSON".
1. Högerklicka på "src/Edge/config/Deployment. amd64. JSON" och klicka på "skapa distribution för enskild enhet" och välj namnet på din Edge-enhet.

    ![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)
1. Du uppmanas sedan att välja en IoT Hub enhet. Välj lva – exempel-Device i list rutan.
1. Om 30 sekunder uppdaterar du Azure IOT Hub i det nedre vänstra avsnittet och ser till att gräns enheten har följande moduler distribuerade:

    * Video analys i real tid för IoT Edge (Modulnamn "lvaEdge")
    * RTSP Simulator (Modulnamn "rtspsim")

RTSP Simulator-modulen simulerar en video ström med hjälp av en videofil som lagras som kopierades till din Edge-enhet när du körde [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). I det här skedet har du distribuerat modulerna men inga medie diagram är aktiva.

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Du kommer att använda live video analys i IoT Edge-modulen för att identifiera rörelser i inkommande direktsända video strömmar och skicka händelser till IoT Hub. Följ dessa steg för att se de här händelserna:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub längst ned till vänster.
1. Expandera noden enheter.
1. Right-klinker på lva-Sample-Device och väljer alternativet "starta övervakning av inbyggd händelse övervakning".

    ![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Kör exempel programmet

Följ stegen nedan för att köra exempel koden.
1. I Visual Studio Code navigerar du till "src/Cloud-to-Device-console-app/Operations. JSON".
1. Under noden GraphTopologySet kontrollerar du följande:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Under noderna GraphInstanceSet och GraphTopologyDelete kontrollerar du att värdet för topologyName matchar värdet för egenskapen "namn" i ovanstående Graph-topologi:

    `"topologyName" : "MotionDetection"`
    
1. Starta en felsökningssession (tryck på F5). Du kommer att börja se vissa meddelanden som skrivs ut i TERMINALFÖNSTRET.
1. Operations. JSON börjar inte med anrop till GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter tidigare snabb starter, returnerar detta tomma listor och pausar sedan för att trycka på RETUR.

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
     
     * Ett anrop till GraphInstanceActivate för att starta graf-instansen och starta video flödet.
     * Ett andra anrop till GraphInstanceList för att visa att graf-instansen faktiskt är i körnings läge.
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

* Meddelandet är en diagnostisk händelse, MediaSessionEstablished, anger att noden RTSP-källa (ämnet) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.
* "Subject" i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* "eventType" i applicationProperties anger att det är en diagnostisk händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.


## <a name="motion-detection-event"></a>Film identifierings händelse

När motion identifieras skickar Live Video Analytics Edge-modulen en händelse för nedgradering. Typen har angetts till "Motion" för att visa att det är ett resultat från processen för identifiering av rörelser och att eventTime talar om vilken tid (UTC) som inträffar. Nedan visas ett exempel:

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

* "subject" i applicationProperties refererar noden i det medie diagram som meddelandet genererades från. I det här fallet kommer meddelandet från noden motion-identifiering.
* "eventType" i applicationProperties anger att det är en analys händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
"Body" innehåller data om Analytics-händelsen. I det här fallet är händelsen en utmatnings händelse och därför innehåller texten "timestamp" och "inferences"-data.
* "inferences"-data anger att "typ" är "rörelse" och innehåller ytterligare information om händelsen "rörelse".
* "box"-avsnittet innehåller koordinaterna för en avgränsnings ruta runt det flyttade objektet. Värdena normaliseras av videons bredd och höjd i bild punkter (t. ex. bredden på 1920 och höjden på 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Rensa resurser

Om du avser att testa de andra snabb starterna ska du hålla på de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp under vilken du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Kör de andra snabb starterna, t. ex. identifiering av ett objekt i en direktsända video flöde.        
