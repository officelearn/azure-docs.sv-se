---
title: Åsidosätt material under modellkonvertering
description: Förklarar det material som åsidosätter arbetsflödet vid konvertering
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681485"
---
# <a name="override-materials-during-model-conversion"></a>Åsidosätt material under modellkonvertering

Under konverteringen används materialinställningarna i källmodellen för att definiera de [PBR-material](../../overview/features/pbr-materials.md) som används av renderaren.
Ibland ger [standardkonverteringen](../../reference/material-mapping.md) inte önskat resultat och du måste göra ändringar.
När en modell konverteras för användning i Azure Remote Rendering kan du tillhandahålla en material-åsidosättningsfil för att anpassa hur materialkonvertering görs per material.
Avsnittet om [konvertering av modeller](configure-model-conversion.md) innehåller instruktioner för hur du deklarerar filnamnet för material åsidosättning.

## <a name="the-override-file-used-during-conversion"></a>Åsidosättningsfilen som används under konverteringen

Som ett enkelt exempel, låt oss säga att en låda modell har ett enda material, som kallas "Standard". Albedofärgen måste justeras för användning i ARR.
I det här `box_materials_override.json` fallet kan en fil skapas på följande sätt:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Filen `box_materials_override.json` placeras i indatabehållaren `ConversionSettings.json` och en `box.fbx`läggs till bredvid , som talar om för konverteringen var åsidosättningsfilen ska hittas (se [Konfigurera modellkonverteringen):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

När modellen konverteras gäller de nya inställningarna.

### <a name="color-materials"></a>Färgmaterial

[Färgmaterialmodellen](../../overview/features/color-materials.md) beskriver en ständigt skuggad yta som är oberoende av belysning.
Detta är användbart för tillgångar som gjorts av Photogrammetry-algoritmer, till exempel.
I material åsidosättningsfiler kan ett material deklareras `unlit` `true`som ett färgmaterial genom att ställa in på .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorera specifika texturkartor

Ibland kanske du vill att konverteringsprocessen ska ignorera specifika texturmappningar. Detta kan vara fallet när din modell genererades av ett verktyg som genererar speciella kartor som inte förstås korrekt av renderaren. Till exempel en "OpacitetMap" som används för att definiera något annat än opacitet, eller en modell där "NormalMap" lagras som "BumpMap". (I det senare fallet vill du ignorera "NormalMap", vilket gör att konverteraren att använda "BumpMap" som "NormalMap".)

Principen är enkel. Lägg bara till `ignoreTextureMaps` en egenskap som heter och lägga till någon textur karta du vill ignorera:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Den fullständiga listan över texturkartor som du kan ignorera finns i JSON-schemat nedan.

## <a name="json-schema"></a>JSON-schema

Det fullständiga JSON-schemat för materialfiler ges här. Med undantag `unlit` för `ignoreTextureMaps`och är de tillgängliga egenskaperna en delmängd av de egenskaper som beskrivs i avsnitten på [färgmaterial-](../../overview/features/color-materials.md) och [PBR-materialmodellerna.](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Nästa steg

* [Färgmaterial](../../overview/features/color-materials.md)
* [PBR-material](../../overview/features/pbr-materials.md)