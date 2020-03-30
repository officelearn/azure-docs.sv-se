---
title: Anslutningssvarstid för Windows Virtual Desktop - Azure
description: Anslutningssvarstid för Windows Virtual Desktop-användare.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128186"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Ta reda på användaranslutningssvarstid i Windows Virtual Desktop

Windows Virtual Desktop är globalt tillgängligt. Administratörer kan skapa virtuella datorer i vilken Azure-region de vill. Anslutningsfördröjningen varierar beroende på var användarna och de virtuella datorerna finns. Windows Virtual Desktop-tjänster distribueras kontinuerligt till nya geografiska områden för att förbättra svarstiden. 
 
Verktyget [Uppskattning av Windows Virtual Desktop Experience](https://azure.microsoft.com/services/virtual-desktop/assessment/) kan hjälpa dig att fastställa den bästa platsen för att optimera svarstiden för dina virtuella datorer. Vi rekommenderar att du använder verktyget varannan till var tredje månad för att se till att den optimala platsen inte har ändrats när Windows Virtual Desktop distribueras till nya områden. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows Virtual Desktop använder Azure Traffic Manager, som kontrollerar platsen för användarens DNS-server för att hitta närmaste Windows Virtual Desktop-tjänstinstans. Vi rekommenderar administratörer att granska platsen för användarens DNS-server innan du väljer plats för de virtuella datorerna.

## <a name="next-steps"></a>Nästa steg

- Om du vill kontrollera den bästa platsen för optimal svarstid läser du [verktyget Uppskattningor](https://azure.microsoft.com/services/virtual-desktop/assessment/)för virtuell dator med Windows Virtual Desktop Experience .
- Prisplaner finns i [Windows Virtual Desktop-priser](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- För att komma igång med din Windows Virtual Desktop-distribution, kolla in [vår handledning](tenant-setup-azure-active-directory.md).