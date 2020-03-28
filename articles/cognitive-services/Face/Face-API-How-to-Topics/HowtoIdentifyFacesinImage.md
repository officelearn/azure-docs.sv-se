---
title: 'Exempel: Identifiera ansikten i bilder - Face'
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du identifierar okända ansikten med hjälp av PersonGroup-objekt, som skapas från kända personer i förväg.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169916"
---
# <a name="example-identify-faces-in-images"></a>Exempel: Identifiera ansikten i bilder

Den här guiden visar hur du identifierar okända ansikten med hjälp av PersonGroup-objekt, som skapas från kända personer i förväg. Exemplen skrivs i C# med hjälp av Azure Cognitive Services Face-klientbiblioteket.

## <a name="preparation"></a>Förberedelse

Detta exempel visar:

- Så här skapar du en persongrupp. Den här persongruppen innehåller en lista över kända personer.
- Hur man tilldelar ansikten till varje person. Dessa ansikten används som en baslinje för att identifiera personer. Vi rekommenderar att du använder tydliga frontvyer av ansikten. Ett exempel är ett foto-ID. En bra uppsättning bilder innehåller ansikten av samma person i olika poser, kläder färger, eller frisyrer.

För att utföra demonstrationen av detta prov, bered

- Några foton med personens ansikte. [Ladda ner exempel bilder](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) för Anna, Bill och Clare.
- En serie testbilder. Bilderna kanske eller kanske inte innehåller ansikten Anna, Bill eller Clare. De används för att testa identifiering. Välj även några exempelbilder från länken ovan.

## <a name="step-1-authorize-the-api-call"></a>Steg 1: Auktorisera API-anrop

Varje anrop till ett ansikts-API för visuellt innehåll kräver en prenumerationsnyckel. Den här nyckeln kan antingen skickas genom en frågesträngparameter eller anges i begäranden. Information om hur du skickar prenumerationsnyckeln via en frågesträng läser du url:en för begäran för [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som ett exempel:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Som ett alternativ anger du prenumerationsnyckeln i HTTP-begäranhuvudet **ocp-apim-subscription-key: &lt;Prenumerationsnyckel&gt;**.
När du använder ett klientbibliotek skickas prenumerationsnyckeln in genom konstruktorn för klassen FaceClient. Ett exempel:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Om du vill hämta prenumerationsnyckeln går du till Azure Marketplace från Azure-portalen. Mer information finns i [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Steg 2: Skapa PersonGroup

I det här steget innehåller en persongrupp med namnet "Mina vänner" Anna, Bill och Clare. Varje person har flera registrerade ansikten. Ansiktena måste identifieras från bilderna. När alla av de här stegen har du en PersonGroup som på följande bild:

![Mina vänner](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Steg 2.1: Definiera personer för persongruppen
En person är en grundläggande enhet för identifiering. En person kan ha ett eller flera kända ansikten registrerade. En PersonGroup är en samling personer. Varje person definieras i en viss persongrupp. Identifiering görs mot en PersonGroup. Uppgiften är att skapa en PersonGroup och sedan skapa personerna i den, till exempel Anna, Bill och Clare.

Skapa först en ny persongrupp med hjälp av [PersonGroup - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. Motsvarande klientbiblioteks-API är metoden CreatePersonGroupAsync för klassen FaceClient. Grupp-ID:et som har angetts för att skapa gruppen är unikt för varje prenumeration. Du kan också hämta, uppdatera eller ta bort PersonGroups med hjälp av andra PersonGroup API:er. 

När en grupp har definierats kan du definiera personer i den med hjälp av [PersonGroup Person - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Klientbibliotekmetoden är CreatePersonAsync. Du kan lägga till ett ansikte till varje person när de har skapats.

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
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Steg 2.2: Identifiera ansikten och registrera dem till rätt person
Identifiering görs genom att skicka en ”POST” webb-begäran till den [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API med bildfilen i HTTP-begärandetexten. När du använder klientbiblioteket utförs ansiktsidentifiering via en av identifieringen. Async-metoder för klassen FaceClient.

För varje ansiktsutnr som identifieras ringer du [persongruppsperson – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) för att lägga till det i rätt person.

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
Om bilden innehåller mer än ett ansikte läggs bara det största ansiktet till. Du kan lägga till andra ansikten till personen. Skicka en sträng i formatet "targetFace = vänster, överst, bredd, höjd" till [PersonGroup Person - Lägg till Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API:s targetFace-frågeparameter. Du kan också använda valfria parametern targetFace för metoden AddPersonFaceAsync för att lägga till andra ansikten. Varje ansikte som läggs till personen får ett unikt beständigt ansikts-ID. Du kan använda det här ID:t i [PersonGroup Person – Ta bort ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) och ansikte – [Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Steg 3: Träna PersonGroup

Persongruppen måste tränas innan en identifiering kan utföras med hjälp av den. Persongruppen måste tränas om efter att du har lagt till eller tagit bort en person eller om du redigerar en persons registrerade ansikte. Utbildningen sker genom API:et [PersonGroup – Utbilda](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). När du använder klientbiblioteket är det ett anrop till metoden TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Utbildning är en asynkron process. Det kanske inte är färdigt även när metoden TrainPersonGroupAsync returneras. Du kan behöva fråga om träningsstatusen. Använd metoden [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) eller GetPersonGroupTrainingStatusAsync för klientbiblioteket. Följande kod visar en enkel logik i att vänta på att PersonGroup-träningen ska slutföras:
 
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

När Face-tjänsten utför identifieringar beräknar den likheten mellan ett testysikte bland alla ansikten inom en grupp. Den returnerar de mest jämförbara personerna för testytan. Den här processen görs via [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API eller IdentifyAsync-metoden för klientbiblioteket.

Testytan måste identifieras med hjälp av föregående steg. Sedan skickas ansikts-ID till identifierings-API:et som ett andra argument. Flera ansikts-ID kan identifieras på en gång. Resultatet innehåller alla identifierade resultat. Som standard returnerar identifieringsprocessen bara en person som matchar testsidan bäst. Om du vill kan du ange den valfria parametern maxNumOfCandidatesReturned för att låta identifieringsprocessen returnera fler kandidater.

Följande kod visar identifieringsprocessen:

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

När du är klar med stegen kan du försöka identifiera olika ansikten. Se om ansikten Anna, Bill eller Clare kan identifieras korrekt enligt de bilder som laddas upp för ansiktsigenkänning. Se följande exempel:

![Identifiera olika ansikten](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Steg 5: Begäran om storskalig

En PersonGroup kan rymma upp till 10 000 personer baserat på den tidigare designbegränsningen.
Mer information om scenarier med upp till miljontals personer finns i [Så här använder du den storskaliga funktionen](how-to-use-large-scale.md).

## <a name="summary"></a>Sammanfattning

I den här guiden lärde du dig processen att skapa en persongrupp och identifiera en person. Följande funktioner förklarades och demonstrerades:

- Identifiera ansikten med hjälp av [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API.
- Skapa persongrupper med hjälp av [PersonGroup - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av [PersonGroup Person - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Träna en persongrupp med hjälp av [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifiera okända ansikten mot PersonGroup med hjälp av [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API.

## <a name="related-topics"></a>Relaterade ämnen

- [Begrepp för ansiktsigenkänning](../concepts/face-recognition.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Lägg till ansikten](how-to-add-faces.md)
- [Använda den storskaliga funktionen](how-to-use-large-scale.md)
