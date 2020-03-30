---
title: Flera frontends - Azure Load Balancer
description: Med den här utbildningssökvägen kommer du igång med en översikt över flera frontends på Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0a54416a70a8561edfad5915944100e0ce686bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771265"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Flera frontends för Azure Load Balancer

Med Azure Load Balancer kan du läsa in aldotjänster på flera portar, flera IP-adresser eller båda. Du kan använda definitioner för offentlig och intern belastningsutjämnare för att läsa in flöden över en uppsättning virtuella datorer.

Den här artikeln beskriver grunderna i denna förmåga, viktiga begrepp och begränsningar. Om du bara tänker exponera tjänster på en IP-adress kan du hitta förenklade instruktioner för [offentliga](load-balancer-get-started-internet-portal.md) eller [interna](load-balancer-get-started-ilb-arm-portal.md) belastningsutjämnarekonfigurationer. Att lägga till flera frontends är inkrementellt till en enda frontend-konfiguration. Med hjälp av begreppen i den här artikeln kan du när som helst utöka en förenklad konfiguration.

När du definierar en Azure Load Balancer är en klientdel och en serverdelspoolkonfiguration kopplad till regler. Hälsoavsökningen som refereras av regeln används för att avgöra hur nya flöden skickas till en nod i serverdelspoolen. Frontend (aka VIP) definieras av en 3-tuppel som består av en IP-adress (offentlig eller intern), ett transportprotokoll (UDP eller TCP) och ett portnummer från belastningsutjämningsregeln. Serverdelspoolen är en samling IP-konfigurationer för virtuella datorer (en del av nätverkskortets resurs) som refererar till serverdelspoolen för belastningsutjämnardel.

Följande tabell innehåller några exempel på frontend-konfigurationer:

| Klientdel | IP-adress | Protokollet | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Tabellen visar fyra olika frontends. Frontends #1, #2 och #3 är en enda frontend med flera regler. Samma IP-adress används men porten eller protokollet är olika för varje klientdel. Frontends #1 och #4 är ett exempel på flera frontends, där samma frontend protokoll och port återanvänds över flera frontends.

Azure Load Balancer ger flexibilitet när du definierar belastningsutjämningsreglerna. En regel förklarar hur en adress och port på klientdelen mappas till måladressen och porten på backend. Om servergruppsportar återanvänds över regler beror på typen av regel. Varje typ av regel har specifika krav som kan påverka värdkonfiguration och avsökningsdesign. Det finns två typer av regler:

1. Standardregeln utan återanvändning av backend-port
2. Regeln Flytande IP där serverdportar återanvänds

Med Azure Load Balancer kan du blanda båda regeltyperna på samma belastningsutjämnad konfiguration. Belastningsutjämnaren kan använda dem samtidigt för en viss virtuell dator, eller valfri kombination, så länge du följer begränsningarna i regeln. Vilken regeltyp du väljer beror på kraven i ditt program och komplexiteten i att stödja den konfigurationen. Du bör utvärdera vilka regeltyper som är bäst för ditt scenario.

Vi utforskar dessa scenarier ytterligare genom att börja med standardbeteendet.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regeltyp #1: Återanvändning av backend-port

![Bild av flera frontend med grön och lila frontend](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

I det här fallet är frontends konfigureras på följande sätt:

| Klientdel | IP-adress | Protokollet | port |
| --- | --- | --- | --- |
| ![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP är destinationen för det inkommande flödet. I backend-poolen exponerar varje virtuell dator önskad tjänst på en unik port på ett DIP. Den här tjänsten är associerad med klientdelen genom en regeldefinition.

Vi definierar två regler:

| Regel | Karta frontend | Till backend pool |
| --- | --- | --- |
| 1 |![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Den fullständiga mappningen i Azure Load Balancer är nu följande:

| Regel | IP-adress för klientdel | Protokollet | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-adress |80 |
| ![lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-adress |81 |

Varje regel måste skapa ett flöde med en unik kombination av mål-IP-adress och målport. Genom att variera flödesfriskernas målport kan flera regler leverera flöden till samma DIP på olika portar.

Hälsoavsökningar dirigeras alltid till DIP för en virtuell dator. Du måste se till att din avsökning återspeglar hälsotillståndet för den virtuella datorn.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltyp #2: återanvändning av backend-port med flytande IP

Azure Load Balancer ger flexibiliteten att återanvända frontend-porten över flera frontends oavsett vilken regeltyp som används. Dessutom föredrar eller kräver vissa programscenarier samma port som ska användas av flera programinstanser på en enda virtuell dator i serverdapoolen. Vanliga exempel på återanvändning av portar är klustring för hög tillgänglighet, virtuella nätverksinstallationer och utsätta flera TLS-slutpunkter utan återkryptering.

Om du vill återanvända backend-porten över flera regler måste du aktivera Flytande IP i regeldefinitionen.

"Flytande IP" är Azures terminologi för en del av det som kallas DSR (Direct Server Return). DSR består av två delar: en flödestopologi och ett IP-adressmappningsschema. På plattformsnivå fungerar Azure Load Balancer alltid i en DSR-flödestopologi oavsett om flytande IP är aktiverat eller inte. Detta innebär att den utgående delen av ett flöde alltid skrivs om korrekt för att flöda direkt tillbaka till ursprunget.

Med standardregeltypen exponerar Azure ett traditionellt system för mappning av belastningsutjämning av IP-adress för enkel användning. Aktivera flytande IP ändrar IP-adressmappningsschemat för att möjliggöra ytterligare flexibilitet enligt beskrivningen nedan.

Följande diagram illustrerar den här konfigurationen:

![Bild med flera frontend med grön och lila frontsida med DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

I det här fallet har varje virtuell dator i backend-poolen tre nätverksgränssnitt:

* DIP: ett virtuellt nätverkskort som är associerat med den virtuella datorn (IP-konfigurationen av Azures nätverkskortresurs)
* Frontend 1: ett loopback-gränssnitt inom gäst-OS som är konfigurerat med IP-adress för Frontend 1
* Frontend 2: ett loopback-gränssnitt inom gäst-OS som är konfigurerat med IP-adressen för Frontend 2

För varje virtuell dator i backend-poolen kör du följande kommandon vid en Kommandotolk i Windows.

Om du vill få en lista över gränssnittsnamn som du har på den virtuella datorn skriver du det här kommandot:

    netsh interface show interface 

För VM-nätverkskortet (Azure-hanterat) skriver du det här kommandot:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (ersätt gränssnittsnamn med namnet på gränssnittet)

Upprepa dessa kommandon för varje loopback-gränssnitt som du har lagt till:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (ersätt gränssnittsnamn med namnet på det här loopback-gränssnittet)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (ersätt gränssnittsnamn med namnet på det här loopback-gränssnittet)

> [!IMPORTANT]
> Konfigurationen av loopback-gränssnitten utförs inom gästoperativsystemet. Den här konfigurationen utförs inte eller hanteras av Azure. Utan den här konfigurationen fungerar inte reglerna. Hälsoavsökningsdefinitioner använder DIP för den virtuella datorn i stället för loopback-gränssnittet som representerar DSR-frontend. Därför måste tjänsten tillhandahålla avsökningssvar på en DIP-port som återspeglar statusen för den tjänst som erbjuds på loopback-gränssnittet som representerar DSR Frontend.


Låt oss anta samma frontend-konfiguration som i föregående scenario:

| Klientdel | IP-adress | Protokollet | port |
| --- | --- | --- | --- |
| ![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Vi definierar två regler:

| Regel | Klientdel | Karta till backend pool |
| --- | --- | --- |
| 1 |![Regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (i VM1 och VM2) |
| 2 |![Regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (i VM1 och VM2) |

I följande tabell visas hela mappningen i belastningsutjämnaren:

| Regel | IP-adress för klientdel | Protokollet | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |samma som frontend (65.52.0.1) |samma som frontend (80) |
| ![lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |samma som frontend (65.52.0.2) |samma som frontend (80) |

Målet för det inkommande flödet är ip-adressen för klientdelen på loopback-gränssnittet i den virtuella datorn. Varje regel måste skapa ett flöde med en unik kombination av mål-IP-adress och målport. Genom att variera mål-IP-adressen för flödet är port återanvändning möjlig på samma virtuella dator. Din tjänst exponeras för belastningsutjämnaren genom att binda den till klientdelens IP-adress och port för respektive loopback-gränssnitt.

Observera att det här exemplet inte ändrar målporten. Även om detta är ett flytande IP-scenario, azure load balancer stöder också definiera en regel för att skriva om backend målporten och att göra det skiljer sig från frontend målporten.

Den flytande IP-regeltypen är grunden för flera belastningsutjämnarekonfigurationsmönster. Ett exempel som för närvarande är tillgängligt är [SQL AlwaysOn med konfigurationen flera lyssnare.](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) Med tiden kommer vi att dokumentera fler av dessa scenarier.

## <a name="limitations"></a>Begränsningar

* Flera frontend-konfigurationer stöds endast med virtuella IaaS-datorer.
* Med regeln Flytande IP måste ditt program använda den primära IP-konfigurationen för utgående SNAT-flöden. Om ditt program binder till ip-adressen för klientdelen som konfigurerats på loopback-gränssnittet i gästoperativsystemet är Azures utgående SNAT inte tillgängligt för att skriva om det utgående flödet och flödet misslyckas.  Granska [utgående scenarier](load-balancer-outbound-connections.md).
* Offentliga IP-adresser påverkar faktureringen. Mer information finns i [IP-adressprissättning](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Prenumerationsgränser gäller. Mer information finns i [Servicegränser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) för mer information.

## <a name="next-steps"></a>Nästa steg

- Granska [utgående anslutningar](load-balancer-outbound-connections.md) för att förstå effekten av flera frontends på utgående anslutningsbeteende.
