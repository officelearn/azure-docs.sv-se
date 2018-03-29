---
title: Funktionen val i Team av vetenskapliga data | Microsoft Docs
description: Förklarar ändamålet med val av funktioner och ger exempel på deras roll i förbättring av data för maskininlärning.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: 44159516a1785f5d65357935000eccc42a7458aa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funktionsval i TDSP (Team Data Science Process)
Den här artikeln beskriver för val av funktioner och innehåller exempel på sin roll i förbättring av data för machine learning. Dessa exempel hämtas från Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Tekniker och funktioner som är en del av det Team Data vetenskap processen (TDSP) beskrivs i artikel [vad är Team av vetenskapliga data?](overview.md). Funktionen tekniker och markeringen är delar av den **utveckla funktioner** steg i TDSP.

* **egenskapsval**: den här processen försöker att skapa ytterligare relevanta funktioner från befintliga raw-funktioner i data och för att öka förutsägande strömmen till Inlärningsalgoritmen.
* **funktionen val**: den här processen väljer viktiga delmängd av den ursprungliga datafunktioner i ett försök att minska dimensionaliteten för utbildning-problem.

Normalt **egenskapsval** används först för att generera ytterligare funktioner, och sedan den **funktion markeringen** steg utförs för att ta bort irrelevanta, redundant eller hög korrelerade funktioner.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrera funktioner från dina data - val av funktioner
Val av funktioner är en process som används ofta för utbildning datauppsättningar för förutsägelsemodellering åtgärder som klassificerings- eller regressionsmodell uppgifter. Målet är att välja en delmängd av funktionerna i den ursprungliga datauppsättningen som minskar dess storlek genom att använda en minimal uppsättning funktioner som representerar längsta variationen i data. Den här delmängd av funktionerna används för att träna modellen. Val av funktioner har två huvudsakliga syften.

* Först Funktionsurval ökar ofta klassificering noggrannhet genom att ta bort irrelevanta, redundanta eller hög korrelerade funktioner.
* Andra, det minskar antalet funktioner, vilket gör att modellen utbildning blir mer effektivt. Effektivitet är särskilt viktigt för inlärning är billigare att träna som support vector datorer.

Även om Funktionsurval avser att minska antalet funktioner i den datamängd som används för att träna modellen, refereras det inte till av termen ”dimensionalitet minskning”. Funktionen val metoder extrahera en delmängd av ursprungliga funktioner i data utan att ändra dem.  Metoder för dimensionalitet minskning av bakåtkompilerade funktioner som kan omvandla de ursprungliga funktionerna och därmed ändra dem. Exempel på dimensionalitet minskning metoder är Principal komponenten analys, kanoniska korrelation analys och enkel värdet uppdelning.

Bland annat kallas en allmänt tillämpade kategori av funktionen markeringsmetoder i en övervakad kontext ”filter-baserade Funktionsurval”. Metoderna gäller ett statistiskt mått om du vill tilldela en poäng varje funktion som utvärderar sambandet mellan varje funktion och Målattributet. Funktionerna för rangordnas sedan poäng, som kan användas för att ange ett tröskelvärde för att hålla eller ta bort en specifik funktion. Exempel på statistiska måtten används i dessa metoder är Person korrelation ömsesidig information och Chi kvadraten testet.

Det finns moduler som angetts för val av funktioner i Azure Machine Learning Studio. I följande bild visas dessa moduler inkluderar [Filter-baserade Funktionsurval] [ filter-based-feature-selection] och [Fisher linjär Discriminant analys][fisher-linear-discriminant-analysis].

![Funktionen val exempel](./media/select-features/feature-Selection.png)

Tänk till exempel användning av den [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modul. Av praktiska skäl kan fortsätta att använda textexempel för utvinningsmodellen. Anta att du vill skapa en regressionsmodell när en uppsättning 256 funktioner har skapats via den [funktions-hashning] [ feature-hashing] modulen, och att variabeln svar är den ”Kol1” som innehåller book granska klassificeringar mellan 1 och 5. Genom att ange ”funktion bedömningsprofil metoden” ska vara ”kvadratvärdet korrelation”, ”målkolumnen” ska vara ”Kol1” och ”antalet önskade funktioner” till 50. Sedan modulen [Filter-baserade Funktionsurval] [ filter-based-feature-selection] producerar en datamängd som innehåller 50 funktioner tillsammans med målattribut ”Kol1”. Följande bild visar flödet av experimentet och indataparametrarna:

![Funktionen val exempel](./media/select-features/feature-Selection1.png)

Följande bild visar de resulterande datauppsättningarna:

![Funktionen val exempel](./media/select-features/feature-Selection2.png)

Varje funktion beräknas baserat på kvadratvärdet sambandet mellan sig själv och Målattributet ”Kol1”. Funktioner med högsta poäng behålls.

Motsvarande resultat för de valda funktionerna visas i följande bild:

![Funktionen val exempel](./media/select-features/feature-Selection3.png)

Genom att använda detta [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modulen, 50 av 256 funktioner är markerade eftersom de har mest korrelerade funktioner med en målvariabel ”Kol1” baserat på bedömningsprofil metoden ”kvadratvärdet korrelation”.

## <a name="conclusion"></a>Sammanfattning
Funktionen tekniker och Funktionsurval är två ofta utformad och de valda funktionerna ökar effektiviteten för utbildning-process som försöker hämta viktig information som finns i data. De kan också förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga mer robustly resultat av intresse. Funktionen tekniker och val kan också kombinera om du vill göra learning mer beräkningsmässigt tractable. Den gör detta genom att öka och minska antalet funktioner som behövs för att kalibrera eller tränar en modell. Funktioner som markerats för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som beskriver mönster i data och förutsäga resultat har.

Det är inte alltid nödvändigtvis att utföra Funktionsurval teknik eller funktion. Om det behövs eller inte beroende av insamlade data och algoritmen valda målet för experimentet.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

