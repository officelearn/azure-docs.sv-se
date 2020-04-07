---
title: Materialmappning för modellformat
description: Beskriver standardkonverteringen från modellkällaformat till PBR-material
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680393"
---
# <a name="material-mapping-for-model-formats"></a>Materialmappning för modellformat

När en källtillgång [konverteras som en modell](../how-tos/conversion/model-conversion.md)skapar konverteraren [material](../concepts/materials.md) för varje [nät](../concepts/meshes.md). Hur material skapas kan [åsidosättas](../how-tos/conversion/override-materials.md). Men som standard skapar konverteringen [PBR-material](../overview/features/pbr-materials.md). Eftersom varje källfilformat, som FBX, använder sina egna konventioner för att definiera material, måste dessa konventioner mappas till PBR-materialparametrarna för Azure Remote Rendering. 

I den här artikeln visas de exakta mappningar som används för att konvertera material från källtillgångar till körningsmaterial.

## <a name="gltf"></a>glTF (på andra)

Nästan allt från glTF 2.0-specifikationen stöds i Azure Remote Rendering, förutom *EmissiveFactor* och *EmissiveTexture*.

I följande tabell visas mappningen:

| glTF (på andra) | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   basColorFactor   |   albedoFärg              |
|   basFärgTextur  |   albedoKarta                |
|   metalliskaFactor    |   metalness (metall)                |
|   metallicTexture (metallicTexture)   |   metallkarta             |
|   ojämnhetFaktor   |   Strävhet                |
|   ojämnhetTextur  |   roughnessMap             |
|   ocklusionFaktor   |   Ocklusion                |
|   ocklusionTextur  |   ocklusionKarta             |
|   normalTextur     |   Normalmap                |
|   alfaKlipp       |   alfaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = falskt, ärTransparent = falskt |
|   alphaMode.MASK    |   alphaClipEnabled = sant, ärTransparent = falskt  |
|   alphaMode.BLEND (alfaMode.BLEND)   |   ärTransparent = sant     |
|   doubleSided       |   ärDoubleSided            |
|   emissiveFactor (2019)    |   -                        |
|   emissiveTexture (2019)   |   -                        |

Varje textur i glTF kan ha ett `texCoord` värde som också stöds i Azure Remote Rendering material.

### <a name="embedded-textures"></a>Inbäddade texturer

Texturer som är inbäddade i * \*.bin-* eller * \*.glb-filer* stöds.

### <a name="supported-gltf-extension"></a>GlTF-tillägg som stöds

Dessutom stöder Azure Remote Rendering följande glTF-tillägg till basfunktionsuppsättningen:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Motsvarar [färgmaterial.](../overview/features/color-materials.md) För *emissiv material,* Det rekommenderas att använda denna förlängning.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Istället för metalliska ojämnheter texturer, kan du ge diffusa-speglande-glossiness texturer. Azure Remote Rendering-implementeringen följer direkt konverteringsformlerna från tillägget.

## <a name="fbx"></a>Fbx

FBX-formatet är sluten källkod och FBX-material är inte kompatibla med PBR-material i allmänhet. FBX använder en komplex beskrivning av ytor med många unika parametrar och egenskaper och **inte alla av dem används av Azure Remote Rendering pipeline**.

> [!IMPORTANT]
> Azure Remote Rendering-modellkonverteringspipelinen stöder endast **FBX 2011 och senare**.

FBX-formatet definierar en konservativ metod för material, det finns bara två typer i den officiella FBX-specifikationen:

* *Lambert* - Används inte ofta under ganska lång tid redan, men det stöds fortfarande genom att konvertera till Phong vid konverteringstid.
* *Phong* - Nästan alla material och de flesta verktyg innehåll använder denna typ.

Phong-modellen är mer exakt och används som den *enda* modellen för FBX-material. Nedan kommer det att kallas *FBX Material*.

> Maya använder två anpassade tillägg för FBX genom att definiera anpassade egenskaper för PBR- och Stingray-typer av ett material. Dessa uppgifter ingår inte i FBX-specifikationen, så den stöds inte av Azure Remote Rendering för närvarande.

FBX Material använder diffus-speglande-speglandeLevel koncept, så att konvertera från en diffus konsistens till en albedo karta måste vi beräkna de andra parametrarna för att subtrahera dem från diffusa.

> Alla färger och texturer i FBX är i sRGB-utrymme (kallas även Gamma-utrymme) men Azure Remote Rendering fungerar med linjärt utrymme under visualisering och i slutet av ramen konverterar allt tillbaka till sRGB utrymme. Azure Remote Rendering tillgång pipeline konverterar allt till linjärt utrymme för att skicka den som förberedda data till renderaren.

Den här tabellen visar hur texturer mappas från FBX-material till Azure Remote Rendering material. Några av dem används inte direkt utan i kombination med andra texturer som deltar i formlerna (till exempel den diffusa texturen):

| Fbx | Azure Remote Rendering |
|:-----|:----|
| AmbientColor (ambientcolor) | Ocklusion Karta   |
| Diffusfärg | *används för Albedo, Metalness* |
| TransparentFärg | *används för alfakanal av Albedo* |
| TransparencyFactor | *används för alfakanal av Albedo* |
| Opacitet | *används för alfakanal av Albedo* |
| Speglandefärg | *används för Albedo, Metalness, Roughness* |
| SpecularFactor| *används för Albedo, Metalness, Roughness* |
| Glansutnyttpande | *används för Albedo, Metalness, Roughness* |
| Normalmap | Normalmap |
| Stöta | *konverteras till NormalMap* |
| EmissiveColor (av fel" | - |
| EmissiveFactor (2019) | - |
| ReflektionFärg | - |
| FörskjutningColor | - |

Kartläggningen ovan är den mest komplexa delen av materialkonverteringen, på grund av många antaganden som måste göras. Vi diskuterar dessa antaganden nedan.

Några definitioner som används nedan:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Röd 0,2125 + `Specular`. Grön 0,7154 + `Specular`. Blå 0,0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Röd<sup>2</sup> + 0,587 * `Diffuse`. Grön<sup>2</sup> + 0,114 * `Diffuse`. Blå<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Röd<sup>2</sup> + 0,587 * `Specular`. Grön<sup>2</sup> + 0,114 * `Specular`. Blå<sup>2</sup>
* `SpecularStrength`= max(`Specular`. Röd, `Specular`. Grön, `Specular`. Blå)

Den SpecularIntensity formel erhålls [härifrån](https://en.wikipedia.org/wiki/Luma_(video)).
Formeln för ljusstyrka beskrivs i den här [specifikationen](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Strävhet

`Roughness`beräknas utifrån `Specular` `ShininessExponent` och med den [här formeln](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). Formeln är en approximation av ojämnhet från Phong speglande exponent:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness (metall)

`Metalness`beräknas från `Diffuse` `Specular` och med den här [formeln från glTF-specifikationen](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Tanken här är att vi löser ekvationen: Ax<sup>2</sup> + Bx + C = 0.
I grund och botten återspeglar dielektriska ytor runt 4% av ljuset på ett speglande sätt, och resten är diffust. Metalliska ytor reflekterar inget ljus på ett diffust sätt, men allt på ett speglande sätt.
Denna formel har några nackdelar, eftersom det inte finns något sätt att skilja mellan blank plast och blanka metalliska ytor. Vi antar att det mesta ytan har metalliska egenskaper, och därmed glänsande plast / gummi ytor kanske inte ser ut som förväntat.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`beräknas från `Diffuse`, `Specular`och `Metalness`.

Som beskrivs i metalness avsnittet, dielektriska ytor reflekterar cirka 4% av ljuset.  
Tanken här är att linjärt `Dielectric` interpolera mellan och `Metal` färger med värde `Metalness` som en faktor. Om metalness `0.0`är , då beroende på speglande det kommer att vara antingen en mörk färg (om speglande är hög) eller diffusa kommer inte att förändras (om ingen speglande finns). Om metalness är ett stort värde, då den diffusa färgen kommer att försvinna till förmån för speglande färg.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`har beräknats av formeln ovan, men alfakanalen kräver ytterligare beräkningar. FBX-formatet är vagt om öppenhet och har många sätt att definiera det. Olika innehållsverktyg använder olika metoder. Tanken här är att förena dem i en formel. Det gör vissa tillgångar felaktigt visas som transparent, men om de inte skapas på ett vanligt sätt.

Detta beräknas från `TransparentColor` `TransparencyFactor`, `Opacity`, :

om `Opacity` definieras, använd den `AlbedoAlpha`  =  `Opacity` direkt: annars  
om `TransparencyColor` definieras, `AlbedoAlpha` sedan = 1,0`TransparentColor`- (( . Röd `TransparentColor`+ . Grön `TransparentColor`+ . Blå) / 3,0) annars  
om `TransparencyFactor`, `AlbedoAlpha` då = 1,0 -`TransparencyFactor`

Den `Albedo` slutliga färgen har fyra `AlbedoRGB` kanaler, `AlbedoAlpha`kombinera med .

### <a name="summary"></a>Sammanfattning

Sammanfattningsvis `Albedo` kommer du att vara `Diffuse`mycket `Specular` nära originalet , om den är nära noll. Annars ytan kommer att se ut som en metallisk yta och förlorar den diffusa färgen. Ytan kommer att se mer polerad och reflekterande om `ShininessExponent` den är tillräckligt stor och `Specular` är ljus. Annars ytan kommer att se grov och knappt reflektera miljön.

### <a name="known-issues"></a>Kända problem

* Den nuvarande formeln fungerar inte bra för enkel färgad geometri. Om `Specular` är tillräckligt ljus, då alla geometrier blir reflekterande metalliska ytor utan någon färg. Lösningen här är att `Specular` sänka till 30% från originalet eller att använda konverteringsinställningen [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* PBR-material har `Maya` nyligen `3DS Max` lagts till och verktyg för att skapa innehåll. De använder anpassade användardefinierade svartlådeegenskaper för att skicka den till FBX. Azure Remote Rendering läser inte dessa ytterligare egenskaper eftersom de inte är dokumenterade och formatet är stängd källa.

## <a name="next-steps"></a>Nästa steg

* [Modellkonvertering](../how-tos/conversion/model-conversion.md)
* [Övergripande material under modellkonvertering](../how-tos/conversion/override-materials.md)
