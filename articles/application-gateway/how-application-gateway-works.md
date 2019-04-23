---
title: Så här fungerar en application gateway
description: Den här artikeln innehåller information om hur en application gateway fungerar
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008323"
---
# <a name="how-an-application-gateway-works"></a>Så här fungerar en application gateway

Den här artikeln beskriver hur en application gateway tar emot inkommande förfrågningar och skickar dem till serverdelen.

![Hur en application gateway tar emot en begäran](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hur en application gateway tar emot en begäran

1. Innan en klient skickar en begäran till en application gateway, löser domännamnet för application gateway med hjälp av en System DNS (Domain Name). DNS-posten styr i Azure eftersom alla application gateways finns i domänen azure.com.

2. Azure DNS returnerar IP-adressen till klienten, vilket är frontend-IP-adressen för application gateway.

3. Application gateway accepterar inkommande trafik på en eller flera lyssnare. En lyssnare är en logisk enhet som söker efter anslutningsbegäranden. Den är konfigurerad med en fronted IP-adress, protokoll och portnummer för anslutningar från klienter till application gateway.

4. Om en brandvägg för webbaserade program (WAF) används, kontrollerar application gateway begärandehuvudena och brödtext, om det finns mot WAF-regler. Den här åtgärden anger om begäran är ogiltigt eller ett säkerhetshot. Om förfrågan är giltig, dirigeras till serverdelen. Om begäran inte är giltig, blockeras den som ett säkerhetshot.

Azure Application Gateway kan användas som en belastningsutjämnare för interna program eller en internetuppkopplad belastningsutjämnare för programmet. En internet-ansluten application gateway använder offentliga IP-adresser. DNS-namnet på en internet-ansluten application gateway är offentligt matchat offentliga IP-adressen. Därför kan internet-riktade programgatewayer vidarebefordra klientbegäranden till internet.

Interna programgatewayer Använd endast privat IP-adresser. DNS-namnet på en intern application gateway är offentligt matchat till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till ett virtuellt nätverk för application gateway.

Både webbservergrupper på internet och interna application gateway dirigera begäranden till backend-servrar med hjälp av privata IP-adresser. Backend-servrarna behöver inte den offentliga IP-adresser att ta emot förfrågningar från en intern eller en internet-ansluten application gateway.

## <a name="how-an-application-gateway-routes-a-request"></a>Hur en application gateway dirigerar en databegäran

Om en begäran är giltig eller en WAF inte används, utvärderar application gateway en routningsregel för begäran som är associerat med lyssnaren. Den här åtgärden anger vilken backend-pool för att dirigera begäran till.

Regler bearbetas i den ordning som de är listade i portalen. Baserat på en routningsregel för begäran, application gateway avgör om du vill dirigera alla begäranden till en specifik serverdelspool-lyssnare för dirigera begäranden till olika serverdelspooler baserat på URL-sökvägen eller omdirigeringsbegäranden till en annan port eller en extern plats.

När application gateway väljer serverdelspoolen, skickar begäran till en felfri backend-servrar i poolen (y.y.y.y). Hälsotillståndet för servern bestäms av en hälsoavsökning. Om serverdelspoolen innehåller flera servrar, använder application gateway en algoritm för resursallokering för att dirigera begäranden mellan felfria servrar. Belastningsutjämning begäranden på servrarna.

När application gateway avgör backend-servern, öppnas en ny TCP-session med backend-servern baserat på HTTP-inställningar. HTTP-inställningarna anger protokollet, porten och andra routning-relaterade inställningar som krävs för att upprätta en ny session med backend-servern.

Port och protokoll som används i HTTP-inställningar avgör om trafiken mellan gatewayen och serverdelen programservrar krypteras (därför utföra slutpunkt till slutpunkt SSL) eller är okrypterad.

När en Programgateway skickar den ursprungliga begäran till backend-servern, godkänner eventuella anpassade konfigurationen som utförts i HTTP-inställningar som rör åsidosätta värdnamn, sökvägen och protokoll. Den här åtgärden sparas cookie-baserad sessionstillhörighet, anslutning tömning, värdnamn markeringen från serverdelen och så vidare.

En intern application gateway använder endast privata IP-adresser. DNS-namnet på en intern application gateway är matchas till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för application gateway.

 >[!NOTE]
 >Både webbservergrupper på internet och interna application gateway dirigera begäranden till backend-servrar med hjälp av privata IP-adresser. Den här åtgärden inträffar när din pool serverdelsresurs innehåller en privat IP-adress, VM NIC-konfiguration eller en internt matchas adress. Om backend-adresspool:
> - **Är en offentlig slutpunkt**, application gateway använder sin klientdel offentlig IP-adress för att nå servern. Om det inte finns en offentlig IP-adress för klientdel, tilldelas en utgående extern anslutning.
> - **Innehåller ett internt matchbar FQDN eller en privat IP-adress**, application gateway dirigerar begäran till backend-servern med dess privata IP-adresser för instansen.
> - **Innehåller en extern slutpunkt eller en externt matchbar FQDN**, application gateway dirigerar begäran till backend-servern med hjälp av dess offentliga IP-adress för klientdel. DNS-matchningen baseras på en privat DNS-zon eller anpassad DNS-server, om konfigurerad eller standard Azure-tillhandahållna DNS används. Om det inte finns en offentlig IP-adress för klientdel, tilldelas en utgående extern anslutning.

### <a name="modifications-to-the-request"></a>Ändringar av begäran

En Programgateway infogar fyra ytterligare rubriker som alla begäranden innan den vidarebefordrar begäranden till serverdelen. Dessa huvuden är x vidarebefordras för, x-vidarebefordrade-protokoll, x-vidarebefordrade-port och x-original-värd. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:port.

Giltiga värden för x-vidarebefordrade-protokoll är HTTP eller HTTPS. X-vidarebefordrade-port Anger den port där begäran nådde denna application gateway. Huvudet för X-original-värd innehåller ursprungliga värdhuvudet som begäran kom. Den här rubriken är användbart i Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen. Om sessionstillhörighet är aktiverat som ett alternativ, läggs en gatewayhanterade tillhörighetscookien.

Du kan konfigurera programgatewayen om du vill ändra rubriker med hjälp av [skriva om HTTP-huvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) eller ändra den URI-sökvägen med hjälp av en inställning för åsidosättning av sökvägen. Om inte konfigurerad för att göra det är dock alla inkommande begäranden via proxy till serverdelen.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om gateway-programkomponenter](application-gateway-components.md)
