---
title: Bakgrunder
description: Arbets flöde för textur resurs
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681667"
---
# <a name="textures"></a>Bakgrunder

Texturer är en oåterkallelig [delad resurs](../concepts/lifetime.md). Texturer kan läsas in från [Blob Storage](../how-tos/conversion/blob-storage.md) och tillämpas på modeller direkt, vilket visas i [Självstudier: ändra miljö och material](../tutorials/unity/changing-environment-and-materials.md). Oftast kommer texturer att ingå i en [konverterad modell](../how-tos/conversion/model-conversion.md)där de refereras till av sitt [material](materials.md).

## <a name="texture-types"></a>Textur typer

Olika struktur typer har olika användnings fall:

* **2D-texturer** används huvudsakligen i [material](materials.md).
* **Cubemaps** kan användas för [luft rummet](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Struktur format som stöds

Alla texturer som har angetts för ARR måste vara i [DDS-format](https://en.wikipedia.org/wiki/DirectDraw_Surface). Helst med mipmaps och textur komprimering. Se [kommando rads verktyget TexConv](../resources/tools/tex-conv.md) om du vill automatisera konverterings processen.

## <a name="loading-textures"></a>Läser in texturer

När du läser in en textur måste du ange dess förväntade typ. Om typen stämmer, Miss lyckas textur inläsningen.
Att läsa in en struktur med samma URI två gånger returnerar samma textur objekt, eftersom det är en [delad resurs](../concepts/lifetime.md).

Precis som vid inläsning av modeller finns det två varianter av att adressera en struktur till gång i Blob Storage för källa:

* Textur till gången kan åtgärdas med hjälp av SAS-URI: n. Relevant inläsnings `LoadTextureFromSASAsync` funktion är `LoadTextureFromSASParams`med-parameter. Använd även den här varianten vid inläsning av [inbyggda texturer](../overview/features/sky.md#built-in-environment-maps).
* Strukturen kan åtgärdas av Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det `LoadTextureAsync` här fallet `LoadTextureParams`är med-parameter.

Följande exempel kod visar hur du läser in en struktur via dess SAS-URI (eller inbyggd struktur) – Observera att endast inläsnings funktionen/-parametern skiljer sig för det andra fallet:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
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

Beroende på vad texturen ska användas för kan det finnas begränsningar för textur typ och innehåll. Till exempel måste en ojämnhets karta för ett [PBR-material](../overview/features/pbr-materials.md) vara gråskala.

> [!CAUTION]
> Alla *asynkrona* funktioner i arr returnerade asynkrona åtgärds objekt. Du måste lagra en referens till dessa objekt tills åtgärden har slutförts. Annars kan skräp insamlaren i C# ta bort åtgärden tidigt och det kan aldrig slutföras. I exempel koden ovanför medlems variabeln _textureLoad används för att lagra en referens tills den *slutförda* händelsen anländer.

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
* [Himmel](../overview/features/sky.md)
