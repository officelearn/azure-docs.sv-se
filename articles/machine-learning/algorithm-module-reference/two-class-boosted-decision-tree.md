---
title: 'Tvåklassförhöjt beslutsträd: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Tvåklassförhöjt beslutsträd i Azure Machine Learning-tjänsten för att skapa en machine learning-modell som baseras på beslutsträd träd algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027952"
---
# <a name="two-class-boosted-decision-tree-module"></a>Tvåklassförhöjt beslutsträd modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en machine learning-modell som baseras på beslutsträd träd algoritmen. 

Ett beslutsträd är en ensemble learning metod där andra trädet Korrigerar fel i första trädet, tredje trädet Korrigerar fel i den första och andra, och så vidare.  Förutsägelserna baseras på hela ensemble av träd tillsammans som gör förutsägelser.
  
I allmänhet är förbättrat beslutsträd de enklaste metoderna som ska få högsta prestanda på en mängd olika maskininlärning när korrekt konfigurerad. Men de är också en av mer minne-inlärning och den aktuella implementationen innehåller allt i minnet. En modell för beslutsträd trädet kan därför inte kan bearbeta stora datamängder som vissa linjär eleverna kan hantera.

## <a name="how-to-configure"></a>Så här konfigurerar du

Den här modulen skapar ett omdöme klassificeringsmodellen. Eftersom klassificeringen är en metod för övervakad inlärning, att öva med modellen, måste du använda en *taggade datauppsättning* som innehåller en etikett-kolumn med ett värde för alla rader.

Du kan skapa den här typen av modellen genom att använda [träna modell](././train-model.md). 

1.  I Azure Machine Learning, lägger du till den **beslutsträd** modulen i experimentet.
  
2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.
  
    + **Enkel parametern**: Om du vet hur du vill konfigurera modellen kan ange du en specifik uppsättning värden som argument.
  
  
3.  För **maximalt antal blad per trädet**, anger det maximala antalet terminal noder (löv) som kan skapas i valfri trädet.
  
     Genom att öka det här värdet du potentiellt öka storleken på trädet och få bättre noggrannhet på millisekunder, dess risk overfitting och utbildning längre tid.
  
4.  För **minsta antal prover per lövnod**, ange antalet fall som krävs för att skapa en terminal nod (löv-) i ett träd.  
  
     Genom att öka det här värdet kan du öka tröskelvärdet för att skapa nya regler. Med standardvärdet 1 kan till exempel även ett enda fall orsaka en ny regel som ska skapas. Om du ökar värdet till 5, skulle träningsdata behöva innehålla minst fem ärenden som uppfyller villkor som är samma.
  
5.  För **Learning rate**, skriver du ett tal mellan 0 och 1 som definierar Stegstorlek vid learning.  
  
     Inlärningsfrekvensen avgör hur snabb eller långsam eleven konvergerar på den bästa lösningen. Om Stegstorlek är för stor, kan du överskridande den bästa lösningen. Om Stegstorlek är för liten, tar utbildning längre tid att Konvergera på den bästa lösningen.
  
6.  För **antal träd konstrueras**, visar det totala antalet beslutsträd för att skapa i ensemble. Du kan eventuellt få bättre täckning genom att skapa mer beslutsträd, men utbildning ökar.
  
     Det här värdet styr också antalet träd som visas när visualisera den tränade modellen. Om du vill visa eller skriva ut ett enda träd, ange värdet till 1. Men när du gör det bara en trädet produceras (trädet med den första uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
7.  För **nummer slumptal**kan du också ange ett icke-negativt heltal som ska användas som slumpmässiga seed-värdet. Ange ett startvärde, vilket ger reproducerbarhet i körningar som har samma data och parametrar.  
  
     Slumpmässig dirigering är som standard till 0, vilket innebär att inledande seed-värdet hämtas från systemklockan.  Efterföljande körningar med hjälp av en slumpmässig dirigering kan ha olika resultat.
  

9. Träna modellen.
  
    + Om du ställer in **skapande trainer läge** till **enda Parameter**, ansluta en taggade datauppsättning och [Träningsmodell](./train-model.md) modulen.  
  
   
## <a name="results"></a>Resultat

När modellen är klar högerklickar du på utdata från [Träningsmodell](./train-model.md) att se resultat:

+ Om du vill se i trädet som har skapats för varje iteration **visualisera**. 
+ Granska nedåt i delningen och regler för varje nod, klickar du på varje träd.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 