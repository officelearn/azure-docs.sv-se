---
title: Vanliga frågor och svar på Azure Media Services v3 vanliga frågor| Microsoft-dokument
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services v3.
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
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309188"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor om Azure Media Services v3.

## <a name="general"></a>Allmänt

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Vilka Azure-roller kan utföra åtgärder på Azure Media Services-resurser? 

Se [Rollbaserad åtkomstkontroll (RBAC) för Media Services-konton](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Hur streamar jag till Apple iOS-enheter?

Kontrollera att du har **(format=m3u8-aapl)** i slutet av sökvägen (efter **den /manifestdel** av WEBBADRESSEN) som talar om för att servern för direktuppspelning ska returnera HTTP Live Streaming (HLS) innehåll för förbrukning på Apple iOS-inbyggda enheter. Mer information finns i [Leverera innehåll](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Hur konfigurerar jag mediereserverade enheter?

För jobben Ljudanalys och videoanalys som utlöses av Media Services v3 eller Video Indexer rekommenderar vi att du etablerar ditt konto med 10 S3-mediereserverade enheter (MRUs). Om du behöver mer än 10 S3 MRUs öppnar du en supportbiljett med hjälp av [Azure-portalen](https://portal.azure.com/).

Mer information finns i [Skala mediebearbetning](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vilken är den rekommenderade metoden för att bearbeta videor?

Använd [Transformeringar](https://docs.microsoft.com/rest/api/media/transforms) för att konfigurera vanliga uppgifter för kodning eller analys av videor. Varje transformering beskriver ett recept eller ett arbetsflöde med uppgifter för bearbetning av video- eller ljudfiler. Ett [jobb](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Media Services om att tillämpa Transform på ett indatavideo- eller ljudinnehåll. När transformeringen har skapats kan du skicka jobb med hjälp av Api:er för Media Services eller någon av de publicerade SDK:erna. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Jag laddade upp, kodade och publicerade en video. Varför spelas inte videon upp när jag försöker streama den?

En av de vanligaste orsakerna är att du inte har den slutpunkt för direktuppspelning som du försöker spela upp i läget Kör.

### <a name="how-does-pagination-work"></a>Hur fungerar sidnumrering?

När du använder sidnumrering bör du alltid använda nästa länk för att räkna upp samlingen och inte vara beroende av en viss sidstorlek. Mer information och exempel finns i [Filtrera, beställa, söka](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Vilka funktioner är ännu inte tillgängliga i Azure Media Services v3?

Mer information finns i [Funktionsluckor med avseende på v2 API:er](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Vad är processen med att flytta ett Media Services-konto mellan prenumerationer?  

Mer information finns i [Flytta ett Media Services-konto mellan prenumerationer](media-services-account-concept.md).

## <a name="live-streaming"></a>Liveuppspelning 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Hur stoppar jag livestreamen när sändningen är klar?

Du kan närma dig den från klientsidan eller serversidan.

#### <a name="client-side"></a>Klientsidan

Webbprogrammet bör fråga användaren om de vill avsluta sändningen när de stänger webbläsaren. Detta är en webbläsarhändelse som webbprogrammet kan hantera.

#### <a name="server-side"></a>Serversidan

Du kan övervaka livehändelser genom att prenumerera på Azure Event Grid-händelser. Mer information finns i [EventGrids händelseschema](media-services-event-schemas.md#live-event-types).

Du kan antingen:

* [Prenumerera på](reacting-to-media-services-events.md) [dataströmsnivå Microsoft.Media.LiveEventEncoderUppkopplade](media-services-event-schemas.md#liveeventencoderdisconnected) händelser och övervaka att inga återanslutningar kommer in på ett tag för att stoppa och ta bort din livehändelse.
* [Prenumerera på](reacting-to-media-services-events.md) [pulsslagshändelser](media-services-event-schemas.md#liveeventingestheartbeat) på spårnivå. Om alla spår har en inkommande bitrate släppa till 0 eller den sista gången stämpeln inte längre ökar, kan du säkert stänga av live-händelsen. Hjärtslagen händelser kommer in på var 20 sekunder för varje spår, så det kan vara lite utförlig.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hur infogar jag raster/videor och bildskiffer under en livestream?

Media Services v3 live-kodning har ännu inte stöd för att infoga video- eller bildskiffer under livestreamen. 

Du kan använda en [lokal kodare](recommended-on-premises-live-encoders.md) för att växla källvideon. Många appar ger möjlighet att byta källor, inklusive Telestream Wirecast, Switcher Studio (på iOS) och OBS Studio (gratis app).

## <a name="content-protection"></a>Innehållsskydd

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Ska jag använda AES-128 clear key kryptering eller ett DRM-system?

Kunderna undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den största skillnaden mellan de två systemen är att med AES-kryptering överförs innehållsnyckeln till klienten via TLS så att nyckeln krypteras under överföring men utan ytterligare kryptering ("i klartext"). Därför är nyckeln som används för att dekryptera innehållet tillgänglig för klientspelaren och kan visas i en nätverksspårning på klienten i oformaterad text. AES-128 tydlig nyckel kryptering är lämplig för användningsfall där betraktaren är en betrodd part (till exempel kryptera företagsvideor distribueras inom ett företag som ska ses av anställda).

DRM-system som PlayReady, Widevine och FairPlay ger alla en extra krypteringsnivå på nyckeln som används för att dekryptera innehållet, jämfört med en klar nyckel för AES-128. Innehållsnyckeln krypteras till en nyckel som skyddas av DRM-körningen utöver all kryptering på transportnivå som tillhandahålls av TLS. Dessutom hanteras dekryptering i en säker miljö på operativsystemsnivå, där det är svårare för en obehörig användare att attackera. Vi rekommenderar DRM för användningsfall där visningsprogrammet kanske inte är en betrodd part och du behöver den högsta säkerhetsnivån.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hur visar jag en video för endast användare som har en specifik behörighet, utan att använda Azure AD?

Du behöver inte använda någon specifik tokenprovider som Azure Active Directory (Azure AD). Du kan skapa din egen [JWT-leverantör](https://jwt.io/) (så kallad Secure Token Service eller STS) med hjälp av asymmetrisk nyckelkryptering. I din anpassade STS kan du lägga till anspråk baserat på din affärslogik.

Se till att utfärdaren, publiken och anspråken stämmer överens exakt mellan `ContentKeyPolicyRestriction` vad som `ContentKeyPolicy`finns i JWT och det värde som används i .

Mer information finns i [Skydda ditt innehåll med hjälp av dynamisk kryptering av Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hur och var fick jag en JWT-token innan jag använde den för att begära en licens eller nyckel?

För produktion måste du ha Secure Token Service (det vill säga en webbtjänst), som utfärdar en JWT-token på en HTTPS-begäran. För test kan du använda koden `GetTokenAsync` som visas i den metod som definieras i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Spelaren gör en begäran, efter att en användare har autentiserats, till STS för en sådan token och tilldelar den som värdet av token. Du kan använda [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

Ett exempel på att köra STS med antingen en symmetrisk nyckel eller en asymmetrisk nyckel finns i [JWT-verktyget](https://aka.ms/jwt). Ett exempel på en spelare som baseras på Azure Media Player med en sådan JWT-token finns i [Testverktyget för Azure Media](https://aka.ms/amtest). (Expandera **länken player_settings** för att se tokenindatan.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hur godkänner jag begäranden om att strömma videor med AES-kryptering?

Den korrekta metoden är att använda Secure Token Service. I STS, beroende på användarprofilen, lägger du till olika anspråk (till exempel "Premium-användare", "Grundläggande användare", "Kostnadsfri utvärderingsanvändare"). Med olika anspråk i en JWT kan användaren se olika innehåll. För olika innehåll `ContentKeyPolicyRestriction` eller tillgångar, `RequiredClaims` kommer att ha motsvarande värde.

Använd Azure Media Services API:er för att konfigurera licens-/nyckelleverans och kryptera dina tillgångar (som visas i [det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Mer information finns i:

- [Översikt över innehållsskydd](content-protection-overview.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Ska jag använda HTTP eller HTTPS?
Programmet ASP.NET MVC-spelare måste ha stöd för följande:

* Användarautentisering via Azure AD, som är under HTTPS.
* JWT-utbyte mellan klienten och Azure AD, som ligger under HTTPS.
* DRM-licensförvärv av klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. Produktpaketet PlayReady föreskriver inte HTTPS för licensleverans. Om licensservern för PlayReady ligger utanför Media Services kan du använda http- eller HTTPS-tjänster.

Programmet ASP.NET spelare använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP är dock att föredra för direktuppspelning, så du måste överväga dessa problem med blandat innehåll:

* Webbläsaren tillåter inte blandat innehåll. Men plug-ins som Silverlight och OSMF plug-in för Smooth och DASH tillåter det. Blandat innehåll är ett säkerhetsproblem på grund av hotet om möjligheten att injicera skadliga JavaScript, vilket kan äventyra kunddata. Webbläsare blockerar den här funktionen som standard. Det enda sättet att komma runt det är på serversidan (ursprung) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte en bra idé heller.
* Undvik blandat innehåll. Både spelarprogrammet och Media Player ska använda HTTP eller HTTPS. När du spelar upp blandat innehåll kräver SilverlightSS-tekniken att du rensar en varning med blandat innehåll. FlashSS-tekniken hanterar blandat innehåll utan en varning om blandat innehåll.
* Om slutpunkten för direktuppspelning skapades före augusti 2014 stöder den inte HTTPS. I det här fallet skapar och använder du en ny slutpunkt för direktuppspelning för HTTPS.

### <a name="what-about-live-streaming"></a>Vad sägs om live streaming?

Du kan använda exakt samma design och implementering för att skydda livestreaming i Media Services genom att behandla tillgången som är associerad med ett program som en VOD-tillgång. Om du vill ge ett multi-DRM-skydd av live-innehållet använder du samma inställning/bearbetning på tillgången som om den vore en VOD-tillgång innan du associerar tillgången med live-utdata.

### <a name="what-about-license-servers-outside-media-services"></a>Hur är det med licensservrar utanför Media Services?

Ofta har kunder investerat i en licensservergrupp antingen i sitt eget datacenter eller i ett som finns hos DRM-tjänsteleverantörer. Med Innehållsskydd för Media Services kan du arbeta i hybridläge. Innehållet kan vara värd och dynamiskt skyddat i Media Services, medan DRM-licenser levereras av servrar utanför Media Services. I så fall bör du tänka på följande ändringar:

* STS måste utfärda token som är acceptabla och som kan verifieras av licensservergruppen. Till exempel kräver Widevine-licensservrarna som tillhandahålls av Axinom en specifik JWT som innehåller ett berättigandemeddelande. Du måste ha en STS att utfärda en sådan JWT. 
* Du behöver inte längre konfigurera licensleveranstjänsten i Media Services. Du måste ange url:erna för licensförvärv (för PlayReady, `ContentKeyPolicy`Widevine och FairPlay) när du konfigurerar .

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google och omfattas av användarvillkoren och sekretesspolicyn för Google.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3-resurser?

För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att:

* Hantera [livehändelser](live-events-outputs-concept.md) i Media Services v3. 
* Visa (inte hantera) [v3-tillgångar](assets-concept.md). 
* [Få information om hur du använder API:er](access-api-portal.md). 

För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) och [content protection)](content-protection-overview.md)använder du [REST API](https://docs.microsoft.com/rest/api/media/), Azure [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er](media-services-apis-overview.md#sdks)som stöds .

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile koncept i v3?

Konceptet `AssetFile` togs bort från API:et för mediatjänster för att separera Media Services från Storage SDK-beroende. Nu Azure Storage, inte Media Services, behåller den information som hör hemma i Storage SDK. 

Mer information finns i [Migrera till Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Vart tog kryptering på klientsidan plats?

Vi rekommenderar nu att du använder lagringskryptering på serversidan (som är aktiverat som standard). Mer information finns i [Azure Storage Service Encryption för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Direktuppspelning offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming för iOS

Följande vanliga frågor ger hjälp med felsökning offline FairPlay streaming för iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Varför spelas bara ljud upp men inte video i offlineläge?

Det här beteendet verkar vara avsiktligt i exempelappen. När det finns ett alternativt ljudspår (vilket är fallet för HLS) i offlineläge, standard både iOS 10 och iOS 11 det alternativa ljudspåret. Om du vill kompensera för det här beteendet för FPS-offlineläge tar du bort det alternativa ljudspåret från strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifestfiltret **audio-only=false**. Med andra ord slutar en HLS-URL med **.ism/manifest(format=m3u8-aapl,audio-only=false).** 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Varför spelar den fortfarande upp ljud endast utan video i offlineläge efter att jag har lagt till ljud-only=false?

Beroende på cachenyckeldesignen för innehållsleveransnätverket kan innehållet cachelagras. Rensa cacheminnet.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Stöds FPS-offlineläge på iOS 11 utöver iOS 10?

Ja. FPS-offlineläge stöds för iOS 10 och iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Varför hittar jag inte dokumentet "Offlineuppspelning med FairPlay Streaming och HTTP Live Streaming" i FPS Server SDK?

Sedan FPS Server SDK version 4 har det här dokumentet slagits samman till "FairPlay Streaming Programming Guide".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Vad är den nedladdade/offlinefilstrukturen på iOS-enheter?

Den nedladdade filstrukturen på en iOS-enhet ser ut som följande skärmbild. Mappen `_keys` lagrar nedladdade FPS-licenser, med en butiksfil för varje licenstjänstvärd. Mappen `.movpkg` lagrar ljud- och videoinnehåll. 

Den första mappen med ett namn som slutar med ett streck följt av ett nummer innehåller videoinnehåll. Det numeriska värdet är den högsta bandbredden för videoåtergivningarna. Den andra mappen med ett namn som slutar med ett streck följt av 0 innehåller ljudinnehåll. Den tredje `Data` mappen som heter innehåller huvudspellistan för FPS-innehållet. Slutligen ger boot.xml en fullständig `.movpkg` beskrivning av mappinnehållet. 

![Offlinefilstruktur för exempelappen FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Här är en exempel boot.xml-fil:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hur kan jag leverera beständiga licenser (offlineaktiverade) för vissa klienter/användare och icke-beständiga licenser (offline inaktiverade) för andra? Måste jag duplicera innehållet och använda separata innehållsnycklar?

Eftersom Media Services v3 gör `StreamingLocator` att en tillgång kan ha flera instanser kan du ha:

* En `ContentKeyPolicy` instans `license_type = "persistent"` `ContentKeyPolicyRestriction` med , `"persistent"`med `StreamingLocator`anspråk på , och dess .
* En `ContentKeyPolicy` annan `license_type="nonpersistent"` `ContentKeyPolicyRestriction` instans med `"nonpersistent`, med `StreamingLocator`anspråk på ", och dess .
* Två `StreamingLocator` instanser som `ContentKey` har olika värden.

Beroende på affärslogiken för anpassade STS utfärdas olika anspråk i JWT-token. Med token kan endast motsvarande licens erhållas och endast motsvarande WEBBADRESS kan spelas upp.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Vad är mappningen mellan Widevine- och Media Services DRM-säkerhetsnivåerna?

Googles "Widevine DRM Architecture Overview" definierar tre säkerhetsnivåer. [I Azure Media Services-dokumentationen på Widevine-licensmallen](widevine-license-template-overview.md) beskrivs dock fem säkerhetsnivåer (klientkontensitetskrav för uppspelning). I det här avsnittet beskrivs hur säkerhetsnivåerna mappas.

Båda uppsättningarna säkerhetsnivåer definieras av Google Widevine. Skillnaden är i användningsnivå: arkitektur eller API. De fem säkerhetsnivåerna används i Widevine API. Objektet, `content_key_specs` som innehåller `security_level`, avserialiseras och skickas till Widevine globala leveranstjänsten av Azure Media Services Widevine-licenstjänsten. I följande tabell visas mappningen mellan de två uppsättningarna säkerhetsnivåer.

| **Säkerhetsnivåer definierade i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Säkerhetsnivå 1:** All innehållsbearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementeringsmodeller kan säkerhetsbearbetning utföras i olika marker.|**security_level=5**: Krypto, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras inom en maskinvarubaserad TEE.<br/><br/>**security_level=4**: Krypto och avkodning av innehåll måste utföras i en maskinvarubaserad TEE.|
**Säkerhetsnivå 2:** Kryptografi (men inte videobearbetning) utförs inom TEE. Dekrypterade buffertar returneras till programdomänen och bearbetas via separat videomaskinvara eller programvara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.| **security_level=3**: Nyckelmaterialet och kryptoåtgärderna måste utföras inom en maskinvarubaserad TEE. |
| **Säkerhet nivå 3:** Det finns ingen TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll i värdoperativsystemet. En nivå 3-implementering kan också innehålla en kryptografisk maskinvarumotor, men som bara förbättrar prestanda, inte säkerhet. | **security_level=2**: Programvarukrypt och en fördunklad dekoder krävs.<br/><br/>**security_level=1**: Programvarubaserad white-box krypto krävs.|

#### <a name="why-does-content-download-take-so-long"></a>Varför tar nedladdningen av innehåll så lång tid?

Det finns två sätt att förbättra nedladdningshastigheten:

* Aktivera ett nätverk för innehållsleverans så att användarna är mer benägna att slå till det i stället för slutpunkten för innehåll/direktuppspelning för nedladdning av innehåll. Om en användare träffar en slutpunkt för direktuppspelning paketeras och krypteras varje HLS-segment eller DASH-fragment dynamiskt. Även om den här svarstiden är i millisekundskala för varje segment eller fragment, kan den ackumulerade svarstiden vara stor när du har en timmes lång video och orsaka en längre hämtning.
* Ge användarna möjlighet att selektivt ladda ner lager videokvalitet och ljudspår i stället för allt innehåll. För offline-läge är det ingen idé att hämta alla kvalitetslager. Det finns två sätt att uppnå detta:

  * Klientkontrollerad: Spelarens app väljer automatiskt, eller användaren väljer, videokvalitetslagret och de ljudspår som ska hämtas.
  * Tjänststyrd: Du kan använda funktionen Dynamiskt manifest i Azure Media Services för att skapa ett (globalt) filter som begränsar HLS-spellista eller DASH MPD till ett enda videokvalitetslager och valda ljudspår. Då nedladdningsadressen presenteras för användarna kommer att innehålla detta filter.

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
