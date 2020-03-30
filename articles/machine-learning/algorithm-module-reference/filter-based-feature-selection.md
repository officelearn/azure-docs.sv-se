---
title: 'Filterbaserat funktionsval: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Filterbaserad funktionsval i Azure Machine Learning för att identifiera funktionerna i en datauppsättning med den största prediktiva kraften.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477603"
---
# <a name="filter-based-feature-selection"></a>Filterbaserat funktionsval

I den här artikeln beskrivs hur du använder modulen Filterbaserad funktionsval i Azure Machine Learning designer (förhandsversion). Den här modulen hjälper dig att identifiera kolumnerna i indatauppsättningen som har den största prediktiva kraften. 

I allmänhet avser *funktionsval* processen att tillämpa statistiska tester på indata, med tanke på en angiven utdata. Målet är att avgöra vilka kolumner som är mer prediktiva för utdata. Modulen Filterbaserad funktionsval innehåller flera funktionsvalsalgoritmer att välja mellan. Modulen innehåller korrelationsmetoder som Pearson korrelation och chi-kvadratvärden. 

När du använder modulen Filterbaserad funktionsval anger du en datauppsättning och identifierar kolumnen som innehåller etiketten eller den beroende variabeln. Du anger sedan en enda metod som ska användas för att mäta funktionsbesyrende.

Modulen matar ut en datauppsättning som innehåller de bästa funktionskolumnerna, som rangordnas efter prediktiv effekt. Den matar också ut namnen på funktionerna och deras poäng från det valda måttet.  

## <a name="what-filter-based-feature-selection-is"></a>Vilken filterbaserad funktionsval är  

Den här modulen för funktionsval kallas "filterbaserad" eftersom du använder det valda måttet för att hitta irrelevanta attribut. Du filtrerar sedan bort överflödiga kolumner från modellen. Du väljer ett enda statistiskt mått som passar dina data och modulen beräknar en poäng för varje funktionskolumn. Kolumnerna returneras rangordnade efter deras funktionspoäng. 

Genom att välja rätt funktioner kan du eventuellt förbättra klassificeringens noggrannhet och effektivitet. 

Du använder vanligtvis bara kolumnerna med de bästa poängen för att skapa din prediktiva modell. Kolumner med dåliga funktionsvalspoäng kan lämnas kvar i datauppsättningen och ignoreras när du skapar en modell.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Så här väljer du ett mått på funktionsval

Modulen Filterbaserad funktionsval innehåller en mängd olika mått för att bedöma informationsvärdet i varje kolumn. Det här avsnittet innehåller en allmän beskrivning av varje mått och hur det tillämpas. Du kan hitta ytterligare krav för att använda varje mått i de [tekniska anteckningarna](#technical-notes) och i [instruktionerna](#how-to-configure-filter-based-feature-selection) för att konfigurera varje modul.

-   **Pearson korrelation**  

    Pearsons korrelationsstatistik, eller Pearsons korrelationskoefficient, är `r` också känd i statistiska modeller som värdet. För två variabler returneras ett värde som anger korrelationens styrka.

    Pearsons korrelationskoefficient beräknas genom att ta kovariansen av två variabler och dividera med produkten av deras standardavvikelser. Skaländringar i de två variablerna påverkar inte koefficienten.  

-   **Chi kvadrat**  

    Det tvåvägs chi-kvadrattest är en statistisk metod som mäter hur nära förväntade värden är till faktiska resultat. Metoden förutsätter att variablerna är slumpmässiga och hämtade från ett lämpligt urval av oberoende variabler. Den resulterande chi-kvadrat statistik visar hur långt resultaten är från det förväntade (slumpmässiga) resultatet.  


> [!TIP]
> Om du behöver ett annat alternativ för den anpassade funktionen valmetod, använda [kör R Script](execute-r-script.md) modul. 

## <a name="how-to-configure-filter-based-feature-selection"></a>Konfigurera filterbaserad funktionsval

Du väljer ett standardiserat statistiskt mått. Modulen beräknar korrelationen mellan ett par kolumner: etikettkolumnen och en funktionskolumn.

1.  Lägg till modulen Filterbaserad funktionsval i pipelinen. Du hittar den i kategorin **Funktionsval** i designern.

2. Anslut en indatauppsättning som innehåller minst två kolumner som är potentiella funktioner.  

    Om du vill vara säkra på att en kolumn analyseras och en funktionspoäng genereras använder du modulen [Redigera metadata](edit-metadata.md) för att ange **attributet IsFeature.** 

    > [!IMPORTANT]
    > Kontrollera att de kolumner som du tillhandahåller som indata är potentiella funktioner. En kolumn som innehåller ett enda värde har till exempel inget informationsvärde.
    >
    > Om du vet att vissa kolumner skulle göra dåliga funktioner kan du ta bort dem från kolumnmarkeringen. Du kan också använda modulen [Redigera metadata](edit-metadata.md) för att flagga dem som **kategoriska**. 
3.  För **Funktionsbedömningsmetod**väljer du en av följande etablerade statistiska metoder som ska användas vid beräkning av poäng.  

    | Metod              | Krav                             |
    | ------------------- | ---------------------------------------- |
    | Pearson korrelation | Etiketten kan vara text eller numerisk. Funktionerna måste vara numeriska. |
    Chi kvadrat| Etiketter och funktioner kan vara text eller numeriska. Använd den här metoden för att beräkna funktionsbetens betydelse för två kategoriska kolumner.|

    > [!TIP]
    > Om du ändrar det valda måttet återställs alla andra val. Så se till att ställa in det här alternativet först.
4.  Välj alternativet **Endast Använd på funktionskolumner** om du bara vill generera en poäng för kolumner som tidigare har markerats som funktioner. 

    Om du avmarkerar det här alternativet skapar modulen en poäng för en kolumn som annars uppfyller villkoren, upp till antalet kolumner som anges i **Antal önskade funktioner**.  

5.  För **inriktningskolumn**väljer du **Starta kolumnväljare** för att välja etikettkolumnen antingen efter namn eller index. (Index är enbaserade.)  
    En etikettkolumn krävs för alla metoder som involverar statistisk korrelation. Modulen returnerar ett designtidsfel om du inte väljer någon etikettkolumn eller flera etikettkolumner. 

6.  För **Antal önskade funktioner**anger du antalet funktionskolumner som du vill returnera som ett resultat:  

    - Det minsta antalet funktioner som du kan ange är en, men vi rekommenderar att du ökar det här värdet.  

    - Om det angivna antalet önskade funktioner är större än antalet kolumner i datauppsättningen returneras alla funktioner. Även funktioner med noll poäng returneras.  

    - Om du anger färre resultatkolumner än det finns funktionskolumner rangordnas funktionerna efter fallande poäng. Endast de bästa funktionerna returneras. 

7.  Skicka pipelinen eller välj modulen Filtrera baserat funktionsval och välj sedan **Kör markerad**.


## <a name="results"></a>Resultat

Efter bearbetningen är klar:

+ Om du vill visa en fullständig lista över de analyserade funktionskolumnerna och deras poäng högerklickar du på modulen och väljer **Visualisera**.  

+ Om du vill visa datauppsättningen baserat på dina funktionsvalskriterier högerklickar du på modulen och väljer **Visualisera**. 

Om datauppsättningen innehåller färre kolumner än förväntat kontrollerar du modulinställningarna. Kontrollera också datatyperna för kolumnerna som anges som indata. Om du till exempel anger **Antal önskade funktioner** till 1 innehåller utdatauppsättningen bara två kolumner: etikettkolumnen och den högst rankade funktionskolumnen.


##  <a name="technical-notes"></a>Tekniska anmärkningar  

### <a name="implementation-details"></a>Information om genomförandet

Om du använder Pearson-korrelation på en numerisk funktion och en kategorisk etikett beräknas funktionspoängen på följande sätt:  

1.  För varje nivå i den kategoriska kolumnen beräknar du det villkorliga medelvärdet för numerisk kolumn.  

2.  Korrelera kolumnen med villkorade medel med den numeriska kolumnen.  

### <a name="requirements"></a>Krav  

-   Det går inte att generera en poäng för funktionsval för en kolumn som har angetts som en **etikett-** eller **poängkolumn.**  

-   Om du försöker använda en bedömningsmetod med en kolumn av en datatyp som metoden inte stöder, kommer modulen att skapa ett fel. Eller så tilldelas en nollpoäng till kolumnen.  

-   Om en kolumn innehåller logiska (sanna/falska) värden `True = 1` bearbetas de som och `False = 0`.  

-   En kolumn kan inte vara en funktion om den har angetts som en **etikett** eller ett **poäng .**  

### <a name="how-missing-values-are-handled"></a>Hur saknade värden hanteras  

-   Du kan inte ange någon kolumn som saknas som målkolumn som har alla värden som saknas som en målkolumn.  

-   Om en kolumn innehåller värden som saknas ignorerar modulen dem när den beräknar poängen för kolumnen.  

-   Om en kolumn som anges som en funktionskolumn har alla värden som saknas tilldelar modulen nollpoäng.   


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

