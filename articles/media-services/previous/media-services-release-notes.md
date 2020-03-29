---
title: Viktig information om Azure Media Services | Microsoft-dokument
description: I den här artikeln beskrivs viktig information om Microsoft Azure Media Services v2-utgivningsanteckningar.
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
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: a55ef4f814c0d13a6efbeeb8cd9a5565d2869432
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183303"
---
# <a name="azure-media-services-release-notes"></a>Viktig information om Azure Media Services

Dessa viktig information för Azure Media Services sammanfattar ändringar från tidigare versioner och kända problem.

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Vi vill höra från våra kunder så att vi kan fokusera på att åtgärda problem som påverkar dig. Om du vill rapportera ett problem eller ställa frågor skickar du ett inlägg i [AZURE Media Services MSDN Forum]. 

## <a name="known-issues"></a><a id="issues"/>Kända problem
### <a name="media-services-general-issues"></a><a id="general_issues"/>Allmänna frågor för Media Services

| Problem | Beskrivning |
| --- | --- |
| Flera vanliga HTTP-huvuden finns inte i REST API. |Om du utvecklar Media Services-program med REST API upptäcker du att vissa vanliga HTTP-huvudfält (inklusive KLIENT-REQUEST-ID, REQUEST-ID och RETURN-CLIENT-REQUEST-ID) inte stöds. Rubrikerna läggs till i en framtida uppdatering. |
| Procentkodning är inte tillåtet. |Media Services använder värdet för egenskapen IAssetFile.Name när webbadresser skapas för `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`direktuppspelningsinnehåll (till exempel ). Därför är procentkodning inte tillåtet. Värdet för egenskapen Name kan inte ha något av följande [procent-kodningsreservat tecken:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Det kan också bara finnas en "." för filnamnstillägget. |
| ListBlobs-metoden som ingår i Azure Storage SDK version 3.x misslyckas. |Media Services genererar SAS-url:er baserat på [2012-02-12-versionen.](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) Om du vill använda Storage SDK för att lista blobbar i en blob-behållare använder du metoden [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) som ingår i Storage SDK version 2.x. |
| Begränsningsmekanismen för Media Services begränsar resursanvändningen för program som gör för stora begäranden till tjänsten. Tjänsten kan returnera HTTP-statuskoden "Tjänsten är inte tillgänglig". |Mer information finns i beskrivningen av 503 HTTP-statuskoden i [mediatjänstfelkoder](media-services-encoding-error-codes.md). |
| När du frågar entiteter returneras en gräns på 1 000 entiteter samtidigt eftersom den offentliga REST-versionen 2 begränsar frågeresultatet till 1 000 resultat. |Använd Hoppa över och ta (.NET)/top (REST) enligt beskrivningen i [det här .NET-exemplet](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [det här REST API-exemplet](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Vissa klienter kan stöta på ett problem med en upprepningstagg i manifestet För jämn strömning. |Mer information finns i [det här avsnittet](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objekt kan inte serialiseras och fungerar därför inte med Azure Cache for Redis. |Om du försöker serialisera SDK AssetCollection-objektet för att lägga till det i Azure Cache för Redis genereras ett undantag. |
|REST API svarar med ett felmeddelande som säger "Filtret kan inte nås av den här versionen av REST Api" när du försöker få en tillgång eller kontonivå filter.|Filtret skapades eller ändrades med en nyare API-version än vad som används för att försöka hämta filtret. Detta kan inträffa om två API-versioner används av kod eller verktyg som används av kunden.  Den bästa lösningen här är att uppgradera koden eller verktygen för att använda de nyare eller de två API-versionerna.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>Historik för REST API-version
Information om versionshistoriken för REST API-API för Media Services finns i [AZURE Media Services REST API-referens].

## <a name="february-2020"></a>Februari 2020

Vissa analysmedieprocessorer kommer att dras tillbaka. För pensioneringsdatumen finns i avsnittet [äldre komponenter.](legacy-components.md)

## <a name="september-2019"></a>September 2019

### <a name="deprecation-of-media-processors"></a>Utfasning av medieprocessorer

Vi tillkännager utfasning av *Azure Media Indexer* och *Azure Media Indexer 2 Preview*. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter dessa äldre medieprocessorer.

För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md)

Se även [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augusti 2019

### <a name="deprecation-of-media-processors"></a>Utfasning av medieprocessorer

Vi meddelar att *mediaprocessorerna (Windows Azure Media Encoder)* och *Azure Media Encoder* (AME) har inaktiverats. För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md)

Mer information finns i [Migrera WAME till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) och [Migrera AME till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Mars 2019

Förhandsgranskningsfunktionen för Media Hyperlapse i Azure Media Services har inaktiverats.

## <a name="december-2018"></a>December 2018

Förhandsfunktionen för Media Hyperlapse i Azure Media Services kommer snart att dras tillbaka. Från och med den 19 december 2018 kommer Media Services inte längre att göra ändringar eller förbättringar av Media Hyperlapse. Den 29 mars 2019 går den i pension och inte längre tillgänglig.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>SUPPORT FÖR CMAF

CMAF- och "cbcs"-krypteringsstöd för Apple HLS (iOS 11+) och MPEG-DASH-spelare som stöder CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatyr sprites

Du kan nu använda Media Services för att generera Web VTT-miniatyrsprites med hjälp av våra v2 API:er. Mer information finns i [Generera en miniatyrsprit](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Juli 2018

Med den senaste tjänstutgåvan görs mindre formateringsändringar i de felmeddelanden som returneras av tjänsten när ett jobb misslyckas, med avseende på hur det delas upp i två eller flera rader.

## <a name="may-2018"></a>Maj 2018 

Från och med den 12 maj 2018 stöder livekanalerna inte längre rtp/MPEG-2-transportströmmens introllningsprotokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmenterade MP4-protokoll (Smooth Streaming).

## <a name="october-2017-release"></a>Version oktober 2017
> [!IMPORTANT] 
> Media Services är inaktuella stöd för Azure Access Control Service autentiseringsnycklar. Den 22 juni 2018 kan du inte längre autentisera med Media Services-backend via kod med hjälp av Access Control Service-nycklar. Du måste uppdatera koden för att kunna använda Azure Active Directory (Azure AD) per [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md). Håll utkik efter varningar om den här ändringen i Azure-portalen.

### <a name="updates-for-october-2017"></a>Uppdateringar för oktober 2017
#### <a name="sdks"></a>SDK:er
* .NET SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för autentisering av åtkomstkontrolltjänsten togs bort från den senaste .NET SDK på Nuget.org för att uppmuntra snabbare migrering till Azure AD. 
* JAVA SDK har uppdaterats för att stödja Azure AD-autentisering. Stöd för Azure AD-autentisering har lagts till i Java SDK. Information om hur du använder Java SDK med Media Services finns i [Komma igång med Java-klienten SDK för Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Filbaserad kodning
* Du kan nu använda Premium Encoder för att koda ditt innehåll till H.265 högeffektiv videokodning (HEVC) videocodec. Det finns ingen prispåverkan om du väljer H.265 framför andra codec-enheter, till exempel H.264. Mer information om HEVC:s patentlicenser finns i [villkoren för onlinetjänster](https://azure.microsoft.com/support/legal/).
* För källvideo som är kodad med H.265 (HEVC) video codec, till exempel video som spelas med hjälp av iOS11 eller GoPro Hero 6, kan du nu använda antingen Premium Encoder eller Standard Encoder för att koda dessa videor. Information om patentlicenser finns i [villkoren för onlinetjänster](https://azure.microsoft.com/support/legal/).
* För innehåll som innehåller flera ljudspår på flera språk måste språkvärdena vara korrekt märkta enligt motsvarande filformatsspecifikation (till exempel ISO MP4). Sedan kan du använda standardkodaren för att koda innehållet för direktuppspelning. Den resulterande streaming locator listar tillgängliga ljudspråk.
* Standard encoder stöder nu två nya ljud-bara system förinställningar, "AAC Audio" och "AAC God kvalitet Audio." Båda producerar stereo avancerad ljudkodning (AAC) utgång, med bithastigheter på 128 kbit/s och 192 kbps, respektive.
* Premium-kodaren stöder nu Filformat för QuickTime/MOV som indata. Video codec måste vara en av [de Apple ProRes-typer som anges i den här GitHub-artikeln](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Ljudet måste vara antingen AAC eller pulskod modulering (PCM). Premium-kodaren stöder inte till exempel DVC/DVCPro-video som är insvept i QuickTime/MOV-filer som indata. Standardkodaren stöder dessa videocodec-enheter.
* Följande buggfixar gjordes i kodare:

    * Du kan nu skicka jobb med hjälp av en indatatillgång. När dessa jobb har avslutats kan du ändra tillgången (till exempel lägga till, ta bort eller byta namn på filer i tillgången) och skicka ytterligare jobb.
    * Kvaliteten på JPEG-miniatyrer som produceras av Standard Encoder förbättras.
    * Standardkodaren hanterar indatametadata och miniatyrgenerering bättre i mycket korta videor.
    * Förbättringar av H.264-avkodaren som används i standardkodaren eliminerar vissa sällsynta artefakter. 

#### <a name="media-analytics"></a>Medieanalys
Allmän tillgänglighet för Azure Media Redactor: Den här medieprocessorn utför anonymisering genom att sudda ut ansiktena på utvalda personer och är idealisk för användning i scenarier för allmän säkerhet och nyhetsmedia. 

En översikt över den nya processorn finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-redactor/). Information om dokumentation och inställningar finns i [Redigera ansikten med Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Juni 2017 release

Media Services stöder nu [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> För närvarande stöder Media Services autentiseringsmodellen för Åtkomstkontrolltjänsten. Auktoriseringen av åtkomstkontrolltjänsten kommer att inaktuella den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="march-2017-release"></a>Mars 2017 release

Du kan nu använda standardkodaren för att [automatiskt generera en bithastighetsstege](media-services-autogen-bitrate-ladder-with-mes.md) genom att ange förinställda strängen "Adaptiv strömning" när du skapar en kodningsaktivitet. Om du vill koda en video för direktuppspelning med Media Services använder du förinställningen "Adaptiv direktuppspelning". Om du vill anpassa en kodningsförinställning för just det scenariot kan du börja med [dessa förinställningar](media-services-mes-presets-overview.md).

Du kan nu använda Media Encoder Standard eller Media Encoder Premium Workflow för att [skapa en kodningsuppgift som genererar fMP4-segment](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Februari 2017 release

Från och med den 1 april 2017 tas alla jobbposter i ditt konto som är äldre än 90 dagar bort automatiskt, tillsammans med tillhörande aktivitetsposter. Borttagningen sker även om det totala antalet poster ligger under den maximala kvoten. Om du vill arkivera jobb-/uppgiftsinformationen kan du använda koden som beskrivs i [Hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Januari 2017 release

I Media Services representerar en slutpunkt för direktuppspelning en strömningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN-nätverk (Content Delivery Network) för vidare distribution. Media Services tillhandahåller också sömlös integrering av Azure Content Delivery Network. Utgående ström från en StreamingEndpoint-tjänst kan vara en livestream, en video på begäran eller en progressiv nedladdning av din tillgång i ditt Media Services-konto. Varje Media Services-konto innehåller en standardslutpunkt för direktuppspelning. Ytterligare slutpunkter för direktuppspelning kan skapas under kontot. 

Det finns två versioner av slutpunkter för direktuppspelning, 1.0 och 2.0. Från och med den 10 januari 2017 innehåller alla nyligen skapade Media Services-konton slutpunkten för standardstreaming. Ytterligare slutpunkter för direktuppspelning som du lägger till i det här kontot är också version 2.0. Den här ändringen påverkar inte befintliga konton. Befintliga slutpunkter för direktuppspelning är version 1.0 och kan uppgraderas till version 2.0. Det finns beteende-, fakturerings- och funktionsändringar med den här ändringen. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

Från och med 2.15-versionen har Media Services lagt till följande egenskaper i slutpunktsentiteten för direktuppspelning:

* CdnProvider (på) 
* CdnProfile (på andra)
* FreeTrialEndTime 
* StreamingEndpointVersion 

Mer information om dessa egenskaper finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>December 2016 release

 Nu kan du använda Media Services för att komma åt telemetri/måttdata för sina tjänster. Du kan använda den aktuella versionen av Media Services för att samla in telemetridata för livekanal, slutpunkt för direktuppspelning och arkiventiteter. Mer information finns i [Telemetrin mediatjänster](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a id="july_changes16"/>Juli 2016 release
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Uppdateringar av manifestfilen (*. ISM) som genereras av kodningsuppgifter
När en kodningsuppgift skickas till Media Encoder Standard eller Media Encoder Premium genererar kodningsuppgiften en manifestfil för [direktuppspelning](media-services-deliver-content-overview.md) (*.ism) i utdatatillgången. Med den senaste tjänstutgåvan uppdaterades syntaxen för den här manifestfilen för direktuppspelning.

> [!NOTE]
> Syntaxen för filen strömmande manifest (.ism) är reserverad för internt bruk. Det kan komma att ändras i framtida versioner. Ändra eller ändra inte innehållet i den här filen.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Ett nytt klientmanifest (*. ISMC)-filen genereras i utdatatillgången när en kodningsuppgift matar ut en eller flera MP4-filer
Från och med den senaste tjänstutgåvan, efter slutförandet av en kodningsuppgift som genererar en eller flera MP4-filer, innehåller utdatatillgången också en strömmande klientmanifest (*.ismc) fil. .ismc-filen hjälper till att förbättra prestanda för dynamisk direktuppspelning. 

> [!NOTE]
> Syntaxen för klientmanifestfilen (.ismc) är reserverad för internt bruk. Det kan komma att ändras i framtida versioner. Ändra eller ändra inte innehållet i den här filen.
> 
> 

Mer information finns i [den här bloggen](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Kända problem
Vissa klienter kan stöta på ett problem med en upprepningstagg i manifestet För jämn strömning. Mer information finns i [det här avsnittet](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>April 2016 release
### <a name="media-analytics"></a>Medieanalys
 Media Services introducerade Media Analytics för kraftfull videoinformation. Mer information finns i [Översikt över Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (förhandsgranskning)
Nu kan du använda Media Services för att dynamiskt kryptera ditt HTTP Live Streaming (HLS) innehåll med Apple FairPlay. Du kan också använda licensleveranstjänsten för Media Services för att leverera FairPlay-licenser till kunder. Mer information finns i "Använd Azure Media Services för att strömma ditt HLS-innehåll som skyddas med Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Februari 2016 release
Den senaste versionen av Media Services SDK för .NET (3.5.3) innehåller en Google Widevine-relaterade buggfix. Det var omöjligt att återanvända AssetDeliveryPolicy för flera tillgångar krypterade med Widevine. Som en del av den här felkorrigeringen lades följande egenskap till i SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Januari 2016 release
Kodning reserverade enheter döptes om för att minska förväxling med kodare namn.

De reserverade enheterna Basic, Standard och Premium har bytt namn till reserverade enheter för S1, S2 och S3. Kunder som använder grundläggande kodning reserverade enheter idag ser S1 som etikett i Azure-portalen (och i fakturan). Kunder som använder Standard och Premium ser etiketterna S2 respektive S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>December 2015 release

### <a name="media-encoder-deprecation-announcement"></a>Meddelande om utfasning av mediakodrar

 Media Encoder kommer att vara inaktuella från och med cirka 12 månader från lanseringen av Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK för PHP
Azure SDK-teamet publicerade en ny version av [Azure SDK för PHP-paketet](https://github.com/Azure/azure-sdk-for-php) som innehåller uppdateringar och nya funktioner för Media Services. I synnerhet stöder Media Services SDK för PHP nu de senaste [funktionerna för innehållsskydd.](media-services-content-protection-overview.md) Dessa funktioner är dynamisk kryptering med AES och DRM (PlayReady och Widevine) med och utan tokenbegränsningar. Den stöder också [skalningskodningsenheter](media-services-dotnet-encoding-units.md).

Mer information finns i:

* Följande [kodexempel](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) hjälper dig att komma igång snabbt:
  * **vodworkflow_aes.php**: Denna PHP-fil visar hur man använder AES-128 dynamisk kryptering och nyckeln leveranstjänst. Den är baserad på .NET-exemplet som beskrivs i [Användning av dynamisk kryptering av AES-128 och nyckelleveranstjänsten](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Denna PHP-fil visar hur man använder PlayReady dynamisk kryptering och licensleveranstjänsten. Den är baserad på .NET-exemplet som beskrivs i [Använd PlayReady och/eller Widevine dynamisk gemensam kryptering](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Denna PHP-fil visar hur man skala kodning reserverade enheter.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>November 2015 release
 Media Services erbjuder nu Widevine-licensleveranstjänsten i molnet. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se även den [här självstudien](media-services-protect-with-playready-widevine.md) och [GitHub-databasen](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevine licensleveranstjänster som tillhandahålls av Media Services är i förhandsversion. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Oktober 2015 release
Media Services är nu live i följande datacenter: Brasilien South, India West, India South och India Central. Du kan nu använda Azure-portalen för att [skapa Media Service-konton](media-services-portal-create-account.md) och utföra olika uppgifter som beskrivs på [mediatjänstdokumentationswebbsidan](https://azure.microsoft.com/documentation/services/media-services/). Live-kodning är inte aktiverat i dessa datacenter. Dessutom är inte alla typer av kodning reserverade enheter tillgängliga i dessa datacenter.

* Södra Brasilien: Endast standard- och grundläggande kodningsreserverade enheter finns tillgängliga.
* Indien Väst, Indien Syd och Indien Central: Endast grundläggande kodning reserverade enheter är tillgängliga.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>September 2015 release
Media Services erbjuder nu möjligheten att skydda både video on demand och live streams med Widevine modulära DRM-teknik. Du kan använda följande partner för leveranstjänster för att hjälpa dig att leverera Widevine-licenser:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Du kan använda [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine. 
* Media Services har lagt till stöd för Apple ProRes-videor. Du kan nu ladda upp dina QuickTime-källvideofiler som använder Apple ProRes eller andra codec-enheter. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Du kan nu använda Media Encoder Standard för att göra subclipping och live arkiv extrahering. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Följande filtreringsuppdateringar gjordes: 
  
  * Du kan nu använda Apple HLS-formatet med ett ljudfilter. Du kan använda den här uppdateringen för att ta bort ett ljudspår genom att ange (endast ljud=false) i webbadressen.
  * När du definierar filter för dina tillgångar kan du nu kombinera flera (upp till tre) filter i en enda URL.
    
    Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services stöder nu I-ramar i HLS version 4. I-frame support optimerar snabbspolning framåt och bakåt. Som standard innehåller alla HLS-version 4-utgångar spellistan I-frame (EXT-X-I-FRAME-STREAM-INF).
Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Augusti 2015 release
* Media Services SDK för Java-versionen 0.8.0-versionen och nya exempel finns nu tillgängliga. Mer information finns i:
    
* Azure Media Player har uppdaterats med stöd för flera ljudström. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Juli 2015 release
* Den allmänna tillgängligheten för Media Encoder Standard tillkännagavs. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard använder förinställningar enligt beskrivningen i [det här avsnittet](https://go.microsoft.com/fwlink/?LinkId=618336). När du använder en förinställning för 4K-kodar hämtar du premiumreserverad enhetstyp. Mer information finns i [Skala kodning](media-services-scale-media-processing-overview.md).
* Live-bildtexter i realtid användes med Media Services och Media Player. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Uppdateringar av Media Services .NET SDK
Media Services .NET SDK är nu version 3.4.0.0. Följande uppdateringar gjordes: 

* Stöd har implementerats för livearkiv. Du kan inte hämta en tillgång som innehåller ett livearkiv.
* Stöd har implementerats för dynamiska filter.
* Funktionen har implementerats så att användarna kan behålla en lagringsbehållare medan de tar bort en tillgång.
* Felkorrigeringar gjordes relaterade till principer för återförsök i kanaler.
* Media Encoder Premium Workflow har aktiverats.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Juni 2015 release
### <a name="media-services-net-sdk-updates"></a>Uppdateringar av Media Services .NET SDK
Media Services .NET SDK är nu version 3.3.0.0. Följande uppdateringar gjordes: 

* Stöd har lagts till för OpenId Connect-identifieringsspecifikationen.
* Stöd har lagts till för hantering av nycklars överrullning på identitetsprovidersidan.

Om du använder en identitetsprovider som exponerar ett OpenID Connect-identifieringsdokument (som Azure AD, Google och Salesforce gör) kan du instruera Media Services att hämta signeringsnycklar för validering av JWTs (OpenID Connect) från OpenID Connect-identifieringsspecifikationen. 

Mer information finns i [Använda JSON-webbnycklar från OpenID Connect-identifieringsspecifikationen för att arbeta med JWT-autentisering i Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Maj 2015 release
Följande nya funktioner tillkännagavs:

* [En förhandsgranskning av livekodning med Medietjänster](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamiskt manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>April 2015 release
### <a name="general-media-services-updates"></a>Uppdateringar av Allmänna medietjänster
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) tillkännagavs.
* Från och med Media Services REST 2.10 skapas kanaler som är konfigurerade för att inta ett RTMP (Real-Time Messaging Protocol) med primära och sekundära intagsadresser. Mer information finns i [Konfigurationer för kanalingest](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer har uppdaterats.
* Stöd för spanska språket lades till.
* En ny konfiguration för XML-formatet har lagts till.

Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Uppdateringar av Media Services .NET SDK
Media Services .NET SDK är nu version 3.2.0.0. Följande uppdateringar gjordes:

* Breaking change: TokenRestrictionTemplate.Issuer och TokenRestrictionTemplate.Audience har ändrats till att vara av en strängtyp.
* Uppdateringar har gjorts relaterade till att skapa principer för anpassat återförsök.
* Buggfixar gjordes relaterade till att ladda upp och ladda ner filer.
* Klassen MediaServicesCredentials accepterar nu primära och sekundära åtkomstkontrollslutpunkter att autentisera mot.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Mars 2015 release
### <a name="general-media-services-updates"></a>Uppdateringar av Allmänna medietjänster
* Media Services tillhandahåller nu integrering av Content Delivery Network. För att stödja integrationen lades egenskapen CdnEnabled till i StreamingEndpoint. CdnEnabled kan användas med REST API:er som börjar med version 2.9. Mer information finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled kan användas med .NET SDK som börjar med version 3.1.0.2. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Media Encoder Premium Workflow tillkännagavs. Mer information finns i [Introduktion till Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Februari 2015 release
### <a name="general-media-services-updates"></a>Uppdateringar av Allmänna medietjänster
REST API för Media Services är nu version 2.9. Från och med den här versionen kan du aktivera integreringen av Content Delivery Network med slutpunkter för direktuppspelning. Mer information finns i [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Januari 2015 release
### <a name="general-media-services-updates"></a>Uppdateringar av Allmänna medietjänster
Den allmänna tillgängligheten för innehållsskydd med dynamisk kryptering tillkännagavs. Mer information finns i [Media Services förbättrar streamingsäkerheten med allmän tillgänglighet för DRM-teknik](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Uppdateringar av Media Services .NET SDK
Media Services .NET SDK är nu version 3.1.0.1.

Den här versionen markerade standardkonstruktören Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate som föråldrad. Den nya konstruktorn tar TokenType som ett argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>December 2014 release
### <a name="general-media-services-updates"></a>Uppdateringar av Allmänna medietjänster
* Vissa uppdateringar och nya funktioner har lagts till i Media Indexer. Mer information finns i [Azure Media Indexer version 1.1.6.7-versionsanteckningar](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Ett nytt REST API har lagts till som du kan använda för att uppdatera kodning reserverade enheter. Mer information finns i [EncodingReservedUnitType med REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* CORS-support har lagts till för nyckelleveranstjänsten.
* Prestandaförbättringar har gjorts för att fråga alternativ för auktoriseringsprinciper.
* I Kina datacenter, [nyckeln leverans URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) är nu per kund (precis som i andra datacenter).
* HLS automatisk målvaraktighet lades till. När du gör livestreaming är HLS alltid paketerat dynamiskt. Som standard beräknar Media Services automatiskt HLS-segmentets förpackningsförhållande (FragmentsPerSegment) baserat på nyckelbildsintervallet (KeyFrameInterval). Den här metoden kallas också en grupp bilder (GOP) som tas emot från live-kodaren. Mer information finns i [Arbeta med livestreaming av Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Uppdateringar av Media Services .NET SDK
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) är nu version 3.1.0.0. Följande uppdateringar gjordes:

* .NET SDK-beroendet uppgraderades till .NET 4.5 Framework.
* Ett nytt API som du kan använda för att uppdatera kodning reserverade enheter har lagts till. Mer information finns i [Uppdatera reserverad enhetstyp och öka kodningen av reserverade enheter med hjälp av .NET](media-services-dotnet-encoding-units.md).
* JWT-stöd för tokenautentisering har lagts till. Mer information finns [i JWT-tokenautentisering i Media Services och dynamisk kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativa förskjutningar för BeginDate och ExpirationDate i licensmallen PlayReady har lagts till.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>November 2014 release
* Nu kan du använda Media Services för att använda innehåll med direktuppspelning (fMP4) via en SSL-anslutning. Om du vill börja använda via SSL måste du uppdatera intvalnings-URL:en till HTTPS. För närvarande stöder Media Services inte SSL med anpassade domäner. Mer information om livestreaming finns i [Arbeta med Azure Media Services Live Streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* För närvarande kan du inte inta en RTMP-livestream via en SSL-anslutning.
* Du kan bara strömma via SSL om slutpunkten för direktuppspelning som du levererar ditt innehåll från skapades från efter den 10 september 2014. Om dina strömmande webbadresser baseras på slutpunkter för direktuppspelning som skapats efter den 10 september 2014 innehåller webbadressen "streaming.mediaservices.windows.net" (det nya formatet). Strömmande webbadresser som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte SSL. Om webbadressen är i det gamla formatet och du vill strömma via SSL skapar du [en ny slutpunkt för direktuppspelning](media-services-portal-manage-streaming-endpoints.md). Om du vill strömma ditt innehåll via SSL använder du webbadresser baserat på den nya slutpunkten för direktuppspelning.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK för .NET-tillägg är nu version 2.0.0.3.

Media Services SDK för .NET är nu version 3.0.0.8. Följande uppdateringar gjordes:

* Återfactoring implementerades i principklasser för återförsök.
* En användaragentsträng har lagts till i HTTP-begäranden.
* Ett NuGet-återställningsversionssteg lades till.
* Scenariotester har åtgärdats för att använda x509 cert från databasen.
* Valideringsinställningar har lagts till för när kanalen och direktuppspelningen slutuppdatering.

### <a name="new-github-repository-to-host-media-services-samples"></a>Ny GitHub-databas som ska vara värd för Exempel på Media Services
Exempel finns i [GitHub-databasen för Media Services-exempel](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>September 2014 release
Rest-metadata för Media Services är nu version 2.7. Mer information om de senaste REST-uppdateringarna finns i [REST API-referensen för Media Services REST.](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)

Media Services SDK för .NET är nu version 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Bryta förändringar
* Origin har bytt namn till [StreamingEndpoint].
* En ändring gjordes i standardbeteendet när du använder Azure-portalen för att koda och sedan publicera MP4-filer.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nya funktioner/scenarier som ingår i den allmänna tillgänglighetsversionen
* Media Indexer-medieprocessorn introducerades. Mer information finns i [Indexera mediefiler med Media Indexer .](https://msdn.microsoft.com/library/azure/dn783455.aspx)
* Du kan använda [StreamingEndpoint-entiteten] för att lägga till anpassade domännamn (värdnamn).
  
    Om du vill använda ett anpassat domännamn som slutpunktsnamn för direktuppspelning av Media Services lägger du till anpassade värdnamn i slutpunkten för direktuppspelning. Använd REST-API:erna för Media Services eller .NET SDK för att lägga till anpassade värdnamn.
  
    Följande gäller:
  
  * Du måste ha ägarskapet för det anpassade domännamnet.
  * Ägarskapet för domännamnet måste valideras av Media Services. Om du vill validera domänen skapar du ett CName som mappar den överordnade domänen MediaServicesAccountId för att verifiera DNS-mediaservices-dns-zone.
  * Du måste skapa ett annat CName som mappar det anpassade värdnamnet (till exempel sports.contoso.com) till ditt Media Services StreamingEndpoint-värdnamn (till exempel amstest.streaming.mediaservices.windows.net).

    Mer information finns i egenskapen CustomHostNames i artikeln [StreamingEndpoint.](https://msdn.microsoft.com/library/azure/dn783468.aspx)

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nya funktioner/scenarier som ingår i den offentliga förhandsversionen
* Förhandsvisning av livestreaming. Mer information finns i [Arbeta med livestreaming av Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Nyckelleveranstjänst. Mer information finns i [Använda dynamisk AES-128-kryptering och nyckelleveranstjänsten](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* AES dynamisk kryptering. Mer information finns i [Använda dynamisk AES-128-kryptering och nyckelleveranstjänsten](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* PlayReady licensleveranstjänst. 
* PlayReady dynamisk kryptering. 
* Licensmall för PlayReady för Media Services PlayReady. Mer information finns i [licensmallen För Media Services PlayReady].
* Strömma lagringskrypterade tillgångar. Mer information finns i [Strömma lagringskrypterat innehåll](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Augusti 2014 release
När du kodar en tillgång skapas en utdatatillgång när kodningsjobbet är klart. Fram till den här versionen producerade Media Services Encoder metadata om utdatatillgångar. Från och med den här versionen producerar kodaren också metadata om indatatillgångar. Mer information finns i [Indatametadata] och [utdatametadata].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Juli 2014 release
Följande buggfixar gjordes för Azure Media Services Packager och Encryptor:

* När en live arkivtillgång överförs till HLS spelas endast ljud upp: Det här problemet har åtgärdats och nu kan både ljud och video spelas upp.
* När en tillgång paketeras till HLS- och AES 128-bitars kuvertkryptering spelas inte de paketerade strömmarna upp på Android-enheter: Den här buggen har åtgärdats och den paketerade strömmen spelas upp på Android-enheter som stöder HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Maj 2014 release
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Uppdateringar av Allmänna medietjänster
Du kan nu använda [dynamisk förpackning] för att strömma HLS version 3. Om du vill strömma HLS version 3 lägger du till följande format i ursprungssöksökvägen: * .ism/manifest(format=m3u8-aapl-v3). För mer information, se [detta forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamisk förpackning stöder nu även leverans av HLS (version 3 och version 4) krypterad med PlayReady baserat på Smooth Streaming statiskt krypterad med PlayReady. Information om hur du krypterar Jämn strömning med PlayReady finns i [Skydda jämn strömning med PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Uppdateringar av Media Services .NET SDK
Media Services .NET SDK är nu version 3.0.0.5. Följande uppdateringar gjordes:

* Snabbhet och återhämtning är bättre när du laddar upp och hämtar medietillgångar.
* Förbättringar har gjorts i logikhantering för återförsök och tillfälliga undantag: 
  
  * Tillfällig felidentifiering och återförsökslogik förbättrades för undantag som orsakas när du frågar, sparar ändringar och överför eller hämtar filer. 
  * När du får webbundantag (till exempel under en åtkomstkontrolltjänsttokenbegäran) misslyckas allvarliga fel snabbare nu.

Mer information finns [i Logiken För återförsök i Media Services SDK för .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Januari/februari 2014 släpps
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Medietjänster .NET SDK 3.0.0.1, 3.0.0.2 och 3.0.0.3
Förändringarna i 3.0.0.1 och 3.0.0.2 inkluderar:

* Frågor relaterade till användningen av LINQ-frågor med OrderBy-satser har åtgärdats.
* Testlösningar i [GitHub] delades upp i enhetsbaserade tester och scenariobaserade tester.

Mer information om ändringarna finns i [utgåvorna Media Services .NET SDK 3.0.0.1 och 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Följande ändringar gjordes i version 3.0.0.3:

* Azure-lagringsberoenden uppgraderades för att använda version 3.0.3.0.
* Ett bakåtkompatibilitetsproblem åtgärdades för 3.0. *.* Släpper.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>December 2013 release
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Medietjänster .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x-versionerna är inte bakåtkompatibla med 2.4.x.x-utgåvor.
> 
> 

Den senaste versionen av Media Services SDK är nu 3.0.0.0. Du kan ladda ner det senaste paketet från NuGet eller få bitar från [GitHub].

Från och med Media Services SDK version 3.0.0.0 kan du återanvända [Azure AD Access Control Service-token.](https://msdn.microsoft.com/library/hh147631.aspx) Mer information finns i avsnittet "Återanvänd åtkomstkontrolltjänsttoken" i [Anslut till mediatjänster med Media Services SDK för .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-tillägg 2.0.0.0
 Media Services .NET SDK-tillägg är en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar koden och gör det enklare att utveckla med Media Services. Du kan hämta de senaste bitarna från [Media Services .NET SDK-tillägg](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>November 2013 release
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services .NET SDK-ändringar
Från och med den här versionen hanterar Media Services SDK för .NET tillfälliga fel som kan uppstå när anrop görs till REST API-lagret för mediatjänster.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Augusti 2013 release
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>PowerShell-cmdlets för Media Services som ingår i Azure SDK-verktyg
Följande PowerShell-powershell-cmdlets för Media Services ingår nu i [Azure SDK-verktyg:](https://github.com/Azure/azure-sdk-tools)

* Hämta AzureMediaServices 

    Exempel: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Exempel: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Exempel: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Exempel: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Juni 2013 release
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Ändringar av Medietjänster
Följande ändringar som nämns i det här avsnittet är uppdateringar som ingår i pressmeddelandena från juni 2013:

* Möjlighet att länka flera lagringskonton till ett Media Service-konto. 
    * StorageAccount
    * Asset.StorageAccountName och Asset.StorageAccount
* Möjlighet att uppdatera Job.Priority. 
* Anmälan-relaterade enheter och egenskaper: 
    * SysselsättningAnmälelseAbonnemang
    * AnmälanEndPoint
    * Jobb
* Tillgång.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services .NET SDK-ändringar
Följande ändringar ingår i SDK-utgåvorna i juni 2013. Den senaste Media Services SDK är tillgänglig på GitHub.

* Från och med version 2.3.0.0 stöder Media Services SDK att länka flera lagringskonton till ett Media Services-konto. Följande API:er stöder den här funktionen:
  
    * Typ av IStorageAccount
    * Egenskapen Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Egenskapen StorageAccount
    * Egenskapen StorageAccountName
  
      Mer information finns i [Hantera Media Services-tillgångar över flera lagringskonton](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Api:er som rör meddelanden. Från och med version 2.2.0.0 kan du lyssna på Azure Queue storage notifications. Mer information finns i [Hantera jobbmeddelanden](https://msdn.microsoft.com/library/azure/dn261241.aspx)för Media Services .
  
    * Egenskapen Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint-typ
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationAbonnemangstyp
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection-typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType-typ
* Beroende av lagringsklienten SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Beroende av OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>December 2012 release
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services .NET SDK-ändringar
* IntelliSense: Saknad IntelliSense-dokumentation har lagts till för många typer.
* Microsoft.Practices.TransientFaultHandling.Core: Ett problem åtgärdades där SDK fortfarande hade ett beroende av en gammal version av den här sammansättningen. SDK refererar nu till version 5.1.1209.1 av den här sammansättningen.

Korrigeringar för problem som hittades i november 2012 SDK:

* IAsset.Locators.Count: Det här antalet rapporteras nu korrekt på nya IAsset-gränssnitt när alla positionerare har tagits bort.
* IAssetFile.ContentFileSize: Det här värdet är nu korrekt inställt efter en uppladdning av IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Den här egenskapen kan nu anges när du skapar en tillgångsfil. Det var tidigare skrivskyddat.
* IAssetFile.Upload(filepath): Ett problem åtgärdades där den här synkrona uppladdningsmetoden kastade följande fel när flera filer laddades upp till tillgången. Felet var "Servern kunde inte autentisera begäran. Kontrollera att värdet för auktoriseringshuvudet är korrekt utformat inklusive signaturen."
* IAssetFile.UploadAsync: Ett problem åtgärdades som begränsade samtidig överföring av filer till fem filer.
* IAssetFile.UploadProgressChanged: Den här händelsen tillhandahålls nu av SDK.
* IAssetFile.DownloadAsync(sträng, BlobTransferClient, ILocator, CancellationToken): Den här metoden överbelastning tillhandahålls nu.
* IAssetFile.DownloadAsync: Ett problem åtgärdades som begränsade samtidig nedladdning av filer till fem filer.
* IAssetFile.Delete(): Ett problem åtgärdades där anropsborttagning kan utlösa ett undantag om ingen fil har överförts för IAssetFile.
* Jobb: Ett problem åtgärdades där en kedja av en "MP4 to Smooth Streams-aktivitet" med en "PlayReady Protection Task" med hjälp av en jobbmall inte skapade några aktiviteter alls.
* EncryptionUtils.GetCertificateFromStore(): Den här metoden genererar inte längre ett null-referensundantag på grund av ett fel i att hitta certifikatet baserat på certifikatkonfigurationsproblem.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>November 2012 release
De ändringar som nämns i det här avsnittet var uppdateringar som ingår i november 2012 (version 2.0.0.0) SDK. Dessa ändringar kan kräva att valfri kod som skrivits för förhandsversionen av SDK från juni 2012 ändras eller skrivs om.

* Tillgångar
  
    * IAsset.Create(assetName) är den *enda* funktionen för att skapa tillgångar. IAsset.Create överför inte längre filer som en del av metodanropet. Använd IAssetFile för uppladdning.
    * Metoden IAsset.Publish och värdet AssetState.Publish togs bort från Services SDK. Alla koder som är beroende av det här värdet måste skrivas om.
* Arkivbild.
  
    * Den här klassen togs bort och ersattes av IAssetFile.
  
* IAssetFiles
  
    * IAssetFile ersätter FileInfo och har ett annat beteende. Om du vill använda det instansierar du IAssetFiles-objektet, följt av en filöverföring antingen med hjälp av Media Services SDK eller Storage SDK. Följande IAssetFile.Upload-överbelastningar kan användas:
  
        * IAssetFile.Upload(filePath): Den här synkrona metoden blockerar tråden och vi rekommenderar den först när du laddar upp en enda fil.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Den här asynkrona metoden är den föredragna uppladdningsmekanismen. 
    
            Känt fel: Om du använder annulleringstoken avbryts överföringen. Uppgifterna kan ha många avbokningstillstånd. Du måste fånga och hantera undantag på rätt sätt.
* Positionerare
  
    * De ursprungsspecifika versionerna togs bort. Det SAS-specifika sammanhanget. Locators.CreateSasLocator (tillgång, accessPolicy) markeras inaktuell eller tas bort av allmän tillgänglighet. Se avsnittet "Locators" under "Ny funktionalitet" för uppdaterat beteende.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Förhandsversion av förhandsgranskningen i juni 2012
Följande funktioner var nya i novemberversionen av SDK:

* Ta bort entiteter
  
    * IAsset-, IAssetFile-, ILocator-, IAccessPolicy- och IContentKey-objekt tas nu bort på objektnivå, det vill än IObject.Delete(), i stället för att kräva en borttagning i samlingen, det vill än cloudMediaContext.ObjCollection.Delete(objInstance).
* Positionerare
  
    * Lokaliseringssätt måste nu skapas med hjälp av metoden CreateLocator. De måste använda värdena LocatorType.SAS eller LocatorType.OnDemandOrigin som ett argument för den specifika typ av positionerare som du vill skapa.
    * Nya egenskaper har lagts till i lokaliseringsapparater för att göra det enklare att få användbara URI:er för ditt innehåll. Den här omdesignen av lokaliseringsorgan ger större flexibilitet för framtida utökningsbarhet från tredje part och ökar användarvänligheten för medieklientprogram.
* Stöd för asynkron metod
  
    * Asynkront stöd har lagts till alla metoder.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[MSDN-forum för Azure Media Services]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[AZURE Media Services REST API-referens]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Inkommande metadata]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Utgående metadata]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Översikt för Media Services PlayReady-licensmall]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamisk paketering]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Återförsökslogik i Media Services SDK för .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

