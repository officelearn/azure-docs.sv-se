---
title: 'Multiclass boosted Decision Tree: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Multiclass Boosted Decision Tree i Azure Machine Learning för att skapa en klassificerare med hjälp av märkta data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920067"
---
# <a name="multiclass-boosted-decision-tree"></a>Förbättrat beslutsträd med flera klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en maskininlärningsmodell som baseras på algoritmen för förstärkta beslutsträd.

Ett förstärkt beslutsträd är en ensemble inlärningsmetod där det andra trädet korrigerar för fel i det första trädet, korrigerar det tredje trädet för felen i första och andra träd, och så vidare. Förutsägelser är baserade på ensemblen av träd tillsammans.

## <a name="how-to-configure"></a>Konfigurerar du 

Den här modulen skapar en otränad klassificeringsmodell. Eftersom klassificering är en övervakad inlärningsmetod behöver du en *märkt datauppsättning* som innehåller en etikettkolumn med ett värde för alla rader.

Du kan träna den här typen av modell med hjälp av [tågmodellen](././train-model.md). 

1.  Lägg till modulen **Multiclass Boosted Decision Tree** i pipelinen.

1.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**

    + **Enkel parameter:** Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.
    
    + **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.  

1. **Maximalt antal löv per träd** begränsar det maximala antalet terminalnoder (löv) som kan skapas i valfritt träd.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minsta antal prover per bladnod** anger antalet fall som krävs för att skapa en terminalnod (löv) i ett träd.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Inlärningshastighet** definierar stegstorleken när du lär dig. Ange ett tal mellan 0 och 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Antal byggda träd** anger det totala antalet beslutsträd att skapa i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden kommer att öka.

1. **Slumptalsutsäde** anger eventuellt ett icke-negativt heltal att använda som slumpmässigt frövärde. Om du anger ett frö säkerställs reproducerbarhet över körningar som har samma data och parametrar.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Om du ställer in **Skapa trainer-läge** till **En parameter**ansluter du en taggad datauppsättning och modulen [Tågmodell.](./train-model.md)

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
