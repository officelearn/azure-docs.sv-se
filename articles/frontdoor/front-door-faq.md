---
title: Ytterdörren i Azure - Vanliga frågor och svar
description: Den här sidan innehåller svar på vanliga frågor om Azure Front Door
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
ms.openlocfilehash: 0fe5d245d629c731a47ca5441afd2a3388a22de4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878025"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Vanliga frågor och svar för Azure Front Door

Den här artikeln svarar på vanliga frågor om Azure Front Door funktioner och funktioner. Om du inte ser svaret på din fråga kan du kontakta oss via följande kanaler (i eskalerande ordning):

1. Kommentarerna i den här artikeln.
2. [Azure ytterdörr UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft-support:** Om du vill skapa en ny supportbegäran väljer du hjälp **+ supportknappen** på fliken Azure på **fliken Hjälp** + och väljer sedan Ny **supportbegäran**.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-front-door"></a>Vad är Azure Front Door?

Azure Front Door är ett ADN-nätverk (Application Delivery Network) som en tjänst som erbjuder olika belastningsutjämningsfunktioner för lager 7 för dina program. Det ger dynamisk platsacceleration (DSA) tillsammans med global belastningsutjämning med nära realtids redundans. Det är en mycket tillgänglig och skalbar tjänst, som hanteras fullt ut av Azure.

### <a name="what-features-does-azure-front-door-support"></a>Vilka funktioner stöder Azure Front Door?

Azure Front Door stöder dynamisk webbplatsacceleration (DSA), TLS/SSL-avlastning och på TLS, webbprogrambrandvägg, cookie-baserad sessionstillhörighet, url-sökvägsbaserad routning, kostnadsfria certifikat och flera domänhanteringer och andra. En fullständig lista över funktioner som stöds finns i [Översikt över Azure Front Door](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Vad är skillnaden mellan Azure Front Door och Azure Application Gateway?

Även om både ytterdörren och Application Gateway är belastningsutjämnare på lager 7 (HTTP/HTTPS), är den primära skillnaden att ytterdörren är en global tjänst medan Application Gateway är en regional tjänst. Även om Ytterdörren kan belastningsbalansen mellan dina olika skalenheter/kluster/stämpelenheter i olika regioner, kan du läsa in application gateway mellan dina virtuella datorer/behållare etc. som finns inom skalenheten.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>När ska vi sätta in en Application Gateway bakom ytterdörren?

De viktigaste scenarierna varför man bör använda Application Gateway bakom Ytterdörren är:

- Ytterdörren kan utföra sökvägsbaserad belastningsutjämning endast på global nivå, men om man vill belastningsjämna trafik ytterligare inom sitt virtuella nätverk (VNET) så bör de använda Application Gateway.
- Eftersom ytterdörren inte fungerar på en VM/container-nivå, så det kan inte göra anslutningsdränning. Med Application Gateway kan du dock göra anslutningsdränning. 
- Med en Application Gateway bakom AFD kan man uppnå 100% TLS/SSL avlastning och dirigera endast HTTP-begäranden inom deras virtuella nätverk (VNET).
- Ytterdörren och Application Gateway stöder båda sessionstillhörighet. Front Door kan dirigera efterföljande trafik från en användarsession till samma kluster eller serverdel i en viss region, men Application Gateway kan direkt affinera trafiken till samma server i klustret.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kan vi distribuera Azure Load Balancer bakom ytterdörren?

Azure Front Door behöver en offentlig VIP eller ett offentligt tillgängligt DNS-namn för att dirigera trafiken till. Distribuera en Azure Load Balancer bakom ytterdörren är ett vanligt användningsfall.

### <a name="what-protocols-does-azure-front-door-support"></a>Vilka protokoll stöder Azure Front Door?

Azure Front Door stöder HTTP, HTTPS och HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hur stöder Azure Front Door HTTP/2?

HTTP/2-protokollsupport är endast tillgängligt för klienter som ansluter till Azure Front Door. Kommunikationen till backends i backend poolen är över HTTP/1.1. HTTP/2-stöd är aktiverat som standard.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Vilka resurser stöds idag som en del av backend-poolen?

Serverdelspooler kan bestå av Lagring, Web App, Kubernetes-instanser eller andra anpassade värdnamn som har offentlig anslutning. Azure Front Door kräver att serverdelen definieras antingen via en offentlig IP eller ett offentligt lösningsbart DNS-värdnamn. Medlemmar i serverda pooler kan vara mellan zoner, regioner eller till och med utanför Azure så länge de har offentlig anslutning.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner är tjänsten tillgänglig i?

Azure Front Door är en global tjänst och är inte knuten till någon specifik Azure-region. Den enda plats du behöver ange när du skapar en ytterdörr är resursgruppsplatsen, som i princip anger var metadata för resursgruppen ska lagras. Front Door-resursen skapas som en global resurs och konfigurationen distribueras globalt till alla POP (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Vilka pop-platser finns för Azure Front Door?

Azure Front Door har samma lista över POP-platser (Point of Presence) som Azure CDN från Microsoft. För den fullständiga listan över våra POP-adresser, vänligen se [Azure CDN POP-platser från Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Är Azure Front Door en dedikerad distribution för mitt program eller delas den mellan kunder?

Azure Front Door är en globalt distribuerad tjänst för flera innehavare. Så är infrastrukturen för Ytterdörren delas över alla sina kunder. Men genom att skapa en front door-profil definierar du den specifika konfiguration som krävs för ditt program och inga ändringar som gjorts i frontdörren påverkar andra frontdörrkonfigurationer.

### <a name="is-http-https-redirection-supported"></a>Stöds HTTP->HTTPS-omdirigering?

Ja. Faktum är att Azure Front Door stöder värd-, sökvägs- och frågesträngomdirigering samt en del av URL-omdirigering. Läs mer om [omdirigering av webbadresser](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>I vilken ordning bearbetas routningsregler?

Rutter för din ytterdörr är inte beställda och en specifik rutt väljs baserat på den bästa matchningen. Läs mer om [Hur Ytterdörren matchar begäranden till en routningsregel](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hur låser jag åtkomsten till min backend till endast Azure Front Door?

Om du vill låsa programmet för att endast acceptera trafik från din specifika ytterdörr måste du ställa in IP-åtkomstkontrollant för serverdelen och sedan begränsa trafiken på serverdelen till det specifika värdet för rubriken "X-Azure-FDID" som skickas av ytterdörren. Dessa steg beskrivs nedan:

- Konfigurera IP ACLing för dina serverdelar för att acceptera trafik från Azure Front Doors serverdels-IP-adressutrymme och Azures infrastrukturtjänster. Se IP-detaljerna nedan för ACLing din servering:
 
    - Referera till *AzureFrontDoor.Backend-avsnittet* i [Azure IP Ranges and Service Tags](https://www.microsoft.com/download/details.aspx?id=56519) for Front Door's IPv4 backend IP-adressintervall eller så kan du också använda tjänsttaggen *AzureFrontDoor.Backend* i dina [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller med [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/service-tags).
    - Front Doors **IPv6-ip-utrymme** för bakdel medan det omfattas av tjänsttaggen visas inte i Azure IP-intervallen JSON-filen. Om du letar efter ett explicit IPv6-adressintervall är det för närvarande begränsat till`2a01:111:2050::/44`
    - Azures [grundläggande infrastrukturtjänster](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) via virtualiserade `168.63.129.16` värd-IP-adresser: och`169.254.169.254`

    > [!WARNING]
    > Ytterdörrens ip-utrymme för bakdel kan ändras senare, men vi kommer att se till att innan det händer, att vi skulle ha integrerat med [Azure IP Ranges och Service Tags](https://www.microsoft.com/download/details.aspx?id=56519). Vi rekommenderar att du prenumererar på [Azure IP-intervall och tjänsttaggar](https://www.microsoft.com/download/details.aspx?id=56519) för eventuella ändringar eller uppdateringar.

-    Utför en GET-åtgärd på din `2020-01-01` ytterdörr med API-versionen eller högre. Leta efter `frontdoorID` fält i API-anropet. Filtrera på det inkommande huvudet '**X-Azure-FDID**' som skickas av ytterdörren `frontdoorID`till serverdelen med värdet som fältet . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan anycast IP förändras under hela min ytterdörr?

Frontend anycast IP för din ytterdörr bör vanligtvis inte ändras och kan förbli statisk under ytterdörrens livstid. Det finns dock **inga garantier** för detsamma. Vänligen ta inte några direkta beroenden på IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Stöder Azure Front Door statiska eller dedikerade IP-adresser?

Nej, Azure Front Door stöder för närvarande inte statiska eller dedikerade frontend anycast IP-adresser. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Stöder Azure Front Door x-forwards-for-huvuden?

Ja, Azure Front Door stöder X-Forwarded-For, X-Forwarded-Host och X-Forwarded-Proto-huvuden. För X-Forwarded-For om huvudet redan fanns sedan Front Door lägger till klient socket IP till den. Annars läggs huvudet med klient socket-IP som värde. För X-Forwarded-Host och X-Forwarded-Proto åsidosätts värdet.

Läs mer om [http-rubriker som stöds](front-door-http-headers-protocol.md)av ytterdörren .  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hur lång tid tar det att distribuera en Azure Front Door? Fungerar min ytterdörr fortfarande när den uppdateras?

En ny frontdörr skapande eller några uppdateringar av en befintlig ytterdörr tar ca 3 till 5 minuter för global distribution. Det innebär att om cirka 3 till 5 minuter kommer din frontdörrkonfiguration att distribueras över alla våra POP globalt.

Anpassade TLS/SSL-certifikatuppdateringar tar cirka 30 minuter att distribuera globalt.

Alla uppdateringar av vägar eller backend-pooler etc. är sömlösa och orsakar noll driftstopp (om den nya konfigurationen är korrekt). Certifikatuppdateringar är också atomära och kommer inte att orsaka något avbrott, om inte byta från "AFD Managed" till "Använd din egen cert" eller vice versa.


## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azure Front Door belastningsbalans eller vägtrafik inom ett virtuellt nätverk?

Azure Front Door (AFD) kräver ett offentligt IP- eller offentligt resolvable DNS-namn för att dirigera trafik. Svaret är alltså ingen AFD direkt kan inte cirkulera i ett virtuellt nätverk, men med hjälp av en Application Gateway eller Azure Load Balancer däremellan löser det här scenariot.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Vilka är de olika tidsgränserna och gränserna för Azure Front Door?

Lär dig mer om alla dokumenterade [timeout och gränser för Azure Front Door](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestanda

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hur stöder Azure Front Door hög tillgänglighet och skalbarhet?

Azure Front Door är en globalt distribuerad plattform för flera innehavare med stor kapacitet för att tillgodose programmets skalbarhetsbehov. Front Door levereras från kanten av Microsofts globala nätverk och erbjuder globala belastningsutjämningsfunktioner som gör att du kan växla över hela programmet eller till och med enskilda mikrotjänster över regioner eller olika moln.

## <a name="tls-configuration"></a>TLS-konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Vilka TLS-versioner stöds av Azure Front Door?

Alla frontdörrprofiler som skapats efter september 2019 använder TLS 1.2 som standardminimum.

Ytterdörren stöder TLS version 1.0, 1.1 och 1.2. TLS 1.3 stöds ännu inte.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Vilka certifikat stöds på Azure Front Door?

Om du vill aktivera HTTPS-protokollet för att på ett säkert sätt leverera innehåll på en anpassad domän för ytterdörren kan du välja att använda ett certifikat som hanteras av Azure Front Door eller använda ditt eget certifikat.
Ytterdörrens hanterade alternativ etablerar ett standard-TLS/SSL-certifikat via Digicert och lagras i Front Door's Key Vault. Om du väljer att använda ditt eget certifikat kan du gå in på ett certifikat från en certifikatutfärdare som stöds och kan vara ett standard-TLS- utökat valideringscertifikat eller till och med ett jokerteckencertifikat. Självsignerade certifikat stöds inte. Lär dig hur du [aktiverar HTTPS för en anpassad domän](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Stöder ytterdörren autorotation av certifikat?

För alternativet Ytterdörr hanteras certifikat, är certifikaten autorotated av ytterdörren. Om du använder ett certifikat för ytterdörrshanterad och se till att certifikatets utgångsdatum är mindre än 60 dagar bort lämnar du in en supportbiljett.
</br>För ditt eget anpassade TLS/SSL-certifikat stöds inte autorotation. I likhet med hur det inrättades första gången för en viss anpassad domän, måste du peka ytterdörren till rätt certifikatversion i key vault och se till att tjänstens huvudnamn för Ytterdörren fortfarande har tillgång till Key Vault. Den här uppdaterade certifikatutrullningen av Ytterdörren är atomär och orsakar ingen produktionspåverkan förutsatt att ämnesnamnet eller SAN för certifikatet inte ändras.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Vilka är de aktuella chiffersviterna som stöds av Azure Front Door?

Följande är de aktuella chiffersviterna som stöds av Azure Front Door:

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kan jag konfigurera TLS-principen för att styra TLS-protokollversioner?

Du kan konfigurera en minsta TLS-version i Azure Front Door i de anpassade domän-HTTPS-inställningarna via Azure-portalen eller [Azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). För närvarande kan du välja mellan 1,0 och 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan jag konfigurera ytterdörren så att den bara stöder specifika chiffersviter?

Nej, det går inte att konfigurera ytterdörren för specifika chiffersviter. Du kan dock få ditt eget anpassade TLS/SSL-certifikat från certifikatutfärdaren (t.o.d. Verisign, Entrust eller Digicert) och ha specifika chiffersviter markerade på certifikatet när du har genererat det. 

### <a name="does-front-door-support-ocsp-stapling"></a>Har Ytterdörren stöd OCSP häftning?

Ja, OCSP häftning stöds som standard av Ytterdörren och ingen konfiguration krävs.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Azure Front Door också omkryptering av trafik till backend?

Ja, Azure Front Door stöder TLS/SSL-avlastning och end to end TLS, som krypterar om trafiken till backend. Eftersom anslutningarna till serverdelen sker över den offentliga IP-adressen rekommenderar vi att du konfigurerar ytterdörren så att den använder HTTPS som vidarebefordringsprotokoll.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Stöder Ytterdörren självsignerade certifikat på serverdelen för HTTPS-anslutning?

Nej, självsignerade certifikat stöds inte på ytterdörren och begränsningen gäller både:

1. **Serverföringar:** Du kan inte använda självsignerade certifikat när du vidarebefordrar trafiken som HTTPS- eller HTTPS-hälsoavsökningar eller fyller cacheminnet för från-ursprung för routningsregler med cachelagring aktiverad.
2. **Klientdel:** Du kan inte använda självsignerade certifikat när du använder ditt eget anpassade TLS/SSL-certifikat för att aktivera HTTPS på din anpassade domän.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Varför misslyckas HTTPS-trafik till min backend?

För att ha lyckade HTTPS-anslutningar till serveringen oavsett om det gäller hälsoavsökningar eller för vidarebefordringsbegäranden kan det finnas två orsaker till att HTTPS-trafik kan misslyckas:

1. **Certifikatämnesnamn matchar inte:** För HTTPS-anslutningar förväntar sig Front Door att serverdelen visar certifikat från en giltig certifikatutfärdare med ämnesnamn som matchar serverdelsvärden. Om ditt serverdelsvärdnamn är inställt `myapp-centralus.contosonews.net` på och certifikatet som serverdelen visar under TLS-handskakningen varken har `myapp-centralus.contosonews.net` eller `*myapp-centralus*.contosonews.net` i ämnesnamnet, kommer ytterdörren att vägra anslutningen och resultera i ett fel. 
    1. **Lösning**: Även om det inte rekommenderas från en efterlevnad synvinkel, kan du lösa det här felet genom att inaktivera certifikatämnesnamn kontrollera din ytterdörr. Detta finns under Inställningar i Azure-portalen och under BackendPoolsSettings i API:et.
2. **Serverdelsvärd certifikat från ogiltig certifikatutfärdare:** Endast certifikat från [giltiga certifikatutfärdare](/azure/frontdoor/front-door-troubleshoot-allowed-ca) kan användas i serverdelen med ytterdörren. Certifikat från interna certifikatutfärdare eller självsignerade certifikat är inte tillåtna.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Vilka typer av mått och loggar är tillgängliga med Azure Front Door?

Information om loggar och andra diagnostiska funktioner finns i [Övervaka mått och loggar för ytterdörren](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarandeprincipen för diagnostikloggarna?

Diagnostikloggar flödar till kundens lagringskonto och kunder kan ange bevarandeprincipen baserat på deras inställningar. Diagnostikloggar kan också skickas till en event hub eller Azure Monitor-loggar. Mer information finns i [Azure Front Door Diagnostics](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hur får jag granskningsloggar för Azure Front Door?

Granskningsloggar är tillgängliga för Azure Front Door. I portalen klickar du på **Aktivitetslogg** i menybladet på ytterdörren för att komma åt granskningsloggen. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan jag ställa in aviseringar med Azure Front Door?

Ja, Azure Front Door stöder aviseringar. Aviseringar är konfigurerade på mått. 

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
