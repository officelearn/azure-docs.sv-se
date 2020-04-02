---
title: Vad är Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Översikt över NAT-funktioner, resurser, arkitektur och implementering av virtuellt nätverk. Lär dig hur VIRTUAL Network NAT fungerar och hur du använder NAT-gatewayresurser i molnet.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 50fc8b9cefe88a80f3f954ce363139b6a4a38589
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548393"
---
# <a name="what-is-virtual-network-nat"></a>Vad är NAT för virtuellt nätverk?

Virtual Network NAT (översättning av nätverksadresser) förenklar internetanslutningen endast utgående för virtuella nätverk. När den konfigureras i ett undernät använder alla utgående anslutningar dina angivna statiska offentliga IP-adresser.  Utgående anslutning är möjlig utan belastningsutjämnare eller offentliga IP-adresser som är direkt kopplade till virtuella datorer. NAT är helt hanterad och mycket motståndskraftig.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="NAT för virtuellt nätverk">
</p>



*Bild: NAT för virtuellt nätverk*

## <a name="static-ip-addresses-for-outbound-only"></a>Statiska IP-adresser för endast utgående

Utgående anslutning kan definieras för varje undernät med NAT.  Flera undernät i samma virtuella nätverk kan ha olika NATs. Ett undernät konfigureras genom att ange vilken NAT-gatewayresurs som ska användas. Alla UDP- och TCP-utgående flöden från alla instanser av virtuella datorer använder NAT. 

NAT är kompatibelt med vanliga SKU-offentliga IP-adressresurser eller offentliga IP-prefixresurser eller en kombination av båda.  Du kan använda ett offentligt IP-prefix direkt eller distribuera prefixets offentliga IP-adresser över flera NAT-gatewayresurser. NAT kommer att träna all trafik till intervallet av IP-adresser för prefixet.  Alla IP-vitlistning av dina distributioner är nu enkelt.

All utgående trafik för undernätet bearbetas av NAT automatiskt utan någon kundkonfiguration.  Användardefinierade vägar är inte nödvändiga. NAT har företräde framför andra utgående scenarier och ersätter standardmålet för Internet för ett undernät.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT på begäran med flera IP-adresser för skala

NAT använder "portnätverksadressöversättning" (PNAT eller PAT) och rekommenderas för de flesta arbetsbelastningar. Dynamiska eller divergerande arbetsbelastningar kan enkelt rymmas med utgående flödesallokering på begäran. Omfattande förplanering, förallokering och slutligen överetablering av utgående resurser undviks. SNAT-portresurser delas och är tillgängliga i alla undernät med hjälp av en specifik NAT-gatewayresurs och tillhandahålls vid behov.

En offentlig IP-adress som är kopplad till NAT ger upp till 64 000 samtidiga flöden för UDP och TCP. Du kan börja med en enda IP-adress och skala upp till 16 offentliga IP-adresser.

NAT gör att flöden kan skapas från det virtuella nätverket till Internet. Returtrafik från Internet är endast tillåten som svar på ett aktivt flöde.

Till skillnad från utgående SNAT har NAT inga begränsningar för vilken privat IP för en instans för virtuella datorer kan skapa utgående anslutningar.  Sekundära IP-konfigurationer kan skapa utgående Internet-anslutning med NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Samexistens mellan inkommande och utgående

NAT är kompatibelt med följande standard-SKU-resurser:

- Lastbalanserare
- Offentlig IP-adress
- Offentligt IP-prefix

När de används tillsammans med NAT tillhandahåller dessa resurser inkommande Internet-anslutning till dina undernät. NAT tillhandahåller all utgående Internetanslutning från undernäten.

NAT och kompatibla Standard SKU-funktioner är medvetna om i vilken riktning flödet startades. Inkommande och utgående scenarier kan samexistera. Dessa scenarier får rätt översättningar av nätverksadresser eftersom dessa funktioner är medvetna om flödesriktningen. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="NAT-flödesriktning för virtuellt nätverk">
</p>

*Bild: NAT-flödesriktning för virtuellt nätverk*

## <a name="fully-managed-highly-resilient"></a>Fullt förvaltad, mycket motståndskraftig

NAT är helt skalas ut från början. Det krävs ingen ramp upp eller utskalning.  Azure hanterar driften av NAT åt dig.  NAT har alltid flera feldomäner och kan hantera flera fel utan avbrott i tjänsten.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP-återställning för okända flöden

Den privata sidan av NAT skickar TCP Reset-paket för försök att kommunicera på en TCP-anslutning som inte finns. Ett exempel är anslutningar som har nått tidsgränsen för inaktiv tidsutskrift. Nästa mottagna paket returnerar en TCP-återställning till den privata IP-adressen för att signalera och tvinga anslutningsstängningen.

Den offentliga sidan av NAT genererar inte TCP Reset-paket eller annan trafik.  Endast trafik som produceras av kundens virtuella nätverk avges.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurerbar tidsgränsen för inaktiv TCP-inaktiv

En standardtidsutgång för TCP-inaktiv på 4 minuter används och kan ökas till upp till 120 minuter. All aktivitet i ett flöde kan också återställa inaktiv timer, inklusive TCP keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regional isolering eller zonisolering med tillgänglighetszoner

NAT är regionalt som standard. När du skapar [scenarier för tillgänglighetszoner](../availability-zones/az-overview.md) kan NAT isoleras i en viss zon (zondistribution).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="NAT för virtuellt nätverk med tillgänglighetszoner">
</p>

*Bild: Nat för virtuellt nätverk med tillgänglighetszoner*

## <a name="multi-dimensional-metrics-for-observability"></a>Flerdimensionella mätvärden för observerbarhet

Du kan övervaka hur din NAT fungerar via flerdimensionella mått som exponeras i Azure Monitor. Dessa mått kan användas för att observera användningen och för felsökning.  NAT-gatewayresurser visar följande mått:
- Byte
- Paket
- Tappade paket
- Totalt antal SNAT-anslutningar
- SNAT-anslutningstillståndsövergångar per intervall.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Vid allmän tillgänglighet är NAT-datasökvägen minst 99,9 % tillgänglig.


## <a name="pricing"></a>Prissättning

NAT-gatewayen faktureras med två separata mätare:

| Mätare | Pris |
| --- | --- |
| Resurstimmar | $0.045/timme |
| Bearbetade data | $0.045/GB |

Resurstimmar konton för den varaktighet under vilken en NAT-gatewayresurs finns.
Data som bearbetas konton för all trafik som bearbetas av en NAT-gatewayresurs.

## <a name="availability"></a>Tillgänglighet

Virtual Network NAT och NAT-gatewayresursen är [regions](https://azure.microsoft.com/global-infrastructure/regions/)tillgängliga i alla offentliga Azure-molnregioner .

## <a name="support"></a>Support

NAT stöds via normala supportkanaler.

## <a name="feedback"></a>Feedback

Vi vill veta hur vi kan förbättra servicen. Föreslå och rösta om vad vi ska bygga nästa på [UserVoice för NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Begränsningar

* NAT är kompatibelt med vanliga offentliga IP-, offentliga IP-prefix- och belastningsutjämnareresurser. Grundläggande resurser, till exempel grundläggande belastningsutjämnare, och alla produkter som härleds från dem är inte kompatibla med NAT.  Grundläggande resurser måste placeras i ett undernät som inte har konfigurerats med NAT.
* IPv4-adressfamilj stöds.  NAT interagerar inte med IPv6-adressfamiljen.  NAT kan inte distribueras i ett undernät med ett IPv6-prefix.
* NSG-flödesloggning stöds inte när NAT.NSG flow logging isn't supported when using NAT.
* NAT kan inte sträcka sig över flera virtuella nätverk.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
* [Berätta vad du ska bygga härnäst för VIRTUELLT NÄTVERK NAT i UserVoice](https://aka.ms/natuservoice).

