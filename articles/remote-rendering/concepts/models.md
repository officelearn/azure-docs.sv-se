---
title: Modeller
description: Beskriver vad en modell är i Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617945"
---
# <a name="models"></a>Modeller

En *modell* i Azure Remote Rendering refererar till en fullständig objektrepresentation, som består av [entiteter](entities.md) och [komponenter](components.md). Modeller är det viktigaste sättet att få anpassade data till fjärrrenderingstjänsten.

## <a name="model-structure"></a>Modellstruktur

En modell har exakt en [entitet](entities.md) som rotnod. Under att det kan ha en godtycklig hierarki av underordnade entiteter. När du läser in en modell returneras en referens till den här rotentiteten.

Varje entitet kan ha [komponenter](components.md) anslutna. I det vanligaste fallet har *entiteterna MeshComponents*, som refererar till [nätresurser](meshes.md).

## <a name="creating-models"></a>Skapa modeller

Skapa modeller för körning uppnås genom [att konvertera indatamodeller](../how-tos/conversion/model-conversion.md) från filformat som FBX och GLTF. Konverteringsprocessen extraherar alla resurser, till exempel texturer, material och nät, och konverterar dem till optimerade körningsformat. Den kommer också att extrahera den strukturella informationen och omvandla den till ARR:s enhets-/komponentgrafstruktur.

> [!IMPORTANT]
>
> [Modellkonvertering](../how-tos/conversion/model-conversion.md) är det enda sättet att skapa [maskor](meshes.md). Även om maskor kan delas mellan entiteter vid körning, finns det inget annat sätt att få ett nät i körningen, annat än att läsa in en modell.

## <a name="loading-models"></a>Laddar modeller

När en modell har konverterats kan den läsas in från Azure blob storage till körningen.

Det finns två olika lastningsfunktioner som skiljer sig åt med det sätt på vilket tillgången hanteras i blob storage:

* Modellen kan åtgärdas av dess SAS URI. Relevant lastningsfunktion är `LoadModelFromSASAsync` med parameter . `LoadModelFromSASParams` Använd den här varianten även vid inläsning av [inbyggda modeller](../samples/sample-model.md).
* Modellen kan åtgärdas med bloblagringsparametrar direkt, om [blob-lagringen är länkad till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant lastfunktion i `LoadModelAsync` det `LoadModelParams`här fallet är med parametern .

Följande kodavsnitt visar hur du läser in modeller med någon av funktionerna. Om du vill läsa in en modell med SAS URI använder du koden som den nedan:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

Om du vill läsa in en modell direkt med hjälp av dess parametrar för bloblagring använder du kod som liknar följande kodavsnitt:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

Därefter kan du gå igenom entitetshierarkin och ändra entiteter och komponenter. Om du läser in samma modell flera gånger skapas flera instanser, var och en med sin egen kopia av entiteten/komponentstrukturen. Eftersom nät, material och texturer är [delade resurser](../concepts/lifetime.md)läses dock inte deras data in igen. Därför instansierar en modell mer än en gång medför relativt lite minne overhead.

> [!CAUTION]
> Alla *Async-funktioner* i ARR returnerar asynkrona åtgärdsobjekt. Du måste lagra en referens till dessa objekt tills åtgärden är klar. Annars kan C# skräpinsamlaren ta bort åtgärden tidigt och den kan aldrig avslutas. I exempelkoden ovan garanterar användningen av *inväntning* att den lokala variabeln "loadOp" har en referens tills modellinläsningen är klar. Om du skulle använda händelsen *Slutförd* i stället måste du dock lagra den asynkrona åtgärden i en medlemsvariabel.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Maskor](meshes.md)
