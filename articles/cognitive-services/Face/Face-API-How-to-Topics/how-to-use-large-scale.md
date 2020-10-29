---
title: 'Exempel: Använd Large-Scale funktion – ansikte'
titleSuffix: Azure Cognitive Services
description: Den här guiden är en artikel om hur du skalar upp från befintliga PersonGroup-och FaceList-objekt till LargePersonGroup-och LargeFaceList-objekt.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: b35b66615bd5c577dd73faca77d3ea20468442f8
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913525"
---
# <a name="example-use-the-large-scale-feature"></a>Exempel: Använd funktionen för storskalig skalning

Den här guiden är en avancerad artikel om hur du skalar upp från befintliga PersonGroup-och FaceList-objekt till LargePersonGroup respektive LargeFaceList-objekten. Den här guiden visar migreringsprocessen. Det förutsätter grundläggande kunskaper om PersonGroup-och FaceList-objekt, [träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) -operationen och ansikts igenkännings funktionerna. Mer information om de här ämnena finns i rikt linjer för [ansikts igenkänning](../concepts/face-recognition.md) .

LargePersonGroup och LargeFaceList kallas gemensamt för storskaliga åtgärder. LargePersonGroup kan innehålla upp till 1 000 000 personer, var och en med högst 248 ansikten. LargeFaceList kan innehålla upp till 1 000 000 ansikten. De storskaliga åtgärderna liknar de konventionella PersonGroup och FaceList, men har vissa skillnader på grund av den nya arkitekturen. 

Exemplen skrivs i C# med hjälp av klient biblioteket för Azure Cognitive Services Face.

> [!NOTE]
> Om du vill aktivera ansikts öknings prestanda för identifiering och FindSimilar i stor skala, introducerar du en åtgärd för att Förbearbeta LargeFaceList och LargePersonGroup. Inlärnings tiden varierar från sekunder till cirka en timme baserat på den faktiska kapaciteten. Under utbildnings perioden är det möjligt att utföra identifierings-och FindSimilar om en lyckad utbildning skedde innan. Nack delen är att nya personer och ansikten inte visas i resultatet förrän en ny post-migrering till storskalig utbildning har slutförts.

## <a name="step-1-initialize-the-client-object"></a>Steg 1: initiera klient objekt

När du använder klient biblioteket ansikte skickas prenumerations nyckeln och prenumerations slut punkten in via FaceClient-klassens konstruktor. Till exempel:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Om du vill hämta prenumerations nyckeln med motsvarande slut punkt går du till Azure Marketplace från Azure Portal.
Mer information finns i [prenumerationer](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Steg 2: kod migrering

Det här avsnittet fokuserar på hur du migrerar PersonGroup-eller FaceList-implementering till LargePersonGroup eller LargeFaceList. Även om LargePersonGroup eller LargeFaceList skiljer sig från PersonGroup eller FaceList i design och intern implementering, är API-gränssnitten liknande för bakåtkompatibilitet.

Datamigrering stöds inte. Du återskapar LargePersonGroup eller LargeFaceList i stället.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrera en PersonGroup till en LargePersonGroup

Det är enkelt att migrera från en PersonGroup till en LargePersonGroup. De delar exakt samma åtgärder på grupp nivå.

För PersonGroup-eller-kundrelaterad implementering behöver du bara ändra API-sökvägar eller SDK-klass/modul till LargePersonGroup och LargePersonGroup person.

Lägg till alla ansikten och personer från PersonGroup till den nya LargePersonGroup. Mer information finns i [Lägg till ansikten](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrera en FaceList till en LargeFaceList

| FaceList API:er | LargeFaceList API:er |
|:---:|:---:|
| Skapa | Skapa |
| Ta bort | Ta bort |
| Hämta | Hämta |
| Lista | Lista |
| Uppdatera | Uppdatera |
| - | Träna |
| - | Hämta träningsstatus |

Föregående tabell är en jämförelse av åtgärder på listnivå mellan FaceList och LargeFaceList. Som visas kommer LargeFaceList med nya åtgärder, träna och få inlärnings status jämfört med FaceList. Träna LargeFaceList är ett villkor för [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) -åtgärden. Träning krävs inte för FaceList. Följande kodfragment är en hjälp funktion som väntar på utbildning av en LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Tidigare har en typisk användning av FaceList med tillagda ansikten och FindSimilar tittat på följande sätt:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

När du migrerar den till LargeFaceList blir det följande:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Som tidigare visas är data hantering och FindSimilar delen i stort sett desamma. Det enda undantaget är att en ny för bearbetning tränar-åtgärd måste slutföras i LargeFaceList innan FindSimilar fungerar.

## <a name="step-3-train-suggestions"></a>Steg 3: träna förslag

Även om tåg åtgärden påskyndar [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [identifieringen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), är inlärnings tiden lidande, särskilt när den kommer till stor skala. Den uppskattade inlärnings tiden i olika skalor visas i följande tabell.

| Skala för ansikten eller personer | Uppskattad utbildnings tid |
|:---:|:---:|
| 1 000 | 1-2 SEK |
| 10 000 | 5-10 SEK |
| 100 000 | 1-2 min |
| 1,000,000 | 10-30 min |

För att bättre kunna använda funktionen för storskalig skalning rekommenderar vi följande strategier.

### <a name="step-31-customize-time-interval"></a>Steg 3,1: anpassa tidsintervall

Som det visas i `TrainLargeFaceList()` , finns det ett tidsintervall i millisekunder för att försena den oändliga status kontroll processen. För LargeFaceList med flera ansikten, minskar anropsantalet och -kostnaden med hjälp av ett större intervall. Anpassa tidsintervallet enligt förväntad kapacitet för LargeFaceList.

Samma strategi gäller även för LargePersonGroup. När du till exempel tränar en LargePersonGroup med 1 000 000 personer kan det `timeIntervalInMilliseconds` vara 60 000, vilket är ett intervall på 1 minut.

### <a name="step-32-small-scale-buffer"></a>Steg 3,2: Small-Scale buffer

Personer eller ansikten i en LargePersonGroup eller en LargeFaceList är sökbara när de har tränats. I ett dynamiskt scenario läggs nya personer eller ansikten ständigt till och måste vara omedelbart sökbara, men det kan ta längre tid än önska att träna. 

Du kan åtgärda det här problemet genom att använda en extra LargePersonGroup-eller LargeFaceList som endast buffert för nyligen tillagda poster. Den här bufferten tar kortare tid att träna på grund av mindre storlek. Den omedelbara Sök funktionen på den här tillfälliga bufferten bör fungera. Använd den här bufferten i kombination med utbildning på huvud-LargePersonGroup eller LargeFaceList genom att köra huvud träningen för ett sparse-intervall. Exempel är i mitten av natten och varje dag.

Ett exempel på ett arbetsflöde:

1. Skapa en huvud-LargePersonGroup eller LargeFaceList, som är huvud samlingen. Skapa en buffert LargePersonGroup eller LargeFaceList, som är buffertens samling. Buffertens samling är endast för nyligen tillagda personer eller ansikten.
1. Lägg till nya personer eller ansikten i både huvud samlingen och bufferten.
1. Träna bara buffert-samlingen med ett kort tidsintervall så att de nyligen tillagda posterna börjar gälla.
1. Anropa identifiering eller FindSimilar mot både huvud samlingen och bufferten. Sammanfoga resultatet.
1. När buffertens samlings storlek ökar till ett tröskelvärde eller när systemet är inaktivt skapar du en ny buffert-samling. Utlös tåg åtgärden i huvud samlingen.
1. Ta bort den gamla bufferten när åtgärden har slutförts i huvud samlingen.

### <a name="step-33-standalone-training"></a>Steg 3,3: fristående utbildning

Om en relativt lång latens är acceptabel, är det inte nödvändigt att utlösa åtgärds åtgärden direkt när du har lagt till nya data. Istället kan träningsåtgärden delas upp från den huvudsakliga logiken och utlösas regelbundet. Den här strategin är lämplig för dynamiska scenarier med acceptabel svars tid. Det kan användas för statiska scenarier för att ytterligare minska tåg frekvensen.

Anta att det finns en `TrainLargePersonGroup` funktion som liknar `TrainLargeFaceList` . En typisk implementering av den fristående utbildningen på en LargePersonGroup genom att anropa [`Timer`](/dotnet/api/system.timers.timer) klassen i `System.Timers` är:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Mer information om data hantering och identifierings-relaterade implementeringar finns i [lägga till ansikten](how-to-add-faces.md).

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du migrerar den befintliga PersonGroup-eller FaceList-koden, inte data, till LargePersonGroup eller LargeFaceList:

- LargePersonGroup och LargeFaceList fungerar ungefär som PersonGroup eller FaceList, förutom att tågets drift krävs av LargeFaceList.
- Vidta rätt tåg strategi för dynamisk data uppdatering för storskaliga data uppsättningar.

## <a name="next-steps"></a>Nästa steg

Följ en instruktions guide för att lära dig hur du lägger till ansikten i en PersonGroup eller skriver ett skript för att utföra identifierings åtgärden på en PersonGroup.

- [Lägg till ansikten](how-to-add-faces.md)
- [Snabb start för ansikts klient bibliotek](../Quickstarts/client-libraries.md)