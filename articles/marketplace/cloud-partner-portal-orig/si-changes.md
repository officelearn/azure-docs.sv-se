---
title: Säljaren insikter viktig information
description: Innehåller information om ändringar i funktionen försäljning insikter.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943115"
---
# <a name="seller-insights-release-notes"></a>Säljaren insikter viktig information 

(Utgivningsdatum: Den 1 mars 2019)

Den här artikeln innehåller information om ändringar i funktionen försäljning insikter i den [Cloud Partner Portal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Versionen viktig information om den 1 mars 2019

* *Kunden Trend* läggs till sammanfattning
* *De främsta fem kunder* återspeglar alla Azure-prenumerationer som en kund har på Sammanfattning
* *Normalized informationen om Användningstrenden & Active order Trend* på Sammanfattning under flyttas *månatliga order i korthet*
* *Payout Licensavstämning* uppdateras
* *De främsta fem kunder* på Payout återspeglar alla Azure-prenumerationer som en kund har
* *Användningsrapporten* uppdateras med kund-ID
* *Kunden tjänstgöringsperiod i* visar alla Azure-prenumerationer som en kund har på order & användning


(Utgivningsdatum: 28 juli 2018)

## <a name="release-highlights-for-july-28-2018"></a>Versionen viktig information om den 28 juli 2018


-   *Beräknad priserna* ge en översikt över tillägg med valuta konvertering effekter.
-   *Prognostiserat payouts* ger en tidigare överblick över potentiella payouts.
-  *Användning referens-ID: n* får payouts dataåtergivningen mellan kundens användning och order
-   *Användning enligt en daglig kornighet* ger mer detaljrikedom och bättre insikt i kundens användning.


### <a name="changes-to-data-structure-and-taxonomy"></a>Ändringar av datastruktur och taxonomi

I följande tabell visas de mått som har lagts till eller ändrats avsevärt med den här versionen. 

| **Ny Term**                   |    **Definition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Pris (kopia)                     | Priset för en enhet för användning för en viss SKU (i kundens valuta).       |
| Beräknad avgift om utökad (kopia) | Beräknad utökad kostnad för antalet enheter för användning för en viss SKU: N (i kundens valuta). Det här värdet är kanske inte exakt på grund av avrundning eller trunkering av fel.   |
| Utgivaren valuta (PC)        | Valuta som rekommenderas av utgivaren för payout.                               |
| Beräknat pris (PC)           | Beräknat pris för en enhet för användning för en viss SKU som baseras på valutaväxling konvertering på datum-användning beräknas (i utgivarens valuta). Det här värdet är kanske inte exakt på grund av avrundning eller trunkering av fel.   |
| Beräknad avgift om utökad (PC) | Beräknad utökad kostnad för antalet enheter för användning för en viss SKU som baseras på valutaväxling konvertering på datum-användning beräknas (i utgivarens valuta). Det här värdet är kanske inte exakt på grund av avrundning eller trunkering av fel. |
| Beräknad Payout (PC)          | Beräknad betalning för antalet enheter för användning för en viss SKU utifrån valutaväxling konvertering datumet användningen beräknas (i utgivarens valuta). Det här värdet är kanske inte exakt på grund av avrundning eller trunkering av fel.   |
| Användning-referens                | Identifierare för en eller flera dagar i kundens användning för en viss SKU som är associerade med en post i betalnings-rapporten. |
|  |  |
