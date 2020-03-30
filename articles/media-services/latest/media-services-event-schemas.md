---
title: Azure Event Grid-scheman för Media Services-händelser
description: Beskriver de egenskaper som tillhandahålls för Media Services-händelser med Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: d4a206bbddedfe9f23a943df27c6ac4b5fe17e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251354"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid-scheman för Media Services-händelser

Den här artikeln innehåller scheman och egenskaper för Media Services-händelser.

En lista över exempelskript och självstudier finns i [Händelsekällan för Media Services](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Jobbrelaterade händelsetyper

Media Services avger de **jobbrelaterade** händelsetyper som beskrivs nedan. Det finns två kategorier för **jobbrelaterade** händelser: "Övervaka ändringar i jobbtillståndet" och "Övervakning av ändringar i jobbutdatatillstånd". 

Du kan registrera dig för alla händelser genom att prenumerera på händelsen JobStateChange. Du kan också prenumerera endast på specifika händelser (till exempel sluttillstånd som JobErrored, JobFinished och JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Övervaka ändringar av jobbtillstånd

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Få en händelse för alla jobbtillståndsändringar. |
| Microsoft.Media.Jobplanerad| Få en händelse när jobbet övergår till schemalagt tillstånd. |
| Microsoft.Media.Jobbbearbetning| Få en händelse när jobbet övergår till bearbetningstillstånd. |
| Microsoft.Media.JobCanceling| Få en händelse när jobbet övergår till att avbryta tillståndet. |
| Microsoft.Media.Avslutade jobb| Hämta en händelse när jobbet övergår till avslutat tillstånd. Detta är ett slutligt tillstånd som innehåller jobbutdata.|
| Microsoft.Media.JobCanceled| Få en händelse när jobbet övergår till inställt tillstånd. Detta är ett slutligt tillstånd som innehåller jobbutdata.|
| Microsoft.Media.JobErrored| Få en händelse när jobbet övergår till feltillstånd. Detta är ett slutligt tillstånd som innehåller jobbutdata.|

Se [Schemaexempel](#event-schema-examples) som följer.

### <a name="monitoring-job-output-state-changes"></a>Övervaka ändringar av jobbutdatatillstånd

Ett jobb kan innehålla flera jobbutdata (om du har konfigurerat transformeringen så att den har flera jobbutdata.) Om du vill spåra information om det enskilda jobbutdatatittningen lyssnar du efter en jobbutdataändringshändelse.

Varje **jobb** kommer att vara på en högre nivå än **JobOutput**, vilket jobbutdatahändelser får sparken inom ett motsvarande jobb. 

Felmeddelandena i `JobFinished`, `JobCanceled` `JobError` , matar ut de aggregerade resultaten för varje projektutdata – när alla är klara. Medan jobbet utdatahändelser brand som varje aktivitet avslutas. Om du till exempel har en kodningsutdata, följt av en Video Analytics-utdata, får du två händelser som aktiveras som jobbutdatahändelser innan den slutliga JobFinished-händelsen utlöses med aggregerade data.

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Hämta en händelse för alla ändringar i jobbutdatatillståndet. |
| Microsoft.Media.JobOutputeduled| Hämta en händelse när jobbutdata övergår till schemalagt tillstånd. |
| Microsoft.Media.JobOutputProcessing| Hämta en händelse när jobbutdata övergår till bearbetningstillstånd. |
| Microsoft.Media.JobOutputCanceling| Få en händelse när jobbutdata övergår till att avbryta tillståndet.|
| Klarering av Microsoft.Media.JobOutput| Hämta en händelse när jobbutdata övergår till avslutat tillstånd.|
| Microsoft.Media.JobOutputCanceled| Hämta en händelse när jobbutdata övergår till avbrutet tillstånd.|
| Microsoft.Media.JobOutputErrored| Hämta en händelse när jobbutdata övergår till feltillstånd.|

Se [Schemaexempel](#event-schema-examples) som följer.

### <a name="monitoring-job-output-progress"></a>Övervaka utvecklingen av jobbutdata

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Den här händelsen återspeglar jobbbearbetningsstatusen, från 0 % till 100 %. Tjänsten försöker skicka en händelse om det har skett 5 % eller större ökning av förloppsvärdet eller om det har gått mer än 30 sekunder sedan den senaste händelsen (pulsslag). Förloppsvärdet är inte garanterat att börja på 0%, eller att nå 100%, inte heller är det garanterat att öka i en konstant takt över tiden. Den här händelsen bör inte användas för att avgöra att bearbetningen har slutförts – du bör i stället använda tillståndsändringshändelserna.|

Se [Schemaexempel](#event-schema-examples) som följer.

## <a name="live-event-types"></a>Typer av liveevenemang

Media Services avger också de **livehändelsetyper** som beskrivs nedan. Det finns två **Live** kategorier för livehändelserna: händelser på strömnivå och händelser på spårnivå. 

### <a name="stream-level-events"></a>Händelser på strömnivå

Händelser på strömnivå höjs per ström eller anslutning. Varje händelse `StreamId` har en parameter som identifierar anslutningen eller strömmen. Varje ström eller anslutning har ett eller flera spår av olika typer. En anslutning från en kodare kan till exempel ha ett ljudspår och fyra videospår. Händelsetyperna för flödet är:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.liveEventConnectionRejected | Kodarens anslutningsförsök avvisas. |
| Microsoft.Media.LiveEventEncoder Ansluten | Kodaren upprättar anslutning till livehändelse. |
| Microsoft.Media.LiveEventEncoderDisconnected Microsoft.Media.LiveEventEncoderDisconnected Microsoft.Media.LiveEventEncoderDisconnected Microsoft. | Kodaren kopplas från. |

Se [Schemaexempel](#event-schema-examples) som följer.

### <a name="track-level-events"></a>Händelser på spårnivå

Händelser på spårnivå höjs per spår. 

> [!NOTE]
> Alla händelser på spårnivå utlöses när en live-kodare är ansluten.

Händelsetyperna på spårnivå är:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.liveEventIncomingDataChunkDroppeped | Media server droppar data segment eftersom det är för sent eller har en överlappande tidsstämpel (tidsstämpel för nya data segment är mindre än sluttiden för föregående datasegment). |
| Microsoft.Media.liveEventIncomingStreamReceived | Media server tar emot första datasegmentet för varje spår i strömmen eller anslutningen. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media server upptäcker ljud-och videoströmmar är ur synk. Använd som en varning eftersom användarupplevelsen kanske inte påverkas. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media server upptäcker någon av de två videoströmmar som kommer från externa kodare är ur synk. Använd som en varning eftersom användarupplevelsen kanske inte påverkas. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publiceras var 20:e sekund för varje spår när livehändelsen körs. Ger intag av hälsosammanfattning.<br/><br/>När kodaren först kopplades in fortsätter pulsslagshändelsen att avge var 20:e plats oavsett om kodaren fortfarande är ansluten eller inte. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected Microsoft.Media.LiveEventTrackDiscontinuityDetected Microsoft.Media.LiveEventTrackDiscontinuityDetected Microsoft. | Medieservern upptäcker avbrott i det inkommande spåret. |

Se [Schemaexempel](#event-schema-examples) som följer.

## <a name="event-schema-examples"></a>Exempel på händelseschema

### <a name="jobstatechange"></a>JobbstateChange

I följande exempel visas schemat för händelsen **JobStateChange:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| föregåendeStat | sträng | Jobbets tillstånd före händelsen. |
| state | sträng | Det nya tillståndet för jobbet som meddelas i det här fallet. Till exempel "Schemalagt: Jobbet är klart att starta" eller "Klart: Jobbet är klart".|

Där jobbtillståndet kan vara ett av värdena: *Köat*, *Schemalagd ,* *Bearbetning*, *Färdig*, *Fel*, *Avbruten*, *Avbryta*

> [!NOTE]
> *Kö kommer* bara att finnas i egenskapen **previousState** men inte i egenskapen **State.**

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>Jobbplanerad, Jobbbearbetning, JobCanceling

För varje icke-slutlig jobbtillståndsändring (till exempel JobScheduled, JobProcessing, JobCanceling) ser exempelschemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>Jobbavslutat, JobCanceled, JobErrored

För varje ändring av det slutliga jobbtillståndet (till exempel Jobbavslutad, JobCanceled, JobErrored) ser exempelschemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Utgångar | Matris | Hämtar jobbutdata.|

### <a name="joboutputstatechange"></a>JobbOutputStateChange

I följande exempel visas schemat för händelsen **JobOutputStateChange:**

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

För varje ändring av JobOutput-tillstånd ser exempelschemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>Utjobbutgång

Exempelschemat liknar följande:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

I följande exempel visas schemat för **liveeventconnectionrejected-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId (streamId) | sträng | Identifierare för strömmen eller anslutningen. Kodaren eller kunden är ansvarig för att lägga till detta ID i intingsadressen. |  
| intagUrl | sträng | Inta WEBBADRESS som tillhandahålls av live-händelsen. |  
| kodareIp | sträng | kodarens IP. |
| kodarePort | sträng | Port av kodaren från där denna ström kommer. |
| resultCode (resultatKod) | sträng | Anledningen till att anslutningen avvisades. Resultatkoderna visas i följande tabell. |

Du hittar felresultatkoderna i [felkoder för livehändelse](live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderAnsluten

I följande exempel visas schemat för **liveeventencoderConnected-händelsen:** 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId (streamId) | sträng | Identifierare för strömmen eller anslutningen. Kodaren eller kunden är ansvarig för att tillhandahålla detta ID i intingsadressen. |
| intagUrl | sträng | Inta WEBBADRESS som tillhandahålls av live-händelsen. |
| kodareIp | sträng | kodarens IP. |
| kodarePort | sträng | Port av kodaren från där denna ström kommer. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderAvslutas

I följande exempel visas schemat för **liveeventencoderDisconnected-händelsen:** 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId (streamId) | sträng | Identifierare för strömmen eller anslutningen. Kodaren eller kunden är ansvarig för att lägga till detta ID i intingsadressen. |  
| intagUrl | sträng | Inta WEBBADRESS som tillhandahålls av live-händelsen. |  
| kodareIp | sträng | kodarens IP. |
| kodarePort | sträng | Port av kodaren från där denna ström kommer. |
| resultCode (resultatKod) | sträng | Orsaken till att kodaren kopplas från. Det kan vara graciösa koppla eller från ett fel. Resultatkoderna visas i följande tabell. |

Du hittar felresultatkoderna i [felkoder för livehändelse](live-event-error-codes.md).

De graciösa frånkopplingsresultatkoderna är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| S_OK | Kodaren har kopplats från. |
| MPE_CLIENT_TERMINATED_SESSION | Kodaren frånkopplad (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodaren frånkopplad (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kommandot Kanalåterställning tas emot. |
| MPI_REST_API_CHANNEL_STOP | Kanalstoppkommandot mottaget. |
| MPI_REST_API_CHANNEL_STOP | Kanal som genomgår underhåll. |
| MPI_STREAM_HIT_EOF | EOF-strömmen skickas av kodaren. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDroppe

I följande exempel visas schemat för **liveeventIncomingDataChunkDropped-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType (spårtyp) | sträng | Typ av spår (Ljud / Video). |
| trackName (trackName) | sträng | Spårets namn. |
| Bitrate | heltal | Bitrate av banan. |
| timestamp | sträng | Tidsstämpeln för datasegmentet har tappats. |
| Tidsskalan | sträng | Tidsskalan för tidsstämpeln. |
| resultCode (resultatKod) | sträng | Orsaken till datasegmentet sjunker. **FragmentDrop_OverlapTimestamp** eller **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

I följande exempel visas schemat för **liveeventIncomingStreamReceived-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType (spårtyp) | sträng | Typ av spår (Ljud / Video). |
| trackName (trackName) | sträng | Namnet på spåret (antingen tillhandahålls av kodaren eller, när det gäller RTMP, servern genererar i *TrackType_Bitrate* format). |
| Bitrate | heltal | Bitrate av banan. |
| intagUrl | sträng | Inta WEBBADRESS som tillhandahålls av live-händelsen. |
| kodareIp | sträng  | kodarens IP. |
| kodarePort | sträng | Port av kodaren från där denna ström kommer. |
| timestamp | sträng | Första tidsstämpeln för det mottagna datasegmentet. |
| Tidsskalan | sträng | Tidsskala där tidsstämpel representeras. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

I följande exempel visas schemat för **liveeventIncomingStreamsOutOfSync-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| minLastTimestamp | sträng | Minst sista tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMinLastTimestamp | sträng | Typ av spår (ljud eller video) med minsta senaste tidsstämpel. |
| maxLastTimestamp | sträng | Maximalt av alla tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMaxLastTimestamp | sträng | Typ av spår (ljud eller video) med maximal sista tidsstämpel. |
| tidsskalaOfMinLastTimestamp| sträng | Hämtar den tidsskala i vilken "MinLastTimestamp" representeras.|
| tidsskalaAvMaxLastTimestamp| sträng | Hämtar den tidsskala som "MaxLastTimestamp" representeras i.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

I följande exempel visas schemat för **liveeventincomingVideoStreamsOutOfSync-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| firstTimestamp (förstaTidsstämpeln) | sträng | Tidsstämpel tas emot för en av spåren / kvalitetsnivåer av typ video. |
| firstDuration (förstaDurationen) | sträng | Varaktighet för datasegmentet med första tidsstämpeln. |
| andraTidsstämpel | sträng  | Tidsstämpel som tagits emot för någon annan spår-/kvalitetsnivå för typvideon. |
| secondDuration (andraDuration) | sträng | Varaktighet för datasegmentet med andra tidsstämpeln. |
| Tidsskalan | sträng | Tidsskala för tidsstämplar och varaktighet.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

I följande exempel visas schemat för **LiveEventIngestHeartbeat-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType (spårtyp) | sträng | Typ av spår (Ljud / Video). |
| trackName (trackName) | sträng | Namnet på spåret (antingen tillhandahålls av kodaren eller, när det gäller RTMP, servern genererar i *TrackType_Bitrate* format). |
| Bitrate | heltal | Bitrate av banan. |
| incomingBitrate | heltal | Beräknad bithastighet baserat på datasegment som kommer från kodaren. |
| lastTimestamp | sträng | Senaste tidsstämpeln fick för ett spår under de senaste 20 sekunderna. |
| Tidsskalan | sträng | Tidsskala där tidsstämplar uttrycks. |
| överlapparCount | heltal | Antalet datasegment hade överlappat tidsstämplar under de senaste 20 sekunderna. |
| diskontinuitetCount | heltal | Antal avbrott som observerats under de senaste 20 sekunderna. |
| icke-fallandeCount | heltal | Antal datasegment med tidsstämplar tidigare togs emot under de senaste 20 sekunderna. |
| oväntatBitrate | bool | Om förväntat och faktiskt bithastigheter skiljer sig åt med mer än tillåten gräns under de senaste 20 sekunderna. Det är sant om och endast om, incomingBitrate >= 2 * bithastighet eller incomingBitrate <= bitrate/2 ELLER IncomingBitrate = 0. |
| state | sträng | Tillståndet för live-händelsen. |
| Friska | bool | Anger om intjest är felfritt baserat på antal och flaggor. Felfri är sant om overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

I följande exempel visas schemat för **liveeventtrackdiscontinuityDetected-händelsen:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType (spårtyp) | sträng | Typ av spår (Ljud / Video). |
| trackName (trackName) | sträng | Namnet på spåret (antingen tillhandahålls av kodaren eller, när det gäller RTMP, servern genererar i *TrackType_Bitrate* format). |
| Bitrate | heltal | Bitrate av banan. |
| föregåendeTimestamp | sträng | Tidsstämpel för föregående fragment. |
| nyaTimestamp | sträng | Tidsstämpel för det aktuella fragmentet. |
| diskontinuitetGap | sträng | Gap mellan över två tidsstämplar. |
| Tidsskalan | sträng | Tidsskala där både tidsstämpel och avbrottsgap representeras. |

### <a name="common-event-properties"></a>Vanliga händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | EventGrid-avsnittet. Den här egenskapen har resurs-ID för Media Services-kontot. |
| Ämne | sträng | Resurssökvägen för medietjänstkanalen under Media Services-kontot. Genom att sammanfoga ämnet och ämnet får du resurs-ID för jobbet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. Till exempel "Microsoft.Media.JobStateChange". |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för Media Services. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

## <a name="next-steps"></a>Nästa steg

[Registrera dig för jobbtillståndsändringshändelser](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Se även

- [EventGrid .NET SDK som innehåller mediatjänsthändelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av mediatjänster händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Felkoder för livehändelse](live-event-error-codes.md)
