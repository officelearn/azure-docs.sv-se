---
title: Vanliga frågor och svar om Azure Application Gateway
description: Hitta svar på vanliga frågor om Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: d9691a6fd5c320242b9677776cbd08be4f800921
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544511"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Vanliga frågor och svar om Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande är vanliga frågor om Azure Application Gateway.

## <a name="general"></a>Allmänt

### <a name="what-is-application-gateway"></a>Vad är Application Gateway?

Azure Application Gateway tillhandahåller en ADC -styrenhet (Application Delivery Controller) som en tjänst. Den erbjuder olika lager 7 belastningsutjämning kapacitet för dina program. Den här tjänsten är mycket tillgänglig, skalbar och hanteras helt av Azure.

### <a name="what-features-does-application-gateway-support"></a>Vilka funktioner stöder Application Gateway?

Application Gateway stöder automatisk skalning, SSL-avlastning och heltäckande SSL, en brandvägg för webbprogram (WAF), cookie-baserad sessionstillhörighet, URL-sökvägsbaserad routning, flerwebbplatshosting och andra funktioner. En fullständig lista över funktioner som stöds finns i [Introduktion till Programgateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hur skiljer sig Application Gateway och Azure Load Balancer åt?

Application Gateway är en belastningsutjämnare för layer 7, vilket innebär att den bara fungerar med webbtrafik (HTTP, HTTPS, WebSocket och HTTP/2). Den stöder funktioner som SSL-avslutning, cookie-baserad sessionstillhörighet och round robin för belastningsutjämningstrafik. Belastningsutjämnad belastningsutjämnar trafik vid lager 4 (TCP eller UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Vilka protokoll stöder Application Gateway?

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hur stöder Application Gateway HTTP/2?

Se [HTTP/2-stöd](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Vilka resurser stöds som en del av en backend-pool?

Se [backend-resurser som stöds](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>I vilka regioner är Application Gateway tillgängligt?

Application Gateway är tillgängligt i alla regioner i globala Azure. Den är också tillgänglig i [Azure China 21Vianet](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Är den här distributionen dedikerad för min prenumeration eller delas den mellan kunder?

Application Gateway är en dedikerad distribution i ditt virtuella nätverk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Stöder Application Gateway omdirigering av HTTP-till-HTTPS?

Omdirigering stöds. Se [Översikt över omdirigering av Programgateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>I vilken ordning bearbetas lyssnare?

Se [ordningen på lyssnaren bearbetning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Var hittar jag Application Gateway IP och DNS?

Om du använder en offentlig IP-adress som slutpunkt hittar du IP- och DNS-informationen på den offentliga IP-adressresursen. Eller hitta den i portalen, på översiktssidan för programgatewayen. Om du använder interna IP-adresser hittar du informationen på översiktssidan.

För v2 SKU öppnar du den offentliga IP-resursen och väljer **Konfiguration**. **Fältet DNS-namnetikett (valfritt)** är tillgängligt för att konfigurera DNS-namnet.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Vilka är inställningarna för Keep-Alive timeout och TCP inaktiv timeout?

*Tidsgränsen keep-Alive* styr hur länge Application Gateway väntar på att en klient ska skicka en annan HTTP-begäran på en beständig anslutning innan den återanvänds eller stängs. *TCP-inaktiv timeout* styr hur länge en TCP-anslutning hålls öppen vid ingen aktivitet. 

*Tidsgränsen keep-alive* i Application Gateway v1 SKU är 120 sekunder och i v2 SKU är det 75 sekunder. *TCP-timeout för inaktiv* är en 4-minuters standard på frontend virtual IP (VIP) för både v1 och v2 SKU of Application Gateway. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP- eller DNS-namnet under programgatewayens livstid?

I Application Gateway V1 SKU kan VIP ändras om du stoppar och startar programgatewayen. Men DNS-namnet som är associerat med programgatewayen ändras inte under gatewayens livstid. Eftersom DNS-namnet inte ändras bör du använda ett CNAME-alias och peka på DNS-adressen för programgatewayen. I Application Gateway V2 SKU kan du ange IP-adressen som statisk, så IP- och DNS-namnet ändras inte under programgatewayens livstid. 

### <a name="does-application-gateway-support-static-ip"></a>Stöder Application Gateway statisk IP?

Ja, Application Gateway v2 SKU stöder statiska offentliga IP-adresser. V1 SKU stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

En programgateway stöder endast en offentlig IP-adress.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stort ska jag göra mitt undernät för Application Gateway?

Se [överväganden för undernätsstorlek för Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan jag distribuera mer än en Application Gateway-resurs till ett enda undernät?

Ja. Förutom flera instanser av en viss Application Gateway-distribution kan du etablera en annan unik Application Gateway-resurs till ett befintligt undernät som innehåller en annan Application Gateway-resurs.

Ett enda undernät kan inte stödja både Standard_v2 och Standard Application Gateway tillsammans.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Stöder Application Gateway v2 användardefinierade vägar (UDR)?

Ja, men bara specifika scenarier. Mer information finns i [konfigurationsöversikt för Programgateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Stöder Application Gateway x-vidarebefordrade för-rubriker?

Ja. Se [Ändringar i en begäran](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hur lång tid tar det att distribuera en programgateway? Fungerar min programgateway medan den uppdateras?

Nya Application Gateway v1 SKU-distributioner kan ta upp till 20 minuter att etablera. Ändringar av instansstorlek eller antal är inte störande och gatewayen förblir aktiv under den här tiden.

De flesta distributioner som använder v2 SKU tar cirka 6 minuter att etablera. Det kan dock ta längre tid beroende på vilken typ av distribution. Distributioner över flera tillgänglighetszoner med många instanser kan till exempel ta mer än 6 minuter. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan jag använda Exchange Server som serverning med Application Gateway?

Nej. Application Gateway stöder inte e-postprotokoll som SMTP, IMAP och POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det vägledning för att migrera från v1 SKU till v2 SKU?

Ja. Mer information finns i [Migrera Azure Application Gateway och brandvägg för webbprogram från v1 till v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Kommer Application Gateway v1 SKU att fortsätta att stödjas?

Ja. Application Gateway v1 SKU kommer att fortsätta att stödjas. Vi rekommenderar dock starkt att du flyttar till v2 för att dra nytta av funktionsuppdateringarna i SKU. Mer information finns i [Automatisk skalning och zon redundant Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder Application Gateway hög tillgänglighet och skalbarhet?

Application Gateway v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller flera instanser. Azure distribuerar dessa instanser över uppdaterings- och feldomäner för att säkerställa att instanser inte alla misslyckas samtidigt. V1 SKU stöder skalbarhet genom att lägga till flera instanser av samma gateway för att dela inläsningen.

V2 SKU säkerställer automatiskt att nya instanser sprids över feldomäner och uppdaterar domäner. Om du väljer zonredundans är de senaste instanserna också spridda över tillgänglighetszoner för att erbjuda zonfelåtersåtersåters.If you choose zone redundancy, the newest instances are also spread across availability zones to offer zonal failure resiliency.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hur uppnår jag ett DR-scenario över datacenter med hjälp av Application Gateway?

Använd Traffic Manager för att distribuera trafik över flera programgateways i olika datacenter.

### <a name="does-application-gateway-support-autoscaling"></a>Stöder Application Gateway automatisk skalning?

Ja, Application Gateway v2 SKU stöder automatisk skalning. Mer information finns i [Automatisk skalning och zon redundant application gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Orsakar manuell eller automatisk skala upp eller skala ned driftstopp?

Nej. Instanser distribueras över uppgraderingsdomäner och feldomäner.

### <a name="does-application-gateway-support-connection-draining"></a>Stöder Application Gateway anslutningsdränning?

Ja. Du kan ställa in anslutningsdränning för att ändra medlemmar i en backend-pool utan avbrott. Mer information finns i [avsnittet om tömning av anslutning i Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instansstorlek från medium till stor utan avbrott?

Ja.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Distribueras Application Gateway alltid i ett virtuellt nätverk?

Ja. Application Gateway distribueras alltid i ett virtuellt nätverksundernät. Det här undernätet kan bara innehålla programgateways. Mer information finns i [krav på virtuellt nätverk och undernät](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway kommunicera med instanser utanför sitt virtuella nätverk eller utanför prenumerationen?

Så länge du har IP-anslutning kan Application Gateway kommunicera med instanser utanför det virtuella nätverk som den finns i. Application Gateway kan också kommunicera med instanser utanför prenumerationen den finns i. Om du planerar att använda interna IP-adresser som serverdpoolmedlemmar använder du [virtuell nätverks peering](../virtual-network/virtual-network-peering-overview.md) eller [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera något annat i undernätet för programgateway?

Nej. Men du kan distribuera andra programgateways i undernätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds nätverkssäkerhetsgrupper i undernätet för programgateway?

Se [Nätverkssäkerhetsgrupper i undernätet Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Stöder undernätet för programgateway användardefinierade vägar?

Se [Användardefinierade vägar som stöds i undernätet Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna för Application Gateway? Kan jag öka dessa gränser?

Se [begränsningar för programgateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan jag samtidigt använda Application Gateway för både extern och intern trafik?

Ja. Application Gateway stöder en intern IP och en extern IP per programgateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Stöder Application Gateway virtuell nätverks peering?

Ja. Virtuell nätverks peering hjälper belastningsbalans trafik i andra virtuella nätverk.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan jag prata med lokala servrar när de är anslutna via ExpressRoute- eller VPN-tunnlar?

Ja, så länge trafik är tillåten.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan en serverningspool betjäna många program på olika portar?

Mikrotjänstarkitektur stöds. Om du vill söka efter på olika portar måste du konfigurera flera HTTP-inställningar.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Stöder anpassade avsökningar jokertecken eller regex på svarsdata?

Nej. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hur bearbetas routningsregler i Application Gateway?

Se [Regler för bearbetningsordning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Vad betyder fältet Värd för anpassade avsökningar?

Fältet Värd anger namnet som ska skickas till avsökningen när du har konfigurerat multiwebbplats på Application Gateway. Använd annars '127.0.0.1'. Det här värdet skiljer sig från värdnamnet för den virtuella datorn. Dess format \<\>är\<\>protokoll\<\>\<://\>värd: portsökväg .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan jag tillåta application gateway-åtkomst till endast ett fåtal käll-IP-adresser?

Ja. Se [begränsa åtkomsten till specifika käll-IPs](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan jag använda samma port för både offentliga och privata lyssnare?

Nej.

### <a name="does-application-gateway-support-ipv6"></a>Stöder Application Gateway IPv6?

Application Gateway v2 stöder för närvarande inte IPv6. Den kan fungera i ett VNet med dubbla staplar med endast IPv4, men gateway-undernätet måste vara endast IPv4. Application Gateway v1 stöder inte virtuella nätverk med dubbla staplar. 

## <a name="configuration---ssl"></a>Konfiguration - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Vilka certifikat stöder Application Gateway?

Application Gateway stöder självsignerade certifikat, certifikatutfärdarecertifikat, EV-certifikat (Extended Validation) och jokerteckencertifikat.

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

Information om hur du anpassar SSL-alternativ finns i [Konfigurera SSL-principversioner och chiffersviter på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Stöder Application Gateway återkryptering av trafik till backend?

Ja. Application Gateway stöder SSL-avlastning och ssl från slutpunkt, som återkrypterar trafik till backend.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-principen för att styra SSL-protokollversioner?

Ja. Du kan konfigurera Application Gateway för att neka TLS1.0, TLS1.1 och TLS1.2. Som standard är SSL 2.0 och 3.0 redan inaktiverade och kan inte konfigureras.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan jag konfigurera chiffersviter och principordning?

Ja. I Application Gateway kan du [konfigurera chiffersviter](application-gateway-ssl-policy-overview.md). Om du vill definiera en anpassad princip aktiverar du minst en av följande chiffersviter. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway använder SHA256 till för hantering av bakåtsträvning.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hur många SSL-certifikat stöder Application Gateway?

Application Gateway stöder upp till 100 SSL-certifikat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hur många autentiseringscertifikat för serveringsåterkryptering stöder Application Gateway?

Application Gateway stöder upp till 100 autentiseringscertifikat.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integreras Application Gateway internt med Azure Key Vault?

Ja, Application Gateway v2 SKU stöder Key Vault. Mer information finns i [SSL-avslutning med Key Vault-certifikat](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hur konfigurerar jag HTTPS-lyssnare för .com- och .net-webbplatser? 

För flera domänbaserade (värdbaserade) routning kan du skapa lyssnare på flera områden, ställa in lyssnare som använder HTTPS som protokoll och associera lyssnarna med routningsreglerna. Mer information finns i [Värd för flera webbplatser med hjälp av Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan jag använda specialtecken i pfx-fillösenordet?

Nej, använd bara alfanumeriska tecken i pfx-fillösenordet.

## <a name="configuration---web-application-firewall-waf"></a>Konfiguration - brandvägg för webbprogram (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Erbjuder WAF SKU alla funktioner som finns i Standard SKU?

Ja. WAF stöder alla funktioner i Standard SKU.

### <a name="how-do-i-monitor-waf"></a>Hur övervakar jag WAF?

Övervaka WAF genom diagnostisk loggning. Mer information finns i [Diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blockerar identifieringsläget trafik?

Nej. Identifieringsläget loggar bara trafik som utlöser en WAF-regel.

### <a name="can-i-customize-waf-rules"></a>Kan jag anpassa WAF-regler?

Ja. Mer information finns i [Anpassa WAF-regelgrupper och regler](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Vilka regler är för närvarande tillgängliga för WAF?

WAF stöder för närvarande CRS [2.2.9,](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229) [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)och [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Dessa regler ger grundläggande säkerhet mot de flesta av de tio säkerhetsproblem som OWASP (Open Web Application Security Project) identifierar: 

* Skydd mot SQL-inmatning
* Skriptskydd över flera webbplatser
* Skydd mot vanliga webbattacker som kommandoinjektion, HTTP-begäransmuggling, HTTP-svarsdelning och attack mot fjärrfilinkludering
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga programmisskonfigurationer (det vill säga Apache, IIS och så vidare)

Mer information finns i [OWASP top-10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Har WAF stöd DDoS-skydd?

Ja. Du kan aktivera DDoS-skydd i det virtuella nätverket där programgatewayen distribueras. Den här inställningen säkerställer att Azure DDoS Protection-tjänsten också skyddar den virtuella IP-adressen (Application Gateway).

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguration - ingångskontrollant för AKS

### <a name="what-is-an-ingress-controller"></a>Vad är en ingress controller?

Kubernetes gör `deployment` det `service` möjligt att skapa och resurs för att exponera en grupp poddar internt i klustret. För att exponera samma tjänst [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) externt definieras en resurs som ger belastningsutjämning, SSL-avslutning och namnbaserad virtuell värd.
För att `Ingress` uppfylla den här resursen krävs en ingående styrenhet som lyssnar efter eventuella ändringar i `Ingress` resurser och konfigurerar belastningsutjämnadsprinciperna.

Application Gateway Ingress Controller gör att [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) kan användas som ingående för en Azure [Kubernetes-tjänst](https://azure.microsoft.com/services/kubernetes-service/) som även kallas ett AKS-kluster.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kan en instans av en ingående styrenhet hantera flera Programgateways?

För närvarande kan en instans av Ingress Controller endast associeras till en Application Gateway.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Vilka typer av loggar tillhandahåller Application Gateway?

Application Gateway innehåller tre loggar: 

* **ApplicationGatewayAccessLog**: Åtkomstloggen innehåller varje begäran som skickas till klientåtkomsten för programmet. Data inkluderar anroparens IP, begärd URL, svarssvarstid, returkod och byte in och ut. Den innehåller en post per programgateway.
* **ApplicationGatewayPerformanceLog**: Prestandaloggen samlar in prestandainformation för varje programgateway. Informationen omfattar dataflödet i byte, totalt antal begäranden, antal misslyckade begäranden och antalet felaktiga serveringsinstanser.
* **ApplicationGatewayFirewallLog**: För programgateways som du konfigurerar med WAF innehåller brandväggsloggen begäranden som loggas antingen i identifieringsläge eller förebyggande läge.

Alla loggar samlas in var 60:e sekund. Mer information finns i [Backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur vet jag om mina backend pool medlemmar är friska?

Verifiera hälsotillståndet med hjälp `Get-AzApplicationGatewayBackendHealth` av PowerShell-cmdleten eller portalen. Mer information finns i [Diagnostik för Programgateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Vad är bevarandeprincipen för diagnostikloggarna?

Diagnostikloggar flödar till kundens lagringskonto. Kunder kan ange bevarandeprincipen baserat på deras önskemål. Diagnostikloggar kan också skickas till en händelsehubb eller Azure Monitor-loggar. Mer information finns i [Diagnostik för Programgateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur får jag granskningsloggar för Application Gateway?

I portalen, på menybladet för en programgateway, väljer du **Aktivitetslogg** för att komma åt granskningsloggen. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ställa in aviseringar med Application Gateway?

Ja. I Application Gateway konfigureras aviseringar på mått. Mer information finns i [Mätvärden för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) och [Ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur analyserar jag trafikstatistik för Application Gateway?

Du kan visa och analysera åtkomstloggar på flera sätt. Använd Azure Monitor-loggar, Excel, Power BI och så vidare.

Du kan också använda en Resource Manager-mall [GoAccess](https://goaccess.io/) som installerar och kör den populära GoAccess-logganalysatorn för Application Gateway-åtkomstloggar. GoAccess tillhandahåller värdefull HTTP-trafikstatistik som unika besökare, begärda filer, värdar, operativsystem, webbläsare och HTTP-statuskoder. Mer information finns i GitHub [i Readme-filen i mallmappen Resurshanteraren](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Vad kan orsaka serverda hälsa för att returnera en okänd status?

Vanligtvis visas en okänd status när åtkomsten till serverdelen blockeras av en nätverkssäkerhetsgrupp (NSG), anpassad DNS eller användardefinierad routning (UDR) i undernätet för programgatewayen. Mer information finns i [Backend-hälsotillstånd, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Finns det något fall där NSG flödesloggar inte kommer att visa tillåten trafik?

Ja. Om konfigurationen matchar följande scenario ser du inte tillåten trafik i NSG-flödesloggarna:
- Du har distribuerat Application Gateway v2
- Du har en NSG på undernätet för programgateway
- Du har aktiverat NSG-flödesloggar på den NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hur använder jag Application Gateway V2 med endast privat ip-adress för klientdel?

Application Gateway V2 stöder för närvarande inte bara privat IP-läge. Den stöder följande kombinationer
* Privat IP och offentlig IP
* Endast offentlig IP

Men om du vill använda Application Gateway V2 med endast privat IP kan du följa processen nedan:
1. Skapa en application gateway med både offentlig och privat IP-adress för klientdel
2. Skapa inga lyssnare för den offentliga klientdelens IP-adress. Application Gateway lyssnar inte på någon trafik på den offentliga IP-adressen om inga lyssnare skapas för den.
3. Skapa och koppla en [nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/security-overview) för undernätet Application Gateway med följande konfiguration i prioritetsordning:
    
    a. Tillåt trafik från Källa som **GatewayManager-tjänsttag** och Mål som **alla** och målporten som **65200-65535**. Det här portintervallet krävs för Azure-infrastrukturkommunikation. Dessa portar skyddas (är låsta) genom certifikatautentisering. Externa entiteter, inklusive gateway-användaradministratörerna, kan inte initiera ändringar på dessa slutpunkter utan att lämpliga certifikat finns på plats
    
    b. Tillåt trafik från källa som **AzureLoadBalancer-tjänsttag** och målport som **alla**
    
    c. Neka all inkommande trafik från Källa som **Internet-tjänsttag** och målport som **Alla**. Ge den här regeln *minst prioritet* i de inkommande reglerna
    
    d. Behåll standardreglerna som att tillåta VirtualNetwork inkommande så att åtkomsten på privat IP-adress inte blockeras
    
    e. Det går inte att blockera utgående internetanslutning. Annars kommer du att möta problem med loggning, mått och så vidare.

Exempel på NSG-konfiguration för ![privat IP-åtkomst: Application Gateway V2 NSG-konfiguration endast för privat IP-åtkomst](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Stöder Application Gateway-tillhörighetscookien attributet SameSite?
Ja, [chromium browser](https://www.chromium.org/Home) [v80 uppdateringen](https://chromiumdash.appspot.com/schedule) infört ett mandat för HTTP-cookies utan SameSite attribut som ska behandlas som SameSite = Lax. Det innebär att tillhörighetscookien Application Gateway inte skickas av webbläsaren i en tredjepartskontext. För att stödja det här scenariot injicerar Application Gateway en annan cookie som kallas *ApplicationGatewayAffinityCORS* utöver den befintliga *ApplicationGatewayAffinity-cookien.*  Dessa cookies är liknande, men *ApplicationGatewayAffinityCORS* cookie har ytterligare två attribut läggs till det: *SameSite = Ingen; Säker*. Dessa attribut upprätthåller klibbiga sessioner även för begäranden med flera ursprung. Mer information finns i [avsnittet cookie-baserad tillhörighet.](configuration-overview.md#cookie-based-affinity)

## <a name="next-steps"></a>Nästa steg

Mer information om Application Gateway finns i [Vad är Azure Application Gateway?](overview.md).
