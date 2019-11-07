---
title: Strömma live med Media Services v3
titleSuffix: Azure Media Services
description: Lär dig att strömma live med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 47d526ea410bc449c91ae4fb10913850c447f1b3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582636"
---
# <a name="tutorial-stream-live-with-media-services"></a>Självstudie: strömma live med Media Services

> [!NOTE]
> Även om självstudien använder [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -exempel är de allmänna stegen desamma för [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK](media-services-apis-overview.md#sdks): er som stöds.

I Azure Media Services ansvarar [livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En livehändelse tillhandahåller en slutpunkt (infognings-URL) som du sedan vidarebefordrar till en livekodare. Livehändelsen tar emot live indataströmmar från livekodaren och gör den tillgänglig för strömning via en eller flera [slutpunkter för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints). Livehändelser tillhandahåller också en slutpunkt för förhandsvisning (förhandsvisnings-URL) som du använder för att förhandsgranska och validera din ström inför vidare behandling och leverans. Den här självstudien visar hur du använder .NET Core för att skapa en **genomströmnings**typ av en livehändelse.

Självstudien visar hur du:

> [!div class="checklist"]
> * Ladda ned exempel appen som beskrivs i avsnittet.
> * Granska koden som utför direkt uppspelning.
> * Titta på händelsen med [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) på https://ampdemo.azureedge.net.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att kunna genomföra vägledningen:

- Installera Visual Studio Code eller Visual Studio.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md).<br/>Kom ihåg att komma ihåg de värden som du använder för resurs gruppens namn och Media Services konto namnet.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att få åtkomst till API: et.
- En kamera eller en enhet (till exempel en bärbar dator) som används för att sända en händelse.
- En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning. Dataströmmen måste anges i **RTMP**- eller **Smooth Streaming**-format.

> [!TIP]
> Var noga att du kollar igenom [Liveuppspelning med Media Services v3](live-streaming-overview.md) innan du fortsätter. 

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Livesändningsexemplet finns i [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp)-mappen.

Öppna [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter som du har fått från [att komma åt API: er](access-api-cli-how-to.md).

> [!IMPORTANT]
> I det här exemplet används ett unikt suffix för varje resurs. Om du avbryter fel sökningen eller avslutar appen utan att köra den via, kommer du att få flera Live-händelser i ditt konto. <br/>Se till att stoppa de livehändelser som körs. Annars **faktureras**du!

## <a name="examine-the-code-that-performs-live-streaming"></a>Granska den kod som utför du liveuppspelningen

Det här avsnittet går igenom funktionerna som definierades i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) för projektet *MediaV3LiveApp*.

Exemplet skapar ett unikt suffix för varje resurs så att du inte har några namn konflikter om du kör exemplet flera gånger utan att rensa.

> [!IMPORTANT]
> I det här exemplet används ett unikt suffix för varje resurs. Om du avbryter fel sökningen eller avslutar appen utan att köra den via, kommer du att få flera Live-händelser i ditt konto. <br/>
> Se till att stoppa de livehändelser som körs. Annars **faktureras**du!

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja med att använda Media Services-API:er med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Skapa en livehändelse

Det här avsnittet visar hur du skapar en **pass-through**-typ av livehändelse (LiveEventEncodingType inställd på None). Mer information om tillgängliga typer av Live-händelser finns i [Live Event types](live-events-outputs-concept.md#live-event-types). 
 
Några saker som du kanske vill ange när du skapar en Live-händelse är:

* Media Services plats.
* Strömningsprotokollet för livehändelsen (för närvarande stöds protokollen RTMP och Smooth Streaming).<br/>Du kan inte ändra alternativet protokoll när Live-händelsen eller dess associerade Live-utdata körs. Om du behöver olika protokoll kan du skapa separata Live-händelser för varje strömnings protokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser har angetts och det inte finns någon regel definition kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IpV4-adress med fyra nummer eller CIDR-adressintervall.
* När du skapar händelsen kan du ange att den ska autostartas. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Det innebär att faktureringen börjar så fort direkt händelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
* Ange "anpassad"-läget för att kunna förutsäga en URL. Mer detaljerad information finns i [Live Event](live-events-outputs-concept.md#live-event-ingest-urls)inmatnings-URL: er.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När Live-händelsen har skapats kan du hämta inmatnings-URL: er som du skickar till Live-kodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Hämta förhandsgransknings-URL:en

Använd previewEndpoint för att förhandsgranska och verifiera att indata från kodaren faktiskt tas emot.

> [!IMPORTANT]
> Se till att videon flödar till för hands versionen av URL: en innan du fortsätter.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Skapa och hantera livehändelser och liveutdata

När dataströmmen väl flödar till livehändelsen kan du påbörja strömningshändelsen genom att skapa en tillgång, liveutdata och en positionerare för direktuppspelning. Detta arkiverar dataströmmen och gör den tillgänglig för visning via strömningsslutpunkten.

#### <a name="create-an-asset"></a>Skapa en tillgång

Skapa en tillgång som kan användas av liveutdata.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Skapa liveutdata

Liveutdata startar när de skapas och avbryts när de tas bort. När du tar bort Live-utdata tar du inte bort den underliggande till gången och innehållet i till gången.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

> [!NOTE]
> När ditt Media Services-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering måste den slutpunkt för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

När du publicerar liveutdata-tillgången med hjälp av en positionerare för direktuppspelning, fortsätter livehändelsen (upp till DVR-fönstrets längd) att vara synlig tills positioneraren för direktuppspelning slutar att gälla eller tas bort, beroende på vilket som inträffar först.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Rensa upp resurserna på ditt Media Services-konto

Om du är klar med strömnings händelser och vill rensa de resurser som etablerades tidigare följer du stegen nedan:

* Stoppa sändningen av dataströmmen från kodaren.
* Stoppa livehändelsen. När Live-händelsen har stoppats debiteras inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. Om Live-händelsen är i stoppat tillstånd debiteras inga avgifter.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Titta på händelsen

Du kan titta på händelsen genom att kopiera den strömmande URL som du fick när du körde kod som beskrivs i skapa en strömmande positionerare. Du kan använda en valfri media spelare. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) är tillgängligt för att testa data strömmen på https://ampdemo.azureedge.net.

Livehändelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas. Även efter att du har stoppat och tagit bort händelsen kan användarna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort till gången. En till gång kan inte tas bort om den används av en händelse. händelsen måste tas bort först.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Om du lämnar livehändelsen igång så medför det kostnader. Obs! Om projektet/programmet kraschar eller stängs av någon anledning så kan det lämna livehändelsen igång i ett debiterbart tillstånd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Strömma filer](stream-files-tutorial-with-api.md)
 
