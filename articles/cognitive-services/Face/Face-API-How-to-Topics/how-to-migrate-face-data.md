---
title: Migrera dina ansikte data mellan prenumerationer – Ansikts-API
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du migrerar dina lagrade ansikts-data från en Ansikts-API-prenumeration till en annan.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 433386ac873b5064b3f71650480b0807e96ddbbd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669526"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrera dina ansikts-data till en annan Ansikts-prenumeration

Den här guiden visar hur du flyttar ansikte data (till exempel en sparad **PersonGroup** för ansikten) till en annan Ansikts-API-prenumeration med hjälp av funktionen för ögonblicksbilder. Detta gör att du slipper att upprepade gånger skapa och träna en **PersonGroup** eller **FaceList** när du flyttar eller utöka din verksamhet. Exempel: du kan ha skapat en **PersonGroup** med en kostnadsfri utvärderingsversion av prenumeration och nu vill migrera den till din betalda prenumeration eller du kan behöva synkronisera ansikte data över regioner för ett stort företag-åtgärden.

Det här samma migreringsstrategi gäller även för **LargePersonGroup** och **LargeFaceList** objekt. Om du inte är bekant med principerna i den här guiden finns i deras definitioner i den [ordlista](../Glossary.md). Den här guiden använder klientbiblioteket för .NET för Ansikts-API med C#.

## <a name="prerequisites"></a>Förutsättningar

- Två Ansikts-API prenumerationsnycklar (med befintliga data och en för att migrera till). Följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på Ansikts-API-tjänst och få din nyckel.
- Ansikts-API-prenumeration ID-sträng för målprenumerationen (finns i den **översikt** bladet på Azure portal). 
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Den här guiden använder en enkel konsolapp för att köra datamigrering ansikte. En fullständig implementering finns i den [Ansikts-API-ögonblicksbild exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) på GitHub.

1. I Visual Studio skapar du en ny **konsolapp (.NET Framework)** projektet och ge den namnet **FaceApiSnapshotSample**. 
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Klicka på fliken **Bläddra** och välj **Inkludera förhandsversion**. Leta sedan reda på och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Skapa ansikts-klienter

I den **Main** -metod i *Program.cs*, skapar två **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** instanser för dina prenumerationer för källa och mål. I det här exemplet använder vi en Ansikts-prenumeration i regionen östra Asien som källa och en prenumeration för USA, västra som mål. Detta visar hur du migrerar data från en Azure-region till en annan. Om dina prenumerationer finns i olika regioner, kommer du behöva ändra den `Endpoint` strängar.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Du måste fylla i prenumerationen nyckelvärden och slutpunkten URL: er för dina prenumerationer för källa och mål.


## <a name="prepare-a-persongroup-for-migration"></a>Förbereda en PersonGroup för migrering

Du behöver ID för den **PersonGroup** i din käll-prenumeration för att migrera den till målprenumerationen. Använd den **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** metod för att hämta en lista över dina **PersonGroup** objekt; sedan den **[ PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** egenskapen. Den här processen ser annorlunda ut beroende på vad **PersonGroup** objekt du har. I det här hjälper källan **PersonGroup** ID lagras i `personGroupId`.

> [!NOTE]
> Den [exempelkoden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) skapar och träna en ny **PersonGroup** för migreringen, men i de flesta fall bör du redan ha en **PersonGroup** att använda.

## <a name="take-snapshot-of-persongroup"></a>Ta ögonblicksbild av PersonGroup

En ögonblicksbild är en tillfällig Fjärrlagring för vissa Ansikts-datatyper. Den fungerar som en typ av Urklipp vid funktionen Kopiera data från en prenumeration till en annan. Först användaren ”hämtar” en ögonblicksbild av data i ursprungligt abonnemang, och sedan de ”gäller” det till ett nytt dataobjekt i målprenumerationen.

Använda källprenumerationen **FaceClient** -instans som ska ta en ögonblicksbild av den **PersonGroup**med hjälp av **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** med  **PersonGroup** ID och mål-prenumerations-ID. Om du har flera målprenumerationer kan du lägga till dem som matris poster i den tredje parametern.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Processen för att utföra och tillämpa ögonblicksbilder inte kommer att störa något regelbundna anrop till käll- eller **PersonGroup**s (eller **FaceList**s). Men vi rekommenderar inte samtidiga anrop som ändrar källobjektet ([ansikte lista hanteringsanrop](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist) eller [Persongrupp – träna](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train) anropar, till exempel), eftersom ögonblicksbildsåtgärden kan Kör före eller efter dessa åtgärder eller kan det uppstå fel. 

## <a name="retrieve-the-snapshot-id"></a>Hämta ögonblicksbild-ID

Ögonblicksbilden med metoden är asynkron, så du måste vänta tills den har slutförandet (det inte går att avbryta operations snapshot). I den här koden i `WaitForOperation` metoden övervakar asynkrona anrop, kontrollerar statusen varje 100 MS. När åtgärden har slutförts kommer du att kunna hämta åtgärds-ID. Du kan skaffa det genom att parsa den `OperationLocation` fält. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

En typisk `OperationLocation` värde ser ut så här:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Den `WaitForOperation` hjälpmetoden är här:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

När Åtgärdsstatus markeras som `Succeeded`, du kan sedan hämta ögonblicksbild-ID genom att parsa den `ResourceLocation` i den returnerade **OperationStatus** instans.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

En typisk `resourceLocation` värde ser ut så här:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Tillämpa ögonblicksbilden till målprenumerationen

Skapa sedan den nya **PersonGroup** i målprenumerationen, med ett slumpmässigt genererat-ID. Använd sedan målprenumerationen **FaceClient** instans att tillämpa ögonblicksbilden på den här PersonGroup passerar i ögonblicksbild-ID och ny **PersonGroup** -ID. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Ett Snapshot-objekt är endast giltig i 48 timmar. Du bör bara ta en ögonblicksbild om du tänker använda den för datamigrering strax efter.

En ögonblicksbild tillämpa begäran returnerar en annan åtgärd-ID. Du kan få detta ID genom att parsa den `OperationLocation` i den returnerade **applySnapshotResult** instans. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Programprocessen ögonblicksbild är också asynkron, så återigen att använda `WaitForOperation` att vänta tills den är klar.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testa migrering av data

När du har använt ögonblicksbilden, den nya **PersonGroup** i mål-prenumeration bör fyllas i med de ursprungliga ansikts-data. Som standard kopieras även utbildning resultat, så den nya **PersonGroup** strax redo för face ID anrop utan att behöva träna.

Du kan köra följande åtgärder och jämför resultatet de skrivs ut på konsolen för att testa migrering av data.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Använd följande helper-metoder:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Nu kan du börja använda den nya **PersonGroup** i målprenumerationen. 

Om du vill uppdatera målet **PersonGroup** igen i framtiden, behöver du skapa en ny **PersonGroup** (Följ stegen i den här guiden) att ta emot ögonblicksbilden. En enda **PersonGroup** objekt kan bara ha en ögonblicksbild som tillämpas på en gång.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar migrerar ansikte data rekommenderar vi att du manuellt ta bort snapshot-objekt.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation för ögonblicksbild (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Ansikts-API: et ögonblicksbild exemplet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Hur du lägger till ansikten](how-to-add-faces.md)
- [Känna igen ansikten i en bild](HowtoDetectFacesinImage.md)
- [Hur du identifierar ansikten i bild](HowtoIdentifyFacesinImage.md)
