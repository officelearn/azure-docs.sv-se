---
title: Kom igång med live video analys på IoT Edge – Azure
description: Den här snabb starten visar hur du kommer igång med video analys i real tid på IoT Edge och hur du identifierar rörelser i en direktsända video ström.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262016"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Snabb start: kom igång – direktsända video analyser på IoT Edge

Den här snabb starten vägleder dig genom stegen för att komma igång med real tids video analys på IoT Edge. Den använder en virtuell Azure-dator som en IoT Edge enhet och en simulerad direktuppspelad video ström. När du har slutfört installations stegen kan du köra en simulerad real tids video ström genom ett medie diagram som identifierar och rapporterar en rörelse i den data strömmen. Diagrammet nedan visar en grafisk representation av det medie diagrammet.

![Real video analys baserat på rörelse identifiering](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT tools-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Nätverket, att utvecklings datorn är ansluten till, bör tillåta AMQP-protokollet via port 5671 (så att Azure IoT-verktyg kan kommunicera med Azure IoT Hub).

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
    
Om skriptet har slutförts bör du se alla resurser som nämns ovan i din prenumeration. Som en del av skriptets utdata genereras en tabell med resurser som visar IoT Hub-namnet. Sök efter resurs typen **Microsoft. Devices/IotHubs**och Anteckna namnet. Du kommer att behöva detta i nästa steg. Skriptet genererar också några konfigurationsfiler i katalogen ~/clouddrive/lva-Sample/-katalogen – du behöver dessa senare i snabb starten.

## <a name="deploy-modules-on-your-edge-device"></a>Distribuera moduler på din Edge-enhet

Kör följande kommando från Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Kommandot ovan distribuerar följande moduler till gräns enheten (den virtuella Linux-datorn):

* Video analys i real tid för IoT Edge (Modulnamn "lvaEdge")
* RTSP Simulator (Modulnamn "rtspsim")

RTSP Simulator-modulen simulerar en video ström med hjälp av en videofil som lagras som kopierades till din Edge-enhet när du körde [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). I det här skedet har du distribuerat modulerna men inga medie diagram är aktiva.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Konfigurera tillägget Azure IoT tools i Visual Studio Code

Starta Visual Studio Code och följ anvisningarna nedan för att ansluta till din Azure-IoT Hub med hjälp av tillägget Azure IoT-verktyg.

1. Gå till fliken Utforskaren i Visual Studio Code via **Visa**  >  **Utforskaren** eller tryck på (Ctrl + Shift + E).
1. På fliken Utforskaren klickar du på "Azure IoT Hub" i det nedre vänstra hörnet.
1. Klicka på ikonen fler alternativ om du vill se snabb menyn och välja alternativet "Ange IoT Hub anslutnings sträng".
1. En inmatad ruta visas och anger sedan IoT Hub anslutnings strängen. Du kan hämta anslutnings strängen för din IoT Hub från ~/clouddrive/lva-Sample/appSettings.json i Cloud Shell.
1. Om anslutningen lyckas visas listan med gräns enheter. Det bör finnas minst en enhet med namnet "lva-Sample-Device".
1. Nu kan du hantera dina IoT Edge enheter och interagera med Azure IoT Hub via snabb menyn.
1. Du kan visa de moduler som distribuerats på gräns enheten genom att expandera noden moduler under "lva-Sample-Device".

    ![lva-exempel-Device-nod](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Använda direkta metoder

Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa direkta metoder. Läs [direkta metoder för video analys på IoT Edge](direct-methods.md) för att förstå alla direkta metoder som tillhandahålls av modulen. 

### <a name="invoke-graphtopologylist"></a>Anropa GraphTopologyList
Detta räknar upp alla [Graph-topologier](media-graph-concept.md#media-graph-topologies-and-instances) i modulen.

1. Högerklicka på modulen "lvaEdge" och välj "anropa modul Direct metod" på snabb menyn.
1. En redigerings ruta visas i det översta mitten av Visual Studio Code-fönstret. Ange "GraphTopologyList" i redigerings rutan och tryck på RETUR.
1. Kopiera och klistra sedan in nedanstående JSON-nyttolast i redigerings rutan och tryck på RETUR.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Inom några sekunder visas UTDATAFÖNSTRET i Visual Studio Code-popup med följande svar

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Ovanstående svar förväntas eftersom inga diagram-topologier har skapats.
    

### <a name="invoke-graphtopologyset"></a>Anropa GraphTopologySet

Med samma steg som de som beskrivs för att anropa GraphTopologyList, kan du anropa GraphTopologySet för att ange en [graf-topologi](media-graph-concept.md#media-graph-topologies-and-instances) med följande JSON som nytto lasten.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


Ovanstående JSON-nyttolast resulterar i att en Graph-topologi skapas som definierar tre parametrar (två av som har standardvärden). Topologin har en källa (RTSP-källa), en processor (process för rörelse identifiering) och en Sink-nod (IoT Hub Sink).

Inom några sekunder visas följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Statusen som returneras är 201, vilket indikerar att en ny topologi har skapats. Prova följande som nästa steg:

* Anropa GraphTopologySet igen och Observera att den returnerade status koden är 200. Status kod 200 anger att en befintlig topologi har uppdaterats.
* Anropa GraphTopologySet igen men ändra beskrivnings strängen. Observera att status koden i svaret är 200 och att beskrivningen uppdateras till det nya värdet.
* Anropa GraphTopologyList enligt beskrivningen i föregående avsnitt och Observera att nu kan du se topologin "MotionDetection" i den returnerade nytto lasten.

### <a name="invoke-graphtopologyget"></a>Anropa GraphTopologyGet

Anropar nu GraphTopologyGet med följande nytto Last

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Observera följande i nytto lasten för svar:

* Status koden är 200, vilket indikerar att det lyckades.
* Nytto lasten har skapats och tidsstämpeln "lastModified".

### <a name="invoke-graphinstanceset"></a>Anropa GraphInstanceSet

Sedan skapar du en diagram instans som hänvisar till diagram sto pol Ogin ovan. Som beskrivs [här](media-graph-concept.md#media-graph-topologies-and-instances)kan du med Graph-instanser analysera direktuppspelade video strömmar från flera kameror med samma graf-topologi.

Anropa den direkta metoden GraphInstanceSet med följande nytto Last.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

. Tänk på följande:

* I nytto lasten ovan anges det Topology-namn (MotionDetection) som instansen måste skapas för.
* Nytto lasten innehåller ett parameter värde för "rtspUrl", som inte har något standardvärde i nytto lasten i graf-topologin.

Inom några sekunder visas följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Observera följande i nytto lasten för svar:

* Status koden är 201, vilket indikerar att en ny instans skapades.
* State är "inaktiv", vilket indikerar att graf-instansen skapades men inte Aktiver ATS. Mer information finns i [Media Graph-tillstånd](media-graph-concept.md).

Prova följande som nästa steg:

* Anropa GraphInstanceSet igen med samma nytto last och Observera att den returnerade status koden nu är 200.
* Anropa GraphInstanceSet igen men med en annan beskrivning och Observera att den uppdaterade beskrivningen i svars nytto lasten indikerar att graf-instansen har uppdaterats.
* Anropa GraphInstanceSet men ändra namnet till "Sample-Graph-2" och observera svarets nytto Last. Observera att en ny diagram instans skapas (dvs. status koden är 201).

### <a name="invoke-graphinstanceactivate"></a>Anropa GraphInstanceActivate

Aktivera nu graf-instansen – som startar flödet av direktsänd video via modulen. Anropa den direkta metoden GraphInstanceActivate med följande nytto Last.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 i nytto lasten för svaret indikerar att graf-instansen har Aktiver ATS.

### <a name="invoke-graphinstanceget"></a>Anropa GraphInstanceGet

Anropa nu den direkta metoden GraphInstanceGet med följande nytto last:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Observera följande i nytto lasten för svar:

* Status koden är 200, vilket indikerar att det lyckades.
* State är "aktivt", vilket indikerar att graf-instansen är i "aktivt" läge.

## <a name="observe-results"></a>Observera resultat

Graf-instansen som vi skapade och aktiverade ovan använder noden motion-identifiering för att identifiera rörelse i inkommande real tids video ström och skickar händelser till noden IoT Hub mottagare. Dessa händelser vidarebefordras sedan till IoT Edge Hub, som nu kan observeras. I så fall följer du dessa steg.

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub längst ned till vänster.
2. Expandera noden enheter.
3. Right-klinker på lva-Sample-Device och väljer alternativet "starta övervakning av inbyggd händelse övervakning".

![Börja övervaka IoT Hub-händelser](./media/quickstarts/start-monitoring-iothub-events.png)

Följande meddelanden visas i fönstret utdata:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Observera följande i ovanstående meddelande

* Meddelandet innehåller avsnittet "Body" och "applicationProperties". För att förstå vad dessa avsnitt representerar kan du läsa artikeln [skapa och läsa IoT Hub meddelande](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "subject" i applicationProperties refererar till noden i den MediaGraph som meddelandet genererades från. I det här fallet kommer meddelandet från processen för identifiering av rörelser.
* "eventType" i applicationProperties anger att det är en analys händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om Analytics-händelsen. I det här fallet är händelsen en utmatnings händelse och därför innehåller texten "timestamp" och "inferences"-data.
* avsnittet "inferences" visar att "typ" är "rörelse" och innehåller ytterligare information om händelsen "Motion".

Om du låter MediaGraph köras för någon gång visas följande meddelande även i fönstret utdata:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Observera följande i ovanstående meddelande

* "subject" i applicationProperties anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* "eventType" i applicationProperties anger att detta är en diagnostisk händelse.
* "Body" innehåller data om Diagnostic-händelsen. I det här fallet är händelsen MediaSessionEstablished och därmed bröd texten.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Starta ytterligare direkta metoder för att rensa

Starta nu direkta metoder för att inaktivera och ta bort graf-instansen (i den ordningen).

### <a name="invoke-graphinstancedeactivate"></a>Anropa GraphInstanceDeactivate

Anropa den direkta metoden GraphInstanceDeactivate med följande nytto Last.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf-instansen har inaktiverats.

Prova följande, som nästa steg.

* Anropa GraphInstanceGet som anges i de tidigare avsnitten och observera "State"-värdet.

### <a name="invoke-graphinstancedelete"></a>Anropa GraphInstanceDelete

Anropa den direkta metoden GraphInstanceDelete med följande nytto Last

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 i svaret indikerar att graf-instansen har tagits bort.

### <a name="invoke-graphtopologydelete"></a>Anropa GraphTopologyDelete

Anropa den direkta metoden GraphTopologyDelete med följande nytto last:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf sto pol Ogin har tagits bort.

Prova följande som nästa steg.

* Anropa GraphTopologyList och Observera att det inte finns några diagram-topologier i modulen.
* Anropa GraphInstanceList med samma nytto last som GraphTopologyList och Observera att inga graf-instanser räknas upp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurser som skapats i den här snabb starten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du registrerar video med hjälp av real tids video analys på IoT Edge
* Läs mer om diagnostiska meddelanden.
