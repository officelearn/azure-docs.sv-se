---
title: Systemavsnitt i Azure Event Grid
description: Beskriver systemavsnitt i Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501190"
---
# <a name="system-topics-in-azure-event-grid"></a>Systemavsnitt i Azure Event Grid
Azure Event Grid-tjänsten skapar systemavsnitt när du skapar en första händelseprenumeration för en Azure-händelsekälla. För närvarande skapar Event Grid inte systemavsnitt för ämneskällor som skapades före den 15 mars 2020. För alla ämneskällor som du skapade på eller efter det här datumet skapar Event Grid automatiskt systemavsnitt. I den här artikeln beskrivs **systemavsnitt** i Azure Event Grid.

> [!NOTE]
> Den här funktionen är för närvarande inte aktiverad för Azure Government-molnet. 

## <a name="overview"></a>Översikt
När du skapar första händelseprenumerationen för en Azure-händelsekälla, till exempel Azure Storage-konto, skapar etableringsprocessen för prenumerationen ytterligare en resurs av typen **Microsoft.EventGrid/systemTopics**. När den senaste händelseprenumerationen till Azure-händelsekällan tas bort tas systemämnet automatiskt bort.

Systemämnet är inte tillämpligt på anpassade ämnesscenarier, det vill än avsnitt om Händelserutnät och domäner i händelserutnät. 

## <a name="location"></a>Location
För Azure-händelsekällor som finns i en viss region/plats skapas systemämnet på samma plats som Azure-händelsekällan. Om du till exempel skapar en händelseprenumeration för en Azure blob-lagring i östra USA skapas systemämnet i östra USA. För globala Azure-händelsekällor som Azure-prenumerationer, resursgrupper eller Azure Maps skapar Event Grid systemämnet på **global** plats. 

## <a name="resource-group"></a>Resursgrupp 
I allmänhet skapas systemämnet i samma resursgrupp som Azure-händelsekällan finns i. För händelseprenumerationer som skapats i Azure-prenumerationsomfattningen skapas systemämnet under resursgruppen **Default-EventGrid**. Om resursgruppen inte finns skapar Azure Event Grid den innan systemämnet skapas. 

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ta bort resursgruppen med lagringskontot visas systemämnet i listan över berörda resurser.  

![Ta bort resursgrupp](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Anpassade ämnen](event-sources.md#custom-topics)
- [Domäner](event-domains.md)