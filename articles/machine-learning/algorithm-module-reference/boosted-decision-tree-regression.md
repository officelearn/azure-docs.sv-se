---
title: 'Regression för beslutsträd: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen förstärkta Regression för beslut i Azure Machine Learning-tjänsten för att skapa en ensemble för regressionsträd med boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028342"
---
# <a name="boosted-decision-tree-regression-module"></a>Bättre beslut förbättrad Regression modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en ensemble för regressionsträd med boosting. *Öka* innebär att varje trädet är beroende av tidigare träd. Algoritmen som lär sig effektivare återstående för träd som föregås den. Därför tenderar boosting i ett beslut trädet ensemble att öka noggrannheten med en liten risk för mindre täckning.  
  
Den här metoden regression är en metod för övervakad inlärning och därför kräver ett *märkta datauppsättning*. Etikettkolumnen måste innehålla numeriska värden.  

> [!NOTE]
> Använd den här modulen endast med datauppsättningar som använder numeriska variabler.  

När du har definierat modellen kan du tränar den med hjälp av den [träna modell](./train-model.md).

> [!TIP]
> Vill du veta mer om träd som har skapats? När modellen har tränats, högerklickar du på utdata från den [Träningsmodell](./train-model.md) modul och välj **visualisera** i trädet som har skapats för varje iteration. Du kan granska nedåt i delningar för varje träd och finns i reglerna för varje nod.  
  
## <a name="more-about-boosted-regression-trees"></a>Mer om förbättrat regressionsträd  

Öka är en av flera klassiska metoder för att skapa ensemble modeller, tillsammans med förpackningskapacitet, slumpmässigt skogar och så vidare.  I Azure Machine Learning använder förbättrat beslutsträd en effektiv implementering av MART-gradient boosting algoritmen. Gradient boosting är en machine learning-teknik för regressionsproblem. Den bygger varje regression trädet i ett steg för steg så sätt, med hjälp av en fördefinierad förlust funktion att mäta felet i varje steg och åtgärda den i nästa. Därför är förutsägelse modellen faktiskt en ensemble av svagare förutsägelsemodeller.  
  
I regressionsproblem boosting bygger en serie träd steg för steg så och väljer sedan optimala trädet med hjälp av en godtyckligt differentiable förlust-funktion.  
  
Mer information finns i följande artiklar:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Den här Wikipedia-artikel gradient boosting innehåller bakgrundsinformation om bättre träd. 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Från RankNet till LambdaRank till LambdaMART: En översikt. Av J.C. Burges.

Gradient boosting metod kan även användas för klassificering, problem genom att minska dem till regression med en lämplig förlust-funktion. Mer information om implementering bättre träd för klassificering finns i [Tvåklassförhöjt beslutsträd](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Så här konfigurerar du förstärkta Regression för beslut

1.  Lägg till den **beslutsträd** modulen i experimentet. Du hittar den här modulen under **Maskininlärning**, **initiera**under den **Regression** kategori. 
  
2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en specifik uppsättning värden som argument.  
   
  
3. **Maximalt antal blad per trädet**: Ange det maximala antalet terminal noder (löv) som kan skapas i valfri trädet.  

    Genom att öka det här värdet du potentiellt öka storleken på trädet och få bättre noggrannhet på millisekunder, dess risk overfitting och utbildning längre tid.  

4. **Minsta antal prover per lövnod**: Ange det minsta antalet fall som krävs för att skapa en terminal nod (löv-) i ett träd.

    Genom att öka det här värdet kan du öka tröskelvärdet för att skapa nya regler. Med standardvärdet 1 kan till exempel även ett enda fall orsaka en ny regel som ska skapas. Om du ökar värdet till 5, skulle träningsdata behöva innehålla minst 5 ärenden som uppfyller villkor som är samma.

5. **Learning rate**: Ange ett tal mellan 0 och 1 som definierar Stegstorlek vid learning. Inlärningsfrekvensen avgör hur snabb eller långsam eleven konvergerar på den bästa lösningen. Om Stegstorlek är för stor, kan du överskridande den bästa lösningen. Om Stegstorlek är för liten, tar utbildning längre tid att Konvergera på den bästa lösningen.

6. **Antal träd konstrueras**: Visa det totala antalet beslutsträd för att skapa i ensemble. Du kan eventuellt få bättre täckning genom att skapa mer beslutsträd, men utbildningstid ökar.

    Det här värdet styr också antalet träd som visas när visualisera den tränade modellen. Om du vill visa eller skriva ut en ingle trädet, kan du ange värdet 1. men bara en trädet produceras (trädet med den första uppsättningen parametrar) och inga ytterligare iterationer utförs.

7. **Slumpmässigt nummer seed**: Ange ett valfritt positivt heltal som ska användas som slumpmässiga seed-värdet. Ange ett startvärde, vilket ger reproducerbarhet i körningar som har samma data och parametrar.

    Som standard anges slumpmässig dirigering till 0, vilket innebär att inledande seed-värdet hämtas från systemklockan.
  
8. **Tillåt okända kategoriska nivåer**: Välj det här alternativet för att skapa en grupp för okända värden i uppsättningar för träning och verifiering. Om du avmarkerar det här alternativet kan modellen Acceptera bara de värdena som finns i träningsdata. Modellen kan vara mindre exakt för kända värden, men det kan ge bättre förutsägelser för nya (okänd) värden.

9. Lägg till en datauppsättning för träning och en av modulerna som utbildning:

    - Om du ställer in **skapande trainer läge** alternativet att **enda Parameter**, använda den [Träningsmodell](train-model.md) modulen.  
  
    

10. Kör experimentet.  
  
### <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill se i trädet som har skapats för varje iteration, högerklickar du på utdata från den [Träningsmodell](train-model.md) modul och välj **visualisera**.
  
     Klicka på varje träd för att granska nedåt i delningen och se regler för varje nod.  

+ Om du vill använda modellen för bedömning, Anslut den till [Poängmodell](./score-model.md), för att förutsäga värden för nya inkommande exempel.

+ Spara en ögonblicksbild av den tränade modellen genom att högerklicka på den **Trained model** utdata från modulen för utbildning och välj **Spara som**. Kopia av den tränade modellen som du har sparat uppdateras inte på efterföljande körningar av experimentet.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 