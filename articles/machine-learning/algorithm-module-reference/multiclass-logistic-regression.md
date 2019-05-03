---
title: 'Inom Logistic Regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Multiclass Logistic Regression-modulen i Azure Machine Learning-tjänsten för att skapa en logistic regression-modell som kan användas för att förutsäga flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029332"
---
# <a name="multiclass-logistic-regression-module"></a>Inom Logistic Regression-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en logistic regression-modell som kan användas för att förutsäga flera värden.

Klassificering med hjälp av logistic regression är en metod för övervakad inlärning och därför kräver en taggade datauppsättning. Du tränar modellen genom att ange modellen och märkta datauppsättningen som indata till en modul som [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya inkommande exempel.

Azure Machine Learning tillhandahåller också en [Tvåklassförhöjt Logistic Regression](./two-class-logistic-regression.md) modulen, som är lämpligt för klassificering av binära eller dichotomous variabler.

## <a name="about-multiclass-logistic-regression"></a>Om inom logistic regression

Logistic regression är en välkänd metod i statistik som används för att förutse sannolikheten för ett resultat och används ofta för klassificering. Algoritmen som förutsäger sannolikheten för förekomst av en händelse effektivare data till en logistisk funktion. 

I inom logistic regression kan klassificeraren användas för att förutsäga flera resultat.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurera en inom logistisk regression

1. Lägg till den **Multiclass Logistic Regression** modulen till arbetsytan för experimentet.

2. Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.

    + **Enkel parametern**: Använd det här alternativet om du vet hur du vill konfigurera modellen och ange en specifik uppsättning värden som argument.

    + **Intervallet för parametern**: Använd det här alternativet om du inte är säker på de lämpligaste parametrarna och vill använda en parameterrensning.

3. **Optimering tolerans**, ange tröskelvärde för optimering konvergens. Om förbättring mellan iterationer är mindre än tröskelvärdet, stoppar algoritmen och returnerar den aktuella modellen.

4. **L1 regularisering vikt**, **L2 regularisering vikt**: Ange ett värde för parametrarna regularisering L1 och L2. Ett annat värde än noll rekommenderas för båda.

    Regularisering är en metod för att förhindra overfitting av penalizing modeller med extrem koefficienten värden. Regularisering fungerar genom att lägga till den särskilda avgifter som är associerad med koefficienten värden till fel hypotesen. En korrekt modell med extrem koefficienten värden skulle vara mer straffas, men en mindre exakta modell med mer konservativ värden skulle straffas mindre.

     L1 och L2 regulariseringshastighet har olika effekter och använder. L1 kan tillämpas på null-optimerade modeller, vilket är användbart när du arbetar med hög-dimensionell data. L2 regularisering är däremot bättre för data som inte är null-optimerad.  Den här algoritmen stöder en linjär värdekombinationen L1 och L2 regularisering: det vill säga om `x = L1` och `y = L2`, `ax + by = c` definierar linjär loppet av regularisering villkoren.

     Olika linjära kombinationer av L1 och L2 villkoren planeras för logistiska regressionsmodeller, till exempel [elastiska net regularisering](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Slumpmässigt nummer seed**: Ange ett heltalsvärde som ska användas som startvärde för algoritmen om du vill att resultatet ska upprepningsbara över körningar. I annat fall används ett systemklockan värde som det startvärde som kan ge lite olika resultat i körningar av samma experimentet.

8. Anslut en taggade datauppsättning och en av modulerna som träna:

    + Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](./train-model.md) modulen.

9. Kör experimentet.

## <a name="results"></a>Resultat

När utbildning är klar visas en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna vet utbildning, högerklicka på utdata från den [Träningsmodell](./train-model.md) modul och välj **visualisera**.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 