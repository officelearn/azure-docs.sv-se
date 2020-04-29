---
title: Viktig information om säljar insikter
description: Innehåller information om ändringar i säljar insikts funktionen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285409"
---
# <a name="seller-insights-release-notes"></a>Viktig information om säljar insikter 

(Lanserings datum: 1 mars 2019)

Den här artikeln innehåller information om ändringar i säljar insikter-funktionen i [Cloud Partner Portal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Versions markeringar för 1 mars 2019

* *Kund trend* har lagts till i Sammanfattning
* *De fem främsta kunderna* om Sammanfattning återspeglar alla Azure-prenumerationer som kunden har
* *Normaliserad användnings trend & aktiva beställningar trender* i sammandrag som flyttats under *månatliga beställningar snabbt*
* *Rapporten om avbetalnings avstämning* har uppdaterats
* *De fem främsta kunderna* om utbetalning återspeglar alla Azure-prenumerationer som kunden har
* *Användnings rapport* uppdaterad med kund-ID
* *Kund besittning* på order & användning visar alla Azure-prenumerationer som kunden har


(Lanserings datum: 28 juli 2018)

## <a name="release-highlights-for-july-28-2018"></a>Versions markeringar den 28 juli 2018


-   *Uppskattade priser* ger en översikt över kund avgifter med valuta omvandlings konsekvenser.
-   *Prognostiserade utbetalningar* ger en tidigare vy över potentiella utbetalningar.
-  *Användnings referens identifierare* ger data åter givning mellan kund användning och beställningar med utbetalningar
-   *Användningen på en daglig kornig het* ger mer detaljerad information och bättre insikter om kund användning.


### <a name="changes-to-data-structure-and-taxonomy"></a>Ändringar i data struktur och taxonomi

I följande tabell visas de mått som har lagts till eller som väsentligen har ändrats i den här versionen. 

| **Ny term**                   |    **Definition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Pris (CC)                     | Priset för en enhets användning för en specifik SKU (i kundens valuta).       |
| Uppskattad utökad avgift (CC) | Uppskattad utökad avgift för antalet användnings enheter för en viss SKU (i kundens valuta). Det här värdet kan inte vara exakt på grund av avrundning eller trunkering-fel.   |
| Utgivar valuta (PC)        | Valuta som föredras av utgivaren för utbetalning.                               |
| Uppskattat pris (PC)           | Uppskattat pris för en användnings enhet för en specifik SKU som baseras på sekundär Exchange-konvertering vid användning av datum beräknas (i utgivar valutan). Det här värdet kan inte vara exakt på grund av avrundning eller trunkering-fel.   |
| Uppskattad utökad avgift (PC) | Uppskattad utökad avgift för antalet användnings enheter för en viss SKU som baseras på sekundär Exchange-konvertering vid användning av datum beräknas (i utgivar valutan). Det här värdet kan inte vara exakt på grund av avrundning eller trunkering-fel. |
| Beräknad utbetalning (PC)          | Beräknad betalning för antalet användnings enheter för en specifik SKU som baseras på sekundär Exchange-konvertering på det datum då användningen beräknas (i utgivar valutan). Det här värdet kan inte vara exakt på grund av avrundning eller trunkering-fel.   |
| Användnings referens                | Identifieraren för en eller flera dagar av kund användning för en specifik SKU som är kopplad till en post i utbetalnings rapporten. |
|  |  |
