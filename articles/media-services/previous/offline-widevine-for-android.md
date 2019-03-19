---
title: Konfigurera ditt konto för offline strömning av Widevine-skyddat innehåll – Azure
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services-konto för offline strömning av Widevine-skyddat innehåll.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf2f2db57f33645389fd751c8c00f9f135416c50
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864141"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine för direktuppspelning för Android  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 3](../latest/offline-widevine-for-android.md)
> * [Version 2](offline-widevine-for-android.md)

Utöver att skydda innehåll för online-direktuppspelning av media innehåll prenumeration och uthyrning tjänster erbjudandet nedladdningsbart innehåll som fungerar när du inte är ansluten till internet. Du kan behöva hämta innehållet på din telefon eller surfplatta för uppspelning i Flygplansläge när som frånkopplad från nätverket. Fler scenarier där du laddar ned innehåll:

- Vissa innehållsleverantörer kan neka DRM-licensleverans utöver ett land kantlinje. Om en användare vill titta på innehållet under resor utomlands krävs offline download.
- I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd begränsat. Användare kan välja att ladda ned innehåll för att kunna se det fungera tillräckligt hög matchning för tillfredsställande tittarupplevelse.

Den här artikeln beskrivs hur du implementerar offlineläge uppspelning för DASH-innehåll som skyddas av Widevine på Android-enheter. Offline DRM kan du tillhandahålla prenumeration, hyra och inköp modeller för innehåll, och kunderna för dina tjänster kan enkelt ta innehåll med dem. när bortkopplad från internet.

För att skapa appar för Android player beskriver vi tre alternativ:

> [!div class="checklist"]
> * Skapa en spelare med hjälp av Java-API för ExoPlayer SDK
> * Skapa en spelare med Xamarin-bindningen för ExoPlayer SDK
> * Skapa en spelare med tillägget EME (Encrypted Media) och Media käll-tillägget (MSE) i Chrome mobila webbläsare v62 eller senare

I artikeln får även svar på vanliga frågor rör offline strömning av Widevine-skyddat innehåll.

## <a name="requirements"></a>Krav 

Innan du implementerar offline DRM för Widevine på Android-enheter, bör du först:

- Bekanta dig med de olika begreppen för online innehållsskydd med Widevine DRM. Detta beskrivs i detalj i följande dokument/samples:
    - [Använda Azure Media Services för att leverera DRM-licenser eller AES-nycklar](media-services-deliver-keys-and-licenses.md)
    - [CENC med Multi-DRM och Access Control: En Referensdesign och implementering i Azure och Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Använda PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Använd Azure Media Services för att leverera PlayReady och/eller Widevine-licenser med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Bekanta dig med Google ExoPlayer SDK för Android, en öppen källkod videospelaren SDK kan stödja offline Widevine DRM-uppspelning. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer Utvecklarguide](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer-bloggen](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Content protection-konfiguration i Azure Media Services

När du konfigurerar Widevine skydd av en tillgång i Media Services kan behöva du skapa ContentKeyAuthorizationPolicyOption som anges i följande tre saker:

1. DRM-system (Widevine)
2. ContentKeyAuthorizationPolicyRestriction att ange hur viktiga innehållsleverans är godkända på licensleveranstjänst (öppen eller token auktorisering)
3. DRM (Widevine)-licensmall

Aktivera **offline** läge för Widevine-licenser, måste du konfigurera [Widevine-licensmall](media-services-widevine-license-template-overview.md). I den **policy_overrides** objekt genom att ange den **can_persist** egenskap **SANT** (standard är FALSKT). 

Följande kodexempel använder .NET för att aktivera **offline** läge för Widevine-licenser. Koden är baserad på den [ med PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) exemplet. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android spelaren för offline uppspelning

Det enklaste sättet att utveckla en spelaren app för Android-enheter är att använda den [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), en öppen källkod videospelaren SDK. ExoPlayer stöder funktioner som stöds för närvarande inte av Androids inbyggt Media Player-API, inklusive MPEG-DASH och Smooth Streaming i Microsoft leveransprotokoll.

ExoPlayer version 2.6 och senare innehåller många klasser som har stöd för offline Widevine DRM-uppspelning. I synnerhet innehåller OfflineLicenseHelper-klassen hjälpfunktioner för att underlätta användningen av DefaultDrmSessionManager för att hämta, förnya och lanserar offline licenser. De klasser som anges i SDK-mappen ”bibliotek/kärnor/src/main/java/com/google/android/exoplayer2/offline /” stöd för offline video innehåll hämtar.

Följande lista över klasser underlätta offline-läge i ExoPlayer SDK för Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Utvecklare ska referera till den [ExoPlayer Utvecklarguide](https://google.github.io/ExoPlayer/guide.html) och motsvarande [utvecklarblogg](https://medium.com/google-exoplayer) under utvecklingen av ett program. Google har inte publicerat en fullständigt dokumenterad referens implementering eller exempel-kod för appen ExoPlayer stöder Widevine offline för tillfället så att informationen är begränsad till guide för utvecklare och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definieras i [Widevine-licensmall](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, och **license_duration_seconds**. Du kan också ange dem till noll, vilket innebär att oändlig/obegränsad giltighetstid.  

Värdena måste anges för att undvika ett heltalsspill har korrigerats. Förklaring om problemet finns i https://github.com/google/ExoPlayer/issues/3150 och https://github.com/google/ExoPlayer/issues/3112. <br/>Om du inte har angett värdena explicit, mycket stora värden för **PlaybackDurationRemaining** och **LicenseDurationRemaining** ska tilldelas (till exempel 9223372036854775807, högsta positivt värde för ett 64-bitars heltal). Därför Widevine-licens visas har upphört att gälla och därför dekrypteringen sker inte. 

Det här problemet uppstår inte på Android 5.0 Lollipop eller senare eftersom Android 5.0 är den första Android-versionen, vilket har utformats för att ge fullt stöd ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) och 64-bitars plattformar, medan Android 4.4 KitKat var ursprungligen har utformats för att stödja ARMv7 och 32-bitars plattformar som med andra äldre Android-versioner.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Med Xamarin för att skapa en app för Android uppspelning

Du kan hitta Xamarin-bindningar för ExoPlayer via följande länkar:

- [Xamarin-bindningar klientbibliotek för Google ExoPlayer-bibliotek](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-bindningar för ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Se även följande tråd: [Xamarin-bindning](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome player-appar för Android

Från och med lanseringen av [Chrome för Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), beständig licens i EME stöds. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) stöds nu även i Chrome för Android. Detta kan du skapa program offline uppspelning i Chrome om dina slutanvändare har detta (eller högre) version av Chrome. 

Dessutom har Google genereras ett exempel på progressiv Web App (PWA) och öppen källkod den: 

- [Källkod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google finns version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar bara i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar mobila webbläsaren Chrome till v62 (eller högre) på en Android-telefon och testa ovanstående värd-exempelapp, ser du att både online strömmande och offline uppspelningen fungerar.

Appen PWA ovan öppen källkod har skrivits i Node.js. Om du vill ha en egen version på en Ubuntu-server, Tänk på följande vanliga påträffades problem som kan förhindra att spela upp:

1. CORS-problem: Exemplet video i exempelappen finns i https://storage.googleapis.com/biograf-video-files/videos/. Google har ställt in CORS för alla sina test-exempel finns i Google Cloud Storage bucket. De hanteras med CORS-huvuden, att uttryckligen ange CORS-post: https://biograf-155113.appspot.com (domän i vilken google är värd för sin exemplet) att förhindra åtkomst av andra platser. Om du, visas följande HTTP-fel: Det gick inte att läsa in https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: ingen ”Access-Control-Allow-Origin”-rubrik finns på den begärda resursen. Ursprung ”https://13.85.80.81:8080” är därför inte tillåten åtkomst. Om ett täckande svar har dina behov, Ställ in på begäran för 'Nej cors' att hämta resursen med CORS inaktiverad.
2. Problemet med certifikatet: EME för Widevine kräver från Chrome v 58 HTTPS. Du måste därför vara värd för exempelappen över HTTPS med ett X509 certifikat. Ett vanligt testcertifikat fungerar inte på grund av följande krav: Du måste skaffa ett certifikat som uppfyller minimikraven för följande:
    - Chrome och Firefox kräver SAN det alternativa ämnesnamnet inställningen finns i certifikatet
    - Certifikatet måste ha betrott CA och ett självsignerat utvecklingscertifikat fungerar inte
    - Certifikatet måste ha ett Certifikatnamn matchar DNS-namnet på webbservern eller gateway

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="question"></a>Fråga

Hur kan jag ge beständiga licenser (offline-aktiverat) för vissa klienter/användare och icke-beständiga licenser (offline inaktiverad) för andra? Måste jag att duplicera innehållet och använda separata innehållsnyckeln?

### <a name="answer"></a>Svar
Du behöver inte duplicera innehållet. Du kan bara använda en enda kopia av innehållet och en enda ContentKeyAuthorizationPolicy, men två separata Contentkeyauthorizationpolicyoption's:

1. IContentKeyAuthorizationPolicyOption 1: använder beständiga licens och ContentKeyAuthorizationPolicyRestriction 1 som innehåller ett anspråk, till exempel license_type = ”beständiga”
2. IContentKeyAuthorizationPolicyOption 2: använder icke-beständiga licens och ContentKeyAuthorizationPolicyRestriction 2 som innehåller ett anspråk, till exempel license_type = ”Nonpersistent”

Det här sättet när en licensbegäran kommer från klientappen, från licensbegäran finns det ingen skillnad. Dock ska olika användare/enhet, STS ha affärslogik att utfärda olika JWT-token som innehåller olika anspråk (ett av de ovanstående två license_type). Värdet för anspråk i JWT-token som används för att bestämma av Licenstjänsten utfärdar du vilken typ av licens: permanenta eller icke-beständiga.

Det innebär att den säkra säkerhetstokentjänst (STS) måste ha företagsinformation för logik och klientenheten/att lägga till motsvarande anspråkets värde i en token.

### <a name="question"></a>Fråga

För Widevine säkerhetsnivåer, i Googles [översikt över Widevine DRM-arkitekturen doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentation, den definierar tre olika säkerhetsnivåer. Men i [Azure Media Services-dokumentation om licensmallen](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), beskrivs fem olika säkerhetsnivåer. Vad är relationen eller mappningen mellan två olika uppsättningar av säkerhetsnivåer?

### <a name="answer"></a>Svar

I Googles [översikt över Widevine DRM-arkitekturen](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), den definierar följande tre säkerhetsnivåer:

1.  Säkerhetsnivå 1: Alla innehållet bearbetas, kryptering och kontrollen utförs inom betrodda körning miljö (TEE). I vissa implementering modeller kan security bearbetningen utföras i olika kretsar.
2.  Säkerhetsnivå 2: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetats med separata video maskinvara eller programvara. På nivå 2 kan dock kryptografiska information fortfarande bearbetas endast inom TEE.
3.  På säkerhetsnivå 3 har inte en TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografiska information och krypterade innehållet på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk maskinvara-motor, men det endast förbättrar prestanda, inte säkerhet.

Samtidig, i [Azure Media Services-dokumentation om licensmallen](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), egenskapen security_level för content_key_specs kan ha följande fem olika värden (stabilitet klientkrav för uppspelning):

1.  Programvarubaserad whitebox crypto krävs.
2.  Programvara crypto och en dold avkodare krävs.
3.  Nyckelmaterial och krypto åtgärder måste utföras inom en maskinvara backas upp TEE.
4.  Kryptering och avkodning av innehåll måste utföras inom en maskinvara backas upp TEE.
5.  Den krypto avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvara backas upp TEE.

Båda säkerhetsnivåer definieras av Google Widevine. Skillnaden finns i dess förbrukningsnivå: arkitektur eller API-nivå. Fem säkerhetsnivåerna används i API: et för Widevine. Content_key_specs-objekt som innehåller security_level avserialiseras och skickas till tjänsten Widevine global leverans av Azure Media Services Widevine-Licenstjänst. Tabellen nedan visar mappningen mellan de två uppsättningarna med säkerhetsnivåer.

| **Säkerhetsnivåer som definierats i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Skyddsnivå 1**: Alla innehållet bearbetas, kryptering och kontrollen utförs inom betrodda körning miljö (TEE). I vissa implementering modeller kan security bearbetningen utföras i olika kretsar.|**security_level=5**: Den krypto avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvara backas upp TEE.<br/><br/>**security_level = 4**: Kryptering och avkodning av innehåll måste utföras inom en maskinvara backas upp TEE.|
**Skyddsnivå 2**: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetats med separata video maskinvara eller programvara. På nivå 2 kan dock kryptografiska information fortfarande bearbetas endast inom TEE.| **security_level=3**: Nyckelmaterial och krypto åtgärder måste utföras inom en maskinvara backas upp TEE. |
| **Skyddsnivå 3**: Har inte en TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografiska information och krypterade innehållet på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk maskinvara-motor, men det endast förbättrar prestanda, inte säkerhet. | **security_level=2**: Programvara crypto och en dold avkodare krävs.<br/><br/>**security_level = 1**: Programvarubaserad whitebox crypto krävs.|

### <a name="question"></a>Fråga

Varför nedladdning av innehåll tar så lång tid?

### <a name="answer"></a>Svar

Det finns två sätt att förbättra nedladdningshastighet:

1.  Aktivera CDN så att slutanvändarna är mer troligt att träffa CDN i stället för ursprung /-slutpunkt för direktuppspelning för nedladdning av innehåll. Om användaren når slutpunkten för direktuppspelning, är varje HLS segment eller DASH fragment dynamiskt paketeras och krypterad. Även om den här fördröjningen i millisekunder skala för varje segment /-fragment, när du har en timme lång video, kan ackumulerade svarstiden vara stora orsakar längre nedladdning.
2.  Ge slutanvändarna kan hämta selektivt videokvalitet lager och ljudspår i stället för allt innehåll. För offline-läge finns det ingen att hämta alla lager kvalitet. Det finns två sätt att göra detta:
    1.  Klienten kontrolleras: player appen automatiskt markeras eller användaren väljer videokvalitet lager och ljudspår att ladda ned;
    2.  Tjänsten kontrolleras: en kan använda dynamiska Manifest-funktionen i Azure Media Services för att skapa ett filter som begränsar HLS spellistan eller DASH MPD till ett enda videokvalitet lager och valt ljudspår (global). Sedan innehåller nedladdnings-URL som visas för slutanvändare det här filtret.

## <a name="summary"></a>Sammanfattning

Den här artikeln beskrivs hur du implementerar offlineläge uppspelning för DASH-innehåll som skyddas av Widevine på Android-enheter.  Den besvaras också några vanliga frågor som rör offline strömning av Widevine-skyddat innehåll.
