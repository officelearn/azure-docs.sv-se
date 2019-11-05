---
title: 'Poängen i SVD-rekommendation: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder den från SVD-Rekommenderadere-modulen i Azure Machine Learning service till Poäng rekommendations förutsägelser för en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517922"
---
# <a name="score-svd-recommender"></a>Poäng för SVD-rekommendation

I den här artikeln beskrivs hur du använder den **SVD-rekommenderade** modulen i Azure Machine Learning designer (för hands version). Använd den här modulen för att skapa förutsägelser med en utbildad rekommendations modell som baseras på den SVD-algoritmen (Single Value diskomposition).

SVD-rekommendationen kan generera två olika typer av förutsägelser:

- [Förutsäg betyg för en specifik användare och objekt](#predict-ratings)

- [Rekommendera objekt till en specifik användare](#recommend)

När du skapar den andra typen av förutsägelser kan du använda antingen *produktions läge* eller *utvärderings läge*.

- **Produktions läget** tar hänsyn till alla användare eller objekt och används vanligt vis i en webb tjänst.

    Du kan skapa Poäng för nya användare, inte bara användare som ses under utbildningen. Mer information finns i [det här avsnittet](#technical-notes). 

- **Utvärderings läget** fungerar på en reducerad uppsättning användare eller objekt som kan utvärderas och används vanligt vis under pipelineation.

Mer information om SVD-krypteringsalgoritmen finns på bladet Research: [Matrix factorization-tekniker för rekommenderade system](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Konfigurera poängen i SVD-rekommenderat

Den här modulen stöder olika typer av rekommendationer, var och en med olika krav. Klicka på länken för den typ av data som du har och den typ av rekommendation som du vill skapa.

+ [Förutsägelse klassificering](#predict-ratings)
+ [Rekommendera objekt](#recommend)

###  <a name="predict-ratings"></a>Förutsägelse klassificering

När du förutsäger klassificeringarna beräknar modellen hur en viss användare kommer att reagera på ett visst objekt, baserat på tränings data. Det innebär att indata för poängsättning måste ange både en användare och objektet som ska bedömas.

1. Lägg till en utbildad rekommendations modell i din pipeline och Anslut den till **tränad SVD-rekommendation**.  Du måste skapa modellen med hjälp av [träna SVD-rekommenderat](train-SVD-recommender.md).

2. **Rekommendations förutsägelse typ**: Välj **bedömnings förutsägelse**. Inga ytterligare parametrar krävs.

3. Lägg till de data som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

    För att förutsäga klassificeringarna måste indata-datamängden innehålla användar objekts par.

    Data uppsättningen kan innehålla en valfri tredje kolumn med klassificeringar för paret User-Item i de första och andra kolumnerna, men den tredje kolumnen kommer att ignoreras under förutsägelse.

4. Köra en pipeline.

### <a name="results-for-rating-predictions"></a>Resultat för bedömnings förutsägelser 

Data uppsättningen för utdata innehåller tre kolumner som innehåller användaren, objektet och den förväntade klassificeringen för varje indata-användare och objekt.

###  <a name="recommend"></a>Rekommenderade 

För att rekommendera objekt för användare, ange en lista med användare och objekt som inmatade. Från dessa data använder modellen sina kunskaper om befintliga objekt och användare för att skapa en lista med objekt som kan överklaga till varje användare. Du kan anpassa antalet rekommendationer som returneras och ange ett tröskelvärde för antalet tidigare rekommendationer som krävs för att skapa en rekommendation.

1. Lägg till en utbildad rekommendations modell i din pipeline och Anslut den till **tränad SVD-rekommendation**.  Du måste skapa modellen med hjälp av [träna SVD-rekommenderat](train-svd-recommender.md).

2. Om du vill rekommendera objekt för en viss lista med användare ställer du in rekommendations **förutsägelse typ** till **artikel rekommendation**.

3. **Rekommenderat objekt val**: Ange om du använder bedömnings modulen i produktion eller för modell utvärdering genom att välja något av följande värden:

    - **Från alla objekt**: Välj det här alternativet om du konfigurerar en pipeline som ska användas i en webb tjänst eller produktion.  Det här alternativet aktiverar **produktions läge**och modulen gör rekommendationer från alla objekt som ses under utbildningen.

    - **Från beräknade objekt (för modell utvärdering)** : Välj det här alternativet om du utvecklar eller testar en modell. Med det här alternativet aktive ras **utvärderings läge**och modulen gör rekommendationer enbart från de objekt i data uppsättningen för indata som har klassificerats.
    
    - **Från oklassificerade objekt (för att föreslå nya objekt till användare)** : Välj det här alternativet så gör modulen bara rekommendationer från de objekt i den utbildnings data uppsättning som inte har klassificerats. 

4. Lägg till den data uppsättning som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

    - För **från alla objekt**ska indata-datauppsättningen bestå av en kolumn som innehåller de identifierare med användare som rekommendationer ska göras för.

        Data uppsättningen kan innehålla extra två kolumner med objekt identifierare och klassificeringar, men dessa två kolumner ignoreras. 

    - För **från beräknade objekt (för modell utvärdering)** ska indata-datauppsättningen bestå av **användar objekts par**. Den första kolumnen ska innehålla **användar** -ID. Den andra kolumnen ska innehålla motsvarande **objekt** identifierare.

        Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.

    - För **från oklassificerade objekt (för att föreslå nya objekt till användare)** ska indata-datauppsättningen bestå av **användar objekts par**. Den första kolumnen ska innehålla **användar** -ID. Den andra kolumnen ska innehålla motsvarande **objekt** identifierare.

        Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.

5. **Maximalt antal objekt att rekommendera till en användare**: Ange antalet objekt som ska returneras för varje användare. Som standard rekommenderas fem objekt.

6. **Minsta storlek på den rekommenderade poolen per användare**: Ange ett värde som anger hur många tidigare rekommendationer som krävs.  Som standard är den här parametern angiven till 2, vilket innebär att objektet måste ha rekommenderats av minst två andra användare.

    Det här alternativet bör endast användas om du bedömer i utvärderings läge. Alternativet är inte tillgängligt om du väljer **från alla objekt** eller **från oklassificerade objekt (för att föreslå nya objekt till användare)** .

7.  För **från oklassificerade objekt (för att föreslå nya objekt till användare)** använder du den tredje Indataporten, med namnet **tränings data**, för att ta bort objekt som redan har klassificerats från förutsägelse resultatet.

    Om du vill använda det här filtret ansluter du den ursprungliga träning-datauppsättningen till Indataporten.

8. Köra en pipeline.

### <a name="results-of-item-recommendation"></a>Resultat av objekt rekommendation

Den returnerade data uppsättningen som returneras av **poängen SVD-rekommendation** listar de rekommenderade objekten för varje användare.

- Den första kolumnen innehåller användar-ID: n.
- Ett antal ytterligare kolumner genereras, beroende på det värde som du anger för **maximalt antal objekt för att rekommendera en användare**. Varje kolumn innehåller ett rekommenderat objekt (efter identifierare). Rekommendationerna ordnas efter mappning mellan användare och objekt, med objektet med högsta tillhörighet i kolumnen **objekt 1**.

> [!WARNING]
> Den här poängen-datamängden kan inte utvärderas med hjälp av [utvärderingen av utvärderingen](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller svar på några vanliga frågor om att använda rekommendationen för att skapa förutsägelser.  

###  <a name="production-use-of-the-svd-recommender"></a>Produktions användning av SVD-rekommendation

Om du har en pipeline med SVD-rekommendation och flyttar modellen till produktion bör du vara medveten om dessa viktiga skillnader när du använder rekommendationen i utvärderings läge och i produktions läge:

- Utvärdering, efter definition, kräver förutsägelser som kan kontrol leras mot *mark-sanningen* i en test uppsättning. När du utvärderar rekommendationen måste det därför bara förutsäga objekt som har klassificerats i test uppsättningen. Detta begränsar förmodligen de möjliga värden som förutsägs.

    Men när du operationalisera modellen ändrar du normalt förutsägelse läget för att göra rekommendationer baserat på alla möjliga objekt, för att få bästa förutsägelser. För många av dessa förutsägelser finns det ingen motsvarande mark-sanningen, så det går inte att verifiera att rekommendationen är korrekt på samma sätt som vid pipelineation.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
