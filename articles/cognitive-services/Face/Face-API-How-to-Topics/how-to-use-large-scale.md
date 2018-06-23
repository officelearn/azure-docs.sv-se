---
title: Funktionen storskaliga i de står inför API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Använd funktionen storskaliga i Ansikts-API för kognitiva Services.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351861"
---
# <a name="how-to-use-the-large-scale-feature"></a>Hur du använder funktionen storskaliga

Den här handboken är en avancerad artikel på koden migrering till att skala upp från befintliga PersonGroup och FaceList LargePersonGroup och LargeFaceList respektive.
Den här guiden visar migreringsprocessen med antagandet att veta grundläggande användning av PersonGroup och FaceList.
För att hämta bekant med grundläggande åtgärder, se andra självstudiekurser som [så här identifierar du ytor i bilder](HowtoIdentifyFacesinImage.md),

Microsoft Ansikts-API släppte nyligen två funktioner för att aktivera storskaliga scenarier, LargePersonGroup och LargeFaceList som gemensamt kallas för storskaliga åtgärder.
LargePersonGroup kan innehålla upp till 1 000 000 personer varje med högst 248 ytor och LargeFaceList kan innehålla upp till 1 000 000 ytor.

Storskaliga åtgärder liknar konventionella PersonGroup och FaceList, men har vissa viktiga skillnader på grund av den nya arkitekturen.
Den här guiden visar migreringsprocessen med antagandet att veta grundläggande användning av PersonGroup och FaceList.
Exemplen är skrivna i C# med hjälp av klientbiblioteket Ansikts-API.

Om du vill aktivera ansikte sökningsprestanda för identifiering och FindSimilar i stor skala måste du införa en Train-åtgärden för att Förbearbeta LargeFaceList och LargePersonGroup.
Utbildningstid varierar från sekunder till ungefär en halvtimme beroende på den faktiska kapaciteten.
Under utbildning är det fortfarande möjligt att utföra identifiering och FindSimilar om en lyckad utbildning görs innan.
Dock är nackdelen att nya tillagda personer/ytorna inte visas i resultatet tills en ny postmigrering till storskaliga utbildning har slutförts.

## <a name="concepts"></a> Begrepp

Om du inte är bekant med följande begrepp i den här handboken definitioner kan hittas i den [ordlista](../Glossary.md):

- LargePersonGroup: En samling av personer med kapacitet upp till 1 000 000.
- LargeFaceList: En samling av ytor med kapacitet upp till 1 000 000.
- Train: En innan processen för att säkerställa identifiering/FindSimilar prestanda.
- Identifiering: Identifiera en eller flera ytor från en PersonGroup eller LargePersonGroup.
- FindSimilar: Sök liknande ytor från en FaceList eller LargeFaceList.

## <a name="initialization"></a> Steg 1: Ge API-anrop

När du använder klientbiblioteket Ansikts-API, skickas den prenumeration och prenumeration endpoint i via konstruktorn för klassen FaceServiceClient. Exempel:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Nyckeln prenumerationen med motsvarande slutpunkt kan hämtas från Marketplace-sidan i din Azure-portalen.
Se [prenumerationer](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Steg 2: Kod migrering i åtgärd

Det här avsnittet fokuserar enbart på migrera PersonGroup/FaceList-implementeringen så att LargePersonGroup/LargeFaceList.
Även om LargePersonGroup/LargeFaceList skiljer sig från PersonGroup/FaceList i design- och interna implementeringen, är API-gränssnitt liknande för tillbaka-kompatibilitet.

Migrering av data stöds inte, måste du återskapa LargePersonGroup/LargeFaceList i stället.

## <a name="largepersongroup"></a> Steg 2.1: Migrera PersonGroup till LargePersonGroup

Migrering från PersonGroup till LargePersonGroup är smooth eftersom de har exakt samma gruppnivå-åtgärder.

Relaterade implementering för PersonGroup/Person behöver bara ändra API sökvägar eller SDK klassen/modulen till LargePersonGroup och LargePersonGroup Person.

Vad gäller migrering av data finns [hur du lägger till står](how-to-add-faces.md) referens.

## <a name="largefacelist"></a> Steg 2.2: Migrera FaceList till LargeFaceList

| FaceList API: er | LargeFaceList API: er |
|:---:|:---:|
| Skapa | Skapa |
| Ta bort | Ta bort |
| Hämta | Hämta |
| Visa lista | Visa lista |
| Uppdatering | Uppdatering |
| - | Lär in |
| - | Hämta Status för utbildning |

I föregående tabell är en jämförelse av nivå åtgärder mellan FaceList och LargeFaceList.
Som visas, innehåller LargeFaceList nya funktioner, tåg och få utbildning Status jämfört med FaceList.
Det är en förutsättning för att få LargeFaceList tränas [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) åtgärden under där är ingen Train som krävs för FaceList.
Följande kodavsnitt är en hjälpfunktion för utbildning av en LargeFaceList.

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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

Tidigare var skulle en typisk användning av FaceList med att lägga till ytor och FindSimilar vara

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

När du migrerar den till LargeFaceList, ska den bli

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Som visas ovan, är datahantering och FindSimilar del nästan desamma.
Det enda undantaget är att en ny före bearbetning Train-åtgärden måste slutföras i LargeFaceList innan FindSimilar fungerar.

## <a name="train"></a>Steg 3: Träna förslag

Även om åtgärden Train snabbare den [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), särskilt när kommer för storskaliga drabbas av utbildningstid.
Den uppskattade utbildningstid i olika skalor anges i följande tabell:

| Skala (ytor eller personer) | Uppskattad tid utbildning |
|:---:|:---:|
| 1,000 | 1 – 2 s |
| 10 000 | 5-10 s |
| 100,000 | 1 - 2 min |
| 1,000,000 | 10 - 30 minuter |

För att bättre kunna utnyttja funktionen storskaliga, rekommenderas några strategier att ta hänsyn till.

## <a name="interval"></a>Steg 3.1: Anpassa tidsintervall

Som visas i den `TrainLargeFaceList()`, det finns en `timeIntervalInMilliseconds` att fördröja oändlig utbildning status kontrolleras.
För LargeFaceList med flera ytor minskar med ett större intervall antalet anrop och kostnaden.
Tidsintervallet bör anpassas enligt LargeFaceList förväntade kapacitet.

Samma strategi gäller även för LargePersonGroup.
Till exempel när en LargePersonGroup utbildning med 1 000 000 personer i `timeIntervalInMilliseconds` kan vara 60 000 (kallas även 1-minutersintervall).

## <a name="buffer"></a>Steg 3.2 småskaliga buffert

Personer/ytor i LargePersonGroup/LargeFaceList är sökbar endast efter att du håller på att tränas.
I ett scenario med dynamiska nya personer/ytorna läggs till kontinuerligt och måste vara sökbara omedelbart, men utbildning kan ta längre tid än önskad.
För att åtgärda problemet, kan du använda en extra småskaliga LargePersonGroup/LargeFaceList som en buffert endast för de nyligen tillagda posterna.
Bufferten tar kortare tid att träna på grund av mycket mindre storlek och omedelbar sökningen på den här temporär buffert ska fungera.
Använd denna buffert i kombination med utbildning på master LargePersonGroup/LargeFaceList genom att köra master utbildning på ett mer sparse intervall, till exempel halva natten, och varje dag.

En Exempelarbetsflöde:
1. Skapa en master LargePersonGroup/LargeFaceList (master samling) och en buffert LargePersonGroup/LargeFaceList (buffert samling). Samlingen bufferten är endast för nytillagda personer/ytor.
1. Lägga till nya personer/ytorna i både master samlingen och samlingen buffert.
1. Endast träna buffert samlingen med ett kort tidsintervall så nyligen tillagda poster gälla.
1. Anropa identifiering/FindSimilar mot både master samlingen och bufferten samlingen och sammanfoga resultatet.
1. När samlingen buffertstorlek ökar med ett tröskelvärde eller i taget inaktiv, skapa en ny samling bufferten och utlösa tåget på master samling.
1. Ta bort den gamla buffer samlingen efter att utbildning på samlingen master.

## <a name="standalone"></a>Steg 3.3 fristående utbildning

Om en relativt lång svarstid som är acceptabel, behöver du inte aktivera Train åtgärden direkt efter att lägga till nya data.
I stället kan Train-åtgärden dela från den huvudsakliga logiken och utlöses regelbundet.
Den här strategin är lämplig för dynamiska scenarier med acceptabel svarstid och kan tillämpas på statisk scenarier för att ytterligare minska frekvensen tåget.

Anta att det finns en `TrainLargePersonGroup` funktion som liknar den `TrainLargeFaceList`.
En typisk implementering av fristående utbildning på LargePersonGroup genom att anropa den [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klassen i `System.Timers` skulle vara:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Mer information om hantering och identifiering-relaterade implementeringar finns [hur du lägger till står](how-to-add-faces.md) och [hur du identifierar står i bilden](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Sammanfattning

I den här guiden har du lärt dig hur du migrerar den befintliga koden PersonGroup/FaceList (inte data) till LargePersonGroup/LargeFaceList:

- LargePersonGroup och LargeFaceList fungerar liknar PersonGroup/FaceList, förutom Train åtgärden krävs av LargeFaceList.
- Ta rätt train-strategi till dynamisk uppdatering för storskaliga dataset.

## <a name="related"></a> Närliggande information

- [Så här identifierar du står i avbildningen](HowtoIdentifyFacesinImage.md)
- [Hur du lägger till står](how-to-add-faces.md)
