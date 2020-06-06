---
title: Konvertera till avbildnings katalog
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen konvertera till avbildnings katalog för att konvertera data uppsättningen till avbildnings katalog format.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450635"
---
# <a name="convert-to-image-directory"></a>Konvertera till avbildnings katalog

Den här artikeln beskriver hur du använder modulen konvertera till avbildnings katalog för att konvertera avbildnings data uppsättningen till data typen image Directory, som är ett standardiserat data format i bildrelaterade uppgifter som bild klassificering i Azure Machine Learning designer (för hands version).

## <a name="how-to-use-convert-to-image-directory"></a>Så här använder du konvertera till avbildnings katalog  

1.  Lägg till modulen **konvertera till avbildnings katalog** i experimentet. Du hittar den här modulen i kategorin "Visuellt innehåll/image data transformation" i listan modul. 

2.  Anslut en avbildnings data uppsättning som indata. Kontrol lera att det finns en bild i indata-datauppsättningen.
    Följande data uppsättnings format stöds:

    - Komprimerad fil i dessa tillägg: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Mapp som innehåller en komprimerad fil i över giltiga tillägg. 
    - Mapp som innehåller bilder.

    > [!NOTE]
    > Bild kategori kan registreras i modulens utdata om den här avbildnings data uppsättningen är ordnad i torchvision ImageFolder-format, se [torchvision data uppsättningar](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) för mer information. Annars sparas bara bilder.

3.  Skicka pipelinen.

## <a name="results"></a>Resultat

Utdata från **konvertering till avbildnings katalog** är i avbildnings katalog format och kan anslutas till andra bild relaterade moduler som Indataporten också är avbildnings katalog för.

## <a name="technical-notes"></a>Tekniska anteckningar 

###  <a name="expected-inputs"></a>Förväntade indata  

| Namn          | Typ                  | Beskrivning   |
| ------------- | --------------------- | ------------- |
| Indata-datauppsättning | AnyDirectory, ZipFile | Indata-datauppsättning |

###  <a name="output"></a>Resultat  

| Namn                   | Typ           | Beskrivning            |
| ---------------------- | -------------- | ---------------------- |
| Avbildnings katalog för utdata | ImageDirect | Avbildnings katalog för utdata |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
