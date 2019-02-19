---
title: Definiera filter i Azure Media Services
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att uppnå den här selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405740"
---
# <a name="define-account-filters-and-asset-filters"></a>Definiera kontofilter och tillgången filter  

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. 

Filtren är serversidan regler som tillåter dina kunder att till exempel: 

- Spela upp endast en del av en video (i stället för hela videon). Exempel:
  - Minska manifest för att visa ett underordnade klipp en direktsändning (”underklipp filtrering”), eller
  - Trimma början av en video (”trimmar en video”).
- Leverera endast den angivna återgivningar och/eller spårar för angivet språk som stöds av den enhet som används för att spela upp innehållet (”återgivningsfiltrering”). 
- Justera Presentation fönstret (DVR) för att tillhandahålla en begränsad tidslängd DVR-perioden i spelaren (”justera presentationsfönster”).

Media Services erbjuder [dynamiska manifest](filters-dynamic-manifest-overview.md) baserat på fördefinierade filter. När du har definierat filter kan klienterna använda dem i strömnings-URL. Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definiera filter

Det finns två typer av tillgången filter: 

* [Kontot filter](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - kan tillämpas på alla tillgångar i Media Services-konto, har en livslängd på kontot.
* [Tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters) (lokal) – endast kan tillämpas på en tillgång som filtret associerades när den har skapats, har en livslängd på tillgången. 

[Kontot Filter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången Filter](https://docs.microsoft.com/rest/api/media/assetfilters) typer har exakt samma egenskaper för att definiera/beskriva filtret. Förutom när du skapar den **tillgången Filter**, måste du ange Tillgångsnamn som du vill associera filtret.

Beroende på ditt scenario bestämma du vilken typ av ett filter är lämpligare (tillgången Filter eller kontofilter). Kontofilter är lämpliga för enhetsprofiler (återgivningsfiltrering) där tillgången filter kan användas för att trimma en specifik tillgång.

Du kan använda följande egenskaper för att beskriva filtren. 

|Namn|Beskrivning|
|---|---|
|firstQuality|Första kvalitet bithastigheten av filtret.|
|presentationTimeRange|Tidsintervallet för presentation. Den här egenskapen används för att filtrera manifest start-/ slutpunkter, presentation längd och live startpositionen. <br/>Mer information finns i [PresentationTimeRange](#PresentationTimeRange).|
|spår|Spårar val av villkor. Mer information finns i [spår](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Använd den här egenskapen med **tillgången filter**. Det rekommenderas inte att ange egenskapen med **kontofilter**.

|Namn|Beskrivning|
|---|---|
|**endTimestamp**|Absolut slutet tid gräns. Gäller för Video på begäran (VoD). För Live-presentationen är det tyst ignoreras och tillämpas när det presentation upphört att gälla och dataströmmen blir VoD.<br/><br/>Värdet som representerar en absolut slutpunkt på dataströmmen. Den hämtar avrundas till närmaste nästa GOP början.<br/><br/>Använd StartTimestamp och EndTimestamp för att trimma listan (manifest). Till exempel StartTimestamp = 40000000 och EndTimestamp = 100000000 genererar en spellista som innehåller media mellan StartTimestamp och EndTimestamp. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.<br/><br/>Se även de **forceEndTimestamp** definition som följer.|
|**forceEndTimestamp**|Gäller för direktfilter.<br/><br/>**forceEndTimestamp** är ett booleskt värde som indikerar huruvida **endTimestamp** har angetts till ett giltigt värde. <br/><br/>Om värdet är **SANT**, **endTimestamp** värdet ska anges. Om det inte anges returneras en felaktig begäran.<br/><br/>Om du vill definiera ett filter som börjar på 5 minuter in indata video, och gäller fram till slutet av strömmen, anger du **forceEndTimestamp** till FALSKT och utelämna inställningen **endTimestamp**.|
|**liveBackoffDuration**|Gäller bara för Live. Egenskapen används för att definiera live uppspelning position. Med den här regeln, kan du fördröja live uppspelning position och skapa en buffert på serversidan för spelare. LiveBackoffDuration är i förhållande till den direktsända positionen. Den maximala live backoff varaktigheten är 300 sekunder.|
|**presentationWindowDuration**|Gäller för Live. Använd **presentationWindowDuration** att tillämpa ett skjutfönster på listan. Till exempel presentationWindowDuration = 1200000000 för att tillämpa en glidande tvåminutersperiod. Media inom två minuter av live edge ska ingå i listan. Om ett fragment är gränsen, inkluderas hela fragment i listan. Minsta presentation fönstervaraktigheten är 60 sekunder.|
|**startTimestamp**|Gäller för VoD eller Live strömmar. Värdet som representerar en absolut startpunkt på dataströmmen. Värdet hämtar avrundat till närmaste nästa GOP början.<br/><br/>Använd **startTimestamp** och **endTimestamp** att trimma listan (manifest). Till exempel startTimestamp = 40000000 och endTimestamp = 100000000 genererar en spellista som innehåller media mellan StartTimestamp och EndTimestamp. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.|
|**tidsskalan**|Gäller för VoD eller Live strömmar. Tidsskalan som används av tidsstämplarna och varaktigheter som anges ovan. Standard tidsskalan är 10000000. Du kan använda ett alternativt tidsskalan. Standardvärdet är 10000000 HNS (100 nanosekunder).|

### <a name="tracks"></a>spår

Du anger en lista med filtervillkor spåra egenskapen (FilterTrackPropertyConditions) baserat som spårar för din dataström (Live eller Video på begäran) ska tas med i dynamiskt skapade manifest. Filter som kombineras med en logisk **AND** och **eller** igen.

Filtervillkor spåra egenskapen beskriver typer av spår, värden (som beskrivs i tabellen nedan) och åtgärder (Equal, NotEqual). 

|Namn|Beskrivning|
|---|---|
|**Bithastighet**|Använda bithastigheten av kursen för filtrering.<br/><br/>Det rekommenderade värdet är en mängd olika bithastigheter i bitar per sekund. Till exempel ”0-2427000”.<br/><br/>Obs: du kan använda en specifik bithastighet värde, till exempel 250000 (bitar per sekund), den här metoden rekommenderas inte, eftersom den exakta bithastighet kan variera från en tillgång till en annan.|
|**FourCC**|Använd FourCC värdet för spåret för filtrering.<br/><br/>Värdet är det första elementet i codec-format, som anges i [RFC 6381](https://tools.ietf.org/html/rfc6381). För närvarande stöds följande: <br/>Video: ”Avc1”, ”hev1”, ”hvc1”<br/>Ljud: ”Mp4a”, ”EG-3”<br/><br/>Att fastställa FourCC värdena för spår i en tillgång [få och granska manifestfilen](#get-and-examine-manifest-files).|
|**Språk**|Använd språket i kursen för filtrering.<br/><br/>Värdet är taggen för ett språk som du vill ska ingå som anges i RFC 5646. Till exempel ”SV”.|
|**Namn**|Använd namnet på kursen för filtrering.|
|**Typ**|Använd typ av kursen för filtrering.<br/><br/>Följande värden tillåts: ”video”, ”ljud” eller ”text”.|

## <a name="example"></a>Exempel

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar visar hur du skapar filter programmässigt.  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

