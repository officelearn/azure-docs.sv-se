---
title: 'Exempel: Identifiera ansikten i bilder – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda Ansikts-API för att identifiera ansikten i bilder.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 1696a20094357d084ba54739767509b8d50c4ad5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341300"
---
# <a name="example-identify-faces-in-images"></a>Exempel: Identifiera ansikten i bilder

Den här guiden visar hur du identifierar okänd ansikten med hjälp av PersonGroup objekt som skapas från kända personer i förväg. Exemplen är skrivna i C# med hjälp av klientbiblioteket för Azure Cognitive Services Ansikts-API.

## <a name="preparation"></a>Förberedelse

Detta exempel visar:

- Så här skapar du en PersonGroup. Den här PersonGroup innehåller en lista med kända personer.
- Så här tilldelar du ansikten till varje person. Dessa ansikten används som utgångspunkt för att identifiera människor. Vi rekommenderar att du använder Rensa främre vyer av ansikten. Ett exempel är ett foto-ID. En bra uppsättning foton innehåller ansikten för samma person i olika attityder, kläder färger eller frisyrer.

Förbered för att utföra demonstration av det här exemplet:

- Några foton med personens ansikte. [Ladda ned exemplet foton](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) för Anna, faktura och Clare.
- En serie test foton. Foton som kanske eller kanske inte innehåller ansikten Anna, faktura eller Clare. De används för att testa identifiering. Välj också några bildexempel länken ovan.

## <a name="step-1-authorize-the-api-call"></a>Steg 1: Auktorisera API-anropet

Varje anrop till ett ansikts-API för visuellt innehåll kräver en prenumerationsnyckel. Den här nyckeln kan antingen skickas via en frågesträngparameter eller anges i förfrågans sidhuvud. Om du vill skicka prenumerationsnyckeln via en frågesträng, finns i URL: en för den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som exempel:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternativt kan du ange prenumerationsnyckeln i HTTP-frågehuvudet **ocp-apim-subscription-key: &lt;Prenumerationsnyckel&gt;** .
När du använder ett klientbibliotek skickas prenumerationsnyckeln i konstruktorn för klassen FaceServiceClient. Exempel:
 
```csharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Prenumerationsnyckeln, gå till Azure Marketplace från Azure-portalen. Mer information finns i [prenumerationer](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Steg 2: Skapa PersonGroup

I det här steget innehåller en PersonGroup med namnet ”MyFriends” Anna faktura och Clare. Varje person har flera registrerade ansikten. Ansiktena måste identifieras från avbildningarna. När alla av de här stegen har du en PersonGroup som på följande bild:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Steg 2.1: Definiera personer för PersonGroup
En person är en grundläggande enhet för identifiering. En person kan ha ett eller flera kända ansikten registrerade. En PersonGroup är en samling med personer. Varje person som har definierats inom en viss PersonGroup. Identifiering görs mot en PersonGroup. Uppgiften är att skapa en PersonGroup och skapa sedan personerna, till exempel Anna faktura och Clare.

Börja med att skapa en ny PersonGroup med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. Motsvarande klientbibliotek-API är metoden CreatePersonGroupAsync för klassen FaceServiceClient. Grupp-ID som anges för att skapa gruppen är unikt för varje prenumeration. Du kan också hämta, uppdatera eller ta bort PersonGroups med andra PersonGroup APIs. 

När en grupp har definierats kan du definiera personer i den med hjälp av den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Klientbibliotekmetoden är CreatePersonAsync. Du kan lägga till ett ansikte till varje person när de skapas.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> Steg 2.2: Identifiera ansikten och registrera dem till rätt person
Identifiering görs genom att skicka en ”POST” webb-begäran till den [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API med bildfilen i HTTP-begärandetexten. När du använder klientbiblioteket görs ansiktsigenkänning via DetectAsync-metoden för klassen FaceServiceClient.

För varje ansikte som upptäcks, anropa [PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) att lägga till den till rätt person.

Visar processen för att känna igen ett ansikte från en bild och lägga till det till en person:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Om avbildningen innehåller fler än en ansikte, läggs endast de största står inför. Du kan lägga till andra ansikten personen. Skicka en sträng i formatet ”targetFace = vänster, top, bredden och höjden” till [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) frågeparameter för API: er targetFace. Du kan också använda den valfria parametern targetFace AddPersonFaceAsync-metoden för att lägga till andra ansikten. Varje ansikte som lagts till personen som ges ett unikt beständiga ansikts-ID. Du kan använda detta ID i [PersonGroup Person – ta bort ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) och [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Steg 3: Träna PersonGroup

PersonGroup måste utbildas innan en identifiering kan utföras med hjälp av den. Vara måste modellkomponenten i PersonGroup när du lägger till eller ta bort en person eller om du redigerar en persons registrerade ansikte. Utbildningen sker genom API:et [PersonGroup – Utbilda](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). När du använder klientbiblioteket, är det ett anrop till metoden TrainPersonGroupAsync:
 
```csharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Utbildning är en asynkron process. Det kan inte slutföras även efter att metoden TrainPersonGroupAsync returnerar. Du kan behöva fråga status för utbildning. Använd den [PersonGroup - hämta Status för utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API eller GetPersonGroupTrainingStatusAsync-metoden för klientbiblioteket. Följande kod visar en enkel logiken för att vänta tills PersonGroup utbildning för att slutföra:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Steg 4: Identifiera ett ansikte mot en definierad PersonGroup

När Ansikts-API utför identifiering, beräknar likheten mellan ett test ansikte bland alla ansikten i en grupp. Returnerar den mest jämförbara personer för testning. Den här processen görs via den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API eller metoden IdentifyAsync av klientbiblioteket.

Testa ansiktet måste identifieras med hjälp av de föregående stegen. Ansikts-ID skickas sedan till identifiering API som andra argument. Flera face ID: N kan identifieras på samma gång. Resultatet innehåller alla identifierade resultat. Som standard returnerar identifieringsprocessen bara en person som bäst matchar test ansiktet. Om du vill kan du ange valfri parameter maxNumOfCandidatesReturned så att identifieringsprocessen returneras flera kandidater.

Följande kod visar identifieringsprocessen:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

När du har slutfört stegen försök att identifiera olika ytor. Se om ansikten Anna, faktura eller Clare kan identifieras korrekt enligt de avbildningar som överförts för ansiktsigenkänning. Se följande exempel:

![Identifiera olika ytor](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Steg 5: Begäran för storskaliga

En PersonGroup kan innehålla upp till 10 000 personer baserat på den föregående punkter.
Mer information om scenarier med upp till miljontals personer finns i [Så här använder du den storskaliga funktionen](how-to-use-large-scale.md).

## <a name="summary"></a>Sammanfattning

I den här guiden beskrivs processen att skapa en PersonGroup och identifiera en person. Följande funktioner har beskrivs och visas:

- Identifiera ansikten med hjälp av den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API.
- Skapa PersonGroups med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Träna en PersonGroup med hjälp av den [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifiera okänt ansikten mot PersonGroup med hjälp av den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API.

## <a name="related-topics"></a>Relaterade ämnen

- [Ansikte erkännande begrepp](../concepts/face-recognition.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Lägg till ansikten](how-to-add-faces.md)
- [Använda funktionen för storskaliga](how-to-use-large-scale.md)
