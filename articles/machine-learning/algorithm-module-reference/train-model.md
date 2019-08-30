---
title: 'Träna modell: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen **träna modell** i Azure Machine Learning-tjänsten för att träna en klassificerings-eller Regressions modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 08b551e766632949db350478fa8d3725906c8af8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128451"
---
# <a name="train-model-module"></a>Train-modell

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att träna en klassificerings-eller Regressions modell. Träningen sker efter att du har definierat en modell och angett dess parametrar och kräver taggade data. Du kan också använda **träna modell** för att omträna en befintlig modell med nya data. 

## <a name="how-the-training-process-works"></a>Så här fungerar inlärnings processen

I Azure Machine Learning är det vanligt vis en tre stegs process att skapa och använda en maskin inlärnings modell. 

1. Du konfigurerar en modell genom att välja en viss typ av algoritm och definiera dess parametrar eller dess egenskaper. Välj någon av följande modell typer: 

    + **Klassificerings** modeller, baserade på neurala nätverk, besluts träd och besluts skogar och andra algoritmer.
    + **Regressions** modeller, som kan innehålla standard linjär regression eller som använder andra algoritmer, inklusive neurala-nätverk och Bayesian regression.  

2. Ange en data mängd som är märkt och har data som är kompatibla med algoritmen. Anslut både data och modellen för att **träna modellen**.

    Vilken utbildning som skapas är ett speciellt binärformat, iLearner, som kapslar in de statistiska mönster som har lärts från data. Du kan inte ändra eller läsa det här formatet direkt. andra moduler kan dock använda den här utbildade modellen. 
    
    Du kan också Visa modellens egenskaper. Mer information finns i avsnittet resultat.

3. När utbildningen har slutförts använder du den tränade modellen med en [](./score-model.md)av poängsättnings-modulerna för att göra förutsägelser på nya data.

## <a name="how-to-use-train-model"></a>Använda **träna modell**  
  
1.  I Azure Machine Learning konfigurerar du en klassificerings modell eller Regressions modell.
    
2. Lägg till modulen **träna modell** i experimentet.  Du hittar den här modulen under kategorin **Machine Learning** . Expandera **träna**och dra modulen **träna modell** till experimentet.
  
3.  Koppla det nedtränade läget till vänster. Koppla data uppsättningen utbildning till den högra indatan för **träna modell**.

    Data uppsättningen för träning måste innehålla en etikett kolumn. Alla rader utan etiketter ignoreras.
  
4.  För **kolumnen etikett**klickar du på **Starta kolumn väljaren**och väljer en enda kolumn som innehåller resultat som modellen kan använda för utbildning.
  
    - För klassificerings problem måste etikett kolumnen innehålla antingen **kategoriska** -värden eller **diskreta** värden. Några exempel kan vara ja/nej-klassificering, en klassificerings kod, ett namn på en sjukdom eller en inkomst grupp.  Om du väljer en noncategorical-kolumn kommer modulen att returnera ett fel under träningen.
  
    -   För Regressions problem måste etikett kolumnen innehålla **numeriska** data som representerar variabeln Response. Det bästa är att de numeriska data representerar en kontinuerlig skala. 
    
    Exempel kan vara ett kredit risk poäng, planerat tid för att Miss lyckas med en hård disk eller det prognostiserade antalet anrop till ett Call Center på en bestämd dag eller tid.  Om du inte väljer en numerisk kolumn kan du få ett fel meddelande.
  
    -   Om du inte anger vilken etikett kolumn som ska användas försöker Azure Machine Learning att härleda som är lämplig etikett kolumn med hjälp av data uppsättningens metadata. Om du väljer fel kolumn använder du kolumn väljaren för att korrigera den.
  
    > [!TIP] 
    > Om du har problem med att använda kolumn Väljaren kan du läsa mer i artikeln [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) . I den här artikeln beskrivs några vanliga scenarier och tips för att använda alternativen **med regler** och **efter namn** .
  
5.  Kör experimentet. Om du har stora mängder data kan det ta en stund.

## <a name="bkmk_results"></a>Gör

När modellen har tränats:

+ Om du vill visa modell parametrar och funktions vikter högerklickar du på utdata och väljer **visualisera**.
+ Om du vill använda modellen i andra experiment högerklickar du på modellen och väljer **Spara modell**. Ange ett namn för modellen. 

    Detta sparar modellen som en ögonblicks bild som inte uppdateras genom upprepade körningar av experimentet.
+ Om du vill använda modellen för att förutsäga nya värden ansluter du den till modulen [Poäng modell](./score-model.md) tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 