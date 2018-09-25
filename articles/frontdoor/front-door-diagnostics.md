---
title: Azure ytterdörren Service – mått och loggning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de olika mått och åtkomstloggar som har stöd för Azure ytterdörren Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998006"
---
# <a name="monitoring-metrics-for-front-door"></a>Övervakning av mått för ytterdörren

Genom att använda Azure ytterdörren Service kan övervaka du dina viktiga mått för att verifiera konfigurationen av ytterdörren, tillsammans med användning, hälsotillstånd och prestanda eller ytterdörren.

## <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser där du kan visa prestandaräknare i portalen. För ytterdörren finns följande mått:

| Mått | Metrisk visningsnamn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Antal | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klientbegäranden som hanteras av ytterdörren.  |
| RequestSize | Begärandestorlek | Byte | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas när begäranden från klienter till ytterdörren. |
| ResponseSize | Svarsstorlek | Byte | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas som svar från åtkomsten till klienter. |
| TotalLatency | Total svarstid | Millisekunder | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Den tid som beräknas från när klientbegäran togs emot av ytterdörren tills klienten godkänt den sista byten svar från ytterdörren. |
| BackendRequestCount | Antal förfrågningar för serverdel | Antal | httpStatus</br>HttpStatusGroup</br>Serverdel | Antal begäranden som skickas från åtkomsten till serverdelen. |
| BackendRequestLatency | Svarstid för backend-begäran | Millisekunder | Serverdel | Den tid som beräknas från när begäran skickades av dörren till serverdelen tills ytterdörren tog emot den sista byten av svaret från serverdelen. |
| BackendHealthPercentage | Serverdelens hälsotillstånd procent | Procent | Serverdel</br>BackendPool | Procentandelen av lyckade hälsorapporter avsökningar från ytterdörren servrar. |
| WebApplicationFirewallRequestCount | Web Application Firewall begäran antal | Antal | Principnamn</br>Regelnamn</br>Åtgärd | Antalet klientbegäranden bearbetas av application layer säkerheten för åtkomsten. |


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en ytterdörren](quickstart-create-front-door.md).
- Lär dig [hur ytterdörren fungerar](front-door-routing-architecture.md).
