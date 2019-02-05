---
title: 'Exempel: Identifiera ansikten i bilder – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda Ansikts-API för att identifiera ansikten i bilder.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: c61852763353189321b8f98711928e0e8b3a389d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208099"
---
# <a name="example-how-to-identify-faces-in-images"></a>Exempel: Så här identifierar du ansikten i bilder

Den här guiden visar hur du identifiera okända ansikten med PersonGroups, som skapas från kända personer i förväg. Exemplen är skrivna i C#- med Ansikts-API-klientbiblioteket.

## <a name="concepts"></a>Begrepp

Om du inte är bekant med följande begrepp i den här guiden kan du söka efter definitioner i vår [ordlista](../Glossary.md) när som helst:

- Ansiktsigenkänning – Känna igen
- Ansiktsigenkänning – identifiera
- PersonGroup

## <a name="preparation"></a>Förberedelse

I det här exemplet visar vi följande:

- Så här skapar du en PersonGroup – denna PersonGroup innehåller en lista över kända personer.
- Så här tilldelar du ansikten till varje person – Dessa ansikten används som utgångspunkt för att identifiera personer. Det rekommenderas att du använder tydliga ansikten framifrån, som din legitimation. En bra uppsättning bilder bör ha samma persons ansikte från olika vinklar, med olika kläder eller frisyrer.

Om du vill demonstrera det här exemplet måste du förbereda ett antal bilder:

- Några foton med personens ansikte. [Klicka här för att ladda ned exempelfoton](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) för Anna, Bill och Clare.
- Som eventuellt får innehåller Anna, Bill eller Clares ansikten används för att testa identifieringen. Du kan också välja några bildexempel från länken ovan.

## <a name="step-1-authorize-the-api-call"></a>Steg 1: Auktorisera API-anropet

Varje anrop till ett ansikts-API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln kan antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud. Om du vill skicka prenumerationsnyckeln via frågesträngen ska du använda URL:en för [Ansiktsigenkänning– identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som exempel:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Prenumerationsnyckeln kan även anges i HTTP-frågehuvudet: **ocp-apim-subscription-key: &lt;Prenumerationsnyckel&gt;** När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för klassen FaceServiceClient. Exempel:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Prenumerationsnyckeln kan hämtas från Marketplace-sidan på Azure-portalen. Se [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Steg 2: Skapa PersonGroup

I det här steget skapade vi en PersonGroup med namnet ”MyFriends” som innehåller tre personer: Anna, Bill och Clare. Varje person har flera registrerade ansikten. Ansiktena måste identifieras från bilderna. När alla av de här stegen har du en PersonGroup som på följande bild:

![Identifiering1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Definiera personer för PersonGroup
En person är en grundläggande enhet för identifiering. En person kan ha ett eller flera kända ansikten registrerade. Men en PersonGroup är en uppsättning personer och varje person definieras inom en viss PersonGroup. Identifieringen sker mot en PersonGroup. Därför är uppgiften att skapa en PersonGroup och sedan skapa personer, till exempel Anna, Bill och Clare.

Först måste du skapa en ny PersonGroup. Detta sker med hjälp av API:et [PersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). Motsvarande klientbibliotek-API är metoden CreatePersonGroupAsync för klassen FaceServiceClient. Det grupp-ID som anges för att skapa gruppen är unikt för varje prenumeration – du kan också hämta, uppdatera eller ta bort PersonGroups med andra PersonGroup API:er. När en grupp har definierats kan personer sedan definieras i den med hjälp av API:et [PersonGroup Person – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). Klientbibliotekmetoden är CreatePersonAsync. Du kan lägga till ett ansikte till varje person efter att de har skapats.

```CSharp 
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
### <a name="step2-2"></a> 2.2 Identifiera ansikten och registrera dem till rätt person
Identifiering görs genom att skicka en ”POST” webb-begäran till den [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API med bildfilen i HTTP-begärandetexten. När du använder klientbiblioteket körs ansiktsigenkänning via DetectAsync-metod för klassen FaceServiceClient.

För varje ansikte som har identifierats kan du anropa [PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) för att lägga till den till rätt person.

Visar processen för att känna igen ett ansikte från en bild och lägga till det till en person:
```CSharp 
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
Observera att om bilderna innehåller mer än ett ansikte läggs endast det största ansiktet till. Du kan lägga till andra ansikten till personen genom att skicka en sträng i formatet ”targetFace = vänster, övre, bredd och höjd” till [PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API: er targetFace Frågeparametern, eller använder den valfria parametern targetFace för metoden AddPersonFaceAsync för att lägga till andra ansikten. Varje ansikte som lagts till personen får ett unikt beständigt ansikts-ID som kan användas i [PersonGroup Person – ta bort ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) och [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Steg 3: Träna PersonGroup

PersonGroup måste utbildas innan en identifiering kan utföras med hjälp av den. Dessutom måste den utbildas om efter att du har lagt till eller tagit bort en person eller om en person har redigerat sitt registrerade ansikte. Utbildningen sker genom API:et [PersonGroup – Utbilda](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). När du använder klientbiblioteket är det bara ett anrop till metoden TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Utbildningen är en asynkron process. Det kanske inte har slutförts även efter att metoden TrainPersonGroupAsync returneras. Du kan behöva fråga efter utbildningsstatusen med API:et [PersonGroup – Hämta status för utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) eller GetPersonGroupTrainingStatusAsync-metoden för klientbiblioteket. Följande kod visar en enkel logik om pågående PersonGroup-utbildning som ska slutföras:
 
```CSharp 
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

När du utför identifiering kan Ansikts-API beräkna likheten mellan ett testansikte bland alla ansikten i en grupp och returnerar den mest jämförbara personen för att testansiktet. Detta görs via API:et [Ansiktsigenkänning – Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) eller metoden IdentifyAsync för klientbiblioteket.

Testansiktet måste identifieras med föregående steg och sedan måste ansikts:ID:t skickas till API:et identifiera som ett andra argument. Flera ansikts-ID:n kan identifiera på en gång och resultatet kommer att innehålla alla identifieringsresultat. Som standard returneras identifiering bara en person som bäst matchar testansiktet. Om du vill kan du ange den valfria parametern maxNumOfCandidatesReturned så att identifiera returnerar fler kandidater.

Följande kod visar hur du identifierar:

```CSharp 
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

När du är klar med stegen kan du försöka att identifiera olika ansikten och se om Anna, Bill och Clares ansikten kan identifieras korrekt enligt bilderna som laddas upp för ansiktsavkänning. Se följande exempel:

![Identifiera2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Steg 5: Begäran om stor skala

En PersonGroup kan som sagt innehålla upp till 10 000 personer på grund av begränsningen hos den föregående designen.
Mer information om scenarier med upp till miljontals personer finns i [Så här använder du den storskaliga funktionen](how-to-use-large-scale.md).

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du skapar en PersonGroup och identifiera en person. Det följande är en snabb påminnelse om de viktiga aspekter som förklarats och beskrivits tidigare:

- Identifiera ansikten med hjälp av API:et [Ansiktsigenkänning – Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Skapa PersonGroups med hjälp av API:et [PersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Skapa personer med hjälp av API:et [PersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Träna en PersonGroup med hjälp av API:et [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identifiera okända ansikten mot en PersonGroup med hjälp av API:et [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Relaterade ämnen

- [Känna igen ansikten i en bild](HowtoDetectFacesinImage.md)
- [Lägga till ansikten](how-to-add-faces.md)
- [Känna igen storskaliga funktioner](how-to-use-large-scale.md)
