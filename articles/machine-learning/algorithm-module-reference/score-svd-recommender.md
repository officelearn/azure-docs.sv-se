---
title: 'Betyg SVD Recommender: Modul referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Betyg SVD Recommender i Azure Machine Learning för att få rekommendationsprognoser för en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455986"
---
# <a name="score-svd-recommender"></a>Poäng för SVD-rekommenderare

I den här artikeln beskrivs hur du använder modulen Betyg SVD Recommender i Azure Machine Learning designer (förhandsversion). Använd den här modulen för att skapa förutsägelser med hjälp av en tränad rekommendationsmodell baserad på SVD-algoritmen (Single Value Decomposition).

SVD-rekommenderaren kan generera två olika typer av förutsägelser:

- [Förutsäga klassificeringar för en viss användare och ett visst objekt](#prediction-of-ratings)
- [Rekommendera objekt till en användare](#recommendations-for-users)

När du skapar den andra typen av förutsägelser kan du arbeta i något av följande lägen:

- **Produktionsläge** tar hänsyn till alla användare eller artiklar. Det används vanligtvis i en webbtjänst.

  Du kan skapa poäng för nya användare, inte bara användare sett under utbildningen. Mer information finns i de [tekniska anteckningarna](#technical-notes). 

- **Utvärderingsläget** fungerar på en reducerad uppsättning användare eller objekt som kan utvärderas. Det används vanligtvis under pipeline-åtgärder.

Mer information om SVD-rekommendationsalgoritmen finns i uppsatsen [Matrix factorization techniques for recommender systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Konfigurera Poäng SVD Recommender

Den här modulen stöder två typer av förutsägelser, var och en med olika krav. 

###  <a name="prediction-of-ratings"></a>Förutsägelse av betyg

När du förutsäger klassificeringar beräknar modellen hur en användare kommer att reagera på ett visst objekt, med tanke på träningsdata. Indata för bedömning måste ge både en användare och objektet att betygsätta.

1. Lägg till en tränad rekommendationsmodell i pipelinen och anslut den till **utbildad SVD-rekommenderare**. Du måste skapa modellen med modulen [Tåg-SVD Recommender.](train-SVD-recommender.md)

2. För **Rekommenderae förutsägelse typ**väljer du Betyg **Förutsägelse**. Inga andra parametrar krävs.

3. Lägg till de data som du vill göra förutsägelser för och anslut dem till **Datauppsättningen för att göra mål**.

   För att modellen ska kunna förutsäga klassificeringar måste indatauppsättningen innehålla par för användarobjekt.

   Datauppsättningen kan innehålla en valfri tredje kolumn med klassificeringar för användarobjektparet i den första och andra kolumnen. Men den tredje kolumnen kommer att ignoreras under förutsägelse.

4. Skicka pipelinen.

### <a name="results-for-rating-predictions"></a>Resultat för klassificeringsprognoser 

Utdatauppsättningen innehåller tre kolumner: användare, objekt och den förväntade klassificeringen för varje indataanvändare och objekt.

###  <a name="recommendations-for-users"></a>Rekommendationer för användare 

Om du vill rekommendera objekt för användare anger du en lista över användare och objekt som indata. Från dessa data använder modellen sin kunskap om befintliga objekt och användare för att generera en lista över objekt med troligt överklagande till varje användare. Du kan anpassa antalet returnerade rekommendationer. Och du kan ange ett tröskelvärde för antalet tidigare rekommendationer som krävs för att generera en rekommendation.

1. Lägg till en tränad rekommendationsmodell i pipelinen och anslut den till **utbildad SVD-rekommenderare**.  Du måste skapa modellen med modulen [Tåg-SVD Recommender.](train-svd-recommender.md)

2. Om du vill rekommendera objekt för en lista över användare anger du **rekommendationsförteckningstypen Recommender** på **Artikelrekommendation**.

3. För **Rekommenderad artikelval**anger du om du använder bedömningsmodulen i produktion eller för modellutvärdering. Välj ett av följande värden:

    - **Från Alla artiklar**: Välj det här alternativet om du konfigurerar en pipeline som ska användas i en webbtjänst eller i produktion.  Det här alternativet aktiverar *produktionsläge*. Modulen ger rekommendationer från alla objekt som ses under utbildningen.

    - **Från betygsatta objekt (för modellutvärdering):** Välj det här alternativet om du utvecklar eller testar en modell. Det här alternativet aktiverar *utvärderingsläge*. Modulen ger endast rekommendationer från de objekt i indatauppsättningen som har klassificerats.
    
    - **Från Oklassade objekt (för att föreslå nya objekt för användare):** Välj det här alternativet om du vill att modulen endast ska ge rekommendationer från de objekt i utbildningsdatauppsättningen som inte har klassificerats. 

4. Lägg till den datauppsättning som du vill göra förutsägelser för och anslut den till **Datauppsättningen för att göra mål**.

    - För **Från alla objekt**ska indatauppsättningen bestå av en kolumn. Den innehåller identifierare för användare som att lämna rekommendationer för.

      Datauppsättningen kan innehålla ytterligare två kolumner med objektidentifierare och klassificeringar, men dessa två kolumner ignoreras. 

    - För **Från betygsatta objekt (för modellutvärdering)** ska indatauppsättningen bestå av användarartikelpar. Den första kolumnen ska innehålla användaridentifieraren. Den andra kolumnen ska innehålla motsvarande artikelidentifierare.

      Datauppsättningen kan innehålla en tredje kolumn med användarobjektklassificeringar, men den här kolumnen ignoreras.

    - För **Från ej betygsatta objekt (för att föreslå nya objekt för användare)** ska indatauppsättningen bestå av användarobjektpar. Den första kolumnen ska innehålla användaridentifieraren. Den andra kolumnen ska innehålla motsvarande artikelidentifierare.

     Datauppsättningen kan innehålla en tredje kolumn med användarobjektklassificeringar, men den här kolumnen ignoreras.

5. **Maximalt antal artiklar som ska rekommenderas för en användare**: Ange antalet artiklar som ska returneras för varje användare. Som standard rekommenderar modulen fem objekt.

6. **Minsta storlek på rekommendationspoolen per användare**: Ange ett värde som anger hur många tidigare rekommendationer som krävs. Som standard är den här parametern inställd på 2, vilket innebär att minst två andra användare har rekommenderat objektet.

   Använd det här alternativet endast om du gör mål i utvärderingsläge. Alternativet är inte tillgängligt om du väljer **Från alla objekt** eller Från ej **betygsatta objekt (om du vill föreslå nya objekt för användare).**

7.  För **Från Oklassade objekt (för att föreslå nya objekt för användare)** använder du den tredje indataporten med namnet **Training Data**för att ta bort objekt som redan har klassificerats från förutsägelseresultaten.

    Om du vill använda det här filtret ansluter du den ursprungliga träningsdatauppsättningen till indataporten.

8. Skicka pipelinen.

### <a name="results-of-item-recommendation"></a>Resultat av artikelrekommendation

Den poängsatta datauppsättningen som returneras av Score SVD Recommender listar de rekommenderade objekten för varje användare:

- Den första kolumnen innehåller användaridentifierare.
- Ett antal ytterligare kolumner genereras, beroende på det värde som du anger för **Maximalt antal objekt att rekommendera till en användare**. Varje kolumn innehåller ett rekommenderat objekt (efter identifierare). Rekommendationerna ordnas efter tillhörighet för användare och objekt. Objektet med högst tillhörighet förs in i **kolumnArtikel 1**.

> [!WARNING]
> Du kan inte utvärdera den här gjorda datauppsättningen med hjälp av modulen [Utvärdera Recommender.](evaluate-recommender.md)


##  <a name="technical-notes"></a>Tekniska anmärkningar

Om du har en pipeline med SVD-rekommenderaren och flyttar modellen till produktion bör du vara medveten om att det finns viktiga skillnader mellan att använda den som rekommenderar i utvärderingsläge och att använda den i produktionsläge.

Utvärdering, per definition, kräver förutsägelser som kan verifieras mot *marken sanningen* i en testuppsättning. När du utvärderar den som rekommenderar måste den bara förutsäga objekt som har klassificerats i testuppsättningen. Detta begränsar de möjliga värden som förutses.

När du operationaliserar modellen ändrar du vanligtvis förutsägelseläget för att ge rekommendationer baserat på alla möjliga objekt, för att få de bästa förutsägelserna. För många av dessa förutsägelser, det finns ingen motsvarande grund sanning. Så noggrannheten i rekommendationen kan inte verifieras på samma sätt som under rörledningsåtgärder.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
