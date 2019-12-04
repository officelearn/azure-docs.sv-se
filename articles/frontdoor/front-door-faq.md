---
title: Azure-tjänsten för front dörr – vanliga frågor och svar
description: Den här sidan innehåller svar på vanliga frågor om Azure-tjänsten för front dörr
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: fa9de74c89ba3f0351169f143146dc21b80ee666
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790540"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Vanliga frågor och svar om Azure-tjänsten för front dörr

I den här artikeln besvaras vanliga frågor om funktioner och funktioner i Azure frontend-tjänsten. Om du inte ser svaret på din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [UserVoice för Azure frontend-tjänsten](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft Support:** Om du vill skapa en ny supportbegäran går du till fliken **Hjälp** i Azure Portal, väljer **Hjälp + Support** -knappen och väljer sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-front-door-service"></a>Vad är Azure Front Door Service?

Azure frontend-tjänsten är en Application Delivery Network (och) som en tjänst som erbjuder olika belastnings Utjämnings funktioner i lager 7 för dina program. Den tillhandahåller en dynamisk webbplats acceleration (DSA) tillsammans med global belastnings utjämning med nästan real tids redundans. Tjänsten är en hög tillgänglig och skalbar tjänst som hanteras helt av Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Vilka funktioner stöder Azure-tjänsten för front dörr?

Azures frontend-tjänst stöder dynamisk webbplats acceleration (DSA), SSL-avlastning och slut punkt till slut punkt SSL, brand vägg för webb program, cookie-baserad sessionsgräns, URL-sökväg baserad routning, kostnads fria certifikat och flera domän hantering, och andra. En fullständig lista över funktioner som stöds finns i [Översikt över Azure frontend-tjänsten](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Vad är skillnaden mellan Azure frontend-tjänsten och Azure Application Gateway?

Både front dörren och Application Gateway är Layer 7-belastningsutjämnare (HTTP/HTTPS), den främsta skillnaden är att frontend-dörren är en global tjänst, medan Application Gateway är en regional tjänst. Även om en front dörr kan belastningsutjämna mellan dina olika skal enheter/kluster/Stamp-enheter över flera regioner kan du Application Gateway belastningsutjämna mellan dina virtuella datorer/behållare osv. i skalnings enheten.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>När bör vi distribuera en Application Gateway bakom en front dörr?

De viktiga scenarier som bör använda Application Gateway bakom front dörren är:

- Front dörren kan bara utföra Path-baserad belastnings utjämning på global nivå, men om en vill belastningsutjämna trafik ytterligare i det virtuella nätverket (VNET) bör de använda Application Gateway.
- Eftersom front dörren inte fungerar på en VM/container-nivå, så det går inte att tömma anslutningen. Application Gateway gör det dock möjligt att tömma anslutningarna. 
- Med en Application Gateway bakom AFD kan du uppnå 100% SSL-avläsning och bara dirigera HTTP-begäranden inom sitt virtuella nätverk (VNET).
- Främre dörren och Application Gateway både tillhörighet mellan sessioner. Även om front dörren kan dirigera efterföljande trafik från en användarsession till samma kluster eller Server del i en specifik region, kan Application Gateway dirigera tilldela trafiken till samma server i klustret.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kan vi Distribuera Azure Load Balancer bakom en front dörr?

Azure-tjänsten för front dörr behöver ett offentligt VIP-namn eller ett offentligt tillgängligt DNS-namn för att dirigera trafiken till. Att distribuera en Azure Load Balancer bakom en front dörr är ett vanligt användnings fall.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Vilka protokoll stöder Azure-tjänsten för front dörr?

Azure-tjänsten för front dörr stöder HTTP, HTTPS och HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Hur stöder Azure-tjänsten för front dörr HTTP/2?

Stöd för HTTP/2-protokoll är endast tillgängligt för klienter som ansluter till Azure-tjänsten för front dörr. Kommunikationen till Server delen i backend-poolen är över HTTP/1.1. HTTP/2-stöd är aktiverat som standard.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Vilka resurser stöds idag som en del av backend-poolen?

Backend-pooler kan bestå av lagring, webbappar, Kubernetes-instanser eller andra anpassade värd namn som har offentlig anslutning. Azure-tjänsten för front dörr kräver att Server delarna definieras antingen via en offentlig IP-adress eller ett offentligt matchat DNS-värdnamn. Medlemmar i backend-pooler kan vara mellan zoner, regioner eller till och med utanför Azure så länge de har offentlig anslutning.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner finns tjänsten i?

Azure frontend-tjänsten är en global tjänst som inte är kopplad till någon annan Azure-region. Den enda plats som du måste ange när du skapar en front dörr är resurs gruppens plats, som i princip anger var metadata för resurs gruppen ska lagras. Resursen front dörr skapas som en global resurs och konfigurationen distribueras globalt till alla pop (punkt för närvaro). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Vilka är POP-platserna för Azure frontend-tjänsten?

Azure-tjänsten för front dörr har samma lista med POP (Point of Presence)-platser som Azure CDN från Microsoft. En fullständig lista över våra pop- [platser finns Azure CDN pop-platser från Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Är Azure frontend service en dedikerad distribution för mitt program eller delas den mellan kunder?

Azure frontend-tjänsten är en globalt distribuerad tjänst för flera innehavare. Därför delas infrastrukturen för front dörren över alla sina kunder. Genom att skapa en profil för en frontend-profil definierar du dock den specifika konfiguration som krävs för ditt program och inga ändringar som gjorts i din front dörr påverkar andra konfigurations inställningar för front dörren.

### <a name="is-http-https-redirection-supported"></a>Stöds HTTP-> HTTPS-omdirigering?

Ja. I själva verket stöder Azure frontend-tjänsten värd, sökväg och omdirigering av frågesträngar samt en del av URL-omdirigeringen. Läs mer om [URL-omdirigering](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>I vilken ordning bearbetas routningsregler?

Vägar för din front dörr sorteras inte och en viss väg väljs utifrån den bästa matchningen. Läs mer om [hur front dörr matchar begär anden till en regel för routning](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hur gör jag för att du bara låsa åtkomsten till min server del till Azures front dörr?

Om du vill låsa ditt program så att det bara accepterar trafik från din specifika front dörr måste du konfigurera IP-ACL: er för Server delen och sedan begränsa uppsättningen godkända värden för rubriken "X-vidarebefordrad"-värd "som skickas av Azures front dörr. De här stegen beskrivs nedan:

- Konfigurera IP-ACLing för dina Server delar för att acceptera trafik från Azure-klientens Server dels IP-adressutrymme och Azures infrastruktur tjänster. Vi arbetar på att integrera med [Azure IP-intervall och service märken,](https://www.microsoft.com/download/details.aspx?id=56519) men för närvarande kan du referera till IP-intervallen enligt nedan:
 
    - Frontend-serverns **IPv4** -Server utrymme: `147.243.0.0/16`
    - IP-utrymme för **IPv6** -Server delen i front dörren: `2a01:111:2050::/44`
    - Azures [grundläggande infrastruktur tjänster](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) via virtualiserade värd-IP-adresser: `168.63.129.16` och `169.254.169.254`

    > [!WARNING]
    > Front dörrens IP-utrymme kan ändras senare, men vi kommer att se till att vi har integrerat med [Azure IP-intervall och service Taggar](https://www.microsoft.com/download/details.aspx?id=56519)innan det inträffar. Vi rekommenderar att du prenumererar på [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) för ändringar eller uppdateringar.

-   Filtrera på värdena för det inkommande huvudet "**X-forwarded-Host**", som skickats av front dörren. De enda tillåtna värdena för rubriken bör vara alla klient dels värdar som har definierats i din konfiguration av din front dörr. I själva verket är det bara de värdnamn för vilka du vill acceptera trafik från, på just den här server delen av din.
    - Exempel – låt oss säga att konfigurationen av den främre dörren har följande klient dels värdar _`contoso.azurefd.net`_ (a), _`www.contoso.com`_ (B), _ (C) och _`notifications.contoso.com`_ (D). Vi antar att du har två server delar X och Y. 
    - Server del X ska bara ta trafik från värdnamn A och B. backend Y kan ta trafik från A, C och D.
    - På Server sidan X bör du bara acceptera trafik som har värdet "**X-forwarded-Host**" inställd på antingen _`contoso.azurefd.net`_ eller _`www.contoso.com`_ . För allt annat ska Server del X avvisa trafiken.
    - På backend-sidan bör du på Server sidan bara acceptera trafik med rubriken "**X-forwarded-Host**" inställd på antingen _`contoso.azurefd.net`_ , _`api.contoso.com`_ eller _`notifications.contoso.com`_ . För allt annat bör backend Y avvisa trafiken.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan anycast-IP-förändring under hela front dörren?

Klient delens anycast-IP för din front dörr bör normalt inte ändras och kan vara statisk för livs längden för front dörren. Det finns dock **inga garantier** för samma. Skriv inte några direkta beroenden på IP-adressen.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Stöder Azure frontend-tjänsten statiska eller dedikerade IP-adresser?

Nej, Azure-frontend-tjänsten stöder för närvarande inte statisk eller dedikerad anycast-IP för klient del. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Har Azure frontend-tjänsten stöd för x-vidarebefordrade – för rubriker?

Ja, Azures frontend-tjänst stöder de X-vidarebefordrade-för-, X-vidarebefordrade-värd-och X-forwarded-proto-huvudena. För X-vidarebefordrad – för om huvudet redan fanns lägger front dörren till klientens socket-IP till den. Annars lägger den till rubriken med klientens socket-IP som värde. För X-vidarebefordrad-värd och X-forwarded-proto, åsidosätts värdet.

Läs mer om [HTTP-huvuden som stöds av frontend-dörren](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Hur lång tid tar det att distribuera en Azure-frontend-tjänst? Fungerar min front dörr fortfarande när den uppdateras?

En ny front dörr skapas eller alla uppdateringar av en befintlig front dörr tar cirka 3 till 5 minuter för global distribution. Det innebär att när du använder 3 till 5 minuter, distribueras din konfiguration av din front dörr över alla dina pop globalt.

Obs! anpassade uppdateringar av SSL-certifikatet tar cirka 30 minuter att distribueras globalt.

## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azures belastnings utjämning för front dörren eller dirigera trafik inom ett virtuellt nätverk?

Azures front dörr (AFD) kräver en offentlig IP-adress eller offentligt matchat DNS-namn för att dirigera trafik. Svaret är därför ingen AFD direkt kan inte dirigera inom ett virtuellt nätverk, men att använda en Application Gateway eller Azure Load Balancer mellan kommer att lösa det här scenariot.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Vilka är de olika tids gränserna och begränsningarna för Azure-tjänsten för front dörr?

Lär dig mer om alla dokumenterade [tids gränser och begränsningar för Azure-tjänsten för front dörr](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestanda

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Hur stöder Azure frontend service hög tillgänglighet och skalbarhet?

Azures frontend-tjänst är en globalt distribuerad plattform för flera innehavare med enorma volymer av kapacitet för att tillgodose ditt programs skalbarhets behov. Front dörren är en global belastnings Utjämnings funktion som gör att du kan redundansväxla hela programmet eller till och med enskilda mikrotjänster i regioner eller olika moln.

## <a name="ssl-configuration"></a>SSL-konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Vilka TLS-versioner stöds av tjänsten Azure frontend-dörr?

Alla profiler för front dörren som skapats efter september 2019 använder TLS 1,2 som standard minimum.

Frontend-dörren stöder TLS-versionerna 1,0, 1,1 och 1,2. TLS 1,3 stöds inte ännu.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Vilka certifikat stöds i Azure frontend-tjänsten?

Om du vill aktivera HTTPS-protokollet för säker leverans av innehåll på en anpassad domän i en annan dator, kan du välja att använda ett certifikat som hanteras av Azures tjänst för front dörr eller använda ditt eget certifikat.
Alternativet front dörr Managed tillhandahåller ett SSL-standardcertifikat via DigiCert och lagras i front dörrens Key Vault. Om du väljer att använda ditt eget certifikat kan du publicera ett certifikat från en certifikat utfärdare som stöds och det kan vara ett standard-SSL, ett utökat verifierings certifikat eller ett certifikat med jokertecken. Självsignerade certifikat stöds inte. Lär dig [hur du aktiverar HTTPS för en anpassad domän](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Stöder front dörren autorotation av certifikat?

För alternativet klient som hanteras av den främre dörren, roteras certifikaten autoefter front dörren. Om du använder ett certifikat från en front dörr och ser att utgångs datumet för certifikatet är mindre än 60 dagar, File a Support Ticket.
</br>Autorotation stöds inte för ditt eget anpassade SSL-certifikat. På samma sätt som när den konfigurerades första gången för en viss anpassad domän måste du peka fram sidan till rätt certifikat version i din Key Vault och kontrol lera att tjänstens huvud namn för front dörren fortfarande har åtkomst till Key Vault. Den här uppdaterade certifikat distributions åtgärden efter front dörren är atomisk och orsakar ingen produktions påverkan förutsatt att ämnes namnet eller SAN-nätverket för certifikatet inte ändras.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Vilka är de aktuella chiffersviter som stöds av Azure frontend-tjänsten?

Följande är de aktuella chiffersviter som stöds av tjänsten Azure frontend-dörr:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Azure frontend-tjänsten även Omkryptering av trafik till Server delen?

Ja, Azures frontend-tjänst har stöd för SSL-avlastning och slut punkt till slut punkt SSL, som krypterar trafiken till Server delen igen. Eftersom anslutningarna till Server delen sker över den offentliga IP-adressen rekommenderar vi i själva verket att du konfigurerar din front dörr att använda HTTPS som protokoll för vidarebefordran.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-principer för att kontrol lera SSL-protokoll versioner?

Du kan konfigurera en lägsta TLS-version i Azures front dörr via [Azure-REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). För närvarande kan du välja mellan 1,0 och 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan jag konfigurera front dörren till att bara stödja vissa chiffersviter?

Nej, det finns inte stöd för att konfigurera front dörren för speciella chiffersviter. 

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Vilka typer av mått och loggar är tillgängliga med Azure frontend-tjänsten?

Information om loggar och andra diagnostiska funktioner finns i [övervaka mått och loggar för front dörren](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostikloggar loggar till lagrings kontot för kunder och kunderna kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Azure-diagnostik för front dörrs tjänsten](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Hur gör jag för att hämta gransknings loggar för Azure frontend-tjänsten?

Gransknings loggar är tillgängliga för Azure-tjänsten för frontend-dörren. I portalen klickar du på **aktivitets logg** på Meny bladet för din front dörr för att få åtkomst till gransknings loggen. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Kan jag ställa in aviseringar med Azure frontend-tjänsten?

Ja, Azure-tjänsten för front dörr stöder aviseringar. Aviseringar har kon figurer ATS för mått. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
