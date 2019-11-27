---
title: 'Besluts träd med djup klass: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för besluts träd med multiklass i Azure Machine Learning för att skapa en klassificerare med märkta data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 7f39d393b96b1515e4815abdc28ac4079f271c1b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232610"
---
# <a name="multiclass-boosted-decision-tree"></a>Förbättrat beslutsträd med flera klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för besluts träd.

Ett utökat besluts träd är en ensemble-utbildnings metod där det andra trädet korrigerar fel i det första trädet, och det tredje trädet korrigeras för felen i de första och andra träden och så vidare. Förutsägelser bygger på trädets ensembler tillsammans.

## <a name="how-to-configure"></a>Så här konfigurerar du 

Den här modulen skapar en modell som inte är tränad. Eftersom klassificeringen är en övervakad inlärnings metod behöver du en *etikettad data uppsättning* som innehåller en etikett kolumn med ett värde för alla rader.

Du kan träna den här typen av modell med hjälp av [träna-modellen](././train-model.md). 

1.  Lägg till modulen **besluts träd med multiklass** i din pipeline.

1.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet för att **skapa utbildare läge** .

    + **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.


    *  **Maximalt antal löv per träd** begränsar det maximala antalet terminalserversessioner (löv) som kan skapas i alla träd.
    
        Genom att öka det här värdet kan du öka storleken på trädet och uppnå högre precision, samtidigt som det är risk för överanpassning och längre inlärnings tid.
  
    * **Minsta antal sampel per lövnod** anger antalet fall som krävs för att skapa en terminalserversession (löv) i ett träd.  

         Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.

    * **Inlärnings takten** definierar steg storleken under inlärningen. Ange ett tal mellan 0 och 1.

         Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar i en optimal lösning. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.

    * **Antal träd konstruktioner** anger det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

    *  Med alternativet för **slumpmässigt nummer** kan du ange ett icke-negativt heltal som ska användas som slumpmässigt Seed-värde. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.  

         Det slumpmässiga startvärdet anges som standard till 42. Efterföljande körningar som använder olika slumpmässiga frön kan ha olika resultat.

> [!Note]
> Om du ställer in **skapa utbildare** för en **parameter**ansluter du en taggad data uppsättning och modulen [träna modell](./train-model.md) .

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
