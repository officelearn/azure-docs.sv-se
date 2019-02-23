---
title: Hur Azure Application Gateway fungerar
description: Den här artikeln innehåller information om hur du Application Gateway fungerar
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d44bb4d3d0ff625d071c87fd39b3776915a6198f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675820"
---
# <a name="how-application-gateway-works"></a>Hur fungerar Application Gateway

Application Gateway är ett moln Application Deliver Controller med layer 7 (HTTP) belastningsutjämning som hjälper dig att hantera Internet-trafik mellan dina servrar. Dessutom finns även funktioner för Web Application Firewall (WAF) som tillhandahåller centraliserat skydd för dina webbtjänster mot vanliga sårbarheter på webben och säkerhetsproblem.

När en klient gör en HTTP-begäran till Application Gateway, fungerar som en omvänd proxy och vidarebefordrar trafik till backend-servrarna utifrån din konfiguration. Dessutom Application Gateway även hälsan hos dess backend-servrarna och ser till att den dirigerar trafik endast till felfria serverdelar.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Hur-begäran har godkänts

Innan en klient skickar en begäran till din Application Gateway, löser domännamn för Application Gateway med hjälp av ett System DNS (Domain Name)-server. DNS-posten styrs av Azure, eftersom Application Gateways finns i domänen azure.com. Azure DNS returnerar IP-adressen till klienten, vilket är den *klientdelens IP-adress* för Programgatewayen. Application Gateway tar emot inkommande trafik på en eller flera *lyssnare*. En lyssnare är en process som söker efter anslutningsbegäranden. Den är konfigurerad med en fronted IP-adress, protokoll och portnummer för anslutningar från klienter till Application Gateway. Om WAF är aktiverad, Application Gateway kontrollerar begärandehuvudena och brödtexten (om tillgängligt) mot den *WAF-regler* för att avgöra om begäran är en giltig begäran – i vilket fall kommer den att dirigeras till backend- eller ett säkerhetshot i då begäran kommer att blockeras.  

## <a name="how-request-is-routed"></a>Hur begäran dirigeras

Om begäran hittas ska vara giltigt (eller om WAF inte är aktiverad), *begär routningsregel* som är associerade med den *lyssnare* utvärderas för att fastställa den *serverdelspool* till som begäran är ska dirigeras. Regler bearbetas i den ordning de visas i portalen. Utifrån den *begär routningsregel* konfiguration, Application Gateway avgör om du vill dirigera alla begäranden till en specifik serverdelspool-lyssnare för eller att dirigera dem till olika serverdelspooler beroende på URL-sökvägen eller *omdirigera begäranden* till en annan port eller extern webbplats

När en *serverdel* *pool* har valts kommer Application Gateway skickar en begäran till en av backend-servrarna konfigurerats i poolen som är felfri (y.y.y.y). Hälsotillståndet för servern bestäms av en *hälsoavsökning*. Om serverdelspoolen innehåller fler än en server, använder Application Gateway algoritmen (round robin) för att dirigera begäranden mellan felfria servrar, vilket belastningsutjämning begäranden på servrarna.

När en backend-servern har fastställts, Application Gateway öppnas en ny TCP-session med backend-servern baserat på konfigurationen i den *HTTP-inställningar*. Den *HTTP-inställningar* är en komponent som anger protokollet, porten och andra routning-relaterade konfiguration som krävs för att upprätta en ny session med backend-servern. Port och protokoll som används i HTTP-inställningarna avgör om trafiken mellan Application Gateway och backend-servrarna är krypterad därför utföra slutpunkt till slutpunkt SSL eller okrypterade. När den ursprungliga begäran till backend-servern, godkänner Application Gateway eventuella anpassade konfigurationen som utförts i HTTP-inställningar som rör åsidosätta värdnamn, sökväg, protokoll. underhålla cookie-baserad sessionstillhörighet, Anslutningstömning, väljer du värdnamnet från serverdel, osv.

Application gateway också infogar dessa tre standard HTTP-* huvuden: x-vidarebefordrade-för x-vidarebefordrade-protokoll och x-vidarebefordrade-port i begäran vidarebefordras till serverdelen.

När backend-servern bearbetar begäran och skickar en HTTP-svar med sidinnehållet till Application Gateway, vidarebefordrar Application Gateway svar till klienten där sidan visas i webbläsaren.

## <a name="application-load-balancing-type"></a>Belastningsutjämning programtyp

Du kan använda Application Gateway som en belastningsutjämnare för interna program eller en internetuppkopplad belastningsutjämnare för programmet. En Internet-ansluten Application Gateway har offentliga IP-adresser. DNS-namnet på en Internet-ansluten Application Gateway är offentligt matchat offentliga IP-adressen. Därför kan Internet-riktade Application Gateways vidarebefordra begäranden från klienter via Internet.

En intern Application-Gateway har endast privat IP-adress. DNS-namnet på en intern Application-Gateway är offentligt matchat till dess privata IP-adress. Interna belastningsutjämnare kan därför bara vidarebefordra begäranden från klienter med åtkomst till det virtuella nätverket för Application Gateway.

Observera att både webbservergrupper på Internet och interna Application Gateway dirigera begäranden till ditt backend-servrar med hjälp av privata IP-adresser. Backend-servrarna måste därför inte offentliga IP-adresser för att ta emot begäranden från en intern eller en Internet-ansluten Application Gateway.

## <a name="next-steps"></a>Nästa steg

När du läst om hur Application Gateway fungerar, går du till [Gateway programkomponenter](application-gateway-components.md) att förstå dess komponenter i mer information.
