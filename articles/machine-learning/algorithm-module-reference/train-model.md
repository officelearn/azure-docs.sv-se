---
title: 'Träna modell: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder den **träna modell** modul i Azure Machine Learning-tjänsten för att träna en modell för klassificerings- eller regressionsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028117"
---
# <a name="train-model-module"></a>Train-modell

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att träna en modell för klassificerings- eller regressionsmodell. Utbildning sker om du har definierat en modell och ange dess parametrar och kräver taggade data. Du kan också använda **Träningsmodell** att träna en befintlig modell med nya data. 

## <a name="how-the-training-process-works"></a>Så här fungerar processen för utbildning

I Azure Machine Learning är skapa och använda en maskininlärningsmodell vanligtvis en trestegsprocess. 

1. Du kan konfigurera en modell genom att välja en viss typ av algoritm och definiera dess parametrar eller hyperparametrar. Välj något av följande modelltyper av: 

    + **Klassificering** modeller, baserat på neurala nätverk, beslutsträd, och beslut skogar och andra algoritmer.
    + **Regression** modeller, vilket kan omfatta standard linjär regression eller som använder andra algoritmer, inklusive neurala nätverk och Bayesian regression.  

2. Ange en datauppsättning som är märkta och som innehåller data som är kompatibel med algoritmen. Ansluta både data och modellen till **Träningsmodell**.

    Vilka utbildning ger är ett specifikt binärformat iLearner som kapslar in statistisk mönster som registrerats från data. Du kan inte direkt ändra eller läsa det här formatet; andra moduler kan dock använda den här tränade modellen. 
    
    Du kan också visa egenskaperna för modellen. Mer information finns i resultatavsnittet.

3. När utbildning har slutförts kan du använda den tränade modellen med någon av de [bedömning moduler](./score-model.md), för att göra förutsägelser kring nya data.

## <a name="how-to-use-train-model"></a>Hur du använder **Träningsmodell**  
  
1.  Konfigurera ett klassificeringsmodellen eller regressionsmodell i Azure Machine Learning.
    
2. Lägg till den **Träningsmodell** modulen till arbetsytan för experimentet.  Du hittar den här modulen under den **Machine Learning** kategori. Expandera **träna**, och dra sedan den **Träningsmodell** modulen i experimentet.
  
3.  Koppla omdöme läge på den vänstra indataporten. Bifoga datauppsättning för träning i den högra indataporten för **Träningsmodell**.

    Datauppsättning för träning måste innehålla en etikett-kolumn. Några rader utan etiketter ignoreras.
  
4.  För **etikettkolumnen**, klickar du på **starta kolumnväljaren**, och välj en enda kolumn som innehåller modellen kan använda för träning resultat.
  
    - För klassificering, problem, etikettkolumnen måste innehålla antingen **kategoriska** värden eller **diskreta** värden. Några exempel kanske en Ja/Nej-klassificering, en sjukdomar klassificeringskod eller namn eller en inkomst-grupp.  Om du väljer en noncategorical kolumn returneras ett fel vid träning i modulen.
  
    -   Regression problem etikettkolumnen måste innehålla **numeriska** data som representerar variabeln svar. Vi rekommenderar representerar de numeriska data som en kontinuerlig skala. 
    
    Exempel kan vara en kredit i riskpoäng planerade tid för fel för en hårddisk eller prognostiserat antal anrop till ett Callcenter på en viss dag och tid.  Om du inte väljer en numerisk kolumn kan du få ett felmeddelande.
  
    -   Om du inte anger vilken kolumn som etiketten ska använda försöker Azure Machine Learning härleda som är rätt etikett-kolumn med hjälp av metadata för datauppsättningen. Om det använder fel kolumn kan du använda kolumnväljaren sätt.
  
    > [!TIP] 
    > Om du har problem med hjälp av kolumnväljaren kan du läsa artikeln [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) tips. Den beskriver några vanliga scenarier och tips för att använda den **med regler** och **efter namn** alternativ.
  
5.  Kör experimentet. Om du har stora mängder data kan ta det en stund.

## <a name="bkmk_results"></a> Resultat

När modellen tränas:

+ Om du vill visa modellen parametrarna och funktionen vikterna, högerklickar du på den utdata och välj **visualisera**.
+ Om du vill använda modellen i andra experiment, högerklicka på modellen och välj **Spara modell**. Skriv ett namn för modellen. 

    Detta sparar modellen som en ögonblicksbild som inte har uppdaterats vid körningar av experimentet.
+ Om du vill använda modellen i förutsäga nya värden, Anslut den till den [Poängmodell](./score-model.md) modulen tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 