---
title: 'Vektordatormaskin i två klasser: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen **Tvåklasssupport vector machine** i Azure Machine Learning för att skapa en modell som baseras på stödvektornsmaskinsalgoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455816"
---
# <a name="two-class-support-vector-machine-module"></a>Vektormaskinsmodul i två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en modell som baseras på stödvektormaskinalgoritmen. 

Stödvektormaskiner (SVMs) är en väl underbyggd klass av övervakade inlärningsmetoder. Detta genomförande lämpar sig för förutsägelse av två möjliga resultat, baserat på antingen kontinuerliga eller kategoriska variabler.

När du har definierat modellparametrarna tränar du modellen med hjälp av utbildningsmodulerna och tillhandahåller en *taggad datauppsättning* som innehåller en etikett eller resultatkolumn.

## <a name="about-support-vector-machines"></a>Om stöd vektordatorer

Supportvektordatorer är bland de tidigaste maskininlärningsalgoritmerna, och SVM-modeller har använts i många program, från informationshämtning till text- och bildklassificering. SVMs kan användas för både klassificerings- och regressionsaktiviteter.

Den här SVM-modellen är en övervakad inlärningsmodell som kräver märkta data. I utbildningsprocessen analyserar algoritmen indata och känner igen mönster i ett flerdimensionellt funktionsutrymme som kallas *hyperplanet*.  Alla indataexempel representeras som punkter i det här utrymmet och mappas till utdatakategorier på ett sådant sätt att kategorierna delas av ett så brett och tydligt mellanrum som möjligt.

För förutsägelse tilldelar SVM-algoritmen nya exempel i den ena eller andra kategorin och mappar dem till samma utrymme. 

## <a name="how-to-configure"></a>Konfigurerar du 

För den här modelltypen rekommenderar vi att du normaliserar datauppsättningen innan du använder den för att träna klassificeraren.
  
1.  Lägg till modulen Vector Machine med **två klasser** i pipelinen.  
  
2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.  

    -   **Parameterintervall:** Om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Du anger ett visst värdeintervall och tränaren itererar över flera kombinationer av inställningarna för att bestämma vilken kombination av värden som ger bäst resultat.

3.  För **Antal iterationer**skriver du ett tal som anger antalet iterationer som används när modellen skapas.  
  
     Den här parametern kan användas för att styra avvägningen mellan träningshastighet och noggrannhet.  
  
4.  För **Lambda**skriver du ett värde som ska användas som vikt för L1-legalisering.  
  
     Den här legaliseringskoefficienten kan användas för att justera modellen. Större värden straffar mer komplexa modeller.  
  
5.  Välj alternativet **Normalisera funktioner**om du vill normalisera funktioner före träning.
  
     Om du tillämpar normalisering, före utbildning, är datapunkter centrerade vid medelvärdet och skalas för att ha en enhet av standardavvikelse.
  
6.  Välj alternativet **Project till enhetsfären**för att normalisera koefficienter.
  
     Att projicera värden till enhetsutrymme innebär att datapunkterna centrerats vid 0 före träningen och skalas för att ha en enhet med standardavvikelse.
  
7.  I **Slumptalsutsäde**skriver du ett heltalsvärde som ska användas som ett frö om du vill säkerställa reproducerbarhet över körningar.  Annars används ett systemklockvärde som ett frö, vilket kan resultera i lite olika resultat över körningar.
  
9. Anslut en märkt datauppsättning och en av [utbildningsmodulerna:](module-reference.md)
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](train-model.md)
  
10. Skicka pipelinen.

## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för bedömning lägger du till modul **poängmodell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 