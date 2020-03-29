---
title: Migrera dina ansiktsdata över prenumerationer - Face
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du migrerar dina lagrade ansiktsdata från en Face-prenumeration till en annan.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169815"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrera dina ansiktsdata till en annan Face-prenumeration

Den här guiden visar hur du flyttar ansiktsdata, till exempel ett sparat PersonGroup-objekt med ansikten, till en annan Azure Cognitive Services Face-prenumeration. Om du vill flytta data använder du funktionen Ögonblicksbild. På så sätt undviker du att behöva bygga och träna ett PersonGroup- eller FaceList-objekt upprepade gånger när du flyttar eller expanderar verksamheten. Du kanske till exempel har skapat ett PersonGroup-objekt med hjälp av en kostnadsfri utvärderingsprenumeration och nu vill migrera det till din betalda prenumeration. Du kan också behöva synkronisera ansiktsdata mellan prenumerationer i olika regioner för en stor företagsåtgärd.

Samma migreringsstrategi gäller även för LargePersonGroup- och LargeFaceList-objekt. Om du inte är bekant med begreppen i den här guiden läser du deras definitioner i konceptguiden [för ansiktsigenkänning.](../concepts/face-recognition.md) Den här guiden använder Face .NET-klientbiblioteket med C#.

## <a name="prerequisites"></a>Krav

Du behöver följande:

- Två Face-prenumerationsnycklar, en med befintliga data och en att migrera till. Om du vill prenumerera på Ansiktstjänsten och hämta din nyckel följer du instruktionerna i [Skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Den Face-prenumerations-ID-sträng som motsvarar målprenumerationen. Om du vill hitta den väljer du **Översikt** i Azure-portalen. 
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Den här guiden använder en enkel konsolapp för att köra ansiktsdatamigrering. En fullständig implementering finns i [exemplet med ögonblicksbilden face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) på GitHub.

1. Skapa ett nytt konsolapp .NET Framework-projekt i Visual Studio. Namn det **FaceApiSnapshotSample**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Lösningsutforskaren och välj **Hantera NuGet-paket**. Välj fliken **Bläddra** och välj **Inkludera förhandsversion**. Hitta och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-förhandsversion](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Skapa ansiktsklienter

Skapa två [FaceClient-instanser](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) för käll- och målprenumerationer i **huvudmetoden** i *Program.cs.* I det här exemplet används en Face-prenumeration i regionen Östasien som källa och en prenumeration i västra USA som mål. Det här exemplet visar hur du migrerar data från en Azure-region till en annan. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

Fyll i prenumerationsnyckelvärdena och slutpunktsadresserna för dina käll- och målprenumerationer.


## <a name="prepare-a-persongroup-for-migration"></a>Förbereda en persongrupp för migrering

Du behöver persongruppens ID i källprenumerationen för att migrera det till målprenumerationen. Använd metoden [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) för att hämta en lista över dina PersonGroup-objekt. Hämta sedan egenskapen [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Den här processen ser annorlunda ut beroende på vilka PersonGroup-objekt du har. I den här guiden lagras källpersongrupps-ID i `personGroupId`.

> [!NOTE]
> [Exempelkoden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) skapar och tränar en ny PersonGroup att migrera. I de flesta fall bör du redan ha en PersonGroup att använda.

## <a name="take-a-snapshot-of-a-persongroup"></a>Ta en ögonblicksbild av en persongrupp

En ögonblicksbild är tillfällig fjärrlagring för vissa Face-datatyper. Den fungerar som ett slags Urklipp för att kopiera data från en prenumeration till en annan. Först tar du en ögonblicksbild av data i källprenumerationen. Sedan använder du det på ett nytt dataobjekt i målprenumerationen.

Använd källprenumerationens FaceClient-instans för att ta en ögonblicksbild av PersonGroup. Använd [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) med PersonGroup-ID:t och målprenumerationens ID. Om du har flera målprenumerationer lägger du till dem som matrisposter i den tredje parametern.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Processen att ta och tillämpa ögonblicksbilder stör inte några vanliga anrop till källan eller rikta persongrupper eller facelists. Ring inte samtidiga samtal som ändrar källobjektet, till exempel [FaceList-hanteringsanrop](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) eller [persongruppstågsanropet,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) till exempel. Ögonblicksbildåtgärden kan köras före eller efter dessa åtgärder eller kan stöta på fel.

## <a name="retrieve-the-snapshot-id"></a>Hämta ögonblicksbild-ID:et

Den metod som används för att ta ögonblicksbilder är asynkron, så du måste vänta på att den har slutförts. Det går inte att avbryta ögonblicksbildåtgärder. I den `WaitForOperation` här koden övervakar metoden det asynkrona anropet. Den kontrollerar status var 100 ms. När åtgärden är klar hämtar du ett operations-ID genom att tolka fältet. `OperationLocation` 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Ett `OperationLocation` typiskt värde ser ut så här:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Hjälpmetoden `WaitForOperation` är här:

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

När åtgärdsstatusen visar `Succeeded`hämtar du ögonblicksbild-ID:n genom att tolka `ResourceLocation` fältet för den returnerade OperationStatus-instansen.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Ett `resourceLocation` typiskt värde ser ut så här:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Använda en ögonblicksbild på en målprenumeration

Skapa sedan den nya PersonGroup i målprenumerationen med hjälp av ett slumpmässigt genererat ID. Använd sedan målprenumerationens FaceClient-instans för att tillämpa ögonblicksbilden på den här persongruppen. Skicka i ögonblicksbild-ID och det nya PersonGroup-ID:et.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Ett Snapshot-objekt är giltigt i endast 48 timmar. Ta bara en ögonblicksbild om du tänker använda den för datamigrering strax efter.

En begäran om att tillämpa ögonblicksbild returnerar ett annat åtgärds-ID. Om du vill hämta det `OperationLocation` här ID:t tolkar du fältet för den returnerade applySnapshotResult-instansen. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Ögonblicksbilden programprocessen är också asynkron, `WaitForOperation` så återigen använda för att vänta på att det ska slutföras.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testa datamigrering

När du har tillämpat ögonblicksbilden fylls den nya PersonGroup i målprenumerationen med de ursprungliga ansiktsdata. Som standard kopieras även träningsresultat. Den nya PersonGroup är redo för ansiktsidentifieringsamtal utan att behöva omskolning.

Om du vill testa datamigreringen kör du följande åtgärder och jämför de resultat de skriver ut på konsolen:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Använd följande hjälpmetoder:

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

Nu kan du använda den nya PersonGroup i målprenumerationen. 

Om du vill uppdatera målpersongruppen igen i framtiden skapar du en ny PersonGroup för att ta emot ögonblicksbilden. Gör det genom att följa stegen i den här guiden. Ett enda PersonGroup-objekt kan ha en ögonblicksbild tillämpad på det bara en gång.

## <a name="clean-up-resources"></a>Rensa resurser

När du har migrerat ansiktsdata tar du bort ögonblicksbildobjektet manuellt.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Nästa steg

Se sedan relevant API-referensdokumentation, utforska en exempelapp som använder snapshot-funktionen eller följa en instruktionsguide för att börja använda de andra API-åtgärder som nämns här:

- [Dokumentation för referens för ögonblicksbilder (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exempel på ansiktsögonblicksbild](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Lägg till ansikten](how-to-add-faces.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
