---
title: Flera Frontends för Azure belastningsutjämnare | Microsoft Docs
description: Översikt över flera Frontends på Azure belastningsutjämnare
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: ''
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: cf8fa396e0518e1c847225dfc1d8f91c3421bd11
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181497"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Flera Frontends för Azure belastningsutjämnare

Azure belastningsutjämnare kan du läsa in Utjämna tjänster på flera portar, flera IP-adresser eller båda. Du kan använda belastning offentliga och interna belastningsutjämnare definitioner för att läsa in saldo flödar över en uppsättning virtuella datorer.

Den här artikeln beskriver grunderna i den här möjligheten, viktiga begrepp och begränsningar. Om du endast vill exponera tjänster på en IP-adress, kan du hitta förenklad instruktioner för [offentliga](load-balancer-get-started-internet-portal.md) eller [interna](load-balancer-get-started-ilb-arm-portal.md) läsa in belastningsutjämning konfigurationer. Lägga till flera frontends är inkrementell till en enda frontend-konfiguration. Du kan expandera en förenklad konfiguration när som helst med hjälp av begrepp i den här artikeln.

När du definierar en Azure belastningsutjämnare ansluten en klientdel och en konfiguration för backend-pool med regler. Hälsoavsökningen som refereras av regeln används för att avgöra hur nya flöden skickas till en nod i serverdelspoolen. Klientdel (aka VIP) definieras av en 3-tuppel som består av en IP-adress (offentlig eller intern), en transportprotokollet (UDP eller TCP) och ett portnummer från regeln för belastningsutjämning. Serverdelspoolen är en samling med virtuella IP-konfigurationer (del av NIC-resurs) som refererar till Belastningsutjämnarens serverdelspool.

Följande tabell innehåller några exempel klientdel konfigurationer:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Tabellen visar fyra olika frontends. Frontends #1, #2 och #3 är en enda klientdel med flera regler. Samma IP-adress används men port eller protokoll är olika för varje klientdel. Frontends #1 och #4 är ett exempel på flera frontends där samma frontend-protokoll och port återanvänds över flera frontends.

Azure belastningsutjämnare ger flexibilitet för att definiera regler för belastningsutjämning. En regel förklarar hur en adress och port på klientdelen mappas till mål-adress och port på serverdelen. Huruvida backend portar återanvänds över regler beror på vilken typ av regeln. Varje regel har specifika krav som kan påverka konfiguration och avsökning design av värd. Det finns två typer av regler:

1. Standardregeln med återanvändning inga backend-port
2. Flytande IP regeln där återanvänds backend-portar

Azure belastningsutjämnare kan du blanda både regeltyper på samma belastningsutjämningskonfigurationen. Belastningsutjämnaren kan använda dem samtidigt för en viss virtuell dator eller en kombination, så länge du följer villkor för regeln. Vilken typ av du väljer beror på kraven i ditt program och komplexitet stöder denna konfiguration. Du bör utvärdera vilka regeltyper lämpar sig bäst för ditt scenario.

Vi utforska dessa scenarier ytterligare genom att börja med standardbeteendet.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regeltyp #1: ingen backend port återanvändning

![Flera frontend-bild med grön och lila klientdel](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

I det här scenariot konfigureras i frontends enligt följande:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| ![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Lila klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP är målet för inkommande flödet. I serverdelspoolen visar varje virtuell dator till önskade-tjänsten på en unik port på en DIP. Den här tjänsten är associerad med klientdel via en regeldefinition av.

Vi definierar två regler:

| Regel | Mappa klientdel | Till backend-adresspool |
| --- | --- | --- |
| 1 |![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Fullständig mappningen i Azure belastningsutjämnare är nu på följande sätt:

| Regel | Frontend-IP-adress | protokoll | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-adress |80 |
| ![Lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-adress |81 |

Varje regel måste generera ett flöde med en unik kombination av IP-adress och målport. Genom att variera flödet målporten kan flera regler leverera flöden till samma DIP på olika portar.

Hälsoavsökningar alltid skickas vidare till DIP-ADRESSEN för en virtuell dator. Du måste se till att du att din avsökning visar hälsotillståndet för den virtuella datorn.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltyp #2: återanvändning av backend-port med hjälp av flytande IP

Azure belastningsutjämnare ger flexibilitet att återanvända klientdelsport över flera frontends oavsett regeltypen används. Dessutom kan vissa Programscenarier föredrar eller kräver samma port som ska användas av flera programinstanser på en enda virtuell dator i serverdelspoolen. Vanliga exempel port återanvändning av omfattar kluster för hög tillgänglighet, virtuella installationer och exponera flera TLS slutpunkter utan omkryptering.

Om du vill återanvända serverporten över flera regler måste du aktivera flytande IP i Regeldefinitionen.

”Flytande IP” är Azures terminologi för en del av känt som direkt Server returnera (DSR). DSR består av två delar: en flödet topologi och en IP-mappning schemat. På en plattformsnivå körs alltid Azure belastningsutjämnare i en topologi för flödet av DSR oavsett om flytande IP är aktiverat eller inte. Det innebär att den utgående delen av ett flöde alltid korrekt skrivits om för att gå direkt till ursprunget.

Med regeltypen standard exponerar Azure en traditionell IP-adress mappning schema för användarvänlighet för belastningsutjämning. Aktivera flytande IP ändrar IP-adress mappning schemat så att ytterligare flexibilitet som beskrivs nedan.

Följande diagram illustrerar den här konfigurationen:

![Flera frontend-bild med grön och lila klientdel med DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

I det här scenariot har varje virtuell dator i serverdelspoolen tre nätverksgränssnitt:

* DIP: ett virtuellt nätverkskort som är kopplade till den virtuella datorn (IP-konfiguration av Azures NIC resurs)
* : 1 en loopback-gränssnitt i gästoperativsystemet som är konfigurerade med IP-adress för klientdel 1
* : 2 en loopback-gränssnitt i gästoperativsystemet som är konfigurerade med IP-adress för klientdel 2

> [!IMPORTANT]
> Konfigurationen av loopback-gränssnitt har utförts i gästoperativsystemet. Den här konfigurationen är inte utföras eller som hanteras av Azure. Reglerna fungerar inte utan den här konfigurationen. Hälsotillstånd avsökningen definitionerna använder DIP-ADRESSEN för den virtuella datorn i stället för loopback-gränssnittet som representerar DSR klientdel. Din tjänst måste därför ange avsökningen svar på en DIP-port som återspeglar status för tjänst som erbjuds på loopback-gränssnittet som representerar DSR klientdel.

Antar vi att samma konfiguration för klientdel som i det föregående scenariot:

| Klientdel | IP-adress | protokoll | port |
| --- | --- | --- | --- |
| ![grön klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Lila klientdel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Vi definierar två regler:

| Regel | Klientdel | Mappa till serverdelspoolen |
| --- | --- | --- |
| 1 |![regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (i VM1 och VM2) |
| 2 |![regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (i VM1 och VM2) |

Följande tabell visar slutförd mappningen i belastningsutjämnaren:

| Regel | Frontend-IP-adress | protokoll | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |samma som klientdel (65.52.0.1) |samma som klientdel (80) |
| ![Lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |samma som klientdel (65.52.0.2) |samma som klientdel (80) |

Målet för inkommande flödet är IP-adress för klientdel på loopback-gränssnittet på den virtuella datorn. Varje regel måste generera ett flöde med en unik kombination av IP-adress och målport. Genom att variera den IP-adressen i flöde går port återanvändning på samma virtuella dator. Tjänsten är exponerad för belastningsutjämnaren genom att binda till klientdelen IP-adress och port för respektive loopback-gränssnittet.

Observera att det här exemplet inte ändrar målporten. Även om det är ett flytande IP-scenario stöder Azure belastningsutjämnare också definiera en regel för att skriva om backend-målport och skiljer den från klientdel målport.

Regeltyp flytande IP är grunden för flera belastningsutjämnare configuration belastningsmönster. Ett exempel som är tillgänglig för närvarande är den [SQL AlwaysOn med flera lyssnare](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfiguration. Över tiden, kommer vi dokumentera flera av dessa scenarier.

## <a name="limitations"></a>Begränsningar

* Flera frontend-konfigurationer stöds bara med IaaS-VM.
* Programmet måste använda primära IP-konfigurationen för utgående flöden med flytande IP-regel. Om ditt program Binder till klientdelens IP-adressen konfigurerad på loopback gränssnitt i gästoperativsystemet, Azure's SNAT är inte tillgängligt för att skriva om utgående flödet och flödet misslyckas.
* Offentliga IP-adresser påverka faktureringen. Mer information finns i [priser för IP-adress](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Prenumerationsbegränsningar gäller. Mer information finns i [gränser](../azure-subscription-service-limits.md#networking-limits) mer information.

## <a name="next-steps"></a>Nästa steg

- Granska [utgående anslutningar](load-balancer-outbound-connections.md) att förstå effekten av flera frontends på utgående anslutning beteende.
