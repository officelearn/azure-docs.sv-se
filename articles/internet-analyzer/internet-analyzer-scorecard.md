---
title: Tolka ditt styrkort | Microsoft-dokument
description: Vanliga frågor och svar för Azure Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512891"
---
# <a name="interpreting-your-scorecard"></a>Tolka ditt styrkort

Fliken Styrkort innehåller de aggregerade och analyserade resultaten från dina tester. Varje test har sina egna styrkort. Styrkort ger snabba och meningsfulla sammanfattningar av mätresultat för att ge datadrivna resultat för dina nätverkskrav. Internet Analyzer tar hand om analysen, så att du kan fokusera på beslutet.

Fliken Styrkort finns på resursmenyn Internet Analyzer. 


## <a name="filters"></a>Filter

* ***Test:*** Välj det test som du vill visa resultat för – varje test har ett eget styrkort. Testdata visas när det finns tillräckligt med data för att slutföra analysen – i de flesta fall bör detta vara inom 24 timmar. 
* ***Tidsperiod & slutdatum:*** Tre styrkort genereras dagligen – varje styrkort återspeglar en annan aggregeringsperiod – 24 timmar före (dag), sju dagar före (vecka) och 30 dagar före (månad). Använd filtret Slutdatum för att välja den sista dagen i den tidsperiod som du vill visa. 
* ***Land:*** För varje land som du har slutanvändare genereras ett styrkort. Det globala filtret innehåller alla slutanvändare.

## <a name="measurement-count"></a>Antal mått

Antalet mätningar påverkar analysens förtroende. Ju högre antal, desto mer exakt resultatet. Minst bör provningarna syfta till minst 100 mätningar per slutpunkt per dag. Om måttantalet är för lågt konfigurerar du JavaScript-klienten så att den körs oftare i ditt program. Mätningen räknas för effektmåtten A och B bör vara mycket lika även om små skillnader förväntas och okej. Vid stora skillnader bör resultaten inte vara betrodda.

## <a name="percentiles"></a>Percentiler

Svarstid, mätt i millisekunder, är ett populärt mått för att mäta hastighet mellan en källa och destination på Internet. Latensdata distribueras normalt inte (dvs. följer inte en "Bell Curve") eftersom det finns en "lång svans" av stora latensvärden som skevar resultat när du använder statistik som det aritmetiska medelvärdet. Som ett alternativ, percentiler ger en "distribution-fri" sätt att analysera data. Som ett exempel sammanfattar medianen, eller den 50:e percentilen, mitten av fördelningen - hälften av värdena är över den och hälften är under den. Ett 75:e percentilvärde innebär att det är större än 75 % av alla värden i fördelningen. Internet Analyzer refererar till percentiler i stenografi som P50, P75 och P95.

Internet Analyzer percentiler är _exempel mått_. Detta står i kontrast till det verkliga _befolkningsmåttet_. Den dagliga verkliga medianfördröjningen för befolkningen mellan studenter vid University of Southern California och Microsoft är till exempel medianvärdet för svarstid för alla begäranden under den dagen. I praktiken är det opraktiskt att mäta värdet av alla förfrågningar, så vi antar att ett någorlunda stort urval är representativt för den verkliga populationen.

För analysändamål är P50 (median) användbart som ett förväntat värde för en latensfördelning. Högre percentiler, till exempel P95, är användbara för att identifiera hur hög latens är i värsta fall. Om du är intresserad av att förstå kundfördröjning i allmänhet är P50 rätt mått att fokusera på. Om du är intresserad av att förstå prestanda för de sämst presterande kunderna, då P95 bör vara i fokus. P75 är en balans mellan dessa två.


## <a name="deltas"></a>Deltan

Ett delta är skillnaden i måttvärden för slutpunkter A och B. Deltas beräknas för att visa fördelen med B över A. Positiva värden anger B presterade bättre än A, medan negativa värden indikerar att B:s prestanda är sämre. Deltan kan vara absoluta (t.ex. 10 millisekunder) eller relativa (5 %).

## <a name="confidence-interval"></a>Konfidensintervall 

Konfidensintervall (CI) är ett värdeintervall som har en sannolikhet att innehålla populationsmåttet, till exempel median, P75 eller medelvärde. Vi följer den gemensamma statistiska konventionen om att använda 95% CI.

För Internet Analyzer är ett smalt konfidensintervall bra eftersom det visar att exempelmåttet sannolikt ligger mycket nära det faktiska populationsmåttet. Ett brett konfidensintervall innebär mindre säkerhet om att vårt exempelmått återspeglar det verkliga populationsmåttet. Det bästa sättet att förbättra CI är att öka antalet mätningar.

## <a name="time-series"></a>Tidsserier 

En tidsserie visar hur ett mått ändras med tiden. På Internet finns det många tidsfaktorer som påverkar prestanda, till exempel perioder med hög belastning, befolkningsskillnader mellan veckodagar och helger och helgdagar.


## <a name="next-steps"></a>Nästa steg

Mer information finns i [översikten över Internet Analyzer](internet-analyzer-overview.md).
