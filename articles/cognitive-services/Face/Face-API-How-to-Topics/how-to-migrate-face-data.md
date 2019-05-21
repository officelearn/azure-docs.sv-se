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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913801"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrera dina ansikts-data till en annan Ansikts-prenumeration

Den här guiden visar hur du flyttar ansikte data, till exempel ett sparade PersonGroup-objekt med ansikten, till en annan prenumeration för Azure Cognitive Services Ansikts-API. Om du vill flytta data, kan du använda funktionen ögonblicksbild. Det här sättet du undvika att behöva upprepade gånger skapa och träna ett PersonGroup eller FaceList objekt när du flyttar eller utöka din verksamhet. Till exempel kanske du skapat ett PersonGroup-objekt med hjälp av en kostnadsfri utvärderingsprenumeration och nu vill migrera den till en betald prenumeration. Eller du kan behöva synkronisera ansikte data över regioner för ett stort företag-åtgärden.

Det här samma migreringsstrategi gäller även för LargePersonGroup och LargeFaceList objekt. Om du inte är bekant med principerna i den här guiden finns i deras definitioner i den [står inför erkännande begrepp](../concepts/face-recognition.md) guide. Den här guiden använder klientbiblioteket för .NET för Ansikts-API med C#.

## <a name="prerequisites"></a>Nödvändiga komponenter

Behöver du följande objekt:

- Två Ansikts-API prenumerationsnycklar, en med befintliga data och en för att migrera till. Om du vill prenumerera på Ansikts-API-tjänst och få din nyckel, följer du anvisningarna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Ansikts-API prenumerations-ID strängen som motsvarar målprenumerationen. Du hittar det genom att välja **översikt** i Azure-portalen. 
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Den här guiden använder en enkel konsolapp för att köra datamigrering ansikte. En fullständig implementering finns i den [Ansikts-API ögonblicksbild exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) på GitHub.

1. Skapa ett nytt Console app .NET Framework-projekt i Visual Studio. Ge den namnet **FaceApiSnapshotSample**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **hantera NuGet-paket**. Välj den **Bläddra** och sedan **inkludera förhandsversion**. Hitta och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Skapa ansikts-klienter

I den **Main** -metod i *Program.cs*, skapar två [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) instanser för dina prenumerationer för källa och mål. Det här exemplet används en Ansikts-prenumeration i regionen östra Asien som källa och en prenumeration för USA, västra som mål. Det här exemplet visar hur du migrerar data från en Azure-region till en annan. Om dina prenumerationer finns i olika regioner, ändra den `Endpoint` strängar.

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

Fyll i prenumerationen nyckelvärden och slutpunkten URL: er för dina prenumerationer för källa och mål.


## <a name="prepare-a-persongroup-for-migration"></a>Förbereda en PersonGroup för migrering

Du behöver ID för PersonGroup i din käll-prenumeration för att migrera den till målprenumerationen. Använd den [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metod för att hämta en lista över PersonGroup-objekt. Hämta sedan den [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) egenskapen. Den här processen ser annorlunda ut baserat på vilka PersonGroup objekt du har. I den här guiden är källan PersonGroup ID lagras i `personGroupId`.

> [!NOTE]
> Den [exempelkoden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) skapar och träna en ny PersonGroup att migrera. I de flesta fall bör du redan har en PersonGroup att använda.

## <a name="take-a-snapshot-of-a-persongroup"></a>Ta en ögonblicksbild av en PersonGroup

En ögonblicksbild är tillfälliga Fjärrlagring för vissa Ansikts-datatyper. Den fungerar som en typ av Urklipp vid funktionen Kopiera data från en prenumeration till en annan. Först måste ta du en ögonblicksbild av data i käll-prenumeration. Du koppla den till ett nytt dataobjekt i målprenumerationen.

Använd den källprenumerationen FaceClient instans för att ta en ögonblicksbild av PersonGroup. Använd [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) med PersonGroup-ID och mål-prenumerations-ID. Om du har flera målprenumerationer kan du lägga till dem som matris poster i den tredje parametern.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Processen för att utföra och tillämpa ögonblicksbilder inte störa alla vanliga anrop till källan eller till PersonGroups eller FaceLists. Gör inte samtidiga anrop som ändra källobjektet, till exempel [FaceList hanteringsanrop](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) eller [PersonGroup träna](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) anropar, till exempel. Ögonblicksbildsåtgärden kan köras före eller efter dessa åtgärder eller fel uppstå.

## <a name="retrieve-the-snapshot-id"></a>Hämta ögonblicksbild-ID

Metoden som används för att ta ögonblicksbilder är asynkron, så du måste vänta tills den har slutförandet. Ögonblicksbild åtgärder kan inte annulleras. I den här koden i `WaitForOperation` metoden övervakar asynkront anrop. Den kontrollerar status för varje 100 ms. När åtgärden har slutförts, hämta en åtgärds-ID genom att parsa den `OperationLocation` fält. 

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

Efter Åtgärdsstatus visar `Succeeded`, hämta ögonblicksbild-ID genom att parsa den `ResourceLocation` i den returnerade OperationStatus-instansen.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

En typisk `resourceLocation` värde ser ut så här:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Tillämpa en ögonblicksbild till en målprenumeration

Därefter skapa nya PersonGroup i målprenumerationen med hjälp av ett slumpmässigt genererat-ID. Sedan använda den målprenumerationen FaceClient instans för att tillämpa ögonblicksbilden till den här PersonGroup. Skicka in ögonblicksbilden ID och den nya PersonGroup-ID.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Ett Snapshot-objekt är giltig i endast 48 timmar. Ta en ögonblicksbild endast om du tänker använda den för datamigrering strax efter.

En ögonblicksbild tillämpa begäran returnerar en annan åtgärd-ID. För att få detta ID kan parsa den `OperationLocation` i den returnerade applySnapshotResult-instansen. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Programprocessen ögonblicksbild är också asynkron, så återigen att använda `WaitForOperation` att vänta tills det slutförts.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testa migrering av data

När du har installerat ögonblicksbilden fylls nya PersonGroup i målprenumerationen med den ursprungliga ansikts-informationen. Som standard kopieras även utbildning resultat. Den nya PersonGroup är redo för face ID anrop utan att behöva träna.

Kör följande åtgärder för att testa migrering av data, och jämför resultatet de skrivs ut på konsolen:

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

Nu kan du använda den nya PersonGroup i målprenumerationen. 

Skapa en ny PersonGroup för att ta emot ögonblicksbilden för att uppdatera målet PersonGroup igen. Gör detta genom att följa stegen i den här guiden. Ett enda PersonGroup-objekt kan ha en ögonblicksbild tillämpas bara en gång.

## <a name="clean-up-resources"></a>Rensa resurser

När du har migrerat data för ansiktsigenkänning, manuellt ta bort snapshot-objekt.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Nästa steg

Därefter se relevanta API referensdokumentationen, utforska en exempelapp som använder funktionen ögonblicksbild eller följa en instruktionsguide för att börja använda andra åtgärder i API: et nämns här:

- [Referensdokumentation för ögonblicksbild (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Ansikts-API-exemplet för ögonblicksbild](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Lägg till ansikten](how-to-add-faces.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
