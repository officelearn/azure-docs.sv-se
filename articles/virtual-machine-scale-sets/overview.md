---
title: Översikt över skalningsuppsättningar för virtuella datorer i Azure | Microsoft Docs
description: Lär dig mer om VM-skalningsuppsättningar i Azure och hur du skalar dina program automatiskt
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: manayar
ms.openlocfilehash: 1ace0ddd3b29f844488eb7579934541fa8273fa0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418905"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Vad är VM-skalningsuppsättningar?
Med VM-skalningsuppsättningar i Azure kan du skapa och hantera grupper med identiska och belastningsutjämnade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Skalningsuppsättningar tillhandahåller hög tillgänglighet för dina program, och låter dig centralt hantera, konfigurera och uppdatera ett stort antal virtuella datorer. Med VM-skalningsuppsättningar kan du skapa storskaliga tjänster för områden som beräkning, stordata och arbetsbelastningar i containrar.


## <a name="why-use-virtual-machine-scale-sets"></a>Varför ska man använda VM-skalningsuppsättningar?
För att ge redundans och förbättrade prestanda distribueras program vanligtvis över flera instanser. Kunder kan komma åt ditt program via en lastbalanserare som sprider förfrågningar till någon av programinstanserna. Om du behöver genomföra underhåll eller uppdatera en programinstans måste du distribuera dina kunder till en annan tillgänglig programinstans. Om du ska kunna uppfylla kundernas behov kan du behöva öka antalet programinstanser som kör programmet.

VM-skalningsuppsättningar i Azure ger hanteringsmöjligheter för program som körs på flera virtuella datorer, [automatisk skalning av resurser](virtual-machine-scale-sets-autoscale-overview.md) och belastningsutjämning av trafik. Skalningsuppsättningar ger följande viktiga fördelar:

- **Enkelt att skapa och hantera flera virtuella datorer**
    - När du har många virtuella datorer som kör ditt program är det viktigt att bibehålla en konsekvent konfiguration i din miljö. Om ditt program ska ha garanterat tillförlitliga prestanda måste den virtuella datorns storlek, diskkonfigurationen och programinstallationerna matcha för samtliga virtuella datorer.
    - Med skalningsuppsättningar skapas alla VM-instanser från samma grundläggande OS-avbildning och konfiguration. Med den här metoden kan du enkelt hantera hundratals virtuella datorer utan att behöva bekymra dig om ytterligare konfigurationsåtgärder eller nätverkshantering.
    - Skalningsuppsättningar stöder användning av [Azure Load Balancer](../load-balancer/load-balancer-overview.md) för grundläggande nivå 4-trafikdistribution och [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) för mer avancerad nivå 7-trafikdistribution och SSL-avslutning.

- **Ger hög tillgänglighet och programåterhämtning**
    - Skalningsuppsättningar använder du för att köra flera instanser av ditt program. Om någon av dessa VM-instanser drabbas av problem har kunderna fortsatt åtkomst till ditt program via någon av de andra VM-instanserna, med minimalt driftstopp som följd.
    - Om du vill ha ytterligare tillgänglighet kan du använda [tillgänglighetszoner](../availability-zones/az-overview.md) till att automatiskt distribuera VM-instanser i en skalningsuppsättning i ett eller flera datacenter.

- **Tillåter att programmet skalanpassas automatiskt i takt med att resursefterfrågan förändras**
    - Kundernas efterfrågan när det gäller ditt program kan ändras under dagens eller veckans lopp. Skalningsuppsättningar kan automatiskt öka antalet VM-instanser i takt med att efterfrågan ökar och minska antalet VM-instanser i takt med att efterfrågan sjunker.
    - Autoskalning minskar också antalet onödiga VM-instanser som kör programmet när efterfrågan är låg, samtidigt som kunderna ändå kan åtnjuta en godtagbar prestandanivå när efterfrågan växer och ytterligare VM-instanser läggs till automatiskt. Den här möjligheten bidrar till att sänka kostnaderna och på ett effektivt sätt skapa de Azure-resurser som krävs.

- **Fungerar i stor skala**
    - En skalningsuppsättning har stöd för upp till 1 000 instanser av virtuella datorer. Om du skapar och överför dina egna anpassade VM-avbildningar är är gränsen 600 VM-instanser.
    - För att få bästa prestanda med produktionsarbetsbelastningar använder du [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Skillnader mellan virtuella datorer och skalningsuppsättningar
Skalningsuppsättningar skapas från virtuella datorer. Med skalningsuppsättningar tillhandahålls hantering och automatiseringsnivåer för körning och skalning av dina program. Du kan även skapa och hantera enskilda virtuella datorer manuellt eller integrera befintliga verktyg för att skapa en liknande automatiseringsnivå. I följande tabell beskrivs fördelarna med skalningsuppsättningar jämfört med att manuellt hantera flera VM-instanser.

| Scenario                           | Manuell grupp av virtuella datorer                                                                    | Skaluppsättning för virtuella datorer |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Lägg till ytterligare VM-instanser        | Bearbeta manuellt för att skapa, konfigurera och säkerställa efterlevnad                             | Skapa automatiskt från central konfiguration |
| Trafikbalansering och distribution | Bearbeta manuellt för att skapa och konfigurera Azure Load Balancer eller Application Gateway      | Kan automatiskt skapa och integrera med Azure Load Balancer eller Application Gateway |
| Hög tillgänglighet och redundans   | Skapa tillgänglighetsuppsättning manuellt eller distribuera och spåra virtuella datorer mellan tillgänglighetszoner | Automatisk distribution av VM-instanser i tillgänglighetszoner eller tillgänglighetsuppsättningar |
| Skalning av virtuella datorer                     | Manuell övervakning och Azure Automation                                                 | Autoskala utifrån värdmått, gästmått, Application Insights eller schema |

Det tillkommer ingen extra kostnad för skalningsuppsättningar. Du betalar bara för de underliggande beräkningsresurserna som VM-instanser, lastbalanserare eller hanterad disklagring. Hanteringen och automatiseringen av funktioner, t.ex. autoskalning och redundans, medför inga ytterligare avgifter vid användningen av virtuella datorer.


## <a name="next-steps"></a>Nästa steg
Kom igång genom att skapa din första VM-skalningsuppsättning i Azure Portal.

> [!div class="nextstepaction"]
> [Skapa en skalningsuppsättning i Azure Portal](quick-create-portal.md)
