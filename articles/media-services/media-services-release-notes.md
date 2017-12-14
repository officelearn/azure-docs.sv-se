---
title: Media Services viktig information | Microsoft Docs
description: Media Services viktig information
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 0da456e13042168f3c8e871f180e6477b73392d5
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services viktig information
Dessa versionsanmärkningar sammanfattar ändringar från tidigare versioner och kända problem.

> [!NOTE]
> Vi vill gärna höra från våra kunder och fokusera på att åtgärda problem som påverkar dig. Om du vill rapportera ett problem eller ställa frågor, bokför i den [Azure Media Services MSDN-Forum].
> 
> 

## <a id="issues"></a>Kända problem
### <a id="general_issues"></a>Allmänna problem med Media Services
| Problem | Beskrivning |
| --- | --- |
| Några vanliga HTTP-huvuden finns inte i REST-API. |Om du utvecklar Media Services-program med hjälp av REST-API kan du upptäcka att vissa vanliga http-huvudfält (inklusive CLIENT-REQUEST-ID, ID för FÖRFRÅGAN och RETUR-CLIENT-REQUEST-ID) stöds inte. Sidhuvuden läggs till i en kommande uppdatering. |
| Kodning i procent är inte tillåtet. |Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför tillåts procent-encoding inte. Värdet för den **namn** egenskapen får inte ha något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det kan bara finnas ett '.' för filnamnstillägget. |
| Metoden ListBlobs som ingår i Azure Storage SDK version 3.x misslyckas. |Media Services genererar SAS-URL: er baserat på de [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) version. Om du vill använda Azure Storage SDK: N till listan blobbar i en blobbbehållare använder den [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metod som är en del av Azure Storage SDK version 2.x. Metoden ListBlobs som är en del av Azure Storage SDK version 3.x misslyckas. |
| Media Services begränsning mekanism begränsar resursanvändningen för program som gör överdriven begäran för tjänsten. Tjänsten kan returnera tjänsten är inte tillgänglig (503) HTTP-statuskoden. |Mer information finns i beskrivningen av 503 HTTP-statuskod i den [felkoder för Azure Media Services](media-services-encoding-error-codes.md) artikel. |
| När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. |Du måste använda **hoppa över** och **ta** (.NET) / **upp** (REST) enligt beskrivningen i [exemplet .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [exemplet REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Vissa klienter kan stöta på ett problem med Upprepa tagg i manifestet Smooth Streaming. |Mer information finns i [det här](media-services-deliver-content-overview.md#known-issues) avsnittet. |
| Azure Media Services .NET SDK-objekt kan inte serialiseras och därför fungerar inte med Azure Caching. |Om du försöker att serialisera objektet om du vill lägga till den i Azure Caching SDK AssetCollection genereras ett undantag. |


## <a id="rest_version_history"></a>Versionshistorik för REST API
Information om tidigare för Media Services REST API-versioner finns [Azure Media Services REST API-referens].

## <a name="october-2017-release"></a>Oktober 2017 version
> [!IMPORTANT] 
> Påminnelse: Azure Media Services kommer att sluta stöd för ACS autentiseringsnycklar.  På den 1 juni 2018, kommer du inte längre att kunna autentisera med AMS serverdelen via kod med ACS-nycklar. Du måste uppdatera din kod för att använda Azure Active Directory (AAD) per artikeln [Azure Active Directory (Azure AD)-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md). Du får även varningar i Azure-portalen för den här ändringen.

### <a name="updates-for-october-2017-include"></a>Uppdateringar för oktober 2017 omfattar:
#### <a name="sdks"></a>SDK:er
* .NET SDK uppdateras till stöd för AAD-autentisering.  Stöd för ACS-autentisering har vi tagit bort från den senaste .NET SDK: N på Nuget.org att uppmana snabbare migrering till AAD. 
* JAVA SDK uppdateras till stöd för AAD-autentisering.  Vi har lagt till stöd för AAD-autentisering i vår Java SDK. Du kan läsa information om hur du använder Java SDK med AMS i artikeln [komma igång med Java-klient-SDK för Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Filbaserade kodning
1.  Du kan nu använda Premium-kodare för att koda ditt innehåll till H.265(HEVC) video-codec. Det finns ingen prisnivå inverkan för att välja H.265 över andra codec som H.264. Mer information finns i [Online Services-villkoren](https://azure.microsoft.com/support/legal/) viktigt om HEVC patentbeviljande i återkallningslicens(er).
2.  Om du har källa video som har kodats med H.265(HEVC) video-codec, som till exempel video till med iOS11 eller GoPro hjälte 6, kan du nu använda Premium-kodare eller Standard-kodare för att koda videoklipp. Mer information finns i [Online Services-villkoren](https://azure.microsoft.com/support/legal/) för viktigt patent återkallningslicens(er).
3.  Om du har innehåll som innehåller flera språk ljud spår, sedan så länge språk-värden är korrekt märkta enligt motsvarande filformatsspecifikation (t.ex. ISO MP4) kan du använda Standard-kodare för att koda innehållet för strömning. Resulterande strömningslokaliseraren visar en lista över tillgängliga ljud språk.
4.  Standard-kodare stöder nu två nya ljuddata systeminställningar, ”AAC ljud” och ”AAC bra kvalitet ljud”. Både resultat stereo AAC, vid bithastigheter 128 kbit/s och 192 kbps respektive.
5.  Premium-kodare stöder nu QuickTime/MOV filformat som indata så länge den video-codecen är någon av de [Apple ProRes varianter i den här listan](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats), och ljuduppspelningen AAC eller PCM.

> [!NOTE]
> Premium-kodare stöder inte, till exempel DVC/DVCPro video omslutas i QuickTime/MOV-filer som indata.  Standard-kodare stöder dessa video-codec.
>
>

6.  Felkorrigeringar i kodare:
    * Du kan nu skicka jobb med hjälp av en indata tillgång när dessa fullständig ändra tillgången (till exempel genom att lägga till/ta bort/byta namn på filerna i tillgången) och skicka ytterligare jobb. 
    * Förbättrad kvalitet för JPEG-miniatyrer produceras av Standard-kodare
    * Standard-kodare förbättringar för kort video. Bättre hantering av inkommande metadata och miniatyr generation i korta varaktighet videor.
    * Förbättringar av H.264 avkodaren används i Standard-kodare eliminerar vissa sällsynta artefakter. 

#### <a name="media-analytics"></a>Medieanalys
* GA för Azure Media Redactor - den här Media Processor (HP) utför anonymization av suddar ut de markerade personer ytor och är idealisk för offentliga scenarier för säkerhet och nyheter media. En översikt över den här nya processorn bloggposten [här](https://azure.microsoft.com/blog/azure-media-redactor/). Detaljerad dokumentation och inställningar finns i [redigera bort personerna bakom Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versionen för juni 2017

Nu har stöd för Media Services [Azure Active Directory (Azure AD)-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control service autentisering modellen. Access Control tillstånd att bli inaktuell på den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="march-2017-release"></a>Mars 2017 version

Nu kan du använda Azure Media Standard till [Autogenerera en bithastighet stege](media-services-autogen-bitrate-ladder-with-mes.md) genom att ange ”anpassningsbar strömning” förinställningen strängen när du skapar en kodning aktivitet. ”Anpassningsbar Streaming” är den rekommenderade förinställningen om du vill koda en video för strömning med Media Services. Om du behöver anpassa kodning förinställningen för ditt specifika scenario börjar du med [dessa](media-services-mes-presets-overview.md) förinställningar.

Du kan nu använda Azure Media Standard eller Media Encoder Premium arbetsflödet [skapa en kodning uppgift som genererar fMP4 segment](media-services-generate-fmp4-chunks.md). 


## <a name="february-2017-release"></a>Februari 2017 version

Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Om du behöver arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs [här](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Januari 2017 version

I Microsoft Azure Media Services (AMS), en **Strömningsslutpunkt** representerar en strömmande tjänst som kan leverera innehåll direkt till ett klientprogram player eller till en innehåll innehållsleveransnätverk (CDN) för vidare distribution. Media Services tillhandahåller också sömlös integration av Azure CDN. Utgående dataströmmen från en StreamingEndpoint-tjänst kan vara en direktsänd dataström, en video på begäran eller progressiv hämtning av dina tillgångar i Media Services-kontot. Varje Azure Media Services-konto innehåller standard StreamingEndpoint. Du kan skapa ytterligare Strömningsslutpunkter under kontot. Det finns två versioner av Strömningsslutpunkter 1.0 och 2.0. Från och med 10 januari 2017 alla nyligen skapade AMS konton innehåller version 2.0 **standard** StreamingEndpoint. Ytterligare strömmande slutpunkter som du lägger till det här kontot kommer också att version 2.0. Den här ändringen påverkar inte befintliga konton; befintliga Strömningsslutpunkter är version 1.0 och kan uppgraderas till version 2.0. Med den här ändringen det blir beteende, fakturering och ändringarna (Mer information finns i [detta](media-services-streaming-endpoints-overview.md) artikel).

Dessutom från och med version 2.15, Azure Media Services lagt till följande egenskaper till entiteten Strömningsslutpunkt: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Detaljerad översikt över de här egenskaperna finns [detta](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>December 2016-versionen

Azure Media Services kan du nu dataåtkomst telemetri/mått för dess tjänster. Den aktuella versionen av AMS kan du samla in telemetridata för live-kanal, StreamingEndpoint, och live Arkiv entiteter. Mer information finns i [detta](media-services-telemetry-overview.md) artikel.

## <a id="july_changes16"></a>Versionen för juli 2016
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Uppdateringar till manifestfil (*. ISM) som genereras av kodningsuppgifter
När en kodning uppgiften har skickats till Media Encoder Standard eller Azure Media Encoder kodning uppgiften genererar en [strömmande manifestfilen](media-services-deliver-content-overview.md) (* .ism)-fil i utdata tillgången. Syntaxen för den här strömmande manifestfilen har uppdaterats med den senaste versionen av tjänsten.

> [!NOTE]
> Syntaxen för strömmande manifestet (.ism)-fil är reserverad för internt bruk och kan ändras i framtida versioner. Ändra eller inte ändra innehållet i den här filen.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>En ny klient manifestet (*. ISMC)-filen har genererats i utdata tillgång när en kodning uppgift matar ut en eller flera MP4-filer
Från och med den senaste versionen av tjänsten efter att en kodning uppgiften som genererar en mer MP4-fil, utdata innehåller tillgång också en strömmande klienten manifestet (*.ismc) fil. Filen .ismc hjälper till att förbättra prestandan för dynamiska strömning. 

> [!NOTE]
> Syntaxen för filen för manifestet (.ismc) är reserverad för internt bruk och kan ändras i framtida versioner. Ändra eller inte ändra innehållet i den här filen.
> 
> 

Mer information finns i [detta](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blogg.

### <a name="known-issues"></a>Kända problem
Vissa klienter kan stöta på ett problem med Upprepa tagg i manifestet Smooth Streaming. Mer information finns i [det här](media-services-deliver-content-overview.md#known-issues) avsnittet.

## <a id="apr_changes16"></a>April 2016-versionen
### <a name="azure-media-analytics"></a>Azure Media Analytics
Azure Media Services introducerade Azure Media Analytics för kraftfulla video intelligence. Detaljerad information finns i [översikt över Azure Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (förhandsgranskning)
Azure Media Services kan du nu dynamiskt kryptera din HTTP Live Streaming (HLS) innehåll med Apple FairPlay. Du kan också använda AMS-Licenstjänsten leverans för att leverera FairPlay-licenser till klienter. Mer detaljerad information finns i [Använd Azure Media Services att strömma ditt innehåll skyddade HLS med Apple FairPlay.

## <a id="feb_changes16"></a>Februari 2016-versionen
Den senaste versionen av Azure Media Services SDK för .NET (3.5.3) innehåller en relaterad felkorrigering Widevine. Problemet var: AssetDeliveryPolicy gick inte att återanvändas för flera resurser som är krypterat med Widevine. Som en del av den här buggfix följande egenskap har lagts till i SDK: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Januari 2016-versionen
Kodningsreserverade enheter har fått nytt namn för att undvika sammanblandning med kodaren namn.

De Basic, Standard och Premium encoding-reserverade enheterna ändras till S1 S2, och S3 reserverade enheter, respektive.  Kunder som använder grundläggande kodning RUs idag ser S1 etikett i Azure-portalen (och i växeln) när Standard och Premium ser etiketterna S2 och S3 respektive. 

## <a id="dec_changes_15"></a>December 2015-versionen

### <a name="azure-media-encoder-deprecation-announcement"></a>Azure Media Encoder utfasningen meddelande

Azure Media Encoder att bli inaktuell från och med cirka 12 månader från versionen av Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK för PHP
Azure SDK-teamet publicerat en ny version av den [Azure SDK för PHP](http://github.com/Azure/azure-sdk-for-php) paket som innehåller uppdateringar och nya funktioner för Microsoft Azure Media Services. I synnerhet Azure Media Services SDK för PHP nu stöder senaste [innehåll skydd](media-services-content-protection-overview.md) funktioner: dynamisk kryptering med AES och DRM (PlayReady och Widevine) med och utan begränsning för Token. Det stöder också skalning [Encoding enheter](media-services-dotnet-encoding-units.md).

Mer information finns i:

* Den [Microsoft Azure Media Services SDK för PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blogg.
* Följande [kodexempel](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) för att komma igång snabbt:
  * **vodworkflow_aes.php**: Detta är en PHP-fil som visar hur du använder AES-128 dynamisk kryptering och leverans av tjänsten. Den är baserad på .NET-exempel som beskrivs i [detta](media-services-protect-with-aes128.md) artikel.
  * **vodworkflow_aes.php**: Detta är en PHP-fil som visar hur du använder PlayReady dynamisk kryptering och Licensleveranstjänst. Den är baserad på .NET-exempel som beskrivs i [detta](media-services-protect-with-playready-widevine.md) artikel.
  * **scale_encoding_units.php**: Detta är en PHP-fil som visar hur du skalar kodning enhet.

## <a id="nov_changes_15"></a>November 2015-versionen
Azure Media Services erbjuder Google Widevines licensleveranstjänst i molnet. Mer information finns i [meddelande bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se även [självstudierna](media-services-protect-with-playready-widevine.md) och [GitHub-lagringsplatsen](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevines licensleveranstjänster i Azure Media Services från Microsoft finns i förhandsgranskningen. Mer information finns i [bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Oktober 2015-versionen
Azure Media Services (AMS) har publicerats i följande datacenter: södra Indien, västra, södra Indien och centrala Indien. Nu kan du använda Azure portal och [skapa Media Service-konton](media-services-portal-create-account.md) och utföra olika uppgifter som beskrivs [här](https://azure.microsoft.com/documentation/services/media-services/). Dock är Live Encoding inte aktiverat i dessa datacenter. Dessutom finns inte alla typer av Encoding-reserverade enheter i dessa datacenter.

* Södra Brasilien:                                          Endast Encoding-reserverade enheter av grundläggande och standardtyp är tillgängliga
* Indien, västra, södra Indien och centrala Indien: endast grundläggande Encoding-reserverade enheter är tillgängliga

## <a id="september_changes_15"></a>Utgåvan från september 2015
* AMS finns nu möjlighet att skydda både Video-On-Demand (VOD) och Live dataströmmar med Widevine modulära DRM-teknik. Du kan använda följande leverans services partner som hjälper dig att leverera Widevine-licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Mer information finns i [bloggen](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    Du kan använda [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine.  
* AMS tillagt stöd för Apple ProRes videor. Du kan överföra QuickTime videor källfilerna som använder Apple ProRes eller andra codec. Mer information finns i [bloggen](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Du kan nu använda Media Encoder Standard för att göra utvinning subclipping och live Arkiv. Mer information finns i [bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Följande filtrera uppdateringar har gjorts: 
  
  * Nu kan du använda Apple HTTP Live Streaming (HLS)-format med ljuddata filter. Den här uppdateringen kan du ta bort ljuddata spåra genom att ange (ljuddata = false) i URL-Adressen.
  * När du definierar filter för dina tillgångar, nu har du möjlighet att kombinera flera (upp till 3) filter i en enskild URL.
    
    Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.
* AMS stöder nu jag ramar i HLS v4. Stöd för jag-ramar optimerar spola framåt och bakåt. Som standard är alla HLS v4-utdata I ramar spelningslista (EXT-X-I-FRAME-STREAM-INF).
  
    Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.

## <a id="august_changes_15"></a>Augusti 2015-versionen
* Azure Media Services SDK för Java V0.8.0 versionen och nya prov är nu tillgänglig. Mer information finns i:
  
  * [Blogginlägget](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Databasen för Java-exempel](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player-uppdateringen med stöd för flera ljudström. Mer information finns i:
  * [Blogginlägget](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>Versionen för juli 2015
* Tillkännagivande Allmänt om Media Encoder Standard. Mer information finns i [i det här blogginlägget](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard använder förinställningar som beskrivs i [detta](http://go.microsoft.com/fwlink/?LinkId=618336) avsnitt. När du använder en förinställning för 4k kodar, ska du hämta den **Premium** reserverade enhetstyp. Mer information finns i [så skala kodning](media-services-scale-media-processing-overview.md).
* Live realtid etiketter med Azure Media Services och spelare. Mer information finns i [i det här blogginlägget](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Azure Media Services .NET SDK är nu version 3.4.0.0. Följande funktioner har lagts till i den här versionen:  

* Implementerade stöd för live Arkiv. Du kan inte hämta en tillgång som innehåller ett live-Arkiv.
* Implementerade stöd för dynamiska filter.
* Implementerad funktionalitet som användarna kan hålla lagringsbehållaren när du tar bort tillgången.
* Felkorrigeringar som rör försök principer i kanaler.
* Aktiverad **Media Encoder Premium arbetsflöde**.

## <a id="june_changes_15"></a>Versionen för juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Azure Media Services .NET SDK är nu version 3.3.0.0. Följande funktioner har lagts till i den här versionen:  

* stöd för identifiering av OpenId Connect-specifikationen
* stöd för hantering av nycklar förnyelse på identitet providern sida. 

Om du använder en identitetsleverantör som exponerar OpenID Connect discovery-dokumentet (som följande providers: Azure Active Directory, Google, Salesforce), du kan instruera Azure Media Services för att hämta Signeringsnycklar för verifiering av JWT-token från OpenID ansluta discovery-specifikationen. 

Mer information finns i [med hjälp av Json Web nycklar från OpenID Connect identifiering spec att arbeta med JWT-token autentisering i Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Maj 2015-versionen
Om följande nya funktioner:

* [En förhandsgranskning av Live Encoding med Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamisk manifest](media-services-dynamic-manifest-overview.md)
* [En förhandsgranskning av Azure Media Hyperlapse media-processor](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>April 2015-versionen
### <a name="general-media-services-updates"></a>Allmänna Media Services-uppdateringar
* [Om Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Från och med Media Services REST 2.10 kanaler som är konfigurerade för att mata in en RTMP-protokollet skapas med primära och sekundära infognings-URL: er. Mer information finns i [kanal infognings-konfigurationer](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Azure Media Indexer uppdateringar
* Stöd för spanska språk
* Ny konfiguration av xml-format

Mer information finns i [bloggen](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Azure Media Services .NET SDK är nu version 3.2.0.0.

Följande är några av kundinriktade uppdateringar:

* **Att ändra**: ändra **TokenRestrictionTemplate.Issuer** och **TokenRestrictionTemplate.Audience** måste vara av typen string.
* Uppdateringar för att skapa anpassade försök principer.
* Felkorrigeringar som rör överför/hämtning av filer.
* Den **MediaServicesCredentials** klass accepterar nu primära och sekundära åtkomst kontrollslutpunkten autentiseras mot.

## <a id="march_changes_15"></a>Mars 2015-versionen
### <a name="general-media-services-updates"></a>Allmänna Media Services-uppdateringar
* Media Services tillhandahåller nu Azure CDN-integration. Till stöd för integrering, den **CdnEnabled** egenskapen har lagts till i **StreamingEndpoint**.  **CdnEnabled** kan användas med REST API: er från och med version 2.9 (Mer information finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)).  **CdnEnabled** kan användas med .NET SDK från och med version 3.1.0.2 (Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* Meddelande om **Media Encoder Premium arbetsflöde**. Mer information finns i [introduktion till Premium kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Februari 2015-versionen
### <a name="general-media-services-updates"></a>Allmänna Media Services-uppdateringar
Media Services REST API är nu version 2.9. Från och med den här versionen kan aktivera du Azure CDN-integrering med strömningsslutpunkter. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Januari 2015-versionen
### <a name="general-media-services-updates"></a>Allmänna Media Services-uppdateringar
Meddelande för allmän tillgänglighet (GA) för innehållsskydd med dynamisk kryptering. Mer information finns i [Azure Media Services förbättrar strömmande säkerhet med teknik för allmän tillgänglighet DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Azure Media Services .NET SDK är nu version 3.1.0.1.

Den här versionen markerade Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate standardkonstruktor som föråldrad. Konstruktorn new tar TokenType som ett argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>December 2014-versionen
### <a name="general-media-services-updates"></a>Allmänna Media Services-uppdateringar
* Vissa uppdateringar och nya funktioner har lagts till i Azure indexeraren medieprocessor. Mer information finns i [Azure Media Indexer Version 1.1.6.7 viktig information](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Lägga till ett nytt REST-API som gör att du kan uppdatera kodning reserverade enheter: [EncodingReservedUnitType med övriga](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Stöd för tillagda CORS för viktiga leverans av tjänsten.
* Prestandaförbättringar frågor till alternativ för auktorisering av principer som gjordes.
* I Kina datacenter i [nyckel leverans URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) är nu per kund (precis som i andra datacenter).
* Tillagda HLS automatiskt mål varaktighet. När du gör direktsänd strömning måste paketeras HLS alltid dynamiskt. Som standard beräknas automatiskt Media Services HLS segment paketering förhållandet (FragmentsPerSegment) baserat på keyframe intervall (KeyFrameInterval), även kallat grupp av bilder – GOP som tas emot från livekodaren. Mer information finns i [arbetar med Azure Media Services Liveströmning].

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
* [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) är nu version 3.1.0.0.
* Uppgraderas beroendet .net SDK för .NET 4.5 Framework.
* Lägga till ett nytt API där du kan uppdatera kodning reserverade enheter. Mer information finns i [uppdatering reserverade enhetstyp och öka kodning RUs med hjälp av .NET](media-services-dotnet-encoding-units.md).
* Tillagda JWT (JSON Web Token) stöd för token-autentisering. Mer information finns i [JWT-token autentisering i Azure Media Services och dynamisk kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Tillagda relativa förskjutningar för BeginDate och ExpirationDate i mallen PlayReady-licens.

## <a id="november_changes_14"></a>November 2014-versionen
* Media Services kan du nu infoga ett Smooth Streaming (FMP4) innehållet via en SSL-anslutning. För att mata in via SSL, se till att uppdatera infognings-URL: en till HTTPS.  För närvarande stöder AMS inte SSL med anpassade domäner.  Läs mer om direktsänd strömning [arbetar med Azure Media Services Liveströmning].
* Du kan för närvarande mata in en direktsänd dataström med RTMP via en SSL-anslutning.
* Du kan bara strömma via SSL om den strömningsslutpunkt från vilken du kan leverera ditt innehåll skapades efter 10 September 2014. Om din strömmande URL: er baseras på strömningsslutpunkter som skapats efter 10 September, innehåller URL: en ”streaming.mediaservices.windows.net” (det nya formatet). Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det gamla formatet) stöder inte SSL. Om URL: en är i formatet gamla och du vill kunna strömma via SSL, [skapa en ny strömmande slutpunkt](media-services-portal-manage-streaming-endpoints.md). Använd URL: er skapas baserat på den nya strömmande slutpunkten för att strömma ditt innehåll via SSL.

## <a id="october_changes_14"></a>Oktober 2014-versionen
### <a id="new_encoder_release"></a>Media Services-kodaren versionen
Om den nya versionen av Media Services Azure Media Encoder. Med den senaste Azure Media Encoder endast debiteras du för utgående GB, men annars nya kodaren är funktionen som är kompatibel med tidigare kodaren. Mer information [prisinformation för Media Services]).

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK för .NET-tilläggen är nu version 2.0.0.3.

Media Services SDK för .NET är nu version 3.0.0.8.

Följande ändringar har gjorts:

* Eftersom i klasser som princip försök igen.
* Lägger till användaragentsträngen för http-huvuden för begäran.
* Lägga till NuGet återställning build steg.
* Åtgärda scenariot testerna för att använda x509 certifikat från databasen.
* Verifiera inställningarna vid uppdatering av kanal och strömmande slutpunkt.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nya GitHub-lagringsplatsen till host Media Services-exempel
Exempel finns i [GitHub-lagringsplatsen för Azure Media Services-exempel](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>September 2014-versionen
Media Services REST-metadata är nu version 2.7. Mer information om de senaste uppdateringarna för REST finns [Azure Media Services REST API-referens].

Media Services SDK för .NET är nu version 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Gör ändringar
* **Ursprung** har bytt namn till [StreamingEndpoint].
* En förändring i standardbeteendet när du använder den **Azure-portalen** att koda och publicera MP4-filer.

### <a id="sept_14_GA_changes"></a>Nya funktioner/scenarier som ingår i GA-version
* **Indexeraren Medieprocessor**. Mer information finns i [indexering mediefiler med Azure Media Indexer].
* Den [StreamingEndpoint] entitet nu kan du lägga till anpassade domännamn (värd).
  
    Du måste lägga till anpassade värdnamn till din strömmande slutpunkten för ett anpassat domännamn som ska användas som namnet på Media Services strömmande slutpunkt. Använda Media Services REST API: er eller .NET SDK för att lägga till anpassade värdnamn.
  
    Följande gäller:
  
  * Du måste ha ägarskap för det anpassade domännamnet.
  * Ägarskap för domännamnet måste verifieras av Azure Media Services. Skapa en CNAME-post som mappar för att verifiera domänen <MediaServicesAccountId> <parent domain> att verifiera DNS. < mediaservices dns-zon >. 
  * Du måste skapa en annan CNAME-post som mappar det anpassa värdnamnet (t.ex: sports.contoso.com) till värdnamnet för ditt Media Services StreamingEndpont (t ex: amstest.streaming.mediaservices.windows.net).

    Mer information finns i **CustomHostNames** egenskap i den [StreamingEndpoint] artikel.

### <a id="sept_14_preview_changes"></a>Nya funktioner/scenarier som ingår i den offentliga förhandsversionen
* Dynamisk strömmande förhandsgranskning. Mer information finns i [arbetar med Azure Media Services Liveströmning].
* Viktiga Delivery Service. Mer information finns i [med hjälp av dynamisk AES-128-kryptering och leverans av tjänsten].
* Dynamisk AES-kryptering. Mer information finns i [med hjälp av dynamisk AES-128-kryptering och leverans av tjänsten].
* PlayReady Licensleveranstjänst. Mer information finns i [med PlayReady dynamisk kryptering och Licensleveranstjänst].
* PlayReady-dynamisk kryptering. Mer information finns i [med PlayReady dynamisk kryptering och Licensleveranstjänst].
* Media Services PlayReady License-mall. Mer information finns i [Media Services PlayReady licens mall översikt].
* Strömning lagring krypteras tillgångar. Mer information finns i [strömning lagring krypterat innehåll].

## <a id="august_changes_14"></a>Augusti 2014-versionen
När du koda en tillgång, skapas en utdatatillgången vid slutförandet av kodningsjobbet. Azure Media Services Encoder gav metadata om utdata tillgångar förrän den här versionen. Från och med den här kodaren ger också metadata om inkommande tillgångar. Mer information finns i [indata Metadata] och [utdata Metadata] artiklar.

## <a id="july_changes_14"></a>Versionen som juli 2014
Följande felkorrigeringar har gjorts för Azure Media Services Paketeraren och Krypterare:

* Bara ljud spelar tillbaka när transmuxing en levande Arkiv tillgång till HTTP Live Streaming – problemet har åtgärdats och nu både ljud och video spelas upp.
* När Paketera en tillgång till HTTP Live Streaming och AES-128-bitars kuvert kryptering paketerade strömmar spelas inte upp på Android-enheter – det här felet är åtgärdat och paketerade dataströmmen spelas upp på Android-enheter som stöder HTTP Live Streaming.

## <a id="may_changes_14"></a>Kan 2014-versionen
### <a id="may_14_changes"></a>Allmänna Media Services-uppdateringar
Du kan nu använda [dynamisk paketering] dataströmmen HTTP Live Streaming (HLS) v3. Strömma HLS v3, lägga till följande format till locator ursprungssökväg: * .ism/manifest(format=m3u8-aapl-v3). Mer information finns i [Nick Drouin blogg].

Dynamisk paketering nu också stöder leverera HLS (v3- och v4) som krypterats med PlayReady utifrån Smooth Streaming statiskt krypteras med PlayReady. Mer information om hur du krypterar Smooth Streaming med PlayReady finns [Protecting Smooth Stream med PlayReady].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-uppdateringar
Följande förbättringar ingår i Media Services .NET SDK 3.0.0.5-versionen:

* Bättre hastighet och återhämtning för att ladda upp/hämta media tillgångar.
* Förbättringar i försök logik och tillfälligt undantagshantering: 
  
  * Tillfälligt fel identifiering och försök igen logik har förbättrats för undantag som orsakas av frågor, spara ändringar, överföra och hämta filer. 
  * Du märker att allvarliga fel misslyckas snabbare nu när du hämtar Web undantag (till exempel under en Tokenbegäran för ACS).

Mer information finns i [försök logiken i Media Services SDK för .NET].

## <a id="april_changes_14"></a>Versionen för april 2014 kodaren
### <a name="april_14_enocer_changes"></a>Media Services-kodaren uppdateringar
* Stöd har lagts till för att föra in AVI filer som har skapats med hjälp av bete DAL EDIUS linjära editor där videon är lätt komprimerade med bete DAL HQ/HQX codec. Mer information finns i [bete DAL annonserar EDIUS 7 strömning via the Cloud].
* Stöd har lagts till för att ange Namngivningskonventionen för de filer som skapas av Media-kodaren. Mer information finns i [styra Media Service-kodaren utdatafilnamn].
* Stöd för video eller ljud överlägg har lagts till. Mer information finns i [skapar överlägg].
* Stöd har lagts till för att gå till samman flera video segment. Mer information finns i [segment för att gå till Video].
* Fast ett fel som rör omkodning av MP4s där ljud har kodats med ljud MPEG-1 layer 3 (aka MP3).

## <a id="jan_feb_changes_14"></a>Januari/februari 2014 versioner
### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 och 3.0.0.3
Ändringar i 3.0.0.1 och 3.0.0.2 är:

* Fast problem relaterade till användning av LINQ-frågor med OrderBy-uttryck.
* Dela testlösningar i [GitHub] i enhetsbaserad testerna och scenariobaserade tester.

Läs mer om ändringarna: [Azure Media Services .NET SDK 3.0.0.1 och 3.0.0.2 släpper].

Följande ändringar har gjorts i 3.0.0.3:

* Uppgradera Azure storage-beroenden version 3.0.3.0 ska användas. 
* Bakåtkompatibilitet problem för 3.0 har åtgärdats. *.* versioner. 

## <a id="december_changes_13"></a>December 2013 Release
### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x versioner är inte bakåtkompatibla med 2.4.x.x versioner.
> 
> 

Den senaste versionen av Media Services SDK är nu 3.0.0.0. Du kan hämta det senaste paketet från NuGet eller hämta bits från [GitHub].

Från och med Media Services SDK version 3.0.0.0 kan du återanvända den [Azure Active Directory Access Control Service (ACS)] token. Mer information finns i avsnittet ”återanvända Access Control Service Tokens” i den [ansluter till Media Services med Media Services SDK för .NET] artikel.

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK-tilläggen 2.0.0.0
Azure Media Services .NET SDK-tilläggen är en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar koden och gör det enklare att utveckla med Azure Media Services. Du kan hämta den senaste bits från [Azure Media Services .NET SDK-tilläggen].

## <a id="november_changes_13"></a>Versionen för november 2013
### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK ändringar
Från och med den här versionen kan hanterar Media Services SDK för .NET tillfälligt fel fel som kan uppstå när du anropar Media Services REST API-lagret.

## <a id="august_changes_13"></a>Augusti 2013 Release
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell cmdletar som ingår i Azure SDK-verktyg
Följande Media Services PowerShell-cmdletar ingår nu i [azure-sdk-verktyg].

* Get-AzureMediaServices 
  
    Till exempel `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    Till exempel `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    Till exempel `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    Till exempel `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>Versionen för juni 2013
### <a name="june_13_general_changes"></a>Ändringar av Azure Media Services
De ändringar som nämns i det här avsnittet är uppdateringar som ingår i juni 2013 Media Services-versioner.

* Möjlighet att koppla flera lagringskonton till ett Media Service-konto. 
  
    StorageAccount
  
    Asset.StorageAccountName och Asset.StorageAccount
* Möjligheten att uppdatera Job.Priority. 
* Meddelande-relaterade entiteter och egenskaper: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Jobb
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Ändringar av Azure Media Services .NET SDK
Följande ändringar är inkluderade i juni 2013 versioner av Media Services SDK. Senaste Media Services SDK finns på GitHub.

* Från och med version 2.3.0.0 Media Services SDK stöder länka flera storage-konton till ett Media Services-konto. Följande API: er stöder den här funktionen:
  
    IStorageAccount-typen.
  
    Egenskapen Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
  
    StorageAccount-egenskapen.
  
    Egenskapen StorageAccountName.
  
    Mer information finns i [hantera Media Services tillgångar över flera Lagringskonton].
* Meddelande-relaterade API: er. Från och med version 2.2.0.0 har du möjlighet att lyssna på Azure Queue storage-meddelanden. Mer information finns i, [hantering av Media Services jobbet meddelanden].
  
    Egenskapen Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
  
    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint-typen.
  
    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription-typen.
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection-typen.
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType-typen.
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState-typen.
* Beroendet av Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Beroendet av OData 5.5 (Microsoft.Data.OData.dll).

## <a id="december_changes_12"></a>December 2012-versionen
### <a name="dec_12_dotnet_changes"></a>Ändringar av Azure Media Services .NET SDK
* IntelliSense: Lägga till saknas Intellisense dokumentationen för många typer.
* Microsoft.Practices.TransientFaultHandling.Core: Ett problem har åtgärdats där SDK fortfarande hade ett beroende till en gammal version av den här sammansättningen. SDK är nu refererar till version 5.1.1209.1 av den här sammansättningen.

Korrigeringar för problem som hittas i November 2012 SDK:

* IAsset.Locators.Count: Det här antalet nu korrekt rapporteras på nya IAsset gränssnitt när alla lokaliserare har tagits bort.
* IAssetFile.ContentFileSize: Det här värdet nu korrekt anges efter en överföring av IAssetFile.Upload (filepath).
* IAssetFile.ContentFileSize: Den här egenskapen kan nu anges när du skapar en resursfil. Det var tidigare skrivskyddad.
* IAssetFile.Upload (filepath): ett problem har åtgärdats där den här metoden Överför synkron har att följande fel vid överföring av flera filer till tillgången. Felkod: ”Det gick inte att autentisera begäran-Server. Kontrollera att värdet för Authorization-huvud är formaterad korrekt inklusive signaturen ”.
* IAssetFile.UploadAsync: Ett problem har åtgärdats där fler än fem filer kan överföras samtidigt.
* IAssetFile.UploadProgressChanged: Den här händelsen tillhandahålls nu av SDK.
* IAssetFile.DownloadAsync (sträng, BlobTransferClient ILocator, CancellationToken): den här metodöverlagringen tillhandahålls nu.
* IAssetFile.DownloadAsync: Ett problem har åtgärdats där fler än fem filer kan hämtas samtidigt.
* IAssetFile.Delete(): Ett problem har åtgärdats där anropa delete kan utlösa ett undantag om ingen fil har överförts för IAssetFile.
* Jobb: Ett problem har åtgärdats där länkning en ”MP4 Smooth dataströmmar uppgiften” med ”PlayReady skydd uppgiften” med en jobbmall kan inte skapa några aktiviteter alls.
* EncryptionUtils.GetCertificateFromStore(): Den här metoden genererar inte längre ett null-referensundantag på grund av ett fel söka efter certifikat som baseras på problem med certifikat.

## <a id="november_changes_12"></a>November 2012-versionen
De ändringar som nämns i det här avsnittet har uppdateringar som ingår i November 2012 (version 2.0.0.0) SDK. De här ändringarna kan kräva någon kod som skrivs för juni 2012 preview SDK-versionen ändras eller skrivas om.

* Tillgångar
  
    IAsset.Create(assetName) är funktionen bara tillgång skapas. IAsset.Create överför inte längre filerna som en del av metodanropet. Använd IAssetFile för överföring.
  
    Metoden IAsset.Publish och uppräkningsvärdet AssetState.Publish har tagits bort från Services SDK. All kod som förlitar sig på detta värde måste skrivas.
* FileInfo
  
    Den här klassen har tagits bort och ersatts av IAssetFile.
  
    IAssetFiles
  
    IAssetFile ersätter FileInfo och fungerar annorlunda. Initiera objektet IAssetFiles följt av en filöverföring antingen med hjälp av Media Services SDK eller Azure Storage SDK: N för att använda den. Du kan använda följande IAssetFile.Upload överlagringar:
  
  * IAssetFile.Upload(filePath): En synkron metod som rekommenderas när du överför en fil och blockerar tråden.
  * IAssetFile.UploadAsync (filePath, blobTransferClient, lokaliserare, cancellationToken): en asynkron metod. Detta är den önskade överför mekanismen. 
    
    Känt fel: med cancellationToken verkligen avbryta uppladdningen; tillståndet annullering av aktiviteterna kan dock vara någon av ett antal tillstånd. Du måste korrekt fånga och hantera undantag.
* Lokaliserare
  
    Ursprung-specifika versioner har tagits bort. SAS-specifika-kontext. Locators.CreateSasLocator (tillgången, accessPolicy) markeras som föråldrad eller tagits bort av GA. Se avsnittet lokaliserare under nya funktioner för uppdaterade beteendet.

## <a id="june_changes_12"></a>Förhandsversionen för juni 2012
Följande funktioner är nya i November-versionen av SDK.

* Ta bort enheter
  
    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objekt har nu tagits bort på objektnivå, det vill säga IObject.Delete() i stället för att en delete i samlingen som är cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokaliserare
  
    Lokaliserare nu måste skapas med metoden CreateLocator och Använd uppräkningsvärden LocatorType.SAS eller LocatorType.OnDemandOrigin som ett argument för typ av lokaliserare som du vill skapa.
  
    Nya egenskaper har lagts till positionerare för att göra det lättare att få användbar URI: er för ditt innehåll. Förändringen av lokaliserare har tänkt att ger mer flexibilitet för framtida från tredje part och öka enkel användning för media klientprogram.
* Stöd för asynkron metod
  
    Asynkron stöd har lagts till alla metoder.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN-Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API-referens]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[prisinformation för Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[indata Metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[utdata Metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Delivering Content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[indexering mediefiler med Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[arbetar med Azure Media Services Liveströmning]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[med hjälp av dynamisk AES-128-kryptering och leverans av tjänsten]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[med PlayReady dynamisk kryptering och Licensleveranstjänst]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady licens mall översikt]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[strömning lagring krypterat innehåll]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[dynamisk paketering]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin blogg]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protecting Smooth Stream med PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[försök logiken i Media Services SDK för .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[bete DAL annonserar EDIUS 7 strömning via the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[styra Media Service-kodaren utdatafilnamn]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[skapar överlägg]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[segment för att gå till Video]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 och 3.0.0.2 släpper]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[ansluter till Media Services med Media Services SDK för .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK-tilläggen]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-verktyg]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[hantera Media Services tillgångar över flera Lagringskonton]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[hantering av Media Services jobbet meddelanden]: http://msdn.microsoft.com/library/azure/dn261241.aspx

