---
title: Azure Event Grid-scheman för Media Services-händelser
description: Beskriver de egenskaper som har angetts för Media Services-händelser med Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058834"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid-scheman för Media Services-händelser

Den här artikeln innehåller scheman och egenskaper för Media Services-händelser.

En lista över exempel på skript och självstudier finns i [medietjänster händelsekälla](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Media Services genererar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Jobbets tillstånd ändras. |
| Microsoft.Media.LiveEventConnectionRejected | Kodarens anslutningsförsöket. |
| Microsoft.Media.LiveEventEncoderConnected | Kodaren upprättar anslutningen med live-händelse. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodaren kopplar från. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Media-servern avslutar datagruppen eftersom den är för sent eller har ett överlappande tidsstämpel (tidsstämpel av nya data-segmentet är mindre än sluttiden tidigare datagruppen). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media-servern tar emot första data-segmentet för varje spår i stream eller anslutning. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-server identifierar ljud och video strömmar är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-server identifierar någon av de två videoflöden som kommer från externa kodaren är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicerade var 20: e sekund för varje spår när direktsändningen körs. Tillhandahåller mata in hälsoöversikt. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Media-server identifierar avbrott i den inkommande kursen. |

Det finns två kategorier för de **Live** händelser: stream på händelser och spåra på händelser. 

Stream-nivå händelser aktiveras per stream eller anslutning. Varje händelse har en `StreamId` parameter som identifierar anslutning eller stream. Varje stream eller anslutningen har en eller flera spårar av olika typer. En anslutning från en kodare kan till exempel ha ett ljudspår och fyra video spår. Händelsetyper stream är:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Spåra på händelser aktiveras per spår. Spåra händelsetyper är:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

I följande exempel visas schemat för den **JobStateChange** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| previousState | sträng | Status för jobbet innan händelsen. |
| state | sträng | Det nya läget för jobbet som ett meddelande i den här händelsen. Till exempel ”i kö: jobbet väntar på resurser” eller ”schemalagd: jobbet är redo att börja”.|

Där jobbets status kan vara något av värdena: *i kö*, *schemalagd*, *bearbetning*, *slutfört*, *fel*, *Har avbrutits*, *avbryts*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

I följande exempel visas schemat för den **LiveEventConnectionRejected** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| StreamId | sträng | Identifierare för stream eller anslutning. Kodaren eller kunden är ansvarig för att lägga till detta ID i URL: en inmatning. |  
| IngestUrl | sträng | Mata in URL: en som tillhandahålls av live-händelse. |  
| EncoderIp | sträng | IP-Adressen för kodaren. |
| EncoderPort | sträng | Porten till kodaren från där den här strömmen är på gång. |
| Resultatkod | sträng | Orsaken till anslutningen avvisades. Resultatkoderna visas i följande tabell. |

Resultatkoderna är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Felaktig infognings-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodaren IP finns inte rotcertifikatuppgifterna i IP-lista över konfigurerade tillåtna |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodaren kunde inte skicka metadata om dataströmmen. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Codec som angetts stöds inte. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Tog emot ett fragment innan du tar emot och rubrik för dataströmmen. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Antalet egenskaper som angetts överskrider tillåtna maxgränsen. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Sammanställda bithastighet överskrider högsta tillåtna gränsen. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpel för video- eller ljudinnehåll FLVTag kan inte användas med RTMP-kodare. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

I följande exempel visas schemat för den **LiveEventEncoderConnected** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| StreamId | sträng | Identifierare för stream eller anslutning. Kodaren eller kunden ansvarar för att tillhandahålla detta ID i URL: en för inmatning. |
| IngestUrl | sträng | Mata in URL: en som tillhandahålls av live-händelse. |
| EncoderIp | sträng | IP-Adressen för kodaren. |
| EncoderPort | sträng | Porten till kodaren från där den här strömmen är på gång. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

I följande exempel visas schemat för den **LiveEventEncoderDisconnected** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| StreamId | sträng | Identifierare för stream eller anslutning. Kodaren eller kunden är ansvarig för att lägga till detta ID i URL: en inmatning. |  
| IngestUrl | sträng | Mata in URL: en som tillhandahålls av live-händelse. |  
| EncoderIp | sträng | IP-Adressen för kodaren. |
| EncoderPort | sträng | Porten till kodaren från där den här strömmen är på gång. |
| Resultatkod | sträng | Orsaken till kodaren kopplar från. Det kan vara korrekt koppla från eller från ett fel. Resultatkoderna visas i följande tabell. |

Resultatkoder för fel är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | RTMP sessionen avbröts efter att ha varit inaktiv i tillåtna tidsgränsen. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpel för video- eller ljudinnehåll FLVTag kan inte användas med RTMP-kodare. |
| MPE_CAPACITY_LIMIT_REACHED | Kodare som skickar data för snabbt. |
| Okänd felkoder | Dessa felkoder kan variera mellan minnesfel till dubbla poster i hash-kartan. |

Resultatkoder för korrekt frånkoppling är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| S_OK | Kodaren har frånkopplats. |
| MPE_CLIENT_TERMINATED_SESSION | Kodaren frånkopplad (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodaren frånkopplad (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kanalen Återställ kommando tas emot. |
| MPI_REST_API_CHANNEL_STOP | Kanalen stoppkommando togs emot. |
| MPI_REST_API_CHANNEL_STOP | Kanalen underhållsarbeten. |
| MPI_STREAM_HIT_EOF | EOF stream skickas av kodaren. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

I följande exempel visas schemat för den **LiveEventIncomingDataChunkDropped** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| TrackType | sträng | Typ av spåret (ljud / Video). |
| TrackName | sträng | Namnet på kursen. |
| Bithastighet | heltal | Bithastighet av kursen. |
| Tidsstämpel | sträng | Tidsstämpel datagruppen släppts. |
| tidsskalan | sträng | Tidsskalan för tidsstämpeln. |
| Resultatkod | sträng | Orsaken till i listrutan för data-segmentet. **FragmentDrop_OverlapTimestamp** eller **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

I följande exempel visas schemat för den **LiveEventIncomingStreamReceived** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| TrackType | sträng | Typ av spåret (ljud / Video). |
| TrackName | sträng | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| Bithastighet | heltal | Bithastighet av kursen. |
| IngestUrl | sträng | Mata in URL: en som tillhandahålls av live-händelse. |
| EncoderIp | sträng  | IP-Adressen för kodaren. |
| EncoderPort | sträng | Porten till kodaren från där den här strömmen är på gång. |
| Tidsstämpel | sträng | Första tidsstämpel datagruppen som tagits emot. |
| tidsskalan | sträng | Tidsskalan där timestamp representeras. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

I följande exempel visas schemat för den **LiveEventIncomingStreamsOutOfSync** händelse: 

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
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| MinLastTimestamp | sträng | Minst senaste tidsstämplar bland alla spår (ljud eller video). |
| TypeOfTrackWithMinLastTimestamp | sträng | Typ av spåra (ljud eller video) med minsta senaste tidsstämpel. |
| MaxLastTimestamp | sträng | Högst alla tidsstämplar bland alla spår (ljud eller video). |
| TypeOfTrackWithMaxLastTimestamp | sträng | Typ av spåra (ljud eller video) med maximalt senaste tidsstämpel. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

I följande exempel visas schemat för den **LiveEventIncomingVideoStreamsOutOfSync** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| FirstTimestamp | sträng | Tidsstämpel som togs emot för en av spår/kvalitetsnivå av typen video. |
| FirstDuration | sträng | Varaktighet datagruppen med första tidsstämpel. |
| SecondTimestamp | sträng  | Tidsstämpel som togs emot för vissa andra spåra/kvalitetsnivå av typen video. |
| SecondDuration | sträng | Varaktighet datagruppen med andra tidsstämpel. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

I följande exempel visas schemat för den **LiveEventIngestHeartbeat** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| TrackType | sträng | Typ av spåret (ljud / Video). |
| TrackName | sträng | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| Bithastighet | heltal | Bithastighet av kursen. |
| IncomingBitrate | heltal | Beräknad bithastighet baserat på datasegment som kommer från kodaren. |
| LastTimestamp | sträng | Senaste tidsstämpel togs emot för ett spår i senaste 20 sekunder. |
| tidsskalan | sträng | Tidsskalan där tidsstämplar uttrycks. |
| OverlapCount | heltal | Antal datasegment hade överlappas tidsstämplar i senaste 20 sekunder. |
| DiscontinuityCount | heltal | Antal avbrott som observerats under de senaste 20 sekunder. |
| NonIncreasingCount | heltal | Antal datasegment med tidsstämplar som tidigare har tagits emot senaste 20 sekunder. |
| UnexpectedBitrate | Bool | Om förväntade och faktiska bithastighet skiljer sig åt i mer än tillåtna gränsen på senaste 20 sekunder. Det är SANT om och bara om, IncomingBitrate > = 2 * bithastighet eller IncomingBitrate < = bithastighet/2 eller IncomingBitrate = 0. |
| Status | sträng | Status för live-händelse. |
| Felfri | Bool | Anger om mata in är felfri baserat på antalet och flaggor. Felfri är SANT om OverlapCount = 0 & & DiscontinuityCount = 0 & & NonIncreasingCount = 0 & & UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

I följande exempel visas schemat för den **LiveEventTrackDiscontinuityDetected** händelse: 

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

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| TrackType | sträng | Typ av spåret (ljud / Video). |
| TrackName | sträng | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| Bithastighet | heltal | Bithastighet av kursen. |
| PreviousTimestamp | sträng | Tidsstämpel för det föregående fragmentet. |
| NewTimestamp | sträng | Tidsstämpel för det aktuella fragmentet. |
| DiscontinuityGap | sträng | Mellanrummet mellan ovan två tidsstämplar. |
| tidsskalan | sträng | Tidsskalan i vilka både tidsstämpel och avbrott mellanrum representeras. |

## <a name="common-event-properties"></a>Gemensamma händelseegenskaper

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | EventGrid-avsnittet. Den här egenskapen har resurs-ID för Media Services-kontot. |
| Ämne | sträng | Resursens sökväg för Media Services-kanalen i Media Services-kontot. Sammanfoga det avsnittet och ämne ger du resursen med ID för jobbet. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. Till exempel ”Microsoft.Media.JobStateChange”. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Media Services händelsedata. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

## <a name="next-steps"></a>Nästa steg

[Registrera dig för jobbet tillståndsändringshändelser](job-state-events-cli-how-to.md)