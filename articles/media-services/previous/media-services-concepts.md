---
title: Azure Media Services-koncepten | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services-koncepten
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: dcefc8bc-e2ea-4b38-a643-9010f4436fb5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: juliako
ms.openlocfilehash: f9d51869b9a6ba63c73637c50f5a19e864bc23e4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942310"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services-koncepten
Det här avsnittet ger en översikt av de viktigaste Media Services-begrepp.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Tillgångar och lagring
### <a name="assets"></a>Tillgångar
En [tillgången](https://docs.microsoft.com/rest/api/media/operations/asset) innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När digitala filer överförs till en tillgång, kan de användas i Media Services encoding och direktöverföring av arbetsflöden.

En tillgång som är mappad till en blob-behållare i Azure Storage-konto och lagras filerna i tillgången som blockblobar i behållaren. Azure Media Services stöder inte sidblobar.

När du bestämmer vilka medieinnehåll att överföra och lagra i en tillgång, gäller följande:

* En tillgång får innehålla endast en enda, unikt instans av medieinnehåll. Till exempel en enda redigering av TV-avsnitt, film eller annons.
* En tillgång får inte innehålla flera återgivningar eller ändringar i en fil som audiovisuella. Ett exempel på en felaktig användning av en tillgång skulle försök gjordes att lagra mer än en TV-avsnitt, annons eller flera kameravinklar från en enda produktion inuti en tillgång. Lagra flera återgivningar eller ändringar i en audiovisuella fil i en tillgång kan det leda till problem med att skicka kodning jobb, strömning och skydda leverans av tillgången senare i arbetsflödet.  

### <a name="asset-file"></a>Resursfil
En [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representerar en faktiska video eller ljud-fil som lagras i en blob-behållare. En resursfil är alltid associerad med en tillgång och en tillgång kan innehålla en eller många filer. Media Services Encoder uppgiften misslyckas om objekttypen tillgången filen inte är associerad med en digital fil i en blob-behållaren.

Den **AssetFile** instansen och den faktiska mediefilen är två distinkta objekt. AssetFile-instans innehåller metadata om filen media när mediefilen innehåller faktiskt medieinnehåll.

Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services utan att använda Media Service API: er.

### <a name="asset-encryption-options"></a>Alternativ för kryptering av tillgångsinformation
Beroende på vilken typ av innehåll som du vill överföra, lagra och leverera tillhandahåller Media Services olika krypteringsalternativ för som du kan välja bland.

>[!NOTE]
>Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagringsutrymmet.

Om du planerar att leverera en MP4 med progressivt nedladdning ska använda det här alternativet för att ladda upp ditt innehåll.

**StorageEncrypted** – Använd det här alternativet för att kryptera innehållet lokalt med hjälp av AES 256-bitars kryptering och överföra den till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina hög kvalitet inkommande mediefiler med stark kryptering i vila på disk. 

För att kunna leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip så Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas strömmande server tar du bort krypteringen lagring och strömmar ditt innehåll med hjälp av angivna leveransprincipen (till exempel AES, PlayReady eller Ingen kryptering). 

**CommonEncryptionProtected** – Använd det här alternativet om du vill kryptera (eller överför redan krypterat) innehåll med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).

**EnvelopeEncryptionProtected** – Använd det här alternativet om du vill skydda (eller överför redan skyddad) HTTP Live Streaming (HLS) krypteras med Advanced Encryption Standard (AES). Om du överför HLS som redan har krypterats med AES, måste den har krypterats av Transform Manager.

### <a name="access-policy"></a>Åtkomstpolicy
En [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definierar behörigheter (t.ex. Läs-, Skriv- och listan) och varaktighet för åtkomst till en tillgång. Du skulle vanligtvis skickar ett AccessPolicy-objekt till en positionerare som sedan används för att komma åt filer i en tillgång.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="blob-container"></a>BLOB-behållare
En blob-behållare grupperar en uppsättning blobbar. BLOB-behållare används som gräns för åtkomstkontroll och delade signatur åtkomst (SAS)-positionerare på tillgångar i Media Services. Ett Azure Storage-konto kan innehålla ett obegränsat antal blob-behållare. En behållare kan lagra ett obegränsat antal blobbar.

>[!NOTE]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services utan att använda Media Service API: er.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Lokaliserare
[Lokaliserare](https://docs.microsoft.com/rest/api/media/operations/locator)s ger en startpunkt för att få åtkomst till filer i en tillgång. En åtkomstprincip används för att definiera behörigheter och varaktighet för att en klient har åtkomst till en given tillgång. Positionerare kan ha en många-till-en relation med en åtkomstprincip så att olika positionerare kan ange olika start- och anslutningstyper för olika klienter när alla använder samma behörighet och varaktigheten; på grund av en delad åtkomst för begränsning av Azure storage-tjänster, kan inte du ha fler än fem unika lokaliserare som är associerade med en viss resurs i taget. 

Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) eller progressivt hämta media och SAS-URL-positionerare som används för att överföra eller hämta media filer to\from Azure storage. 

>[!NOTE]
>Behörigheten lista (AccessPermissions.List) bör inte användas när du skapar en OnDemandOrigin-positionerare. 

### <a name="storage-account"></a>Lagringskonto
All åtkomst till Azure Storage görs genom ett lagringskonto. Ett Media Service-konto kan associera med en eller flera lagringskonton. Ett konto kan innehålla ett obegränsat antal behållare, så länge som deras sammanlagda storlek är under 500TB per lagringskonto.  Media Services tillhandahåller SDK nivån verktygsuppsättning så att du kan hantera flera lagringskonton och belastningsutjämna distribution av dina tillgångar vid överföring till dessa konton baserat på mått eller en slumpmässig distributionsplats. Mer information finns i Arbeta med [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Jobb och uppgifter
En [jobbet](https://docs.microsoft.com/rest/api/media/operations/job) används vanligtvis för att bearbeta (till exempel index eller koda) ljud/video presentationer. Om du bearbetar flera videor, skapa ett jobb varje video ska kodas.

Ett jobb innehåller metadata om bearbetning ska utföras. Varje jobb innehåller en eller flera [aktivitet](https://docs.microsoft.com/rest/api/media/operations/task)s som anger en atomisk Bearbetningsuppgift tillgångarna indata, utdata tillgångar, en medieprocessor och dess associerade inställningarna. Uppgifter i ett jobb kan sammankopplas, där utdatatillgången av en aktivitet anges som indata tillgången till nästa aktivitet. På så sätt kan ett jobb innehåller alla bearbetning behövs för en media.

## <a id="encoding"></a>Kodning
Azure Media Services erbjuder flera alternativ för kodning av media i molnet.

När börjat med Media Services är det viktigt att förstå skillnaden mellan codec och filformat.
Codec-rutiner är programvara som implementerar komprimering/dekomprimering algoritmer filformat är behållare som innehåller den komprimerad videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt innehåll MP4 eller Smooth Streaming-kodade med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver packa om till dessa strömningsformat.

Dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md), måste du koda din mezzaninfil (källa) till en uppsättning med anpassningsbar bithastighet MP4-filer eller Smooth Streaming-filer och har minst en standard eller premium strömmande slutpunkten i startat tillstånd.

Media Services stöder följande på begäran-kodare som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Information om stöds kodare finns [kodare](media-services-encode-asset.md).

## <a name="live-streaming"></a>Liveströmning
I Azure Media Services representerar en kanal en pipeline för bearbetning av liveströmmat innehåll. En kanal som tar emot live indata i ett av två sätt:

* En lokal livekodare skickar flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) till kanalen. Du kan använda de följande livekodare som skickar Smooth Streaming i flera bithastigheter: MediaExcel, Ateme, anta kommunikation, Envivio, Cisco och Elemental. Följande livekodare skickar RTMP: Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision och Tricaster kodare. De infogade strömmarna passerar genom kanalerna utan ytterligare omkodning och kodning. På begäran levererar Media Services strömmen till kunder.
* En dataström med enkel bithastighet (i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4)) som skickas till den kanal som är aktiverad för att utföra live encoding med Media Services. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

### <a name="channel"></a>Kanal
I Media Services [kanal](https://docs.microsoft.com/rest/api/media/operations/channel)s ansvarar för bearbetning av liveströmmat innehåll. En kanal som tillhandahåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en levande transcoder. Kanalen tar emot live indata från live transcoder och gör den tillgänglig för strömning via en eller flera Strömningsslutpunkter. Kanaler ger också förhandsgranskningsslutpunkten (förhandsgranskning URL) som används för att förhandsgranska och verifiera strömmen innan ytterligare bearbetning och leverans.

Du kan få infognings-URL och förhandsgransknings-URL när du skapar kanalen. För att få dessa URL: er, behöver kanalen inte vara i startat tillstånd. När du är redo att börja skicka data från en levande transcoder till kanalen måste kanalen vara igång. När levande transcoder startar mata in data, kan du förhandsgranska dataströmmen.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera Strömningsslutpunkter. Beroende på behov bandbredds- och kan StreamingEndpoint tjänster dedikeras till en eller flera kanaler. Alla StreamingEndpoint dra från varje kanal.

### <a name="program-event"></a>Program (händelse)
En [Program (händelse)](https://docs.microsoft.com/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program (händelser). Relationen mellan kanal och Program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för programmet genom att ange den **ArchiveWindowLength** egenskapen. Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength avgör också hur lång tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program (händelse) är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Mer information finns i:

* [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Skydda innehållet
### <a name="dynamic-encryption"></a>Dynamisk kryptering
Azure Media Services kan du skydda mediet från den tidpunkt som den lämnar din dator via lagring, bearbetning och leverans. Media Services kan du leverera ditt innehåll krypteras dynamiskt med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar) och vanliga kryptering (CENC) med PlayReady och/eller Widevine DRM. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och PlayReady-licenser till auktoriserade klienter.

För närvarande kan du kryptera följande strömningsformat: HLS MPEG DASH och Smooth Streaming. Det går inte att kryptera progressiv hämtning.

Om du vill använda för Media Services att kryptera en tillgång, måste du associera en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med din tillgång och även konfigurera auktoriseringsprinciper för nyckeln.

Om du vill strömma en krypterad tillgång lagring måste du konfigurera den tillgångsleveransprincip för att ange hur du vill leverera din tillgång.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med en kuvert kryptering (med AES) eller vanliga kryptering (med PlayReady eller Widevine). Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

### <a name="token-restriction"></a>Tokenbegränsningar
Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar: öppen och token begränsning eller en IP-begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formatet Simple Web Tokens (SWT) och JSON-Webbtoken (JWT)-format. Media Services tillhandahåller inte Secure Token tjänster. Du kan skapa en anpassad STS eller använda Microsoft Azure ACS problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returneras den begärda (eller licensinformation) till klienten om token är giltig och anspråk i token matchar de som konfigurerats för nyckeln (eller licens).

När du konfigurerar token begränsad princip, måste du ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar. Primära Verifieringsnyckeln innehåller den nyckel som token som signerats med, utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

Mer information finns i följande artiklar:
- [Skydda innehåll-översikt](media-services-content-protection-overview.md)
- [Skydda med AES-128](media-services-protect-with-aes128.md)
- [Skydda med PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Leverera
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamisk paketering
När du arbetar med Media Services, rekommenderas att koda mezzanine filerna till en MP4-uppsättningen med anpassad bithastighet och sedan konvertera uppsättningen till önskade format med hjälp av den [dynamisk paketering](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning
En StreamingEndpoint representerar en strömmande tjänst som kan leverera innehåll direkt till ett klientprogram player eller till en innehåll innehållsleveransnätverk (CDN) för vidare distribution (Azure Media Services tillhandahåller nu Azure CDN-integration.) Utgående dataströmmen från en strömmande slutpunkt-tjänst kan vara en direktsänd dataström eller en video på begäran tillgångar i Media Services-kontot. Media Services-kunder väljer antingen en **Standard**-slutpunkt för direktuppspelning eller en eller flera **Premium**-slutpunkter för direktuppspelning, utifrån behov. Standard strömmande slutpunkten är lämplig för de flesta strömmande arbetsbelastningar. 

Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Standard Strömningsslutpunkter ger flexibilitet för att leverera ditt innehåll till praktiskt taget alla enheten via dynamisk paketering till HLS, MPEG DASH, Smooth Streaming samt och dynamisk kryptering för Microsoft PlayReady, Google Widevines, Apple Fairplay och AES128.  De även skala från mycket små till stora målgrupper med tusentals samtidiga användare via Azure CDN-integrering. Om du har en avancerad arbetsbelastning eller dina strömmande kapacitetskrav inte får plats på standard strömmande slutpunkten genomströmning mål eller du vill styra tjänsten StreamingEndpoint förmåga att hantera växande behov av bandbredd, rekommenderas att allokera skalenheter (även kallat premium enheter för strömning).

Det rekommenderas att använda dynamisk paketering och dynamisk kryptering.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Mer information finns i [detta](media-services-portal-manage-streaming-endpoints.md) avsnitt.

Du kan ha upp till 2 strömningsslutpunkter i Media Services-kontot som standard. Om du vill begära en högre gräns finns [kvoter och begränsningar](media-services-quotas-and-limitations.md).

Du debiteras endast när din StreamingEndpoint är i körningstillstånd.

### <a name="asset-delivery-policy"></a>Principen för tillgångsleverans
Ett av stegen i Media Services innehållsleverans arbetsflödet konfigurerar [leveransprinciperna för tillgångar ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)som du vill att strömmas. Anger tillgångsleveransprincip Media Services hur du vill använda för din tillgång som ska levereras: till vilka streaming-protokollet bör din tillgång dynamiskt paketeras (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt din tillgång och hur (envelope eller vanliga kryptering).

Om du har en tillgång med lagring krypterade innan din tillgång kan strömmas strömmande server tar du bort lagringskryptering och strömmar ditt innehåll med hjälp av angivna leveransprincipen. Exempelvis för att leverera din tillgång som krypterats med Advanced Encryption Standard (AES) krypteringsnyckeln ställer du in Principtyp DynamicEnvelopeEncryption. Om du vill ta bort lagringskryptering och strömma tillgången i klartext, anger du principen till NoDynamicEncryption.

### <a name="progressive-download"></a>Progressiv hämtning
Progressiv nedladdning kan du spela upp media innan hela filen har hämtats. Du kan bara progressivt hämta MP4-fil.

>[!NOTE]
>Du måste dekryptera krypterade tillgångar om du vill att de ska vara tillgängliga för progressiv nedladdning.

För att ge användare URL: er för progressiv nedladdning måste skapa du först en OnDemandOrigin-positionerare. Skapar positioneraren får du rotsökvägen till tillgången. Sedan måste du lägga till namnet på MP4-fil. Exempel:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Direktuppspelning av ditt innehåll till klienter. För att ge användare URL: er för strömning måste skapa du först en OnDemandOrigin-positionerare. Skapa positioneraren ger rotsökvägen till den tillgång som du vill strömma innehållet. Men om du vill kunna strömma innehållet behöver du ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till den strömmande manifestfilen måste du sammanfoga värdet för den positionerare sökväg och manifestet (filename.ism) namn. Lägg sedan till /Manifest och ett lämpligt format (vid behov) till sökvägen lokaliserare.

Du kan också strömma ditt innehåll via en SSL-anslutning. Gör detta genom att kontrollera att din strömmande URL: er som börjar med HTTPS. För närvarande stöder AMS inte SSL med anpassade domäner.  

>[!NOTE]
>Du kan bara strömma via SSL om den strömningsslutpunkt från vilken du kan leverera ditt innehåll skapades efter 10 September 2014. Om din strömmande URL: er baseras på strömningsslutpunkter som skapats efter 10 September, innehåller URL: en ”streaming.mediaservices.windows.net” (det nya formatet). Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det gamla formatet) stöder inte SSL. Om URL: en är i formatet gamla och du vill kunna strömma via SSL, skapa en ny strömmande slutpunkt. Använd URL: er skapas baserat på den nya strömmande slutpunkten för att strömma ditt innehåll via SSL.

I följande lista beskrivs olika strömningsformat och ger exempel:

* Smooth Streaming

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

