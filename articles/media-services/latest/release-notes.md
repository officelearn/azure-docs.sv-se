---
title: Azure Media Services v3-viktig information | Microsoft-dokument
description: För att hålla dig uppdaterad med den senaste utvecklingen innehåller den här artikeln de senaste uppdateringarna om Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769759"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3-viktig information

>Få ett meddelande om när du ska gå tillbaka till `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` den här sidan för uppdateringar genom att kopiera och klistra in den här webbadressen: i rss-flödesläsaren.

För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln dig information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

## <a name="known-issues"></a>Kända problem

> [!NOTE]
> Du kan använda [Azure-portalen](https://portal.azure.com/) för att hantera v3 [Live Events,](live-events-outputs-concept.md)visa v3-tillgångar och få information om hur du använder API:er. [Assets](assets-concept.md) För alla andra hanteringsuppgifter (till exempel Transformeringar och jobb) använder [du REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

Mer information finns i [Migreringsvägledning för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>April 2020

### <a name="improvements-in-documentation"></a>Förbättringar i dokumentationen

Azure Media Player-dokument migrerades till [Azure-dokumentationen](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Januari 2020

### <a name="improvements-in-media-processors"></a>Förbättringar i medieprocessorer

- Förbättrat stöd för sammanflätade källor i videoanalys - sådant innehåll är nu de-sammanflätade korrekt innan de skickas till inferens motorer.
- När du skapar miniatyrer med "Bästa" läge, söker kodaren nu bortom 30 sekunder för att välja en ram som inte är monokromatisk.

### <a name="azure-government-cloud-updates"></a>Molnuppdateringar för Azure Government

Media Services GA'ed i följande Azure Government regioner: *USGov Arizona* och *USGov Texas*.

## <a name="december-2019"></a>December 2019

Lade till CDN-stöd för *Origin-Assist Prefetch-rubriker* för streaming på både live och video. tillgängliga för kunder som har direkt kontrakt med Akamai CDN. Origin-Assist CDN-Prefetch-funktionen omfattar följande HTTP-huvudutbyten mellan Akamai CDN och Azure Media Services ursprung:

|HTTP-huvud|Värden|Avsändare|Mottagare|Syfte|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Aktiverad | 1 (standard) eller 0 |CDN|Ursprung|För att indikera att CDN är prefetch aktiverat|
|CDN-Origin-Assist-Prefetch-Path| Exempel: <br/>Fragment(video=1400000000,format=mpd-time-cmaf)|Ursprung|CDN|Så här ger du prefetch-sökvägen till CDN|
|CDN-Origin-Assist-Prefetch-Request|1 (prefetch begäran) eller 0 (vanlig begäran)|CDN|Ursprung|För att ange begäran från CDN är en prefetch|

Om du vill se en del av sidutbytet i praktiken kan du prova följande steg:

1. Använd Postman eller curl för att utfärda en begäran till Media Services ursprung för ett ljud- eller videosegment eller fragment. Se till att lägga till rubriken CDN-Origin-Assist-Prefetch-Enabled: 1 i begäran.
2. I svaret bör du se rubriken CDN-Origin-Assist-Prefetch-Path med en relativ sökväg som värde.

## <a name="november-2019"></a>November 2019

### <a name="live-transcription-preview"></a>Förhandsgranskning av livedeskription

Live transkription är nu i offentlig förhandsvisning och tillgänglig för användning i västra USA 2 regionen.

Live transkription är utformad för att fungera i samband med live-evenemang som en tilläggsfunktion.  Det stöds på både genomströmning och Standard eller Premium kodning live-evenemang.  När den här funktionen är aktiverad använder tjänsten funktionen [Tal-till-text](../../cognitive-services/speech-service/speech-to-text.md) i Cognitive Services för att transkribera de talade orden i det inkommande ljudet till text. Denna text görs sedan tillgänglig för leverans tillsammans med video och ljud i MPEG-DASH och HLS protokoll. Fakturering baseras på en ny tilläggsmätare som är extra kostnad för livehändelsen när den är i tillståndet "Kör".  Mer information om live transkription och fakturering finns i [Live transkription](live-transcription.md)

> [!NOTE]
> För närvarande är live transkription endast tillgänglig som en förhandsgranskningsfunktion i regionen Västra USA 2. Det stöder transkription av talade ord på engelska (en-us) endast vid denna tidpunkt.

### <a name="content-protection"></a>Innehållsskydd

Funktionen *Token Replay Prevention* som släpps i begränsade regioner redan i september är nu tillgänglig i alla regioner.
Media Services-kunder kan nu ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Mer information finns i [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nya rekommenderade live-kodarpartner

Lade till stöd för följande nya rekommenderade partnerkodrar för RTMP live streaming:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 och Max actionkameror](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Förbättringar av filkodning

- En ny förinställning för innehållsmedveten kodning är nu tillgänglig. Den ger en uppsättning GOP-anpassade MP4:er med hjälp av innehållsmedveten kodning. Med tanke på eventuellt indatainnehåll utför tjänsten en första lättviktsanalys av indatainnehållet. Den använder dessa resultat för att bestämma det optimala antalet lager, lämplig bithastighet och upplösningsinställningar för leverans genom adaptiv direktuppspelning. Den här förinställningen är särskilt effektiv för videor med låg komplexitet och medelkomplexitet, där utdatafilerna har lägre bithastigheter men med en kvalitet som fortfarande ger tittarna en bra upplevelse. Utdata kommer att innehålla MP4-filer med video och ljud interfolierade. Mer information finns i öppna [API-specifikationer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Förbättrad prestanda och flertrådning för omstorleksren i Standardencoder. Under särskilda förhållanden bör kunden se en prestandaökning mellan 5-40% VOD-kodning. Låg komplexitet innehåll kodade i flera bithastigheter kommer att se de högsta prestandaökningarna. 
- Standardkodning upprätthåller nu en vanlig GOP-kadens för VFR-innehåll (variable frame rate) under VOD-kodning när du använder den tidsbaserade GOP-inställningen.  Detta innebär att kunden skickar blandade bildhastighet innehåll som varierar mellan 15-30 fps till exempel bör nu se regelbundna GOP avstånd beräknas på utdata till adaptiv bitrate streaming MP4-filer. Detta kommer att förbättra möjligheten att växla sömlöst mellan spår när de levererar över HLS eller DASH. 
-  Förbättrat AV-synkronisering för VFR-källinnehåll (variable frame rate)

### <a name="video-indexer-video-analytics"></a>Videoindexerare, Videoanalys

- Nyckelrutor som extraheras med videoanalyzer-förinställningen är nu i videons ursprungliga upplösning i stället för att storleksas om. Hög upplösning keyframe extraktion ger dig originalkvalitet bilder och låter dig använda sig av bildbaserade artificiell intelligens modeller som tillhandahålls av Microsoft Computer Vision och Custom Vision tjänster för att få ännu mer insikter från din video.

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Levande linjär kodning av liveevenemang

Media Services v3 tillkännager förhandsvisning av 24 timmar x 365 dagar av levande linjär kodning av live-evenemang.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medieprocessorer

Vi tillkännager utfasning av *Azure Media Indexer* och *Azure Media Indexer 2 Preview*. För pensioneringsdatumen finns i avsnittet [äldre komponenter.](../previous/legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter dessa äldre medieprocessorer.

Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augusti 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Sydafrika regionala par är öppen för Media Services 

Media Services är nu tillgänglig i Sydafrika Nord-och Sydafrika Västregioner.

Mer information finns [i Moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medieprocessorer

Vi meddelar att deprecation av *windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (AME) medieprocessorer, som dras tillbaka. För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](../previous/legacy-components.md)

Mer information finns i [Migrera WAME till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) och [Migrera AME till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Innehållsskydd

När direktuppspelning av innehåll skyddas med tokenbegränsning måste slutanvändare hämta en token som skickas som en del av nyckelleveransbegäran. Funktionen *Token Replay Prevention* gör det möjligt för Media Services-kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Mer information finns i [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

Från och med juli var förhandsgranskningsfunktionen endast tillgänglig i US Central och US West Central.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Videounderklipp

Du kan nu trimma eller underkräna en video när du kodar den med ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). 

Den här funktionen fungerar med alla [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) som skapas med hjälp av antingen [förinställningarna BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) eller [förinställningarna StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Se exempel:

* [Subclip en video med .NET](subclip-video-dotnet-howto.md)
* [Subclip en video med REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maj 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor-stöd för diagnostikloggar och mätvärden för Media Services

Du kan nu använda Azure Monitor för att visa telemetridata som skickas ut av Media Services.

* Använd diagnostikloggarna för Azure Monitor för att övervaka begäranden som skickas av slutpunkten för nyckelleverans för mediatjänster. 
* Övervaka mätvärden som avges av Media Services [Streaming Endpoints](streaming-endpoint-concept.md).   

Mer information finns i [Övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Stöd för flerljudspår i Dynamic Packaging 

När du streamar tillgångar som har flera ljudspår med flera codec-enheter och språk stöder [Dynamic Packaging](dynamic-packaging-overview.md) nu flera ljudspår för HLS-utdata (version 4 eller högre).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionala par är öppen för Media Services 

Media Services är nu tillgängligt i Sydkoreas och Sydkoreas regioner. 

Mer information finns [i Moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Prestandaförbättringar

Lade till uppdateringar som innehåller prestandaförbättringar för Media Services.

* Den maximala filstorleken som stöds för bearbetning har uppdaterats. Se [Kvotering och begränsningar](limits-quotas-constraints.md).
* [Kodning hastigheter förbättringar](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nya förinställningar

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) har lagts till i de inbyggda analysatorförinställningarna.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) lades till i de inbyggda kodarförinställningarna. Mer information finns i [Innehållsmedveten kodning](content-aware-encoding.md). 

## <a name="march-2019"></a>Mars 2019

Dynamic Packaging stöder nu Dolby Atmos. Mer information finns i [Ljudkodenheter som stöds av dynamisk förpackning](dynamic-packaging-overview.md#audio-codecs).

Nu kan du ange en lista över tillgångs- eller kontofilter som gäller för din streamingpositionerare. Mer information finns i [Associera filter med direktuppsökaren](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Februari 2019

Media Services v3 stöds nu i Azure nationella moln. Alla funktioner är inte tillgängliga i alla moln ännu. Mer information finns [i Moln och regioner där Azure Media Services v3 finns](azure-clouds-regions.md).

[Microsoft.Media.JobOutputProgress-händelsen](media-services-event-schemas.md#monitoring-job-output-progress) har lagts till i Azure Event Grid-schemana för Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard- och MPI-filer 

När kodning med Media Encoder Standard för att producera MP4-filer genereras en ny MPI-fil och läggs till i utdatatillgången. Den här MPI-filen är avsedd att förbättra prestanda för [dynamiska paketerings-](dynamic-packaging-overview.md) och direktuppspelningsscenarier.

Du bör inte ändra eller ta bort MPI-filen eller ta något beroende av din tjänst på förekomsten (eller inte) av en sådan fil.

## <a name="december-2018"></a>December 2018

Uppdateringar från GA-versionen av V3 API inkluderar:
       
* **Egenskaperna PresentationTimeRange** krävs inte längre för **tillgångsfilter** och **kontofilter**. 
* Frågealternativen $top och $skip för **jobb** och **transformeringar** har tagits bort och $orderby har lagts till. Som en del av att lägga till den nya beställningsfunktionen upptäcktes att $top och $skip alternativ av misstag hade exponerats tidigare även om de inte har implementerats.
* Uppräkning extensibility aktiverades på ett sätt. Den här funktionen aktiverades i förhandsgranskningsversionerna av SDK och inaktiverades av misstag i GA-versionen.
* Två fördefinierade direktuppspelningsprinciper har bytt namn. **SecureStreaming** är nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** är nu **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

CLI 2.0-modulen är nu tillgänglig för [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nya kommandon

- [az ams konto](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams konto-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams tillgång](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams tillgång-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams jobb](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output az ams live-output az ams live-output az am](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-slutpunkt](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams konto mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - gör att du kan hantera media reserverade enheter. Mer information finns i [Skala mediereserverade enheter](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nya funktioner och bryta förändringar

#### <a name="asset-commands"></a>Kommandon för tillgångar

- ```--storage-account```och ```--container``` argument tillagda.
- Standardvärden för utgångsdatum (Nu+23h) och behörigheter (Läs) i ```az ams asset get-sas-url``` kommandot har lagts till.

#### <a name="job-commands"></a>Jobbkommandon

- ```--correlation-data```och ```--label``` argument tillagda
- ```--output-asset-names```omdöpt ```--output-assets```till . Nu accepteras en utrymmesavskiljande lista över tillgångar i formatet assetName=label. En tillgång utan etikett kan skickas så här: 'assetName='.

#### <a name="streaming-locator-commands"></a>Kommandon för strömmande positionerare

- ```az ams streaming locator```baskommandot ```az ams streaming-locator```ersatt med .
- ```--streaming-locator-id```och ```--alternative-media-id support``` argument tillagda.
- ```--content-keys argument```argumentet uppdateras.
- ```--content-policy-name```omdöpt ```--content-key-policy-name```till .

#### <a name="streaming-policy-commands"></a>Kommandon för strömmande princip

- ```az ams streaming policy```baskommandot ```az ams streaming-policy```ersatt med .
- Krypteringsparametrar stöder tillagd. ```az ams streaming-policy create```

#### <a name="transform-commands"></a>Omforma kommandon

- ```--preset-names```argument ersättas ```--preset```med . Nu kan du bara ställa in 1 utgång/förinställning i ```az ams transform output add```taget (för att lägga till fler måste du köra). Du kan också ange anpassad StandardEncoderPreset genom att skicka sökvägen till din anpassade JSON.
- ```az ams transform output remove```kan utföras genom att skicka utdataindexet för att ta bort.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argument som ```az ams transform create``` ```az ams transform output add``` lagts till och kommandon.

## <a name="october-2018---ga"></a>Oktober 2018 - GA

I det här avsnittet beskrivs uppdateringar av Azure Media Services (AMS) oktober.

### <a name="rest-v3-ga-release"></a>REST v3 GA release

[REST v3 GA-versionen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) innehåller fler API:er för manifestfilter på konto-/tillgångsnivå och DRM-stöd.

#### <a name="azure-resource-management"></a>Azure Resource Management 

Stöd för Azure Resource Management möjliggör enhetlig hantering och åtgärder API (nu allt på ett ställe).

Från och med den här versionen kan du använda Resource Manager-mallar för att skapa livehändelser.

#### <a name="improvement-of-asset-operations"></a>Förbättring av tillgångsverksamheten 

Följande förbättringar infördes:

- Inta från HTTP:er-URL:er eller Azure Blob Storage SAS-URL:er.
- Ange egna behållarnamn för tillgångar. 
- Enklare utdatastöd för att skapa anpassade arbetsflöden med Azure Functions.

#### <a name="new-transform-object"></a>Nytt transformeringsobjekt

Det nya **Transform-objektet** förenklar kodningsmodellen. Det nya objektet gör det enkelt att skapa och dela mallar och förinställningar för Resurshanteraren. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-autentisering och RBAC

Azure AD-autentisering och rollbaserad åtkomstkontroll (RBAC) möjliggör säkra transformeringar, LiveEvents, Content Key-principer eller tillgångar efter roll eller användare i Azure AD.

#### <a name="client-sdks"></a>Klient-SDK: er  

Språk som stöds i Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Live-kodningsuppdateringar

Följande live-kodningsuppdateringar introduceras:

- Nytt låg latensläge för live (10 sekunder från på nytt).
- Förbättrat RTMP-stöd (ökad stabilitet och mer stöd för källkodare).
- RTMPS säkrar intag.

    När du skapar en Live Event får du nu 4 intövande webbadresser. De 4 intövande webbadresserna är nästan identiska, har samma streamingtoken (AppId), endast portnummerdelen är annorlunda. Två av webbadresserna är primära och säkerhetskopierade för RTMPS. 
- 24-timmars omkodningsstöd. 
- Förbättrat stöd för annonssignalering i RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Förbättrat stöd för händelserutnät

Du kan se följande förbättringar av supporten för Event Grid:

- Azure Event Grid-integrering för enklare utveckling med Logic Apps och Azure Functions. 
- Prenumerera på evenemang på kodning, livekanaler med mera.

### <a name="cmaf-support"></a>SUPPORT FÖR CMAF

CMAF- och "cbcs"-krypteringsstöd för Apple HLS (iOS 11+) och MPEG-DASH-spelare som stöder CMAF.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA release tillkännagavs i augusti. Ny information om funktioner som stöds för närvarande finns i [Vad är Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planer för förändringar

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2.0-modulen som innehåller åtgärder på alla funktioner (inklusive Live, Content Key Policies, Account/Asset Filters, Streaming Policies) kommer snart. 

### <a name="known-issues"></a>Kända problem

Endast kunder som använde förhandsversionen av API för tillgång eller accountfilter påverkas av följande problem.

Om du skapade tillgångar eller kontofilter mellan 09/28 och 10/12 med Media Services v3 CLI eller API:er måste du ta bort alla tillgångs- och kontofilter och återskapa dem på grund av en versionskonflikt. 

## <a name="may-2018---preview"></a>Maj 2018 - Förhandsgranska

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .NET SDK:

* **Omvandlar** och **jobb** för att koda eller analysera medieinnehåll. Exempel finns i [Strömma filer](stream-files-tutorial-with-api.md) och [Analysera](analyze-videos-tutorial-with-api.md).
* **Streaming locators** för publicering och direktuppspelning av innehåll till slutanvändarens enheter
* **Streamingprinciper** och **principer för innehållsnyckel** för att konfigurera nyckelleverans och innehållsskydd (DRM) när innehåll levereras.
* **Live-händelser** och **liveutdata** för att konfigurera inmatning och arkivering av direktuppspelning av innehåll.
* **Resurser** för att lagra och publicera medieinnehåll i Azure Storage. 
* **Strömmande slutpunkter** för att konfigurera och skala dynamisk paketering, kryptering och direktuppspelning för både direkt och on-demand-medieinnehåll.

### <a name="known-issues"></a>Kända problem

* När du skickar ett jobb kan du ange att källvideon ska intas med HTTPS-URL:er, SAS-URL:er eller sökvägar till filer som finns i Azure Blob-lagring. AMS v3 stöder för närvarande inte segmentvis överföringskodning över HTTPS-URL:er.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

- [Översikt](media-services-overview.md)
- [Uppdateringar av mediatjänster v3-dokumentation](docs-release-notes.md)
- [Viktig information om Media Services v2](../previous/media-services-release-notes.md)
