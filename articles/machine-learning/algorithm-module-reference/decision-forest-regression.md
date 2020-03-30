---
title: 'Beslut Forest Regression: Modul Referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Decision Forest Regression i Azure Machine Learning för att skapa en regressionsmodell baserat på en ensemble av beslutsträd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456547"
---
# <a name="decision-forest-regression-module"></a>Beslut Forest Regression modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en regressionsmodell baserat på en ensemble av beslutsträd.

När du har konfigurerat modellen måste du träna modellen med hjälp av en märkt datauppsättning och modulen [Tågmodell.](./train-model.md) Den tränade modellen kan sedan användas för att göra förutsägelser. 

## <a name="how-it-works"></a>Hur det fungerar

Beslutsträd är icke-parametriska modeller som utför en sekvens av enkla tester för varje instans, genom att korsa en binär träddatastruktur tills en lövnod (beslut) har nåtts.

Beslutsträd har dessa fördelar:

- De är effektiva i både beräkning och minnesanvändning under träning och förutsägelse.

- De kan representera icke-linjära beslutsgränser.

- De utför integrerat funktionsval och klassificering och är motståndskraftiga i närvaro av bullriga funktioner.

Denna regressionsmodell består av en ensemble av beslutsträd. Varje träd i en regressionsbeslutskog matar ut en gaussisk fördelning som en förutsägelse. En aggregering utförs över ensemblen av träd för att hitta en gaussisk fördelning närmast den kombinerade fördelningen för alla träd i modellen.

För mer information om det teoretiska ramverket för denna algoritm och dess genomförande, se den här artikeln: [Beslut Skogar: En enhetlig ram för klassificering, regression, densitet uppskattning, mångfaldigt lärande och semi-övervakad lärande](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Konfigurera beslutsskogsregressionsmodell

1. Lägg till modulen **Beslutsskogsregression** i pipelinen. Du hittar modulen i designern under **Machine Learning**, **Initialize Model**och **Regression**.

2. Öppna modulegenskaperna och för **omsamplingsmetod**väljer du den metod som används för att skapa de enskilda träden.  Du kan välja mellan **Uppsamlare** eller **Replikera**.

    - **Uppsamlare:** Uppsamlare kallas också *bootstrap aggregating*. Varje träd i en regressionsbeslutskog matar ut en Gaussisk fördelning i form av förutsägelse. Aggregeringen är att hitta en Gaussian vars första två ögonblick matchar ögonblicken i blandningen av Gaussian distributioner ges genom att kombinera alla distributioner tillbaka av enskilda träd.

         Mer information finns i Wikipedia-posten för [Bootstrap-samling](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikera**: I replikering tränas varje träd på exakt samma indata. Fastställandet av vilken delad predikat används för varje trädnod förblir slumpmässigt och träden kommer att vara olika.

         Mer information om utbildningsprocessen med alternativet **Replikera** finns i [Beslutsskogar för datorseende och medicinsk bildanalys. Criminisi och J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**

    - **En parameter**

      Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument. Du kan ha lärt dig dessa värden genom att experimentera eller tagit emot dem som vägledning.

    - **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat. 



4. För **Antal beslutsträd**anger du det totala antalet beslutsträd att skapa i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden kommer att öka.

    > [!TIP]
    > Det här värdet styr också antalet träd som visas när den tränade modellen visualiseras. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. Detta innebär dock att endast ett träd kommer att produceras (trädet med den ursprungliga uppsättningen parametrar) och inga ytterligare iterationer kommer att utföras.

5. För **Maximalt djup för beslutsträden**skriver du ett tal för att begränsa det maximala djupet för ett beslutsträd. Öka djupet av trädet kan öka precisionen, med risk för viss övermontering och ökad träningstid.

6. För **Antal slumpmässiga delningar per nod**anger du antalet delningar som ska användas när varje nod i trädet skapas. En *delning* innebär att funktioner i varje nivå i trädet (noden) delas upp slumpmässigt.

7. För **Minsta antal prover per bladnod**anger du det minsta antal fall som krävs för att skapa en terminalnod (löv) i ett träd.

     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel med standardvärdet 1 kan även ett enskilt ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste utbildningsdata innehålla minst fem fall som uppfyller samma villkor.


9. Anslut en märkt datauppsättning, välj en kolumn med en etikett som inte innehåller fler än två resultat och anslut till [Train Model](./train-model.md).

    - Om du ställer in alternativet **Skapa trainer mode** till Single **Parameter**tränar du modellen med modulen [Tågmodell.](./train-model.md)

   

10. Skicka pipelinen.

### <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du träningsmodulen och växlar sedan till fliken **Utdata** på den högra panelen. Klicka på ikonen **Registrera modell**.  Du hittar den sparade modellen som en modul i modulträdet. 

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 