---
title: Filter och Azure Media Services dynamiska manifest | Microsoft Docs
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835819"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Filtrera före manifest med dynamiska Paketeraren

När du levererar innehåll till enheter för direktuppspelning med anpassningsbar bithastighet, behöver du ofta publicera flera versioner av ett manifest target specifika enhetsfunktioner eller tillgänglig nätverksbandbredd. Den [dynamisk Paketeraren](dynamic-packaging-overview.md) kan du ange filter som kan filtrera ut specifika codec upplösningar och bithastigheter för utdata ljud spåra kombinationer på direkt tar bort behovet av att skapa flera kopior. Du behöver bara publicera en ny URL med en specifik uppsättning filter som konfigurerats för att dina målenheter (iOS, Android, SmartTV eller webbläsare) och nätverksfunktioner (hög bandbredd, Mobil eller med låg bandbredd-scenario). I det här fallet klienter kan manipulera strömning av innehåll via frågesträngen (genom att ange tillgänglig [tillgången filter eller kontofilter](filters-concept.md)) och Använd filter för att stream vissa delar av en dataström.

Vissa leverans scenarier kräver att du gör att en kund är inte har tillgång till specifika spår. Till exempel kanske du inte vill publicera ett manifest som innehåller HD spårar till en specifik prenumerations-nivå. Eller så kan du ta bort specifika med anpassningsbar bithastighet (ABR) spårar för att minska kostnaden för leverans till en specifik enhet som inte skulle ha nytta av ytterligare spår. I det här fallet kunde du koppla en lista med förinställda filter med din [Strömningspositionerare](streaming-locators-concept.md) skapats. Klienter kan inte ändra hur innehåll strömmas i det här fallet, den definieras av den **Strömningspositionerare**.

Du kan kombinera filtrering genom att ange [filter på Strömningspositionerare](filters-concept.md#associating-filters-with-streaming-locator) + ytterligare specifika filter som klienten anger i URL: en. Detta kan vara användbart att begränsa ytterligare spårar som metadata eller händelse strömmar, ljud språk eller beskrivande ljudspår. 

Den här möjligheten att ange olika filter på din stream ger en kraftfull **dynamiska Manifest** manipulering av lösning för att rikta in flera Användningsscenarier för din målenheter. Det här avsnittet förklarar begrepp som rör **dynamiska manifest** och ger exempel på scenarier där du kanske vill använda den här funktionen.

> [!NOTE]
> Dynamiska manifest ändras inte tillgången och standard-manifest för tillgången. 
> 

##  <a name="overview-of-manifests"></a>Översikt över manifest

Azure Media Services har stöd för HLS, MPEG DASH och Smooth Streaming-protokoll. Som en del av [dynamisk paketering](dynamic-packaging-overview.md), strömmande klientmanifesten (HLS Master Playlist, DASH Media presentationsbeskrivning [MPD] och Smooth Streaming) genereras dynamiskt baserat på väljaren format i URL: en. Se leveransprotokoll i [vanliga arbetsflöde](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Hämta och granska manifestfiler

Du kan ange en lista över filter spåra Egenskapsvillkor baserat på vilka spår för din dataström (live eller video på begäran [VOD]) som ska ingå i ett dynamiskt skapade manifest. Om du vill hämta och granska egenskaperna för spåren behövs för att läsa Smooth Streaming-manifestet först.

Den [överföra, koda och strömma filer med .NET](stream-files-tutorial-with-api.md#get-streaming-urls) kursen visar hur du skapar en strömmande URL: er med .NET. Om du kör appen, någon av webbadresser pekar Smooth Streaming-manifest: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Kopiera och klistra in Webbadressen i adressfältet i en webbläsare. Filen laddas ned. Du kan öppna den i en textredigerare som du önskar.

En REST-exempel finns i [överföra, koda och strömma filer med REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Övervaka bithastigheten av en videoström

Du kan använda den [Demonstrationssida för Azure Media Player](http://aka.ms/azuremediaplayer) att övervaka bithastigheten av en videoström. Demo-sidan visar diagnostik information på den **diagnostik** fliken:

![Azure Media Player-diagnostik][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Exempel: URL: er med filter i frågesträngen

Du kan använda filter på ABR strömningsprotokoll: HLS, MPEG-DASH och Smooth Streaming. I följande tabell visas några exempel på URL: er med filter:

|Protocol|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Återgivningsfiltrering

Du kan välja att koda din tillgång till flera kodning profiler (H.264 baslinje, H.264 hög, AACL, AACH, Dolby Digital Plus) och flera olika bithastigheter kvalitet. Dock stöder inte alla klientenheter din tillgångs-profiler och bithastigheter för utdata. Till exempel stöd för äldre Android-enheter bara H.264 baslinje + AACL. Skicka högre bithastighet till en enhet som inte kan erhålla fördelarna slösar nätverksbandbredd och enheten beräkning. Sådana en enhet måste avkoda alla angivna information endast för Nedskalning för visning.

Med dynamiska Manifest kan du skapa enhetsprofiler (till exempel mobile, konsolen eller HD/SD) och inkludera spårar och egenskaper som du vill ska vara en del av varje profil. Som kallas återgivningsfiltrering. Följande diagram visar ett exempel på den.

![Exempel på återgivningsfiltrering][renditions2]

I följande exempel har en kodare använts för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p 1080p). Den kodade tillgången kan vara [dynamiskt paketerade](dynamic-packaging-overview.md) på någon av de följande protokollen med direktuppspelning: HLS, MPEG DASH och Smooth. 

Längst upp i följande diagram visar HLS manifest för tillgången utan filter. (Den innehåller alla sju återgivningar.)  Diagrammet visar ett manifest för HLS som ett filter med namnet ”ott” användes längst ned till vänster. Filtret ”ott” anger borttagning av alla bithastigheter för utdata nedan 1 Mbit/s, så att de två kvalitetsnivå längst ned och har därför i svaret. Diagrammet visar HLS manifestet som ett filter med namnet ”mobil” användes i det nedre högra hörnet. Filtret ”mobil” anger borttagning av återgivningar där lösningen är större än 720p, så att de två 1080 p återgivningar inte har och därför.

![Återgivningsfiltrering][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska, osv. Vanligtvis Player SDK-chefer standard ljudspår val och tillgängliga ljud spårar per användarens val.

Utveckla sådana Player SDK utmaning eftersom den kräver olika implementeringar över enhetsspecifika spelarramverk. Dessutom begränsas Player API: er på vissa plattformar och omfattar inte funktionen ljudspår där användare inte kan markera eller ändra standard ljudspår. Du kan styra beteendet genom att skapa filter som endast innehåller önskade ljud språk med tillgången filter.

![Filtrering av språkspår][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Trimma början av en tillgång

I de flesta liveuppspelningshändelser kör operatörer vissa tester innan den faktiska händelsen. De kan till exempel innehålla en bakgrundsbild så här innan händelsen: ”Inleds under ett ögonblick”. 

Om programmet arkivering, test- och statiska data också arkiveras och ingår i presentationen. Den här informationen bör dock inte att visas för klienterna. Du kan skapa en starttidsfilter och ta bort oönskade data från manifestet med dynamiska Manifest.

![Ta bort start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underklipp (vyer) från en live-arkivet

Många direktsändningar är långvariga och live-arkivet kan innehålla flera händelser. När livehändelsen är slut, kanske sändningsföretag vill dela upp live-arkivet till logiska programmet startas och stoppa sekvenser. 

Du kan publicera dessa virtuella program separat utan efter bearbetning av live-arkivet och inte skapa separata resurser (som inte får fördelen med de befintliga cachelagrade fragment i de CDN-nät). Exempel på sådana virtuella program är kvartal football eller basket spel, innings i basket eller enskilda händelser för alla sport-program.

Med dynamiska Manifest kan du skapa filter med hjälp av start-och sluttider och skapa virtuella vyer över dina live-arkivet. 

![Underklipp filter][subclip_filter]

Här är den filtrera tillgången:

![Skidåkning][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Justera fönstret (DVR)

Azure Media Services erbjuder för närvarande, cirkulär Arkiv där varaktigheten kan konfigureras mellan 5 minuter och 25: e timme. Manifest filtrering kan användas för att skapa en löpande DVR-perioden över upp arkivet, utan att ta bort media. Det finns många scenarier där sändningsföretag vill tillhandahålla en begränsad DVR-perioden för att flytta med live edge och samtidigt hålla ett större arkivering fönster. En broadcast kanske vill använda de data som ligger utanför fönstret DVR att markera klipp eller kan de vilja ange olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hantera inte exempelvis stora DVR windows (du kan ha en 2-minut DVR-perioden för mobila enheter och 1 timme för stationära klienter).

![DVR-perioden][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (direktsända positionen)

Manifest filtrering kan användas för att ta bort några sekunder från live gränsen för en live. Filtrering kan sändningsföretag se presentationen på förhandsversion publikationen punkt och skapa annonsen infogas punkter innan användarna får den dataström (understöds av 30 sekunder). Programleverantörer kan sedan skicka dessa annonser till sina klientramverk i tid för dem att ta emot och bearbeta information innan annons-möjlighet.

Förutom annons-stöd kan live backoff-inställningen användas för att justera de visningsprogram position så att när klienter avviker och når live edge, kan de fortfarande få fragment från servern. På så sätt kan klienterna inte hämta 412 fel eller ett HTTP 404.

![Filter för live backoff][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter

Du kan kombinera flera filtreringsregler i ett enda filter. Som exempel kan du definiera ”intervallet regeln” ta bort pekdatorer från en live-arkivet och även filtrerat bort tillgängliga bithastighet. När du använder flera filtreringsregler är slutresultatet skärningspunkten för alla regler.

![Flera filtreringsregler][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter sammansättning)

Du kan också kombinera flera filter i en enskild URL. Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera din video egenskaper för mobila enheter som Android- eller iPad (för att begränsa video egenskaper). Om du vill ta bort oönskade egenskaper, ska du skapa ett kontofilter passar enhetsprofilerna. Du kan använda kontofilter för alla dina tillgångar med samma Media Services-konto utan ytterligare association.
1. Du bör också att trimma start- och tidpunkten för en tillgång. Om du vill göra detta kan du skapa ett filter för tillgången och ange starttid/sluttid. 
1. Du kan kombinera båda dessa filter. Utan kombination, skulle du måste lägga till kvalitet filtrering för ta bort filtret, vilket skulle göra filter användning svårare.


Om du vill kombinera filter, måste du ange filternamn i manifestet/spelningslistan URL: en i semikolonavgränsat format. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar egenskaper och det finns en annan med namnet *MyStartTime* att ställa in en viss starttid. Du kan kombinera upp till tre filter. 

Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

- Värdena för **forceEndTimestamp**, **presentationWindowDuration**, och **liveBackoffDuration** ska inte anges för en VOD-filtret. De används endast för live-filtret scenarier. 
-  Ett dynamiskt manifest körs i GOP gränser (viktiga ramar), så ta har GOP precision.
-  Du kan använda samma Filternamn för kontot och tillgången filter. Tillgången filter har högre prioritet och åsidosätter kontofilter.
- Om du uppdaterar ett filter, kan det ta upp till 2 minuter för den strömmande slutpunkten att uppdatera reglerna. Om du använde filter för att hantera innehållet (och du cachelagras innehållet i proxyservrar och CDN cachelagrar), kan uppdaterar de här filtren leda till player-fel. Vi rekommenderar att du har rensat när du har uppdaterat filtret. Överväg att använda ett annat filter om det här alternativet inte är möjligt.
- Kunder måste hämta manifestet och parsa exakta Starttidsstämpeln och tid skala manuellt.
    
    - Att fastställa egenskaperna för spår i en tillgång [få och granska manifestfilen](#get-and-examine-manifest-files).
    - Formel för att ange tidsstämpeln för tillgången filteregenskaper är: <br/>startTimestamp = &lt;starttid i manifestet&gt; +  &lt;förväntat filter starttiden på några sekunder&gt; * tidsskalan

## <a name="next-steps"></a>Nästa steg

I följande artiklar visar hur du programmässigt skapar filter:  

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
