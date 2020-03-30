---
title: Säljaren Insights viktig information
description: Innehåller information om ändringar i funktionen Säljarstatistik.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285409"
---
# <a name="seller-insights-release-notes"></a>Säljaren Insights viktig information 

(Utgivningsdatum: 1 mars 2019)

Den här artikeln innehåller information om ändringar i funktionen Säljarstatistik i [Cloud Partner Portal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Höjdpunkter för 1 mars 2019

* *Kundtrend* tillagd i sammanfattning
* *De fem bästa kunderna* i Sammanfattning återspeglar alla Azure-prenumerationer som en kund har
* *Normaliserad användningstrend & trend för aktiva order* vid sammanfattning flyttas under *månadsorder med ett ögonkast*
* *Rapporten för utbetalningsavstämning* uppdaterad
* *De fem bästa kunderna* på utbetalningen återspeglar alla Azure-prenumerationer som en kund har
* *Användningsrapport* uppdaterad med kund-ID
* *Kundtidsorder* på order & användning återspeglar alla Azure-prenumerationer som en kund har


(Utgivningsdatum: 28 juli 2018)

## <a name="release-highlights-for-july-28-2018"></a>Höjdpunkter för 28 juli 2018


-   *Uppskattade priser* ger en bild av kundavgifter med valutakonverteringskonsekvenser.
-   *Prognostiserade utbetalningar* ger en tidigare bild av potentiella utbetalningar.
-  *Användningsreferensidentifierare* ger dataåtergivning mellan kundanvändning och order med utbetalningar
-   *Användning på en daglig spannmål* ger mer granularitet och bättre insikter i kundanvändning.


### <a name="changes-to-data-structure-and-taxonomy"></a>Ändringar i datastruktur och taxonomi

I följande tabell visas de mått som har lagts till eller ändrats väsentligt med den här versionen. 

| **Ny term**                   |    **Definition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Pris (CC)                     | Pris för en användningsenhet för en viss SKU (i kundens valuta).       |
| Uppskattad utökad laddning (CC) | Uppskattad utökad avgift för antalet enheter av användningsenheter för en viss SKU (i kundens valuta). Det här värdet kanske inte är exakt på grund av avrundnings- eller trunkeringsfel.   |
| Utgivarvalutan (PC)        | Valuta som utgivaren föredrar för utbetalning.                               |
| Uppskattat pris (PC)           | Uppskattat pris för en användningsenhet för en viss SKU baserat på valutakonvertering på datumanvändningen beräknas (i utgivarens valuta). Det här värdet kanske inte är exakt på grund av avrundnings- eller trunkeringsfel.   |
| Uppskattad utökad laddning (PC) | Uppskattad utökad avgift för antalet enheter av användningsenheter för en viss SKU baserat på valutakonvertering på datumanvändningen beräknas (i utgivarens valuta). Det här värdet kanske inte är exakt på grund av avrundnings- eller trunkeringsfel. |
| Beräknad utbetalning (PC)          | Uppskattad betalning för antalet enheter av användningsenheter för en viss SKU baserat på valutakonvertering det datum då användningen beräknas (i utgivarens valuta). Det här värdet kanske inte är exakt på grund av avrundnings- eller trunkeringsfel.   |
| Användningsreferens                | Identifieraren för en eller flera dagar av kundanvändning för en viss SKU som är associerad med en post i utbetalningsrapporten. |
|  |  |
