---
title: Val av funktioner i Team Data Science Process
description: Förklarar ändamålet med val av funktioner och ger exempel på deras roll i hur data förbättring av machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716679"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funktionsval i TDSP (Team Data Science Process)
Den här artikeln förklarar för val av funktioner och innehåller exempel på dess roll i processen för data förbättring av machine learning. Följande exempel hämtas från Azure Machine Learning Studio.

Teknikerna och valet av funktioner är en del av TDSP (Team data science process) som beskrivs i artikeln [Vad är team data science-processen?](overview.md). Funktions teknik och urval är delar av steget **utveckla funktioner** i TDSP.

* **funktions teknik**: den här processen försöker skapa ytterligare relevanta funktioner från befintliga RAW-funktioner i data och öka förutsägande kraft för inlärningen.
* **Val av funktion**: den här processen väljer en nyckel del av de ursprungliga data funktionerna i ett försök att minska inlärnings problemets dimensionalitet.

Normalt **används funktionerna först** för att generera ytterligare funktioner och sedan utförs **funktions urvalet** för att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrera funktioner från dina data, val av funktioner
Funktions val kan användas för klassificerings-eller Regressions uppgifter. Målet är att välja en delmängd av funktionerna i den ursprungliga datauppsättningen som minska dess storlek med hjälp av en minimal uppsättning funktioner som representerar längsta avvikelse i data. Den här delmängd av funktionerna för att träna modellen. Val av funktioner har två huvudsakliga syften.

* Först, val av funktioner ökar ofta klassificering noggrannhet genom att ta bort irrelevanta, redundanta eller mycket korrelerade funktioner.
* Dessutom minskas hur många funktioner, vilket gör träningsprocess modell effektivare. Effektiviteten är viktigt för att lära sig som är dyra att träna, till exempel för att stödja Vector-datorer.

Även om Funktionsurval avser att minska antalet funktioner i datauppsättningen som används för att träna modellen, är det inte refereras till av termen ”minskar dimensionalitet”. Funktionen av klientautentiseringskapaciteten extrahera en delmängd av ursprungliga funktioner i data utan att ändra dem.  Dimensionalitet minskning metoder använder bakåtkompilerade funktioner som kan omvandla de ursprungliga funktionerna och därmed ändra dem. Exempel på dimensionalitet minskning metoder är huvudnamn komponenten analys, canonical korrelation analys och rapportanvändare värdet uppdelning.

Bland annat kallas en mycket tillämpade kategori av funktionen val av metoder i ett övervakat sammanhang ”val av filter-baserade funktioner”. Genom att utvärdera sambandet mellan varje funktion och Målattributet, gäller de här metoderna ett statistiska mått om du vill tilldela en poäng varje funktion. Funktionerna är sedan rankade efter poäng, som kan användas för att ange ett tröskelvärde för att behålla eller ta bort en specifik funktion. Exempel på statistiska åtgärder som används i de här metoderna är Person korrelation ömsesidig information och Chi kvadraten testet.

Det finns moduler som angetts för val av funktioner i Azure Machine Learning Studio. Som du ser i följande bild inkluderar dessa moduler [filtrerings-baserade funktions val][filter-based-feature-selection] och [analys av Fisher-discriminant][fisher-linear-discriminant-analysis].

![Funktionen val av moduler](./media/select-features/feature-Selection.png)

Överväg till exempel användningen av modulen för [filtrerings-baserade funktions val][filter-based-feature-selection] . För enkelhetens skull fortsätta att använda textexempel för datautvinning. Anta att du vill skapa en Regressions modell efter att en uppsättning 256 funktioner har skapats via modulen för [funktions-hash][feature-hashing] och att variabeln respons är "Col1" som innehåller gransknings klassificeringen från 1 till 5. Genom att ange ”funktionen bedömnings metoden” ska vara ”Pearson korrelation”, ”målkolumnen” att vara ”Kol1” och ”antal önskade funktioner” till mellan 50. Sedan skapar modulen [filterbaserade funktions val][filter-based-feature-selection] en data uppsättning som innehåller 50-funktioner tillsammans med målattributet "Col1". Följande bild visar flödet av det här experimentet och indataparametrarna som:

![Filterbaserade egenskaper för modulen för funktions val](./media/select-features/feature-Selection1.png)

Följande bild visar de resulterande datauppsättningarna:

![Resultatet för Filter baserat Funktionsurval modul](./media/select-features/feature-Selection2.png)

Varje funktion beräknas baserat på Pearson sambandet mellan sig och Målattributet ”Kol1”. Funktioner med översta poäng hålls.

Motsvarande poäng för de valda funktionerna visas i följande bild:

![Poäng för Filter baserat Funktionsurval modul](./media/select-features/feature-Selection3.png)

Genom att använda den här [filterbaserade modulen för funktions val][filter-based-feature-selection] är 50 av 256 funktioner markerade eftersom de har de mest korrelerade funktionerna med Target-variabeln "Col1", baserat på bedömnings metoden "Pearson korrelation".

## <a name="conclusion"></a>Sammanfattning
Funktions teknik och funktions val är två vanliga och valda funktioner som ökar effektiviteten i inlärnings processen som försöker extrahera nyckelinformation som finns i data. De kan även förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga resultat av intresse mer kraftigare. Funktioner och egenskapsval kan också kombinera för att göra utbildningsresurser mer beräkningsmässigt tractable. Detta sker genom bättre och minskar antalet funktioner som behövs för att kalibrera eller tränar en modell. De funktioner som valts för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som förklarar mönster i data och förutsäga resultat har.

Det är inte alltid nödvändigtvis för val av teknik eller funktion av funktioner. Om det behövs eller inte beror på de data som samlas in, den algoritm som valts och syftet med experimentet.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

