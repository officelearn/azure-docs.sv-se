---
title: Vanliga frågor om Azure Application Gateway
description: Få svar på vanliga frågor om Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: 9d47986df9b9cbc6b8519aa0466b3434d6216e72
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986010"
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

*Keep-Alive-timeout* styr hur länge Application Gateway väntar på att en klient ska skicka en annan http-begäran på en permanent anslutning innan den återanvänds eller stängas. *Timeout för TCP-inaktivitet* styr hur länge en TCP-anslutning hålls öppen om ingen aktivitet är öppen. 

*Timeoutvärdet för Keep-Alive* i Application Gateway v1 sku är 120 sekunder och i v2-SKU: n är 75 sekunder. *Timeout för TCP-inaktivitet* är en fyra minuters standard på klient delens virtuella IP (VIP) för både v1-och v2-SKU: er för Application Gateway. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP-eller DNS-namn under programgatewayens livs längd?

I Application Gateway v1 SKU: n kan du ändra VIP om du stoppar och startar programgatewayen. Men det DNS-namn som är associerat med programgatewayen ändras inte över gatewayens livstid. Eftersom DNS-namnet inte ändras bör du använda ett CNAME-alias och peka det mot DNS-adressen för Application Gateway. I Application Gateway v2 SKU: n kan du ange IP-adressen som statisk, så IP-och DNS-namn kommer inte att ändras under programmets livs längd. 

### <a name="does-application-gateway-support-static-ip"></a>Stöder Application Gateway statisk IP?

Ja, Application Gateway v2-SKU: n stöder statiska offentliga IP-adresser. V1 SKU stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

En Application Gateway stöder bara en offentlig IP-adress.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stor ska jag göra mitt undernät för Application Gateway?

Se [Application Gateway-undernätets storleks överväganden](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan jag distribuera fler än en Application Gateway resurs till ett enda undernät?

Ja. Förutom flera instanser av en specifik Application Gateway distribution kan du etablera en annan unik Application Gateway resurs till ett befintligt undernät som innehåller en annan Application Gateway resurs.

Ett enda undernät har inte stöd för både Standard_v2-och standard Application Gateway tillsammans.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Stöder Application Gateway x-vidarebefordrade-för rubriker?

Ja. Se [ändringar av en begäran](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hur lång tid tar det att distribuera en Application Gateway? Fungerar min Application Gateway när den uppdateras?

Det kan ta upp till 20 minuter att etablera nya Application Gateway v1 SKU-distributioner. Ändringar av instans storleken eller antalet är inte störande och gatewayen förblir aktiv under den här tiden.

De flesta distributioner som använder v2-SKU: n tar cirka 6 minuter att etablera. Det kan dock ta längre tid beroende på typen av distribution. Till exempel kan distributioner över flera Tillgänglighetszoner med många instanser ta mer än 6 minuter. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan jag använda Exchange Server som Server del med Application Gateway?

Nej. Application Gateway stöder inte e-postprotokoll som SMTP, IMAP och POP3. 

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder Application Gateway hög tillgänglighet och skalbarhet?

Application Gateway v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller fler instanser. Azure distribuerar dessa instanser över uppdaterings-och fel domäner för att säkerställa att instanserna inte fungerar samtidigt. V1 SKU stöder skalbarhet genom att lägga till flera instanser av samma Gateway för att dela belastningen.

V2-SKU: n ser automatiskt till att nya instanser sprids mellan fel domäner och uppdaterings domäner. Om du väljer zon redundans sprids även de nyaste instanserna över tillgänglighets zoner för att erbjuda zonindelade-felåterhämtning.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hur gör jag för att uppnå ett DR-scenario över data Center med Application Gateway?

Använd Traffic Manager för att distribuera trafik över flera programgatewayer i olika data Center.

### <a name="does-application-gateway-support-autoscaling"></a>Stöder Application Gateway automatisk skalning?

Ja, Application Gateway v2-SKU: n stöder automatisk skalning. Mer information finns i [autoskalning och zon-redundant Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Är manuell eller automatisk skalning eller manuell skalning att göra drift stopp?

Nej. Instanser distribueras mellan uppgraderings domäner och fel domäner.

### <a name="does-application-gateway-support-connection-draining"></a>Har Application Gateway stöd för anslutning som töms?

Ja. Du kan konfigurera anslutnings tömning för att ändra medlemmar i en backend-pool utan avbrott. Mer information finns i [avsnittet om tömning av anslutningar i Application Gateway](overview.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instans storleken från medel till stor utan avbrott?

Ja. Azure distribuerar instanser över uppdaterings-och fel domäner för att se till att instanserna inte växlar alla samtidigt. Application Gateway stöder skalning genom att lägga till flera instanser av samma Gateway för att dela belastningen.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Är Application Gateway alltid distribuerat i ett virtuellt nätverk?

Ja. Application Gateway distribueras alltid i ett undernät för virtuellt nätverk. Det här under nätet får bara innehålla programgatewayer. Mer information finns i [krav för virtuellt nätverk och undernät](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway kommunicera med instanser utanför det virtuella nätverket eller utanför prenumerationen?

Så länge som du har IP-anslutning kan Application Gateway kommunicera med instanser utanför det virtuella nätverk där det finns. Application Gateway kan också kommunicera med instanser utanför prenumerationen som det finns i. Om du planerar att använda interna IP-adresser som medlemmar i backend-poolen använder du [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) eller [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera något annat i Application Gateway-undernätet?

Nej. Men du kan distribuera andra programgatewayer i under nätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds nätverks säkerhets grupper i undernät för Application Gateway?

Se [nätverks säkerhets grupper i Application Gateway under nätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Har Application Gateway-undernätet stöd för användardefinierade vägar?

Se [användardefinierade vägar som stöds i Application Gateway under nätet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna för Application Gateway? Kan jag öka de här gränserna?

Se [Application Gateway gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

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

Fältet värd anger namnet som avsökningen ska skickas till när du har konfigurerat multisite på Application Gateway. Använd annars 127.0.0.1. Det här värdet skiljer sig från värd namnet för den virtuella datorn. Formatet är \<protokoll\>://\<Host\>:\<port\>\<sökväg\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan jag bara tillåta Application Gateway åtkomst till några få käll-IP-adresser?

Ja. Se [begränsa åtkomsten till vissa käll-IP-adresser](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan jag använda samma port för både offentlig och privat lyssnare?

Nej.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det några rikt linjer som du kan använda för att migrera från v1 SKU till v2-SKU: n?

Ja. Mer information finns i [migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2](migrate-v1-v2.md).

### <a name="does-application-gateway-support-ipv6"></a>Stöder Application Gateway IPv6?

Application Gateway v2 stöder för närvarande inte IPv6. Den kan köras i ett VNet med dubbla stackar som bara använder IPv4, men Gateway-undernätet måste vara endast IPv4. Application Gateway v1 stöder inte dubbla stack-virtuella nätverk. 

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

Application Gateway stöder upp till 100 autentiserings-certifikat.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integrerar Application Gateway internt med Azure Key Vault?

Ja, Application Gateway v2-SKU: n stöder Key Vault. Mer information finns i [SSL-avslutning med Key Vault certifikat](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hur gör jag för att konfigurera HTTPS-lyssnare för. com-och .net-platser? 

För flera domänbaserade (värdbaserade) routningar kan du skapa multisite-lyssnare, konfigurera lyssnare som använder HTTPS som protokoll och associera lyssnarna med routningsregler. Mer information finns i vara [värd för flera platser med hjälp av Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan jag använda specialtecken i lösen ordet för PFX-filen?

Nej, Använd endast alfanumeriska tecken i PFX-filens lösen ord.

## <a name="configuration---web-application-firewall-waf"></a>Konfiguration – brand vägg för webbaserade program (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Erbjuder WAF SKU alla funktioner som är tillgängliga i standard-SKU: n?

Ja. WAF stöder alla funktioner i standard-SKU: n.

### <a name="how-do-i-monitor-waf"></a>Hur gör jag för att övervaka WAF?

Övervaka WAF via diagnostisk loggning. Mer information finns i [diagnostisk loggning och statistik för Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blockeras trafik i identifierings läge?

Nej. Identifierings läge loggar enbart trafik som utlöser en WAF-regel.

### <a name="can-i-customize-waf-rules"></a>Kan jag anpassa WAF-regler?

Ja. Mer information finns i [Anpassa WAF Rule Groups och Rules](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Vilka regler är för närvarande tillgängliga för WAF?

WAF stöder för närvarande BOKNINGs- [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)och [3,1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). De här reglerna ger grundläggande säkerhet mot de flesta 10 högsta säkerhets risker som öppnar webb program säkerhets projekt (OWASP): 

* Skydd mot SQL-inmatning
* Skript skydd över hela platsen
* Skydd mot vanliga webb attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och attack för att ta emot fjärrfiler
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga program konfigurationer (det vill säga Apache, IIS och så vidare)

Mer information finns i [OWASP topp 10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Stöder WAF DDoS-skydd?

Ja. Du kan aktivera DDoS-skydd på det virtuella nätverket där programgatewayen distribueras. Denna inställning garanterar att tjänsten Azure DDoS Protection även skyddar applikationsgatewayens virtuella IP-adress (VIP).

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det några rikt linjer som du kan använda för att migrera från v1 SKU till v2-SKU: n?

Ja. Mer information finns i [migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguration – ingress-styrenhet för AKS

### <a name="what-is-an-ingress-controller"></a>Vad är en ingångs kontroll?

Med Kubernetes kan du skapa `deployment` och `service` resurs för att exponera en grupp poddar internt i klustret. För att exponera samma tjänst externt definieras en [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) resurs som tillhandahåller belastnings utjämning, SSL-avslutning och namnbaserade virtuella värdar.
För att tillfredsställa denna `Ingress` resurs krävs en ingångs kontroll som lyssnar efter ändringar i `Ingress` resurser och konfigurerar belastnings Utjämnings principerna.

På Application Gateway ingångs styrenheten kan [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) användas som ingress för en [Azure Kubernetes-tjänst](https://azure.microsoft.com/services/kubernetes-service/) även kallat ett AKS-kluster.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kan en enskild instans av kontroll enheten hantera flera programgatewayer?

För närvarande kan en instans av ingångs styrenheten bara kopplas till en Application Gateway.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Vilka typer av loggar Application Gateway tillhandahåller?

Application Gateway innehåller tre loggar: 

* **ApplicationGatewayAccessLog**: åtkomst loggen innehåller varje begäran som skickats till Application Gateway-frontend. Data innehåller anroparens IP, URL begärd, svars fördröjning, retur kod och byte in och ut. Åtkomst loggen samlas in var 300: e sekund. Den innehåller en post per Application Gateway.
* **ApplicationGatewayPerformanceLog**: prestanda loggen samlar in prestanda information för varje Application Gateway. Informationen omfattar data flödet i byte, totalt antal hanterade begär Anden, misslyckade begär Anden och antalet felfria och felaktiga Server dels instanser.
* **ApplicationGatewayFirewallLog**: för programgatewayer som du konfigurerar med WAF innehåller brand Väggs loggen begär Anden som loggas via antingen identifierings läge eller skydds läge.

Mer information finns i [backend-hälsohälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur gör jag för att veta om mina medlemmar i Server delens medlemmar är felfria?

Verifiera hälsan genom att använda PowerShell-cmdleten `Get-AzApplicationGatewayBackendHealth` eller portalen. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostiska loggar flödar till kundens lagrings konto. Kunder kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur gör jag för att hämta gransknings loggar för Application Gateway?

I portalen väljer du **aktivitets logg** för att få åtkomst till gransknings loggen på Meny bladet för en Programgateway. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ange aviseringar med Application Gateway?

Ja. I Application Gateway konfigureras aviseringar för mått. Mer information finns i [Application Gateway mått](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) och [ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur gör jag för att analysera trafik statistik för Application Gateway?

Du kan visa och analysera åtkomst loggar på flera sätt. Använd Azure Monitor loggar, Excel, Power BI och så vidare.

Du kan också använda en Resource Manager-mall som installerar och kör den populära [GoAccess](https://goaccess.io/) log analyzer för Application Gateway åtkomst loggar. GoAccess tillhandahåller värdefull statistik för HTTP-trafik, till exempel unika besökare, begärda filer, värdar, operativ system, webbläsare och HTTP-statuskod. Mer information finns i [Readme-filen i mappen Resource Manager-mall](https://aka.ms/appgwgoaccessreadme)i GitHub.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Vad kan orsaka Server dels hälso tillstånd att returnera en okänd status?

Normalt ser du en okänd status när åtkomst till Server delen blockeras av en nätverks säkerhets grupp (NSG), anpassad DNS eller användardefinierad routning (UDR) i Application Gateway-undernätet. Mer information finns i [backend-hälsa, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Finns det några fall där NSG Flow-loggar inte visar tillåten trafik?

Ja. Om konfigurationen matchar följande scenario visas inte tillåten trafik i dina NSG-flödes loggar:
- Du har distribuerat Application Gateway v2
- Du har en NSG på Application Gateway-undernätet
- Du har aktiverat NSG Flow-loggar på den NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hur gör jag för att använda Application Gateway v2 med endast en privat klient dels-IP-adress?

Application Gateway v2 stöder för närvarande inte enbart privat IP-läge. Det stöder följande kombinationer
* Privat IP och offentlig IP
* Endast offentlig IP

Men om du vill använda Application Gateway v2 med endast privat IP kan du följa stegen nedan:
1. Skapa en Application Gateway med både offentlig och privat klient dels-IP-adress
2. Skapa inga lyssnare för den offentliga IP-adressen för klient delen. Application Gateway lyssnar inte på någon trafik på den offentliga IP-adressen om inga lyssnare har skapats för den.
3. Skapa och koppla en [nätverks säkerhets grupp](https://docs.microsoft.com/azure/virtual-network/security-overview) för Application Gateway under nätet med följande konfiguration i prioritetsordning:
    
    a. Tillåt trafik från källa som **GatewayManager** service tag och destination som **valfri** port och målport som **65200-65535**. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av certifikatautentisering. Externa entiteter, inklusive Gateway-användarens administratörer, kan inte påbörja ändringar av dessa slut punkter utan lämpliga certifikat på plats
    
    b. Tillåt trafik från källa som **AzureLoadBalancer** service tag och mål-och mål port som **alla**
    
    c. Neka all inkommande trafik från källan som **Internet** service tag och mål-och mål port som **en**. Ge den här regeln *lägst prioritet* i reglerna för inkommande trafik
    
    d. Behåll standard reglerna som att tillåta VirtualNetwork inkommande så att åtkomsten till den privata IP-adressen inte är blockerad
    
    e. Det går inte att blockera utgående Internet anslutning. Annars är det problem med loggning, mått osv.

Exempel på NSG-konfiguration för privat IP-åtkomst: ![Application Gateway v2 NSG-konfiguration endast för privat IP-åtkomst](./media/application-gateway-faq/appgw-privip-nsg.png)


## <a name="next-steps"></a>Nästa steg

Mer information om Application Gateway finns i [Vad är Azure Application Gateway?](overview.md).
