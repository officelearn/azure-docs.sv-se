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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450108"
---
# <a name="convert-to-image-directory"></a>Konvertera till bildkatalog

Den här artikeln beskriver hur du använder modulen konvertera till avbildnings katalog för att konvertera avbildnings data uppsättningen till data typen image Directory, som är ett standardiserat data format i bildrelaterade uppgifter som bild klassificering i Azure Machine Learning designer.

## <a name="how-to-use-convert-to-image-directory"></a>Så här använder du konvertera till avbildnings katalog  

1.  Lägg till modulen **konvertera till avbildnings katalog** på arbets ytan. Du hittar den här modulen i kategorin "Visuellt innehåll/image data transformation" i listan modul. 

2.  Indata från **Convert to image Directory** -modulen måste vara en fil data uppsättning. [Registrera en avbildnings data uppsättning](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) och Anslut den till porten för modulens indataport. Kontrol lera att det finns en bild i indata-datauppsättningen. För närvarande har designern inte stöd för visualisering av bild data uppsättning.
 
    Följande data uppsättnings format stöds:

    - Komprimerad fil i dessa tillägg: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Mapp som innehåller bilder. Vi **rekommenderar att du först komprimerar mappen och använder sedan den komprimerade filen som data uppsättning**.

    > [!WARNING]
    > Du **kan inte** använda modulen **Importera data** för att importera avbildnings data uppsättning eftersom utdatatypen för modulen **Importera data** är DataFrame Directory, som endast innehåller en sträng med fil Sök vägar.
    

    > [!NOTE]
    > - Om Använd bild data uppsättning i övervakad inlärning måste du ange etiketten för träning-datauppsättningen.
    > - För bild klassificerings aktivitet kan etiketter skapas som bild "kategori" i modulens utdata om den här avbildnings data uppsättningen är ordnad i torchvision ImageFolder-format. Annars sparas bara bilder utan etikett. Följande är ett exempel på hur du kan organisera bild data uppsättningen för att få etikett, använda bild kategori som undermapps namn. 
    > - Du behöver inte ladda upp samma antal avbildningar i varje Category-mapp.
    > - Bilder med dessa fil namns tillägg (i gemener) stöds:. jpg,. jpeg,. png,. ppm,. bmp,. PGM,. tif,. TIFF,. webp. Du kan också ha flera olika typer av avbildningar i en mapp.    
    > - Mer information finns i [torchvision-datauppsättningar](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Om Använd bild data uppsättning för poängsättning ska indatafilens data uppsättning innehålla icke-klassificerade avbildningar.
    
3.  Skicka pipelinen. Den här modulen kan köras på antingen GPU eller CPU.

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
