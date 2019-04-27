---
title: Val av funktioner i Team Data Science Process
description: Förklarar ändamålet med val av funktioner och ger exempel på deras roll i hur data förbättring av machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a74f2c21746deb16372174d4a769f9abb825a1cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809596"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funktionsval i TDSP (Team Data Science Process)
Den här artikeln förklarar för val av funktioner och innehåller exempel på dess roll i processen för data förbättring av machine learning. Följande exempel hämtas från Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Teknik- och urval funktioner är en del av Team Data Science Process (TDSP) som beskrivs i artikeln [vad är Team Data Science Process?](overview.md). Funktioner och egenskapsval är delar av den **utveckla funktioner** steg i TDSP.

* **egenskapsval**: Den här processen försöker att skapa ytterligare relevanta funktioner från de befintliga raw-funktionerna i data och för att öka förutsägande kraften att Inlärningsalgoritmen.
* **Funktionsval**: Den här processen väljer viktiga delmängd av den ursprungliga datafunktioner i ett försök att minska dimensionaliteten för utbildning-problem.

Normalt **funktionstekniker** tillämpas först för att generera ytterligare funktioner, och sedan den **Funktionsval** steg utförs för att ta bort irrelevanta, redundant eller mycket korrelerade funktioner.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrera funktioner från dina data, val av funktioner
Val av funktioner är en process som används ofta för datauppsättningar för utbildning för förutsägelsemodellering aktiviteter till exempel klassificerings- eller regressionsmodell aktivitet. Målet är att välja en delmängd av funktionerna i den ursprungliga datauppsättningen som minska dess storlek med hjälp av en minimal uppsättning funktioner som representerar längsta avvikelse i data. Den här delmängd av funktionerna för att träna modellen. Val av funktioner har två huvudsakliga syften.

* Först, val av funktioner ökar ofta klassificering noggrannhet genom att ta bort irrelevanta, redundanta eller mycket korrelerade funktioner.
* Dessutom minskas hur många funktioner, vilket gör träningsprocess modell effektivare. Effektivitet är särskilt viktigt för elever som är dyra att träna, till exempel support vector datorer.

Även om Funktionsurval avser att minska antalet funktioner i datauppsättningen som används för att träna modellen, är det inte refereras till av termen ”minskar dimensionalitet”. Funktionen av klientautentiseringskapaciteten extrahera en delmängd av ursprungliga funktioner i data utan att ändra dem.  Dimensionalitet minskning metoder använder bakåtkompilerade funktioner som kan omvandla de ursprungliga funktionerna och därmed ändra dem. Exempel på dimensionalitet minskning metoder är huvudnamn komponenten analys, canonical korrelation analys och rapportanvändare värdet uppdelning.

Bland annat kallas en mycket tillämpade kategori av funktionen val av metoder i ett övervakat sammanhang ”val av filter-baserade funktioner”. Genom att utvärdera sambandet mellan varje funktion och Målattributet, gäller de här metoderna ett statistiska mått om du vill tilldela en poäng varje funktion. Funktionerna är sedan rankade efter poäng, som kan användas för att ange ett tröskelvärde för att behålla eller ta bort en specifik funktion. Exempel på statistiska åtgärder som används i de här metoderna är Person korrelation ömsesidig information och Chi kvadraten testet.

Det finns moduler som angetts för val av funktioner i Azure Machine Learning Studio. I följande bild visas dessa moduler omfattar [Filter-baserade Funktionsurval] [ filter-based-feature-selection] och [Fisher linjär Discriminant analys] [ fisher-linear-discriminant-analysis].

![Funktionen val av moduler](./media/select-features/feature-Selection.png)

Till exempel överväga att använda den [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modulen. För enkelhetens skull fortsätta att använda textexempel för datautvinning. Anta att du vill skapa en regressionsmodell när en uppsättning 256 funktioner har skapats via den [funktions-hashning] [ feature-hashing] modulen och att variabeln response är den ”Kol1” som innehåller boken granska betyg mellan 1 och 5. Genom att ange ”funktionen bedömnings metoden” ska vara ”Pearson korrelation”, ”målkolumnen” att vara ”Kol1” och ”antal önskade funktioner” till mellan 50. Sedan modulen [Filter-baserade Funktionsurval] [ filter-based-feature-selection] producerar en datauppsättning som innehåller 50 funktioner tillsammans med Målattributet ”Kol1”. Följande bild visar flödet av det här experimentet och indataparametrarna som:

![Funktionen markering modulen filteregenskaper](./media/select-features/feature-Selection1.png)

Följande bild visar de resulterande datauppsättningarna:

![Resultatet för Filter baserat Funktionsurval modul](./media/select-features/feature-Selection2.png)

Varje funktion beräknas baserat på Pearson sambandet mellan sig och Målattributet ”Kol1”. Funktioner med översta poäng hålls.

Motsvarande poäng för de valda funktionerna visas i följande bild:

![Poäng för Filter baserat Funktionsurval modul](./media/select-features/feature-Selection3.png)

Genom att använda detta [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modulen, 50 av 256 funktioner är markerade eftersom de har mest korrelerade funktioner med en målvariabel ”Kol1” baserat på bedömnings-metoden ”Pearson korrelation”.

## <a name="conclusion"></a>Sammanfattning
Funktionsframställning och val av funktioner är två vanliga utformat och valda funktioner öka effektiviteten för utbildning-processen som försöker extrahera nyckelinformationen ingå i data. De kan även förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga resultat av intresse mer kraftigare. Funktioner och egenskapsval kan också kombinera för att göra utbildningsresurser mer beräkningsmässigt tractable. Detta sker genom bättre och minskar antalet funktioner som behövs för att kalibrera eller tränar en modell. De funktioner som valts för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som förklarar mönster i data och förutsäga resultat har.

Det är inte alltid nödvändigtvis för val av teknik eller funktion av funktioner. Om det behövs eller inte beror på de data som samlas in, den algoritm som valts och syftet med experimentet.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

