---
title: Hur Azure Application Gateway fungerar
description: Den här artikeln innehåller information om hur du Application Gateway fungerar
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905791"
---
# <a name="how-application-gateway-works"></a>Hur fungerar Application Gateway

Den här artikeln förklarar hur application gateway tar emot inkommande förfrågningar och skickar dem till serverdelen.

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Hur en begäran har godkänts

Innan en klient skickar en begäran till din application gateway, löser domännamn för application gateway med hjälp av ett System DNS (Domain Name)-server. DNS-posten styrs av Azure, eftersom din programgatewayer finns i domänen azure.com. Azure DNS returnerar IP-adressen till klienten, vilket är den *klientdelens IP-adress* för Programgatewayen. Application gateway tar emot inkommande trafik på en eller flera *lyssnare*. En lyssnare är en logisk enhet som söker efter anslutningsbegäranden. Den är konfigurerad med en fronted IP-adress, protokoll och portnummer för anslutningar från klienter till application gateway. Om Web Application Firewall (WAF) är aktiverad, Application Gateway kontrollerar begärandehuvudena och brödtexten (om tillgängligt) mot den *WAF-regler* för att avgöra om begäran är en giltig begäran – i vilket fall kommer den att dirigeras till den backend – eller ett säkerhetshot, i vilket fall begäran kommer att blockeras.  

Application gateway kan användas som en belastningsutjämnare för interna program eller en internetuppkopplad belastningsutjämnare för programmet. En Internet-ansluten application gateway har offentliga IP-adresser. DNS-namnet på en Internet-ansluten application gateway är offentligt matchat offentliga IP-adressen. Därför kan Internet-riktade programgatewayer vidarebefordra begäranden från klienter via Internet. Intern programgatewayerna har endast privat IP-adress. DNS-namnet på en intern application gateway är offentligt matchat till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för application gateway. Både webbservergrupper på Internet och interna Application Gateway dirigera begäranden till ditt backend-servrar med hjälp av privata IP-adresser. Backend-servrarna måste därför inte offentliga IP-adresser för att ta emot begäranden från en intern eller en Internet-ansluten Application Gateway.

## <a name="how-a-request-is-routed"></a>Hur en begäran dirigeras

Om begäran hittas ska vara giltigt (eller om WAF inte är aktiverad), *begär routningsregel* som är associerade med den *lyssnare* utvärderas för att fastställa den *serverdelspool* till som begäran är ska dirigeras. Regler bearbetas i den ordning de visas i portalen. Utifrån den *begär routningsregel* konfiguration, application gateway avgör om du vill dirigera alla begäranden till en specifik serverdelspool-lyssnare för eller att dirigera dem till olika serverdelspooler beroende på URL-sökvägen eller *omdirigera begäranden* till en annan port eller extern webbplats

När en *serverdel* *pool* har valts kommer Programgateway skickar en begäran till en av backend-servrarna konfigurerats i poolen som är felfri (y.y.y.y). Hälsotillståndet för servern bestäms av en *hälsoavsökning*. Om serverdelspoolen innehåller fler än en server, använder Programgateway algoritmen (round robin) för att dirigera begäranden mellan felfria servrar, vilket belastningsutjämning begäranden på servrarna.

När en backend-servern har fastställts, Programgateway öppnas en ny TCP-session med backend-servern baserat på konfigurationen i den *HTTP-inställningar*. Den *HTTP-inställningar* är en komponent som anger protokollet, porten och andra routning-relaterade konfiguration som krävs för att upprätta en ny session med backend-servern. Port och protokoll som används i HTTP-inställningarna avgör om trafiken mellan gatewayen och serverdelen programservrar krypteras, därför utföra slutpunkt till slutpunkt SSL eller okrypterade. När den ursprungliga begäran till backend-servern, godkänner Programgateway eventuella anpassade konfigurationen som utförts i HTTP-inställningar som rör åsidosätta värdnamn, sökväg, protokoll. underhålla cookie-baserad sessionstillhörighet, Anslutningstömning, väljer du värdnamnet från serverdel, osv.

En intern Application-Gateway har endast privat IP-adress. DNS-namnet på en intern Application-Gateway är internt matchas till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för Application Gateway.

Om poolen innehåller ett internt matchbar FQDN eller en privat IP-adress, dirigerar Application Gateway begäran till backend-servern med dess privata IP-adresser för instansen. Om poolen innehåller en extern slutpunkt eller en externt matchbar FQDN, dirigerar Application Gateway begäran till backend-servern med dess offentliga IP-adress för klientdel. DNS-matchningen baseras på en privat DNS-zon eller en anpassad DNS-server om konfigurerat eller det tar standardalternativet av DNS i Azure. Om du inte har etablerat en offentlig IP-adress för klientdel, tilldelas en utgående extern anslutning.

### <a name="modifications-to-the-request"></a>Ändringar av begäran

Programgateway infogar 4 ytterligare rubriker som alla begäranden innan den vidarebefordrar begäranden till serverdelen. Dessa huvuden är X vidarebefordras för, X-vidarebefordrade-protokoll, X-vidarebefordrade-port och X-original-värd. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:port. Giltiga värden för x-vidarebefordrade-protokoll är HTTP eller HTTPS. X-vidarebefordrade-port Anger den port som nått begäran vid application gateway. Huvudet för X-original-värd innehåller ursprungliga värdhuvudet som begäran kom. Den här rubriken är användbart i scenarier som Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen. Du kan också om sessionstillhörighet är aktiverat infogas sedan ett gateway-hanterade tillhörighetscookien. 

Du kan dessutom konfigurera programgatewayen om du vill ändra rubriker med hjälp av [skriva om HTTP-huvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) eller ändra URI-sökväg med hjälp av sökvägen åsidosättning inställningen. Men om inte konfigurerad för att göra det, alla inkommande begäranden är via proxy skick till serverdelen.


## <a name="next-steps"></a>Nästa steg

När du läst om hur application gateway fungerar, se [gateway programkomponenter](application-gateway-components.md) att förstå dess komponenter i mer information.
