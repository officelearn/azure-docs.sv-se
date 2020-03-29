---
title: Funktionsval i teamdatavetenskapsprocessen
description: Förklarar syftet med funktionsvalet och ger exempel på deras roll i dataförbättringsprocessen för maskininlärning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716679"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funktionsval i TDSP (Team Data Science Process)
I den här artikeln beskrivs syftet med funktionsvalet och exempel på dess roll i dataförbättringsprocessen för maskininlärning. Dessa exempel hämtas från Azure Machine Learning Studio.

Teknik och urval av funktioner är en del av Team Data Science Process (TDSP) som beskrivs i artikeln [Vad är Team Data Science Process?](overview.md). Funktionsteknik och val är delar av steget **Utveckla funktioner** i TDSP.

* **funktionsteknik:** Den här processen försöker skapa ytterligare relevanta funktioner från de befintliga råfunktionerna i data och öka prediktiv kraft till inlärningsalgoritmen.
* **funktionsval:** Den här processen väljer den viktigaste delmängden av ursprungliga datafunktioner i ett försök att minska dimensionaliteten i utbildningsproblemet.

Normalt **funktionsteknik** tillämpas först för att generera ytterligare funktioner, och sedan funktionen **urvalssteget** utförs för att eliminera irrelevanta, redundanta eller starkt korrelerade funktioner.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrera funktioner från dina data - funktionsval
Funktionsval kan användas för klassificerings- eller regressionsaktiviteter. Målet är att välja en delmängd av funktionerna från den ursprungliga datauppsättningen som minskar dess dimensioner genom att använda en minimal uppsättning funktioner för att representera den maximala variansen i data. Denna delmängd av funktioner används för att träna modellen. Funktionsval tjänar två huvudsakliga syften.

* För det första ökar funktionsvalet ofta klassificeringsnoggrannheten genom att eliminera irrelevanta, redundanta eller starkt korrelerade funktioner.
* För det andra minskar det antalet funktioner, vilket gör modellutbildningsprocessen effektivare. Effektivitet är viktigt för elever som är dyra att träna, till exempel stöd vektormaskiner.

Även om funktionen urval syftar till att minska antalet funktioner i datauppsättningen som används för att träna modellen, det är inte hänvisas till av termen "dimensionalitet minskning". Funktionsvalsmetoder extraherar en delmängd av de ursprungliga funktionerna i data utan att ändra dem.  Dimensionality reduction metoder använder konstruerade funktioner som kan omvandla de ursprungliga funktionerna och därmed ändra dem. Exempel på dimensionalitetsreduktionsmetoder är Principal Component Analysis, kanonisk korrelationsanalys och singularvärdesdeposition.

En allmänt tillämpad kategori av funktionsvalsmetoder i en övervakad kontext kallas bland annat "filterbaserat funktionsval". Genom att utvärdera korrelationen mellan varje funktion och målattributet tillämpar dessa metoder ett statistiskt mått för att tilldela en poäng till varje funktion. Funktionerna rangordnas sedan efter poängen, som kan användas för att ange tröskelvärdet för att behålla eller eliminera en viss funktion. Exempel på de statistiska mått som används i dessa metoder är personkorrelation, ömsesidig information och Chi-kvadrattestet.

I Azure Machine Learning Studio finns moduler för funktionsval. Som visas i följande bild, dessa moduler inkluderar [filterbaserad funktionsval][filter-based-feature-selection] och [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis].

![Moduler för funktionsval](./media/select-features/feature-Selection.png)

Tänk till exempel på användningen av modulen [Filterbaserad funktionsval.][filter-based-feature-selection] För enkelhetens skull, fortsätt att använda exemplet med textbrytning. Anta att du vill skapa en regressionsmodell efter att en uppsättning 256-funktioner har skapats via modulen [Feature Hashing][feature-hashing] och att svarsvariabeln är "Col1" som innehåller bokrecensionsbetyg från 1 till 5. Genom att ställa in "Feature scoring method" som "Pearson Korrelation", "Target column" som ska vara "Col1" och "Antal önskade funktioner" till 50. Sedan producerar modulen [Filterbaserad funktionsval][filter-based-feature-selection] en datauppsättning som innehåller 50 funktioner tillsammans med målattributet "Col1". Följande bild visar flödet av detta experiment och indataparametrarna:

![Egenskaper för filterbaserad funktionsvalsmodul](./media/select-features/feature-Selection1.png)

Följande bild visar de resulterande datamängderna:

![Resulterande datauppsättning för filterbaserad funktionsvalsmodul](./media/select-features/feature-Selection2.png)

Varje funktion poängsätts baserat på Pearson Korrelation mellan sig själv och målattributet "Col1". Funktionerna med toppbetyg behålls.

Motsvarande poäng för de valda funktionerna visas i följande bild:

![Poäng för filterbaserad funktionsvalsmodul](./media/select-features/feature-Selection3.png)

Genom att använda den här [filterbaserade funktionsvalsmodulen][filter-based-feature-selection] väljs 50 av 256 funktioner eftersom de har de mest korrelerade funktionerna med målvariabeln "Col1", baserat på bedömningsmetoden "Pearson Korrelation".

## <a name="conclusion"></a>Slutsats
Funktionsteknik och funktionsval är två vanliga konstruerade och valda funktioner ökar effektiviteten i den utbildningsprocess som försöker extrahera viktig information som finns i data. De förbättrar också kraften i dessa modeller för att klassificera indata korrekt och förutsäga resultat av intresse mer robust. Funktionsteknik och val kan också kombineras för att göra lärandet mer beräkningsmässigt tractable. Det gör det genom att förbättra och sedan minska antalet funktioner som behövs för att kalibrera eller träna en modell. Matematiskt sett är de funktioner som valts för att träna modellen en minimal uppsättning oberoende variabler som förklarar mönstren i data och sedan förutsäga resultaten framgångsrikt.

Det är inte alltid nödvändigtvis att utföra funktionsteknik eller funktionsval. Om det behövs eller inte beror på vilka data som samlas in, vilken algoritm som valts och syftet med experimentet.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

