---
title: Azure Media Services-koncept | Microsoft-dokument
description: Den här artikeln innehåller en kort översikt över Microsoft Azure Media Services-begrepp och länkar till andra artiklar för mer information.
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
ms.openlocfilehash: 69e2c053c9fb874889bc3d5b08be6e0c7ce875a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162913"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services-koncept 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Det här avsnittet innehåller en översikt över de viktigaste begreppen för Medietjänster.

## <a name="assets-and-storage"></a><a id="assets"/>Tillgångar och lagring
### <a name="assets"></a>Tillgångar
En [tillgång](https://docs.microsoft.com/rest/api/media/operations/asset) innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text) och metadata om dessa filer. När de digitala filerna har överförts till en tillgång kan de användas i mediatjänsts kodnings- och strömningsarbetsflödena.

En tillgång mappas till en blob-behållare i Azure Storage-kontot och filerna i tillgången lagras som blockblobar i den behållaren. Sidblobar stöds inte av Azure Media Services.

När du bestämmer vilket medieinnehåll som ska laddas upp och lagras i en tillgång gäller följande:

* En tillgång bör endast innehålla en enda, unik instans av medieinnehåll. Till exempel en enda redigering av ett TV-avsnitt, en film eller en annons.
* En tillgång bör inte innehålla flera återgivningar eller redigeringar av en audiovisuell fil. Ett exempel på en felaktig användning av en tillgång skulle vara att försöka lagra mer än en TV-episod, annons eller flera kameravinklar från en enda produktion inuti en tillgång. Lagring av flera återgivningar eller redigeringar av en audiovisuell fil i en tillgång kan leda till svårigheter att skicka kodningsjobb, strömma och säkra leveransen av tillgången senare i arbetsflödet.  

### <a name="asset-file"></a>Tillgångsfil
En [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representerar en faktisk video- eller ljudfil som lagras i en blob-behållare. En tillgångsfil är alltid associerad med en tillgång och en tillgång kan innehålla en eller flera filer. Aktiviteten Media Services Encoder misslyckas om ett tillgångsfilobjekt inte är associerat med en digital fil i en blob-behållare.

**AssetFile-förekomsten** och den faktiska mediefilen är två olika objekt. AssetFile-instansen innehåller metadata om mediefilen, medan mediefilen innehåller det faktiska medieinnehållet.

Du bör inte försöka ändra innehållet i blob-behållare som genererades av Media Services utan att använda API:er för medietjänsten.

### <a name="asset-encryption-options"></a>Krypteringsalternativ för tillgångar
Beroende på vilken typ av innehåll du vill ladda upp, lagra och leverera tillhandahåller Media Services olika krypteringsalternativ som du kan välja mellan.

>[!NOTE]
>Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet är ditt innehåll inte skyddat under överföring eller i vila i lagring.

Om du planerar att leverera en MP4 med progressiv nedladdning använder du det här alternativet för att ladda upp innehållet.

**StorageEncrypted** – Använd det här alternativet för att kryptera ditt rensa innehåll lokalt med AES 256-bitars kryptering och sedan ladda upp det till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering okrypteras automatiskt och placeras i ett krypterat filsystem före kodning och krypteras eventuellt igen innan de laddas upp som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina högkvalitativa indatamediefiler med stark kryptering i vila på disken. 

För att kunna leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas tar den strömmande servern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen (till exempel AES, PlayReady eller ingen kryptering). 

**CommonEncryptionProtected** - Använd det här alternativet om du vill kryptera (eller ladda upp redan krypterat) innehåll med gemensam kryptering eller PlayReady DRM (till exempel Smooth Streaming skyddas med PlayReady DRM).

**EnvelopeEncryptionProtected** – Använd det här alternativet om du vill skydda (eller ladda upp redan skyddade) HTTP Live Streaming (HLS) krypterad med Advanced Encryption Standard (AES). Om du laddar upp HLS redan krypterad med AES måste den ha krypterats av Transform Manager.

### <a name="access-policy"></a>Åtkomstprincip

En [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definierar behörigheter (som läst, skriv och lista) och varaktighet för åtkomst till en tillgång. Du skickar vanligtvis ett AccessPolicy-objekt till en positionerare som sedan skulle användas för att komma åt filerna i en tillgång.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="blob-container"></a>Blobcontainer
En blob-behållare innehåller en gruppering av en uppsättning blobbar. Blob-behållare används i Media Services som gränspunkt för åtkomstkontroll och SAS-positionerare (Shared Access Signature) på tillgångar. Ett Azure Storage-konto kan innehålla ett obegränsat antal blob-behållare. En container kan lagra ett obegränsat antal blobar.

>[!NOTE]
> Du bör inte försöka ändra innehållet i blob-behållare som genererades av Media Services utan att använda API:er för medietjänsten.
> 
> 

### <a name="locators"></a><a id="locators"/>Positionerare
[Positionerare](https://docs.microsoft.com/rest/api/media/operations/locator)ger en startpunkt för att komma åt filerna i en tillgång. En åtkomstprincip används för att definiera de behörigheter och varaktigheter som en klient har åtkomst till en viss tillgång. Positionerare kan ha en många till en relation med en åtkomstprincip, så att olika positionerare kan ge olika starttider och anslutningstyper till olika klienter medan alla använder samma behörighets- och varaktighetsinställningar. På grund av en principbegränsning för delad åtkomst som har angetts av Azure-lagringstjänster kan du inte ha fler än fem unika positionerare som är associerade med en viss tillgång samtidigt. 

Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare, som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) eller gradvis hämta media och SAS URL-positionerare, som används för att ladda upp eller ladda ned mediefiler till\från Azure-lagring. 

>[!NOTE]
>Listbehörigheten (AccessPermissions.List) ska inte användas när du skapar en OnDemandOrigin-positionerare. 

### <a name="storage-account"></a>Lagringskonto
All åtkomst till Azure Storage sker via ett lagringskonto. Ett Media Service-konto kan associera med ett eller flera lagringskonton. Ett konto kan innehålla ett obegränsat antal behållare, så länge deras totala storlek är under 500 TB per lagringskonto.  Media Services tillhandahåller verktyg på SDK-nivå så att du kan hantera flera lagringskonton och belastningsutjämnande fördelningen av dina tillgångar under överföringen till dessa konton baserat på mått eller slumpmässig distribution. Mer information finns i Arbeta med [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Jobb och uppgifter
Ett [jobb](https://docs.microsoft.com/rest/api/media/operations/job) används vanligtvis för att bearbeta (till exempel index eller koda) en ljud-/videopresentation. Om du bearbetar flera videor skapar du ett jobb för varje video som ska kodas.

Ett jobb innehåller metadata om den bearbetning som ska utföras. Varje jobb innehåller en eller [flera aktiviteter](https://docs.microsoft.com/rest/api/media/operations/task)som anger en atombearbetningsuppgift, dess indatatillgångar, utdatatillgångar, en medieprocessor och tillhörande inställningar. Aktiviteter inom ett projekt kan kedjas ihop, där utdatatillgången för en aktivitet anges som indatatillgång till nästa aktivitet. På så sätt kan ett jobb innehålla all bearbetning som krävs för en mediepresentation.

## <a name="encoding"></a><a id="encoding"></a>Kodning
Azure Media Services innehåller flera alternativ för kodning av media i molnet.

När du börjar med Media Services är det viktigt att förstå skillnaden mellan codec-enheter och filformat.
Codecs är den programvara som implementerar komprimerings-/dekompressionsalgoritmerna medan filformat är behållare som innehåller den komprimerade videon.

Media Services tillhandahåller dynamiska förpackningar som gör att du kan leverera ditt adaptiva bithastighets-MP4- eller Smooth Streaming-kodat innehåll i strömmande format som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) utan att du behöver paketera om till dessa strömmande format.

För att dra nytta av [dynamiska förpackningar](media-services-dynamic-packaging-overview.md)måste du koda din mezzanine (källa) fil i en uppsättning adaptiva bitrate MP4-filer eller adaptiv bitrate Smooth Streaming filer och har minst en standard eller premium streaming slutpunkt i startat tillstånd.

Media Services stöder följande kodare på begäran som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Information om kodare som stöds finns i [Kodare](media-services-encode-asset.md).

## <a name="live-streaming"></a>Liveuppspelning
I Azure Media Services representerar en kanal en pipeline för bearbetning av direktuppspelning av innehåll. En kanal tar emot live-indataströmmar på ett av två sätt:

* En lokal live-kodare skickar rtmp-kodning med flera bithastigheter (Smooth Streaming )till kanalen. Du kan använda följande live-kodare som matar ut multibitrate Smooth Streaming: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. Följande live-kodare matar ut RTMP: Adobe Flash Live Encoder, [Telestream Wirecast,](media-services-configure-wirecast-live-encoder.md)Teradek, Haivision och Tricaster-kodare. De intas strömmarna passerar genom kanaler utan ytterligare omkodning och kodning. På begäran levererar Media Services strömmen till kunder.
* En enda bithastighetsström (i något av följande format: RTMP eller Smooth Streaming (Fragmenterad MP4)) skickas till kanalen som är aktiverad för att utföra live-kodning med Media Services. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

### <a name="channel"></a>Kanal
I Media Services ansvarar [Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s för att bearbeta livestreaminginnehåll. En kanal innehåller en ingångsslutpunkt (inmatnings-URL) som du sedan tillhandahåller en direktomkodare. Kanalen tar emot live-indataströmmar från live-omkodaren och gör den tillgänglig för direktuppspelning via en eller flera StreamingEndpoints. Kanalerna tillhandahåller också en förhandsgranskningsslutpunkt (förhandsgransknings-URL) som du använder för att förhandsgranska och validera flödet innan du bearbetar och levererar vidare.

Du kan hämta den intbeteringsadressen och förhandsgranskningsadressen när du skapar kanalen. För att få dessa webbadresser behöver kanalen inte vara i startat tillstånd. När du är redo att börja skicka data från en live-omkodare till kanalen måste kanalen startas. När den aktiva omkodaren börjar inta data kan du förhandsgranska din ström.

Varje Media Services-konto kan innehålla flera kanaler, flera program och flera StreamingEndpoints. Beroende på bandbredd och säkerhetsbehov kan StreamingEndpoint-tjänster desdiceras till en eller flera kanaler. Alla StreamingEndpoint kan hämta från vilken kanal som helst.

### <a name="program-event"></a>Program (händelse)
Med [ett program (händelse)](https://docs.microsoft.com/rest/api/media/operations/program) kan du styra publicering och lagring av segment i en livestream. Kanaler hanterar program (händelser). Kanal- och programrelationen liknar traditionella medier där en kanal har en konstant ström av innehåll och ett program är begränsat till en tidsinnöjd händelse på den kanalen.
Du kan ange hur många timmar du vill behålla det inspelade innehållet för programmet genom att ange egenskapen **ArchiveWindowLength.** Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArkivWindowLength dikterar också den maximala tid klienter kan söka tillbaka i tiden från den aktuella live-positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program (händelse) är associerat med en tillgång. Om du vill publicera programmet måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Mer information finns i:

* [Arbeta med kanaler som är aktiverade för att utföra livekodning med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Skydda innehåll
### <a name="dynamic-encryption"></a>Dynamisk kryptering
Med Azure Media Services kan du skydda dina media från det att datorn lämnas via lagring, bearbetning och leverans. Med Media Services kan du leverera ditt innehåll krypterat dynamiskt med Advanced Encryption Standard (AES) (med 128-bitars krypteringsnycklar) och gemensam kryptering (CENC) med PlayReady och/eller Widevine DRM. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och PlayReady-licenser till auktoriserade klienter.

För närvarande kan du kryptera följande streamingformat: HLS, MPEG DASH och Smooth Streaming. Du kan inte kryptera progressiva nedladdningar.

Om du vill att Media Services ska kryptera en tillgång måste du associera en krypteringsnyckel (CommonEncryption eller EnvelopeEncryption) med din tillgång och även konfigurera auktoriseringsprinciper för nyckeln.

Om du vill strömma en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip för att ange hur du vill leverera din tillgång.

När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av en kuvertkryptering (med AES) eller gemensam kryptering (med PlayReady eller Widevine). För att dekryptera strömmen kommer spelaren att begära nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de auktoriseringsprinciper som du har angett för nyckeln.

### <a name="token-restriction"></a>Token begränsning
Behörighetsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppen, tokenbegränsning eller IP-begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten Simple Web Tokens (SWT) och JSON Web Token (JWT). Media Services tillhandahåller inte säkra tokentjänster. Du kan skapa en anpassad STS. STS måste konfigureras för att skapa en token som har signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen. Media Services nyckelleveranstjänst returnerar den begärda nyckeln (eller licensen) till klienten om token är giltig och anspråken i token matchar de som konfigurerats för nyckeln (eller licensen).

När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med, utfärdaren är den säkra tokentjänsten som utfärdar token. Målgruppen (kallas ibland scope) beskriver avsikten med token eller den resurs som token auktoriserar åtkomst till. Medietjänstnyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

Mer information finns i följande artiklar:
- [Översikt över skydda innehåll](media-services-content-protection-overview.md)
- [Skydda med AES-128](media-services-protect-with-aes128.md)
- [Skydda med PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Leverera
### <a name="dynamic-packaging"></a><a id="dynamic_packaging"/>Dynamisk paketering
När du arbetar med Media Services rekommenderar vi att du kodar mezzaninfilerna till en adaptiv bithastighet MP4-uppsättning och sedan konverterar uppsättningen till önskat format med dynamisk [förpackning](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning
En StreamingEndpoint representerar en streamingtjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN (Content Delivery Network) för vidare distribution (Azure Media Services tillhandahåller nu Azure CDN-integreringen.) Den utgående strömmen från en slutpunktstjänst för direktuppspelning kan vara en livestream eller en video på begäran Tillgång i ditt Media Services-konto. Media Services-kunder väljer antingen en **Standard**-slutpunkt för direktuppspelning eller en eller flera **Premium**-slutpunkter för direktuppspelning, utifrån behov. Standardströmavslutpunkten för direktuppspelning är lämplig för de flesta arbetsbelastningar för direktuppspelning. 

Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Standardstreamingslutpunkter erbjuder flexibiliteten att leverera ditt innehåll till praktiskt taget alla enheter genom dynamisk paketering till HLS, MPEG-DASH och Smooth Streaming samt dynamisk kryptering för Microsoft PlayReady, Google Widevine, Apple Fairplay och AES128.  De skalar också från mycket liten till mycket stor publik med tusentals samtidiga tittare via Azure CDN-integrering. Om du har en avancerad arbetsbelastning eller om kraven på strömningskapacitet inte passar till standardmål för dataflöde för direktuppspelning eller om du vill styra streamingens kapacitet för att hantera växande bandbreddsbehov, rekommenderas att tillföra skalningsenheter (kallas även premiumstreamingenheter).

Vi rekommenderar att du använder dynamisk förpackning och/eller dynamisk kryptering.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Mer information finns i [detta](media-services-portal-manage-streaming-endpoints.md) avsnitt.

Som standard kan du ha upp till två slutpunkter för direktuppspelning i ditt Media Services-konto. Information om hur du begär en högre gräns finns i [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

Du faktureras bara när din StreamingEndpoint körs.

### <a name="asset-delivery-policy"></a>Policy för leverans av tillgångar
Ett av stegen i arbetsflödet för innehållsleverans i Media Services är att konfigurera [leveransprinciper för tillgångar](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)som du vill ska strömmas. Principen för tillgångsleverans talar om för Media Services hur du vill att din tillgång ska levereras: till vilket direktuppspelningsprotokoll som din tillgång ska paketeras dynamiskt (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt eller inte tillgång och hur (kuvert eller gemensam kryptering).

Om du har en lagringskrypterad tillgång, innan din tillgång kan strömmas, tar strömningsservern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen. Om du till exempel vill leverera din tillgång krypterad med AES-krypteringsnyckel (Advanced Encryption Standard) anger du principtypen till DynamicEnvelopeEncryption. Om du vill ta bort lagringskryptering och strömma tillgången i clear anger du principtypen till NoDynamicEncryption.

### <a name="progressive-download"></a>Progressiv nedladdning
Progressiv nedladdning kan du börja spela upp media innan hela filen har hämtats. Du kan bara hämta en MP4-fil successivt.

>[!NOTE]
>Du måste dekryptera krypterade tillgångar om du vill att de ska vara tillgängliga för progressiv nedladdning.

Om du vill ge användarna progressiva hämtningsadresser måste du först skapa en OnDemandOrigin-positionerare. När du skapar positioneraren får du bassökvägen till tillgången. Du måste sedan lägga till namnet på MP4-filen. Ett exempel:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Url:er för direktuppspelning
Strömma ditt innehåll till klienter. Om du vill ge användarna strömmande webbadresser måste du först skapa en OnDemandOrigin-positionerare. När du skapar positioneraren får du bassökvägen till den tillgång som innehåller det innehåll som du vill strömma. För att kunna strömma det här innehållet måste du dock ändra den här sökvägen ytterligare. Om du vill skapa en fullständig URL till manifestfilen för direktuppspelning måste du sammanfoga locatorns sökvägsvärde och filnamnet manifest (filename.ism). Lägg sedan till /Manifest och ett lämpligt format (om det behövs) i positionerarsökvägen.

Du kan också strömma ditt innehåll via en SSL-anslutning. Det gör du genom att se till att dina strömmande webbadresser börjar med HTTPS. För närvarande stöder AMS inte SSL med anpassade domäner.  

>[!NOTE]
>Du kan bara strömma via SSL om slutpunkten för direktuppspelning som du levererar ditt innehåll från skapades från efter den 10 september 2014. Om dina strömmande webbadresser baseras på slutpunkter för direktuppspelning som skapats efter den 10 september innehåller webbadressen "streaming.mediaservices.windows.net" (det nya formatet). Strömmande webbadresser som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte SSL. Om webbadressen är i det gamla formatet och du vill kunna strömma via SSL skapar du en ny slutpunkt för direktuppspelning. Använd webbadresser som skapats baserat på den nya slutpunkten för direktuppspelning för att strömma ditt innehåll via SSL.

I följande lista beskrivs olika direktuppspelningsformat och exempel:

* Smooth Streaming

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{strömmande slutpunktnamn-mediatjänster kontonamn}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

