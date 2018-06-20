---
title: Ladda upp, koda och strömma med Azure Media Services | Microsoft Docs
description: Följ stegen i den här självstudien för att ladda upp en fil, koda videon och strömma ditt innehåll med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 0216a95a5209f5545b34e446904b3215950c6fbc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638117"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Självstudie: Ladda upp, koda och strömma videor med API:er

Med Media Services kan du koda mediefilerna till format som kan spelas upp på en mängd olika webbläsare och enheter. Du vill kanske strömma ditt innehåll med Apples HLS- eller MPEG DASH-format. Innan du strömmar ska du kontrollera din fil med digitalt högkvalitativt medieinnehåll. Vägledning om kodning finns i [Kodningskoncept](encoding-concept.md). Den här självstudiekursen laddar upp en lokal videofil och kodar den överförda filen. Du kan också koda innehåll som du gör tillgänglita via en HTTPS-URL. Mer information finns i [Skapa jobbindata från en HTTP(s)-URL](job-input-from-http-how-to.md).

![Spela upp videon](./media/stream-files-tutorial-with-api/final-video.png)

I den här självstudiekursen lär du dig att:    

> [!div class="checklist"]
> * Skapa ett Media Services-konto
> * Åtkomst till Media Services API
> * Konfigurera exempelappen
> * Granska koden som överför, kodar och strömmar
> * Kör appen
> * Testa strömnings-URL:en
> * Rensa resurser

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Du hittar exemplet i mappen [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Granska koden som överför, kodar och strömmar

Det här avsnittet går igenom funktionerna som definierats i filen [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) för projektet *UploadEncodeAndStreamFiles*.

Exemplet utför följande åtgärder:

1. Skapar en ny transformering (först kontrolleras om den angivna transformeringen finns). 
2. Skapa en utdatatillgång som används som kodningsjobbets utdata.
3. Skapa en indatatillgång och laddar upp den angivna lokala videofilen till den. TIllgången används som jobbets indata. 
4. Skickar kodningsjobbet med de indata och utdata som skapades.
5. Kontrollerar jobbets status.
6. Skapar en StreamingLocator.
7. Skapar strömnings-URL:er.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapar funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Skapa en indatatillgång och ladda upp en lokal fil till den 

Funktionen **CreateInputAsset** skapar en ny [indatatillgång](https://docs.microsoft.com/rest/api/media/assets) och laddar upp den angivna lokala videofilen till den. Tillgången används som indata för kodningsjobbet. I Media Services v3 kan indata till ett jobb antingen vara en tillgång eller innehåll som du gör tillgängligt för Media Services-kontot via HTTPS-webbadresser. Om du vill lära dig hur du kodar från en HTTPS-webbadress kan du läsa [denna](job-input-from-http-how-to.md) artikel.  

I Media Services v3 använder du Azure Storage-API:er till att ladda upp filer. I följande .NET-kodfragment visas hur du gör detta.

Den här funktionen utför följande åtgärder:

* Skapar en tillgång 
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [behållare i lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Laddar upp filen till behållaren i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Skapa en utdatatillgång där resultatet av ett jobb lagras 

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av ditt kodningsjobb. Projektet definierar funktionen **DownloadResults** som laddar ner resultaten från utdatatillgången till mappen ”utdata”, så att du kan se vad du har fått.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Skapa en transformering och ett jobb som kodar den uppladdade filen
När kodningen eller bearbetningen av innehåll i Media Services görs, konfigureras vanligtvis kodningsinställningarna som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video, tillämpar du receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **Transformering**. Mer information finns i [Transformeringar och jobb](transform-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som kodar videon för att strömma den till olika iOS- och Android-enheter. 

#### <a name="transform"></a>Transformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden nedan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. Information om denna förinställning finns i [Automatisk generering av bithastighetsstege](autogen-bitrate-ladder.md).

Du kan använda en inbyggd EncoderNamedPreset eller anpassade förinställningar. Mer information finns i [Så här anpassar du förinställningar för kodning](customize-encoder-presets-how-to.md).

När du skapar en [Transformering](https://docs.microsoft.com/rest/api/media/transforms) bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Jobb

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I det här exemplet har indatavideon laddats upp från din lokala dator. Om du vill lära dig hur du kodar från en HTTPS-webbadress kan du läsa [denna](job-input-from-http-how-to.md) artikel.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kodexemplet nedan visar hur du kontrollerar [jobbets](https://docs.microsoft.com/rest/api/media/jobs) status i tjänsten. Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid.

Event Grid är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser.  Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Hämta en StreamingLocator

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra detta i två steg: Först skapar du en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) och därefter skapar du de strömmande URL:er som klienterna ska använda. 

Processen att skapa en **StreamingLocator** kallas för publicering. Som standard kan din **StreamingLocator** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) måste du ange önskat **StreamingPolicyName**. I det här exemplet strömmar du rensat (eller icke-krypterat innehåll), så den fördefinierade rensningsströmningsprincipen **PredefinedStreamingPolicy.ClearStreamingOnly** används.

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

Följande kod förutsätter att du anropar funktionen med ett unikt locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Även om exemplet i det här avsnittet beskriver strömning, kan du använda samma anrop för att skapa en StreamingLocator som levererar video via progressiv nedladdning.

### <a name="get-streaming-urls"></a>Hämta strömnings-URL:er

Nu när [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelnings-URL:erna, som du ser i **GetStreamingURLs**. Om du vill skapa en URL måste du sammanfoga värdnamnet [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) och sökvägen **StreamingLocator**. I det här exemplet används *standardvärdet* **StreamingEndpoint**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde* för **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

> [!NOTE]
> I den här metoden behöver du det locatorName som användes när du skapade **StreamingLocator** för utdatatillgången.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  Följande kod tar exempelvis bort Jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Kör exempelappen

1. Tryck på Ctrl+F5 för att köra programmet *EncodeAndStreamFiles*.
2. Kopiera en av de strömmande URL:erna från konsolen.

I det här exemplet visas URL:er som kan användas för att spela upp videon med olika protokoll:

![Resultat](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testa strömnings-URL:en

I den här artikeln används Azure Media Player till att testa strömningen. 

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och navigera till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. I rutan **URL:** klistrar du in ett av de strömmande URL-värden som du fick när du körde programmet. 
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare. Du kan använda verktyget **CloudShell**.

Kör följande kommando i **CloudShell**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Flertrådsteknik

SDK:erna i Azure Media Services v3 är inte trådsäkra. När du utvecklar ett flertrådat program bör du skapa och använda ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du ska ladda upp, koda och strömma videon kan du läsa följande artikel: 

> [!div class="nextstepaction"]
> [Analysera videor](analyze-videos-tutorial-with-api.md)
