---
title: Använda leveranstjänst för dynamisk DRM-krypteringstjänst med Azure Media Services| Microsoft Docs
description: Du kan använda Azure Media Services till att leverera strömmar som krypterats med Microsoft PlayReady, Google Widevine eller Apple FairPlay-licenser.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f53ae122e9888f3e537a3557b6ac5bd76856c2eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995878"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Använda leveranstjänst för dynamisk DRM-krypteringstjänst

Du kan använda Azure Media Services när du levererar MPEG-DASH-, Smooth Streaming- och HTTP-Live-Streaming-dataströmmar som skyddas med [PlayReady DRM](https://www.microsoft.com/playready/overview/). Du kan också använda Media Services när du levererar krypterade DASH-strömmar med DRM-licenser av typen **Google Widevine**. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC). Med Media Services kan du dessutom kryptera HLS-innehåll med **Apple FairPlay** (AES-128 CBC). 

Media Services är vidare en tjänst för att leverera PlayReady-, Widevine och FairPlay-DRM-licenser. När en användare begär ett DRM-skyddat innehåll, begär spelarappen en licens från Media Services-licenstjänsten. Om spelarappen är auktoriserad utfärdar Media Services-licensen en licens till spelaren. En licens innehåller en krypteringsnyckel som kan användas av klientspelaren för att dekryptera och direktuppspela innehållet.

Den här artikeln bygger på exemplet om [kryptering med DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). Vi visar bland annat hur du:

* Skapa en kodtransformering som använder ett inbyggt förval för adaptiv kodning av bithastighet och mata in en fil direkt från en [HTTP-källwebbadress](job-input-from-http-how-to.md).
* Ställ in den signeringsnyckel som användes för att verifiera din token.
* Ställ in de krav (begränsningar) på innehållsnyckelprincipen som måste uppfyllas för att leverera nycklar med den specificerade konfigurationen. 

    * Konfiguration 
    
        I det här exemplet är licenserna för [PlayReady](playready-license-template-overview.md) och [Widevine](widevine-license-template-overview.md) konfigurerade så att de kan levereras av Media Services-licensleveranstjänsten. Det här exempelprogrammet konfigurerar inte [FairPlay](fairplay-license-overview.md)-licensen men det innehåller en metod som du kan använda för att konfigurera FairPlay. Om du vill kan du lägga till FairPlay-konfiguration som ytterligare ett alternativ.

    * Begränsning

        Programmet anger en JWT-tokentypbegränsning för principen.

* Skapa en StreamingLocator för den angivna tillgången och med det angivna direktuppspelningspolicynamnet. I det här fallet används den fördefinierade principen. Två nycklar anges på StreamingLocator: AES-128 (kuvert) och CENC (PlayReady och Widevine).  
    
    När du har skapat en StreamingLocator är utdatatillgången publicerad och tillgänglig för klienter för uppspelning.

    > [!NOTE]
    > Kontrollera att slutpunkten du vill spela upp innehåll från körs.

* Skapa en webbadress till Azure Media Player som innehåller både det DASH-manifest och den PlayReady-token som behövs för att spela upp PlayReady-krypterat innehåll. I exemplet är utgången för token satt till en timme. 

    Du kan öppna en webbläsare och klistra in den webbadress som bildas för att starta Azure Media Player-demosidan där webbadressen en och token redan är ifyllda.  

    ![Skydda med DRM](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera.

Exemplet som beskrivs i den här artikeln ger följande resultat:

![AMS med DRM-skyddad video](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att kunna genomföra självstudien.

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Gå igenom artikeln om att [utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
* Installera Visual Studio Code eller Visual Studio
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter som krävs för att använda API:er för Media Services med hjälp av [åtkomst till API:er](access-api-cli-how-to.md)
* Ställ in lämpliga värden i programmets konfigurationsfil (appsettings.json).

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Du hittar exemplet om att kryptera med DRM i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör programmet. Normalt återanvänder du befintliga resurser som transformeringar och policyer (om befintlig resurs har nödvändiga konfigurationer). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja med att använda Media Services-API:er med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av ditt kodningsjobb.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. 

Innan du skapar en ny [transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I den här självstudiekursen skapar vi jobbets indata baserat på en fil som matas direkt från en [HTTP-källwebbadress](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kodexemplet nedan visar hur du kontrollerar [jobbets](https://docs.microsoft.com/rest/api/media/jobs) status i tjänsten. Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobbet** går vanligtvis igenom följande tillstånd: **Schemalagd**, **I kö**, **Bearbetar**, **Slutfört** (sluttillstånd). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Skapa ContentKeyPolicy

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du behöver skapa en innehållsnyckelprincip som konfigurerar hur innehållsnyckeln levereras till slutklienter. Innehållsnyckeln är kopplad till StreamingLocator. Media Services tillhandahåller också nyckelleveranstjänsten som levererar krypteringsnycklar och licenser till behöriga användare. 

Du måste ange krav (begränsningar) på innehållsnyckelprincipen som måste uppfyllas för att leverera nycklar med den angivna konfigurationen. I det här exemplet anger vi följande konfigurationer och krav:

* Konfiguration 

    Licenserna för [PlayReady](playready-license-template-overview.md) och [Widevine](widevine-license-template-overview.md) är konfigurerade så att de kan levereras av Media Services-licensleveranstjänsten. Det här exempelprogrammet konfigurerar inte [FairPlay](fairplay-license-overview.md)-licensen men det innehåller en metod som du kan använda för att konfigurera FairPlay. Du kan lägga till FairPlay-konfiguration som ytterligare ett alternativ.

* Begränsning

    Programmet anger en JWT-tokentypbegränsning för principen.

När en ström begärs av en spelare krypterar Media Services innehållet med den angivna nyckeln. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Skapa en StreamingLocator

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra det i två steg: 

1. Skapa en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Skapa webbadresserna för direktuppspelning som klienter kan använda. 

Processen att skapa **StreamingLocator** kallas för publicering. Som standard kan din **StreamingLocator** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) behöver du ange önskat **StreamingPolicyName**. I den här självstudien använder vi en av de fördefinierade StreamingPolicy som talar om för Azure Media Services hur innehåll för direktuppspelning ska publiceras. I det här exemplet ställer vi in StreamingLocator.StreamingPolicyName för principen ”Predefined_MultiDrmCencStreaming”. Den här principen indikerar att du vill att två innehållsnycklar (kuvert och CENC) ska skapas och ställas in för positioneraren. Krypteringarna för kuvert, PlayReady och Widevine tillämpas därför (nyckeln levereras till uppspelningsklienten utifrån de konfigurerade DRM-licenserna). Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken
        
I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) och det är det vi anger i det här exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för nyckelleveranstjänst måste innehålla identifieraren för ContentKey. I det här exemplet vi anger inte någon innehållsnyckel när vi skapar StreamingLocator. Systemet skapar en slumpmässigt åt oss. För att generera testtoken måste vi få ContentKeyId och placera det i ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Skapa en DASH-uppspelningsadress

Nu när [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelningswebbadresserna. Om du vill skapa en URL måste du sammanfoga värdnamnet [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **StreamingLocator**. I det här exemplet används *standardvärdet* **StreamingEndpoint**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde* för **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  Följande kod tar exempelvis bort Jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Nästa steg

Se hur du [skyddar med AES-128](protect-with-aes128.md)
