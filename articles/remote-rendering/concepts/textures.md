---
title: Bakgrunder
description: Arbets flöde för textur resurs
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: b951dab1ad01187c7612fad047bc52eb6aa9700e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701882"
---
# <a name="textures"></a>Bakgrunder

Texturer är en oåterkallelig [delad resurs](../concepts/lifetime.md). Texturer kan läsas in från [Blob Storage](../how-tos/conversion/blob-storage.md) och tillämpas på modeller direkt, vilket visas i [Självstudier: ändra miljö och material](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Oftast kommer texturer att ingå i en [konverterad modell](../how-tos/conversion/model-conversion.md)där de refereras till av sitt [material](materials.md).

## <a name="texture-types"></a>Textur typer

Olika struktur typer har olika användnings fall:

* **2D-texturer** används huvudsakligen i [material](materials.md).
* **Cubemaps** kan användas för [luft rummet](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Struktur format som stöds

Alla texturer som har angetts för ARR måste vara i [DDS-format](https://en.wikipedia.org/wiki/DirectDraw_Surface). Helst med mipmaps och textur komprimering.

## <a name="loading-textures"></a>Läser in texturer

När du läser in en textur måste du ange dess förväntade typ. Om typen stämmer, Miss lyckas textur inläsningen.
Att läsa in en struktur med samma URI två gånger returnerar samma textur objekt, eftersom det är en [delad resurs](../concepts/lifetime.md).

Precis som vid inläsning av modeller finns det två varianter av att adressera en struktur till gång i Blob Storage för källa:

* Textur till gången kan åtgärdas med hjälp av SAS-URI: n. Relevant inläsnings funktion är `LoadTextureFromSASAsync` med-parameter `LoadTextureFromSASParams` . Använd även den här varianten vid inläsning av [inbyggda texturer](../overview/features/sky.md#built-in-environment-maps).
* Strukturen kan åtgärdas av Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det här fallet är `LoadTextureAsync` med-parameter `LoadTextureParams` .

Följande exempel kod visar hur du läser in en struktur via dess SAS-URI (eller inbyggd struktur) – Observera att endast inläsnings funktionen/-parametern skiljer sig för det andra fallet:

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

Beroende på vad texturen ska användas för kan det finnas begränsningar för textur typ och innehåll. Till exempel måste en ojämnhets karta för ett [PBR-material](../overview/features/pbr-materials.md) vara gråskala.

> [!CAUTION]
> Alla *asynkrona* funktioner i arr returnerade asynkrona åtgärds objekt. Du måste lagra en referens till dessa objekt tills åtgärden har slutförts. Annars kan skräp insamlaren i C# ta bort åtgärden tidigt och det kan aldrig slutföras. I exempel koden ovanför medlems variabeln _textureLoad används för att lagra en referens tills den *slutförda* händelsen anländer.

## <a name="api-documentation"></a>API-dokumentation

* [C# textur klass](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RemoteManager. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C# RemoteManager. LoadTextureFromSASAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [C++ textur klass](/cpp/api/remote-rendering/texture)
* [C++ RemoteManager:: LoadTextureAsync ()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C++ RemoteManager:: LoadTextureFromSASAsync ()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
* [Himmel](../overview/features/sky.md)