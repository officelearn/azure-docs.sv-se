---
title: 'Snabbstart: Face-klientbibliotek för .NET'
description: Kom igång med Face-klientbiblioteket för .NET med den här snabbstarten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 89ef221fce9aed3f9e2c948e89933b8650bb4b4b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769817"
---
# <a name="quickstart-face-client-library-for-net"></a>Snabbstart: Face-klientbibliotek för .NET

Kom igång med Face-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Face-tjänsten ger dig tillgång till avancerade algoritmer för att upptäcka och känna igen mänskliga ansikten i bilder.

Använd Face-klientbiblioteket för .NET för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en persongrupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicksbild för datamigrering](#take-a-snapshot-for-data-migration)

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Exempel](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-face-azure-resource"></a>Skapa en Face Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Face med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `FACE_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `FACE_SUBSCRIPTION_KEY` respektive , respektive.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `face-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```dotnetcli
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Öppna den *Program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Lägg till `using` följande direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Skapa variabler `Main` för resursens Azure-slutpunkt och -nyckel i programmets metod.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera Face-klientbiblioteket för .NET i programkatalogen med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av huvudfunktionerna i Face .NET SDK:

|Namn|Beskrivning|
|---|---|
|[FaceClient (70)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Den här klassen representerar din behörighet att använda Face-tjänsten och du behöver den för alla Face-funktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Den här klassen hanterar de grundläggande identifierings- och igenkänningsuppgifter som du kan göra med mänskliga ansikten. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Den här klassen representerar alla data som har identifierats från ett enda ansikte i en bild. Du kan använda den för att hämta detaljerad information om ansiktet.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Den här klassen hanterar molnlagrade **FaceList-konstruktioner** som lagrar en olika uppsättning ansikten. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Den här klassen hanterar **molnlagringspersonkonstruktioner,** som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Den här klassen hanterar de molnbevarade **PersonGroup-konstruktionerna,** som lagrar en uppsättning olika **personobjekt.** |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Den här klassen hanterar funktionen Ögonblicksbild. Du kan använda den för att tillfälligt spara alla dina molnbaserade Face-data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

Kodavsnitten nedan visar hur du utför följande uppgifter med Face-klientbiblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en persongrupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicksbild för datamigrering](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för ansiktsnyckeln och slutpunkten, med namnet `FACE_SUBSCRIPTION_KEY` och `FACE_ENDPOINT`.

I en ny metod instansierar du en klient med slutpunkten och nyckeln. Skapa ett **[ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** med nyckeln och använd det med slutpunkten för att skapa ett **[FaceClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Du kommer förmodligen vill anropa `Main` den här metoden i metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Deklarera hjälpfält

Följande fält behövs för flera av de Face-åtgärder som du lägger till senare. Definiera följande URL-sträng i klassens rot. Den här URL:en pekar på en mapp med exempelbilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definiera strängar som pekar på de olika typerna av igenkänningsmodeller. Senare kan du ange vilken igenkänningsmodell du vill använda för ansiktsidentifiering. Se [Ange en igenkänningsmodell](../Face-API-How-to-Topics/specify-recognition-model.md) för information om dessa alternativ.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Lägg till följande metodanrop till **huvudmetoden.** Du definierar metoden nästa. Den slutliga identifieringsåtgärden tar ett **[FaceClient-objekt,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** en bild-URL och en igenkänningsmodell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Få upptäckta ansiktsobjekt

I nästa kodblock identifierar `DetectFaceExtract` metoden ansikten i tre av bilderna vid den angivna webbadressen och skapar en lista över **[DetectedFace-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** i programminnet. Listan över **[FaceAttributeType-värden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** anger vilka funktioner som ska extraheras. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visa upptäckta ansiktsdata

Resten av `DetectFaceExtract` metoden tolkar och skriver ut attributdata för varje identifierad yta. Varje attribut måste anges separat i det ursprungliga API-anropet för ansiktsidentifiering (i **[listan FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** Följande kod bearbetar varje attribut, men du behöver förmodligen bara använda ett eller ett fåtal.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar en enda identifierad ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar. När en matchning hittas skrivs ID:t för det matchade ansiktet ut på konsolen.

### <a name="detect-faces-for-comparison"></a>Identifiera ansikten för jämförelse

Definiera först en andra ansiktsigenkänningsmetod. Du måste identifiera ansikten i bilder innan du kan jämföra dem, och den här identifieringsmetoden är optimerad för jämförelseåtgärder. Den extraherar inte detaljerade ansiktsattribut som i avsnittet ovan, och den använder en annan igenkänningsmodell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Hitta matchningar

Följande metod identifierar ansikten i en uppsättning målbilder och i en enda källbild. Sedan jämför den dem och hittar alla målbilder som liknar källbilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Skriv ut matchningar

Följande kod skriver ut matchningsinformationen till konsolen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Åtgärden Identifiera tar en bild av en person (eller flera personer) och letar efter identiteten för varje ansikte i bilden. Den jämför varje upptäckt ansikte med en **PersonGroup**, en databas med olika **personobjekt** vars ansiktsdrag är kända. För att kunna identifiera-åtgärden måste du först skapa och träna en **PersonGroup**

### <a name="create-and-train-a-person-group"></a>Skapa och träna en persongrupp

Följande kod skapar en **persongrupp** med sex olika **personobjekt.** Det associerar varje **person** med en uppsättning exempel bilder, och sedan det tåg att känna igen varje person genom deras ansiktsegenskaper. **Person-** och **persongruppsobjekt** används i åtgärderna Verifiera, Identifiera och Gruppera.

#### <a name="create-persongroup"></a>Skapa persongrupp

Deklarera en strängvariabel i klassens rot för att representera ID:t för den **persongrupp** som du ska skapa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Lägg till följande kod i en ny metod. Den här metoden utför identifieringsåtgärden. Det första kodblocket associerar namnen på personer med deras exempelbilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Lägg sedan till följande kod för att skapa ett **personobjekt** för varje person i ordlistan och lägga till ansiktsdata från lämpliga bilder. Varje **person-objekt** associeras med samma **PersonGroup** via sin unika ID-sträng. Kom ihåg att `client`skicka `url`variablerna , och `RECOGNITION_MODEL1` till den här metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Tåg PersonGroup

När du har extraherat ansiktsdata från dina bilder och sorterat dem i olika **personobjekt** måste du träna **PersonGroup** för att identifiera de visuella funktioner som är associerade med var och en av dess **personobjekt.** Följande kod anropar den asynkrona **tågmetoden** och avsöker resultatet och skriver ut statusen till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Den här **persongruppen** och dess associerade **personobjekt** är nu klara att användas i åtgärderna Verifiera, Identifiera eller Gruppera.

### <a name="get-a-test-image"></a>Få en testbild

Observera att koden för Skapa och träna en `sourceImageFileName` [persongrupp](#create-and-train-a-person-group) definierar en variabel . Den här variabeln motsvarar&mdash;källbilden som bilden som innehåller personer att identifiera.

### <a name="identify-faces"></a>Identifiera ansikten

Följande kod tar källavbildningen och skapar en lista över alla ansikten som identifieras i bilden. Det här är ansiktena som kommer att identifieras mot **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Nästa kodavsnitt anropar **identifyasync-åtgärden** och skriver ut resultaten till konsolen. Här försöker tjänsten matcha varje ansikte från källbilden till en **person** i den angivna **persongruppen**. Detta stänger din Identifiera metod.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicksbild för datamigrering

Med funktionen Ögonblicksbilder kan du flytta sparade Ansiktsdata, till exempel en tränad **PersonGroup,** till en annan Azure Cognitive Services Face-prenumeration. Du kanske vill använda den här funktionen om du till exempel har skapat ett **PersonGroup-objekt** med en kostnadsfri utvärderingsprenumeration och vill migrera det till en betald prenumeration. Se [Migrera dina ansiktsdata](../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en översikt över funktionen Ögonblicksbilder.

I det här exemplet migrerar du den **persongrupp som** du skapade i [Skapa och tränar en persongrupp](#create-and-train-a-person-group). Du kan antingen fylla i det avsnittet först eller skapa egna ansiktsdatakonstruktioner som ska migreras.

### <a name="set-up-target-subscription"></a>Ställ in målprenumeration

Först måste du ha en andra Azure-prenumeration med en Face-resurs. Du kan göra detta genom att följa stegen i avsnittet [Konfigurera.](#setting-up) 

Definiera sedan följande variabler `Main` i metoden för programmet. Du måste skapa nya miljövariabler för prenumerations-ID för ditt Azure-konto, samt nyckel-, slutpunkt och prenumerations-ID för ditt nya (mål)konto. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

I det här exemplet deklarerar du en variabel för ID:t för **målpersongruppen**&mdash;som objektet som tillhör den nya prenumerationen, som du kopierar dina data till.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autentisera målklient

Lägg sedan till koden för att autentisera din sekundära Face-prenumeration.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Använda en ögonblicksbild

Resten av ögonblicksbildåtgärderna måste ske inom en asynkron metod. 

1. Det första steget är att **ta** ögonblicksbilden, som sparar din ursprungliga prenumerations ansiktsdata till en tillfällig molnplats. Den här metoden returnerar ett ID som du använder för att fråga åtgärdens status.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Fråga sedan ID:t tills åtgärden har slutförts.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Använd sedan **funktionen apply** för att skriva dina ansiktsdata till din målprenumeration. Den här metoden returnerar också ett ID-värde.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Fråga det nya ID:t igen tills åtgärden har slutförts.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Slutför slutligen try/catch-blocket och slutför metoden.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

Nu bör ditt nya **PersonGroup-objekt** ha samma data som det ursprungliga och bör vara tillgängligt från din nya (mål) Azure Face-prenumeration.

## <a name="run-the-application"></a>Köra appen

Kör programmet från programkatalogen `dotnet run` med kommandot.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabbstarten och vill ta bort den kör du följande kod i programmet:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definiera borttagningsmetoden med följande kod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Om du migrerade data med hjälp av funktionen Ögonblicksbild i den här snabbstarten måste du dessutom ta bort den **persongrupp som** sparats i målprenumerationen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Face-biblioteket för .NET för att utföra basuppgifter. Därefter kan du utforska referensdokumentationen om du vill veta mer om biblioteket.

> [!div class="nextstepaction"]
> [Referens för ansikts-API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Vad är tjänsten Ansiktsigenkänning?](../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
