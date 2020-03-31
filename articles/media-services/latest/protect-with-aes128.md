---
title: Kryptera video med AES-128
titleSuffix: Azure Media Services
description: Lär dig hur du krypterar video med AES 128-bitars kryptering och hur du använder nyckelleveranstjänsten i Azure Media Services.
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
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974180"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Självstudiekurs: Kryptera video med AES-128 och använd nyckelleveranstjänsten

> [!NOTE]
> Även om självstudien använder [exemplen .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) är de allmänna stegen desamma för [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK-filer](media-services-apis-overview.md#sdks)som stöds .

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS), MPEG-DASH och Smooth Streaming krypterat med AES med hjälp av 128-bitars krypteringsnycklar. Media Services tillhandahåller också den viktiga leveranstjänsten som levererar krypteringsnycklar till behöriga användare. Om du vill att Media Services ska kryptera videon dynamiskt associerar du krypteringsnyckeln med en streamingpositionerare och konfigurerar även innehållsnyckelprincipen. När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med AES-128. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](content-protection-overview.md#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera. Se också [Hur du skyddar med DRM](protect-with-drm.md).

Utdata från exemplet den här artikeln innehåller en URL till Azure Media Player, manifest-URL och AES-token som behövs för att spela upp innehållet. Exemplet anger att JWT-token (JWT) (JWT) (JWT) ska upphöra att gälla till 1 timme. Du kan öppna en webbläsare och klistra in den resulterande URL:en för att starta demosidan ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```för Azure Media Player med url:en och token ifylld för dig som redan är i följande format: .

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Ladda ner [encryptwithAES-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) som beskrivs i artikeln.
> * Börja använda API:er för Media Services med .NET SDK.
> * Skapa en utdatatillgång.
> * Skapa en kodningstransformning.
> * Skicka in ett jobb.
> * Vänta tills jobbet är klart.
> * Skapa en innehållsnyckelprincip
> * Konfigurera principen för att använda JWT-tokenbegränsning.
> * Skapa en streaming locator.
> * Konfigurera streamingpositioneraren för att kryptera videon med AES (ClearKey).
> * Skaffa en testtoken.
> * Skapa en url för direktuppspelning.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Följande krävs för att kunna genomföra självstudien.

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Installera Visual Studio Code eller Visual Studio.
* [Skapa ett Media Services-konto](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter som behövs för att använda Api:er för Media Services genom att följa [Access-API:er](access-api-cli-how-to.md).

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Exemplet "Kryptera med AES-128" finns i mappen [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör appen. Vanligtvis ska du återanvända befintliga resurser som transformeringar och principer (om befintlig resurs har nödvändiga konfigurationer).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET skapar du ett **AzureMediaServicesClient-objekt.** När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av kodningsjobbet.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon till en automatiskt upparglad bithastighetsstege (bitrate-upplösningspar) baserat på indataupplösningen och bithastigheten och producerar sedan ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje bitrate-upplösningspar.

Innan du skapar en ny [transformering](https://docs.microsoft.com/rest/api/media/transforms)kontrollerar du först om det redan finns en med metoden **Hämta,** som visas i koden som följer. I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indatavideon och platsen för utdata.

I den här självstudien skapar vi jobbets indata baserat på en fil som matas in direkt från en [HTTPs-käll-URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar lite tid att slutföra. När den gör det vill du bli meddelad. Kodexemplet nedan visar hur du söker efter tjänstens status för [jobbet](https://docs.microsoft.com/rest/api/media/jobs). Avsökning är inte en rekommenderad metod för produktionsappar på grund av potentiell svarstid. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Mer information finns i [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har stött på ett **Error** fel får du feltillståndet. Om jobbet håller på att avbrytas får du **avbryta** och **avbryta när** det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Skapa en innehållsnyckelprincip

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en **princip för innehållsnyckel** som konfigurerar hur innehållsnyckeln levereras till slutklienter. Innehållsnyckeln är associerad med **streamingpositioneraren**. Media Services tillhandahåller också den viktiga leveranstjänsten som levererar krypteringsnycklar till behöriga användare.

När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll (i det här fallet med hjälp av AES-kryptering.) För att dekryptera strömmen begär spelaren nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du gör videon tillgänglig i två steg:

1. Skapa en [streamingpositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Skapa webbadresserna för direktuppspelning som klienter kan använda.

Processen att skapa **streaming locator** kallas publicering. Som standard är **strömningspositioneraren** giltig direkt efter att du har ringt API-anropen. Den varar tills den tas bort, såvida du inte konfigurerar de valfria start- och sluttiderna.

När du skapar en [streamingpositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators)måste du ange önskat **StreamingPolicyName**. I den här självstudien använder vi en av PredefinedStreamingPolicies, som talar om för Azure Media Services hur du publicerar innehållet för direktuppspelning. I det här exemplet tillämpas AES-kuvertkrypteringen (den här krypteringen kallas även ClearKey-kryptering eftersom nyckeln levereras till uppspelningsklienten via HTTPS och inte en DRM-licens).

> [!IMPORTANT]
> När du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och återanvända dem för dina streamingpositionerare när samma krypteringsalternativ och protokoll behövs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje streaming locator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken

I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i [JWT-format](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) och det är vad vi konfigurerar i exemplet.

ContentKeyIdentifierClaim används i **innehållsnyckelprincipen**, vilket innebär att den token som visas för tjänsten Nyckelleverans måste ha identifieraren för innehållsnyckeln i den. I exemplet angav vi inte en innehållsnyckel när vi skapade streaming locator, systemet skapade en slumpmässig en för oss. För att generera testtoken måste vi hämta ContentKeyId för att lägga in ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Skapa en DASH-uppspelningsadress

Nu när [streaming locator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du få strömmande webbadresser. Om du vill skapa en URL måste du sammanfoga [streamingendpoint-värdnamnet](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **för strömmar.** I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för * **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom objekt som du planerar att återanvända (vanligtvis ska du återanvända transformeringar, strömningspositionerare och så vidare). Om du vill att kontot ska vara rent efter att du har experimenterat tar du bort de resurser som du inte tänker återanvända. Följande kod tar till exempel bort jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skydda med DRM](protect-with-drm.md)
