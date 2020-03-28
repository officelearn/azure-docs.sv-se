---
title: 'Exempel: Använd funktionen Storskalig - Face'
titleSuffix: Azure Cognitive Services
description: Den här guiden är en artikel om hur du skalar upp från befintliga PersonGroup- och FaceList-objekt till LargePersonGroup- och LargeFaceList-objekt.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169771"
---
# <a name="example-use-the-large-scale-feature"></a>Exempel: Använda den storskaliga funktionen

Den här guiden är en avancerad artikel om hur du skalar upp från befintliga PersonGroup- och FaceList-objekt till LargePersonGroup- respektive LargeFaceList-objekt. Den här guiden visar migreringsprocessen. Det förutsätter en grundläggande förtrogenhet med PersonGroup och FaceList objekt, [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) drift, och ansiktsigenkänning funktioner. Mer information om dessa ämnen finns i konceptguiden [för ansiktsigenkänning.](../concepts/face-recognition.md)

LargePersonGroup och LargeFaceList kallas gemensamt för storskaliga åtgärder. LargePersonGroup kan innehålla upp till 1 miljon personer, var och en med högst 248 ansikten. LargeFaceList kan innehålla upp till 1 miljon ansikten. De storskaliga åtgärderna liknar den konventionella PersonGroup och FaceList men har vissa skillnader på grund av den nya arkitekturen. 

Exemplen skrivs i C# med hjälp av Azure Cognitive Services Face-klientbiblioteket.

> [!NOTE]
> Om du vill aktivera prestanda för ansiktssökning för identifiering och FindSimilar i stor skala introducerar du en tågåtgärd för att förbehandla LargeFaceList och LargePersonGroup. Träningstiden varierar från sekunder till ungefär en halvtimme baserat på den faktiska kapaciteten. Under träningsperioden är det möjligt att utföra Identifiering och FindSimilar om en framgångsrik utbildning har gjorts tidigare. Nackdelen är att de nya tillagda personerna och ansiktena inte visas i resultatet förrän en ny postmigrering till storskalig utbildning är klar.

## <a name="step-1-initialize-the-client-object"></a>Steg 1: Initiera klientobjektet

När du använder Face-klientbiblioteket skickas prenumerationsnyckeln och prenumerationsslutpunkten in genom konstruktorn för klassen FaceClient. Ett exempel:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Om du vill hämta prenumerationsnyckeln med motsvarande slutpunkt går du till Azure Marketplace från Azure-portalen.
Mer information finns i [Prenumerationer](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Steg 2: Kodmigrering

Det här avsnittet fokuserar på hur du migrerar PersonGroup eller FaceList-implementering till LargePersonGroup eller LargeFaceList. Även om LargePersonGroup eller LargeFaceList skiljer sig från PersonGroup eller FaceList i design och intern implementering, är API-gränssnitten liknande för bakåtkompatibilitet.

Datamigrering stöds inte. Du återskapar LargePersonGroup eller LargeFaceList i stället.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrera en persongrupp till en largepersongrupp

Migrering från en persongrupp till en LargePersonGroup är enkel. De delar exakt samma verksamhet på koncernnivå.

För PersonGroup- eller personrelaterad implementering är det nödvändigt att bara ändra API-sökvägarna eller SDK-klass/modulen till LargePersonGroup och LargePersonGroup Person.

Lägg till alla ansikten och personer från PersonGroup i den nya LargePersonGroup. Mer information finns i [Lägga till ansikten](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrera en facelist till en LargeFaceList

| FaceList API:er | LargeFaceList API:er |
|:---:|:---:|
| Skapa | Skapa |
| Ta bort | Ta bort |
| Hämta | Hämta |
| Visa lista | Visa lista |
| Uppdatering | Uppdatering |
| - | Träna |
| - | Hämta träningsstatus |

Föregående tabell är en jämförelse av åtgärder på listnivå mellan FaceList och LargeFaceList. Som visas kommer LargeFaceList med nya operationer, Train and Get Training Status, jämfört med FaceList. Utbildning av LargeFaceList är en förutsättning för [FindSimilar-operationen.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) Utbildning krävs inte för FaceList. Följande utdrag är en hjälpfunktion som väntar på utbildning av en LargeFaceList:

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

Tidigare såg en typisk användning av FaceList med tillagda ansikten och FindSimilar ut så här:

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

När du migrerar den till LargeFaceList blir den följande:

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

Som tidigare visats är datahanteringen och FindSimilar-delen nästan desamma. Det enda undantaget är att en ny förbearbetning Tågåtgärd måste slutföras i LargeFaceList innan FindSimilar fungerar.

## <a name="step-3-train-suggestions"></a>Steg 3: Tågförslag

Även om tågdriften snabbar upp [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [Identifiering,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)lider träningstiden, särskilt när den kommer i stor skala. Den uppskattade träningstiden i olika skalor visas i följande tabell.

| Skala för ansikten eller personer | Beräknad träningstid |
|:---:|:---:|
| 1,000 | 1-2 sek |
| 10 000 | 5-10 sek |
| 100 000 | 1-2 min |
| 1,000,000 | 10-30 min |

För att bättre utnyttja den storskaliga funktionen rekommenderar vi följande strategier.

### <a name="step-31-customize-time-interval"></a>Steg 3.1: Anpassa tidsintervall

Som visas `TrainLargeFaceList()`i finns det ett tidsintervall i millisekunder för att fördröja den oändliga processen för kontroll av träningsstatus. För LargeFaceList med flera ansikten, minskar anropsantalet och -kostnaden med hjälp av ett större intervall. Anpassa tidsintervallet enligt largefacelists förväntade kapacitet.

Samma strategi gäller även för LargePersonGroup. När du till exempel tränar en LargePersonGroup `timeIntervalInMilliseconds` med 1 miljon personer kan det vara 60 000, vilket är ett 1-minutersintervall.

### <a name="step-32-small-scale-buffer"></a>Steg 3.2: Småskalig buffert

Personer eller ansikten i en LargePersonGroup eller en LargeFaceList är sökbara först efter att ha tränats. I ett dynamiskt scenario läggs nya personer eller ansikten ständigt till och måste vara omedelbart sökbara, men utbildningen kan ta längre tid än önskat. 

Om du vill minska problemet använder du en extra småskalig LargePersonGroup eller LargeFaceList som buffert endast för de nyligen tillagda posterna. Den här bufferten tar kortare tid att träna på grund av den mindre storleken. Den omedelbara sökfunktionen på den här tillfälliga bufferten bör fungera. Använd den här bufferten i kombination med utbildning på huvudstorgruppen eller LargeFaceList genom att köra huvudutbildningen med ett glesare intervall. Exempel är mitt i natten och dagligen.

Ett exempelarbetsflöde:

1. Skapa en huvudstorgrupp eller LargeFaceList, som är huvudsamlingen. Skapa en buffert LargePersonGroup eller LargeFaceList, som är buffertsamlingen. Buffertsamlingen är endast för nyligen tillagda personer eller ansikten.
1. Lägg till nya personer eller ansikten i både huvudsamlingen och buffertsamlingen.
1. Träna endast buffertsamlingen med ett kort tidsintervall för att säkerställa att de nyligen tillagda transaktionerna börjar gälla.
1. Samtalsidentifiering eller FindSimilar mot både huvudsamlingen och buffertsamlingen. Slå ihop resultaten.
1. När buffertsamlingsstorleken ökar till ett tröskelvärde eller vid en inaktiv tid för systemet skapar du en ny buffertsamling. Utlösa tågåtgärden på huvudsamlingen.
1. Ta bort den gamla buffertsamlingen när tågåtgärden är klar i huvudsamlingen.

### <a name="step-33-standalone-training"></a>Steg 3.3: Fristående utbildning

Om en relativt lång svarstid är acceptabel är det inte nödvändigt att utlösa tågåtgärden direkt efter att du har lagt till nya data. Istället kan träningsåtgärden delas upp från den huvudsakliga logiken och utlösas regelbundet. Den här strategin är lämplig för dynamiska scenarier med acceptabel svarstid. Den kan tillämpas på statiska scenarier för att ytterligare minska tågfrekvensen.

Anta att det `TrainLargePersonGroup` finns `TrainLargeFaceList`en funktion som liknar . En typisk implementering av den fristående utbildningen på en [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) LargePersonGroup genom att anropa klassen i `System.Timers` är:

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

Mer information om datahantering och identifieringsrelaterade implementeringar finns i [Lägga till ansikten](how-to-add-faces.md) och Identifiera ansikten i en [bild](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du migrerar den befintliga PersonGroup- eller FaceList-koden, inte data, till LargePersonGroup eller LargeFaceList:

- LargePersonGroup och LargeFaceList fungerar ungefär som PersonGroup eller FaceList, förutom att tågåtgärden krävs av LargeFaceList.
- Ta rätt Tågstrategi till dynamisk datauppdatering för storskaliga datauppsättningar.

## <a name="next-steps"></a>Nästa steg

Följ en vägledning om hur du lär dig hur du lägger till ansikten i en persongrupp eller kör åtgärden Identifiera på en persongrupp.

- [Lägg till ansikten](how-to-add-faces.md)
- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
