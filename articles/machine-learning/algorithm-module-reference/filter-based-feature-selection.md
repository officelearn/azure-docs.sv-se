---
title: 'Filtrera baserat funktions val: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder funktionen filtrera baserat funktions val i Azure Machine Learning-tjänsten för att identifiera funktionerna i en data uppsättning med den största förutsägelse kraften.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517974"
---
# <a name="filter-based-feature-selection"></a>Filtrera baserat funktions val

Den här artikeln beskriver hur du använder den [filterbaserade modulen för funktions val](filter-based-feature-selection.md) i Azure Machine Learning designer (för hands version) för att identifiera kolumnerna i din indata-datauppsättning som har den största förutsägelse kraften. 

I allmänhet avser *funktions val* processen att tillämpa statistiska tester på indata, baserat på en angiven utmatning, för att avgöra vilka kolumner som är mer förutsägbara. [Funktionen filterbaserade funktioner för val](filter-based-feature-selection.md) av funktioner innehåller flera algoritmer för val av funktioner som du kan välja bland, inklusive korrelations metoder som Pearson-korrelation och chi2-värden. 

När du använder den [filterbaserade](filter-based-feature-selection.md) modulen för val av funktioner, anger du en data uppsättning, identifierar kolumnen som innehåller etiketten eller den beroende variabeln och anger sedan en enda metod som ska användas för att mäta funktions prioriteten.

Modulen matar ut en data uppsättning som innehåller de bästa funktions kolumnerna som rankas av förutsägande kraft. Den ger också ut namnen på funktionerna och deras resultat från det valda måttet.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Vad är ett filter baserat funktions val och varför använda det?  

Den här modulen för funktions val kallas "filterbaserade" eftersom du använder det valda måttet för att identifiera irrelevanta attribut och filtrera bort överflödiga kolumner från modellen.  Du väljer ett enda statistiskt mått som passar dina data och modulen beräknar en poäng för varje funktions kolumn. Kolumnerna returneras rangordnade efter deras funktions resultat. 

Genom att välja rätt funktioner kan du eventuellt förbättra klassificeringens exakthet och effektivitet. 

Du använder vanligt vis bara kolumnerna med de bästa poängen för att bygga en förutsägelse modell. Kolumner med dålig funktions val kan bli kvar i data uppsättningen och ignoreras när du skapar en modell.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Hur man väljer mått för funktions val

Det **filterbaserade funktions urvalet** innehåller en mängd mått för att utvärdera värdet i varje kolumn.  Det här avsnittet innehåller en allmän beskrivning av varje mått och hur det tillämpas. Ytterligare krav för att använda varje mått anges i avsnittet [tekniska anteckningar](#technical-notes) och i [anvisningarna](#how-to-configure-filter-based-feature-selection) för att konfigurera varje modul.

-   **Pearson-korrelation**  

     Pearsons korrelations statistik, eller Pearsons korrelations koefficient, är också känd i statistiska modeller som `r` värde. För två variabler returnerar den ett värde som anger styrkan i korrelationen

     Korrelationskoefficienten till Pearson är beräknad genom att ta den kovariansen av två variabler och dividera med produkten av standard avvikelserna. Koefficienten påverkas inte av skalnings ändringar i de två variablerna.  

-   **Chi, kvadrat**  

     Det tvåvägs chi2-kvadrat-testet är en statistisk metod som mäter hur nära förväntade värden är faktiska resultat. Metoden förutsätter att variablerna är slumpmässiga och ritade från ett lämpligt exempel av oberoende variabler. Den resulterande Chi-kvadrat-statistiken anger hur långt resultat som kommer från det förväntade resultatet (slumpmässigt).  


> [!TIP]
> Om du behöver ett annat alternativ för anpassad funktions val metod använder du modulen [Kör R-skript](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Konfigurera filtrering baserat funktions val

Du väljer ett standard statistik mått och modulen beräknar korrelationen mellan ett kolumn par: kolumnen etikett och en funktions kolumn

1.  Lägg till den **filterbaserade modulen för funktions val** i din pipeline. Du hittar den i kategorin **funktions val** i designern.

2. Anslut en indatamängd som innehåller minst två kolumner som är potentiella funktioner.  

    För att säkerställa att en kolumn analyseras och att funktions poängen genereras, använder du modulen [Redigera metadata](edit-metadata.md) för att ange attributet **IsFeature** . 

    > [!IMPORTANT]
    > Se till att de kolumner som du anger som indatamängd är möjliga funktioner. Till exempel har en kolumn som innehåller ett enda värde inget värde för information.
    >
    > Om du vet att det finns kolumner som skulle göra dåliga funktioner kan du ta bort dem från kolumn urvalet. Du kan också använda modulen [Redigera metadata](edit-metadata.md) för att flagga dem som **kategoriska**. 
3.  För **funktions bedömnings metod**väljer du någon av följande etablerade statistiska metoder som ska användas för att beräkna poängen.  

    | Metod              | Krav                             |
    | ------------------- | ---------------------------------------- |
    | Pearson-korrelation | Etiketten kan vara text eller numerisk. Funktioner måste vara numeriska. |
    Chi, kvadrat| Etiketter och funktioner kan vara text eller numeriska. Använd den här metoden för att beräkna funktioner i två kategoriska-kolumner.|

    > [!TIP]
    > Om du ändrar det valda måttet kommer alla andra val att återställas, så se till att du anger det här alternativet först!)
4.  Välj alternativet för att **endast använda funktions kolumner** om du vill generera en poäng för de kolumner som tidigare har marker ATS som funktioner. 

    Om du avmarkerar det här alternativet skapar modulen en poäng för en kolumn som på annat sätt uppfyller villkoren, upp till antalet kolumner som anges i **antal önskade funktioner**.  

5.  För **kolumnen mål**klickar du på **Starta kolumn väljaren** för att välja kolumnen etikett antingen efter namn eller efter index (index är en-baserad).  

     En etikett kolumn krävs för alla metoder som inbegriper statistisk korrelation. Modulen returnerar ett design tids fel om du väljer ingen etikett kolumn eller flera etikett kolumner. 

6.  För **antal önskade funktioner**anger du det antal funktions kolumner som du vill ska returneras som ett resultat.  

     - Det minsta antalet funktioner som du kan ange är 1, men vi rekommenderar att du ökar det här värdet.  

     - Om det angivna antalet önskade funktioner är större än antalet kolumner i data uppsättningen returneras alla funktioner, även de med noll poäng.  

    - Om du anger färre resultat kolumner än det finns funktions kolumner rangordnas funktionerna efter fallande Poäng och endast de översta funktionerna returneras. 

7.  Kör pipelinen eller Välj modulen [filtrera baserat funktions val](filter-based-feature-selection.md) och klicka sedan på **Kör vald**.


## <a name="results"></a>Resultat

När bearbetningen är klar:

+ Om du vill se en fullständig lista över funktions kolumnerna som analyserades och deras resultat högerklickar du på modulen, väljer **funktioner**och klickar på **visualisera**.  

+ Om du vill visa den data uppsättning som genereras baserat på ditt val av funktions villkor högerklickar du på modulen, väljer **data uppsättning**och klickar på **visualisera**. 

Om data uppsättningen innehåller färre kolumner än vad du förväntade dig, kontrollerar du inställningarna för modulen och data typerna för kolumnerna som angetts som indata. Om du till exempel anger **antalet önskade funktioner** till 1 innehåller den utgående data uppsättningen bara två kolumner: kolumnen etikett och den mest rankade funktions kolumnen.


##  <a name="technical-notes"></a>Tekniska anteckningar  

### <a name="implementation-details"></a>Implementerings information

Om du använder Pearson-korrelation på en numerisk funktion och en kategoriska-etikett, beräknas funktions poängen på följande sätt:  

1.  Beräkna det villkorliga medelvärdet för en numerisk kolumn för varje nivå i kolumnen kategoriska.  

2.  Korrelera kolumnen med villkorliga medelvärden med den numeriska kolumnen.  

### <a name="requirements"></a>Krav  

-   Det går inte att generera en funktions markerings Poäng för en kolumn som har angetts som en **etikett** eller som en **resultat** kolumn.  

-   Om du försöker använda en bedömnings metod med en kolumn av en datatyp som inte stöds av-metoden, kommer antingen modulen att utlösa ett fel eller så tilldelas noll poäng till kolumnen.  

-   Om en kolumn innehåller logiska värden (sant/falskt) bearbetas de som sant = 1 och falskt = 0.  

-   En kolumn kan inte vara en funktion om den har angetts som en **etikett** eller **Poäng**.  

### <a name="how-missing-values-are-handled"></a>Hur värden som saknas hanteras  

-   Det går inte att ange som mål kolumn (etikett) kolumn som innehåller alla värden som saknas.  

-   Om en kolumn innehåller saknade värden ignoreras de när du beräknar poängen för kolumnen.  

-   Om en kolumn som har angetts som en funktions kolumn har alla värden som saknas tilldelas noll poäng noll.   


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 

