---
title: Azure Media Services-koncepten | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services-begrepp
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 79de09631b33f838c1a2f12fdc58b2648548004b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998428"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services-begrepp 

Det här avsnittet ger en översikt över de viktigaste begreppen för Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Tillgångar och lagring
### <a name="assets"></a>Tillgångar
En [tillgången](https://docs.microsoft.com/rest/api/media/operations/asset) innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När de digitala filerna överförs till en tillgång, kan de användas i Media Services kodning och strömning arbetsflöden.

En tillgång är mappad till en blobbehållare i Azure Storage-kontot och filerna i tillgången lagras som blockblobar i den behållaren. Sidblobar stöds inte av Azure Media Services.

När du bestämmer vilka medieinnehåll att överföra och lagra i en tillgång, gäller följande:

* En tillgång får innehålla endast en enda, unikt instans av medieinnehåll. Till exempel en enkel redigering av TV-avsnittet, film eller annons.
* En tillgång får inte innehålla flera återgivningar eller ändringar i en fil som audiovisuellt. Ett exempel på en felaktig användning av en tillgång skulle försöker lagra mer än en TV-avsnittet, annonsering eller flera kameravinklar från en enda produktion inuti en tillgång. Lagra flera återgivningar eller ändringar i en fil som audiovisuellt i en tillgång kan leda till problem som skickar kodningsjobb, strömning och skydda leveransen av tillgången senare i arbetsflödet.  

### <a name="asset-file"></a>Tillgångsfil
En [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representerar en faktiska video- eller ljudinnehåll fil som lagras i en blob-behållare. En resursfil är alltid kopplad till en tillgång och en tillgång kan innehålla en eller flera filer. Media Services Encoder aktiviteten misslyckas om ett objekt för tillgången-filen inte är associerad med en digital fil i en blobbehållare.

Den **AssetFile** instans och den faktiska mediefilen finns två olika objekt. AssetFile-instans innehåller metadata om filen media medan mediefilen innehåller faktiskt medieinnehåll.

Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services utan att använda Media Service API: er.

### <a name="asset-encryption-options"></a>Alternativ för kryptering av tillgången
Beroende på vilken typ av innehåll som du vill ladda upp, lagra och leverera erbjuder Media Services olika alternativ för kryptering av som du kan välja bland.

>[!NOTE]
>Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagring.

Om du planerar att leverera en MP4 med progressivt nedladdning ska använda det här alternativet för att överföra innehållet.

**StorageEncrypted** – Använd det här alternativet för att kryptera innehållet lokalt med hjälp av AES 256-bitars kryptering och sedan överföra det till Azure Storage var den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. I första hand för lagringskryptering är när du vill skydda din högkvalitativa inkommande mediefiler med stark kryptering i vila på disk. 

För att kunna leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan du kan strömma din tillgång, kommer servern för strömning tar bort lagringskryptering och strömmar ditt innehåll med den angivna leveransprincipen (exempelvis AES, PlayReady eller Ingen kryptering). 

**CommonEncryptionProtected** – Använd det här alternativet om du vill kryptera (eller överför redan krypterat) innehåll med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).

**EnvelopeEncryptionProtected** – Använd det här alternativet om du vill skydda (eller överför redan skyddas) HTTP Live Streaming (HLS) krypteras med Advanced Encryption Standard (AES). Om du överför HLS som redan har krypterats med AES, måste den har krypterats av Transform Manager.

### <a name="access-policy"></a>Åtkomstpolicy
En [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definierar behörigheter (till exempel läsa, skriva och lista) och varaktighet för åtkomst till en tillgång. Du skulle vanligtvis skicka ett AccessPolicy-objekt till en positionerare som skulle användas för att komma åt filer i en tillgång.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="blob-container"></a>BLOB-behållare
En blob-behållare grupperar en uppsättning blobbar. BLOB-behållare används som gräns för åtkomstkontroll och signatur för delad åtkomst (SAS)-positionerare på tillgångar i Media Services. Ett Azure Storage-konto kan innehålla ett obegränsat antal blob-behållare. En container kan lagra ett obegränsat antal blobar.

>[!NOTE]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services utan att använda Media Service API: er.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Positionerare
[Lokaliserare](https://docs.microsoft.com/rest/api/media/operations/locator)s ger en startpunkt för att få åtkomst till filer i en tillgång. En åtkomstprincip används för att definiera behörigheter och varaktighet för att en klient har åtkomst till en given tillgång. Lokaliserare kan ha en många-till-en-relation med en åtkomstprincip, så att olika lokaliserare kan ge olika start- och anslutningstyper till olika klienter samtidigt som alla använder samma behörighet och varaktighet inställningar. på grund av en delad åtkomst principbegränsning som anges av Azure storage-tjänster, kan inte du ha fler än fem unik positionerare som är associerade med en given tillgång i taget. 

Media Services stöder två typer av lokaliserare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) eller hämta progressivt media och SAS-URL-positionerare som används för att överföra eller hämta media filer to\from Azure storage. 

>[!NOTE]
>Behörigheten lista (AccessPermissions.List) ska inte användas när du skapar en OnDemandOrigin-positionerare. 

### <a name="storage-account"></a>Lagringskonto
All åtkomst till Azure Storage görs genom ett lagringskonto. Ett Media Services-konto kan associera med en eller flera lagringskonton. Ett konto kan innehålla ett obegränsat antal behållare, så länge som deras totala storlek är under 500TB per lagringskonto.  Media Services tillhandahåller SDK på Verktyg så att du kan hantera flera lagringskonton och belastningsutjämna distributionen av dina tillgångar vid överföring till dessa konton baserat på mått eller slumpmässig distribution. Mer information finns i Arbeta med [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Jobb och aktiviteter
En [jobbet](https://docs.microsoft.com/rest/api/media/operations/job) används vanligtvis för att bearbeta (till exempel index eller koda) ett ljud/video presentation. Om du bearbetar flera videor, skapa ett jobb för varje video som ska kodas.

Ett jobb innehåller metadata om bearbetningen som ska utföras. Varje jobb som innehåller en eller flera [uppgift](https://docs.microsoft.com/rest/api/media/operations/task)s som anger en atomisk bearbetning uppgift, tillgångarna indata, utdata tillgångar, en medieprocessor och dess tillhörande inställningar. Uppgifter i ett jobb kan sammanlänkas, där utdatatillgången för en aktivitet ges som indata tillgången till nästa aktivitet. På så vis kan ett jobb innehålla all bearbetning behövs för en media.

## <a id="encoding"></a>Encoding
Azure Media Services erbjuder flera alternativ för kodning av media i molnet.

När du börjar med Media Services, är det viktigt att förstå skillnaden mellan codec- och filformat.
Codec är program som implementerar komprimering/dekomprimering algoritmer filformat är behållare som innehåller komprimerade videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera innehåll med anpassad bithastighet MP4 eller Smooth Streaming-kodade i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver packa om till dessa strömningsformat.

Att dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md), måste du koda din mezzaninfil (källa) till en uppsättning MP4-filer eller Smooth Streaming-filer och har minst en standard- eller premium slutpunkten för direktuppspelning i Starta tillstånd.

Media Services stöder följande på begäran-kodare som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Information om kodare som stöds finns i [kodare](media-services-encode-asset.md).

## <a name="live-streaming"></a>Liveströmning
I Azure Media Services representerar en kanal en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal tar emot inkommande direktsändningar i ett av två sätt:

* En lokal livekodare skickar med flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) till kanalen. Du kan använda följande livekodare som Smooth Streaming med flera bithastigheter: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. I följande livekodare RTMP: Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision och Tricaster-kodare. De infogade strömmarna passerar via kanaler utan vidare transkodning eller kodning. På begäran levererar Media Services strömmen till kunder.
* En enda bithastighet (i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4)) skickas till den kanal som är aktiverad för att utföra live encoding med Media Services. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

### <a name="channel"></a>Kanal
I Media Services [kanal](https://docs.microsoft.com/rest/api/media/operations/channel)s ansvarar för bearbetning av liveuppspelningsinnehåll. En kanal som innehåller en slutpunkt för indata (infognings-URL) som du sedan vidarebefordra till en levande transkodare. Kanalen tar emot inkommande direktsändningar från live transkodare och gör den tillgänglig för direktuppspelning via en eller flera Strömningsslutpunkter. Kanaler tillhandahåller även en slutpunkt för förhandsversionen (förhandsgransknings-URL) som används för att förhandsgranska och validera dataströmmen inför vidare behandling och leverans.

Du kan hämta URL: en för inmatning och förhandsgransknings-URL när du skapar kanalen. För att få dessa URL: er, behöver inte kanalen vara tillståndet startad. När du är redo att börja skicka data från en levande transkodare till kanalen startas kanalen. När live transkodare startar mata in data, kan du förhandsgranska dataströmmen.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera Strömningsslutpunkter. Beroende på behov bandbredds- och kan StreamingEndpoint-tjänster vara dedikerad till en eller flera kanaler. Alla StreamingEndpoint kan hämta från alla kanaler.

### <a name="program-event"></a>Programmet (händelse)
En [Program (händelse)](https://docs.microsoft.com/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program (händelser). Relationen mellan kanal och Program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.
Du kan ange antalet timmar som du vill behålla inspelat innehåll för programmet genom att ange den **ArchiveWindowLength** egenskapen. Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength avgör också längsta tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program (händelse) är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Mer information finns i:

* [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Skydda innehåll
### <a name="dynamic-encryption"></a>Dynamisk kryptering
Azure Media Services kan du skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Media Services kan du leverera ditt innehåll dynamiskt krypterad med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar) och gemensam kryptering (CENC) med PlayReady och/eller Widevine DRM. Media Services tillhandahåller också en tjänst för leverans av nycklar för AES och PlayReady-licenser till auktoriserade klienter.

För närvarande kan kryptera du följande strömningsformat: HLS, MPEG DASH och Smooth Streaming. Du kan inte krypteras progressiva nedladdningar.

Om du vill använda för Media Services för att kryptera en tillgång, måste du associera en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med din tillgång och även konfigurera auktoriseringsprinciper för nyckeln.

Om du vill spela en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip för att ange hur du vill leverera din tillgång.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med en kuvert-kryptering (med AES) eller gemensam kryptering (med PlayReady eller Widevine). Om du vill dekryptera dataströmmen begär spelaren nyckeln från nyckelleveranstjänst. När du beslutar om användaren har behörighet att hämta nyckel måste utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

### <a name="token-restriction"></a>Tokenbegränsningar
Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppna, token begränsning eller IP-begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formatet Simple Web token (SWT) och format för JSON Web Token (JWT). Media Services tillhandahåller säkra Säkerhetstokentjänster. Du kan skapa en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda (eller licensinformation) till klienten om token är giltig och anspråk i token matchar de som konfigurerats för nyckeln (eller licens).

När du konfigurerar token tokenbegränsade principen, måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med, utfärdare är den säkra tokentjänst som utfärdar en token. Målgruppen (kallas ibland för omfång) beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

Mer information finns i följande artiklar:
- [Skydda innehåll – översikt](media-services-content-protection-overview.md)
- [Skydda med AES-128](media-services-protect-with-aes128.md)
- [Skydda med PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Levererar
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamisk paketering
När du arbetar med Media Services, rekommenderar vi att koda ditt mezzanine-filer till en med anpassningsbar bithastighet MP4-uppsättningen och sedan konvertera uppsättningen till det önskade formatet med hjälp av den [dynamisk paketering](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning
En StreamingEndpoint representerar en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN Content Delivery Network () för vidare distribution (Azure Media Services ger nu Azure CDN-integreringen.) Den utgående dataströmmen från en strömmande slutpunkt-tjänst kan vara en direktsänd dataström eller en video på begäran-tillgång i Media Services-kontot. Media Services-kunder väljer antingen en **Standard**-slutpunkt för direktuppspelning eller en eller flera **Premium**-slutpunkter för direktuppspelning, utifrån behov. Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. 

Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Standard-slutpunkter för direktuppspelning erbjuder flexibilitet för att leverera ditt innehåll till i stort sett alla enheter via dynamisk paketering till HLS, MPEG-DASH, Smooth Streaming samt och dynamisk kryptering för Microsoft PlayReady, Google Widevine, Apple Fairplay och AES128.  De även skala från mycket små till mycket stora målgrupper med tusentals samtidiga användare via Azure CDN-integreringen. Om du har en avancerad arbetsbelastning eller dina kapacitetskrav för direktuppspelning inte passar att standard strömmande slutpunkt dataflödesmål eller du vill styra StreamingEndpoint-tjänstens kapacitet att hantera växande behov av bandbredd, rekommenderar vi att du allokera skalningsenheter (kallas även premiumenheter för strömning).

Det rekommenderas att använda dynamisk paketering och/eller dynamisk kryptering.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Mer information finns i [detta](media-services-portal-manage-streaming-endpoints.md) avsnitt.

Du kan ha upp till 2 strömmande slutpunkter i Media Services-kontot som standard. Om du vill begära en högre gräns, se [kvoter och begränsningar](media-services-quotas-and-limitations.md).

Du debiteras endast när din StreamingEndpoint är i körningstillstånd.

### <a name="asset-delivery-policy"></a>Principen för tillgångsleverans
Ett av stegen i arbetsflödet för Media Services-leverans av innehåll som krävs konfigureras [leveransprinciper för tillgångar](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)som du vill att strömmas. Talar tillgångsleveransprincip om Media Services för hur du vill för tillgången som ska levereras: i vilket strömningsprotokoll bör din tillgång dynamiskt paketeras (till exempel, MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt din tillgång och hur (kuvert eller gemensam kryptering).

Om du har en storage krypterade tillgången, innan du kan strömma din tillgång kommer servern för strömning tar bort storage-kryptering och strömning ditt innehåll med den angivna principen. Exempelvis för att leverera din tillgång som krypterats med Advanced Encryption Standard (AES) krypteringsnyckeln, ställer du in typen av DynamicEnvelopeEncryption. Om du vill ta bort lagringskryptering och strömma tillgången i klartext, anger du principtypen till NoDynamicEncryption.

### <a name="progressive-download"></a>Progressiv nedladdning
Progressiv nedladdning kan du börja spela upp media innan hela filen har laddats ned. Du kan endast progressivt hämta MP4-fil.

>[!NOTE]
>Du måste dekryptera krypterade tillgångar om du vill att de ska vara tillgängliga för progressiv nedladdning.

För att ge användare URL: er för progressiv nedladdning måste skapa du först en OnDemandOrigin-positionerare. Skapa lokaliseraren får du grundläggande sökvägen till tillgången. Sedan måste du lägga till namnet på MP4-fil. Exempel:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Direktuppspelning av innehåll till klienter. För att ge användare strömmande URL: er måste skapa du först en OnDemandOrigin-positionerare. Skapar lokaliseraren får du grundläggande sökvägen till den tillgång som innehåller det innehåll som du vill spela. Men om du vill kunna strömma innehållet behöver du ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till strömmande manifestfilen måste du sammanfoga den lokaliseraren sökvägsvärde och manifestet (filename.ism) filnamn. Lägg sedan till /Manifest och ett lämpligt format (vid behov) till positionerare-sökväg.

Du kan också strömma ditt innehåll över en SSL-anslutning. Om du vill göra detta måste du kontrollera att din strömmande URL: er börjar med HTTPS. För närvarande stöder AMS inte SSL med anpassade domäner.  

>[!NOTE]
>Du kan endast strömmas via SSL om slutpunkten för direktuppspelning som du kan leverera ditt innehåll har skapats efter den 10 September 2014. Om din strömmande URL: er baseras på de slutpunkter för direktuppspelning som skapats efter 10 September, innehåller URL: en ”streaming.mediaservices.windows.net” (det nya formatet). Strömmande URL: er som innehåller ”origin.mediaservices.windows.net” (det äldre formatet) stöder inte SSL. Om din URL: en används det äldre formatet och du vill kunna strömma via SSL, kan du skapa en ny slutpunkt för direktuppspelning. Använd URL: er skapa baserat på den nya slutpunkten för direktuppspelning för att strömma ditt innehåll via SSL.

I följande lista beskrivs olika strömningsformat och ger exempel:

* Smooth Streaming

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

