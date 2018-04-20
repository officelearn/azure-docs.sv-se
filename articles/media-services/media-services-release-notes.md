---
title: Media Services viktig information | Microsoft Docs
description: Media Services viktig information
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 20a24aa19989370c6ce82a738193a78f24b0eba9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services viktig information
Dessa viktig information för Azure Media Services sammanfattar ändringar från tidigare versioner och kända problem.

> [!NOTE]
> Vi vill gärna höra från våra kunder, så att vi kan fokusera på att åtgärda problem som påverkar dig. Om du vill rapportera ett problem eller ställa frågor, skicka ett inlägg i den [Azure Media Services MSDN-Forum].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Kända problem
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Media Services allmänna frågor

| Problem | Beskrivning |
| --- | --- |
| Några vanliga HTTP-rubriker angetts inte i REST-API. |Om du utvecklar program för Media Services med hjälp av REST-API kan du upptäcka att vissa vanliga http-huvudfält (inklusive CLIENT-REQUEST-ID, ID för FÖRFRÅGAN och RETUR-CLIENT-REQUEST-ID) stöds inte. Sidhuvuden läggs till i en kommande uppdatering. |
| Kodning i procent är inte tillåten. |Värdet på egenskapen IAssetFile.Name använder Media Services när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Därför tillåts procent-encoding inte. Värdet på egenskapen namn kan inte ha något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det kan bara finnas ett ””. för filnamnstillägget. |
| Metoden ListBlobs som ingår i Azure Storage SDK version 3.x misslyckas. |Media Services genererar SAS-URL: er baserat på de [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) version. Om du vill använda Storage SDK: N till listan blobbar i en blobbbehållare använder den [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metod som är en del av lagring SDK-version 2.x. |
| Medietjänster begränsning mekanism begränsar resursanvändningen för program som gör långa förfrågningar till tjänsten. Tjänsten kan returnera ”tjänst ej tillgänglig” 503 HTTP-statuskoden. |Mer information finns i beskrivningen av 503 HTTP-statuskod i [Media Services felkoder](media-services-encoding-error-codes.md). |
| När du frågar entiteter returneras en gräns på 1 000 enheter samtidigt eftersom offentliga RESTEN version 2 begränsar frågeresultaten 1 000 resultaten. |Använd hoppa över och ta (.NET) / uppifrån (REST) enligt beskrivningen i [exemplet .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [exemplet REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Vissa klienter kan stöta på ett problem med Upprepa tagg i manifestet Smooth Streaming. |Mer information finns i [i det här avsnittet](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objekt kan inte serialiseras och därför fungerar inte med Azure Redis-Cache. |Om du försöker att serialisera SDK AssetCollection-objekt om du vill lägga till den i Azure Redis-Cache, genereras ett undantag. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Versionshistorik för REST-API
Information om tidigare för Media Services REST API-versioner finns i [Azure Media Services REST API-referensen].

## <a name="october-2017-release"></a>Oktober 2017 version
> [!IMPORTANT] 
> Media Services sluta stöd för Azure Access Control Service autentiseringsnycklar. På den 22 juni 2018 du inte längre kan autentiseras med Media Services serverdelen via kod med hjälp av åtkomstkontrolltjänsten nycklar. Du måste uppdatera din kod för att använda Azure Active Directory (AD Azure) per [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md). Håll utkik efter varningar om den här ändringen i Azure-portalen.

### <a name="updates-for-october-2017"></a>Uppdateringar för oktober 2017
#### <a name="sdks"></a>SDK:er
* .NET SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för åtkomstkontrolltjänsten autentisering togs bort från den senaste .NET SDK: N på Nuget.org att uppmana snabbare migrering till Azure AD. 
* JAVA SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för Azure AD-autentisering har lagts till i Java SDK. Mer information om hur du använder Java SDK med Media Services finns [komma igång med Java-klient-SDK för Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Filbaserade kodning
* Du kan nu använda Premium-kodare för att koda ditt innehåll till H.265 högeffektiv videon kodning (HEVC) video-codec. Det finns ingen prisnivå inverkan om du väljer H.265 över andra codec, till exempel H.264. Information om HEVC patent licenser finns [Online Services-villkoren](https://azure.microsoft.com/support/legal/).
* Källan video som är kodade med den H.265 (HEVC) video-codecen, som till exempel video avbildas med hjälp av iOS11 eller GoPro hjälte 6 kan du nu använda Premium-kodare eller Standard-kodare för att koda videoklipp. Information om patent licenser finns [Online Services-villkoren](https://azure.microsoft.com/support/legal/).
* För innehåll som innehåller flera språk ljud spår, måste språk-värden korrekt märkas enligt motsvarande filformatsspecifikation (till exempel ISO MP4). Du kan sedan använda Standard-kodare för att koda innehåll för strömning. Resulterande strömningslokaliseraren visar vilka språk som ljud.
* Standard-kodare stöder nu två nya ljuddata systeminställningar, ”AAC ljud” och ”AAC bra ljud kvalitet”. Båda producera stereo avancerade ljud respektive kodning (AAC) utdata vid bithastigheter 128 kbit/s och 192 kbit/s.
* Premium-kodare stöder nu QuickTime/MOV filformat som indata. Video-codec måste vara något av de [Apple ProRes typer anges GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Ljuduppspelningen måste vara antingen AAC eller pulsval kod modulering (PCM). Premium-kodare stöder inte, till exempel DVC/DVCPro video omslutas i QuickTime/MOV-filer som indata. Standard-kodare stöder dessa video-codec.
* Följande felkorrigeringar har gjorts i kodare:

    * Du kan nu skicka jobb med hjälp av en inkommande tillgång. När dessa jobb är klar kan du ändra tillgången (till exempel lägga till, ta bort eller byta namn på filerna i tillgången), och skicka ytterligare jobb.
    * Kvaliteten på JPEG-miniatyrer produceras av Standard-kodare har förbättrats.
    * Standard-kodare hanterar inkommande metadata och miniatyr generation bättre i korta varaktighet videor.
    * Förbättringar av H.264 avkodaren används i Standard-kodare eliminera vissa sällsynta artefakter. 

#### <a name="media-analytics"></a>Medieanalys
Allmän tillgång till Azure Media Redactor: denna media-processor utför anonymization av suddar ut de markerade personer ytor och är idealisk för offentliga scenarier för säkerhet och nyheter media. 

En översikt på den här nya processorn finns [i det här blogginlägget](https://azure.microsoft.com/blog/azure-media-redactor/). Mer information om inställningar och dokumentation finns [redigera bort personerna bakom Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versionen för juni 2017

Nu har stöd för Media Services [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Media Services stöder för närvarande åtkomstkontrolltjänsten autentisering modellen. Access Control Service tillstånd att bli inaktuell på den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="march-2017-release"></a>Mars 2017 version

Du kan nu använda Standard-kodare till [Autogenerera en bithastighet stege](media-services-autogen-bitrate-ladder-with-mes.md) genom att ange ”anpassningsbar strömning” förinställda strängen när du skapar en kodning aktivitet. Använda förinställningen ”anpassningsbar strömning” om du vill koda en video för strömning med Media Services. Om du vill anpassa en kodning förinställda för din situation kan du börja med [dessa förinställningar](media-services-mes-presets-overview.md).

Du kan nu använda Media Encoder Standard eller Media Encoder Premium arbetsflödet [skapa en kodning uppgift som genererar fMP4 segment](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Februari 2017 version

Starta 1 April 2017 tas alla jobb poster i ditt konto som är äldre än 90 dagar bort automatiskt, tillsammans med dess tillhörande uppgifter poster. Även om det totala antalet poster som är lägre än den maximala kvoten sker. Om du vill arkivera informationen projektaktivitet/du kan använda koden som beskrivs i [hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Januari 2017 version

I Media Services representerar en strömningsslutpunkt som en tjänst för liveströmning som kan leverera innehåll direkt till ett klientprogram player eller till ett nätverk för innehållsleverans (CDN) för vidare distribution. Media Services tillhandahåller också sömlös integrering i Azure Content Delivery Network. Utgående dataströmmen från en StreamingEndpoint-tjänst kan vara en direktsänd dataström, en video på begäran eller en progressiv nedladdning av dina tillgångar i Media Services-kontot. Varje Media Services-konto innehåller standard strömmande slutpunkten. Du kan skapa ytterligare strömningsslutpunkter under kontot. 

Det finns två versioner av strömningsslutpunkter, 1.0 och 2.0. Starta 10 januari 2017 inkluderar alla Media Services-konton som nyligen skapade version 2.0 standard strömmande slutpunkten. Ytterligare strömmande slutpunkter som du lägger till det här kontot är också version 2.0. Den här ändringen påverkar inte befintliga konton. Befintliga strömningsslutpunkter version 1.0 och kan uppgraderas till version 2.0. Det finns problem, fakturering och ändringarna med den här ändringen. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

Från och med 2.15 version Media Services läggas till följande egenskaper till entiteten strömmande slutpunkt:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Mer information om dessa egenskaper finns [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>December 2016-versionen

 Du kan nu använda Media Services att komma åt telemetri/mått data för dess tjänster. Du kan använda den aktuella versionen av Media Services för att samla in telemetridata för live kanal strömmande slutpunkten och arkivera entiteter. Mer information finns i [Media Services telemetri](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Versionen för juli 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Uppdateringar till manifestfilen (*. ISM) som genereras av kodningsuppgifter
När en kodning uppgiften har skickats till Media Encoder Standard eller Premium-Media Encoder kodning uppgiften genererar en [strömmande manifestfilen](media-services-deliver-content-overview.md) (* .ism) i utdatatillgången. Syntaxen för den här strömmande manifestfilen har uppdaterats med den senaste versionen av tjänsten.

> [!NOTE]
> Syntaxen för strömmande manifestet (.ism)-fil är reserverad för internt bruk. Den kan ändras i framtida versioner. Ändra eller inte ändra innehållet i den här filen.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>En ny klient manifestet (*. ISMC)-filen har genererats i utdatatillgången när en kodning uppgift matar ut en eller flera MP4-filer
Från och med den senaste versionen av tjänsten efter att en kodning uppgiften som genererar en eller flera MP4-filer, innehåller utdatatillgången även en strömmande klienten manifestet (*.ismc) fil. Filen .ismc hjälper till att förbättra prestandan för dynamiska strömning. 

> [!NOTE]
> Syntaxen för filen för manifestet (.ismc) är reserverad för internt bruk. Den kan ändras i framtida versioner. Ändra eller inte ändra innehållet i den här filen.
> 
> 

Mer information finns i [bloggen](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Kända problem
Vissa klienter kan stöta på ett problem med Upprepa tagg i manifestet Smooth Streaming. Mer information finns i [i det här avsnittet](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>April 2016-versionen
### <a name="media-analytics"></a>Medieanalys
 Media Services introducerade Media Analytics för kraftfulla video intelligence. Mer information finns i [översikt över Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (förhandsgranskning)
Du kan nu använda Media Services för att kryptera din HTTP Live Streaming (HLS) innehåll med Apple FairPlay dynamiskt. Du kan också använda Media Services licensleveranstjänst för att leverera FairPlay-licenser till klienter. Mer information finns i ”använda Azure Media Services för att strömma ditt HLS innehåll som skyddas med Apple FairPlay”.

## <a id="feb_changes16"></a>Februari 2016-versionen
Den senaste versionen av Media Services SDK för .NET (3.5.3) innehåller en Google Widevines-relaterade felkorrigering. Det är omöjligt att återanvända AssetDeliveryPolicy för flera tillgångar krypterat med Widevine. Som en del av den här buggfix, av följande egenskap har lagts till i SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Januari 2016-versionen
Kodning reserverade enheter ändrades för att undvika sammanblandning med kodaren namn.

De Basic, Standard och Premium encoding-reserverade enheterna har bytt namn till S1 S2, och S3 reserverade enheter, respektive. Kunder som använder kodning reserverade enheter av grundläggande idag finns S1 som etikett i Azure-portalen (och i växeln). Kunder som använder Standard- och Premium finns etiketterna S2 och S3, respektive. 

## <a id="dec_changes_15"></a>December 2015-versionen

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder utfasningen meddelande

 Media Encoder att bli inaktuell från och med cirka 12 månader från versionen av Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK för PHP
Azure SDK-teamet publicerat en ny version av den [Azure SDK för PHP](http://github.com/Azure/azure-sdk-for-php) paket som innehåller uppdateringar och nya funktioner för Media Services. I synnerhet Media Services SDK för PHP nu stöder senaste [innehåll skydd](media-services-content-protection-overview.md) funktioner. Dessa funktioner är dynamisk kryptering med AES och DRM (PlayReady och Widevine) med och utan begränsningar för token. Det stöder också skalning [kodning enheter](media-services-dotnet-encoding-units.md).

Mer information finns i:

* Den [Media Services SDK för PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blogg.
* Följande [kodexempel](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) hjälper dig att snabbt komma igång:
  * **vodworkflow_aes.php**: den här PHP-filen visas hur du använder AES-128 dynamisk kryptering och tjänsten nyckel. Den är baserad på .NET-exempel som beskrivs i [Använd AES-128 dynamisk kryptering och tjänsten key](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: den här PHP-filen visas hur du använder PlayReady dynamisk kryptering och licensleveranstjänst. Den är baserad på .NET-exempel som beskrivs i [Använd PlayReady och/eller Widevine dynamic vanliga kryptering](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: PHP för den här filen visar hur du skala reserverade enheter av kodning.

## <a id="nov_changes_15"></a>November 2015-versionen
 Media Services erbjuder Widevine licensleveranstjänst i molnet. Mer information finns i [bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se även [självstudierna](media-services-protect-with-playready-widevine.md) och [GitHub-lagringsplatsen](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevines licensleveranstjänster som tillhandahålls av Media Services finns i förhandsgranskningen. Mer information finns i [bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Oktober 2015-versionen
Media Services har publicerats i följande datacenter: södra Indien, västra, södra Indien och centrala Indien. Nu kan du använda Azure portal och [skapa Media Service-konton](media-services-portal-create-account.md) och utföra olika uppgifter som beskrivs i den [Media Services dokumentationen webbsidan](https://azure.microsoft.com/documentation/services/media-services/). Direktsänd kodning är inte aktiverat i dessa datacenter. Dessutom är finns inte alla typer av kodningsreserverade enheter i dessa datacenter.

* Södra Brasilien: Endast Standard- och grundläggande kodningsreserverade enheter är tillgängliga.
* Indien, västra, södra Indien och centrala Indien: endast grundläggande kodningsreserverade enheter som är tillgängliga.

## <a id="september_changes_15"></a>Utgåvan från september 2015
Media Services erbjuder nu möjlighet att skydda både video på begäran och live dataströmmar med Widevine modulära DRM-teknik. Du kan använda följande leverans services partner som hjälper dig att leverera Widevine-licenser:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Mer information finns i [bloggen](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Du kan använda [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine. 
* Media Services tillagt stöd för Apple ProRes videor. Du kan överföra QuickTime videor källfilerna som använder Apple ProRes eller andra codec. Mer information finns i [bloggen](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Du kan nu använda Media Encoder Standard för att göra utvinning subclipping och live Arkiv. Mer information finns i [bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Följande filtrera uppdateringar har gjorts: 
  
  * Nu kan du använda Apple HLS-format med ett ljuddata filter. Du kan använda den här uppdateringen för att ta bort en ljuddata spår genom att ange (ljuddata = false) i URL-Adressen.
  * När du definierar filter för dina tillgångar nu kan du kombinera flera (upp till tre) filter i en enskild URL.
    
    Mer information finns i [bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services stöder nu jag ramar i HLS version 4. Stöd för jag-ramar optimerar spola framåt och bakåt. Som standard är alla HLS version 4 utdata jag ramar spelningslista (EXT-X-I-FRAME-STREAM-INF).
Mer information finns i [bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Augusti 2015-versionen
* Media Services SDK för Java-versionen 0.8.0 och nya prov är nu tillgängliga. Mer information finns i:
  
  * [Det här blogginlägget](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [I prover Java-databasen](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player har uppdaterats med stöd för flera ljudström. Mer information finns i [i det här blogginlägget](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Versionen för juli 2015
* Den allmänna tillgängligheten för Media Encoder Standard har meddelats. Mer information finns i [i det här blogginlägget](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard använder förinställningar, enligt beskrivningen i [i det här avsnittet](http://go.microsoft.com/fwlink/?LinkId=618336). När du använder en förinställning för 4K kodar, hämta Premium reserverade enhetstyp. Mer information finns i [skala kodning](media-services-scale-media-processing-overview.md).
* Live realtid bildtexter användes med Media Services och Media Player. Mer information finns i [i det här blogginlägget](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.4.0.0. Följande uppdateringar har gjorts: 

* Stöd har implementerats för live Arkiv. Du kan inte hämta en tillgång som innehåller ett live-Arkiv.
* Stöd har implementerats för dynamiska filter.
* Funktionen implementerades så att användare kan ha en lagringsbehållare medan de ta bort en tillgång.
* Felkorrigeringar har gjorts rör försök principer i kanaler.
* Media Encoder Premium arbetsflödet har aktiverats.

## <a id="june_changes_15"></a>Versionen för juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.3.0.0. Följande uppdateringar har gjorts: 

* Stöd har lagts till för OpenId Connect discovery-specifikationen.
* Stöd har lagts till för hantering av nycklar förnyelse på identitet providern sida.

Om du använder en identitetsleverantör som Exponerar en OpenID Connect discovery-dokumentet (som Azure AD, Google och Salesforce gör) du kan instruera Media Services för att hämta Signeringsnycklar för verifiering av JSON Web token (JWTs) från OpenID Connect discovery-specifikationen. 

Mer information finns i [Använd JSON web nycklar från OpenID Connect discovery-specifikationen för JWT-autentisering i Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Maj 2015-versionen
Följande nya funktioner har meddelats:

* [En förhandsgranskning av live encoding med Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamisk manifest](media-services-dynamic-manifest-overview.md)
* [En förhandsgranskning av medieprocessor Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>April 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) har meddelats.
* Från och med Media Services REST 2.10 kanaler som är konfigurerade för att mata in en realtid RTMP (Messaging Protocol) skapas med primära och sekundära infognings-URL: er. Mer information finns i [kanal infognings-konfigurationer](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer har uppdaterats.
* Stöd för spanska språk har lagts till.
* En ny konfiguration för XML-format har lagts till.

Mer information finns i [bloggen](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.2.0.0. Följande uppdateringar har gjorts:

* Att ändra: TokenRestrictionTemplate.Issuer och TokenRestrictionTemplate.Audience har ändrats för att vara av typen string.
* Uppdateringar har gjorts relaterat till att skapa principer för anpassade försök igen.
* Felkorrigeringar har gjorts avseende överföring och hämtning av filer.
* Klassen MediaServicesCredentials accepterar nu primära och sekundära access control slutpunkter autentiseras mot.

## <a id="march_changes_15"></a>Mars 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* Media Services tillhandahåller nu innehållsleveransnätverk integrering. Egenskapen CdnEnabled har lagts till StreamingEndpoint för att stödja integrationen. CdnEnabled kan användas med REST API: er från och med version 2.9. Mer information finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled kan användas med .NET SDK från och med version 3.1.0.2. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Media Encoder Premium arbetsflödet har meddelats. Mer information finns i [introduktion till Premium kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Februari 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
Media Services REST API är nu version 2.9. Från och med den här versionen kan aktivera du innehållsleveransnätverk-integrering med strömningsslutpunkter. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Januari 2015-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
Den allmänna tillgängligheten för innehållsskydd med dynamisk kryptering har meddelats. Mer information finns i [Media Services ger ökad säkerhet för strömning med allmän tillgänglighet av DRM-tekniken](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.1.0.1.

Den här versionen markerade Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate standardkonstruktor som föråldrad. Konstruktorn new tar TokenType som ett argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>December 2014-versionen
### <a name="general-media-services-updates"></a>Allmän Media Services-uppdateringar
* Vissa uppdateringar och nya funktioner har lagts till indexeraren Media. Mer information finns i [Azure Media Indexer version 1.1.6.7 viktig information](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Ett nytt REST-API har lagts till som du kan använda för att uppdatera kodning reserverade enheter. Mer information finns i [EncodingReservedUnitType med övriga](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Stöd för CORS har lagts till för tjänsten nyckel.
* Prestandaförbättringar har gjorts i fråga auktorisering principalternativ.
* I datacentret Kina den [nyckeln leverans URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) är nu per kund (precis som i andra datacenter).
* Varaktighet för HLS automatiskt mål har lagts till. När du gör direktsänd strömning måste paketeras HLS alltid dynamiskt. Som standard beräknas automatiskt Media Services HLS segment paketering förhållandet (FragmentsPerSegment) baserat på keyframe intervall (KeyFrameInterval). Den här metoden kallas också för en grupp med bilder (GOP) som tas emot från livekodaren. Mer information finns i [fungerar med Media Services live strömmande](http://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Den [Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) är nu version 3.1.0.0. Följande uppdateringar har gjorts:

* .NET SDK-beroendet har uppgraderats till .NET-Framework 4.5.
* Ett nytt API där du kan använda för att uppdatera kodning reserverade enheter har lagts till. Mer information finns i [uppdatering reserverade enhetstyp och öka kodningsreserverade enheter med hjälp av .NET](media-services-dotnet-encoding-units.md).
* JWT-stöd för token-autentisering har lagts till. Mer information finns i [JWT-token autentisering i Media Services och dynamisk kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativa förskjutningar för BeginDate och ExpirationDate i mallen PlayReady-licens har lagts till.

## <a id="november_changes_14"></a>November 2014-versionen
* Du kan nu använda Media Services för att mata in Smooth Streaming (fMP4) innehållet via en SSL-anslutning. För att mata in via SSL, se till att uppdatera infognings-URL: en till HTTPS. Media Services kan för närvarande inte stöder SSL anpassade domäner. Läs mer om direktsänd strömning [arbeta med Azure Media Services Liveströmning](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Du kan för närvarande mata in en direktsänd dataström med RTMP via en SSL-anslutning.
* Strömma via SSL endast om den strömningsslutpunkt från vilken du kan leverera ditt innehåll som har skapats efter 10 September 2014. Om din strömmande URL: er som är baserade på strömningsslutpunkter som skapats efter 10 September 2014 innehåller URL: en ”streaming.mediaservices.windows.net” (det nya formatet). Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det gamla formatet) stöder inte SSL. Om URL: en är i formatet gamla och du vill strömma via SSL, [skapa en ny strömmande slutpunkt](media-services-portal-manage-streaming-endpoints.md). Använd webbadresserna baserat på den nya strömningsslutpunkt om du vill strömma ditt innehåll via SSL.

## <a id="october_changes_14"></a>Oktober 2014-versionen
### <a id="new_encoder_release"></a>Media Services Encoder versionen
 Den nya versionen av Media Services Azure Media Encoder har meddelats. Med den senaste Media Encoder, är du debiteras för utdata GB. Annars är nya kodaren funktionen som är kompatibel med tidigare kodaren. Mer information finns i [Media Services prisinformation].

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK för .NET-tilläggen är nu version 2.0.0.3.

Media Services SDK för .NET är nu version 3.0.0.8. Följande uppdateringar har gjorts:

* Eftersom har implementerats i klasser som princip försök igen.
* En användaragentsträngen har lagts till HTTP-huvuden för begäran.
* En build återställningssteget NuGet har lagts till.
* Scenariot tester korrigerades att använda x509 certifikat från databasen.
* Verifieringsinställningarna har lagts till för när kanal och strömmande slutpunkt uppdaterar.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nya GitHub-lagringsplatsen till host Media Services-exempel
Exempel finns i den [Media Services-exempel GitHub-lagringsplatsen](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>September 2014-versionen
Media Services REST-metadata är nu version 2.7. Läs mer om de senaste uppdateringarna för RESTEN av [Media Services REST API-referensen](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK för .NET är nu version 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Gör ändringar
* Ursprung har bytt namn till [StreamingEndpoint].
* En ändring har gjorts i standardbeteendet när du använder Azure-portalen för att koda och publicera MP4-filer.

### <a id="sept_14_GA_changes"></a>Nya funktioner/scenarier som ingår i den allmänna tillgänglighet versionen
* Medieprocessor Media indexeraren introducerades. Mer information finns i [Index mediefiler med Media indexeraren](http://msdn.microsoft.com/library/azure/dn783455.aspx).
* Du kan använda den [StreamingEndpoint] entitet att lägga till anpassade domännamn (värd).
  
    Lägga till anpassade värdnamn din strömningsslutpunkt om du vill använda ett anpassat domännamn som namnet på Media Services strömmande slutpunkt. Lägga till anpassade värdnamn med hjälp av Media Services REST API: er eller .NET SDK.
  
    Följande gäller:
  
  * Du måste ha ägarskap för det anpassade domännamnet.
  * Ägarskap för domännamnet måste verifieras av Media Services. Skapa en CNAME-post som mappar MediaServicesAccountId överordnade domänen för att verifiera DNS-mediaservices-dns-zon för att verifiera domänen.
  * Du måste skapa en annan CNAME-post som mappar det anpassa värdnamnet (till exempel sports.contoso.com) till ditt Media Services StreamingEndpoint värdnamn (exempelvis amstest.streaming.mediaservices.windows.net).

    Mer information finns i egenskapen CustomHostNames i den [StreamingEndpoint](http://msdn.microsoft.com/library/azure/dn783468.aspx) artikel.

### <a id="sept_14_preview_changes"></a>Nya funktioner/scenarier som ingår i den offentliga förhandsversionen
* Dynamisk strömmande förhandsgranskning. Mer information finns i [fungerar med Media Services live strömmande](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Viktiga leverans av tjänsten. Mer information finns i [Använd AES-128 dynamisk kryptering och tjänsten key](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamisk AES-kryptering. Mer information finns i [Använd AES-128 dynamisk kryptering och tjänsten key](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Playreadys licensleveranstjänst. 
* PlayReady dynamisk kryptering. 
* Media Services PlayReady license-mall. Mer information finns i [Media Services PlayReady licens mall översikt].
* Dataströmmen lagring krypteras tillgångar. Mer information finns i [strömma lagring-krypterat innehåll](http://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Augusti 2014-versionen
När du koda en tillgång, skapas en utdatatillgången när kodningsjobbet är klar. Media Services-kodaren gav metadata om utdata tillgångar förrän den här versionen. Från och med den här versionen, producerar kodaren också metadata om inkommande tillgångar. Mer information finns i [ange metadata] och [utdata metadata].

## <a id="july_changes_14"></a>Versionen som juli 2014
Följande felkorrigeringar har gjorts för Azure Media Services Paketeraren och Krypterare:

* När en levande Arkiv tillgång överförs till HLS, bara ljud spelar upp: det här problemet åtgärdades och nu både ljud och video kan spela upp.
* När en tillgång paketeras till HLS och AES 128-bitars kryptering för kuvert paketerade strömmar inte spela upp på Android-enheter: det här felet åtgärdades och paketerade dataströmmen spelar upp på Android-enheter som har stöd för HLS.

## <a id="may_changes_14"></a>Kan 2014-versionen
### <a id="may_14_changes"></a>Allmän Media Services-uppdateringar
Du kan nu använda [dynamisk paketering] till strömmen HLS version 3. Dataströmmen HLS version 3, lägga till följande format till locator ursprungssökväg: * .ism/manifest(format=m3u8-aapl-v3). Mer information finns i [bloggen](http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/).

Dynamisk paketering nu stöder också leverera HLS (version 3 och version 4) som krypterats med PlayReady baserat på Smooth Streaming statiskt krypteras med PlayReady. Mer information om hur du krypterar Smooth Streaming med PlayReady finns [skydda Smooth Streaming med PlayReady](http://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.0.0.5. Följande uppdateringar har gjorts:

* Hastighet och återhämtning är bättre när du ladda upp och hämta media tillgångar.
* Förbättringar har gjorts i försök logik och tillfälligt undantagshantering: 
  
  * Tillfälligt fel identifiering och försök igen logik har förbättrats för undantag som orsakas när du frågar, spara ändringar, och överföra eller hämta filer. 
  * När du får web undantag (till exempel under ett åtkomstkontrolltjänsten tokenbegäran) misslyckas allvarliga fel snabbare nu.

Mer information finns i [försök logiken i Media Services SDK för .NET].

## <a id="april_changes_14"></a>April 2014 kodarens version
### <a name="april_14_enocer_changes"></a>Media Services Encoder uppdateringar
* Stöd har lagts till för att mata in AVI-filer som skapats med hjälp av den linjära bete DAL EDIUS-redigeraren. I den här processen komprimeras lätt videon med bete DAL HQ/HQX codec. Mer information finns i [bete DAL annonserar EDIUS 7 strömning via molnet].
*  Stöd har lagts till för att ange Namngivningskonventionen för de filer som skapas av Media Services-kodaren. Mer information finns i [kontrollen Media Services Encoder utdata-filnamn](http://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Stöd har lagts till för video eller ljud överlägg. Mer information finns i [skapa överlägg](http://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Stöd har lagts till sammanfoga flera video segment. Mer information finns i [häfta video segment](http://msdn.microsoft.com/library/azure/dn640504.aspx).
* Ett programfel har åtgärdats som är relaterade till omkodning MP4s där ljuduppspelningen kodats med MPEG-1 ljud Layer 3 (även kallat MP3).

## <a id="jan_feb_changes_14"></a>Januari/februari 2014 versioner
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 och 3.0.0.3
Ändringar i 3.0.0.1 och 3.0.0.2 är:

* Problem relaterade till användningen av LINQ-frågor med OrderBy-uttryck har lösts.
* Testa lösningar i [GitHub] delades upp i enhetsbaserad testerna och scenariobaserade test.

Läs mer om ändringarna i den [Media Services .NET SDK 3.0.0.1 och 3.0.0.2 släpper](http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/).

Följande ändringar har gjorts i version 3.0.0.3:

* Azure storage-beroenden har uppgraderats för att använda version 3.0.3.0.
* En bakåtkompatibilitet problemet åtgärdades för 3.0. *.* versioner.

## <a id="december_changes_13"></a>December 2013 release
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x versioner är inte bakåtkompatibla med 2.4.x.x versioner.
> 
> 

Den senaste versionen av Media Services SDK är nu 3.0.0.0. Du kan hämta det senaste paketet från NuGet eller hämta bits från [GitHub].

Från och med Media Services SDK version 3.0.0.0 kan du återanvända den [Azure AD åtkomstkontrolltjänsten](http://msdn.microsoft.com/library/hh147631.aspx) token. Mer information finns i avsnittet ”återanvändning åtkomstkontrolltjänsten token” i [Anslut till Media Services med Media Services SDK för .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-tillägg 2.0.0.0
 Media Services .NET SDK-tilläggen är en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar koden och gör det enklare att utveckla med Media Services. Du kan hämta den senaste bits från [Media Services .NET SDK-tillägg](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Versionen för november 2013
### <a name="nov_13_donnet_changes"></a>Media Services .NET SDK ändringar
Från och med den här versionen kan Media Services SDK för .NET handtag tillfälligt fel fel som kan uppstå när anrop som görs till Media Services REST API-lagret.

## <a id="august_changes_13"></a>Augusti 2013 release
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell-cmdletar som ingår i Azure SDK-verktyg
Följande Media Services PowerShell-cmdletar ingår nu i [Azure SDK-verktyg](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Exempel: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Exempel: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Exempel: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Exempel: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Versionen för juni 2013
### <a name="june_13_general_changes"></a>Media Services ändringar
Följande ändringar som nämns i det här avsnittet är uppdateringar som ingår i juni 2013 Media Services-versioner:

* Möjlighet att koppla flera lagringskonton till ett Media Service-konto. 
    * StorageAccount
    * Asset.StorageAccountName och Asset.StorageAccount
* Möjligheten att uppdatera Job.Priority. 
* Meddelande-relaterade entiteter och egenskaper: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Jobb
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Media Services .NET SDK ändringar
Följande ändringar är inkluderade i juni 2013 versioner av Media Services SDK. Senaste Media Services SDK finns på GitHub.

* Från och med version 2.3.0.0 Media Services SDK stöder länka flera storage-konton till ett Media Services-konto. Följande API: er stöder den här funktionen:
  
    * IStorageAccount typ
    * Egenskapen Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * StorageAccount egenskapen
    * StorageAccountName egenskapen
  
    Mer information finns i [hantera Media Services tillgångar över flera lagringskonton](http://msdn.microsoft.com/library/azure/dn271889.aspx).
* Meddelande-relaterade API: er. Från och med version 2.2.0.0, kan du lyssna på Azure Queue storage-meddelanden. Mer information finns i [hantera Media Services jobbet meddelanden](http://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Egenskapen Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint typ
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType typ
* Beroende på lagring klienten SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Beroendet av OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>December 2012-versionen
### <a name="dec_12_dotnet_changes"></a>Media Services .NET SDK ändringar
* IntelliSense: Saknas IntelliSense dokumentationen har lagts till för många typer.
* Microsoft.Practices.TransientFaultHandling.Core: Ett problem har åtgärdats där SDK fortfarande hade ett beroende till en gammal version av den här sammansättningen. SDK är nu refererar till version 5.1.1209.1 av den här sammansättningen.

Korrigeringar för problem som hittas i November 2012 SDK:

* IAsset.Locators.Count: Det här antalet nu korrekt rapporteras på nya IAsset gränssnitt efter alla lokaliserare raderas.
* IAssetFile.ContentFileSize: Det här värdet nu korrekt anges efter en överföring av IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Den här egenskapen kan nu anges när du skapar en resursfil. Det har tidigare skrivskyddat.
* IAssetFile.Upload(filepath): Ett problem har åtgärdats där den här metoden Överför synkron var att följande fel när flera filer har överförts till tillgången. Felkod: ”Det gick inte att autentisera begäran-Server. Kontrollera att värdet för Authorization-huvud är formaterad korrekt inklusive signaturen ”.
* IAssetFile.UploadAsync: Ett problem har åtgärdats som begränsad samtidiga överföringen av filer till fem filer.
* IAssetFile.UploadProgressChanged: Den här händelsen tillhandahålls nu av SDK.
* IAssetFile.DownloadAsync (sträng, BlobTransferClient ILocator, CancellationToken): den här metodöverlagringen tillhandahålls nu.
* IAssetFile.DownloadAsync: Ett problem har åtgärdats som begränsad samtidiga hämtning av filer till fem filer.
* IAssetFile.Delete(): Ett problem har åtgärdats där anropa delete kan utlösa ett undantag om ingen fil har överförts för IAssetFile.
* Jobb: Ett problem har åtgärdats där länkning en ”MP4 Smooth dataströmmar uppgiften” med ”PlayReady skydd uppgiften” med hjälp av en jobbmall för inte har skapat några aktiviteter alls.
* EncryptionUtils.GetCertificateFromStore(): Den här metoden genererar inte längre ett null-referensundantag på grund av ett fel i söka efter certifikat som baseras på problem med certifikat.

## <a id="november_changes_12"></a>November 2012-versionen
De ändringar som nämns i det här avsnittet har uppdateringar som ingår i November 2012 (version 2.0.0.0) SDK. Dessa förändringar kan kräva någon kod som skrivs för juni 2012 preview SDK-versionen ändras eller skrivas om.

* Tillgångar
  
    * IAsset.Create(assetName) är den *endast* tillgången skapa funktionen. IAsset.Create överför inte längre filerna som en del av metodanropet. Använd IAssetFile för överföring.
    * Metoden IAsset.Publish och uppräkningsvärdet AssetState.Publish togs bort från Services SDK. All kod som förlitar sig på detta värde måste skrivas.
* FileInfo
  
    * Den här klassen har tagits bort och ersättas av IAssetFile.
  
* IAssetFiles
  
    * IAssetFile ersätter FileInfo och fungerar annorlunda. Initiera objektet IAssetFiles följt av en filöverföring antingen med hjälp av Media Services SDK eller Storage SDK: N för att använda den. Du kan använda följande IAssetFile.Upload överlagringar:
  
        * IAssetFile.Upload(filePath): Den här synkron metod blockerar tråden och rekommenderas endast när du överför en fil.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, lokaliserare, cancellationToken): den här asynkrona metoden är en överföringsmekanism för prioriterade. 
    
            Känt fel: Om du använder token avbryta uppladdningen har avbrutits. Uppgifterna kan ha många annullering tillstånd. Du måste korrekt fånga och hantera undantag.
* Positionerare
  
    * Ursprung-specifika versioner har tagits bort. SAS-specifika-kontext. Locators.CreateSasLocator (tillgången, accessPolicy) markeras föråldrad eller tagits bort av allmän tillgänglighet. Se avsnittet ”lokaliserare” under ”nya funktioner” för uppdaterade beteendet.

## <a id="june_changes_12"></a>Förhandsversionen för juni 2012
Följande funktioner är nya i November-versionen av SDK:

* Ta bort enheter
  
    * IAsset, IAssetFile, ILocator, IAccessPolicy och IContentKey objekt nu raderas på objektnivå, det vill säga IObject.Delete() i stället för att en delete i d.v.s. cloudMediaContext.ObjCollection.Delete(objInstance)-samlingen.
* Positionerare
  
    * Lokaliserare måste nu skapas med hjälp av metoden CreateLocator. De måste använda uppräkningsvärden LocatorType.SAS eller LocatorType.OnDemandOrigin som ett argument för typ av lokaliserare som du vill skapa.
    * Nya egenskaper har lagts till positionerare för att göra det lättare att få användbar URI: er för ditt innehåll. Förändringen av positionerare ger mer flexibilitet för framtida från tredje part utökningsbarhet och ökar användarvänlighet för media klientprogram.
* Stöd för asynkron metod
  
    * Asynkron stöd har lagts till för alla metoder.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN-Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API-referensen]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services prisinformation]: http://azure.microsoft.com/pricing/details/media-services/
[ange metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[utdata metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady licens mall översikt]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamisk paketering]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[försök logiken i Media Services SDK för .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[bete DAL annonserar EDIUS 7 strömning via molnet]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
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

