---
title: 'Exempel: Funktionen storskaliga – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda den storskaliga funktionen i Ansikts-API.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449050"
---
# <a name="example-use-the-large-scale-feature"></a>Exempel: Använda den storskaliga funktionen

Den här guiden är en avancerad artikel om hur du skala upp från befintliga PersonGroup och FaceList objekt till LargePersonGroup och LargeFaceList objekt, respektive. Den här guiden visar migreringsprocessen. Det förutsätts att en grundläggande kunskaper om PersonGroup och FaceList objekt, den [träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) åtgärden och funktionerna för igenkänning av ansikte. Mer information om dessa ämnen finns den [står inför erkännande](../concepts/face-recognition.md) konceptuella guide.

LargePersonGroup och LargeFaceList kallas gemensamt för storskaliga åtgärder. LargePersonGroup kan innehålla upp till 1 miljon personer, var och en med högst 248 ansikten. LargeFaceList kan innehålla upp till 1 miljon ansikten. Storskaliga åtgärder liknar konventionella PersonGroup och FaceList men med vissa skillnader på grund av den nya arkitekturen. 

Exemplen är skrivna i C# med hjälp av klientbiblioteket för Azure Cognitive Services Ansikts-API.

> [!NOTE]
> Aktivera ansikte search prestanda för identifiering och FindSimilar i stor skala genom att införa en Train-åtgärd för att Förbearbeta LargeFaceList och LargePersonGroup. Utbildning-tiden varierar från sekunder till ungefär en halvtimme baserat på den faktiska kapaciteten. Det är möjligt att utföra identifiering och FindSimilar om en lyckad utbildning som fungerar gjordes innan under hela utbildning. Nackdelen är att de nya har lagts till personer och ansikten inte visas i resultatet tills en ny postmigrering till storskaliga utbildning har slutförts.

## <a name="step-1-initialize-the-client-object"></a>Steg 1: Initiera klientobjektet

När du använder klientbiblioteket Ansikts-API, skickas prenumerationsnyckel och prenumerationens slutpunkt via konstruktor i klassen FaceClient. Exempel:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Prenumerationsnyckel med dess motsvarande slutpunkt, gå till Azure Marketplace från Azure-portalen.
Mer information finns i [prenumerationer](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Steg 2: Kod migrering

Det här avsnittet fokuserar på hur du migrerar PersonGroup eller FaceList implementering till LargePersonGroup eller LargeFaceList. Även om de skiljer sig från PersonGroup eller FaceList i utformningen och interna implementeringen LargePersonGroup eller LargeFaceList, är API-gränssnitt liknande för bakåtkompatibilitet.

Migrering av data stöds inte. Du återskapa LargePersonGroup eller LargeFaceList i stället.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrera en PersonGroup till en LargePersonGroup

Migrering från en PersonGroup till en LargePersonGroup är enkelt. De delar exakt samma gruppnivå-åtgärder.

För PersonGroup eller person relaterade implementering är det nödvändigt att ändra den API-sökvägar eller SDK-klass/modulen LargePersonGroup och LargePersonGroup Person.

Lägg till alla ansikten och personer från PersonGroup till den nya LargePersonGroup. Mer information finns i [lägga till ansikten](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrera en FaceList till en LargeFaceList

| FaceList API:er | LargeFaceList API:er |
|:---:|:---:|
| Skapa | Skapa |
| Ta bort | Ta bort |
| Hämta | Hämta |
| List | List |
| Uppdatera | Uppdatera |
| - | Träna |
| - | Hämta träningsstatus |

Föregående tabell är en jämförelse av åtgärder på listnivå mellan FaceList och LargeFaceList. Visas kan LargeFaceList som levereras med nya funktioner, träna upp och hämta utbildning Status jämfört med FaceList. Utbildning i LargeFaceList är ett villkor för den [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) igen. Utbildning krävs inte för FaceList. Följande fragment är en hjälpfunktionen vänta utbildning av en LargeFaceList:

```CSharp
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

En vanlig användning av FaceList med tillagda ansikten och FindSimilar tidigare såg ut som följande:

```CSharp
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

När du migrerar den till LargeFaceList, blir den följande:

```CSharp
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

Som du tidigare såg är datahantering och FindSimilar delen nästan desamma. Det enda undantaget är att en ny förbearbetning träna åtgärd måste slutföras i LargeFaceList innan FindSimilar fungerar.

## <a name="step-3-train-suggestions"></a>Steg 3: Träna förslag

Även om åtgärden träna påskyndar [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), utbildning tiden drabbas, särskilt när snart i stor skala. Den uppskattade utbildning tiden i olika skalor visas i följande tabell.

| Skala för ansikten eller personer | Beräknad utbildning |
|:---:|:---:|
| 1,000 | 1 – 2 sek |
| 10 000 | 5 – 10 sek |
| 100,000 | 1 – 2 minuter |
| 1,000,000 | 10-30 min |

För att bättre utnyttja funktionen storskaliga, rekommenderar vi följande strategier.

### <a name="step-31-customize-time-interval"></a>Steg 3.1: Anpassa tidsintervall

Som visas i `TrainLargeFaceList()`, det finns ett tidsintervall i millisekunder för att fördröja oändlig utbildning statusen kontrollerar processen. För LargeFaceList med flera ansikten, minskar anropsantalet och -kostnaden med hjälp av ett större intervall. Anpassa tidsintervall enligt förväntade kapaciteten för LargeFaceList.

Samma strategin gäller även för LargePersonGroup. Till exempel när du tränar en LargePersonGroup med 1 miljon personer `timeIntervalInMilliseconds` kanske 60 000, vilket är en 1-minutersintervall.

### <a name="step-32-small-scale-buffer"></a>Steg 3.2: Småskalig bufferten

Personer eller ansikten i en LargePersonGroup eller en LargeFaceList är sökbara först efter att du håller på att tränas. I ett scenario med dynamiska nya personer eller ansikten läggs hela tiden och måste vara sökbar omedelbart, men utbildning kan ta längre tid än önskad. 

För att lösa problemet, Använd en extra småskalig LargePersonGroup eller LargeFaceList som en buffert enbart för de nytillagda posterna. Bufferten tar kortare tid att träna på grund av den mindre storleken. Omedelbar search-funktionen på den här tillfälliga bufferten ska fungera. Använd denna buffert i kombination med utbildning om master LargePersonGroup eller LargeFaceList genom att köra master utbildning med sparser intervall. Exempel är mitt i natten och varje dag.

Ett exempelarbetsflöde:

1. Skapa en master LargePersonGroup eller LargeFaceList, vilket är den överordnade samlingen. Skapa en buffert LargePersonGroup eller LargeFaceList, vilket är en buffert-samling. Samlingen bufferten är endast för nyligen tillagda personer eller ansikten.
1. Lägga till nya personer eller ansikten för både den överordnade samlingen och samlingen buffert.
1. Endast träna i bufferten samlingen med en kort tidsintervall för att gälla de nytillagda posterna.
1. Anropa identifiering eller FindSimilar mot både samlingen master och samlingen buffert. Sammanfoga resultaten.
1. När samlingen buffertstorleken ökar till ett tröskelvärde eller i taget inaktiv, skapar du en ny buffert-samling. Utlös Train-åtgärden på den överordnade samlingen.
1. Ta bort den gamla buffer-samlingen när tåget är klar på den överordnade samlingen.

### <a name="step-33-standalone-training"></a>Steg 3.3: Fristående utbildning

Om en relativt lång svarstid är godtagbara kan du behöver inte utlösare rätt träna igen när du lägger till nya data. Istället kan träningsåtgärden delas upp från den huvudsakliga logiken och utlösas regelbundet. Den här strategin är lämplig för dynamisk scenarier med acceptabel svarstid. Det kan tillämpas på statisk scenarier för att ytterligare minska frekvensen träna.

Anta att det finns en `TrainLargePersonGroup` funktion liknande `TrainLargeFaceList`. En typisk implementering av fristående utbildning om en LargePersonGroup genom att aktivera den [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klassen i `System.Timers` är:

```CSharp
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

Läs mer om datahantering och identifiering-relaterade implementeringar [lägga till ansikten](how-to-add-faces.md) och [identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Sammanfattning

I den här handboken beskrivs hur du migrerar befintliga PersonGroup eller FaceList kod, inte data till LargePersonGroup eller LargeFaceList:

- LargePersonGroup och LargeFaceList fungerar liknar PersonGroup eller FaceList, förutom att träna åtgärden krävs av LargeFaceList.
- Vidta rätt Train-strategi för att dynamisk uppdatering för storskaliga datauppsättningar.

## <a name="next-steps"></a>Nästa steg

Följ en instruktionsguide för att lära dig att lägga till ansikten i en PersonGroup eller köra identifiering igen på en PersonGroup.

- [Lägg till ansikten](how-to-add-faces.md)
- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)