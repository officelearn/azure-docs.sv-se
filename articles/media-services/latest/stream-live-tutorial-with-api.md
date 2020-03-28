---
title: Streama live med Media Services v3
titleSuffix: Azure Media Services
description: Läs om hur du streamar live med Azure Media Services v3.
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
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472024"
---
# <a name="tutorial-stream-live-with-media-services"></a>Självstudiekurs: Streama live med medietjänster

> [!NOTE]
> Även om självstudien använder [.NET SDK-exempel](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) är de allmänna stegen desamma för [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK-filer](media-services-apis-overview.md#sdks)som stöds .

I Azure Media Services ansvarar [livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En livehändelse tillhandahåller en slutpunkt (infognings-URL) som du sedan vidarebefordrar till en livekodare. Livehändelsen tar emot live indataströmmar från livekodaren och gör den tillgänglig för strömning via en eller flera [slutpunkter för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints). Livehändelser tillhandahåller också en slutpunkt för förhandsvisning (förhandsvisnings-URL) som du använder för att förhandsgranska och validera din ström inför vidare behandling och leverans. Den här självstudien visar hur du använder .NET Core för att skapa en **genomströmnings**typ av en livehändelse.

Självstudien visar hur du:

> [!div class="checklist"]
> * Hämta exempelappen som beskrivs i avsnittet.
> * Undersök koden som utför livestreaming.
> * Titta på evenemanget med [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)Azure Media [Player](https://amp.azure.net/libs/amp/latest/docs/index.html) på .
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Följande krävs för att kunna genomföra vägledningen:

- Installera Visual Studio Code eller Visual Studio.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md).<br/>Se till att komma ihåg de värden du använder för resursgruppens namn och Media Services-kontonamn.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att komma åt API:et.
- En kamera eller en enhet (som en bärbar dator) som används för att sända en händelse.
- En lokal live-kodare som konverterar signaler från kameran till strömmar som skickas till tjänsten Livestreaming i Media Services, se [rekommenderade lokala live-kodare](recommended-on-premises-live-encoders.md). Dataströmmen måste anges i **RTMP**- eller **Smooth Streaming**-format.

> [!TIP]
> Var noga att du kollar igenom [Liveuppspelning med Media Services v3](live-streaming-overview.md) innan du fortsätter. 

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Livesändningsexemplet finns i [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp)-mappen.

Öppna [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter du fick [från åtkomst till API:er](access-api-cli-how-to.md).

> [!IMPORTANT]
> Det här exemplet använder ett unikt suffix för varje resurs. Om du avbryter felsökningen eller avslutar appen utan att köra igenom den får du flera livehändelser i ditt konto. <br/>Se till att stoppa de livehändelser som körs. Annars kommer du att **faktureras!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Granska den kod som utför du liveuppspelningen

Det här avsnittet går igenom funktionerna som definierades i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) för projektet *MediaV3LiveApp*.

Exemplet skapar ett unikt suffix för varje resurs så att du inte har namnkollisioner om du kör exemplet flera gånger utan att rensa.

> [!IMPORTANT]
> Det här exemplet använder ett unikt suffix för varje resurs. Om du avbryter felsökningen eller avslutar appen utan att köra igenom den får du flera livehändelser i ditt konto. <br/>
> Se till att stoppa de livehändelser som körs. Annars kommer du att **faktureras!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapar funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Skapa en livehändelse

Det här avsnittet visar hur du skapar en **pass-through**-typ av livehändelse (LiveEventEncodingType inställd på None). Mer information om tillgängliga typer av livehändelser finns i Typer av [livehändelser](live-events-outputs-concept.md#live-event-types). 
 
Några saker som du kanske vill ange när du skapar livehändelsen är:

* Plats för medietjänster.
* Strömningsprotokollet för livehändelsen (för närvarande stöds protokollen RTMP och Smooth Streaming).<br/>Du kan inte ändra protokollalternativet medan Live-händelsen eller dess associerade liveutdata körs. Om du behöver olika protokoll skapar du separat Live Event för varje direktuppspelningsprotokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser anges och det inte finns någon regeldefinition tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IPV4-adress med fyra nummer eller CIDR-adressintervall.
* När du skapar händelsen kan du ange att den ska startas automatiskt. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Det innebär att faktureringen startar så snart livehändelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
* För att en webbadress ska vara prediktiv ställer du in "fåfänga"-läget. Detaljerad information finns i [Webbadresser för livehändelseinta.](live-events-outputs-concept.md#live-event-ingest-urls)

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När Live-händelsen har skapats kan du få intag av webbadresser som du tillhandahåller den aktiva kodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Hämta förhandsgransknings-URL:en

Använd previewEndpoint för att förhandsgranska och verifiera att indata från kodaren faktiskt tas emot.

> [!IMPORTANT]
> Kontrollera att videon flödar till förhandsgranskningsadressen innan du fortsätter.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Skapa och hantera livehändelser och liveutdata

När dataströmmen väl flödar till livehändelsen kan du påbörja strömningshändelsen genom att skapa en tillgång, liveutdata och en positionerare för direktuppspelning. Detta arkiverar dataströmmen och gör den tillgänglig för visning via strömningsslutpunkten.

#### <a name="create-an-asset"></a>Skapa en tillgång

Skapa en tillgång som kan användas av liveutdata.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Skapa liveutdata

Liveutdata startar när de skapas och avbryts när de tas bort. När du tar bort Live Output tar du inte bort den underliggande tillgången och innehållet i tillgången.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

> [!NOTE]
> När ditt Media Services-konto skapas läggs en **standardslutpunkt** för direktuppspelning till ditt konto i tillståndet **Stoppad.** Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill strömma innehåll från vara i **körläge.**

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

Om du är klar med direktuppspelningshändelser och vill rensa de resurser som etablerats tidigare följer du följande procedur:

* Stoppa sändningen av dataströmmen från kodaren.
* Stoppa livehändelsen. När Live-händelsen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. Om Live-händelsen är i ett stoppat tillstånd medför den inga avgifter.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Titta på händelsen

Om du vill titta på händelsen kopierar du den direktuppspelnings-URL:en som du fick när du körde kod som beskrivs i Skapa en streamingpositionerare. Du kan använda valfri mediaspelare. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) är tillgängligt https://ampdemo.azureedge.netför att testa din stream på .

Livehändelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas. Även efter att du har stannat och tagit bort händelsen kan användarna strömma det arkiverade innehållet som en video på begäran så länge du inte tar bort tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Om du lämnar livehändelsen igång så medför det kostnader. Obs! Om projektet/programmet kraschar eller stängs av någon anledning så kan det lämna livehändelsen igång i ett debiterbart tillstånd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

[Strömma filer](stream-files-tutorial-with-api.md)
 
