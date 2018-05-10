---
title: Konfigurera ditt konto för offline-direktuppspelning av Widevine skyddat innehåll – Azure
description: Det här avsnittet visar hur du konfigurerar Azure Media Services-kontot för offline strömning av Widevine skyddat innehåll.
services: media-services
keywords: TANKSTRECK, DRM, Widevine Offline-läge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine strömning för Android

Utöver att skydda innehåll för online strömmande media innehåll prenumeration och uthyrning services erbjudande nedladdningsbara innehåll som fungerar när du inte är ansluten till internet. Du kan behöva hämta innehållet på din telefon eller surfplatta för uppspelning i Flygplansläge när för frånkopplad från nätverket. Fler scenarier, som du kanske vill ladda ned innehåll:

- Vissa innehållsleverantörer får inte tillåta DRM-licensleverans utanför ett land kantlinje. Om en användare vill titta på innehållet resande utomlands behövs offline download.
- I vissa länder är Internet tillgänglighet och/eller bandbredd begränsad. Användare kan välja att ladda ned innehåll för att kunna titta i tillräckligt hög upplösning för tillfredsställande bild.

Den här artikeln beskrivs hur du implementerar uppspelning av offline-läge för DASH-innehåll som skyddas av Widevine på Android-enheter. Offline DRM kan du ange prenumeration, uthyrning och inköp modeller för innehåll, aktivera kunder tjänster ska kunna innehåll med dem när frånkopplad från internet.

För att skapa appar för Android player beskriver vi tre alternativ:

> [!div class="checklist"]
> * Skapa en spelare med Java API för ExoPlayer SDK
> * Skapa en spelare använder Xamarin bindning av ExoPlayer SDK
> * Skapa en Windows Media player krypterade Media tillägg (EME) och Media källa tillägg (mus) i Chrome mobila webbläsare v62 eller senare

Vanliga frågor rör offline strömning av Widevine skyddat innehåll besvaras också.

## <a name="requirements"></a>Krav 

Innan du implementerar offline DRM för Widevine på Android-enheter, bör du först:

- Bekanta dig med de olika begreppen för online-innehåll skydd med Widevine DRM. Detta beskrivs i detalj i följande dokument/prov:
    - [Använd Azure Media Services för att leverera DRM-licenser eller AES-nycklar](media-services-deliver-keys-and-licenses.md)
    - [CENC med Multi-DRM och Access Control: En referensdesign och implementering i Azure och Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Använda PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Använda Azure Media Services för att leverera PlayReady och/eller Widevine-licenser med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Bekanta dig med Google ExoPlayer SDK för Android, en öppen källkod videospelare SDK kan stödja offline Widevine DRM uppspelning. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Utvecklarhandbok för ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer blogg](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Innehållsskydd konfigurationen i Azure Media Services

När du konfigurerar Widevine skydd av en tillgång i Media Services kan behöva du skapa ContentKeyAuthorizationPolicyOption som anges i följande tre saker:

1. DRM-systemet (Widevine)
2. Anger hur viktiga innehållsleverans ContentKeyAuthorizationPolicyRestriction är auktoriserad i licensleveranstjänst (öppen eller token auktorisering)
3. Mallen för DRM (Widevine)

Så här aktiverar du **offline** läge för Widevine-licenser måste du konfigurera [Widevine-licensmall](media-services-widevine-license-template-overview.md). I den **policy_overrides** objekt, ange den **can_persist** egenskapen **SANT** (standard är FALSKT). 

Följande kodexempel används .NET för att aktivera **offline** läge för Widevine-licenser. Koden är baserad på den [ med PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) exempel. 

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

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android player för uppspelning av offline

Det enklaste sättet att utveckla en inbyggd player-app för Android-enheter är att använda den [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), en öppen källkod videospelare SDK. ExoPlayer stöder funktioner som stöds för närvarande inte av Androids interna MediaPlayer-API, inklusive MPEG-DASH och Microsoft Smooth Streaming leverans-protokoll.

ExoPlayer version 2.6 och senare innehåller många klasser som stöder offline Widevine DRM uppspelning. I synnerhet innehåller OfflineLicenseHelper-klassen funktioner för verktyg för att underlätta användning av DefaultDrmSessionManager för att hämta, förnya och släppa offline licenser. De klasser som finns i SDK-mappen ”bibliotek/core/src/main/java/com/google/android/exoplayer2/offline /” stöder offline video innehåll hämtas.

Följande lista över klasser underlätta offline-läge i ExoPlayer SDK för Android:

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

Utvecklare ska referera den [ExoPlayer Utvecklarhandbok](https://google.github.io/ExoPlayer/guide.html) och motsvarande [bloggen för utvecklare](https://medium.com/google-exoplayer) under utvecklingen av ett program. Google har inte släppt en fullständigt dokumenterade referens implementering eller exempel kod för appen ExoPlayer stöder Widevine offline just nu, så att informationen är begränsad till guide för utvecklare och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definieras i [Widevine-licensmall](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, och **license_duration_seconds**. Alternativt kan du ställa dem till noll, vilket innebär oändlig obegränsad giltighetstid.  

Värden måste anges för att undvika ett heltal spill programfel. Mer information om problemet finns i https://github.com/google/ExoPlayer/issues/3150 och https://github.com/google/ExoPlayer/issues/3112. <br/>Om du inte anger värdena explicit, mycket stora värden för **PlaybackDurationRemaining** och **LicenseDurationRemaining** ska tilldelas (till exempel 9223372036854775807, vilket är högsta positivt värde för ett 64-bitars heltal). Därför Widevine-licens visas har upphört att gälla och därför dekrypteringen sker inte. 

Det här problemet uppstår inte på Android 5.0 Lollipop eller senare eftersom Android 5.0 är den första Android versionen som har utformats för att ge fullt stöd ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) och 64-bitars plattformar, medan Android 4.4 KitKat ursprungligen för att stödja ARMv7 och 32-bitars plattformar som med andra äldre versioner av Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Använder Xamarin för att bygga en uppspelning av Android-app

Du kan hitta Xamarin bindningar för ExoPlayer med hjälp av följande länkar:

- [Xamarin bindningar bibliotek för Google ExoPlayer-biblioteket](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin bindningar för ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Se även följande tråden: [Xamarin bindning](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome player-appar för Android

Från och med lanseringen av [Chrome för Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), beständig licens i EME stöds. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) stöds nu även i Chrome för Android. Detta kan du skapa offline uppspelning program i Chrome om användarna har denna (eller högre) version av Chrome. 

Dessutom har Google genereras ett exempel på en progressiv Web App (PWA) och öppna-källkod den: 

- [Källkoden](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google finns version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar endast i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar mobila webbläsaren Chrome till v62 (eller högre) på en Android-telefon och testa ovanstående finns exempelapp ser du att både online strömmande och offline uppspelningen fungerar.

Ovanstående öppen källkod PWA appen har skapats i Node.js. Om du vill ha en egen version på en Ubuntu server tänka på följande vanliga påträffade problem som kan förhindra uppspelning:

1. CORS problemet: exempel video i exempelappen finns i https://storage.googleapis.com/biograf-video-files/videos/. Google har ställt in CORS för alla sina test prover finns i Google Molnlagring bucket. De hanteras med CORS sidhuvud, att explicit ange CORS-post: https://biograf-155113.appspot.com (domän i vilken google värd sina exempel) förhindra åtkomst av andra platser. Om du försöker, visas följande HTTP-fel: Det gick inte att läsa in https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: inget 'Access Control-Tillåt-ursprung'-huvud är installerat på den begärda resursen. Ursprung 'https://13.85.80.81:8080' är därför inte tillåten åtkomst. Om ett täckande svar har dina behov, Ställ in en begäran för 'Nej-cors' för att hämta resursen med CORS inaktiverad.
2. Utfärdande av certifikat: från Chrome v 58 EME för Widevine kräver HTTPS. Du måste därför vara värd för exempelappen via HTTPS med X509 certifikat. Ett vanligt testcertifikat fungerar inte på grund av följande krav: du måste skaffa ett certifikat som uppfyller följande minimikrav:
    - Chrome och Firefox kräver SAN det alternativa namnet inställningen finns i certifikatet
    - Certifikatet måste ha betrodda Certifikatutfärdaren och av självsignerade utvecklingscertifikat fungerar inte
    - Certifikatet måste ha ett Certifikatnamn matchar DNS-namnet på webbservern eller gateway

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="question"></a>Fråga

Hur kan jag ge beständiga licenser (offline-aktiverat) för vissa klienter/användare och icke-beständig licenser (offline inaktiverad) för andra? Behöver duplicera innehållet och använda separata innehållsnyckeln?

### <a name="answer"></a>Svar
Du behöver inte kopiera innehållet. Du kan bara använda en enda kopia av innehållet och en enda ContentKeyAuthorizationPolicy, men två separata Contentkeyauthorizationpolicyoption's:

1. IContentKeyAuthorizationPolicyOption 1: använder beständiga licens och ContentKeyAuthorizationPolicyRestriction 1 som innehåller ett anspråk som license_type = ”Persistent”
2. IContentKeyAuthorizationPolicyOption 2: använder icke-beständig licens och ContentKeyAuthorizationPolicyRestriction 2 som innehåller ett anspråk som license_type = ”Nonpersistent”

Det här sättet när en licensbegäran kommer från klienten-app från licensbegäran finns det ingen skillnad. Dock har olika användare/enhet, STS affärslogik till olika JWT-token som innehåller olika anspråk (ett av de ovanstående två license_type). Värdet för anspråk i JWT-token används för att besluta om av Licenstjänsten vilken typ av licens: beständig eller icke-beständig.

Det innebär att den säkra säkerhetstokentjänst (STS) måste ha den logik och klientenheten/företagsinformation att lägga till motsvarande anspråkets värde i en token.

### <a name="question"></a>Fråga

För Widevine säkerhetsnivåer i Googles [Widevine DRM arkitektur, översikt doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentation, definierar tre olika säkerhetsnivåer. Men i [Azure Media Services-dokumentationen på Widevine-licensmall](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), beskrivs fem olika säkerhetsnivåer. Vad är relationen eller mappningen mellan två olika uppsättningar av säkerhetsnivåer?

### <a name="answer"></a>Svar

I Google [Widevine DRM arkitektur, översikt](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definierar följande tre säkerhetsnivåerna:

1.  Säkerhetsnivå 1: Alla innehåll bearbetning, kryptering och kontroll utförs inom betrodda Execution Environment (TEE). I vissa implementering modeller kan säkerhet bearbetning utföras i olika modulerna.
2.  Säkerhetsnivå 2: Utför kryptografi (men inte video bearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat video maskinvara eller programvara. På nivå 2, men bearbetas Krypteringsinformation fortfarande endast inom TEE.
3.  Nivå 3 har inte en TEE på enheten. Lämpliga åtgärder vidtas för att skydda kryptografiska information och dekryptera innehåll på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk motor för maskinvara, men som bara ger högre prestanda, inte säkerhet.

Samtidig, i [Azure Media Services-dokumentationen på Widevine-licensmall](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), egenskapen security_level för content_key_specs kan ha följande fem olika värden (stabilitet klientkrav för uppspelning):

1.  Programvarubaserad whitebox crypto krävs.
2.  Programvara crypto och en dolda avkodare krävs.
3.  Nyckelmaterial och krypto-åtgärder måste utföras inom en maskinvara säkerhetskopieras TEE.
4.  Kryptografi och avkodning av innehåll måste utföras inom en maskinvara säkerhetskopieras TEE.
5.  Den kryptografi avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras i en maskinvara säkerhetskopieras TEE.

Båda säkerhetsnivåer definieras av Google Widevines. Skillnaden är i sin användning nivå: arkitektur eller API-nivå. Fem säkerhetsnivåerna används i Widevine-API. Content_key_specs-objekt som innehåller security_level avserialiseras och skickas till tjänsten Widevine globala leverans av tjänsten för Azure Media Services Widevine-licens. Tabellen nedan visar mappningen mellan de två olika säkerhetsnivåer.

| **Säkerhetsnivåer som definierats i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Skyddsnivå 1**: alla innehåll bearbetning, kryptering och kontroll utförs inom betrodda Execution Environment (TEE). I vissa implementering modeller kan säkerhet bearbetning utföras i olika modulerna.|**security_level = 5**: den kryptografi avkodning och alla hantering av media (komprimerade och okomprimerade) måste hanteras i en maskinvara säkerhetskopieras TEE.<br/><br/>**security_level = 4**: kryptografi och avkodning av innehåll måste utföras inom en maskinvara säkerhetskopieras TEE.|
**Nivå 2**: Utför kryptografi (men inte video bearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat video maskinvara eller programvara. På nivå 2, men bearbetas Krypteringsinformation fortfarande endast inom TEE.| **security_level = 3**: nyckelmaterial och krypto-åtgärder måste utföras inom en maskinvara säkerhetskopieras TEE. |
| **Skyddsnivå 3**: har inte en TEE på enheten. Lämpliga åtgärder vidtas för att skydda kryptografiska information och dekryptera innehåll på värdens operativsystem. En nivå 3-implementering kan även innehålla en kryptografisk motor för maskinvara, men som bara ger högre prestanda, inte säkerhet. | **security_level = 2**: programvara crypto och en dolda avkodare krävs.<br/><br/>**security_level = 1**: programvarubaserad whitebox crypto krävs.|

### <a name="question"></a>Fråga

Varför nedladdning av innehåll tar så lång tid?

### <a name="answer"></a>Svar

Det finns två sätt att hämta snabbare:

1.  Aktivera CDN så att slutanvändarna är mer troligt att träffa CDN i stället för ursprung/strömmande slutpunkt för nedladdning av innehåll. Om användaren träffar strömmande slutpunkt, varje HLS segment eller TANKSTRECK fragment dynamiskt paketeras och krypterad. Även om den här fördröjningen i millisekunder skala för varje segment/fragment, när du har en lång video timme, kan ackumulerade svarstiden vara stora orsakar längre hämtning.
2.  Ge slutanvändare möjlighet att hämta selektivt bildkvaliteten lager och ljud spårar i stället för allt innehåll. Det finns ingen anledning att hämta alla kvalitet lager för för offline-läge. Det finns två sätt att åstadkomma detta:
    1.  Klienten kontrolleras: player appen automatiskt väljer eller användare väljer bildkvaliteten lager och ljud spårar för att hämta;
    2.  Tjänsten kontrolleras: en kan använda dynamiska Manifest funktionen i Azure Media Services för att skapa ett filter som begränsar HLS spelningslista eller TANKSTRECK MPD till ett enda bildkvaliteten lager och markerade ljud spår (global). Nedladdnings-URL som visas för slutanvändare innehåller det här filtret.

## <a name="summary"></a>Sammanfattning

Den här artikeln beskrivs hur du implementerar uppspelning av offline-läge för DASH-innehåll som skyddas av Widevine på Android-enheter.  Också svarade vanliga frågor rör offline strömning av Widevine skyddat innehåll.
