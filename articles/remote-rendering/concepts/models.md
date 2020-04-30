---
title: Modeller
description: Beskriver vad en modell är i Azure Remote rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617945"
---
# <a name="models"></a>Modeller

En *modell* i Azure Remote rendering refererar till en fullständig objekt representation, som består av [entiteter](entities.md) och [komponenter](components.md). Modeller är det huvudsakliga sättet att hämta anpassade data till tjänsten Remote rendering.

## <a name="model-structure"></a>Modellstruktur

En modell har exakt en [entitet](entities.md) som dess rotnod. Nedan kan det finnas en godtycklig hierarki med underordnade entiteter. När du läser in en modell returneras en referens till den här rot enheten.

Varje entitet kan ha [komponenter](components.md) kopplade. I det vanligaste fallet har entiteterna *MeshComponents*, som refererar till [nät resurserna](meshes.md).

## <a name="creating-models"></a>Skapa modeller

Att skapa modeller för körning uppnås genom att [konvertera inmatade modeller](../how-tos/conversion/model-conversion.md) från fil format som FBX och GLTF. Konverterings processen extraherar alla resurser, till exempel texturer, material och maskor, och konverterar dem till optimerade körnings format. Den kommer också att extrahera struktur information och omvandla den till ARR: s entitet/komponent diagram struktur.

> [!IMPORTANT]
>
> [Modell konvertering](../how-tos/conversion/model-conversion.md) är det enda sättet att skapa [nät](meshes.md). Även om maskor kan delas mellan entiteter vid körning, finns det inte något annat sätt att få ett nät i körningen, förutom att läsa in en modell.

## <a name="loading-models"></a>Läser in modeller

När en modell har konverterats kan den läsas in från Azure Blob Storage till körnings miljön.

Det finns två distinkta inläsnings funktioner som skiljer sig åt av sättet till gången i Blob Storage:

* Modellen kan åtgärdas med hjälp av SAS-URI: n. Relevant inläsnings `LoadModelFromSASAsync` funktion är `LoadModelFromSASParams`med-parameter. Använd även den här varianten vid inläsning av [inbyggda modeller](../samples/sample-model.md).
* Modellen kan åtgärdas av Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det `LoadModelAsync` här fallet `LoadModelParams`är med-parameter.

Följande kodfragment visar hur du läser in modeller med någon av funktionerna. Om du vill läsa in en modell med SAS URI använder du kod som den nedan:

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

Om du vill läsa in en modell genom att direkt använda dess Blob Storage-parametrar använder du kod som liknar följande kodfragment:

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

Sedan kan du bläddra i organisationshierarkin och ändra entiteter och komponenter. Att läsa in samma modell flera gånger skapar flera instanser, var och en med en egen kopia av entiteten/komponent strukturen. Eftersom nät, material och texturer är [delade resurser](../concepts/lifetime.md)kommer deras data inte att läsas in igen, trots. Därför instansierar en modell mer än en gång, vilket innebär relativt lite minnes belastning.

> [!CAUTION]
> Alla *asynkrona* funktioner i arr returnerade asynkrona åtgärds objekt. Du måste lagra en referens till dessa objekt tills åtgärden har slutförts. Annars kan skräp insamlaren i C# ta bort åtgärden tidigt och det kan aldrig slutföras. I exempel koden över användningen av *väntar* garanterar att den lokala variabeln ' loadOp ' innehåller en referens tills modell inläsningen är färdig. Om du däremot skulle använda den *slutförda* händelsen i stället måste du lagra den asynkrona åtgärden i en medlems variabel.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Maskor](meshes.md)
