---
title: Vanliga frågor och svar på Azure Media Services v3 vanliga frågor| Microsoft-dokument
description: Den här artikeln ger svar på Azure Media Services v3 vanliga frågor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886831"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 vanliga frågor och svar

Den här artikeln ger svar på Azure Media Services (AMS) v3 vanliga frågor.

## <a name="general"></a>Allmänt

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Vilka Azure-roller kan utföra åtgärder på Azure Media Services-resurser? 

Se [Rollbaserad åtkomstkontroll (RBAC) för Media Services-konton](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Hur streamar du till Apple iOS-enheter?

Se till att du har "(format=m3u8-aapl)" i slutet av sökvägen (efter den "/manifest" delen av webbadressen) för att tala om för den strömmande ursprungsservern att returnera HLS-innehåll för konsumtion på Apple iOS-inbyggda enheter (för mer information, se [leverera innehåll](dynamic-packaging-overview.md)).

### <a name="how-do-i-configure-media-reserved-units"></a>Hur konfigurerar jag mediereserverade enheter?

För ljudanalys- och videoanalysjobb som utlöses av Media Services v3 eller Video Indexer rekommenderar vi starkt att du etablerar ditt konto med 10 S3-MRU:er. Om du behöver mer än 10 S3 MRUs öppnar du en supportbiljett med [Azure-portalen](https://portal.azure.com/).

Mer information finns i [Skala mediebearbetning med CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vilken är den rekommenderade metoden för att bearbeta videor?

Använd [Transformeringar](https://docs.microsoft.com/rest/api/media/transforms) för att konfigurera vanliga uppgifter för kodning eller analys av videor. Varje **transformering** beskriver ett recept eller ett arbetsflöde med uppgifter för bearbetning av video- eller ljudfiler. Ett [jobb](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Media Services att tillämpa **Transform** på ett visst indatavideo- eller ljudinnehåll. När transformeringen har skapats kan du skicka jobb med Api:er för Media Services eller någon av de publicerade SDK:erna. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Jag laddade upp, kodade och publicerade en video. Vad skulle vara anledningen till att videon inte spelas upp när jag försöker streama den?

En av de vanligaste orsakerna är att du inte har den slutpunkt för direktuppspelning som du försöker spela upp i läget Kör.

### <a name="how-does-pagination-work"></a>Hur fungerar sidnumrering?

När du använder sidnumrering bör du alltid använda nästa länk för att räkna upp samlingen och inte vara beroende av en viss sidstorlek. Mer information och exempel finns i [Filtrera, beställa, söka](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Vilka funktioner är ännu inte tillgängliga i Azure Media Services v3?

Mer information finns [i funktionsluckor med avseende på v2 API:er](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Vad är processen med att flytta ett Media Services-konto mellan prenumerationer?  

Mer information finns i [Flytta ett Media Services-konto mellan prenumerationer](media-services-account-concept.md).

## <a name="live-streaming"></a>Liveuppspelning 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Hur man stoppar live stream efter sändningen görs?

Du kan närma dig den från en klient- eller serversida.

#### <a name="client-side"></a>Klientsidan

Webbprogrammet bör fråga användaren om de vill avsluta sändningen om de stänger webbläsaren. Detta är en webbläsarhändelse som webbprogrammet kan hantera.

#### <a name="server-side"></a>Serversidan

Du kan övervaka livehändelser genom att prenumerera på eventrutnät. Mer information finns i [eventgrid-händelseschemat](media-services-event-schemas.md#live-event-types).

* Du kan antingen [prenumerera på](reacting-to-media-services-events.md) strömnivån [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) och övervaka att inga återanslutningar kommer in på ett tag för att stoppa och ta bort din livehändelse.
* Du kan också [prenumerera på](reacting-to-media-services-events.md) [pulsslagshändelser](media-services-event-schemas.md#liveeventingestheartbeat) på spårnivå. Om alla spår har inkommande bithastighet sjunker till 0; eller den sista tidsstämpeln inte längre ökar, då kan du också säkert stänga av live-händelsen. Hjärtslagen händelser kommer in på var 20 sekunder för varje spår så det kan vara lite utförlig.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hur man sätter in raster / videor och griffeltavlor bild under live stream?

Media Services v3 live-kodning har ännu inte stöd för att infoga video- eller bildskiffer under livestreamen. 

Du kan använda en [lokal kodare](recommended-on-premises-live-encoders.md) för att växla källvideon. Många appar ger möjlighet att byta källor, inklusive Telestream Wirecast, Switcher Studio (på iOS), OBS Studio (gratis app) och många fler.

## <a name="content-protection"></a>Innehållsskydd

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Ska jag använda en AES-128 klar nyckel kryptering eller ett DRM-system?

Kunderna undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den primära skillnaden mellan de två systemen är att med AES-kryptering överförs innehållsnyckeln till klienten via TLS så att nyckeln krypteras under överföring men utan ytterligare kryptering ("i klartext"). Därför är nyckeln som används för att dekryptera innehållet tillgänglig för klientspelaren och kan visas i en nätverksspårning på klienten i oformaterad text. En AES-128-kryptering med klar nyckel är lämplig för användningsfall där tittaren är en betrodd part (till exempel kryptera företagsvideor som distribueras inom ett företag som ska visas av anställda).

DRM-system som PlayReady, Widevine och FairPlay ger alla en extra krypteringsnivå på nyckeln som används för att dekryptera innehållet jämfört med en klar nyckel för AES-128. Innehållsnyckeln krypteras till en nyckel som skyddas av DRM-körningen utöver all kryptering på transportnivå som tillhandahålls av TLS. Dessutom hanteras dekryptering i en säker miljö på operativsystemsnivå, där det är svårare för en obehörig användare att attackera. DRM rekommenderas för användningsfall där visningsprogrammet kanske inte är en betrodd part och du behöver den högsta säkerhetsnivån.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hur visar man en video endast för användare som har en specifik behörighet, utan att använda Azure AD?

Du behöver inte använda någon specifik tokenprovider (till exempel Azure AD). Du kan skapa din egen [JWT-leverantör](https://jwt.io/) (så kallad STS, Secure Token Service) med hjälp av asymmetrisk nyckelkryptering. I din anpassade STS kan du lägga till anspråk baserat på din affärslogik.

Se till att utfärdaren, målgruppen och anspråken stämmer överens exakt mellan vad som finns i JWT och ContentKeyPolicyRestriction som används i ContentKeyPolicy.

Mer information finns i [Skydda ditt innehåll med hjälp av dynamisk kryptering av Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hur och var du kan få JWT token innan du använder den för att begära licens eller nyckel?

1. För produktion måste du ha en säker tokentjänst (STS) (webbtjänst) som utfärdar JWT-token på en HTTPS-begäran. För test kan du använda koden som visas i **GetTokenAsync-metoden** som definierats i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Spelaren måste göra en begäran, efter att en användare har autentiserats, till STS för en sådan token och tilldela den som värdet av token. Du kan använda [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Ett exempel på att köra STS, med antingen symmetrisk och asymmetrisk nyckel, se [https://aka.ms/jwt](https://aka.ms/jwt). 
* Ett exempel på en spelare som baseras på Azure Media [https://aka.ms/amtest](https://aka.ms/amtest) Player med en sådan JWT-token finns i länken "player_settings" för att se tokenindata).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hur godkänner du begäranden om att strömma videor med AES-kryptering?

Den korrekta metoden är att utnyttja STS (Secure Token Service):

I STS, beroende på användarprofil, lägger du till olika anspråk (till exempel "Premium-användare", "Grundläggande användare", "Kostnadsfri utvärderingsanvändare"). Med olika anspråk i en JWT kan användaren se olika innehåll. För olika innehåll/tillgångar har ContentKeyPolicyRestriction naturligtvis motsvarande RequiredClaims.

Använd Azure Media Services API:er för att konfigurera licens-/nyckelleverans och kryptera dina tillgångar (som visas i [det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Mer information finns i:

- [Översikt över innehållsskydd](content-protection-overview.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP eller HTTPS?
Programmet ASP.NET MVC-spelare måste ha stöd för följande:

* Användarautentisering via Azure AD, som är under HTTPS.
* JWT-utbyte mellan klienten och Azure AD, som ligger under HTTPS.
* DRM-licensförvärv av klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. Produktpaketet PlayReady föreskriver inte HTTPS för licensleverans. Om licensservern för PlayReady ligger utanför Media Services kan du använda http- eller HTTPS-tjänster.

Programmet ASP.NET spelare använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP är dock att föredra för direktuppspelning, så du måste överväga problemet med blandat innehåll.

* Webbläsaren tillåter inte blandat innehåll. Men plug-ins som Silverlight och OSMF plug-in för smidig och DASH gör det. Blandat innehåll är ett säkerhetsproblem på grund av hotet om möjligheten att injicera skadliga JavaScript, vilket kan orsaka att kunddata är i fara. Webbläsare blockerar den här funktionen som standard. Det enda sättet att komma runt det är på serversidan (ursprung) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte en bra idé heller.
* Undvik blandat innehåll. Både spelarprogrammet och Media Player ska använda HTTP eller HTTPS. När du spelar upp blandat innehåll kräver silverlightSS-tekniken att du rensar en varning med blandat innehåll. FlashSS-tekniken hanterar blandat innehåll utan en varning om blandat innehåll.
* Om slutpunkten för direktuppspelning skapades före augusti 2014 stöder den inte HTTPS. I det här fallet skapar och använder du en ny slutpunkt för direktuppspelning för HTTPS.

### <a name="what-about-live-streaming"></a>Vad sägs om live streaming?

Du kan använda exakt samma design och implementering för att skydda direktuppspelning i Media Services genom att behandla tillgången som är associerad med ett program som en VOD-tillgång. Om du vill ge ett multi-DRM-skydd av live-innehållet använder du samma inställning/bearbetning på tillgången som om det vore en VOD-tillgång innan du associerar tillgången med Live Output.

### <a name="what-about-license-servers-outside-media-services"></a>Hur är det med licensservrar utanför Media Services?

Ofta investerade kunder i en licensservergrupp antingen i sitt eget datacenter eller ett som finns hos DRM-tjänsteleverantörer. Med Innehållsskydd för Media Services kan du arbeta i hybridläge. Innehållet kan vara värd och dynamiskt skyddat i Media Services, medan DRM-licenser levereras av servrar utanför Media Services. I så fall bör du tänka på följande ändringar:

* STS måste utfärda token som är acceptabla och som kan verifieras av licensservergruppen. Till exempel kräver Widevine-licensservrarna som tillhandahålls av Axinom en specifik JWT som innehåller ett berättigandemeddelande. Därför måste du ha en STS att utfärda en sådan JWT. 
* Du behöver inte längre konfigurera licensleveranstjänsten i Media Services. Du måste ange url:er för licensförvärv (för PlayReady, Widevine och FairPlay) när du konfigurerar ContentKeyPolicies.

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3-resurser?

För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att:

* hantera Media Services v3 [Live Events](live-events-outputs-concept.md), 
* visa (inte hantera) [v3-tillgångar](assets-concept.md), 
* [få information om hur du använder API:er](access-api-portal.md). 

För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) and [Content protection)](content-protection-overview.md)använder du [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile koncept i v3?

AssetFiles togs bort från AMS API för att skilja Media Services från Storage SDK beroende. Nu lagring, inte Media Services, behåller den information som hör hemma i Lagring. 

Mer information finns i [Migrera till Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Vart tog kryptering på klientsidan plats?

Det rekommenderas nu att använda lagringskrypteringen på serversidan (som är aktiverat som standard). Mer information finns i [Azure Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Direktuppspelning offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming för iOS

Följande vanliga frågor ger hjälp med felsökning offline FairPlay streaming för iOS:

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Varför spelas bara ljud upp men inte video i offlineläge?

Det här beteendet verkar vara avsiktligt i exempelappen. När det finns ett alternativt ljudspår (vilket är fallet för HLS) i offlineläge, standard både iOS 10 och iOS 11 det alternativa ljudspåret. Om du vill kompensera för det här beteendet för FPS-offlineläge tar du bort det alternativa ljudspåret från strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifestfiltret "endast ljud=false". Med andra ord slutar en HLS-URL med .ism/manifest(format=m3u8-aapl,audio-only=false). 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Varför spelar den fortfarande upp ljud endast utan video i offlineläge efter att jag har lagt till ljud-only=false?

Beroende på utformningen av cdn-cachenyckeln (Content Delivery Network) kan innehållet cachelagras. Rensa cacheminnet.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>Stöds även FPS-offlineläge på iOS 11 utöver iOS 10?

Ja. FPS-offlineläge stöds för iOS 10 och iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Varför hittar jag inte dokumentet "Offlineuppspelning med FairPlay Streaming och HTTP Live Streaming" i FPS Server SDK?

Sedan FPS Server SDK version 4 har det här dokumentet slagits samman till "FairPlay Streaming Programming Guide".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Vad är den nedladdade/offlinefilstrukturen på iOS-enheter?

Den nedladdade filstrukturen på en iOS-enhet ser ut som följande skärmbild. Mappen `_keys` lagrar nedladdade FPS-licenser, med en butiksfil för varje licenstjänstvärd. Mappen `.movpkg` lagrar ljud- och videoinnehåll. Den första mappen med ett namn som slutar med ett streck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är PeakBandwidth för videoåtergivningarna. Den andra mappen med ett namn som slutar med ett streck följt av 0 innehåller ljudinnehåll. Den tredje mappen med namnet "Data" innehåller huvudspellistan för FPS-innehållet. Slutligen ger boot.xml en fullständig `.movpkg` beskrivning av mappinnehållet. 

![Offline FairPlay iOS-exempelappfilstruktur](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Ett exempel boot.xml-fil:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine streaming för Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>Hur kan jag leverera beständiga licenser (offlineaktiverade) för vissa klienter/användare och icke-beständiga licenser (offline inaktiverade) för andra? Måste jag duplicera innehållet och använda separat innehållsnyckel?

Eftersom Media Services v3 tillåter en tillgång att ha flera StreamingLocators. Du kan få

* En ContentKeyPolicy med license_type = "persistent", ContentKeyPolicyRestriction med anspråk på "persistent" och dess StreamingLocator;
* En annan ContentKeyPolicy med license_type="nonpersistent", ContentKeyPolicyRestriction med anspråk på "nonpersistent" och dess StreamingLocator.
* De två StreamingLocators har olika ContentKey.

Beroende på affärslogiken för anpassade STS utfärdas olika anspråk i JWT-token. Med token kan endast motsvarande licens erhållas och endast motsvarande WEBBADRESS kan spelas upp.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Vad är mappningen mellan Widevine- och Media Services DRM-säkerhetsnivåerna?

Googles "Widevine DRM Architecture Overview" definierar tre olika säkerhetsnivåer. I [Azure Media Services-dokumentationen på Widevine-licensmallen](widevine-license-template-overview.md)beskrivs dock fem olika säkerhetsnivåer. I det här avsnittet beskrivs hur säkerhetsnivåerna mappas.

Googles doc "Widevine DRM Architecture Review" definierar följande tre säkerhetsnivåer:

* Säkerhetsnivå 1: All innehållsbearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementeringsmodeller kan säkerhetsbearbetning utföras i olika marker.
* Säkerhetsnivå 2: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat videomaskinvara eller programvara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.
* Säkerhetsnivå 3 Har ingen TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll i värdoperativsystemet. En nivå 3-implementering kan också innehålla en kryptografisk maskinvarumotor, men som bara förbättrar prestanda, inte säkerhet.

I Azure Media [Services-dokumentationen på Widevine-licensmallen](widevine-license-template-overview.md)kan security_level-egenskapen för content_key_specs ha följande fem olika värden (klients robusthetskrav för uppspelning):

* Programvarubaserad white-box krypto krävs.
* Programvarukrypt och en fördunklad dekoder krävs.
* Nyckelmaterialet och kryptoåtgärderna måste utföras inom en maskinvarubaserad TEE.
* Krypto och avkodning av innehåll måste utföras inom en maskinvarubaserad TEE.
* Krypto, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras inom en maskinvarubaserad TEE.

Båda säkerhetsnivåerna definieras av Google Widevine. Skillnaden är i dess användningsnivå: arkitekturnivå eller API-nivå. De fem säkerhetsnivåerna används i Widevine API. Det content_key_specs objektet, som innehåller security_level är deserialiserat och skickas till Widevine globala leveranstjänst av Azure Media Services Widevine-licenstjänsten. Tabellen nedan visar mappningen mellan de två uppsättningarna säkerhetsnivåer.

| **Säkerhetsnivåer definierade i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Säkerhetsnivå 1:** All innehållsbearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementeringsmodeller kan säkerhetsbearbetning utföras i olika marker.|**security_level=5**: Krypto, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras inom en maskinvarubackad TEE.<br/><br/>**security_level=4**: Krypto och avkodning av innehåll måste utföras i en maskinvarubackad TEE.|
**Säkerhetsnivå 2:** Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat videomaskinvara eller programvara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.| **security_level=3**: Nyckelmaterialet och kryptoåtgärderna måste utföras inom en maskinvarubaserad TEE. |
| **Säkerhetsnivå 3:** Har ingen TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll i värdoperativsystemet. En nivå 3-implementering kan också innehålla en kryptografisk maskinvarumotor, men som bara förbättrar prestanda, inte säkerhet. | **security_level=2**: Programvarukrypt och en fördunklad dekoder krävs.<br/><br/>**security_level=1**: Programvarubaserad white-box krypto krävs.|

#### <a name="why-does-content-download-take-so-long"></a>Varför tar nedladdningen av innehåll så lång tid?

Det finns två sätt att förbättra nedladdningshastigheten:

* Aktivera CDN så att slutanvändarna är mer benägna att träffa CDN istället för ursprung / streaming slutpunkt för nedladdning av innehåll. Om användaren träffar slutpunkten för direktuppspelning paketeras och krypteras varje HLS-segment eller DASH-fragment dynamiskt. Även om den här svarstiden är i millisekundskala för varje segment/fragment, kan den ackumulerade svarstiden vara stor och orsaka längre hämtning.
* Ge slutanvändare möjlighet att selektivt ladda ner videokvalitet lager och ljudspår i stället för allt innehåll. För offline-läge är det ingen idé att hämta alla kvalitetslager. Det finns två sätt att uppnå detta:

   * Klientkontrollerad: antingen väljer spelarens app automatiskt eller så väljer användaren videokvalitetslager och ljudspår att ladda ned.
   * Tjänststyrd: man kan använda funktionen Dynamiskt manifest i Azure Media Services för att skapa ett (globalt) filter som begränsar HLS-spellista eller DASH MPD till ett enda videokvalitetslager och valda ljudspår. Då nedladdnings-URL presenteras för slutanvändare kommer att innehålla detta filter.

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
