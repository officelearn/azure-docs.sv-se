---
title: 'Snabbstart: Klientbibliotek för Content Moderator för .NET'
titleSuffix: Azure Cognitive Services
description: Kom igång med klientbiblioteket Innehållsmoderator för .NET med den här snabbstarten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e93cfd6d44e6a59b4b3d94a49bef766fadf4381c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770629"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Snabbstart: Klientbibliotek för Content Moderator för .NET

Kom igång med klientbiblioteket Innehållsmoderator för .NET. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Content Moderator är en kognitiv tjänst som kontrollerar text-, bild- och videoinnehåll för material som kan vara stötande, riskabelt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Din app kan sedan hantera flaggat innehåll för att följa regler eller underhålla den avsedda miljön för användare.

Använd klientbiblioteket Innehållsmoderator för .NET för att:

* [Moderera text](#moderate-text)
* [Måttliga bilder](#moderate-images)
* [Skapa en recension](#create-a-review)

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Exempel](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-content-moderator-azure-resource"></a>Skapa en Azure-resurs för innehållsmoderator

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Content Moderator med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/)

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `CONTENT_MODERATOR_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `CONTENT_MODERATOR_SUBSCRIPTION_KEY` respektive , respektive.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad textredigerare eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `content-moderator-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Öppna den *Program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Lägg till följande `using`-uttryck:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Skapa variabler för resursens slutpunktsplats och nyckel som miljövariabler i klassen **Program.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Om du har skapat miljövariablerna efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör det för att komma åt variablerna.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera klientbiblioteket Innehåll moderator för .NET i programkatalogen med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

Följande klasser hanterar några av de viktigaste funktionerna i Innehållsmoderatorn .NET SDK.

|Namn|Beskrivning|
|---|---|
|[InnehållModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Den här klassen behövs för alla funktioner för innehållsmodererator. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Den här klassen tillhandahåller funktioner för att analysera bilder för barnförbjudet innehåll, personlig information eller mänskliga ansikten.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Den här klassen tillhandahåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Den här klassen innehåller funktionerna i gransknings-API:erna, inklusive metoder för att skapa jobb, anpassade arbetsflöden och mänskliga granskningar.|

## <a name="code-examples"></a>Kodexempel


De här kodavsnitten visar hur du utför följande uppgifter med klientbiblioteket Innehållsmoderator för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Moderera text](#moderate-text)
* [Måttliga bilder](#moderate-images)
* [Skapa en recension](#create-a-review)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du klientobjekt med slutpunkten och nyckeln. Du behöver inte en annan klient för varje scenario, men det kan hjälpa till att hålla koden organiserad.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Moderera text

Följande kod använder en Content Moderator-klient för att analysera en texttext och skriva ut resultaten till konsolen. Definiera in- och utdatafiler i roten **för klassen Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Sedan vid roten av ditt projekt och lägga till en *TextFile.txt* fil. Lägg till din egen text i den här filen eller använd följande exempeltext:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Lägg till följande metodanrop i metoden: `Main`

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Definiera sedan textmoderingsmetoden någonstans i **klassen Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Måttliga bilder

Följande kod använder en Content Moderator-klient, tillsammans med ett [ImageModeration-objekt,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) för att analysera fjärrbilder för barnförbjudet och racy-innehåll.

> [!NOTE]
> Du kan också analysera innehållet i en lokal bild. Se [referensdokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) för metoder och åtgärder som fungerar med lokala bilder.

### <a name="get-sample-images"></a>Hämta exempelbilder

Definiera in- och utdatafiler:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Skapa sedan indatafilen *ImageFiles.txt*i projektets rot. I den här filen lägger du&mdash;till url:erna för bilder för att analysera en URL på varje rad. Du kan använda följande exempelbilder:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Skicka indata- och utdatafilerna `Main` till följande metodanrop i metoden. Du definierar den här metoden i ett senare steg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definiera hjälpklass

Lägg till följande klassdefinition i klassen **Program.** Den här inre klassen hanterar resultatet av bildmoderering.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definiera metoden för bildmoderering

Följande metod iterar igenom bild-URL:erna i en textfil, skapar en **EvaluationData-instans** och analyserar bilden för innehåll med barnförbjudet/kretering. Sedan lägger den slutliga **EvaluationData-instansen** till i en lista och den fullständiga listan över returnerade data till konsolen.

#### <a name="iterate-through-image-urls"></a>Iterera genom bildadresser

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analysera innehåll

Mer information om bildattributen som Innehållsmoderator söker efter finns i begreppsguiden för begrepp för [bildmoderering.](./image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Skriv modereringsresultat till fil

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Skapa en recension

Du kan använda Innehållsmoderatorn .NET SDK för att mata in innehåll i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) så att mänskliga moderatorer kan granska det. Mer information om granskningsverktyget finns i [konceptguiden för granskningsverktyg](./review-tool-user-guide/human-in-the-loop.md).

Metoden i det här avsnittet använder klassen [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) för att skapa en granskning, hämta dess ID och kontrollera dess information efter att ha tagit emot mänsklig indata via granskningsverktygets webbportal. Den loggar all denna information i en utdatatextfil. Anropa metoden från `Main` din metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Hämta exempelbilder

Deklarera följande matris i roten till **programklassen.** Den här variabeln refererar till en exempelbild som ska användas för att skapa granskningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Hämta granskningsautentiseringsuppgifter

Logga in på [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) och hämta ditt teamnamn. Tilldela den sedan till lämplig variabel i **klassen Program.** Du kan också ställa in en motringningsslutpunkt för att få uppdateringar om granskningens aktivitet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definiera hjälpklass

Lägg till följande klassdefinition i **klassen Program.** Den här klassen används för att representera en enda granskningsinstans som skickas till granskningsverktyget.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definiera hjälpmetod

Lägg till följande metod i klassen **Program**. Den här metoden skriver resultatet av granskningsfrågor till utdatatextfilen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definiera metoden för att skapa granskning

Nu är du redo att definiera den metod som ska hantera granskningens skapande och frågor. Lägg till en ny metod, **CreateReviews**och definiera följande lokala variabler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Skicka recensioner till granskningsverktyget

Lägg sedan till följande kod för att iterera genom de givna exempelbilderna, lägga till metadata och skicka dem till granskningsverktyget i en enda batch. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objektet som returneras från API-anropet innehåller unika ID-värden för varje uppladdad bild. Följande kod tolkar dessa ID:n och använder dem sedan för att fråga innehållsmoderator för status för varje bild i batchen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Få information om recension

Följande kod gör att programmet väntar på indata från användaren. När du kommer till det här steget vid körning kan du själv gå till [granskningsverktyget,](https://contentmoderator.cognitive.microsoft.com) kontrollera att exempelbilden laddades upp och interagerar med den. Information om hur du interagerar med en recension finns i [vägledningen för recensioner](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). När du är klar kan du trycka på valfri tangent för att fortsätta programmet och hämta resultatet av granskningsprocessen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Om du har använt en motringningsslutpunkt i det här scenariot bör den få en händelse i det här formatet:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Köra appen

Kör programmet från programkatalogen `dotnet run` med kommandot.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portalen](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Content Moderator .NET-biblioteket för att utföra modereringsuppgifter. Läs sedan mer om måttfullhet av bilder eller andra medier genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
> [Begrepp för bildmoderering](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Vad är Azure Content Moderator?](./overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
