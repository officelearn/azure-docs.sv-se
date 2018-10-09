---
title: Vanliga frågor om Azure Application Gateway
description: Den här sidan innehåller svar på vanliga frågor och svar om Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 7b2a550c902e85caf02f05fcbbe5dd7b02acd0cc
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868861"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Vanliga frågor om Application Gateway

## <a name="general"></a>Allmänt

**FRÅGOR OCH. Vad är Application Gateway?**

Azure Application Gateway är en Application Delivery Controller (ADC) som en tjänst erbjuder olika layer 7-belastningsutjämning funktioner för dina program. Den erbjuder hög tillgänglighet och skalbar-tjänsten, som är fullständigt hanterad av Azure.

**FRÅGOR OCH. Vilka funktioner stöder Application Gateway?**

Application Gateway stöder automatisk skalning, SSL-avlastning och slutpunkt till slutpunkt SSL, Brandvägg för webbaserade program, cookie-baserad sessionstillhörighet, url-sökvägsbaserad routning, flera webbplatser och andra. En fullständig lista över funktioner som stöds finns i [introduktion till Application Gateway](application-gateway-introduction.md).

**FRÅGOR OCH. Vad är skillnaden mellan Application Gateway och Azure Load Balancer?**

Application Gateway är en layer 7 belastningsutjämnare, vilket innebär att den fungerar med Internet-trafik endast (WebSocket-HTTP/HTTPS). Det stöder funktioner som SSL-avslutning, cookie-baserad sessionstillhörighet och resursallokering för belastningsutjämning trafik. Läsa in belastningsutjämnare belastningen utjämnar trafiken på nivå 4 (TCP/UDP).

**FRÅGOR OCH. Vilka protokoll stöder Application Gateway?**

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

**FRÅGOR OCH. Hur stöder HTTP/2 i Application Gateway?**

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. 

Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet kodexempel visar hur du kan aktivera den:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**FRÅGOR OCH. Vilka resurser som stöds i dag som en del av backend-pool?**

Serverdelspooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure Web Apps. Medlemmar i Application Gateway serverdelspoolen är inte kopplade till en tillgänglighetsuppsättning. Medlemmar i serverdelspooler kan vara över kluster, Datacenter, eller utanför Azure så länge som de har IP-anslutning.

**FRÅGOR OCH. Vilka regioner är tillgängliga i tjänsten?**

Application Gateway är tillgängliga i alla globala Azure-regioner. Det är också tillgängliga i [Azure Kina](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

**FRÅGOR OCH. Är detta en dedikerad distribution för min prenumeration eller delas mellan kunder?**

Application Gateway är en särskild distribution i det virtuella nätverket.

**FRÅGOR OCH. Är HTTP -> HTTPS-omdirigering stöds?**

Omdirigering av stöds. Se [översikt för omdirigering i Application Gateway](application-gateway-redirect-overview.md) vill veta mer.

**FRÅGOR OCH. I vilken ordning bearbetas lyssnare?**

Lyssnare bearbetas i den ordning som de visas. Därför om en grundläggande lyssnare matchar en inkommande begäran bearbetas först.  Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.

**FRÅGOR OCH. Var hittar jag Application Gateway IP och DNS?**

När du använder en offentlig IP-adress som en slutpunkt kan kan den här informationen hittas på den offentliga IP-adressresursen eller på översiktssidan för application gateway i portalen. För interna IP-adresser finns det på sidan Översikt.

**FRÅGOR OCH. Ändras IP-Adressen eller DNS-namnet över livslängden för Application Gateway?**

VIP-Adressen kan ändras om application gateway stoppas och startas. DNS-namnet som associeras med application gateway ändras inte under hela livscykeln för gatewayen. Därför rekommenderar vi att du använder en CNAME-alias och gå till DNS-adressen för application gateway.

**FRÅGOR OCH. Application Gateway har stöd för statisk IP-adress?**

Ja, stöder Application Gateway V2-SKU statiska offentliga IP-adresser. V1-SKU: N stöder statiska interna IP-adresser.

**FRÅGOR OCH. Stöder Application Gateway flera offentliga IP-adresser på gatewayen?**

Endast en offentlig IP-adress har stöd för en Programgateway.

**FRÅGOR OCH. Hur stor ska jag göra mitt undernät för Application Gateway?**

Application Gateway förbrukar en privat IP-adress per instans, plus en annan privat IP-adress om en privat klientdels-IP-konfiguration har konfigurerats. Dessutom Azure reserverar fyra första och sista IP-adress i varje undernät för intern användning.
Exempel: om en application gateway är inställd på tre instanser och ingen privat klientdels-IP, sedan ett/29 undernät storlek eller högre krävs. I det här fallet använder application gateway tre IP-adresser. Om du har tre instanser och en IP-adress för privata klientdelens IP-konfiguration, sedan en/28 undernät storlek eller högre krävs eftersom det krävs fyra IP-adresser.

**FRÅGOR OCH. Application Gateway har stöd för x-vidarebefordrade-för-huvuden?**

Ja, infogar Application Gateway x vidarebefordras för x-vidarebefordrade-protokoll och x-vidarebefordrade-port rubriker i begäran vidarebefordras till serverdelen. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:Port. Giltiga värden för x-vidarebefordrade-protokoll är http eller https. X-vidarebefordrade-port Anger den port som nått begäran vid application gateway.

Application Gateway infogar också X-Original-värd-huvud som innehåller den ursprungliga värdhuvud som begäran kom. Den här rubriken är användbart i scenarier som Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen.

**FRÅGOR OCH. Hur lång tid tar det för att distribuera en Programgateway? Min Application-Gateway fortfarande fungerar när uppdateras?**

Nya SKU: N för Application Gateway-V1-distributioner kan ta upp till 20 minuter att etablera. Ändringar i storlek/instansantal är inte störande och gatewayen förblir aktiv under den här tiden.

V2-SKU-distributioner kan ta ungefär fem till sex minuter att etablera.

## <a name="configuration"></a>Konfiguration

**FRÅGOR OCH. Application Gateway alltid distribueras i ett virtuellt nätverk?**

Ja, distribueras alltid Application Gateway i ett virtuellt nätverksundernät. Det här undernätet får bara innehålla Application Gateways.

**FRÅGOR OCH. Application Gateway kan kommunicera med instanser utanför dess virtuella nätverk?**

Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den tillhör, så länge det finns en IP-anslutning. Om du planerar att använda interna IP-adresser som medlemmar i serverdelspool så det krävs [VNET-Peering](../virtual-network/virtual-network-peering-overview.md) eller [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**FRÅGOR OCH. Kan jag distribuera allt annat i application gateway-undernätet?**

Nej, men du kan distribuera andra application gatewayer i undernätet.

**FRÅGOR OCH. Stöds Nätverkssäkerhetsgrupper på application gateway-undernät?**

Nätverkssäkerhetsgrupper kan användas i application gateway-undernätet med följande begränsningar:

* Undantag måste placeras i inkommande trafik på portar 65503 65534 för 65200 – 65535 Application Gateway V1-SKU och portar för V2-SKU. Den här portintervall krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte initiera alla ändringar på dessa slutpunkter.

* Det går inte att blockera utgående internet-anslutning.

* Trafik från taggen AzureLoadBalancer måste tillåtas.

**FRÅGOR OCH. Användardefinierade vägar stöds på application gateway-undernät?**

Användardefinierade vägar (Udr) stöds i application gateway-undernätet, så länge de inte ändrar slutpunkt till slutpunkt begäran/svar-kommunikation.

Exempel: du kan ställa in en UDR i application gateway-undernätet så att den pekar till en brandväggsinstallation för paketinspektion, men måste du kontrollera att paketet kan nå den avsedda mål post granskar. I annat fall kan leda till felaktig hälsotillstånd avsökning eller SNMP-trafiken routning beteende. Detta inkluderar inlärda eller 0.0.0.0/0 standardvägar sprids av ExpressRoute eller VPN-gatewayer i det virtuella nätverket.

**FRÅGOR OCH. Vilka är gränserna på Application Gateway? Kan jag öka gränserna?**

Se [gränserna för Programgateways](../azure-subscription-service-limits.md#application-gateway-limits) att visa gränserna.

**FRÅGOR OCH. Kan jag använda Application Gateway för både externa och interna trafik samtidigt?**

Ja, stöder Application Gateway har en intern IP-adress och en extern IP-adress per Programgateway.

**FRÅGOR OCH. VNet-peering stöds?**

Ja, VNet-peering stöds och är bra för belastningsutjämning trafik i andra virtuella nätverk.

**FRÅGOR OCH. Kan jag kontakta lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?**

Ja, förutsatt att trafik tillåts.

**FRÅGOR OCH. Kan jag ha en serverdelspool som betjänar många program på olika portar?**

Micro tjänstarkitektur stöds. Du behöver flera http-inställningar som konfigurerats för att avsöka på olika portar.

**FRÅGOR OCH. Stöder anpassade avsökningar jokertecken/regex på svarsdata?**

Anpassade avsökningar stöder inte jokertecken eller regex på svarsdata. 

**FRÅGOR OCH. Hur bearbetas regler?**

Regler bearbetas i den ordning som de är konfigurerade. Du rekommenderas att multisite regler konfigureras innan grundläggande regler för att minska risken att trafik dirigeras till olämplig serverdelen som grundläggande regel matchar trafik baserat på port innan multisite-regeln som utvärderas.

**FRÅGOR OCH. Vad fältet värden för anpassade avsökningar en obestämd?**

Värdfältet namnet att skicka om avsökningen för att. Gäller endast när flera platser har konfigurerats på Application Gateway, annars använda ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn och är i formatet \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>.

**FRÅGOR OCH. Kan jag listan över godkända Application Gateway-åtkomst till några käll-IP-adresser?**

Det här scenariot kan göras med NSG: er på application gateway-undernätet. Följande begränsningar försätts i undernät i listan prioritetsordning:

* Tillåt inkommande trafik från källa IP/IP-adressintervall.

* Tillåt inkommande begäranden från alla källor till portar 65503 65534 för [kommunikation för backend-hälsotillstånd](application-gateway-diagnostics.md). Intervallet måste anges för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte initiera alla ändringar på dessa slutpunkter.

* Tillåt inkommande Azure belastningsutjämnare avsökningar (taggen AzureLoadBalancer) och inkommande trafik i virtuella nätverk (taggen VirtualNetwork) på den [NSG](../virtual-network/security-overview.md).

* Blockera alla andra inkommande trafik med en neka alla-regeln.

* Tillåt utgående trafik till internet för alla mål.

**FRÅGOR OCH. Kan användas på samma port för både offentliga och privata riktade lyssnare?**

Nej, det stöds inte.

## <a name="performance"></a>Prestanda

**FRÅGOR OCH. Hur stöder hög tillgänglighet och skalbarhet av Application Gateway?**

Application Gateway V1-SKU har stöd för scenarier med hög tillgänglighet när du har två eller fler distribuerade instanser. Azure distribuerar dessa instanser mellan uppdaterings- och feldomäner domäner så att alla instanser inte misslyckas på samma gång. V1-SKU: N stöder skalbarhet genom att lägga till flera instanser av samma gateway att dela belastningen.

V2-SKU säkerställer automatiskt att nya instanser är fördelade över feldomäner och uppdateringsdomäner. Om du väljer zonredundans sprids också senaste instanser i olika tillgänglighetszoner och erbjuder zonindelad fel återhämtning.

**FRÅGOR OCH. Hur jag för att uppnå katastrofåterställning i datacenter med Application Gateway?**

Kunder kan använda Traffic Manager för att distribuera trafik över flera Programgatewayer i olika datacenter.

**FRÅGOR OCH. Det finns stöd för automatisk skalning**

Ja, stöder Application Gateway V2-SKU automatisk skalning. Mer information finns i [automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)](application-gateway-autoscaling-zone-redundant.md).

**FRÅGOR OCH. Stöder manuell skala upp/ned orsak driftavbrott?**

Stilleståndstid ingen, instanser är fördelade på uppgraderingsdomäner och feldomäner.

**FRÅGOR OCH. Stöder Application Gateway Anslutningstömning?**

Ja. Du kan konfigurera Anslutningstömning om du vill ändra medlemmar i en backend-pool utan avbrott. Detta gör att befintliga anslutningar fortsätter som ska skickas till sina tidigare målvolymen tills anslutningen är stängd eller en konfigurerbar tidsgränsen har nåtts. Anslutningstömning endast väntar aktuella pågående anslutningar att slutföra. Application Gateway är inte medveten om sessionstillstånd för programmet.

**FRÅGOR OCH. Vad är application gateway-storlekar?**

Application Gateway finns för närvarande i tre storlekar: **liten**, **medel** och **stor**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

Du kan skapa upp till 50 Application Gateways per prenumeration och varje Application Gateway kan ha upp till 10 instanser styck. Varje Application Gateway kan bestå av 20 http-lyssnare. En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Följande tabell visar ett genomsnittligt prestanda-dataflöde för varje Application Gateway-instans som har SSL-avlastning aktiverat:

| Genomsnittlig backend-svar sidstorlek | Liten | Medel | Stor |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 KB |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

**FRÅGOR OCH. Kan jag ändra instansstorleken från medel till stora utan avbrott?**

Ja, distribuerar Azure instanser över uppdaterings- och feldomäner domäner så att alla instanser inte misslyckas på samma gång. Application Gateway stöder skalning genom att lägga till flera instanser av samma gateway att dela belastningen.

## <a name="ssl-configuration"></a>SSL-konfiguration

**FRÅGOR OCH. Vilka certifikat stöds på Application Gateway?**

Självsignerade certifikat, CA-certifikat och jokertecken certifikat stöds. EV certifikat stöds inte.

**FRÅGOR OCH. Vilka är de aktuella krypteringssviter som stöds av Application Gateway?**

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

**FRÅGOR OCH. Stöder Application Gateway också omkryptering av trafik till serverdelen?**

Ja, Application Gateway stöder SSL-avlastning och slutpunkt till slutpunkt SSL, som krypterar trafiken till serverdelen.

**FRÅGOR OCH. Kan jag konfigurera SSL-princip för att styra SSL-protokoll version?**

Ja, du kan konfigurera Programgatewayen för att neka TLS 1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 är redan inaktiverad som standard och kan inte konfigureras.

**FRÅGOR OCH. Kan jag konfigurera chiffersviter och principordning?**

Ja, [konfigurationen av krypteringssviter](application-gateway-ssl-policy-overview.md) stöds. När du definierar en anpassad princip måste måste minst en av de följande krypteringssviter som aktiveras. Application gateway använder SHA256 till för hantering av serverdelen.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**FRÅGOR OCH. Hur många SSL-certifikat stöds?**

Upp till 20 SSL stöds-certifikat.

**FRÅGOR OCH. Hur många autentiseringscertifikat för serverdelen omkryptering stöds?**

Upp till 10 autentisering stöds certifikat med standardvärdet 5.

**FRÅGOR OCH. Application Gateway integrera med Azure Key Vault internt?**

Nej, det inte är integrerat med Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfiguration av webbprogram Firewall (WAF)

**FRÅGOR OCH. Erbjuder WAF SKU alla funktioner som är tillgängliga med Standard-SKU?**

Ja, WAF stöder alla funktioner i Standard-SKU.

**FRÅGOR OCH. Vad är versionen som CRS Application Gateway stöder?**

Application Gateway stöder CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**FRÅGOR OCH. Hur övervakar jag WAF?**

WAF övervakas via Diagnostisk loggning, mer information om Diagnostisk loggning kan hittas på [diagnostik loggning och mått för Application Gateway](application-gateway-diagnostics.md)

**FRÅGOR OCH. Blockerar trafik i identifieringsläge?**

Identifieringsläge loggar Nej, endast trafik, vilket utlöste en WAF-regel.

**FRÅGOR OCH. Hur jag för att anpassa WAF-regler?**

Ja, WAF-regler är anpassningsbara för mer information om hur du anpassar den se [anpassa WAF-regelgrupper och -regler](application-gateway-customize-waf-rules-portal.md)

**FRÅGOR OCH. Vilka regler är för närvarande tillgängliga?**

WAF stöder för närvarande CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), som ger grundläggande säkerhet mot de flesta av de översta 10 sårbarheter som identifieras av OWASP Open Web Application Security Project () finns här [ OWASP topp 10 sårbarheter](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Skydd mot SQL-inmatning

* Skydd mot skriptkörning över flera webbplatser

* Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

* Skydd mot åtgärder som inte följer HTTP-protokollet

* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

* Skydd mot robotar, crawlers och skannrar

 * Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS osv.)

**FRÅGOR OCH. Stöder WAF också DDoS-skydd?**

Ja. Du kan aktivera DDos-skydd på det virtuella nätverket där application gateway distribueras. Detta säkerställer att application gateway VIP också skyddas med hjälp av Azure DDos Protection-tjänsten.

## <a name="diagnostics-and-logging"></a>Diagnostik- och loggning

**FRÅGOR OCH. Vilka typer av loggar är tillgängliga med Application Gateway?**

Det finns tre loggar som är tillgängliga för Application Gateway. Mer information om dessa loggar och andra diagnostiska funktioner finns i [serverdelens hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** -åtkomst-loggen innehåller varje begäran som skickas till application gateway-klientdel. Innehåller information som anroparens IP-adress, Webbadress som efterfrågas, svarsfördröjning, returkod, byte in och ut. Åtkomstlogg samlas in var 300 sekund. Den här loggfilen innehåller en post per instans av en Programgateway.
- **ApplicationGatewayPerformanceLog** -prestandaloggen samlar in prestandainformation regelbundet per instans inklusive totala antalet begäranden som hanteras, genomflöde i byte, totalt antal begäranden hanteras, antalet misslyckade förfrågningar, felfria och defekta backend-server instansantal.
- **ApplicationGatewayFirewallLog** -brandväggsloggen innehåller begäranden som loggas via identifiering eller förhindra läget för en Programgateway med brandväggen för webbaserade program.

**FRÅGOR OCH. Hur vet jag om min medlemmar i serverdelspoolen är felfria?**

Du kan använda PowerShell-cmdleten `Get-AzureRmApplicationGatewayBackendHealth` eller kontrollera hälsa via portalen genom att besöka [Application Gateway-diagnostik](application-gateway-diagnostics.md)

**FRÅGOR OCH. Vad är bevarandeprincipen på diagnostikloggar?**

Flöde för diagnostikloggar till kunder storage-konto och kunder kan definiera bevarandeprincipen baserat på deras prioritet. Diagnostikloggar kan också skickas till en Event Hub eller Log Analytics. Se [Application Gateway Diagnostics](application-gateway-diagnostics.md) för mer information.

**FRÅGOR OCH. Hur får jag granskningsloggar för Application Gateway?**

Granskningsloggarna är tillgängliga för Application Gateway. I portalen klickar du på **aktivitetsloggen** på bladet menyn i en Programgateway för att få åtkomst till granskningsloggen. 

**FRÅGOR OCH. Kan jag ställa in aviseringar med Application Gateway?**

Ja, stöder Application Gateway aviseringar. Aviseringar kan konfigureras på mått. Se [mätvärden för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) vill veta mer om Application Gateway-mått. Läs mer om aviseringar i [Varningsaviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**FRÅGOR OCH. Hur jag för att analysera trafik statistik för Application Gateway?**

Du kan visa och analysera loggar för åtkomst via ett antal metoder, till exempel Azure Log Analytics, Excel, Power BI osv.

Vi har också publicerat en Resource Manager-mall som installerar och kör populära [GoAccess](https://goaccess.io/) logga analyzer för Åtkomstloggar för Application Gateway. GoAccess ger värdefull HTTP-trafik statistik, till exempel unika besökare, begärt filer, värdar, operativsystem, webbläsare, HTTP-statuskoder och mycket mer. Mer information finns i den [Readme-filen i mallmappen för Resource Manager-i GitHub](https://aka.ms/appgwgoaccessreadme).

**FRÅGOR OCH. Serverdelens hälsotillstånd returnerar okänd status, vad som kan vara orsaken denna status?**

Den vanligaste orsaken är åtkomst till serverdelen blockeras av en NSG eller anpassad DNS. Se [serverdelens hälsotillstånd, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md) vill veta mer.

## <a name="next-steps"></a>Nästa steg

Läs mer om Application Gateway i [vad är Azure Application Gateway?](overview.md)
