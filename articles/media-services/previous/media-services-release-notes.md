---
title: Media Services viktig information | Microsoft Docs
description: Media Services viktig information
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 81139734d0eb7d15e78c465c06fc429de48480c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182994"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services viktig information

Dessa informationstexter för Azure Media Services sammanfatta ändringar från tidigare versioner och kända problem.

> [!NOTE]
> Inga nya funktioner eller funktioner läggs till Media Services v2. 

Vi vill gärna höra från våra kunder så att vi kan fokusera på att åtgärda problem som påverkar dig. Om du vill rapportera ett problem eller Ställ frågor, skicka ett inlägg i den [Azure Media Services MSDN-Forum]. 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Kända problem
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Media Services allmänna problem

| Problem | Beskrivning |
| --- | --- |
| Flera vanliga HTTP-rubriker angetts inte i REST-API. |Om du utvecklar program för Media Services med hjälp av REST-API kan du upptäcka att vissa vanliga HTTP-huvudfält (inklusive CLIENT-REQUEST-ID, ID för FÖRFRÅGAN och RETUR-CLIENT-REQUEST-ID) stöds inte. Rubrikerna som läggs till i en kommande uppdatering. |
| Procent-kodning är inte tillåten. |Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Därför är inte procent-kodning tillåten. Värdet för egenskapen Name får inte innehålla något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det kan bara finnas en ””. för filnamnstillägget. |
| Metoden ListBlobs som ingår i Azure Storage SDK version 3.x misslyckas. |Media Services genererar SAS URL: er utifrån den [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) version. Om du vill använda Storage SDK och lista blobar i en blobbehållare kan använda den [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metod som är en del av Storage SDK-version 2.x. |
| Medietjänster begränsning mekanism begränsar resursanvändningen för program som gör långa förfrågningar till tjänsten. Tjänsten kan returnera ”tjänst ej tillgänglig” 503 HTTP-statuskoden. |Mer information finns i beskrivningen av 503 HTTP-statuskod i [felkoder för Media Services](media-services-encoding-error-codes.md). |
| När du frågar entiteter returneras en gräns på 1 000 entiteter i taget eftersom offentlig REST version 2 begränsar frågeresultaten till 1 000 resultat. |Använd hoppa över och ta (.NET) / viktigaste (REST) enligt beskrivningen i [exemplet .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [det här REST API-exemplet](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Vissa klienter kan stöta på ett problem för upprepningar tagg i manifestet Smooth Streaming. |Mer information finns i [i det här avsnittet](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objekt kan inte serialiseras och därför fungerar inte med Azure Cache för Redis. |Om du försöker att serialisera objektet SDK AssetCollection om du vill lägga till den i Azure Cache för Redis genereras ett undantag. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>REST API-versionshistorik
Information om versionshistorik för Media Services REST API finns i den [Azure Media Services REST API-referens].

## <a name="december-2018"></a>December 2018

Den [Media Hyperlapse förhandsversion](media-services-hyperlapse-content.md) funktion i Azure Media Services snart dras in. Från och med 19 December 2018, kommer Media Services inte längre göra ändringar eller förbättringar i Media Hyperlapse. Den 29 mars 2019 blir den indragna och inte längre tillgänglig.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>CMAF support

Stöd för CMAF och 'cbcs' kryptering för (iOS 11 +) för Apple HLS och MPEG-DASH spelare som har stöd för CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatyr spriter

Du kan nu använda Media Services för att generera Web VTT miniatyr spriter med hjälp av våra v2 API: er. Mer information finns i [generera miniatyrbilder sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Juli 2018

Med den senaste tjänstversionen finns det mindre formateringsändringar till felmeddelanden som returneras av tjänsten när ett jobb inte med avseende på hur den har delats upp i två eller fler rader.

## <a name="may-2018"></a>Maj 2018 

Startar den 12 maj 2018 livekanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) inmatningsprotokollen.

## <a name="october-2017-release"></a>Version oktober 2017
> [!IMPORTANT] 
> Media Services avvecklat stöd för Azure Access Control Service autentiseringsnycklar. Den 22 juni 2018 kan du inte längre autentisera med Media Services backend-servern via kod med hjälp av Access Control Service nycklar. Du måste uppdatera din kod för att använda Azure Active Directory (Azure AD) per [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md). Håll utkik efter varningar om den här ändringen i Azure-portalen.

### <a name="updates-for-october-2017"></a>Uppdateringar för oktober 2017
#### <a name="sdks"></a>SDK:er
* .NET SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för Access Control Service-autentisering har tagits bort från den senaste .NET-SDK på Nuget.org att främja snabbare migrering till Azure AD. 
* JAVA SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för Azure AD-autentisering har lagts till i Java-SDK. Information om hur du använder Java SDK med Media Services finns i [Kom igång med Java-klientens SDK för Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Filbaserade kodning
* Du kan nu använda Premium-kodare för att koda ditt innehåll till kodning (– HEVC) video-codec H.265 högeffektiv videon. Det finns inga prissättningen påverkas om du väljer H.265 över andra codecs, till exempel H.264. Information om – HEVC patent licenser finns i [villkoren för Online Services](https://azure.microsoft.com/support/legal/).
* För källvideo som är kodad med den H.265 (– HEVC) video-codecen, till exempel videoinspelning med hjälp av iOS11 eller GoPro Hero-6, kan du nu använda Premium-kodare eller Standard-kodare koda dessa videor. Information om patent licenser finns i [villkoren för Online Services](https://azure.microsoft.com/support/legal/).
* För innehåll som innehåller ljudspår för flera språk, märkas språk-värdena korrekt enligt motsvarande filen formatspecifikationen (till exempel ISO MP4). Du kan sedan använda Standard-kodare för att koda innehållet för direktuppspelning. Den resulterande strömningslokaliseraren visar vilka språk som ljud.
* Standard-kodare stöder nu två nya ljuddata systeminställningar, ”AAC ljud” och ”AAC god kvalitet med endast ljud”. Båda producerar stereo avancerade ljud kodning (AAC) utdata vid bithastigheter av 128 kbit/s och 192 kbit/s respektive.
* Premium-kodaren stöder nu QuickTime/MOV filformat som indata. Video-codec måste vara något av de [Apple ProRes typer som anges i den här GitHub-artikeln](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Ljudet måste vara antingen AAC eller pulsval kod modulering (PCM). Premium-kodaren stöder inte, till exempel DVC/DVCPro video och är inneslutna i QuickTime/MOV filer som indata. Standard-kodare stöder dessa codec för indatavideo.
* Följande felkorrigeringar har gjorts i kodare:

    * Du kan nu skicka jobb med hjälp av en inkommande tillgång. När dessa jobb är klar kan du ändra tillgången (till exempel lägga till, ta bort eller byter namn på filerna i tillgången), och skicka ytterligare jobb.
    * Kvaliteten på JPEG miniatyrer genereras av Standard-kodare förbättras.
    * Standard-kodare hanterar indatametadata och miniatyrbilder bättre i mycket kort tid videor.
    * Förbättringar av H.264-avkodare som används i Standard-kodare eliminera vissa sällsynta artefakter. 

#### <a name="media-analytics"></a>Medieanalys
Allmän tillgänglighet för Azure Media Redactor: Den här medieprocessor utför maskering av suddiga ansikten för valda individer och är perfekt för användning i offentliga säkerhet samt nyhetsmediescenarier. 

En översikt över den här nya processor finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-redactor/). Information om inställningar för och dokumentation finns i [redigera ansikten med Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Juni 2017-versionen

Media Services har nu stöd [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Media Services stöder för närvarande, Access Control Service-autentiseringsmodellen. Access Control Service auktorisering upphör att gälla den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="march-2017-release"></a>Mars 2017

Du kan nu använda Standard-kodare till [Autogenerera en bithastighetsstege](media-services-autogen-bitrate-ladder-with-mes.md) genom att ange den ”Adaptiv direktuppspelning” förinställda strängen när du skapar ett kodningsjobb. Använda förinställningen ”Adaptiv direktuppspelning” för att koda en video för direktuppspelning med Media Services. Om du vill anpassa en kodning som förinställning för ditt specifika scenario, kan du börja med [dessa förinställningar](media-services-mes-presets-overview.md).

Du kan nu använda Media Encoder Standard eller Media Encoder Premium Workflow till [skapa ett kodningsjobb som genererar fMP4-segment](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Februari 2017-versionen

Från den 1 April 2017 tas alla jobbposter i ditt konto som är äldre än 90 dagar bort automatiskt, tillsammans med dess associerade uppgiftsposter. Borttagningen sker även om det totala antalet poster är lägre än den maximala kvoten. Om du vill arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs i [hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Januari 2017-versionen

I Media Services representerar en slutpunkt för direktuppspelning en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett nätverk för innehållsleverans (CDN) för vidare distribution. Media Services tillhandahåller också en smidig integrering med Azure Content Delivery Network. Den utgående dataströmmen från en StreamingEndpoint-tjänst kan vara en direktsänd dataström, en video på begäran eller en progressiv nedladdning av din tillgång i Media Services-kontot. Varje Media Services-konto innehåller en standardslutpunkt för direktuppspelning. Du kan skapa ytterligare slutpunkter för direktuppspelning under kontot. 

Det finns två versioner av slutpunkter för direktuppspelning, 1.0 och 2.0. Alla nya Media Services-konton innehåller från den 10 januari 2017 den version 2.0 standardslutpunkt för direktuppspelning. Ytterligare slutpunkter för direktuppspelning som du lägger till det här kontot är också version 2.0. Den här ändringen påverkar inte befintliga konton. Befintliga slutpunkter för direktuppspelning är version 1.0 och kan uppgraderas till version 2.0. Det finns beteende, fakturering och ändringarna med den här ändringen. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

Från och med den 2.15 versionen, lägga Media Services till följande egenskaper i entiteten strömmande slutpunkt:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Mer information om dessa egenskaper finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>December 2016-versionen

 Du kan nu använda Media Services att få åtkomst till telemetri/mått data för sina tjänster. Du kan använda den aktuella versionen av Media Services för att samla in telemetridata för livekanal, slutpunkt för direktuppspelning och arkivera entiteter. Mer information finns i [medietjänster telemetri](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Juli 2016-versionen
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Uppdateringar av manifestfilen (*. ISM) som genereras av kodningsuppgifter
När ett kodningsjobb skickas till Media Encoder Standard eller Media Encoder Premium encoding uppgiften genererar en [strömmande manifestfilen](media-services-deliver-content-overview.md) (* .ism) i utdatatillgången. Syntaxen för den här strömmande manifestfilen har uppdaterats med den senaste tjänstversionen.

> [!NOTE]
> Syntaxen för strömmande manifest (.ism)-fil är reserverat för internt bruk. Den kan komma att ändras i framtida versioner. Inte ändra eller manipulera innehållet i den här filen.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>En ny klient-manifest (*. ISMC) filen skapades i utdatatillgången när ett kodningsjobb matar ut en eller flera MP4-filer
Från och med den senaste tjänstversionen efter att ett kodningsjobb som genererar en eller flera MP4-filer, innehåller utdatatillgången också en strömmande klienten manifest (*.ismc) fil. Filen .ismc hjälper till att förbättra prestandan för dynamisk strömning. 

> [!NOTE]
> Syntaxen för den manifest (.ismc)-filen är reserverat för internt bruk. Den kan komma att ändras i framtida versioner. Inte ändra eller manipulera innehållet i den här filen.
> 
> 

Mer information finns i [den här bloggen](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Kända problem
Vissa klienter kan stöta på ett problem för upprepningar tagg i manifestet Smooth Streaming. Mer information finns i [i det här avsnittet](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>April 2016-versionen
### <a name="media-analytics"></a>Medieanalys
 Media Services introducerades Media Analytics för kraftfull video intelligens. Mer information finns i [översikt över Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (förhandsversion)
Du kan nu använda Media Services för att dynamiskt kryptera din HTTP Live Streaming (HLS) innehåll med Apple FairPlay. Du kan också använda av Media Services licensleveranstjänst för att leverera FairPlay-licenser till klienter. Mer information finns i ”använda Azure Media Services för att strömma HLS-innehåll som skyddats med Apple FairPlay”.

## <a id="feb_changes16"></a>Februari 2016-versionen
Den senaste versionen av Media Services SDK för .NET (3.5.3) innehåller en buggfix för Google Widevine-relaterade. Det gick inte att återanvända AssetDeliveryPolicy för flera resurser som är krypterat med Widevine. Som en del av den här felkorrigering, har följande egenskap lagts till SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Januari 2016-versionen
Encoding-reserverade enheter ändrades för att minska förvirring med encoder namn.

I Basic, Standard och Premium encoding-reserverade enheter har bytt namn till S1, S2, och S3 mediereserverade enheter, respektive. Kunder som använder grundläggande kodningsreserverade enheterna idag Se S1 som etikett i Azure-portalen (och på fakturan). Kunder som använder Standard och Premium finns i respektive etiketterna S2 och S3. 

## <a id="dec_changes_15"></a>December 2015-versionen

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder utfasning meddelande

 Media Encoder upphör att gälla från och med cirka 12 månader från versionen av Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK för PHP
Azure SDK-teamet publicerat en ny version av den [Azure SDK för PHP](http://github.com/Azure/azure-sdk-for-php) paket som innehåller uppdateringar och nya funktioner för Media Services. I synnerhet Media Services SDK för PHP nu stöder senaste [innehållsskydd](media-services-content-protection-overview.md) funktioner. Dessa funktioner är dynamisk kryptering med AES och DRM (PlayReady och Widevine) med och utan begränsningar för token. Det stöder också skalning [kodning enheter](media-services-dotnet-encoding-units.md).

Mer information finns i:

* Följande [kodexempel](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) hjälper dig att snabbt komma igång:
  * **vodworkflow_aes.php**: Den här PHP-filen visar hur du använder dynamisk kryptering för AES-128 och nyckelleveranstjänst. Den är baserad på .NET-exempel som beskrivs i [dynamisk användning AES-128-kryptering och nyckelleveranstjänst](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Den här PHP-filen visar hur du använder dynamisk PlayReady-kryptering och av licensleveranstjänst. Den är baserad på .NET-exempel som beskrivs i [använda PlayReady och/eller Widevine dynamic common kryptering](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Den här PHP-filen visar hur du skalar encoding-reserverade enheter.

## <a id="nov_changes_15"></a>November 2015-versionen
 Media Services erbjuder nu Widevine licensleveranstjänst i molnet. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se även [den här självstudien](media-services-protect-with-playready-widevine.md) och [GitHub-lagringsplatsen](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevines licensleveranstjänster som tillhandahålls av Media Services finns i förhandsversion. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versionen för oktober 2015
Media Services är nu live i följande datacenter: Södra Brasilien, västra Indien, södra och Indien, centrala. Du kan nu använda Azure portal för att [skapa Media Service-konton](media-services-portal-create-account.md) och utföra olika uppgifter som beskrivs i den [Media Services-webbsidan](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding aktiverat inte i dessa datacenter. Dessutom inte alla typer av kodningsreserverade enheter finns i dessa datacenter.

* Södra Brasilien:                                          Endast Standard och Basic kodningsreserverade enheter är tillgängliga.
* Indien, västra, Indien, södra och Indien, centrala Indien:             Basic encoding-reserverade enheter är bara tillgängliga.

## <a id="september_changes_15"></a>Utgåvan från september 2015
Media Services erbjuder nu möjlighet att skydda både video på begäran och direktsända strömmar med Widevine modulära DRM-teknik. Du kan använda följande leverans services partner för att leverera Widevine-licenser:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Du kan använda [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine. 
* Media Services lagt till stöd för Apple ProRes videor. Du kan nu överföra QuickTime videor källfilerna som använder Apple ProRes eller andra codec-enheter. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Du kan nu använda Media Encoder Standard göra utvinning av underklipp och live-Arkiv. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Följande filtrering uppdateringar har gjorts: 
  
  * Nu kan du använda Apple HLS-format med ett filter med enbart ljud. Du kan använda den här uppdateringen för att ta bort en ljuddata spår genom att ange (enbart ljud = false) i URL: en.
  * När du definierar filter för dina resurser kan du nu kan kombinera flera (upp till tre) filter i en enskild URL.
    
    Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services stöder nu jag ramar i HLS version 4. Jag bildruta support optimerar spola framåt och bakåt. Som standard är alla HLS version 4 utdata jag bildruta spellistan (EXT-X-I-FRAME-STREAM-INF).
Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Augusti 2015-versionen
* Media Services SDK för Java-versionen 0.8.0 och nya exempel är nu tillgängliga. Mer information finns i:
    
* Azure Media Player har uppdaterats med stöd för flera ljudström. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Versionen för juli 2015
* Den allmänna tillgängligheten för Media Encoder Standard lanserades. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard använder förinställningar, enligt beskrivningen i [i det här avsnittet](https://go.microsoft.com/fwlink/?LinkId=618336). När du använder en förinställning för 4K kodar, få Premium reserverade enhetstypen. Mer information finns i [skala kodning](media-services-scale-media-processing-overview.md).
* Live i realtid undertexter användes med Media Services och Media Player. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.4.0.0. Följande uppdateringar har gjorts: 

* Stöd har införts för live-arkivet. Du kan inte hämta en tillgång som innehåller en live-arkivet.
* Stöd har införts för dynamiska filter.
* Funktionen implementerades så att användarna kan hålla en lagringsbehållare medan de ta bort en tillgång.
* Felkorrigeringar har gjorts rör återförsöksprinciper i kanaler.
* Media Encoder Premium Workflow har aktiverats.

## <a id="june_changes_15"></a>Versionen för juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.3.0.0. Följande uppdateringar har gjorts: 

* Stöd har lagts till för OpenId Connect discovery-specifikationen.
* Stöd har lagts till för hantering av förnyelse av nycklar på identitet providern sida.

Om du använder en identitetsprovider som exponerar ett OpenID Connect discovery-dokument (som Azure AD, Google och Salesforce gör) du kan instruera Media Services för att hämta Signeringsnycklar för verifiering av JSON Web token (JWTs) från OpenID Connect discovery-specifikationen. 

Mer information finns i [använda JSON web nycklar från OpenID Connect discovery-specifikationen att arbeta med JWT-autentisering i Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Maj 2015-versionen
Följande nya funktioner har meddelats:

* [En förhandsgranskning av live encoding med Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamiska manifest](media-services-dynamic-manifest-overview.md)
* [En förhandsversion av Azure Media Hyperlapse mediebearbetare](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>April 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) lanserades.
* Från och med Media Services REST 2.10, Channel som konfigurerats för att mata in en Real-Time RTMP (Messaging Protocol) skapas med primära och sekundära mata in URL: er. Mer information finns i [konfigurationer för kanalinmatning](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer har uppdaterats.
* Stöd för spansk har lagts till.
* En ny konfiguration för XML-format har lagts till.

Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.2.0.0. Följande uppdateringar har gjorts:

* Icke-bakåtkompatibel ändring: TokenRestrictionTemplate.Issuer och TokenRestrictionTemplate.Audience har ändrats för att vara av typen string.
* Uppdateringar har gjorts relaterade till att skapa anpassade återförsöksprinciper.
* Felkorrigeringar har gjorts rör överföring och hämtning av filer.
* Klassen MediaServicesCredentials accepterar nu slutpunkter för kontroll av primära och sekundära åtkomst att autentisera mot.

## <a id="march_changes_15"></a>Mars 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* Media Services erbjuder nu Content Delivery Network-integration. Egenskapen CdnEnabled har lagts till StreamingEndpoint för att stödja integrationen. CdnEnabled kan användas med REST API: er från och med version 2.9. Mer information finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled kan användas med .NET SDK från och med version 3.1.0.2. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Media Encoder Premium Workflow lanserades. Mer information finns i [Vi presenterar Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Februari 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
Media Services REST-API är nu version 2.9. Från och med den här versionen kan aktivera du Content Delivery Network-integration med slutpunkter för direktuppspelning. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Januari 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
Den allmänna tillgängligheten för innehållsskydd med dynamisk kryptering lanserades. Mer information finns i [Media Services förbättrar strömmande säkerhet med DRM-teknik är allmänt](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.1.0.1.

Den här versionen markerad Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate Standardkonstruktorn som föråldrad. Konstruktorn new använder TokenType som ett argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>December 2014-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* Vissa uppdateringar och nya funktioner har lagts till i Media Indexer. Mer information finns i [viktig för Azure Media Indexer version 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Ett nytt REST-API har lagts till som du kan använda för att uppdatera encoding-reserverade enheter. Mer information finns i [EncodingReservedUnitType med REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* CORS-stöd har lagts till för nyckelleveranstjänst.
* Prestandaförbättringar har gjorts för frågor alternativ för auktoriseringsprinciper.
* I datacenter Kina den [leverans webbadress](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) är nu per kund (precis som i andra datacenter).
* Varaktighet för HLS automatisk mål har lagts till. När du gör direktsänd strömning är HLS alltid paketerat dynamiskt. Som standard beräknar Media Services automatiskt HLS segment paketering förhållandet (FragmentsPerSegment) baserat på det bildrutan intervallet (KeyFrameInterval). Den här metoden kallas också för en grupp med bilder (GOP) som tas emot från live-kodare. Mer information finns i [arbete med Media Services live direktuppspelning](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Den [Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) är nu version 3.1.0.0. Följande uppdateringar har gjorts:

* .NET SDK-beroende har uppgraderats till .NET 4.5 Framework.
* Ett nytt API som du kan använda för att uppdatera encoding-reserverade enheter har lagts till. Mer information finns i [uppdatering reserverade enhetstypen och öka kodningsreserverade enheter med hjälp av .NET](media-services-dotnet-encoding-units.md).
* JWT-stöd för autentisering med enhetstoken har lagts till. Mer information finns i [autentisering för JWT-token i Media Services och dynamisk kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativa förskjutningar för BeginDate och ExpirationDate i PlayReady-licensmall har lagts till.

## <a id="november_changes_14"></a>November 2014-versionen
* Du kan nu använda Media Services för att mata in live Smooth Streaming (fMP4)-innehåll via en SSL-anslutning. För att mata in via SSL, se till att uppdatera URL: en inmatning till HTTPS. För närvarande stöder Media Services inte SSL med anpassade domäner. Läs mer om direktsänd strömning [arbeta med Azure Media Services Live Streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* För närvarande kan du mata in en direktsänd dataström RTMP över en SSL-anslutning.
* Du kan strömma via SSL endast om slutpunkten för direktuppspelning som du kan leverera ditt innehåll har skapats efter den 10 September 2014. Om din strömmande URL: er baseras på de slutpunkter för direktuppspelning som skapats efter den 10 September 2014 innehåller URL: en ”streaming.mediaservices.windows.net” (det nya formatet). Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det äldre formatet) stöder inte SSL. Om din URL är i det äldre formatet och du vill spela över SSL, [skapa en ny slutpunkt för direktuppspelning](media-services-portal-manage-streaming-endpoints.md). Använd webbadresserna baserat på den nya strömmande slutpunkten för att strömma ditt innehåll via SSL.

## <a id="october_changes_14"></a>Oktober 2014-versionen
### <a id="new_encoder_release"></a>Media Services Encoder-version
 Den nya versionen av Azure Media Encoder för Media Services har meddelats. Med den senaste Media Encoder, du debiteras endast för utdata GB. Annars är den nya encoder funktionen som är kompatibel med den föregående kodaren. Mer information finns i [prisinformation om Media Services].

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK för .NET-tilläggen är nu version 2.0.0.3.

Media Services SDK för .NET är nu version 3.0.0.8. Följande uppdateringar har gjorts:

* Omstrukturering har implementerats i återförsöksklasser för principen.
* En användaragentsträng lades till i HTTP-begärans sidhuvud.
* Ett byggsteg för återställning av NuGet har lagts till.
* Scenario-tester har lösts till använder x509 certifikat från databasen.
* Verifieringsinställningar har lagts till för när kanal och strömmande slutpunkt uppdaterar.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nya GitHub-lagringsplatsen till värd Media Services-exempel
Exempel finns i den [GitHub-lagringsplatsen för Media Services-exempel](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>September 2014-versionen
Media Services REST-metadata är nu version 2.7. Mer information om de senaste uppdateringarna för REST finns i den [Media Services REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK för .NET är nu version 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Större ändringar
* Ursprung har bytt namn till [StreamingEndpoint].
* En ändring har gjorts i standardbeteendet när du använder Azure-portalen för att koda och publicera MP4-filer.

### <a id="sept_14_GA_changes"></a>Nya funktioner/scenarier som ingår i den allmänna tillgängliga versionen
* Mediebearbetare Media Indexer introducerades. Mer information finns i [indexera mediefiler med Media Indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Du kan använda den [StreamingEndpoint] entitet för att lägga till anpassade domännamn (värd).
  
    Om du vill använda ett anpassat domännamn som namnet på Media Services strömmande slutpunkt, att lägga till anpassade värdnamn till slutpunkten för direktuppspelning. Använd Media Services REST API: er eller .NET SDK för att lägga till anpassade värdnamn.
  
    Följande gäller:
  
  * Du måste ha ägarskapet för det anpassade domännamnet.
  * Ägare till domännamnet måste verifieras av Media Services. Verifiera domänen genom att skapa en CNAME-post som mappar den MediaServicesAccountId överordnade domänen för att verifiera DNS-mediaservices-dns-zon.
  * Du måste skapa en annan CNAME-post som mappar det anpassa värdnamnet (till exempel sports.contoso.com) till ditt Media Services StreamingEndpoint värdnamn (till exempel amstest.streaming.mediaservices.windows.net).

    Mer information finns i egenskapen CustomHostNames i den [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) artikeln.

### <a id="sept_14_preview_changes"></a>Nya funktioner/scenarier som ingår i den offentliga förhandsversionen
* Live direktuppspelning förhandsversion. Mer information finns i [arbete med Media Services live direktuppspelning](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Nyckelleveranstjänst. Mer information finns i [dynamisk användning AES-128-kryptering och nyckelleveranstjänst](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamisk kryptering med AES. Mer information finns i [dynamisk användning AES-128-kryptering och nyckelleveranstjänst](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Playreadys licensleveranstjänst. 
* Dynamisk kryptering med PlayReady. 
* Media Services PlayReady-licensmall. Mer information finns i den [Översikt för Media Services PlayReady-licensmall].
* Stream storage-krypterad tillgångar. Mer information finns i [Stream storage-krypterat innehåll](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Augusti 2014-versionen
När du koda en tillgång, skapas en utdatatillgången när kodningsjobbet är klar. Media Services Encoder gav metadata om utdataresultat fram till den här versionen. Från och med den här versionen, genererar kodaren även metadata om inkommande tillgångar. Mer information finns i [inkommande metadata] och [Utgående metadata].

## <a id="july_changes_14"></a>Version juli 2014
Följande felkorrigeringar har gjorts för Azure Media Services Packager och Encryptor:

* När en live-arkivet tillgång överförs till HLS, spelar bara ljud: Det här problemet åtgärdades och nu både ljud och video kan spela upp.
* När en tillgång är paketerad till HLS och AES 128-bitars kryptering för kuvert spela paketerade strömmar inte på Android-enheter: Den här buggen åtgärdades och paketerade dataströmmen spelar på Android-enheter som har stöd för HLS.

## <a id="may_changes_14"></a>Maj 2014-versionen
### <a id="may_14_changes"></a>Allmän Media Services-uppdateringar
Du kan nu använda [dynamisk paketering] att strömma HLS version 3. Stream HLS version 3, Lägg till följande format i ursprung positionerare sökvägen: * .ism/manifest(format=m3u8-aapl-v3). Mer information finns i [detta forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamisk paketering nu också har stöd för leverans HLS (version 3 och version 4) som krypterats med PlayReady utifrån Smooth Streaming statiskt krypteras med PlayReady. Information om hur du krypterar Smooth Streaming med PlayReady finns [skydda Smooth Streaming med PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.0.0.5. Följande uppdateringar har gjorts:

* Hastighet och elasticitet är bättre när du laddar upp och ladda ned medietillgångar.
* Förbättringar har gjorts i återförsök logik och tillfälliga undantagshantering: 
  
  * Tillfälligt fel identifiering och försök igen logic har förbättrats för undantag orsakade genom att fråga, spara ändringar och ladda upp eller ladda ned filer. 
  * När du får web-undantag (exempelvis under en Tokenbegäran för Access Control Service) kan misslyckas allvarliga fel snabbare nu.

Mer information finns i [logik för omprövning i Media Services SDK för .NET].

## <a id="april_changes_14"></a>April 2014 Encoder-versionen
### <a name="april_14_enocer_changes"></a>Media Services Encoder-uppdateringar
* Stöd har lagts till för att mata in AVI-filer som skapats med hjälp av icke-linjära gräset Valley EDIUS-redigeraren. I den här processen komprimeras lätt videon med hjälp av gräset Valley HQ/HQX-codec. Mer information finns i [gräset Valley tillkännager EDIUS 7 direktuppspelning via molnet].
*  Stöd har lagts till för att ange Namngivningskonventionen för filer som skapas av Media Services Encoder. Mer information finns i [kontroll Media Services Encoder utdata filnamn](https://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Stöd har lagts till för video och/eller ljud överlägg. Mer information finns i [skapa överlägg](https://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Stöd har lagts till flera video segment sätta ihop. Mer information finns i [sy ihop video segment](https://msdn.microsoft.com/library/azure/dn640504.aspx).
* En bugg har åtgärdats som har relaterade till transkodning MP4s där ljudet kodats med MPEG-1 ljud Layer 3 (även kallat MP3).

## <a id="jan_feb_changes_14"></a>Versionerna för januari/februari 2014
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 och 3.0.0.3
Några förändringar i 3.0.0.1 och 3.0.0.2:

* Problem som rör användningen av LINQ-frågor med OrderBy-uttryck har åtgärdats.
* Testa lösningar i [GitHub] delades upp i enhetsbaserade tester och scenario-baserade tester.

Mer information om ändringarna finns i den [Media Services .NET SDK 3.0.0.1 och 3.0.0.2 släpper](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Följande ändringar har gjorts i version 3.0.0.3:

* Azure storage-beroenden har uppgraderats för att använda version 3.0.3.0.
* Ett bakåtkompatibilitet problem har åtgärdats för 3.0. *.* versioner.

## <a id="december_changes_13"></a>Version december 2013
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x-versioner är inte bakåtkompatibla med 2.4.x.x versioner.
> 
> 

Den senaste versionen av Media Services SDK är nu 3.0.0.0. Du kan ladda ned det senaste paketet från NuGet eller hämta bits från [GitHub].

Från och med Media Services SDK version 3.0.0.0, som du kan återanvända den [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) token. Mer information finns i avsnittet ”återanvändning Access Control Service token” i [Anslut till Media Services med Media Services SDK för .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-tillägg 2.0.0.0
 Media Services .NET SDK-tillägg är en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar koden och gör det enklare att utveckla med Media Services. Du kan få de senaste nyheterna från [Media Services .NET SDK-tillägg](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Version november 2013
### <a name="nov_13_donnet_changes"></a>Ändringar av Media Services .NET SDK
Från och med den här versionen av Media Services SDK för .NET hanterar tillfälliga fel fel som kan uppstå när anrop görs i Media Services REST API-lagret.

## <a id="august_changes_13"></a>Augusti 2013 release
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell-cmdletar som ingår i Azure SDK-verktyg
Följande Media Services PowerShell-cmdletar ingår nu i [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Exempel: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Exempel: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Exempel: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Exempel: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Versionen för juni 2013
### <a name="june_13_general_changes"></a>Media Services-ändringar
Följande ändringar som nämns i det här avsnittet är uppdateringar som ingår i juni 2013 Media Services-versioner:

* Möjlighet att länka flera lagringskonton till ett Media Services-konto. 
    * StorageAccount
    * Asset.StorageAccountName och Asset.StorageAccount
* Möjlighet att uppdatera Job.Priority. 
* Meddelande-relaterade entiteter och egenskaper: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Jobb
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Ändringar av Media Services .NET SDK
Följande ändringar ingår i juni 2013 Media Services SDK-versioner. Senaste Media Services SDK finns på GitHub.

* Från och med version 2.3.0.0, som Media Services SDK stöder länka flera storage-konton till ett Media Services-konto. Följande API: er stöder den här funktionen:
  
    * IStorageAccount typ
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts egenskap
    * StorageAccount-egenskapen
    * StorageAccountName egenskap
  
      Mer information finns i [hantera Media Services-tillgångar över flera lagringskonton](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Meddelande-relaterade API: er. Från och med version 2.2.0.0 kan lyssna du på Azure Queue storage-meddelanden. Mer information finns i [hantera medietjänster jobb meddelanden](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions egenskap
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint typ
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType typ
* Beroende på Storage-klient SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Beroende på OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>December 2012-versionen
### <a name="dec_12_dotnet_changes"></a>Ändringar av Media Services .NET SDK
* IntelliSense: Saknas IntelliSense dokumentationen har lagts till för många typer.
* Microsoft.Practices.TransientFaultHandling.Core: Ett problem har åtgärdats där SDK fortfarande har ett beroende till en äldre version av den här sammansättningen. SDK: N refererar nu version 5.1.1209.1 av den här sammansättningen.

Korrigeringar för problem som hittas i November 2012 SDK:

* IAsset.Locators.Count: Det här antalet rapporteras nu korrekt på den nya IAsset gränssnitt efter alla positionerare tas bort.
* IAssetFile.ContentFileSize: Det här värdet har nu konfigurerats korrekt efter en överföring av IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Den här egenskapen kan nu ställas in när du skapar en resursfil. Det var tidigare skrivskyddat.
* IAssetFile.Upload(filepath): Ett problem har åtgärdats där den här metoden för synkron upload har utlöser följande fel när flera filer har överförts till tillgången. Felet ”Det gick inte att autentisera begäran-Server. Kontrollera att värdet för auktoriseringsrubriken har formaterats korrekt inklusive signaturen ”.
* IAssetFile.UploadAsync: Ett problem har åtgärdats som begränsad samtidiga överföringen av filer till fem filer.
* IAssetFile.UploadProgressChanged: Den här händelsen tillhandahålls nu av SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Den här metoden överlagring tillhandahålls nu.
* IAssetFile.DownloadAsync: Ett problem har åtgärdats som begränsad samtidiga hämtning av filer till fem filer.
* IAssetFile.Delete(): Ett problem har åtgärdats där anropande delete kan generera ett undantag om ingen fil har överförts för IAssetFile.
* Jobb: Ett problem har åtgärdats där länkning en ”MP4 Smooth strömmar uppgiften” med ”PlayReady-skydd uppgiften” med hjälp av en jobbmall för inte har skapat några uppgifter alls.
* EncryptionUtils.GetCertificateFromStore(): Den här metoden genererar inte längre en null-referensundantag på grund av att hitta certifikatet som är baserat på problem med konfigurationen av certifikat.

## <a id="november_changes_12"></a>November 2012-versionen
De ändringar som nämns i det här avsnittet har uppdateringar som ingår i November 2012 (version 2.0.0.0) SDK. Dessa förändringar kan kräva någon kod som skrivits för förhandsversionen av juni 2012 SDK versionen ändras eller har skrivits.

* Tillgångar
  
    * IAsset.Create(assetName) är den *endast* tillgången skapa funktionen. IAsset.Create överför inte längre filerna som en del av metodanropet. Använd IAssetFile för att ladda upp.
    * Metoden IAsset.Publish och uppräkningsvärde AssetState.Publish togs bort från SDK: N för tjänster. All kod som är beroende av det här värdet måste skrivas.
* FileInfo
  
    * Den här klassen har tagits bort och ersättas av IAssetFile.
  
* IAssetFiles
  
    * IAssetFile ersätter FileInfo och fungerar annorlunda. Initiera objektet IAssetFiles, följt av en uppladdning antingen genom att använda Media Services SDK eller Storage SDK för att använda den. Du kan använda följande IAssetFile.Upload överlagringar:
  
        * IAssetFile.Upload(filePath): Den här synkron metod blockerar tråden och rekommenderas endast när du överför en fil.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, positionerare, cancellationToken): Den här asynkrona metoden är den prioriterade överföringsmekanism. 
    
            Känt fel: Om du använder token annullering har överföringen avbrutits. Uppgifterna kan ha många annullering tillstånd. Du måste korrekt fånga upp och hantera undantag.
* Positionerare
  
    * Ursprung-specifika versioner har tagits bort. SAS-specifika-kontext. Locators.CreateSasLocator (tillgången, accessPolicy) markeras inaktuella eller tagits bort av allmänt tillgängliga. Se avsnittet ”positionerare” under ”nya funktioner” för uppdaterade beteendet.

## <a id="june_changes_12"></a>Juni 2012 förhandsversionen
Följande funktioner som är nytt i November-versionen av SDK:

* Ta bort entiteter
  
    * IAsset, IAssetFile, ILocator, IAccessPolicy och IContentKey objekt har nu tagits bort på objektnivå, det vill säga IObject.Delete(), i stället för att en borttagning i samlingen, det vill säga cloudMediaContext.ObjCollection.Delete(objInstance).
* Positionerare
  
    * Lokaliserare måste nu skapas med hjälp av metoden CreateLocator. De måste använda uppräkningsvärden LocatorType.SAS eller LocatorType.OnDemandOrigin som ett argument för vilken typ av positionerare som du vill skapa.
    * Nya egenskaper har lagts till positionerare för att göra det enklare att få användbara URI: er för ditt innehåll. Förändringen av lokaliserare ger bättre flexibilitet för framtida från tredje part utökningsbarhet och ökar användarvänlighet för media klientprogram.
* Stöd för asynkron metod
  
    * Asynkront stöd har lagts till för alla metoder.

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN-Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API-referens]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Prisinformation om Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[Inkommande metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Utgående metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Översikt för Media Services PlayReady-licensmall]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamisk paketering]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Logik för omprövning i Media Services SDK för .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Gräset Valley tillkännager EDIUS 7 direktuppspelning via molnet]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

