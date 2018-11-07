---
title: Flera klienter för Azure Load Balancer | Microsoft Docs
description: Översikt över flera klienter på Azure-belastningsutjämnare
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: d435d2c491cf17356e96f7bbb05b1e22c8e04aca
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219366"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Flera klienter för Azure Load Balancer

Azure Load Balancer kan du belastningsutjämnar tjänster på flera portar, flera IP-adresser eller båda. Du kan använda offentliga och interna load balancer definitioner kan belastningsutjämna flöden för en uppsättning virtuella datorer.

Den här artikeln beskriver grunderna i den här möjligheten, viktiga begrepp och begränsningar. Om du bara vill exponera tjänster på en IP-adress, hittar du förenklad anvisningar [offentliga](load-balancer-get-started-internet-portal.md) eller [interna](load-balancer-get-started-ilb-arm-portal.md) läsa in belastningsutjämnare konfigurationer. Att lägga till flera klienter är inkrementell till en enda frontend-konfiguration. Du kan expandera en förenklad konfiguration när som helst med hjälp av begreppen i den här artikeln.

När du definierar en Azure Load Balancer, är en klientdel och en serverdel för adresspool anslutna med regler. Hälsoavsökningen som refereras av regeln används för att avgöra hur nya flöden som ska skickas till en nod i serverdelspoolen. Klientdelen (även kallat VIP) definieras av en 3-tuppel som består av en IP-adress (offentlig eller intern), en transportprotokoll (UDP eller TCP) och ett portnummer från regel för belastningsutjämning. Backend-pool är en samling med virtuella IP-konfigurationer (del av NIC-resursen) som refererar till serverdelspoolen för belastningsutjämnaren.

I följande tabell innehåller några exempel frontend-konfigurationer:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Tabellen visar fyra olika klienter. Klienter #1, #2 och #3 är en enda klientdel med flera regler. Samma IP-adress används, men den porten eller det protokollet är olika för varje klientdel. Klienter nr 1 och #4 är ett exempel på flera klienter där samma frontend-protokoll och port återanvänds i flera klienter.

Azure Load Balancer ger flexibilitet för att definiera regler för belastningsutjämning. En regel som anger hur en adress och port på klientdelen mappas till måladress och port på serverdelen. Huruvida serverportar återanvänds i regler beror på vilken typ av regeln. Varje typ av regel har särskilda krav som kan påverka design av värd konfiguration och avsökningen. Det finns två typer av regler:

1. Standardregeln med återanvändning inga backend-port
2. Flytande IP-regeln där serverportar återanvänds

Azure Load Balancer kan du blanda både regeltyper på samma belastningsutjämningskonfigurationen. Belastningsutjämnaren kan använda dem samtidigt för en viss virtuell dator eller valfri kombination, så länge du följer begränsningarna för regeln. Vilken typ du väljer beror på kraven för ditt program och komplexiteten med stöd för konfigurationen. Du ska utvärdera vilka regeltyper lämpar sig bäst för ditt scenario.

Vi utforska vidare dessa scenarier genom att börja med standardbeteendet.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regeltyp #1: ingen återanvändning för backend-port

![Flera frontend-bild med gröna och lila klientdel](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

I det här scenariot konfigureras på klienter enligt följande:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| ![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Lila klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP är målet för det inkommande flödet. Varje virtuell dator exponerar den önskade tjänsten på en unik port på en DIP i backend-poolen. Den här tjänsten är associerad med klientdelen via en Regeldefinitionen.

Vi definierar två regler:

| Regel | Mappa klientdel | Till serverdelspoolen |
| --- | --- | --- |
| 1 |![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Fullständig mappningen i Azure Load Balancer är nu på följande sätt:

| Regel | Frontend-IP-adress | protokoll | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-adress |80 |
| ![Lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-adress |81 |

Varje regel måste skapa ett flöde med en unik kombination av målets IP-adress och målport. Genom att variera målporten för flödet, kan flera regler leverera flöden till samma DIP på olika portar.

Hälsokontroller av slutpunkter är alltid dirigeras till DIP för en virtuell dator. Du måste se till att du att din avsökningen återspeglar hälsotillståndet för den virtuella datorn.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltyp #2: återanvändning av backend-porten med hjälp av flytande IP

Azure Load Balancer ger flexibiliteten att återanvända frontend-port över flera klienter oavsett regeltypen används. Dessutom kan vissa Programscenarier föredrar eller kräva att samma port som ska användas av flera instanser av programmet på en enskild virtuell dator i serverdelspoolen. Vanliga exempel på port återanvändning omfattar kluster för hög tillgänglighet, nätverks-virtuella installationer och exponera flera slutpunkter för TLS utan omkryptering.

Om du vill återanvända serverdelsporten över flera regler måste du aktivera flytande IP i Regeldefinitionen för.

”Flytande IP” är Azures terminologi för en del av känt som direkt Server returnera (DSR). DSR består av två delar: en flow-topologi och en IP-mappning av schemat. På en plattformsnivå körs Azure Load Balancer alltid på en DSR flow topologi, oavsett om flytande IP är aktiverat eller inte. Det innebär att den utgående delen av ett flöde alltid korrekt skrivs om för att flöda direkt till ursprunget.

Med regeltypen standard exponerar Azure en traditionell IP-adress mappning schema för enkel användning för belastningsutjämning. Aktivera flytande IP ändrar IP-adress mappning schemat så att ytterligare flexibilitet som beskrivs nedan.

Följande diagram illustrerar den här konfigurationen:

![Flera frontend-bild med gröna och lila klientdel med DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Det här scenariot har varje virtuell dator i serverdelspoolen tre nätverksgränssnitt:

* DIP: ett virtuellt nätverkskort som är associerade med den virtuella datorn (IP-konfiguration av Azures NIC-resurs)
* Klientdel 1: en loopback-gränssnittet i gästoperativsystemet som konfigureras med IP-adress för klientdel 1
* Klientdel 2: en loopback-gränssnittet i gästoperativsystemet som konfigureras med IP-adress för klientdel 2

> [!IMPORTANT]
> Konfigurationen av loopback-gränssnitt utförs i gästoperativsystemet. Den här konfigurationen är inte utförs eller hanteras av Azure. Reglerna fungerar inte utan den här konfigurationen. Hälsotillstånd avsökningen definitioner använda DIP av den virtuella datorn i stället för loopback-gränssnittet som representerar DSR-klientdel. Din tjänst måste därför tillhandahålla avsökningssvar på en DIP-port som återspeglar status för den tjänst som erbjuds på loopback-gränssnittet som representerar DSR-klientdel.

Vi antar att samma konfiguration för klientdel som i scenariot ovan:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| ![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Lila klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Vi definierar två regler:

| Regel | Klientdel | Mappa till backend-pool |
| --- | --- | --- |
| 1 |![regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (i VM1 och VM2) |
| 2 |![regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Serverdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (i VM1 och VM2) |

I följande tabell visar slutförd mappningen i belastningsutjämnaren:

| Regel | Frontend-IP-adress | protokoll | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |samma som klientdelen (65.52.0.1) |samma som klientdelen (80) |
| ![Lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |samma som klientdelen (65.52.0.2) |samma som klientdelen (80) |

Mål för det inkommande flödet är klientdelens IP-adress på loopback-gränssnittet på den virtuella datorn. Varje regel måste skapa ett flöde med en unik kombination av målets IP-adress och målport. Port återanvändning är möjligt på samma virtuella dator genom att variera målets IP-adress till flödet. Belastningsutjämnaren visas din tjänst genom att binda den till frontend IP-adressen och porten för respektive loopback-gränssnittet.

Observera att det här exemplet inte ändrar målporten. Även om det här är ett scenario med flytande IP, stöder Azure Load Balancer också definiera en regel för att skriva om backend-målport och skiljer den från frontend-målport.

Regeltyp flytande IP är grunden för flera load balancer configuration mönster. Ett exempel som finns för närvarande är den [SQL AlwaysOn med flera lyssnare](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfiguration. Med tiden kommer vi dokumenterar flera av dessa scenarier.

## <a name="limitations"></a>Begränsningar

* Flera frontend-konfigurationer stöds endast med virtuella IaaS-datorer.
* Programmet måste använda den primära IP-konfigurationen för utgående flöden med flytande IP-regel. Om ditt program som binder till klientdelens IP-adress som konfigurerats på loopback gränssnitt i gästoperativsystemet, Azure's SNAT går inte att skriva om utgående flödet och flödet misslyckas.
* Offentliga IP-adresser påverka faktureringen. Mer information finns i [priser för IP-adress](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Prenumerationsbegränsningar gäller. Mer information finns i [tjänstbegränsningar](../azure-subscription-service-limits.md#networking-limits) mer information.

## <a name="next-steps"></a>Nästa steg

- Granska [utgående anslutningar](load-balancer-outbound-connections.md) att förstå effekten av flera klienter på utgående anslutning beteende.
