---
title: Konvertera till bildkatalog
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen konvertera till avbildnings katalog för att konvertera data uppsättningen till avbildnings katalog format.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 677cf60ff3e614fd1486445786154fbf026b7cd9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898683"
---
# <a name="convert-to-image-directory"></a>Konvertera till bildkatalog

Den här artikeln beskriver hur du använder modulen konvertera till avbildnings katalog för att konvertera avbildnings data uppsättningen till data typen image Directory, som är ett standardiserat data format i bildrelaterade uppgifter som bild klassificering i Azure Machine Learning designer.

## <a name="how-to-use-convert-to-image-directory"></a>Så här använder du konvertera till avbildnings katalog  

1.  Lägg till modulen **konvertera till avbildnings katalog** i experimentet. Du hittar den här modulen i kategorin "Visuellt innehåll/image data transformation" i listan modul. 

2.  [Registrera en avbildnings data uppsättning](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) och Anslut den till porten för modulens indataport. Kontrol lera att det finns en bild i indata-datauppsättningen. 
    Följande data uppsättnings format stöds:

    - Komprimerad fil i dessa tillägg: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Mapp som innehåller bilder. Vi **rekommenderar att du först komprimerar mappen och använder sedan den komprimerade filen som data uppsättning**.

    > [!WARNING]
    > Du **kan inte** använda modulen **Importera data** för att importera avbildnings data uppsättning eftersom utdatatypen för modulen **Importera data** är DataFrame Directory, som endast innehåller en sträng med fil Sök vägar.
    

    > [!NOTE]
    > Om Använd bild data uppsättning i övervakad inlärning krävs etikett.
    > För bild klassificerings aktivitet kan etiketter skapas som bild "kategori" i modulens utdata om den här avbildnings data uppsättningen är ordnad i torchvision ImageFolder-format. Annars sparas bara bilder utan etikett. Här är ett exempel på hur du kan organisera bild data uppsättningen för att få etikett, använda bild kategori som undermapps namn. Mer information finns i [torchvision-datauppsättningar](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  Skicka pipelinen.

## <a name="results"></a>Resultat

Utdata från **konvertering till avbildnings katalog** är i avbildnings katalog format och kan anslutas till andra bild relaterade moduler som Indataporten också är avbildnings katalog för.

## <a name="technical-notes"></a>Tekniska anteckningar 

###  <a name="expected-inputs"></a>Förväntade indata  

| Namn          | Typ                  | Beskrivning   |
| ------------- | --------------------- | ------------- |
| Indata-datauppsättning | AnyDirectory, ZipFile | Indata-datauppsättning |

###  <a name="output"></a>Utdata  

| Namn                   | Typ           | Beskrivning            |
| ---------------------- | -------------- | ---------------------- |
| Avbildnings katalog för utdata | ImageDirect | Avbildnings katalog för utdata |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
