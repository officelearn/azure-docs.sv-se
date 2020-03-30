---
title: DRM-krypterings- och licensleveranstjänst för Azure Media Services
titleSuffix: Azure Media Services
description: Lär dig hur du använder DRM-tjänsten för dynamisk kryptering och licensleverans för att leverera strömmar krypterade med Microsoft PlayReady-, Google Widevine- eller Apple FairPlay-licenser.
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
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086726"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Självstudiekurs: Använd DRM-tjänsten för dynamisk kryptering och licensleverans

> [!NOTE]
> Även om den här självstudien använder [exemplen .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) är de allmänna stegen desamma för [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK-filer](media-services-apis-overview.md#sdks)som stöds .

Du kan använda Azure Media Services till att leverera strömmar som krypterats med Microsoft PlayReady, Google Widevine eller Apple FairPlay-licenser. Detaljerad förklaring finns i [Innehållsskydd med dynamisk kryptering](content-protection-overview.md).

Media Services tillhandahåller också en tjänst för att leverera PlayReady-, Widevine- och FairPlay DRM-licenser. När en användare begär DRM-skyddat innehåll begär spelarappen en licens från licenstjänsten för Media Services. Om spelarappen är auktoriserad utfärdar licenstjänsten Media Services en licens till spelaren. En licens innehåller en krypteringsnyckel som kan användas av klientspelaren för att dekryptera och direktuppspela innehållet.

Den här artikeln bygger på exemplet om [kryptering med DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Exemplet som beskrivs i den här artikeln ger följande resultat:

![AMS med DRM-skyddad video i Azure Media Player](./media/protect-with-drm/ams_player.png)

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa en kodningstransformning.
> * Ställ in den signeringsnyckel som användes för att verifiera din token.
> * Ange krav på innehållsnyckelprincipen.
> * Skapa en StreamingLocator med den angivna direktuppspelningsprincipen.
> * Skapa en URL som används för att spela upp filen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Följande krävs för att kunna genomföra vägledningen:

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Granska [designens multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md).
* Installera Visual Studio Code eller Visual Studio.
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter som krävs för att använda API:er för Media Services med hjälp av [åtkomst till API:er](access-api-cli-how-to.md)
* Ange lämpliga värden i appkonfigurationsfilen (appsettings.json).

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Du hittar exemplet om att kryptera med DRM i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör appen. Vanligtvis ska du återanvända befintliga resurser som transformeringar och principer (om befintlig resurs har nödvändiga konfigurationer).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET skapar du ett **AzureMediaServicesClient-objekt.** När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](assets-concept.md) lagrar resultatet av kodningsjobbet.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](transforms-jobs-concept.md), måste du ange vilken utdata du vill att den ska skapa. Parametern som `transformOutput` krävs är ett objekt, som visas i koden nedan. Varje TransformOutput innehåller en **Förinställning**. I Förinställning finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad TransformOutput. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon till en automatiskt upparglad bithastighetsstege (bitrate-upplösningspar) baserat på indataupplösningen och bithastigheten, och producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje bitrate-upplösningspar. 

Innan du skapar en ny **transformering** bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet **Transformering** receptet och ett [Jobb](transforms-jobs-concept.md) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indatavideon och platsen för utdata.

I den här självstudien skapar vi jobbets indata baserat på en fil som matas in direkt från en [HTTPs-käll-URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar lite tid att slutföra. När den gör det vill du bli meddelad. Kodexemplet nedan visar hur du söker efter tjänstens status för **jobbet**. Avsökning är inte en rekommenderad metod för produktionsappar på grund av potentiell svarstid. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har stött på ett **Error** fel får du feltillståndet. Om jobbet håller på att avbrytas får du **avbryta** och **avbryta när** det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Skapa en innehållsnyckelprincip

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en [policy för innehållsnyckel](content-key-policy-concept.md) när du krypterar ditt innehåll med en DRM. Principen konfigurerar hur innehållsnyckeln levereras till slutklienter. Innehållsnyckeln är associerad med en streamingpositionerare. Media Services tillhandahåller också nyckelleveranstjänsten som levererar krypteringsnycklar och licenser till behöriga användare.

Du måste ange kraven (begränsningarna) för **innehållsnyckelprincipen** som måste uppfyllas för att leverera nycklar med den angivna konfigurationen. I det här exemplet anger vi följande konfigurationer och krav:

* Konfiguration

    Licenserna för [PlayReady](playready-license-template-overview.md) och [Widevine](widevine-license-template-overview.md) är konfigurerade så att de kan levereras av Media Services-licensleveranstjänsten. Även om den här exempelappen inte konfigurerar [FairPlay-licensen](fairplay-license-overview.md) innehåller den en metod som du kan använda för att konfigurera FairPlay. Du kan lägga till FairPlay-konfiguration som ett annat alternativ.

* Begränsning

    Appen anger en JSON Web Token (JWT) token typ begränsning på principen.

När en ström begärs av en spelare krypterar Media Services innehållet med den angivna nyckeln. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du gör videon tillgänglig i två steg:

1. Skapa en [streamingpositionerare](streaming-locators-concept.md).
2. Skapa webbadresserna för direktuppspelning som klienter kan använda.

Processen att skapa **streaming locator** kallas publicering. Som standard är **strömningspositioneraren** giltig direkt efter att du har ringt API-anropen. Den varar tills den tas bort, såvida du inte konfigurerar de valfria start- och sluttiderna.

När du skapar en **streamingpositionerare**måste `StreamingPolicyName`du ange önskad . I den här självstudien använder vi en av de fördefinierade streamingprinciperna, som talar om för Azure Media Services hur du publicerar innehållet för direktuppspelning. I det här exemplet ställer vi in StreamingLocator.StreamingPolicyName för principen ”Predefined_MultiDrmCencStreaming”. PlayReady- och Widevine-krypteringarna tillämpas och nyckeln levereras till uppspelningsklienten baserat på de konfigurerade DRM-licenserna. Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Om du använder en anpassad [strömningsprincip](streaming-policy-concept.md) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken

I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i [JWT-format](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) och det är vad vi konfigurerar i exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för nyckelleveranstjänst måste innehålla identifieraren för ContentKey. I exemplet anger vi inte en innehållsnyckel när vi skapar streaming locator, systemet skapar en slumpmässig en för oss. För att generera testtoken måste vi hämta ContentKeyId för att lägga in ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Skapa en url för direktuppspelning

Nu när [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelningswebbadresserna. Om du vill skapa en URL måste du sammanfoga [streamingendpoint-värdnamnet](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **för strömmar.** I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för * **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

När du kör appen visas följande skärm:

![Skydda med DRM](./media/protect-with-drm/playready_encrypted_url.png)

Du kan öppna en webbläsare och klistra in den webbadress som bildas för att starta Azure Media Player-demosidan där webbadressen en och token redan är ifyllda.

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom objekt som du planerar att återanvända (vanligtvis ska du återanvända transformeringar, StreamingLocators och så vidare). Om du vill att kontot ska vara rent efter att du har experimenterat tar du bort de resurser som du inte tänker återanvända. Följande kod tar till exempel bort jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

Checka ut

> [!div class="nextstepaction"]
> [Skydda med AES-128](protect-with-aes128.md)
