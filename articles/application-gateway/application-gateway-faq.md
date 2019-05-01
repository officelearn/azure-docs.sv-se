---
title: Vanliga frågor och svar om Azure Application Gateway
description: Få svar på vanliga frågor och svar om Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 72eaa18d570e85274efbea80f12e9a7301eecbfa
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947073"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Vanliga frågor och svar om Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Allmänt

### <a name="what-is-application-gateway"></a>Vad är Application Gateway?

Azure Application Gateway erbjuder application Deliver controller (ADC) som en tjänst. Den erbjuder olika layer 7-belastningsutjämning för dina program. Den här tjänsten är med hög tillgänglighet, skalbar och helt hanterad av Azure.

### <a name="what-features-does-application-gateway-support"></a>Vilka funktioner stöder Application Gateway?

Application Gateway stöder automatisk skalning, SSL-avlastning, och slutpunkt till slutpunkt SSL, en brandvägg för webbaserade program (WAF), Cookiebaserad sessionstillhörighet, URL-sökvägsbaserad routning, värd för flera platser och andra funktioner. En fullständig lista över funktioner som stöds finns i [introduktion till Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hur skiljer Application Gateway och Azure Load Balancer?

Application Gateway är en layer 7 belastningsutjämnare, vilket innebär att den fungerar bara med webbtrafik (HTTP, HTTPS, WebSocket och HTTP/2). Det stöder funktioner som SSL-avslutning, cookie-baserad sessionstillhörighet och resursallokering för belastningsutjämning trafik. Läsa in belastningsutjämnare belastningsutjämnar trafik på layer 4 (TCP eller UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Vilka protokoll stöder Application Gateway?

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hur stöder HTTP/2 i Application Gateway?

Se [stöd för HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Vilka resurser som stöds som en del av en serverdelspool?

Se [stöds serverdelsresurser](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>I vilka regioner är Application Gateway som är tillgängligt?

Application Gateway är tillgängliga i alla globala Azure-regioner. Det är också tillgängliga i [Azure Kina 21Vianet](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Är den här distributionen som är dedikerad för min prenumeration eller delas mellan kunder?

Application Gateway är en särskild distribution i det virtuella nätverket.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway har stöd för omdirigering för HTTP till HTTPS?

Omdirigering av stöds. Se [översikt för omdirigering i Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>I vilken ordning bearbetas lyssnare?

Se den [ordningen för lyssnare som bearbetar](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Var hittar jag Application Gateway-IP och DNS?

Om du använder en offentlig IP-adress som en slutpunkt, hittar du IP-adress och DNS-informationen på den offentliga IP-adressresursen. Eller hitta i portalen på översiktssidan för application gateway. Om du använder interna IP-adresser, hittar du information på översiktssidan.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Vad är inställningarna för timeout för Keep-Alive och timeout för TCP-inaktivitet?

 I SKU: N för Application Gateway v1 är Keep-Alive tidsgränsen 120 sekunder. Keep-Alive tidsgränsen för v2-SKU är 75 sekunder. TCP-tidsgränsen för inaktivitet är ett 4-minuters standard på klientdelens virtuella IP-Adressen (VIP) för Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP-Adressen eller DNS-namnet över livslängden för application gateway?

VIP-Adressen kan ändras om du stoppar och startar application gateway. Men DNS-namnet som associeras med application gateway ändras inte under livslängden för gatewayen. Eftersom DNS-namnet inte ändras, bör du använda en CNAME-alias och gå till DNS-adressen för application gateway.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway har stöd för statisk IP-adress?

Ja, stöder Application Gateway v2-SKU statiska offentliga IP-adresser. V1-SKU: N stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

En application gateway stöder endast en offentlig IP-adress.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stor ska jag göra mitt undernät för Application Gateway?

Se [Application Gateway-undernätet storlek överväganden](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan jag distribuera fler än en Application Gateway-resurs till ett enda undernät?

Ja. Förutom att flera instanser av en viss Application Gateway-distribution kan du etablera en annan unik Application Gateway-resurs i ett befintligt undernät som innehåller en annan resurs i Application Gateway.

Ett enda undernät stöd inte för både Standard_v2 och Standard Application Gateway tillsammans.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway har stöd för x-vidarebefordrade-för-huvuden?

Ja. Se [ändringar på en begäran](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hur lång tid tar det för att distribuera en Programgateway? Fungerar min Programgateway medan det uppdateras?

Ny Programgateway v1 SKU-distributioner kan ta upp till 20 minuter att etablera. Ändringar i instansstorleken eller antal är inte störande och gatewayen förblir aktiv under den här tiden.

Distributioner som använder v2-SKU: N kan ta upp till 6 minuter att etablera.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan jag använda Exchange Server som en serverdel med Application Gateway?

Nej. Application Gateway stöder inte e-postprotokoll som SMTP, IMAP och POP3. 

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder hög tillgänglighet och skalbarhet av Application Gateway?

V1-SKU: N för Application Gateway stöder scenarier med hög tillgänglighet när du har distribuerat minst två instanser. Azure distribuerar dessa instanser mellan uppdaterings- och feldomäner domäner att säkerställa att instanser inte alla misslyckas på samma gång. V1-SKU: N stöder skalbarhet genom att lägga till flera instanser av samma gateway att dela belastningen.

V2-SKU säkerställer automatiskt att nya instanser är fördelade över feldomäner och uppdateringsdomäner. Om du väljer zonredundans sprids också de senaste instanserna i olika tillgänglighetszoner och erbjuder zonindelad fel återhämtning.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hur jag uppnå katastrofåterställning i datacenter med hjälp av Application Gateway?

Använd Traffic Manager för att distribuera trafik över flera programgatewayer i olika datacenter.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway har stöd för automatisk skalning?

Ja, v2-SKU: N för Application Gateway stöder automatisk skalning. Mer information finns i [automatisk skalning och zonredundant Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Tillåter manuell skala eller ned orsak driftavbrott?

Nej. Instanser distribueras bland uppgraderingsdomäner och feldomäner.

### <a name="does-application-gateway-support-connection-draining"></a>Stöder Application Gateway Anslutningstömning?

Ja. Du kan ställa in Anslutningstömning om du vill ändra medlemmar i en backend-pool utan avbrott. Den här inställningen kan du fortsätta att skicka befintliga anslutningar till sina tidigare målvolymen förrän antingen anslutningen stängs eller en konfigurerbar tidsgränsen har nåtts. Anslutningstömning väntar bara aktuella pågående anslutningar ska slutföras. Application Gateway är inte medveten om sessionstillstånd för programmet.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instansstorleken från medel till stora utan avbrott?

Ja. Azure distribuerar instanser mellan uppdaterings- och feldomäner domäner att säkerställa att instanser inte misslyckas alla på samma gång. Application Gateway stöder skalning genom att lägga till flera instanser av samma gateway att dela belastningen.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway alltid distribueras i ett virtuellt nätverk?

Ja. Application Gateway distribueras alltid i ett virtuellt nätverksundernät. Det här undernätet kan innehålla endast programgatewayer. Mer information finns i [virtuella nätverk och undernät krav](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway kan kommunicera med instanser utanför dess virtuella nätverk eller utanför sin prenumeration?

Så länge som du har IP-anslutning kan kan Application Gateway kommunicera med instanser utanför det virtuella nätverket som den tillhör. Application Gateway kan även kommunicera med instanser utanför den prenumeration som den tillhör. Om du planerar att använda interna IP-adresser som medlemmar i serverdelspool använda [virtuell nätverkspeering](../virtual-network/virtual-network-peering-overview.md) eller [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera allt annat i application gateway-undernätet?

Nej. Men du kan distribuera andra application gatewayer i undernätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds nätverkssäkerhetsgrupper på application gateway-undernät?

Se [Nätverkssäkerhetsgrupper i Application Gateway-undernätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Application gateway-undernät som har stöd för användardefinierade vägar?

Se [användardefinierade vägar som stöds i Application Gateway-undernätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna på Application Gateway? Kan jag öka gränserna?

Se [Application Gateway begränsar](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan jag samtidigt använda Application Gateway för både externa och interna trafik?

Ja. Application Gateway stöder en intern IP-adress och en extern IP-adress per Programgateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Stöder Application Gateway virtuell nätverkspeering?

Ja. Virtuell nätverkspeering hjälper till att belastningsutjämna trafik i andra virtuella nätverk.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan jag kontakta lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?

Ja, förutsatt att trafik tillåts.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>En serverdelspool kan hantera många program på olika portar?

Mikrotjänstarkitektur stöds. Om du vill söka på olika portar, måste du konfigurera flera HTTP-inställningar.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Anpassade avsökningar stöder jokertecken eller regex på svarsdata?

Nej. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hur bearbetas routningsregler i Application Gateway?

Se [ordningen på bearbetningsregler](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>För anpassade avsökningar vad fältet värd en obestämd?

Fältet värd namnet skicka om avsökningen för att när du har konfigurerat flera platser för Application Gateway. Annars använda ”127.0.0.1”. Det här värdet skiljer sig från virtuella datorns värdnamn. Dess format är \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>.

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>Kan jag listan över godkända Application Gateway-åtkomst till endast några source IP-adresser?

Ja. Se [begränsa åtkomsten till specifika käll-IP-adresser](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan jag använda samma port för både offentliga och privata-ansluten lyssnare?

Nej.

## <a name="configuration---ssl"></a>Konfiguration – SSL

### <a name="what-certificates-does-application-gateway-support"></a>Vilka certifikat har stöd för Application Gateway?

Application Gateway stöder självsignerade certifikat, certificate authority (CA) certifikat, certifikat (Extended Validation) och jokerteckencertifikat.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Vilka krypteringssviter har Application Gateway stöder?

Application Gateway stöder följande krypteringssviter. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Information om hur du anpassar SSL-alternativ finns i [Konfigurera SSL-princip versioner och krypteringssviter på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway har stöd för krypteringen av trafik till serverdelen?

Ja. Application Gateway stöder SSL-avlastning och slutpunkt till slutpunkt SSL, som omkryptera trafik till serverdelen.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-princip att kontrollen SSL-protokollsversioner?

Ja. Du kan konfigurera Programgatewayen för att neka TLS 1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 är redan inaktiverat som standard och inte kan konfigureras.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan jag konfigurera chiffersviter och principordning?

Ja. I Application Gateway, kan du [konfigurera krypteringssviter](application-gateway-ssl-policy-overview.md). Aktivera minst en av de följande krypteringssviter som om du vill definiera en anpassad princip. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway använder SHA256 till för hantering av serverdelen.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hur många SSL-certifikat har stöd för Application Gateway?

Application Gateway stöder upp till 100 SSL-certifikat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hur många autentiseringscertifikat för serverdelen omkrypteringsgenomsökningar har stöd för Application Gateway?

Application Gateway stöder upp till 10 autentiseringscertifikat. Standardvärdet är 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway internt integrera med Azure Key Vault?

Ja, stöder Application Gateway v2-SKU Key Vault. Mer information finns i [SSL-avslutning med Key Vault-certifikat](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hur konfigurerar HTTPS-lyssnare för .com och .net platser? 

För flera domänbaserade (värd-baserad) routning, kan du skapa multisite lyssnare, konfigurera lyssnare som använder HTTPS som protokoll och associera lyssnarna med regler för vidarebefordran. Mer information finns i [som är värd för flera platser med hjälp av Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguration – Brandvägg för webbaserade program (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Erbjuder WAF SKU alla tillgängliga funktioner i Standard-SKU?

Ja. WAF stöder alla funktioner i Standard-SKU.

### <a name="which-crs-versions-does-application-gateway-support"></a>Vilka CRS versioner stöder Application Gateway?

Application Gateway stöder CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Hur övervakar jag WAF?

Övervaka WAF via Diagnostisk loggning. Mer information finns i [Diagnostisk loggning och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blockerar trafik i identifieringsläge?

Nej. Identifieringsläge loggar endast trafik som utlöser en WAF-regel.

### <a name="can-i-customize-waf-rules"></a>Kan jag Anpassa WAF-regler?

Ja. Mer information finns i [anpassa WAF-regelgrupper och -regler](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Vilka regler är för närvarande tillgängligt för WAF?

WAF stöder för närvarande CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Dessa regler innehåller grundläggande säkerhet mot de flesta av de topp 10-säkerhetsrisker som identifierar OWASP Open Web Application Security Project (): 

* Skydd mot SQL-inmatning
* Cross site scripting skydd
* Skydd mot vanliga webbattacker, till exempel kommandot inmatning, HTTP-begäran som kommandoinmatning, HTTP-svar och attack för inkludering av fjärrfil
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS och så vidare)

Mer information finns i [OWASP topp 10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Stöder WAF DDoS protection?

Ja. Du kan aktivera DDoS-skydd i det virtuella nätverket där application gateway distribueras. Den här inställningen garanterar att tjänsten Azure DDoS Protection även skyddar application gateway virtuell IP (VIP).

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Vilka typer av loggar tillhandahåller Application Gateway?

Application Gateway erbjuder tre loggar: 

* **ApplicationGatewayAccessLog**: Åtkomst-loggen innehåller varje begäran som skickas till application gateway-klientdel. Data innehåller anroparens IP, Webbadress som efterfrågas, svarsfördröjning, returkod och byte in och ut. Åtkomstlogg samlas in var 300 sekund. Den innehåller en post per Programgateway.
* **ApplicationGatewayPerformanceLog**: Prestandaloggen samlar in prestandainformation för varje application gateway. Informationen omfattar dataflödet i byte, totalt antal begäranden hanteras, antalet misslyckade förfrågningar och felfria och defekta serverdel instansantalet.
* **ApplicationGatewayFirewallLog**: För programgatewayer som du konfigurerar med WAF, innehåller brandväggsloggen begäranden som loggas via identifieringsläge eller förhindringsläge.

Mer information finns i [serverdelens hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur vet jag om min medlemmar i serverdelspoolen är felfria?

Kontrollera hälsa med hjälp av PowerShell-cmdleten `Get-AzApplicationGatewayBackendHealth` eller portalen. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Vad är bevarandeprincipen för diagnostikloggarna?

Diagnostikloggar flöde till kundens lagringskonto. Kunderna kan ange bevarandeprincip baserat på deras prioritet. Diagnostikloggar kan även skickas till en event hub eller Azure Monitor-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur får jag granskningsloggar för Application Gateway?

I portalen på bladet menyn för en Programgateway väljer **aktivitetsloggen** att få åtkomst till granskningsloggen. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ställa in aviseringar med Application Gateway?

Ja. Aviseringar kan konfigureras på mått i Application Gateway. Mer information finns i [Application Gateway-mått](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) och [Varningsaviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur jag för att analysera trafik statistik för Application Gateway?

Du kan visa och analysera åtkomstloggar på flera olika sätt. Använda Azure Monitor-loggar, Excel, Power BI och så vidare.

Du kan också använda en Resource Manager-mall som installerar och kör populära [GoAccess](https://goaccess.io/) logga analyzer för åtkomstloggar för Application Gateway. GoAccess ger värdefull HTTP-trafik statistik, till exempel unika besökare, begärda filer, värdar, operativsystem, webbläsare och HTTP-statuskoder. Läs mer i GitHub, den [Readme-filen i mallmappen Resource Manager-](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Vad kan orsaka serverdelens hälsotillstånd att returnera en okänd status?

Okänd status visas vanligtvis när blockeras åtkomst till serverdelen av en nätverkssäkerhetsgrupp (NSG), en anpassad DNS eller en användardefinierad routning (UDR) i application gateway-undernätet. Mer information finns i [serverdelens hälsotillstånd, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om Application Gateway i [vad är Azure Application Gateway?](overview.md).
