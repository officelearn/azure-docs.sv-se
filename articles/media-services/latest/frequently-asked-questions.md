---
title: Vanliga frågor och svar om Azure Media Services v3 | Microsoft Docs
description: Den här artikeln ger svar på Azure Media Services v3 vanliga frågor och svar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: c9da29ad288811bbed225fd906f2a7eb1fd9edf7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977734"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Vanliga frågor och svar om Media Services v3

Den här artikeln ger svar på Azure Media Services (AMS) v3 vanliga frågor och svar.

## <a name="general"></a>Allmänt

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Vilka Azure-roller kan utföra åtgärder på Azure Media Services resurser? 

Se [rollbaserad åtkomst kontroll (RBAC) för Media Services-konton](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Hur gör jag för att konfigurerar du reserverade enheter?

För ljudanalys- och videoanalysjobb som utlöses av Media Services v3 eller Video Indexer rekommenderar vi starkt att du etablerar ditt konto med 10 S3-MRU:er. Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

Mer information finns i [skala medie bearbetning med CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vad är den rekommenderade metoden för att bearbeta videor?

Använd [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) för att konfigurera vanliga aktiviteter för kodning eller analys av videor. Varje **transformering** beskriver ett recept, eller ett arbets flöde för uppgifter för bearbetning av video-eller ljudfiler. Ett [jobb](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran om Media Services att tillämpa **transformeringen** på ett angivet video-eller ljud innehåll. När transformeringen har skapats kan du skicka jobb med Media Services-API: er eller någon av de publicerade SDK: erna. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Hur fungerar sid brytning?

När du använder sid brytning bör du alltid använda nästa länk för att räkna upp samlingen och inte är beroende av en viss sid storlek. Mer information och exempel finns i [filtrering, sortering, växling](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Vilka funktioner är inte tillgängliga än i Azure Media Services v3?

Mer information finns i [funktions luckor med avseende på v2-API: er](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Vad är processen att flytta ett Media Services konto mellan prenumerationer?  

Mer information finns i [Flytta ett Media Services konto mellan prenumerationer](media-services-account-concept.md).

## <a name="live-streaming"></a>Liveuppspelning 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Så här infogar du brytningar/videor och bildskriver under Live Stream?

Media Services v3 Live encoding stöder ännu inte infogning av video-eller bildskärmar under Live Stream. 

Du kan använda en [aktiv lokal kodare](recommended-on-premises-live-encoders.md) för att växla mellan käll videon. Många appar ger möjlighet att växla källor, inklusive multistream-Wirecast, växlaren Studio (på iOS), ONLINEBANKSYSTEM Studio (gratis app) och många fler.

## <a name="content-protection"></a>Innehållsskydd

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Bör jag använda en AES-128-kryptering eller ett DRM-system?

Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den största skillnaden mellan de två systemen är att med AES-kryptering överförs innehålls nyckeln till klienten via TLS så att nyckeln krypteras under överföringen men utan ytterligare kryptering ("i klartext"). Det innebär att den nyckel som används för att dekryptera innehållet är tillgänglig för klient spelaren och kan visas i ett nätverks spår på klienten i klartext. En AES-128-kryptering är lämplig för användnings fall där visnings programmet är en betrodd part (till exempel kryptering av företags videor som distribueras i ett företag som kan ses av anställda).

DRM-system som PlayReady, Widevine och FairPlay innehåller en ytterligare krypterings nivå för den nyckel som används för att dekryptera innehållet jämfört med en AES-128-rensa-nyckel. Innehålls nyckeln krypteras till en nyckel som skyddas av DRM-körningen i tillägg till all kryptering på transport nivå som tillhandahålls av TLS. Dessutom hanteras dekrypteringen i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta säkerhetsnivån.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Så här visar du en video endast för användare som har en särskild behörighet, utan att använda Azure AD?

Du behöver inte använda någon speciell token-Provider (till exempel Azure AD). Du kan skapa en egen [JWT](https://jwt.io/) -Provider (så kallade STS, Secure token service) med hjälp av asymmetrisk nyckel kryptering. I din anpassade STS kan du lägga till anspråk baserat på din affärs logik.

Se till att utfärdaren, mål gruppen och alla anspråk matchar exakt mellan vad som är i JWT och ContentKeyPolicyRestriction som används i ContentKeyPolicy.

Mer information finns i [skydda ditt innehåll med hjälp av Media Services dynamisk kryptering](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hur och var du vill hämta JWT-token innan det att begäran licens eller nyckel?

1. För produktion måste du ha en säker token-tjänst (STS) (webb tjänst) som utfärdar JWT-token vid en HTTPS-begäran. För testning kan du använda koden som visas i **GetTokenAsync** metod som definieras i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player behöver du göra en begäran när en användare autentiseras till STS för sådana en token och tilldela den som värde för token. Du kan använda den [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Ett exempel för att köra STS, med symmetriska och asymmetrisk nyckel finns i [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Ett exempel på en spelare baserat på Azure Media Player med hjälp av sådana JWT-token finns i [ https://aka.ms/amtest ](https://aka.ms/amtest) (expandera ”player_settings”-länk om du vill se token indata).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hur du för att godkänna begäranden att strömma videor med AES-kryptering?

Det korrekta sättet är att använda STS (Secure Token Service):

I STS, beroende på användar profil, lägger du till olika anspråk (till exempel "Premium användare", "grundläggande användare", "kostnads fri utvärderings användare"). Användaren kan se olika innehållet med olika anspråk i en JWT. Naturligtvis för annat innehåll/tillgången har ContentKeyPolicyRestriction motsvarande RequiredClaims.

Använd Azure Media Services-API: er för att konfigurera licens-/nyckel leverans och kryptera dina till gångar (som visas i [det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Mer information finns här:

- [Översikt över innehållsskydd](content-protection-overview.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP eller HTTPS?
ASP.NET MVC player-program måste ha stöd för följande:

* Användarautentisering via Azure AD, som finns under HTTPS.
* JWT exchange mellan klienten och Azure AD, som är under HTTPS.
* DRM-licenser genom klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. Produktsvit PlayReady säkerhetsbehov inte HTTPS för leverans av licens. Om licensservern PlayReady är utanför Media Services kan använda du antingen HTTP eller HTTPS.

Player ASP.NET-programmet använder HTTPS som bästa praxis så Media Player är på en sida under HTTPS. HTTP är prioriterade för strömning, så du behöver tänka på problemet av blandat innehåll.

* Blandat innehåll tillåts inte i webbläsaren. Men Tillåt att av plugin-program som Silverlight och OSMF plugin-program för smooth och bindestreck. Blandat innehåll är en säkerhetsfunktion på grund av hotet att möjligheten att mata in skadliga JavaScript, vilket kan orsaka kunddata ska vara utsatt för risk. Webbläsare blockera den här funktionen som standard. Det är det enda sättet att åtgärda problemet på serversidan (ursprungliga) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte bra antingen.
* Undvik att blandat innehåll. Både player-program och Media Player bör använda HTTP eller HTTPS. När du spelar upp blandat innehåll kräver silverlightSS teknisk att du avmarkerar en varning om blandat innehåll. FlashSS teknisk hanterar blandat innehåll utan att en varning om blandat innehåll.
* Om slutpunkten för direktuppspelning har skapats före augusti 2014, stöd inte det för HTTPS. I det här fallet, skapa och använda en ny slutpunkt för direktuppspelning för HTTPS.

### <a name="what-about-live-streaming"></a>Vad gäller direktsänd strömning?

Du kan använda exakt samma design och implementering för att skydda liveströmning i Media Services genom att behandla tillgången som associeras med ett program som en VOD tillgång. Om du vill tillhandahålla ett multi-DRM-skydd för Live-innehållet använder du samma installation/bearbetning till till gången som om det vore en VOD till gång innan du kopplar till gången till Live-utdata.

### <a name="what-about-license-servers-outside-media-services"></a>Vad gäller servrar för fjärrskrivbordslicenser utanför Media Services?

Ofta kunder har investerat i en licens-servergrupp i sina egna Datacenter eller en med DRM-tjänstleverantörer. Med medietjänster content protection kan driva du i hybridläge. Innehållet kan som värd och dynamiskt skyddas i Media Services, medan DRM-licenser som levereras av servrar utanför Media Services. I det här fallet ska du tänka på följande ändringar:

* STS måste utfärda token som accepteras och kan verifieras av licens-servergruppen. Widevine-licens-servrar som tillhandahålls av Axinom kräver till exempel en specifik JWT som innehåller rätt meddelanden. Därför måste ha en STS att utfärda en sådan JWT. 
* Du behöver inte längre konfigurera licensleveranstjänst i Media Services. Du måste ange licensen förvärv URL: er (för PlayReady, Widevine och FairPlay) när du konfigurerar ContentKeyPolicies.

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure Portal för att hantera v3-resurser?

För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile-koncept i v3?

AssetFiles har tagits bort från AMS-API: et för att separera Media Services från SDK-beroendet för lagring. Nu är lagring, inte Media Services, den information som tillhör lagringen. 

Mer information finns i [migrera till Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Var finns lagrings kryptering på klient Sidan?

Nu rekommenderar vi att du använder lagrings kryptering på Server sidan (som är aktiverat som standard). Mer information finns i [Azure Storage tjänst kryptering för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
