---
title: Så här fungerar en programgateway
description: Den här artikeln innehåller information om hur en programgateway accepterar inkommande begäranden och dirigerar dem till serverings-servergången.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132971"
---
# <a name="how-an-application-gateway-works"></a>Så här fungerar en programgateway

I den här artikeln beskrivs hur en programgateway accepterar inkommande begäranden och dirigerar dem till serverings-servergången.

![Så här accepterar en programgateway en begäran](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Så här accepterar en programgateway en begäran

1. Innan en klient skickar en begäran till en programgateway matchas domännamnet för programgatewayen med hjälp av en DNS-server (Domain Name System). Azure styr DNS-posten eftersom alla programgateways finns i azure.com domänen.

2. Azure DNS returnerar IP-adressen till klienten, som är klientens IP-adress för programgatewayen.

3. Programgatewayen accepterar inkommande trafik på en eller flera lyssnare. En lyssnare är en logisk entitet som söker efter anslutningsbegäranden. Den är konfigurerad med en klient-IP-adress, protokoll och portnummer för anslutningar från klienter till programgatewayen.

4. Om en brandvägg för webbprogram (WAF) används kontrollerar programgatewayen begäranhuvudena och brödtexten, om sådana finns, mot WAF-regler. Den här åtgärden avgör om begäran är giltig begäran eller ett säkerhetshot. Om begäran är giltig dirigeras den till backend. Om begäran inte är giltig och WAF är i förebyggande läge blockeras den som ett säkerhetshot. Om den är i identifieringsläge utvärderas och loggas begäran, men vidarebefordras fortfarande till serverfördsservern.

Azure Application Gateway kan användas som en intern programbelastningsutjämnare eller som en internetinriktad programbelastningsutjämnare. En internetinriktad programgateway använder offentliga IP-adresser. DNS-namnet på en internetinriktad programgateway kan lösas offentligt till dess offentliga IP-adress. Som ett resultat kan internetvända programgateways dirigera klientförfrågningar till Internet.

Interna programgateways använder endast privata IP-adresser. Om du använder en anpassad eller [privat DNS-zon](https://docs.microsoft.com/azure/dns/private-dns-overview)ska domännamnet vara internt resolvable till den privata IP-adressen för Application Gateway. Därför kan interna belastningsutjämningsenheter endast dirigera begäranden från klienter med åtkomst till ett virtuellt nätverk för programgatewayen.

## <a name="how-an-application-gateway-routes-a-request"></a>Hur en programgateway dirigerar en begäran

Om en begäran är giltig och inte blockerad av WAF utvärderar programgatewayen den routningsregel för begäran som är associerad med lyssnaren. Den här åtgärden avgör vilken backend-pool som begäran ska dirigeras till.

Baserat på routningsregeln för begäran avgör programgatewayen om alla begäranden på lyssnaren ska dirigeras till en viss backend-pool, dirigera begäranden till olika backend-pooler baserat på URL-sökvägen eller omdirigera begäranden till en annan port eller extern plats.
>[!NOTE]
>Regler bearbetas i den ordning de visas i portalen för v1 SKU. 

När programgatewayen väljer servergruppspoolen skickas begäran till en av de felfria servergruppsservrarna i poolen (y.y.y.y). Serverns hälsotillstånd bestäms av en hälsoavsökning. Om serverda poolen innehåller flera servrar använder programgatewayen en algoritm för avrundning för att dirigera begäranden mellan felfria servrar. Den här belastningen balanserar begäranden på servrarna.

När programgatewayen har fastställt serveråtkomstservern öppnas en ny TCP-session med servergången för server för servergrupp baserat på HTTP-inställningar. HTTP-inställningar anger protokoll, port och andra routningsrelaterade inställningar som krävs för att upprätta en ny session med serverdaservern.

Porten och protokollet som används i HTTP-inställningarna avgör om trafiken mellan programgateway- och serverdelsservrarna är krypterad (vilket åstadkommer TLS från slutpunkt till slutpunkt) eller är okrypterad.

När en programgateway skickar den ursprungliga begäran till serverdservern, respekterar den alla anpassade konfigurationer som görs i HTTP-inställningarna för att åsidosätta värdnamnet, sökvägen och protokollet. Den här åtgärden upprätthåller cookie-baserad sessionstillhörighet, anslutningsdränning, värdnamnsval från backend och så vidare.

 >[!NOTE]
>Om backend poolen:
> - **Är en offentlig slutpunkt**använder programgatewayen sin offentliga IP för frontend för att nå servern. Om det inte finns en offentlig IP-adress för frontend tilldelas en för den utgående externa anslutningen.
> - **Innehåller en internt resolvable FQDN eller en privat IP-adress**, dirigerar programmet gateway begäran till servergruppsservern med hjälp av dess instans privata IP-adresser.
> - **Innehåller en extern slutpunkt eller en externt resolvable FQDN**, dirigerar programmet gateway begäran till serverdelsservern med hjälp av dess frontend offentliga IP-adress. DNS-lösningen baseras på en privat DNS-zon eller anpassad DNS-server, om den är konfigurerad, eller använder standard-Azure-tillerkännahållen DNS. Om det inte finns en offentlig IP-adress för frontend tilldelas en för den utgående externa anslutningen.

### <a name="modifications-to-the-request"></a>Ändringar av begäran

En programgateway infogar ytterligare fyra rubriker till alla begäranden innan begäranden vidarebefordras till backend. Dessa rubriker är x-forwarded-for, x-forwarded-proto, x-forwarded-port och x-original-host. Formatet för x-forwards-for header är en kommaavgränsad lista över IP:port.

Giltiga värden för x-forwards-proto är HTTP eller HTTPS. X-vidarebefordrad port anger den port där begäran nådde programgatewayen. X-original-värdhuvudet innehåller det ursprungliga värdhuvudet som begäran har anlänt med. Det här huvudet är användbart i Azure-webbplatsintegrering, där det inkommande värdhuvudet ändras innan trafiken dirigeras till serverd. Om sessionstillhörighet är aktiverat som ett alternativ läggs en gateway-hanterad tillhörighetscookie till.

Du kan konfigurera programgatewayen för att ändra rubriker genom att skriva [om HTTP-huvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) eller ändra URI-sökvägen med hjälp av en vägspärrinställning. Men om inte konfigureras för att göra det, är alla inkommande begäranden proxied till serverdelen.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om komponenter för programgateway](application-gateway-components.md)
