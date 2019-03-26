---
title: Azure ytterdörren Service – vanliga frågor om åtkomsten | Microsoft Docs
description: Den här sidan innehåller svar på vanliga frågor och svar om Azure ytterdörren Service
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
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407759"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Vanliga frågor och svar för Azure ytterdörren Service

Den här artikeln får du svar på vanliga frågor om Azure ytterdörren Service och funktioner. Om du inte ser svar på din fråga, kan du kontakta oss via följande kanaler (i ständigt växande ordning):

1. Kommentarer i den här artikeln.
2. [Azure ytterdörren Service UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft Support:** Att skapa en ny supportbegäran i Azure-portalen på den **hjälpa** fliken den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-front-door-service"></a>Vad är Azure Front Door Service?

Azure ytterdörren Service är en Application Delivery Network (ADN) som en tjänst erbjuder olika layer 7-belastningsutjämning funktioner för dina program. Det ger acceleration av dynamisk webbplats (DSA) tillsammans med globala Utjämning av nätverksbelastning med nästan i realtid redundans. Det är en mycket tillgänglig och skalbar tjänst som hanteras fullständigt av Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Vilka funktioner stöder Azure ytterdörren Service?

Azure ytterdörren Service stöder acceleration av dynamisk webbplats (DSA), SSL-avlastning och slutpunkt till slutpunkt SSL, Brandvägg för webbaserade program, Cookiebaserad sessionstillhörighet, url-sökvägsbaserad routning, kostnadsfri certifikat och flera domänhantering och annat. En fullständig lista över funktioner som stöds finns i [översikt för Azure ytterdörren Service](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Vad är skillnaden mellan Azure ytterdörren Service och Azure Application Gateway?

Även om både ytterdörren och Application Gateway är layer 7 belastningsutjämnare för (HTTP/HTTPS), är den viktigaste skillnaden att åtkomsten är en global tjänst Application Gateway är en regional tjänst. Medan ytterdörren kan belastningsutjämna mellan dina olika skalningsenheter enheter/kluster/stämpel i olika regioner, kan du belastningsutjämna mellan dina virtuella datorer/behållare etc. som ligger inom skalningsenheten Application Gateway.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>När ska vi distribuera en Application Gateway bakom åtkomsten?

Viktiga scenarier varför en ska använda Application Gateway bakom åtkomsten är:

- Åtkomsten kan utföra sökvägsbaserad belastningsutjämning bara på global nivå men om någon vill läsa in belastningsutjämna trafik ytterligare i sina virtuella nätverk (VNET) och sedan de ska använda Application Gateway.
- Eftersom ytterdörren inte fungerar på en virtuell dator/container-nivå, så den kan inte göra Anslutningstömning. Application Gateway kan du göra Anslutningstömning. 
- Med Application Gateway bakom AFD uppnå en 100% SSL-avlastning och dirigera endast HTTP-begäranden i sina virtuella nätverk (VNET).
- Ytterdörren och Application Gateway stöder sessionstillhörighet. Medan ytterdörren kan dirigera efterföljande trafik från en användarsession till samma kluster eller serverdelen i en viss region, kan Application Gateway direkt tilldela trafiken till samma server i klustret.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kan vi distribuera Azure Load Balancer bakom åtkomsten?

Azure ytterdörren tjänsten måste en offentlig VIP eller ett offentligt tillgängliga DNS-namn för att dirigera trafiken till. Distribuera en Azure Load Balancer bakom ytterdörren är ett vanligt användningsfall.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Vilka protokoll som stöder Azure ytterdörren Service?

Azure ytterdörren Service stöder HTTP, HTTPS och HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Hur stöder Azure ytterdörren Service HTTP/2?

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till Azure ytterdörren Service endast. Kommunikation till serverdelar i serverdelspoolen är över HTTP/1.1. Stöd för HTTP/2 är aktiverat som standard.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Vilka resurser som stöds i dag som en del av backend-pool?

Serverdelspooler kan bestå av lagring, Web App, Kubernetes instanser eller andra anpassade värdnamnet som har offentlig anslutning. Azure ytterdörren-tjänsten kräver att serverdelar definieras via en offentlig IP-adress eller ett offentligt matchat DNS-värdnamn. Medlemmar i serverdelspooler kan vara i olika zoner, regioner, och även utanför Azure så länge som de har offentlig anslutning.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner är tillgängliga i tjänsten?

Azure ytterdörren-tjänsten är en global tjänst och är inte kopplat till någon specifik Azure-region. Den enda plats måste du ange när du skapar en ytterdörren är resursgruppens plats, vilket är i princip som anger var metadata för resursgruppen kommer att lagras. Själva ytterdörren resursen skapas som en global resurs och konfigurationen distribueras globalt till alla POP (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Vad är de POP-platserna för Azure ytterdörren Service?

Azure ytterdörren-tjänsten har samma lista över närvaropunkter (Point of Presence) som Azure CDN från Microsoft. Den fullständiga listan med vår POP, finns [Azure CDN POP-platser från Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Är Azure ytterdörren Service en dedikerad distribution för mitt program eller delas mellan kunder?

Azure ytterdörren Service är en globalt distribuerad tjänst för flera innehavare. Därför delas infrastrukturen för ytterdörren mellan alla sina kunder. Men genom att skapa en ytterdörren du definierar den specifika konfiguration som krävs för ditt program och 

### <a name="is-http-https-redirection-supported"></a>Är HTTP -> HTTPS-omdirigering stöds?

Ytterdörren stöder för närvarande inte URL-omdirigering.

### <a name="in-what-order-are-routing-rules-processed"></a>I vilken ordning bearbetas routningsregler?

Vägar för ytterdörren sorteras inte och en specifik väg väljs baserat på bästa möjliga matchning. Läs mer om [hur ytterdörren matchar begäranden till en routningsregel](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Hur jag låsa åtkomsten till min serverdel till endast Azure ytterdörren Service?

Du kan konfigurera IP-ACLing för serverdelen för att godkänna endast trafik från Azure ytterdörren-tjänsten. Du kan begränsa dina program ta emot inkommande anslutningar enbart från IP-adressutrymmet för serverdelen för Azure ytterdörren Service. Vi arbetar för att integrera med [Azure IP-intervall och Tjänsttaggar](https://www.microsoft.com/download/details.aspx?id=56519) men du kan nu finns IP-adressintervall enligt nedan:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Vår backend-IP-adressutrymme kan ändras senare, men vi kommer innan se till att det sker så att vi skulle har integrerat med [Azure IP-intervall och Tjänsttaggar](https://www.microsoft.com/download/details.aspx?id=56519). Vi rekommenderar att du prenumererar på [Azure IP-intervall och Tjänsttaggar](https://www.microsoft.com/download/details.aspx?id=56519) för alla ändringar och uppdateringar. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Anycast IP-Adressen kan ändras med livslängden för min åtkomsten?

Frontend-IP för anycast för ytterdörren vanligtvis bör inte ändra och kan vara statisk under livslängden för åtkomsten. Det finns dock **inga garantier** för samma. Se inte vidta några direkta beroenden på IP-Adressen.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Stöder Azure ytterdörren Service statiska eller dedikerade IP-adresser?

Nej, Azure ytterdörren Service stöder för närvarande inte statisk eller dedikerade klientdel anycast IP-adresser. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure ytterdörren Service har stöd för x-vidarebefordrade-för-huvuden?

Ja, stöder Azure ytterdörren Service rubriker för X-vidarebefordrade-för X-vidarebefordrade-värd och X-vidarebefordrade-protokoll. För X-vidarebefordrade-för om rubriken fanns redan sedan ytterdörren lägger till klientens socket IP-Adressen till den. Annars läggs rubriken med klientens socket IP-Adressen som värde. För X-vidarebefordrade-värd och X-vidarebefordrade-Proto åsidosätts värdet.

Läs mer om den [ytterdörren stöd för HTTP-huvuden](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Hur lång tid tar det för att distribuera en Azure ytterdörren tjänst? Min ytterdörren fortfarande fungerar när uppdateras?

Skapa en ny ytterdörren eller några uppdateringar till en befintlig ytterdörren tar cirka 3 till 5 minuter för global distribution. Det innebär att på 3 till 5 minuter ytterdörren konfigurationen kommer att distribueras på alla våra POP globalt.

Obs! – anpassad SSL-certifikatuppdateringar tar cirka 30 minuter för att distribueras globalt.

## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azure ytterdörren belastningsutjämning eller dirigera trafik, inom ett virtuellt nätverk?

Azure ytterdörren (AFD) kräver en offentlig IP-adress eller offentligt matchat DNS-namn för att dirigera trafik. Svaret är alltså, ingen AFD direkt kan inte routa inom ett virtuellt nätverk, men använder en Application Gateway eller Azure Load Balancer mellan kommer att lösa det här scenariot.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Vilka är de olika tidsgränser och begränsningar för Azure ytterdörren tjänsten?

Lär dig mer om alla dokumentet [tidsgränser och begränsningar för Azure ytterdörren tjänsten](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestanda

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Hur stöder Azure ytterdörren Service hög tillgänglighet och skalbarhet?

Azure ytterdörren Service är en globalt distribuerad plattform flera innehavare med stora volymer av kapacitet för att uppfylla programmets behov av skalbarhet. Levereras från gränsen på Microsofts globala nätverk innehåller ytterdörren globala belastningsutjämning funktioner som gör det möjligt att växla över hela programmet eller även enskilda mikrotjänster över regioner eller flera moln.

## <a name="ssl-configuration"></a>SSL-konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Vilka TLS-versioner som stöds av Azure ytterdörren Service?

Dörren har stöd för TLS 1.0, 1.1 och 1.2. TLS 1.3 stöds inte ännu.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Vilka certifikat stöds på Azure ytterdörren Service?

Om du vill aktivera HTTPS-protokollet för att säkert leverera innehåll på en anpassad domän ytterdörren, kan du använda ett certifikat som hanteras av Azure ytterdörren Service eller använda ditt eget certifikat.
Åtkomsten hanteras alternativet tillhandahåller ett standard SSL-certifikat via Digicert och lagras framför dörrens Key Vault. Om du väljer att använda ditt eget certifikat så du kan registrera ett certifikat från en Certifikatutfärdare som stöds och kan vara ett standard SSL, utökad validering certifikat eller även ett jokerteckencertifikat. Självsignerade certifikat stöds inte. Lär dig [aktivera HTTPS för en anpassad domän](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Vilka är de aktuella krypteringssviter som stöds av Azure ytterdörren Service?

Följande är de aktuella krypteringssviter som stöds av Azure ytterdörren Service:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Azure ytterdörren Service också omkryptering av trafik till serverdelen?

Ja, Azure ytterdörren Service har stöd för SSL-avlastning och slutpunkt till slutpunkt SSL, som krypterar trafiken till serverdelen. I själva verket eftersom anslutningarna till serverdelen sker via det är offentlig IP-adress, vi rekommenderar att du konfigurerar ytterdörren för att använda HTTPS som protokoll för vidarebefordran.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-princip för att styra SSL-protokoll version?

Nej, ytterdörren stöder inte för närvarande för att neka specifika TLS-versioner eller kan ange minimal TLS-versioner. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan jag konfigurera åtkomsten för endast specifika krypteringssviter?

Nej, konfigurera åtkomsten för specifika krypteringssviter som stöds inte. 

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Vilka typer av mått och loggar är tillgängliga med Azure ytterdörren Service?

Information om loggar och andra diagnostiska funktioner finns i [övervakning mått och loggar för ytterdörren](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarandeprincipen på diagnostikloggar?

Flöde för diagnostikloggar till kunder storage-konto och kunder kan definiera bevarandeprincipen baserat på deras prioritet. Diagnostikloggar kan också skickas till en Event Hub eller Azure Monitor loggar. Mer information finns i [Azure ytterdörren Tjänstdiagnostiken](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Hur får jag granskningsloggarna för Azure ytterdörren Service?

Granskningsloggarna är tillgängliga för Azure ytterdörren Service. I portalen klickar du på **aktivitetsloggen** menyn blad ytterdörren för att få åtkomst till granskningsloggen. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Kan jag ställa in aviseringar med Azure ytterdörren Service?

Ja, Azure ytterdörren Service har stöd för aviseringar. Aviseringar kan konfigureras på mått. 

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).