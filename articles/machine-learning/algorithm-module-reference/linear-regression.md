---
title: 'Linjär regression: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Linjär regression i Azure Machine Learning för att skapa en linjär regressionsmodell för användning i en pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477569"
---
# <a name="linear-regression-module"></a>Linjär regressionsmodul
I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen om du vill skapa en linjär regressionsmodell för användning i en pipeline.  Linjär regression försöker upprätta en linjär relation mellan en eller flera oberoende variabler och ett numeriskt resultat eller en beroende variabel. 

Du använder den här modulen för att definiera en linjär regressionsmetod och sedan träna en modell med hjälp av en märkt datauppsättning. Den tränade modellen kan sedan användas för att göra förutsägelser.

## <a name="about-linear-regression"></a>Om linjär regression

Linjär regression är en vanlig statistisk metod som har antagits i maskininlärning och förbättrats med många nya metoder för montering av linjen och mätfel. I den mest grundläggande bemärkelsen refererar regression till förutsägelse av ett numeriskt mål. Linjär regression är fortfarande ett bra val när du vill ha en enkel modell för en grundläggande prediktiv uppgift. Linjär regression tenderar också att fungera bra på högdimensionella, glesa datamängder som saknar komplexitet.

Azure Machine Learning stöder en mängd olika regressionsmodeller, förutom linjär regression. Termen "regression" kan dock tolkas löst och vissa typer av regression som tillhandahålls i andra verktyg stöds inte.

+ Det klassiska regressionsproblemet innebär en enda oberoende variabel och en beroende variabel. Detta kallas *enkel regression*.  Den här modulen stöder enkel regression.

+ *Flera linjär regression* innebär två eller flera oberoende variabler som bidrar till en enda beroende variabel. Problem där flera indata används för att förutsäga ett enda numeriskt resultat kallas också *multivariat linjär regression*.

    Den **linjära regressionsmodulen** kan lösa dessa problem, liksom de flesta andra regressionsmoduler.

+ *Regression med flera etiketter* är uppgiften att förutsäga flera beroende variabler i en enda modell. I logistisk regression med flera etiketter kan till exempel ett exempel tilldelas flera olika etiketter. (Detta skiljer sig från uppgiften att förutsäga flera nivåer inom en enda klassvariabel.)

    Den här typen av regression stöds inte i Azure Machine Learning. Om du vill förutsäga flera variabler skapar du en separat elev för varje utdata som du vill förutsäga.

I åratal har statistiker utvecklat allt mer avancerade metoder för regression. Detta gäller även för linjär regression. Den här modulen stöder två metoder för att mäta fel och passa regressionslinjen: vanlig minsta kvadratmetod och gradientnedstigning.

- **Gradient descent** är en metod som minimerar mängden fel vid varje steg i modellen utbildningsprocessen. Det finns många variationer på gradient nedstigning och dess optimering för olika inlärningsproblem har studerats utförligt. Om du väljer det här alternativet för **lösningsmetod**kan du ställa in en mängd olika parametrar för att styra stegstorlek, inlärningsfrekvens och så vidare. Det här alternativet stöder också användning av ett integrerat parametervep.

- **Vanliga minsta kvadrater** är en av de vanligaste teknikerna i linjär regression. Minsta kvadrater är till exempel den metod som används i Analysis Toolpak för Microsoft Excel.

    Vanliga minsta kvadrater refererar till förlustfunktionen, som beräknar felet som summan av kvadraten av avståndet från det faktiska värdet till den förväntade linjen, och passar modellen genom att minimera det fyrkantiga felet. Den här metoden förutsätter ett starkt linjärt förhållande mellan indata och den beroende variabeln.

## <a name="configure-linear-regression"></a>Konfigurera linjär regression

Den här modulen stöder två metoder för montering av en regressionsmodell, med olika alternativ:

+ [Montera en regressionsmodell med vanliga minsta rutor](#create-a-regression-model-using-ordinary-least-squares)

    För små datamängder är det bäst att välja vanliga minsta kvadrater. Detta bör ge liknande resultat som Excel.
    
+ [Skapa en regressionsmodell med hjälp av gradientnedstigning online](#create-a-regression-model-using-online-gradient-descent)

    Gradient descent är en bättre förlustfunktion för modeller som är mer komplexa, eller som har för lite träningsdata med tanke på antalet variabler.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Skapa en regressionsmodell med vanliga minsta kvadrater

1. Lägg till modulen **Linjär regressionsmodell** i pipelinen i designern.

    Du hittar den här modulen i kategorin **Maskininlärning.** Expandera **Initiera modell,** expandera **Regression**och dra sedan modulen **Linjär regressionsmodell** till pipelinen.

2. Välj **Vanliga minsta kvadrater**i listrutan **Lösningsmetod** i fönstret **Egenskaper** . Det här alternativet anger beräkningsmetoden som används för att hitta regressionsraden.

3. I **L2-legaliseringsvikt**skriver du det värde som ska användas som vikt för L2-legalisering. Vi rekommenderar att du använder ett värde som inte är noll för att undvika övermontering.

     Mer information om hur legalisering påverkar modellmontering finns i den här artikeln: [L1 och L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Välj alternativet **Inkludera skärningspunktsterm**om du vill visa termen för skärningspunkten.

    Avmarkera det här alternativet om du inte behöver granska regressionsformeln.

5. För **slumptalsfrö**kan du eventuellt skriva ett värde för att dirigera slumptalsgeneratorn som används av modellen.

    Det är användbart att använda ett frövärde om du vill behålla samma resultat i olika körningar av samma pipeline. Annars är standardinställningen att använda ett värde från systemklockan.


7. Lägg till modulen [Tågmodell](./train-model.md) i pipelinen och anslut en märkt datauppsättning.

8. Skicka pipelinen.

### <a name="results-for-ordinary-least-squares-model"></a>Resultat för vanlig minsta kvadratmodell

Efter träningen är klar:


+ Om du vill göra förutsägelser ansluter du den tränade modellen till modulen [Poängmodell,](./score-model.md) tillsammans med en datauppsättning med nya värden. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Skapa en regressionsmodell med hjälp av gradientnedstigning online

1. Lägg till modulen **Linjär regressionsmodell** i pipelinen i designern.

    Du hittar den här modulen i kategorin **Maskininlärning.** Expandera **Initiera modell,** expandera **Regression**och dra modulen **Linjär regressionsmodell** till pipelinen

2. I listrutan **Lösningsmetod** i fönstret **Lösningsmetod** väljer du **Onlinegraderingsneddragning** som beräkningsmetod som används för att hitta regressionsraden.

3. För **Skapa trainer-läge**anger du om du vill träna modellen med en fördefinierad uppsättning parametrar eller om du vill optimera modellen med hjälp av ett parametervep.

    + **Enkel parameter:** Om du vet hur du vill konfigurera det linjära regressionsnätverket kan du ange en specifik uppsättning värden som argument.
    
    + **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.  

   
4. För **inlärningshastighet**anger du den ursprungliga inlärningshastigheten för den stokastiska lutningsnedstigningsoptimeraren.

5. För **Antal utbildningsepoker**skriver du ett värde som anger hur många gånger algoritmen ska iterera genom exempel. För datamängder med ett litet antal exempel bör detta antal vara stort för att nå konvergens.

6. **Normalisera funktioner**: Om du redan har normaliserat de numeriska data som används för att träna modellen kan du avmarkera det här alternativet. Som standard normaliserar modulen alla numeriska indata till ett intervall mellan 0 och 1.

    > [!NOTE]
    > 
    > Kom ihåg att tillämpa samma normaliseringsmetod på nya data som används för bedömning.

7. I **L2-legaliseringsvikt**skriver du det värde som ska användas som vikt för L2-legalisering. Vi rekommenderar att du använder ett värde som inte är noll för att undvika övermontering.

    Mer information om hur legalisering påverkar modellmontering finns i den här artikeln: [L1 och L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Välj alternativet **Minska inlärningsfrekvensen**om du vill att inlärningsfrekvensen ska minska allteftersom iterationer fortskrider.  

10. För **slumptalsfrö**kan du eventuellt skriva ett värde för att dirigera slumptalsgeneratorn som används av modellen. Det är användbart att använda ett frövärde om du vill behålla samma resultat i olika körningar av samma pipeline.


12. Lägg till en märkt datauppsättning och en av utbildningsmodulerna.

    Om du inte använder ett parametervep använder du modulen [Tågmodell.](train-model.md)

13. Skicka pipelinen.

### <a name="results-for-online-gradient-descent"></a>Resultat för gradientnedstigning online

Efter träningen är klar:

+ Om du vill göra förutsägelser ansluter du den tränade modellen till modulen [Poängmodell](./score-model.md) tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 