---
title: Sky reflektioner
description: Beskriver hur du ställer in miljökartor för himmelsreflektioner
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680614"
---
# <a name="sky-reflections"></a>Sky reflektioner

I Azure Remote Rendering används en himmelsstruktur för att tända objekt realistiskt. För augmented reality-applikationer bör denna textur likna din verkliga omgivning, för att göra objekt verkar övertygande. I den här artikeln beskrivs hur du ändrar himmelsstrukturen.

> [!NOTE]
> Himlen konsistens kallas också en *miljö karta*. Dessa termer används omväxlande.

## <a name="object-lighting"></a>Objektbelysning

Azure Remote Rendering använder *fysiskt baserad rendering* (PBR) för realistiska belysningsberäkningar. Även om du kan lägga till [ljuskällor](lights.md) till din scen, med hjälp av en bra himmel konsistens har störst inverkan.

Bilderna nedan visar resultaten av belysning olika ytor endast med en himmel konsistens:

| Strävhet  | 0                                        | 0.25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Icke-metall  | ![Dielektriska0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metall      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Mer information om ljusmodellen finns [materials](../../concepts/materials.md) i materialkapitlet.

> [!IMPORTANT]
> Azure Remote Rendering använder himmelsstrukturen endast för belysningsmodeller. Det gör inte himlen som en bakgrund, eftersom Augmented Reality applikationer redan har en ordentlig bakgrund - den verkliga världen.

## <a name="changing-the-sky-texture"></a>Ändra himmelsstrukturen

För att ändra miljökartan behöver du bara läsa in en `SkyReflectionSettings` [textur](../../concepts/textures.md) och ändra sessionens:

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Observera att `LoadTextureFromSASAsync` varianten används ovan eftersom en inbyggd textur läses in. Vid inläsning från [länkade blob-lagringar](../../how-tos/create-an-account.md#link-storage-accounts)använder du varianten. `LoadTextureAsync`

## <a name="sky-texture-types"></a>Textningstyper för sky

Du kan använda både *[kubmappar](https://en.wikipedia.org/wiki/Cube_mapping)* och *2D-texturer* som miljökartor.

Alla texturer måste vara i ett [texturformat som stöds](../../concepts/textures.md#supported-texture-formats). Du behöver inte ange mipmaps för himmelsstrukturer.

### <a name="cube-environment-maps"></a>Kubmiljökartor

Som referens, här är en oförpackad kubkarta:

![En oförpackad kubkarta](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Använd `LoadTextureFromSASAsync` `TextureType.CubeMap` med för att ladda kubmappstexturer.

### <a name="sphere-environment-maps"></a>Sfäriska miljökartor

När du använder en 2D-textur som en miljökarta måste bilden vara i [sfäriskt koordinatutrymme](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![En himmelsbild i sfäriska koordinater](media/spheremap-example.png)

Använd `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` med för att läsa in sfäriska miljökartor.

## <a name="built-in-environment-maps"></a>Inbyggda miljökartor

Azure Remote Rendering innehåller några inbyggda miljömappningar som alltid är tillgängliga. Alla inbyggda miljökartor är kubkartor.

|Identifierare                         | Beskrivning                                              | Illustration                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Olika randljus, ljus inomhus basbelysning    | ![Autoshop (bilhandel)](media/autoshop.png)
|builtin://BoilerRoom               | Ljus inomhusljusinställning, flera fönsterlampor      | ![BoilerRoom (Kanter)](media/boiler-room.png)
|builtin://ColorfulStudio           | Varierande färgade lampor i medelljus inomhusmiljö  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Måttligt ljust omgivande hallljus                     | ![SmallHangar (småhängar)](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Dim inomhusinställning med ljus-mörk kontrast              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Dagsljus i omgivande rum, ljust fönsterområde ljus     | ![Lebombo (på andra sätt)](media/lebombo.png)
|builtin://SataraNight              | Mörk natthimmel och mark med många omgivande ljus   | ![SataraNatt](media/satara-night.png)
|builtin://SunnyVondelpark          | Starkt solljus och skuggkontrast                      | ![SunnyVondelpark (500)](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Klart himmelsljus med måttlig markbelysning            | ![Syferfontein (syferfontein)](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Måttligt varierande sol och skugga                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Kvällssolnedgångsljus närmar sig skymning                    | ![VenedigSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Ljusa, frodiga-gröna och vita ljustoner, nedtonad mark | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dagtid med starkt omgivande markljus                 | ![Vinterströmmen](media/winter-river.png)
|builtin://DefaultSky               | Samma som TearsOfSteelBridge                               | ![StandardSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Nästa steg

* [Cykellyktor](../../overview/features/lights.md)
* [Material](../../concepts/materials.md)
* [Texturer](../../concepts/textures.md)
* [Kommandoradsverktyget TexConv](../../resources/tools/tex-conv.md)
