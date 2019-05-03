---
title: 'Beslutsskog Regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen beslut skog Regression i Azure Machine Learning-tjänsten för att skapa en regressionsmodell baserat på en ensemble för beslutsträd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67016582149824c8deb83b54102190a57bd19383
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028807"
---
# <a name="decision-forest-regression-module"></a>Beslutsskog Regression modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en regressionsmodell baserat på en ensemble för beslutsträd.

När du har konfigurerat modellen, måste du träna modellen med hjälp av en datauppsättning som är märkta och [träna modell](./train-model.md) modulen.  Den tränade modellen kan sedan användas för att göra förutsägelser. 

## <a name="how-it-works"></a>Hur det fungerar

Beslutsträd är icke-parametrisk modeller som utför en sekvens med enkla tester för varje instans kan gå igenom en binär data trädstruktur tills en lövnod (beslut) har uppnåtts.

Beslutsträd har följande fördelar:

- De är effektiva i både beräknings- och minnesanvändning under träning och förutsägelser.

- De kan representera icke-linjära beslut gränser.

- De utför val av integrerade funktioner och klassificering och är motståndskraftiga när det finns störningar funktioner.

Den här regressionsmodellen består av en ensemble för beslutsträd. Varje trädet i en regression beslutsskog matar ut en Gaussisk-distribution som en förutsägelse. En aggregering utförs över ensemble av träd för att hitta en Gaussisk distribution närmast kombinerade fördelningen för alla träd i modellen.

Mer information om teoretisk framework för den här algoritmen och dess implementering finns i den här artikeln: [Beslutet skogar: Ett enhetligt ramverk för klassificering, Regression, densitet uppskattning inloppsrör Learning och delvis övervakad inlärning ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Så här konfigurerar du beslut skog Regression-modellen

1. Lägg till den **beslut skog Regression** modulen till arbetsytan för experimentet. Du kan hitta modulen i gränssnittet under **Maskininlärning**, **initiera modell**, och **Regression**.

2. Öppna egenskaperna för modulen och för **sampla metoden**, välja den metod som används för att skapa enskilda träd.  Du kan välja mellan **Bagging** eller **replikera**.

    - **Bagging**: Kallas även för förpackningskapacitet *bootstrap sammanställa*. Varje trädet i en regression beslutsskog matar ut en Gaussisk distribution via förutsägelse. Aggregeringen är att hitta en Gaussisk vars första två stund matchar stund av blandning av Gaussians anges genom att kombinera alla Gaussians som returneras av enskilda träd.

         Mer information finns i Wikipedia-posten för [Bootstrap sammanställa](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikera**: I replikering tränas varje träd på exakt samma inkommande data. Bestämning av vilka dela predikatet används för varje trädnod är slumpmässig och träd kommer att vara olika.

         Mer information om hur utbildning med den **replikera** finns i [beslut skogar för visuellt innehåll och medicinska bildanalys. Criminisi och J. Shotton. Springer 2013. ](http://research.microsoft.com/projects/decisionforests/).

3. Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.

    - **Enskild Parameter**

      Om du vet hur du vill konfigurera modellen kan ange du en specifik uppsättning värden som argument. Du kanske har lärt dig de här värdena genom experiment eller fått dem som vägledning.



4. För **antalet beslutsträd**, visar det totala antalet beslutsträd för att skapa i ensemble. Du kan eventuellt få bättre täckning genom att skapa mer beslutsträd, men utbildning ökar.

    > [!TIP]
    > Det här värdet styr också antalet träd som visas när visualisera den tränade modellen. Om du vill visa eller skriva ut ett enda träd, kan du ange värdet 1. Detta innebär dock bara en trädet kommer att produceras (trädet med den första uppsättningen parametrar) och inga ytterligare iterationer utförs.

5. För **maximalt djup för beslutsträd**, Skriv ett tal som begränsar det maximala djupet på alla beslutsträdet. Öka djupet i trädet kan öka noggrannhet på millisekunder, dess risk utbildningstid overfitting och ökad.

6. För **antalet slumpmässigt delningar per nod**, anger du antalet delningar ska användas för att skapa varje nod i trädet. En *dela* innebär att funktioner i varje nivå i trädet (node) delas slumpmässigt.

7. För **minsta antal prover per lövnod**, ange det minsta antalet fall som krävs för att skapa en terminal nod (löv-) i ett träd.

     Genom att öka det här värdet kan du öka tröskelvärdet för att skapa nya regler. Med standardvärdet 1 kan till exempel även ett enda fall orsaka en ny regel som ska skapas. Om du ökar värdet till 5, skulle träningsdata behöva innehålla minst fem ärenden som uppfyller villkor som är samma.


9. Ansluta en taggade datauppsättning, välja en enskild etikett-kolumn som innehåller mer än två resultat och ansluta till [Träningsmodell](./train-model.md).

    - Om du ställer in **skapande trainer läge** alternativet att **enda Parameter**, öva med modellen med hjälp av den [träna modell](./train-model.md) modulen.

   

10. Kör experimentet.

### <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill se i trädet som har skapats för varje iteration, högerklicka på utdata från modulen utbildning och välj **visualisera**.

+ Om du vill visa reglerna för varje nod, klicka på varje träd och granska nedåt i delningen.

+ Högerklicka på utdata från modulen utbildning för att spara en ögonblicksbild av den tränade modellen, och välj **Spara som Trained Model**. Den här kopian av modellen uppdateras inte på efterföljande körningar av experimentet. 

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 