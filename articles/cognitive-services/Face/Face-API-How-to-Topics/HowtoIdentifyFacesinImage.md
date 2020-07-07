---
title: 'Exempel: identifiera ansikten i bilder – ansikte'
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du identifierar okända ansikten med PersonGroup-objekt, som skapas från kända personer i förväg.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.openlocfilehash: 607f67258c5d069590f934891c09ccada780c977
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918747"
---
# <a name="example-identify-faces-in-images"></a>Exempel: identifiera ansikten i bilder

Den här guiden visar hur du identifierar okända ansikten med PersonGroup-objekt, som skapas från kända personer i förväg. Exemplen skrivs i C# med hjälp av klient biblioteket för Azure Cognitive Services Face.

Det här exemplet visar:

- Så här skapar du en PersonGroup. Den här PersonGroup innehåller en lista över kända personer.
- Så här tilldelar du ansikten till varje person. Dessa ansikten används som en bas linje för att identifiera personer. Vi rekommenderar att du använder tydliga vyer av ansikten. Ett exempel är ett foto-ID. En utmärkt uppsättning foton innehåller ansikten av samma person i olika attityder, kläder eller hairstyles.

## <a name="prerequisites"></a>Förutsättningar
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs** och kopierar din nyckel.
* Några foton med ansiktena för identifierade personer. [Hämta exempel foton](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) för Anna, Bill och Clare.
* En serie test foton. Fotona kan eventuellt innehålla ansikten för de identifierade personerna. Använd några bilder från exempel länken för foto.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `face-identify` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Steg 1: Auktorisera API-anrop

Varje anrop till ett ansikts-API för visuellt innehåll kräver en prenumerationsnyckel. Den här nyckeln kan antingen skickas via en frågesträngparametern eller anges i begär ande huvudet. När du använder ett klient bibliotek skickas prenumerations nyckeln genom **FaceClient** -klassens konstruktor. Lägg till följande kod i **main** -metoden för *program.cs* -filen.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Steg 2: Skapa PersonGroup

I det här steget innehåller en **PersonGroup** med namnet "mina vänner" som innehåller Anna, Bill och Clare. Varje person har flera registrerade ansikten. Ansikten måste identifieras från bilderna. Efter alla de här stegen har du en **PersonGroup** som följande bild:

![Mina vänner](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Steg 2,1: definiera personer för PersonGroup
En person är en grundläggande enhet för identifiering. En person kan ha ett eller flera kända ansikten registrerade. En **PersonGroup** är en samling personer. Varje person definieras inom en viss **PersonGroup**. Identifiering görs mot en **PersonGroup**. Uppgiften är att skapa en **PersonGroup**och sedan skapa personer i den, till exempel Anna, Bill och Clare.

Börja med att skapa en ny **PersonGroup** med hjälp av [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. Motsvarande klient biblioteks-API är **CreatePersonGroupAsync** -metoden för **FaceClient** -klassen. Grupp-ID: t som har angetts för att skapa gruppen är unikt för varje prenumeration. Du kan också hämta, uppdatera eller ta bort **PersonGroup**s genom att använda andra **PersonGroup** -API: er. 

När en grupp har definierats kan du definiera personer i den med hjälp av [PersonGroup person – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Klient biblioteks metoden är **CreatePersonAsync**. Du kan lägga till en ansikte till varje person när de har skapats.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Steg 2,2: identifiera ansikten och registrera dem på rätt person
Identifiering görs genom att skicka en ”POST” webb-begäran till den [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API med bildfilen i HTTP-begärandetexten. När du använder klient biblioteket görs ansikts identifiering genom en av identifieraren... Asynkrona metoder för klassen FaceClient.

Ring [PersonGroup personal – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) för varje ansikte som identifieras och Lägg till den till rätt person.

Visar processen för att känna igen ett ansikte från en bild och lägga till det till en person:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Om bilden innehåller fler än en ansikte läggs bara den största ytan till. Du kan lägga till andra ansikten till personen. Skicka en sträng i formatet "targetFace = Left, Top, width, height" till [PersonGroup person – Lägg till ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -API: et targetFace Frågeparametern. Du kan också använda den valfria parametern targetFace för AddPersonFaceAsync-metoden för att lägga till andra ansikten. Varje ansikte som läggs till personen får ett unikt sparat ansikts-ID. Du kan använda detta ID i [PersonGroup person – ta bort ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) och [ansikte – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Steg 3: Träna PersonGroup

**PersonGroup** måste tränas innan en identifiering kan utföras med hjälp av den. **PersonGroup** måste omtränas efter att du har lagt till eller tagit bort en person eller om du redigerar en persons registrerade ansikte. Utbildningen sker genom API:et [PersonGroup – Utbilda](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). När du använder klient biblioteket är det ett anrop till **TrainPersonGroupAsync** -metoden:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Träning är en asynkron process. Det kan hända att den inte är avslutad trots att **TrainPersonGroupAsync** -metoden returnerar. Du kan behöva fråga kursens status. Använd [PersonGroup-API för utbildnings status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) eller **GetPersonGroupTrainingStatusAsync** -metoden i klient biblioteket. Följande kod visar en enkel logik i väntan på att **PersonGroup** -utbildningen ska slutföras:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Steg 4: Identifiera ett ansikte mot en definierad PersonGroup

När ansikts tjänsten utför identifieringar, beräknar den likheten av ett test ansikte mellan alla ansikten i en grupp. Den returnerar de mest jämförbara personerna för testets ansikte. Den här processen görs via API: t för [ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) igenkänning eller IdentifyAsync-metoden för klient biblioteket.

Test ansikte måste identifieras med hjälp av föregående steg. Sedan skickas ansikts-ID: t till ID: t som ett andra argument. Du kan identifiera flera ansikts-ID: n samtidigt. Resultatet innehåller alla identifierade resultat. Som standard returnerar identifierings processen bara en person som matchar test ytans bästa. Om du vill kan du ange den valfria parametern _maxNumOfCandidatesReturned_ så att identifierings processen kan returnera fler kandidater.

Följande kod visar identifierings processen:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

När du har slutfört stegen försöker du identifiera olika ansikten. Se om ansikten för Anna, Bill eller Clare kan identifieras korrekt enligt de avbildningar som laddats upp för ansikts igenkänning. Se följande exempel:

![Identifiera olika ansikten](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Steg 5: begäran om stor skala

En **PersonGroup** kan lagra upp till 10 000 personer baserat på den tidigare design begränsningen. Mer information om scenarier med upp till miljontals personer finns i [Så här använder du den storskaliga funktionen](how-to-use-large-scale.md).

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du skapar en **PersonGroup** och identifierar en person. Följande funktioner förklaras och visas:

- Identifiera ansikten med hjälp av API: t för [ansikts igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) .
- Skapa PersonGroups med hjälp av [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av [PersonGroup-personen – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Träna en PersonGroup med hjälp av [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifiera okända ansikten mot PersonGroup med hjälp av API: t [FACET-Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="related-topics"></a>Relaterade ämnen

- [Koncept för ansikts igenkänning](../concepts/face-recognition.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Lägg till ansikten](how-to-add-faces.md)
- [Använda den storskaliga funktionen](how-to-use-large-scale.md)
