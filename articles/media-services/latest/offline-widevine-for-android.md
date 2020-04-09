---
title: Stream Widevine Android Offline med Azure Media Services v3
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services-konto för offlinestreaming av Widevine-skyddat innehåll.
services: media-services
keywords: DASH, DRM, Widevine offlineläge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2020
ms.author: willzhan
ms.openlocfilehash: 94edec8261d9916b7575fb247e1698273f244130
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887205"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streaming för Android med Media Services v3

Förutom att skydda innehåll för onlinestreaming erbjuder prenumerationer på medieinnehåll och uthyrningstjänster nedladdningsbart innehåll som fungerar när du inte är ansluten till internet. Du kan behöva hämta innehåll till telefonen eller surfplattan för uppspelning i flygplansläge när du flyger frånkopplad från nätverket. Ytterligare scenarier där du kanske vill hämta innehåll:

- Vissa innehållsleverantörer kan inte tillåta leverans av DRM-licenser utanför ett lands/regions gräns. Om en användare vill titta på innehåll när du reser utomlands behövs nedladdning offline.
- I vissa länder/regioner är Internettillgängligheten och/eller bandbredden begränsad. Användare kan välja att ladda ner innehåll för att kunna titta på det i tillräckligt hög upplösning för tillfredsställande tittarupplevelse.

I den här artikeln beskrivs hur du implementerar uppspelning av offlineläge för DASH-innehåll som skyddas av Widevine på Android-enheter. Offline DRM kan du tillhandahålla prenumerations-, uthyrnings- och inköpsmodeller för ditt innehåll, vilket gör det möjligt för kunder i dina tjänster att enkelt ta med sig innehåll när de kopplas bort från internet.

För att bygga Android-spelarens appar beskriver vi tre alternativ:

> [!div class="checklist"]
> * Skapa en spelare med Hjälp av Java API för ExoPlayer SDK
> * Bygg en spelare med Xamarin-bindning av ExoPlayer SDK
> * Skapa en spelare med EME (Encrypted Media Extension) och MSE (Media Source Extension) i webbläsaren Chrome mobile v62 eller senare

Artikeln besvarar också några vanliga frågor relaterade till offline streaming av Widevine skyddat innehåll.

> [!NOTE]
> Offline DRM faktureras endast för att göra en enda begäran om en licens när du hämtar innehållet. Eventuella fel faktureras inte.

## <a name="prerequisites"></a>Krav 

Innan du implementerar offline DRM för Widevine på Android-enheter bör du först:

- Bekanta dig med de koncept som introduceras för innehållsskydd online med Widevine DRM. Detta behandlas i detalj i följande dokument/prover:
    - [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
    - [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
- Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Du måste ändra koden i [Kryptera med DRM med .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) för att lägga till Widevine-konfigurationer.  
- Bekanta dig med Google ExoPlayer SDK för Android, en videospelare med öppen källkod SDK som kan stödja offline Widevine DRM-uppspelning. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Utvecklare Blogg](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehållsskydd i Azure Media Services

I metoden [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) finns följande nödvändiga steg:

1. Ange hur leverans av innehållsnyckel är auktoriserad i licensleveranstjänsten: 

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
2. Konfigurera Widevine-licensmallen:  

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

## <a name="enable-offline-mode"></a>Aktivera offlineläge

Om du vill aktivera **offlineläge** för Widevine-licenser måste du konfigurera [Widevine-licensmallen](widevine-license-template-overview.md). I **policy_overrides-objektet** anger du **egenskapen can_persist** till **true** (standard är false), som visas i [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android-spelaren för offlineuppspelning

Det enklaste sättet att utveckla en inbyggd spelarapp för Android-enheter är att använda [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), en videospelare med öppen källkod SDK. ExoPlayer stöder funktioner som för närvarande inte stöds av Androids inbyggda MediaPlayer-API, inklusive MPEG-DASH och Microsoft Smooth Streaming-leveransprotokoll.

ExoPlayer version 2.6 och högre innehåller många klasser som stöder offline Widevine DRM uppspelning. I synnerhet offlinelicenseHelper-klassen tillhandahåller verktygsfunktioner för att underlätta användningen av DefaultDrmSessionManager för nedladdning, förnyelse och publicering av offlinelicenser. Klasserna i SDK-mappen "library/core/src/main/java/com/google/android/exoplayer2/offline/" stöder nedladdning av videoinnehåll offline.

Följande lista över klasser underlättar offlineläge i ExoPlayer SDK för Android:

- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- bibliotek/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Utvecklare bör referera till [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) och motsvarande [Developer Blog](https://medium.com/google-exoplayer) under utvecklingen av ett program. Google har inte släppt en fullt dokumenterad referens genomförande eller exempelkod för ExoPlayer app stödja Widevine offline just nu, så informationen är begränsad till utvecklarens guide och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definierad i [Widevine-licensmallen:](widevine-license-template-overview.md) **rental_duration_seconds,** **playback_duration_seconds**och **license_duration_seconds**. Alternativt kan du ställa in dem till noll, vilket innebär oändlig / obegränsad varaktighet.  

Värdena måste ställas in för att undvika ett heltalsspillfel. Mer förklaring om problemet https://github.com/google/ExoPlayer/issues/3150 finns https://github.com/google/ExoPlayer/issues/3112i och . <br/>Om du inte uttryckligen anger värdena tilldelas mycket stora värden för **PlaybackDurationRemaining** och **LicenseDurationRemaining** (till exempel 9223372036854775807, vilket är det högsta positiva värdet för ett 64-bitars heltal). Som ett resultat verkar Widevine-licensen ha upphört att gälla och därför kommer dekrypteringen inte att ske. 

Det här problemet uppstår inte på Android 5.0 Lollipop eller senare eftersom Android 5.0 är den första Android-versionen, som har utformats för att fullt ut stödja ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) och 64-bitars plattformar, medan Android 4.4 KitKat ursprungligen utformades för att stödja ARMv7 och 32-bitars plattformar som med andra äldre Android-versioner.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Använda Xamarin för att skapa en Android-uppspelningsapp

Du kan hitta Xamarin bindningar för ExoPlayer med hjälp av följande länkar:

- [Xamarin binder bibliotek för Google ExoPlayer-biblioteket](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin bindningar för ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Se även följande tråd: [Xamarin bindning](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-appar för Android

Från och med lanseringen av [Chrome för Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)stöds beständig licens i EME. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) stöds nu även i Chrome för Android. På så sätt kan du skapa offlineuppspelningsprogram i Chrome om slutanvändarna har den här (eller högre) versionen av Chrome. 

Dessutom har Google producerat en Progressiv Web App (PWA) exempel och öppen källkod det: 

- [Källkod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google värd version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar bara i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar webbläsaren Chrome till v62 (eller högre) på en Android-telefon och testar exempelappen ovan värd ser du att både onlinestreaming och offlineuppspelning fungerar.

Pwa-appen ovan med öppen källkod skapas i Node.js. Om du vill vara värd för din egen version på en Ubuntu-server bör du tänka på följande vanliga problem som kan förhindra uppspelning:

1. CORS-problemet: Exempelvideon i exempelappen https://storage.googleapis.com/biograf-video-files/videos/finns i . Google har satt upp CORS för alla sina testprover som finns i Google Cloud Storage bucket. De serveras med CORS-huvuden och anger uttryckligen `https://biograf-155113.appspot.com` CORS-posten: (den domän där google är värd för deras exempel) som förhindrar åtkomst av andra webbplatser. Om du försöker visas följande HTTP-fel:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Certifikatproblem: Från Chrome v 58 kräver EME för Widevine HTTPS. Därför måste du vara värd för exempelappen via HTTPS med ett X509-certifikat. Ett vanligt testcertifikat fungerar inte på grund av följande krav: Du måste skaffa ett certifikat som uppfyller följande minimikrav:
    - Chrome och Firefox kräver att inställningen för alternativt namn för SAN-ämne finns i certifikatet
    - Certifikatet måste ha betrodd certifikatutfärdare och ett självsignerat utvecklingscertifikat fungerar inte
    - Certifikatet måste ha ett KN som matchar DNS-namnet på webbservern eller gatewayen

## <a name="faqs"></a>Vanliga frågor och svar

Mer information finns i [Vanliga frågor om Widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Ytterligare information

Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="summary"></a>Sammanfattning

I den här artikeln diskuterades hur du implementerar uppspelning av offlineläge för DASH-innehåll som skyddas av Widevine på Android-enheter.  Det besvarade också några vanliga frågor om offline streaming av Widevine skyddat innehåll.
