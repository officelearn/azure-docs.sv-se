---
title: Översikt över vanliga Autoskala mönster
description: Läs om några av de vanliga mönster för att automatiskt skala din resurs i Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 84727ec3694f64d40ad002a248a255df9074d7f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263271"
---
# <a name="overview-of-common-autoscale-patterns"></a>Översikt över vanliga Autoskala mönster
Den här artikeln beskrivs några vanliga mönster för att skala din resurs i Azure.

Azure övervakaren Autoskala gäller enbart för Virtual Machine Scale uppsättningar (VMSS), cloud services, apptjänstplaner och apptjänstmiljöer. 

# <a name="lets-get-started"></a>Kan komma igång

Den här artikeln förutsätter att du är bekant med automatisk skalning. Du kan [starta här att skala din resurs][1]. Följande är några av de vanliga skala mönster.

## <a name="scale-based-on-cpu"></a>Skala baserat på CPU

Du har ett webbprogram (/ VMSS/molnet rolltjänst) och 

- Du vill skala ut/skala i baserat på CPU.
- Dessutom kan vill du se till att det finns ett minsta antal instanser. 
- Du också se till att du anger en övre gräns för antalet instanser som kan skalas till.

![Skala baserat på CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skala annorlunda veckodagar vs helger

Du har ett webbprogram (/ VMSS/molnet rolltjänst) och

- Du vill 3 instanser som standard (på vardagar)
- Du förväntar dig inte trafik på helger och därför du vill skala ned 1 instans på helger.

![Skala annorlunda veckodagar vs helger][3]

## <a name="scale-differently-during-holidays"></a>Skala annorlunda under helgdagar

Du har ett webbprogram (/ VMSS/molnet rolltjänst) och 

- Du vill skala upp/ned baserat på CPU-användning som standard
- Dock under köpstarka jul (eller särskilda dagar som är viktiga för ditt företag) vill du åsidosätta standardvärdena och dina anställda har mer kapacitet.

![Skala annorlunda på helgdagar][4]

## <a name="scale-based-on-custom-metric"></a>Skala baserat på anpassade mått

Du har en frontwebb och en API-nivå som kommunicerar med serverdelen. 

- Du vill skala API-nivå som baseras på anpassade händelser i klientdelen (exempel: du vill skala utcheckningen processen baserat på antalet objekt i kundvagnen)

![Skala baserat på anpassade mått][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png