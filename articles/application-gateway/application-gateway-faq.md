---
title: Vanliga frågor om Azure Application Gateway
description: Den här sidan innehåller svar på vanliga frågor och svar om Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 1/11/2019
ms.author: victorh
ms.openlocfilehash: 5552ca80059b4aa7ef96caf7984b4c15ec177d38
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316599"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Vanliga frågor om Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Allmänt

### <a name="what-is-application-gateway"></a>Vad är Application Gateway?

Azure Application Gateway är en Application Delivery Controller (ADC) som en tjänst erbjuder olika layer 7-belastningsutjämning funktioner för dina program. Den erbjuder hög tillgänglighet och skalbar-tjänsten, som är fullständigt hanterad av Azure.

### <a name="what-features-does-application-gateway-support"></a>Vilka funktioner stöder Application Gateway?

Application Gateway stöder automatisk skalning, SSL-avlastning och slutpunkt till slutpunkt SSL, Brandvägg för webbaserade program, Cookiebaserad sessionstillhörighet, url-baserad routning, flera platser som är värd för och andra. En fullständig lista över funktioner som stöds finns i [introduktion till Application Gateway](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Vad är skillnaden mellan Application Gateway och Azure Load Balancer?

Application Gateway är en layer 7 belastningsutjämnare, vilket innebär att den fungerar med Internet-trafik endast (WebSocket-HTTP/HTTPS). Det stöder funktioner som SSL-avslutning, cookie-baserad sessionstillhörighet och resursallokering för belastningsutjämning trafik. Läsa in belastningsutjämnare belastningen utjämnar trafiken på nivå 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Vilka protokoll stöder Application Gateway?

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hur stöder HTTP/2 i Application Gateway?

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. 

Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet kodexempel visar hur du kan aktivera den:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Vilka resurser som stöds i dag som en del av backend-pool?

Serverdelspooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. Medlemmar i Application Gateway serverdelspoolen är inte kopplade till en tillgänglighetsuppsättning. Medlemmar i serverdelspooler kan vara över kluster, Datacenter, eller utanför Azure så länge som de har IP-anslutning.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner är tillgängliga i tjänsten?

Application Gateway är tillgängliga i alla globala Azure-regioner. Det är också tillgängliga i [Azure Kina 21Vianet](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Är detta en dedikerad distribution för min prenumeration eller delas mellan kunder?

Application Gateway är en särskild distribution i det virtuella nätverket.

### <a name="is-http-https-redirection-supported"></a>Är HTTP -> HTTPS-omdirigering stöds?

Omdirigering av stöds. Se [översikt för omdirigering i Application Gateway](application-gateway-redirect-overview.md) vill veta mer.

### <a name="in-what-order-are-listeners-processed"></a>I vilken ordning bearbetas lyssnare?

Lyssnare bearbetas i den ordning som de visas. Därför om en grundläggande lyssnare matchar en inkommande begäran bearbetas först.  Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Var hittar jag Application Gateway IP och DNS?

När du använder en offentlig IP-adress som en slutpunkt kan kan den här informationen hittas på den offentliga IP-adressresursen eller på översiktssidan för application gateway i portalen. För interna IP-adresser finns det på sidan Översikt.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Vad är timeoutvärdet och inställningen för TCP-tidsgräns vid inaktivitet på Application Gateway?

Keep-Alive tidsgränsen på v1-SKU är 120 sek. Keep-Alive-tidsgräns vid v2-SKU är 75 sek. timeout för inaktivitet av TCP är 4 min standard på klientdelen VIP för Programgatewayen.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP-Adressen eller DNS-namnet över livslängden för Application Gateway?

VIP-Adressen kan ändras om application gateway stoppas och startas. DNS-namnet som associeras med application gateway ändras inte under hela livscykeln för gatewayen. Därför rekommenderar vi att du använder en CNAME-alias och gå till DNS-adressen för application gateway.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway har stöd för statisk IP-adress?

Ja, v2-SKU: N för Application Gateway stöder statiska offentliga IP-adresser. V1-SKU: N stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

Endast en offentlig IP-adress har stöd för en Programgateway.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stor ska jag göra mitt undernät för Application Gateway?

Application Gateway förbrukar en privat IP-adress per instans, plus en annan privat IP-adress om en privat klientdels-IP-konfiguration har konfigurerats. Dessutom Azure reserverar fyra första och sista IP-adress i varje undernät för intern användning.
Exempel: om en application gateway är inställd på tre instanser och ingen privat klientdels-IP, sedan ett/29 undernät storlek eller högre krävs. I det här fallet använder application gateway tre IP-adresser. Om du har tre instanser och en IP-adress för privata klientdelens IP-konfiguration, sedan en/28 undernät storlek eller högre krävs eftersom det krävs fyra IP-adresser.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>F. Kan jag distribuera fler än en Application Gateway-resurs till ett enda undernät?

Ja, förutom att ha flera instanser av en viss Application Gateway-distribution, kan du etablera en annan unik Application Gateway-resurs i ett befintligt undernät som innehåller en annan resurs i Application Gateway.

Blanda Standard_v2 och Standard Application Gateway i samma undernät stöds inte. Dessutom om autoskalning är aktiverat, kan ett undernät bara ha en application gateway.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway har stöd för x-vidarebefordrade-för-huvuden?

Ja, infogar Application Gateway x vidarebefordras för x-vidarebefordrade-protokoll och x-vidarebefordrade-port rubriker i begäran vidarebefordras till serverdelen. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:Port. Giltiga värden för x-vidarebefordrade-protokoll är http eller https. X-vidarebefordrade-port Anger den port som nått begäran vid application gateway.

Application Gateway infogar också X-Original-värd-huvud som innehåller den ursprungliga värdhuvud som begäran kom. Den här rubriken är användbart i scenarier som Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Hur lång tid tar det för att distribuera en Programgateway? Min Application-Gateway fortfarande fungerar när uppdateras?

Ny Programgateway v1 SKU-distributioner kan ta upp till 20 minuter att etablera. Ändringar i storlek/instansantal är inte störande och gatewayen förblir aktiv under den här tiden.

V2-SKU-distributioner kan ta ungefär fem till sex minuter att etablera.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway alltid distribueras i ett virtuellt nätverk?

Ja, distribueras alltid Application Gateway i ett virtuellt nätverksundernät. Det här undernätet får bara innehålla Application Gateways.

### <a name="can-application-gateway-communicate-with-instances-outside-its-virtual-network"></a>Application Gateway kan kommunicera med instanser utanför dess virtuella nätverk?

Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den tillhör, så länge det finns en IP-anslutning. Om du planerar att använda interna IP-adresser som medlemmar i serverdelspool så det krävs [VNET-Peering](../virtual-network/virtual-network-peering-overview.md) eller [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera allt annat i application gateway-undernätet?

Nej, men du kan distribuera andra application gatewayer i undernätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds Nätverkssäkerhetsgrupper på application gateway-undernät?

Nätverkssäkerhetsgrupper (NSG) stöds i application gateway-undernätet med följande begränsningar:

* Undantag måste placeras i inkommande trafik på portarna 65503 65534 för Application Gateway v1-SKU och portar 65200 – 65535 för v2-SKU. Den här portintervall krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer går externa entiteter, inklusive kunderna till dessa gateways, inte att initiera alla ändringar på dessa slutpunkter.

* Det går inte att blockera utgående internet-anslutning. Utgående standardregler i NSG: N kan redan ansluten till internet. Vi rekommenderar att du inte tar bort utgående standardregler och att du inte skapar andra utgående regler som nekar utgående internet-anslutning.

* Trafik från taggen AzureLoadBalancer måste tillåtas.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Användardefinierade vägar stöds på application gateway-undernät?

Användardefinierade vägar (Udr) stöds i application gateway-undernätet, så länge de inte ändrar slutpunkt till slutpunkt begäran/svar-kommunikation.

Exempel: du kan ställa in en UDR i application gateway-undernätet så att den pekar till en brandväggsinstallation för paketinspektion, men måste du kontrollera att paketet kan nå den avsedda mål post granskar. I annat fall kan leda till felaktig hälsotillstånd avsökning eller SNMP-trafiken routning beteende. Detta inkluderar inlärda eller 0.0.0.0/0 standardvägar sprids av ExpressRoute eller VPN-gatewayer i det virtuella nätverket.

Udr: er i application gateway-undernätet är **inte** stöds på v2-SKU. Mer information finns i [automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna på Application Gateway? Kan jag öka gränserna?

Se [gränserna för Programgateways](../azure-subscription-service-limits.md#application-gateway-limits) att visa gränserna.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Kan jag använda Application Gateway för både externa och interna trafik samtidigt?

Ja, stöder Application Gateway har en intern IP-adress och en extern IP-adress per Programgateway.

### <a name="is-vnet-peering-supported"></a>VNet-peering stöds?

Ja, VNet-peering stöds och är bra för belastningsutjämning trafik i andra virtuella nätverk.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Kan jag kontakta lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?

Ja, förutsatt att trafik tillåts.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Kan jag ha en serverdelspool som betjänar många program på olika portar?

Micro tjänstarkitektur stöds. Du behöver flera http-inställningar som konfigurerats för att avsöka på olika portar.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Stöder anpassade avsökningar jokertecken/regex på svarsdata?

Anpassade avsökningar stöder inte jokertecken eller regex på svarsdata.

### <a name="how-are-rules-processed"></a>Hur bearbetas regler?

Regler bearbetas i den ordning som de är konfigurerade. Du rekommenderas att multisite regler konfigureras innan grundläggande regler för att minska risken att trafik dirigeras till olämplig serverdelen som grundläggande regel matchar trafik baserat på port innan multisite-regeln som utvärderas.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Vad fältet värden för anpassade avsökningar en obestämd?

Värdfältet namnet att skicka om avsökningen för att. Gäller endast när flera platser har konfigurerats på Application Gateway, annars använda ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn och är i formatet \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Kan jag listan över godkända Application Gateway-åtkomst till några käll-IP-adresser?

Det här scenariot kan göras med NSG: er på application gateway-undernätet. Följande begränsningar försätts i undernät i listan prioritetsordning:

* Tillåt inkommande trafik från källa IP/IP-adressintervall.

* Tillåt inkommande begäranden från alla källor till portar 65503 65534 för [kommunikation för backend-hälsotillstånd](application-gateway-diagnostics.md). Intervallet måste anges för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte initiera alla ändringar på dessa slutpunkter.

* Tillåt inkommande Azure belastningsutjämnare avsökningar (taggen AzureLoadBalancer) och inkommande trafik i virtuella nätverk (taggen VirtualNetwork) på den [NSG](../virtual-network/security-overview.md).

* Blockera alla andra inkommande trafik med en neka alla-regeln.

* Tillåt utgående trafik till internet för alla mål.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Kan användas på samma port för både offentliga och privata riktade lyssnare?

Nej, det stöds inte.

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder hög tillgänglighet och skalbarhet av Application Gateway?

V1-SKU: N för Application Gateway har stöd för scenarier med hög tillgänglighet när du har två eller fler distribuerade instanser. Azure distribuerar dessa instanser mellan uppdaterings- och feldomäner domäner så att alla instanser inte misslyckas på samma gång. V1-SKU: N stöder skalbarhet genom att lägga till flera instanser av samma gateway att dela belastningen.

V2-SKU säkerställer automatiskt att nya instanser är fördelade över feldomäner och uppdateringsdomäner. Om du väljer zonredundans sprids också senaste instanser i olika tillgänglighetszoner och erbjuder zonindelad fel återhämtning.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Hur jag för att uppnå katastrofåterställning i datacenter med Application Gateway?

Kunder kan använda Traffic Manager för att distribuera trafik över flera Programgatewayer i olika datacenter.

### <a name="is-autoscaling-supported"></a>Det finns stöd för automatisk skalning

Ja, v2-SKU: N för Application Gateway stöder automatisk skalning. Mer information finns i [automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Stöder manuell skala upp/ned orsak driftavbrott?

Det finns inget avbrott. Instanser distribueras bland uppgraderingsdomäner och feldomäner.

### <a name="does-application-gateway-support-connection-draining"></a>Stöder Application Gateway Anslutningstömning?

Ja. Du kan konfigurera Anslutningstömning om du vill ändra medlemmar i en backend-pool utan avbrott. På så sätt kan befintliga anslutningar fortsätter som ska skickas till sina tidigare målvolymen tills anslutningen är stängd eller en konfigurerbar tidsgränsen har nåtts. Anslutningstömning endast väntar aktuella pågående anslutningar att slutföra. Application Gateway är inte medveten om sessionstillstånd för programmet.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instansstorleken från medel till stora utan avbrott?

Ja, distribuerar Azure instanser över uppdaterings- och feldomäner domäner så att alla instanser inte misslyckas på samma gång. Application Gateway stöder skalning genom att lägga till flera instanser av samma gateway att dela belastningen.

## <a name="ssl-configuration"></a>SSL-konfiguration

### <a name="what-certificates-are-supported-on-application-gateway"></a>Vilka certifikat stöds på Application Gateway?

Självsignerade certifikat, CA-certifikat, EV certifikat och jokertecken certifikat stöds.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Vilka är de aktuella krypteringssviter som stöds av Application Gateway?

Följande är de aktuella krypteringssviter som stöds av Application Gateway. Se [Konfigurera SSL-princip versioner och krypteringssviter på Application Gateway](application-gateway-configure-ssl-policy-powershell.md) att lära dig hur du anpassar SSL-alternativ.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Application Gateway också omkryptering av trafik till serverdelen?

Ja, Application Gateway stöder SSL-avlastning och slutpunkt till slutpunkt SSL, som krypterar trafiken till serverdelen.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan jag konfigurera SSL-princip för att styra SSL-protokoll version?

Ja, du kan konfigurera Programgatewayen för att neka TLS 1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 är redan inaktiverad som standard och kan inte konfigureras.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan jag konfigurera chiffersviter och principordning?

Ja, [konfigurationen av krypteringssviter](application-gateway-ssl-policy-overview.md) stöds. När du definierar en anpassad princip måste måste minst en av de följande krypteringssviter som aktiveras. Application gateway använder SHA256 till för hantering av serverdelen.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Hur många SSL-certifikat stöds?

Upp till 100 SSL stöds-certifikat.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Hur många autentiseringscertifikat för serverdelen omkryptering stöds?

Upp till 10 autentisering stöds certifikat med standardvärdet 5.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Application Gateway integrera med Azure Key Vault internt?

Nej, det inte är integrerat med Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfiguration av webbprogram Firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>Erbjuder WAF SKU alla funktioner som är tillgängliga med Standard-SKU?

Ja, WAF stöder alla funktioner i Standard-SKU.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Vad är versionen som CRS Application Gateway stöder?

Application Gateway stöder CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Hur övervakar jag WAF?

WAF övervakas via Diagnostisk loggning, mer information om Diagnostisk loggning kan hittas på [diagnostik loggning och mått för Application Gateway](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Blockerar trafik i identifieringsläge?

Identifieringsläge loggar Nej, endast trafik, vilket utlöste en WAF-regel.

### <a name="can-i-customize-waf-rules"></a>Kan jag Anpassa WAF-regler?

Ja, WAF-regler är anpassningsbara. Mer information finns i [anpassa WAF-regelgrupper och -regler](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Vilka regler som är tillgängliga

WAF stöder för närvarande CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), som ger grundläggande säkerhet mot de flesta av de översta 10 sårbarheter som identifieras av OWASP Open Web Application Security Project () finns här [ OWASP topp 10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Skydd mot SQL-inmatning

* Skydd mot skriptkörning över flera webbplatser

* Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

* Skydd mot åtgärder som inte följer HTTP-protokollet

* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

* Skydd mot robotar, crawlers och skannrar

* Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS osv.)

### <a name="does-waf-also-support-ddos-prevention"></a>Stöder WAF också DDoS-skydd?

Ja. Du kan aktivera DDos-skydd på det virtuella nätverket där application gateway distribueras. Detta säkerställer att application gateway VIP också skyddas med hjälp av Azure DDos Protection-tjänsten.

## <a name="diagnostics-and-logging"></a>Diagnostik- och loggning

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Vilka typer av loggar är tillgängliga med Application Gateway?

Det finns tre loggar som är tillgängliga för Application Gateway. Mer information om dessa loggar och andra diagnostiska funktioner finns i [serverdelens hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** -åtkomst-loggen innehåller varje begäran som skickas till application gateway-klientdel. Innehåller information som anroparens IP-adress, Webbadress som efterfrågas, svarsfördröjning, returkod, byte in och ut. Åtkomstlogg samlas in var 300 sekund. Den här loggfilen innehåller en post per instans av en Programgateway.
* **ApplicationGatewayPerformanceLog** -prestandaloggen samlar in prestandainformation regelbundet per instans inklusive totala antalet begäranden som hanteras, genomflöde i byte, totalt antal begäranden hanteras, antalet misslyckade förfrågningar, felfria och defekta backend-server instansantal.
* **ApplicationGatewayFirewallLog** -brandväggsloggen innehåller begäranden som loggas via identifiering eller förhindra läget för en Programgateway med brandväggen för webbaserade program.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur vet jag om min medlemmar i serverdelspoolen är felfria?

Du kan använda PowerShell-cmdleten `Get-AzApplicationGatewayBackendHealth` eller kontrollera hälsa via portalen genom att besöka [Application Gateway-diagnostik](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarandeprincipen på diagnostikloggar?

Flöde för diagnostikloggar till kunder storage-konto och kunder kan definiera bevarandeprincipen baserat på deras prioritet. Diagnostikloggar kan också skickas till en Event Hub eller Azure Monitor loggar. Se [Application Gateway Diagnostics](application-gateway-diagnostics.md) för mer information.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur får jag granskningsloggar för Application Gateway?

Granskningsloggarna är tillgängliga för Application Gateway. I portalen klickar du på **aktivitetsloggen** på bladet menyn i en Programgateway för att få åtkomst till granskningsloggen. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ställa in aviseringar med Application Gateway?

Ja, stöder Application Gateway aviseringar. Aviseringar kan konfigureras på mått. Se [mätvärden för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) vill veta mer om Application Gateway-mått. Läs mer om aviseringar i [Varningsaviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur jag för att analysera trafik statistik för Application Gateway?

Du kan visa och analysera loggar för åtkomst via flera mekanismer, till exempel Azure Monitor-loggar, Excel, Power BI osv.

Vi har också publicerat en Resource Manager-mall som installerar och kör populära [GoAccess](https://goaccess.io/) logga analyzer för Åtkomstloggar för Application Gateway. GoAccess ger värdefull HTTP-trafik statistik, till exempel unika besökare, begärt filer, värdar, operativsystem, webbläsare, HTTP-statuskoder och mycket mer. Mer information finns i den [Readme-filen i mallmappen för Resource Manager-i GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Serverdelens hälsotillstånd returnerar okänd status, vad som kan vara orsaken denna status?

Den vanligaste orsaken är blockeras åtkomsten till serverdelen av en NSG eller anpassad DNS. Se [serverdelens hälsotillstånd, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md) vill veta mer.

## <a name="next-steps"></a>Nästa steg

Läs mer om Application Gateway i [vad är Azure Application Gateway?](overview.md)
