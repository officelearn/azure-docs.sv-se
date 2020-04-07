---
title: Texturer
description: Arbetsflöde för texturresurs
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681667"
---
# <a name="textures"></a>Texturer

Texturer är en oföränderlig [delad resurs](../concepts/lifetime.md). Texturer kan laddas från [blob lagring](../how-tos/conversion/blob-storage.md) och tillämpas på modeller direkt, vilket visas i [Handledning: Ändra miljö och material](../tutorials/unity/changing-environment-and-materials.md). Oftast kommer dock texturer vara en del av en [konverterad modell](../how-tos/conversion/model-conversion.md), där de refereras av dess [material](materials.md).

## <a name="texture-types"></a>Texturtyper

Olika texturtyper har olika användningsfall:

* **2D Texturer** används främst i [material](materials.md).
* **Kubkartor** kan användas för [himlen](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Texturformat som stöds

Alla texturer som ges till ARR måste vara i [DDS-format](https://en.wikipedia.org/wiki/DirectDraw_Surface). Gärna med mipmaps och texturkomprimering. Se [kommandoradsverktyget TexConv](../resources/tools/tex-conv.md) om du vill automatisera konverteringsprocessen.

## <a name="loading-textures"></a>Läsa in texturer

När du läser in en textur måste du ange den förväntade typen. Om typen stämmer överens misslyckas texturbelastningen.
Om du läser in en textur med samma URI två gånger returneras samma texturobjekt, eftersom det är en [delad resurs](../concepts/lifetime.md).

I likhet med lastningsmodeller finns det två varianter av att ta itu med en texturtillgång i källblobblagring:

* Texturtillgången kan åtgärdas av dess SAS URI. Relevant lastningsfunktion är `LoadTextureFromSASAsync` med parameter . `LoadTextureFromSASParams` Använd den här varianten även vid inläsning [av inbyggda texturer](../overview/features/sky.md#built-in-environment-maps).
* Texturen kan åtgärdas med bloblagringsparametrar direkt, om [blob-lagringen är länkad till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant lastfunktion i `LoadTextureAsync` det `LoadTextureParams`här fallet är med parametern .

Följande exempelkod visar hur du läser in en textur via dess SAS URI (eller inbyggd textur) - observera att endast inläsningsfunktionen/parametern skiljer sig åt för det andra fallet:

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

Beroende på vad texturen ska användas till kan det finnas begränsningar för texturtyp och innehåll. Till exempel måste ojämnhetskartan för ett [PBR-material](../overview/features/pbr-materials.md) vara gråskala.

> [!CAUTION]
> Alla *Async-funktioner* i ARR returnerar asynkrona åtgärdsobjekt. Du måste lagra en referens till dessa objekt tills åtgärden är klar. Annars kan C# skräpinsamlaren ta bort åtgärden tidigt och den kan aldrig avslutas. I exempelkoden ovan används medlemsvariabeln "_textureLoad" för att innehålla en referens tills händelsen *Slutförd* anländer.

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
* [Himlen](../overview/features/sky.md)
