---
title: 'Tågmodell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen **Tågmodell** i Azure Machine Learning för att träna en klassificerings- eller regressionsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455867"
---
# <a name="train-model-module"></a>Tågmodellmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att träna en klassificerings- eller regressionsmodell. Träningen sker efter att du har definierat en modell och angett dess parametrar och kräver taggade data. Du kan också använda **Train Model** för att omskola en befintlig modell med nya data. 

## <a name="how-the-training-process-works"></a>Så här fungerar utbildningsprocessen

I Azure Machine Learning är det vanligtvis en trestegsprocess att skapa och använda en maskininlärningsmodell. 

1. Du konfigurerar en modell genom att välja en viss typ av algoritm och definiera dess parametrar eller hyperparametrar. Välj någon av följande modelltyper: 

    + **Klassificeringsmodeller,** baserade på neurala nätverk, beslutsträd och beslutsskogar och andra algoritmer.
    + **Regressionsmodeller,** som kan innehålla linjär standardregression, eller som använder andra algoritmer, inklusive neurala nätverk och Bayesiansk regression.  

2. Ange en datauppsättning som är märkt och har data som är kompatibla med algoritmen. Anslut både data och modell till **Train Model**.

    Vad utbildning producerar är ett specifikt binärt format, iLearner, som kapslar in de statistiska mönster som lärts från data. Du kan inte ändra eller läsa det här formatet direkt. Andra moduler kan dock använda den här tränade modellen. 
    
    Du kan också visa modellens egenskaper. Mer information finns i avsnittet Resultat.

3. När träningen är klar använder du den tränade modellen med en av [bedömningsmodulerna](./score-model.md)för att göra förutsägelser om nya data.

## <a name="how-to-use-train-model"></a>Så här använder du tågmodell 
  
1.  Konfigurera en klassificeringsmodell eller regressionsmodell i Azure Machine Learning.
    
2. Lägg till modulen **Tågmodell** i pipelinen.  Du hittar den här modulen under kategorin **Maskininlärning.** Expandera **Tåg**och dra sedan **tågmodellmodulen** till pipelinen.
  
3.  Fäst det otränade läget i vänster ingång. Bifoga träningsdatauppsättningen till den högra inmatningen av **Tågmodell**.

    Träningsdatauppsättningen måste innehålla en etikettkolumn. Alla rader utan etiketter ignoreras.
  
4.  För **kolumnen Etikett**klickar du på Redigera **kolumn** på modulens högra panel och väljer en enskild kolumn som innehåller resultat som modellen kan använda för utbildning.
  
    - För klassificeringsproblem måste etikettkolumnen innehålla antingen **kategoriska** värden eller **diskreta** värden. Några exempel kan vara ett ja/nej-betyg, en sjukdomsklassificeringskod eller ett namn eller en inkomstgrupp.  Om du väljer en icke-kategorisk kolumn returnerar modulen ett fel under träningen.
  
    -   För regressionsproblem måste etikettkolumnen innehålla **numeriska** data som representerar svarsvariabeln. Helst representerar numeriska data en kontinuerlig skala. 
    
    Exempel kan vara en kreditriskpoäng, den beräknade tiden till fel för en hårddisk eller det prognostiserade antalet samtal till ett callcenter en viss dag eller tid.  Om du inte väljer en numerisk kolumn kan du få ett felmeddelande.
  
    -   Om du inte anger vilken etikettkolumn som ska användas, kommer Azure Machine Learning att försöka dra slutsatsen vilken som är lämplig etikettkolumn, med hjälp av metadata för datauppsättningen. Om den väljer fel kolumn använder du kolumnväljaren för att korrigera den.
  
    > [!TIP] 
    > Om du har problem med att använda kolumnväljaren läser du artikeln [Välj kolumner i datauppsättningen](./select-columns-in-dataset.md) för tips. Den beskriver några vanliga scenarier och tips för att använda **med reglerna och** **bynamn** alternativ.
  
5.  Skicka pipelinen. Om du har mycket data kan det ta ett tag.

## <a name="results"></a><a name="bkmk_results"></a>Resultat

När modellen är utbildad:


+ Om du vill använda modellen i andra pipelines markerar du modulen och väljer ikonen **Registrera datauppsättning** under fliken **Utdata** på höger panel. Du kan komma åt sparade modeller i modulpaletten under **Datauppsättningar**.

+ Om du vill använda modellen för att förutsäga nya värden ansluter du den till modulen [Poängmodell](./score-model.md) tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 