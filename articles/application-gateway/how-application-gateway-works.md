---
title: Hur Azure Application Gateway fungerar
description: Den här artikeln innehåller information om hur du Application Gateway fungerar
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d37114fda7f442a5fa077c8dde9fd8aec3ac4378
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317109"
---
# <a name="how-application-gateway-works"></a>Hur fungerar Application Gateway

Den här artikeln förklarar hur application gateway tar emot inkommande förfrågningar och skickar dem till serverdelen.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Hur-begäran har godkänts

Innan en klient skickar en begäran till din application gateway, löser domännamn för application gateway med hjälp av ett System DNS (Domain Name)-server. DNS-posten styrs av Azure, eftersom din programgatewayer finns i domänen azure.com. Azure DNS returnerar IP-adressen till klienten, vilket är den *klientdelens IP-adress* för Programgatewayen. Application gateway tar emot inkommande trafik på en eller flera *lyssnare*. En lyssnare är en logisk enhet som söker efter anslutningsbegäranden. Den är konfigurerad med en fronted IP-adress, protokoll och portnummer för anslutningar från klienter till application gateway. Om Web Application Firewall (WAF) är aktiverad, Application Gateway kontrollerar begärandehuvudena och brödtexten (om tillgängligt) mot den *WAF-regler* för att avgöra om begäran är en giltig begäran – i vilket fall kommer den att dirigeras till den backend – eller ett säkerhetshot, i vilket fall begäran kommer att blockeras.  

Application gateway kan användas som en belastningsutjämnare för interna program eller en internetuppkopplad belastningsutjämnare för programmet. En Internet-ansluten application gateway har offentliga IP-adresser. DNS-namnet på en Internet-ansluten application gateway är offentligt matchat offentliga IP-adressen. Därför kan Internet-riktade programgatewayer vidarebefordra begäranden från klienter via Internet. Intern programgatewayerna har endast privat IP-adress. DNS-namnet på en intern application gateway är offentligt matchat till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för application gateway. Både webbservergrupper på Internet och interna Application Gateway dirigera begäranden till ditt backend-servrar med hjälp av privata IP-adresser. Backend-servrarna måste därför inte offentliga IP-adresser för att ta emot begäranden från en intern eller en Internet-ansluten Application Gateway.

## <a name="how-request-is-routed"></a>Hur begäran dirigeras

Om begäran hittas ska vara giltigt (eller om WAF inte är aktiverad), *begär routningsregel* som är associerade med den *lyssnare* utvärderas för att fastställa den *serverdelspool* till som begäran är ska dirigeras. Regler bearbetas i den ordning de visas i portalen. Utifrån den *begär routningsregel* konfiguration, application gateway avgör om du vill dirigera alla begäranden till en specifik serverdelspool-lyssnare för eller att dirigera dem till olika serverdelspooler beroende på URL-sökvägen eller *omdirigera begäranden* till en annan port eller extern webbplats

När en *serverdel* *pool* har valts kommer Programgateway skickar en begäran till en av backend-servrarna konfigurerats i poolen som är felfri (y.y.y.y). Hälsotillståndet för servern bestäms av en *hälsoavsökning*. Om serverdelspoolen innehåller fler än en server, använder Programgateway algoritmen (round robin) för att dirigera begäranden mellan felfria servrar, vilket belastningsutjämning begäranden på servrarna.

När en backend-servern har fastställts, Programgateway öppnas en ny TCP-session med backend-servern baserat på konfigurationen i den *HTTP-inställningar*. Den *HTTP-inställningar* är en komponent som anger protokollet, porten och andra routning-relaterade konfiguration som krävs för att upprätta en ny session med backend-servern. Port och protokoll som används i HTTP-inställningarna avgör om trafiken mellan gatewayen och serverdelen programservrar krypteras, därför utföra slutpunkt till slutpunkt SSL eller okrypterade. När den ursprungliga begäran till backend-servern, godkänner Programgateway eventuella anpassade konfigurationen som utförts i HTTP-inställningar som rör åsidosätta värdnamn, sökväg, protokoll. underhålla cookie-baserad sessionstillhörighet, Anslutningstömning, väljer du värdnamnet från serverdel, osv.

En intern Application-Gateway har endast privat IP-adress. DNS-namnet på en intern Application-Gateway är internt matchas till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för Application Gateway.

Observera att både webbservergrupper på Internet och interna Application Gateway dirigera begäranden till ditt backend-servrar med hjälp av privata IP-adresser. Om din pool serverdelsresurs innehåller en privat IP-adress, VM NIC-konfiguration eller en internt matchas adress och om backend-pool är en offentlig slutpunkt kan Application Gateway använder sin klientdel offentlig IP-adress för att nå servern. Om du inte har etablerat en offentlig IP-adress för klientdel, tilldelas en utgående extern anslutning.


## <a name="next-steps"></a>Nästa steg

När du läst om hur application gateway fungerar, se [gateway programkomponenter](application-gateway-components.md) att förstå dess komponenter i mer information.
