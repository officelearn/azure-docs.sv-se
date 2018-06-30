---
title: Använda AES Azure Media Services dynamisk kryptering | Microsoft Docs
description: Leverera ditt innehåll som krypterats med AES 128-bitars krypteringsnycklar med hjälp av Microsoft Azure Media Services. Media Services tillhandahåller också viktiga tjänsten som levererar krypteringsnycklar till behöriga användare. Det här avsnittet visar hur du dynamiskt kryptera med AES-128 och använda tjänsten nyckel.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: juliako
ms.openlocfilehash: 0da5bbee6d0d6401a35c301a8b35dc0efa77da7d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133032"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använd dynamiska AES-128-kryptering och tjänsten nyckel

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming krypteras med AES med 128-bitars krypteringsnycklar. Media Services tillhandahåller också viktiga tjänsten som levererar krypteringsnycklar till behöriga användare. Om du vill använda för Media Services att kryptera en tillgång, associera krypteringsnyckeln med StreamingLocator och även konfigurera innehåll viktiga principen. När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med hjälp av AES-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. För att avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Artikeln är baserad på den [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) exempel. Exemplet visar hur du skapar en kodning transformering som använder en inbyggd förinställningen för anpassningsbar bithastighet kodning och en en fil direkt från en [HTTPs Käll-URL](job-input-from-http-how-to.md). Utdatatillgången publiceras sedan med hjälp av AES (ClearKey)-kryptering. Utdata från exemplet är URL: en till Azure Media Player, inklusive både manifestet DASH och AES-token som behövs för att spela upp innehållet. Exemplet anger JWT-token upphör att gälla 1 timme. Du kan öppna en webbläsare och klistra in den resulterande URL Om du vill starta Azure Media Player demo-sida med URL och token som används för att du redan (i följande format: ``` https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.)

> [!NOTE]
> Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). Se [strömning protokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types), för att se vad klokt att kombinera.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra självstudien.

* Granska de [innehåll protection-översikt](content-protection-overview.md) artikel.
* Installera Visual Studio Code eller Visual Studio
* Skapa ett nytt Azure Media Services-konto, enligt beskrivningen i [denna Snabbstart](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter för att kunna använda API: er för Media Services genom att följa [åtkomst API: er](access-api-cli-how-to.md)

## <a name="download-code"></a>Hämta koden

Klona en GitHub-databas som innehåller fullständig .NET-exempel som beskrivs i det här avsnittet till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
”Kryptera med AES-128” exempel finns i den [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mapp.

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör programmet. Normalt du återanvänder befintliga resurser som transformeringar och principer (om befintlig resurs har nödvändiga konfigurationer). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av ditt kodningsjobb. När kodningen är klar publiceras utdatatillgången med hjälp av AES (ClearKey)-kryptering.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodning transformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. 

Innan du skapar en ny [transformera](https://docs.microsoft.com/rest/api/media/transforms), bör du först kontrollera om det redan finns en med hjälp av den **hämta** -metoden, som visas i koden som visas nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobbet

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I den här självstudiekursen skapar vi jobbets inmatning baserat på en fil som inhämtas direkt från en [HTTPs Käll-URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kodexemplet nedan visar hur du kontrollerar [jobbets](https://docs.microsoft.com/rest/api/media/jobs) status i tjänsten. Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkey-policy"></a>Skapa en princip för ContentKey

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du behöver skapa en princip för innehåll nycklar som konfigurerar hur innehållsnyckeln levereras för att avsluta klienter. Innehållsnyckeln är associerad med StreamingLocator. Media Services tillhandahåller också viktiga tjänsten som levererar krypteringsnycklar till behöriga användare. 

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll (i det här fallet med hjälp av AES-kryptering.) Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. För att avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="get-a-token"></a>Hämta en token
        
I den här självstudiekursen ange principens innehåll nycklar har en token begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i den [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT)-format och som är vad vi konfigurera i exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för tjänsten nyckeln har identifieraren för ContentKey i den. I det här exemplet vi anger inte en innehållsnyckel när du skapar StreamingLocator, systemet skapar ett slumpmässigt för oss. För att generera testet token, vi måste få ContentKeyId i ContentKeyIdentifierClaim anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="create-a-streaminglocator"></a>Skapa en StreamingLocator

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra detta i två steg: Först skapar du en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) och därefter skapar du de strömmande URL:er som klienterna ska använda. 

Processen att skapa en **StreamingLocator** kallas för publicering. Som standard kan din **StreamingLocator** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) måste du ange önskat **StreamingPolicyName**. I den här kursen använder en av PredefinedStreamingPolicies som talar om hur du publicerar innehåll för strömning för Azure Media Services. I det här exemplet används AES Envelope-kryptering (även kallat ClearKey kryptering eftersom nyckeln levereras till klienten uppspelning via HTTPS och inte en DRM-licens).

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="build-a-dash-streaming-url"></a>Skapa ett STRECK strömnings-URL

Nu när den [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du strömmande URL: er. Om du vill skapa en URL måste du sammanfoga värdnamnet [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **StreamingLocator**. I det här exemplet används *standardvärdet* **StreamingEndpoint**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde* för **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  Följande kod tar exempelvis bort Jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Nästa steg

[Översikt](content-protection-overview.md)