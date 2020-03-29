---
title: Översikt över vanliga autoskalningsmönster
description: Lär dig några av de vanligaste mönstren för att automatiskt skala din resurs i Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396093"
---
# <a name="overview-of-common-autoscale-patterns"></a>Översikt över vanliga autoskalningsmönster
I den här artikeln beskrivs några av de vanligaste mönstren för att skala din resurs i Azure.

Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Låt oss komma igång

Den här artikeln förutsätter att du är bekant med automatisk skala. Du kan [komma igång här för att skala din resurs][1]. Följande är några av de gemensamma skalmönster.

## <a name="scale-based-on-cpu"></a>Skala baserad på CPU

Du har en webbapp (/VMSS/cloud service-roll) och

- Du vill skala ut/skala in baserat på CPU.
- Dessutom vill du se till att det finns ett minsta antal instanser.
- Du vill också se till att du anger en maximal gräns för antalet instanser som du kan skala till.

![Skala baserad på CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skala olika på vardagar vs helger

Du har en webbapp (/VMSS/cloud service-roll) och

- Du vill ha 3 instanser som standard (på vardagar)
- Du förväntar dig inte trafik på helgerna och därför vill du skala ner till 1 instans på helgerna.

![Skala olika på vardagar vs helger][3]

## <a name="scale-differently-during-holidays"></a>Skala olika under helgdagar

Du har en webbapp (/VMSS/cloud service-roll) och

- Du vill skala upp/ned baserat på CPU-användning som standard
- Men under semesterperioden (eller specifika dagar som är viktiga för ditt företag) du vill åsidosätta standardvärden och har mer kapacitet till ditt förfogande.

![Skala olika på helgdagar][4]

## <a name="scale-based-on-custom-metric"></a>Skala baserat på anpassat mått

Du har en webbklämd och en API-nivå som kommunicerar med serverdelen.

- Du vill skala API-nivån baserat på anpassade händelser i frontend (exempel: Du vill skala utcheckningsprocessen baserat på antalet artiklar i kundvagnen)

![Skala baserat på anpassat mått][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

