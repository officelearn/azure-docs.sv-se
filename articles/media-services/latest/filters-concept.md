---
title: Definiera filter i Azure Media Services
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma specifika avsnitt i en ström. Media Services skapar dynamiska manifest för att uppnå denna selektiva strömning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251471"
---
# <a name="filters"></a>Filter

När du levererar ditt innehåll till kunder (Live Streaming-händelser eller Video on Demand) kan din klient behöva mer flexibilitet än vad som beskrivs i standardtillgångens manifestfil. Azure Media Services erbjuder [dynamiska manifest](filters-dynamic-manifest-overview.md) baserat på fördefinierade filter. 

Filter är regler på serversidan som gör att kunderna kan göra saker som: 

- Spela bara upp en del av en video (i stället för att spela upp hela videon). Ett exempel:
  - Minska manifestet för att visa ett underklipp av en livehändelse ("sub-clip-filtrering" eller
  - Trimma början på en video ("trimma en video").
- Leverera endast angivna återgivningar och/eller angivna språkspår som stöds av enheten som används för att spela upp innehållet ("återgivningsfiltrering"). 
- Justera presentationsfönstret (DVR) för att ge en begränsad längd på DVR-fönstret i spelaren ("justera presentationsfönstret").

Med Media Services kan du skapa **kontofilter** och **tillgångsfilter** för ditt innehåll. Dessutom kan du associera dina förskapade filter med en **streamingpositionerare**.

## <a name="defining-filters"></a>Definiera filter

Det finns två typer av filter: 

* [Kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - kan tillämpas på alla tillgångar i Azure Media Services-kontot, har en livstid för kontot.
* [Tillgångsfilter](https://docs.microsoft.com/rest/api/media/assetfilters) (lokala) - kan endast tillämpas på en tillgång som filtret var associerat när det skapades, har en livstid för tillgången. 

**Kontofilter** och **tillgångsfiltertyper** har exakt samma egenskaper för att definiera/beskriva filtret. Förutom när du skapar **tillgångsfiltret**måste du ange det tillgångsnamn som du vill associera filtret med.

Beroende på ditt scenario bestämmer du vilken typ av filter som är lämpligare (tillgångsfilter eller kontofilter). Kontofilter är lämpliga för enhetsprofiler (återgivningsfiltrering) där tillgångsfilter kan användas för att trimma en viss tillgång.

Du använder följande egenskaper för att beskriva filtren. 

|Namn|Beskrivning|
|---|---|
|förstKvalitet|Filtrets första kvalitetbithastighet.|
|presentationTimeRange|Tidsintervallet för presentationen. Den här egenskapen används för filtrering av manifestets start-/slutpunkter, presentationsfönsterlängd och startpositionen för live. <br/>Mer information finns i [PresentationTimeRange](#presentationtimerange).|
|Spår|Spårvalsvillkoren. Mer information finns i [spår](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Använd den här egenskapen med **tillgångsfilter**. Det rekommenderas inte att ange egenskapen med **kontofilter**.

|Namn|Beskrivning|
|---|---|
|**endTimestamp**|Gäller video på begäran (VoD).<br/>För livestreaming-presentationen ignoreras den tyst och tillämpas när presentationen avslutas och strömmen blir VoD.<br/>Detta är ett långt värde som representerar en absolut slutpunkt för presentationen, avrundad till närmaste nästa GOP-start. Enheten är tidsskalan, så en endTimestamp på 1800000000 skulle vara i 3 minuter.<br/>Använd startTimestamp och endTimestamp för att trimma de fragment som ska finnas i spellistan (manifestet).<br/>StartTimestamp=40000000 och endTimestamp=100000000 med standardtidsskalan genererar till exempel en spellista som innehåller fragment från mellan 4 sekunder och 10 sekunder av VoD-presentationen. Om ett fragment sträcker sig över gränsen inkluderas hela fragmentet i manifestet.|
|**forceEndTimestamp**|Gäller endast direktuppspelning.<br/>Anger om egenskapen endTimestamp måste finnas. Om värdet är true måste endTimestamp anges eller så returneras en kod för felaktig begäran.<br/>Tillåtna värden: falskt, sant.|
|**liveBackoffDuration**|Gäller endast direktuppspelning.<br/> Det här värdet definierar den senaste aktiva positionen som en klient kan söka efter.<br/>Med den här egenskapen kan du fördröja uppspelningspositionen och skapa en buffert på serversidan för spelare.<br/>Enheten för den här egenskapen är tidsskala (se nedan).<br/>Den maximala varaktigheten för direktstöd är 300 sekunder (3000000000).<br/>Ett värde på 20000000000 innebär till exempel att det senaste tillgängliga innehållet är 20 sekunder försenat från den verkliga livekanten.|
|**presentationFönster**|Gäller endast direktuppspelning.<br/>Använd presentationFönstervarunder för att använda ett skjutfönster med fragment som ska inkluderas i en spellista.<br/>Enheten för den här egenskapen är tidsskala (se nedan).<br/>Ange till exempel presentationWindowDuration=12000000000 för att använda ett tvåminutersfönster. Media inom 2 minuter från live kanten kommer att ingå i spellistan. Om ett fragment sträcker sig över gränsen inkluderas hela fragmentet i spellistan. Minsta varaktighet för presentationsfönstret är 60 sekunder.|
|**startTimestamp**|Gäller video på begäran (VoD) eller livestreaming.<br/>Detta är ett långt värde som representerar en absolut startpunkt för strömmen. Värdet avrundas till närmaste nästa GOP start. Enheten är tidsskalan, så en startTimestamp på 150000000 skulle vara i 15 sekunder.<br/>Använd startTimestamp och endTimestampp för att trimma de fragment som ska finnas i spellistan (manifestet).<br/>StartTimestamp=40000000 och endTimestamp=100000000 med standardtidsskalan genererar till exempel en spellista som innehåller fragment från mellan 4 sekunder och 10 sekunder av VoD-presentationen. Om ett fragment sträcker sig över gränsen inkluderas hela fragmentet i manifestet.|
|**Tidsskalan**|Gäller för alla tidsstämplar och varaktigheter i ett presentationstidsintervall, som anges som antalet steg på en sekund.<br/>Standard är 10000000 - tio miljoner steg på en sekund, där varje ökning skulle vara 100 nanoseconds lång.<br/>Om du till exempel vill ange en startTimestamp på 30 sekunder använder du värdet 300000000 när du använder standardtidsskalan.|

### <a name="tracks"></a>Spår

Du anger en lista över filterspårsegenskaper (FilterTrackPropertyConditions) baserat på vilken spåren på din ström (Live Streaming eller Video on Demand) ska inkluderas i ett dynamiskt skapat manifest. Filtren kombineras med en logisk **OCH-** och **ELLER-åtgärd.**

Villkor för filterspårsegenskap beskriver spårtyper, värden (beskrivs i följande tabell) och åtgärder (Lika, ej.Filtergenskapsförhållanden beskriver spårtyper, värden (beskrivs i följande tabell) och åtgärder (Lika, ej.Filtergenskapsförhållanden beskriver spårtyper, värden (beskrivs i följande tabell) och åtgärder (Lika, ej.Filtergen 

|Namn|Beskrivning|
|---|---|
|**Bitrate**|Använd bithastigheten på spåret för filtrering.<br/><br/>Det rekommenderade värdet är ett intervall av bithastigheter, i bitar per sekund. Till exempel "0-2427000".<br/><br/>Obs: medan du kan använda ett visst bithastighetsvärde, till exempel 250000 (bitar per sekund), rekommenderas inte den här metoden, eftersom de exakta bithastigheterna kan variera från en tillgång till en annan.|
|**Fourcc**|Använd fourcc-värdet för spåret för filtrering.<br/><br/>Värdet är det första elementet i codecs-format, enligt vad som anges i [RFC 6381](https://tools.ietf.org/html/rfc6381). För närvarande stöds följande codec-enheter: <br/>För video: "avc1", "hev1", "hvc1"<br/>För ljud: "mp4a", "ec-3"<br/><br/>Om du vill bestämma FourCC-värdena för spår i en tillgång hämtar och undersöker du manifestfilen.|
|**Språk**|Använd språket i spåret för filtrering.<br/><br/>Värdet är taggen för ett språk som du vill inkludera, enligt vad som anges i RFC 5646. Till exempel "en".|
|**Namn**|Använd namnet på spåret för filtrering.|
|**Typ**|Använd typen av spår för filtrering.<br/><br/>Följande värden är tillåtna: "video", "ljud" eller "text".|

### <a name="example"></a>Exempel

I följande exempel definieras ett direktuppspelningsfilter: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associera filter med streamingpositionerare

Du kan ange en lista över [tillgångs- eller kontofilter](filters-concept.md) i [din streamingpositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). Den [dynamiska paketeraren](dynamic-packaging-overview.md) tillämpar den här listan med filter tillsammans med de som klienten anger i URL:en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i URL + filter som du anger på strömningspositioneraren. 

Se följande exempel:

* [Associera filter med streamingpositionerare - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associera filter med Streaming Locator - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Uppdatera filter
 
**Streaming Locators** är inte uppdateringsbara medan filter kan uppdateras. 

Det rekommenderas inte att uppdatera definitionen av filter som är associerade med en aktivt publicerad **streaming locator**, särskilt när CDN är aktiverat. Strömmande servrar och CDN-nätverk kan ha interna cacheminnen som kan resultera i inaktuella cachelagrade data som ska returneras. 

Om filterdefinitionen behöver ändras kan du överväga att skapa ett nytt filter och lägga till det i url:en för **direktuppspelningspositionerare** eller publicera en ny **strömningspositionerare** som refererar till filtret direkt.

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar hur du skapar filter programmässigt.  

- [Skapa filter med REST-API:er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

