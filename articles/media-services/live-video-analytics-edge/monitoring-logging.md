---
title: Övervakning och loggning – Azure
description: Den här artikeln innehåller en översikt över video analys på IoT Edge övervakning och loggning.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e1f31c6bb3ea344286ad9af89417ca9f8fd59527
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934301"
---
# <a name="monitoring-and-logging"></a>Övervakning och loggning

I den här artikeln får du lära dig mer om hur du kan ta emot händelser från direktsänd video analys i IoT Edge modul för fjärrövervakning. 

Du får också lära dig hur du kan kontrol lera vilka loggar som genereras av modulen.

## <a name="taxonomy-of-events"></a>Händelsetaxonomi

Real tids analys på IoT Edge avger händelser eller telemetridata enligt följande taxonomi.

![Video analys i real tid för IoT Edge telemetri](./media/telemetry-schema/taxonomy.png)

* Drift: händelser som genereras som en del av åtgärder som utförs av en användare eller under körningen av ett [medie diagram](media-graph-concept.md).
   
   * Volym: förväntas vara låg (några gånger om en minut eller till och med lägre pris).
   * Exempel:

      Inspelningen startade (nedan), inspelningen stoppades
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostik: händelser som hjälper till att diagnostisera problem och/eller problem med prestanda.

   * Volym: kan vara hög (flera gånger per minut).
   * Exempel:
   
      RTSP [-](https://en.wikipedia.org/wiki/Session_Description_Protocol) information (nedan) eller luckor i det inkommande video flödet.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analys: händelser som genereras som en del av video analysen.

   * Volym: kan vara hög (flera gånger per minut eller oftare).
   * Exempel:
      
      Rörelse identifierad (nedan), resultat för nedgradering.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
De händelser som genereras av modulen skickas till [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)och därifrån kan de dirigeras till andra mål. 

### <a name="timestamps-in-analytic-events"></a>Tidsstämplar i analys händelser
Som anges ovan har händelser som genererats som en del av video analysen en tidsstämpel som är kopplad till dem. Om du har [spelat in direktsänd video](video-recording-concept.md) som en del av din graf-topologi, hjälper den här tidsstämpeln dig att hitta var i den inspelade videon att en viss händelse inträffade. Nedan följer rikt linjerna för hur du mappar tidsstämpeln i en analys händelse till tids linjen för videon som spelas in i en [Azure Media service-till gång](terminology.md#asset).

Extrahera först `eventTime` värdet. Använd det här värdet i ett [tids intervall filter](playback-recordings-how-to.md#time-range-filters) för att hämta en lämplig del av inspelningen. Till exempel kanske du vill hämta video som börjar 30 sekunder innan `eventTime` och slutar 30 sekunder efteråt. Med ovanstående exempel, där `eventTime` är 2020-05-12T23:33:09.381 z, skulle en begäran om ett HLS-manifest för fönstret +/-30 s se ut så här:
```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```
URL: en ovan skulle returnera en så kallad [huvud-spelnings lista](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)med URL: er för medie spelnings listor. Medie spelnings listan skulle innehålla poster som följande:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
I ovanstående rapport rapporterar posten att ett video fragment är tillgängligt som startar vid ett tidsstämpel-värde för `143039375031270` . `timestamp`Värdet i analys händelsen använder samma tids skala som medie spelnings listan och kan användas för att identifiera relevanta videofragment och söka efter rätt ram.

För mer information, kan du läsa en av de många [artiklarna](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) i den ram som är korrekta i HLS.

## <a name="controlling-events"></a>Kontrollera händelser

Du kan använda följande modul dubbla egenskaper, enligt beskrivningen i [modulens dubbla JSON-schema](module-twin-configuration-schema.md), för att styra de operativa och diagnostiska händelser som publiceras av video analysen i IoT Edge modul.

`diagnosticsEventsOutputName` – inkludera och ange (valfritt) värde för den här egenskapen för att hämta diagnostiska händelser från modulen. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera diagnostiska händelser.
   
`operationalEventsOutputName` – inkludera och ange (valfritt) värde för den här egenskapen för att hämta drift händelser från modulen. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera drift händelser.
   
Analytics-händelserna genereras av noder som rörelse identifierings processor, eller HTTP-tilläggsbegäranden, och IoT Hub-mottagaren används för att skicka dem till IoT Edge Hub. 

Du kan styra [operationsföljden för alla ovanstående händelser](../../iot-edge/module-composition.md#declare-routes) via en önskad egenskap i $edgeHub modul, dubbla (i distributions manifestet):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

I det här exemplet är lvaEdge namnet på den direktsända video analysen i IoT Edge modul, och regeln för routning följer schemat som definierats i [deklarerar vägar](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> För att säkerställa att Analytics-händelser når IoT Edge hubben måste det finnas en IoT Hub-nod för en nod för avkänning av rörelse avkänning och/eller valfri HTTP-tilläggsprovider.

## <a name="event-schema"></a>Händelseschema

Händelser kommer från gräns enheten och kan förbrukas på gränsen eller i molnet. Händelser som genereras av real tids analys på IoT Edge överensstämmer med det [mönster för strömnings meddelande](../../iot-hub/iot-hub-devguide-messages-construct.md) som upprättats av Azure IoT Hub, med system egenskaper, program egenskaper och en brödtext.

### <a name="summary"></a>Sammanfattning

Varje händelse, vid observation via IoT Hub, har en uppsättning gemensamma egenskaper enligt beskrivningen nedan.

|Egenskap   |Egenskapstyp| Datatyp   |Beskrivning|
|---|---|---|---|
|meddelande-ID |säker |guid|  Unikt händelse-ID.|
|ämne| applicationProperty |sträng|    Azure Resource Manager sökväg för Media Servicess kontot.|
|motiv|   applicationProperty |sträng|    Under Sök väg till den enhet som avger händelsen.|
|Händelsetid| applicationProperty|    sträng| Tiden då händelsen skapades.|
|Händelsetyp| applicationProperty |sträng|    Händelse typ identifierare (se nedan).|
|body|body  |objekt|    Specifika händelse data.|
|Dataversion    |applicationProperty|   sträng  |{Major}. Del|

### <a name="properties"></a>Egenskaper

#### <a name="message-id"></a>meddelande-ID

Händelse globalt unik identifierare (GUID)

#### <a name="topic"></a>ämne

Representerar Azure Media Service-kontot som är associerat med grafen.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>motiv

Enhet som avger händelsen:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Egenskapen subject gör det möjligt för allmänna händelser att mappas till den genererade modulen. Om t. ex. ogiltigt RTSP-användarnamn eller lösen ord finns den genererade händelsen finns `Microsoft.Media.Graph.Diagnostics.ProtocolError` på `/graphInstances/myGraph/sources/myRtspSource` noden.

#### <a name="event-types"></a>Händelsetyper

Händelse typer tilldelas en namnrymd enligt följande schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Händelseklasser

|Klassnamn|Beskrivning|
|---|---|
|Analytics  |Händelser som genereras som en del av innehålls analysen.|
|Diagnostik    |Händelser som är till hjälp vid diagnostik av problem och prestanda.|
|Operativ    |Händelser som genererats som en del av resurs åtgärden.|

Händelse typerna är speciella för varje händelse klass.

Exempel:

* Microsoft. Media. Graph. Analytics. härledning
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Tid för händelsen

Händelse tiden beskrivs i ISO8601-strängen och den tidpunkt då händelsen inträffade.

## <a name="logging"></a>Loggning

Precis som med andra IoT Edge moduler kan du också [Granska behållar loggarna](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) på gräns enheten. Informationen som skrivs till loggarna kan styras av [Följande modul, dubbla](module-twin-configuration-schema.md) egenskaper:

* logLevel

   * Tillåtna värden är utförlig, information, varning, fel, ingen.
   * Standardvärdet är information – loggarna innehåller fel, varning och information. Kömeddelanden.
   * Om du ställer in värdet på varning kommer loggarna att innehålla fel och varnings meddelanden
   * Om du ställer in värdet på fel kommer loggarna bara att innehålla fel meddelanden.
   * Om du anger värdet Ingen skapas inga loggar (detta rekommenderas inte).
   * Du bör endast använda verbose om du behöver dela loggar med Azure-Support för att diagnosticera ett problem.
* logCategories

   * En kommaavgränsad lista över ett eller flera av följande: program, händelser, MediaPipeline.
   * Standard: program, händelser.
   * Program – det här är en hög nivå information från modulen, t. ex. start meddelanden i moduler, miljö fel och direkta metod anrop.
   * Händelser – dessa är alla händelser som beskrevs tidigare i den här artikeln.
   * MediaPipeline – dessa är några lågnivå loggar som kan ge insikt vid fel sökning av problem, till exempel problem med att upprätta en anslutning med en RTSP-kompatibel kamera.
   
### <a name="generating-debug-logs"></a>Genererar fel söknings loggar

I vissa fall kan du behöva generera mer detaljerade loggar än de som beskrivs ovan, för att hjälpa Azure-supporten att lösa ett problem. Det finns två steg för att göra detta.

Först länkar du [modulens lagring till enhets lagringen](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via createOptions. Om du undersöker en [mall för distributions manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) från snabb starterna visas:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Ovan kan Edge-modulen skriva loggar till lagrings Sök vägen för (enhet) "/var/Local/MediaServices/". Om du lägger till följande önskade egenskap i modulen:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Sedan skriver modulen fel söknings loggar i binärformat till lagrings Sök vägen (enhet)/var/Local/MediaServices/debuglogs/, som du kan dela med Azure-supporten.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[Vanliga frågor och svar](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Nästa steg

[Kontinuerlig videoinspelning](continuous-video-recording-tutorial.md)
