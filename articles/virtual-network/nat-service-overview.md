---
title: Vad är Azure NAT-tjänsten?
titlesuffix: Azure NAT
description: Översikt över Azure NAT service-funktioner, arkitektur och implementering. Lär dig hur NAT-tjänsten fungerar och hur du använder den i molnet.
services: nat
documentationcenter: na
author: asudbring
ms.service: nat
Customer intent: As an IT administrator, I want to learn more about the Azure NAT service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: 04464716384c1700571096443963499b0ca1b261
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376506"
---
# <a name="what-is-azure-nat-service-public-preview"></a>Vad är Azure NAT-tjänsten (offentlig för hands version)

Du kan använda Azure NAT-tjänsten för att tillhandahålla utgående anslutningar för offentliga IP-destinationer för alla virtuella datorer i det virtuella nätverket. NAT-tjänsten kan hantera olika typer av arbets belastningar genom att tillhandahålla översättningar på begäran utan att planera per instans efter frågan.

Du kan definiera vilka undernät i ett virtuellt nätverk som ska använda NAT-tjänsten. Du kan skala utgående anslutningar med en eller flera IP-adresser. Och du kan finjustera tids gränsen för inaktivitet mellan 4 och 120 minuter. NAT-tjänsten kommer också alltid att skicka TCP-återställningar när inaktiva anslutningar återanvänds, vilket är fallet när tids gränsen för inaktivitet har nåtts. 

Du kan skapa [Tillgänglighetszoner](../availability-zones/az-overview.md) scenarier genom att placera NAT-tjänst i en speciell tillgänglighets zon.

NAT-tjänsten är en hanterad, elastisk tjänst för utgående anslutningar. Du kan kombinera NAT-tjänsten med standard offentliga IP-adressresurser och standard belastnings utjämning för att skapa ytterligare inkommande scenarier.

>[!NOTE] 
>Azure NAT-tjänsten är tillgänglig som en offentlig för hands version för tillfället. För närvarande är det bara tillgängligt i en begränsad uppsättning [regioner](#regions). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="nat-gateways"></a>NAT-gatewayer

Azure NAT-tjänsten tillhandahåller en NAT-gateway-resurs. Den här resursen kan konfigureras med en eller flera offentliga IP-adresser med [standard-offentliga IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md#standard) -adresser eller standard resurser för [offentliga IP-prefix](../virtual-network/public-ip-address-prefix.md) eller både och. Du använder NAT-tjänsten genom att konfigurera undernät för en virtuell nätverks resurs för att använda en speciell NAT-gateway. En NAT-gateway kan användas på flera undernät i samma virtuella nätverk. Olika virtuella nätverk behöver separata NAT-gatewayer.

Ett undernät kommer att börja använda NAT-gatewayen för utgående anslutningar när den har kon figurer ATS för ett undernät i ett virtuellt nätverk. NAT-gateway ersätter det förinställda Internet målet för ett undernät och konfigurationen av en UDR behövs inte. Utgående översättningar anges på begäran eftersom virtuella datorer skapar dem.

>[!IMPORTANT]
>Utgående flöden kommer inte att lyckas förrän du också tilldelar NAT-gatewayen minst en offentlig IP-adress.

>[!NOTE]
> Om du behöver garanteras per allokering av virtuella datorer eller om du behöver mer detaljerad information för att definiera utgående anslutningar på per instans nivå eller pooler med instanser eller för att garantera käll Network Address Translation (SNAT) till en viss instans använder du [standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) med [utgående regler](../load-balancer/load-balancer-outbound-rules-overview.md) för att definiera Load Balancer poolbaserade utgående käll Network Address Translation (SNAT).   Den här flexibiliteten ger avsevärt ökad komplexitet och ytterligare planering när olika arbets belastningar kombineras, eller så kan den utgående anslutningen till arbets belastningarna inte korrekt anpassas till en enskild instans.

## <a name="combination-of-inbound-and-outbound-scenarios-and-skus"></a>Kombination av inkommande och utgående scenarier och SKU: er

Du kan skapa inkommande och utgående scenarier i samma undernät med NAT-gatewayen, genom att kombinera NAT-gateway med offentlig standard-IP för att få åtkomst till den virtuella och den offentliga belastnings Utjämnings slut punkter, inklusive alla tjänster som är baserade på dem. NAT-gateway och standard offentlig IP-adress och standard-offentlig belastningsutjämnare är flödes riktnings medveten. För utgående flöden ersätter NAT-gateway alla andra utgående scenarier och har företräde framför [Load Balancer utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) och [offentliga IP-adresser på instans nivå på virtuella datorer](../load-balancer/load-balancer-outbound-connections.md) specifikt. För inkommande inhämtade flöden kan offentliga IP-adresser på instans nivå eller offentliga slut punkter på en belastningsutjämnare användas samtidigt med NAT-gateway.  När det här scenariot är klart kommer inkommande/utgående flöden att lyckas i samma undernät.  Det resulterande scenariot är "säkert som standard" för inkommande, ursprungliga scenarier som en NSG krävs och det måste uttryckligen tillåta inkommande ursprunglig trafik.

Du kan dela upp dina distributioner i ett virtuellt nätverk i undernät för att påverka det utgående beteendet. Du kan också ha offentliga IP-adresser som används som offentliga IP-adresser på instans nivå på virtuella datorer och belastningsutjämnare som tillhandahålls utgående käll Network Address Translation (SNAT) för utgående anslutningar i ett undernät och NAT-gateway i ett annat undernät av samma virtuella nätverk. Du kan inte kombinera NAT-gatewayer med resurser som använder grundläggande offentliga IP-adressresurser eller grundläggande belastnings Utjämnings resurser i samma undernät. Du kan distribuera de grundläggande SKU-resurserna till ett annat undernät i samma virtuella nätverk.

## <a name="network-address-translation"></a>Översättning av nätverks adresser

När konfigurationen har slutförts tillhandahåller NAT-gatewayen port maskerad source Network Address Translation (SNAT) för alla TCP-och UDP-program genom att översätta en virtuell dator instanss privata IP-adress till offentliga IP-adresser.

Flöden från privata IP-adresser översätts till en eller flera offentliga IP-adresser baserat på din konfiguration. Översättningen sker eftersom den privata IP-adressen för käll Network Address Translation (SNAT) ändras till en offentlig IP-adress. Port maskerad innebär att käll porten skrivs om för att skilja flöden efter översättning och förenkla en många till en/flera-mappning. Käll port numret för det resulterande flödet i det offentliga IP-adressutrymmet tilldelas på begäran som virtuella käll datorer start flöden. Flera anslutningar till samma mål som den offentliga IP-adressen, IP-protokollet och mål Port kombinationen använder ytterligare en port per flöde.

När virtuella datorer gör utgående anslutningar i dessa undernät, anges käll portar på begäran och släpps efter att flödet har slutförts eller om tids gränsen för inaktivitet har uppnåtts. Varje offentlig IP-adress ger 55 000 SNAT-portar som ska användas och du kan skala ut med hjälp av flera offentliga IP-adresser. NAT-gatewayen försöker återanvända portar för flöden till olika mål för att öka skalningen för utgående anslutningar ytterligare. 

Du kan enkelt hantera flera arbets belastningar inom samma undernät.  Till skillnad från Azure Load Balancer utgående anslutning behöver du inte förallokera eller planera för det värsta tänkbara scenariot för en virtuell dators förbrukning av SNAT-portar. I stället planerar du för den totala mängden utgående anslutningar för alla virtuella datorer i konfigurerade undernät. Alla tillgängliga portar delas för alla instanser efter behov.

## <a name="timeout-and-tcp-resets"></a>Timeout och TCP återställs

NAT-gatewayen implementerar tids gränser för inaktivitet och skickar TCP-återställningar för flöden som inte finns.  Tids gränser kan konfigureras från 4 minuter (standard) till 120 minuter. Ett TCP-paket för varje port returneras till källan när det kommer till NAT-gatewayen och ingen matchande anslutning finns. När ett flöde har uppnått tids gränsen för inaktivitet, tas flödet bort från NAT-gatewayen och porten blir tillgänglig för nästa flöde som har upprättats. Om en TCP-anslutning till exempel har nått tids gränsen för inaktivitet och ytterligare paket visas för en tids gräns som redan nåtts, skickas en TCP-till källa till källan.  Ditt program kan använda TCP keepalive-meddelanden för att signalera och tillhandahålla synkronisering av slut punkts tillstånd vid behov.

## <a name="availability-zones"></a>Tillgänglighetszoner

NAT-gatewayer kan placeras i en speciell tillgänglighets zon om det behövs.  

En offentlig IP-zonindelade måste matcha samma tillgänglighets zon som NAT-gatewayen. Det offentliga IP-prefixet stöder inte tillgänglighets zoner och kan inte användas.

När en zonindelade NAT-gateway har kon figurer ATS justeras data planet för NAT-gatewayen med och isoleras till den begärda tillgänglighets zonen.

Du kan justera data planet för NAT-gatewayen med en virtuell dator i en speciell zon.  Du kan göra den här justeringen genom att skapa ett regionalt undernät som bara innehåller virtuella datorer i samma zon som den NAT-gateway som kon figurer ATS för under nätet.

Det går inte att ändra tillgänglighets zon placeringen för en NAT-gateway och du kan inte konvertera en NAT-gateway från regional till zonindelade eller zonindelade till regional.

## <a name="outbound-connection-service-comparison"></a>Jämförelse av utgående anslutnings tjänst

NAT-gateway och Load Balancer utgående anslutning är avsedda för olika scenarier. Enklare, allokering på begäran och virtuell nätverks nivå jämfört med pool-inriktad, per instans kornig het och mer komplicerade scenarier är viktiga inkonsekvenser.  Det finns också ett antal andra beteende skillnader och överväganden vid planering.

NAT-tjänsten är ett enklare sätt att konfigurera utgående anslutningar för virtuella nätverk i stället för poolbaserade konfigurationer för [standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) att använda [utgående regler](../load-balancer/load-balancer-outbound-rules-overview.md).  Du kan enkelt konfigurera och skala utgående anslutningar för ett undernät och uppnå scenarier som inte tidigare var möjligt.

NAT-tjänsten prioriteras för utgående SNAT för belastningsutjämnaren, men båda kan kombineras för ett inkommande och utgående scenario. Om du kombinerar inkommande insamlade scenarier med offentliga IP-adresser och belastnings Utjämnings slut punkter, kan du också dra nytta av "Secure som standard" för offentlig IP-adress och standard belastnings utjämning.  Endast efter att en NSG finns och tillåter trafik uttryckligen, kommer inkommande trafik tillåtas att flöda.

NAT-tjänsten kan skapa förutsägbara utgående anslutningar för det virtuella nätverket. Om allt du behöver är utgående anslutning behöver du inte konfigurera en belastningsutjämnare med utgående regler, ansluta datorer till poolen, beräkna och definiera SNAT-port tilldelning eller tilldela offentliga IP-adresser till enskilda datorer. Du kan delegera ansvaret för utgående anslutning till administratören för NAT-gateway-resursen i stället för-administratören för belastningsutjämnaren.

Med NAT-tjänsten kan du planera utgående anslutningar för hög belastnings skala för ett virtuellt nätverk eller undernät i, i stället för den högsta virtuella datorns instans skala. Du kan enkelt hantera ytterligare efter frågan genom att öka antalet offentliga IP-adresser dynamiskt för alla undernät i hela det virtuella nätverket. I stället för att beräkna och Omplanera belastningsutjämnaren för en SNAT-port, delar NAT-tjänsten SNAT-portar som är tillgängliga för utgående anslutningar och ger dem på begäran för att hantera burst eller inte enkelt förutsägbara arbets belastningar.  Load Balancer förallokerar ett specifikt antal SNAT-portar för en angiven virtuell dator instans.  

NAT-tjänsten returnerar alltid TCP reset-paket till avsändaren för icke-befintliga flöden (till exempel på grund av tids gräns för inaktivitet). Standard Load Balancer kan konfigureras för att aktivera [TCP-återställning vid inaktivitet](../load-balancer/load-balancer-tcp-reset.md), vilket skapar TCP-paket till båda slut punkterna för en anslutning vid tids gränsen för inaktivitet.

## <a name="regions"></a>Regioner

NAT-tjänsten är tillgänglig i dessa regioner
- USA, östra 2
- USA, västra centrala

## <a name="limitations"></a>Begränsningar

- NAT-tjänsten är inte kompatibel med grundläggande offentliga IP-eller grundläggande belastningsutjämnare i samma undernät.  De måste finnas i ett undernät som inte hanteras av en NAT-gateway.
- TCP-och UDP-baserade program protokoll stöds.
- NAT-gatewayer kan konfigureras i ett eller flera undernät i ett virtuellt nätverk.
- IPv6 stöds inte.
- Det offentliga IP-prefixet stöder inte zonindelade-placering och kan inte användas med en zonindelade NAT-gateway.

## <a name="next-steps"></a>Nästa steg

- [Ge feedback för NAT-tjänstens översikt](https://aka.ms/natuservoice) .
