---
title: Översikt över vanliga autoskalningsmönster
description: Läs om några av de vanliga mönster för att automatiskt skala din resurs i Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7fa2cbc831c6bcb734b4a4596a3f75f81e8ee085
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317040"
---
# <a name="overview-of-common-autoscale-patterns"></a>Översikt över vanliga autoskalningsmönster
Den här artikeln beskrivs några vanliga mönster för att skala din resurs i Azure.

Automatisk skalning i Azure Monitor gäller endast [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Kan komma igång

Den här artikeln förutsätter att du är bekant med automatisk skalning. Du kan [Kom igång här för att skala din resurs][1]. Följande är några av de vanliga mönster i skala.

## <a name="scale-based-on-cpu"></a>Skala baserat på CPU

Du har en webbapp (/ VMSS/molntjänstroll) och

- Du vill skala ut/skala in baserat på CPU.
- Dessutom kan du se till att det finns ett minsta antal instanser.
- Dessutom bör du se till att du ställer in en övre gräns för antalet instanser som du kan skala till.

![Skala baserat på CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skala på olika sätt på vardagar vs helger

Du har en webbapp (/ VMSS/molntjänstroll) och

- Du vill ha 3 instanser som standard (på vardagar)
- Du förväntar dig inte att trafik på helger och kan därför du vill skala ned till 1 instans på helger.

![Skala på olika sätt på vardagar vs helger][3]

## <a name="scale-differently-during-holidays"></a>Skalas annorlunda under helger

Du har en webbapp (/ VMSS/molntjänstroll) och

- Du vill skala upp och ned baserat på CPU-användning som standard
- Men under Rean (eller särskilda dagar som är viktiga för ditt företag) vill du åsidosätta standardinställningarna och har mer kapacitet till ditt förfogande.

![Skala på olika sätt på helger][4]

## <a name="scale-based-on-custom-metric"></a>Skala baserat på anpassat mått

Du har en frontwebb och en API-nivå som kommunicerar med serverdelen.

- Du vill skala API-nivå som baseras på anpassade händelser i klientdelen (exempel: Du vill skala ditt kassan baserat på antalet objekt i kundvagnen)

![Skala baserat på anpassat mått][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
