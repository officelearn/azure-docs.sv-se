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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225419"
---
# <a name="filters"></a>Filter

När du levererar ditt innehåll till kunder (liveuppspelningshändelser eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services erbjuder [dynamiska manifest](filters-dynamic-manifest-overview.md) baserat på fördefinierade filter. 

Filtren är serversidan regler som tillåter dina kunder att till exempel: 

- Spela upp endast en del av en video (i stället för hela videon). Exempel:
  - Minska manifest för att visa ett underordnade klipp en direktsändning (”underklipp filtrering”), eller
  - Trimma början av en video (”trimmar en video”).
- Leverera endast den angivna återgivningar och/eller spårar för angivet språk som stöds av den enhet som används för att spela upp innehållet (”återgivningsfiltrering”). 
- Justera Presentation fönstret (DVR) för att tillhandahålla en begränsad tidslängd DVR-perioden i spelaren (”justera presentationsfönster”).

Media Services kan du skapa **konto filter** och **tillgången filter** för ditt innehåll. Dessutom kan du associera din förinställda filter med en **Strömningspositionerare**.

## <a name="defining-filters"></a>Definiera filter

Det finns två typer av filter: 

* [Kontot filter](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - kan tillämpas på alla tillgångar i Media Services-konto, har en livslängd på kontot.
* [Tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters) (lokal) – endast kan tillämpas på en tillgång som filtret associerades när den har skapats, har en livslängd på tillgången. 

**Kontot filter** och **tillgången filter** typer har exakt samma egenskaper för att definiera/beskriva filtret. Förutom när du skapar den **tillgången Filter**, måste du ange Tillgångsnamn som du vill associera filtret.

Beroende på ditt scenario bestämma du vilken typ av ett filter är lämpligare (tillgången Filter eller kontofilter). Kontofilter är lämpliga för enhetsprofiler (återgivningsfiltrering) där tillgången filter kan användas för att trimma en specifik tillgång.

Du kan använda följande egenskaper för att beskriva filtren. 

|Namn|Beskrivning|
|---|---|
|firstQuality|Första kvalitet bithastigheten av filtret.|
|presentationTimeRange|Tidsintervallet för presentation. Den här egenskapen används för att filtrera manifest start-/ slutpunkter, presentation längd och live startpositionen. <br/>Mer information finns i [PresentationTimeRange](#presentationtimerange).|
|spår|Spårar val av villkor. Mer information finns i [spår](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Använd den här egenskapen med **tillgången filter**. Det rekommenderas inte att ange egenskapen med **kontofilter**.

|Namn|Beskrivning|
|---|---|
|**endTimestamp**|Gäller för Video på begäran (VoD).<br/>För Live Streaming-presentation är det tyst ignoreras och tillämpas när det presentation upphört att gälla och dataströmmen blir VoD.<br/>Det här är ett långt värde som representerar en absolut slutpunkt i presentationen, avrundat till närmaste nästa GOP-start. Enheten är tidsskalan, så en endTimestamp av 1800000000 skulle vara för 3 minuter.<br/>Använd startTimestamp och endTimestamp att trimma fragment som ska ingå i listan (manifest).<br/>Till exempel startTimestamp = 40000000 och endTimestamp = 100000000 med hjälp av standard-tidsskalan genererar en spellista som innehåller fragment finns mellan 4 sekunder och 10 sekunder för VoD-presentationen. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.|
|**forceEndTimestamp**|Gäller endast direktsänd strömning.<br/>Anger om egenskapen endTimestamp måste finnas. Om värdet är true endTimestamp måste anges eller en felaktig begäran kod returneras.<br/>Tillåtna värden: FALSKT, SANT.|
|**liveBackoffDuration**|Gäller endast direktsänd strömning.<br/> Det här värdet anger den senaste direktsända positionen som en klient kan försöka.<br/>Med den här egenskapen kan kan du fördröja live uppspelning position och skapa en buffert på serversidan för spelare.<br/>Enheten för den här egenskapen är tidsskalan (se nedan).<br/>Högsta live backoff varaktighet är 300 sekunder (3000000000).<br/>Till exempel ett värde av 2000000000 innebär att det senaste innehållet är 20 sekunder fördröjd från möjlighet att få gränsen.|
|**presentationWindowDuration**|Gäller endast direktsänd strömning.<br/>Använda presentationWindowDuration för att tillämpa ett skjutfönster av fragment ska ingå i en spellista.<br/>Enheten för den här egenskapen är tidsskalan (se nedan).<br/>Till exempel presentationWindowDuration = 1200000000 för att tillämpa en glidande tvåminutersperiod. Media inom två minuter av live edge ska ingå i listan. Om ett fragment är gränsen, inkluderas hela fragment i listan. Minsta presentation fönstervaraktigheten är 60 sekunder.|
|**startTimestamp**|Gäller för Video på begäran (VoD) eller direktsänd strömning.<br/>Det här är ett långt värde som representerar en absolut startpunkt på dataströmmen. Värdet hämtar avrundat till närmaste nästa GOP början. Enheten är tidsskalan, så en startTimestamp av 150000000 skulle vara för 15 sekunder.<br/>Använd startTimestamp och endTimestampp att trimma fragment som ska ingå i listan (manifest).<br/>Till exempel startTimestamp = 40000000 och endTimestamp = 100000000 med hjälp av standard-tidsskalan genererar en spellista som innehåller fragment finns mellan 4 sekunder och 10 sekunder för VoD-presentationen. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.|
|**tidsskalan**|Gäller för alla tidsstämplar och varaktigheter i en Presentation tidsintervallet som angetts som antalet steg i en sekund.<br/>Standardvärdet är 10000000 - tio miljoner steg i en sekund, där varje säkerhetskopieringssteg är 100 nanosekunder lång.<br/>Till exempel använder du värdet 300000000 när du använder standard tidsskalan om du vill ange en startTimestamp på 30 sekunder.|

### <a name="tracks"></a>spår

Du anger en lista med filtervillkor spåra egenskapen (FilterTrackPropertyConditions) baserat som spårar för din dataström (Live Streaming eller Video på begäran) ska tas med i dynamiskt skapade manifest. Filter som kombineras med en logisk **AND** och **eller** igen.

Filtervillkor spåra egenskapen beskriver typer av spår, värden (som beskrivs i tabellen nedan) och åtgärder (Equal, NotEqual). 

|Namn|Beskrivning|
|---|---|
|**Bithastighet**|Använda bithastigheten av kursen för filtrering.<br/><br/>Det rekommenderade värdet är en mängd olika bithastigheter i bitar per sekund. Till exempel ”0-2427000”.<br/><br/>Obs: du kan använda en specifik bithastighet värde, till exempel 250000 (bitar per sekund), den här metoden rekommenderas inte, eftersom den exakta bithastighet kan variera från en tillgång till en annan.|
|**FourCC**|Använd FourCC värdet för spåret för filtrering.<br/><br/>Värdet är det första elementet i codec-format, som anges i [RFC 6381](https://tools.ietf.org/html/rfc6381). För närvarande stöds följande: <br/>Video: ”Avc1”, ”hev1”, ”hvc1”<br/>Ljud: ”Mp4a”, ”EG-3”<br/><br/>För att fastställa FourCC värdena för spår i en tillgång, hämta och granska manifestfilen.|
|**Språk**|Använd språket i kursen för filtrering.<br/><br/>Värdet är taggen för ett språk som du vill ska ingå som anges i RFC 5646. Till exempel ”SV”.|
|**Namn**|Använd namnet på kursen för filtrering.|
|**Typ**|Använd typ av kursen för filtrering.<br/><br/>Följande värden tillåts: ”video”, ”ljud” eller ”text”.|

### <a name="example"></a>Exempel

I följande exempel definierar ett Live Streaming filter: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associera filter med Strömningspositionerare

Du kan ange en lista över [tillgång eller konto filter](filters-concept.md) på din [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). Den [dynamisk Paketeraren](dynamic-packaging-overview.md) gäller den här listan över filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar en [dynamiska Manifest](filters-dynamic-manifest-overview.md), som grundar sig på filter i URL: en + filter som du anger på den Strömningspositionerare. 

Se följande exempel:

* [Associera filter med Strömningspositionerare – .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associera filter med Strömningspositionerare – CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Uppdatera filter
 
**Positionerare för direktuppspelning** kan inte uppdateras, medan filter kan uppdateras. 

Det rekommenderas inte att uppdatera definitionen av filter som är associerade med ett aktivt publicerade **Strömningspositionerare**, särskilt när CDN är aktiverat. Direktuppspelning av servrar och CDN-nät kan ha interna cacheminnen som kan leda till inaktuella cachelagrade data som ska returneras. 

Om filterdefinitionen behöver ändras bör du skapa ett nytt filter och lägger till den på den **Strömningspositionerare** URL eller publicera en ny **Strömningspositionerare** som refererar till filtret direkt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar visar hur du skapar filter programmässigt.  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

