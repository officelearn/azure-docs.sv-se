---
title: ResNet
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en bild klassificerings modell med ResNet-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450733"
---
# <a name="resnet"></a>ResNet

Den här artikeln beskriver hur du använder **ResNet** -modulen i Azure Machine Learning designer (för hands version) för att skapa en bild klassificerings modell med ResNet-algoritmen.  

Den här klassificerings algoritmen är en övervakad inlärnings metod och kräver en etikettad data uppsättning. Mer information om hur du hämtar en mappad avbildnings katalog hittar du i avsnittet [konvertera till avbildnings katalog](convert-to-image-directory.md) . Du kan träna modellen genom att tillhandahålla en modell och en etikettad bild katalog som indata för att [träna Pytorch-modellen](train-pytorch-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen med [Poäng avbildnings modell](score-image-model.md).

### <a name="more-about-resnet"></a>Mer om ResNet

Mer information om ResNet finns i [det här dokumentet](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) .

## <a name="how-to-configure-resnet"></a>Så här konfigurerar du ResNet

1.  Lägg till **ResNet** -modulen i din pipeline i designern.  

2.  För **modell namn**anger du namnet på en viss ResNet-struktur och du kan välja från ResNet: "resnet18", "resnet34", "resnet50", "resnet101", "resnet152", "resnet152", "resnext50 \_ 32x4d", "resnext101 \_ 32x8d", "wide_resnet50 \_ 2", "wide_resnet101 \_ 2".

3.  För **förtränat**anger du om du vill använda en förtränad modell på ImageNet. Om du väljer det här kan du finjustera modellen baserat på vald modell. om det är avmarkerat kan du träna från början.

4.  Anslut utdata från modulen **DenseNet** , Training and validation image data uppsättning till [träna Pytorch-modellen](train-pytorch-model.md). 

5. Skicka pipelinen.

## <a name="results"></a>Resultat

När pipeline-körningen har slutförts ska du använda modellen för att skapa en modell för [Pytorch-modellen](train-pytorch-model.md) för att skapa en [bild modell](score-image-model.md)för att förutsäga värden för nya exempel på indata.

## <a name="technical-notes"></a>Tekniska anteckningar  

###  <a name="module-parameters"></a>Parametrar för modul  

| Name       | Intervall | Typ    | Default           | Beskrivning                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Modellnamn | Alla   | Läge    | resnext101 \_ 32x8d | Namn på en viss ResNet-struktur       |
| Tränats | Alla   | Boolesk | Sant              | Om du vill använda en förtränad modell på ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Utdata  

| Name            | Typ                    | Beskrivning                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modell som inte är tränad | UntrainedModelDirectory | En ResNet modell som kan anslutas till träna Pytorch-modellen. |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 