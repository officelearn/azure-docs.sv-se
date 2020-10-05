---
title: Azure Load Balancer begrepp
description: Översikt över Azure Load Balancer koncept
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 61620a8497765c4d8f90a3d616bd2f4b932f8dcc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91629037"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer algoritm

Load Balancer har flera funktioner för både UDP-och TCP-program.

## <a name="load-balancing-algorithm"></a>Belastningsutjämningsalgoritm

Du kan skapa en regel för belastnings utjämning för att distribuera trafik från klient delen till en backend-pool. Azure Load Balancer använder en hash-algoritm för distribution av inkommande flöden (inte byte). Load Balancer skriver om meddelandehuvuden för flöden till instanser i backend-poolen. En server är tillgänglig för att ta emot nya flöden när en hälso avsökning indikerar en felfri slut punkt för backend.

Som standard använder belastningsutjämnaren en hash med fem tupler.

Hashen innehåller:

- **Källans IP-adress**
- **Källport**
- **Mål-IP-adress**
- **Målport**
- **IP-protokoll nummer för att mappa flöden till tillgängliga servrar**

Tillhörighet till en käll-IP-adress skapas med hjälp av en hash med två eller tre tupler. Paket av samma flöde kommer till samma instans bakom den belastningsutjämnade klient delen.

Käll porten ändras när en klient startar ett nytt flöde från samma käll-IP. Därför kan en hash med fem tupler orsaka att trafiken går till en annan server dels slut punkt.
Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](./load-balancer-distribution-mode.md).

Följande bild visar den hash-baserade distributionen:

![Hash-baserad fördelning](./media/load-balancer-overview/load-balancer-distribution.png)

*Bild: Hash-baserad distribution*

## <a name="application-independence-and-transparency"></a>Program oberoende och öppenhet

Load Balancer interagerar inte direkt med TCP eller UDP eller program skiktet. Det kan finnas stöd för alla TCP-eller UDP-programscenarier. Belastningsutjämnaren stänger eller kommer inte att stänga flöden eller interagera med flödets nytto Last. Load Balancer tillhandahåller inte Application Layer Gateway-funktioner. Protokoll hand skakningar sker alltid direkt mellan klienten och backend-instansen. Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator. När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.

- Varje slut punkt besvaras av en virtuell dator. En TCP-handskakning sker till exempel mellan klienten och den valda backend-VM: en. Ett svar på en begäran till en klient del är ett svar som genereras av en backend-VM. När du har verifierat anslutningen till en klient del, verifierar du anslutningen i hela till minst en virtuell server dels dator.
- Program nytto laster är transparenta för belastningsutjämnaren. Alla UDP-eller TCP-program kan stödjas.
- Eftersom belastningsutjämnaren inte interagerar med TCP-nyttolasten och ger TLS-avlastning, kan du skapa omfattande krypterade scenarier. Att använda belastningsutjämnare ökar stor skalbarhet för TLS-program genom att avsluta TLS-anslutningen på den virtuella datorn. Din nyckel kapacitet för TLS-sessioner begränsas till exempel bara av typen och antalet virtuella datorer som du lägger till i backend-poolen.

## <a name="limitations"></a><a name = "limitations"></a>Hos

- En belastnings Utjämnings regel kan inte omfatta två virtuella nätverk.  Frontend-enheter och deras server dels instanser måste finnas i samma virtuella nätverk.  

- Load Balancer tillhandahåller belastnings utjämning och port vidarebefordring för vissa TCP-eller UDP-protokoll. Regler för belastnings utjämning och inkommande NAT-regler stöder TCP och UDP, men inte andra IP-protokoll, inklusive ICMP.

- Utgående flöden från en VM-VM till en klient del för en intern Load Balancer Miss kommer att Miss förfaller.

- Vidarebefordring av IP-fragment stöds inte för regler för belastnings utjämning. IP-fragmentering av UDP-och TCP-paket stöds inte i regler för belastnings utjämning. Belastnings Utjämnings regler för HA portar kan användas för att vidarebefordra befintliga IP-fragment. Mer information finns i [Översikt över portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer: skapa en, skapa virtuella datorer med ett anpassat IIS-tillägg installerat och belastningsutjämna webbappen mellan de virtuella datorerna.
- Läs mer om [Azure Load Balancer utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
