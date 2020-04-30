---
title: Åsidosätta material under modellkonverteringen
description: Förklarar det material som åsidosätter arbets flödet vid konverterings tillfället
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681485"
---
# <a name="override-materials-during-model-conversion"></a>Åsidosätta material under modellkonverteringen

Under konverteringen används material inställningarna i käll modellen för att definiera det PBR- [material](../../overview/features/pbr-materials.md) som används av åter givningen.
Ibland ger [standard konverteringen](../../reference/material-mapping.md) inga önskade resultat och du måste göra ändringar.
När en modell konverteras för användning i Azure-fjärrrendering kan du ange en material-åsidosättande fil för att anpassa hur material konvertering sker per material.
Avsnittet om hur du [konfigurerar modell konvertering](configure-model-conversion.md) har instruktioner för att deklarera fil namnet för åsidosättning av material.

## <a name="the-override-file-used-during-conversion"></a>Den åsidosättande fil som används under konverteringen

Ett enkelt exempel är att anta att en box-modell har ett enda material, som kallas "default". Albedo-färgen måste justeras för användning i ARR.
I det här fallet kan `box_materials_override.json` en fil skapas på följande sätt:

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

`box_materials_override.json` Filen placeras i behållaren för indata och en `ConversionSettings.json` läggs till bredvid `box.fbx`, vilket anger att konverteringen ska hitta åsidosättning-filen (se [Konfigurera modell konverteringen](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

När modellen konverteras används de nya inställningarna.

### <a name="color-materials"></a>Färgmaterial

[Färg Materials](../../overview/features/color-materials.md) modellen beskriver en konstant skuggad yta som är oberoende av belysningen.
Detta är användbart för till gångar som görs av Photogrammetry-algoritmer, till exempel.
Vid åsidosättning av materialiserade filer kan ett material deklareras som ett färg material genom att `unlit` ställa `true`in på.

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

### <a name="ignore-specific-texture-maps"></a>Ignorera vissa textur kartor

Ibland kanske du vill att konverterings processen ska ignorera vissa textur kartor. Detta kan vara fallet när modellen genererades av ett verktyg som genererar särskilda Maps som inte tolkas korrekt av åter givningen. Till exempel en "OpacityMap" som används för att definiera något annat än ogenomskinlighet, eller en modell där "NormalMap" lagras som "BumpMap". (I det senare fallet vill du ignorera "NormalMap", vilket innebär att konverteraren använder "BumpMap" som "NormalMap".)

Principen är enkel. Lägg bara till en egenskap `ignoreTextureMaps` som heter och Lägg till eventuella textur scheman som du vill ignorera:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

En fullständig lista över textur mappningar som du kan ignorera finns i JSON-schemat nedan.

## <a name="json-schema"></a>JSON-schema

Det fullständiga JSON-schemat för material-filer anges här. Med undantag `unlit` för `ignoreTextureMaps`och, är tillgängliga egenskaper en delmängd av egenskaperna som beskrivs i avsnitten om [färg material](../../overview/features/color-materials.md) -och PBR- [materialets](../../overview/features/pbr-materials.md) modeller.

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