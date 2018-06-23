---
title: Identifiera personerna bakom i bilder Ansikts-API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Använd Ansikts-API i kognitiva Services för att identifiera ytor i bilder.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355389"
---
# <a name="how-to-identify-faces-in-images"></a>Så här identifierar du ytor i bilder

Den här guiden visar hur du identifierar okänd ytor med PersonGroups som skapas från kända personer i förväg. Exemplen är skrivna i C# med hjälp av klientbiblioteket Ansikts-API.

## <a name="concepts"></a> Begrepp

Om du inte är bekant med följande begrepp i den här guiden söker du efter definitioner i vår [ordlista](../Glossary.md) när som helst:

- Står inför – identifiera
- Står inför – identifiera
- PersonGroup

## <a name="preparation"></a> Förberedelse

I det här exemplet visar vi följande:

- Så här skapar du en PersonGroup - den här PersonGroup innehåller en lista över kända personer.
- Hur du tilldelar varje person - ytor dessa ytor används som utgångspunkt för att identifiera personer. Det rekommenderas att använda Rensa främre ytorna, precis som foto-ID. En bra uppsättning foton ska innehålla ytor samma person på olika utgör, kläder färger eller hår format.

Om du vill utföra demonstration av det här exemplet måste du förbereda en massa bilder:

- Några foton med personens ansikte. [Klicka här för att ladda ned exempel foton](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) för Anna, faktura och Clare.
- En serie test foton som eventuellt kan inte innehålla ytor Anna, faktura eller Clare används för att testa identifiering. Du kan också välja vissa exempel bilder från föregående länk.

## <a name="step1"></a> Steg 1: Ge API-anrop

Varje anrop till Ansikts-API kräver en nyckel för prenumerationen. Den här nyckeln kan vara antingen skickas via en frågesträngsparameter eller anges i huvudet i begäran. Om du vill överföra prenumerationen nyckeln via frågesträng, referera till URL: en för den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som exempel:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternativt kan nyckeln prenumeration kan också ange i HTTP-frågehuvudet: **ocp-apim-prenumeration-nyckel: &lt;prenumeration nyckeln&gt;**  när du använder ett klientbibliotek nyckeln prenumeration skickas via konstruktorn för klassen FaceServiceClient. Exempel:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Nyckeln för prenumerationen kan hämtas från Marketplace-sidan i din Azure-portalen. Se [prenumerationer](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> Steg 2: Skapa PersonGroup

Vi har skapat en PersonGroup med namnet ”MyFriends” som innehåller tre personer i det här steget: Anna faktura och Clare. Varje person har flera ytor registrerad. Ytor måste identifieras från avbildningar. När alla av de här stegen har du en PersonGroup som på följande bild:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 definiera personer för PersonGroup
En person är en grundläggande identifiera. En person kan ha en eller flera kända ytor registrerad. Men en PersonGroup är en samling personer och varje person definieras inom en viss PersonGroup. Identifieringen görs mot en PersonGroup. Aktiviteten är så att skapa en PersonGroup och sedan skapa personer, till exempel Anna, faktura och Clare.

Först måste du skapa en ny PersonGroup. Detta utförs med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. Motsvarande klientbiblioteket API är CreatePersonGroupAsync metod för klassen FaceServiceClient. Grupp-ID som angetts är unik för varje prenumeration att skapa gruppen – du kan också hämta, uppdatera eller ta bort PersonGroups med andra PersonGroup APIs. När en grupp definieras personer kan sedan definieras i den med hjälp av den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Metoden klienten biblioteket är CreatePersonAsync. Du kan lägga till står inför varje person när de skapas.

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
### <a name="step2-2"></a> 2.2 identifiera ytor och registrera dem till rätt person
Identifiering görs genom att skicka en ”POST” webbegäran till den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API med bildfilen i brödtexten för HTTP-begäran. När du använder klientbiblioteket körs ansikte identifiering via metoden DetectAsync för klassen FaceServiceClient.

Varje yta har identifierats kan du anropa [PersonGroup Person – Lägg till min](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) att lägga till rätt person.

Följande kod visar processen för hur du identifierar ett ansikte från en avbildning och lägga till den i en person:
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
Observera att om bilden innehåller mer än en yta, endast de största står inför har lagts till. Du kan lägga till andra ytor personen genom att skicka en sträng i formatet ”targetFace = vänstra, övre, bredden och höjden” till [PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API: er targetFace frågeparameter eller använder den valfria parametern targetFace för den AddPersonFaceAsync metod för att lägga till andra ytor. Varje yta som lagts till personen som kommer att få en unik beständiga ansikts-ID som kan användas i [PersonGroup Person – ta bort ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) och [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> Steg 3: Träna PersonGroup

PersonGroup måste utbildas innan en identifiering kan utföras med hjälp av den. Det har till retrained efter att lägga till eller ta bort någon, eller om en person har registrerade framsidan redigeras. Utbildningen görs den [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API. När du använder klientbiblioteket är bara ett anrop till metoden TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Utbildningen är en asynkron åtgärd. Den kan inte slutföras även efter att metoden TrainPersonGroupAsync returneras. Du kan behöva läsa status utbildning av [PersonGroup - Erhåll Status för utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API eller GetPersonGroupTrainingStatusAsync metoden av klientbiblioteket. Följande kod visar en enkel logik om väntande PersonGroup utbildning för att avsluta:
 
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

## <a name="step4"></a> Steg 4: Identifiera ett ansikte mot en definierad PersonGroup
När du utför identifieringar, Ansikts-API kan beräkna likhet av ett test ansikte bland alla ytor i en grupp och returnerar den mest jämförbara som för att testa yta. Detta görs via den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API eller metoden IdentifyAsync av klientbiblioteket.

De testa står inför måste identifieras med hjälp av de föregående stegen och sedan ansikte ID skickas till identifiera API som andra argument. Flera ansikte ID: N kan identifieras på en gång och resultatet innehåller alla identifiera resultat. Som standard returnerar identifiera endast en person som bäst matchar de står inför test. Om du vill kan ange du valfri parameter maxNumOfCandidatesReturned för att identifiera returnera flera kandidater.

Följande kod visar processen att identifiera:
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

När du är klar med steg som kan du försöka att identifiera olika ytor och se om ytor Anna, faktura eller Clare korrekt identifieras, enligt bilder som har överförts för identifiering av står inför. Se följande exempel:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> Steg 5: Begäran för storskaliga

Som är känd kan en PersonGroup innehålla upp till 10 000 personer på grund av begränsningar i föregående design.
Mer information om upp till miljoner skala scenarier finns [hur funktionen storskaliga](how-to-use-large-scale.md).

## <a name="summary"></a> Sammanfattning

I den här guiden har du lärt dig hur du skapar en PersonGroup och identifiera en person. Följande är en snabb påminnelse funktioner beskrivs tidigare och visas:

- Identifiera står med i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API
- Skapa PersonGroups med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API
- Skapa personer som använder den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API
- Träna en PersonGroup med hjälp av den [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API
- Identifiera okänt ytor mot PersonGroup med hjälp av den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API

## <a name="related"></a> Närliggande information

- [Hur du identifierar står i avbildningen](HowtoDetectFacesinImage.md)
- [Hur du lägger till står](how-to-add-faces.md)
- [Hur du använder funktionen storskaliga](how-to-use-large-scale.md)
