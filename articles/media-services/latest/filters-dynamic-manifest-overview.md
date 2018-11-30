---
title: Filter och Azure Media Services dynamiska manifest | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att arkivera den här selektiva strömning.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 05c089c0e92672b3602bd9c7bfb3263c56db76ef
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633307"
---
# <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest

När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera kontofilter och tillgången filter för ditt innehåll. 

Filtren är serversidan regler som tillåter dina kunder att till exempel: 

- Spela upp endast en del av en video (i stället för hela videon). Exempel:

    - Minska manifest för att visa ett underklipp för en direktsänd händelse (”underklipp filtrering”), eller
    - Trimma början av en video (”trimmar en video”).

- Leverera endast den angivna återgivningar och/eller spårar för angivet språk som stöds av den enhet som används för att spela upp innehållet (”återgivningsfiltrering”). 
- Justera Presentation fönstret (DVR) för att tillhandahålla en begränsad tidslängd DVR-perioden i spelaren (”justera presentationsfönster”).

Det här avsnittet beskriver [begrepp](#concepts) och [visar filtrerar definitioner](#definitions). Den lämnar sedan över information om [vanliga scenarier](#common-scenarios). I slutet av artikeln hittar du länkar som visar hur du skapar filter programmässigt.  

## <a name="concepts"></a>Begrepp

### <a name="dynamic-manifests"></a>Dynamiska manifest

Media Services erbjuder **dynamiska manifest** baserat på fördefinierade [filter](#filters). När du har definierat filter kan dina klienter använda dem för att strömma en specifik återgivning eller underordnade klipp av videon. Filter ska anges i strömnings-URL. Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: Apple HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming. 

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> Dynamiska manifest ändras inte tillgången och standard-manifest för tillgången. Klienten kan du begära en dataström med eller utan filter. 
> 
> 

### <a name="manifest-files"></a>Manifestfiler

När du koda en tillgång för strömning med anpassad bithastighet en **manifest** () spellistefil skapas (filen är text- eller XML-baserade). Den **manifest** -filen innehåller metadata för direktuppspelning till exempel: spåra typ (ljud, video eller text), spåra namn, start-och sluttid, med flera bithastigheter (Egenskaper), spåra språk, presentationsfönstret (Hoppande fönster med fast varaktighet), video-codec ( FourCC). Det instruerar också spelaren att hämta nästa fragment genom att tillhandahålla information om nästa kan spelas upp video fragmenten tillgängliga och deras plats. Fragment (eller segment) är de faktiska ”mängder” en videoinnehåll.

Här är ett exempel på en HLS-manifestfilen: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Hämta och granska manifestfiler

Du anger en lista med filtervillkor spåra egenskap baserat som spårar för din dataström (Live eller Video på begäran) ska tas med i dynamiskt skapade manifest. Om du vill hämta och granska egenskaperna för spåren behövs för att läsa Smooth Streaming-manifestet först.

Den [överföra, koda och strömma filer med .NET](stream-files-tutorial-with-api.md) kursen visar hur du skapar en strömmande URL: er med .NET (visas den [att skapa URL: er](stream-files-tutorial-with-api.md#get-streaming-urls) avsnittet). Om du kör appen, någon av webbadresser pekar Smooth Streaming-manifest: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Kopiera och klistra in Webbadressen i adressfältet i en webbläsare. Filen laddas ned. Du kan öppna den i en textredigerare som du önskar.

REST-exempel finns i [överföra, koda och strömma filer med REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Övervaka bithastigheten av en videoström

Du kan använda den [Demonstrationssida för Azure Media Player](http://aka.ms/amp) att övervaka bithastigheten av en videoström. Demo-sidan visar diagnostik information i den **diagnostik** fliken:

![Azure Media Player-diagnostik][amp_diagnostics]

## <a name="defining-filters"></a>Definiera filter

Det finns två typer av tillgången filter: 

* [Kontot filter](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - kan tillämpas på alla tillgångar i Media Services-konto, har en livslängd på kontot.
* [Tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters) (lokal) – endast kan tillämpas på en tillgång som filtret associerades när den har skapats, har en livslängd på tillgången. 

[Kontot Filter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången Filter](https://docs.microsoft.com/rest/api/media/assetfilters) typer har exakt samma egenskaper för att definiera/beskriva filtret. Förutom när du skapar den **tillgången Filter**, måste du ange Tillgångsnamn som du vill associera filtret.

Beroende på ditt scenario bestämma du vilken typ av ett filter är lämpligare (tillgången Filter eller kontofilter). Kontofilter är lämpliga för enhetsprofiler (återgivningsfiltrering) där tillgången filter kan användas för att trimma en specifik tillgång.

Du kan använda följande egenskaper för att beskriva filtren. 

|Namn|Beskrivning|
|---|---|
|firstQuality|Första kvalitet bithastigheten av filtret.|
|PresentationTimeRange|Tidsintervallet för presentation. Den här egenskapen används för att filtrera manifest start-/ slutpunkter, presentation längd och live startpositionen. <br/>Mer information finns i [PresentationTimeRange](#PresentationTimeRange).|
|spår|Spårar val av villkor. Mer information finns i [spår](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Använd den här egenskapen med **tillgången filter**. Det rekommenderas inte att ange egenskapen med **kontofilter**.

|Namn|Beskrivning|
|---|---|
|**endTimestamp**|Absolut slutet tid gräns. Gäller för Video på begäran (VoD). För Live-presentationen är det tyst ignoreras och tillämpas när det presentation upphört att gälla och dataströmmen blir VoD.<br/><br/>Värdet som representerar en absolut slutpunkt på dataströmmen. Den hämtar avrundas till närmaste nästa GOP början.<br/><br/>Använd StartTimestamp och EndTimestamp för att trimma listan (manifest). Till exempel StartTimestamp = 40000000 och EndTimestamp = 100000000 genererar en spellista som innehåller media mellan StartTimestamp och EndTimestamp. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.<br/><br/>Se även de **forceEndTimestamp** definition som följer.|
|**forceEndTimestamp**|Gäller för direktfilter.<br/><br/>**forceEndTimestamp** är ett booleskt värde som indikerar huruvida **endTimestamp** har angetts till ett giltigt värde. <br/><br/>Om värdet är **SANT**, **endTimestamp** värdet ska anges. Om det inte anges returneras en felaktig begäran.<br/><br/>Om du vill definiera ett filter som börjar på 5 minuter in indata video, och gäller fram till slutet av strömmen, anger du **forceEndTimestamp** till FALSKT och utelämna inställningen **endTimestamp**.|
|**liveBackoffDuration**|Gäller bara för Live. Egenskapen används för att definiera live uppspelning position. Med den här regeln, kan du fördröja live uppspelning position och skapa en buffert på serversidan för spelare. LiveBackoffDuration är i förhållande till den direktsända positionen. Den maximala live backoff varaktigheten är 60 sekunder.|
|**presentationWindowDuration**|Gäller för Live. Använd **presentationWindowDuration** att tillämpa ett skjutfönster på listan. Till exempel presentationWindowDuration = 1200000000 för att tillämpa en glidande tvåminutersperiod. Media inom två minuter av live edge ska ingå i listan. Om ett fragment är gränsen, inkluderas hela fragment i listan. Minsta presentation fönstervaraktigheten är 120 sekunder.|
|**startTimestamp**|Gäller för VoD eller Live strömmar. Värdet som representerar en absolut startpunkt på dataströmmen. Värdet hämtar avrundat till närmaste nästa GOP början.<br/><br/>Använd **startTimestamp** och **endTimestamp** att trimma listan (manifest). Till exempel startTimestamp = 40000000 och endTimestamp = 100000000 genererar en spellista som innehåller media mellan StartTimestamp och EndTimestamp. Om ett fragment är gränsen, inkluderas hela fragment i manifestet.|
|**tidsskalan**|Gäller för VoD eller Live strömmar. Tidsskalan som används av tidsstämplarna och varaktigheter som anges ovan. Standard tidsskalan är 10000000. Du kan använda ett alternativt tidsskalan. Standardvärdet är 10000000 HNS (100 nanosekunder).|

### <a name="tracks"></a>Spår

Du anger en lista med filtervillkor spåra egenskapen (FilterTrackPropertyConditions) baserat som spårar för din dataström (Live eller Video på begäran) ska tas med i dynamiskt skapade manifest. Filter som kombineras med en logisk **AND** och **eller** igen.

Filtervillkor spåra egenskapen beskriver typer av spår, värden (som beskrivs i tabellen nedan) och åtgärder (Equal, NotEqual). 

|Namn|Beskrivning|
|---|---|
|**Bithastighet**|Använda bithastigheten av kursen för filtrering.<br/><br/>Det rekommenderade värdet är en mängd olika bithastigheter i bitar per sekund. Till exempel ”0-2427000”.<br/><br/>Obs: du kan använda en specifik bithastighet värde, till exempel 250000 (bitar per sekund), den här metoden rekommenderas inte, eftersom den exakta bithastighet kan variera från en tillgång till en annan.|
|**FourCC**|Använd FourCC värdet för spåret för filtrering.<br/><br/>Värdet är det första elementet i codec-format, som anges i [RFC 6381](https://tools.ietf.org/html/rfc6381). För närvarande stöds följande: <br/>Video: ”Avc1”, ”hev1”, ”hvc1”<br/>Ljud: ”Mp4a”, ”EG-3”<br/><br/>Att fastställa FourCC värdena för spår i en tillgång [få och granska manifestfilen](#get-and-examine-manifest-files).|
|**Språk**|Använd språket i kursen för filtrering.<br/><br/>Värdet är taggen för ett språk som du vill ska ingå som anges i RFC 5646. Till exempel ”SV”.|
|**Namn**|Använd namnet på kursen för filtrering.|
|**Typ**|Använd typ av kursen för filtrering.<br/><br/>Följande värden tillåts: ”video”, ”ljud” eller ”text”.|

### <a name="example"></a>Exempel

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

## <a name="rendition-filtering"></a>Återgivningsfiltrering

Du kan välja att koda din tillgång till flera kodning profiler (H.264 baslinje, H.264 hög, AACL, AACH, Dolby Digital Plus) och flera olika bithastigheter kvalitet. Dock stöder inte alla klientenheter din tillgångs-profiler och bithastigheter för utdata. Äldre Android-enheter stöder till exempel bara H.264 baslinje + AACL. Skicka högre bithastighet till en enhet, vilket inte kan hämta fördelarna, slösar nätverksbandbredd och alla beräkningar som enheten. Sådana enheter måste avkoda alla angivna information endast för Nedskalning för visning.

Med dynamiska Manifest kan du skapa enhetsprofiler som mobila enheter, konsolen HD/SD osv och inkludera spårar och egenskaper som du vill ska vara en del av varje profil.

![Återgivning filtrering exempel][renditions2]

I följande exempel har en kodare använts för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p 1080p). Den kodade tillgången kan dynamiskt paketeras i någon av de följande protokollen med direktuppspelning: HLS, MPEG DASH och Smooth.  Längst ned i diagrammet visas HLS-manifest för tillgången utan filter (den innehåller alla sju återgivningar).  HLS-manifest som ett filter med namnet ”ott” användes visas i nedre vänstra hörnet. Filtret ”ott” anger för att ta bort alla bithastigheter för utdata nedan 1 Mbit/s, vilket resulterade i kvalitetsnivå längst ned två tas bort i svaret. I nederkant högra visas HLS manifestet som ett filter med namnet ”mobil” användes. Filtret ”mobil” anger för att ta bort återgivningar där den är större än 720p, vilket resulterade i två 1080p återgivningar tas bort.

![Återgivningsfiltrering][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska, osv. Vanligtvis Player SDK-chefer standard ljudspår val och tillgängliga ljud spårar per användarens val. Är det svårt att utveckla sådana Player SDK: er, den kräver olika implementeringar över enhetsspecifika spelarramverk. Dessutom begränsas Player API: er på vissa plattformar och inkluderar inte ljudspår funktion där användare inte kan markera eller ändra standard ljudspår. Du kan styra beteendet genom att skapa filter som endast innehåller önskade ljud språk med tillgången filter.

![Språk spårar filtrering][language_filter]

## <a name="trimming-start-of-an-asset"></a>Ta bort början av en tillgång
I de flesta liveuppspelningshändelser kör operatörer vissa tester innan den faktiska händelsen. De kan till exempel innehålla en bakgrundsbild så här innan händelsen: ”programmet börjar tillfälligt”. Om programmet arkivering, test- och statiska data också arkiveras och ingår i presentationen. Den här informationen bör dock inte att visas för klienterna. Du kan skapa en starttidsfilter och ta bort oönskade data från manifestet med dynamiska Manifest.

![Ta bort start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underklipp (vyer) från en live-arkivet
Många direktsändningar är långvariga och live-arkivet kan innehålla flera händelser. När livehändelsen är slut, kanske sändningsföretag vill dela upp live-arkivet till logiska programmet startas och stoppa sekvenser. Därefter publicera separat dessa virtuella program utan efter bearbetning av live-arkivet och inte skapa separata resurser (som inte får fördelen med de befintliga cachelagrade fragment i de CDN-nät). Exempel på sådana virtuella program är kvartal football eller basket spel, innings i basket eller enskilda händelser för alla sport-program.

Med dynamiska Manifest kan du skapa filter med start-och sluttider och skapa virtuella vyer över dina live-arkivet. 

![Underklipp filter][subclip_filter]

Filtrerade tillgång:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)
Azure Media Services erbjuder för närvarande, cirkulär Arkiv där varaktigheten kan konfigureras mellan 5 minuter – 25: e timme. Manifest filtrering kan användas för att skapa en löpande DVR-perioden över upp arkivet, utan att ta bort media. Det finns många scenarier där sändningsföretag vill tillhandahålla en begränsad DVR-perioden för att flytta med live edge och samtidigt hålla ett större arkivering fönster. En broadcast kanske vill använda de data som ligger utanför fönstret DVR att markera klipp eller kan de vilja ange olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hantera inte exempelvis stora DVR windows (du kan ha en 2-minut DVR-perioden för mobila enheter och en timme för stationära klienter).

![DVR-perioden][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (direktsända positionen)
Manifest filtrering kan användas för att ta bort några sekunder från live gränsen för en live. Filtrering kan sändningsföretag se presentationen på förhandsversion publikationen punkt och skapa annonsen infogas punkter innan användarna får den dataström (backas upp av med 30 sekunder). Programleverantörer kan sedan skicka dessa annonser till sina klientramverk i tid för dem att mottagna och bearbeta information innan annons-möjlighet.

Förutom annons-support användas inställningen LiveBackoff för att justera positionen för användarna så att när klienter avviker och når live edge kan de fortfarande få fragment från servern i stället för att få ett HTTP 404 eller 412 fel.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtreringsregler i ett enda filter. Som exempel kan du definiera ”intervallet regeln” ta bort pekdatorer från en live-arkivet och även filtrerat bort tillgängliga bithastighet. När du använder flera filtreringsregler är slutresultatet skärningspunkten för alla regler.

![flera regler][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter sammansättning)

Du kan också kombinera flera filter i en enskild URL. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera din video egenskaper för mobila enheter som Android- eller iPAD (för att begränsa video egenskaper). Om du vill ta bort oönskade egenskaper, skapar du en kontofilter passar enhetsprofilerna. Kontofilter kan användas för alla dina tillgångar under samma media services-konto utan ytterligare association. 
2. Du bör också att trimma start- och tidpunkten för en tillgång. Om du vill göra detta kan du skapa ett filter för tillgången och ange starttid/sluttid. 
3. Du vill kombinera båda dessa filter (utan kombination, du måste lägga till kvalitet filtrering för ta bort filtret, vilket gör det svårare filter användning).

Om du vill kombinera filter, måste du ange filternamn i manifestet/spelningslistan URL med semikolonavgränsad lista. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar egenskaper och det finns en annan med namnet *MyStartTime* att ställa in en viss starttid. Du kan kombinera dem så här:

Du kan kombinera upp till tre filter. 

Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

- Värdena för **forceEndTimestamp**, **presentationWindowDuration**, och **liveBackoffDuration** ska inte anges för en VoD-filtret. De används endast för live-filtret scenarier. 
- Dynamiska manifest körs i GOP gränser (nyckel ramar) därför trimmar har GOP precision. 
- Du kan använda samma filternamnet för konto- och Tillgångsnivå filter. Tillgången filter har högre prioritet och åsidosätter kontofilter.
- Om du uppdaterar ett filter, kan det ta upp till 2 minuter för slutpunkten för direktuppspelning att uppdatera reglerna. Om innehållet behandlades använda filter och cachelagras i proxyservrar och CDN cacheminnen, kan uppdaterar de här filtren resultera i player-fel. Det rekommenderas att rensa cacheminnet när du har uppdaterat filtret. Överväg att använda ett annat filter om det här alternativet inte är möjligt.
- Kunder måste hämta manifestet och parsa exakta startTimestamp och skala manuellt.
    
    - Att fastställa egenskaperna för spår i en tillgång [få och granska manifestfilen](#get-and-examine-manifest-files).
    - Formeln för att ange filtret tillgången tidsstämpel egenskaper: <br/>startTimestamp = &lt;starttid i manifestet&gt; +  &lt;förväntat filter starttiden på några sekunder&gt;* tidsskalan


## <a name="next-steps"></a>Nästa steg

I följande artiklar visar hur du skapar filter programmässigt.  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
