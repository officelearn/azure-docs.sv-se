---
title: Vanliga frågor om Azure Application Gateway
description: Få svar på vanliga frågor om Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: dc3b9cceda10ae1ff648306236d5ac4868b5b426
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722163"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Vanliga frågor och svar om Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande är vanliga frågor som ställs om Azure Application Gateway.

## <a name="general"></a>Allmänt

### <a name="what-is-application-gateway"></a>Vad är Application Gateway?

Azure Application Gateway tillhandahåller en program leverans kontroll (ADC) som en tjänst. Det erbjuder olika belastnings Utjämnings funktioner i lager 7 för dina program. Den här tjänsten är hög tillgänglig, skalbar och helt hanterad av Azure.

### <a name="what-features-does-application-gateway-support"></a>Vilka funktioner stöder Application Gateway?

Application Gateway stöder automatisk skalning, SSL-avlastning och SSL (end-to-end), en brand vägg för webbaserade program (WAF), cookie-baserad, URL-sökväg baserad routning, multisite-värd och andra funktioner. En fullständig lista över funktioner som stöds finns i [Introduktion till Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hur skiljer sig Application Gateway och Azure Load Balancer?

Application Gateway är en Layer 7-belastningsutjämnare, vilket innebär att den endast fungerar med webb trafik (HTTP, HTTPS, WebSocket och HTTP/2). Den har stöd för funktioner som SSL-avslutning, cookie-baserad sessionsgräns och resursallokering för belastnings Utjämnings trafik. Load Balancer belastnings Utjämnings trafik på Layer 4 (TCP eller UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Vilka protokoll stöder Application Gateway?

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hur stöder Application Gateway HTTP/2?

Se [stöd för http/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Vilka resurser stöds som en del av en backend-pool?

Se [stödda Server dels resurser](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>I vilka regioner är Application Gateway tillgängliga?

Application Gateway är tillgängligt i alla regioner i Global Azure. Det är också tillgängligt i [Azure Kina 21Vianet](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Är den här distributionen dedikerad för min prenumeration eller delas den mellan kunder?

Application Gateway är en dedikerad distribution i det virtuella nätverket.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Stöder Application Gateway HTTP-till-HTTPS-omdirigering?

Omdirigering stöds. Se [Application Gateway omdirigerings översikt](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>I vilken ordning bearbetas lyssnare?

Se [ordningen för avlyssnings bearbetning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Var hittar jag Application Gateway IP och DNS?

Om du använder en offentlig IP-adress som en slut punkt hittar du IP-och DNS-information om den offentliga IP-adressresursen. Eller hitta den i portalen på sidan Översikt för Application Gateway. Om du använder interna IP-adresser hittar du informationen på sidan Översikt.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Vad är inställningarna för Keep-Alive-timeout och timeout för TCP-inaktivitet?

 I Application Gateway v1 SKU är timeoutvärdet för Keep-Alive 120 sekunder. Timeoutvärdet för Keep-Alive för v2-SKU: n är 75 sekunder. Timeout för TCP-inaktivitet är en fyra minuters standard på den virtuella IP-adressen i klient delen (VIP) för Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP-eller DNS-namn under programgatewayens livs längd?

VIP kan ändras om du stoppar och startar Application Gateway. Men det DNS-namn som är associerat med programgatewayen ändras inte över gatewayens livstid. Eftersom DNS-namnet inte ändras bör du använda ett CNAME-alias och peka det mot DNS-adressen för Application Gateway.

### <a name="does-application-gateway-support-static-ip"></a>Stöder Application Gateway statisk IP?

Ja, Application Gateway v2-SKU: n stöder statiska offentliga IP-adresser. V1 SKU stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

En Application Gateway stöder bara en offentlig IP-adress.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stor ska jag göra mitt undernät för Application Gateway?

Se [Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)-undernätets storleks överväganden.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan jag distribuera fler än en Application Gateway resurs till ett enda undernät?

Ja. Förutom flera instanser av en specifik Application Gateway distribution kan du etablera en annan unik Application Gateway resurs till ett befintligt undernät som innehåller en annan Application Gateway resurs.

Ett enda undernät kan inte stödja både Standard_v2 och standard Application Gateway tillsammans.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Stöder Application Gateway x-vidarebefordrade-för rubriker?

Ja. Se [ändringar av en begäran](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hur lång tid tar det att distribuera en Application Gateway? Fungerar min Application Gateway när den uppdateras?

Det kan ta upp till 20 minuter att etablera nya Application Gateway v1 SKU-distributioner. Ändringar av instans storleken eller antalet är inte störande och gatewayen förblir aktiv under den här tiden.

Distributioner som använder v2-SKU: n kan ta upp till 6 minuter att etablera.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan jag använda Exchange Server som Server del med Application Gateway?

Nej. Application Gateway stöder inte e-postprotokoll som SMTP, IMAP och POP3. 

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder Application Gateway hög tillgänglighet och skalbarhet?

Application Gateway v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller fler instanser. Azure distribuerar dessa instanser över uppdaterings-och fel domäner för att säkerställa att instanserna inte fungerar samtidigt. V1 SKU stöder skalbarhet genom att lägga till flera instanser av samma Gateway för att dela belastningen.

V2-SKU: n ser automatiskt till att nya instanser sprids mellan fel domäner och uppdaterings domäner. Om du väljer zon redundans sprids även de nyaste instanserna över tillgänglighets zoner för att erbjuda zonindelade-felåterhämtning.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hur gör jag för att uppnå ett DR-scenario över data Center med Application Gateway?

Använd Traffic Manager för att distribuera trafik över flera programgatewayer i olika data Center.

### <a name="does-application-gateway-support-autoscaling"></a>Stöder Application Gateway automatisk skalning?

Ja, Application Gateway v2-SKU: n stöder automatisk skalning. Mer information finns i autoskalning [och zon-redundant Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Går det att skala upp eller ned manuellt under drift stopp?

Nej. Instanser distribueras mellan uppgraderings domäner och fel domäner.

### <a name="does-application-gateway-support-connection-draining"></a>Har Application Gateway stöd för anslutning som töms?

Ja. Du kan konfigurera anslutnings tömning för att ändra medlemmar i en backend-pool utan avbrott. Med den här installationen kan du fortsätta att skicka befintliga anslutningar till deras tidigare mål tills antingen anslutningen stängs eller en konfigurerbar tids gräns upphör att gälla. Anslutnings tömning väntar på att endast aktuella anslutningar i flygningen ska slutföras. Application Gateway är inte medvetna om appens sessionstillstånd.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instans storleken från medel till stor utan avbrott?

Ja. Azure distribuerar instanser över uppdaterings-och fel domäner för att se till att instanserna inte växlar alla samtidigt. Application Gateway stöder skalning genom att lägga till flera instanser av samma Gateway för att dela belastningen.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Är Application Gateway alltid distribuerat i ett virtuellt nätverk?

Ja. Application Gateway distribueras alltid i ett undernät för virtuellt nätverk. Det här under nätet får bara innehålla programgatewayer. Mer information finns i [krav för virtuellt nätverk och undernät](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway kommunicera med instanser utanför det virtuella nätverket eller utanför prenumerationen?

Så länge som du har IP-anslutning kan Application Gateway kommunicera med instanser utanför det virtuella nätverk där det finns. Application Gateway kan också kommunicera med instanser utanför prenumerationen som det finns i. Om du planerar att använda interna IP-adresser som medlemmar i backend-poolen använder du peering för [virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) eller [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera något annat i Application Gateway-undernätet?

Nej. Men du kan distribuera andra programgatewayer i under nätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds nätverks säkerhets grupper i undernät för Application Gateway?

Se [nätverks säkerhets grupper i Application Gateway under nätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Har Application Gateway-undernätet stöd för användardefinierade vägar?

Se [användardefinierade vägar som stöds i Application Gateway under nätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna för Application Gateway? Kan jag öka de här gränserna?

Se [Application Gateway gränser](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan jag använda Application Gateway samtidigt för både extern och intern trafik?

Ja. Application Gateway stöder en intern IP-adress och en extern IP per Application Gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Stöder Application Gateway virtuell nätverks-peering?

Ja. Det virtuella nätverkets peering hjälper belastnings Utjämnings trafik i andra virtuella nätverk.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan jag prata med lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?

Ja, så länge trafik tillåts.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan en backend-pool betjäna många program på olika portar?

Mikrotjänstens arkitektur stöds. Om du vill avsöka på olika portar måste du konfigurera flera HTTP-inställningar.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Stöder anpassade avsökningar jokertecken eller regex för svars data?

Nej. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hur bearbetas routningsregler i Application Gateway?

Se [ordning för bearbetnings regler](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Vad indikerar värd fältet för anpassade avsökningar?

Fältet värd anger namnet som avsökningen ska skickas till när du har konfigurerat multisite på Application Gateway. Använd annars 127.0.0.1. Det här värdet skiljer sig från värd namnet för den virtuella datorn. Formatet \<är Protocol\<://\>Host:port\<Sökväg\>.\>\>\<

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan jag bara tillåta Application Gateway åtkomst till några få käll-IP-adresser?

Ja. Se [begränsa åtkomsten till vissa käll-IP-adresser](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan jag använda samma port för både offentlig och privat lyssnare?

Nej.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det några rikt linjer som du kan använda för att migrera från v1 SKU till v2-SKU: n?

Ja. Mer information finns i [migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Konfiguration – SSL

### <a name="what-certificates-does-application-gateway-support"></a>Vilka certifikat stöder Application Gateway?

Application Gateway stöder självsignerade certifikat, certifikat utfärdare (CA)-certifikat, utöknings verifiering (EV) och certifikat med jokertecken.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Vilka chiffersviter stöder Application Gateway?

Application Gateway stöder följande chiffersviter. 

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

Information om hur du anpassar SSL-alternativ finns i [Konfigurera SSL-principinställningar och chiffersviter på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Stöder Application Gateway kryptering av trafik till Server delen?

Ja. Application Gateway stöder SSL-avläsning och SSL (end-to-end) som krypterar trafik till Server delen.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-principer för att kontrol lera SSL-protokoll versioner?

Ja. Du kan konfigurera Application Gateway att neka TLS 1.0, TLS 1.1 och TLS 1.2. Som standard är SSL 2,0 och 3,0 redan inaktiverat och kan inte konfigureras.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan jag konfigurera cipher-paket och princip ordning?

Ja. I Application Gateway kan du [Konfigurera chiffersviter](application-gateway-ssl-policy-overview.md). Aktivera minst en av följande chiffersviter om du vill definiera en anpassad princip. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway använder SHA256 för Server dels hantering.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hur många SSL-certifikat stöder Application Gateway?

Application Gateway stöder upp till 100 SSL-certifikat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hur många autentiserings certifikat för Omkryptering av backend-Application Gateway stöd för?

Application Gateway stöder upp till 10 certifikat för autentisering. Standardvärdet är 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integrerar Application Gateway internt med Azure Key Vault?

Ja, Application Gateway v2-SKU: n stöder Key Vault. Mer information finns i [SSL-avslutning med Key Vault certifikat](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hur gör jag för att konfigurera HTTPS-lyssnare för. com-och .net-platser? 

För flera domänbaserade (värdbaserade) routningar kan du skapa multisite-lyssnare, konfigurera lyssnare som använder HTTPS som protokoll och associera lyssnarna med routningsregler. Mer information finns i vara [värd för flera platser med hjälp av Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguration – brand vägg för webbaserade program (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Erbjuder WAF SKU alla funktioner som är tillgängliga i standard-SKU: n?

Ja. WAF stöder alla funktioner i standard-SKU: n.

### <a name="which-crs-versions-does-application-gateway-support"></a>Vilka datoriserade boknings versioner stöder Application Gateway?

Application Gateway stöder boknings system för [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och datoriserat system som [3,0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Hur gör jag för att övervaka WAF?

Övervaka WAF via diagnostisk loggning. Mer information finns i [diagnostisk loggning och statistik för Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blockeras trafik i identifierings läge?

Nej. Identifierings läge loggar enbart trafik som utlöser en WAF-regel.

### <a name="can-i-customize-waf-rules"></a>Kan jag anpassa WAF-regler?

Ja. Mer information finns i [Anpassa WAF Rule Groups och Rules](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Vilka regler är för närvarande tillgängliga för WAF?

WAF stöder för närvarande BOKNINGs- [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och [3,0](application-gateway-crs-rulegroups-rules.md#owasp30). De här reglerna ger grundläggande säkerhet mot de flesta 10 högsta säkerhets risker som öppnar webb program säkerhets projekt (OWASP): 

* Skydd mot SQL-inmatning
* Skript skydd över hela platsen
* Skydd mot vanliga webb attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och attack för att ta emot fjärrfiler
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga program konfigurationer (det vill säga Apache, IIS och så vidare)

Mer information finns i [OWASP topp 10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Stöder WAF DDoS-skydd?

Ja. Du kan aktivera DDoS-skydd på det virtuella nätverk där programgatewayen distribueras. Den här inställningen säkerställer att tjänsten Azure DDoS Protection också skyddar den virtuella IP-adressen (VIP) för Programgateway.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det några rikt linjer som du kan använda för att migrera från v1 SKU till v2-SKU: n?

Ja. Mer information finns i [migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2](migrate-v1-v2.md).

### <a name="does-waf-support-non-utf-8-encoding"></a>Stöder WAF icke-UTF-8-kodning?

Nej. WAF stöder för närvarande endast UTF-8-kodning.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Vilka typer av loggar Application Gateway tillhandahåller?

Application Gateway innehåller tre loggar: 

* **ApplicationGatewayAccessLog**: Åtkomst loggen innehåller varje begäran som skickas till Application Gateway-frontend. Data innehåller anroparens IP, URL begärd, svars fördröjning, retur kod och byte in och ut. Åtkomst loggen samlas in var 300: e sekund. Den innehåller en post per Application Gateway.
* **ApplicationGatewayPerformanceLog**: Prestanda loggen samlar in prestanda information för varje Application Gateway. Informationen omfattar data flödet i byte, totalt antal hanterade begär Anden, misslyckade begär Anden och antalet felfria och felaktiga Server dels instanser.
* **ApplicationGatewayFirewallLog**: För programgatewayer som du konfigurerar med WAF innehåller brand Väggs loggen begär Anden som loggas via antingen identifierings läge eller skydds läge.

Mer information finns i [backend-hälsohälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur gör jag för att veta om mina medlemmar i Server delens medlemmar är felfria?

Verifiera hälsan med hjälp av PowerShell- `Get-AzApplicationGatewayBackendHealth` cmdleten eller portalen. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostiska loggar flödar till kundens lagrings konto. Kunder kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur gör jag för att hämta gransknings loggar för Application Gateway?

I portalen väljer du **aktivitets logg** för att få åtkomst till gransknings loggen på Meny bladet för en Programgateway. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ange aviseringar med Application Gateway?

Ja. I Application Gateway konfigureras aviseringar för mått. Mer information finns i [Application Gateway mått](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) och [ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur gör jag för att analysera trafik statistik för Application Gateway?

Du kan visa och analysera åtkomst loggar på flera sätt. Använd Azure Monitor loggar, Excel, Power BI och så vidare.

Du kan också använda en Resource Manager-mall som installerar och kör den populära [GoAccess](https://goaccess.io/) log analyzer för Application Gateway åtkomst loggar. GoAccess tillhandahåller värdefull statistik för HTTP-trafik, till exempel unika besökare, begärda filer, värdar, operativ system, webbläsare och HTTP-statuskod. Mer information finns i [Readme-filen i mappen Resource Manager-mall](https://aka.ms/appgwgoaccessreadme)i GitHub.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Vad kan orsaka Server dels hälso tillstånd att returnera en okänd status?

Normalt ser du en okänd status när åtkomst till Server delen blockeras av en nätverks säkerhets grupp (NSG), anpassad DNS eller användardefinierad routning (UDR) i Application Gateway-undernätet. Mer information finns i [backend-hälsa, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Application Gateway finns i [Vad är Azure Application Gateway?](overview.md).
