---
title: 'Förstärkt regression av beslutsträd: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Regressionsmodul för marknadsförda beslutsträd i Azure Machine Learning för att skapa en ensemble med regressionsträd med hjälp av öka.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456734"
---
# <a name="boosted-decision-tree-regression-module"></a>Förstärkt moduler för regressionsmodul för beslutsträd

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en ensemble av regressionsträd med hjälp av öka. *Öka* innebär att varje träd är beroende av tidigare träd. Algoritmen lär sig genom att montera kvarvarande träd som föregick den. Således, öka i ett beslut träd ensemble tenderar att förbättra noggrannheten med några små risk för mindre täckning.  
  
Den här regressionsmetoden är en övervakad inlärningsmetod och kräver därför en *märkt datauppsättning*. Etikettkolumnen måste innehålla numeriska värden.  

> [!NOTE]
> Använd endast den här modulen med datauppsättningar som använder numeriska variabler.  

När du har definierat modellen tränar du den med hjälp av [tågmodellen](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mer om förstärkta regressionsträd  

Att öka är en av flera klassiska metoder för att skapa ensemblemodeller, tillsammans med uppsamlare, slumpmässiga skogar och så vidare.  I Azure Machine Learning använder förstärkta beslutsträd en effektiv implementering av MART-lutningshöjande algoritmen. Gradient öka är en maskininlärning teknik för regression problem. Den bygger varje regressionsträd på ett stegvis sätt, med hjälp av en fördefinierad förlustfunktion för att mäta felet i varje steg och korrigera för det i nästa. Således är förutsägelsemodellen faktiskt en ensemble av svagare förutsägelsemodeller.  
  
I regression problem, öka bygger en serie träd i ett steg-vis sätt, och sedan väljer det optimala trädet med hjälp av en godtycklig differentiable förlust funktion.  
  
Mer information finns i följande artiklar:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Denna Wikipedia artikel om gradient öka ger lite bakgrund på förstärkta träd. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Från RankNet till LambdaRank till LambdaMART: En översikt. Av J.C. Burges, det är jag.

Gradient boost metoden kan också användas för klassificeringsproblem genom att minska dem till regression med en lämplig förlustfunktion. Mer information om implementeringen av förstärkta träd för klassificeringsuppgifter finns i [Tvåklassens förstärkta beslutsträd](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Konfigurera boostade beslutsträd regression

1.  Lägg till modulen **Marknadsfört beslutsträd** i pipelinen. Du hittar den här modulen under **Machine Learning**, **Initialize**, under kategorin **Regression.** 
  
2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en specifik uppsättning värden som argument. 
     
    -   **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.    
   
  
3. **Maximalt antal löv per träd**: Ange det maximala antalet terminalnoder (löv) som kan skapas i valfritt träd.  

    Genom att öka detta värde, du potentiellt öka storleken på trädet och få bättre precision, med risk för övermontering och längre träningstid.  

4. **Minsta antal prover per bladnod:** Ange det minsta antal fall som krävs för att skapa en terminalnod (löv) i ett träd.

    Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel med standardvärdet 1 kan även ett enskilt ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste utbildningsdata innehålla minst 5 fall som uppfyller samma villkor.

5. **Inlärningsfrekvens**: Skriv ett tal mellan 0 och 1 som definierar stegstorleken när du lär dig. Inlärningsfrekvensen avgör hur snabbt eller långsamt eleven konvergerar på den optimala lösningen. Om stegstorleken är för stor kan du överskrida den optimala lösningen. Om stegstorleken är för liten tar träningen längre tid att konvergera på den bästa lösningen.

6. **Antal byggda träd**: Ange det totala antalet beslutsträd att skapa i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden ökar.

    Det här värdet styr också antalet träd som visas när den tränade modellen visualiseras. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. Dock produceras endast ett träd (trädet med den ursprungliga uppsättningen parametrar) och inga ytterligare iterationer utförs.

7. **Slumptalsutsäde**: Skriv ett valfritt icke-negativt heltal som ska användas som slumpmässigt frövärde. Om du anger ett frö säkerställs reproducerbarhet över körningar som har samma data och parametrar.

    Som standard är det slumpmässiga fröet inställt på 0, vilket innebär att det ursprungliga frövärdet erhålls från systemklockan.
  

9. Lägg till en träningsdatauppsättning och en av utbildningsmodulerna:

    - Om du ställer in alternativet **Skapa trainer mode** till Single **Parameter**använder du modulen [Tågmodell.](train-model.md)  
  
    

10. Skicka pipelinen.  
  
## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill använda modellen för bedömning ansluter du den till [Poängmodell](./score-model.md)för att förutsäga värden för nya indataexempel.

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **Tränad modell** och klickar på **Registrera datauppsättningsikonen.** Kopian av den tränade modellen sparas som en modul i modulträdet och kommer inte att uppdateras på efterföljande körningar av pipelinen.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
