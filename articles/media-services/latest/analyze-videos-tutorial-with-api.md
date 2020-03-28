---
title: Analysera videor med Media Services v3
titleSuffix: Azure Media Services
description: Läs om hur du analyserar videor med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b7864d89cc14a1473fd43e94bfe74c368bcb391d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349487"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Självstudiekurs: Analysera videor med Media Services v3

> [!NOTE]
> Även om den här självstudien använder [exemplen .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) är de allmänna stegen desamma för [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK-filer](media-services-apis-overview.md#sdks)som stöds .

Den här självstudien visar hur du analyserar videor med Azure Media Services. Det finns många scenarier där du kanske vill veta mer om inspelat video- eller ljudinnehåll. Organisationer kan till exempel för att uppnå högre kundnöjdhet köra tal-till-text-bearbetning och konvertera inspelningar från kundtjänst till en sökbar katalog med index och instrumentpaneler. Sedan kan de få insikter i sin verksamhet. Dessa insikter innehåller en lista över vanliga klagomål, källor till sådana klagomål och annan användbar information.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Hämta exempelappen som beskrivs i avsnittet.
> * Undersök koden som analyserar den angivna videon.
> * Kör appen.
> * Granska utdata.
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet
 
Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer, och du får inte använda Video Indexer eller någon annan Azure-tjänst på ett sätt som bryter mot andras rättigheter eller kan vara skadligt för andra. Innan du laddar upp några videor, inklusive biometriska data, till videoindexertjänsten för bearbetning och lagring måste du ha alla rätt rättigheter, inklusive alla lämpliga medgivanden, från personen/individerna i videon. Om du vill veta mer om efterlevnad, sekretess och säkerhet i Video Indexer, Microsoft [Cognitive Services Terms](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Om du vill ha Microsofts sekretessskyldigheter och hantering av dina data läser du Microsofts [sekretesspolicy,](https://privacy.microsoft.com/PrivacyStatement) [villkoren för onlinetjänster](https://www.microsoft.com/licensing/product-licensing/products) ("OST") och [tillägget för databehandling](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ytterligare sekretessinformation, inklusive om lagring av data, radering/destruktion, finns i OST och [här](../video-indexer/faq.md). Genom att använda Video Indexer samtycker du till att vara bunden av villkoren för kognitiva tjänster, OST, DPA och sekretesspolicyn.

## <a name="prerequisites"></a>Krav

- Om du inte har Installerat Visual Studio kan du skaffa [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att komma åt API:et.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Du hittar exemplet i mappen [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Öppna [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter du fick [från åtkomst till API:er](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Granska koden som analyserar den angivna videon

Det här avsnittet går igenom funktionerna som definierades i filen [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) för projektet *AnalyzeVideos*.

I exemplet slutförs följande åtgärder:

1. Skapa en **transformering** och ett **jobb** som analyserar dina videor.
2. Skapar en inkommande **tillgång** och överför videon till den. TIllgången används som jobbets indata.
3. Skapar en utgående tillgång som lagrar utdata för jobbet.
4. Skickar jobbet.
5. Kontrollerar jobbets status.
6. Laddar ned filer som härrör från körningen av jobbet.

> [!NOTE]
> När du använder en Video- eller Audio Analyzer-förinställningen använder du Azure-portalen för att ställa in ditt konto på att ha 10 mediereserverade S3-enheter. Mer information finns i [Skala mediebearbetning](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapar funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Skapa en indatatillgång och ladda upp en lokal fil till den 

Funktionen **CreateInputAsset** skapar en ny [indatatillgång](https://docs.microsoft.com/rest/api/media/assets) och överför den angivna lokala videofilen till den. Tillgången används som indata för kodningsjobbet. I Media Services v3 kan indata till ett jobb antingen vara en tillgång eller innehåll som du gör tillgängligt för Media Services-kontot via HTTPS-webbadresser. Mer information om hur du kodar från en HTTPS-URL finns i [den här](job-input-from-http-how-to.md) artikeln.  

I Media Services v3 använder du Azure Storage-API:er till att ladda upp filer. I följande .NET-kodfragment visas hur du gör detta.

Följande funktion slutför dessa åtgärder:

* Skapar en tillgång.
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [behållare i lagring](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).

    Om du använder tillgångens [ListContainerS-funktion](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) för att hämta SAS-URL:er bör du tänka på att funktionen returnerar flera SAS-URL:er eftersom det finns två lagringskontonycklar för varje lagringskonto. Ett lagringskonto har två nycklar eftersom det möjliggör sömlös rotation av lagringskontonycklar (till exempel ändra en när du använder den andra och börja använda den nya nyckeln och rotera den andra nyckeln). Den första SAS-URL:en representerar lagringsnyckel1 och andra en lagringsnyckel2.
* Överför filen till behållaren i lagring med SAS-URL:en.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Skapa en utdatatillgång där resultatet av ett jobb lagras

[Utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) lagrar resultatet av ditt jobb. Projektet definierar funktionen **DownloadResults** som laddar ner resultaten från den här utdatatillgången till mappen ”utdata”, så att du kan se vad du har fått.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Skapa en transformering och ett jobb som analyserar videor

När du kodar eller bearbetar innehåll i Media Services är det ett vanligt mönster att ställa in kodningsinställningarna som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka in nya jobb för varje ny video använder du receptet på alla videor i biblioteket. Ett recept i Media Services kallas **transformera**. Mer information finns i [Transformeringar och jobb](transform-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som analyserar den angivna videon.

#### <a name="transform"></a>Transformering

När du skapar en ny instans för en [Transformering](https://docs.microsoft.com/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. **TransformOutput** är en obligatorisk parameter. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. I det här exemplet används förinställningen **VideoAnalyzerPreset** och språket ("en-US") skickas till konstruktorn (`new VideoAnalyzerPreset("en-US")`). Med denna förinställning kan du extrahera flera ljud- och videoinsikter från en video. Du kan använda förinställningen **AudioAnalyzerPreset** om du vill extrahera flera ljudinsikter från en video.

När du skapar en **transformering**kontrollerar du först om det redan finns en med metoden **Hämta,** som visas i koden som följer. I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Jobb

Som nämns ovan är objektet [Transformering](https://docs.microsoft.com/rest/api/media/transforms) receptet och ett [Jobb](https://docs.microsoft.com/rest/api/media/jobs) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indatavideon och platsen för utdata. Du kan ange platsen för din video med: HTTPS-webbadresser, SAS-URL:er eller tillgångar som finns på ditt Media Services-konto.

I det här exemplet är jobbets indata en lokal video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar lite tid att slutföra. När den gör det vill du bli meddelad. Det finns olika alternativ för att få meddelanden om när [jobben](https://docs.microsoft.com/rest/api/media/jobs) är klara. Det enklaste alternativet (som visas här) är att använda avsökning.

Avsökning är inte en rekommenderad metod för produktionsappar på grund av potentiell svarstid. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid.

Event Grid är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser. Mer information finns i [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har stött på ett **Error** fel får du feltillståndet. Om jobbet håller på att avbrytas får du **avbryta** och sedan **avbrytas** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Ladda ned jobbets resultat

Följande funktion hämtar resultaten från [utdatatillgången](https://docs.microsoft.com/rest/api/media/assets) till mappen "utdata" så att du kan undersöka resultatet av jobbet.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom objekt som du planerar att återanvända (vanligtvis ska du återanvända transformeringar och beständiga StreamingLocators). Om du vill att kontot ska vara rent efter att du har experimenterat tar du bort de resurser som du inte tänker återanvända. Följande kod tar till exempel bort jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Kör exempelappen

Tryck på Ctrl+F5 för att köra appen *AnalyzeVideos.*

När vi kör programmet skapar jobbet miniatyrer för varje ansikte som hittas i videon. Det skapar också filen insights.json.

## <a name="examine-the-output"></a>Granska utdatan

Utdatafilen för videoanalysen kallas insights.json. Den här filen innehåller insikter om videon. Beskrivning av elementen i json-filen finns i artikeln [Medieintelligens](intelligence-concept.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Flertrådsteknik

Azure Media Services v3 SDK:er är inte trådsäkra. När du arbetar med en app med flera trådar bör du skapa ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)
