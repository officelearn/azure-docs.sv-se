---
title: 'Logistik regression i multiklass: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder MultiClass Logistisk regression-modulen i Azure Machine Learning för att skapa en logistik Regressions modell som kan användas för att förutsäga flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: aba33730d235cc50742cdc86007933e12f763053
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548466"
---
# <a name="multiclass-logistic-regression-module"></a>Logistik Regressions-modul för multiklass

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en logistik Regressions modell som kan användas för att förutsäga flera värden.

Klassificering med Logistisk regression är en övervakad inlärnings metod och kräver därför en etikettad data uppsättning. Du tränar modellen genom att tillhandahålla modellen och den märkta data uppsättningen som indata till en modul, till exempel [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya exempel på ingångar.

Azure Machine Learning tillhandahåller också en [logistik Regressions modell med två klasser](./two-class-logistic-regression.md) som passar för klassificering av binära variabler eller dichotomous variabler.

## <a name="about-multiclass-logistic-regression"></a>Om multiklass logistik regression

Logistisk regression är en välkänd metod i statistik som används för att förutsäga sannolikheten för ett resultat och är populär för klassificerings aktiviteter. Algoritmen förutsäger sannolikheten för förekomst av en händelse genom att anpassa data till en logistik funktion. 

I multiklass Logistisk regression kan klassificeraren användas för att förutsäga flera resultat.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurera en logistik regression med multiklass

1. Lägg till **logistik Regressions** -modulen för multiklass i pipelinen.

2. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .

    + **Enskild parameter**: Använd det här alternativet om du vet hur du vill konfigurera modellen och ange en viss uppsättning värden som argument.

    + **Parameter intervall**: Använd det här alternativet om du inte är säker på de bästa parametrarna och vill använda en parameter rensning.

3. **Optimerings tolerans**, ange tröskelvärdet för optimerings konvergens. Om förbättringen mellan iterationer är mindre än tröskelvärdet stannar algoritmen och returnerar den aktuella modellen.

4. **L1 regulariseringshastigheten vikt**, **L2 regulariseringshastigheten vikt**: Ange ett värde som ska användas för regulariseringshastigheten-parametrarna L1 och L2. Ett värde som inte är noll rekommenderas för båda.

    Regulariseringshastigheten är en metod för att förhindra övermontering genom att motverka modeller med extrema koefficient värden. Regulariseringshastigheten fungerar genom att lägga till den påföljd som är kopplad till koefficienterna till felet i hypotesen. En korrekt modell med extrema koefficienter skulle innebära mer, men en mindre exakt modell med fler restriktiva värden skulle bli mindre.

     L1-och L2-regulariseringshastigheten har olika effekter och användnings områden. L1 kan användas för sparse-modeller, vilket är användbart när du arbetar med avancerade data. L2-regulariseringshastigheten är däremot bättre för data som inte är sparse.  Den här algoritmen stöder en linjär kombination av L1-och L2-regulariseringshastigheten värden: det vill säga om `x = L1` och `y = L2`, definierar `ax + by = c` det linjära omfånget för regulariseringshastigheten-villkoren.

     Olika linjära kombinationer av L1-och L2-villkor har gjorts för logistik Regressions modeller som [elastiska net regulariseringshastigheten](https://wikipedia.org/wiki/Elastic_net_regularization).

6. Värde för **slumpmässig siffra**: Ange ett heltals värde som ska användas som startvärdet för algoritmen om du vill att resultatet ska upprepas vid körning. Annars används ett system klock värde som startvärdet, vilket kan producera något annorlunda resultat i körningar av samma pipeline.

8. Anslut en data uppsättning med etiketter och en av träna-modulerna:

    + Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](./train-model.md) .

9. Köra en pipeline.



## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 