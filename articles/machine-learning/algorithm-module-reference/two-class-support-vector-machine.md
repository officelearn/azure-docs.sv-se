---
title: 'Två-Class Support Vector Machine: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder den **två-Class Support Vector Machine** modul i Azure Machine Learning-tjänsten för att skapa en modell som baseras på algoritmen för stödvektormaskin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027937"
---
# <a name="two-class-support-vector-machine-module"></a>Två-Class Support Vector Machine-modulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en modell som baseras på algoritmen för stödvektormaskin. 

Support vector datorer (SVMs) är en väl utforskade klass av övervakad inlärning metoder. Den här specifika implementeringen lämpade för förutsägelse med två möjliga resultat baserat på kontinuerlig eller kategorisk variabler.

När du har definierat parametrarna modell, öva med modellen genom att använda modulerna som utbildning och att tillhandahålla en *taggade datauppsättning* som innehåller en etikett eller resultat kolumn.

## <a name="about-support-vector-machines"></a>Om support vector datorer

Support vector datorer är bland så snart som möjligt av machine learning-algoritmer och SVM modeller har använts i många program från informationshämtning till text- och klassificeringen. SVMs kan användas för både klassificering och regression uppgifter.

Den här SVM modellen är en övervakad inlärningsmodell som kräver märkta data. Pågående utbildning algoritmen analyserar indata och kan identifiera mönster i ett utrymme för flerdimensionella funktion som kallas den *hyperplane*.  Alla inkommande exempel representeras som punkter inom detta område och mappas om du vill spara kategorier på ett sätt som kategorier är dividerat med bredare och rensa en lucka som möjligt.

För förutsägelse tilldelar algoritmen SVM nya exempel i en kategori eller andra mappningen dem till att samma utrymme. 

## <a name="how-to-configure"></a>Så här konfigurerar du 

För den här modelltypen rekommenderas du att normalisera datauppsättningen innan du använder den för att träna klassificeraren.
  
1.  Lägg till den **två-Class Support Vector Machine** modulen i experimentet.  
  
2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Om du vet hur du vill konfigurera modellen kan ange du en specifik uppsättning värden som argument.  

3.  För **antalet iterationer**, skriver du ett tal som anger antalet upprepningar vid skapandet av modellen.  
  
     Den här parametern kan användas till att styra säkerhetsaspekten utbildning hastighet och Precision.  
  
4.  För **Lambda**, ange ett värde som ska användas som vikten för L1 regulariseringshastigheter.  
  
     Detta regularisering värde kan användas för att justera modellen. Större värden bestraffa mer komplexa modeller.  
  
5.  Välj alternativet **normalisera funktioner**om du vill att normalisera funktionerna innan utbildning.
  
     Om du använder normalisering innan utbildning, är datapunkter inriktade på medelvärdet och skalas så att den har en enhet av standardavvikelsen.
  
6.  Välj alternativet **projekt till enhet sphere**, för att normalisera koefficienter.
  
     Förutsäga värden till enhet utrymme innebär att innan du utbildning, datapunkter är inriktade på 0 och skalas så att den har en enhet av standardavvikelsen.
  
7.  I **nummer slumptal**, skriva ett heltalsvärde som ska användas som ett startvärde om du vill säkerställa reproducerbarhet från körningarna.  Annars kan används ett systemklockan värdet som startvärde, vilket kan resultera i något annorlunda resultat från körningarna.
  
9. Ansluta en taggade datauppsättning och en av de [utbildningsmoduler](module-reference.md):
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](train-model.md) modulen.
  

10. Kör experimentet.

## <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna vet utbildning, högerklickar du på utdata från [Träningsmodell](./train-model.md), och välj **visualisera**.

+ Om du vill använda tränade modeller för att göra förutsägelser, ansluta den tränade modellen till den [Poängmodell](score-model.md) modulen.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 