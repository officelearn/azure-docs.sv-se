---
title: Använda AES Azure Media Services dynamisk kryptering | Microsoft Docs
description: Leverera ditt innehåll som krypteras med AES 128-bitars krypteringsnycklar med hjälp av Microsoft Azure Media Services. Media Services tillhandahåller också nyckelleveranstjänst som levererar krypteringsnycklar till behöriga användare. Det här avsnittet visar hur du dynamiskt kryptera med AES-128 och använda nyckelleveranstjänst.
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
ms.openlocfilehash: da2df60e3111055729bbae2c6684ccbb9671272e
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007871"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming som krypterats med AES med hjälp av 128-bitars krypteringsnycklar. Media Services tillhandahåller också nyckelleveranstjänst som levererar krypteringsnycklar till behöriga användare. Om du vill för Media Services för att kryptera en tillgång kan du associera krypteringsnyckeln med StreamingLocator och även ange innehåll viktiga principen. När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från nyckelleveranstjänst. Tjänsten utvärderar innehåll viktiga principen som du angav för nyckeln för att avgöra om användaren har behörighet att hämta nyckel.

Den här artikeln är baserad på den [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) exemplet. Exemplet visar hur du skapar en kodning transformeringen som använder en inbyggd förinställning för kodning med anpassningsbar bithastighet och matar in en fil direkt från en [HTTPs Käll-URL](job-input-from-http-how-to.md). Utdatatillgången publiceras sedan med hjälp av AES (ClearKey)-kryptering. Utdata från exemplet är en URL till Azure Media Player, inklusive både manifestet DASH och AES-token som behövs för att spela upp innehållet. Exemplet utgångsdatumet för JWT-token till 1 timme. Du kan öppna en webbläsare och klistra in URL som bildas för att starta Azure Media Player demo-sida med URL: en och token som används för att redan (i följande format: ``` https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.)

> [!NOTE]
> Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). Se [Streaming protokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types), för att se vad är det bra att kombinera.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra självstudien.

* Granska den [Content protection översikt](content-protection-overview.md) artikeln.
* Installera Visual Studio Code eller Visual Studio
* Skapa ett nytt Azure Media Services-konto, enligt beskrivningen i [snabbstarten](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter som krävs för att använda API: er för Media Services genom att följa [API: er för åtkomst](access-api-cli-how-to.md)

## <a name="download-code"></a>Ladda ned koden

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET-exemplet som beskrivs i det här avsnittet för din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Du hittar exemplet ”kryptera med AES-128” i den [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mapp.

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör programmet. Normalt kan du återanvänder befintliga resurser som transformeringar och principer (om befintlig resurs har nödvändiga konfigurationer). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja med att använda Media Services-API:er med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av ditt kodningsjobb. När kodningen är klar, publiceras utdatatillgången med hjälp av AES (ClearKey)-kryptering.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodning transformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. 

Innan du skapar en ny [transformera](https://docs.microsoft.com/rest/api/media/transforms), bör du först kontrollera om det redan finns med hjälp av den **hämta** metod, som visas i den kod som följer.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobbet

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I den här självstudiekursen skapar vi jobbets indata som baseras på en fil som matas direkt från en [HTTPs Käll-URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kodexemplet nedan visar hur du kontrollerar [jobbets](https://docs.microsoft.com/rest/api/media/jobs) status i tjänsten. Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkey-policy"></a>Skapa en princip för ContentKey

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en princip för innehåll nyckel som konfigurerar hur innehållsnyckeln levereras om du vill avsluta klienter. Innehållsnyckeln är associerad med StreamingLocator. Media Services tillhandahåller också nyckelleveranstjänst som levererar krypteringsnycklar till behöriga användare. 

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll (i det här fallet med hjälp av AES-kryptering.) Om du vill dekryptera dataströmmen begär spelaren nyckeln från nyckelleveranstjänst. Tjänsten utvärderar innehåll viktiga principen som du angav för nyckeln för att avgöra om användaren har behörighet att hämta nyckel.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="get-a-token"></a>Hämta en token
        
I de här självstudierna ange principens innehåll nyckel har en token begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i den [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT)-format och som är vad vi konfigurerar i exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för tjänsten nyckeln måste ha ContentKey identifierare i den. I det här exemplet vi anger inte en innehållsnyckel när du skapar StreamingLocator, systemet skapar ett slumpmässigt för oss. För att generera testet token, vi måste få ContentKeyId att placera i ContentKeyIdentifierClaim anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="create-a-streaminglocator"></a>Skapa en StreamingLocator

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra detta i två steg: Först skapar du en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) och därefter skapar du de strömmande URL:er som klienterna ska använda. 

Processen att skapa en **StreamingLocator** kallas för publicering. Som standard kan din **StreamingLocator** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) måste du ange önskat **StreamingPolicyName**. I den här självstudien använder en av PredefinedStreamingPolicies som talar om hur du publicerar innehåll för direktuppspelning för Azure Media Services. I det här exemplet används AES Envelope-kryptering (även kallat ClearKey kryptering eftersom nyckeln levereras till klienten uppspelning via HTTPS och inte en DRM-licens).

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="build-a-dash-streaming-url"></a>Skapa ett bindestreck strömnings-URL

Nu när den [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du få strömmande URL: er. Om du vill skapa en URL måste du sammanfoga värdnamnet [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **StreamingLocator**. I det här exemplet används *standardvärdet* **StreamingEndpoint**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde* för **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  Följande kod tar exempelvis bort Jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Nästa steg

[Översikt](content-protection-overview.md)