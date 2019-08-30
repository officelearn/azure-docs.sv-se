---
title: 'Besluts skogs regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder den genomsnittliga Perceptron-modulen med två klasser i Azure Machine Learning service för att skapa en maskin inlärnings modell baserat på den genomsnittliga Perceptron-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f560923b0a5457ac5fd03c7f76fc4315c6ca08e8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128428"
---
# <a name="two-class-averaged-perceptron-module"></a>Genomsnittlig Perceptron-modul med två klasser

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på den genomsnittliga Perceptron-algoritmen.  
  
Den här klassificerings algoritmen är en övervakad inlärnings metod och kräver en *Taggad data uppsättning*som innehåller en etikett kolumn. Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  

### <a name="about-averaged-perceptron-models"></a>Om Genomsnittligt antal Perceptron-modeller

Den *genomsnittliga Perceptron-metoden* är en tidig och enkel version av ett neurala-nätverk. I den här metoden klassificeras indata i flera möjliga utdata baserat på en linjär funktion och kombineras sedan med en uppsättning vikter som härleds från funktions vektorn, och därför namnet "Perceptron".

De enklare Perceptron-modellerna är lämpliga för inlärning av linjärt särskiljande mönster, medan neurala-nätverk (särskilt djup neurala-nätverk) kan modellera mer komplexa klass gränser. Perceptrons är dock snabbare, och eftersom de behandlar ärenden seriellt kan perceptrons användas med kontinuerlig utbildning.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Konfigurera genomsnittlig Perceptron i två klasser

1.  Lägg till den **genomsnittliga Perceptron-modulen med två klasser** i experimentet.  

2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: Om du vet hur du vill konfigurera modellen anger du en angiven uppsättning värden som argument.
  
3.  För **inlärnings frekvens**anger du ett värde för *inlärnings takten*. Inlärnings takt svärdet styr storleken på det steg som används i Stochastic gradient-brantaste varje gång modellen testas och korrigeras.
  
     Genom att göra hastigheten mindre testar du modellen oftare, med den risk att du kan fastna i en lokal platå. Genom att göra steget större kan du konvergera snabbare, vid risken för att den sanna minimi överskrids.
  
4.  För **maximalt antal iterationer**anger du hur många gånger du vill att algoritmen ska undersöka tränings data.  
  
     Att stoppa tidigt ger ofta bättre generalisering. Genom att öka antalet iterationer kan du förbättra anpassningen vid risk för överanpassning.
  
5.  Om du vill använda **slumpmässigt antal frön**kan du ange ett heltals värde som ska användas som start värde. Du rekommenderas att använda ett start värde om du vill säkerställa reproducerbarheten för experimentet i flera körningar.  
  
1.  Anslut en data uppsättning för utbildning och en av inlärnings modulerna:
  
    -   Om du ställer in **skapa** utbildare för **en parameter**använder du modulen [träna modell](train-model.md) .

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se en översikt över modellens parametrar, tillsammans med funktions vikten från träningen, högerklickar du på utdata från [träna modell](./train-model.md).


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 