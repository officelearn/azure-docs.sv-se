---
title: Konfigurera ditt konto för offline strömning av Widevine-skyddat innehåll – Azure
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services-konto för offline strömning av Widevine-skyddat innehåll.
services: media-services
keywords: DASH, DRM, Widevine Offline-läge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 18c83717e761f22363ccc69c827f5e383f8a9e85
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54122350"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine för direktuppspelning för Android

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

## <a name="prerequisites"></a>Förutsättningar 

Innan du implementerar offline DRM för Widevine på Android-enheter, bör du först:

- Bekanta dig med de olika begreppen för online innehållsskydd med Widevine DRM. Detta beskrivs i detalj i följande dokument/samples:
    - [Designen av ett system med multi-DRM innehållsskydd med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
    - [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
- Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Du behöver ändra koden i [kryptera med DRM med hjälp av .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) att lägga till Widevine-konfigurationer.  
- Bekanta dig med Google ExoPlayer SDK för Android, en öppen källkod videospelaren SDK kan stödja offline Widevine DRM-uppspelning. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer Utvecklarguide](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer-bloggen](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehållsskydd i Azure Media Services

I den [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metoden följande nödvändiga steg finns:

1. Ange hur innehåll viktiga leverans har behörighet i av licensleveranstjänst: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Konfigurera Widevine-licensmall:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Skapa ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Aktivera offline-läge

Aktivera **offline** läge för Widevine-licenser, måste du konfigurera [Widevine-licensmall](widevine-license-template-overview.md). I den **policy_overrides** objekt genom att ange den **can_persist** egenskap **SANT** (standard är FALSKT), som visas i [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android spelaren för offline uppspelning

Det enklaste sättet att utveckla en spelaren app för Android-enheter är att använda den [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), en öppen källkod videospelaren SDK. ExoPlayer stöder funktioner som stöds för närvarande inte av Androids inbyggt Media Player-API, inklusive MPEG-DASH och Smooth Streaming i Microsoft leveransprotokoll.

ExoPlayer version 2.6 och senare innehåller många klasser som har stöd för offline Widevine DRM-uppspelning. I synnerhet innehåller OfflineLicenseHelper-klassen hjälpfunktioner för att underlätta användningen av DefaultDrmSessionManager för att hämta, förnya och lanserar offline licenser. De klasser som anges i SDK-mappen ”bibliotek/kärnor/src/main/java/com/google/android/exoplayer2/offline /” stöd för offline video innehåll hämtar.

Följande lista över klasser underlättar offline-läge i ExoPlayer SDK för Android:

- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper.Java  
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession.Java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager.Java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DrmSession.Java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession.Java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader.Java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper.Java 
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/Downloader.Java
- Library/dash/src/main/Java/com/Google/Android/exoplayer2/Source/dash/offline/DashDownloader.Java 

Utvecklare ska referera till den [ExoPlayer Utvecklarguide](https://google.github.io/ExoPlayer/guide.html) och motsvarande [utvecklarblogg](https://medium.com/google-exoplayer) under utvecklingen av ett program. Google har inte publicerat en fullständigt dokumenterad referens implementering eller exempel-kod för appen ExoPlayer stöder Widevine offline för tillfället så att informationen är begränsad till utvecklarna guide och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definieras i [Widevine-licensmall](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, och **license_duration_seconds**. Du kan också ange dem till noll, vilket innebär att oändlig/obegränsad giltighetstid.  

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
Eftersom en tillgång så att du har flera StreamingLocators tillåter att Media Services v3. Du kan ha

1.  En ContentKeyPolicy med license_type = ”permanent”, ContentKeyPolicyRestriction med anspråk på ”beständiga” och dess StreamingLocator;
2.  En annan ContentKeyPolicy med license_type = ”ickebeständig” ContentKeyPolicyRestriction med anspråk på ”ickebeständig” och dess StreamingLocator.
3.  Två StreamingLocators har olika ContentKey.

Beroende på affärslogiken i anpassade STS utfärdas olika anspråk i JWT-token. Med denna token får endast motsvarande licens kan hämtas och endast motsvarande URL: en kan spelas upp.

### <a name="question"></a>Fråga

För Widevine säkerhetsnivåer, i Googles [översikt över Widevine DRM-arkitekturen doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentation, den definierar tre olika säkerhetsnivåer. Men i [Azure Media Services-dokumentation om licensmallen](widevine-license-template-overview.md), beskrivs fem olika säkerhetsnivåer. Vad är relationen eller mappningen mellan två olika uppsättningar av säkerhetsnivåer?

### <a name="answer"></a>Svar

I Googles [översikt över Widevine DRM-arkitekturen](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), den definierar följande tre säkerhetsnivåer:

1.  Säkerhetsnivå 1: Alla innehållet bearbetas, kryptering och kontrollen utförs inom betrodda körning miljö (TEE). I vissa implementering modeller kan security bearbetningen utföras i olika kretsar.
2.  Säkerhetsnivå 2: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetats med separata video maskinvara eller programvara. På nivå 2 kan dock kryptografiska information fortfarande bearbetas endast inom TEE.
3.  På säkerhetsnivå 3 har inte en TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografiska information och krypterade innehållet på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk maskinvara-motor, men det endast förbättrar prestanda, inte säkerhet.

Samtidig, i [Azure Media Services-dokumentation om licensmallen](widevine-license-template-overview.md), egenskapen security_level för content_key_specs kan ha följande fem olika värden (stabilitet klientkrav för uppspelning):

1.  Programvarubaserad whitebox crypto krävs.
2.  Programvara crypto och en dold avkodare krävs.
3.  Nyckelmaterial och krypto åtgärder måste utföras inom en maskinvara backas upp TEE.
4.  Kryptering och avkodning av innehåll måste utföras inom en maskinvara backas upp TEE.
5.  Den krypto avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvara backas upp TEE.

Båda säkerhetsnivåer definieras av Google Widevine. Skillnaden finns i dess förbrukningsnivå: arkitektur eller API-nivå. Fem säkerhetsnivåerna används i API: et för Widevine. Content_key_specs-objekt som innehåller security_level avserialiseras och skickas till tjänsten Widevine global leverans av Azure Media Services Widevine-Licenstjänst. Tabellen nedan visar mappningen mellan de två uppsättningarna med säkerhetsnivåer.

| **Säkerhetsnivåer som definierats i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Skyddsnivå 1**: Alla innehållet bearbetas, kryptering och kontrollen utförs inom betrodda körning miljö (TEE). I vissa implementering modeller kan security bearbetningen utföras i olika kretsar.|**security_level = 5**: Den krypto avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras inom en maskinvara backas upp TEE.<br/><br/>**security_level = 4**: Kryptering och avkodning av innehåll måste utföras inom en maskinvara backas upp TEE.|
**Skyddsnivå 2**: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetats med separata video maskinvara eller programvara. På nivå 2 kan dock kryptografiska information fortfarande bearbetas endast inom TEE.| **security_level = 3**: Nyckelmaterial och krypto åtgärder måste utföras inom en maskinvara backas upp TEE. |
| **Skyddsnivå 3**: Har inte en TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografiska information och krypterade innehållet på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk maskinvara-motor, men det endast förbättrar prestanda, inte säkerhet. | **security_level = 2**: Programvara crypto och en dold avkodare måste anges.<br/><br/>**security_level = 1**: Programvarubaserad whitebox crypto krävs.|

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
