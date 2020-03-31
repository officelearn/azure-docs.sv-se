---
title: Filtrera dina manifest med dynamisk paketerare
titleSuffix: Azure Media Services
description: Lär dig hur du skapar filter med dynamisk paketerare för att filtrera och selektivt strömma dina manifest.
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
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186218"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrera dina manifest med dynamisk paketerare

När du levererar adaptivt bithastighetsströmningsinnehåll till enheter måste du ibland publicera flera versioner av ett manifest för att rikta specifika enhetsfunktioner eller tillgänglig nätverksbandbredd. Med [den dynamiska paketeraren](dynamic-packaging-overview.md) kan du ange filter som kan filtrera bort specifika codec-enheter, upplösningar, bithastigheter och kombinationer av ljudspår i farten. Den här filtrningen tar bort behovet av att skapa flera kopior. Du behöver bara publicera en ny webbadress med en specifik uppsättning filter som konfigurerats till dina målenheter (iOS, Android, SmartTV eller webbläsare) och nätverksfunktionerna (scenarier med hög bandbredd, mobil eller låg bandbredd). I det här fallet kan klienter manipulera direktuppspelningen av innehållet via frågesträngen (genom att ange tillgängliga [tillgångsfilter eller kontofilter)](filters-concept.md)och använda filter för att strömma specifika avsnitt i en ström.

Vissa leveransscenarier kräver att du ser till att en kund inte kan komma åt specifika spår. Du kanske till exempel inte vill publicera ett manifest som innehåller HD-spår till en viss prenumerationsnivå. Eller kanske du vill ta bort specifika adaptiva bithastighetsspår (ABR) för att minska leveranskostnaderna till en viss enhet som inte skulle dra nytta av ytterligare spår. I det här fallet kan du associera en lista över förskapade filter med din [streaming locator](streaming-locators-concept.md) när du skapar. Klienter kan då inte manipulera hur innehållet strömmas eftersom det definieras av **streaming locator**.

Du kan kombinera filtrering genom att ange [filter på strömningspositionerare](filters-concept.md#associating-filters-with-streaming-locator) + ytterligare enhetsspecifika filter som klienten anger i URL:en. Den här kombinationen är användbar för att begränsa ytterligare spår som metadata eller händelseströmmar, ljudspråk eller beskrivande ljudspår.

Den här möjligheten att ange olika filter på din ström ger en kraftfull dynamisk manifestmanipuleringslösning för att rikta in sig på flera användningsfall-scenarier för dina målenheter. **Dynamic Manifest** I det här avsnittet **beskrivs** begrepp som är relaterade till dynamiska manifest och exempel på scenarier där du kan använda den här funktionen.

> [!NOTE]
> Dynamiska manifest ändrar inte tillgången och standardmanifestet för den tillgången.

## <a name="overview-of-manifests"></a>Översikt över manifest

Azure Media Services stöder protokoll för HLS, MPEG DASH och Smooth Streaming. Som en del av [Dynamic Packaging](dynamic-packaging-overview.md)genereras delningsklientmanifest (HLS Master Playlist, DASH Media Presentation Description [MPD] och Smooth Streaming) dynamiskt baserat på formatväljaren i URL:en. Mer information finns i leveransprotokollen i [Common on-demand-arbetsflödet](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Hämta och granska manifestfiler

Du anger en lista över filterspårsegenskapsvillkor baserat på vilka spår av din ström (live eller video on-demand [VOD]) ska inkluderas i ett dynamiskt skapat manifest. För att få och undersöka egenskaperna för spåren måste du läsa in smooth streaming-manifestet först.

Upload- [, encode- och stream-filerna med](stream-files-tutorial-with-api.md#get-streaming-urls) .NET-självstudien visar hur du skapar strömmande url:er med .NET. Om du kör appen pekar en av webbadresserna på `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`manifestet För jämn direktuppspelning: .<br/> Kopiera och klistra in webbadressen i en webbläsares adressfält. Filen hämtas. Du kan öppna den i valfri textredigerare.

Ett REST-exempel finns i [Ladda upp, koda och strömma filer med REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Övervaka bithastigheten för en videoström

Du kan använda [demosidan](https://aka.ms/azuremediaplayer) för Azure Media Player för att övervaka bithastigheten för en videoström. Demosidan visar diagnostikinformation på fliken **Diagnostik:**

![Diagnostik i Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Exempel: Webbadresser med filter i frågesträngen

Du kan använda filter på ABR-direktuppspelningsprotokoll: HLS, MPEG-DASH och Smooth Streaming. I följande tabell visas några exempel på webbadresser med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrering av återgivning

Du kan välja att koda din tillgång till flera kodningsprofiler (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) och flera kvalitetsbithastigheter. Alla klientenheter stöder dock inte alla dina tillgångs profiler och bithastigheter. Äldre Android-enheter stöder till exempel endast H.264 Baseline+AACL. Skicka högre bithastigheter till en enhet som inte kan få fördelarna slösar bandbredd och enhetsberäkning. En sådan anordning måste avkoda all viss information, bara för att skala ner den för visning.

Med Dynamiskt manifest kan du skapa enhetsprofiler (till exempel mobil, konsol eller HD/SD) och inkludera de spår och egenskaper som du vill ska vara en del av varje profil. Det kallas återgivningsfiltrering. Följande diagram visar ett exempel på det.

![Exempel på återgivningsfiltrering med dynamiskt manifest][renditions2]

I följande exempel användes en kodare för att koda en mezzanintillgång i sju ISO MP4-videoåtergivningar (från 180p till 1080p). Den kodade tillgången kan [paketeras dynamiskt](dynamic-packaging-overview.md) i något av följande direktuppspelningsprotokoll: HLS, MPEG DASH och Smooth.

Högst upp i följande diagram visas HLS-manifestet för tillgången utan filter. (Den innehåller alla sju återgivningar.)  I det nedre vänstra visar diagrammet ett HLS-manifest som ett filter med namnet "ott" tillämpades på. Filtret "ott" anger att alla bithastigheter tas bort under 1 Mbit/s, så de två lägsta kvalitetsnivåerna togs bort i svaret. Längst ned till höger visar diagrammet HLS-manifestet som ett filter med namnet "mobil" har tillämpats på. Filtret "mobil" anger borttagningen av återgivningar där upplösningen är större än 720p, så de två 1080p-återgivningarna togs bort.

![Återgivningsfiltrering med dynamiskt manifest][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljudspråk som engelska, spanska, franska och så vidare. Vanligtvis hanterar Player SDK standardval av ljudspår och tillgängliga ljudspår per användarval.

Att utveckla sådana Player SDK:er är utmanande eftersom det kräver olika implementeringar över enhetsspecifika spelarramverk. På vissa plattformar är spelar-API:er begränsade och inkluderar inte ljudvalsfunktionen där användarna inte kan välja eller ändra standardljudspåret. Med tillgångsfilter kan du styra beteendet genom att skapa filter som bara innehåller önskade ljudspråk.

![Filtrering av språkspår med dynamiskt manifest][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Trimma början av en tillgång

I de flesta direktuppspelningshändelser kör operatörerna några tester före den faktiska händelsen. De kan till exempel innehålla en griffeltavla som denna innan evenemanget börjar: "Programmet börjar tillfälligt."

Om programmet arkiveras arkiveras och inkluderas även test- och skifferdata i presentationen. Den här informationen ska dock inte visas för klienterna. Med Dynamiskt manifest kan du skapa ett starttidsfilter och ta bort oönskade data från manifestet.

![Trimma början på en tillgång med dynamiskt manifest][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underknurer (vyer) från ett livearkiv

Många livehändelser är tidskrävande och live-arkiv kan innehålla flera händelser. När liveevenemanget har avslutats kanske programföretagen vill dela upp livearkivet i logiska programstart- och stoppsekvenser.

Du kan publicera dessa virtuella program separat utan efterbearbetning av livearkivet och inte skapa separata tillgångar (som inte får nytta av de befintliga cachelagrade fragmenten i CDN:erna). Exempel på sådana virtuella program är kvartalen i en fotboll eller basketmatch, innings i baseball, eller enskilda händelser i något sportprogram.

Med Dynamiskt manifest kan du skapa filter med start-/sluttider och skapa virtuella vyer överst i ditt livearkiv.

![Underknäckfilter med dynamiskt manifest][subclip_filter]

Här är den filtrerade tillgången:

![Filtrerad tillgång med dynamiskt manifest][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)

För närvarande erbjuder Azure Media Services cirkulära arkiv där varaktigheten kan konfigureras mellan 1 minut och 25 timmar. Manifestfiltrering kan användas för att skapa ett rullande DVR-fönster överarkivets överkant, utan att ta bort media. Det finns många scenarier där programföretag vill tillhandahålla ett begränsat DVR-fönster för att flytta med live kanten och samtidigt hålla ett större arkiveringsfönster. Ett programföretag kanske vill använda de data som finns utanför DVR-fönstret för att markera klipp, eller så kanske de vill tillhandahålla olika DVR-fönster för olika enheter. De flesta mobila enheter hanterar till exempel inte stora DVR-fönster (du kan ha ett 2-minuters DVR-fönster för mobila enheter och en timme för stationära klienter).

![DVR-fönster med dynamiskt manifest][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (liveposition)

Manifestfiltrering kan användas för att ta bort flera sekunder från live kanten av ett live-program. Filtrering gör det möjligt för programföretag att titta på presentationen på förhandsgranskningspubliceringspunkten och skapa insättningspunkter för annonser innan tittarna får strömmen (backas upp med 30 sekunder). Programföretag kan sedan driva dessa annonser till sina kundramverk i tid för dem att ta emot och bearbeta informationen innan annonsen tillfälle.

Förutom annonsstöd kan den direktsända back-off-inställningen användas för att justera tittarnas position så att när klienter driver och träffar live-kanten kan de fortfarande få fragment från servern. På så sätt får klienterna inte ett HTTP 404- eller 412-fel.

![Filter för live back-off med dynamiskt manifest][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter

Du kan kombinera flera filtreringsregler i ett enda filter. Du kan till exempel definiera en "intervallregel" för att ta bort griffeltavlor från ett livearkiv och även filtrera bort tillgängliga bithastigheter. När du tillämpar flera filtreringsregler är slutresultatet skärningspunkten mellan alla regler.

![Flera filtreringsregler med dynamiskt manifest][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filtersammansättning)

Du kan också kombinera flera filter i en enda WEBBADRESS. Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera dina videokvaliteter för mobila enheter, som Android eller iPad (för att begränsa videokvaliteter). Om du vill ta bort de oönskade egenskaperna skapar du ett kontofilter som passar enhetsprofilerna. Du kan använda kontofilter för alla dina tillgångar under samma Media Services-konto utan någon ytterligare association.
1. Du vill också trimma start- och sluttiden för en tillgång. Om du vill trimma skapar du ett tillgångsfilter och ställer in start-/sluttid.
1. Du vill kombinera båda dessa filter. Utan kombination måste du lägga till kvalitetsfiltrering i trimningsfiltret, vilket skulle göra filteranvändningen svårare.

Om du vill kombinera filter anger du filternamnen på manifest-/spelliste-URL:en i semikolonavgränsat format. Anta att du har ett filter med namnet *MyMobileDevice* som filtrerar kvaliteter, och du har en annan som heter *MyStartTime* för att ställa in en viss starttid. Du kan kombinera upp till tre filter.

Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

- Värdena för **forceEndTimestamp**, **presentationWindowDuration**och **liveBackoffDuration** bör inte anges för ett VOD-filter. De används bara för levande filterscenarier.
- Ett dynamiskt manifest fungerar i GOP-gränser (nyckelbildrutor), så trimning har GOP-noggrannhet.
- Du kan använda samma filternamn för konto- och tillgångsfilter. Tillgångsfilter har högre prioritet och åsidosätter kontofilter.
- Om du uppdaterar ett filter kan det ta upp till 2 minuter innan slutpunkten för direktuppspelning uppdateras. Om du använde filter för att visa innehållet (och du cachelagrade innehållet i proxyservrar och CDN-cacheminnen) kan uppdatering av dessa filter resultera i spelarfel. Vi rekommenderar att du rensar cacheminnet när du har uppdaterat filtret. Om det här alternativet inte är möjligt kan du överväga att använda ett annat filter.
- Kunderna måste manuellt hämta manifestet och tolka den exakta starttidsstämpeln och tidsskalan.

    - Om du vill bestämma egenskaperna för spåren i en tillgång [hämtar och undersöker](#get-and-examine-manifest-files)du manifestfilen .
    - Formeln för att ange tidsstämpelegenskaper för tillgångsfiltret är: <br/>startTimestamp &lt;= starttid&gt; +  &lt;i manifestet förväntade&gt; filterstarttid i sekunder * tidsskala

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar hur du skapar filter programmässigt:  

- [Skapa filter med REST-API:er](filters-dynamic-manifest-rest-howto.md)
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
