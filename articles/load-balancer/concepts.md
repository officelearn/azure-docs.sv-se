---
title: Azure Load Balancer begrepp
description: Översikt över Azure Load Balancer koncept
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: cb8b3b58f1029a722121f491d202e245300d1aee
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801020"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer begrepp

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

  ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

  *Bild: Hash-baserad distribution*

## <a name="application-independence-and-transparency"></a>Program oberoende och öppenhet

Load Balancer interagerar inte direkt med TCP eller UDP eller program skiktet. Det kan finnas stöd för alla TCP-eller UDP-programscenarier. Belastningsutjämnaren stänger eller kommer inte att stänga flöden eller interagera med flödets nytto Last. Load Balancer tillhandahåller inte Application Layer Gateway-funktioner. Protokoll hand skakningar sker alltid direkt mellan klienten och backend-instansen. Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator. När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.

* Varje slut punkt besvaras av en virtuell dator. En TCP-handskakning sker till exempel mellan klienten och den valda backend-VM: en. Ett svar på en begäran till en klient del är ett svar som genereras av en backend-VM. När du har verifierat anslutningen till en klient del, verifierar du anslutningen i hela till minst en virtuell server dels dator.
* Program nytto laster är transparenta för belastningsutjämnaren. Alla UDP-eller TCP-program kan stödjas.
* Eftersom belastningsutjämnaren inte interagerar med TCP-nyttolasten och ger TLS-avlastning, kan du skapa omfattande krypterade scenarier. Att använda belastningsutjämnare ökar stor skalbarhet för TLS-program genom att avsluta TLS-anslutningen på den virtuella datorn. Din nyckel kapacitet för TLS-sessioner begränsas till exempel bara av typen och antalet virtuella datorer som du lägger till i backend-poolen.

## <a name="load-balancer-terminology"></a>Load Balancer terminologi
| Koncept | Vad betyder det? | Detaljerat dokument |
| ---------- | ---------- | ----------|
Utgående anslutningar | Flöden från backend-poolen till offentliga IP-adresser mappas till klient delen. Azure översätter utgående anslutningar till den offentliga IP-adressen i klient delen via den utgående regeln för belastnings utjämning. Den här konfigurationen har följande fördelar. Enkel uppgradering och haveri beredskap för tjänster, eftersom klient delen kan mappas dynamiskt till en annan instans av tjänsten. Enklare hantering av åtkomst kontrol lista (ACL). ACL: er uttryckt som klient-IP-adresser ändras inte när tjänster skalas upp eller ned eller omdistribueras. Översättning av utgående anslutningar till ett mindre antal IP-adresser än datorer minskar belastningen på att implementera listor över betrodda mottagare.| Om du vill veta mer om käll översättning av nätverks adresser (SNAT) och Azure Load Balancer, se [SNAT och Azure Load Balancer](load-balancer-outbound-connections.md).
Tillgänglighetszoner | Standard Load Balancer stöder ytterligare funktioner i regioner där Tillgänglighetszoner är tillgängliga. Dessa funktioner är stegvisa för alla standard Load Balancer.  Tillgänglighetszoner konfigurationer är tillgängliga för båda typerna av standard belastningsutjämnare. offentlig och intern. En zon-redundant klient del överleva zon haverier genom att använda dedikerad infrastruktur i alla zoner samtidigt. Dessutom kan du garantera en klient del till en speciell zon. En zonindelade-frontend hanteras av en dedikerad infrastruktur i en enda zon. Belastnings utjämning mellan zoner är tillgänglig för backend-poolen. Alla virtuella dator resurser i ett virtuellt nätverk kan ingå i en backend-pool. En Basic Load Balancer stöder inte zoner.| Mer information hittar du [i detaljerad beskrivning av Tillgänglighetszoner relaterade](load-balancer-standard-availability-zones.md) funktioner och [Tillgänglighetszoner översikt](../availability-zones/az-overview.md) .
| HA-portar | Du kan konfigurera belastnings Utjämnings regler med belastnings utjämning för att göra programmet skalbart och vara mycket tillförlitligt. Belastnings utjämning per flöde på kort levde portar i den interna belastningsutjämnarens klient del IP tillhandahålls av dessa regler. Funktionen är användbar när det är opraktiskt eller olämpligt att ange enskilda portar. Med regeln för HA-portar kan du skapa aktiva, passiva eller aktiva-aktiva n + 1-scenarier. De här scenarierna gäller för virtuella nätverks enheter och alla program, vilket kräver stora intervall av inkommande portar. En hälso avsökning kan användas för att avgöra vilka backend-ändar som ska ta emot nya flöden.  Du kan använda en nätverks säkerhets grupp för att emulera ett scenario för port intervall. Den grundläggande belastningsutjämnaren har inte stöd för HA-portar. | Granska [detaljerad diskussion om ha-portar](load-balancer-ha-ports-overview.md)
| Flera klienter | Belastnings utjämning stöder flera regler med flera klient delar.  Standard Load Balancer utökar den här funktionen till utgående scenarier. Utgående regler är inversen till en regel för inkommande trafik. Regeln för utgående trafik skapar en Association för utgående anslutningar. Standard Load Balancer använder alla klient delar som är associerade med en virtuell dator resurs via en belastnings Utjämnings regel. Dessutom kan du använda en parameter i belastnings Utjämnings regeln för att utelämna en belastnings Utjämnings regel för utgående anslutningar, vilket gör att du kan välja vissa klient delar, inklusive ingen. För jämförelse väljer Basic Load Balancer en enda klient del vid slumpmässig het. Det går inte att styra vilken klient del som valdes.|


## <a name="limitations"></a><a name = "limitations"></a>Begränsningar

- En belastnings Utjämnings regel kan inte omfatta två virtuella nätverk.  Frontend-enheter och deras server dels instanser måste finnas i samma virtuella nätverk.  

- Webb arbets roller utan ett virtuellt nätverk och andra Microsoft Platform-tjänster kan nås från instanser enbart bakom en intern belastningsutjämnare som är standard. Använd inte den här åtkomsten eftersom själva själva tjänsten eller den underliggande plattformen kan ändras utan föregående meddelande. Om den utgående anslutningen krävs när du använder en intern standardbelastningsutjämnare måste den [utgående anslutningen](load-balancer-outbound-connections.md) konfigureras.

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
