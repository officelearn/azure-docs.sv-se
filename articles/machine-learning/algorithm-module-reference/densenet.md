---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en bild klassificerings modell med densenet-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d64933f7b2c8ebc8597b93cbd16b34158f936f96
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450092"
---
# <a name="densenet"></a>DenseNet

Den här artikeln beskriver hur du använder modulen **DenseNet** i Azure Machine Learning designer för att skapa en bild klassificerings modell med DenseNet-algoritmen.  

Den här klassificerings algoritmen är en övervakad inlärnings metod och kräver en etikettad avbildnings katalog. 

> [!NOTE]
> Den här modulen stöder inte etikettad data uppsättning som genererats från *data etiketter* i Studio, men stöder bara den namngivna avbildnings katalogen som genererades från [Convert to image Directory](convert-to-image-directory.md) -modulen. 

Du kan träna modellen genom att tillhandahålla modellen och den märkta bild katalogen som indata för att [träna Pytorch-modellen](train-pytorch-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen med [Poäng avbildnings modell](score-image-model.md).

### <a name="more-about-densenet"></a>Mer om DenseNet

Mer information finns i [tätt anslutna (convolutional-nätverk](https://arxiv.org/abs/1608.06993) .

## <a name="how-to-configure-densenet"></a>Så här konfigurerar du DenseNet

1.  Lägg till **DenseNet** -modulen i din pipeline i designern.  

2.  För **modell namn**anger du namnet på en viss densenet-struktur och du kan välja från densenet som stöds: "densenet121", "densenet161", "densenet169", "densenet201".

3.  För **förtränat**anger du om du vill använda en förtränad modell på ImageNet. Om du väljer det här kan du finjustera modellen baserat på vald modell. om det är avmarkerat kan du träna från början.

4.  För **minnes effektiv**anger du om du vill använda kontroll punkter, vilket är mycket mer minnes effektivt men långsammare. Mer information finns i https://arxiv.org/pdf/1707.06990.pdf.

5.  Anslut utdata från modulen **DenseNet** , Training and validation image data uppsättning till [träna Pytorch-modellen](train-pytorch-model.md). 

6. Skicka pipelinen.


## <a name="results"></a>Resultat

När pipeline-körningen har slutförts ska du använda modellen för att skapa en modell för [Pytorch-modellen](train-pytorch-model.md) för att skapa en [bild modell](score-image-model.md)för att förutsäga värden för nya exempel på indata.

## <a name="technical-notes"></a>Tekniska anteckningar  

###  <a name="module-parameters"></a>Parametrar för modul  

| Name             | Intervall | Typ    | Standardvärde     | Beskrivning                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Modellnamn       | Alla   | Läge    | densenet201 | Namn på en viss densenet-struktur     |
| Tränats       | Alla   | Boolesk | Sant        | Om du vill använda en förtränad modell på ImageNet |
| Minnes effektiv | Alla   | Boolesk | Falskt       | Om du vill använda kontroll punkter, vilket är mycket mer minnes effektivt men långsammare |

###  <a name="output"></a>Utdata  

| Namn            | Typ                    | Beskrivning                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modell som inte är tränad | UntrainedModelDirectory | En densenet modell som kan anslutas till träna Pytorch-modellen. |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
