---
title: Få information om en konverterad modell
description: Beskrivning av alla modellkonverteringsparametrar
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681524"
---
# <a name="get-information-about-a-converted-model"></a>Få information om en konverterad modell

Den arrAsset-fil som produceras av konverteringstjänsten är endast avsedd för konsumtion av renderingstjänsten. Det kan dock finnas tillfällen när du vill komma åt information om en modell utan att starta en återgivningssession. Därför placerar konverteringstjänsten en JSON-fil bredvid arrAsset-filen i utdatabehållaren. Om en fil `buggy.gltf` till exempel konverteras innehåller utdatabehållaren en fil som anropas `buggy.info.json` bredvid den konverterade tillgången `buggy.arrAsset`. Den innehåller information om källmodellen, den konverterade modellen och om själva konverteringen.

## <a name="example-info-file"></a>Exempel *på informationsfil*

Här är ett exempel *info* fil som produceras `buggy.gltf`genom att konvertera en fil som heter:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Information i informationsfilen

### <a name="the-files-section"></a>Avsnittet *filer*

Det här avsnittet innehåller de angivna filnamnen.

* `input`: Namnet på källfilen.
* `output`: Namnet på utdatafilen när användaren har angett ett icke-standardnamn.

### <a name="the-conversionsettings-section"></a>Avsnittet *conversionSettings*

Det här avsnittet innehåller en kopia av [de ConversionSettings](configure-model-conversion.md#settings-file) som angavs när modellen konverterades.

### <a name="the-inputinfo-section"></a>Avsnittet *inputInfo*

I det här avsnittet beskrivs information om källfilformatet.

* `sourceAssetExtension`: Källfilens filnamnstillägg.
* `sourceAssetFormat`: En beskrivning av källfilformatet.
* `sourceAssetFormatVersion`: Versionen av källfilformatet.
* `sourceAssetGenerator`: Namnet på verktyget som genererade källfilen, om tillgängligt.

### <a name="the-inputstatistics-section"></a>Avsnittet *inputStatistics*

Det här avsnittet innehåller information om källscenen. Det finns ofta avvikelser mellan värdena i det här avsnittet och motsvarande värden i verktyget som skapade källmodellen. Sådana skillnader förväntas, eftersom modellen ändras under export- och konverteringsstegen.

* `numMeshes`: Antalet maskdelar, där varje del kan referera till ett enda material.
* `numFaces`: Det totala antalet _trianglar_ i hela modellen. Observera att nätet trianguleras under konverteringen.
* `numVertices`: Det totala antalet hörn i hela modellen.
* `numMaterial`: Det totala antalet material i hela modellen.
* `numFacesSmallestMesh`: Antalet trianglar i modellens minsta nät.
* `numFacesBiggestMesh`: Antalet trianglar i modellens största nät.
* `numNodes`: Antalet noder i modellens scendiagram.
* `numMeshUsagesInScene`: Antalet gånger noder refererar till maskor. Mer än en nod kan referera till samma nät.
* `maxNodeDepth`: Det maximala djupet av noderna i scendiagrammet.

### <a name="the-outputinfo-section"></a>*Avsnittet outputInfo*

I det här avsnittet beskrivs allmän information om den genererade utdata.

* `conversionToolVersion`: Version av modellkonverteraren.
* `conversionHash`: En hash-av data i arrAsset som kan bidra till rendering. Kan användas för att förstå om konverteringstjänsten har gett ett annat resultat när den körs på samma fil igen.

### <a name="the-outputstatistics-section"></a>Avsnittet *outputStatistics*

I det här avsnittet visas information som beräknas från den konverterade tillgången.

* `numMeshPartsCreated`: Antalet maskor i arrAsset. Det kan `numMeshes` skilja `inputStatistics` sig från i avsnittet, eftersom instancing påverkas av konverteringsprocessen.
* `numMeshPartsInstanced`: Antalet maskor som återanvänds i arrAsset.
* `recenteringOffset`: När `recenterToOrigin` alternativet i [ConversionSettings](configure-model-conversion.md) är aktiverat är det här värdet den översättning som skulle flytta den konverterade modellen tillbaka till sin ursprungliga position.
* `boundingBox`: Modellens gränser.

## <a name="next-steps"></a>Nästa steg

* [Modellkonvertering](model-conversion.md)
* [Konfigurera modellkonverteringen](configure-model-conversion.md)
